# User Story [US-06] : Inscription & Connexion Sécurisée
**Priorité (MoSCoW) :** Must Have  
**Module associé :** `:feature:auth` (Gestion de l'Authentification)

### Description
**En tant qu'** utilisateur de la plateforme PulsePath,  
**Je veux** pouvoir créer un compte et m'authentifier de manière sécurisée,  
**Afin d'** accéder à mon espace personnel de suivi de performance métabolique tout en garantissant la confidentialité absolue de mes données de santé au repos.

---

## 🛠️ Critères d'Acceptation & Scénarios de Test (Format Gherkin)

### Scénario 1 : Inscription réussie avec un mot de passe conforme
* **Étant donné que** je suis un nouvel utilisateur sur la page d'inscription,
* **Et que** l'adresse email saisie n'est pas encore enregistrée dans le système,
* **Quand** je saisis une adresse email valide,
* **Et que** je saisis un mot de passe qui respecte tous les critères de complexité de la règle `BR-AUTH-01`,
* **Et que** je valide le formulaire d'inscription,
* **Alors** l'API backend crée mon compte utilisateur de manière sécurisée,
* **Et** mes secrets d'authentification et mots de passe sont stockés sous forme hachée et non réversible au repos (`BR-DB-01`),
* **Le système** m'affiche un message confirmant la réussite de mon inscription.

### Scénario 2 : Échec de l'inscription pour mot de passe non conforme
* **Étant donné que** je suis sur la page d'inscription,
* **Quand** je saisis une adresse email valide,
* **Et que** je saisis un mot de passe ne respectant pas les critères de la règle `BR-AUTH-01` (ex: moins de 12 caractères ou sans caractère spécial),
* **Alors** l'interface utilisateur empêche la validation du formulaire,
* **Et** un message d'erreur visuel dynamique m'indique explicitement les critères de complexité manquants.

### Scénario 3 : Échec de l'inscription pour email déjà existant
* **Étant donné que** je suis sur la page d'inscription,
* **Quand** je saisis une adresse email déjà associée à un compte actif dans le système,
* **Et que** je valide le formulaire avec un mot de passe conforme,
* **Alors** l'API backend refuse la création du compte et retourne un code HTTP 409 Conflict,
* **Et** l'interface utilisateur affiche un message d'erreur m'informant que cet identifiant est déjà utilisé, sans compromettre la sécurité des données existantes.

### Scénario 4 : Connexion réussie et émission des jetons d'accès
* **Étant donné que** je possède un compte valide, actif et déverrouillé,
* **Quand** je saisis mes identifiants corrects sur la page de connexion,
* **Et que** je valide le formulaire,
* **Alors** le système m'authentifie avec succès,
* **Et** il génère un mécanisme de session double (Access Token de 15 minutes et Refresh Token de 7 jours) conformément à la règle `BR-AUTH-02`,
* **Et** le jeton d'accès court est maintenu uniquement en mémoire vive (State applicatif client) tandis que je suis automatiquement redirigé vers mon tableau de bord quotidien.

### Scénario 5 : Échec de connexion et politique de verrouillage temporaire (Force Brute)
* **Étant donné que** je tente de me connecter à un compte utilisateur existant,
* **Quand** je saisis un mot de passe incorrect à 5 reprises consécutives,
* **Alors** le serveur applique immédiatement la politique de verrouillage de la règle `BR-AUTH-03`,
* **Et** le statut du compte passe en mode verrouillé (`Locked`) pour une durée stricte de 15 minutes,
* **Et** une notification d'alerte de sécurité est envoyée de manière asynchrone à l'adresse de contact,
* **Et** toute tentative de connexion ultérieure durant cette période est rejetée avec un code HTTP 423 Locked.

---

## 🎛️ Spécifications des Éléments d'Interface (UI Outlines)

L'écran d'authentification s'appuie sur les composants atomiques standardisés de la plateforme et expose des indicateurs de robustesse en temps réel :

+-----------------------------------------------------------------------+
|                             PulsePath                                 |


|[ Connexion | Inscription ]|

+-----------------------------------------------------------------------+
|Adresse E-mail :|
|[ utilisateur@email.com] |
||
|Mot de passe :|
|[ ************]|
||
|🔒 Indicateur de complexité (Règle BR-AUTH-01) :|
|[X] Min. 12 car. [X] Majuscule [X] Chiffre [X] Car. Spécial|
+-----------------------------------------------------------------------+

||
|[ S'inscrire / Se Connecter ]|
+-----------------------------------------------------------------------+
