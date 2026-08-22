# 🗺️ Feuille de Route Macro (Roadmap) — PulsePath Engine

Ce document présente la planification macro-temporelle, les jalons stratégiques et le séquençage des livraisons pour l'implémentation du moteur de performance métabolique PulsePath. La stratégie de lotissement est structurée pour minimiser le couplage de code et valider les briques fondamentales avant le déploiement des fonctionnalités algorithmiques avancées.

---

## 📅 Chronologie Macro des Jalons (Milestones)

L'exécution du projet est découpée en **4 Jalons Majeurs (M1 à M4)**, répartis sur un cycle de développement et de stabilisation de 7 mois.

[ M1 : Fondations ] ──► [ M2 : Capture & Routines ] ──► [ M3 : Intelligence Engine ] ──► [ M4 : Recette & Clôture ](Mois 1 - 2)              (Mois 3 - 4)                    (Mois 5 - 6)                    (Mois 7)
---

## 🛠️ Plan de Lotissement et Périmètre des Jalons

### Jalon 1 (M1 - M2) : Fondations Architecturales & Sécurité
**Objectif :** Valider l'étanchéité de l'infrastructure partagée (`:core`) et sécuriser les tunnels de communication ainsi que la persistance locale des données de santé au repos.

*   📦 **Modules Inclus :** `:core:biometric-api`, `:core:database`, `:core:network`, `:feature:auth`
*   🎯 **Livrables Clés :**
    *   Mise en place du contrat pivot immuable pour l'unification des structures de données biométriques.
    *   Chiffrement transparent de la base de données locale reposant sur une clé matérielle (`BR-DB-01`).
    *   Intercepteur de sécurité et cycle de vie de rotation automatique des jetons Bearer (`BR-NET-01`).
    *   Formulaires d'inscription et de connexion nominale avec critères de complexité (`BR-AUTH-01`).

### Jalon 2 (M3 - M4) : Collecte Passive, Capture Manuelle & Routines
**Objectif :** Déployer les interfaces de capture de données (manuelle et automatique), corréler les sessions temporelles et valider le tableau de bord principal.

*   📦 **Modules Inclus :** `:feature:dashboard-daily`, `:feature:onboarding`, `:feature:journal`, `:feature:telemetry`, `:feature:habits`, `:core:notification-service`
*   🎯 **Livrables Clés :**
    *   Interface unifiée du tableau de bord quotidien avec système de navigation calendrier (`:feature:dashboard-daily`).
    *   Conteneur de navigation composite en 9 étapes pour le profilage de départ de l'utilisateur.
    *   Pipeline d'ingestion asynchrone par lots des pas et de la fréquence cardiaque avec gestion des seuils de batterie (`BR-TEL-02`).
    *   Raccourcis de saisies incrémentales rapides dans le journal quotidien.
    *   Traqueur de sommeil (`Model 2`) avec application des pénalités d'éveils et compte à rebours de jeûne intermittent (`BR-HAB-02`, `BR-HAB-03`).
    *   Planificateur d'alarmes locales déconnectées et gestion des fenêtres de silence de nuit (`BR-NOT-02`).

### Jalon 3 (M5 - M6) : Intelligence Métabolique & Moteur Prédictif
**Objectif :** Activer la couche de calcul de haute précision, superposer les courbes analytiques et distribuer les recommandations du coaching virtuel.

*   📦 **Modules Inclus :** `:feature:goals`, `:feature:nutrition`, `:feature:workout`, `:feature:body-composition`, `:feature:analytics`, `:feature:coaching`, `:core:simulation-engine`
*   🎯 **Livrables Clés :**
    *   Moteur d'évaluation du TDEE réel empirique et lissage par moyenne mobile centrée sur 7 jours (`BR-SIM-02`, `BR-ANL-01`).
    *   Algorithme de projection métabolique non linéaire avec application du ralentissement adaptatif (`BR-SIM-03`).
    *   Planificateur de menus avec filtres d'exclusions d'allergènes et validation de l'équilibre calorique d'un aliment (`BR-NUT-01`).
    *   Carnet de musculation calculant le tonnage effectif et ajustant dynamiquement la jauge d'eau (`BR-WRK-01`).
    *   Génération de fiches d'insights en triptyque (Constat-Cause-Action) et régulation de la densité UI (`BR-COA-01`).

### Jalon 4 (M7) : Gamification, Stabilisation & Recette Globale
**Objectif :** Injecter le moteur d'engagement, exécuter la suite de tests fonctionnels unitaires/croisés et finaliser la documentation de clôture.

*   📦 **Modules Inclus :** `:feature:gamification`, Écosystème Global (Phase 4 & 5)
*   🎯 **Livrables Clés :**
    *   Moteur d'attribution d'XP sur courbe quadratique, déblocage des badges et plafond anti-triche (`BR-GAM-01`, `BR-GAM-03`).
    *   Exécution complète du Cahier de Recette sur simulateurs et environnement de staging.
    *   Résolution des anomalies critiques documentées dans le Bug Tracker.
    *   Mise à jour et livraison du Guide de Formation Utilisateur et des fiches support.

---

## 📊 Matrice d'Avancement des Sprints (Statut Réel)

| Jalon | Sprint | Objectif du Sprint | Statut | Risques Associés |
| :---: | :---: | :--- | :---: | :--- |
| **M1** | SP-01 | Initialisation du Contrat Pivot Biométrique et du Client Réseau HTTP | 🟢 Complété | Aucun |
| **M1** | SP-02 | Chiffrement de la Base de Données au repos et Inscription Sécurisée (US-06) | 🟢 Complété | Complexité de l'isolation du Hardware Keystore |
| **M2** | SP-03 | Cinématique de l'Onboarding Composite et Ingestion Passive IoT | 🟢 Complété | Abandon utilisateur si parcours jugé trop long |
| **M2** | SP-04 | Implémentation du Tableau de Bord Quotidien et du Traqueur de Sommeil (Model 2) | 🟢 Complété | Chevauchement des sessions multi-dates |
| **M3** | SP-05 | Intégration du Catalogue de Nutrition et Saisie du Tonnage de Force | 🟡 En Cours | Incohérence des fiches nutritionnelles tierces |
| **M3** | SP-06 | Moteur de Simulation Mathématique et Courbes de Tendances Lissées | ⚪ Planifié | Bruit de signal sur les pesées quotidiennes |
| **M4** | SP-07 | Distribution des Insights du Coaching Virtuel et Moteur de Gamification | ⚪ Planifié | Fatigue attentionnelle face aux notifications |
| **M4** | SP-08 | Campagne de Recette Globale, Résolution de Bugs et Clôture de Phase | ⚪ Planifié | Décalage des fenêtres de mise en production |

---

## 📈 Critères de Sortie et Go-NoGo Applicatifs

Pour autoriser le passage à une mise en production effective (Release), le système doit obligatoirement valider les indicateurs qualité suivants :
1.  **Couverture de Recette :** 100% des scénarios critiques définis dans le `CAHIER_DE_RECETTE.md` doivent afficher un statut **Pass**.
2.  **Densité des Défauts :** Zéro anomalie ouverte de sévérité `Bloquante` ou `Majeure` au sein du `REGISTRE_DES_ANOMALIES.md`.
3.  **Conformité Légale :** Validation signée par le DPO sur l'étanchéité des purges de comptes et l'anonymisation des notifications push (RGPD/RGPD-BE).
