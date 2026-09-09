# SHUYAHBF.md — Règles générales de travail (tous dépôts)

Ce fichier est le socle générique utilisé comme point de départ pour le
`CLAUDE.md` de chaque nouveau dépôt/projet. Il contient les règles et
consignes valables **quel que soit le projet**. Les règles propres à un
projet précis (conventions métier, structure de fichiers, choix techniques
spécifiques) vont dans le `CLAUDE.md` de CE projet, pas ici.

À chaque nouveau dépôt ou nouvelle branche de travail : proposer d'utiliser
ce fichier comme base, le faire valider, puis le copier dans le dépôt en tant
que `CLAUDE.md` (ou en complément d'un `CLAUDE.md` existant). Ce fichier
s'enrichit au fil des projets — toute règle générale découverte ailleurs doit
être proposée pour ajout ici, pas seulement dans le `CLAUDE.md` du projet où
elle a été découverte.

## Prudence — production (actuelle ou à venir)

- Par défaut, considérer qu'un dépôt de production hébergeant une activité
  réelle (site, outil métier) l'est réellement, sauf indication contraire.
  Tester avant de considérer une tâche terminée ; éviter toute régression sur
  l'existant.
- Cette prudence s'applique aussi à un projet **pas encore en production
  mais destiné à y aller** (nouveau développement, MVP, prototype en cours
  de construction vers un lancement réel) : dès qu'un projet a vocation à
  devenir une source de production, le canaliser avec la même discipline
  (tests, validation avant livraison, pas de raccourci) plutôt que d'attendre
  sa mise en ligne effective pour commencer à s'en préoccuper.
- Ne jamais faire de `git push` sans validation explicite pour CE lot précis
  — une autorisation donnée pour un lot précédent ne vaut pas pour la suite.
- Avant toute action destructrice ou difficile à annuler (reset, force-push,
  suppression), vérifier l'état (`git status`) et demander confirmation.

## Vérification et tests

- Après toute modification de code : vérifier la syntaxe (parseur adapté au
  langage — `ast.parse` en Python, `@babel/parser` en JS/JSX, etc.) et, quand
  c'est possible à moindre coût, rejouer la logique clé isolément (petits
  scripts de test reproduisant les fonctions/validateurs modifiés) avant de
  livrer quoi que ce soit.
- Une fois une correction déployée par un agent tiers (Emergent.sh ou
  équivalent) : vérifier systématiquement que le résultat correspond
  exactement à ce qui a été transmis (comparaison fichier par fichier),
  qu'aucun fichier hors scope n'a été touché, et qu'aucune régression n'a été
  introduite. Signaler clairement tout écart.

## Workflow de livraison — projets déployés via un agent tiers (Emergent.sh ou équivalent)

Quand le déploiement se fait via un agent IA tiers qui relance ses propres
vérifications par défaut (ex. Emergent.sh et son Testing Agent) :

1. Développer, vérifier, committer localement.
2. Livrer exactement **2 fichiers** :
   - Un **patch git unique et cumulatif** pour tout le lot de corrections
     (tous les commits du lot concaténés via
     `git format-patch <base>..<head> --stdout`), nommé
     `<projet>-corrections_<premier>_to_<dernier>_<hash-court-du-dernier-commit>.patch`.
     `<premier>`/`<dernier>` = numéros d'ordre des patches DE CE LOT (le lot
     suivant repart du numéro suivant, ex. `6_to_8`, jamais `1_to_8` s'il
     fait suite à un lot `1_to_5` déjà transmis). `<hash-court>` = hash court
     Git (`git log --oneline`) du dernier commit inclus. Toujours vérifié
     applicable en une seule commande `git am` sur une copie propre avant
     remise.
   - Un **prompt Markdown unique**, rédigé à la première personne (comme si
     l'utilisateur l'envoyait lui-même à l'agent tiers), listant précisément
     les fichiers/changements de ce lot, et incluant TOUJOURS :
     - "Ne lance pas le Testing Agent / les vérifications automatiques sur
       ces corrections — déjà validées."
     - "Ne revérifie pas les interfaces — déjà validées par l'utilisateur."
     - la liste exacte des fichiers modifiés (rien d'autre à toucher).
     - "Applique le patch et déploie directement, sans ré-analyser."
3. Ne jamais pousser sur GitHub sans autorisation explicite pour ce lot.

## Aperçus visuels avant/à l'implémentation d'un écran

- Dès qu'une tâche crée ou modifie un écran/interface utilisateur (nouveau
  formulaire, nouveau champ visible, changement de disposition), générer
  systématiquement un aperçu visuel (artifact HTML) montrant le rendu
  attendu — pas seulement une description textuelle des changements.
  Produire cet aperçu à l'implémentation (en même temps que le code, pas
  seulement avant), pour que l'utilisateur voie concrètement le résultat
  sans devoir attendre un déploiement.
- L'aperçu doit refléter fidèlement le design system réel du projet
  (couleurs, typographies, composants existants) plutôt qu'un style
  générique — s'appuyer sur le code existant (tailwind.config, classes
  déjà utilisées) pour rester cohérent avec l'app réelle.
- Mettre en évidence ce qui est nouveau/modifié (badge, encadré, légende)
  plutôt que de livrer un aperçu qu'on ne peut pas distinguer de l'existant.
- Itérer sur cet aperçu au fil des retours avant de considérer le besoin
  bien compris, surtout pour des changements ambigus ou à plusieurs
  variantes possibles — c'est plus rapide à ajuster qu'une implémentation
  complète.

## Documentation persistante par projet

- Chaque projet actif doit avoir son propre `CLAUDE.md` à la racine,
  documentant ses règles métier spécifiques (conventions UI, format de
  données, pièges connus, cartographie des fichiers clés). Le maintenir à
  jour au fil des sessions plutôt que de laisser l'utilisateur répéter les
  mêmes consignes.
- Proposer explicitement d'ajouter une règle au `CLAUDE.md` du projet dès
  qu'une consigne métier est donnée deux fois, ou qu'elle a un impact
  durable (facturation, sécurité, cohérence des données).
