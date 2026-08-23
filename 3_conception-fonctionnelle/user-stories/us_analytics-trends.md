# User Story [US-28-29] : Rapports Périodiques, Courbes de Tendances & Modèle Prédictif
**Priorité (MoSCoW) :** Must Have  
**Module associé :** `:feature:analytics` (Rapports & Analyses)

### Description
**En tant que** membre soucieux de comprendre l'évolution réelle de ma composition corporelle et de valider mes progrès,  
**Je veux** visualiser mes données de santé sous forme de tendances lissées et obtenir des rapports de performance ainsi que des projections à 30 jours,  
**Afin d'** éliminer les fluctuations quotidiennes parasites (rétention d'eau) et de piloter ma stratégie métabolique avec un temps d'avance.

---

## 🏗️ Alignement Architectural & Couplage Analytique

Le module `:feature:analytics` est un moteur d'audit purement consommateur de données :
*   **Intégration du Suivi des Progrès :** Conformément à l'alignement architectural, la couche de suivi des progrès à long terme est **exclue** du module journal (`:feature:journal`) et centralisée ici pour isoler la saisie quotidienne de l'analyse statistique.
*   **Flux de Données Transverses :** Il extrait et croise de manière asynchrone les données de repas (`:feature:nutrition`), d'effort physique (`:feature:workout`) et d'habitudes circadiennes (`:feature:habits`) sans jamais modifier les bases de données d'origine.

---

## 🛠️ Critères d'Acceptation & Scénarios de Test (Format Gherkin)

### Scénario 1 : Lissage des pics de poids hydriques artificiels (BR-ANL-01)
* **Étant donné que** mon historique de poids réel affiche une moyenne stable à `80.0 kg` sur plusieurs jours,
* **Et que** suite à un excès passager de sodium, ma pesée brute affiche un pic subit de `82.0 kg` au jour $T$,
* **Quand** j'affiche le graphique d'évolution de ma composition corporelle avec le tracé du "Poids Lissée",
* **Alors** le système applique la formule de la moyenne mobile centrée sur 7 jours de la règle `BR-ANL-01`,
* **Et** la valeur calculée et projetée à l'écran pour le jour $T$ amortit l'écart pour afficher précisément `80.28 kg`, révélant ainsi la tendance tissulaire réelle.

### Scénario 2 : Résilience visuelle par interpolation linéaire des oublis de saisie (BR-ANL-02)
* **Étant donné que** je me suis pesé le lundi (`80.0 kg`) puis que j'ai oublié de remplir mon journal le mardi et le mercredi,
* **Quand** je me pèse à nouveau le jeudi en inscrivant `79.1 kg` dans mon journal quotidien,
* **Alors** l'algorithme statistique applique l'équation d'interpolation de la règle `BR-ANL-02` pour combler le vide,
* **Et** le graphique dessine une courbe continue et fluide en générant des points extrapolés cohérents à `79.7 kg` pour le mardi et `79.4 kg` pour le mercredi, évitant ainsi les ruptures visuelles ou les chutes à zéro.

### Scénario 3 : Génération du rapport de performance métabolique périodique
* **Étant donné que** l'application arrive à l'échéance de mon cycle hebdomadaire de suivi,
* **Quand** le système compile mon rapport récapitulatif de performance,
* **Alors** il croise mes variables quantitatives (cumul des déficits caloriques réels) avec mes indices de ressentis qualitatifs (fatigue et stress moyens constatés),
* **Et** il met en évidence la corrélation logique pour m'expliquer mes éventuelles baisses de force constatées dans le carnet d'entraînement physique.

### Scénario 4 : Modélisation et projection de courbe de poids à 30 jours (BR-ANL-03)
* **En tant que** membre en phase de perte de masse grasse,
* **Quand** j'active l'option de simulation prédictive de ma trajectoire physique future,
* **Alors** le moteur de simulation de la règle `BR-ANL-03` analyse l'équilibre de ma balance énergétique réelle des 14 derniers jours,
* **Et** l'interface visuelle projette ma courbe d'évolution probable sur les 4 prochaines semaines, adossée à une bande de confiance graphique (fourchette haute / basse) proportionnelle à la régularité de mes données.

---

## 🎛️ Spécifications des Éléments d'Interface (UI Outlines)

L'écran d'analyses superpose les tracés mathématiques et intègre des contrôles interactifs de masquage pour préserver la lisibilité de la composition corporelle :

+-----------------------------------------------------------------------+

|
[ Analyses ] Tendances, Bilans & Modèles Prédictifs   
|

+-----------------------------------------------------------------------+
|  
📊 ÉVOLUTION DE LA COMPOSITION CORPORELLE :                          
|
|  
Période : [ 7j | (30j) | 90j | Année ]                               
|
|                                                                       
|
|  
82.0 kg ------------------------x----------------------------------  
|
<-- Poids Réel (Pic)
|  
80.3 kg -------------------/--------------------------------------  
| 
<-- Poids Lissé (BR-ANL-01)
|  
79.0 kg --------------/-------------------------------------------  
|
|          
Lundi      Mardi      Mercredi   (Jeudi)    Vendredi         
|
|                                                                       
|
|  
Options :  [X] Afficher Poids Brut   
|  
[X] Afficher Tendance Lissée 
|
<-- Interrupteurs UI

+-----------------------------------------------------------------------+

|  
🔮 PROJECTION MÉTABOLIQUE À +30 JOURS (Règle BR-ANL-03) :            
|
|  
* Comportement actuel constaté : Déficit moyen réel de 450 kcal/jour.
|
|
* Poids projeté dans 4 semaines : 77.8 kg (± 0.4 kg de variance)
|

+-----------------------------------------------------------------------+
