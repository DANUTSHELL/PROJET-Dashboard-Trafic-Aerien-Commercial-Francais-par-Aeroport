# CLAUDE.md

Consignes pour Claude Code sur ce dépôt.

## Projet

Tableau de bord Power BI sur le trafic aérien commercial français par aéroport et par liaison depuis 1990 (données DGAC, data.gouv.fr). Projet pédagogique à deux auteurs : @DANUTSHELL et @jules-gillet. Le détail (problématique, cas d'usage, modèle en étoile, points de vigilance) est dans `README.md`.

État actuel : seule la documentation existe. L'arborescence prévue (`data/raw`, `data/clean`, `notebooks/`, `dashboard/`, `docs/`) n'est pas encore créée.

## Règles de commit

- **Interdit : aucune ligne `Co-Authored-By:`** dans les messages de commit, quelle qu'elle soit. Claude ne doit jamais s'ajouter comme co-auteur ni apparaître dans les contributeurs du dépôt.
- Pas de mention « Generated with Claude Code » dans les commits ni dans les descriptions de pull request.
- Le hook `.githooks/commit-msg` rejette tout commit contenant `Co-Authored-By`. Pour l'activer après un clone : `git config core.hooksPath .githooks`.
- Messages de commit en français, au format `type: description` (`docs:`, `feat:`, `fix:`, `data:`…).

## Conventions

- Documentation en français ; toute modification de `README.md` doit être répercutée dans `README.en.md`.
- `data/raw/` contient les CSV DGAC bruts, jamais modifiés. Les transformations produisent des fichiers dans `data/clean/`.
- Préparation des données : Python (pandas) ou Power Query. Tableau de bord : Power BI Desktop (`.pbix` dans `dashboard/`).

## Points de vigilance sur les données

- Vérifier la granularité réelle du CSV (mois × paire d'aéroports × sens) avant de bâtir le modèle.
- Seuil de publication à 1 UDT : l'absence d'un petit aéroport n'est pas un déclin.
- Ruptures de série sur 35 ans (codes, périmètres, nomenclatures).
- Données par sens : dédoublonner avant d'agréger au niveau aéroport.
