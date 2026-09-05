# ADR-0001 — Classement calculé, données saisies manuellement

**Statut** : Acceptée — 2026-09-05

## Contexte

L'AFF-FFV ne publie pas de classement pour les Juniors D-9 : la catégorie
n'a que le calendrier et les résultats. Bastien veut néanmoins un
classement, notamment pour évaluer objectivement le niveau d'une équipe
dans son groupe.

Par ailleurs, le match center et le widget officiels de l'ASF/SFV
répondent à toute requête automatisée par un message explicite interdisant
l'accès machine et renvoyant vers `clubservices@football.ch` pour un accès
autorisé. Contourner ce blocage (navigateur headless, imitation de
requêtes humaines) n'a pas été envisagé.

## Décision

1. **Le classement est calculé, pas repris d'une source officielle.** Il
   suit l'ordre de critères habituel d'un classement de football : points,
   puis confrontation directe entre équipes à égalité (sur leurs seules
   rencontres mutuelles), puis différence de buts, puis buts marqués. Un
   départage alphabétique final assure un ordre déterministe si tout le
   reste est encore égal — ce n'est pas un critère du règlement, seulement
   une convention d'affichage documentée comme telle.
2. **Les données sont saisies manuellement**, à partir de captures d'écran
   que Bastien envoie du widget officiel. La skill
   `mettre-a-jour-classement` normalise ce traitement (dédoublonnage par
   numéro de match, fusion dans `data/matches.json`, régénération de
   `dashboard.html`, republication).
3. **La page ne porte aucun jugement** (niveau d'une équipe, opportunité
   d'inscription en division supérieure) : elle documente des faits.

## Conséquences

- Le classement peut diverger légèrement d'un classement officiel
  équivalent si l'AFF-FFV utilisait un critère de départage différent
  au-delà de la différence de buts et des buts marqués (non documenté
  publiquement pour cette catégorie) — accepté comme limite connue.
- La fraîcheur des données dépend de la fréquence à laquelle Bastien
  envoie des captures ; pas de mise à jour automatique tant qu'un accès
  autorisé n'est pas obtenu.
- Si un accès autorisé aux données est obtenu plus tard, cette décision
  sera révisée dans un nouvel ADR plutôt que modifiée sur place.
