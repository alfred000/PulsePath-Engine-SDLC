# Moteur de Simulation Mathématique [CORE-SIM] : Algorithmes de Calcul Prédictif
**Priorité (MoSCoW) :** Could Have  
**Module associé :** `:core:simulation-engine` (Moteur Computationnel Asynchrone)

### Description
**En tant qu'** architecte de la solution PulsePath,  
**Je veux** centraliser l'exécution des calculs prévisionnels et des projections de trajectoires physiques à long terme,  
**Afin de** consommer des séries temporelles multi-variables nettoyées et de fournir des vecteurs de tendances fiables pour alimenter les briques d'objectifs et de composition corporelle sans impacter les performances de l'interface utilisateur.

---

## 🏗️ Positionnement Architectural & Isolation Logique Stricte

Le module `:core:simulation-engine` est un bloc de calcul pur, apatride (stateless) à l'exécution et totalement dépourvu de couche de présentation (No UI) :
*   **Indépendance visuelle :** Il ne possède aucune dépendance vers des frameworks de rendu graphique. Il expose exclusivement des vecteurs de points mathématiques bruts.
*   **Fournisseur de services :** Il agit comme un moteur d'infrastructure de données pour les modules fonctionnels de pilotage stratégique (`:feature:goals`) et de graphiques corporels (`:feature:body-composition`).

---

## 🛠️ Critères d'Acceptation & Spécifications (Functional Design)

### [REQ-SIM-01] : Pipeline d'Analyse Asynchrone & Non-Convergence (REQ-SIM-01)
Le traitement des données historiques denses doit s'exécuter au sein d'un thread d'arrière-plan isolé pour ne jamais figer la navigation principale de l'utilisateur :
*   *Critère 1 :* Le moteur extrait de manière parallèle les historiques de repas (calories entrantes), d'efforts physiques (calories sortantes actives) et de composition corporelle sur les 14 derniers jours.
*   *Critère 2 :* Si le bilan de la balance énergétique actuelle contredit l'objectif configuré (ex: excédent calorique systématique alors que l'utilisateur vise une perte de poids), l'algorithme doit immédiatement couper court à la boucle de projection itérative et retourner un code d'état explicite `HorizonStatus = NonConvergent` afin d'éviter les boucles infinies ou les plantages mémoires.

### [REQ-SIM-02] : Algorithme de Filtrage des Anomalies de Pesée (BR-SIM-01)
Avant toute intégration d'un point de poids réel au sein de la matrice de projection, le moteur doit appliquer une unité de nettoyage de signal pour éliminer le "bruit" des variations hydriques passagères :
*   *Critère 1 :* L'algorithme évalue l'écart par rapport à la moyenne lissée des 3 jours précédents. Si la variation brute est supérieure à $\pm 2.5\%$ de la masse totale en moins de 24 heures, le point est qualifié d'anomalie.
*   *Critère 2 :* La valeur aberrante est neutralisée et remplacée par une valeur interpolée linéairement selon la règle `BR-ANL-02` pour éviter de fausser la pente de la régression linéaire finale.

### [REQ-SIM-03] : Équation du TDEE Réel Empirique (BR-SIM-02)
Pour s'affranchir des approximations théoriques des formules génériques, la vitesse métabolique de référence de l'utilisateur est calculée rétroactivement sur une fenêtre de 14 jours selon la formule de transfert de masse tissulaire :
$$\text{TDEE Réel Moyen} = \frac{\sum_{k=1}^{14} \text{Calories Consommées}_k - (\Delta \text{Masse Tissulaire} \times 7700)}{14}$$
*Où $\Delta$ Masse Tissulaire représente la variation nette du poids lissé (en kg) sur la période, considérant qu'un déficit cumulé de 7700 kcal correspond théoriquement à la perte d'un kilogramme de tissu.*

### [REQ-SIM-04] : Modélisation Non Linéaire du Ralentissement Métabolique (BR-SIM-03)
Pour coller à la réalité physiologique humaine, l'algorithme de projection itératif jour par jour doit moduler le métabolisme de base (BMR) lors des restrictions d'énergie prolongées :
*   *Critère 1 :* À partir du 21ème jour consécutif de déficit calorique supérieur à 300 kcal par rapport au TDEE, le système applique une décote logarithmique progressive régie par l'équation :
$$\text{BMR Adjusted}_t = \text{BMR Initial} \times (1 - \alpha \times \ln(t - 20))$$
*Où $t$ représente le nombre de jours cumulés de déficit et $\alpha$ est le coefficient de sensibilité métabolique individuel (fixé par défaut à 0.015).*

---

## 💻 Spécification Technique du Contrat (C# Layout Blueprint)

```csharp
// C# .NET Core Clean Architecture - Interface du Moteur Computationnel
// Emplacement : PulsePath.Core.Contracts.Simulation.ISimulationEngine.cs

namespace PulsePath.Core.Contracts.Simulation;

public interface ISimulationEngine
{
    /// <summary>
    /// Calcule de manière asynchrone le vecteur de points prédictifs et évalue la convergence de la trajectoire.
    /// </summary>
    /// <param name="userId">Identifiant unique du profil utilisateur.</param>
    /// <param name="horizonDays">Nombre de jours de projection demandés (ex: 30 jours).</param>
    /// <returns>Une structure unifiée contenant le statut d'horizon et la série temporelle de points projetés.</returns>
    Task<SimulationVectorResponseDto> ComputePredictiveTrajectoryAsync(Guid userId, int horizonDays);
}
```
