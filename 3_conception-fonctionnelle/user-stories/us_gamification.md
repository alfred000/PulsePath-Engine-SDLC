# User Story [US-30-32] : Moteur de Gamification, Points d'Expérience & Badges
**Priorité (MoSCoW) :** Could Have  
**Module associé :** `:feature:gamification` (Moteur de Récompenses)

### Description
**En tant que** membre engagé de la communauté PulsePath,  
**Je veux** accumuler des points d'expériences (XP) et débloquer des badges de réussite lors de la validation de mes habitudes,  
**Afin d'** obtenir une gratification visuelle continue qui renforce ma discipline comportementale et d'évaluer ma régularité métabolique à long terme.

---

## 🏗️ Alignement Architectural & Flux d'Événements

Le module `:feature:gamification` est entièrement découplé des interfaces de saisie principales. Il fonctionne de manière passive et asynchrone :
*   **Écoute des signaux :** Il s'abonne aux notifications transactionnelles et événements du bus de messages global émis par `:feature:journal` (validation des repas, hydratation), `:feature:habits` (sessions de jeûne, sommeil) et `:feature:workout` (complétion d'entraînements).
*   **Traitement de fond :** Le calcul des points s'exécute en arrière-plan afin de ne jamais ralentir le temps de réponse de la validation des formulaires utilisateurs.

---

## 🛠️ Critères d'Acceptation & Scénarios de Test (Format Gherkin)

### Scénario 1 : Passage de niveau nominal et équation de seuil quadratique (BR-GAM-01)
* **Étant donné que** mon profil affiche un Niveau 1 actif avec un solde actuel de `90 XP`,
* **Et que** l'équation de la règle `BR-GAM-01` fixe le seuil du Niveau 2 à exactement $100 \times (2 - 1)^2 = 100\text{ XP}$,
* **Quand** je valide une action dans mon journal qui m'octroie un gain standard de `15 XP`,
* **Alors** le moteur de gamification crédite mon solde pour atteindre un total de 105 XP,
* **Et** le système applique la règle de transition automatique pour faire basculer mon compte au **Niveau 2** (105 XP > 100 XP),
* **L'interface client** affiche instantanément une fenêtre modale de célébration de montée de niveau.

### Scénario 2 : Blocage des abus et application du plafond quotidien (BR-GAM-03)
* **Étant donné que** j'ai déjà cumulé le montant limite de `500 XP` aujourd'hui à la suite de nombreuses validations d'entraînements et de repas,
* **Quand** je tente d'enregistrer une nouvelle métrique répétitive dans mon journal pour récolter de l'expérience supplémentaire (+20 XP),
* **Alors** l'API backend intercepte la transaction et applique la barrière de sécurité anti-triche de la règle `BR-GAM-03`,
* **Et** l'attribution d'XP est rejetée pour la journée courante, maintenant mon solde bloqué à la valeur plafond (500 XP), sans altérer la sauvegarde de ma métrique de santé.

### Scénario 3 : Attribution automatique d'un badge de performance (BR-GAM-02)
* **Étant donné que** mon historique affiche 6 jours consécutifs de conformité parfaite sur mon objectif d'eau quotidien,
* **Quand** je valide mon hydratation pour le 7ème jour consécutif dans mon journal,
* **Alors** le système intercepte la complétion de la série temporelle et applique les critères de la règle `BR-GAM-02`,
* **Et** le badge permanent "Hydrate de carbone" est automatiquement associé à mon profil utilisateur,
* **Et** les points d'XP bonus liés à ce exploit unique me sont crédités de manière irréversible, en contournant le plafond quotidien de sécurité.

---

## 🎛️ Spécifications des Éléments d'Interface (UI Outlines)

L'onglet de gamification affiche la jauge d'engagement globale et grisera les distinctions non encore obtenues :

+-----------------------------------------------------------------------+
|   
[ Récompenses ] Niveaux du Compte & Badges Débloqués     
|

+-----------------------------------------------------------------------+

|  
🏆 SÉQUENCE DE PROGRESSION :                                         

|
|  
Niveau Actuel : [ Niveau 2 ]                                         
||  Jauge d'XP : [========■--------------------------] 105 / 400 XP (5%) | <-- Règle BR-GAM-01|  
* Prochain palier : Niveau 3 requis à 400 XP
|

+-----------------------------------------------------------------------+
|  
🏅 GALERIE DES BADGES DE PERFORMANCE (Règle BR-GAM-02) :             
|
|                                                                       
|
|[ 💧 Hydrate de carbone ]   [ 🏋️‍♂️ Surcharge Métab. ]  [ ⏱️ Chronos ]  
|
|( Débloqué ✔ )             ( Débloqué ✔ )         ( Grisé 🔒 )   
|
|7 jours d'eau cons.         +5% tonnage force       5 jeûnes parfaits

|

+-----------------------------------------------------------------------+
