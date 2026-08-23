# 📂 Chorégraphie des Processus Métiers (To-Be) — PulsePath

Ce document modélise les flux opérationnels cibles au standard BPMN (Business Process Model and Notation) sous forme textuelle structurée. Ces chorégraphies cartographient les interactions asynchrones et synchrones entre l'Utilisateur, l'Interface Client (Frontend) et l'API Serveur (Backend).

---

## 🔐 1. Authentification, Contrôle d'Accès & Sécurité (BR-AUTH-03)

Ce processus décrit la cinématique de validation des credentials et la cinématique défensive de blocage temporaire automatique en cas d'attaque par force brute.

[ Couloir : UTILISATEUR ]
|
o (Début : Souhaite accéder à son tableau de bord sécurisé)
|
Saisir les identifiants de connexion (Email / Mot de passe)
|
v
[ Couloir : INTERFACE CLIENT ]
|
Valider la structure des champs locaux (Format email valide)
|
+---> [Champs Invalides] ---> Afficher l'erreur de format visuelle et bloquer la soumission
|
Transmettre la payload d'authentification chiffrée (HTTPS/TLS 1.3)
|
v
[ Couloir : API SERVEUR ]
|
Vérifier le statut de verrouillage du compte ciblé en base de données
|
+---> [Statut == 'Locked']

||
|v
|Rejeter la requête instantanément (Code HTTP 423 Locked)

||
|v
|[INTERFACE CLIENT] ---> Afficher : "Compte temporairement bloqué." -> (Fin : Échec)
|+---> [Statut == 'Active']
|
v
Appliquer l'algorithme de hachage sur le mot de passe soumis et comparer à l'empreinte au repos
|
+---> [Identifiants INCORRECTS]

||
|v
|Incrémenter le compteur d'échecs consécutifs en base de données

||
|+---> [Compteur d'échecs == 5]

|||
||v
||Basculer le statut du compte à 'Locked' (Pendant 15 minutes)
||Déclencher l'envoi asynchrone d'un e-mail d'alerte de sécurité
|||
||v
||[INTERFACE CLIENT] ---> Afficher message d'échec générique -> (Fin : Échec)

||
|+---> [Compteur d'échecs < 5]

||
|v
|[INTERFACE CLIENT] ---> Afficher message d'échec générique -> Retours
|+---> [Identifiants CORRECTS]
|v
Réinitialiser le compteur d'échecs à zéro (0)
Générer le couple Access Token (15 min) / Refresh Token (7 jours)
Transmettre les jetons chiffrés et configurer le cookie sécurisé
|
v
[ Couloir : INTERFACE CLIENT ]
|
Stocker l'Access Token en mémoire vive (State applicatif)
Lever les verrous de navigation (Guards) et rediriger automatiquement
|
v
[ Couloir : UTILISATEUR ]
|
O (Fin : Accès au Tableau de Bord accordé)

---

## 🚀 2. Parcours d'Accueil Composite & Initialisation (Launchpad)

Ce processus détaille la navigation séquentielle à travers l'interface parent-enfant de l'onboarding et le déclenchement du traitement global asynchrone final.

[ Couloir : UTILISATEUR ]|o (Début : Première connexion réussie post-inscription)|v[ Couloir : INTERFACE CLIENT (Conteneur Parent / Composants Enfants) ]|Initialiser le State temporaire de l'Onboarding|+---> Étape 1 : Afficher l'introduction pédagogique et valider les prérequis|+---> Étape 2 [US-07] : Saisir Sexe, Âge, Taille, Poids et Mensurations initiales|        ||        v (Validation stricte des plages de la règle BR-ONB-01)|+---> Étape 3 [US-08] : Sélectionner l'Objectif de performance (Perte, Gain, Maintien)|+---> Étape 4 [US-11] : Autoriser et coupler les capteurs IoT / Wearables (Optionnel)|+---> Étape 5 [US-15] : Définir les fenêtres de jeûne et les cibles de pas/sommeil|+---> Étape 6 [US-12] : Sélectionner le Régime, le nombre de convives et les exclusions|+---> Étape 7 [US-13] : Choisir la répartition d'entraînement hebdomadaire (Split)|+---> Étape 8 [US-14] : Confirmer le Niveau d'Activité et la formule Katch-McArdle|+---> Étape 9 : Cliquer sur le bouton de finalisation "Lancer mon profil" (Launchpad)|v[ Couloir : INTERFACE CLIENT ]|Désactiver l'écran, afficher un loader de calcul et envoyer la payload高度 consolidée|v[ Couloir : API SERVEUR (Traitement Asynchrone en Arrière-plan) ]|Ouvrir une transaction atomique en base de données locale chiffréeEnregistrer le profil biologique et initialiser le statut [IsOnboarded = Vrai]Calculer le BMR de référence (Katch-McArdle) et la dépense énergétique journalière (TDEE)Déclencher le planificateur pour compiler :1. Le premier cycle de menus personnalisé (sans les allergènes exclus)2. L'agenda d'entraînement physique de la semaine (Musculation / Cardio)Valider la transaction (Commit)Retourner le signal de succès (HTTP 201 Created)|v[ Couloir : INTERFACE CLIENT ]|Détruire définitivement le contexte visuel de l'OnboardingRediriger l'utilisateur vers l'espace Tableau de Bord personnalisé|v[ Couloir : UTILISATEUR ]|O (Fin : Plateforme configurée et prête pour le pilotage métabolique)
---

## 🧭 3. Chargement et Navigation Temporelle du Tableau de Bord Quotidien

Ce processus décrit la chorégraphie fonctionnelle et l'ajustement dynamique de l'affichage lorsque l'utilisateur utilise le calendrier pour naviguer dans le temps.

[ Couloir : UTILISATEUR ]|o (Début : L'utilisateur ouvre son application ou change la date sur le calendrier)|Sélectionner une date cible sur la barre de navigation temporelle (Jour J | Jour J-N | Jour J+N)|v[ Couloir : INTERFACE CLIENT (State Manager Centralisé) ]|Bloquer temporairement les interactions graphiques (Afficher squelette de chargement)Purger l'état mémoire vive de la journée précédenteÉmettre une requête asynchrone d'agrégation indexée sur la date sélectionnée et le fuseau de l'utilisateur|v[ Couloir : API SERVEUR / COMPOSANT D'AGRÉGATION (BFF) ]|Intercepter la demande temporelleOuvrir une lecture parallèle isolée sur les tables du stockage persistant :1. Extraire le budget de calories cibles et consommées (:feature:nutrition & :feature:goals)2. Récupérer le cumul des pas, l'eau bue et l'état du jeûne (:feature:habits)3. Extraire le dernier poids valide et le vecteur de tendance lissé (:feature:body-composition & :feature:analytics)|v[ Sous-système : ÉVALUATEUR DE PERSPECTIVE TEMPORELLE ]|Analyser la nature de la date sélectionnée par rapport à la date du jour réel|+---> [Date Sélectionnée dans le FUTUR (Jour J+N)] ---> Masquer les boutons de saisie réelle, mode prévisions|+---> [Date Sélectionnée dans le PASSÉ / PRÉSENT] ---> Autoriser l'accès aux formulaires d'ajustements|v[ Couloir : API SERVEUR ]|Compiler le lot d'indicateurs consolidé (DailyDashboardPayload)Retourner la structure de données unifiée (HTTP 200 OK)|v[ Couloir : INTERFACE CLIENT ]|Réceptionner la payload unifiée et valoriser le State graphique de la journéeInjecter les valeurs dans les jauges (Calories, Protéines, Glucides, Lipides, Eau, Pas)Dessiner le tracé local de la courbe de poidsDésactiver le squelette de chargement et rendre la main à l'utilisateur|v[ Couloir : UTILISATEUR ]|O (Fin : Synthèse de la journée cible entraînée et synchronisée au calendrier)
---

## 📡 4. Ingestion Asynchrone de la Télémétrie et Gestion de Batterie

Ce processus décrit le comportement passif de l'application en tâche de fond pour collecter les pas et la fréquence cardiaque tout en préservant l'autonomie de l'appareil.

[ Couloir : MATÉRIEL / WEARABLE / SYSTEME MOBILE ]|o (Départ : Une échéance temporelle de 60 minutes se déclenche en arrière-plan)|Émettre un signal d'exécution de tâche de fond vers l'application|v[ Couloir : INTERFACE CLIENT (Gestionnaire de Tâches d'Arrière-plan) ]|Intercepter l'événement d'infrastructureInterroger l'API système du terminal pour lire le niveau de batterie actuel|+---> [Niveau de Batterie < 15% ET l'appareil n'est pas branché]|        ||        v (Règle BR-TEL-02)|     Annuler la tâche réseau, maintenir les métriques dans la mémoire tampon chiffrée|     Planifier une nouvelle tentative au prochain cycle de charge ---> (Fin du cycle de fond)|+---> [Conditions Énergétiques Nominales (> 15% ou En Charge)]|vScruter les capteurs locaux ou les agrégateurs de santé tiersCompiler les enregistrements de pas et de rythme cardiaque en un Lot Unique (Bulk Payload)Vérifier la disponibilité de la connectivité réseau|+---> [Mode Hors-ligne Actif] ---> Stocker le lot dans le tampon local chiffré -> (Fin du cycle)|+---> [Mode En Ligne Actif]|vExpédier la charge utile signée vers l'API distante|v[ Couloir : API SERVEUR ]|Valider la structure de la payload vis-à-vis du contrat pivot (:core:biometric-api)Appliquer la matrice de préséance pour éliminer les doublons (Règle BR-TEL-01)Persister les métriques épurées dans les séries temporelles de la baseRetourner un accusé de réception positif (HTTP 201)|v[ Couloir : INTERFACE CLIENT ]|Réceptionner le signal de succès de l'APIPurger intégralement la mémoire tampon chiffrée localeÉmettre un signal d'état interne pour rafraîchir les jauges si l'écran est ouvert|vO (Fin : Données IoT ingérées et batterie du terminal utilisateur préservée)
---

## 🍏 5. Déclenchement Automatique de Fin de Jeûne par Collision Alimentaire

Ce processus modélise l'interopérabilité fonctionnelle et l'étanchéité modulaire lorsque l'ajout d'un repas dans le journal vient rompre de manière automatique un chronomètre de jeûne intermittent actif.

[ Couloir : UTILISATEUR ]|o (Début : L'utilisateur souhaite enregistrer une collation nocturne dans son journal)|Ouvrir le module ":feature:journal" (Diary)Rechercher un aliment, ajuster la portion et cliquer sur "Enregistrer le repas"|v[ Couloir : INTERFACE CLIENT ]|Calculer l'apport énergétique linéaire de la portion saisie (Règle BR-NUT-02)Enregistrer la ligne dans le State de la journée en coursTransmettre la payload de transaction nutritionnelle chiffrée|v[ Couloir : API SERVEUR ]|Persister l'enregistrement du repas dans le stockage lié à la date du jour|v[ Sous-système : UNITÉ DE CONTRÔLE DE COLLISION DES ROUTINES ]|Évaluer la valeur énergétique nette de la ligne de repas insérée|+---> [Valeur Énergétique <= 5 kcal] ---> Maintenir le compteur de jeûne actif -> (Fin du processus)|+---> [Valeur Énergétique > 5 kcal]|v (Règle BR-NUT-03 / BR-HAB-03)Intercepter la présence d'un chronomètre de jeûne actif dans le module ":feature:habits"Clôturer de manière automatique la fenêtre de jeûne à l'horodatage exact du repasCalculer la durée totale d'abstinence réellement accomplieEnregistrer la session de jeûne fermée avec le statut 'Completed' en base de donnéesÉmettre un événement métier sur le bus : [Event: FastingWindowClosedEvent]|v[ Couloir : INTERFACE CLIENT ]|Intercepter l'événement d'arrêt du jeûne en arrière-planArrêter le compte à rebours dynamique sur l'interface graphique des habitudesMettre à jour la jauge de suivi et rafraîchir le compteur de calories consommées du journal|v[ Couloir : UTILISATEUR ]|O (Fin : Repas comptabilisé, jeûne arrêté automatiquement et synchronisation visuelle immédiate)
