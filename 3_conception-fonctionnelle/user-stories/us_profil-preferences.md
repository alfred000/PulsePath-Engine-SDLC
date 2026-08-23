# User Story [US-10] : Configuration des Préférences Système, Affichage & RGPD
**Priorité (MoSCoW) :** Must Have  
**Module associé :** `:feature:profile` (Gestion du Profil & Configurations)

### Description
**En tant que** membre actif de la plateforme PulsePath,  
**Je veux** configurer mes préférences d'affichage (système métrique ou impérial, fuseau horaire, langue) et accéder aux options réglementaires de mes données personnelles,  
**Afin d'** adapter l'expérience visuelle de mes métriques de santé à mes habitudes tout en conservant le contrôle total sur la portabilité et l'effacement de mes informations.

---

## 🏗️ Alignement Architectural & Stockage Neutre

Pour éviter toute distorsion mathématique ou corruption lors du croisement des historiques au sein du moteur de calcul (`:core:simulation-engine`), ce module applique strictement la règle **`BR-PRF-02`** :
*   **Données au repos :** Toutes les valeurs de masse et de distance sont converties et stockées exclusivement en kilogrammes (kg) et en centimètres (cm) dans la base locale chiffrée.
*   **Données en présentation :** L'application du système métrique ou impérial s'effectue dynamiquement lors du rendu visuel de l'interface client (Frontend).

---

## 🛠️ Critères d'Acceptation & Scénarios de Test (Format Gherkin)

### Scénario 1 : Basculement dynamique des unités de mesure sans altération de la base (BR-PRF-02)
* **Étant donné que** mon profil possède un historique de poids enregistré avec la valeur neutre de `80.0 kg` en base de données,
* **Et que** mon interface est configurée par défaut sur le système métrique,
* **Quand** j'accède à mes paramètres de profil et que je sélectionne le système impérial (`lbs / pouces`),
* **Alors** le State applicatif client est mis à jour instantanément à l'écran,
* **Et** si je navigue vers mon tableau de bord quotidien, la jauge affiche mon poids converti à la volée soit `176.37 lbs`,
* **Et** toute nouvelle saisie de poids effectuée en livres (ex: 180 lbs) est convertie par le client réseau pour envoyer précisément `81.64 kg` vers l'API, respectant la base de référence immuable.

### Scénario 2 : Modification du fuseau horaire et impact sur l'ordonnancement
* **Étant donné que** je suis connecté à mon espace personnel et que je modifie mon fuseau horaire local,
* **Quand** j'enregistre ma nouvelle zone géographique (ex: Europe/Brussels),
* **Alors** le client transmet la payload de configuration chiffrée à l'API serveur,
* **Et** le service de notification (`:core:notification-service`) réaligne immédiatement le planificateur d'alarmes locales pour synchroniser les fenêtres de silence de nuit (`BR-NOT-02`) sur mes nouvelles coordonnées temporelles.

### Scénario 3 : Exportation et Portabilité des Données de Santé (RGPD)
* **Étant donné que** je souhaite auditer l'ensemble des informations collectées par l'application,
* **Quand** je clique sur l'action "Exporter mes données de santé" dans l'onglet confidentialité de mon profil,
* **Alors** le système compile de manière asynchrone un fichier texte structuré universel et standardisé (ex: JSON/CSV),
* **Et** le fichier généré intègre de façon intègre mon historique de pas, mes phases de sommeil (`:core:biometric-api`), mes journaux de repas et mes bilans d'entraînements.

### Scénario 4 : Suppression définitive et purge en cascade du compte (Droit à l'oubli)
* **Étant donné que** je décide de fermer définitivement mon espace PulsePath,
* **Quand** je clique sur l'option "Supprimer mon compte" et que je saisis le code de sécurité éphémère à 6 caractères reçu par courriel (`BR-PRF-03`),
* **Alors** l'API serveur déclenche une transaction de nettoyage globale en cascade avec un effacement non réversible sur les supports de stockage,
* **Et** mon compte est instantanément banni, l'accès à mes anciens identifiants est bloqué, et l'interface me redirige automatiquement vers l'écran d'accueil public public.

---

## 🎛️ Spécifications des Éléments d'Interface (UI Outlines)

L'écran de configuration segmente clairement les actions de préférences visuelles des options juridiques et de conformité :

+-----------------------------------------------------------------------+

|[ Mon Profil ]Préférences & Confidentialité|

+-----------------------------------------------------------------------+

|⚙️ CONFIGURATION DE L'AFFICHAGE :|
|Système d'unités :[ (X) Métrique (kg/cm)  |  ( ) Impérial (lbs/in) ] | <-- Règle BR-PRF-02
|Langue:[ Français (BE) ▾ ]|
|Fuseau Horaire:[ Europe/Brussels (UTC+02:00) ▾ ]|

+-----------------------------------------------------------------------+

|🔒 DONNÉES PERSONNELLES & SÉCURITÉ (RGPD) :|
|[ 📄 Télécharger mon historique complet (Fichier Structuré) ]| <-- Portabilité
||
|⚠️ ZONE CRITIQUE :|
|[ 🚨 Supprimer définitivement mon compte et purger mes données ]| <-- Droit à l'oubli (BR-PRF-03)

+-----------------------------------------------------------------------+
