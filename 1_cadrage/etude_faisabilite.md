# ⚠️ Registre Centralisé des Risques Fonctionnels & Techniques — PulsePath

Ce registre recense, qualifie et documente les contre-mesures pour l'ensemble des risques fonctionnels, techniques et réglementaires identifiés sur la plateforme PulsePath. La mitigation de ces risques garantit la stabilité du système, la rétention utilisateur et la protection absolue des données de santé au repos.

---

## ⚖️ 1. Grille de Cotation et Criticité des Risques

Chaque risque est évalué selon deux indices pour déterminer sa criticité globale :
*   **Impact (De 1 à 4) :** 1 (Faible), 2 (Modéré), 3 (Élevé), 4 (Critique / Bloquant).
*   **Probabilité (De 1 à 4) :** 1 (Rare), 2 (Faible), 3 (Moyenne), 4 (Élevée / Fréquente).

$$\text{Criticité} = \text{Impact} \times \text{Probabilité}$$

*   **Zone Rouge (Score 9 à 16) :** **Haute** — Traitement prioritaire obligatoire avec contre-mesure automatisée.
*   **Zone Orange (Score 4 à 8) :** **Moyenne** — Surveillance continue et guards de validation logiciels.
*   **Zone Verte (Score 1 à 3) :** **Basse** — Acceptation résiduelle ou message d'erreur d'interface simple.

---

## 🛠️ 2. Tableau de Suivi et de Mitigation des Risques

### 🧭 Module Pivot : Vue d'Ensemble Quotidienne (`:feature:dashboard-daily`)

| ID | Description du Risque | Impact | Prob. | Criticité | Plan de Mitigation / Contre-mesure Fonctionnelle |
| :--- | :--- | :---: | :---: | :---: | :--- |
| **RSK-DSB-01** | **Désynchronisation temporelle lors du changement de date**<br>L'utilisateur clique sur une date passée et les compteurs affichent par erreur des données du jour courant. | 3 | 2 | **6 (Moyenne)** | Application stricte du *State Pattern* côté client. Le changement de date détruit l'ancien modèle de données en mémoire vive et force une relecture isolée indexée sur la date cible. |
| **RSK-DSB-02** | **Chute des performances de l'interface (Latence d'agrégation)**<br>Le chargement de l'écran principal est ralenti par les requêtes simultanées vers la nutrition, les habitudes et la balance. | 3 | 3 | **9 (Haute)** | Utilisation d'un contrat d'API d'agrégation unique (BFF Pattern / Facade) retournant un seul vecteur de données consolidé pour la journée demandée. |
| **RSK-DSB-03** | **Incohérence temporelle lors des voyages (Changement de fuseau)**<br>La navigation par calendrier décale les repas ou le sommeil sur la journée précédente ou suivante lors d'un vol long-courrier. | 2 | 3 | **6 (Moyenne)** | Indexation de toutes les requêtes du calendrier sur le fuseau horaire local stocké dans le profil de l'utilisateur (`:feature:profile`) et non sur l'horloge système brute de l'appareil. |

### 🔑 Sécurité, Accès et Persistance (`:feature:auth` & `:core:database`)

| ID | Description du Risque | Impact | Prob. | Criticité | Plan de Mitigation / Contre-mesure Fonctionnelle |
| :--- | :--- | :---: | :---: | :---: | :--- |
| **RSK-SEC-01** | **Interception des identifiants (Man-in-the-Middle)**<br>Les jetons de session ou les secrets d'accès sont capturés en transit réseau. | 4 | 1 | **4 (Moyenne)** | Utilisation stricte de protocoles de transport chiffrés (HTTPS/TLS 1.3). Implémentation obligatoire de mécanismes de sécurité HSTS au niveau de l'API backend. |
| **RSK-SEC-02** | **Fuite de données de santé au repos (Fichier corrompu/volé)**<br>Accès non autorisé au stockage persistant local contenant les identifiants ou métriques. | 4 | 1 | **4 (Moyenne)** | Hachage asymétrique et salage des mots de passe. Chiffrement complet des pages de la base de données locale au repos via l'algorithme AES-256 (`:core:database`). |
| **RSK-SEC-03** | **Attaque par force brute**<br>Tentatives répétées et automatisées de connexion sur un compte utilisateur pour deviner le mot de passe. | 2 | 4 | **8 (Moyenne)** | Verrouillage automatique temporaire du compte (15 minutes) après 5 échecs consécutifs (`BR-AUTH-03`). Rate limiting appliqué sur l'endpoint d'authentification. |

### 🚀 Parcours d'Accueil & Profilage (`:feature:onboarding`)

| ID | Description du Risque | Impact | Prob. | Criticité | Plan de Mitigation / Contre-mesure Fonctionnelle |
| :--- | :--- | :---: | :---: | :---: | :--- |
| **RSK-ONB-01** | **Abandon du parcours utilisateur (Churn)**<br>Le parcours d'onboarding en 9 étapes est jugé trop long ou intrusif par l'utilisateur. | 3 | 4 | **12 (Haute)** | Sauvegarde automatique de l'état temporaire à chaque validation d'étape. Permettre à l'utilisateur de quitter et de reprendre le parcours là où il s'est arrêté. |
| **RSK-ONB-02** | **Incohérence ou aberration des données biologiques**<br>L'utilisateur saisit des valeurs aberrantes (ex: poids de 450 kg ou âge de 4 ans) qui corrompent les modèles de calcul. | 3 | 2 | **6 (Moyenne)** | Implémentation de barrières de validation strictes (bornes minimales et maximales réalistes de la règle `BR-ONB-01`) bloquant le passage à l'étape suivante. |

### 📡 Collecte, Télémétrie & Routines (`:feature:telemetry` & `:feature:habits`)

| ID | Description du Risque | Impact | Prob. | Criticité | Plan de Mitigation / Contre-mesure Fonctionnelle |
| :--- | :--- | :---: | :---: | :---: | :--- |
| **RSK-TEL-01** | **Épuisement accéléré de la batterie de l'appareil**<br>La scrutation ou la synchronisation trop fréquente en arrière-plan vide les ressources énergétiques du terminal. | 3 | 3 | **9 (Haute)** | Implémentation de la règle `BR-TEL-02` modulant la fréquence de synchronisation par lots (Batch Ingestion) ou coupant le service sous le seuil critique de 15%. |
| **RSK-TEL-02** | **Perte de données en condition hors-ligne (Offline)**<br>L'utilisateur génère des métriques sans connectivité internet et l'application s'arrête de manière inattendue. | 3 | 2 | **6 (Moyenne)** | Utilisation d'une file d'attente de persistance locale temporaire chiffrée (Tampon local). Les données sont purgées uniquement après confirmation de réception de l'API. |
| **RSK-HAB-01** | **Chevauchement temporel des blocs de sommeil (Model 2)**<br>L'utilisateur ou le capteur insère une session de sommeil qui intersecte ou se superpose à un enregistrement existant. | 2 | 3 | **6 (Moyenne)** | Application du validateur d'exclusion temporelle mutuelle (`BR-HAB-01`) rejetant fermement la transaction d'écriture en cas de collision sur l'axe du temps. |

### 🍏 Moteur de Calcul & Projections (`:core:simulation-engine`)

| ID | Description du Risque | Impact | Prob. | Criticité | Plan de Mitigation / Contre-mesure Fonctionnelle |
| :--- | :--- | :---: | :---: | :---: | :--- |
| **RSK-SIM-01** | **Amplification d'erreur sur données bruitées (Outliers)**<br>Une pesée aberrante ou un écart hydrique ponctuel fausse la pente de la régression, projetant une date d'objectif fausse. | 3 | 3 | **9 (Haute)** | Application obligatoire de l'algorithme d'isolation des anomalies (`BR-SIM-01`) neutralisant les variations supérieures à $\pm 2.5\%$ en 24h au profit d'une valeur lissée. |
| **RSK-SIM-02** | **Anxiété utilisateur face aux projections métaboliques**<br>Une projection linéaire mathématique brute annonce une stagnation, décourageant l'utilisateur. | 2 | 3 | **6 (Moyenne)** | Introduction visuelle de bandes de tolérance de variance (intervalles de confiance) proportionnelles à la régularité des données saisies (`REQ-SIM-02`). |

---

## 📈 3. Protocole de Réévaluation des Risques

Ce registre est vivant et réévalué à la fin de chaque itération (Sprint Review) par le Business Analyst et le DPO. Si l'implémentation technique d'une contre-mesure est validée par un test d'intégration conforme (`Pass` dans le cahier de recette), l'indice de probabilité du risque associé est automatiquement dégradé d'un point au sein du tableau de suivi.

