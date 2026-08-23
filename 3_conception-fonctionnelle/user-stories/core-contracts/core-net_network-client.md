# Contrat Réseau Transversal [CORE-NET] : Client HTTP, Sécurité des Payloads & Jetons
**Priorité (MoSCoW) :** Must Have  
**Module associé :** `:core:network` (Client Réseau Commun)

### Description
**En tant qu'** architecte de la solution PulsePath,  
**Je veux** centraliser l'interception et le routage de l'ensemble des communications HTTP sortantes de l'application,  
**Afin d'** injecter automatiquement les jetons d'accès, d'assurer le renouvellement transparent des sessions expirées et d'appliquer des politiques de résilience robustes face aux coupures réseau transitoires.

---

## 🏗️ Positionnement Architectural & Découpage Étanche

Le module `:core:network` est un composant horizontal pur qui fait office de plomberie sécurisée pour l'ensemble du système :
*   **Neutralité métier :** Il ignore totalement les concepts métiers et les règles de calcul des fonctionnalités verticales (`:feature`). Il manipule des enveloppes réseau génériques.
*   **Automatisation :** Il abstrait la gestion de la sécurité (HTTPS, TLS 1.3) et de l'authentification pour éviter aux développeurs de devoir réécrire la logique d'autorisation dans chaque sous-module fonctionnel.

---

## 🛠️ Critères d'Acceptation & Spécifications (Functional Design)

### [REQ-NET-01] : Interception et Injection Transparente du Jeton (Bearer)
Toute requête HTTP sortante à destination des endpoints sécurisés de l'API distante doit obligatoirement être capturée à la volée par un intercepteur réseau centralisé :
*   *Critère 1 :* L'intercepteur extrait l'Access Token valide maintenu en mémoire vive (State applicatif client) et l'injecte dans l'en-tête de sécurité sous la syntaxe `Authorization: Bearer <Jeton>`.
*   *Critère 2 :* Le module doit analyser les routes et autoriser le passage sans jeton (bypass) exclusivement pour les requêtes publiques explicitement déclarées (ex: `/api/auth/login` ou `/api/auth/register`).

### [REQ-NET-02] : Protocole de Rafraîchissement Transparent de Session (BR-NET-01)
Si une requête métier (ex: l'envoi d'un repas) est rejetée par l'API avec un code HTTP 401 Unauthorized suite à l'expiration naturelle du jeton court (15 minutes écoulées selon la règle `BR-AUTH-02`), le client réseau suspend le pipeline de transmission et applique la règle `BR-NET-01` :
*   *Critère 1 :* La requête initiale en échec est mise en attente (Queue) locale. L'interface utilisateur reste stable à l'écran, sans déconnexion visuelle pour l'utilisateur.
*   *Critère 2 :* Le client émet une demande de synchronisation unique et isolée vers l'endpoint `/api/auth/refresh` en transmettant le Refresh Token stocké dans le cookie sécurisé (`HttpOnly`, `Secure`).
*   *Critère 3 :* En cas de succès, le State applicatif est mis à jour avec le nouvel Access Token et la requête mise en attente est automatiquement réémise avec la nouvelle clé pour finaliser l'action. En cas d'échec (Refresh Token expiré après 7 jours), le client lève un signal de déconnexion forcée globale.

### [REQ-NET-03] : Algorithme de Résilience par Relance Exponentielle (BR-NET-02)
Lorsqu'un appel réseau échoue en raison d'une panne transitoire qualifiée (absence temporaire de signal, code HTTP 503 Service Unavailable, code HTTP 408 Timeout), le module doit interdire le blocage immédiat de l'interface et appliquer l'ordonnancement de la règle `BR-NET-02` :
*   *Tentative 1 :* Exécutée immédiatement après l'échec initial ($T+0$).
*   *Tentative 2 :* Exécutée après un délai d'attente de **2 secondes**.
*   *Tentative 3 :* Exécutée après un délai d'attente de **4 secondes**.
*   *Tentative 4 :* Exécutée après un délai d'attente de **8 secondes** (Exponential Backoff).
*   *Critère de repli :* Si la 4ème tentative échoue également, le client réseau bascule formellement l'application en mode "Hors-ligne dégradé" et notifie le gestionnaire de persistance locale (`:core:database`).

---

## 💻 Spécification Technique de l'Intercepteur (C# Layout Blueprint)

```csharp
// C# .NET Core Clean Architecture - Pipeline d'Interception HTTP
// Emplacement : PulsePath.Core.Infrastructure.Network.SecureNetworkInterceptor.cs

namespace PulsePath.Core.Infrastructure.Network;

public interface INetworkCommunicationClient
{
    /// <summary>
    /// Expédie de manière sécurisée une charge utile chiffrée après injection des en-têtes d'autorisation.
    /// </summary>
    Task<NetworkResponseEnvelope<TResponse>> SendSecureRequestAsync<TRequest, TResponse>(
        string endpoint, 
        HttpMethod method, 
        TRequest payload) where TRequest : class;
}
```

