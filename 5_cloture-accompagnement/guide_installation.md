# 📖 Guide de Formation & Manuel Utilisateur — Moteur PulsePath

Ce document constitue le guide officiel d'accompagnement et de prise en main de la plateforme PulsePath. Il est divisé en sections ciblant d'une part la vulgarisation des fonctionnalités pour les utilisateurs finaux, et d'autre part les fiches de diagnostic opérationnel pour les agents du support client.

---

## 👤 SECTION 1 : Guide d'Utilisation et d'Accueil (Client Final)

### 1. Maîtriser le Calendrier Temporel et les Modes d'Affichage
Le Tableau de Bord Quotidien (`:feature:dashboard-daily`) est votre tour de contrôle. Située en haut de votre écran, la frise de calendrier interactive vous permet de voyager dans le temps pour piloter votre stratégie métabolique :
*   **Consulter votre Historique (Jour J-N) :** En sélectionnant une date passée, l'application fige l'état de vos jauges et repas pour vous permettre d'auditer votre conformité. Vous pouvez modifier des lignes pour corriger des oublis de saisie.
*   **Piloter le Présent (Jour J) :** Affiche vos compteurs dynamiques alimentés en temps réel par vos capteurs ou vos raccourcis de saisies immédiates.
*   **Anticiper l'Avenir (Jour J+N) — *Mode Prévisionnel* :** Lorsque vous cliquez sur une date future, l'application bascule automatiquement en mode d'anticipation selon la règle `BR-DSB-01`. Vous visualisez vos repas programmés et vos cibles théoriques pour organiser votre semaine. Les boutons de saisies réelles (comme les pesées) y sont masqués pour interdire toute erreur de manipulation.

### 2. Comprendre le Compteur de Calories Restantes
Le grand compteur central de votre écran calcule en permanence votre budget énergétique vivant selon l'équation de la règle `BR-DSB-02` :
\[\text{Calories Restantes} = \text{Budget Cible} - \text{Total Consommé} + \text{Dépense Cardio Validée}\]
Si vous enregistrez ou synchronisez une séance d'activité physique (comme une course à pied) au cours de votre journée, PulsePath applique instantanément un crédit de calories supplémentaires à consommer sur votre jauge pour compenser l'énergie dépensée lors de votre effort.

### 3. Sécurisation de l'Accès et Verrouillage
Pour préserver le secret de vos données de performance, la création de votre compte impose un mot de passe d'au moins 12 caractères avec majuscule, minuscule, chiffre et caractère spécial (`BR-AUTH-01`). Si vous commettez **5 erreurs consécutives** lors de votre connexion, votre compte est automatiquement verrouillé pendant **15 minutes** par sécurité (`BR-AUTH-03`). Un e-mail d'alerte vous est immédiatement envoyé.

---

## 🔬 SECTION 2 : Comprendre les Dispositifs Physiologiques Automatiques

### 1. Pourquoi mon poids sur le graphique diffère-t-il de ma balance ?
Le corps humain subit des variations de poids quotidiennes brutales qui ne sont pas liées à une prise de gras ou une perte de muscle, mais à des mouvements d'eau (sel, stress, digestion). Pour vous éviter de fausses interprétations, l'application superpose deux trames sur vos graphiques :
*   **Le Poids Brut :** Le reflet exact et brut de vos pesées du matin.
*   **Le Poids Lissé :** Une moyenne mobile centrée sur 7 jours calculée par notre moteur statistique (`BR-ANL-01`) pour gommer le bruit hydrique et révéler votre réelle tendance de transformation tissulaire.

### 2. Le fonctionnement de la coupure automatique du Jeûne
Si vous utilisez le minuteur de jeûne intermittent (`:feature:habits`), le système surveille vos insertions alimentaires. Dès que vous enregistrez une prise de repas ou une collation dont la valeur calculée dépasse le seuil strict de **5 kcal**, le système applique la règle `BR-NUT-03` / `BR-HAB-03`. Le minuteur de jeûne est automatiquement arrêté à la minute précise de votre repas et enregistre votre temps d'abstinence réel, vous évitant toute action manuelle.

---

## 🛠️ SECTION 3 : Guide de Diagnostic et d'Écalade (Support Technique)

*Lorsqu'un utilisateur remonte un comportement jugé anormal, appliquez scrupuleusement la grille de diagnostic suivante avant toute escalade vers l'équipe d'ingénierie :*

```text
┌───────────────────────────────────────┬────────────────────────────────────────────────────────┐
│ Symptôme Signalé par l'Utilisateur    │ Contrôle Fonctionnel et Action Support Recommandée     │
├───────────────────────────────────────┼────────────────────────────────────────────────────────┤
│ L'écran se fige ou charge sans fin à  │ - Analyser les logs de l'API de télémétrie externe.    │
│ l'Étape 9 de l'Onboarding.            │   Une panne de serveur tiers peut bloquer le Launchpad.│
│                                       │ - Confirmer la conformité des bornes de la règle       │
│                                       │   BR-ONB-01 sur les mensurations (ex: Taille > 120cm). │
├───────────────────────────────────────┼────────────────────────────────────────────────────────┤
│ Le graphique de projection affiche    │ - Extraire le code d'état 'HorizonStatus' de l'API.    │
│ une "Stagnation Infinie".             │ - Si 'NonConvergent' (REQ-SIM-01), expliquer au client │
│                                       │   que ses repas récents contredisent sa cible de perte.│
├───────────────────────────────────────┼────────────────────────────────────────────────────────┤
│ Les boutons d'ajouts rapides (+250ml) │ - Vérifier la date sélectionnée sur l'écran.           │
│ ont disparu de l'écran d'accueil.     │ - Si la date est dans le futur, le comportement est    │
│                                       │   normal et conforme au Mode Prévisionnel (BR-DSB-01). │
├───────────────────────────────────────┼────────────────────────────────────────────────────────┤
│ Les alertes de coaching n'apparaissent│ - Vérifier le fuseau horaire enregistré dans le profil.│
│ pas sur le téléphone en soirée.       │ - Appliquer la règle BR-NOT-02 (Quiet Hours) : les     │
│                                       │   push non critiques sont bloqués entre 22h et 7h.     │
└───────────────────────────────────────┴────────────────────────────────────────────────────────┘
```
