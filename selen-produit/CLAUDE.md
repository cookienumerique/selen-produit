# Selen — Produit & Stratégie

Ce repo contient le travail **produit/stratégie** de Selen. Il est distinct des deux repos code voisins :
- App mobile (React Native / Expo) : `/var/www/selen/selen/`
- API backend (Symfony / PHP) : `/var/www/selen/selen-api/`

Les agents Claude définis ici peuvent lire le code des deux autres repos via leurs outils standards (`Read`, `Grep`, `Bash`) — l'accès n'est pas restreint.

## Mission de ce repo

Centraliser le travail non-code de Selen :
- Décisions produit et leur rationale
- Hypothèses avec statut de validation
- Données utilisateurs et synthèses d'interviews
- Roadmap active
- Équipe d'agents Claude qui simule une startup autour de Mickaël (founder solo)

## Structure

```
selen-produit/
├── CLAUDE.md                       # ce fichier — chargé auto par Claude Code
├── meta-prompt-agent-selen.md      # template pour créer de nouveaux agents
├── roadmap.md                      # feuille de route active
├── docs/                           # base de connaissance versionnée
│   ├── decisions-produit.md
│   ├── hypotheses.md
│   └── validations.md
└── .claude/
    └── agents/                     # agents opérationnels
        ├── selen-operating-partner.md    # premier appel — audit 360° et orchestration
        ├── selen-ux-researcher.md
        ├── selen-data-analyst.md
        ├── selen-growth-activation.md
        ├── selen-devil-advocate.md
        └── selen-psychologue.md
```

## État au 24/05/2026 (snapshot — vérifier `roadmap.md` pour l'à-jour)

- 180 users total, 13 actifs sur 15j, 0 € de revenue
- **Problème prioritaire : activation** — 63 % des 125 users post-10/03 ne dépassent pas J0
- **Phase en cours :** interviews Mom Test
  - Segment capsule_j0 (22 users) envoyé le 16/05
  - Segment meteo_uniquement_j0 (31 users) — statut à confirmer
- Le produit retient ceux qui activent (43 % à J14+) → blocage = entrée, pas rétention

## Garde-fous communs à tous les agents

1. **Sécurité émotionnelle** — app santé mentale, pas de dark patterns wellness
2. **RGPD + données santé** — données sensibles, HDS potentiel à valider
3. **Mom Test compliant** — décisions basées sur comportement observé, pas intention déclarée
4. **Pas de feature sans interview** — leçon payée par la mise en pause précédente

## Comment travailler avec les agents

Chaque agent dans `.claude/agents/` est invocable via le menu d'agents Claude Code ou `Agent({subagent_type: "selen-..."})`. Ils sont conçus pour :
- Challenger avant de proposer
- Refuser les vagues réponses et les solutions sans donnée
- Référencer la base documentaire de ce repo (`docs/`, `roadmap.md`)
- Renvoyer un format de réponse standardisé : **Diagnostic → Options → Reco → Next step**

Les agents se citent les uns les autres : un agent Growth te renverra parfois vers le UX Researcher ou la Psychologue avant de trancher. C'est volontaire.

## Créer un nouvel agent

Suivre `meta-prompt-agent-selen.md` :
1. Copier le template
2. Remplir le frontmatter YAML et les variables `{{...}}`
3. Conserver les sections communes verbatim — c'est ce qui garantit la cohérence inter-agents
