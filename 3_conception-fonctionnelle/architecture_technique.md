# 🗺️ Blueprint d'Architecture : Organisation des Modules

Ce document définit la structure modulaire de **PulsePath Engine**. L'application est découpée en 6 modules fonctionnels verticaux autonomes et découplés, interagissant exclusivement via les contrats d'interface de la couche `:core`.

---

## 🧱 1. Liste des Modules Réorganisés

### 🔐 Module 1 : Identité & Accès (`:module:identity`)
Ce module encapsule l'ensemble de la sécurité, des structures de profils et des états de session. Il isole les données utilisateur hautement sensibles et les flux d'authentification.
*   **`:feature:auth`** : Gère l'interface de connexion, la génération des jetons (tokens) et les contrôles de sécurité.
*   **`:feature:profile`** : Gère les paramètres utilisateur, les préférences et la suppression définitive du compte.

### 📥 Module 2 : Ingestion de Données & Saisie (`:module:tracking-core`)
Ce module agit comme le moteur d'acquisition du système. Il coordonne les flux automatisés des capteurs matériels et les couches de saisie manuelle de secours.
*   **`:feature:telemetry`** : Traite la diffusion en arrière-plan des données IoT et les connexions Bluetooth (BLE).
*   **`:feature:journal`** : Gère l'interface de saisie manuelle de secours et les notes textuelles quotidiennes.
*   **`:feature:habits`** : Suit les routines quotidiennes répétables (hydratation, sommeil, jeûne).

### 📊 Module 3 : Métriques de Santé & Physiques (`:module:health-metrics`)
Ce module possède les modèles de données structurels concernant la progression physique, les bases de données alimentaires et l'historique des entraînements.
*   **`:feature:body-composition`** : Gère les mesures anthropométriques et les objectifs de poids.
*   **`:feature:nutrition`** : Régit le suivi des repas, le catalogue de recettes et la journalisation calorique.
*   **`:feature:workout`** : Coordonne la création de programmes sportifs, la saisie des séries et les données GPS cardio.

### 🧠 Module 4 : Intelligence & Insights (`:module:intelligence`)
La couche analytique du système. Elle lit l'historique des métriques à travers l'application pour générer des graphiques et produire des retours virtuels automatisés (Lecture seule).
*   **`:feature:analytics`** : Traite les agrégations long terme et les graphiques de progression multi-axes.
*   **`:feature:coaching`** : Génère des conseils métaboliques adaptatifs et des astuces textuelles personnalisées.

### 🏆 Module 5 : Engagement Utilisateur (`:module:engagement`)
Piloté strictement par les notifications d'événements (events) provenant de tout l'écosystème, ce module gère la motivation de l'utilisateur et les boucles de progression.
*   **`:feature:gamification`** : Traite le suivi des récompenses, les niveaux d'expérience (XP) et l'attribution des badges.

### 🎭 Module 6 : Orchestration de la Présentation (`:module:orchestration`)
Le cœur de l'interface utilisateur. Ces fonctionnalités ne possèdent aucun schéma de base de données ; elles servent de coordinateurs de flux ou de tableaux de bord.
*   **`:feature:onboarding`** : Régit le tunnel de diagnostic et le questionnaire initial de santé.
*   **`:feature:dashboard`** : Agrège les vues enfants dans un tableau de bord quotidien centralisé en lecture seule.

---

## 🛠️ Règles de Communication Inter-Modules

Pour éviter tout couplage fort entre vos nouveaux modules, les règles architecturales suivantes doivent être strictement appliquées :

1.  **Isolation des Données** : Le module `:module:health-metrics` ne peut pas lire directement dans les tables de la base de données de `:module:identity`. Si le moteur de nutrition a besoin de l'âge biologique de l'utilisateur, il doit obligatoirement l'interroger via un contrat d'interface exposé dans `:core:biometric-api`.
2.  **Mécanisme de Secours (UC-TEL-06)** : Lorsque `:module:tracking-core` détecte une déconnexion Bluetooth (BLE) dans `:feature:telemetry`, il émet un événement via le système d'interface `IInputFallbackService` situé dans `:core:biometric-api`. La fonctionnalité `:feature:journal` capture cet événement et affiche immédiatement les cartes de saisie manuelle à l'écran.
3.  **Séparation des Services Noyaux (Core)** : Aucun module métier ne contient de chaînes de connexion à la base de données ni de fichiers de composants graphiques bruts. Ils consomment uniquement des interfaces abstraites comme `ISecureStorageProvider` (provenant de `:core:database`) ou des modèles de mise en page (provenant de `:core:design-system`).

