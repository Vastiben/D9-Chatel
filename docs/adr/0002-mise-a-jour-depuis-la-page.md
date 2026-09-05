# ADR-0002 — Mise à jour du classement directement depuis la page publiée

**Statut** : Remplacée par ADR-0003 — 2026-09-05 (Bastien a préféré garder
le partage public plutôt que la mise à jour automatique, incompatibles sur
cet artifact ; le code décrit ci-dessous a été retiré de `dashboard.html`)

## Contexte

Le chemin de mise à jour de l'ADR-0001 (capture d'écran → session Claude
Code → skill → commit) impose d'ouvrir une session à chaque nouveau score.
Bastien veut pouvoir déposer une capture directement sur la page publiée et
que la mise à jour se fasse là, sans lui.

Les Artifacts Claude exposent des capacités runtime pour ça :
- `sample` : la page peut demander à Claude d'analyser une image, sur le
  compte de la personne qui regarde la page.
- `artifact` : la page peut republier une nouvelle version d'elle-même.

## Décision

1. **`dashboard.html` déclare `sample` et `artifact`.** Une section
   « Ajouter de nouveaux scores » accepte des images ; au clic, la page
   envoie les captures à `sample.json` avec une consigne d'extraction
   stricte (champs exacts, dates ISO, `null` si non visible), fusionne le
   résultat dans les données en mémoire par numéro de match (ou par
   date+équipes si le numéro manque), recalcule le classement, puis
   republie via `artifact.publish(html)`.
2. **Le document complet à republier est reconstruit depuis le DOM, pas
   depuis une copie de son propre code source.** La documentation de la
   capacité `artifact` interdit de sérialiser le DOM en direct
   (`outerHTML`) car il peut contenir de l'état de session ou des scripts
   injectés par la plateforme. Solution retenue : reconstruire le document
   par une liste explicite d'éléments connus (`<title>`, les `<link>` de
   police, le `<style id="app-style">`, le squelette `.wrap` vidé de son
   contenu généré, le `<script id="app-script">` relu via `textContent`) —
   jamais une copie figée du HTML entretenue à la main. Ainsi, toute future
   modification de la page se répercute automatiquement dans ce qu'elle
   republie, sans double maintenance.
3. **Les journées ne sont plus un champ stocké** (voir aussi
   `CONTEXT.md`) : elles sont recalculées à chaque rendu en triant toutes
   les rencontres par date puis en les découpant en tranches de 8. Ça
   évite à l'extraction (ou à Bastien) d'avoir à trancher une numérotation
   ambiguë à chaque capture.
4. **Le statut déduit d'une extraction automatique est binaire** :
   `termine` si les deux scores sont lisibles, sinon `a_venir`. La nuance
   `score_non_saisi` (coup d'envoi passé mais score pas encore saisi côté
   AFF-FFV) reste possible dans les données mais n'est plus produite par
   l'extraction automatique — la distinction exigerait de connaître
   l'heure actuelle au moment de la capture, information non fiable à
   extraire d'une image. Sans conséquence sur le classement : les deux
   statuts sont également exclus du calcul.

## Conséquences

- Testé de bout en bout par simulation (extraction → fusion → recalcul →
  reconstruction du document → republication → relecture dans un DOM
  neuf) : voir historique de session. **Non testé en conditions réelles**
  dans un vrai viewer Claude — je n'ai pas pu observer un appel réel à
  `sample`/`artifact` depuis cette session. Si le premier essai réel
  échoue silencieusement ou renvoie une erreur inattendue, le signaler
  plutôt que réessayer en boucle.
- `data/matches.json` (ADR-0001) et la page publiée peuvent diverger — voir
  `CONTEXT.md` § Deux façons de mettre à jour.
- La page reste utilisable en lecture seule si `sample`/`artifact` ne sont
  pas accordés dans une vue donnée (section masquée automatiquement) —
  aucune régression pour un lecteur qui ne fait que consulter le
  classement.
