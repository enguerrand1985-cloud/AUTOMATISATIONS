# REGLE__AUTOMATISATIONS_SYNC

## 1. Objet
Cette regle formalise la synchronisation entre :
- le runtime local des automatisations dans `C:\Users\engue\.codex\automations`
- et leur miroir versionne dans `C:\Users\engue\__SYSTEME - GITHUB__\AUTOMATISATIONS`

Le depot GitHub `AUTOMATISATIONS` sert a :
- versionner les definitions declaratives
- les rendre lisibles et modifiables depuis le perimetre GPT
- garder un historique des changements

Il ne remplace pas le moteur d execution local.

## 2. Statut du composant
- `C:\Users\engue\.codex\automations` = `local-active`
- `C:\Users\engue\__SYSTEME - GITHUB__\AUTOMATISATIONS` = `github-mirror`

Sens retenu par defaut :
- la verite d execution reste locale
- GitHub recoit un miroir versionne des definitions utiles

## 3. Artefacts synchronisables
Peuvent etre repercutes dans `AUTOMATISATIONS` :
- `automation.toml`
- un index canonique des automatisations connues
- une regle de synchronisation
- une documentation courte par automation si utile

Ne doivent pas etre versionnes :
- `memory.md`
- etats de run
- horodatages runtime calcules par le moteur
- journaux temporaires
- caches
- bases SQLite
- inbox state
- tout secret ou element machine-specifique

## 4. Structure cible
Format recommande :
- `C:\Users\engue\__SYSTEME - GITHUB__\AUTOMATISATIONS\<automation_id>\automation.toml`
- `C:\Users\engue\__SYSTEME - GITHUB__\AUTOMATISATIONS\INDEX__AUTOMATISATIONS.json`
- `C:\Users\engue\__SYSTEME - GITHUB__\AUTOMATISATIONS\REGLE__AUTOMATISATIONS_SYNC.md`

## 5. Regle de repercussion
Apres toute creation, suppression, activation, pause ou modification structurelle d une automation locale :
- mettre a jour le miroir GitHub correspondant
- realigner `INDEX__AUTOMATISATIONS.json`
- ne jamais envoyer `memory.md`

Types de changements a repercuter :
- `name`
- `prompt`
- `status`
- `rrule`
- `model`
- `reasoning_effort`
- `execution_environment`
- `cwds`
- ajout ou suppression d une automation

## 6. Politique de derive
Cas toleres :
- `local-ahead` : automation locale modifiee, miroir GitHub pas encore mis a jour
- `github-ahead` : changement de definition prepare dans GitHub mais pas encore reapplique localement

Cas interdit sans arbitrage :
- `conflict-both-sides`

Regle :
- ne jamais ecraser silencieusement des changements concurrents
- STOP et arbitrage humain si les deux cotes ont bouge

## 7. Politique de retour GitHub vers local
GitHub peut servir de couche de modification et de gouvernance.

Quand une definition change cote GitHub :
- la modification reste declarative
- elle doit etre reappliquee explicitement dans `C:\Users\engue\.codex\automations`
- la bascule locale ne doit pas etre implicite ni destructive

## 8. Niveaux d automatisation cibles
Niveau par defaut :
- `prepare-sync`

Lecture pratique :
- detection de derive autorisee
- preparation de diff ou commit autorisee
- aucune ecriture destructive automatique sur le runtime local
- aucun push automatique sans validation

## 9. Exclusions obligatoires
Ne jamais versionner dans `AUTOMATISATIONS` :
- secrets
- tokens
- cookies
- sessions
- etats runtime temporaires
- sorties de run
- artefacts machine-specifiques

## 10. Condition de stop
STOP si :
- une automation locale n a pas de `automation.toml`
- le miroir GitHub et le runtime local ont diverge des deux cotes
- une automation expose un secret ou une donnee sensible dans son prompt ou ses metadonnees
- une ecriture locale devrait modifier un etat runtime non declaratif

