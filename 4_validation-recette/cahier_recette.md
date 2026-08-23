# 🧪 Cahier de Recette Fonctionnel — Suite de Validation Globale

Ce document rassemble l'ensemble des scénarios de tests fonctionnels permettant de vérifier la conformité du moteur PulsePath par rapport aux exigences du Product Backlog et aux règles de gestion (BR) établies.

## 📊 Matrice des Tests Fonctionnels

| ID Test | Module Ciblé | Objectif de Validation | Règle de Gestion Associée |
| :--- | :--- | :--- | :--- |
| **CT-DSB-01** | `:feature:dashboard-daily` | Basculement en Mode Prévisionnel sur date future | `BR-DSB-01` |
| **CT-DSB-02** | `:feature:dashboard-daily` | Recalcul dynamique du reliquat par l'effort physique | `BR-DSB-02` |
| **CT-AUTH-01** | `:feature:auth` | Robustesse et complexité des mots de passe à l'inscription | `BR-AUTH-01` |
| **CT-AUTH-02** | `:feature:auth` | Connexion nominale, émission et gestion des jetons JWT | `BR-AUTH-02` |
| **CT-AUTH-03** | `:feature:auth` | Blocage automatique sur attaque par force brute | `BR-AUTH-03` |
| **CT-ONB-01** | `:feature:onboarding` | Persistance et maintien de l'état local inter-étapes | Contexte Composite |
| **CT-ONB-02** | `:feature:onboarding` | Blocage des saisies physiologiques hors-normes | `BR-ONB-01` |
| **CT-ONB-03** | `:feature:onboarding` | Finalisation nominale asynchrone sur le Launchpad (Étape 9) | Processus global |
| **CT-PRF-01** | `:feature:profile` | Basculement dynamique des unités de mesure sans distorsion | `BR-PRF-02` |
| **CT-PRF-02** | `:feature:profile` | Sécurisation et double validation de la purge (Droit à l'oubli)| `BR-PRF-03` |
| **CT-GOL-01** | `:feature:goals` | Blocage des objectifs de perte agressifs et dangereux | `BR-GOL-01` |
| **CT-GOL-02** | `:feature:goals` | Ajustement dynamique de l'hydratation par l'effort | `BR-GOL-02` |
| **CT-TEL-01** | `:feature:telemetry` | Résilience et stockage tampon en mode hors-ligne | `BR-TEL-01` |
| **CT-TEL-02** | `:feature:telemetry` | Suspension de la scrutation et des transferts sur batterie faible | `BR-TEL-02` |
| **CT-JRN-01** | `:feature:journal` | Saisies incrémentales de l'hydratation et cumul en temps réel | `BR-JRN-01` |
| **CT-JRN-02** | `:feature:journal` | Gestion des conflits et priorité à la saisie manuelle directe | `BR-JRN-02` |
| **CT-HAB-01** | `:feature:habits` | Calcul automatique du taux d'efficacité du sommeil | `BR-HAB-02` |
| **CT-HAB-02** | `:feature:habits` | Interdiction de chevauchement de sessions de repos | `BR-HAB-01` |
| **CT-NUT-01** | `:feature:nutrition` | Calcul linéaire exact de l'apport d'une portion personnalisée | `BR-NUT-02` |
| **CT-NUT-02** | `:feature:nutrition` | Refus d'insertion d'un aliment aux valeurs incohérentes | `BR-NUT-01` |
| **CT-WRK-01** | `:feature:workout` | Calcul automatique du Tonnage Effectif en musculation | `BR-WRK-01` |
| **CT-WRK-02** | `:feature:workout` | Estimation de la dépense calorique cardio par index MET | `BR-WRK-02` |
| **CT-ANL-01** | `:feature:analytics` | Lissage des pics de poids hydriques par moyenne mobile centrée | `BR-ANL-01` |
| **CT-ANL-02** | `:feature:analytics` | Résilience visuelle par interpolation linéaire des oublis de pesée | `BR-ANL-02` |
| **CT-GAM-01** | `:feature:gamification` | Évaluation nominale des seuils de passage de niveau XP | `BR-GAM-01` |
| **CT-GAM-02** | `:feature:gamification` | Blocage des abus et application du plafond journalier | `BR-GAM-03` |
| **CT-BDC-01** | `:feature:body-composition`| Refus d'une répartition de masse tissulaire incohérente | `BR-BDC-01` |
| **CT-BDC-02** | `:feature:body-composition`| Isolation interactive des courbes d'affichage graphique | `BR-BDC-02` |
| **CT-COA-01** | `:feature:coaching` | Respect des plafonds de densité visuelle et de mise en page | `BR-COA-01` |
| **CT-COA-02** | `:feature:coaching` | Priorisation absolue des alertes de sécurité critique | `BR-COA-02` |
| **CT-SIM-01** | `:core:simulation-engine` | Détection et gestion de non-convergence (Horizon infini) | `REQ-SIM-01` |
| **CT-SIM-02** | `:core:simulation-engine` | Application de la décote métabolique adaptative non linéaire | `BR-SIM-03` |
| **CT-NOT-01** | `:core:notification-service`| Mise en attente et respect des heures de silence (Quiet Hours) | `BR-NOT-02` |
| **CT-NOT-02** | `:core:notification-service`| Routage dynamique Foreground vs Background et Deep Linking | `REQ-NOT-01` |

---

## 📋 Détail des Scénarios de Test Majeurs

### Scénario : CT-DSB-01 — Basculement en Mode Prévisionnel sur date future
* **Objectif :** Vérifier que le calendrier verrouille l'insertion de données réelles dans le futur.
* **Pré-requis :** L'application est ouverte sur la date du jour réel de l'utilisateur.

| Étape | Action attendue | Résultat attendu | Statut |
| :---: | :--- | :--- | :---: |
| **1** | Sur la frise calendrier, sélectionner la date du lendemain ($J+1$). | L'interface déclenche le chargement asynchrone des données de prévisions. | |
| **2** | Analyser l'état visuel général de l'écran. | L'application passe en *Mode Prévisionnel*. Les boutons "+ Ajouter un repas", "+ 250ml d'eau" et les champs de poids sont masqués ou désactivés (`BR-DSB-01`). | |

### Scénario : CT-AUTH-03 — Mécanisme de blocage sur Force Brute
* **Objectif :** Valider le basculement en mode défensif après des échecs répétés.
* **Pré-requis :** Le compte utilisateur cible est actif et déverrouillé.

| Étape | Action attendue | Résultat attendu | Statut |
| :---: | :--- | :--- | :---: |
| **1** | Saisir un mot de passe erroné sur un compte valide 5 fois de suite. | À la 5ème tentative, le serveur rejette la requête avec un code HTTP 423 Locked. | |
| **2** | Effectuer une 6ème tentative immédiate avec les **bons** identifiants. | La connexion reste refusée. L'interface affiche : "Compte temporairement bloqué. Réessayez dans 15 minutes." (`BR-AUTH-03`). | |

### Scénario : CT-HAB-01 — Calcul de l'Efficacité du Sommeil (Model 2)
* **Objectif :** Valider l'application de la pénalité sur les interruptions circadiennes.
* **Pré-requis :** L'utilisateur ouvre le formulaire de saisie de sommeil.

| Étape | Action attendue | Résultat attendu | Statut |
| :---: | :--- | :--- | :---: |
| **1** | Renseigner un coucher à `22:00` et un réveil à `06:00` (480 min au lit). Indiquer `2` éveils nocturnes. Valider. | L'API déduit 30 min de pénalité (2 × 15 min), établissant le sommeil net à 450 minutes. | |
| **2** | Consulter la carte de synthèse du sommeil validée. | L'interface affiche un Taux d'Efficacité du Sommeil de `93.75%` (450 / 480 × 100), conformément à la règle `BR-HAB-02`. | |

### Scénario : CT-NUT-02 — Refus d'un aliment aux macros incohérentes
* **Objectif :** S'assurer de la cohérence énergétique du catalogue.
* **Pré-requis :** L'utilisateur ouvre le formulaire de création d'un aliment personnalisé.

| Étape | Action attendue | Résultat attendu | Statut |
| :---: | :--- | :--- | :---: |
| **1** | Saisir un nom d'aliment et entrer : 10g Protéines, 10g Glucides, 10g Lipides. Entrer manuellement un total de `500 kcal`. | Le validateur calcule l'énergie réelle : $(10 \times 4) + (10 \times 4) + (10 \times 9) = 170\text{ kcal}$. 500 kcal est hors tolérance. | |
| **2** | Cliquer sur "Enregistrer l'aliment". | Le système refuse l'enregistrement, bloque la soumission et affiche : "Les valeurs énergétiques ne correspondent pas aux macronutriments saisis" (`BR-NUT-01`). | |

### Scénario : CT-SIM-01 — Détection et gestion de non-convergence
* **Objectif :** Empêcher les boucles infinies lors de comportements contradictoires.
* **Pré-requis :** L'utilisateur a un objectif de perte de poids mais son journal des 14 derniers jours affiche un excédent calorique systématique (+200 kcal/jour).

| Étape | Action attendue | Résultat attendu | Statut |
| :---: | :--- | :--- | :---: |
| **1** | Déclencher manuellement l'exécution de la simulation prédictive. | Le moteur de simulation calcule un solde énergétique positif contredisant l'objectif de perte. | |
| **2** | Inspecter la structure de la payload de réponse de l'API. | L'algorithme coupe le calcul, renvoie `HorizonStatus = NonConvergent` et le champ `EstimatedDaysToGoal` renvoie une valeur infinie ou nulle (`REQ-SIM-01`). | |

### Scénario : CT-NOT-01 — File d'attente et respect des Heures de Silence
* **Objectif :** Garantir l'absence de perturbations pendant le sommeil de l'utilisateur.
* **Pré-requis :** L'horloge du terminal affiche `23:30` (Fenêtre de silence active de 22h à 7h). Le moteur émet un conseil de coaching non critique.

| Étape | Action attendue | Résultat attendu | Statut |
| :---: | :--- | :--- | :---: |
| **1** | Envoyer l'événement de communication au service de notification. | Le service constate que l'heure locale matches la restriction. La notification est mise en attente (Queue) locale (`BR-NOT-02`). | |
| **2** | Analyser l'état du terminal mobile de l'utilisateur. | Le téléphone reste inerte (aucun son, aucune vibration, aucun push visible sur l'écran verrouillé). | |
| **3** | Avancer virtuellement l'horloge locale du terminal à `07:15` le lendemain matin. | L'ordonnanceur d'arrière-plan se réveille et traite la file d'attente des messages stockés. | |
| **4** | Consulter le centre de notifications de l'appareil. | L'alerte est délivrée de manière anonymisée à l'écran : "PulsePath : Un nouvel insight métabolique est disponible" (`BR-NOT-01`). | |

