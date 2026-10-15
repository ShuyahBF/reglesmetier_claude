# Conception — Moteur GRH versionné (« jeux de règles »)

Statut : **conception, aucun code**. Rédigé le 15/10/2026 à la demande du propriétaire.
Cible : **sawali-GRH** (plateforme GRH multi-entreprises). ALBARKA n'est pas modifié (voir §8).
Complète `GRH_BURKINA.md` (règle 4 : paramètres versionnés ; bulletins validés jamais recalculés).

## 1. Objectif
Choisir dans le paramétrage **quelle version du moteur** s'applique à une entreprise / un établissement. Changer de
version change automatiquement les valeurs (taux, plafonds, barèmes), les termes (libellés, mentions) et la structure
du calcul (assiettes), sans toucher au code ni aux bulletins déjà validés.

## 2. Vocabulaire
- **Jeu de règles** : ensemble nommé, daté, sourcé de valeurs + termes + structure de calcul (ex. « BF-2013-TR »,
  « BF-2024-ALBARKA »).
- **Variante de moteur** : logique de calcul codée à l'avance (ex. `assiette_cnss_v1`). Un jeu de règles *choisit*
  des variantes, il ne contient JAMAIS de code exécutable.
- **Affectation** : lien (entreprise ou établissement) → jeu de règles, avec une date d'effet.
- **Bulletin figé** : bulletin validé, qui garde la version utilisée ET toutes ses valeurs calculées.

## 3. Modèle de données (MongoDB)
```
jeux_regles            (un document par version publiée)
  id, code ("BF-2024-ALBARKA"), libelle, pays, version (entier croissant)
  statut: brouillon | publie | retire
  date_effet_minimale, source ("TR 15/10/2026", "classeur ALBARKA 08/2024", texte officiel + référence)
  valeurs: { cnss:{taux_salarie, plafond, ...}, exonerations:[...], abattement:{...},
             bareme_iuts:[...], charges_famille:[...], charges_patronales:[...], arrondis:{...} }
  termes:  { total_brut:"Total brut", net_a_payer:"Net à payer", ... }   (libellés et mentions du bulletin)
  structure: { assiette_cnss:"assiette_cnss_v1", abattement:"abattement_par_categorie_v1", ... }
  parent_id (version dont celle-ci dérive), cree_par, cree_le, publie_par, publie_le, empreinte (hash du contenu)

affectations_regles    (entreprise/établissement → jeu de règles)
  id, entreprise_id, etablissement_id|null, jeu_regles_id, date_effet (première période de paie concernée),
  surcharges: { chemin_de_valeur: nouvelle_valeur }  (personnalisation identifiée, voir §6),
  cree_par, cree_le, motif

bulletins
  ... + jeu_regles_id, jeu_regles_empreinte, affectation_id, valeurs_calculees (toutes les lignes),
        statut: brouillon | valide, valide_par, valide_le
journal_regles         (qui a publié / affecté / retiré quoi, quand, pourquoi)
```
Index : `affectations_regles` (entreprise_id, etablissement_id, date_effet), `bulletins` (entreprise_id, periode),
`jeux_regles` (code, version).

## 4. Cycle de vie d'un jeu de règles
1. **Brouillon** : modifiable, jamais utilisable pour un bulletin validé.
2. **Publié** : **immuable** (l'empreinte le prouve). Toute correction = nouvelle version (parent_id = ancienne).
   Publication exigée : source écrite renseignée, tests de valeurs verts (§7), validation par un administrateur.
3. **Retiré** : n'est plus proposé pour de nouvelles affectations, reste lisible (bulletins anciens).
Jamais de suppression d'une version utilisée par un bulletin.

## 5. Résolution à l'exécution
Pour une entreprise/établissement et une **période de paie** : prendre l'affectation dont la date d'effet est la plus
récente ≤ début de période (établissement avant entreprise), charger le jeu de règles, appliquer les surcharges,
puis exécuter les variantes de moteur désignées. Le résultat et la version sont écrits dans le bulletin.
- Changement de version en cours de mois : interdit ; la date d'effet est toujours un début de période.
- Un bulletin **valide** n'est jamais recalculé. Une régularisation passe par un **nouveau bulletin rectificatif**
  (version choisie explicitement, motif obligatoire, ligne au journal).

## 6. Personnalisation par un employeur
- Versions officielles = figées. Un employeur peut ajouter des **surcharges** (ex. plafond d'exonération conventionnel),
  stockées dans l'affectation, affichées en évidence (« personnalisé ») sur l'écran et le bulletin de contrôle.
- Une surcharge garde son motif et son auteur. Pas de modification libre d'un jeu officiel.

## 7. Tests et contrôles (règle 7 de GRH_BURKINA.md)
- Chaque jeu de règles a un fichier de **cas chiffrés calculés à la main** (bulletins types : cadre/non-cadre,
  charges de famille, plafond CNSS atteint, heures supplémentaires, avantage en nature). Publication impossible si un cas échoue.
- Test de non-régression : un bulletin figé relu après publication d'une nouvelle version donne exactement les mêmes montants.
- Test d'immuabilité : modifier un jeu publié est refusé (l'empreinte change).
- Test de résolution : choix de l'affectation selon la date d'effet, établissement prioritaire sur l'entreprise.

## 8. Interface (paramétrage)
- Écran « Version du moteur » par entreprise/établissement : version actuelle, date d'effet, source, surcharges.
- **Aperçu avant/après** sur quelques salariés (écarts net/brut/IUTS/CNSS) avant de confirmer un changement.
- Droit réservé à l'administrateur ; confirmation explicite ; ligne au journal ; carte « Nouveautés » (règle 5).
- Termes et libellés de l'interface lus dans `termes` du jeu de règles (aucun libellé de paie en dur).

## 9. Risques et parades
| Risque | Parade |
|---|---|
| Bulletins validés modifiés par un changement de version | Bulletin figé (version + valeurs) ; rectificatif seulement |
| Mauvaise version choisie | Aperçu avant/après, droit admin, confirmation, journal |
| Valeur officielle fausse diffusée partout | Source obligatoire, tests chiffrés, publication par admin, version corrigée = nouvelle version |
| Écarts TR / classeur non arbitrés | Aucune version « officielle » publiée tant que les écarts de l'audit ne sont pas tranchés |
| Dérive par personnalisations | Surcharges identifiées, jamais d'édition d'un jeu publié |
| Logique nouvelle non prévue | Nouvelle variante de moteur codée + testée, jamais de formule saisie |
| Une version en casse une autre | Variantes versionnées (`_v1`, `_v2`), jamais modifiées après publication |

## 10. ALBARKA (existant)
Aucun changement tant que le propriétaire ne le demande pas. Migration en deux temps, plus tard :
1. figer l'existant (`DEFAULT_PARAMS` + `upgrade_params_lot26/27`) comme jeu « BF-2024-ALBARKA », avec un test prouvant que
   tous les bulletins existants donnent des montants **identiques** ;
2. seulement ensuite, ouvrir d'autres versions.

## 11. Questions ouvertes (à arbitrer avant le premier code)
1. Arbitrage des écarts de l'audit : plafonds CNSS (800 000 / 600 000), plafonds d'exonération, abattement forfaitaire,
   assiette du plafond 8 %, FSP, sursalaire. Source officielle en vigueur à fournir.
2. Une version par pays ou par pays + convention collective ?
3. Granularité de l'affectation : entreprise seule, ou aussi établissement / catégorie de personnel ?
4. Qui peut publier une version officielle (propriétaire seul, ou administrateur de plateforme) ?
5. Langue des termes : français seul, ou prévoir d'autres langues dès le départ ?

## 12. Étapes proposées (quand le propriétaire dit « go »)
1. Arbitrage des écarts + textes officiels. 2. Modèle de données et résolution (module pur, testé).
3. Variantes de moteur reprenant la logique d'ALBARKA, avec cas chiffrés. 4. Écran de paramétrage + aperçu avant/après.
5. Bulletins figés et rectificatifs. 6. Migration d'ALBARKA (sur demande explicite).
