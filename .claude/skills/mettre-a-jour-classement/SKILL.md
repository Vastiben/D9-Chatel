---
name: mettre-a-jour-classement
description: Traite des captures d'écran du match center ou du widget AFF-FFV pour le Groupe 8 (Juniors D-9, Team Veveyse 5014 c) et met à jour data/matches.json + dashboard.html dans ce repo. Se déclenche quand Bastien envoie des captures dans une SESSION CLAUDE CODE ouverte sur ce repo (pas depuis la page publiée elle-même, qui a son propre chemin de mise à jour — voir ADR-0002). Utile pour un rattrapage en masse, une correction historique, ou une resynchronisation du repo après des mises à jour faites uniquement depuis la page.
---

# Mettre à jour le classement du Groupe 8

Depuis une session Claude Code, ce qui suit régénère `data/matches.json` et
`dashboard.html`. Pour l'usage courant (une capture, à la volée), la page
publiée le fait elle-même sans passer par ici — voir ADR-0002 et le champ
« Ajouter de nouveaux scores » de `dashboard.html`.

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
   id="matches-data">` de `dashboard.html` — c'est la seule copie tolérée
   des données, nécessaire parce qu'un Artifact est un fichier unique sans
   accès à un fichier externe.

6. **Republier** `dashboard.html` comme Artifact en passant l'URL déjà
   enregistrée dans `README.md` (jamais en créer un nouveau).

7. **Committer et pousser** `data/matches.json` et `dashboard.html` sur
   `main`, message de commit décrivant les rencontres ajoutées.

Terminé quand : chaque numéro de match n'apparaît qu'une fois dans
`data/matches.json`, le total de rencontres correspond au cumul réellement
vu dans les captures (pas de perte au recoupement des chevauchements), et
l'URL de l'artifact republié est inchangée.

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
