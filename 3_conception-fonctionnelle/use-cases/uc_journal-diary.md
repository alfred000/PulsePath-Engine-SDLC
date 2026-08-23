# 🎭 Modèle des Cas d'Utilisation (Use Cases) — Spécifications Fonctionnelles

Ce document centralise et formalise les Cas d'Utilisation (Use Cases - UC) de la plateforme PulsePath. Conformément aux meilleures pratiques de la Business Analysis moderne, les cas d'utilisation sont regroupés par modules et contextes fonctionnels étanches afin de garantir l'indépendance de la logique métier et de guider le développement technique.

---

## 🧭 1.Ingestion Journalière & Routines (`:feature:journal` & `:feature:habits`)

### [UC-JRN-01] : Enregistrer des Métriques Quotidiennes (Cœur du Système)
*   **Acteur Principal :** Utilisateur Authentifié
*   **Description :** Permet à l'utilisateur de consigner manuellement ses apports ou ses mesures biologiques depuis l'interface du journal (Diary).
*   **Flux Nominal :**
    1. L'utilisateur sélectionne un bloc de saisie rapide (ex: le bouton d'hydratation `+250 ml`).
    2. L'interface client intercepte le clic et vérifie le respect des plafonds quotidiens (`BR-JRN-01`).
    3. Le système met à jour le cumul de la journée dans le State graphique et expédie la transaction à l'API.
    4. L'API applique la règle de préséance (`BR-TEL-01`) : la saisie manuelle écrase toute donnée automatique simultanée.
    5. Le système persiste la ligne, recalcule le reliquat calorique global de la journée (`BR-DSB-02`) et confirme visuellement l'action.
*   **Flux d'Exception (Collision Métabolique de Fin de Jeûne) :**
    *   À l'étape 4, si l'action est l'insertion d'un aliment dont la valeur est supérieure à 5 kcal : Le système applique la règle `BR-NUT-03` / `BR-HAB-03`. Il intercepte le chronomètre de jeûne actif dans le module `:feature:habits`, clôture de manière automatique la fenêtre d'abstinence alimentaire à la minute exacte de la prise, et émet l'événement `FastingWindowClosedEvent` sur le bus de messages.

---

