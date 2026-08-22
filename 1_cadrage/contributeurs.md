# 👥 Registre des Contributeurs et Gouvernance Fonctionnelle — PulsePath

Ce document établit la matrice des responsabilités et cartographie l'implication des parties prenantes internes et des experts cliniques externes. La validation collective de ces contributeurs garantit la rigueur scientifique des algorithmes, l'ergonomie des interfaces et la conformité légale de la plateforme PulsePath.

---

## 🏛️ Matrice de Gouvernance et de Validation

Le moteur de PulsePath croise des données physiologiques et comportementales complexes. Pour éviter toute dérive algorithmique ou faille de sécurité, chaque règle de gestion (BR) ou contrat de données est soumis à un flux d'approbation strict :

[ Expert Externe / Conseil ] ──► (Validation Scientifique)│▼[ Product Owner / BA ]       ──► (Spécification & Logique) ──► [ Architecture Technique ]│▼[ Expert DPO / Conformité ]  ──► (Validation Légale & Sécurité)

---

## 👥 Répertoire des Parties Prenantes

### 1. Experts Métiers Cliniques & Scientifiques (Externes)

#### 🩺 Docteur en Physiologie Énergétique & Métabolisme
*   **Périmètre d'implication :** Validation des modèles d'adaptation métabolique et d'équilibre énergétique.
*   **Responsabilité Fonctionnelle :** 
    *   Co-conception et validation de l'équation de ralentissement adaptatif de la thyroïde lors de restrictions caloriques prolongées (`BR-SIM-03`).
    *   Validation clinique du calcul du TDEE réel moyen empirique (`BR-SIM-02`).

#### 🍎 Diététicien - Nutritionniste Clinique
*   **Périmètre d'implication :** Encadrement des structures nutritionnelles, macro-répartitions et profils énergétiques.
*   **Responsabilité Fonctionnelle :** 
    *   Validation de la matrice de conversion énergétique des macronutriments (`BR-NUT-01`).
    *   Définition des profils de régimes types et des plafonds de sécurité d'apports caloriques minimaux (`BR-GOL-01`).
    *   Supervision des filtres d'exclusions et de détection des allergènes alimentaires (`US-12`).

#### 😴 Somnologue / Chronobiologiste
*   **Périmètre d'implication :** Traqueur de sommeil (`Model 2`) et alignements circadiens.
*   **Responsabilité Fonctionnelle :** 
    *   Validation fonctionnelle de la formule de calcul du taux d'efficacité du sommeil (`BR-HAB-02`).
    *   Définition des critères de pénalités forfaitaires appliqués aux micro-éveils nocturnes.
    *   Validation de la politique de préservation du sommeil et des fenêtres de silence (`BR-NOT-02`).

#### 🏋️‍♂️ Préparateur Physique / Kinésithérapeute du Sport
*   **Périmètre d'implication :** Programmation de l'activité physique, surcharges progressives et dépenses d'effort.
*   **Responsabilité Fonctionnelle :** 
    *   Validation de l'équation de calcul du volume de travail et du tonnage en musculation (`BR-WRK-01`).
    *   Validation des barèmes d'équivalents métaboliques (MET) pour l'estimation des dépenses cardiovasculaires (`BR-WRK-02`).

---

### 2. Équipe Produit & Ingénierie (Internes)

#### 👤 Business Analyst Principal (BA) & Product Owner (PO)
*   **Périmètre d'implication :** Cycle de vie global du projet, priorisation du backlog, ergonomie des interfaces et cinématiques de processus.
*   **Responsabilité Fonctionnelle :** 
    *   Rédaction, mise à jour et priorisation (MoSCoW) du Product Backlog global, incluant l'agrégateur de présentation visuelle du jour (`:feature:dashboard-daily`).
    *   Modélisation des chorégraphies de processus au standard BPMN textuel (Navigation calendrier, collisions de routines).
    *   Validation de la logique métier de calcul du reliquat calorique dynamique en temps réel (`BR-DSB-02`).
    *   Traduction des modèles mathématiques bruts en User Stories exploitables pour le développement.

#### 🛡️ Délégué à la Protection des Données (DPO) / Expert Conformité
*   **Périmètre d'implication :** Sécurité, confidentialité, secret médical et conformité réglementaire européenne (RGPD / RGPD-BE / HIPAA).
*   **Responsabilité Fonctionnelle :** 
    *   Validation fonctionnelle des processus de portabilité des données de santé (`US-16`).
    *   Audit et approbation du protocole strict de suppression définitive en cascade du compte utilisateur (`BR-PRF-03`).
    *   Validation de l'anonymisation des charges utiles réseau (Payload Striping) sur écran verrouillé (`BR-NOT-01`).

#### 💻 Architecte Solution & Ingénieurs Logiciels
*   **Périmètre d'implication :** Traduction technique des spécifications, gestion de l'état (State), infrastructure, réseau et persistance.
*   **Responsabilité Fonctionnelle :** 
    *   Application fonctionnelle du State Pattern client pour isoler les requêtes temporelles du calendrier du tableau de bord (`RSK-DSB-01`).
    *   Garantie de l'immuabilité et de la généricité du contrat pivot biométrique (`:core:biometric-api`).
    *   Validation de la politique de chiffrement transparent des pages au repos via clé matérielle (`BR-DB-01`).
    *   Implémentation des intercepteurs réseau pour le rafraîchissement transparent des jetons d'accès (`BR-NET-01`).

---

## 📈 Historique des Signatures et Approbations

Chaque modification majeure apportée aux spécifications fonctionnelles ou aux contrats inter-modules doit être enregistrée ci-dessous après validation formelle du comité de gouvernance :

| Version | Date | Module Impacté | Validé Par (Rôles) | Statut |
| :---: | :--- | :--- | :--- | :--- |
| **v1.0** | 12/03/2026 | `:core:biometric-api` | Architecte Solution, Somnologue | 🟢 Approuvé |
| **v1.1** | 04/04/2026 | `:feature:auth` | Business Analyst, DPO, Expert Sécurité | 🟢 Approuvé |
| **v1.2** | 22/05/2026 | `:feature:nutrition` | Product Owner, Diététicien Externe | 🟢 Approuvé |
| **v2.0** | 19/08/2026 | `:core:simulation-engine` | Mathématicien, Physiologiste Métabolique | 🟢 Approuvé |
| **v2.1** | 22/08/2026 | `:feature:dashboard-daily` | Product Owner, Business Analyst, Architecte | 🟢 Approuvé |
