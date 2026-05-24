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
