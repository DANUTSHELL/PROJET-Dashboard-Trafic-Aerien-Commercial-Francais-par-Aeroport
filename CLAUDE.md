# CLAUDE.md

Consignes pour Claude Code sur ce dépôt.

## Projet

Projet BI (IPSA 3IA, 2025-2026) sur le trafic aérien commercial français depuis 1990 (données DGAC, data.gouv.fr) : entrepôt SQL Server, alimentation SSIS, modèle SSAS, tableau de bord Power BI. Projet pédagogique à trois auteurs : @DANUTSHELL, @jules-gillet et @thibault-dlb. Le détail (problématique, cinq cas d'usage, séries retenues, modèle, matrice de bus, grille de décision, limites) est dans `README.md`.

État actuel : seule la documentation existe, avec le dossier `docs/`. Les autres dossiers prévus (`data/raw`, `data/ref`, `sql/`, `ssis/`, `ssas/`, `notebooks/`, `dashboard/`) ne sont pas encore créés.

Contraintes du cours (supports dans `C:\Users\thibd\OneDrive\Cours\B3\Systèmes d'aide à la décision + projet`, grille détaillée dans `Projet/notation_projet.pdf`) :

- Chaîne imposée : CSV bruts chargés par SSIS dans SQL Server, tables de dimensions et de faits créées dans SSMS, packages SSIS déployés dans SSMS, cube OLAP SSAS (projet multidimensionnel) construit sur l'étoile, Power BI connecté au cube SSAS et publié sur le serveur de l'école.
- Au moins quatre cas d'usage, chacun avec des visuels.
- Vocabulaire du cours : « matrice de bus décisionnel » (processus métiers en lignes, dimensions en colonnes), « grille de priorisation » (valeur métier × facilité), clés de substitution numériques sans signification, pseudo-entités, dimensions à évolution lente.
- Livrables : support de soutenance PowerPoint (avec membres et répartition du travail) et fichier `.pbix` ; pas de vidéo.
- Notation sur 20 : présentation 5, base SQL Server et SSIS 5, étoile et cube SSAS 5, tableaux de bord Power BI 5.

## Règles de commit

- **Interdit : aucune ligne `Co-Authored-By:`** dans les messages de commit, quelle qu'elle soit. Claude ne doit jamais s'ajouter comme co-auteur ni apparaître dans les contributeurs du dépôt.
- Pas de mention « Generated with Claude Code » dans les commits ni dans les descriptions de pull request.
- Le hook `.githooks/commit-msg` rejette tout commit contenant `Co-Authored-By`. Pour l'activer après un clone : `git config core.hooksPath .githooks`.
- Messages de commit en français, au format `type: description` (`docs:`, `feat:`, `fix:`, `data:`…).

## Conventions

- **Emojis interdits** partout : documentation, code, commentaires, messages de commit, descriptions de PR, et réponses dans le terminal.
- Documentation en français ; toute modification de `README.md` doit être répercutée dans `README.en.md`.
- `data/raw/` contient les CSV DGAC bruts, jamais modifiés. Les tables d'enrichissement sont dans `data/ref/`.
- Noms de tables et de colonnes en français : faits `f_*`, dimensions `d_*`.
- Python (pandas) sert à l'exploration ; la chaîne de production passe par SSIS.

## Points de vigilance sur les données

- Séries retenues : `ASP_APT` (aéroports), `ASP_LSN` (faisceaux et pays), `ASP_CIE` (compagnies, depuis 2010). `ASP_PAP` est écartée (environ 40 % du trafic, filtrée par secret statistique).
- CSV : séparateur `;`, décimales à la virgule, UTF-8 avec BOM.
- Seuils de publication : un aéroport, une liaison ou une compagnie absent n'est pas forcément fermé ou en déclin.
- APT compte le domestique deux fois (départ et arrivée) : ne jamais additionner APT avec LSN ou CIE.
- Lignes regroupées à traiter à part : `ZZMT`, `ZZOM`, « AUTRES CIE », pays `*_AUTRE`.
- Entités multiples d'une même compagnie (easyJet UK, Europe, Switzerland) à regrouper.
