# Registre des activités de traitement — Selen
**Article 30 RGPD — Document interne**

> Document interne non publié. À tenir à jour. À présenter en cas de contrôle CNIL.
> Modèle conforme à la fiche pratique CNIL : https://www.cnil.fr/fr/RGPD-le-registre-des-activites-de-traitement

---

## Identité du responsable de traitement

| Champ | Valeur |
|---|---|
| Nom | Marie Cavelier |
| Forme juridique | Micro-entreprise (entreprise individuelle), nom commercial **Selen** |
| SIRET | `82936679800033` |
| Adresse | `[À COMPLÉTER]` |
| Contact RGPD | contact@instantselen.fr |
| DPO désigné | Non — non requis au regard de la taille et du volume (< 250 personnes, traitement non « à grande échelle » au seuil actuel) |
| Date de création de ce registre | 2026-05-25 |
| Dernière mise à jour | 2026-05-25 |

---

## Traitement n°1 — Gestion des comptes utilisateurs

| Champ | Valeur |
|---|---|
| Finalité principale | Créer et gérer un compte utilisateur pour permettre l'accès à l'application Selen |
| Sous-finalités | Authentification, identification, gestion du cycle de vie du compte (création, modification, suppression) |
| Catégories de personnes concernées | Utilisatrices et utilisateurs de l'application Selen |
| Catégories de données | Email, identifiant Google ou Apple, nom, prénom, photo de profil |
| Catégories sensibles | Non (données identifiantes uniquement) |
| Destinataires internes | Responsable de traitement |
| Sous-traitants | IONOS SE (hébergement DB), Google LLC (auth), Apple Inc. (auth) |
| Transferts hors UE | États-Unis pour Google et Apple, encadrés par le Data Privacy Framework UE-USA |
| Durée de conservation | Pendant toute la durée de vie du compte. Suppression complète sous 30 jours après demande |
| Base légale | Art. 6.1.b — Exécution du contrat |
| Mesures de sécurité | HTTPS/TLS 1.2+, JWT signé, base PostgreSQL chiffrée au repos, accès restreint au responsable |

---

## Traitement n°2 — Suivi émotionnel (météo intérieure)

| Champ | Valeur |
|---|---|
| Finalité principale | Permettre à l'utilisatrice de consigner son état émotionnel quotidien |
| Catégories de personnes concernées | Utilisatrices ayant donné leur consentement explicite (art. 9.2.a RGPD) |
| Catégories de données | État émotionnel choisi parmi 6 (CLEAR, SUNNY, SOFT, FOGGY, TENSE, OVERWHELMED), date, horodatage, lien vers utilisateur |
| Catégories sensibles | **OUI — données concernant la santé (art. 9 RGPD)** |
| Destinataires internes | Responsable de traitement |
| Sous-traitants | IONOS SE (hébergement) |
| Transferts hors UE | Non |
| Durée de conservation | Pendant toute la durée de vie du compte. Suppression sous 30 jours après retrait du consentement |
| Base légale | **Art. 9.2.a — Consentement explicite**, recueilli via écran dédié à l'inscription |
| Mesures de sécurité | Identiques au traitement n°1 + suppression en cascade côté DB |

---

## Traitement n°3 — Capsules thérapeutiques et réponses

| Champ | Valeur |
|---|---|
| Finalité principale | Proposer des capsules thérapeutiques quotidiennes et recueillir les réponses libres de l'utilisatrice |
| Catégories de personnes concernées | Utilisatrices ayant donné leur consentement explicite |
| Catégories de données | Texte libre saisi par l'utilisatrice en réponse à une capsule, horodatage, lien vers la capsule, lien vers l'utilisatrice |
| Catégories sensibles | **OUI — données concernant la santé (art. 9 RGPD)** — réflexions personnelles sur état mental |
| Destinataires internes | Responsable de traitement |
| Sous-traitants | IONOS SE (hébergement), OpenAI Inc. (génération de la « réponse de la lune » par IA — sous-traitement n°4) |
| Transferts hors UE | États-Unis (OpenAI), encadrés par les CCT et le Data Processing Addendum OpenAI |
| Durée de conservation | Pendant toute la durée de vie du compte. Suppression sous 30 jours après retrait du consentement |
| Base légale | **Art. 9.2.a — Consentement explicite** |
| Mesures de sécurité | Identiques + OpenAI s'engage à ne pas utiliser les données pour entraîner ses modèles (ToS API depuis mars 2023) |

---

## Traitement n°4 — Journal intime augmenté par IA

| Champ | Valeur |
|---|---|
| Finalité principale | Permettre à l'utilisatrice d'écrire un journal intime et de recevoir une réponse générée par IA |
| Catégories de personnes concernées | Utilisatrices ayant donné leur consentement explicite, y compris pour le transfert vers OpenAI |
| Catégories de données | Texte libre du journal, réponse générée par OpenAI, horodatage, lien vers l'utilisatrice |
| Catégories sensibles | **OUI — données concernant la santé (art. 9 RGPD)** — contenu intime à fort potentiel sensibilité |
| Destinataires internes | Responsable de traitement |
| Sous-traitants | IONOS SE (hébergement), OpenAI Inc. (génération IA) |
| Transferts hors UE | États-Unis (OpenAI) |
| Durée de conservation côté Selen | Pendant toute la durée de vie du compte. Suppression sous 30 jours après retrait du consentement |
| Durée de conservation côté OpenAI | Maximum 30 jours (cache abuse detection), conformément à la politique API OpenAI |
| Base légale | **Art. 9.2.a — Consentement explicite** (case dédiée pour l'IA, refusable indépendamment du consentement principal) |
| Mesures de sécurité | Identiques + DPA OpenAI signé (clauses contractuelles types) |

---

## Traitement n°5 — Gestion des abonnements payants

| Champ | Valeur |
|---|---|
| Finalité principale | Gérer les abonnements premium souscrits via Apple App Store ou Google Play Store |
| Catégories de personnes concernées | Utilisatrices ayant souscrit à un abonnement |
| Catégories de données | Identifiant de transaction, plateforme (Apple/Google), statut d'abonnement, date d'expiration, base plan ID, product ID |
| Catégories sensibles | Non |
| Destinataires internes | Responsable de traitement |
| Sous-traitants | Apple Inc., Google LLC, IONOS SE |
| Transferts hors UE | États-Unis (Apple, Google), encadrés par le Data Privacy Framework |
| Durée de conservation | Durée légale comptable de 10 ans (obligation fiscale) |
| Base légale | Art. 6.1.b — Exécution du contrat ; Art. 6.1.c — Obligation légale (comptabilité) |
| Mesures de sécurité | Identiques au traitement n°1 |

---

## Traitement n°6 — Logs techniques et sécurité

| Champ | Valeur |
|---|---|
| Finalité principale | Assurer la sécurité de l'application, prévenir et détecter les abus, déboguer les anomalies |
| Catégories de personnes concernées | Toutes utilisatrices, ainsi que toute personne accédant à l'API Selen |
| Catégories de données | Adresse IP, horodatage des requêtes, user-agent, endpoint appelé, code de réponse HTTP, message d'erreur le cas échéant |
| Catégories sensibles | Non, sauf corrélation indirecte (adresse IP rattachable à un compte avec données santé) |
| Destinataires internes | Responsable de traitement |
| Sous-traitants | IONOS SE (hébergement, logs server) |
| Transferts hors UE | Non |
| Durée de conservation | 12 mois |
| Base légale | Art. 6.1.f — Intérêt légitime (sécurité du service) |
| Mesures de sécurité | Logs stockés sur serveur Ionos chiffré, accès restreint au responsable, rotation et purge automatique |

---

## Annexe — Sous-traitants identifiés

| Sous-traitant | Service | Localisation | Garanties |
|---|---|---|---|
| **IONOS SE** | Hébergement DB + serveur Symfony | Allemagne (UE) | RGPD-compliant par défaut, contrat client Ionos |
| **OpenAI, Inc.** | API de génération IA | États-Unis | **DPA contresigné le 2026-05-25 11:02 (Paris)** entre Marie Cavelier et OpenAI Inc. via Ironclad — PDF archivé dans `docs/preuves-conformite/Data Processing Agreement (Marie Cavelier and OpenAI).pdf`. Avant cette date : DPA incorporé par référence aux Terms of Use depuis le 6 mars 2026 (premier paiement). Standard Contractual Clauses (SCC) incluses en annexe. Data Controls vérifiés OFF (training opt-out) le 2026-05-25. |
| **Google LLC** | Authentification, Firebase | États-Unis | Data Privacy Framework UE-USA |
| **Apple Inc.** | Sign-In with Apple, IAP | États-Unis | Data Privacy Framework UE-USA |

---

> ⚠️ **À compléter avant validation finale :**
> 1. SIRET + adresse responsable de traitement
> 2. Date de signature du DPA OpenAI (action à exécuter : https://openai.com/policies/eu-data-processing-addendum/)
> 3. Confirmer politique de backup Ionos et l'ajouter au traitement n°1 (durée + rotation)
