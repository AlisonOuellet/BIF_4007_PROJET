# Projet - MTBLS28 (Métabolomique urinaire) - Équipe 2
___

### 1) Importation des packages

On importe les librairies utilisées pour la manipulation des tableaux et la visualisation


```python
import pandas as pd
import numpy as np
from collections import Counter
import seaborn as sns
import matplotlib.pyplot as plt
```

### 2) Préparation des données

#### 2.1) Création du dossier de travail

On copie des données du répertoire du cours vers notre espace de projet (equipe2bac) pour travailler localement sur le serveur.


```python
cp -r /project/def-sponsor00/data/MTBLS28_urinaryMetabolomic/ /project/def-sponsor00/projects/equipe2bac/
```

#### 2.2) Chemin vers les fichiers

Les deux fichiers sont au format CSV:
- MTBLS28_CombinedData2_forML.csv: données métabolomiques (features)
- s_MTBLS28_merged.csv: métadonnées


```python
path_to_metabolomic = "/project/def-sponsor00/projects/equipe2bac/MTBLS28_urinaryMetabolomic/MTBLS28_CombinedData2_forML.csv"
path_to_metadata = "/project/def-sponsor00/projects/equipe2bac/MTBLS28_urinaryMetabolomic/s_MTBLS28_merged.csv"
```

### 3) Importation des données et métadonnées

On lit les deux fichiers en donnant les chemins vers ceux-ci, grâce à la fonction read_csv de pandas (pd).


```python
metabolomic_df = pd.read_csv(path_to_metabolomic, sep=";")
metadata_df = pd.read_csv(path_to_metadata, sep=";")
```

#### 3.1) Dimension des tableaux


```python
print(metabolomic_df.shape)
print(metadata_df.shape)
```

#### 3.1) Aperçu des données

Pour voir à quoi ressemblent les premières lignes du fichier de données 'MTBLS28_CombinedData2_forML.csv' sauvegardé dans metabolomic_df:


```python
metabolomic_df.head()
```

Pour voir à quoi ressemblent les premières lignes du fichier de données 's_MTBLS28_merged.csv' sauvegardé dans metadata_df:


```python
metadata_df.head()
```

### 4) Vérification des données manquantes (NA)


```python
print("Metabolomic NA :")
print(metabolomic_df.isnull().sum())
print("Somme totale NA metabolomic:")
print(metabolomic_df.isnull().sum().sum())

print("\nMetadata NA :")
print(metadata_df.isnull().sum())
print("Somme totale NA metadata:")
print(metadata_df.isnull().sum().sum())
```

### 5) Nombre d'échantillons uniques


```python
n_samples = metadata_df["Sample_Name"].nunique()
print(f"Il y a {n_samples} échantillons uniques dans cette étude.")
```

### 6) Statistiques descriptives de base sur le dataframe metabolomic_df

Cette commande permet d'obtenir le count (somme), mean (moyenne), std (écart-type), min, 25% (percentile), 50%, 75% et max.


```python
print(metabolomic_df.describe())
```

### 7) Filtre les données du dataframe metadata_df selon le statut "Case/Control"

Cela nous donnera le nombre de particpant étant des 'Cas' et des 'Control'


```python
case_metadata_df = metadata_df[metadata_df["Factor Value[Sample Type]"] == "Case"]
control_metadata_df = metadata_df[metadata_df["Factor Value[Sample Type]"] == "Control"]

print("Case:", len(case_metadata_df))
print("Control:", len(control_metadata_df))

print(case_metadata_df.head())
```

### 8) Fusion des dataframes

On fusionne les deux tableaux sur une clé commune. Ainsi, nous renommons dans metabolomic_df la colonne Unnamed:0 par Sample_ID et dans metadata_df, la colonne Sample_Name devient Sample_ID. Puis on fusionne ces tableaux sur cette clé.


```python
metabolomic_df.rename(columns={"Unnamed: 0": "SampleID"}, inplace=True)
metadata_df.rename(columns={"Sample_Name": "SampleID"}, inplace=True)

combined_df = pd.merge(metabolomic_df, metadata_df, on="SampleID")
print(combined_df.shape)
combined_df.head()
```

### 9) Exploration de la variable "Smoking"


```python
print(combined_df["Factor Value[Smoking]"].value_counts(dropna=False))
print("\nValeurs uniques (top 20):")
print(combined_df["Factor Value[Smoking]"].unique()[:20])
```

### 10) Filtrage - Current Smoker


```python
current_smoker_df = combined_df[
    combined_df["Factor Value[Smoking]"]
    .astype(str)
    .str.strip()
    .str.lower()
    .str.contains("current")
].copy()

print(current_smoker_df.shape)
current_smoker_df.head()
```

#### 10.1) Conserver uniquement Case vs Control


```python
current_smoker_df = current_smoker_df[
    current_smoker_df["Factor Value[Sample Type]"].isin(["Case", "Control"])
].copy()

print(current_smoker_df["Factor Value[Sample Type]"].value_counts())
```

### 11) Création de la variable cicle (label)


```python
current_smoker_df["Label"] = current_smoker_df["Factor Value[Sample Type]"].map({
    "Case": 1,
    "Control": 0
})

print(current_smoker_df["Label"].value_counts())
```

### 12) Matrice de features X et vecteur cible y


```python
feature_cols = [c for c in metabolomic_df.columns if c != "SampleID"]

X = current_smoker_df[feature_cols].apply(pd.to_numeric, errors="coerce")
y = current_smoker_df["Label"].astype(int)

print(X.shape, y.shape)
```

### 13) Visualisation

#### 13.1) Histogramme d'un métabolite


```python
sns.histplot(data=current_smoker_df, x=feature_cols[0])
plt.title("Histogramme d'un métabolite (Current smokers)")
plt.show()
```

#### 13.2) Bosplot Case vs Control pour un métabolite


```python
sns.boxplot(data=current_smoker_df, x="Factor Value[Sample Type]", y=feature_cols[0])
plt.title("Case vs Control (Current smokers)")
plt.show()
```

### 14) Vérification du désequilibre de classe

Avant d’entraîner un modèle de classification, il est important de vérifier la distribution des classes afin d’identifier un éventuel déséquilibre pouvant influencer les performances du modèle.

```python
print(current_smoker_df["Factor Value[Sample Type]"].value_counts())

sns.countplot(
    data=current_smoker_df,
    x="Factor Value[Sample Type]"
)

plt.title("Distribution des classes (Current smokers)")
plt.show()
```