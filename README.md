# D9-Chatel

Calendrier et classement calculé du Groupe 8 (Juniors D-9, AFF-FFV) pour
Team Veveyse (5014) c.

**Dashboard publié** : https://claude.ai/code/artifact/c91020d5-028d-4711-885b-0cda7dc7af3a

## Structure

- `dashboard.html` — la page publiée comme Artifact. Contient les données
  intégrées dans un bloc `<script type="application/json">` : un Artifact
  est un fichier unique, il ne peut pas aller chercher `data/matches.json`
  par un `fetch`.
- `data/matches.json` — la source de vérité des données. `dashboard.html`
  en est une copie générée, jamais éditée à la main indépendamment.
- `CONTEXT.md` — vocabulaire du domaine et portée du projet.
- `docs/adr/` — décisions structurantes (voir ADR-0001 sur le calcul du
  classement et la saisie manuelle des données).
- `.claude/skills/mettre-a-jour-classement/` — la skill qui traite les
  captures d'écran envoyées par Bastien et republie le dashboard.

## Mettre à jour le dashboard

Envoyer à une session Claude Code ouverte sur ce repo une ou plusieurs
captures d'écran du widget ou du match center AFF-FFV pour le Groupe 8 —
en vrac, chevauchements entre captures inclus. La skill
`mettre-a-jour-classement` se déclenche automatiquement, met à jour
`data/matches.json`, régénère `dashboard.html` et republie l'Artifact à
la même URL.

## Pourquoi c'est manuel

L'accès automatisé au match center et au widget de l'ASF/SFV est
explicitement interdit par la fédération (message de blocage renvoyant
vers `clubservices@football.ch`). Ce projet ne le contourne pas — voir
`CONTEXT.md` § Source des données.
