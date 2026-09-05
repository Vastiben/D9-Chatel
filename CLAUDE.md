# D9-Chatel

Dashboard du Groupe 8 (Juniors D-9, AFF-FFV, Team Veveyse 5014 c), hébergé
sur GitHub Pages (ADR-0004). Voir `README.md` pour la structure et
`CONTEXT.md` pour le vocabulaire du domaine.

## Règles qui ne se discutent pas

- **Toujours en français.**
- **Aucun jugement ni recommandation** dans `index.html` (niveau d'une
  équipe, opportunité d'inscription en division supérieure). La page
  documente des faits — classement et scores — jamais un avis.
- **Jamais d'accès automatisé** au match center ou au widget AFF-FFV
  (interdit explicitement par la fédération — voir `CONTEXT.md`). Les
  données arrivent uniquement par captures d'écran envoyées par Bastien.
- `data/matches.json` est la seule source de vérité, chargée par `fetch`
  à chaque visite — plus de copie embarquée dans `index.html` à maintenir.

## Mise à jour depuis des captures d'écran

Skill `mettre-a-jour-classement` (`.claude/skills/`) : se déclenche
automatiquement dès réception d'images du calendrier ou des résultats du
groupe. Met à jour `data/matches.json` et pousse sur `main` — GitHub Pages
sert la nouvelle version sans autre étape.
