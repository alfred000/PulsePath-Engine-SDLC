# User Story [US-33-35] : Suivi de la Composition Corporelle, Métriques Somatiques & Graphiques UI
**Priorité (MoSCoW) :** Must Have  
**Module associé :** `:feature:body-composition` (Composition Corporelle)

### Description
**En tant que** membre attentif à l'évolution de ma structure physique,  
**Je veux** consigner ou synchroniser mes métriques somatiques (Poids total, taux de masse grasse, masse musculaire squelettique et densité osseuse) et interagir avec les graphiques d'évolution,  
**Afin d'** analyser de manière granulaire mes gains de tissu musculaire et mes pertes adipeuses réelles tout en éliminant les interprétations erronées liées aux variations d'eau.

---

## 🏗️ Alignement Architectural & Flux Temporels

Le module `:feature:body-composition` agit comme la vitrine d'affichage biologique de l'utilisateur :
*   **Neutralité de Stockage :** Toutes les métriques sont stockées en kilogrammes (kg) et centimètres (cm) de manière neutre selon la règle `BR-PRF-02`, l'affichage adaptant l'unité à la volée.
*   **Traitement Aval :** Le poids réel brut alimente passivement le moteur de trajectoire de `:feature:goals`. Il sert de matrice d'entrée pour l'évaluation de la vitesse métabolique calculée par le simulateur (`:core:simulation-engine`).

---

## 🛠️ Critères d'Acceptation & Scénarios de Test (Format Gherkin)

### Scénario 1 : Refus d'une répartition de masse tissulaire incohérente (BR-BDC-01)
* **Étant donné que** j'ouvre le formulaire de saisie manuelle de mes paramètres corporels,
* **Quand** je renseigne un Poids Total Brut de `100.0 kg`,
* **Et que** je saisis une Masse Grasse de `40.0 kg`, une Masse Musculaire de `55.0 kg` et une Masse Osseuse de `10.0 kg`,
* **Alors** le validateur applicatif calcule la masse cumulée sèche $(40 + 55 + 10 = 105\text{ kg})$,
* **Et** le système rejette fermement la transaction pour violation de l'équation de la règle `BR-BDC-01` ($105\text{ kg} > 100\text{ kg}$),
* **L'interface utilisateur** bloque la soumission et affiche : "La somme de vos composants corporels ne peut pas dépasser votre poids total."

### Scénario 2 : Isolation interactive des courbes d'affichage graphique (US-34)
* **Étant donné que** mon tableau de bord de composition corporelle affiche mes graphiques d'évolutions,
* **Et que** l'ensemble des tracés (Poids total, Masse grasse, Muscle, Os) est actif par défaut à l'écran,
* **Quand** je clique sur le filtre de légende interactif étiqueté "Masse Grasse",
* **Alors** l'interface utilisateur masque instantanément la courbe représentant le taux de graisse,
* **Et** la mise en page des axes s'ajuste dynamiquement pour préserver la lisibilité parfaite des tendances de la masse musculaire et du poids total sans aucun rechargement de page.

### Scénario 3 : Ingestion automatisée depuis le flux de télémétrie passive
* **Étant donné que** j'utilise une balance intelligente impédancemètre couplée à la plateforme,
* **Quand** le module de télémétrie (`:feature:telemetry`) valide un lot d'ingestion asynchrone contenant mes informations de composition corporelle du matin,
* **Alors** l'API persiste proprement ces métriques somatiques au sein de mes séries temporelles,
* **Et** mes graphiques de tendances se mettent automatiquement à jour lors de ma prochaine ouverture du module, superposant la mesure réelle brute à ma courbe de tendance lissée.

---

## 🎛️ Spécifications des Éléments d'Interface (UI Outlines)

L'écran de composition corporelle intègre les filtres interactifs et isole le rendu graphique des curseurs de saisie manuelle :

+-----------------------------------------------------------------------+

|   
[ Composition ] Analyse Somatique & Compartiments Tissulaires 
|

+-----------------------------------------------------------------------+

|  
📊 TENDANCES DE COMPOSITION CORPORELLE :                             
|
|                                                                       
|
|  
(Graphique UI Linéaire Continu Multi-Courbes)                        
|
|
- Poids Brut (80.2 kg)  ==================o======================
|
|
- Masse Musculaire (38.5 kg)  ------------*----------------------
|
|
|
|
Légendes Interactives :  [(X) Poids]  [(X) Muscle]  [ ] Gras (Masqué)| <-- Toggles Graphiques

+-----------------------------------------------------------------------+

|  
🩺 COMPARTIMENTS ACTUELS (Règle BR-BDC-01) :                          
|
|  * Poids Total :   80.2 kg     
|  * Masse Musculaire : 38.5 kg (48%) 
|
|  * Masse Grasse :  14.4 kg (18%)
|  * Masse Minérale Osseuse : 3.2 kg  
|
|                                                                       
|
|  
[ ➕ Enregistrer une nouvelle pesée manuelle (Impédancemétrie) ]      
|

+-----------------------------------------------------------------------+
