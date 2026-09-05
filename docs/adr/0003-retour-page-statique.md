# ADR-0003 — Retour à une page statique, partage public conservé

**Statut** : Acceptée — 2026-09-05 · Remplace ADR-0002

## Contexte

ADR-0002 ajoutait à `dashboard.html` la possibilité de se mettre à jour
depuis elle-même (capacités `sample`/`artifact`). Au moment de l'activer,
Claude a refusé : ces capacités ne sont pas autorisées sur un artifact
partagé publiquement (protection contre un visiteur anonyme qui
consommerait le quota Claude du propriétaire).

Bastien a tranché : garder le partage public plutôt que la mise à jour
automatique.

## Décision

Retirer entièrement de `dashboard.html` le code ajouté par ADR-0002:
section « Ajouter de nouveaux scores », extraction/fusion des rencontres,
reconstruction du document, republication. La page redevient un fichier
HTML statique, sans capacité déclarée. Seul le calcul automatique des
journées à partir des dates (voir ADR-0001, confirmé indépendamment de
cette décision) est conservé — il ne dépend d'aucune capacité.

## Conséquences

- Mise à jour à nouveau uniquement via la skill `mettre-a-jour-classement`
  dans une session Claude Code (voir `README.md`) — plus de chemin direct
  depuis la page.
- `data/matches.json` redevient la seule source de vérité ; la divergence
  décrite dans ADR-0002 / `CONTEXT.md` n'a plus lieu d'être — cette
  mention est retirée de `CONTEXT.md`.
- ADR-0002 reste dans l'historique pour mémoire (le mécanisme a été conçu
  et testé, seul le compromis partage-public / auto-actualisation a
  changé) mais ne décrit plus l'état réel de `dashboard.html`.
