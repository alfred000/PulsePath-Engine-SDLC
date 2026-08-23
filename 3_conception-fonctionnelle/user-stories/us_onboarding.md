# User Story [US-07-08] : Parcours d'Accueil Composite & Initialisation
**Priorité (MoSCoW) :** Must Have  
**Module associé :** `:feature:onboarding` (Parcours d'Accueil)

### Description
**En tant que** nouvel utilisateur authentifié sur PulsePath,  
**Je veux** remplir un formulaire séquentiel guidé en 9 étapes pour renseigner mes informations physiologiques, mes objectifs, mes exclusions et mes routines,  
**Afin que** le système initialise mon profil biologique, calcule mes dépenses de base et planifie mon premier cycle personnalisé d'entraînement et de nutrition.

---

## 🏗️ Spécification de l'Architecture Composite (Parent/Enfants)

Pour éviter la perte de données et fluidifier la navigation, le module applique un modèle d'architecture strict :
*   **Composant Parent (Conteneur) :** Responsable du maintien du State temporaire global, de l'affichage de la barre de progression macro et de la validation de la transition entre les fenêtres.
*   **Composants Enfants (Étapes) :** Formulaires autonomes isolés responsables de la capture de données spécifiques et de la validation immédiate des règles de formats.

---

## 🛠️ Critères d'Acceptation & Scénarios de Test (Format Gherkin)

### Scénario 1 : Persistance et maintien de l'état local lors des retours en arrière
* **Étant donné que** je suis au milieu du parcours d'onboarding sur l'interface client,
* **Et que** j'ai validé l'Étape 2 en saisissant mon poids à `80 kg` et mon âge à `30 ans`,
* **Quand** je navigue vers l'Étape 3 puis clique sur le bouton "Retour" du conteneur parent,
* **Alors** le composant parent recharge l'interface de l'Étape 2,
* **Et** les champs d'entrées numériques restituent mes données initiales de `80 kg` et `30 ans` sans perte d'état en mémoire vive.

### Scénario 2 : Blocage des données physiologiques hors-normes (BR-ONB-01)
* **Étant donné que** je suis sur l'Étape 2 dédiée au profil et aux mensurations,
* **Quand** je tente de saisir un âge de `14 ans` ou une taille de `100 cm`,
* **Alors** le validateur du composant enfant intercepte la non-conformité de la règle `BR-ONB-01`,
* **Et** le bouton "Suivant" du conteneur parent reste désactivé tandis qu'un message d'erreur visuel indique les bornes de validité requises (18 ans et 120 cm minimum).

### Scénario 3 : Finalisation nominale asynchrone sur le Launchpad (Étape 9)
* **Étant donné que** j'ai validé avec succès l'ensemble des 8 premières étapes de profilage,
* **Et que** je suis sur l'Étape 9 finale (Le Launchpad),
* **Quand** je clique sur le bouton d'action global "Lancer mon profil",
* **Alors** le conteneur parent fige les interactions à l'écran et affiche une animation de chargement,
* **Et** le système expédie la payload d'agrégation consolidée chiffrée vers l'API serveur,
* **Et** après le calcul et l'enregistrement asynchrone réussi de mes plans en base de données, l'application détruit le contexte d'onboarding et me redirige vers mon tableau de bord quotidien.

---

## 🎛️ Séquençage des 9 Étapes du Parcours (Functional Flow)

[ Étape 1 : Bienvenue ] ──────► Présentation pédagogique, validation des prérequis système.│[ Étape 2 : Profil ]    ──────► Saisie Sexe, Âge, Taille, Poids initial, Mensurations (BR-ONB-01).│[ Étape 3 : Objectifs ] ──────► Sélection exclusive : Perte de gras, Gain de muscle, Maintien.│[ Étape 4 : Télémétrie ] ─────► Couplage OAuth2 des capteurs (Optionnel, bouton "Passer").│[ Étape 5 : Habitudes ] ──────► Choix des fenêtres de jeûne (ex: 16:8), objectifs pas et sommeil.│[ Étape 6 : Nutrition ] ──────► Choix du Régime (Hyperprotéiné...), Convives, Favoris, Exclusions.│[ Étape 7 : Entraînement ] ───► Choix de la routine de force de référence (Split Push/Pull/Legs).│[ Étape 8 : Calibrage ] ──────► Validation Niveau d'Activité et confirmation Katch-McArdle (BR-ONB-03).│[ Étape 9 : Launchpad ] ──────► Déclenchement du traitement global asynchrone en arrière-plan.
---

## 📐 Spécifications de la Barre de Progression (UI Blueprint)

+-----------------------------------------------------------------------+| [═══■══════════════════════════════════════════════════════]  15%     | <-- Progression Parent| Étape 2 sur 9 : Profil biologique et Mensurations de référence        |+-----------------------------------------------------------------------+| Sexe : [ Homme | Femme ]              Âge : [ 30 ] ans                || Taille : [ 180 ] cm                   Poids : [ 80.0 ] kg             ||                                                                       || Tour de taille : [ 85 ] cm            Tour de hanches : [ 96 ] cm      | <-- Formulaire Enfant| Poitrine : [ 102 ] cm                 Cuisses : [ 58 ] cm             |+-----------------------------------------------------------------------+| [ Retour ]                                                [ Suivant ] | <-- Actions Parent+-----------------------------------------------------------------------+
