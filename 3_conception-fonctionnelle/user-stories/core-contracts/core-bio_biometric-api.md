# Contrat Pivot Biométrique [CORE-BIO] : Structures Communes & Interfaces de Secours
**Priorité (MoSCoW) :** Must Have  
**Module associé :** `:core:biometric-api` (Contrats Transversaux Biométriques)

### Description
**En tant qu'** architecte de la solution PulsePath,  
**Je veux** imposer une structure de données unique, typée et immuable pour les pas, le sommeil et le rythme cardiaque,  
**Afin d'** isoler complètement la logique de calcul de l'application des spécificités techniques des fabricants d'appareils et de fournir des interfaces de secours (`Fallback`) en cas de panne matérielle.

---

## 🏗️ Positionnement Architectural & Inversion de Contrôle

Le module `:core:biometric-api` se situe au niveau le plus bas de la couche de domaine partagée :
*   **Étanchéité stricte :** Il ne connaît aucun module fonctionnel visuel (`:feature`).
*   **Inversion de contrôle :** Il expose des interfaces et des structures contractuelles immuables que les modules d'infrastructure (comme `:feature:telemetry`) doivent implémenter et que les modules supérieurs (comme `:feature:analytics`) consomment.

---

## 🛠️ Critères d'Acceptation & Spécifications (Functional Design)

### [REQ-BIO-01] : Standardisation Structurelle du Sommeil Pivot (BR-BIO-01)
Toute charge utile (payload) représentant une session de sommeil capturée par l'application doit être découpée selon une nomenclature standardisée immuable, indépendamment des termes propriétaires des fabricants :
*   `Awake` (Éveil)
*   `Light` (Sommeil léger)
*   `Deep` (Sommeil profond)
*   `REM` (Sommeil paradoxal)
*   *Critère d'intégrité :* La somme des durées de ces quatre phases doit être rigoureusement égale à la différence temporelle brute entre l'horodatage de réveil et l'horodatage de coucher.

### [REQ-BIO-02] : Activation Automatique de l'Interface de Secours (BR-BIO-02)
Lorsqu'un module sollicite une série de données de santé (ex: le cumul des pas) et que le capteur principal est indisponible ou déconnecté, le contrat active son interface de secours :
*   *Critère 1 :* Le système applique la stratégie de substitution ordonnée de la règle `BR-BIO-02` en calculant la moyenne historique pondérée des 3 mêmes jours de la semaine sur les 3 semaines précédentes.
*   *Critère 2 :* La payload de secours générée injecte obligatoirement la métadonnée de traçabilité `IsFallback = True` afin de notifier de manière transparente les modules visuels en aval.

### [REQ-BIO-03] : Agrégation Obligatoire Haute Fréquence (Time-Buckets)
Pour préserver la bande passante réseau et éviter la saturation de la mémoire vive client lors du transit des données cardiaques denses (échantillonnage à la seconde) :
*   *Critère 1 :* Les données de fréquence cardiaque doivent être structurées sous forme de Blocs Temporels (`Time-Buckets`) d'une granularité maximale de 5 minutes.
*   *Critère 2 :* Chaque bloc transporte uniquement les trois indicateurs statistiques condensés du segment : `HeartRateMin`, `HeartRateMax` et `HeartRateAvg`.

---

## 💻 Spécification Technique de l'Interface (C# Layout Blueprint)

```csharp
// C# .NET Core Clean Architecture API Contract Layout
// Emplacement : PulsePath.Core.Contracts.Biometric.IBiometricApi.cs

namespace PulsePath.Core.Contracts.Biometric;

public interface IBiometricFallbackProvider
{
    /// <summary>
    /// Applique la règle de substitution BR-BIO-02 en cas d'absence de données réelles.
    /// </summary>
    Task<BiometricSeriesResponse> GetFallbackStepsAsync(Guid userId, DateTimeOffset targetDate);
}

public interface IBiometricQueryService
{
    /// <summary>
    /// Récupère la série temporelle normalisée d'une métrique biométrique.
    /// </summary>
    Task<IEnumerable<BiometricRecordDto>> GetMetricSeriesAsync(Guid userId, string metricType, DateTimeOffset start, DateTimeOffset end);
}
```
