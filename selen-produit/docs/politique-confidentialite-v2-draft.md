# Politique de confidentialité — Selen
**Version 2 — [Date de publication]**

> Cette politique remplace la version de mars 2026. Les modifications principales : ajout de la mention article 9 RGPD (données concernant la santé), encadrement du transfert de données vers OpenAI (USA), précision sur l'identité du responsable de traitement, ajout du droit de réclamation CNIL.

---

## 1. Préambule

Selen est une application mobile d'introspection et de bien-être émotionnel, conçue pour accompagner ses utilisatrices au quotidien à travers des capsules thérapeutiques, un suivi de l'état émotionnel (« météo intérieure ») et un journal intime augmenté par une intelligence artificielle générative.

Selen accorde une attention particulière à la protection de vos données émotionnelles, qui sont qualifiées de **données concernant la santé** au sens de l'article 9 du Règlement Général sur la Protection des Données (RGPD). Cette politique décrit comment Selen recueille, utilise, conserve et protège ces données.

## 2. Responsable de traitement

- **Identité** : Marie Cavelier, exerçant sous le nom commercial **Selen**
- **Forme juridique** : Micro-entreprise (entreprise individuelle)
- **SIRET** : `82936679800033`
- **Adresse** : `[À COMPLÉTER — adresse de domiciliation professionnelle]`
- **Contact** : contact@instantselen.fr

Selen n'est pas tenue de désigner un Délégué à la Protection des Données (DPO) au regard de sa taille et de son volume d'activité actuel. Pour toute question relative à vos données ou pour exercer vos droits, écrivez à **contact@instantselen.fr**.

## 3. Données collectées

| Catégorie | Données | Source |
|---|---|---|
| **Identification** | Adresse email, identifiant unique Google ou Apple (selon la méthode d'authentification choisie), nom et prénom (lorsqu'ils sont fournis par Google/Apple) | Fournis par vous via Google Sign-In ou Sign-In with Apple |
| **Profil** | Photo de profil (si fournie par Google) | Fournie par vous |
| **Données concernant la santé (art. 9 RGPD)** | Météo intérieure quotidienne (état émotionnel parmi 6 catégories), réponses textuelles aux capsules thérapeutiques, entrées de journal intime, horodatage de chaque entrée | Saisies par vous dans l'application |
| **Données générées par IA** | Réponses produites par l'intelligence artificielle (« réponse de la lune ») à partir de vos saisies | Générées par OpenAI à partir de vos contenus |
| **Données techniques** | Adresse IP (logs serveur), horodatage des requêtes, informations device (système d'exploitation, version de l'application) | Automatiques |
| **Données d'abonnement** | Statut d'abonnement, plateforme (Apple/Google), date d'expiration, identifiant de transaction | Apple App Store / Google Play Store |

## 4. Finalités et bases légales

| Finalité | Données concernées | Base légale RGPD |
|---|---|---|
| Création et gestion du compte utilisateur | Identification, profil | Art. 6.1.b — Exécution du contrat |
| Fonctionnement de l'application (capsules, météo, journal) hors données santé | Identification, données techniques | Art. 6.1.b — Exécution du contrat |
| **Traitement des données concernant la santé** (météo, capsules, journal) | Données art. 9 ci-dessus | **Art. 9.2.a — Consentement explicite** |
| Génération de la « réponse de la lune » via OpenAI | Capsule responses, journal entries | **Art. 9.2.a — Consentement explicite** (case dédiée à l'inscription) |
| Sécurité, prévention de l'abus, débogage | Logs techniques, adresse IP | Art. 6.1.f — Intérêt légitime |
| Gestion des abonnements payants | Données Apple/Google IAP | Art. 6.1.b — Exécution du contrat |
| Obligations comptables et fiscales | Données d'abonnement | Art. 6.1.c — Obligation légale |

## 5. Données concernant la santé (article 9 RGPD)

Selen traite des données concernant votre santé mentale, à savoir :
- Vos états émotionnels datés (« météo intérieure »)
- Le contenu textuel de votre journal intime
- Vos réponses aux capsules thérapeutiques
- Les réponses générées par l'intelligence artificielle à partir de ces contenus

Le traitement de ces données est **strictement conditionné à votre consentement explicite**, recueilli avant tout usage de l'application via un écran de consentement dédié, conformément à l'article 9.2.a du RGPD.

**Vous pouvez retirer votre consentement à tout moment.** Le retrait du consentement entraîne la suppression définitive de votre compte et de l'ensemble des données associées dans un délai maximum de 30 jours, à l'exception des données nécessaires au respect d'obligations légales (comptabilité notamment).

## 6. Destinataires et sous-traitants

Vos données peuvent être communiquées aux entités suivantes, dans la mesure strictement nécessaire à la finalité poursuivie :

| Sous-traitant | Rôle | Localisation |
|---|---|---|
| **IONOS SE** | Hébergement de la base de données et des fichiers serveur | Allemagne (Union européenne) |
| **OpenAI, Inc.** | Génération des « réponses de la lune » via API | États-Unis |
| **Google LLC** | Authentification Google Sign-In, Firebase | États-Unis |
| **Apple Inc.** | Authentification Sign-In with Apple, gestion des abonnements iOS | États-Unis |

Aucune donnée n'est cédée, vendue ou louée à des tiers à des fins commerciales.

## 7. Transferts de données hors Union européenne

Certains traitements impliquent le transfert de vos données vers les États-Unis (OpenAI, Google, Apple). Ces transferts sont encadrés par les mécanismes prévus par le RGPD :

- **Clauses contractuelles types (CCT)** approuvées par la Commission européenne, formalisées par le **Data Processing Addendum** signé entre Selen et OpenAI (consultable : https://openai.com/policies/eu-data-processing-addendum/)
- **Data Privacy Framework UE-USA** pour Google LLC et Apple Inc., qui sont certifiés
- **Politique API OpenAI** : OpenAI s'engage contractuellement à ne pas utiliser les données envoyées via son API pour entraîner ses modèles, conformément à sa politique applicable depuis mars 2023

## 8. Durée de conservation

| Type de données | Durée |
|---|---|
| Compte utilisateur (identification, profil) | Tant que le compte est actif. Suppression dans les 30 jours suivant une demande |
| Données émotionnelles (météo, capsules, journal, réponses IA) | Tant que le compte est actif. Suppression dans les 30 jours suivant une demande de retrait du consentement |
| Logs techniques | 12 mois |
| Données d'abonnement | Durée légale comptable de 10 ans (obligation fiscale) |
| Sauvegardes (backups) | `[X jours en rotation glissante — À COMPLÉTER selon politique Ionos]`, puis purgées automatiquement |
| Données envoyées à OpenAI (cache abuse detection) | Maximum 30 jours côté OpenAI, conformément à leur politique API |

## 9. Sécurité

Selen met en œuvre les mesures techniques et organisationnelles suivantes pour protéger vos données :

- Communications chiffrées (HTTPS / TLS 1.2 ou supérieur) sur l'ensemble des échanges client-serveur
- Stockage chiffré au repos via les mécanismes de l'hébergeur
- Authentification par token JWT signé
- Accès aux données restreint au responsable de traitement
- Sauvegardes régulières
- Suppression en cascade des données associées à un compte supprimé (vérifiée techniquement par contraintes de base de données)

## 10. Vos droits

Conformément aux articles 12 à 23 du RGPD, vous disposez des droits suivants :

- **Droit d'accès** (art. 15) — obtenir confirmation que vos données sont traitées et en recevoir une copie
- **Droit de rectification** (art. 16) — corriger des données inexactes ou incomplètes
- **Droit à l'effacement** (art. 17) — demander la suppression de votre compte et de l'ensemble de vos données
- **Droit à la limitation du traitement** (art. 18) — limiter l'usage de vos données dans certains cas
- **Droit à la portabilité** (art. 20) — recevoir vos données dans un format structuré et lisible par machine
- **Droit d'opposition** (art. 21) — vous opposer à certains traitements (notamment ceux fondés sur l'intérêt légitime)
- **Droit de retirer votre consentement à tout moment** (art. 7.3)

## 11. Comment exercer vos droits

Pour exercer un ou plusieurs de ces droits, écrivez à : **contact@instantselen.fr**

Vous recevrez une réponse dans un délai maximum de **30 jours** à compter de la réception de votre demande (délai pouvant être prolongé à 90 jours pour des demandes complexes, sur notification motivée).

Afin de vérifier votre identité et de prévenir toute usurpation, Selen peut être amenée à vous demander une preuve d'identité raisonnable (capture d'écran de votre compte connecté, ou copie d'une pièce d'identité dont les éléments non strictement nécessaires peuvent être masqués).

## 12. Droit de réclamation auprès de la CNIL

Si vous estimez que vos droits ne sont pas respectés ou que le traitement de vos données ne respecte pas le RGPD, vous avez le droit d'introduire une réclamation auprès de la **Commission Nationale de l'Informatique et des Libertés (CNIL)** :

- 3 Place de Fontenoy, TSA 80715, 75334 Paris CEDEX 07
- Téléphone : +33 1 53 73 22 22
- Plainte en ligne : https://www.cnil.fr/fr/plaintes

## 13. Cookies et traceurs

Selen est une application mobile et ne dépose pas de cookies au sens du droit communautaire. Les données techniques nécessaires au fonctionnement de l'application (token d'authentification, préférences locales) sont stockées localement sur votre appareil via les mécanismes natifs iOS / Android (UserDefaults, SharedPreferences, AsyncStorage).

Aucun traceur publicitaire ni outil d'analyse comportementale tiers (type Google Analytics, Facebook Pixel) n'est utilisé dans l'application à ce jour.

## 14. Modifications de la politique

Selen peut modifier cette politique pour refléter les évolutions de l'application, des sous-traitants ou de la réglementation. Les modifications substantielles vous seront notifiées par email à l'adresse associée à votre compte, ainsi que par une notification dans l'application, et un nouveau consentement vous sera demandé lorsque la modification l'exige (notamment toute évolution touchant aux données de santé ou aux transferts hors UE).

## 15. Date de mise à jour

**[Date de publication]** — Version 2

---

> ⚠️ **À compléter avant publication** :
> 1. SIRET (à récupérer sur https://avis-situation-sirene.insee.fr/)
> 2. Adresse de domiciliation professionnelle
> 3. Durée de conservation des backups (selon réponse Ionos)
> 4. Date de publication (recommandée : 28 mai 2026, après signature du DPA OpenAI)
