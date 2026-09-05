# D9-Chatel

Dashboard du Groupe 8 (Juniors D-9, AFF-FFV, Team Veveyse 5014 c). Voir
`README.md` pour la structure et `CONTEXT.md` pour le vocabulaire du
domaine.

## Règles qui ne se discutent pas

- **Toujours en français.**
- **Aucun jugement ni recommandation** dans `dashboard.html` (niveau d'une
  équipe, opportunité d'inscription en division supérieure). La page
  documente des faits — classement et scores — jamais un avis.
- **Jamais d'accès automatisé** au match center ou au widget AFF-FFV
  (interdit explicitement par la fédération — voir `CONTEXT.md`). Les
  données arrivent uniquement par captures d'écran envoyées par Bastien.
- `data/matches.json` est la source de vérité. `dashboard.html` en est une
  copie générée (voir la skill ci-dessous) — ne jamais l'éditer
  indépendamment.
- `dashboard.html` est **statique, sans capacité déclarée** — le partage
  public de la page l'exige (voir ADR-0003). Ne pas réintroduire
  `sample`/`artifact` sans en reparler avec Bastien.

## Mise à jour depuis des captures d'écran

Skill `mettre-a-jour-classement` (`.claude/skills/`) : se déclenche
automatiquement dès réception d'images du calendrier ou des résultats du
groupe, dans une session Claude Code ouverte sur ce repo.
