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

On lit les deux fichiers en donnant les chemins vers ceux-ci, grâce à la fonction read_csv de pandans (pd).


```python
metabolomic_df = pd.read_csv(path_to_metabolomic)
metadata_df = pd.read_csv(path_to_metadata)
```

Pour connaître la taille des tableaux:


```python
print(metabolomic_df.shape)
print(metadata_df.shape)
```

```python
metabolomic_df.head()
```
