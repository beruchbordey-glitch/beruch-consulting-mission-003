# Beruch Consulting — Mission #003 : HealthCo (prédiction du diabète)

 # Construction d'un modèle de Machine Learning pour prédire le diabète à partir de facteurs démographiques, cliniques et comportementaux, avec Python (scikit-learn).
 Cas fictif réalisé dans le cadre d'un défi data portfolio.


## À propos

Beruch Consulting accompagne les entreprises et organisations dans leur transition vers une culture *data-driven*, en transformant leurs données brutes en décisions stratégiques.
De la donnée à la décision.

Chaque projet de ce portfolio est présenté sous forme de mission client : un contexte, une problématique, des analyses et des recommandations concrètes.

Cette mission est la **suite directe de la Mission #002** : après avoir exploré les facteurs de risque du diabète dans Power BI (analyse descriptive), HealthCo souhaite aller plus loin et disposer d'un **modèle prédictif**.

---

## Le client : HealthCo

HealthCo (organisme de santé publique fictif) souhaite se doter d'un outil capable d'identifier les patients à risque de diabète à partir de leur profil, afin d'orienter le dépistage de façon proactive plutôt que réactive.

## Problématique
Peut-on prédire le diabète d'un patient à partir de ses caractéristiques non diagnostiques (âge, mode de vie, antécédents, corpulence) ? Et quels facteurs pèsent le plus dans cette prédiction ?

## Données
- Source : jeu de données public de santé (type Kaggle), utilisé pour simuler le cas HealthCo.
- 100 000 patients · 31 variables · aucune valeur manquante.
- Cible : diagnosed_diabetes (0 = non diabétique, 1 = diabétique), avec une répartition de 60 % / 40 %.

## Outils & compétences mobilisées

| Domaine | Détail |
| Langage | Python (pandas, numpy, matplotlib, seaborn) |
| Machine Learning | scikit-learn |
| Préparation | Pipeline + ColumnTransformer (StandardScaler, OneHotEncoder) |
| Modélisation | Régression logistique, Random Forest |
| Optimisation | GridSearchCV (validation croisée), class_weight |
| Évaluation | accuracy, recall, classification report, matrice de confusion |
| Interprétation | coefficients & odds ratios |

## Démarche

### 1 Exploration des données (EDA)
- Inspection des dimensions, types, valeurs manquantes (aucune).
- Distribution des variables numériques (histogrammes, boxplots pour les outliers).
- Matrice de corrélation pour repérer les liens entre variables.

### 2 Préparation & garde-fou méthodologique
- Exclusion volontaire des marqueurs diagnostiques : glucose_fasting, glucose_postprandial, hba1c, insulin_level, diabetes_risk_score, diabetes_stage.
- Pourquoi ? Ces variables définissent ou mesurent le diabète : les utiliser pour le prédire reviendrait à un raisonnement circulaire (fuite de données / data leakage). On ne garde que des facteurs de risque réellement exploitables en amont d'un diagnostic.
- L'exclusion de insulin_level a par ailleurs été testée empiriquement : sa réintégration ne modifie pas les performances, confirmant la décision.
- Préprocessing via un Pipeline scikit-learn : standardisation des variables numériques, encodage one-hot des variables catégorielles.

### 3 Modélisation & comparaison
- Régression logistique (modèle de référence, interprétable).
- Random Forest (modèle plus complexe, en comparaison).
- Rééquilibrage des classes via class_weight="balanced".
- Séparation train / test (80/20) pour évaluer la généralisation.

### 4 Optimisation
- Recherche des meilleurs hyperparamètres de la régression logistique par GridSearchCV (sur 'C', 'penalty', 'solver'), en optimisant le recall (priorité métier : ne pas rater de patients à risque).

### 5 Interprétation
- Extraction des coefficients de la régression logistique et conversion en odds ratios pour quantifier l'effet de chaque facteur.

## Résultats

### Performance
- Accuracy : environ 60 % (train et test quasi identiques , pas de surapprentissage).
- Recall équilibré entre les deux classes (environ 0,56 à 0,67 selon la classe et le modèle).
- Régression logistique et Random Forest donnent des performances quasi identiques.

### Le constat clé
Trois expériences indépendantes aboutissent à la même conclusion :
1- Deux modèles très différents (logistique vs Random Forest)  même performance.
2- Ajouter ou retirer une variable (insuline)  même performance.
3- Optimiser les hyperparamètres (GridSearch)  aucun gain.

- Le plafond de performance (environ 60 %) ne vient ni du modèle ni de son réglage, mais de la nature synthétique des données : le signal prédictif y est faible. Ce résultat est cohérent avec la Mission #002, qui avait déjà mis en évidence l'absence d'association de nombreuses variables avec la cible.

### Les facteurs de risque identifiés
La régression logistique met en avant la même hiérarchie que l'analyse Power BI de la Mission #002 :

| Facteur | Odds ratio | Effet |
| Antécédents familiaux | environ 1,6 | facteur le plus fort |
| Âge | environ 1,3 | risque croissant |
| Genre | environ 1,2 | effet modéré |
| IMC | environ 1,1 | facteur modifiable |

# Lecture : pour les variables standardisées (âge, IMC), l'effet s'entend « par écart-type ». Les odds ratios quantifient un effet sur les cotes (odds), à interpréter comme une augmentation du risque associée, et non comme un lien de causalité.

Convergence des deux missions : analyse descriptive (Power BI) et modèle prédictif (Python) désignent les mêmes facteurs dominants antécédents familiaux, âge et IMC.

## Recommandations pour HealthCo

1- Cibler le dépistage sur les patients cumulant antécédents familiaux, âge élevé et surpoids  le profil de risque le plus net.
2- Ne pas surestimer un modèle prédictif sur ces données : avec un signal aussi faible, le modèle ne peut pas remplacer un diagnostic clinique. Il peut au mieux servir de pré-filtre indicatif.
3- Investir dans la qualité des données : pour un outil prédictif réellement performant, des données réelles et mieux renseignées seraient nécessaires.

## Limites

- Données probablement synthétiques : signal prédictif faible, performances plafonnées, peu de variables réellement discriminantes.
- Corrélation ≠ causalité : les coefficients indiquent des associations, pas des relations causales.
- Marqueurs diagnostiques exclus volontairement pour éviter le data leakage (choix méthodologique assumé, au prix d'une performance plus basse mais honnête).

## Contenu du dépôt

```
beruch-consulting-mission-003/
├── README.md                       
├── diabetes_prediction.py          
└── screenshots/
    └── coefficients.png           


## Auteur

# Beruch Ibala
Projet réalisé dans le cadre d'un défi data portfolio (30 jours).



Données : jeu de données public de santé, synthétiques. Cas client fictif à but pédagogique.
