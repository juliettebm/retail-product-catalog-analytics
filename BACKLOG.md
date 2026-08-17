# Backlog produit : Qualité catalogue & priorisation par impact business

Ce backlog simule le travail d'un Proxy Product Owner en charge d'un catalogue produit e-commerce : qualifier les anomalies, mesurer leur impact business, prioriser les corrections. Le notebook (`notebooks/01_catalog_quality_conversion.ipynb`) est le livrable qui y répond.

## Contexte produit

Le catalogue produit contient des anomalies connues (codes non-produits mélangés aux articles, libellés incohérents, prix aberrants). Avant de corriger quoi que ce soit, il faut savoir combien ça coûte de ne pas corriger, sinon la priorisation est arbitraire.

## Epics

### Epic 1 — Fiabiliser le catalogue produit (Master Data)

| # | User story | Critères d'acceptation | Priorité |
|---|---|---|---|
| 1.1 | En tant que Data Product Owner, je veux un catalogue de produits documenté et audité afin que les équipes commerciales ne travaillent pas sur des fiches produit dupliquées ou incohérentes | Chaque anomalie de `DATA_CATALOG.md` (Q1-Q5) est quantifiée en nombre de lignes concernées | Must |
| 1.2 | En tant que Data Product Owner, je veux isoler les codes non-produits (frais, ajustements, tests) du catalogue produit afin que les KPIs de performance produit ne soient pas pollués | Une liste explicite des codes exclus est documentée, pas un filtre implicite dans le code | Must |

### Epic 2 — Prioriser les corrections par impact business et conversion

| # | User story | Critères d'acceptation | KPI associé | Priorité |
|---|---|---|---|---|
| 2.1 | En tant que Proxy Product Owner, je veux connaître le chiffre d'affaires exposé par les anomalies catalogue afin de prioriser les corrections par impact plutôt qu'au hasard | Le CA lié aux produits en anomalie (Q2, Q4) est calculé et comparé au CA total | CA exposé / CA total | Must |
| 2.2 | En tant que Proxy Product Owner, je veux suivre le taux d'annulation par produit afin de qualifier les anomalies qui affectent réellement la conversion, pas seulement la donnée | Les produits sont classés par taux d'annulation, avec effectif de commandes affiché à côté | Taux d'annulation par `StockCode` | Should |
| 2.3 | En tant que Proxy Product Owner, je veux un classement des produits par performance (meilleures/pires ventes) afin d'allouer l'effort de correction aux produits qui comptent le plus | Le classement croise performance business et présence d'anomalie catalogue | Top/bottom produits par CA, croisé avec statut qualité | Should |

## Roadmap simplifiée

- **Sprint 1** : Epic 1 (fiabiliser le catalogue) — condition bloquante.
- **Sprint 2** : Epic 2 (chiffrer l'impact, prioriser) — cœur de la valeur, c'est la partie "Proxy PO" du backlog.

## Definition of Done

Une user story est "faite" si l'anomalie est quantifiée (nombre de lignes ET impact business en CA), pas juste listée.

## Note de transparence

Comme pour le projet HR, ce backlog est construit en solo sur un jeu de données public, sans vrai cycle de retours utilisateurs. La méthode de priorisation par impact business est réelle et appliquée dans le notebook, mais aucune décision n'a été validée par une vraie équipe métier.
