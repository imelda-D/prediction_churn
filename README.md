# prediction_churn
modele de prediction du desabonnement ou non d'un client
# **Projet : Prédiction de Churn Client**

##  Aperçu du Projet
Ce projet vise à prédire le désabonnement (churn) des clients d'une entreprise en utilisant des techniques d'apprentissage automatique. L'objectif est d'identifier les clients susceptibles de quitter le service pour mettre en place des actions de rétention ciblées.

## Source des données
- **Plateforme** : Kaggle
- **Compétition/Dataset** : Customer Churn Prediction
- **Disponibilité** : Dataset public pour analyse et modélisation
- **Lien** : [Kaggle - Customer Churn Dataset](https://www.kaggle.com/datasets/muhammadshahidazeem/customer-churn-dataset?select=customer_churn_dataset-testing-master.csv)

##  Structure des Données

### Dataset d'entraînement
- **Lignes** : 440,833 observations
- **Colonnes** : 12 variables

### Variables disponibles :
1. **Age** : Âge du client (numérique)
2. **Gender** : Genre du client (0=Femme, 1=Homme)
3. **Tenure** : Ancienneté en mois (numérique)
4. **Usage Frequency** : Fréquence d'utilisation mensuelle (numérique)
5. **Support Calls** : Nombre d'appels au support (numérique)
6. **Payment Delay** : Retard de paiement en jours (numérique)
7. **Subscription Type** : Type d'abonnement (catégorielle)
8. **Contract Length** : Durée du contrat (catégorielle)
9. **Total Spend** : Dépenses totales (numérique)
10. **Last Interaction** : Dernière interaction en jours (numérique)
11. **Churn** : Variable cible (0=Non, 1=Oui)
12. **CustomerID**: Identifient client
### Dataset de test
- **Lignes** : 64,374 observations
- **Mêmes variables** que le dataset d'entraînement

##  Pré-traitement des Données

### 1. Gestion des valeurs manquantes
#### Variables numériques : remplacement par la moyenne

#### Variables catégorielles : remplacement par le mode


### 2. Encodage des variables catégorielles
- **Gender** : Label Encoding (0/1)
- **Subscription Type** : Ordinal Encoding (1, 2, 3)
- **Contract Length** : Ordinal Encoding (1, 2, 3)

### 3. Distribution des classes
- **Non-Churn (0)** : 43.3%
- **Churn (1)** : 56.7%

##  Modèles Implémentés

### 1. Régression Logistique
#### Avantages :
- Simple et interprétable
- Rapide à entraîner
- Bonne baseline pour les problèmes de classification binaire

#### Limitations rencontrées :
- Faible performance (accuracy ~52%)
- Sensible aux variables corrélées
- Ne capture pas les interactions complexes entre variables
- Nécessite un prétraitement minutieux des données

### 2. Arbre de Décision (Conditional Inference Tree)
#### Configuration optimale :
```r
ctree(
  Churn ~ Gender + Tenure + Usage.Frequency + Last.Interaction,
  controls = ctree_control(
    mincriterion = 0.95,
    minsplit = 500,
    minbucket = 250,
    maxdepth = 3
  )
)
```


#### Observations clés :
1. **Problème identifié** : Certaines variables présentaient des "règles parfaites" non réalistes dans le dataset d'entraînement (ex: 100% de churn pour certaines valeurs)
2. **Solution appliquée** : Exclusion des variables problématiques et régularisation forte
3. **Performance** : 52.89% d'accuracy (légèrement au-dessus de la baseline de 52.63%)

#### **Variables retenues** :
- Gender
- Tenure
- Usage Frequency
- Last Interaction

## Résultats

### Performance finale :
- **Accuracy** : 52.89%
- **Baseline** : 52.63% (toujours prédire la classe majoritaire)
- **Amélioration** : +0.26 points de pourcentage

### **Distribution des prédictions** :
- **Non-Churn prédit** : 58.19%
- **Churn prédit** : 41.81%

### **Distribution réelle (test set)** :
- **Non-Churn réel** : 52.63%
- **Churn réel** : 47.37%

##  Problèmes Rencontrés et Solutions

### Problème 1 : Overfitting sévère
- **Symptôme** : Performance parfaite sur l'entraînement (OOB=1%) mais faible sur le test (52%)
- **Cause** : Règles déterministes non réalistes dans les données
- **Solution** : Régularisation forte et exclusion des variables problématiques

### Problème 2 : Variables avec règles parfaites
- **Exemples** : 
  - `Contract.Length == 1` → 100% Churn (87,104 observations)
  - `Age` entre 51-65 → 100% Churn
  - `Payment.Delay` > 20 → 100% Churn
  - support.calls>5 →100% churn (83,029 observations)
- **Impact** : Modèles apprennent des patterns non généralisables
- **Solution** : Utilisation d'un sous-ensemble de variables "sûres"

### Problème 3 : Dataset potentiellement synthétique
- **Indices** : Règles trop parfaites, distributions artificielles
- **Conséquence** : Difficulté à obtenir un modèle qui généralise bien
- **Solution** : Modèle simple avec régularisation agressive

##  Insights Business

### Variables importantes :
1. **Last Interaction** : Dernier contact avec le service
2. **Tenure** : Ancienneté du client
3. **Usage Frequency** : Fréquence d'utilisation
4. **Gender** : Genre du client

### Recommandations :
1. **Surveillance proactive** : Cibler les clients avec dernière interaction récente
2. **Programmes de fidélité** : Récompenser l'ancienneté et la fréquence d'utilisation
3. **Communication personnalisée** : Adapter les messages selon le profil client

##  Améliorations Futures

1. **Collecte de données** : Obtenir des données plus réalistes et représentatives
2. **Feature Engineering** : Créer des variables dérivées plus informatives
3. **Autres algorithmes** : Tester XGBoost, Random Forest avec régularisation
4. **Validation temporelle** : S'assurer que le modèle généralise dans le temps

##  Technologies Utilisées

- **Langage** : R
- **Librairies principales** : `party`, `caret`, `dplyr`,'ggplot2'
- **Environnement** : RStudio
- **Version control** : Git/GitHub

## Références

- Documentations : `?ctree`, `?caret`
- Best practices en machine learning
- Principes de régularisation et prévention de l'overfitting

##  Auteurs

- imelda
- Date : 19/01/2026

