# User Story [US-15] : Traqueur de Sommeil (Model 2), Jeûne Intermittent & Routines
**Priorité (MoSCoW) :** Must Have  
**Module associé :** `:feature:habits` (Suivi des Routines & Habitudes)

### Description
**En tant que** membre soucieux de sa récupération circadienne et de sa flexibilité métabolique,  
**Je veux** consigner mes fenêtres de sommeil (Model 2) et activer un compteur de jeûne intermittent en temps réel,  
**Afin de** valider mon alignement biologique quotidien et d'obtenir un score d'efficacité de récupération précis sans calcul mental.

---

## 🏗️ Alignement Architectural & Signaux Inter-Modules

Le module `:feature:habits` agit comme un agrégateur de comportements temporels. Il s'appuie sur une isolation stricte mais intercepte les signaux transversaux de la plateforme :
*   **Acquisition :** Il reçoit les phases de sommeil filtrées provenant du contrat pivot `:core:biometric-api`.
*   **Collision Métabolique :** Il écoute le bus de messages global pour détecter toute insertion calorique en provenance du module `:feature:journal` (Diary) ou `:feature:nutrition` afin de réguler la coupure du jeûne.

---

## 🛠️ Critères d'Acceptation & Scénarios de Test (Format Gherkin)

### Scénario 1 : Calcul automatique du Taux d'Efficacité du Sommeil (BR-HAB-02)
* **Étant donné que** j'ouvre le formulaire du Traqueur de Sommeil (Model 2) pour consigner ma nuit,
* **Quand** je renseigne une heure de coucher à `22:00` et une heure de réveil à `06:00` (soit 8 heures / 480 min au lit),
* **Et que** je déclare avoir subi `2` micro-éveils nocturnes au cours de la nuit,
* **Alors** l'API backend applique la pénalité forfaitaire de 15 minutes par éveil de la règle `BR-HAB-02`,
* **Et** le système valide la session avec un temps de sommeil net de 450 minutes,
* **Le tableau de bord** affiche instantanément un Taux d'Efficacité du Sommeil de `93.75%` (450 / 480 × 100).

### Scénario 2 : Imputation d'une nuit de sommeil à cheval sur deux journées calandres
* **Étant donné que** je commence ma session de sommeil le lundi soir à `23:00` (Jour J),
* **Quand** je me réveille le mardi matin à `07:00` (Jour J+1) et que la session est clôturée,
* **Alors** le système applique la règle de gestion de structure temporelle unifiée,
* **Et** l'intégralité du volume brut de la nuit (8 heures) ainsi que le score d'efficacité calculé sont imputés à la journée du réveil (le mardi, Jour J+1) sur les graphiques du module `:feature:analytics`.

### Scénario 3 : Interdiction de chevauchement de sessions de repos (BR-HAB-01)
* **Étant donné que** mon historique comporte déjà une nuit de sommeil validée de 22h00 à 06h00 pour la date courante,
* **Quand** je tente d'ajouter manuellement une nouvelle sieste qui intersecte cette plage (ex: de 05h00 à 07h00),
* **Alors** le validateur de l'interface client applique la contrainte d'exclusion mutuelle de la règle `BR-HAB-01`,
* **Et** la soumission du formulaire est rejetée tandis qu'un message d'alerte m'indique : "Cette plage horaire est en conflit avec une session de sommeil existante."

### Scénario 4 : Fermeture automatique du jeûne intermittent par collision alimentaire (BR-HAB-03)
* **Étant donné que** j'ai un compteur de jeûne intermittent actif et en cours d'exécution dans mon application,
* **Quand** j'enregistre une prise alimentaire dont la valeur énergétique calculée est de `250 kcal` (supérieure au seuil de 5 kcal de la règle `BR-HAB-03`) dans mon journal quotidien,
* **Alors** le module de nutrition émet un signal de rupture sur le bus de messages,
* **Et** le module des habitudes intercepte ce signal, arrête immédiatement le minuteur de jeûne en arrière-plan à la minute précise du repas, et enregistre la durée d'abstinence réelle accomplie.

---

## 🎛️ Spécifications des Éléments d'Interface (UI Outlines)

L'écran du Traqueur de Sommeil (Model 2) et du Jeûne centralise les minuteurs dynamiques et les compteurs de régularité circadienne :

+-----------------------------------------------------------------------+

|
[ Routines ]Sommeil (Model 2) & Jeûne Intermittent    
|
+-----------------------------------------------------------------------+
|
😴 TRAQUEUR DE SOMMEIL :                                              
|
| Heure de coucher : [ 22:00 ]       Heure de réveil : [ 06:00 ]       
|
|  Nombre d'éveils perçus : [ 2  ]                                      
|
|
⭐ Score d'efficacité calculé (Règle BR-HAB-02) : [ 93.75% ]          
|
+-----------------------------------------------------------------------+
|  
⏱️ CHRONOMÈTRE DE JEÛNE INTERMITTENT :                                
|
|Statut : [ 🟢 Jeûne en cours depuis 14h 12min ]                      
|
|Objectif cible : 16h 00min (Protocole 16:8)                          
|
|  
[ 🛑 Arrêter manuellement ]    
*Note: s'arrête automatiquement si repas 
|
+-----------------------------------------------------------------------+
