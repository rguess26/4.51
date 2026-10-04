# Collection Équinoxe — Prévision de la hausse de loyer 2026

## Objectif

Ce projet vise à estimer la hausse des loyers de la Collection Équinoxe en 2026 à partir des historiques de baux, loyers demandés et concessions fournis dans le cadre du défi JADCO.

L'indicateur principal retenu est la **croissance médiane annualisée du loyer effectif (`sRentEffective`) à unité constante**. Autrement dit, nous comparons le nouveau bail d'un logement à son propre bail précédent, plutôt que de comparer directement les loyers moyens ou médians du portefeuille d'une année à l'autre.

### Estimation finale

**Hausse estimée des loyers en 2026 : 4,51 %**

Plage de sensibilité empirique : **3,04 % à 5,99 %**.

La méthode obtient une MAE d'environ **1,48 point de pourcentage** sur les backtests 2023, 2024 et 2025.

---

## Contenu du projet

Le livrable principal est le notebook Jupyter :

- `starter.ipynb` — analyse complète, fonctions `estimate_2026()` et `backtest()`, graphiques, backtests et estimation finale ;
- `README.md` — documentation d'exécution et références ;
- `requirements.txt` — dépendances Python utilisées.

Les quatre fichiers de données fournis par JADCO ne sont **pas redistribués** avec le livrable, puisqu'ils contiennent des données CRM.

Aucun fichier de modèle entraîné n'est requis : la méthode finale est **déterministe** et est recalculée directement à partir des données.

---

## Données nécessaires

Pour exécuter le notebook, placer les quatre fichiers CSV suivants dans le **même dossier** que le notebook :

```text
equinoxe_listings.csv
equinoxe_lease_history.csv
equinoxe_concessions.csv
equinoxe_asking_history.csv
```

Structure recommandée :

```text
projet/
├── starter.ipynb
├── README.md
├── requirements.txt
├── equinoxe_listings.csv
├── equinoxe_lease_history.csv
├── equinoxe_concessions.csv
└── equinoxe_asking_history.csv
```

> Les fichiers CSV sont nécessaires pour reproduire l'analyse, mais ne doivent pas être inclus dans un livrable public.

---

## Installation et exécution

### 1. Créer un environnement Python

Python **3.11.9** a été utilisé pour le développement.

Exemple avec `venv` :

```bash
python -m venv .venv
```

Activation sous Windows :

```bash
.venv\Scripts\activate
```

Activation sous macOS / Linux :

```bash
source .venv/bin/activate
```

### 2. Installer les dépendances

```bash
pip install -r requirements.txt
```

Versions utilisées lors de l'exécution finale :

| Bibliothèque | Version |
|---|---:|
| Python | 3.11.9 |
| pandas | 2.2.3 |
| NumPy | 2.2.4 |
| Matplotlib | 3.10.1 |

### 3. Lancer Jupyter

```bash
jupyter notebook
```

Ouvrir ensuite `starter.ipynb`.

### 4. Exécuter le notebook

Dans Jupyter :

1. redémarrer le kernel ;
2. exécuter toutes les cellules **de haut en bas** ;
3. vérifier qu'aucune cellule ne retourne d'erreur.

Le notebook effectue notamment des contrôles automatiques sur les colonnes nécessaires, les dates, les identifiants d'unités et la cohérence des clés avant de poursuivre l'analyse.

---

## Méthode

L'analyse repose sur quatre principes principaux.

### 1. Comparer le même logement à lui-même

Les variations brutes du portefeuille peuvent être influencées par l'arrivée de nouveaux immeubles ou par un changement dans la proportion de studios, 1 chambre, 2 chambres, etc.

Pour éviter cet effet de composition, les baux successifs d'une même unité sont appariés et leur évolution est annualisée.

### 2. Mesurer le loyer réellement capté

Le loyer contractuel (`sRent`) peut surestimer la croissance économique lorsque des promotions ou concessions sont accordées.

La variable principale utilisée est donc `sRentEffective`, qui reflète mieux le loyer capté après concessions.

### 3. Distinguer les situations de marché

L'analyse sépare notamment :

- les renouvellements et les changements de locataires ;
- le Québec et l'Ontario ;
- les résultats globaux et les résultats par immeuble.

The Met est traité séparément dans l'analyse réglementaire en raison du cadre ontarien.

### 4. Prévoir à partir du signal de prix et des concessions

La logique centrale est :

$$
\text{Prévision}_{t} = \text{Croissance des loyers demandés}_{t-1} - \text{Impact des concessions}_{t-1}
$$

Pour 2026 :

\[
7,98\% - 3,47\% = 4,51\%
\]

Les données de marché externes servent à **vérifier la cohérence économique** du résultat. Elles ne sont pas intégrées à la formule à l'aide de pondérations arbitraires.

---

## Fonctions demandées

Le notebook contient les deux fonctions demandées dans l'énoncé :

```python
estimate_2026(leases, asking, external=None)
```

Cette fonction retourne l'estimation centrale de la hausse de loyer 2026.

```python
backtest(leases, target_year, asking_data=None)
```

Cette fonction reproduit la méthode de prévision pour une année passée, en utilisant uniquement les informations disponibles avant l'année cible, puis compare la prévision au résultat réellement observé.

Les backtests principaux sont effectués sur **2023, 2024 et 2025**.

---

## Validation et limites

La méthode est évaluée par backtest et comparée à plusieurs baselines simples.

Résultats principaux :

- estimation 2026 : **4,51 %** ;
- MAE 2023–2025 : **≈ 1,48 pp** ;
- plage de sensibilité empirique : **3,04 % à 5,99 %** ;
- méthode alternative de mesure des concessions : **4,63 %**, soit seulement **0,12 pp** d'écart avec l'estimation principale.

La plage 3,04 %–5,99 % est une **analyse de sensibilité empirique**, et non un intervalle de confiance statistique.

Les principales limites sont le faible nombre d'années disponibles pour le backtest, l'incertitude sur les concessions futures et la possibilité d'un changement de régime du marché locatif.

Le jeu de données fourni ne permet pas non plus de reconstruire de manière fiable le taux d'occupation global du portefeuille.

---

## Sources publiques

### Société canadienne d'hypothèques et de logement — SCHL

**Rental Market Report / Enquête sur les logements locatifs**, éditions 2022 à 2025.

Utilisation : évolution des loyers, taux d'inoccupation et contexte locatif à Montréal et Ottawa.

https://www.cmhc-schl.gc.ca/professionals/housing-markets-data-and-research/market-reports/rental-market-reports-major-centres

Consulté le 3 octobre 2026.

### Statistique Canada

**Tableau 18-10-0004-01 — Indice des prix à la consommation, mensuel, non désaisonnalisé**, composante loyers.

Utilisation : évolution des loyers au Québec et en Ontario.

https://www150.statcan.gc.ca/n1/daily-quotidien/251021/cg-a005-fra.htm

Consulté le 3 octobre 2026.

### Gouvernement de l'Ontario

**Residential rent increases / 2026 Rent Increase Guideline**

Utilisation : cadre réglementaire applicable aux hausses de loyer en Ontario et analyse de The Met.

https://www.ontario.ca/page/residential-rent-increases

Consulté le 3 octobre 2026.

### Tribunal administratif du logement — Québec

**Le calcul de l'ajustement des loyers en 2026**

Le pourcentage publié en janvier 2026 est documenté uniquement comme contexte postérieur à la date limite de prévision et n'est pas utilisé dans le calcul central.

https://www.tal.gouv.qc.ca/fr/actualites/detail?code=le-calcul-de-l-ajustement-des-loyers-en-2026

Consulté le 3 octobre 2026.

---

## Utilisation de l'intelligence artificielle

**ChatGPT (OpenAI)** a été utilisé comme outil d'assistance pour :

- expliquer certains concepts immobiliers et statistiques ;
- structurer l'analyse et le notebook ;
- proposer et réviser certaines portions de code ;
- soutenir la recherche et la vérification documentaire ;
- améliorer la présentation du raisonnement et des résultats.

L'IA n'a pas été utilisée comme source de vérité pour les résultats numériques. Les calculs, sorties, hypothèses, choix méthodologiques et références ont été exécutés, inspectés et validés dans le notebook par l'équipe.

---

## Confidentialité des données

Les fichiers CRM fournis dans le cadre du défi sont utilisés uniquement pour l'analyse et ne doivent pas être publiés ou redistribués.

Le notebook et la documentation présentent uniquement des résultats agrégés nécessaires à l'analyse.

---

## Résultat final

\[
\boxed{\text{Hausse estimée des loyers en 2026 : } 4,51\%}
\]

Définition : **croissance médiane annualisée du loyer effectif à unité constante**.
