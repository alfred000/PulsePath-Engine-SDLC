# 🐛 Registre des Anomalies (Bug Tracker Functional) — PulsePath

Ce registre assure la traçabilité, la qualification, le tri et le suivi de la résolution des écarts constatés entre le comportement attendu (défini dans le Cahier de Recette) et le comportement réel observé lors des phases de validations fonctionnelles.

---

## ⚖️ 1. Grille de Qualification (Sévérité vs Priorité)

Pour garantir un cycle de traitement efficace de l'assurance qualité (QA), chaque défaut est qualifié selon deux critères distincts :

*   **Sévérité (Impact technique sur le système) :** 
    *   `Bloquante` : Arrêt total du système, fuite de données confidentielles ou faille de sécurité majeure.
    *   `Majeure` : Fonctionnalité clé indisponible ou calcul métabolique faussé sans solution de contournement.
    *   `Mineure` : Dysfonctionnement ergonomique localisé ou simple anomalie cosmétique/visuelle.
*   **Priorité (Urgence de traitement métier) :** 
    *   `Haute` : Correction immédiate requise avant la clôture de l'itération en cours.
    *   `Moyenne` : À corriger avant la fin du cycle de stabilisation et la mise en production.
    *   `Basse` : Correction optionnelle, planifiée ou reportée sur le backlog à long terme.

---

## 🛠️ 2. Tableau de Suivi des Anomalies Qualifiées

| ID Anomalie | Scénario Lié | Description du Défaut Constaté | Sévérité | Priorité | Statut | Plan de Correctif / Résolution Fonctionnelle |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **BUG-DSB-01** | `CT-DSB-01` | **Boutons de saisie actifs en date future**<br>Lors de la navigation sur le calendrier à J+1, les raccourcis d'hydratation restent visibles, violant la règle `BR-DSB-01`. | **Majeure** | **Haute** | **Corrigé** | Injection de la contrainte d'invalidation temporelle au niveau du State Manager pour forcer le verrouillage du Mode Prévisionnel. |
| **BUG-AUTH-01** | `CT-AUTH-01` | **Validation de mot de passe trop permissive**<br>Le système valide l'inscription avec un mot de passe de 12 caractères ne contenant aucun chiffre, violant la règle `BR-AUTH-01`. | **Majeure** | **Haute** | **Corrigé** | Ajustement et renforcement de l'expression régulière (Regex) globale de validation sur l'interface client et l'API serveur. |
| **BUG-AUTH-02** | `CT-AUTH-03` | **Compteur de force brute non réinitialisé**<br>Après une connexion réussie faisant suite à 3 échecs, le compteur ne retombe pas à 0. Une nouvelle erreur bloque le compte prématurément. | **Majeure** | **Haute** | **En Cours** | Injecter la routine de remise à zéro du compteur d'échecs dans le service de génération des jetons de session (Succès). |
| **BUG-AUTH-03** | `CT-AUTH-02` | **Message d'erreur trop explicite à la connexion**<br>En cas de mauvais mot de passe, l'API renvoie "Mot de passe incorrect pour cet email", ce qui permet de valider l'existence d'un compte. | **Majeure** | **Moyenne** | **À Traiter** | Modifier le message renvoyé par l'API pour une formulation neutre : "Identifiant ou mot de passe incorrect", conformément au RGPD. |
| **BUG-ONB-01** | `CT-ONB-02` | **Acceptation de tailles humaines aberrantes**<br>L'étape 2 de l'onboarding valide une taille utilisateur de 12 cm au lieu de bloquer à la borne minimale de 120 cm définie dans `BR-ONB-01`. | **Majeure** | **Haute** | **Corrigé** | Synchronisation des bornes du validateur du composant de saisie enfant avec la plage réglementaire de la règle `BR-ONB-01`. |
| **BUG-HAB-01** | `CT-HAB-02` | **Chevauchement de sommeil accepté sur deux jours**<br>Le système accepte l'insertion d'une sieste qui intersecte une nuit existante à cheval sur minuit, violant la règle `BR-HAB-01`. | **Majeure** | **Haute** | **En Cours** | Réécriture de la logique de calcul d'intersection de l'enveloppe temporelle pour couvrir les chevauchements multi-dates. |
| **BUG-NUT-01** | `CT-NUT-02` | **Enregistrement d'un aliment mathématiquement faux**<br>Le catalogue valide un produit personnalisé affichant 500 kcal pour seulement 10g de lipides (90 kcal réelles selon `BR-NUT-01`). | **Majeure** | **Moyenne** | **À Traiter** | Bloquer la soumission de la fiche si l'écart de l'équation de contrôle dépasse la tolérance stricte de 5% de la règle `BR-NUT-01`. |

---

## 📝 3. Modèle d'Ouverture d'une Nouvelle Anomalie

*Lorsqu'un testeur identifie un nouvel écart lors de l'exécution de la recette, il doit dupliquer et compléter ce canevas :*

```text
## [BUG-MODULE-XXXX] : Titre explicite et concis du défaut constaté

* **Date de découverte :** 22/08/2026
* **Scénario de test d'origine :** [Ex: CT-DSB-02]
* **Sévérité :** [Bloquante | Majeure | Mineure]
* **Priorité :** [Haute | Moyenne | Basse]

### Étapes pour reproduire l'écart :
1. Naviguer vers l'écran ou le module [...].
2. Sélectionner ou saisir la valeur [...] dans le champ [...].
3. Cliquer sur l'action [...].

### Comportement attendu (Spécifications) :
[Description claire de ce que le système aurait dû faire selon les règles de gestion ou les critères de la story]

### Comportement observé (Dysfonctionnement) :
[Description précise de l'anomalie, du bug visuel ou code d'erreur applicatif capturé]
```

