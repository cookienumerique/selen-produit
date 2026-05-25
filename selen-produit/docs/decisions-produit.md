# Décisions Produit Selen

## Ordre de priorité validé
1. Diagnostiquer le churn (J1/J3/J7) via données BDD
2. Interviews Mom Test des 13 actifs
3. Fix du hook avant toute monétisation
4. Construction offre premium sur douleur réelle

## Décisions prises

### 2026-05-25 — RGPD priorité avant relance acquisition
**Constat :** politique de confidentialité existante (https://instantselen.fr/politique-de-confidentialite) mais avec 5 gaps critiques (identité légale incomplète, base légale par finalité absente, art. 9 RGPD non mentionné alors que journal_entry + inner_weather_response sont des données santé, transfert USA via OpenAI non régulé, droit CNIL non mentionné). Aucun écran de consentement à l'inscription.
**Décision :** **bloquer la relance d'acquisition jusqu'à ce que** : (1) la politique soit mise à jour sur les 5 gaps, (2) un écran de consentement explicite soit intégré au flow signup, (3) les 180 users existants soient invités à re-consentir au prochain login.
**Rationale :** sans consentement explicite art. 9, le traitement des journal_entry et inner_weather_response est en zone grise. Chaque nouveau signup sans consentement = risque structurel cumulé. Coût de la conformité (~1-1.5j dev) << coût d'une mise en demeure CNIL ou d'un signalement utilisateur.
**Effet sur la roadmap :** la relance d'acquisition (prévue Lun 26/05) est décalée à **vendredi 30/05** au plus tôt.

### 2026-05-24 (PM) — Erreur de diagnostic founder identifiée
**Constat :** Mickaël a volontairement arrêté l'acquisition (Instagram + bouche-à-oreille) parce qu'il interprétait les 63 % de non-activation comme une rétention cassée (« remplir un sceau percé »). C'est une **erreur de diagnostic** : la rétention conditionnelle est de 43 % à J14+, soit *au-dessus* du benchmark wellness (20-25 % à J30). Le vrai problème est un **funnel d'activation étroit**, pas une fuite en rétention.
**Décision :** **annuler le gel d'acquisition**. Relancer une activité d'acquisition légère cette semaine pour générer le volume nécessaire aux expérimentations d'activation.
**Rationale :** sans nouveaux signups, aucune expérimentation d'activation n'a de N suffisant. Garder le robinet fermé revient à se priver du moyen de mesurer ses propres corrections.
**Garde-fou :** acquisition légère (~10-20 signups/semaine), pas de budget pub. Posts Instagram organiques + activation du réseau personnel. Réévaluer dans 2 semaines.

### 2026-05-24 (PM) — Mom Test segment 1+2 : abandon de la méthode email
**Constat :** 0 réponse sur 53 emails Mom Test (22 capsule_j0 + 31 meteo_uniquement_j0). Deux causes probables : (1) réputation SMTP Ionos / atterrissage spam, (2) ces users n'ont jamais investi émotionnellement dans Selen donc le coût de répondre est trop élevé.
**Décision :** abandonner la méthode email cold sur les churned. Passer à une **méthode incentivée sur les power users actifs** : 5 interviews 30 min via Zoom/Calendly, incentivées (carte cadeau 30 € ou similaire), recrutées via push in-app aux 20 users actifs sur 15j. Question Mom Test centrée sur le JTBD identifié (gratitude / charge mentale / ancrage).
**Rationale :** mieux vaut 5 vraies interviews de power users que 0 verbatim de churned. Les insights des power users sont actionnables (industrialisation des comportements gagnants), ceux des churned sont incertains (silence = bruit ou désintérêt).

### 2026-05-24 — Reformulation : double crise simultanée
**Décision :** le problème prioritaire est désormais formulé comme une **double crise** :
- Crise d'activation cohorte (42 % jamais ouvert capsule sur cohorte post-10/03, dont 33 % ont fait la météo et calé au seuil suivant)
- **Crise d'acquisition** : 76 inscriptions en mars → 54 en avril → **7 en mai 2026**. Le robinet d'entrée se ferme indépendamment du problème d'activation.
Les deux sont traitées en parallèle ; l'acquisition prend la priorité immédiate.
**Rationale :** audit prod du 24/05 — la baisse d'acquisition n'apparaissait dans aucun doc précédent. Angle mort founder-solo.
**Source :** `docs/audit-prod-2026-05-24.md`

### 2026-05-24 — Gel monétisation confirmé
**Décision :** aucun travail monétisation (pricing, paywall, codes promo, abonnement) jusqu'à activation cohorte ≥ 50 % **ET** nouveaux signups ≥ 30 / mois.
**Rationale :** 0 utilisateur payant organique en prod (les 9 subscriptions sont 8 grants manuels à des amis + 1 essai Apple sandbox de 1 jour, confirmé par Mickaël). Infra Subscription complète mais inopérante côté revenue. Tout investissement monétisation maintenant est une fuite en avant.

### 2026-05-24 — Industrialiser la « win » mars→avril non documentée
**Décision :** avant de concevoir de nouveaux tests d'activation, rétro-ingénierer le passage du taux « jamais ouvert » de 56 % (mars) à 22 % (avril). Premier indice via CHANGELOG : 3 releases majeures le **06/03/2026** (IA capsule + recommandation par météo + paywall placement). Cohorte mars = mixte (pré + post 06/03).
**Rationale :** levier asymétrique — la cause est déjà active en prod, il s'agit juste de l'identifier précisément et de la renforcer. Découper la cohorte mars en « avant 06/03 » et « après 06/03 » devrait confirmer ou infirmer rapidement.
**Action concrète :** requête de découpage cohorte mars + audit App Store + Play Store consoles.

### 2026-05-16 — Recentrage sur l'activation
**Décision :** Le problème prioritaire n'est pas la rétention ni le hook, c'est l'activation.
**Rationale :** Sur 125 users post-10/03, 63% ne dépassent pas J0. Parmi ceux qui activent, 43% sont encore là à J14+ — le produit fonctionne pour ceux qui y entrent.
**Impact sur priorités :**
1. ~~Diagnostiquer le churn J1/J3/J7~~ → **FAIT** — problème identifié : activation
2. Comprendre pourquoi 53 users n'ont jamais ouvert une capsule (interviews)
3. Comprendre pourquoi 26 users ne sont pas revenus après J0 (interviews)
4. Fix onboarding avant toute autre feature
5. Construction offre premium sur douleur réelle (inchangé)

### 2026-05-16 — Outreach Mom Test lancé
**Décision :** Contacter les users J0 en deux segments distincts avec deux questions différentes (Mom Test — pas de pitch, une seule question ouverte).
**Exécution :**
- Segment 1 (capsule_j0, 22 users) : envoyé le 16/05 — "qu'est-ce que tu cherchais ce jour-là ?"
- Segment 2 (meteo_uniquement_j0, 31 users) : drafts prêts, envoi prévu 17/05 — "qu'est-ce qui t'a arrêtée/arrêté ?"
**Rationale :** Les deux segments ont eu des expériences produit différentes. Les regrouper biaiserait les réponses.
