# User Story [US-26-27] : Tracker d'Activités Physiques, Musculation & Cardio
**Priorité (MoSCoW) :** Must Have  
**Module associé :** `:feature:workout` (Suivi de l'Entraînement)

### Description
**En tant que** membre de la plateforme PulsePath,  
**Je veux** consigner mes séances de musculation série par série et enregistrer mes activités cardiovasculaires,  
**Afin de** documenter ma surcharge progressive, d'estimer ma dépense énergétique ajoutée et de déclencher les bonus de récupération hydrique du système.

---

## 🏗️ Alignement Architectural & Flux d'Efforts

Le module `:feature:workout` alimente directement la balance énergétique et la planification de la récupération de la plateforme :
*   **Balance Énergétique :** Ses estimations de dépenses caloriques sont transmises au module `:feature:analytics` pour l'ajustement à long terme.
*   **Compensation Hydrique :** Dès la complétion d'un effort de plus de 30 minutes, il émet une notification interne forçant la majoration immédiate de la jauge d'eau du jour même dans le module `:feature:goals` (`BR-GOL-02`).

---

## 🛠️ Critères d'Acceptation & Scénarios de Test (Format Gherkin)

### Scénario 1 : Calcul automatique du Tonnage Effectif en musculation (BR-WRK-01)
* **Étant donné que** j'initialise un exercice de musculation (ex: Développé couché) au sein de mon carnet d'entraînement,
* **Quand** j'insère une Série 1 marquée comme "Échauffement" avec 10 répétitions à 40 kg,
* **Et que** j'insère une Série 2 effective avec 10 répétitions à 80 kg,
* **Et que** j'insère une Série 3 effective avec 8 répétitions à 80 kg,
* **Alors** le système applique l'équation de cumul de volume de la règle `BR-WRK-01` en excluant la série d'échauffement,
* **Le carnet d'entraînement** affiche un Tonnage Total de l'exercice égal à `1440 kg` $(10 \times 80) + (8 \times 80)$.

### Scénario 2 : Estimation de la dépense calorique d'une activité cardio par index MET (BR-WRK-02)
* **Étant donné que** mon profil utilisateur valide un poids de corps de de `70.0 kg`,
* **Quand** j'enregistre une séance d'endurance d'une durée exacte de `30 minutes` indexée sur la valeur de `10 MET` (Course à pied modérée),
* **Alors** l'API backend applique la formule métabolique neutre de la règle `BR-WRK-02` : $10 \times 70\text{ kg} \times (30\text{ min} / 60\text{ min})$,
* **Le bilan de ma séance** affiche rigoureusement une dépense énergétique de `350 kcal`, qui vient immédiatement s'ajouter à mon crédit de calories restantes sur mon tableau de bord quotidien (`BR-DSB-02`).

### Scénario 3 : Préservation des données de séance via le Mode Brouillon Actif
* **Étant donné que** j'exécute une séance d'entraînement physique et que je saisis mes performances au fil de l'effort,
* **Quand** l'application s'arrête brusquement ou que le téléphone s'éteint au milieu de l'activité (panne de batterie),
* **Alors** le système s'appuie sur le cache temporaire incrémental du mode brouillon local,
* **Et** dès le redémarrage de PulsePath, ma session active est entièrement restaurée avec l'ensemble des séries et répétitions déjà cochées, évitant toute perte d'historique de force.

---

## 🎛️ Spécifications des Éléments d'Interface (UI Outlines)

L'écran d'entraînement intègre le chronomètre de repos et sépare les performances de musculation point par point :

+-----------------------------------------------------------------------+

|[ Entraînement ]éance active : Routine Push (Split)    
|

+-----------------------------------------------------------------------+

| 
⏱️ CHRONOMÈTRE GLOBAL : 00:42:15      
|  
⏱️ REPOS SÉRIE : [ 01:28 ]   
|

+-----------------------------------------------------------------------+

| 🏋️‍♂️ EXERCICE 1 : Développé couché (Historique précédent : 4x10 @ 75kg) 
|
|[X] Série 1 (Échauff.) : [ 10 ] réps  x  [ 40 ] kg                   
|
|[X] Série 2 (Effective): [ 10 ] réps  x  [ 80 ] kg                   
|
|[ ] Série 3 (Effective): [ 8  ] réps  x  [ 80 ] kg  [✔ Cocher]       
|
|                                                                       
|
| 📈 Volume Exercise (Règle BR-WRK-01) : 1 440 kg                       
|

+-----------------------------------------------------------------------+

|🔥 BILAN DE SÉANCE ESTIMÉ :                                          
|
|Volume déplacé : 4 520 kg   
|Dépense énergétique : 150 kcal        
|
|[ 🏁 Terminer et Enregistrer l'Entraînement ]                        
|

+-----------------------------------------------------------------------+
