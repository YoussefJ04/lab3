# DE1 — Lab 3: Physical Representations and Batch II Costs

## Objectif du lab

L’objectif de ce lab était de comprendre comment les choix de représentation physique des données influencent les performances et les coûts de calcul dans Apache Spark.  
En particulier, le travail portait sur la comparaison entre différents formats de données, stratégies de partitionnement et méthodes de jointure, en s’appuyant sur les plans d’exécution Spark et les métriques du Spark UI.

---

## Travail réalisé

### Analyse avec SQL et DataFrames

Les données analytiques issues de l’Assignment 2 ont été exploitées à l’aide :
- de requêtes SQL Spark,
- et d’opérations équivalentes en PySpark DataFrames.

Cette première étape a permis d’analyser l’impact des projections (`select("*")` vs sélection de colonnes ciblées) sur les volumes de données lus et les plans d’exécution générés par Spark.

---

### Implémentation des algorithmes RDD

Deux familles d’algorithmes basés sur les RDD ont été mises en œuvre :

- des calculs de moyennes (means) à partir de transformations RDD,
- des jointures RDD, en comparant :
  - une jointure classique impliquant un shuffle,
  - et une jointure optimisée de type hash/broadcast lorsque cela était pertinent.

Les différences de comportement ont été analysées à l’aide de `explain("formatted")` ainsi que via les métriques observées dans le Spark UI (temps d’exécution, shuffle, nombre de tâches).

---

### Comparaison des représentations physiques

Deux représentations principales des données ont été comparées :

- Format row (CSV)  
  - utilisé comme baseline,
  - sans partitionnement,
  - impliquant des scans complets des fichiers lors des requêtes analytiques.

- Format column (Parquet) 
  - avec un partitionnement prévu par `(year, month)`,
  - visant à réduire les volumes de données lus et à améliorer les performances analytiques.

Les mêmes requêtes ont été exécutées (ou analysées) afin de comparer les coûts en termes de lecture, de shuffle et de temps de calcul.

---

## Problème rencontré

Lors de l’écriture des données au format Parquet partitionné, un problème technique est apparu sur l’environnement local (Windows + PySpark).

Malgré une implémentation que l'on pense correcte du point de vue logique, l’utilisation de `partitionBy(year, month)` a conduit à des erreurs ou à des comportements instables lors de l’écriture des fichiers.  
Ce problème semble lié à l’environnement local (gestion des chemins, permissions ou conflits de dossiers existants sous Windows).

Le problème n’a pas pu être résolu complètement.

---

## Approche adoptée et justification

Plutôt que de forcer une solution instable, le choix a été fait de :
- documenter clairement la structure attendue du layout Parquet partitionné,
- exploiter les métriques du Spark UI pour comparer les approches CSV vs Parquet,

Cette démarche permet de répondre aux objectifs pédagogiques du lab, même si la reproduction complète du pipeline Parquet partitionné n’a pas été possible localement.

---

## Conclusion

Ce lab met en évidence l’impact majeur des formats de données, du partitionnement et des stratégies de jointure sur les performances Spark.  
Il souligne également l’importance de savoir analyser les plans d’exécution et les métriques système, ainsi que de prendre en compte les contraintes réelles d’un environnement de travail.
