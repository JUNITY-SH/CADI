# Instructions pour Claude Code — Recréation du rapport "Exécution — FIT Score" dans le projet CADI

## Objectif

Recréer dans le projet **CADI** (Power BI) une copie fonctionnelle du rapport `Exécution___Fit_Score.pbix`, actuellement connecté au Semantic Model Kronenbourg (`DatasetId: 2c56cc84-d5b8-4cc3-ad60-b18bf83751ff`, workspace `80936fe7-2e69-4666-af9f-4c6f88c9c2f4`).

## Contexte technique du rapport source

- **Format** : Power BI Desktop `.pbix` version 1.28
- **Mode connexion** : Live Connection (le rapport ne contient **aucune donnée** — tout vit dans le Semantic Model distant)
- **Taille du canvas** : 1280 × 720 (type 16:9)
- **Thème** : `CY21SU11` (thème Microsoft de base)
- **9 pages** au total, dont 1 seule visible (Accueil) — toutes les autres sont masquées (`visibility: 1`) et accessibles via bookmarks
- **81 bookmarks** — la navigation est entièrement pilotée par un système complexe de bookmarks et sous-menus
- **3 Custom Visuals** (marketplace) à réinstaller :
  - `CircularGauge` (jauge circulaire)
  - `multiInfoCards`
  - `payPalKPIDonutChart`

## Stratégie de recréation

Pour le projet CADI :

1. **Étape 1 — Modèle de données** : s'assurer que le modèle CADI expose les mêmes tables, colonnes et mesures que celles listées ci-dessous. Créer les mesures manquantes en DAX.
2. **Étape 2 — Squelette du rapport** : créer les 9 pages avec leurs noms exacts et masquer celles qui doivent l'être.
3. **Étape 3 — Page par page** : reconstruire les visuels en suivant les specs détaillées ci-après.
4. **Étape 4 — Navigation** : reconstruire le système de bookmarks (menus ouverts/fermés, panneaux de filtres, sous-menus).
5. **Étape 5 — Custom visuals** : importer les 3 custom visuals depuis le store Power BI.

---

## 1. Modèle de données requis

### 1.1 Tables utilisées (14)

| Table | Rôle dans le rapport |
|-------|---------------------|
| `Calendrier` | Table temps (Année, Quadrimestre, Trimestre) |
| `Clients` | Référentiel PDV avec typologie, socle, priorité |
| `Conformité` | Table de conformité / flags d'expression FIT |
| `ExportReleves` | Vue pour export des relevés (First / Last / Δ) |
| `Force de vente` | Hiérarchie géographique (Zone → DV → CD) |
| `Mesure (relevé)` | Table regroupant 59 mesures liées aux relevés |
| `Mesure (volume)` | Table mesure volume |
| `Objectif-FitScore` | Objectifs FitScore |
| `PerfectServe` | Composants du PerfectServe |
| `Produits` | Référentiel produits (marque, conditionnement, catégories) |
| `PromoByOffre` | Types d'offres promo |
| `Relevés` | Table relevés brute (flags premium) |
| `Relevés Summary` | Table synthèse relevés (année N-1) |
| `Relevés Summary (2)` | Table synthèse relevés v2 (classification) |

### 1.2 Mesures à implémenter (65)

> **Note** : Les mesures sont groupées par table de stockage. Les noms entre `[...]` sont les noms exacts à conserver pour que les références des visuels fonctionnent sans modification.

#### Table `Mesure (relevé)` (59 mesures)

```
[% Releves saisis]
[Activation]
[Activation promo(%)]
[Activation promo(Cal)]
[Brillance Conforme]
[Carte Boissons(%)]
[Carte Boissons(Cal)]
[Colonnes & Poignées (%)]
[Colonnes & Poignées(Cal)]
[DN (%All)]
[DN (All)]
[First Nb Becs]
[First YTD Détails]
[FitScore First (value)]
[FitScore Last (value)]
[Gain]
[Gain Nb Becs]
[Gamme Bouteille (%)]
[Gamme Bouteille(Cal)]
[Gamme Fut(%)]
[Gamme Fut(Cal)]
[Last Nb Becs]
[Last YTD Détails]
[Marque First rel (prem)]
[Marque Last rel (prem)]
[NbPDVScorable]
[Nbre Enquête]
[Nombre d'occurence (Offre)]
[Nombre releve]
[Obj. Becs TransfLager annuel (cdb) 2]
[Obj. BecsPremium annuel (cdb) 2]
[Obj. FitScore Periode]
[Obj. FitScore Q1]
[Obj. FitScore Q2]
[Obj. FitScore Q3]
[Obj. FitScore T4]
[Objectif proratisé Becs Big Bets]
[Perfect Serve (%)]
[Perfect Serve (Cal)]
[Perte]
[Perte Nb Becs]
[Prem vs obj 2]
[Realise BigBets (BB)]
[Realise BigBets Brooklyn]
[Réa(Cal)]
[Solde (Gain-Perte)]
[Solde Clients Premium 2]
[Solde Nb Becs]
[Transferts Lager]
[Univers (Becs)]
[Univers (Socle)]
[Univers (Socle) par période]
[Verre à la  Marque]     -- ⚠️ attention : double espace avant "Marque"
[Δ Light La Bête]
[Δ Nb Becs BB]
[Δ Nb Becs Détails]
[Δ vs Obj. Brooklyn]
[Δ vs Obj. La Bête]
[Δ vs Obj. TransLager]
```

#### Table `Clients` (5 mesures)

```
[Nombre Becs Tranfères]           -- ⚠️ "Tranfères" (faute d'orthographe conservée)
[Nombre Transfert Lager]
[Nombre_premiumisation]
[Obj. BecsPremium annuel (cdb)]
[Solde transfert Lager premium]
```

#### Table `Conformité` (1 mesure)

```
[Avg Exp Flag SommeColonneFit]
```

#### Table `Mesure (volume)` (1 mesure)

```
[Volume Obj Crafts (%)]
```

### 1.3 Colonnes utilisées par table

#### `Calendrier` (5)
`[Année]`, `[Année relative]`, `[Quadrimestre(Q)]`, `[Trimestre relatif]`, `[chr_Nom Trimestre]`

#### `Clients` (30)
`[CD]`, `[ClassePriorite_SousPriorite]`, `[Code PdV]`, `[Code Postal]`, `[Demande d'investissement]`, `[Entrepôt - Nom]`, `[EstAnnéeEnCours]`, `[FicheFIT]`, `[Full Client]`, `[IsValid Details Premiumisation]`, `[Nom]`, `[Premiumisation]`, `[Prémium ACQ]`, `[Prémium Hors-ACQ]`, `[Repartition score Fit]`, `[Secteur]`, `[Socle]`, `[Socle - Classe Priorité]`, `[Socle - Priorite]`, `[SoclePersonnalisé]`, `[Socle_Classe_Priorite]`, `[Sous-Enseigne]`, `[Statut]`, `[Trade Channel (niveau 4)]`, `[Typologie FIT]`, `[Typologie personnalisée]`, `[Ville]`, `[Z1]`, `[Z2]`, `[Zone]`

#### `Conformité` (26)
`[1664 Blanc]`, `[Autre]`, `[BecGroupe]`, `[Becs Bk + HOb + Concu]`, `[Blanche]`, `[BoutGroupe]`, `[Bouteille Bk + Hob + Concu]`, `[Brassin de saison]`, `[Carlsberg]`, `[Dégustation]`, `[EstScorable]`, `[Exp Flag ActivationPromo]`, `[Exp Flag Carte boisson]`, `[Exp Flag Colonne]`, `[Exp Flag Gamme Bouteille]`, `[Exp Flag Gamme Fût]`, `[Exp Flag PerfectServe]`, `[Exp Flag SommeColonneFit]`, `[IPA]`, `[Kanterbau]`, `[Kro R&W]`, `[La bête ambrée]`, `[Lager Premium]`, `[Regions]`, `[Repartition score Fit]`, `[Requis]`

#### `ExportReleves` (7)
`[Conditionnement]`, `[First]`, `[Last]`, `[Last - First]`, `[Marque]`, `[Société]`, `[Sous Marque]`

#### `Force de vente` (4)
`[CD]`, `[DV]`, `[Zone]`, `[Zone corrigée]`

#### `Objectif-FitScore` (1)
`[Objectif]`

#### `PerfectServe` (7)
`[Activation]`, `[Activation promo]`, `[Comunication]` (⚠️ faute conservée), `[Odeur conforme]`, `[Outils BK/HOB]`, `[Promo]`, `[Verre à la marque]`

#### `Produits` (9)
`[CategorieBigBets]`, `[CategoriePrem]`, `[Conditionnement]`, `[Marque]`, `[Niveau sup Big bets]`, `[Société]`, `[Sous Marque]`, `[Sous-Marque Prem]`, `[TypeCategorieSousFormat]`

#### `PromoByOffre` (2)
`[Offre]`, `[Offre (ordre)]`

#### `Relevés` (3)
`[IsPremium]`, `[Nb Lagers]`, `[Nb Lagers Premiums]`

#### `Relevés Summary` (2)
`[Full Client]`, `[LastYear]`, `[ComboBec]`, `[ComboBout]`, `[Classification Relevé]`, `[Date Formatée]`

#### `Relevés Summary (2)` (4)
`[Classification Relevé]`, `[Date Formatée]`, `[Full Client]`, `[MarqueBec]`

---

## 2. Filtres au niveau rapport

Quatre filtres appliqués à tout le rapport :

| Filtre | Type | Valeur par défaut |
|--------|------|-------------------|
| `Calendrier[Année relative]` | Categorical | **`'Année en cours'`** |
| `Calendrier[Trimestre relatif]` | Categorical | (aucune valeur par défaut — ouvert) |
| `Force de vente[DV]` | Categorical | (aucune valeur par défaut — ouvert) |
| `Clients[EstAnnéeEnCours]` | Categorical | (aucune valeur par défaut — ouvert) |

---

## 3. Spécifications page par page

### Page 0 — `Accueil` (visible, page d'entrée)

**Dimensions** : 1280 × 720
**Page active par défaut** : oui
**Filtre page** : `Clients[ClassePriorite_SousPriorite]`
**Visuels totaux** : 119 (dont 43 boutons + 18 "unknown"/bookmark visuals + 16 shapes + 16 text boxes + 10 cards + 7 images + 5 slicers + 2 clustered bar charts + 1 matrix + 1 hundredPercentStackedBar)

**Visuels de données clés** (19) — trié par position (y, x) :

| Type | Titre | x | y | w | h | Champs |
|------|-------|---|---|---|---|--------|
| card | Score FIT : Note | 0 | 0 | 233 | 118 | `Conformité[Avg Exp Flag SommeColonneFit]` |
| 100% stacked bar | Conformité | 0 | 0 | 383 | 134 | axe: `Conformité[Repartition score Fit]`, valeur: `Mesure (relevé)[Univers (Socle) par période]` |
| card | Gamme Bouteille % | 283 | 0 | 62 | 35 | `Mesure (relevé)[Gamme Bouteille(Cal)]` |
| card | Objectif FitScore | 277 | 0 | 229 | 116 | `Mesure (relevé)[Obj. FitScore Periode]` |
| card | % Relevés Saisis | 545 | 0 | 233 | 116 | `Mesure (relevé)[% Releves saisis]` |
| card | Gamme Fût % | 12 | 20 | 108 | 72 | `Mesure (relevé)[Gamme Fut(Cal)]` |
| card | Objectif FitScore | 763 | 25 | 230 | 90 | `SUM(Objectif-FitScore[Objectif])` |
| card | Carte Boisson % | 279 | 45 | 70 | 40 | `Mesure (relevé)[Carte Boissons(Cal)]` |
| clustered bar | Nombre de PDV | 28 | 90 | 412 | 261 | axe: `Clients[Socle_Classe_Priorite]`, valeur: `Mesure (relevé)[Univers (Socle)]` |
| card | Colonnes % | 283 | 92 | 69 | 35 | `Mesure (relevé)[Colonnes & Poignées(Cal)]` |
| slicer | Quadrimestre | 6 | 128 | 291 | 69 | `Calendrier[Quadrimestre(Q)]` |
| card | PerfectServe % | 283 | 138 | 69 | 36 | `Mesure (relevé)[Perfect Serve (Cal)]` |
| card | Promo % | 282 | 190 | 71 | 35 | `Mesure (relevé)[Activation promo(Cal)]` |
| slicer | Force de vente | 6 | 234 | 293 | 78 | `Force de vente[Zone]`, `Force de vente[CD]` (hiérarchique) |
| matrix | FitScore par secteur | 896 | 238 | 384 | 423 | lignes: `Clients[Typologie FIT]`, `Clients[Typologie personnalisée]`<br>valeurs: `Mesure (relevé)[Univers (Socle)]`, `Conformité[Avg Exp Flag SommeColonneFit]` |
| slicer | Typologie | 7 | 349 | 292 | 78 | `Clients[Typologie personnalisée]`, `Clients[Typologie FIT]` |
| clustered bar | FitScore | 28 | 402 | 413 | 261 | axe: `Clients[Socle_Classe_Priorite]`, valeurs: `FitScore First (value)` + `FitScore Last (value)` |
| slicer | Socle | 7 | 463 | 292 | 78 | `Clients[SoclePersonnalisé]`, `Clients[Socle_Classe_Priorite]` |
| slicer | EstScorable | 3 | 576 | 304 | 64 | `Conformité[EstScorable]` |

**Ressources statiques (images) utilisées sur cette page** (à réimporter) :
- `Fit_Score*.png` (logo Fit Score)
- `BRASSERIES_KRONENBOURG_LOGOTYP*.png` (logo Kronenbourg)
- `Bandeau_gauche_-_menu*.jpg` (bandeau décoratif gauche)
- `Big_Bets_copie*.jpg` (visuel Big Bets)
- `SLIDES_FIT_POUR_PBI_2026-*.png` (fonds de diapositives)
- `FiltreBlanc*.svg` (icône filtre)
- `Menu*.png` (icône menu)
- `croix*.png` (icône fermer)
- `pngtree-vector-house-icon*.png` (icône accueil)

---

### Page 1 — `Secteur` (masquée, accessible via bookmark)

**Dimensions** : 1280 × 720
**Visuels totaux** : 79

**Visuels de données** (8) :

| Type | Titre | Position | Champs |
|------|-------|----------|--------|
| slicer | (sans titre) | x=352 y=0, 590×78 | `Clients[Full Client]` |
| matrix | FitScore par secteur | x=0 y=32, 1224×456 | lignes: `Zone`/`CD`/`Full Client` · valeurs: `Obj. FitScore Q1`, `NbPDVScorable`, tous les `[Cal]` (Gamme Fut, Gamme Bouteille, Carte Boissons, Colonnes, Perfect Serve, Activation promo), `Exp Flag SommeColonneFit` (agg SUM) |
| matrix | Par Socle | x=29 y=112, 1223×534 | lignes: `Zone`/`CD` · valeurs: `Obj. FitScore Q1/Q2/Q3/T4`, `FitScore Last (value)`, tous les `(%)` (Gamme Fut, Gamme Bouteille, Carte Boissons, Colonnes, Perfect Serve, Activation promo) |
| slicer | Quadrimestre | x=6 y=135, 291×69 | `Calendrier[Quadrimestre(Q)]` |
| slicer | Force de vente | x=6 y=240, 293×78 | `Force de vente[Zone]`, `[CD]` |
| slicer | Typologie | x=7 y=355, 292×78 | `Clients[Typologie personnalisée]`, `[Typologie FIT]` |
| slicer | Socle | x=7 y=470, 292×78 | `Clients[SoclePersonnalisé]`, `[Socle_Classe_Priorite]` |
| slicer | EstScorable | x=8 y=576, 288×80 | `Conformité[EstScorable]` |

---

### Page 2 — `Export Surveys` (masquée)

**Dimensions** : 1280 × 720
**Visuels totaux** : 76

**Visuels de données** (8) :

| Type | Titre | Position | Champs clés |
|------|-------|----------|-------------|
| slicer | (sans titre) | x=352 y=0, 590×78 | `Clients[Full Client]` |
| **table (tableEx)** | **Export des relevés** | x=17 y=81, 1240×554 | **36 colonnes — voir détail ci-dessous** |
| matrix | Par Socle | x=29 y=112, 1223×534 | (idem page Secteur) |
| 5 slicers | Quadrimestre / Force de vente / Typologie / Socle / EstScorable | (idem) | (idem) |

**Colonnes de la table "Export des relevés" (page Export Surveys)** — 36 colonnes :
1. `Clients[Zone]`
2. `Clients[CD]`
3. `Clients[Z1]`
4. `Clients[Typologie FIT]`
5. `Clients[Entrepôt - Nom]`
6. `Conformité[Regions]`
7. `Conformité[BecGroupe]`
8. `Conformité[BoutGroupe]`
9. `Conformité[Exp Flag Gamme Bouteille]`
10. `Conformité[Exp Flag Gamme Fût]`
11. `Conformité[Exp Flag Colonne]`
12. `Conformité[Exp Flag Carte boisson]`
13. `Conformité[Exp Flag PerfectServe]`
14. `Conformité[Exp Flag ActivationPromo]`
15. `Conformité[Requis]`
16. `Conformité[Lager Premium]`
17. `Conformité[Dégustation]`
18. `Conformité[La bête ambrée]`
19. `Conformité[Blanche]`
20. `Conformité[IPA]`
21. `Conformité[Brassin de saison]`
22. `Conformité[Autre]`
23. `Conformité[Kro R&W]`
24. `Conformité[Kanterbau]`
25. `Conformité[Carlsberg]`
26. `Conformité[1664 Blanc]`
27. `Conformité[EstScorable]`
28. `Clients[Full Client]`
29. `SUM(Conformité[Becs Bk + HOb + Concu])`
30. `SUM(Conformité[Bouteille Bk + Hob + Concu])`
31. `Clients[FicheFIT]`
32. `Clients[Trade Channel (niveau 4)]`
33. `Clients[Socle - Classe Priorité]`
34. `Clients[Socle]`
35. `Clients[Demande d'investissement]`
36. `SUM(Conformité[Exp Flag SommeColonneFit])`

---

### Page 3 — `PerfectServe` (masquée)

**Dimensions** : 1280 × 720
**Visuels totaux** : 90

**Visuels de données** (11) :

| Type | Titre | Position | Champs |
|------|-------|----------|--------|
| matrix | FitScore par secteur | x=0 y=30, 466×437 | lignes: Zone/CD/Full Client · valeurs: `Verre à la  Marque`, `Activation` |
| card | Objectif (%Craft) | x=416 y=72, 259×97 | `Mesure (relevé)[Nbre Enquête]` |
| card | Objectif (%Craft) | x=711 y=72, 242×97 | `Mesure (relevé)[Verre à la  Marque]` |
| card | Objectif (%Craft) | x=982 y=72, 263×97 | `Mesure (relevé)[Activation]` |
| clustered bar | Type d'offre (TOP 20) | x=18 y=77, 376×536 | axe: `PromoByOffre[Offre]` · valeur: `Mesure (relevé)[Nombre d'occurence (Offre)]` (avec Top N = 20) |
| clustered bar | Type d'offre (Full) | x=215 y=124, 871×431 | axe: `PromoByOffre[Offre]` · valeur: `Nombre d'occurence (Offre)` (sans Top) |
| slicer | Quadrimestre | x=6 y=128, 291×69 | `Calendrier[Quadrimestre(Q)]` |
| pie chart | Communication | x=416 y=187, 361×467 | légende: `PerfectServe[Comunication]` · valeur: `Nombre d'occurence (Offre)` |
| slicer | Force de vente | x=6 y=234, 293×78 | idem |
| slicer | Typologie | x=7 y=349, 292×78 | idem |
| slicer | Socle | x=7 y=463, 292×78 | idem |

---

### Page 4 — `Base Calcul FIT` (masquée)

**Dimensions** : 1280 × 720
**Visuels totaux** : 157 (très chargée en boutons + images + textboxes — beaucoup d'explications visuelles)

**Visuels de données** (3) — juste les slicers :

| Type | Position | Champs |
|------|----------|--------|
| slicer | Force de vente | idem |
| slicer | Typologie | idem |
| slicer | Socle | idem |

**Cette page contient surtout des éléments pédagogiques** (formules, schémas d'explication, exemples de calcul) réalisés avec :
- 82 boutons (probablement un système d'onglets à bookmark pour naviguer entre 7 sous-sections du bookmark "12- Base de calcul conformité")
- 28 "unknown" (certainement des bookmark visuals)
- 18 text boxes (descriptions des règles de calcul)
- 14 shapes
- 12 images (captures d'écran d'explications)

👉 **Les 7 sous-bookmarks à recréer** (voir section Bookmarks) :
- Fit Score
- Relevé Fût
- Visibilité Bouteille
- Visibilité TP
- Visibilité Carte
- Promotion
- Qualité

---

### Page 5 — `Lager` (masquée)

**Dimensions** : 1280 × 720
**Visuels totaux** : 111

**Visuels de données** (11) — plusieurs superposés (bookmark-driven : "Détails Transfert Lager" et "Détails prémiumisation" sont alternativement visibles) :

| Type | Titre | Position | Champs |
|------|-------|----------|--------|
| card | (sans titre) | x=51 y=80, 234×132 | `Clients[Nombre Becs Tranfères]` |
| card | (sans titre) | x=51 y=228, 234×144 | `Clients[Nombre Transfert Lager]` |
| card | (sans titre) | x=51 y=386, 234×144 | `Clients[Solde transfert Lager premium]` |
| **matrix (vue synthèse)** | (sans titre) | x=101 y=86, 1086×526 | lignes: `Force de vente[DV]`, `[Zone]`, `[CD]` · valeurs: `Obj. BecsPremium annuel (cdb) 2`, `Solde Clients Premium 2`, `Prem vs obj 2`, `Obj. Becs TransfLager annuel (cdb) 2`, `Transferts Lager`, `Δ vs Obj. TransLager` |
| **table (vue PDV)** | (sans titre) | x=102 y=86, 1086×526 | colonnes: `Clients[Full Client]`, `[SoclePersonnalisé]`, `Produits[Conditionnement]`, `[Marque]`, `[Sous Marque]`, `Force de vente[CD]`/`[DV]`/`[Zone]` · mesures: `Nombre releve`, `Δ Nb Becs Détails` |
| **matrix Détails Transfert Lager** | x=328 y=87, 906×548 | lignes: `Force de vente[DV]`, `Clients[Zone]`/`[CD]`/`[SoclePersonnalisé]`/`[Full Client]`, `Produits[CategoriePrem]` · valeurs: `Δ Nb Becs Détails`, `Last YTD Détails`, `First YTD Détails` |
| **table Détails prémiumisation** | x=101 y=91, 1088×518 | colonnes: `Clients[SoclePersonnalisé]`/`[Zone]`/`[CD]`/`[Full Client]`/`[Demande d'investissement]` · mesures: `Marque First rel (prem)`, `Marque Last rel (prem)`, `SUM(Clients[Prémium ACQ])`, `SUM(Clients[Prémium Hors-ACQ])`, `Transferts Lager` |
| 4 slicers | (idem autres pages) | | Force de vente, Typologie, Socle, EstScorable |

---

### Page 6 — `Bodega` (masquée)

**Dimensions** : 1280 × 720
**Visuels totaux** : 86
**Filtres page** : `Produits[CategorieBigBets]`, `Produits[Marque]`, `Produits[TypeCategorieSousFormat]`, `Produits[Sous Marque]`

**Visuels de données** (8) :

| Type | Titre | Position | Champs |
|------|-------|----------|--------|
| table | (sans titre) | x=102 y=86, 1086×526 | colonnes: `Relevés Summary (2)[Full Client]`, `Clients[Zone]`/`[CD]`/`[Typologie personnalisée]`, `Relevés Summary (2)[Date Formatée]`/`[MarqueBec]` |
| matrix | Détails Gain/Perte Becs Bodega | x=322 y=102, 909×516 | lignes: `Force de vente[DV]`, `Clients[Zone]`/`[CD]`/`[SoclePersonnalisé]`/`[Full Client]`, `Produits[Sous Marque]` · valeur: `Δ Nb Becs Détails` |
| card | (sans titre) | x=26 y=134, 275×137 | `Mesure (relevé)[Δ Nb Becs Détails]` |
| card | (sans titre) | x=28 y=432, 275×143 | `Mesure (relevé)[Realise BigBets Brooklyn]` |
| 4 slicers | | | Force de vente, Typologie, Socle, EstScorable |

---

### Page 7 — `Page noé` (masquée, 1 seul visuel)

**Dimensions** : 1280 × 720
**Visuels totaux** : 1 (table unique)

**Table unique** :
- Colonnes : `Clients[Zone]`, `Clients[CD]`, `Relevés Summary[Full Client]`, `Clients[Typologie personnalisée]`, `Relevés Summary[Date Formatée]`, `Relevés Summary[Classification Relevé]`, `Relevés Summary[ComboBec]`, `Relevés Summary[ComboBout]`

👉 Cette page est probablement un export de travail spécifique (pour un utilisateur nommé "Noé" ?). À garder telle quelle.

---

### Page 8 — `Export CODIR` (masquée)

**Dimensions** : 1280 × 720
**Visuels totaux** : 2 (1 table + 1 bouton)

**Table "Export des relevés"** (24 colonnes) :
1. `Clients[Zone]`
2. `Clients[Z1]`
3. `Clients[Typologie FIT]`
4. `Clients[Entrepôt - Nom]`
5. `Clients[Full Client]`
6. `Clients[Trade Channel (niveau 4)]`
7. `Clients[Socle]`
8. `Clients[Z2]`
9. `Clients[Code PdV]`
10. `Clients[Nom]`
11. `Clients[Code Postal]`
12. `Clients[Ville]`
13. `Clients[Statut]`
14. `Clients[Sous-Enseigne]`
15. `Clients[FicheFIT]`
16. `Clients[Socle - Priorite]`
17. `Clients[Demande d'investissement]`
18. `ExportReleves[Société]`
19. `ExportReleves[Marque]`
20. `ExportReleves[Sous Marque]`
21. `ExportReleves[Conditionnement]`
22. `SUM(ExportReleves[First])`
23. `SUM(ExportReleves[Last])`
24. `SUM(ExportReleves[Last - First])`

---

## 4. Système de bookmarks (81 bookmarks)

La navigation est **entièrement pilotée par bookmarks**. Il faut recréer cette structure.

### 4.1 Bookmarks principaux (22 "top-level" avec sous-bookmarks)

| # | Nom | Page cible | Sous-bookmarks |
|---|-----|-----------|----------------|
| 1 | **1- Accueil** | Accueil | Ouvrir/Fermer filtre, Ouvrir/Fermer menu, Réinitialiser filtre |
| 2 | **2- Secteur** | Secteur | Ouvrir/Fermer filtre, Ouvrir/Fermer menu |
| 3 | **3- Détails Grimbergen** | Détails Grimbergen | Ouvrir/Fermer filtre, Réinitialiser filtre, Ouvrir/Fermer menu |
| 4 | **4- Détails BigBets** | Détails Big-Bets | Ouvrir/Fermer filtre, Réinitialiser, Ouvrir/Fermer menu |
| 5 | **5- Détails Prémiumisation** | Détails Prémiumisation | (idem) |
| 6 | **6- Export avec ciblage** | ExportCiblage | Réinitialiser filtre, Ouvrir menu, FermerMenuExportCiblage, FermerFiltreExportCiblage, OuvirFiltreExportCiblage |
| 7 | **7- Rélevés Marques** | RelevesMarques | Ouvrir/Fermer filtre marque, Ouvrir vue marque/pdv, Ouvrir/Fermer menu marque, Réinitialiser filtre marque, Ouvrir/Fermer filtre pdv, Réinitialiser, Ouvrir/Fermer export codir |
| 8 | **8- Fiche PDV (FitScore)** | FichePdV | Ouvrir/Fermer menu, Ouvrir/Fermer filtre, Ouvrir/Fermer détails présence, Réinitialiser filtre |
| 9 | **9- Exports (FitScore)** | Exports | Ouvrir/Fermer filtre, Ouvrir/Fermer menu |
| 10 | **10- PerfectServe** | PerfectServe | Ouvrir/Fermer filtre, Ouvrir/Fermer plus offres, Ouvrir/Fermer menu, Réinitialiser filtre |
| 11 | **11- Export avec classification** | ExportClassification | OuvrirMenuExpClassif, FermerMenuExpClassif |
| 12 | **12- Base de calcul conformité** | Base de calcul conformité | Ouvrir/Fermer menu, **7 sous-bookmarks** : Calcul conformité - Fit Score / Relevé Fût / Visibilité Bouteille / Visibilité TP / Visibilité Carte / Promotion / Qualité |
| 13 | **13- Exports liste des PDV** | Exports PDV | Ouvrir/Fermer menu, Ouvrir/Fermer Filtre Export |
| 14 | **14- Lager** | | Ouvrir/Fermer Menu Lager, Ouvrir/Fermer Filtre Lager |
| 15 | **15- BigBets** | | Ouvrir/Fermer Menu BigBets, Ouvrir/Fermer Filtre BigBets |
| 16 | **16- Grimbergen** | | Ouvrir/Fermer Menu Grimbergen, Ouvrir/Fermer Filtre Grimbergen |
| 17 | **17- Fit- Acc** | | Ouvrir/Fermer Menu Fit-Acc |
| 18 | **18- Fit- sect** | | Ouvrir/Fermer Menu Fit-Sect |
| 19 | **19- Fit- pdv** | | Ouvrir/Fermer Menu Fit-pdv |
| 20 | **20 - Fit- Perfect** | | Ouvrir/Fermer Menu Anim |
| 21 | **21- Fit Base** | | Ouvrir/Fermer Menu Base |
| 22 | **22- Export** | | Ouvrir/Fermer Menu Export |

### 4.2 Bookmarks "techniques" (pilotés par boutons, ~60 bookmarks)

Grandes familles :
- **Sous-menus FIT** : Ouvrir/Fermer SM-Fit-{Lager, BigBets, Grimbergen, Sect, pdv, Anim, Base, Exp}
- **Sous-menus Pass** : Ouvrir/Fermer SM-Pass-{Lager, BigBets, Grimbergen}
- **Sous-menus FichePdv** : Ouvrir/Fermer SM-FichePdv-{Lager, BigBets, Grimbergen, Fit}
- **Plans d'action** : Ouvrir/Fermer plan-d'action
- **Résultat objectifs** : Ouvrir/Fermer resultat-objectif
- **Synthèses ACQ / Non ACQ** : pour Grimbergen et BigBets
- **Filtres spécifiques** : Ouvrir/Fermer filtre pour Lager-new, BigBets
- **Affichage Détail prémiumisation** : Afficher/Fermer Detail Prem

### 4.3 Recréation pratique

**Dans Claude Code / Power BI Desktop**, chaque bookmark doit capturer :
- L'état de visibilité des visuels (panneau sélection)
- Les filtres actifs
- La page courante (sauf pour les bookmarks "menu" qui gardent la même page mais changent la visibilité)

> ⚠️ **Méthode recommandée** : Ne PAS cocher "Données" dans les options du bookmark pour les bookmarks de type "Ouvrir/Fermer menu" — seulement "Affichage" et "Page active". Sinon les filtres utilisateur seront écrasés à chaque clic sur un bouton de menu.

---

## 5. Custom visuals à importer

Dans Power BI Desktop → **... → Obtenir plus de visuels** → chercher et importer :

1. **Circular Gauge** (par MAQ Software) — utilisé probablement sur les cards "Objectif (%Craft)" de la page PerfectServe
2. **Multi Info Cards** (par appsource) — probablement utilisé pour afficher plusieurs KPIs compactés
3. **PayPal KPI Donut Chart** — utilisé pour des donuts KPI (conformité, score FIT...)

---

## 6. Étapes recommandées pour Claude Code

### Étape 1 — Vérification du modèle CADI

```
Ouvre le projet CADI (.pbip ou .pbix converti).
Vérifie que toutes les tables listées en section 1.1 existent avec les colonnes de la section 1.3.
Liste les mesures manquantes en comparant avec la section 1.2.
```

### Étape 2 — Créer les mesures manquantes

Pour chaque mesure manquante, demander à Claude Code de :
1. Chercher dans le `model.bim` / `model.tmdl` si une mesure équivalente existe avec un autre nom → proposer un renommage plutôt qu'une duplication
2. Sinon, créer la mesure avec un commentaire `// TODO: valider la formule DAX avec l'équipe métier` car les formules sources sont dans le Semantic Model distant (pas récupérables depuis le `.pbix` analysé)

### Étape 3 — Squelette du rapport (format PBIP/TMDL)

Créer 9 pages avec les noms exacts :
- `Accueil` (visible)
- `Secteur` (hidden)
- `Export Surveys` (hidden)
- `PerfectServe` (hidden)
- `Base Calcul FIT` (hidden)
- `Lager` (hidden)
- `Bodega` (hidden)
- `Page noé` (hidden)
- `Export CODIR` (hidden)

Appliquer le thème `CY21SU11` (fichier JSON extrait du .pbix source disponible).

Appliquer les 4 filtres au niveau rapport (section 2).

### Étape 4 — Reconstruire page par page

Utiliser les tableaux de la section 3 pour placer chaque visuel avec ses bons champs, sa bonne position et sa bonne taille.

### Étape 5 — Bookmarks & navigation

À faire **en dernier**, une fois tous les visuels en place :
1. Recréer les 22 bookmarks principaux
2. Recréer les ~60 bookmarks techniques (menus ouverts/fermés)
3. Lier les boutons aux bookmarks

---

## 7. Points d'attention / pièges connus

| Point | Attention |
|-------|-----------|
| `[Verre à la  Marque]` | **Double espace** avant "Marque" — faute conservée pour compatibilité |
| `[Nombre Becs Tranfères]` | **"Tranfères"** avec faute — conservée |
| `PerfectServe[Comunication]` | **Un seul "m"** — conservée |
| Table `Relevés Summary` vs `Relevés Summary (2)` | **Deux tables distinctes**, ne pas les fusionner |
| Mesures `(Cal)` vs `(%)` | `(Cal)` retourne probablement la **valeur calculée** pondérée/brute ; `(%)` le **pourcentage** — ce sont deux mesures différentes par indicateur |
| Mesures avec suffixe ` 2` | Ex: `Obj. BecsPremium annuel (cdb) 2` — ce sont des **versions remaniées** des mesures originales ; attention à ne pas les confondre |
| Visuels superposés (Lager, Bodega) | Les tables/matrices à la même position sont **pilotées par bookmark** — une seule visible à la fois |

---

## 8. Fichiers ressources du rapport source

Liste des images/ressources statiques embarquées dans le `.pbix` (41 fichiers) — à récupérer pour refaire l'habillage :
- Logos Kronenbourg : `BRASSERIES_KRONENBOURG_LOGOTYP*.png`, `KRO_LogoGroup*.png`
- Logos FIT : `Fit_Score*.png`
- Bandeaux : `Bandeau_gauche_-_menu*.jpg`, `Execution_-_FIT_score_copie*.jpg`
- Slides explicatives : `SLIDES_FIT_POUR_PBI_2026-*.png` (4 fichiers, pour la page Base Calcul)
- Gammes : `Gamme_Bouteilles*.png`, `Gamme_Fut*.png`, `Lager*.jpg`
- Icônes : `Menu*.png`, `croix*.png`, `FiltreBlanc*.svg`, `pngtree-vector-house-icon*.png`
- Big Bets : `Big_Bets_copie*.jpg`
- Décors : `7064491-fond-de-gouttes-d-eau*.png`, `7269138*.png`

> Si tu veux les récupérer : dézippe le `.pbix` et tu les trouveras dans `Report/StaticResources/RegisteredResources/`.

---

## 9. Résumé en 1 coup d'œil

- **9 pages** (1 visible + 8 masquées), dimensions 1280×720
- **14 tables** mobilisées
- **66 mesures** à vérifier/créer (59 dans `Mesure (relevé)`, 5 dans `Clients`, 1 dans `Conformité`, 1 dans `Mesure (volume)`)
- **100+ colonnes** à référencer
- **4 filtres rapport** (dont 1 avec valeur par défaut : `Année relative = 'Année en cours'`)
- **81 bookmarks** (22 principaux + 59 techniques)
- **3 custom visuals** à importer
- **Thème** : `CY21SU11`
- **41 fichiers** ressource (images/SVG)
- **Navigation** : bookmark-driven via boutons (système complexe de menus/sous-menus/filtres ouvrables)
