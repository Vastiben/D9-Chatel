# D9-Chatel

Calendrier et classement calculé du Groupe 8 (Juniors D-9, AFF-FFV) pour
Team Veveyse (5014) c.

**Dashboard publié** : https://claude.ai/code/artifact/c91020d5-028d-4711-885b-0cda7dc7af3a

## Structure

- `dashboard.html` — la page publiée comme Artifact. Contient les données
  intégrées dans un bloc `<script type="application/json">`, et se met à
  jour elle-même quand on lui dépose une capture d'écran (voir ci-dessous).
- `data/matches.json` — une copie de référence des données, utile pour un
  rattrapage en masse ou une correction historique depuis une session
  Claude Code. Peut diverger de la page publiée — voir `CONTEXT.md` §
  Deux façons de mettre à jour.
- `CONTEXT.md` — vocabulaire du domaine et portée du projet.
- `docs/adr/` — décisions structurantes : ADR-0001 (calcul du classement,
  saisie manuelle), ADR-0002 (mise à jour depuis la page publiée).
- `.claude/skills/mettre-a-jour-classement/` — la skill pour le chemin
  « session Claude Code ».

## Mettre à jour le dashboard

**Au quotidien** : ouvrir le dashboard publié (lien ci-dessus), section
« Ajouter de nouveaux scores », déposer une ou plusieurs captures d'écran
du widget ou du match center AFF-FFV pour le Groupe 8 — en vrac,
chevauchements entre captures inclus. La page lit les images et se
republie elle-même, sans session Claude Code.

**Pour un rattrapage en masse ou une correction historique** : envoyer les
captures dans une session Claude Code ouverte sur ce repo — la skill
`mettre-a-jour-classement` met à jour `data/matches.json`, régénère
`dashboard.html` et republie l'Artifact à la même URL.

## Pourquoi la capture d'écran reste nécessaire

L'accès automatisé au match center et au widget de l'ASF/SFV est
explicitement interdit par la fédération (message de blocage renvoyant
vers `clubservices@football.ch`). Ce projet ne le contourne pas — une
personne prend toujours la capture ; seule la lecture et la mise à jour
qui suivent sont automatisées. Voir `CONTEXT.md` § Source des données.
