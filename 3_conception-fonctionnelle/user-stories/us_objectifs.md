# User Story [US-18-20] : Configuration des Objectifs Métaboliques & Hydratation Dynamique
**Priorité (MoSCoW) :** Must Have  
**Module associé :** `:feature:goals` (Gestion des Objectifs)

### Description
**En tant que** membre de la plateforme PulsePath,  
**Je veux** définir mon poids de forme cible ainsi que ma date butoir, et disposer d'un objectif d'hydratation recalculé automatiquement,  
**Afin que** le système encadre ma stratégie énergétique de manière sécurisée et ajuste mes besoins en eau en fonction de mes dépenses physiques réelles.

---

## 🏗️ Alignement Architectural & Interopérabilité

Le module `:feature:goals` fait office de pilote stratégique pour les couches de saisie quotidienne. Il n'agit pas de manière isolée :
*   **Pilotage Nutritionnel :** Ses calculs dictent les plafonds de macronutriments du module `:feature:nutrition`.
*   **Ajustement Glissant :** Il intercepte les séries temporelles du module `:feature:body-composition` pour alimenter le moteur de trajectoire à 7 jours (`RM-COR-01`).

---

## 🛠️ Critères d'Acceptation & Scénarios de Test (Format Gherkin)

### Scénario 1 : Blocage des objectifs de perte agressifs et dangereux (BR-GOL-01)
* **Étant donné que** mon profil affiche un poids actuel de `100.0 kg` et un TDEE calculé à `2500 kcal`,
* **Quand** je tente de configurer un objectif de perte de poids à `90.0 kg` avec une date butoir fixée à seulement 4 jours plus tard,
* **Alors** l'interface utilisateur évalue le rythme de variation hebdomadaire nécessaire,
* **Et** le système bloque immédiatement la soumission du formulaire pour non-respect du plafond de 1% de la règle `BR-GOL-01`,
* **L'écran** affiche une alerte bloquante : "Ce rythme de perte est trop agressif pour votre métabolisme. Veuillez allonger votre date butoir."

### Scénario 2 : Respect de l'apport calorique plancher absolu du système (BR-GOL-01)
* **Étant donné que** le système calcule mon budget énergétique cible à la suite d'un ajustement de trajectoire,
* **Quand** les mathématiques de l'algorithme poussent le calcul théorique du déficit à un niveau inférieur à `1200 kcal` pour mon profil féminin (ou `1500 kcal` pour un profil masculin),
* **Alors** le moteur applique le garde-fou absolu de la règle `BR-GOL-01`,
* **Et** mon budget quotidien affiché est automatiquement bloqué à la valeur plancher de sécurité de `1200 kcal` (ou `1500 kcal`), indépendamment de l'agressivité de l'objectif d'origine.

### Scénario 3 : Ajustement dynamique de l'hydratation de base par l'effort physique (BR-GOL-02)
* **Étant donné que** mon poids actuel de `80.0 kg` fixe mon objectif d'hydratation de base à `2800 ml` selon l'équation de masse $\text{Poids} \times 35$ (`BR-GOL-02`),
* **Et que** ma jauge affiche cette cible de départ en début de journée,
* **Quand** le module d'entraînement physique (`:feature:workout`) valide la complétion d'une séance de cardio d'une durée exacte de `60 minutes`,
* **Alors** le module diffuse un signal de complétion d'effort sur le bus de messages global,
* **Et** le module des objectifs intercepte ce signal pour appliquer la majoration de la règle `BR-GOL-02`,
* **Ma cible d'hydratation** du jour passe automatiquement à `3300 ml` (+500 ml) à l'écran pour compenser la sudation.

---

## 🎛️ Spécifications des Éléments d'Interface (UI Outlines)

L'écran de configuration des objectifs affiche de manière transparente les vitesses théoriques et les alertes physiologiques associées :

+-----------------------------------------------------------------------+

|   
[ Objectifs ]              
Stratégie Métabolique & Hydratation      
|

+-----------------------------------------------------------------------+
|  
🎯 COMPOSITION CORPORELLE CIBLE :                                    
|
|  
Poids actuel : 80.0 kg             
Poids cible souhaité : [ 75.0 ] kg 

|
|  
Date butoir de l'objectif : [ 30 / 09 / 2026 ▾ ]              
|
|
|
|
📉 Rythme estimé : -0.72 kg / semaine (Zone Sûre conforme BR-GOL-01) 
|
|  
🔥 Budget énergétique quotidien alloué : 1 650 kcal                  
|

+-----------------------------------------------------------------------+
|  
💧 HYDRO-RÉGULATION DYNAMIQUE :                                      
|
|
Objectif d'eau du jour : [ 3 300 ml ]                                 
|
|
* Base métabolique : 2800 ml
|
* Bonus effort physique : +500 ml
|
 <-- Règle BR-GOL-02

+-----------------------------------------------------------------------+
