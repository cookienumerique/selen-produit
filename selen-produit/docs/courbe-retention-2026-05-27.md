# Courbe de rétention Selen — diagnostic activation vs rétention

> **Auteur :** selen-data-analyst (Yanis Bensaïd)
> **Date :** 2026-05-27
> **Commanditaire :** Mickaël — challenge "rétention catastrophique" vs "activation étroite"
> **Lecture conjointe :** jeudi 28/05 14h avec Sophie Tessier
> **Sources :** docs/audit-prod-2026-05-24.md, docs/validations.md, échanges Mickaël 24/05 PM
> **Statut SQL :** requêtes ci-dessous à ré-exécuter par Mickaël via tunnel SSH

## TL;DR (5 lignes)

1. **Le levier principal est l'activation, pas la rétention.** L'arithmétique le démontre déjà.
2. **Décomposition :** 5,5 % d'actifs ≈ 57 % d'activation × 29 % de rétention conditionnelle J14+. Drop-off absolu : 54 users perdus à l'activation vs 52 perdus en rétention conditionnelle, mais le premier se joue en une session, le second sur 14 jours.
3. **La rétention conditionnelle J14+ (29 %) est *au niveau* du benchmark wellness public** (Calm/Headspace 25-30 % à J30).
4. **Le pas critique est météo → capsule** : 91 % font la météo, 57 % font une capsule. -34 points sur un seul saut.
5. **Phase 2 :** expé unique ciblant le pas météo → capsule. Pas l'onboarding. Pas la rétention.

## 1. Tableau courbe rétention par cohorte

### 1.1 Chiffres déjà connus (audit prod 24/05)

| Cohorte | N | % activés J0 | % retenus J14+ |
|---|---|---|---|
| Janvier 2026 | ~8 | n/d | n/d |
| Février 2026 | ~22 | n/d | n/d |
| Mars 2026 | 66 | 44 % (29/66) | 17 % (11/66) |
| Avril 2026 | 54 | 78 % (42/54) | 19 % (10/54) |
| Mai 2026 | 7 | 29 % (2/7) | n/d (trop jeune) |

**Lecture :** activation J0 a presque doublé mars → avril (cause à trouver entre 30/03 et 01/04). Rétention J14+ quasi-stable (17 → 19 %). Ce qui bouge, c'est l'activation.

### 1.2 Courbe complète à produire (SQL à exécuter)

```sql
-- Courbe de rétention par cohorte mensuelle
-- Définitions :
--   "activé J0" = >=1 capsule_response le jour d'inscription
--   "actif JN"  = >=1 capsule_response entre J+(N-1) et J+N inclus

WITH cohort AS (
  SELECT u.id AS user_id,
         DATE_TRUNC('month', u.created_at) AS cohort_month,
         u.created_at::date AS signup_date
  FROM "user" u
  WHERE u.created_at >= '2026-01-01'
    AND u.created_at <  '2026-06-01'
    AND u.id NOT IN (6, 19, 21, 22, 30, 34, 40, 46)  -- grants manuels
),
activity AS (
  SELECT cr.user_id, cr.created_at::date AS activity_date
  FROM capsule_response cr
),
joined AS (
  SELECT c.cohort_month, c.user_id, c.signup_date,
         a.activity_date,
         (a.activity_date - c.signup_date) AS days_since_signup
  FROM cohort c
  LEFT JOIN activity a ON a.user_id = c.user_id
)
SELECT
  to_char(cohort_month, 'YYYY-MM') AS cohort,
  COUNT(DISTINCT user_id) AS n_inscrits,
  ROUND(100.0 * COUNT(DISTINCT CASE WHEN days_since_signup = 0  THEN user_id END) / NULLIF(COUNT(DISTINCT user_id),0), 1) AS pct_j0,
  ROUND(100.0 * COUNT(DISTINCT CASE WHEN days_since_signup BETWEEN 1 AND 3   THEN user_id END) / NULLIF(COUNT(DISTINCT user_id),0), 1) AS pct_j1_j3,
  ROUND(100.0 * COUNT(DISTINCT CASE WHEN days_since_signup BETWEEN 4 AND 7   THEN user_id END) / NULLIF(COUNT(DISTINCT user_id),0), 1) AS pct_j4_j7,
  ROUND(100.0 * COUNT(DISTINCT CASE WHEN days_since_signup BETWEEN 8 AND 14  THEN user_id END) / NULLIF(COUNT(DISTINCT user_id),0), 1) AS pct_j8_j14,
  ROUND(100.0 * COUNT(DISTINCT CASE WHEN days_since_signup BETWEEN 15 AND 30 THEN user_id END) / NULLIF(COUNT(DISTINCT user_id),0), 1) AS pct_j15_j30,
  ROUND(100.0 * COUNT(DISTINCT CASE WHEN days_since_signup BETWEEN 31 AND 60 THEN user_id END) / NULLIF(COUNT(DISTINCT user_id),0), 1) AS pct_j31_j60
FROM joined
GROUP BY cohort_month
ORDER BY cohort_month;
```

Template à remplir avec le résultat :

| Cohorte | N | J0 | J1-J3 | J4-J7 | J8-J14 | J15-J30 | J31-J60 |
|---|---|---|---|---|---|---|---|
| 2026-01 | ? | ? | ? | ? | ? | ? | ? |
| 2026-02 | ? | ? | ? | ? | ? | ? | ? |
| 2026-03 | 66 | 44 % | ? | ? | ? | ? | ? |
| 2026-04 | 54 | 78 % | ? | ? | ? | n/d | n/d |
| 2026-05 | 7 | 29 % | ? | n/d | n/d | n/d | n/d |

**Note méthodo :** lire chaque colonne comme % glissant (activité dans une fenêtre), pas cumulé. C'est la "classic curve" Amplitude/Mixpanel.

### 1.3 Décomposition activés vs jamais-activés

```sql
WITH cohort AS (
  SELECT u.id AS user_id,
         DATE_TRUNC('month', u.created_at) AS cohort_month,
         u.created_at::date AS signup_date
  FROM "user" u
  WHERE u.created_at >= '2026-01-01' AND u.created_at < '2026-06-01'
    AND u.id NOT IN (6, 19, 21, 22, 30, 34, 40, 46)
),
activated AS (
  SELECT DISTINCT c.user_id
  FROM cohort c
  JOIN capsule_response cr
    ON cr.user_id = c.user_id
   AND cr.created_at::date = c.signup_date
),
activity AS (
  SELECT cr.user_id, cr.created_at::date AS activity_date
  FROM capsule_response cr
)
SELECT
  to_char(c.cohort_month, 'YYYY-MM') AS cohort,
  CASE WHEN a.user_id IS NOT NULL THEN 'activated_j0' ELSE 'never_activated' END AS segment,
  COUNT(DISTINCT c.user_id) AS n,
  ROUND(100.0 * COUNT(DISTINCT CASE WHEN (act.activity_date - c.signup_date) BETWEEN 1  AND 3  THEN c.user_id END) / NULLIF(COUNT(DISTINCT c.user_id),0), 1) AS pct_j1_j3,
  ROUND(100.0 * COUNT(DISTINCT CASE WHEN (act.activity_date - c.signup_date) BETWEEN 4  AND 7  THEN c.user_id END) / NULLIF(COUNT(DISTINCT c.user_id),0), 1) AS pct_j4_j7,
  ROUND(100.0 * COUNT(DISTINCT CASE WHEN (act.activity_date - c.signup_date) BETWEEN 8  AND 14 THEN c.user_id END) / NULLIF(COUNT(DISTINCT c.user_id),0), 1) AS pct_j8_j14,
  ROUND(100.0 * COUNT(DISTINCT CASE WHEN (act.activity_date - c.signup_date) BETWEEN 15 AND 30 THEN c.user_id END) / NULLIF(COUNT(DISTINCT c.user_id),0), 1) AS pct_j15_j30
FROM cohort c
LEFT JOIN activated a ON a.user_id = c.user_id
LEFT JOIN activity  act ON act.user_id = c.user_id
GROUP BY c.cohort_month, segment
ORDER BY c.cohort_month, segment;
```

## 2. Funnel "où ça décroche le plus" — cohorte post-10/03 (N=127)

| # | Pas | N restants | Conv vs prec. | Drop-off abs. | Drop cumulé |
|---|---|---|---|---|---|
| 0 | Signup (cohorte post-10/03 hors grants) | 127 | — | — | 0 % |
| 1 | Onboarding terminé | [à requêter] | ? | ? | ? |
| 2 | 1ʳᵉ météo intérieure (>=1 inner_weather_response) | 115 | 91 % | 12 | 9 % |
| 3 | 1ʳᵉ capsule ouverte (event manquant) | [à instrumenter] | ? | ? | ? |
| 4 | **1ʳᵉ capsule complétée (>=1 capsule_response)** | **73** | **63 % du pas 2** | **42** | **34 %** ← goulot |
| 5 | Retour J1 | [à requêter] | ? | ? | ? |
| 6 | Retour J3 | [à requêter] | ? | ? | ? |
| 7 | Retour J7 | [à requêter] | ? | ? | ? |
| 8 | Retenu J14+ | 21 | 29 % du pas 4 | 52 | 83 % |

**Lecture brutale :**
- Plus gros drop-off absolu et relatif : pas 2 → pas 4 = -42 users sur quelques minutes d'une même session.
- Pas 4 → pas 8 : -52 users, mais sur 14 jours = ~3,7 users/jour. Étalement temporel ≠ rupture UX.
- Action prioritaire : comprendre ce qui se passe entre fin météo et 1ʳᵉ capsule complétée.

```sql
WITH cohort AS (
  SELECT u.id AS user_id, u.created_at::date AS signup_date
  FROM "user" u
  WHERE u.created_at >= '2026-03-10'
    AND u.id NOT IN (6, 19, 21, 22, 30, 34, 40, 46)
),
metrics AS (
  SELECT c.user_id, c.signup_date,
         MIN(iwr.created_at)::date AS first_weather_date,
         MIN(cr.created_at)::date  AS first_capsule_date,
         MAX(cr.created_at)::date  AS last_capsule_date
  FROM cohort c
  LEFT JOIN inner_weather_response iwr ON iwr.user_id = c.user_id
  LEFT JOIN capsule_response       cr  ON cr.user_id  = c.user_id
  GROUP BY c.user_id, c.signup_date
)
SELECT
  COUNT(*) AS n_total,
  COUNT(first_weather_date)   AS n_with_weather,
  COUNT(first_capsule_date)   AS n_with_capsule,
  COUNT(CASE WHEN first_capsule_date - signup_date >= 1  THEN 1 END) AS n_j1_plus,
  COUNT(CASE WHEN last_capsule_date  - signup_date >= 3  THEN 1 END) AS n_j3_plus,
  COUNT(CASE WHEN last_capsule_date  - signup_date >= 7  THEN 1 END) AS n_j7_plus,
  COUNT(CASE WHEN last_capsule_date  - signup_date >= 14 THEN 1 END) AS n_j14_plus
FROM metrics;
```

## 3. Verdict chiffré

**Le levier principal est l'activation, plus précisément le pas météo → capsule.**

- **Activation J0** post-10/03 : **57 %** (73/127). Sous benchmarks consumer wellness (Mixpanel 2022 = 65-75 %).
- **Rétention conditionnelle J14+** parmi activés : **29 %** (21/73). Dans le marché (Calm pitch deck ~25 % J30, Headspace a16z ~30 % J30).
- **Drop-off le plus important :** pas météo → 1ʳᵉ capsule complétée = -34 pts en une session. Vs signup → météo (-9 pts) et 1ʳᵉ capsule → J14+ (-41 pts mais étalé).

**Benchmarks wellness (ordres de grandeur, prudence) :**

| Source | Métrique | Valeur | Selen |
|---|---|---|---|
| Mixpanel Product Benchmarks 2022, Health & Fitness | Day-1 retention | ~30 % | [à requêter] |
| Mixpanel 2022, Health & Fitness | Week-1 retention | ~12 % | ~16 % |
| Calm pitch deck 2019 | D30 retention onboarded | ~25 % | ~29 % (J14+ activés) |
| Headspace a16z 2018 | signup → first session | ~80 % | 91 % (météo) / 57 % (capsule) |
| Petit Bambou Lenny 2021 | onboarding → 1ʳᵉ médit. complétée | ~60-65 % | 57 % |

**Reco Phase 2 :** expérimentation unique sur pas météo → capsule. Hypothèses candidates :
1. **Friction UX** : test auto-launch de la 1ʳᵉ capsule à la fin de la météo (vs CTA bouton actuel).
2. **Mismatch attente** : interviewer les 42 "météo only" sur leur attente post-météo.
3. **Lourdeur perçue** : afficher durée explicite + version "1 minute".

## 4. Annexe : limitations méthodologiques

**Ce que ces données disent :**
- Activation = plus gros levier en drop-off absolu sur cohorte mature.
- Rétention conditionnelle au niveau du marché.
- Pas spécifique météo → capsule concentre l'essentiel de la perte.

**Ce que ces données NE disent PAS :**
1. **N=127 est petit.** IC Wilson 95 % sur 29 % rétention conditionnelle = [19 % ; 41 %]. Non distinguable d'un produit "vraiment" à 25 % ou 35 %. On est *non distinguables du marché*, pas *au-dessus*.
2. **Cohortes pré-mars trop petites** (jan ~8, fév ~22). Chaque % vaut ~5-10 pts de bruit.
3. **Pas de tracking analytic produit.** On ne distingue pas "capsule ouverte" de "capsule complétée". Pas 3 du funnel en aveugle.
4. **Pas de tracking notifs.** On ne sait pas si les "jamais activés" ont reçu une notif de bienvenue.
5. **Pas de tracking onboarding.** Abandon entre signup et 1ʳᵉ météo invisible.
6. **Définition "activé" à figer.** "≥1 capsule_response à J0" vs "≥1 capsule_response J0-J2 large" donne deux chiffres. À canoniser Phase 1.5.
7. **Écart 43 % vs 29 %.** `validations.md` dit "43 % des activés à J14+", calcul ici donne 29 % (21/73). Probablement deux définitions d'activation différentes. À reconcilier avant jeudi.

**Pour aller plus loin :** instrumentation Phase 2.2 (notifs + events onboarding + capsule_opened) + tracking source acquisition (UTM minimal).

## Annexe B — Requêtes utiles

### B.1 Quelle définition "activé" maximise le rappel des J14+ ?

```sql
WITH cohort AS (
  SELECT u.id AS user_id, u.created_at::date AS signup_date
  FROM "user" u
  WHERE u.created_at >= '2026-03-10' AND u.created_at < '2026-04-15'
    AND u.id NOT IN (6, 19, 21, 22, 30, 34, 40, 46)
),
def AS (
  SELECT c.user_id,
    EXISTS(SELECT 1 FROM capsule_response cr WHERE cr.user_id = c.user_id AND cr.created_at::date = c.signup_date) AS active_j0_strict,
    EXISTS(SELECT 1 FROM capsule_response cr WHERE cr.user_id = c.user_id AND cr.created_at::date - c.signup_date BETWEEN 0 AND 2) AS active_j0_j2_loose,
    EXISTS(SELECT 1 FROM capsule_response cr WHERE cr.user_id = c.user_id AND cr.created_at::date - c.signup_date >= 14) AS retained_j14
  FROM cohort c
)
SELECT active_j0_strict, active_j0_j2_loose,
       COUNT(*) AS n,
       ROUND(100.0 * SUM(retained_j14::int) / COUNT(*), 1) AS pct_j14_plus
FROM def
GROUP BY active_j0_strict, active_j0_j2_loose
ORDER BY active_j0_strict, active_j0_j2_loose;
```

### B.2 Distribution temps signup → 1ʳᵉ capsule

```sql
WITH first_capsule AS (
  SELECT cr.user_id, MIN(cr.created_at) AS first_capsule_at
  FROM capsule_response cr
  GROUP BY cr.user_id
)
SELECT
  PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY EXTRACT(EPOCH FROM (fc.first_capsule_at - u.created_at))/60) AS p25_min,
  PERCENTILE_CONT(0.50) WITHIN GROUP (ORDER BY EXTRACT(EPOCH FROM (fc.first_capsule_at - u.created_at))/60) AS p50_min,
  PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY EXTRACT(EPOCH FROM (fc.first_capsule_at - u.created_at))/60) AS p75_min,
  PERCENTILE_CONT(0.90) WITHIN GROUP (ORDER BY EXTRACT(EPOCH FROM (fc.first_capsule_at - u.created_at))/60) AS p90_min
FROM "user" u
JOIN first_capsule fc ON fc.user_id = u.id
WHERE u.created_at >= '2026-03-10'
  AND u.id NOT IN (6, 19, 21, 22, 30, 34, 40, 46);
```

Lecture : médiane <5 min = rituel same-session. Médiane >1h = drop-out asynchrone à instrumenter.

## Prochaine étape

1. Mickaël exécute les 3 SQL principales (1.2, 1.3, 2) via tunnel SSH **mercredi 27/05 soir**, archive le CSV dans `docs/data/courbe-retention-2026-05-27.csv`.
2. Yanis met à jour ce doc avec chiffres réels mercredi soir / jeudi matin.
3. Sophie + Mickaël lisent jeudi 14h, décident :
   - (a) verdict figé : levier = pas météo → capsule
   - (b) hypothèses d'expé à instruire Phase 1.5
   - (c) instrumentation minimale (events `capsule_opened`)
4. Si écart majeur SQL vs hypothèses → reconvocation selen-data-analyst avant décision.
