# User Story [US-09] : Enregistrement du Journal Quotidien (Diary) & Saisie de Ressentis
**Priorité (MoSCoW) :** Must Have  
**Module associé :** `:feature:journal` (Journal / Diary)

### Description
**En tant que** membre de la plateforme PulsePath,  
**Je veux** consigner manuellement mes métriques quantitatives (poids instantané, repas consommés, verres d'eau) et mes ressentis qualitatifs (fatigue, stress, notes d'humeur),  
**Afin de** documenter le contexte comportemental immédiat de ma journée et de compléter mes indicateurs lorsque mes capteurs automatiques sont absents.

---

## 🏗️ Alignement Fonctionnel & Découpage Étanche

Conformément à la matrice macro de la solution, ce module se concentre exclusivement sur l'ingestion immédiate et la capture au quotidien (**UC-01**). L'analyse de l'historique long terme, les courbes de tendances et les bilans de performance prédictifs sont formellement hébergés au sein du module **`:feature:analytics`**.

---

## 🛠️ Critères d'Acceptation & Scénarios de Test (Format Gherkin)

### Scénario 1 : Utilisation des raccourcis incrémentaux de l'hydratation (UC-01)
* **Étant donné que** mon journal quotidien affiche un volume d'eau initial de `0 ml`,
* **Quand** je clique à deux reprises sur le bouton d'action rapide `+250 ml` du bloc d'hydratation,
* **Alors** l'interface client incrémente localement l'état de ma consommation,
* **Et** le système transmet de manière asynchrone la payload de mise à jour à l'API,
* **Et** le cumul total d'eau affiché sur l'écran se met instantanément à jour pour indiquer `500 ml` sans rechargement de page.

### Scénario 2 : Application des plafonds de sécurité sur les entrées numériques (BR-JRN-01)
* **Étant donné que** je saisis manuellement mes données d'hydratation,
* **Quand** je tente de soumettre une entrée unique de `2500 ml` d'eau dans le formulaire,
* **Alors** le validateur applicatif intercepte le dépassement du plafond de la règle `BR-JRN-01`,
* **Et** la transaction d'écriture est bloquée localement tandis qu'un message m'informe que la saisie unique maximale autorisée est de 2000 ml.

### Scénario 3 : Conflit de données et priorité à la saisie manuelle directe (BR-JRN-02)
* **Étant donné que** le module de télémétrie automatique (`:feature:telemetry`) a déjà synchronisé un poids de `80.5 kg` reçu d'une balance connectée pour la plage horaire actuelle,
* **Quand** j'ouvre mon journal et que je modifie manuellement cette ligne de poids pour inscrire `80.1 kg`,
* **Alors** le système applique la politique de préséance de la règle `BR-JRN-02`,
* **Et** l'API met à jour la valeur de référence de ma journée avec le chiffre `80.1 kg`,
* **Et** la donnée issue du capteur automatique passe au statut `Superseded` (Archivée pour audit mais exclue des calculs métaboliques courants).

### Scénario 4 : Persistance et intégrité des notes textuelles qualitatives
* **Étant donné que** je rédige un commentaire libre dans le bloc "Notes de ressenti" de ma journée,
* **Quand** je saisis le texte "Manque d'énergie suite à un réveil nocturne" et que je valide mon entrée,
* **Alors** l'API serveur persiste l'annotation de manière sécurisée et chiffrée,
* **Et** si je quitte l'application puis reviens sur le journal du jour, le bloc de ressentis recharge et restitue ma note textuelle de manière intègre.

---

## 🎛️ Spécifications des Éléments d'Interface (UI Outlines)

L'interface du journal est divisée en sections indépendantes pour limiter la friction de saisie et accélérer l'encadrement des données du jour :

+-----------------------------------------------------------------------+
|   
[ < ]                 Journal d'Aujourd'hui                 [ > ]   
|
+-----------------------------------------------------------------------+
|  
💧 HYDRATATION :  500 ml / 3 000 ml                                  
|| [ +250 ml ]    [ +500 ml ]    [ Saisie Manuelle Avancée ]            
| <-- Raccourcis Incrémentaux

+-----------------------------------------------------------------------+
|  🍎 REPAS & MACROS : 1 200 kcal                                       
|
|  * Petit-déjeuner : Œufs, Avocat, Pain (450 kcal)                     
||  [ + Enregistrer un aliment / Plat composé ]                          
| <-- Nutrition Ingestion

+-----------------------------------------------------------------------+
|  
🧠 NOTES & RESSENTIS QUALITATIFS :                                   
|
|  Niveau de fatigue : [ 1 | 2 | 3 | (4) | 5 ]                          
| <-- Échelles Closes
|  Note libre : [ Manque d'énergie suite à un réveil nocturne         ] 
| <-- Texte Libre

+-----------------------------------------------------------------------+  
