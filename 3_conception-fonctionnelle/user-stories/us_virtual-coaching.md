# User Story [US-36-38] : Tableau de Bord du Coaching Virtuel & Cartes d'Insights Dynamiques
**Priorité (MoSCoW) :** Must Have  
**Module associé :** `:feature:coaching` (Coaching Virtuel)

### Description
**En tant que** membre guidé par la plateforme PulsePath,  
**Je veux** disposer d'un espace interactif épuré affichant des conseils, des suggestions et du contenu éducatif contextuel généré automatiquement à partir de mes données réelles,  
**Afin d'** adapter mes comportements physiques et nutritionnels quotidiens sans avoir à interpréter moi-même des graphiques de données complexes.

---

## 🏗️ Alignement Architectural & Flux d'Intelligence

Le module `:feature:coaching` agit comme une couche de restitution et d'affichage à haute valeur ajoutée. Il ne génère pas de données de santé brutes :
*   **Consommation asynchrone :** Il est alimenté de manière asynchrone par les synthèses mathématiques et prédictives compilées au sein de `:feature:analytics` et de `:core:simulation-engine`.
*   **Rendu visuel :** Tous ses composants graphiques (cartes interactives, tiroirs contextuels) héritent directement des contraintes de mise en page atomiques de `:core:design-system`.

---

## 🛠️ Critères d'Acceptation & Scénarios de Test (Format Gherkin)

### Scénario 1 : Structure fonctionnelle obligatoire d'une carte d'insight (BR-COA-02)
* **Étant donné que** l'engendrage de conseils est déclenché par le système,
* **Quand** une nouvelle recommandation est calculée et envoyée vers mon tableau de bord de coaching,
* **Alors** la structure de la carte d'insight doit obligatoirement respecter le triptyque logique de la règle `BR-COA-02`,
* **Et** le composant visuel restitue de façon intègre :
    1. **Le Constat factuel** basé sur les chiffres réels (ex: *"Votre volume de force a baissé de 10% hier"*).
    2. **La Cause corrélée** issue des variables transversales (ex: *"Votre journal indique une fatigue de 4/5 liée à une nuit écourtée"*).
    3. **L'Action corrective** immédiate et mesurable (ex: *"Avancez votre heure de coucher et buvez +500ml d'eau"*).

### Scénario 2 : Respect des plafonds de densité visuelle et de mise en page (BR-COA-01)
* **Étant donné que** le serveur a généré un lot de 4 recommandations distinctes de niveau "Conseil" pour mon profil,
* **Quand** j'ouvre l'interface de mon tableau de bord de coaching virtuel,
* **Alors** le système applique la barrière de mise en page de la règle `BR-COA-01`,
* **Et** l'écran affiche précisément les `3` premières cartes d'insights prioritaires, tandis que la 4ème carte est temporairement masquée et maintenue en file d'attente (Queue) en arrière-plan.

### Scénario 3 : Libération de la file d'attente lors d'un rejet de carte
* **Étant donné que** mon tableau de bord de coaching affiche son plafond réglementaire de 3 cartes d'insights simultanées,
* **Quand** je rejette la première carte visible à l'écran via un geste de balayage (swipe) ou en cliquant sur l'action "Masquer",
* **Alors** la carte sélectionnée disparaît de l'affichage avec une animation fluide,
* **Et** la 4ème carte de conseil maintenue en file d'attente est injectée de manière dynamique au bas de la liste visuelle, maintenant le nombre de sollicitations actives à sa valeur limite de 3.

### Scénario 4 : Priorisation absolue des alertes de sécurité critique (BR-COA-01)
* **Étant donné que** je consulte mes conseils de performance musculaire standards sur mon tableau de bord,
* **Quand** l'API backend intercepte une dérive biologique critique (ex: un déficit d'énergie sévère ou une déshydratation dangereuse),
* **Alors** le système applique les règles de tri et d'urgence de la règle `BR-COA-01`,
* **Et** une bannière d'Alerte de Sécurité Prioritaire s'affiche de manière bloquante au sommet de l'écran, grisant ou reléguant au second plan les conseils de confort tant que l'alerte n'a pas fait l'objet d'une lecture obligatoire.

---

## 🎛️ Spécifications des Éléments d'Interface (UI Outlines)

L'interface de coaching centralise les conseils dynamiques et intègre des tiroirs de lecture pour le micro-apprentissage :

+-----------------------------------------------------------------------+

|[ Coaching ] Conseils Métaboliques & Apprentissage|

+-----------------------------------------------------------------------+

|⚠️ ALERTE DE SÉCURITÉ PRIORITAIRE :|
|[ 🚨 Déficit énergétique trop agressif détecté (Règle BR-GOL-01) ]| <-- Alerte Bloquante

+-----------------------------------------------------------------------+

|  🧠 CARTES D'INSIGHTS ACTIVES (Maximum 3 - Règle BR-COA-01) :          |                                                                       ||  

+-----------------------------------------------------------------+  

||  | ⭐ TENDANCE : Récupération Musculaire Incomplète                 |  
||  | - Constat : Votre volume de force a chuté de 1440kg à 1200kg.   |  
||  | - Cause   : Votre nuit (Model 2) affiche une efficacité de 72%. |  | <-- Triptyque (BR-COA-02)|  

| - Action  : Visez une cible d'hydratation de 3300ml aujourd'hui.|  
||  |                                                                 |  
||  | [ 📄 En savoir plus (Physiologie) ]               [ ✖ Masquer ] |  
||  +-----------------------------------------------------------------+  ||                                                                       ||  +-----------------------------------------------------------------+  ||  
| ⭐ HABITUDE : Alignement Circadien Optimisé (Jeûne 16:8 OK)       |  
||  

+-----------------------------------------------------------------+  
|
+-----------------------------------------------------------------------+
