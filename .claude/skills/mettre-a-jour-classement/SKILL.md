---
name: mettre-a-jour-classement
description: Traite des captures d'écran du match center ou du widget AFF-FFV pour le Groupe 8 (Juniors D-9, Team Veveyse 5014 c) et met à jour le dashboard. Se déclenche dès que Bastien envoie une ou plusieurs images du calendrier ou des résultats du groupe — même en vrac, même avec des chevauchements entre captures consécutives.
---

# Mettre à jour le classement du Groupe 8

`index.html` est hébergé sur GitHub Pages et charge `data/matches.json`
par `fetch` à chaque visite (voir ADR-0004) : mettre à jour ce fichier et
le pousser sur `main` suffit, la page se met à jour toute seule — pas de
republication à faire.

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
   - Pas de champ `journee` à assigner : `index.html` le déduit lui-même
     des dates à l'affichage (tri chronologique, tranches de 8 rencontres —
     voir CONTEXT.md).

4. Mettre à jour `derniere_maj` à la date du traitement.

5. **Committer et pousser** `data/matches.json` sur `main`, message de
   commit décrivant les rencontres ajoutées. Rien d'autre à toucher :
   `index.html` ne contient pas les données.

Terminé quand : chaque numéro de match n'apparaît qu'une fois dans
`data/matches.json`, et le total de rencontres correspond au cumul
réellement vu dans les captures (pas de perte au recoupement des
chevauchements).

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
