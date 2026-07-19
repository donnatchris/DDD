#  Domain Driven Design

Cet article présente des notions de base nécesaires à la compréhension de l'article sur l'[architecture logicielle].

## Concepts de base

Métier > Modèle > Code

On part de la compréhension du métier, on construit progressivement un modèle, puis on traduit ce modèle dans le code. Ce travail est généralement itératif : le modèle évolue à mesure que l’équipe comprend mieux le domaine.

### Définitions

> Le DDD (Domain-Driven Design) est une approche de conception qui consiste à construire le logiciel autour du métier réel, de ses règles, de son vocabulaire et de ses processus. L’objectif est que le code reflète clairement les concepts métier importants, afin de rendre l’application plus compréhensible, maintenable et évolutive.

* **Le métier** (domaine) est le domaine réel que l’application doit gérer, avec ses règles, ses contraintes, ses acteurs et son vocabulaire. Par exemple, pour Tubert : les clients, les sites, les interventions, les techniciens, les bons d’intervention, la facturation, etc.
* **Le modèle** est la représentation simplifiée de ce métier dans le logiciel. Il traduit les concepts importants du métier en objets, entités, règles et relations compréhensibles par le code.
* **Les design patterns** sont des solutions de conception réutilisables pour répondre à des problèmes fréquents d’architecture ou d’organisation du code. Ils ne sont pas du code prêt à copier, mais plutôt des modèles de solution qui aident à structurer le logiciel de façon plus claire, maintenable et évolutive.
* **Conception en cascade** : méthode de projet séquentielle où l’on avance étape par étape — analyse, conception, développement, test, livraison — avec peu de retours en arrière.
* **Développement agile** : méthode itérative où l’on construit le produit progressivement, par cycles courts, en intégrant régulièrement les retours utilisateurs.
* Un **use case** représente un cas d’utilisation concret de l’application, c’est-à-dire une action que le système doit permettre de réaliser pour répondre à un besoin utilisateur ou métier. _Exemple : créer une intervention, clôturer une intervention, générer une facture ou envoyer un rappel._

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
y a à faire, n’exprime pas forcément ce qu’il y a à exprimer. Écrire un modèle en code
est très difficile.


---

## Les blocs de construction d’une conception orientée Modèle

La programmation orientée objet convient bien à l’implémentation d’un modèle, car ces deux approches reposent sur des concepts compatibles. La programmation orientée objet prévoit
des classes d’objets et leurs associations, des instances d’objets, et l’envoi de messages
entre eux. Les langages de POO rendent possible la création de mappages directs entre
les objets du modèle avec leurs relations, et leurs équivalents en programmation.

Les langages procéduraux offrent un support limité de la conception dirigée par le
Modèle. Ces langages ne proposent pas les briques nécessaires pour implémenter les
composants-clés d’un modèle.

### L’architecture en couches

> L’architecture en couches est une manière d’organiser une application en niveaux de responsabilité distincts — par exemple interface, application, domaine et infrastructure — afin de séparer clairement l’affichage, les cas d’usage, les règles métier et les détails techniques.

Dans une architecture en couches traditionnelle, les dépendances vont généralement des couches supérieures vers les couches inférieures. Toutefois, grâce à l’inversion de dépendance, le domaine et l’application peuvent définir des interfaces que l’infrastructure vient implémenter. Les règles métier restent ainsi indépendantes des technologies utilisées.

Le code lié au modèle est concentré dans une couche isolée de l’interface utilisateur, des tâches applicatives et des mécanismes techniques de persistance.

Une solution architecturale courante pour les conceptions dirigées par le domaine
contient quatre couches conceptuelles :

* **Couche de présentation** (interface utilisateur) : présentation de l'information à l'utilisateur / interprétation des commandes utilisateurs
* **Couche application** : coordonne l’activité de l’application. Elle orchestre les cas d’usage, mais ne contient pas les règles métier essentielles et ne conserve pas l’état durable des objets métier. Elle peut toutefois suivre la progression d’une tâche applicative.
* **Couche domaine** : contient le modèle et les règles du domaine ; elle constitue le cœur du logiciel métier.
* **Couche infrastructure** : fournit les moyens techniques nécessaires aux autres couches : persistance, communication avec des services externes, fichiers, messagerie, bibliothèques auxiliaires, etc.

### Les Entités

> Les **Entités** sont des objets métier identifiés par une identité unique et durable, dont l’état peut évoluer dans le temps, par exemple un `Client`, une `Intervention` ou un `Technicien`.

Il est important que deux objets avec des
identités différentes soient facilement distingués par le système, et que deux objets de
même identité soient considérés comme les mêmes. Si cette condition n’est pas
satisfaite, alors le système entier peut devenir corrompu.

L’identité unique de chaque objet peut être créée de différentes manières :

* clé primaire générée dans la base de données ;
* identifiant unique créé par l’application ;
* dans certains cas, une combinaison stable d’attributs métier peut servir d’identité naturelle.

```ts
class Client {
  constructor(
    public readonly id: string,
    public nom: string,
    public email: string,
  ) {}

  equals(other: Client): boolean {
    return this.id === other.id;
  }
}
```
```ts
const client1 = new Client("123", "Alice", "alice@mail.com");
const client2 = new Client("123", "Alice", "nouveau@mail.com");

console.log(client1.equals(client2)); // true

// Les deux objets représentent le même client car ils ont la même identité, même si leur état diffère.
```

### Les Objets-Valeurs

> Les Objets-Valeurs sont des objets métier définis uniquement par leurs valeurs, sans identité propre, généralement immuable, par exemple une `Adresse`, une `Période`, ou un `Montant`.

Il y a des cas où on a purement besoin de stocker des attributs d’un élément du
domaine. Ce qui nous intéresse n’est pas de savoir de quel objet il s’agit, mais quels
attributs il a. Un objet qui est utilisé pour décrire certains aspects d’un domaine et qui
n’a pas d’identité, est appelé Objet-Valeur.

Il est fortement recommandé que les objets-valeurs soient immuables. Ils sont créés à
l’aide d’un constructeur, et jamais modifiés au cours de leur vie. Lorsque vous voulez
une valeur différente pour l’objet, vous en créez tout simplement un autre. Étant immuables, ils sont facilement partageables.

Les Objets-Valeurs peuvent contenir d’autres Objets-Valeurs, ils peuvent même
contenir des références à des Entités.

```ts
class Montant {
  constructor(
    public readonly valeur: number,
    public readonly devise: string,
  ) {}

  equals(other: Montant): boolean {
    return (
      this.valeur === other.valeur &&
      this.devise === other.devise
    );
  }
}
```
```ts
const prix1 = new Montant(50, "EUR");
const prix2 = new Montant(50, "EUR");

console.log(prix1.equals(prix2)); // true

// Les deux objets sont égaux car ils contiennent les mêmes valeurs. Ils n’ont pas d’identité propre.
```

### Les Services

> En DDD, un **Service de domaine** sert à représenter une opération métier importante qui n’appartient naturellement ni à une Entité ni à un Objet-Valeur.

Une Entité ou un Objet-Valeur doit contenir les comportements qui lui appartiennent vraiment. Mais certaines opérations concernent plusieurs objets à la fois, ou ne peuvent pas être rattachées clairement à un seul objet. Dans ce cas, les mettre dans une Entité rendrait le modèle confus et créerait trop de dépendances entre les objets.

_Exemple : transférer de l’argent d’un compte à un autre. Cette action ne semble appartenir ni uniquement au compte qui envoie, ni uniquement au compte qui reçoit. Elle peut donc être représentée par un Service._

Un Service de domaine doit généralement respecter trois idées :

* il représente une **opération importante du métier**
* cette opération peut **impliquer plusieurs objets du domaine**, sans que cela soit obligatoire ;
* le Service ne possède généralement **pas d’état métier propre et durable**.

Il ne faut pas créer un Service pour chaque petite opération. Si une règle ou une action appartient clairement à une Entité ou à un Objet-Valeur, elle doit rester dans cet objet. Le Service est utile seulement quand l’opération est un vrai concept métier indépendant.

**Il faut aussi distinguer plusieurs types de services :**

* un **Service de domaine** contient une logique métier importante
* un **Service d’application** orchestre un cas d’usage
* un **Service d’infrastructure** gère des détails techniques comme la base de données, les fichiers ou les API externes

_Par exemple, dans une application de reporting, un Service de domaine peut déterminer quel gabarit utiliser selon le type de rapport, le client ou d’autres règles métier. La récupération technique du fichier correspondant est ensuite déléguée à un Repository ou à un port d’infrastructure._

En résumé, un Service de domaine sert à placer une logique métier importante au bon endroit, quand cette logique ne correspond pas naturellement à la responsabilité d’une Entité ou d’un Objet-Valeur.

```ts
class Compte {
  constructor(public solde: number) {}

  debiter(montant: number): void {
    if (montant > this.solde) {
      throw new Error("Solde insuffisant");
    }

    this.solde -= montant;
  }

  crediter(montant: number): void {
    this.solde += montant;
  }
}

class ServiceDeTransfert {
  transferer(source: Compte, destination: Compte, montant: number): void {
    source.debiter(montant);
    destination.crediter(montant);
  }
}
```
```ts
const compteA = new Compte(100);
const compteB = new Compte(20);

const transfert = new ServiceDeTransfert();
transfert.transferer(compteA, compteB, 30);

// ServiceDeTransfert est un service de domaine, car le transfert implique deux entités Compte et n’appartient naturellement à aucune des deux.
```

### Les Modules

> En DDD, les **modules** servent à organiser un modèle métier devenu trop grand ou trop complexe.

Au lieu d’essayer de comprendre toute l’application d’un seul bloc, on regroupe les concepts proches dans des modules cohérents. Cela permet de mieux comprendre le système, de réduire la complexité et de faciliter la maintenance.

Un bon module doit avoir une **forte cohésion** : les éléments qu’il contient doivent travailler ensemble autour d’un même concept ou d’une même tâche métier. _Par exemple, on pourrait avoir des modules comme **Interventions**, **Clients**, **Techniciens**, **Facturation** ou **Matériel**._

Il y a plusieurs types de cohésion. Deux des plus répandues sont la cohésion
communicationnelle et la cohésion fonctionnelle.
* La **cohésion communicationnelle** est effective lorsque des parties du module opèrent sur les mêmes données. Cela a du sens
de les regrouper, car il existe une forte relation entre elles.
* La **cohésion fonctionnelle** est atteinte quand toutes les parties du module travaillent de concert pour réaliser une
tâche bien définie. Elle est considérée comme la meilleure forme de cohésion.

Les modules doivent aussi être **faiblement couplés entre eux**. Cela signifie qu’un module ne doit pas dépendre trop fortement des détails internes des autres modules. Idéalement, chaque module expose une interface claire, et les autres parties de l’application passent par cette interface plutôt que d’accéder directement à ses objets internes.

Les noms des modules sont importants : ils doivent refléter le métier et faire partie du langage commun utilisé par les développeurs et les utilisateurs métier.

Enfin, les modules ne doivent pas être figés trop tôt. Leur découpage peut évoluer avec la compréhension du domaine. Si on se rend compte qu’un module est mal découpé, il vaut mieux le refactoriser proprement plutôt que d’ajouter des solutions bricolées.

En résumé, les modules servent à découper le domaine en parties cohérentes, compréhensibles et relativement indépendantes, afin de rendre l’application plus claire, maintenable et évolutive.

```text
Voici un exemple simple avec trois modules métier.

Chaque module regroupe des éléments liés à un même concept métier :
- interventions gère la planification et le suivi des interventions ;
- clients gère les clients et leurs informations ;
- facturation gère les factures et les montants.

src/
└── modules/
    ├── interventions/
    │   ├── Intervention.ts
    │   ├── PlanifierIntervention.ts
    │   └── InterventionRepository.ts
    │
    ├── clients/
    │   ├── Client.ts
    │   ├── Adresse.ts
    │   └── ClientRepository.ts
    │
    └── facturation/
        ├── Facture.ts
        ├── Montant.ts
        └── GenererFacture.ts
```
Par exemple, le module `interventions` ne devrait pas modifier directement les objets internes du module `clients`.

Il pourrait utiliser une interface claire :

```ts
interface ClientProvider {
  existe(clientId: string): Promise<boolean>;
}
```

Le module `interventions` dépend ainsi d’un contrat et non de l’implémentation interne du module `clients`.

---

## Les patterns liés au cycle de vie des objets du domaine

> Gérer le cycle de vie d’un objet du domaine constitue un défi en soi, et si ce n’est pas
fait correctement, cela peut avoir un impact négatif sur le modèle du domaine. Nous
allons présenter trois patterns qui nous aident à régler cela.

* **Agrégat** est un pattern de domaine utilisé pour définir l’appartenance et les frontières des objets.
* Les **Fabriques** et les **repositories** sont deux design patterns qui nous aident à traiter la création des
objets et leur stockage.

### Les Agrégats

> En DDD, un **agrégat** est un groupe d’objets métier fortement liés, que l’on considère comme un seul bloc cohérent lorsqu’on modifie les données.

Dans une application complexe, les objets du domaine peuvent avoir beaucoup de relations entre eux. Si chaque objet peut accéder directement aux autres et les modifier librement, le modèle devient difficile à comprendre, difficile à maintenir et risqué pour l’intégrité des données.

L’objectif des agrégats est donc de simplifier ces relations et de protéger les règles métier importantes.

#### La Racine d'Agrégat

Un agrégat possède une **racine**, appelée **racine d’agrégat**. Cette racine est une Entité et constitue le seul point d’entrée permettant de modifier l’agrégat. Les objets internes ne doivent pas être modifiés directement par le reste de l’application, même s’ils peuvent parfois être exposés en lecture.

Par exemple, si `Client` est la racine d’un agrégat, les autres objets comme `Adresse`, `Contact` ou `InformationsFacturation` peuvent être internes à cet agrégat. Le reste de l’application ne modifie pas directement l’adresse du client : il demande au `Client` de le faire.

Cela permet à la racine de contrôler les changements et de garantir les règles métier, aussi appelées **invariants**.

#### Les Invariants

Un **invariant** est une règle qui doit toujours rester vraie. Par exemple :

* une intervention clôturée ne peut plus être modifiée ;
* une facture ne peut pas être validée sans client ;
* un client doit toujours avoir au moins une adresse principale ;
* une ligne de facture ne peut pas avoir un montant négatif.

Grâce à l’agrégat, ces règles sont centralisées et protégées. Les objets extérieurs ne peuvent pas modifier n’importe quoi n’importe comment.

En base de données, on peut stocker plusieurs objets liés, mais dans le modèle métier, on ne devrait récupérer et manipuler directement que la racine de l’agrégat. Les objets internes sont accessibles à travers elle.

**En résumé, un agrégat sert à regrouper des Entités et des Objets-Valeurs autour d’une racine, afin de contrôler les modifications, de protéger les règles métier et de réduire la complexité des relations entre objets.**

_Exemple : `Client` peut être la racine qui donne accès à `Adresse` et à `InfosContact`._

_Exemple avec `Client` qui est la **racine d’agrégat**. L’extérieur ne modifie pas directement la liste des adresses : il passe toujours par les méthodes de `Client`._
```ts
class Adresse {
  constructor(
    public readonly rue: string,
    public readonly ville: string,
  ) {}

  equals(other: Adresse): boolean {
    return this.rue === other.rue && this.ville === other.ville;
  }
}

class Client {
  private adresses: Adresse[] = [];
  private adressePrincipale?: Adresse;

  constructor(
    public readonly id: string,
    public nom: string,
  ) {}

  ajouterAdresse(adresse: Adresse): void {
    if (!this.adresses.some(a => a.equals(adresse))) {
      this.adresses.push(adresse);
    }
  }

  definirAdressePrincipale(adresse: Adresse): void {
    this.ajouterAdresse(adresse);
    this.adressePrincipale = adresse;
  }

  getAdresses(): readonly Adresse[] {
    return this.adresses;
  }

  getAdressePrincipale(): Adresse | undefined {
    return this.adressePrincipale;
  }
}
```

```ts
const client = new Client("client-1", "Alice");
const adresse = new Adresse("10 rue des Fleurs", "Perpignan");

client.definirAdressePrincipale(adresse);
```

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
* **Créer un nouvel objet** demande de générer une nouvelle identité et de vérifier les règles de départ (toute violation d’un invariant provoque une erreur).
* **Recharger un objet depuis la base** consiste plutôt à reconstruire un état déjà existant (une violation d’un invariant dans ce cas nécessite de réparer l'objet sinon il peut y avoir une perte de données). 

**En résumé, une Fabrique sert à centraliser et sécuriser la création d’objets métier complexes, afin d’éviter que cette logique soit dispersée dans l’application.**

_La fabrique génère l’identité, crée les lignes et garantit que la commande est valide dès sa création._
```ts
class LigneCommande {
  constructor(
    public readonly produitId: string,
    public readonly quantite: number,
  ) {
    if (quantite <= 0) {
      throw new Error("La quantité doit être positive");
    }
  }
}

class Commande {
  private constructor(
    public readonly id: string,
    public readonly clientId: string,
    public readonly lignes: readonly LigneCommande[],
  ) {}

  static creer(
    clientId: string,
    produits: Array<{ produitId: string; quantite: number }>,
  ): Commande {
    if (produits.length === 0) {
      throw new Error("Une commande doit contenir au moins un produit");
    }

    const lignes = produits.map(
      produit => new LigneCommande(produit.produitId, produit.quantite),
    );

    return new Commande(
      crypto.randomUUID(),
      clientId,
      lignes,
    );
  }
}
```

```ts
const commande = Commande.creer("client-1", [
  { produitId: "produit-1", quantite: 2 },
  { produitId: "produit-2", quantite: 1 },
]);
```

### Les Entrepôts (Repository)

> En DDD, un **Entrepôt** — ou **Repository** — sert à récupérer et persister des agrégats, sans exposer les détails techniques du stockage.

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

En DDD, on crée généralement des repositories uniquement pour les **racines d’agrégats**, c’est-à-dire les objets principaux qu’on a besoin de récupérer directement. Les objets internes d’un agrégat doivent normalement être accessibles à travers leur racine, et non récupérés directement depuis la base.

L’interface du Repository appartient au domaine lorsqu’elle représente une collection d’agrégats dans le langage métier, ou à la couche application lorsqu’elle répond à un besoin précis d’un cas d’usage. Dans les deux cas, elle ne doit pas être définie par l’infrastructure. Son implémentation concrète appartient à l’infrastructure, car elle utilise une technologie précise comme Prisma, PostgreSQL ou une API externe.

Il faut aussi distinguer **Fabrique** et **Entrepôt** :

* la **Fabrique** construit un nouvel objet ou un nouvel agrégat ;
* l’**Entrepôt** récupère et persiste les agrégats, qu’ils viennent d’être créés ou qu’ils existaient déjà.

Même si un Entrepôt peut reconstruire un objet à partir des données stockées en base, ce n’est pas une création métier nouvelle : c’est une reconstitution d’un objet qui existait déjà.

**En résumé, un Entrepôt permet de garder le domaine propre en isolant la logique d’accès aux données, tout en fournissant une interface simple pour récupérer et sauvegarder les agrégats importants.**

_Voici un exemple simple avec une interface dans l’application et une implémentation technique séparée._
```ts
// Domaine : racine d’agrégat
class Intervention {
  constructor(
    public readonly id: string,
    public statut: "planifiée" | "terminée",
  ) {}

  terminer(): void {
    this.statut = "terminée";
  }
}
```
```ts
// Port sortant : contrat du Repository
interface InterventionRepository {
  findById(id: string): Promise<Intervention | null>;
  save(intervention: Intervention): Promise<void>;
  delete(id: string): Promise<void>;
}
```
```ts
// Adaptateur simplifié pour les tests
class FakeInterventionRepository
  implements InterventionRepository
{
  private interventions = new Map<string, Intervention>();

  async findById(id: string): Promise<Intervention | null> {
    return this.interventions.get(id) ?? null;
  }

  async save(intervention: Intervention): Promise<void> {
    this.interventions.set(intervention.id, intervention);
  }

  async delete(id: string): Promise<void> {
    this.interventions.delete(id);
  }
}
```
_Utilisation dans un cas d’usage :_
_(Le cas d’usage dépend uniquement de l’interface `InterventionRepository`. Il ne sait pas si les données viennent de Prisma, PostgreSQL ou d’un fake en mémoire.)_
```ts
class TerminerIntervention {
  constructor(
    private readonly repository: InterventionRepository,
  ) {}

  async execute(id: string): Promise<void> {
    const intervention = await this.repository.findById(id);

    if (!intervention) {
      throw new Error("Intervention introuvable");
    }

    intervention.terminer();

    await this.repository.save(intervention);
  }
}
```


---

## Rendre explicites les concepts métier

> En DDD, lorsqu’on améliore progressivement le modèle, on cherche à rendre explicites les concepts importants du domaine. Certains concepts sont d’abord implicites : ils existent dans les discussions, les règles métier ou les processus, mais ne sont pas encore représentés clairement dans le code.

Les rendre explicites permet de clarifier le modèle, de mieux organiser le code et parfois de provoquer une véritable amélioration du design. **Contrainte** et **Processus** sont avant tout des concepts métier à représenter clairement ; la **Spécification** est un pattern utile pour encapsuler certains critères métier.

### Contrainte

Une **Contrainte** représente une règle qui doit toujours être respectée. Elle exprime souvent un **invariant**, c’est-à-dire une condition qui doit rester vraie quoi qu’il arrive. Plutôt que de cacher cette règle dans du code difficile à lire, on peut l’exprimer dans une méthode ou un objet dédié.

_Par exemple, une intervention clôturée ne peut plus être modifiée :_

```ts
class Intervention {
  constructor(
    public status: 'draft' | 'done',
    public description: string,
  ) {}

  updateDescription(newDescription: string) {
    if (this.status === 'done') {
      throw new Error('Une intervention clôturée ne peut plus être modifiée.');
    }

    this.description = newDescription;
  }
}
```

### Processus

Un **Processus** représente une action ou un enchaînement d’actions important dans le domaine. S’il n’appartient pas naturellement à une Entité ou à un Objet-Valeur, on peut le placer dans un **Service de domaine**. Si plusieurs manières de l’exécuter existent, une **Stratégie** peut encapsuler l’algorithme choisi. Tous les processus n’ont pas besoin d’être modélisés explicitement : on le fait surtout lorsqu’ils sont importants dans le langage métier.

_Par exemple, planifier une intervention peut nécessiter plusieurs objets : un client, un technicien et une date._

```ts
class Intervention {
  private constructor(
    public readonly clientId: string,
    public readonly technicienId: string,
    public readonly date: Date,
    public readonly statut: "planifiée",
  ) {}

  static planifier(
    clientId: string,
    technicienId: string,
    date: Date,
  ): Intervention {
    return new Intervention(clientId, technicienId, date, "planifiée");
  }
}

class Technicien {
  constructor(
    public readonly id: string,
    public readonly competences: readonly string[],
    private readonly indisponibilites: readonly Date[],
  ) {}

  estDisponible(date: Date): boolean {
    return !this.indisponibilites.some(
      indisponible => indisponible.getTime() === date.getTime(),
    );
  }

  possedeCompetence(competence: string): boolean {
    return this.competences.includes(competence);
  }
}

class PlanningService {
  planifierIntervention(
    clientId: string,
    technicien: Technicien,
    date: Date,
    competenceRequise: string,
  ): Intervention {
    if (!technicien.estDisponible(date)) {
      throw new Error("Le technicien n’est pas disponible à cette date");
    }

    if (!technicien.possedeCompetence(competenceRequise)) {
      throw new Error("Le technicien ne possède pas la compétence requise");
    }

    return Intervention.planifier(clientId, technicien.id, date);
  }
}
```

_Ici, le processus de planification combine plusieurs règles métier et ne relève pas uniquement du client, du technicien ou de l’intervention. Il peut donc être placé dans un Service de domaine._

### Spécification

Une **Spécification** sert à vérifier si un objet respecte certains critères. Elle répond généralement à une question par oui ou non. Par exemple : “ce client est-il éligible à un crédit ?”, “cette intervention peut-elle être clôturée ?”, “cette facture peut-elle être validée ?”. Si la règle devient trop complexe pour rester dans l’Entité, on peut la déplacer dans une Spécification dédiée, qui reste dans la couche domaine.

La Spécification est utile pour :

* tester si un objet respecte une règle ;
* sélectionner des objets dans une collection ;
* vérifier une condition avant de créer ou modifier un objet ;
* combiner plusieurs petites règles simples en une règle métier plus complète.

_Par exemple, vérifier si une intervention peut être clôturée :_

```ts
class InterventionPeutEtreClotureeSpecification {
  isSatisfiedBy(intervention: Intervention): boolean {
    return intervention.status === 'draft'
      && intervention.description.length > 0;
  }
}
```

_On peut ensuite l’utiliser ainsi :_

```ts
const specification = new InterventionPeutEtreClotureeSpecification();

if (!specification.isSatisfiedBy(intervention)) {
  throw new Error('Cette intervention ne peut pas être clôturée.');
}
```

La Spécification permet d’isoler une règle de validation métier dans un objet dédié, au lieu de disperser cette logique dans plusieurs services ou controllers.

**En résumé, ces trois concepts permettent de rendre le modèle plus clair : la Contrainte protège une règle qui doit toujours être vraie, le Processus représente une action métier importante, et la Spécification permet de vérifier si un objet satisfait des critères métier.**


---

## Préserver l’intégrité du modèle

Dans les gros projets, plusieurs équipes peuvent travailler en parallèle sur différentes parties du modèle métier. Le risque est que chaque équipe fasse évoluer sa partie sans toujours mesurer l’impact sur les autres, ce qui peut rendre le modèle incohérent.

Un modèle doit rester cohérent : les termes doivent être clairs, les concepts ne doivent pas se contredire, et chaque partie du système doit être compréhensible. Cette cohérence interne est appelée **unification**.

Dans l’idéal, une entreprise pourrait avoir un grand modèle unique pour tout son domaine. Mais dans la pratique, sur de gros projets, c’est souvent trop difficile à maintenir : trop d’équipes, trop de besoins différents, trop de coordination nécessaire.

La solution n’est donc pas forcément de garder un seul énorme modèle, mais plutôt de découper le domaine en plusieurs modèles plus petits, chacun avec une frontière claire.

Chaque modèle peut alors évoluer de manière plus indépendante, à condition que les liens entre les modèles soient bien définis. Ces liens doivent reposer sur des contrats clairs pour éviter les malentendus et préserver l’intégrité globale du système.

En résumé, préserver l’intégrité du modèle consiste à éviter qu’un gros modèle devienne confus ou contradictoire, en découpant le domaine en modèles cohérents, bien délimités et correctement reliés entre eux.

### Contexte borné

> Un **contexte borné** est une frontière claire à l’intérieur de laquelle un modèle métier a un sens précis et reste cohérent.

Dans un petit projet, on peut souvent travailler avec un seul modèle. Mais dans une grosse application, plusieurs parties du système peuvent utiliser des concepts similaires avec des sens différents. Si on mélange ces modèles, le code devient confus, fragile et difficile à maintenir.

**Le contexte borné** sert donc à définir où un modèle s’applique, avec son propre vocabulaire, ses propres règles et ses propres responsabilités. À l’intérieur de cette frontière, les termes doivent être cohérents et compris de la même manière par l’équipe.

Un contexte borné peut correspondre à une partie de l’application, à une équipe, à une base de code, à un module métier important ou même à un schéma de base de données. L’objectif est que chaque contexte puisse évoluer sans casser les autres.

Il ne faut pas confondre **contexte borné** et **Module**. Le contexte borné définit le périmètre global d’un modèle. Les Modules servent ensuite à organiser les éléments à l’intérieur de ce modèle.

_Par exemple, dans une application e-commerce, on pourrait avoir plusieurs contextes bornés :_

* un contexte **Boutique en ligne** pour les clients, les produits, le panier et les commandes ;
* un contexte **Reporting** pour les statistiques, les ventes et les analyses ;
* un contexte **Expédition** pour la préparation et l’envoi des commandes._

Ces contextes peuvent utiliser certaines données communes, comme les produits ou les clients, mais ils ne les voient pas forcément de la même manière. Le reporting n’a pas besoin du même modèle métier que la boutique en ligne, et l’expédition n’a pas besoin de connaître toute la logique commerciale.

Chaque contexte doit donc avoir son propre modèle, et les échanges entre contextes doivent passer par des interfaces ou des messages bien définis.

**En résumé, un contexte borné permet de découper un grand domaine en modèles plus petits, cohérents et indépendants, afin d’éviter qu’un seul modèle global devienne trop confus ou impossible à maintenir.**

### Intégration Continue

> L’**Intégration continue** sert à maintenir un **contexte borné** cohérent lorsque plusieurs développeurs travaillent sur le même modèle.

Même dans une petite équipe, le modèle peut se fragmenter : chacun peut comprendre une règle différemment, dupliquer du code, ajouter une fonctionnalité au mauvais endroit ou modifier une partie du modèle sans voir l’impact sur le reste.

Comme le modèle évolue avec la compréhension du domaine, les nouveaux concepts doivent être intégrés proprement au modèle existant et au code. L’intégration continue permet de vérifier régulièrement que tout reste cohérent.

Concrètement, cela signifie :

* **fusionner le code souvent**, idéalement tous les jours ;
* **compiler automatiquement** le projet ;
* **exécuter des tests** automatisés ;
* **détecter rapidement les erreurs** ou incohérences ;
* **corriger tôt**, avant que les problèmes ne deviennent trop coûteux.

**L’intégration continue ne sert pas seulement à vérifier que le code fonctionne techniquement. Dans une démarche DDD, elle aide aussi à préserver l’unité du modèle dans un même contexte borné. Elle s’applique donc surtout à l’intérieur d’un contexte borné, et non à la gestion des relations entre plusieurs contextes bornés.**

### Carte de Contexte

> Une **Carte de Contexte** est un document ou un schéma qui montre les différents **contextes bornés** d’une application et les relations entre eux.

Dans une application d’entreprise, il existe souvent plusieurs modèles métier, chacun avec son propre contexte borné. Chaque équipe peut travailler sur son contexte, mais tout le monde doit comprendre comment ces contextes s’articulent dans le système global.

La Carte de Contexte sert donc à donner une vue d’ensemble : elle indique les limites de chaque contexte, leurs noms, leurs responsabilités et les liens qui existent entre eux.

Elle permet d’éviter les chevauchements, les malentendus et les problèmes d’intégration. Si les relations entre contextes ne sont pas clairement définies, le système risque de mal fonctionner lorsqu’on assemble toutes les parties.

Chaque contexte borné doit avoir un nom clair, intégré au langage commun du projet. On peut ensuite organiser le code et les modules en respectant ces frontières.

La Carte de Contexte peut aussi montrer le type de relation entre les contextes, par exemple :

* **Noyau partagé** : deux contextes partagent une partie commune du modèle ;
* **Client-Fournisseur** : un contexte dépend d’un autre pour obtenir des données ou des services ;
* **chemins séparés** : deux contextes évoluent indépendamment ;
* **Service hôte ouvert** : un contexte expose une interface claire aux autres ;
* **Couche anticorruption** : un contexte protège son modèle face à un système externe ou ancien.

**En résumé, la Carte de Contexte permet de visualiser les grands blocs métier du système, leurs frontières et leurs relations, afin de garder une architecture claire et cohérente.**

### Noyau partagé

> Le **Noyau partagé** est une partie du modèle, du code ou de la base de données que plusieurs équipes ou plusieurs **contextes bornés** acceptent de partager.

Il est utile lorsque deux contextes doivent rester séparés, mais qu’ils ont quand même besoin de certains concepts communs. Au lieu de dupliquer ces concepts dans chaque contexte, on définit une partie commune officielle : le noyau partagé.

Ce noyau doit être petit, clair et stable. Comme il est utilisé par plusieurs équipes, il ne doit pas être modifié librement sans concertation. Chaque changement peut avoir un impact sur les autres contextes.

Le **Noyau partagé** permet d’éviter les doublons et de conserver un vocabulaire commun sur certains concepts importants. Mais il demande de la discipline : les équipes doivent communiquer, intégrer régulièrement leurs changements et tester le noyau partagé à chaque modification.

En pratique, il faut :

* définir précisément ce qui fait partie du noyau partagé ;
* éviter d’y mettre trop de choses ;
* prévenir les autres équipes avant de le modifier ;
* fusionner régulièrement les changements ;
* exécuter les tests des équipes concernées.

**En résumé, le Noyau partagé permet à plusieurs contextes de partager une partie commune du modèle, tout en gardant leur indépendance sur le reste.**

### Client-Fournisseur

> Le pattern **Client-Fournisseur** décrit la relation entre deux **contextes bornés** lorsque l’un dépend fortement de l’autre.

Le **fournisseur** produit des données, des services ou une interface dont le **client** a besoin pour fonctionner. Les deux contextes restent séparés, avec leurs propres modèles, mais ils doivent se coordonner parce que les décisions du fournisseur peuvent impacter le client.

_Par exemple, dans une application e-commerce, le contexte **Boutique en ligne** enregistre les commandes, les clients et les produits. Le contexte **Reporting**, lui, a besoin de ces données pour produire des analyses. La boutique en ligne joue donc le rôle de fournisseur, et le reporting celui de client._

Dans ce type de relation, il ne faut pas forcément créer un modèle commun ou un noyau partagé. Les deux contextes peuvent avoir des objectifs, des technologies et des modèles différents. Ce qui compte, c’est de définir clairement ce que le fournisseur doit exposer au client.

Les équipes doivent donc se coordonner comme dans une vraie relation client-fournisseur :

* le client exprime ses besoins ;
* le fournisseur décide comment et quand les intégrer ;
* les engagements sont discutés et planifiés ;
* l’interface entre les deux systèmes est clairement définie ;
* des tests automatisés vérifient que le fournisseur respecte bien ce que le client attend.

Ce pattern fonctionne bien lorsque les deux équipes peuvent se coordonner correctement, idéalement avec un management commun ou une organisation claire.

**En résumé, le pattern Client-Fournisseur permet à deux contextes séparés de collaborer lorsque l’un dépend des données ou services de l’autre, sans pour autant fusionner leurs modèles.**

### Conformiste

> Le pattern **Conformiste** s’applique quand un contexte client dépend d’un contexte fournisseur, mais que le fournisseur ne s’adapte pas vraiment aux besoins du client.

Dans une relation idéale **Client-Fournisseur**, le fournisseur écoute les besoins du client et fait évoluer son interface en conséquence. Mais dans la réalité, le fournisseur peut avoir ses propres priorités, ses propres délais, ou appartenir à une autre organisation. Le client ne peut donc pas compter sur lui pour obtenir les changements souhaités.

Dans ce cas, l’équipe cliente a plusieurs options. Elle peut couper la relation et devenir totalement indépendante, mais ce n’est pas toujours possible ou rentable. Elle peut aussi protéger son modèle avec une couche de traduction, appelée **Couche anticorruption**, surtout si le modèle du fournisseur est mal adapté ou mal conçu.

Mais si le modèle du fournisseur est correct, stable et utile, l’équipe cliente peut choisir de s’y conformer. Cela signifie qu’elle adopte le modèle du fournisseur tel quel, sans essayer de le modifier ni de le traduire complètement.

Le client construit alors son propre système en acceptant les concepts, les règles et les structures imposés par le fournisseur. Il perd une partie de sa liberté de conception, mais gagne en simplicité d’intégration.

Ce pattern ressemble au **Noyau partagé**, mais avec une différence importante : dans un Noyau partagé, les deux équipes peuvent faire évoluer la partie commune ensemble. Dans le pattern Conformiste, le client ne contrôle pas le modèle fournisseur. Il doit simplement s’y adapter.

En résumé, le pattern **Conformiste** consiste à accepter et utiliser le modèle d’un fournisseur tel qu’il est, lorsque ce modèle est suffisamment bon et que le coût de s’en protéger ou de le remplacer serait trop élevé.

### Couche anticorruption

> La **Couche anticorruption** sert à protéger notre modèle métier lorsqu’il doit communiquer avec un système externe, ancien ou mal adapté.

Dans un projet, il arrive souvent qu’une nouvelle application doive échanger avec une application historique, une API externe ou une base de données déjà existante. Le problème est que ce système externe possède son propre modèle, son propre vocabulaire et ses propres contraintes. Si on l’utilise directement dans notre code, il risque de contaminer notre modèle et de le rendre confus.

**La Couche anticorruption se place donc entre notre application et le système externe**. Elle agit comme un traducteur : côté application, elle parle le langage de notre domaine ; côté système externe, elle parle le langage technique ou métier du système distant.

Son objectif est de garder notre modèle propre, cohérent et indépendant. Notre domaine ne doit pas connaître les détails de l’ancien système, ses noms de champs, ses structures de données ou ses règles internes.

_Par exemple, si notre application parle d’`Intervention`, mais que l’ancien logiciel utilise des notions comme `Ticket`, `CodeAffaire` ou `OperationLegacy`, la Couche anticorruption traduit ces concepts pour que notre domaine continue à manipuler ses propres objets._

Elle peut être composée de plusieurs éléments :

* un **Service**, vu depuis notre domaine comme une interface simple ;
* une **Façade**, qui simplifie l’accès au système externe ;
* un **Adaptateur**, qui convertit l’interface externe vers une interface compréhensible par notre application ;
* un **Traducteur / Mapper**, qui transforme les données externes en objets de notre domaine, et inversement.

Il ne faut pas créer un seul gros adaptateur qui fait tout. Il vaut mieux séparer les responsabilités, surtout si plusieurs services externes ou plusieurs types de données sont concernés.

**En résumé, la Couche anticorruption permet d’intégrer un système externe sans laisser son modèle, son vocabulaire ou sa complexité polluer notre propre domaine.**

### Chemins séparés

> Le pattern **chemins séparés** s’applique lorsqu’il est préférable de ne pas intégrer deux sous-systèmes, parce que cette intégration coûterait plus cher qu’elle ne rapporterait.

Dans certains projets, on cherche à faire travailler plusieurs sous-systèmes ensemble. Mais cela demande souvent beaucoup d’efforts : coordination entre équipes, fusion de code, tests d’intégration, compromis sur les modèles, couches de traduction, adaptation aux contraintes de l’autre système, etc.

Il faut donc se demander si l’intégration apporte une vraie valeur. **Si deux parties de l’application ont peu de choses en commun au niveau métier, il peut être plus simple et plus sain de les développer séparément.**

Dans ce cas, chaque partie possède son propre **contexte borné**, son propre modèle et éventuellement sa propre technologie. Pour l’utilisateur, l’ensemble peut quand même apparaître comme une seule application, par exemple grâce à une interface commune ou un portail qui donne accès aux différents modules.

L’avantage principal est la liberté : chaque sous-système peut évoluer indépendamment, avec moins de dépendances et moins de compromis.

Mais il faut être prudent : une fois que deux modèles ont évolué séparément, il devient très difficile de les réintégrer plus tard. Il faut donc choisir les chemins séparés seulement si l’on est assez sûr que l’intégration profonde n’est pas nécessaire.

**En résumé, les chemins séparés consistent à accepter que deux parties du système évoluent indépendamment, lorsqu’elles n’ont pas assez de liens métier pour justifier une intégration forte.**

### Service hôte ouvert

> Le **Service hôte ouvert** s’applique lorsqu’un sous-système doit être utilisé par plusieurs autres sous-systèmes.

Sans ce pattern, chaque client risque de créer sa propre couche de traduction pour communiquer avec ce sous-système. Cela produit beaucoup de code en double, plus de maintenance, et davantage de risques à chaque changement.

L’idée est donc de transformer le sous-système en **fournisseur de services**. Il expose une interface claire, stable et partagée, que les autres systèmes peuvent utiliser directement.

Cette interface peut prendre la forme d’une API, d’un protocole, de messages ou de services applicatifs. Elle doit être suffisamment générale pour répondre aux besoins communs des différents clients, sans devenir trop complexe.

Si un client a un besoin très spécifique, il vaut mieux créer un adaptateur ou une traduction particulière pour ce cas précis, plutôt que de compliquer l’interface commune pour tout le monde.

**En résumé, le Service hôte ouvert permet à un sous-système d’exposer une interface publique claire et réutilisable, afin de faciliter son intégration avec plusieurs autres systèmes.**

### Distillation

> La **Distillation** consiste à identifier ce qui constitue le cœur réel du domaine métier, puis à le séparer du reste du modèle.

Dans un gros système, le modèle peut devenir très vaste, même après plusieurs améliorations. Il contient souvent beaucoup d’éléments nécessaires, mais tous n’ont pas la même importance. Certains représentent la vraie valeur métier de l’application, tandis que d’autres sont plus génériques ou secondaires.

On distingue généralement trois catégories : le **cœur de domaine**, les **sous-domaines de support** et les **sous-domaines génériques**.

Le **cœur de domaine** correspond à la partie la plus importante, la plus spécifique et la plus stratégique du système. C’est là que se trouve la vraie valeur métier. C’est aussi là qu’il faut concentrer les meilleurs efforts de conception, de modélisation et de développement.

Les **sous-domaines de support** sont spécifiques à l’activité de l’entreprise et nécessaires à son fonctionnement, mais ils ne constituent pas son principal avantage concurrentiel. Ils peuvent être développés sur mesure, avec un effort de conception proportionné à leur importance.

Les **sous-domaines génériques** répondent à des besoins courants que de nombreuses applications partagent, comme l’authentification, la comptabilité générale ou l’envoi d’e-mails. Ils peuvent souvent être confiés à des solutions existantes ou isolés dans des composants séparés.

_Par exemple, dans un système de surveillance du trafic aérien, la notion de route peut sembler importante. Mais le vrai cœur du domaine est plutôt le calcul de trajectoires d’avion et la détection des risques de collision. La route devient alors un sous-domaine plus générique, tandis que la trajectoire et l’alerte collision constituent le cœur métier._

Le cœur de domaine dépend toujours du point de vue de l’application. Un concept peut être central dans un système, mais secondaire dans un autre.

La **Distillation** aide donc à ne pas mettre tous les éléments du modèle au même niveau. Elle permet de concentrer l’énergie sur ce qui fait réellement la valeur du projet, tout en isolant les parties plus génériques.

Pour les sous-domaines génériques — et parfois pour certains sous-domaines de support — plusieurs choix sont possibles :

* utiliser une solution du commerce ;
* sous-traiter le développement ;
* s’appuyer sur un modèle existant ;
* développer une solution maison si l’intégration le justifie.

**En résumé, la Distillation sert à faire ressortir le cœur de domaine, à distinguer les sous-domaines de support et génériques, puis à concentrer les meilleurs efforts sur ce qui crée réellement la valeur métier.**



