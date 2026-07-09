# Domain Driven Design

## Concepts de base

Métier > Modèle > Code

On part du métier, puis on passe au modèle, pour finir avec le code (en utilisant les design patterns et en apliquant la conception en cascade ou au contraire des méthodes agiles).

### Définitions

> Le DDD (Domain-Driven Design) est une approche de conception qui consiste à construire le logiciel autour du métier réel, de ses règles, de son vocabulaire et de ses processus. L’objectif est que le code reflète clairement les concepts métier importants, afin de rendre l’application plus compréhensible, maintenable et évolutive.

* **Le métier** (domaine) est le domaine réel que l’application doit gérer, avec ses règles, ses contraintes, ses acteurs et son vocabulaire. Par exemple, pour Tubert : les clients, les sites, les interventions, les techniciens, les bons d’intervention, la facturation, etc.
* **Le modèle** est la représentation simplifiée de ce métier dans le logiciel. Il traduit les concepts importants du métier en objets, entités, règles et relations compréhensibles par le code.
* **Les design patterns** sont des solutions de conception réutilisables pour répondre à des problèmes fréquents d’architecture ou d’organisation du code. Ils ne sont pas du code prêt à copier, mais plutôt des modèles de solution qui aident à structurer le logiciel de façon plus claire, maintenable et évolutive.
* **Conception en cascade** : méthode de projet séquentielle où l’on avance étape par étape — analyse, conception, développement, test, livraison — avec peu de retours en arrière.
* **Développement agile** : méthode itérative où l’on construit le produit progressivement, par cycles courts, en intégrant régulièrement les retours utilisateurs.
* Un **use case **représente un cas d’utilisation concret de l’application, c’est-à-dire une action que le système doit permettre de réaliser pour répondre à un besoin utilisateur ou métier. _Exemple : créer une intervention, clôturer une intervention, générer une facture ou envoyer un rappel._
* 
### Bâtir la connaissance du domaine

* Échanger avec les utilisateurs métier : comprendre leur quotidien, leurs problèmes, leurs règles et leur vocabulaire.
* Observer les processus réels : voir comment le travail est fait aujourd’hui, pas seulement comment il est décrit théoriquement.
* Identifier les concepts clés : clients, interventions, techniciens, bons d’intervention, factures, statuts, validations, etc.
* Recenser les règles métier : ce qui est autorisé, interdit, obligatoire, exceptionnel ou conditionnel.
* Définir un vocabulaire commun : utiliser les mêmes mots dans les échanges, les documents, l’interface et le code.
* Modéliser progressivement : créer des schémas simples, diagrammes, workflows ou premiers modèles de données.
* Valider régulièrement avec le métier : vérifier que le modèle correspond bien à la réalité et l’ajuster au fil des retours.

### Le besoin d'un langage commun

Le besoin d’un langage commun est central dans une démarche DDD : il s’agit d’utiliser les mêmes termes côté métier, côté interface et côté code afin d’éviter les malentendus et de construire un logiciel fidèle à la réalité des utilisateurs. Ce vocabulaire partagé facilite les échanges, la conception, la maintenance et l’évolution de l’application.

> Bien que le comportement exprimé par une méthode soit clair, le nom de la méthode est-il aussi clair que son corps ?
Les assertions d’un test parlent d’elles-mêmes, mais
qu’en est-il du nom des variables et de la structure générale du code ? Donnent-ils
haut et fort une version intégrale des faits ? Du code qui fonctionnellement fait ce qu’il
y a à faire, n’exprime pas forcément ce qu’il y a à exprimer. Ecrire un modèle en code
est très difficile.


---

## Les blocs de construction d’une conception orientée Modèle

La programmation orientée objet convient bien à l’implémentation de modèle car ils
sont tous deux basés sur le même paradigme. La programmation orientée objet prévoit
des classes d’objets et leurs associations, des instances d’objets, et l’envoi de messages
entre eux. Les langages de POO rendent possible la création de mappages directs entre
les objets du modèle avec leurs relations, et leurs équivalents en programmation.

Les langages procéduraux offrent un support limité de la conception dirigée par le
Modèle. Ces langages ne proposent pas les briques nécessaires pour implémenter les
composants-clés d’un modèle.

### L’architecture en couches

> L’architecture en couches est une manière d’organiser une application en niveaux de responsabilité distincts — par exemple interface, application, domaine et infrastructure — afin de séparer clairement l’affichage, les cas d’usage, les règles métier et les détails techniques.

Chaque couche ne dépend que des couches en-dessous. Ainsi il est plus simple de gérer les changements.

Le code lié au modèle est concentré dans une couche isolée du l'UI, des tâches applicatives, du besoin de se stocker eux-mêmes...

Une solution architecturale courante pour les conceptions dirigées par le domaine
contient quatre couches conceptuelles :

* **Couche de présentation** (interface utilisateur) : présentation de l'information à l'utilisateur / interprétation des commandes utilisateurs
* **Couche application **: coordonne l'activité de l 'application (ne contient pas de logique métier, ne recèle pas l’étatdes objets métier, mais  peut détenir l’état de la progression d’une tâche applicative)
* **Couche domaine** : contient les informations sur le domaine, coeur du logiciel métier
* **Couche infrastructure** : agit comme une bibliothèque de soutien pour les autres couches : communication entre les couches, persistance des objets métiers, bibliothèque auxiliaires de la couche de présentation, ...

### Les Entités

> Les **Entités** sont des objets métiers identifiés par une identité unique et durable, dont l’état peut évoluer dans le temps, par exemple un `Client`, une `Intervention` ou un `Technicien`.

Il est important que deux objets avec des
identités différentes soient facilement distingués par le système, et que deux objets de
même identité soient considérés comme les mêmes. Si cette condition n’est pas
satisfaite, alors le système entier peut devenir corrompu.

L'identité unique de chaque objet peut être crée de différentes manières:

* clé primaire générée dans la base de donnée
* ID unique créé par l'
* Une autre solution est d’utiliser les attributs de
l’objet pour créer l’ID

### Les Objets-Valeurs

> Les Objets-Valeurs sont des objets métier définis uniquement par leurs valeurs, sans identité propre, généralement immuable, par exemple une `Adresse`, une `Période`, ou un `Montant`.

Il y a des cas où on a purement besoin de stocker des attributs d’un élément du
domaine. Ce qui nous intéresse n’est pas de savoir de quel objet il s’agit, mais quels
attributs il a. Un objet qui est utilisé pour décrire certains aspects d’un domaine et qui
n’a pas d’identité, est appelé Objet-Valeur.

Il est fortement recommandé que les objets-valeurs soient immuables. Ils sont créés à
l’aide d’un constructeur, et jamais modifiés au cours de leur vie. Lorsque vous voulez
une valeur différente pour l’objet, vous en créez tout simplement un autre. Etant immuables, ils sont facilement partageables.

Les Objets-Valeurs peuvent contenir d’autres Objets-Valeurs, ils peuvent même
contenir des références à des Entités.

### Les Services

> En DDD, un Service sert à représenter une action importante du métier qui ne appartient naturellement ni à une Entité ni à un Objet-Valeur.

Une Entité ou un Objet-Valeur doit contenir les comportements qui lui appartiennent vraiment. Mais certaines opérations concernent plusieurs objets à la fois, ou ne peuvent pas être rattachées clairement à un seul objet. Dans ce cas, les mettre dans une Entité rendrait le modèle confus et créerait trop de dépendances entre les objets.

_Exemple : transférer de l’argent d’un compte à un autre. Cette action ne semble appartenir ni uniquement au compte qui envoie, ni uniquement au compte qui reçoit. Elle peut donc être représentée par un Service._

Un Service de domaine doit généralement respecter trois idées :

* il représente une **opération importante du métier**
* cette opération **implique souvent plusieurs objets du domaine**
* le Service **ne possède pas d’état interne**

Il ne faut pas créer un Service pour chaque petite opération. Si une règle ou une action appartient clairement à une Entité ou à un Objet-Valeur, elle doit rester dans cet objet. Le Service est utile seulement quand l’opération est un vrai concept métier indépendant.

**Il faut aussi distinguer plusieurs types de services :**

* un **Service de domaine **contient une logique métier importante
* un **Service d’application **orchestre un cas d’usage
* un **Service d’infrastructure** gère des détails techniques comme la base de données, les fichiers ou les APIs externes

_Par exemple, dans une application de reporting, l’utilisateur choisit un rapport à générer. La couche application reçoit la demande, puis le domaine s’occupe de créer le rapport. Si le rapport doit être généré à partir d’un gabarit, on peut créer un Service de domaine chargé de retrouver le bon gabarit à partir de l’identifiant du rapport. Cette opération n'appartient ni vraiment à l’objet Rapport, ni vraiment à l’objet Gabarit : elle mérite donc d’être isolée dans un Service._

En résumé, un Service de domaine sert à placer une logique métier importante au bon endroit, quand cette logique ne correspond pas naturellement à la responsabilité d’une Entité ou d’un Objet-Valeur.

### Les Modules

> En DDD, les **modules** servent à organiser un modèle métier devenu trop grand ou trop complexe.

Au lieu d’essayer de comprendre toute l’application d’un seul bloc, on regroupe les concepts proches dans des modules cohérents. Cela permet de mieux comprendre le système, de réduire la complexité et de faciliter la maintenance.

Un bon module doit avoir une **forte cohésion** : les éléments qu’il contient doivent travailler ensemble autour d’un même concept ou d’une même tâche métier. _Par exemple, on pourrait avoir des modules comme **Interventions**, **Clients**, **Techniciens**, **Facturation** ou **Matériel**._

Il y a plusieurs types de cohésion. Deux des plus répandues sont la cohésion
communicationnelle et la cohésion fonctionnelle.
* La **cohésion communicationnelle** est effective lorsque des parties du module opèrent sur les mêmes données. Ca a du sens
de les regrouper, car il existe une forte relation entre elles.
* La **cohésion fonctionnelle** est atteinte quand toutes les parties du module travaillent de concert pour réaliser une
tâche bien définie. Elle est considérée comme la meilleure forme de cohésion.

Les modules doivent aussi être **faiblement couplés entre eux**. Cela signifie qu’un module ne doit pas dépendre trop fortement des détails internes des autres modules. Idéalement, chaque module expose une interface claire, et les autres parties de l’application passent par cette interface plutôt que d’accéder directement à ses objets internes.

Les noms des modules sont importants : ils doivent refléter le métier et faire partie du langage commun utilisé par les développeurs et les utilisateurs métier.

Enfin, les modules ne doivent pas être figés trop tôt. Leur découpage peut évoluer avec la compréhension du domaine. Si on se rend compte qu’un module est mal découpé, il vaut mieux le refactoriser proprement plutôt que d’ajouter des solutions bricolées.

En résumé, les modules servent à découper le domaine en parties cohérentes, compréhensibles et relativement indépendantes, afin de rendre l’application plus claire, maintenable et évolutive.


---

## Les patterns liés au cycle de vie des objets du domaine

> Gérer le cycle de vie d’un objet du domaine constitue un défi en soi, et si ce n’est pas
fait correctement, cela peut avoir un impact négatif sur le modèle du domaine. Nous
allons présenter trois patterns qui nous aident à régler cela.

* **Agrégat** est un pattern de domaine utilisé pour définir l’appartenance et les frontières des objets.
* Les **Fabriques** et les **Entrepôts** sont deux design patterns qui nous aident à traiter la création des
objets et leur stockage.

### Les Agrégats

> En DDD, un **agrégat** est un groupe d’objets métier fortement liés, que l’on considère comme un seul bloc cohérent lorsqu’on modifie les données.

Dans une application complexe, les objets du domaine peuvent avoir beaucoup de relations entre eux. Si chaque objet peut accéder directement aux autres et les modifier librement, le modèle devient difficile à comprendre, difficile à maintenir et risqué pour l’intégrité des données.

L’objectif des agrégats est donc de simplifier ces relations et de protéger les règles métier importantes.

#### La Racine d'Agrégat

Un agrégat possède une **racine**, appelée **racine d’agrégat**. Cette racine est une Entité, et c’est le seul objet accessible depuis l’extérieur. Les objets internes de l’agrégat ne doivent pas être modifiés directement par le reste de l’application.

Par exemple, si `Client` est la racine d’un agrégat, les autres objets comme `Adresse`, `Contact` ou `InformationsFacturation` peuvent être internes à cet agrégat. Le reste de l’application ne modifie pas directement l’adresse du client : il demande au `Client` de le faire.

Cela permet à la racine de contrôler les changements et de garantir les règles métier, aussi appelées **invariants**.

#### Les Invarients

Un **invariant** est une règle qui doit toujours rester vraie. Par exemple :

* une intervention clôturée ne peut plus être modifiée ;
* une facture ne peut pas être validée sans client ;
* un client doit toujours avoir au moins une adresse principale ;
* une ligne de facture ne peut pas avoir un montant négatif.

Grâce à l’agrégat, ces règles sont centralisées et protégées. Les objets extérieurs ne peuvent pas modifier n’importe quoi n’importe comment.

En base de données, on peut stocker plusieurs objets liés, mais dans le modèle métier, on ne devrait récupérer et manipuler directement que la racine de l’agrégat. Les objets internes sont accessibles à travers elle.

**En résumé, un agrégat sert à regrouper des Entités et des Objets Valeurs autour d’une racine, afin de contrôler les modifications, protéger les règles métier et réduire la complexité des relations entre objets.**

_Exemple : `Client` peut être la racine qui accède à `Adresse`et à `InfosContact`._

### Les Fabriques

> En DDD, une **Fabrique** sert à créer des objets métier complexes, en particulier des Entités ou des Agrégats.

Quand un objet est simple, un constructeur suffit. Mais parfois, créer un objet demande plusieurs étapes, plusieurs objets liés, des règles métier à respecter, ou une identité à générer. Dans ce cas, mettre toute cette logique dans le constructeur ou dans la couche application rendrait le code confus et fragile.

La Fabrique permet donc d’isoler la logique de création dans un objet dédié. Elle sait comment construire l’objet correctement, avec les bonnes valeurs, les bonnes relations et les règles métier respectées.

C’est particulièrement utile pour les Agrégats : lorsque la racine de l’agrégat est créée, les objets internes nécessaires doivent aussi être créés, et l’ensemble doit être valide dès le départ.

Une Fabrique doit garantir que l’objet créé est dans un état cohérent. Si l’objet ne peut pas être créé correctement, elle doit refuser la création, par exemple en lançant une erreur.

Il existe plusieurs formes de Fabriques :

* une méthode de création directement dans une Entité ou une racine d’Agrégat ;
* une classe Fabrique séparée, dédiée à la création d’un objet ou d’un Agrégat complexe.

**Il ne faut pas utiliser une Fabrique systématiquement. Si la création est simple, si tous les champs sont fournis directement, et si aucune règle complexe n’est nécessaire, un constructeur classique suffit.**

Les Fabriques sont surtout utiles quand :

* la création est complexe ;
* plusieurs objets doivent être créés ensemble ;
* des invariants doivent être respectés dès le départ ;
* une identité doit être générée ;
* le client ne doit pas connaître les détails internes de construction.

Il faut aussi distinguer la création d’un nouvel objet et la reconstitution d’un objet déjà existant depuis la base de données.
* **Créer un nouvel objet** demande de générer une nouvelle identité et de vérifier les règles de départ (toute violation d'invariant finit en exception).
* **Recharger un objet depuis la base** consiste plutôt à reconstruire un état déjà existant (une violation d'invariant dans ce cas nécessite de réparer l'objet sinon il y a perte de donnée). 

**En résumé, une Fabrique sert à centraliser et sécuriser la création d’objets métier complexes, afin d’éviter que cette logique soit dispersée dans l’application.**

### Les Entrepôts (repository)

> En DDD, un **Entrepôt** — ou **Repository** — sert à retrouver, stocker et supprimer des objets du domaine déjà existants, sans exposer les détails techniques de la base de données.

Dans une application, les objets métier ont un cycle de vie : ils sont créés, utilisés, sauvegardés, retrouvés plus tard, puis parfois supprimés ou archivés. Pour créer un nouvel objet complexe, on peut utiliser une **Fabrique**. Mais pour récupérer un objet déjà existant, on utilise plutôt un **Entrepôt**.

Sans Entrepôt, le code métier ou applicatif pourrait être tenté d’aller directement interroger la base de données avec du SQL, Prisma, ou une autre technologie. Cela poserait problème, car la logique métier se retrouverait mélangée avec des détails techniques d’infrastructure.

L’Entrepôt permet d’éviter cela. Il donne l’impression que les objets du domaine sont disponibles dans une collection en mémoire, même si en réalité ils sont stockés dans une base de données, un fichier ou un service externe.

_Par exemple, au lieu d’écrire partout du code Prisma pour récupérer une intervention, on passe par un Repository :_

```ts
interventionRepository.findById(id)
interventionRepository.save(intervention)
interventionRepository.delete(id)
```

Le reste de l’application n’a pas besoin de savoir comment les données sont réellement stockées.

En DDD, on crée généralement des Entrepôts uniquement pour les **racines d’agrégats**, c’est-à-dire les objets principaux qu’on a besoin de récupérer directement. Les objets internes d’un agrégat doivent normalement être accessibles à travers leur racine, et non récupérés directement depuis la base.

L’interface du Repository appartient au domaine ou à la couche application, car elle exprime un besoin métier : retrouver une intervention, sauvegarder un client, lister des factures, etc. En revanche, son implémentation concrète appartient à l’infrastructure, car elle utilise une technologie précise comme Prisma, PostgreSQL ou une API externe.

Il faut aussi distinguer **Fabrique** et **Entrepôt** :

* la **Fabrique** crée de nouveaux objets ;
* l’**Entrepôt** retrouve et persiste des objets déjà existants.

Même si un Entrepôt peut reconstruire un objet à partir des données stockées en base, ce n’est pas une création métier nouvelle : c’est une reconstitution d’un objet qui existait déjà.

**En résumé, un Entrepôt permet de garder le domaine propre en isolant la logique d’accès aux données, tout en fournissant une interface simple pour récupérer, sauvegarder ou supprimer les objets métier importants.**

