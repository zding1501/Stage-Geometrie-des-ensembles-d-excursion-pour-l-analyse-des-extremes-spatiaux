# Géométrie des ensembles d'excursion pour l'analyse des extrêmes spatiaux

Ce dépôt contient le code Python développé dans le cadre d'un **stage de M2** portant sur l'analyse des extrêmes spatiaux (température et précipitations) en France, à partir des données grillées **SAFRAN**.

## Contexte scientifique

On construit le modèle mathématique suivant. Soit $(\Omega, \mathcal{F}, \mathbb{P})$ un espace de probabilité. Soit $X : \mathbb{R}^2 \times [0,T] \times \Omega \to \mathbb{R}$ un champ aléatoire défini sur $\mathbb{R}^2 \times [0,T]$. Soit $D \subset \mathbb{R}^2$ un sous-ensemble de $\mathbb{R}^2$, qui représente le domaine spatial, et $[0,T] \subset \mathbb{R}$ un ensemble du temps, qui représente chaque date, où $T$ est le nombre de jours observés.

On définit l'**ensemble d'excursion** de $X$ pour un seuil $u$ :

$$E_X(u,t) = \{s \in D \mid X(s,t) > u\}.$$

### Choix du seuil

Deux types de seuil sont utilisés pour définir ces ensembles :

- **seuil global constant** : un seuil unique $u(\alpha) = \hat{q}_\alpha(\{X(s,t) : s \in D, t \in [0,T]\})$, calculé sur l'ensemble des valeurs (toutes localisations et toutes dates confondues) ;
- **seuil spatial** : un seuil propre à chaque localisation, $u(\alpha, s) = \hat{q}_\alpha(\{X(s,t) : t \in [0,T]\})$, calculé à partir des seules observations temporelles au point $s$. Ce seuil s'adapte à la climatologie locale de chaque point de la grille plutôt que d'imposer un niveau uniforme sur tout le domaine.

On s'intéresse aux fonctionnelles géométriques de l'ensemble d'excursion pour chaque date $t$ :

- l'**aire** $A_t$,
- le **périmètre** $P_t$,
- le **nombre de composantes connexes** $N_t$,
- le **rayon maximal du disque inscrit** $R_{\max,t}$.

### Transformation de Pareto

Après une **transformation de Pareto marginale**

$$X^*(s) = \frac{1}{1 - F_s(X(s))}$$

qui standardise l'hétérogénéité spatiale des marges, l'objectif est de caractériser la dynamique temporelle des ensembles d'excursion (modélisation AR(1), extremogramme pour la dépendance de queue) et de valider la méthodologie par une approche **peaks-over-threshold (POT)** pour les précipitations, avec des QQ-plots exponentiels ($Z = \log X^* \sim \text{Exp}(1)$).

La validation est faite hors échantillon : estimation sur la période **1986–2001**, test sur **2002–2005**, avec une classification des stations en "chaud", "froid" ou "neutre" selon les résidus QQ.

## Contenu du dépôt

| Fichier | Description |
|---|---|
| `code_ziqin.ipynb` | Notebook principal : construction de la grille, calcul des composantes connexes et fonctionnelles géométriques, analyse de qualité temporelle, calcul des résidus QQ, classification des stations |

## Données

Les données SAFRAN utilisées pour ce projet sont disponibles ici : [Google Drive](https://drive.google.com/drive/folders/1pn6rDkmtR_9oWB_DSvADOJ-hu-94POmT?usp=drive_link)

## Fonctions principales

- `build_grid` — construction de la grille spatiale commune (distances haversine entre stations)
- `get_components_2d` — extraction des composantes connexes des ensembles d'excursion
- calcul de l'aire, du périmètre et du rayon du disque inscrit pour chaque composante
- `compute_qualite_temporelle` / `plot_qualite_temporelle` — analyse de la qualité temporelle des séries
- `qq_residue` — calcul des résidus QQ (avec branche POT dédiée aux précipitations, seuils estimés uniquement sur le train)
- validation hors échantillon (train 1986–2001 / test 2002–2005) et classification des stations en "chaud", "froid" ou "neutre" selon les résidus QQ

## Points méthodologiques à noter

- Pour les précipitations, la masse ponctuelle en zéro (jours secs) rend la transformation PIT non uniforme ; les QQ-plots sont donc restreints aux dépassements de seuil (exceedances), ce qui n'affecte pas l'analyse de queue.
- La fonction de répartition $\hat{F}_s$ est toujours estimée sur les données d'entraînement uniquement, afin d'éviter toute circularité dans la validation.

## Outils

Python (`pandas`, `NumPy`, `matplotlib`, `statsmodels`, `scikit-learn`), notamment `sklearn.metrics.pairwise.haversine_distances` et `statsmodels.tsa.ar_model.AutoReg`.

Le rapport complet (rédaction mathématique et interprétation des résultats) est rédigé séparément en LaTeX.
