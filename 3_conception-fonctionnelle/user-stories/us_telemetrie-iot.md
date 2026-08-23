# User Story [US-11] : Collecte Passive en Arrière-plan, Ingestion IoT & Batterie
**Priorité (MoSCoW) :** Must Have  
**Module associé :** `:feature:telemetry` (Collecte de Télémétrie)

### Description
**En tant que** membre équipé d'appareils de santé connectés (montres, balances intelligentes),  
**Je veux** que mes pas réels, ma fréquence cardiaque et mes données de sommeil soient collectés automatiquement en tâche de fond lorsque l'application est fermée,  
**Afin de** maintenir mon tableau de bord quotidien et mes indicateurs de routines à jour sans effort de saisie manuelle.

---

## 🏗️ Alignement Architectural & Flux de Ingestion

Le module `:feature:telemetry` sert de pipeline asynchrone d'acquisition de données. Il n'applique aucune règle de présentation visuelle.
*   **Contrats Amont :** Il implémente et mappe les structures de données strictes définies par le module partagé `:core:biometric-api`.
*   **Acheminement Réseau :** Il délègue l'expédition sécurisée par lots (Batch Ingestion) au client HTTP commun `:core:network`.

---

## 🛠️ Critères d'Acceptation & Scénarios de Test (Format Gherkin)

### Scénario 1 : Ingestion par lots asynchrone nominale en arrière-plan
* **Étant donné que** j'ai accordé explicitement les autorisations de synchronisation en tâche de fond à l'application,
* **Et que** mon terminal dispose d'une connectivité réseau active et d'un niveau de batterie nominal (> 20%),
* **Quand** le gestionnaire de tâches d'arrière-plan système se réveille à l'échéance des 60 minutes,
* **Alors** le module scrute passivement les capteurs locaux ou agrégateurs tiers pour extraire les nouveaux relevés de pas,
* **Et** il compile ces événements au sein d'une payload unifiée et signée (Bulk Payload) transmise de manière transparente à l'API serveur,
* **Et** l'API persiste ces enregistrements épurés dans mes séries temporelles de données de santé.

### Scénario 2 : Résilience en mode hors-ligne et mémoire tampon locale
* **Étant donné que** mon appareil collecte mes données biométriques en tâche de fond,
* **Quand** le client réseau intercepte une coupure totale de connectivité internet (Mode Avion simulé),
* **Alors** le module de télémétrie bascule instantanément sur sa file d'attente locale de secours,
* **Et** mes pas et relevés cardiaques sont stockés de façon intègre au sein du stockage tampon chiffré temporaire de l'appareil,
* **Et** dès le rétablissement du réseau, le lot accumulé (jusqu'à 7 jours de rétention) est expédié de manière asynchrone vers l'API sans bloquer l'interface.

### Scénario 3 : Préservation de l'autonomie et suspension sur batterie faible (BR-TEL-02)
* **Étant donné que** l'ordonnanceur d'arrière-plan se réveille pour exécuter un cycle de synchronisation IoT,
* **Quand** le système détecte que la batterie de mon téléphone chute en dessous du seuil critique de `15%` et que l'appareil n'est pas branché,
* **Alors** l'application applique la politique d'économie d'énergie de la règle `BR-TEL-02`,
* **Et** la tâche de scrutation réseau et d'interrogation Bluetooth est immédiatement annulée pour préserver l'autonomie de mon appareil,
* **Et** les relevés matériels s'accumulent localement en attendant que mon terminal soit reconnecté à une source d'énergie.

---

## 🎛️ Spécifications Métrologiques des Charges Utiles (Data Contracts)

Pour valider le contrat pivot `:core:biometric-api`, chaque événement capturé par la télémétrie doit respecter un format de payload anonymisé strict avant sa transmission :

```json
{
  "EventId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "UserId": "93b2a3c4-1102-4b6e-88c1-7d2d3e4f5a6b",
  "MetricsType": "StepCount",
  "StartTime": "2026-08-22T14:00:00Z",
  "EndTime": "2026-08-22T15:00:00Z",
  "RawValue": 1250,
  "Metadata": {
    "DeviceManufacturer": "WearableVendorA",
    "IsSensorInput": true,
    "BatteryLevelAtLog": 42
  }
}
```
