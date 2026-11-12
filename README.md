# metaltura/application

Le métamodèle d'application et ce que toutes les piles partagent : les patrons qui écrivent une application depuis un domaine, et les patrons de génération communs. Les dépôts d'une pile spécialisent ce métamodèle :

- `node` ;
- `react` ;
- `spring` ;
- `angular`.

Les dépôts d'une pile complète emploient ses patrons :

- `node-react` ;
- `spring-angular` ;
- `platform`.

## Ce qu'il contient

Il y a deux sortes de patrons :

- ceux qui écrivent une application à partir d'un modèle de domaine (`DomainTo…`) ;
- ceux qui écrivent le code d'une application, fichier par fichier, dans un dossier système (`…ToCode`).

Un patron est une classe qui étend `Mapping:Mapper` et porte son code dans sa fonction `map`. Chaque patron a une instance prête à l'emploi du même nom en minuscule (`entityToTable`). Le patron de tête d'une pile est celui qu'on branche par un Mapping : il appelle les autres.

- `models/Application.yaml` — le métamodèle d'application (PLAT-77) : un tronc abstrait (app · data) et des spécialisations par technologie de stockage (sql · mongo) · l'API HTTP (http) · l'interface (ui) · et la partie commune aux cadres d'interface web à page unique (spa · PLAT-308) : une interface de cadre (spa.Frontend) est une interface de ui qui porte en plus ses composants · ses routes et ses services. Un composant ne porte que ce qui change d'une application à l'autre et contient ses enfants : le cadre commun (Layout) · la barre de navigation (Navbar · un titre et une couleur) · le composant de page (PageComponent) · le tableau (Table) · la fiche (Detail) · le formulaire (Form). Une route (spa.Route) mène à un composant de page · un service (spa.ApiService) appelle une ressource de l'API. Ce paquet est abstrait : React et Angular le spécialisent. Un champ de page qui désigne un élément d'une autre ressource (le `clientId` d'une commande) nomme cette ressource par `resource` (ui.Field · PLAT-81) : un générateur peut en faire une liste de choix. Chacun de ses sept paquets (`app`, `data`, `sql`, `mongo`, `http`, `ui`, `spa`) porte son diagramme de classes. Une application désigne ses interfaces sans les contenir (`frontends`) : une interface est rangée à part et désigne son application par `application`.
- `models/DomainToApplication.yaml` — les patrons communs du domaine vers l'application (PLAT-79), en quatre paquets :
  - `app` : les bases communes.
    - `DomainPattern`, la base de tous les patrons, porte les fonctions utilitaires : les entités d'un modèle, les champs hérités, le nom d'un champ de référence, et le nom de l'application qu'un domaine donne (`applicationNameOf` : le nom du domaine sans le suffixe `Domain`, `BoutiqueDomain` donne `Boutique`).
    - `DomainToStack`, la base des patrons de tête, porte l'option `audit`.
    - `StackApplication` produit l'Application d'une pile sans connaître aucune pile. Chaque instance, rangée dans les patrons de sa pile, lui donne ce qui est propre à la pile : la classe de la base (`database`), le patron de l'enregistrement d'une entité (`record`), celui des tables de jointure (`joinTables`), la classe de l'interface (`frontend`), le patron de son service (`service`), et le paquet où le patron de tête range l'interface (`frontendPackage`).
  - `http` : l'API, une ressource par entité.
  - `ui` : les pages et le menu. Le patron de tête pose sur chaque champ de relation d'une page la ressource qu'il désigne (`resource`).
  - `spa` : le cadre, les composants de page et les routes.

  Son diagramme `PatternsClassDiagram` montre la hiérarchie de `Mapping:Mapper` aux bases et les patrons de ces quatre paquets.
- `models/ApplicationToCode.yaml` — les patrons de génération communs (PLAT-80, PLAT-81, PLAT-82), dans le paquet `code` :
  - `CodePattern`, la base de tous les patrons de génération, porte les fonctions partagées : les formes d'un nom (`pascalOf` · `camelOf` · `kebabOf`), le nom encodé d'un fichier (`Commande__java`), les types Java, Mongoose et TypeScript d'un champ, l'opération d'un point d'entrée, le chemin d'une ressource, le client d'API d'une interface pour une ressource et le champ qui nomme un élément dans une liste de choix.
  - `resourceToPayloadTypes` écrit les types des charges utiles d'une ressource : les interfaces React et Angular l'empruntent toutes deux.

  Un patron de génération écrit dans un dossier système du modèle FileSystem, que la plateforme écrit sur disque sous `code/<rootPath>/`. Son diagramme `CodePatternsClassDiagram` montre `CodePattern` et `resourceToPayloadTypes`.

## Ce dont il dépend

| Dépôt | Ce qu'il en emploie | Ligne `depends` |
| --- | --- | --- |
| `metaltura/domain` | `Domain`, que lit `DomainToApplication` | `metaltura/domain/Domain@v2.0.0` |

Ces modèles système de Metaltura s'écrivent par leur nom seul :

- `Mapping` ;
- `FileSystem` ;
- `Diagram` ;
- `Uml`.

Un modèle de ce dépôt nomme un autre modèle du dépôt par son nom seul (`ApplicationToCode` nomme `Application`).

## L'utiliser depuis un autre dépôt

Écrire dans la liste `depends` du modèle la ligne de chaque modèle employé :

```yaml
depends:
  - metaltura/application/Application@v2.0.0
  - metaltura/application/DomainToApplication@v2.0.0
  - metaltura/application/ApplicationToCode@v2.0.0
```

Metaltura clone ce dépôt à l'étiquette `v2.0.0`, puis `metaltura/domain` dont il dépend. Toute la librairie est à `v2.0.0` : Metaltura refuse qu'un même dépôt soit demandé à deux versions.

## D'où il vient

Ce dépôt vient du dépôt privé `Kogitec/simple-mda`, `main` à `96a1a75` (PLAT-1461), rangé par sujet par PLAT-1498 en un seul commit : ses modèles étaient sous `models/applications/common/`. L'historique de ses fichiers reste dans `simple-mda`, archivé.
