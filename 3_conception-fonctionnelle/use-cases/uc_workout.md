# 🎭 Modèle des Cas d'Utilisation (Use Cases) — Spécifications Fonctionnelles

Ce document centralise et formalise les Cas d'Utilisation (Use Cases - UC) de la plateforme PulsePath. Conformément aux meilleures pratiques de la Business Analysis moderne, les cas d'utilisation sont regroupés par modules et contextes fonctionnels étanches afin de garantir l'indépendance de la logique métier et de guider le développement technique.

---

## 🧭 1.Suivi de l'Effort Physique & Musculation (`:feature:workout`)

### [UC-WRK-01] : Enregistrer une Série Effective de Force
*   **Acteur Principal :** Utilisateur Authentifié
*   **Description :** Permet à l'utilisateur de consigner ses performances de musculation mouvement par mouvement afin de documenter sa surcharge progressive.
*   **Flux Nominal :**
    1. L'utilisateur ouvre son carnet d'entraînement et sélectionne un exercice.
    2. Il saisit le nombre de répétitions effectuées, la charge déplacée (kg) et coche la case de complétion.
    3. L'interface valide localement l'entrée, stocke la ligne dans le cache du *Mode Brouillon Actif* et lance le compte à rebours de récupération (Timer repos).
    4. Lors du clic sur "Terminer l'entraînement", le client compile la structure de la séance et l'envoie à l'API.
    5. Le serveur applique l'équation de la règle `BR-WRK-01` pour calculer le Tonnage Effectif de la séance en excluant les séries d'échauffement.
    6. Le système émet un signal de mise à jour qui incrémente automatiquement l'objectif d'eau du jour de +500 ml dans le module `:feature:goals` (`BR-GOL-02`).

---

