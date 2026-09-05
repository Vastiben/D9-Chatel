# Contexte — D9-Chatel

Suivi du Groupe 8 (Juniors D-9, AFF-FFV) pour Team Veveyse (5014) c :
calendrier, résultats, et un classement calculé puisque la fédération n'en
publie pas dans cette catégorie.

## Vocabulaire

- **AFF-FFV** : Association fribourgeoise de football, fédération qui gère
  le championnat.
- **Groupe 8** : la poule de 16 équipes suivie ici, au sein de la
  Stärkeklasse 2 (niveau de force 2) des Juniors D-9.
- **Journée** : un cycle de matchs où chacune des 16 équipes du groupe joue
  une fois (8 rencontres). Les journées ne sont pas numérotées par
  l'AFF-FFV. `dashboard.html` les déduit automatiquement : toutes les
  rencontres sont triées par date (puis heure, puis numéro de match), puis
  découpées en tranches de 8 — la 1ère tranche est la journée 1, etc. Ce
  n'est jamais stocké dans les données, seulement recalculé à l'affichage.
- **Spielnummer / numéro de match** : identifiant unique de chaque
  rencontre côté AFF-FFV. Sert de clé pour dédupliquer les rencontres vues
  sur plusieurs captures d'écran qui se chevauchent.
- **Statuts d'une rencontre** : `termine` (score saisi), `score_non_saisi`
  (coup d'envoi passé, score pas encore entré côté AFF-FFV),
  `a_venir` (pas encore commencée).
- **Classement calculé** : reconstitué à partir des scores, avec le même
  ordre de critères qu'un classement officiel — points, confrontation
  directe, différence de buts, buts marqués — mais ce n'est pas un document
  de la fédération. Voir `docs/adr/0001-classement-calcule.md`.
- **Confrontation directe** : au sein d'un groupe d'équipes à égalité de
  points, les points gagnés uniquement dans les rencontres jouées entre
  elles (pas l'ensemble de leurs matchs).

## Portée

- **Dans le périmètre** : le Groupe 8, ronde d'automne 2026, calendrier et
  scores, classement calculé.
- **Hors périmètre** : tout jugement ou recommandation sur le niveau d'une
  équipe ou une éventuelle inscription en division supérieure — ce repo
  documente des faits, pas des avis.

## Source des données — pourquoi c'est manuel

Le match center (`matchcenter.aff-ffv.ch`) et le service de widgets
(`widget.football.ch`) de l'ASF/SFV bloquent explicitement l'accès
automatisé : une requête machine reçoit un message indiquant que l'accès
est interdit et renvoie vers `clubservices@football.ch` pour toute demande
d'accès aux données. Ce repo respecte cette règle — **aucun scraping,
aucun accès programmatique**. Une personne prend toujours la capture
d'écran ; c'est l'étape ensuite (lecture de l'image, mise à jour du
classement) qui est automatisée.

Si un accès autorisé aux données est un jour obtenu (voir la demande à
`clubservices@football.ch`), cette contrainte disparaît et la capture
d'écran elle-même devient inutile — à documenter dans un nouvel ADR le cas
échéant.

## Deux façons de mettre à jour, une source qui peut diverger

1. **Directement sur la page publiée** (voir ADR-0002) : Bastien dépose
   une capture dans la section « Ajouter de nouveaux scores » de
   `dashboard.html`, Claude (capacité `sample`) lit l'image, la page
   fusionne et republie elle-même (capacité `artifact`) — sans passer par
   une session Claude Code. C'est le chemin du quotidien.
2. **Via la skill `mettre-a-jour-classement`**, dans une session Claude
   Code ouverte sur ce repo : met à jour `data/matches.json`, régénère
   `dashboard.html`, committe et republie.

Ces deux chemins écrivent le même format de données, mais **pas le même
exemplaire** : le chemin 1 ne touche que la page publiée en ligne, jamais
ce repo. Après une série de mises à jour faites uniquement depuis la page,
`data/matches.json` devient un instantané figé, pas la vérité courante. Si
ça devient gênant, redemander à Claude de resynchroniser `data/matches.json`
depuis le contenu actuellement publié.
