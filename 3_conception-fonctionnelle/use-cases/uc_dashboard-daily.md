# 🎭 Modèle des Cas d'Utilisation (Use Cases) — Spécifications Fonctionnelles

Ce document centralise et formalise les Cas d'Utilisation (Use Cases - UC) de la plateforme PulsePath. Conformément aux meilleures pratiques de la Business Analysis moderne, les cas d'utilisation sont regroupés par modules et contextes fonctionnels étanches afin de garantir l'indépendance de la logique métier et de guider le développement technique.

---

## 🧭 1. Module Pivot : Tableau de Bord Quotidien (`:feature:dashboard-daily`)

### [UC-DSB-01] : Naviguer Temporellement sur la Frise Calendrier
*   **Acteur Principal :** Utilisateur Authentifié
*   **Description :** Permet à l'utilisateur de modifier la date de référence de l'application pour auditer ses performances passées ou anticiper ses cibles futures.
*   **Pré-requis :** L'utilisateur est connecté à son espace personnel.
*   **Flux Nominal :**
    1. L'utilisateur clique sur une date cible ($J \pm N$) ou utilise les flèches de navigation de la frise calendrier.
    2. L'interface client intercepte l'action, fige l'écran et déclenche un squelette de chargement.
    3. Le système interroge le service d'agrégation API pour extraire la charge utile unifiée du jour demandé.
    4. L'interface client valorise les jauges et les graphiques avec les données reçues.
    5. Le système libère l'écran et affiche la synthèse de la journée sélectionnée.
*   **Flux Alternatif (Date Future - Mode Prévisionnel) :**
    *   À l'étape 4, si $\text{Date Sélectionnée} > \text{Date Réelle}$ : Le système applique la règle `BR-DSB-01`. Il charge les structures de menus et d'entraînements programmés à l'avance, bascule en *Mode Prévisionnel* et masque ou désactive l'ensemble des boutons de saisies de données réelles (poids, hydratation, repas).
*   **Flux d'Exception (Panne Réseau) :**
    *   À l'étape 3, si l'API est injoignable : Le système bascule sur la persistance locale (`:core:database`), extrait la payload mise en cache et affiche un indicateur de synchronisation dégradée discret.

---

