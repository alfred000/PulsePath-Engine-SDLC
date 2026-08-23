# 📖 Glossaire Métier Centralisé — Référentiel de Terminologie PulsePath

Ce glossaire constitue la source unique de vérité terminologique pour l'ensemble des parties prenantes du projet PulsePath (Experts Métiers, Business Analysts, Product Owners et Équipes d'Ingénierie). Il vise à unifier le langage de l'application et à éliminer toute ambiguïté lors de la traduction des concepts métaboliques en spécifications techniques.

---

## 🔬 1. Concepts Physiologiques & Métaboliques

### Métabolisme de Base (BMR - Basal Metabolic Rate)
Quantité d'énergie (exprimée en kilocalories par jour) minimale dont l'organisme humain a besoin pour assurer ses fonctions vitales au repos complet (respiration, circulation sanguine, régulation thermique, renouvellement cellulaire), calculée de manière personnalisée via la formule de Katch-McArdle (`BR-ONB-02`).

### Dépense Journalière Totale (TDEE - Total Daily Energy Expenditure)
Volume global d'énergie brûlé par un individu sur une période de 24 heures. Le TDEE englobe le Métabolisme de Base (BMR) majoré par l'index multiplicateur lié au niveau d'activité physique quotidien (`BR-ONB-03`) et par le coût énergétique des entraînements.

### Vitesse Métabolique Réelle (Metabolic Velocity)
Mesure empirique et non théorique de la dépense énergétique réelle d'un utilisateur. Elle est calculée rétroactivement sur une fenêtre glissante de 14 jours en croisant l'historique nutritionnel net avec la variation exacte de la masse tissulaire de l'individu (`BR-SIM-02`).

### Masse Maigre (Fat-Free Mass)
Masse globale de l'organisme de laquelle on a soustrait la totalité du tissu adipeux (graisse). La masse maigre regroupe les muscles squelettiques, l'eau intra et extracellulaire, les os et les organes vitaux. C'est la variable pivot de la formule de Katch-McArdle.

### Recomposition Corporelle
Processus métabolique consistant à modifier la structure de la composition corporelle en réduisant la masse grasse tout en maintenant ou en développant simultanément la masse musculaire sèche, stabilisant ainsi le poids global brut tout en optimisant la performance physique.

### Ralentissement Adaptatif (Metabolic Adaptation)
Mécanisme de défense et de survie hormonale de l'organisme humain. Soumis à une restriction calorique prononcée et ininterrompue, le corps réduit de manière non linéaire son métabolisme de base afin de limiter la baisse de ses réserves énergétiques adipeuses (`BR-SIM-03`).

### Équivalent Métabolique (MET - Metabolic Equivalent of Task)
Unité statistique standardisée permettant d'évaluer l'intensité d'un effort physique. 1 MET correspond à la dépense énergétique d'un individu au repos (environ 1 kcal/kg/heure). Les activités physiques sont indexées sur cette unité pour calculer les calories brûlées lors du cardio (`BR-WRK-02`).

---

## ⚙️ 2. Gestion des Routines & Navigation Écran

### Navigation Temporelle (Calendar Navigation)
Composant d'interface utilisateur sous forme de frise chronologique ou de calendrier interactif permettant de modifier dynamiquement la date de référence de l'application afin de consulter l'état du système dans le passé, le présent ou le futur (`:feature:dashboard-daily`).

### Mode Prévisionnel (Forward-Looking Board)
État spécifique de l'affichage du tableau de bord quotidien lorsque l'utilisateur sélectionne une date future, mettant en avant les structures de repas planifiées et les objectifs physiques théoriques tout en verrouillant la saisie de métriques réelles impossibles par nature (`BR-DSB-01`).

### Payload d'Agrégation Quotidienne (Daily Summary Packet)
Structure de données unifiée et optimisée regroupant en un seul message l'ensemble des indicateurs de performance, de routines et biologiques d'un utilisateur pour une date unique, évitant la multiplication des appels réseau (BFF Pattern / Facade).

### Journal Quotidien (Diary)
Interface centrale de saisie et de consultation chronologique regroupant, pour une date précise, l'ensemble des données d'entrées factuelles (eau, repas, poids, entraînements) et subjectives (notes, ressentis) fournies directement par l'utilisateur (`:feature:journal`).

### Fenêtre de Jeûne Intermittent
Intervalle de temps quotidien ininterrompu durant lequel l'utilisateur s'abstient de toute ingestion calorique (ex: protocole 16:8 comportant 16 heures de jeûne pour 8 heures d'alimentation), visant à optimiser la sensibilité à l'insuline et la flexibilité métabolique.

### Efficacité du Sommeil (Sleep Efficiency Rate)
Indicateur mathématique exprimé en pourcentage représentant le rapport entre le temps passé réellement endormi et le temps total passé au lit entre l'heure de coucher et l'heure de réveil, ajusté par une pénalité forfaitaire lors de micro-éveils nocturnes (`BR-HAB-02`).

### Split d'Entraînement
Méthode de planification de l'entraînement de force consistant à diviser le travail des différents groupes musculaires sur plusieurs jours de la semaine (ex: Push/Pull/Legs) afin de maximiser la tension mécanique et la récupération locale (`:feature:workout`).

### Tonnage Global
Indicateur quantitatif de charge de travail global utilisé en musculation, calculé en multipliant le nombre de séries complétées par le nombre de répétitions effectuées et par la charge déplacée, en excluant les séries d'échauffement (`BR-WRK-01`).

### Carte d'Insight (Coaching Card)
Composant d'interface autonome, dynamique et contextuel présentant à l'utilisateur un triptyque logique strict : un Constat factuel issu des données, une Cause corrélée par les variables transversales, et une Action corrective immédiate et mesurable (`BR-COA-02`).

### Courbe de Niveau (Level Scaling)
Modèle mathématique quadratique non linéaire définissant le nombre de points d'expérience (XP) requis pour franchir chaque palier de niveau d'engagement, assurant une progression de plus en plus difficile au fil du temps (`BR-GAM-01`).

---

## 🔒 3. Architecture Technique & Sécurité des Données

### Contrat Pivot Biométrique
Modèle de structure de données universel, standardisé et immuable au sein de la solution, agissant comme traducteur unique pour unifier les formats hétérogènes provenant de divers capteurs ou API externes (`:core:biometric-api`).

### Interface de Secours (Fallback Interface)
Mécanisme logique de sécurité applicative conçu pour prendre le relais de manière transparente lorsque la source de données principale (capteur ou API tierce) est défaillante, injectant une estimation calculée sur l'historique de l'utilisateur (`BR-BIO-02`).

### Chiffrement au Repos (Encryption at Rest)
Dispositif de sécurité cryptographique garantissant que les données sauvegardées sur la mémoire flash locale de l'appareil sont totalement illisibles sans l'utilisation d'une clé secrète AES-256 liée au conteneur matériel isolé du processeur (`BR-DB-01`).

### Cascade Transactionnelle
Mécanisme du système de stockage garantissant que lorsqu'une transaction est annulée ou qu'un profil parent est définitivement purgé (Droit à l'oubli), toutes les entités enfants dépendantes au sein de l'ensemble des modules sont détruites simultanément de manière propre et atomique.

### Ingestion par Lots (Batch Ingestion)
Stratégie de communication réseau consistant à regrouper plusieurs enregistrements de données au sein d'une seule transmission chiffrée périodique en arrière-plan, optimisant la bande passante et réduisant la consommation de la batterie du terminal (`BR-TEL-02`).

### Relance Exponentielle (Exponential Backoff)
Algorithme de résilience réseau qui augmente progressivement le temps d'attente entre chaque tentative de reconnexion successive lors d'un échec de communication HTTP transitoire, évitant de s'auto-bloquer ou de surcharger l'API serveur (`BR-NET-02`).

### Anonymisation de Charge Utile (Payload Striping)
Mesure de protection de la vie privée éliminant toutes les données médicales, biologiques ou nominatives explicites du texte d'une notification push lors de son transit par des serveurs tiers, les remplaçant par des indicateurs cryptographiques neutres décodés localement à l'ouverture sécurisée (`BR-NOT-01`).

