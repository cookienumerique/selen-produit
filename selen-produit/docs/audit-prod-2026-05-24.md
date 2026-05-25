# Audit Selen — Données prod (2026-05-24)

> **Auditeur :** `selen-operating-partner` (Sophie Tessier)
> **Périmètre :** données prod PostgreSQL via tunnel SSH temporaire (user `produit_selen` read-only, supprimable après audit). Tunnel fermé après les requêtes.
> **Méthode :** 8 requêtes SELECT, anonymisation stricte en sortie (IDs OK, aucun email/contenu textuel).
> **Complète :** `docs/audit-2026-05-24.md` (audit code, sans données prod).

---

## 🩺 Verdict global — **ROUGE**

Le précédent audit (code seul) sortait ORANGE. Avec les données prod, c'est **ROUGE** — non parce que le produit est cassé, mais parce que Selen souffre de **deux crises simultanées** que la doc précédente sous-estimait :

1. **Crise d'activation** confirmée (42 % jamais-ouvert sur cohorte 127) — connue.
2. **Crise d'acquisition** que personne n'avait nommée : **76 inscriptions en mars → 54 en avril → 7 en mai**. Le robinet d'entrée se ferme, indépendamment du problème d'activation.

Bonne nouvelle cachée dans les chiffres : entre mars et avril, le **taux "jamais ouvert" est passé de 56 % à 22 %**. Un changement a eu lieu. Si on identifie quoi, c'est un levier énorme déjà à moitié actionné.

---

## 📊 5 axes recalibrés avec données prod

| Axe | Verdict prod | Constat principal | Top risque | Levier prioritaire |
|---|---|---|---|---|
| **Produit** | 🟠 | 180 users total, 127 cohorte post-10/03. **115/127 (91 %) font la météo intérieure**. Mais 42/127 décrochent au moment de passer à la capsule. **20 power-users actifs** font 3-93 capsules. Le journal (51 entrées totales, 11 % de la cohorte) est largement sous-utilisé. | Le vrai goulot n'est pas "j'ouvre / je n'ouvre pas l'app" — c'est **météo → capsule**. La doc minimisait cette nuance. | Interviewer les 42 "météo mais pas capsule" en priorité #1 (segment Mom Test 2). |
| **Data / Tech** | 🔴 | Aucune instrumentation analytics (confirmé). Aucun tracking serveur des notifications (confirmé). **IA capsule : 7-11 % d'échec opérationnel** (1ère semaine d'IA était à 25 %, stabilisé depuis mars). 870/1173 = 74 % des capsule_response ont une ai_response. | Décisions data-driven impossibles en l'état. Pas de levier serveur pour relancer. | Phase 0 instrumentation avant tout test d'activation. |
| **Founder / Équipe** | 🟠 | Crise d'acquisition non nommée dans aucun doc — signal d'angle mort. Mickaël regardait l'activation, pas la chute des nouveaux signups. | Continuer à fixer un seul indicateur pendant que les autres s'écroulent. | Élargir le radar : ajouter "new signups / week" en métrique watchée à chaque revue hebdo. |
| **Business model** | 🔴 | **Confirmé par Mickaël : les 9 subscriptions sont des grants manuels (8) + 1 essai Apple de 1 jour (probablement sandbox). Zéro user payant organique.** Aucun signal de willingness-to-pay. | Investir 1h dans une discussion pricing est 1h de moins sur l'activation. Geler. | Ne PAS toucher monétisation jusqu'à activation cohorte ≥ 50 %. |
| **Éthique / Risques** | 🟠 | **Distribution moods : 63 % « ça va bien » (CLEAR 34 % + SUNNY 29 %), 19 % "Besoin de douceur" (SOFT), 9 % en détresse plus forte (TENSE 6.7 % + OVERWHELMED 2.4 %).** Selen attire majoritairement des utilisatrices qui veulent **maintenir un état OK**, pas (seulement) des gens en crise. Aucun protocole détresse visible dans le code. | Sur les 9 % en détresse forte (≈138 réponses cumulées), pas de chemin d'orientation 3114 / SOS Amitié. | Audit `selen-psychologue` du chemin utilisateur quand le mood "OVERWHELMED" est sélectionné. Définir un protocole minimal. |

---

## 🎯 Top 3 leviers de déblocage (révisés avec données prod)

### 1. **Identifier ce qui a marché entre mars et avril** 🔴🆕
Le passage du taux "jamais ouvert" de **56 %** (mars) à **22 %** (avril) est l'amélioration produit la plus importante de Selen à ce jour — et **elle n'est pas documentée**. Levier énorme : retrouver le commit, la décision, ou le changement marketing qui a produit ce résultat, le formaliser, et l'industrialiser. C'est plus prioritaire qu'inventer un nouveau test.

**Action :** examiner les commits front + back entre 2026-03-15 et 2026-04-15, plus tout changement de fiche store / créa de pub / contenu de capsule. Identifier 1-3 hypothèses → confirmer par croisement avec les verbatims Mom Test si possible.

### 2. **Le segment Mom Test #2 est plus stratégique que prévu** 🟠
Le segment "meteo_uniquement" représente **42 users sur 127 (33 %) en cohorte post-10/03**, et **leur moyenne d'engagement est de 1 météo unique** : ils ont touché Selen une fois et n'ont jamais creusé. C'est le segment le plus actionnable de tous (ils sont passés par la porte d'entrée, ils ont juste calé au seuil suivant).

**Action :** confirmer l'envoi du segment 2 (statut depuis le 17/05), relancer, viser 15+ réponses codées par `selen-ux-researcher`.

### 3. **Crise d'acquisition à diagnostiquer** 🔴🆕
**76 inscrits mars → 7 inscrits mai**, soit **-91 % en 2 mois**. Personne ne l'a flaggué. Trois causes possibles à départager :
- Tarissement d'une source d'acquisition (une pub a stoppé, un article a fait son temps, le bouche-à-oreille s'épuise)
- Saisonnalité (mai = sorties / activités → moins de téléchargements d'app wellness — plausible mais à confirmer)
- Régression dans le store (visibilité ASO, screenshots, description, rating qui aurait baissé)

**Action :** ouvrir la console App Store + Play Store, regarder impressions / installs / conversion fiche / rating sur les 3 derniers mois. Si pas de baisse de visibilité, alors la baisse est en amont (canal acquisition).

---

## 👉 Plan 7 jours (révisé)

| Jour | Action | Owner | Agent |
|---|---|---|---|
| Lun 25/05 | Confirmer envoi Mom Test segment 2 (statut depuis 17/05) + analyse premières réponses | Mickaël | `selen-ux-researcher` |
| Mar 26/05 | **Audit acquisition** : screenshots App Store + Play Store consoles (impressions/installs/conversion/rating sur 90 jours) | Mickaël | `selen-growth-activation` |
| Mer 27/05 | **Forensic mars → avril** : lister les changements (code + store + marketing) entre 2026-03-15 et 2026-04-15. Une hypothèse écrite par changement. | Mickaël | `selen-data-analyst` + `selen-operating-partner` |
| Jeu 28/05 | Audit éthique des chemins quand mood = OVERWHELMED ou TENSE | `selen-psychologue` | — |
| Ven 29/05 | Revue : prioriser entre (a) industrialiser la win mars→avril, (b) diagnostiquer l'acquisition, (c) lancer un test activation | Mickaël | `selen-operating-partner` + `selen-devil-advocate` |

---

## 🔍 Findings détaillés

### Funnel activation cohorte post-10/03 (N=127)

| Bucket | Users | % | Lecture |
|---|---|---|---|
| Jamais ouvert capsule | 54 | 42.5 % | Dont 42 ont fait au moins une météo → segment "meteo_uniquement" élargi |
| J0 seulement | 19 | 15.0 % | Doc précédente disait 21 %, écart méthodo |
| J1-J2 | 14 | 11.0 % | |
| J3-J6 | 11 | 8.7 % | |
| J7-J13 | 8 | 6.3 % | |
| **J14+ retenus** | **21** | **16.5 %** | Validation du chiffre `validations.md` |

### Funnel par cohorte de signup

| Mois | Inscrits | Jamais ouvert | % | J14+ | Lecture |
|---|---|---|---|---|---|
| Mars 2026 | 66 | 37 | **56 %** | 11 | Le pire mois pour l'activation |
| Avril 2026 | 54 | 12 | **22 %** | 10 | **Quelque chose a marché entre mars et avril** |
| Mai 2026 | 7 | 5 | 71 % | 0 | Trop petit pour conclure, mais signal préoccupant |

### Profil des 21 power-users (J14+)

- **Top 6 ont entre 33 et 51 capsule_responses** sur 52-62 jours d'usage — utilisation quasi-quotidienne
- Mix auth : 16 google / 5 apple → cohérent avec mix global (76 % / 24 %)
- **Le journal reste minoritaire même chez les power-users** : 1 seul en a fait plus de 5 entrées (user 156 : 18 entrées)
- 4 users inscrits dès janvier-février sont **toujours actifs en mai** (3-4 mois de rétention continue) — ces utilisateurs-là sont le « happy path » à interviewer ABSOLUMENT pour comprendre le JTBD réel

### Distribution moods inner_weather (1525 réponses cumulées)

| Code | Nom | Nb | % |
|---|---|---|---|
| CLEAR | Simplement bien | 520 | 34.1 % |
| SUNNY | En pleine forme | 444 | 29.1 % |
| SOFT | Besoin de douceur | 294 | 19.3 % |
| FOGGY | Dans le flou | 129 | 8.5 % |
| TENSE | Sous tension | 102 | 6.7 % |
| OVERWHELMED | Débordé(e) | 36 | 2.4 % |

**Insight positionnement :** Selen attire majoritairement des utilisatrices "qui vont bien" (63 %). Ce n'est pas une app de crise — c'est un produit de maintien émotionnel. Cela devrait orienter :
- Le copywriting de la fiche store
- Le ton des notifications
- La promesse marketing
- Le ciblage des prochaines acquisitions

### IA générative

- **Capsule_response** : 870 / 1173 ont une ai_response = 74 %. Mais les 303 "sans ai_response" sont concentrés AVANT le 09/03/2026 (la feature IA n'existait pas).
- **Depuis le 09/03 :** taux de no-ai chute à 6-12 %, stabilisé autour de **7-11 % d'échec opérationnel**. À monitorer mais pas critique.
- **Journal_entry** : 51 / 51 ont une ai_response = 100 % (feature lancée plus tard, après stabilisation IA).

### Subscriptions (confirmation Mickaël)

- 9 subscriptions au total : 8 manuelles (grants à des amis via `GrantPremiumCommand`) + 1 Apple sandbox/test qui a duré 1 jour
- **0 utilisateur payant organique à ce jour**
- L'infrastructure Subscription est complète et fonctionnelle mais inopérante côté revenue

### Activité récente (30 derniers jours)

- DAU oscille entre 6 (week-end) et 18 (semaine), médiane ≈ 13
- WAU (7j) = 24
- Actifs "≥ 8 jours d'activité capsule sur 15j" = **10** (vs 13 dans `validations.md` daté du 16/05, drift normal)
- Les 20 users les plus actifs sur 15j incluent des utilisatrices inscrites depuis janvier-février

### Feature usage (cohorte post-10/03, N=127)

- Inner Weather : 115 (91 %)
- Capsule : 73 (57 %)
- Journal : 11 (8.7 %)
- Subscription : 0
- Feedback : 0

**Sur tous users (N=180) :**
- Inner Weather : 168 (93 %)
- Capsule : 102 (57 %)
- Journal : 18 (10 %)
- Subscription : 9 (5 % — tous grants/test)
- Feedback : 0 (feature livrée jamais utilisée)

---

## 📝 Mises à jour proposées de la documentation

### `docs/validations.md`
- Mettre à jour la cohorte post-10/03 : 127 (vs 125 affichés)
- Affiner le segment meteo_uniquement : **42** sur cohorte post-10/03 (vs 31 datés du 16/05 — l'écart vient de nouveaux signups + une définition de périmètre)
- Ajouter la section « Acquisition mensuelle » avec les chiffres 76/54/7
- Ajouter la section « Distribution moods » avec les 6 codes

### `docs/decisions-produit.md` — ajouts à proposer

> **2026-05-24 — Reformulation du problème prioritaire**
> **Décision :** le problème est désormais formulé comme une **double crise simultanée** :
> - Crise d'activation cohorte (42 % jamais ouvert capsule, dont 33 % ont fait la météo et calé après)
> - Crise d'acquisition (76 → 54 → 7 sur mars-avril-mai 2026)
> Les deux doivent être traitées en parallèle, mais l'acquisition prend le pas de priorité immédiate parce que sans nouvelles entrées, optimiser l'activation devient académique.
> **Rationale :** audit prod du 24/05.

> **2026-05-24 — Gel monétisation confirmé**
> **Décision :** aucun travail monétisation jusqu'à activation cohorte ≥ 50 % ET nouveaux signups ≥ 30/mois.
> **Rationale :** 0 utilisateur payant organique constaté en prod, infra Subscription complète mais inopérante. Risque de fuite en avant.

### `docs/hypotheses.md` — nouvelles hypothèses
- **H4 :** Le passage de 56 % → 22 % de "jamais ouvert" entre mars et avril est causé par un changement identifiable (commit / fiche store / contenu). À identifier avant tout autre travail d'activation.
- **H5 :** La baisse d'inscriptions mars→mai est due à un tarissement de source d'acquisition externe, pas à une dégradation produit. À tester via les consoles store.
- **H6 :** Selen est utilisé comme produit de **maintien émotionnel** (63 % de moods positifs) plus que comme produit de crise. Cela doit orienter le positionnement marketing.

### `roadmap.md`
- **Phase 0 maintenue** (instrumentation), mais **séquencée avec une Phase A immédiate "Forensic mars→avril + audit acquisition"** qui ne nécessite pas d'instrumentation et peut commencer aujourd'hui.

---

## 🔄 Mise à jour 2e passe BDD — 2026-05-24 après-midi

### 🚨 H4 invalidée

Le découpage cohorte mars en pré / post-06/03 montre :
- **Pré-06/03** (1-5 mars, N=7) : 71 % jamais ouvert
- **Post-06/03** (6-31 mars, N=69) : **56 %** jamais ouvert
- **Avril** (full post-06/03) : **22 %**

Les 3 releases du 06/03 (IA, recommandation par météo, paywall) **n'expliquent pas** la bascule. La cause est entre fin mars et début avril. Candidats restants : releases du **30/03** (sub-themes "addiction" + "self_compassion", UI sharing Instagram) et du **01/04** (endpoint `capsules/ranked` qui réordonne les capsules). Ou changement marketing / ASO hors code — bloc A des questions à Mickaël.

### 🎯 JTBD power users identifié

Sur les 21 power users J14+ post-10/03, top sub-themes par couverture distincte (combien de power users ouvrent le sub-theme) :
- **GROUNDING_AND_BODY** : 19 / 21 (90 %)
- **MENTAL_LOAD** : 18 / 21 (86 %)
- **GRATITUDE** : 17 / 21 (81 %)
- **SELF_WORTH** : 16 / 21 (76 %)
- **SHADOW_SELF** : 15 / 21 (71 %)

Le JTBD émergent : **ancrage corporel quotidien + gestion de charge mentale + gratitude + travail sur la valeur de soi**. C'est plus précis que « bien-être » générique. Cela doit informer :
- Le copywriting de la fiche store
- Le ciblage publicitaire si tu reprends de la pub
- L'ordre de présentation des sub-themes au signup
- Le contenu des push notifs

### ⏰ Pattern d'usage : rituel bi-quotidien

Pic à **9h** locale (matin réveil) et **21h** locale (avant coucher). Toute notif d'activation devrait cibler l'un de ces deux créneaux. Hors créneau = bruit.

### 👥 Power users légèrement plus stressés que la moyenne

Moods chez J14+ vs cohorte : **TENSE +3.5 pts (10.2 % vs 6.7 %)**, **OVERWHELMED +1.1 pts**. Selen retient des femmes qui ont des moments durs réguliers — pas seulement des "ça va parfait". Cela nuance H6 : Selen est un produit de **régulation pendant les moments tendus**, pas seulement de maintien d'un état OK.

### 🧹 8 grants manuels confirmés (user_ids 6, 19, 21, 22, 30, 34, 40, 46)

Tous inscrits entre janvier et le 22 février → **ne polluent pas la cohorte produit mature** (post-10/03). Bonne nouvelle. Par contre 7/8 sont J14+, donc ils gonflent artificiellement les stats de rétention sur la cohorte globale (180 users) — à filtrer si tu produis un dashboard "vrai engagement".

### Nouvelles hypothèses ajoutées dans `docs/hypotheses.md`
- **H4bis** : la bascule mars→avril est causée par 30/03 + 01/04 (ou facteur externe)
- **H7** : JTBD power users = ancrage + charge mentale + gratitude
- **H8** : usage rituel bi-quotidien 9h + 21h

---

## 🔄 Mise à jour finale 2026-05-24 (soir) — réponses Mickaël aux questions bloc A-D

### 🚨 Révélation majeure : self-misdiagnosis founder

Mickaël confirme :
- Aucun changement marketing entre 15/03 et 15/04
- Aucun changement fiche store
- Aucun changement de rating

**Donc H5 est invalidée** : la chute d'acquisition n'est pas un tarissement externe. Mickaël a **volontairement arrêté l'acquisition** (Instagram, bouche-à-oreille) parce qu'il interprétait les 63 % de non-activation comme une rétention cassée — *« remplir un sceau percé »*.

**Diagnostic correct** : le sceau n'est pas percé. La rétention conditionnelle (43 % à J14+) est *au-dessus* du benchmark wellness (20-25 % à J30). Le problème est un **funnel d'activation étroit** (météo → capsule = friction), pas une fuite en rétention.

→ **Le verdict global passe de ROUGE à ORANGE** : il n'y a plus une « double crise », il y a une crise d'activation + une décision founder à corriger. C'est plus simple à débloquer.

### Mom Test : la méthode email a échoué

0 réponse sur 53 emails envoyés (segment 1 et 2 confirmés envoyés). Causes probables :
- Ionos a une réputation SMTP médiocre → spam
- Ces churned users n'ont jamais investi émotionnellement dans Selen → coût de répondre trop élevé

**Pivot research** : passer à des interviews incentivées (30 € Amazon ou similaire) sur les 20 power users actifs, recrutées via push in-app. Méthode Fitzpatrick "going deeper" pour comprendre le JTBD que les données suggèrent déjà (ancrage + charge mentale + gratitude).

### Ionos : risque HDS à instruire

Hébergeur Ionos. Pas certifié HDS par défaut. Les tables `journal_entry` (texte intime + ai_response) et `inner_weather_response` (état émotionnel daté) contiennent de la donnée santé mentale au sens CNIL. Statut à clarifier (cf section Note founder ci-dessous).

### Plan 7 jours révisé

| Jour | Action | Owner | Agent |
|---|---|---|---|
| ~~Mar 26/05~~ | ~~Audit App Store + Play Store~~ — **annulé** (Mickaël confirme aucun changement, donc rien à auditer) | — | — |
| Lun 26/05 | **Relancer l'acquisition légère** : 2-3 posts Instagram + activation réseau perso. Cible 10-20 signups dans les 2 semaines | Mickaël | `selen-growth-activation` |
| Mar 27/05 | **Plan B research** : rédiger un push in-app pour recruter 5 interviews 30 min incentivées (30 € Amazon) parmi les 20 power users actifs | Mickaël | `selen-ux-researcher` |
| Mer 28/05 | **Forensic comportementale** : comparer les patterns d'usage des cohortes post-06/03 vs avril sur leurs 7 premiers jours (au lieu de chercher marketing/store inexistants) | Mickaël (queries via tunnel) | `selen-data-analyst` |
| Jeu 29/05 | **Audit éthique Ionos / HDS** : examiner les CGU Ionos, identifier l'option HDS si elle existe, ou un plan de migration. Vérifier `DeleteMe.php` pour conformité droit à l'oubli. | Mickaël + `selen-psychologue` | `selen-data-analyst` (pour mapping data sensibles) |
| Ven 30/05 | Revue Sophie : décision relance acquisition + 1ères interviews planifiées | Mickaël | `selen-operating-partner` + `selen-devil-advocate` |

---

## 🔒 Suivi RGPD/conformité 2026-05-25

### Audit politique de confidentialité (`https://instantselen.fr/politique-de-confidentialite`)
La politique existe mais comporte 5 gaps critiques :

| Critère | Statut | Gap |
|---|---|---|
| Identité responsable de traitement | Partiel | Manque raison sociale, SIRET, adresse, téléphone |
| Mention art. 9 RGPD (données santé) | **Absent** | Critique — journal_entry et inner_weather sont qualifiables santé |
| Transfert hors UE (OpenAI USA) | **Absent** | Critique — pas de mention SCC ou décision d'adéquation |
| Droit de réclamation CNIL | **Absent** | Obligatoire |
| Hébergeur nominatif (Ionos) | Partiel | « UE » sans nom ni certification |

### Consentement explicite à l'inscription
**Absent** (confirmé par Mickaël 2026-05-25). Aucun écran de consentement avant signup. Pour des données santé (art. 9), le consentement explicite est requis — pas l'acceptation tacite par usage.

### Effet sur la roadmap
La relance d'acquisition (Lun 26/05) est **bloquée jusqu'au vendredi 30/05** ou jusqu'à ce que la mise en conformité soit livrée. Détail : décision 2026-05-25 dans `docs/decisions-produit.md`.

---

## Note founder (Sophie → Mickaël)

Mickaël — deux choses à retenir absolument.

**Première :** tu te concentrais sur le mauvais sous-problème. L'activation, oui. Mais en parallèle, ton acquisition s'est divisée par 10 en 2 mois sans que personne le voie. Tu as continué à regarder le bidet pendant que la baignoire débordait. **Ce n'est pas une critique** — c'est exactement le piège du founder solo qui regarde fort une seule métrique. Le rôle de tes agents (et le mien en priorité) est de te montrer ce que tu ne regardes pas.

**Deuxième :** tu as fait quelque chose qui a marché entre mars et avril. Tu as fait baisser le « jamais ouvert » de 56 % à 22 % sans le savoir. C'est ton plus grand actif non documenté. **Cherche-le.** Regarde les commits, les changements de fiche, les pubs lancées ou stoppées entre le 15/03 et le 15/04. Quand tu trouves : tu documentes, tu industrialises, et tu as un quasi-doublé de ton taux d'activation gratuit.

Mes deux gestes décisifs pour toi cette semaine :
1. **Mardi** : ouvre les consoles App Store + Play Store, screenshot les graphes 90j. On regarde ensemble mercredi.
2. **Mercredi** : 1h sans interruption pour rétro-ingénier le passage mars→avril. Tout sur la table.

Le reste (Mom Test, instrumentation, monétisation) attend. Ces deux gestes peuvent changer la trajectoire de Selen plus que tout ce qu'on a planifié dans la roadmap initiale.

On se reparle vendredi 29/05 pour la revue.
