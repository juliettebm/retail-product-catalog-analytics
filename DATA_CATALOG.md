# Catalogue de données : Product Catalog & Transactions (Online Retail II)

Contrairement au projet `customer-analytics-retail` (centré client : segmentation, churn), ce catalogue est centré **produit** : c'est le référentiel qu'un Product Owner en charge d'un catalogue de données regarderait avant de lancer un chantier de nettoyage ou un dashboard de suivi catalogue.

## 1. Domaines métier

| Domaine | Champs | Rôle |
|---|---|---|
| Catalogue produit (Master Data) | `StockCode`, `Description` | Référentiel produit, cœur de ce catalogue |
| Transaction | `Invoice`, `Quantity`, `Price`, `InvoiceDate` | Données transactionnelles, volatiles |
| Client | `Customer ID` | Référentiel client (voir aussi `customer-analytics-retail` pour le détail) |
| Géographie | `Country` | Référentiel pays |

## 2. Dictionnaire de champs

| Champ | Type | Définition métier | Constat qualité |
|---|---|---|---|
| `Invoice` | str, 6 chiffres, préfixe `C` si annulation | Identifiant de facture/commande | 19 494 lignes (1,8 %) sont des annulations (préfixe `C`) |
| `StockCode` | str | Identifiant produit, clé du catalogue | 5 305 codes uniques, mais 62 ne suivent pas le format standard 5 chiffres (voir Q1) |
| `Description` | str | Libellé produit | 4 382 lignes (0,4 %) sans description ; 1 232 codes produit ont plusieurs libellés différents (voir Q2) |
| `Quantity` | int | Quantité vendue (négative si annulation) | 3 455 lignes à quantité négative sans être des annulations (voir Q6) |
| `InvoiceDate` | datetime | Date/heure de la transaction | Décembre 2009 à décembre 2011 |
| `Price` | float (GBP) | Prix unitaire | 6 207 lignes à prix ≤ 0 (voir Q3) |
| `Customer ID` | float (identifiant) | Identifiant client | 243 007 lignes (22,8 %) sans identifiant client (achats invités, hors périmètre des KPIs par client) |
| `Country` | str | Pays de livraison | RAS |

## 3. Glossaire métier

- **Code non-produit** : un `StockCode` qui ne désigne pas un article physique vendu, mais un ajustement comptable, des frais, un test, ou un geste commercial (port, remise, etc.). Il pollue le catalogue produit s'il n'est pas distingué explicitement.
- **Anomalie catalogue** : un produit dont la fiche (description) est manquante ou incohérente (plusieurs libellés pour un même code), indépendamment de ses ventes.
- **Impact business d'une anomalie** : dans ce projet, mesuré comme le chiffre d'affaires généré par les codes produits concernés par une anomalie catalogue, pas comme une estimation qualitative. C'est ce chiffre qui sert à prioriser le backlog (`BACKLOG.md`).

## 4. Règles de qualité identifiées (auditées dans le notebook, sections 3 et 4)

| Règle | Constat | Action recommandée |
|---|---|---|
| **Q1 : codes non-produits mélangés au catalogue** | 62 `StockCode` sur 5 305 ne suivent pas le format standard (ex. `POST`, `BANK CHARGES`, `ADJUST`, `AMAZONFEE`, `DOT`, `M`, `C2`, `TEST001`, `TEST002`) | À isoler du catalogue produit avant tout calcul de performance produit, ce sont des frais/ajustements, pas des articles |
| **Q2 : incohérence de libellé** | 1 232 codes produit (23 % du catalogue) ont plusieurs descriptions différentes selon les lignes (fautes de frappe, variantes, mises à jour non rétroactives) | Nécessite une règle de dédoublonnage (ex. description la plus fréquente par code), documentée ici pour que le choix soit traçable, pas caché dans le code |
| **Q3 : prix incohérents** | 6 207 lignes à prix ≤ 0 | À ne pas supprimer aveuglément : certains sont des annulations légitimes, d'autres des erreurs de saisie, distingués dans le notebook |
| **Q4 : description manquante** | 4 382 lignes (0,4 %) sans libellé produit | Le `StockCode` reste exploitable, mais le produit est invisible dans toute restitution basée sur le libellé |
| **Q5 : identifiant client absent** | 22,8 % des lignes n'ont pas de `Customer ID` (achats invités) | Hors périmètre de toute analyse au niveau client, mais valide pour l'analyse au niveau produit menée ici |
| **Q6 : ajustements de stock déguisés en transactions** | 3 455 lignes à `Quantity` négative sans être des annulations (pas de préfixe `C` sur `Invoice`), toutes à `Price` nul, libellées *lost*, *damages*, *short*, *mixed*. Trouvé dans l'analyse univariée, pas dans l'audit Q1-Q4 initial, ces codes ont un format standard donc échappent à Q1 | N'affecte pas les calculs de chiffre d'affaires (prix nul), mais à exclure explicitement si `Quantity` est réutilisé seul (ex. volume physique mouvementé) sans filtrer sur le prix |

## 5. Utilisation

Ce catalogue encadre le notebook `notebooks/01_catalog_quality_conversion.ipynb` et le backlog `BACKLOG.md`. Il est volontairement scindé du catalogue "client" du projet `customer-analytics-retail`, un vrai référentiel Master Data sépare ses domaines plutôt que de tout documenter dans un seul fichier fourre-tout.
