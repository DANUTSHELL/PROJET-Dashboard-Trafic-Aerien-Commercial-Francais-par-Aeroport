# Contribuer

Règles communes aux trois auteurs. En cas de doute, `CLAUDE.md` fait foi.

## Mainteneurs

[@DANUTSHELL](https://github.com/DANUTSHELL) et [@thibault-dlb](https://github.com/thibault-dlb). Eux seuls fusionnent `develop` dans `main`.

## Branches

| Branche | Rôle |
| --- | --- |
| `main` | Version livrable. Reçoit uniquement `develop`, fusionnée par un mainteneur. |
| `develop` | Intégration. Reçoit uniquement des pull requests relues, jamais de push direct. |
| `feat/<nom>` | Nouvelle fonctionnalité (table, package SSIS, mesure, visuel). |
| `fix/<nom>` | Correction. |
| `docs/<nom>` | Documentation. |
| `data/<nom>` | Données de référence (`data/ref/`) ou dictionnaire des variables. |
| `chore/<nom>` | Configuration, outillage, organisation du dépôt. |

Toutes les branches de travail partent d'un `develop` à jour. Noms en anglais, en minuscules, avec des tirets : `feat/dim-airport`, `fix/apt-double-count`.

## Déroulé d'une modification

```bash
git switch develop
git pull
git switch -c feat/dim-airport
# travail, commits
git push -u origin feat/dim-airport
```

Puis ouvrir une pull request vers `develop` sur GitHub et la faire relire par un autre auteur. Une pull request ne contient qu'un seul sujet.

## Messages de commit

Format [Conventional Commits](https://www.conventionalcommits.org/fr/), en français, à l'impératif :

```
type(portée): description courte
```

La portée est facultative. Exemples : `feat(ssis): ajouter le chargement brut de ASP_APT`, `fix(sql): corriger la clé de d_compagnie`, `docs: compléter le dictionnaire des variables`.

| Type | Usage |
| --- | --- |
| `feat` | Nouvelle fonctionnalité |
| `fix` | Correction |
| `docs` | Documentation |
| `data` | Données de référence ou dictionnaire |
| `test` | Requêtes ou scripts de contrôle |
| `refactor` | Réorganisation sans changement de résultat |
| `build` | Projets Visual Studio (SSIS, SSAS), déploiement |
| `ci` | Intégration continue |
| `chore` | Configuration, outillage |

Portées usuelles : `sql`, `ssis`, `ssas`, `powerbi`, `data`, `notebooks`, `docs`.

Le hook `.githooks/commit-msg` refuse les messages hors format et toute ligne `Co-Authored-By`. Pour l'activer après un clone : `git config core.hooksPath .githooks`.

## Nommage

- Fichiers et dossiers : minuscules, sans espace ni accent, mots séparés par `_` (`creation_base.sql`). Les scripts à exécuter dans un ordre précis sont numérotés : `01_creation_base.sql`, `02_tables_brutes.sql`.
- Tables et colonnes en français : faits `f_*`, dimensions `d_*`, tables brutes `raw_*`.
- Clés de substitution numériques sans signification, nommées `id_<dimension>` (`id_aeroport`).
- Packages SSIS : `<ordre>_<action>_<cible>.dtsx` (`01_charger_raw_apt.dtsx`).

## Contrôles (tests)

Il n'y a pas de tests automatisés au sens logiciel : la chaîne SQL Server, SSIS et SSAS tourne en local. Les contrôles sont des requêtes SQL rangées dans `sql/controles/`, nommées `ctl_<objet>.sql`, et lancées avant chaque pull request qui touche aux données.

| Type de contrôle | Ce qu'il vérifie |
| --- | --- |
| Volumétrie | Le nombre de lignes chargées est égal à celui du CSV source. |
| Unicité | Chaque clé de substitution et chaque clé métier est unique dans sa dimension. |
| Intégrité | Aucune ligne de fait ne pointe vers une dimension absente (pas d'orphelin). |
| Réconciliation | Les totaux de l'entrepôt et du cube égalent les valeurs de contrôle de `docs/feuille-de-route.md`. |
| Règles métier | Chaque règle (doublon domestique APT, lignes `ZZMT`/`ZZOM`, entités easyJet) a un cas qui la déclenche et un cas qui ne la déclenche pas. |

Les notebooks Python servent à l'exploration et ne font pas partie de la chaîne de production.

## Secrets

Aucun mot de passe, chaîne de connexion ni clé dans Git. Ils vont dans un fichier `.env` ou dans la configuration locale, exclus par `.gitignore`.

## Versions

Une version livrée est un commit de `main` marqué d'un tag : `v1.0.0` pour la soutenance.

```bash
git tag -a v1.0.0 -m "Version de soutenance"
git push origin v1.0.0
```
