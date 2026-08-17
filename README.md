# 🏷️ Retail Product Catalog Analytics

[![Dataset](https://img.shields.io/badge/Dataset-Online%20Retail%20II%20(UCI)-blue?logo=databricks&logoColor=white)](https://archive.ics.uci.edu/dataset/502/online+retail+ii)
[![Python](https://img.shields.io/badge/Python-3.x-blue?logo=python&logoColor=white)](https://www.python.org/)
[![pandas](https://img.shields.io/badge/pandas-Data%20Wrangling-150458?logo=pandas&logoColor=white)](https://pandas.pydata.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white)](https://jupyter.org/)

Un catalogue produit audité et un backlog priorisé par impact business, construits comme le ferait un Proxy Product Owner en charge d'un catalogue e-commerce, sur le même jeu de données que `customer-analytics-retail` mais avec un angle différent : produit, pas client.

## Objectif

Qualifier les anomalies d'un catalogue produit (libellés incohérents, codes non-produits, descriptions manquantes) et les prioriser par impact business réel plutôt qu'au hasard, avec un proxy de conversion (taux d'annulation) là où aucune donnée de navigation n'existe.

## Structure du projet

```
retail-product-catalog-analytics/
├── data/
│   └── raw/
│       ├── online_retail_II.xlsx        # source brute (non versionnée si volumineuse, voir .gitignore)
│       └── online_retail_ii.parquet     # cache pour un rechargement rapide
├── notebooks/
│   └── 01_catalog_quality_conversion.ipynb
├── reports/
│   └── *.png
├── DATA_CATALOG.md                      # dictionnaire de champs, glossaire, règles de qualité
├── BACKLOG.md                           # epics, user stories, KPIs, roadmap
├── requirements.txt
└── README.md
```

## Dataset

| | |
|---|---|
| Source | [Online Retail II, UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/502/online+retail+ii) |
| Volume | 1 067 371 lignes de transaction, décembre 2009 à décembre 2011 |
| Catalogué dans | `DATA_CATALOG.md` |

## Reproduire

```bash
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook notebooks/01_catalog_quality_conversion.ipynb
```

## Contenu du notebook

1. **Contexte et objectif**
2. **Audit qualité du catalogue** : 17 codes non-produits confirmés un par un via leur libellé (pas devinés sur un simple format), 1 230 codes à libellé incohérent, distinction entre codes jamais décrits (353, orphelins réels) et codes partiellement manquants mais récupérables (2 092).
3. **Impact business des anomalies** : chiffre d'affaires exposé par anomalie, pas juste un nombre de lignes.
4. **Produits les plus vendus croisés avec le statut qualité**, pour prioriser la correction là où l'impact est le plus fort.
5. **Taux d'annulation par produit**, utilisé comme proxy de conversion en l'absence de données de navigation.
6. **Synthèse** : ce qui a été livré, et les limites explicites.

## Principaux résultats (obtenus en exécutant le notebook)

- 17 codes confirmés non-produits (ajustements, frais, port, dons, tests) représentent 5 813 lignes (0,5 %), exclus avant tout calcul de performance produit.
- **46,1 % du chiffre d'affaires catalogue** (£20,1M au total) est exposé à un libellé incohérent, dont **7 des 10 produits les plus vendus**. C'est l'argument concret pour prioriser leur correction en premier.
- Les 353 codes jamais décrits représentent **£0 de chiffre d'affaires mesurable**, un résultat négatif gardé tel quel : ce sont probablement des lignes de test ou d'ajustement résiduel, pas une perte commerciale réelle.
- Le taux d'annulation le plus élevé se concentre sur une même famille de produits (variantes du même article), pas sur des incidents isolés, ce qui change la nature de la correction à prioriser (fiche produit ou process, pas un SKU au hasard).

## Stack

Python, pandas, matplotlib, seaborn, Jupyter, nbformat/nbclient (génération et exécution reproductible du notebook), Parquet pour le cache.

## Notes méthodologiques (pourquoi X plutôt que Y)

**Pourquoi vérifier chaque code non-produit individuellement plutôt qu'un filtre sur le format ?** Un filtre naïf sur le format (5 chiffres) exclut à tort des vrais produits comme `PADS` ou `SP1002`, qui ont un code non-standard mais un libellé de produit réel. Vérifier le libellé de chacun des 62 codes candidats a ramené la liste à 17 codes réellement non-produits.

**Pourquoi distinguer "jamais décrit" de "partiellement manquant" ?** Un code avec quelques lignes sans description mais au moins une ligne valide ailleurs est un problème de saisie récupérable par une jointure, pas un trou de catalogue. Confondre les deux aurait fait paraître le problème deux fois plus grave qu'il ne l'est.

**Pourquoi le taux d'annulation comme proxy de conversion ?** Ce jeu de données transactionnel n'a pas de données de navigation (pages vues, paniers abandonnés). Une commande annulée après passage est le signal le plus proche d'un problème rencontré par le client sur ce produit, disponible dans les données réellement présentes.

## Source des données

[Online Retail II, UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/502/online+retail+ii), distribué sous ses propres conditions d'utilisation, téléchargé séparément et non redistribué ici.

## Auteur

Juliette Bouli-Mengue
