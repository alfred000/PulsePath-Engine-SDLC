# 🗺️ Spécifications Fonctionnelles Globales & Product Backlog — PulsePath

Ce document définit l'organisation macro-fonctionnelle de la solution PulsePath, cartographie les dépendances inter-modules et qualifie la priorité des exigences du Product Backlog global selon la méthodologie MoSCoW.

---

## 1. Cartographie Étanche de l'Architecture Solution

Pour garantir une maintenance sans régression et permettre le remplacement futur de n'importe quelle brique, le système applique un découpage strict :
*   **Modules Fonctionnels Verticaux (`:feature`) :** Portent l'interface utilisateur (UI) et la logique métier propre à leur domaine. Ils ne se connaissent jamais entre eux et communiquent uniquement par l'émission de signaux asynchrones sur le bus de messages.
*   **Modules Fondamentaux Horizontaux (`:core`) :** Fournissent des outils transversaux, des structures de données immuables et des contrats d'interface (Inversion de Contrôle).

+-----------------------------------------------------------------------------------+
|                        MODULES FONCTIONNELS VERTICAUX (:feature)                 |
|      Contiennent l'interface utilisateur et la logique propre à chaque domaine   |

+-----------------------------------------------------------------------------------+
| :dashboard-daily | :auth  | :onboarding | :profile  | :goals  | :telemetry   |
| (Agrégateur)     |        |             |           |         |              |

+------------------+-------------+-------------+-------------+-----------+--------------+
| :journal (Diary) | :habits     | :nutrition  | :workout    | :analytics| :coaching    |
|                  |             |             |             |           |              |

+------------------+-------------+-------------+-------------+-----------+--------------+
|
| 

Consomment et dépendent de/

+-----------------------------------------------------------------------------------+
|                        MODULES FONDAMENTAUX PARTAGÉS (:core)                      |
|           Fournissent les outils, contrats pivots et moteurs de calcul            |

+-----------------------------------------------------------------------------------+
| :biometric-api   | :network    | :database   | :simulation-engine | :notification |

+------------------+-------------+-------------+--------------------+---------------+
---

## 2. Focus Nouveau Module : `:feature:dashboard-daily` (Vue Quotidienne)

Le module `:feature:dashboard-daily` est le **point d'entrée principal** de l'espace connecté. Il agit comme un agrégateur de présentation pur au quotidien. Il consomme les données d'états des autres modules pour une date sélectionnée sans jamais manipuler directement les bases de données sous-jacentes.

### 🧭 Système de Navigation Calendrier
Le tableau de bord embarque un composant de navigation temporelle (Barre de calendrier glissante ou sélecteur de date) permettant à l'utilisateur de changer de jour cible :
*   **Sélection du Jour J (Courant) :** Restitue les compteurs dynamiques en temps réel alimentés par la télémétrie passive ou les saisies immédiates.
*   **Navigation Historique (Jour J-N) :** Charge l'état figé des cumuls passés pour auditer sa conformité.
*   **Navigation Prévisionnelle (Jour J+N) :** Affiche le budget calorique planifié et les routines cibles programmées à l'avance pour permettre l'anticipation.

### 🎛️ Blocs de Restitution Centralisés
1.  **Volet Calories & Macronutriments :** Synthèse visuelle (jauges circulaires) affichant le budget cible vs les calories consommées, découpées en Protéines, Glucides et Lipides (provenant de `:feature:nutrition` et `:feature:goals`).
2.  **Volet Habitudes & Routines Circadiennes :** État de complétion des pas quotidiens, volume d'hydratation, score d'efficacité de la dernière nuit de sommeil et état du chronomètre de jeûne intermittent (provenant de `:feature:habits`).
3.  **Volet Composition Corporelle & Tendances :** Affichage instantané du dernier Poids Brut et du Taux de Masse Grasse enregistrés, superposés à la courbe de tendance lissée (provenant de `:feature:body-composition` et `:feature:analytics`).

---

## 📊 Matrice d'Interaction et de Dépendance Globale

Pour préserver le découpage, le nouveau tableau de bord utilise exclusivement des flux de données découplés :

[ :feature:nutrition ] ──(Event: DietUpdated)───► [ Bus de Messages ] ───► Updates UI ──► [ :feature:dashboard-daily ][ :feature:habits    ] ──(Event: RoutineUpdated)─► [ Central ]        ───► Gauges UI   ──► (Sélecteur de Date Actif)[ :feature:analytics ] ──(Request: TrendVector)──► [ :coreContract ]  ───► Charts UI   ──► [ Version Neutre stockée ]
---

## 🎯 3. Product Backlog Qualifié (Méthode MoSCoW)

### 🟢 Must Have (Obligatoire)
*   **[REQ-DSB-01]** Interface unifiée du tableau de bord quotidien segmentée en briques visuelles étanches (Nutrition, Habitudes, Poids).
*   **[REQ-DSB-02]** Composant de navigation par calendrier permettant de basculer d'une date à une autre (Passé, Présent, Futur) et de rafraîchir les blocs associés.
*   **[REQ-AUTH-01]** Formulaire d'inscription et de connexion appliquant strictement la complexité des mots de passe (`BR-AUTH-01`).
*   **[REQ-ONB-01]** Parcours d'accueil composite en 9 étapes pour la capture des mensurations physiologiques initiales.
*   **[REQ-NUT-01]** Journalisation des repas avec calcul linéaire des portions et affichage du reliquat calorique quotidien (`BR-NUT-02`).
*   **[REQ-HAB-01]** Traqueur de sommeil (Model 2) calculant le taux d'efficacité net selon le nombre de micro-éveils (`BR-HAB-02`).
*   **[REQ-DB-01]** Chiffrement matériel de la base de données locale au repos via clé AES-256 (`BR-DB-01`).

### 🟡 Should Have (Fortement Recommandé)
*   **[REQ-DSB-03]** Affichage superposé de la courbe de poids brut vis-à-vis de la tendance de poids lissé sur le bloc de composition corporelle.
*   **[REQ-ANL-01]** Génération de rapports périodiques croisant les données quantitatives (calories) et qualitatives (fatigue, stress du journal).
*   **[REQ-GOL-01]** Ajustement automatique du budget d'hydratation quotidienne (+500ml) dès la complétion d'une heure d'entraînement physique (`BR-GOL-02`).
*   **[REQ-NET-01]** Mécanisme de rafraîchissement transparent des jetons d'accès expirés en arrière-plan sans déconnexion utilisateur (`BR-NET-01`).

### 🔵 Could Have (Envisageable)
*   **[REQ-DSB-04]** Mode prévisionnel du tableau de bord affichant les listes de courses pré-calculées pour les dates futures sélectionnées au calendrier.
*   **[REQ-GAM-01]** Moteur d'attribution de points d'expérience (XP) quadratique et déblocage de badges de régularité comportementale (`BR-GAM-01`).
*   **[REQ-SIM-01]** Modèle prédictif affichant une courbe de projection métabolique à 30 jours avec bandes de confiance statistiques.

### 🔴 Won't Have (Hors Périmètre pour cette Version)
*   **[REQ-COA-03]** Connexion ou interaction en temps réel avec un coach humain externe via visioconférence intégrée.
*   **[REQ-NUT-03]** Authentification et synchronisation avec des plateformes tierces de livraison de repas à domicile.
