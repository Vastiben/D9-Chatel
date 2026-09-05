# ADR-0004 — Hébergement sur GitHub Pages plutôt qu'un Artifact Claude

**Statut** : Acceptée — 2026-09-05

## Contexte

ADR-0003 revenait à un Artifact Claude statique pour préserver le partage
public. Mais un partage public claude.ai a une contrainte plus profonde que
prévu : **aucune capacité runtime (`sample`, `db`, `artifact`, ...) ne peut
être déclarée sur un artifact partagé publiquement** — la documentation de
la capacité `db` le dit explicitement, pas seulement `sample`. Un artifact
public est donc, par construction de la plateforme, figé sur une version
précise (« Shared version » dans son menu de partage), et l'interface
elle-même empêche de choisir « Latest » tant que l'accès reste public. Il
faut à chaque mise à jour redevenir privé, changer la version épinglée,
republier public — ce que Bastien juge, à raison, pas praticable pour un
usage régulier.

## Décision

Héberger la page sur **GitHub Pages**, à partir de ce même repo :
- `index.html` (anciennement `dashboard.html`) charge `data/matches.json`
  par un `fetch` relatif ordinaire — possible ici car ce n'est plus un
  Artifact Claude (dont le bac à sable bloque toute requête réseau externe),
  mais un vrai site statique.
- L'URL est stable et ne dépend d'aucun mécanisme de partage à refaire :
  toute mise à jour de `data/matches.json` poussée sur `main` est visible
  immédiatement à la prochaine visite, sans republier quoi que ce soit.
- Testé de bout en bout via un serveur HTTP local avant activation, `fetch`
  compris (voir historique de session).

Activation de GitHub Pages sur le repo (Settings → Pages → Deploy from a
branch → `main` → `/`) : une action manuelle de Bastien, aucun outil ne
permet de le faire depuis une session Claude Code.

## Conséquences

- Les deux liens Artifact Claude (l'original figé, et le second créé en
  contournement) sont abandonnés — plus la peine de les maintenir à jour.
- `dashboard-v2.html` est supprimé ; retour à un seul fichier de page.
- La skill `mettre-a-jour-classement` n'a plus qu'à committer/pousser
  `data/matches.json` — plus de régénération d'un bloc JSON embarqué, plus
  de republication d'Artifact.
- Le lien à partager devient l'URL GitHub Pages (voir `README.md`), pas une
  URL `claude.ai/code/artifact/...`.
