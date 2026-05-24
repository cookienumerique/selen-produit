# Décisions Produit Selen

## Ordre de priorité validé
1. Diagnostiquer le churn (J1/J3/J7) via données BDD
2. Interviews Mom Test des 13 actifs
3. Fix du hook avant toute monétisation
4. Construction offre premium sur douleur réelle

## Décisions prises

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
