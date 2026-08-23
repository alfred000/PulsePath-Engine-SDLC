# User Story [US-01.D] : Vue d'Ensemble Unifiée & Navigation Calendrier
**Priorité (MoSCoW) :** Must Have  
**Module associé :** `:feature:dashboard-daily` (Tableau de Bord Quotidien)

### Description
**En tant que** membre actif de la plateforme PulsePath,  
**Je veux** disposer d'un écran d'accueil centralisé qui regroupe mes calories, mes macronutriments, mes routines circadiennes et l'évolution de ma composition corporelle pour une date sélectionnée,  
**Afin de** piloter ma stratégie de performance métabolique au quotidien et d'auditer ma conformité temporelle d'un seul coup d'œil.

---

## 🛠️ Critères d'Acceptation & Scénarios de Test (Format Gherkin)

### Scénario 1 : Initialisation nominale du tableau de bord au jour courant
* **Étant donné que** je suis un utilisateur authentifié et profilé,
* **Et que** l'application charge l'écran d'accueil principal,
* **Quand** la page s'initialise par défaut à la date du jour réel de mon fuseau horaire,
* **Alors** le système sollicite le composant d'agrégation asynchrone pour collecter ma payload quotidienne,
* **Et** l'interface affiche mes indicateurs nutritionnels en calculant le reliquat d'énergie exact selon la règle `BR-DSB-02`,
* **Et** mes jauges de routines (pas réels, hydratation cumulée, sommeil) ainsi que mon dernier poids de corps sont mis à jour en temps réel.

### Scénario 2 : Navigation historique vers une date passée
* **Étant donné que** je suis sur l'écran du tableau de bord quotidien,
* **Quand** je sélectionne une date passée ($J-N$) sur la barre de calendrier interactive,
* **Alors** le gestionnaire d'état client (State) purge la mémoire vive de la journée précédente,
* **Et** le système charge l'état historique figé et immuable des métriques correspondant à cette date spécifique,
* **Et** les formulaires d'ajustements ou de saisies rapides restent accessibles pour me permettre de corriger des oublis a posteriori.

### Scénario 3 : Navigation prévisionnelle vers une date future (Mode Prévisionnel)
* **Étant donné que** je suis sur l'écran du tableau de bord quotidien,
* **Quand** je sélectionne une date future ($J+N$) sur la barre de calendrier interactive,
* **Alors** l'application active automatiquement le *Mode Prévisionnel* conformément à la règle `BR-DSB-01`,
* **Et** l'interface affiche mes cibles de calories théoriques et mes repas planifiés à l'avance,
* **Et** les boutons d'insertions de métriques réelles (comme "+250ml d'eau" ou les pesées) sont rendus invisibles ou désactivés pour interdire toute corruption temporelle.

### Scénario 4 : Recalcul dynamique du crédit énergétique par l'effort physique
* **Étant donné que** je consulte mon tableau de bord pour la journée en cours,
* **Et que** mon reliquat calorique affiche une valeur initiale basée sur mes repas,
* **Quand** une nouvelle séance d'entraînement cardiovasculaire de 300 kcal est validée dans le module `:feature:workout`,
* **Alors** le module d'entraînement diffuse un signal de mise à jour sur le bus de messages global,
* **Et** le tableau de bord intercepte ce signal pour appliquer instantanément l'équation de la règle `BR-DSB-02`,
* **Et** ma jauge de calories restantes s'incrémente immédiatement de 300 kcal à l'écran sans nécessiter un rechargement complet de la page.

---

## 🎛️ Spécifications des Éléments d'Interface (UI Outlines)

Pour respecter la charte standardisée du projet, ce composant d'interface utilisateur composite est découpé en briques visuelles autonomes alimentées par des flux asynchrones :

+-----------------------------------------------------------------------+

|[ < ]Lundi 22 Août 2026 (Fuseau Local)[ > ]|<-- Navigation Calendrier

+-----------------------------------------------------------------------+

||
|( 800 kcal )<-- Calories Restantes (Cible - Consommé + Cardio)|
||
|[Prot: 45g/150g][Gluc: 120g/200g][Lip: 35g/70g]|<-- Blocs Nutrition

+-----------------------------------------------------------------------+

||
|👟 Pas:8 500 / 10 000 💧 Eau : 1 750 / 3 000 ml|<-- Blocs Habitudes
|😴 Sommeil : 93.75% (Model 2)|

+-----------------------------------------------------------------------+

||
|⚖️ Poids Réel : 80.2 kg 📉 Tendance Lissée : 79.8 kg|<-- Blocs Composition

+-----------------------------------------------------------------------+
