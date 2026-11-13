# Consignes du dépôt

Consignes pour l'assistant de code utilisé sur ce dépôt.

## Projet

Projet BI (IPSA 3IA, 2025-2026) sur le trafic aérien commercial français depuis 1990 (données DGAC, data.gouv.fr) : entrepôt SQL Server, alimentation SSIS, modèle SSAS, tableau de bord Power BI. Projet pédagogique à trois auteurs : @DANUTSHELL, @jules-gillet et @thibault-dlb. Le détail (problématique, cinq cas d'usage, séries retenues, modèle, matrice de bus, grille de décision, limites) est dans `README.md`.

État actuel : seule la documentation existe, avec le dossier `docs/`. Les autres dossiers prévus (`data/raw`, `data/ref`, `sql/`, `ssis/`, `ssas/`, `notebooks/`, `dashboard/`) ne sont pas encore créés.

Contraintes du cours (supports dans `C:\Users\thibd\OneDrive\Cours\B3\Systèmes d'aide à la décision + projet`, grille détaillée dans `Projet/notation_projet.pdf`) :

- Chaîne imposée : CSV bruts chargés par SSIS dans SQL Server, tables de dimensions et de faits créées dans SSMS, packages SSIS déployés dans SSMS, cube OLAP SSAS (projet multidimensionnel) construit sur l'étoile, Power BI connecté au cube SSAS. Toute la chaîne tourne en local (SQL Server sur chaque poste) et la démonstration se fait en local, sans publication sur le serveur de l'école : consigne de l'enseignant, qui prime sur le PowerPoint du cours.
- Au moins quatre cas d'usage, chacun avec des visuels.
- Vocabulaire du cours : « matrice de bus décisionnel » (processus métiers en lignes, dimensions en colonnes), « grille de priorisation » (valeur métier × facilité), clés de substitution numériques sans signification, pseudo-entités, dimensions à évolution lente.
- Livrables : support de soutenance PowerPoint (avec membres et répartition du travail) et fichier `.pbix` ; pas de vidéo.
- Notation sur 20 : présentation 5, base SQL Server et SSIS 5, étoile et cube SSAS 5, tableaux de bord Power BI 5.

## Répartition et rôle de l'assistant

Chaque membre travaille avec son propre assistant de code sur sa propre série. Au début de chaque session, identifie l'utilisateur avec `git config user.name` et applique la ligne qui le concerne. Si le nom ne correspond à personne, demande-lui qui il est avant de modifier quoi que ce soit.

| `git config user.name` | Membre | Série | Fichiers dont il est responsable |
|---|---|---|---|
| `DANUTSHELL` | @DANUTSHELL | Aéroports (APT), cas 1, 2, 3 | suffixe `_apt`, `ref_aeroport.csv`, `d_temps`, `d_aeroport`, `f_trafic_aeroport` |
| `Jules GILLET` | @jules-gillet | Liaisons (LSN), cas 4, page d'accueil, charte | suffixe `_lsn`, `ref_pays.csv`, `ref_lignes_tgv.csv`, `d_faisceau`, `d_pays`, `f_trafic_liaison` |
| `Thibault Tripier de Laubrière` | @thibault-dlb | Compagnies (CIE), cas 5, assemblage final | suffixe `_cie`, `ref_compagnie.csv`, `d_compagnie`, `f_trafic_compagnie`, cube et `.pbix` finaux |

Le tableau complet et les règles d'indépendance sont dans `docs/feuille-de-route.md`, section 0.2.

Consignes pour aider le membre identifié :

- **Rester dans sa série.** Propose et réalise les tâches de sa colonne dans la feuille de route, dans l'ordre des phases, et coche les cases quand le critère de validation est atteint. Pour savoir où il en est, lis la feuille de route et `git log`.
- **Ne pas toucher aux fichiers d'un autre membre** ni au socle commun (scripts de création des tables partagées). Si un changement y est nécessaire, arrête-toi, explique pourquoi et propose au membre de prévenir le responsable ; la modification passe alors par une pull request relue par ce responsable.
- **Ne jamais attendre un autre membre.** Si une référence manque (par exemple `d_pays` pour CIE), utilise le membre « Inconnu » de clé `-1` et note le rechargement à faire.
- **Avant chaque modification** : `git switch develop`, `git pull`, puis une branche `feat/`, `fix/`, `docs/`, `data/` ou `chore/` nommée en anglais avec la série (`feat/fact-table-cie`). Jamais de commit sur `main` ni sur `develop`.
- **Commits** : un sujet par commit, en français, au format `type(portée): description` (`feat(ssis): ajouter le chargement brut de ASP_CIE`), sans emoji, sans ligne `Co-Authored-By`, sans mention d'un assistant ni d'une IA. Vérifie que le hook est actif : `git config core.hooksPath` doit renvoyer `.githooks`.
- **Avant la pull request** : lancer les contrôles de la série (`sql/controles/ctl_*_<serie>.sql`) et comparer aux valeurs de référence de la feuille de route. Pousser la branche et donner le lien pour ouvrir la pull request vers `develop`, jamais vers `main`.
- **Faire comprendre, pas seulement faire.** Le membre devra présenter et justifier son travail à la soutenance : explique chaque choix (grain, clés, pseudo-membres, mesures) avec le vocabulaire du cours, et laisse-le exécuter lui-même dans SSMS, Visual Studio et Power BI ce qui ne se fait pas en ligne de commande.


- **Interdit : aucune ligne `Co-Authored-By:`** dans les messages de commit, quelle qu'elle soit. L'assistant ne doit jamais s'ajouter comme co-auteur ni apparaître dans les contributeurs du dépôt.
- Pas de ligne de signature automatique d'un assistant dans les commits ni dans les descriptions de pull request.
- Le hook `.githooks/commit-msg` rejette tout commit contenant `Co-Authored-By`. Pour l'activer après un clone : `git config core.hooksPath .githooks`.
- Messages de commit en français, au format `type(portée): description`, portée facultative (`docs:`, `feat(ssis):`, `fix(sql):`, `data:`…). Le hook refuse les messages hors format.
- Branches, pull requests, nommage, contrôles et versions : voir `CONTRIBUTING.md`. Ne jamais commiter directement sur `main` ni sur `develop`.

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
