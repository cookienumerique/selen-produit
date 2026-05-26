# Décisions Produit Selen

## Ordre de priorité validé
1. Diagnostiquer le churn (J1/J3/J7) via données BDD
2. Interviews Mom Test des 13 actifs
3. Fix du hook avant toute monétisation
4. Construction offre premium sur douleur réelle

## Décisions prises

### 2026-05-25 — Ship 1.25.0 sans écran d'accueil + report écran d'accueil new signups en 1.25.1
**Contexte :** lors de la review wording d'aujourd'hui, `selen-psychologue` (Brun) a signalé un risque clinique sur la modale de consent qui s'affiche brutalement au premier lancement, sans accueil préalable — inverse de l'alliance thérapeutique (Bordin 1979). Sa proposition (Option C) : insérer 1 écran d'accueil "Bienvenue. Selen est un espace pour toi…" + bouton "Continuer" AVANT la modale, **uniquement pour les new signups** (les 180 existantes en re-consent gardent la modale directe). Coût : ~1 jour de dev mobile. Pattern : Headspace, Petit Bambou.

**Arbitrage `selen-operating-partner` (Tessier) :** le goulot critique cette semaine est le ship 1.25.0 lui-même, pas le first-install. Sans le ship :
1. Conformité RGPD fragile côté mobile (consentement par défaut sans information préalable = exactement ce que la CNIL retoque sur art. 9 santé)
2. 180 users existantes restent sans réponse de la lune (`consent_ai_optin = true` patché en SQL mais l'UI mobile ne matérialise pas le consentement)
3. Phase A roadmap en zone rouge si le ship glisse (échéance fermeture 30/05)

Volume d'acquisition réel : ~1-2 new signups par semaine (7 en mai). Le calcul : retarder le ship d'1 jour pour protéger 1-2 personnes vs laisser 180 sans AI + Phase A qui glisse → ne passe pas.

**Décision : Option D (3ᵉ voie Tessier)**
- Ship 1.25.0 cette semaine **sans** écran d'accueil, avec juste la réécriture du wording de la modale (titre adouci "Un instant avant d'entrer", paragraphes humanisés, mention "OpenAI/États-Unis" sortie vers la politique de confidentialité v2 à venir)
- **Écran d'accueil dev en 1.25.1, ship cible jeudi 05/06** (cf. roadmap A.7)
- Les libellés de boutons "J'accepte tout" / "Continuer sans" sont conservés (Option 2 Mickaël) — pas de DPO/cabinet sous la main pour valider une réécriture en "Activer la réponse de la lune"

**Conditions de la décision (à respecter) :**
1. La politique de confidentialité v2 (A.2 roadmap, échéance 28/05) doit être publiée AVANT le ship 1.25.0 et doit mentionner explicitement OpenAI + États-Unis + SCC (sinon le retrait de ces mentions de la modale fragilise le consentement éclairé art. 13)
2. La ligne A.7 est lockée dans la roadmap — pas de "on verra"

**Rationale clinique non négligé :** l'alliance thérapeutique se joue aussi sur les 7-30 jours suivants, pas uniquement sur l'écran 1. L'argument de Brun reste valide mais arbitré dans le temps, pas annulé.

**Source :** échanges avec `selen-psychologue` (review wording) et `selen-operating-partner` (arbitrage Option D) le 2026-05-25.

### 2026-05-25 — Opt-in IA OpenAI reste optionnelle (gate dur rejeté)
**Contexte :** dev en cours de l'écran de consentement RGPD (Phase A). 3 cases : politique (requise), données santé art. 9 (requise), opt-in IA OpenAI (optionnelle). Mickaël a proposé de passer l'opt-in IA en gate dur : sans accord OpenAI, pas d'accès à Selen.
**Décision :** **refus du gate dur.** L'opt-in IA reste une vraie opt-in optionnelle — l'app fonctionne sans, la "réponse de la lune" n'est simplement pas générée ni persistée pour les users sans consentement.
**Rationale (verdict `selen-psychologue` / Dr. Brun) :**
- **Légal :** art. 7§4 RGPD + EDPB Guidelines 05/2020 sur le consentement — un consentement n'est libre que si le refus n'entraîne pas de détriment et n'est pas conditionné à la fourniture du service hors strict nécessaire. Selen est une app de capsules + journal ; la "réponse de la lune" est un enrichissement narratif, pas le service. Conditionner l'accès à un transfert USA de données art. 9 = consentement vicié donc nul, position constante CNIL.
- **Clinique :** sur app santé mentale, conditionner l'accès à "accepte que ton intimité parte chez OpenAI" est l'inverse de ce que demande l'éthique numérique en santé mentale (HAS Bonnes pratiques apps santé 2021).
- **Produit :** 63 % des users post-10/03 n'activent déjà jamais. Ajouter un gate IA aggrave un funnel déjà cassé sans rapport coût/bénéfice défendable.
**Patterns de référence cités :** Petit Bambou (analytics opt-in), Wysa (cloud opt-in séparé du soin), pattern "just-in-time consent" ICO UK + HAS 2021.
**Effet sur la roadmap :** ajout d'un ticket Phase B "opt-in IA contextuel post-capsule 1" + instrumentation à mettre en place dès la 1.25.0 (taux opt-in IA au onboarding, complétion capsule 1 segmenté opt-in/opt-out, rétention J7 par segment). Revue à 15j.
**Code :** branche `1.20.0` (API) + `1.25.0` (front) — l'opt-in actuelle est conforme à cette décision, aucune modif à faire.

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
