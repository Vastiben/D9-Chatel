---
name: mettre-a-jour-classement
description: Traite des captures d'écran du match center ou du widget AFF-FFV pour le Groupe 8 (Juniors D-9, Team Veveyse 5014 c) et met à jour le dashboard. Se déclenche dès que Bastien envoie une ou plusieurs images du calendrier ou des résultats du groupe — même en vrac, même avec des chevauchements entre captures consécutives.
---

# Mettre à jour le classement du Groupe 8

`dashboard.html` est un artifact statique et partagé publiquement : il ne
se met pas à jour lui-même (voir ADR-0003). C'est cette skill, exécutée
depuis une session Claude Code, qui régénère `data/matches.json` et
`dashboard.html` à chaque nouvelle capture.

Chaque capture montre une portion du calendrier du widget officiel, avec des
chevauchements fréquents entre captures consécutives (le bas de l'une est le
haut de la suivante). Le numéro de match (Spielnummer) est la clé de
déduplication : une même rencontre ne doit jamais apparaître deux fois dans
`data/matches.json`.

## Étapes

1. **Extraire** de chaque capture, pour chaque rencontre visible : date au
   format ISO `AAAA-MM-JJ`, numéro de match, équipe domicile, équipe
   extérieur, score si joué, heure de coup d'envoi si affichée.

2. **Dédupliquer** par numéro de match. Pour une rencontre sans numéro (pas
   encore commencée), dédupliquer par le triplet (date, domicile, extérieur)
   — si un numéro lui est attribué plus tard, il vient compléter cette même
   entrée, jamais en créer une nouvelle.

3. **Fusionner** dans `data/matches.json` :
   - nouvelles rencontres → ajoutées ;
   - rencontres déjà connues dont le score vient d'être saisi → mises à
     jour, `statut` passe à `"termine"`.
   - Pas de champ `journee` à assigner : `dashboard.html` le déduit lui-même
     des dates à l'affichage (tri chronologique, tranches de 8 rencontres —
     voir CONTEXT.md).

4. Mettre à jour `derniere_maj` à la date du traitement.

5. **Recopier** le contenu exact de `data/matches.json` (identique, pas
   reformulé) dans le bloc `<script type="application/json"
   id="matches-data">` de `dashboard.html`, **puis dans `dashboard-v2.html`
   à l'identique** (`cp dashboard.html dashboard-v2.html`) — deux fichiers
   tant que le bug de cache décrit dans `README.md` n'est pas réglé.

6. **Republier les deux fichiers** comme Artifacts, chacun en passant son
   URL déjà enregistrée dans `README.md` (jamais en créer un nouveau).

7. **Committer et pousser** `data/matches.json`, `dashboard.html` et
   `dashboard-v2.html` sur `main`, message de commit décrivant les
   rencontres ajoutées.

Terminé quand : chaque numéro de match n'apparaît qu'une fois dans
`data/matches.json`, le total de rencontres correspond au cumul réellement
vu dans les captures (pas de perte au recoupement des chevauchements),
`dashboard.html` et `dashboard-v2.html` sont identiques, et les deux URLs
republiées sont inchangées.

## Invariants de la page (à ne jamais réintroduire)

- **Aucun jugement ni recommandation** dans le contenu (pas de verdict sur
  une inscription en division supérieure, pas de commentaire sur le niveau
  d'une équipe). La page montre le classement et les scores, point.
- **Tri du classement** : points, puis confrontation directe entre équipes
  à égalité de points (calculée uniquement sur les rencontres jouées entre
  elles), puis différence de buts, puis buts marqués. Départage final
  alphabétique si tout est encore égal (convention d'affichage, pas une
  règle du règlement).
- **Tout le texte de la page est en français.**
- Le classement n'est pas officiel : l'AFF-FFV n'en publie pas en Juniors
  D-9. Le dire dans les notes méthodologiques de la page, ne pas le
  cacher.
