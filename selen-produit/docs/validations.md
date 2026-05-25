# Validations Selen Produit

## Données confirmées
- 180 utilisateurs total (toutes cohortes)
- 13 actifs sur 15 derniers jours (ouverture capsule 1 jour sur 2 minimum)
- Benchmark industrie app mobile bien-être : 20-25% J30
- 0 revenue à ce jour

## Données BDD — Cohorte produit mature (depuis 10/03/2026, post "réponse de la lune")
Périmètre : 125 users inscrits après le déploiement de la feature IA.

### Funnel d'activation
| Bucket | Nb users | % | Lecture |
|--------|----------|---|---------|
| Jamais ouvert | 53 | 42% | Compte créé, jamais répondu à une capsule |
| J0 seulement | 26 | 21% | Répondu le jour J, jamais revenu |
| **Total non-activés** | **79** | **63%** | Perdus avant d'avoir établi une habitude |
| Churn J1-J2 | 10 | 8% | Essai 2-3 jours, décroché |
| Churn J3-J6 | 9 | 7% | Essai ~1 semaine, décroché |
| Churn J7-J13 | 7 | 6% | Essai ~2 semaines, décroché |
| **Retenus J14+** | **20** | **16%** | Vrais utilisateurs avec habitude |

### Insight clé
- Le problème est l'**activation**, pas la rétention
- Parmi les 46 users qui ont dépassé J0 : **43% sont retenus à J14+** (rétention correcte)
- Le produit tient ses utilisateurs engagés — le blocage est d'y faire entrer les gens

## Segmentation J0 (cohorte post-10/03)
| Segment | Nb users | Description |
|---------|----------|-------------|
| capsule_j0 | 22 | A répondu à au moins une capsule le jour J — a vu la "réponse de la lune" |
| meteo_uniquement_j0 | 35 | A choisi sa météo intérieure J0 mais n'a jamais ouvert de capsule |
| privaterelay (exclus) | 4 | Adresses Apple relay — injoignables par email tiers |

## Outreach interviews — Mom Test
### Segment 1 — capsule_j0 (question : "qu'est-ce que tu cherchais ce jour-là ?")
- **Statut : 22 emails envoyés le 16/05/2026** depuis mika@instantselen.fr via Gmail
- Tous dans le dossier Sent, confirmé via MCP Gmail
- 1 bounce reçu (g99r8snvym@privaterelay.appleid.com — attendu)

### Segment 2 — meteo_uniquement_j0 (question : "qu'est-ce qui t'a arrêtée/arrêté ?")
- **Statut : 31 drafts prêts dans Gmail** (4 privaterelay exclus)
- À envoyer le 17/05/2026 (limite Ionos SMTP atteinte le 16/05)

---

## Données prod 2026-05-24 (audit `selen-operating-partner`)

> Source : requêtes SELECT en read-only via tunnel SSH + user `produit_selen`. Détails complets dans `docs/audit-prod-2026-05-24.md`.

### Counts globaux
- **180** users total (confirmé)
- **127** users post-10/03/2026 (vs 125 annoncés, écart méthodologique mineur)
- 1525 inner_weather_responses cumulées
- 1173 capsule_responses cumulées
- 51 journal_entries cumulées
- 9 subscriptions (toutes non-organiques — voir ci-dessous)
- 426 capsules de contenu
- **0 feedback** (feature livrée jamais utilisée)

### Acquisition mensuelle (angle mort précédent)
| Mois | Nouveaux signups | Lecture |
|---|---|---|
| Jan 2026 | 8 | démarrage |
| Fév 2026 | 35 | |
| Mars 2026 | 76 | pic |
| Avril 2026 | 54 | -29 % |
| **Mai 2026** | **7** | **-87 % vs avril, -91 % vs mars** |

🚨 **Crise d'acquisition** non documentée avant l'audit.

### Funnel activation cohorte post-10/03 (N=127) — affiné
| Bucket | Users | % |
|---|---|---|
| Jamais ouvert capsule | 54 | 42.5 % |
| J0 seulement | 19 | 15.0 % |
| J1-J2 | 14 | 11.0 % |
| J3-J6 | 11 | 8.7 % |
| J7-J13 | 8 | 6.3 % |
| **J14+ retenus** | **21** | **16.5 %** |

**Nuance majeure** : sur les 54 « jamais ouvert capsule », **42 ont fait au moins une météo intérieure**. Le vrai gap d'activation est **météo → capsule**, pas signup → app.

### Funnel par cohorte de signup — la « win » mars→avril
| Mois inscription | Inscrits | Jamais ouvert | % | J14+ |
|---|---|---|---|---|
| Mars 2026 | 66 | 37 | **56 %** | 11 (17 %) |
| Avril 2026 | 54 | 12 | **22 %** | 10 (19 %) |
| Mai 2026 | 7 | 5 | 71 % | 0 (trop tôt) |

Le passage de **56 % → 22 %** est l'amélioration produit la plus importante de Selen — non documentée dans `docs/decisions-produit.md` avant l'audit.

**Indice CHANGELOG :** 3 releases majeures le 2026-03-06 (IA capsule, recommandation par météo, paywall placement). Cohorte mars = mixte (pré + post 06/03). À découper pour confirmer H4.

### Distribution inner_weather (signal positionnement)
| Code | Nom | % |
|---|---|---|
| CLEAR | Simplement bien | 34.1 % |
| SUNNY | En pleine forme | 29.1 % |
| SOFT | Besoin de douceur | 19.3 % |
| FOGGY | Dans le flou | 8.5 % |
| TENSE | Sous tension | 6.7 % |
| OVERWHELMED | Débordé(e) | 2.4 % |

**63 % des météos sont positives** → Selen utilisée comme outil de **maintien émotionnel** (vs crise).

### Subscriptions
- 9 subscriptions total : **8 grants manuels** (`GrantPremiumCommand` à des amis, confirmé par Mickaël 24/05) + **1 essai Apple sandbox** de 1 jour
- **0 utilisateur payant organique**

### IA générative
- Capsule_response : 870 / 1173 = 74 % avec ai_response (les 303 sans IA sont concentrés avant le **06/03/2026** = date de mise en prod de la feature IA selon CHANGELOG front 1.17.0)
- Depuis le 06/03 : taux d'échec opérationnel stabilisé à **7-11 %**
- Journal_entry : 51 / 51 = 100 % avec ai_response (feature lancée le 08/04 — release 1.24.0)

### Activité récente
- DAU : 6 (week-end) à 18 (semaine), médiane ≈ 13
- WAU 7j : 24
- Actifs « ≥ 8 jours d'activité capsule sur 15j » : **10** (vs 13 le 16/05 — drift normal)
- 4 users inscrits dès janvier-février sont **toujours actifs en mai** (rétention 3-4 mois) → segment « happy path » à interviewer

### Feature usage (cohorte post-10/03, N=127)
| Feature | Users | % |
|---|---|---|
| Inner Weather | 115 | 91 % |
| Capsule | 73 | 57 % |
| Journal | 11 | 8.7 % |
| Subscription | 0 | 0 % |
| Feedback | 0 | 0 % |

---

## Données prod — 2e passe (2026-05-24, après-midi)

### Cohorte mars découpée avant/après 06/03 — H4 INVALIDÉE
| Sous-cohorte | Total | Jamais ouvert | % | J14+ | % |
|---|---|---|---|---|---|
| Pré-06/03 (1-5 mars) | 7 | 5 | 71.4 % | 1 | 14.3 % |
| Post-06/03 (6-31 mars) | 69 | 39 | **56.5 %** | 12 | 17.4 % |
| Avril (full post-06/03) | 54 | 12 | **22 %** | 10 | 19 % |

→ La cause du drop **n'est PAS** les releases du 06/03 (l'IA, le ranking par météo, le paywall). La vraie bascule est **entre fin mars et début avril** (releases 1.22.0 du 30/03 et 1.23.0 du 01/04, ou facteur externe).

### Liste des 8 grants manuels (à exclure des stats organic)
| user_id | signup | sub_created | auth |
|---|---|---|---|
| 6 | 2026-01-08 | 2026-03-01 | google |
| 19 | 2026-01-28 | 2026-03-01 | google |
| 21 | 2026-01-30 | 2026-03-01 | google |
| 22 | 2026-01-31 | 2026-03-01 | google |
| 30 | 2026-02-03 | 2026-03-01 | google |
| 34 | 2026-02-07 | 2026-03-01 | google |
| 40 | 2026-02-12 | 2026-03-01 | google |
| 46 | 2026-02-22 | 2026-03-01 | apple |

⚠️ **Tous inscrits AVANT le 10/03** → n'affectent PAS la cohorte produit mature. Mais **7 sur 8 sont J14+** (88 %) vs 18.6 % chez les organiques. Filtrer quand on parle de rétention historique.

### JTBD power users — top sub-themes ouverts (N=21 J14+ post-10/03)
| Sub-theme | Thème | nb_resp | users distincts |
|---|---|---|---|
| GRATITUDE | LIFE_BALANCE | 59 | **17 / 21** |
| SELF_WORTH | WORK_LIFE | 53 | 16 / 21 |
| GROUNDING_AND_BODY | LIFE_BALANCE | 48 | **19 / 21** |
| MENTAL_LOAD | PERSONAL_LIFE | 40 | **18 / 21** |
| SELF_DISCONNECTION | WORK_LIFE | 32 | 12 / 21 |
| SHADOW_SELF | INNER_WORLD | 30 | 15 / 21 |
| LIFE_TRANSITIONS | INNER_WORLD | 28 | 13 / 21 |
| INNER_CHILD | INNER_WORLD | 28 | 11 / 21 |
| BOUNDARIES | PERSONAL_LIFE | 22 | 7 / 21 |
| ADDICTIONS | INNER_WORLD | 21 | 9 / 21 |

**Signal JTBD : ancrage corporel + charge mentale + gratitude** dominent en couverture (par # d'users distincts). Le sub-theme ADDICTIONS (lancé 30/03) a déjà 9 / 21 power users — confirme une demande latente.

### Usage horaire — pattern bi-quotidien
Pics nets sur inner_weather_response des power users (heures UTC, équivalent local +2 en mai été) :
- **7h UTC = 9h locale** : 78 sessions (15.2 %) — **pic matinal**
- **6h UTC = 8h locale** : 49 (9.6 %)
- **19h UTC = 21h locale** : 46 (9.0 %) — **pic soirée**
- **18h UTC = 20h locale** : 28 (5.5 %)

→ Selen est un **rituel bi-quotidien matin (8-9h) + soir (20-21h)**. Push d'activation doivent cibler ces créneaux.

### Moods power users vs cohorte globale
| Code | J14+ % | Cohorte % | Δ |
|---|---|---|---|
| CLEAR | 31.4 | 34.1 | -2.7 |
| SUNNY | 26.2 | 29.1 | -2.9 |
| SOFT | 20.1 | 19.3 | +0.8 |
| FOGGY | 8.6 | 8.5 | +0.1 |
| **TENSE** | **10.2** | **6.7** | **+3.5** |
| **OVERWHELMED** | **3.5** | **2.4** | **+1.1** |

→ Les power users sont **légèrement plus souvent en détresse** que la moyenne. Selen est plus un outil de régulation pendant les moments tendus qu'un outil d'auto-célébration quand tout va bien.
