# GRH — Burkina Faso : règle permanente et référence (15/10/2026)

**Source** : document « Textes réglementaires GRH » (extrait du cours « Les charges de personnel ») transmis par le
propriétaire le 15/10/2026. C'est un support pédagogique : il **ne cite aucun numéro d'article** et son barème IUTS est
daté « à partir du 1er octobre 2013 ». Il sert de **référence de départ**, pas de texte de loi définitif.

## 1. La règle (à appliquer à toute implémentation GRH, actuelle ou future)

Périmètre : paie, bulletins, livre de paie, états de salaire, charges patronales, heures supplémentaires, avantages en
nature, grille salariale, fiches du personnel — dans ALBARKA, SAWALI, sawali-GRH et tout autre projet.

1. **Lire ce fichier avant de coder** tout ce qui touche à la GRH.
2. **Ne jamais modifier un calcul existant** (indemnités, IUTS, CNSS, charges patronales…) sans demande explicite du
   propriétaire. Un écart entre le texte réglementaire (TR) et l'existant se **signale** dans un tableau
   *Existant / TR / À faire / Motif* ; c'est le propriétaire qui arbitre.
3. **Ordre des sources** : textes officiels en vigueur (CGI, décrets CNSS, Code du travail, conventions collectives)
   > ce TR > classeurs du cabinet. Quand TR et classeur divergent : « **à arbitrer** », jamais tranché seul.
   Ne jamais inventer un numéro d'article ou une valeur : demander le texte.
4. **Aucun taux, plafond, tranche ni barème en dur** : tout est paramètre **versionné** (date d'effet + source
   écrite, ex. « TR 15/10/2026 », « classeur ALBARKA 08/2024 »). Un bulletin **validé** n'est jamais recalculé.
5. **Contenu minimal du bulletin** (modèle du TR) : employeur ; « BULLETIN DE PAIE n° … » et mois ; nom, prénoms,
   situation matrimoniale, n° CNSS, profession/fonction, période ; salaire de base ; heures supplémentaires ;
   indemnités (logement, fonction, transport) ; primes ; remboursement de frais ; autres ; **Total brut (A)** ;
   IUTS ; cotisation CNSS ; avances ; oppositions ; autres ; **Total retenues (B)** ; **Net à payer (A − B)** ;
   signature et cachet de l'employeur ; mention de conservation « sans limitation de durée ».
6. **Multi-employeurs** (sawali-GRH) : chaque entreprise / établissement a ses paramètres, son en-tête complet (adresse,
   BP, tél., e-mail, IFU, n° employeur CNSS) et ses salariés ; l'IFU / le n° CNSS d'un employeur ne s'imprime jamais
   sur le bulletin d'un autre.
7. **Écart de valeur → test** : toute valeur du TR reprise dans le code a un test qui la fixe (cas chiffré à la main).

## 2. Référence : ce que dit le TR

### Rémunération
- **Salaire de base (Sb)** = salaire catégoriel (contrat), **hors** indemnités, primes, gratifications.
  Sb = taux horaire × **173,33 h** (40 h × 52 semaines / 12). Durée légale : 40 h/semaine, répartition libre.
- **Rémunération totale** : RT = Sb + HS + P + I + AN.
- **Heures supplémentaires** (au-delà de 40 h/semaine, 8 h/jour) — majorations :

  | Heures | Jour | Nuit |
  |---|---|---|
  | 41e à 48e heure | 15 % | 50 % |
  | 49e heure et plus | 35 % | 50 % |
  | Dimanches et jours fériés | 60 % | 120 % |

- **Primes** : gratifications (ancienneté, rendement, productivité, fin d'année, danger, 13e mois, vacances,
  mariage…) = élément de salaire ; primes de **remboursement de frais** = pas de nature de salaire.
- **Indemnités** : compensation du coût de la vie ou remboursement d'une dépense professionnelle (fonction, caisse,
  logement, transport…).
- **Avantages en nature (AN)** : évaluation souvent forfaitaire ; immobilisation (ex. véhicule) = **1/240** de la
  valeur d'origine (5 % × 1/12) ; employé de maison = Sb + CNSS + charges patronales 16 % + TPA 3 %.
- Appellations : *salaire* (ouvriers), *appointements* (employés, cadres), *commissions* (représentants),
  *honoraires* (exploitants individuels), *traitements / soldes* (fonctionnaires, corps habillés).

### Retenues
- **CNSS salariale** : 5,5 % de (RT − AN), assiette plafonnée à **600 000 F/mois** ; ne doit pas excéder 8 % de Sb.
- **Retenues particulières** : avances et acomptes ; oppositions / saisies-arrêts (épouses séparées ou divorcées,
  créanciers).
- **Remboursement de frais** : sur pièces justificatives, peut figurer sur le bulletin.

### IUTS
- Salaire brut (SB) = RT − CNSS ; **base imposable** = SB − exonérations − abattement forfaitaire, **arrondie à la
  centaine inférieure**.
- **Exonérations** : logement 20 % du brut, max **50 000** F ; transport 5 %, max **20 000** F ; fonction 5 %, max
  **30 000** F ; pensions militaires ou civiles ; indemnités parlementaires ; majorations de solde pour charges de
  famille (allocations familiales) ; indemnités de licenciement au sens strict (hors préavis).
- **Abattement forfaitaire** : **15 % de Sb** (frais et charges professionnelles).
- **Barème** (effet 1er octobre 2013) : 0–30 000 : 0 % · 30 100–50 000 : 12,1 % · 50 100–80 000 : 13,9 % ·
  80 100–120 000 : 15,7 % · 120 100–170 000 : 18,4 % · 170 100–250 000 : 21,7 % · au-delà de 250 100 : 25 %.
- **Charges de famille** (abattement sur l'IUTS brut) : 1 → 8 % · 2 → 10 % · 3 → 12 % · 4 → 14 % · 5 → 16 % ·
  6 → 18 % · 7 et plus → 20 %.

### Charges patronales
- **CNSS employeur 16 %** de (RT − AN), plafond **600 000** F : vieillesse 5,5 % + risques professionnels 3,5 % +
  prestations familiales 7 %.
- **TPA 3 %** (nationaux et étrangers) sur (RT − AN).

### Documents
- Le **bulletin de paie** est obligatoire ; le salarié le conserve sans limitation de durée.
- Le **livre de paie** regroupe les bulletins.

## 3. Points du TR à clarifier avant d'en faire une règle de calcul
- Plafonds et taux **datés** (barème 2013) : plafond CNSS 600 000, plafonds d'exonération, abattement 15 % — le
  classeur d'ALBARKA (août 2024) en retient d'autres. À arbitrer avec le texte officiel en vigueur.
- Plafond « 8 % de Sb » : porte-t-il sur la retenue ou seulement sur la CNSS déductible ? Sur Sb seul ?
- La « prime de transport » est citée comme remboursement de frais (non salaire) ET l'indemnité de transport figure
  parmi les exonérations plafonnées : le TR se contredit.
- Vocabulaire : le TR appelle « salaire brut » RT − CNSS, alors que le bulletin affiche « Total brut » = RT.

## 4. Mise à jour du 15/10/2026 — FSP (prélèvement pour effort de paix)
- **Confirmé par le propriétaire** : taxe instaurée depuis 2023, obligatoire pour toute entreprise (elle s'applique aussi
  aux transactions courantes : recharges téléphoniques, paiements…). Le TR (Word, antérieur) n'en parle pas : son silence
  ne signifie pas absence d'obligation.
- ALBARKA applique 1 % du salaire net. **Restent à vérifier dans le texte officiel** : le taux exact, l'assiette (net,
  brut ou autre), les exclusions éventuelles et l'ordre dans le bulletin (avant ou après les retenues). Ne pas modifier
  le calcul existant avant cette vérification et l'accord du propriétaire.
- Dans le moteur versionné : le FSP est une valeur du jeu de règles (taux, assiette, date d'effet = 2023), jamais en dur.
