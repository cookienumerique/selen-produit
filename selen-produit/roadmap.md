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

### 🔒 Phase A — Mise en conformité RGPD (semaine du 26/05 — 1 semaine)

**Objectif :** rendre Selen légalement défendable AVANT toute relance d'acquisition. Bloque la suite de la roadmap.

**Pourquoi maintenant :** Selen traite des données concernant la santé au sens RGPD art. 9 (journal_entry, inner_weather_response, ai_response — 6/6 critères CNIL cochés). Politique de confidentialité existante mais lacunaire sur 5 points critiques. Aucun consentement explicite à l'inscription. Chaque nouveau signup sans correction = risque structurel cumulé.

| # | Action | Owner | Agent | Échéance | Métrique de succès |
|---|---|---|---|---|---|
| A.1 | Dev écran consentement RGPD (2 boutons : "J'accepte tout" / "Continuer sans la réponse de la lune" + lien politique) + migration `User.consent_at` / `consent_version` / `consent_ai_optin` + endpoint `POST /api/users/consent` + gate AI côté API (pas de middleware bloquant) + bloquage root layout front | Mickaël | `selen-data-analyst` (specs) + `selen-psychologue` (verdict opt-in vraie 2026-05-25) | 27/05 | Code livré + déployé en staging |
| A.2 | Réécriture politique de confidentialité : compléter les 5 sections manquantes (identité légale + SIRET, mention art. 9, transfert USA + SCC OpenAI, droit CNIL, hébergeur Ionos nominatif) **+ ajouter table par finalité (nom, base légale, destinataires, durée conservation, transfert hors UE) — pré-requis pour que l'écran consentement simplifié 2-boutons reste valide (consentement éclairé art. 13)** | Mickaël | `selen-psychologue` (validation éthique) + `selen-operating-partner` (draft si demandé) | 28/05 | Politique v2 publiée sur instantselen.fr |
| A.3 | OpenAI API : activer l'opt-out training (https://platform.openai.com/docs/data-usage-policies). Vérifier les logs des 30 derniers jours qui peuvent encore exister chez OpenAI | Mickaël | `selen-data-analyst` | 28/05 | Configuration vérifiée, capture d'écran |
| A.4 | Tester `DeleteMe` en prod : créer un compte test, le supprimer, vérifier en BDD que toutes les tables filles sont vides | Mickaël | `selen-data-analyst` | 29/05 | Compte test bien purgé partout |
| A.5 | Force re-consentement des 180 users existants au prochain login (modal bloquant) | Mickaël | — | 29/05 | Déployé en prod |
| A.6 | Documenter le registre des traitements RGPD art. 30 (modèle CNIL) | Mickaël | `selen-operating-partner` | 30/05 | Doc créé dans `docs/registre-traitements-rgpd.md` |
| A.7 | **1.25.1 — écran d'accueil avant la modale de consent**, uniquement pour les new signups (les 180 existantes en re-consent gardent la modale directe). 1 écran simple : 1 phrase d'accueil + 1 bouton "Continuer". Pattern : Headspace, Petit Bambou. Reportée en 1.25.1 (vs 1.25.0) suite verdict `selen-operating-partner` 25/05 — trade-off entre risque clinique faible volume (~1-2 new signups/sem) et goulot critique du ship 1.25.0 (Phase A à fermer, AI rétabli pour les 180). | Mickaël | `selen-psychologue` (validation copy de l'écran) + `selen-operating-partner` (priorisation) | 05/06 | Code livré, ship 1.25.1 sur les stores, écran visible chez les nouveaux comptes uniquement |
| A.8 | **Politique : mention de la granularité du consentement IA** — ajouter une phrase en section 5 indiquant que le retrait du consentement IA est possible depuis Paramètres → Confidentialité sans perte d'accès au reste de l'app. Côté tech c'est déjà fait, juste à documenter. | Mickaël | — | 02/06 | Phrase publiée dans la politique |
| A.9 | **Seuil d'âge : décider 17 ou 18 ans** — sur app santé mentale, 17 ans implique consentement parental art. 8 + art. 9 RGPD. Reco `selen-psychologue` 25/05 : remonter à 18 ans (alignement Petit Bambou, Calm). Si maintien à 17 : ouvrir un projet "dispositif mineur" séparé (consentement parental, audit capsules par psy ado, modération). | Mickaël | `selen-psychologue` + `selen-operating-partner` | 04/06 | Décision actée dans `docs/decisions-produit.md` + CGU mise à jour |
| A.10 | **Protocole de crise dans la politique** — ajouter une section "Si tu traverses une crise" en tête de la politique de confidentialité avec 3114 (prévention suicide, gratuit 24/7), 15 (SAMU), SOS Amitié (09 72 39 40 50), Suicide Écoute (01 45 39 40 00). Wording fourni par `selen-psychologue` 25/05. | Mickaël | `selen-psychologue` (validation finale) | 06/06 | Section publiée dans la politique |
| A.11 | **Protocole de crise in-app** — ajouter un lien permanent "Besoin d'aide immédiate ?" en footer de la "réponse de la lune" et dans le menu Paramètres → Confidentialité. Pointe vers une fiche d'urgence avec les 4 numéros ci-dessus. Pattern Wysa. Coût dev : ~2h. Non-négociable selon `selen-psychologue`. | Mickaël | `selen-psychologue` | 10/06 | Lien visible en bas de chaque réponse de la lune + dans menu Confidentialité |
| A.12 | **CGU section 06 (responsabilité IA) — reformulation** — remplacer le ton "pas notre problème" par une formulation qui assume une responsabilité éditoriale (garde-fous + mécanisme de signalement) tout en gardant une limite raisonnable sur les décisions personnelles de l'utilisatrice. Wording proposé par `selen-psychologue` 25/05 disponible. Couplé obligatoirement à A.13. | Mickaël | `selen-psychologue` | 10/06 | CGU v2 publiée |
| A.13 | **Mécanisme de signalement d'une réponse IA délétère** — bouton "Signaler cette réponse" sous chaque "réponse de la lune", endpoint backend pour collecter, fiche de revue manuelle des cas. Non-négociable selon `selen-psychologue` (alimente A.12). | Mickaël | `selen-psychologue` (revue protocole) | 13/06 | UI + endpoint en prod, fiche de revue créée |
| A.14 | **Politique : mention validation clinique des capsules** — ajouter une phrase indiquant que les capsules d'introspection sont conçues en référence aux pratiques de TCC et validées par [conseil scientifique]. Périmètre exact à clarifier avec `selen-psychologue` (relation contractuelle Brun ↔ Selen, ce qu'elle a effectivement validé). | Mickaël | `selen-psychologue` | 13/06 | Phrase publiée avec attribution validée |
| A.15 | **Audit CGU complet** — `selen-operating-partner` a flagué le 25/05 que les CGU n'ont pas été auditées en profondeur dans le cadre de la conformité RGPD. Couvrir notamment : limitation de responsabilité IA, droit applicable au cas mineur, mention OpenAI explicite, table de droits utilisateur côté CGU. | Mickaël | `selen-operating-partner` (audit ops) + `selen-psychologue` (éthique) | 16/06 | Doc d'audit CGU livré + liste de corrections priorisées |

**Livrable Phase A :** une note `docs/conformite-rgpd-2026-05-30.md` listant les preuves de conformité (politique publiée, screenshot consent screen, test DeleteMe, opt-out OpenAI, registre traitements).

**Garde-fou :** **aucune action d'acquisition ni de communication publique avant validation Phase A**. La relance d'acquisition (Phase 1.0c) démarre vendredi 30/05 au plus tôt.

---

### 🔥 Phase 1 — Diagnostic (semaines du 02/06 au 30/06 — 4 semaines, décalée d'1 semaine)

**Objectif :** comprendre AVANT de décider. Pas de code produit. Pas de nouvelle feature. Pas de monétisation.

| # | Action | Owner | Agent à consulter | Échéance | Métrique de succès |
|---|---|---|---|---|---|
| 1.0 | Audit 360° initial de Selen (verdict 5 axes + top 3 leviers + plan 7 jours) — **livré** | `selen-operating-partner` | — | 24/05 | ✅ `docs/audit-2026-05-24.md` + `docs/audit-prod-2026-05-24.md` |
| 1.0a | **Forensic mars→avril** : retrouver le(s) changement(s) qui a fait passer le taux « jamais ouvert » de 56 % à 22 %. Examiner commits front+back 15/03→15/04, store, marketing. Formuler 1-3 hypothèses. | Mickaël | `selen-data-analyst` + `selen-operating-partner` | 27/05 | Hypothèses écrites dans `docs/hypotheses.md` (H4) |
| 1.0b | **Audit acquisition** : screenshots App Store + Play Store consoles (impressions / installs / conversion / rating sur 90 jours). Diagnostiquer la chute 76→7. | Mickaël | `selen-growth-activation` | 26/05 | Screenshots + analyse écrite |
| 1.1 | Confirmer envoi segment 2 Mom Test (31 emails meteo_uniquement_j0 — désormais 42 users à reconsidérer) | Mickaël | `selen-ux-researcher` | 26/05 | Drafts envoyés, statut confirmé |
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
