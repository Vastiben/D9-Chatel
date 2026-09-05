# D9-Chatel

Calendrier et classement calculé du Groupe 8 (Juniors D-9, AFF-FFV) pour
Team Veveyse (5014) c.

**Dashboard publié** : https://claude.ai/code/artifact/c91020d5-028d-4711-885b-0cda7dc7af3a

**Second lien** (contournement d'un bug de cache — voir plus bas) :
https://claude.ai/code/artifact/78e8172a-be3a-4428-afea-c48b81d640eb

## Bug connu — deux liens en circulation

Le premier lien sert du contenu périmé à un visiteur public (constaté
le 2026-09-05 : la femme de Bastien voyait une ancienne version dès sa
toute première ouverture, alors que Bastien lui-même, propriétaire
connecté, voyait toujours la dernière version). Tout indique un cache
côté serveur propre aux pages publiques, qui ne s'invalide pas à la
republication — hors de portée depuis ce repo ou cette session Claude
Code. Un second artifact a été créé en attendant (jamais mis en cache,
donc fiable pour l'instant) ; à signaler en feedback Claude, et à
n'utiliser qu'un seul lien une fois corrigé.

## Structure

- `dashboard.html` / `dashboard-v2.html` — la page publiée comme Artifact,
  en double tant que le bug ci-dessus n'est pas réglé (les deux fichiers
  doivent rester identiques). Statique (partagée publiquement — voir
  ADR-0003), données intégrées dans un bloc `<script
  type="application/json">` : un Artifact est un fichier unique, il ne
  peut pas aller chercher `data/matches.json` par un `fetch`.
- `data/matches.json` — la source de vérité des données. `dashboard.html`
  en est une copie générée, jamais éditée à la main indépendamment.
- `CONTEXT.md` — vocabulaire du domaine et portée du projet.
- `docs/adr/` — décisions structurantes : ADR-0001 (calcul du classement,
  saisie manuelle), ADR-0002 (mise à jour depuis la page — tentée puis
  retirée), ADR-0003 (retour à une page statique).
- `.claude/skills/mettre-a-jour-classement/` — la skill qui traite les
  captures d'écran envoyées par Bastien et republie le dashboard.

## Mettre à jour le dashboard

Envoyer à une session Claude Code ouverte sur ce repo une ou plusieurs
captures d'écran du widget ou du match center AFF-FFV pour le Groupe 8 —
en vrac, chevauchements entre captures inclus. La skill
`mettre-a-jour-classement` se déclenche automatiquement, met à jour
`data/matches.json`, régénère `dashboard.html` et `dashboard-v2.html`
(identiques tant que le bug ci-dessus n'est pas réglé) et republie les
deux Artifacts, chacun à sa propre URL.

## Pourquoi c'est manuel

L'accès automatisé au match center et au widget de l'ASF/SFV est
explicitement interdit par la fédération (message de blocage renvoyant
vers `clubservices@football.ch`). Ce projet ne le contourne pas — voir
`CONTEXT.md` § Source des données.
