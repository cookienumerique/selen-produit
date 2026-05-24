# Roadmap Selen — Plan de déblocage

> **Dernière mise à jour :** 2026-05-24
> **Owner :** Mickaël (founder solo)
> **Statut :** Sortie de pause produit — phase activation / discovery

## Point de situation au 24/05/2026

### Ce qu'on sait (donnée brute, pas opinion)
- 180 users total, **13 actifs** sur 15 derniers jours, **0 €** de revenue
- Cohorte produit mature post-10/03/2026 (125 users) :
  - **63 % n'activent jamais** (42 % aucune capsule, 21 % J0 seulement)
  - 16 % retenus à J14+ (vrais utilisateurs avec habitude)
  - **43 % des activés sont là à J14+** → le produit tient quand on y entre
- Phase recherche en cours :
  - Segment capsule_j0 (22 users) : emails envoyés 16/05
  - Segment meteo_uniquement_j0 (31 users) : envoi prévu 17/05 — **statut à confirmer**

### Diagnostic figé
- Le problème prioritaire est **l'activation**, pas la rétention ni le hook
- Selen a été mise en pause faute de méthode — les erreurs à ne pas reproduire :
  1. Coder une solution avant d'avoir interrogé les users
  2. Mesurer l'activité (vanity) au lieu de la valeur (actionable)
  3. Empiler des features pour résoudre un problème non diagnostiqué
  4. Fuir vers la monétisation pour éviter le diagnostic dur

### Ce qu'on ne sait pas (à instruire)
- Pourquoi les 53 « jamais ouvert » n'ont pas ouvert (verbatims à venir via Mom Test segment 2)
- Pourquoi les 26 « J0 seulement » ne sont pas revenues (verbatims à venir via Mom Test segment 1)
- Si les 53 ont reçu une notification de bienvenue (instrumentation à vérifier)
- Le job-to-be-done réel des 20 utilisatrices retenues J14+ (à interviewer en + après les churned)

---

## Roadmap

### 🔥 Phase 1 — Diagnostic (semaines du 26/05 au 23/06 — 4 semaines)

**Objectif :** comprendre AVANT de décider. Pas de code produit. Pas de nouvelle feature. Pas de monétisation.

| # | Action | Owner | Agent à consulter | Échéance | Métrique de succès |
|---|---|---|---|---|---|
| 1.1 | Confirmer envoi segment 2 Mom Test (31 emails meteo_uniquement_j0) | Mickaël | — | 26/05 | Drafts envoyés, statut confirmé |
| 1.2 | Auditer la traçabilité notifs/emails côté API (les 53 « jamais ouvert » ont-elles reçu quelque chose ?) | Mickaël + `selen-data-analyst` | `selen-data-analyst` | 28/05 | Verdict écrit : trace oui/non, gap d'instrumentation chiffré |
| 1.3 | Récupérer les premières réponses Mom Test, anonymiser, coder en thèmes | Mickaël + `selen-ux-researcher` | `selen-ux-researcher` | 03/06 | 12+ verbatims codés, 3-5 thèmes stabilisés |
| 1.4 | Relancer les non-répondeurs (segment 1 + segment 2 si applicable) | Mickaël | `selen-ux-researcher` | 30/05 | Relance envoyée, +5 à +10 réponses attendues |
| 1.5 | Identifier dans la BDD les variables qui distinguent activés vs non-activés (heure d'inscription, source, device, opt-in push) | `selen-data-analyst` | — | 10/06 | Top 3 variables différenciantes documentées |
| 1.6 | Interviewer 3-5 utilisatrices retenues J14+ (segment ignoré jusque-là) | Mickaël + `selen-ux-researcher` | `selen-ux-researcher` | 17/06 | JTBD du « happy path » formulé |
| 1.7 | Audit éthique des contenus capsule existants et de « réponse de la lune » | `selen-psychologue` | — | 23/06 | Note de risques + recommandations correctives priorisées |

**Livrable phase 1 :** un document de 2 pages max — *« Diagnostic activation Selen »* — avec :
- Les 3 raisons confirmées du décrochage pré-J1 (citations de verbatim)
- Les 3 raisons confirmées du décrochage J0 seulement
- Le aha-moment hypothétique formulé (à valider en phase 2)
- La cible comportementale prioritaire (pas démographique)
- 1 expérimentation d'activation prête à lancer en phase 2

**Garde-fou phase 1 :** si à mi-parcours (10/06) Mickaël se surprend à coder une feature, `selen-devil-advocate` est appelé immédiatement.

---

### 🧪 Phase 2 — Première expérimentation d'activation (semaines du 24/06 au 21/07 — 4 semaines)

**Objectif :** lancer UNE expérimentation issue du diagnostic, la mesurer rigoureusement, en tirer une décision.

| # | Action | Owner | Agent à consulter | Échéance |
|---|---|---|---|---|
| 2.1 | Concevoir l'expérimentation (hypothèse, métrique, durée, segment) | `selen-growth-activation` | `selen-ux-researcher`, `selen-psychologue` | 26/06 |
| 2.2 | Instrumenter les KPIs manquants (notifs sent/delivered/clicked si gap identifié en 1.2) | Mickaël (dev) | `selen-data-analyst` | 03/07 |
| 2.3 | Lancer l'expérimentation | Mickaël | `selen-growth-activation` | 07/07 |
| 2.4 | Mesurer + lire les résultats sans biais de confirmation | `selen-data-analyst` + `selen-devil-advocate` | — | 21/07 |

**Livrable phase 2 :** un mémo *« Résultats expérimentation activation #1 »* — succès, échec ou inconclusif clairement formulé. Si succès : industrialiser. Si échec : retour phase 1 sur un autre angle. Si inconclusif : redesign de l'expérimentation.

**Garde-fou phase 2 :** UNE expérimentation à la fois. Pas deux en parallèle. Lecture des résultats sans cherry-picking.

---

### 🔄 Phase 3 — Itération activation jusqu'à seuil (à partir du 22/07)

**Critère de sortie de phase 3 :** taux d'activation post-onboarding ≥ 50 % sur 2 cohortes consécutives (vs 37 % actuel).

**Méthode :** boucle Build-Measure-Learn hebdomadaire. UNE expérimentation par sprint, mesurée proprement, archivée dans `docs/decisions-produit.md`.

**Sujets à ne PAS aborder en phase 3 (sauf accord explicite de `selen-devil-advocate`) :**
- Monétisation
- Nouvelles fonctionnalités produit non liées à l'activation
- Refonte UI globale
- Acquisition payante
- Levée de fonds

---

### 💰 Phase 4 — Validation business model (pas avant Q3 2026)

**Pré-requis pour démarrer :**
- Taux d'activation ≥ 50 % sur 2 cohortes consécutives
- ≥ 100 utilisatrices actives W4 sur 15 derniers jours
- Verbatims clairs sur la disposition à payer (au moins 5 interviews avec engagement explicite)

Avant cette phase, toute discussion sur le pricing est renvoyée vers `selen-devil-advocate`.

---

## Cadence de revue

| Rituel | Fréquence | Format |
|---|---|---|
| Stand-up Mickaël | Quotidien (5 min) | « Hier / aujourd'hui / blocage » écrit dans `docs/journal.md` (à créer) |
| Revue hebdo avec agents | Vendredi (1h max) | Synthèse phase en cours + KPIs + décisions à prendre la semaine d'après |
| Pre-mortem | Trimestriel (1h) | Avec `selen-devil-advocate` : « si Selen est morte dans 12 mois, qu'est-ce qui s'est passé ? » |
| Audit éthique | Avant chaque nouvelle feature touchant l'expérience émotionnelle | Avec `selen-psychologue` |

---

## Comment utiliser cette roadmap

1. Cette roadmap est **vivante** — Mickaël la met à jour à chaque fin de phase, et chaque agent peut la référencer pour situer sa contribution
2. À chaque décision majeure : noter dans `docs/decisions-produit.md` la décision + le rationale + la date
3. À chaque hypothèse formulée : la consigner dans `docs/hypotheses.md` avec statut À tester / Validée / Invalidée
4. À chaque donnée nouvelle : l'archiver dans `docs/validations.md`

> ⚠️ Si à un moment Mickaël se demande « est-ce que je devrais faire X ? » et que X n'apparaît pas dans la roadmap de la phase en cours, la réponse par défaut est **non** — sauf si un agent à consulter le valide explicitement.
