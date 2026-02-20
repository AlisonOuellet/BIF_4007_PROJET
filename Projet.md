# Projet
___

### Importation des packages utilisées


```python
import pandas as pd
import numpy as np
```

### Création du dossier equipe2bac

### Copie des données de tous les participants


```python
cp -r /project/def-sponsor00/data/MTBLS28_urinaryMetabolomic/ /project/def-sponsor00/projects/equipe2bac/
```

### Analyse des données

Les deux fichiers sont de type CSV, Nous les plaçons donc dans des variables


```python
path_to_metabolomic = "/project/def-sponsor00/projects/equipe2bac/MTBLS28_urinaryMetabolomic/MTBLS28_CombinedData2_forML.csv"
path_to_metadata = "/project/def-sponsor00/projects/equipe2bac/MTBLS28_urinaryMetabolomic/s_MTBLS28_merged.csv"
```

### Importation des données et métadonnées

On lit les deux fichiers en donnant les chemins vers ceux-ci, grâce à la fonction read_csv de pandas (pd).


```python
metabolomic_df = pd.read_csv(path_to_metabolomic, sep=";")
metadata_df = pd.read_csv(path_to_metadata, sep=";")
```

Pour connaître la taille des tableaux:


```python
print(metabolomic_df.shape)
print(metadata_df.shape)
```

Pour voir à quoi ressemblent les premières lignes du fichier de données 'MTBLS28_CombinedData2_forML.csv' sauvegardé dans metabolomic_df:


```python
metabolomic_df.head()
```

Pour voir à quoi ressemblent les premières lignes du fichier de données 's_MTBLS28_merged.csv' sauvegardé dans metadata_df:


```python
metadata_df.head()
```

## Manipulation des DataFrames


```python
print("Métabolomique :", metabolomic_df.isnull().sum().sum())
print("Métadonnées :")
print(metadata_df.isnull().sum())
```

### Vérifier s'il y a des données manquantes

Les données métabolomiques ne contiennent aucune valeur manquante. Certaines colonnes des métadonnées sont entièrement vides (1005 NaN), ce qui est attendu dans ce type de fichier.

### Nombre de partcipants unique de cette étude


```python
df = len(set(metadata_df["Sample_Name"].unique()))
print(f"Il y a {df} participants uniques qui ont pris part à cette étude.")
```

### Statistiques descriptives de base sur le dataframe metabolomic_df

Cette commande permet d'obtenir le count (somme), mean (moyenne), std (écart-type), min, 25% (percentile), 50%, 75% et max.


```python
print(metabolomic_df.describe())
```

### Filtre les données du dataframe metadata_df pour conserver uniquement les données où la colonne  
