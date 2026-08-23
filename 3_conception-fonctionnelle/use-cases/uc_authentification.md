# 🎭 Modèle des Cas d'Utilisation (Use Cases) — Spécifications Fonctionnelles

Ce document centralise et formalise les Cas d'Utilisation (Use Cases - UC) de la plateforme PulsePath. Conformément aux meilleures pratiques de la Business Analysis moderne, les cas d'utilisation sont regroupés par modules et contextes fonctionnels étanches afin de garantir l'indépendance de la logique métier et de guider le développement technique.

---

## 🧭 1.Contexte d'Accès & Inscription (`:feature:auth`)

### [UC-AUTH-01] : S'authentifier et Initialiser une Session Sécurisée
*   **Acteur Principal :** Utilisateur Enregistré
*   **Description :** Permet à l'utilisateur de valider son identité pour obtenir ses privilèges d'accès et déchiffrer son coffre-fort de données local.
*   **Pré-requis :** Le compte utilisateur est actif et non verrouillé.
*   **Flux Nominal :**
    1. L'utilisateur saisit son adresse électronique et son mot de passe sur l'écran de connexion.
    2. L'interface client valide localement le format des champs et transmet la payload chiffrée à l'API.
    3. L'API serveur compare l'empreinte cryptographique du mot de passe avec celle stockée au repos.
    4. L'API réinitialise le compteur d'échecs à zéro, émet le couple de jetons JWT (Access / Refresh) et injecte le cookie sécurisé.
    5. L'interface client stocke l'Access Token en mémoire vive (State) et lève les verrous de navigation (Guards).
*   **Flux Alternatif (Seuils d'Échecs Répétés - Force Brute) :**
    *   À l'étape 3, si le mot de passe est incorrect : L'API incrémente le compteur d'échecs. Si le compteur atteint 5 échecs consécutifs, le système applique la règle `BR-AUTH-03`, bascule le compte au statut `Locked` pendant 15 minutes, déclenche l'envoi d'un e-mail d'alerte et rejette l'accès avec un code HTTP 423.

---

