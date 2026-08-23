# User Story [US-24-25] : Planificateur de Repas, Suivi des Macronutriments & Catalogue
**Priorité (MoSCoW) :** Must Have  
**Module associé :** `:feature:nutrition` (Suivi Nutritionnel)

### Description
**En tant que** membre soucieux de sa composition corporelle et de sa santé métabolique,  
**Je veux** rechercher des aliments dans un catalogue de référence, ajuster mes portions et planifier mes repas tout en filtrant mes allergènes,  
**Afin de** respecter mon budget calorique et ma répartition de macronutriments cibles sans risquer d'ingérer un ingrédient exclu.

---

## 🏗️ Interopérabilité & Alignement de l'Architecture

Le module `:feature:nutrition` orchestre l'ensemble de la comptabilité énergétique quotidienne :
*   **Données Cibles :** Il consomme le budget calorique alloué par le module de pilotage stratégique `:feature:goals`.
*   **Couplage Circadien :** Toute insertion d'une ligne de repas d'une valeur supérieure à 5 kcal émet un signal de rupture automatique vers le module des habitudes (`:feature:habits`) afin de clôturer la fenêtre de jeûne active (`BR-NUT-03` / `BR-HAB-03`).

---

## 🛠️ Critères d'Acceptation & Scénarios de Test (Format Gherkin)

### Scénario 1 : Calcul linéaire exact de l'apport d'une portion personnalisée (BR-NUT-02)
* **Étant donné que** l'aliment "Blanc de poulet" existe dans le catalogue de référence avec les valeurs pour 100g suivantes : 22g Protéines, 0g Glucides, 2g Lipides et 106 kcal,
* **Quand** je sélectionne cet aliment pour mon Déjeuner et que je configure la portion consommée à `200g`,
* **Alors** l'interface client applique l'équation de proportionnalité linéaire de la règle `BR-NUT-02`,
* **Et** les totaux du Déjeuner s'incrémentent de manière rigoureuse : 44g de Protéines, 0g de Glucides, 4g de Lipides et 212 kcal,
* **Le tableau de bord quotidien** reflète instantanément cette hausse sur mes jauges de la journée.

### Scénario 2 : Refus d'insertion d'un aliment aux valeurs incohérentes (BR-NUT-01)
* **Étant donné que** je tente de créer une fiche d'aliment personnalisé dans le catalogue de la plateforme,
* **Quand** je saisis les macronutriments suivants : 10g Protéines, 10g Glucides, 10g Lipides, et que j'indique une valeur énergétique de `500 kcal`,
* **Alors** le validateur du système applique l'équation de contrôle de la règle `BR-NUT-01` qui établit la valeur réelle à $(10 \times 4) + (10 \times 4) + (10 \times 9) = 170\text{ kcal}$,
* **Et** l'enregistrement de l'aliment est fermement rejeté pour non-respect de la marge de tolérance de 5%, tandis qu'un message d'erreur m'indique : "Les valeurs énergétiques ne correspondent pas aux macronutriments saisis."

### Scénario 3 : Filtrage du catalogue et alertes d'exclusions alimentaires
* **Étant donné que** mon profil utilisateur comporte une exclusion stricte marquée sur le "Gluten",
* **Quand** j'effectue une recherche textuelle dans le catalogue alimentaire et que je sélectionne un produit contenant du blé,
* **Alors** le système intercepte la collision avec mon profil de santé,
* **Et** l'interface utilisateur affiche une étiquette d'avertissement visuelle rouge explicite et bloque le bouton d'ajout rapide pour éviter une journalisation ou une ingestion par erreur.

---

## 🎛️ Spécifications des Éléments d'Interface (UI Outlines)

L'écran nutritionnel intègre les compteurs de macronutriments et isole le catalogue de la gestion des repas de la journée :

+-----------------------------------------------------------------------+
|[ Nutrition ] Planificateur de Menus & Journal        
|
+-----------------------------------------------------------------------+
|🔍 CATALOGUE ALIMENTAIRE :                                           
|
|[ Rechercher un ingrédient ou une recette...                       ] 
|
| * Résultats :                                                        
|
|- [ 🍏 Pomme verte (100g) : 52 kcal ]  [+ Ajouter rapide]          
|
|- [ 🍞 Pain de mie (100g) : 260 kcal ] [⚠️ ALERTE : Contient Gluten] 
| <-- Filtre Exclusion

+-----------------------------------------------------------------------+
|🍽️ REPAS DU JOUR — DÉJEUNER :                                         
|
|* Blanc de poulet 
|Portion : [ 200 ] g                               
| <-- Règle BR-NUT-02
| [ P: 44g  |  G: 0g  |  L: 4g  |  Énergie : 212 kcal ]         
|
|
|
|➕ Total Déjeuner : 212 kcal / [ Copier ce repas vers demain ]        
|
+-----------------------------------------------------------------------+
