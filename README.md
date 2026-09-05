# D9-Chatel

Calendrier et classement calculé du Groupe 8 (Juniors D-9, AFF-FFV) pour
Team Veveyse (5014) c.

**Dashboard publié** : https://vastiben.github.io/D9-Chatel/

## Structure

- `index.html` — la page, hébergée par GitHub Pages (voir ADR-0004). Charge
  `data/matches.json` par un simple `fetch` relatif à chaque visite — pas
  de copie des données à maintenir dans la page.
- `data/matches.json` — la seule source de vérité des données.
- `CONTEXT.md` — vocabulaire du domaine et portée du projet.
- `docs/adr/` — décisions structurantes : ADR-0001 (calcul du classement,
  saisie manuelle), ADR-0002 et ADR-0003 (mise à jour depuis un Artifact
  Claude — tentée puis abandonnée), ADR-0004 (hébergement GitHub Pages).
- `.claude/skills/mettre-a-jour-classement/` — la skill qui traite les
  captures d'écran envoyées par Bastien et met à jour les données.

## Mettre à jour le dashboard

Envoyer à une session Claude Code ouverte sur ce repo une ou plusieurs
captures d'écran du widget ou du match center AFF-FFV pour le Groupe 8 —
en vrac, chevauchements entre captures inclus. La skill
`mettre-a-jour-classement` se déclenche automatiquement, met à jour
`data/matches.json` et pousse sur `main` — la page se met à jour toute
seule à la prochaine visite, aucune republication à faire.

## Pourquoi c'est manuel

L'accès automatisé au match center et au widget de l'ASF/SFV est
explicitement interdit par la fédération (message de blocage renvoyant
vers `clubservices@football.ch`). Ce projet ne le contourne pas — voir
`CONTEXT.md` § Source des données.
