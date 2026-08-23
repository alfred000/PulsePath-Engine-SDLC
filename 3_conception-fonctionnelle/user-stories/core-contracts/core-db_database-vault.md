# Coffre-Fort de Persistance Locale [CORE-DB] : Transactions & Chiffrement au Repos
**Priorité (MoSCoW) :** Must Have  
**Module associé :** `:core:database` (Persistance Locale Sécurisée)

### Description
**En tant qu'** architecte de la solution PulsePath,  
**Je veux** centraliser l'accès et l'écriture sur le support de stockage physique local du terminal de l'utilisateur,  
**Afin d'** imposer un chiffrement complet des données de santé au repos, de garantir l'atomisation des écritures via des blocs transactionnels et de maintenir la résilience des historiques en mode déconnecté.

---

## 🏗️ Positionnement Architectural & Garanties d'Infrastructure

Le module `:core:database` se situe au niveau le plus bas de la couche de domaine partagée :
*   **Horizontalité universelle :** Il ignore les règles métiers spécifiques des fonctionnalités de l'application (`:feature`). Il fournit des contrats d'accès aux tables et des garanties de chiffrement consommés de manière étanche par les autres briques du système.
*   **Source de vérité locale :** Il agit comme le tampon de stockage persistant primaire permettant à l'interface client de fonctionner de manière fluide, avec ou sans connectivité réseau active.

---

## 🛠️ Critères d'Acceptation & Spécifications (Functional Design)

### [REQ-DB-01] : Chiffrement Transparent au Repos par Clé Matérielle (BR-DB-01)
Le fichier de stockage local contenant des données biométriques, nutritionnelles ou qualitatives doit faire l'objet d'une isolation cryptographique totale au repos :
*   *Critère 1 :* Zéro texte brut. Aucun identifiant, métrique, note de ressenti ou configuration ne doit être écrit en clair sur la mémoire flash physique du téléphone. Le chiffrement doit utiliser un algorithme par blocs standardisé robuste (AES-256) appliqué sur l'intégralité des pages du fichier.
*   *Critère 2 :* Gestion de la clé. La clé de déchiffrement est extraite du conteneur de clés sécurisé et isolé du processeur de l'appareil (`Hardware Keystore`) uniquement lors de la phase active de la session de l'utilisateur. Elle est immédiatement effacée de la mémoire vive (purgée) lors d'un événement de déconnexion explicite ou après 15 minutes d'inactivity.

### [REQ-DB-02] : Isolation Transactionnelle Strict et Mode Hors-ligne
Pour protéger le système contre la corruption de fichiers lors des phases d'ingestions lourdes ou de saisies manuelles simultanées :
*   *Critère 1 :* Toute modification multiple au cours d'une même action utilisateur (ex: valider les 3 séries d'un exercice) doit obligatoirement s'exécuter au sein d'une seule transaction atomique. En cas de coupure de batterie au milieu du traitement, le système applique un Rollback automatique pour restaurer la table dans son état initial sain.
*   *Critère 2 :* Le module de persistance intègre un indicateur de synchronisation binaire (`IsSynchronized = Faux`) sur chaque ligne insérée en mode déconnecté, permettant au client réseau (`:core:network`) d'identifier précisément les données restant à propager vers le serveur distant dès le rétablissement du signal.

### [REQ-DB-03] : Politique d'Évolution de Schémas Non-Destructive (BR-DB-02)
Lors de l'application d'une mise à jour logicielle modifiant la structure relationnelle des tables (ajout de colonnes, indexation) :
*   *Critère 1 :* L'application d'une nouvelle version de base de données doit être accompagnée d'un script de migration explicite pas-à-pas (Migration Plan). 
*   *Critère 2 :* Le recours à une réinitialisation destructive automatique (Wipe / Destructive Fallback) est formellement interdit pour l'ensemble des modules stockant des données de santé (notamment `:feature:journal`, `:feature:nutrition`, `:feature:workout`, et `:feature:habits`). En cas d'échec technique critique de la migration locale, l'application doit isoler le fichier corrompu et déclencher une restauration sécurisée depuis le miroir cloud de l'API.

---

## 💻 Spécification Technique du Gestionnaire (C# Layout Blueprint)

```csharp
// C# .NET Core Clean Architecture - Unité de Travail de la Base Locale
// Emplacement : PulsePath.Core.Infrastructure.Database.ILocalStorageVault.cs

namespace PulsePath.Core.Infrastructure.Database;

public interface ILocalStorageVault
{
    /// <summary>
    /// Initialise le volume de stockage local chiffré en montant la clé sécurisée du matériel.
    /// </summary>
    Task InitializeVaultAsync(string hardwareSecureToken);

    /// <summary>
    /// Exécute un ensemble d'instructions d'écritures au sein d'une transaction atomique étanche.
    /// </summary>
    Task ExecuteInTransactionAsync(Func<Task> databaseUnitOfWork);
}
```

