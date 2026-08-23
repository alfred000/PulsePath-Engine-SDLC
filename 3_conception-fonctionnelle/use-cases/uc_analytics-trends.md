# 🎭 Modèle des Cas d'Utilisation (Use Cases) — Spécifications Fonctionnelles

Ce document centralise et formalise les Cas d'Utilisation (Use Cases - UC) de la plateforme PulsePath. Conformément aux meilleures pratiques de la Business Analysis moderne, les cas d'utilisation sont regroupés par modules et contextes fonctionnels étanches afin de garantir l'indépendance de la logique métier et de guider le développement technique.

---

## 🧭 1.Intelligence Données & Projections (`:core:simulation-engine` & `:feature:analytics`)

### [UC-SIM-01] : Calculer la Trajectoire Métabolique Prédictive
*   **Acteur Principal :** Système (Automatisé / Asynchrone)
*   **Description :** Pipeline de calcul d'arrière-plan analysant le comportement réel récent de l'utilisateur pour simuler sa courbe de poids à +30 jours.
*   **Déclencheur :** Échéance temporelle ou ouverture de l'onglet analytique par l'utilisateur.
*   **Flux Nominal :**
    1. Le moteur de simulation ouvre un thread d'exécution asynchrone isolé et extrait les historiques des 14 derniers jours.
    2. Le système applique le filtre d'isolation des anomalies de pesée (`BR-SIM-01`) pour nettoyer la courbe du bruit hydrique.
    3. L'algorithme calcule le TDEE réel empirique de l'individu via l'équation de transfert de masse tissulaire (`BR-SIM-02`).
    4. Le moteur génère la projection jour par jour en appliquant le coefficient de ralentissement métabolique adaptatif (`BR-SIM-03`) à partir du 21ème jour de déficit.
    5. Le système compile le vecteur de points prédictifs (Date, Poids Estimé, Variance) et l'enregistre en cache.
    6. Les modules visuels amont (`:feature:body-composition`) consomment la payload pour dessiner la courbe future.
*   **Flux Alternatif (Non-Convergence Métabolique) :**
    *   À l'étape 3, si le solde de la balance énergétique réelle est inverse ou neutre par rapport à l'objectif : Le moteur stoppe immédiatement l'itération, renvoie le statut `HorizonStatus = NonConvergent` et affecte une valeur infinie ou nulle à la date d'atteinte de la cible, déclenchant une alerte prioritaire dans le module `:feature:coaching`.
