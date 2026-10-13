# CLAUDE.md

Consignes pour Claude Code sur ce dépôt.

## Projet

Projet BI (IPSA 3IA, 2025-2026) sur le trafic aérien commercial français depuis 1990 (données DGAC, data.gouv.fr) : entrepôt SQL Server, alimentation SSIS, modèle SSAS, tableau de bord Power BI. Projet pédagogique à trois auteurs : @DANUTSHELL, @jules-gillet et @thibault-dlb. Le détail (problématique, cinq cas d'usage, séries retenues, modèle, matrice de bus, grille de décision, limites) est dans `README.md`.

État actuel : seule la documentation existe, avec le dossier `docs/`. Les autres dossiers prévus (`data/raw`, `data/ref`, `sql/`, `ssis/`, `ssas/`, `notebooks/`, `dashboard/`) ne sont pas encore créés.

Contraintes du cours : stack SQL Server / SSIS / SSAS / Power BI imposée ; Power BI doit interroger le modèle en étoile ; au moins quatre cas d'usage ; livrables = support de soutenance PowerPoint et fichier `.pbix` (pas de vidéo). Barème : présentation 5, modélisation 6, nettoyage et enrichissement 4, restitution 5.

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
