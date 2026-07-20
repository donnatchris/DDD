# Authentification, autorisation et gestion des permissions

Dans une application, il faut distinguer deux questions différentes :

1. **Qui est l’utilisateur ?**
2. **Que peut-il faire ?**

La première relève de l’**authentification**.

La seconde relève de l’**autorisation**.

```text
Authentification
    ↓
Identifier l’utilisateur

Autorisation
    ↓
Vérifier ce qu’il a le droit de faire
```

Exemple :

```text
Christophe se connecte avec son adresse e-mail
    ↓
Le système confirme son identité
    ↓
Christophe demande à supprimer un utilisateur
    ↓
Le système vérifie s’il possède cette permission
```

---

# Authentification

## Définition

L’**authentification** consiste à vérifier l’identité d’un utilisateur, d’une application ou d’un appareil.

Elle répond à la question :

> Qui effectue cette action ?

Les moyens d’authentification les plus courants sont :

* mot de passe ;
* lien de connexion envoyé par e-mail ;
* code à usage unique ;
* authentification Google, Microsoft ou autre fournisseur ;
* certificat numérique ;
* clé d’API ;
* jeton d’accès ;
* authentification biométrique ;
* authentification multifacteur.

---

## Identification et authentification

Ces deux notions sont proches mais différentes.

L’identification consiste à déclarer une identité :

```text
Je suis christophe@example.com
```

L’authentification consiste à le prouver :

```text
Je connais le mot de passe associé
```

ou :

```text
Je contrôle le compte Google associé
```

---

## Authentification par session

Avec une authentification par session, le serveur conserve une session associée à l’utilisateur.

```text
Utilisateur
    ↓ identifiants
Serveur d’authentification
    ↓
Création d’une session
    ↓
Cookie de session envoyé au navigateur
```

Lors des requêtes suivantes, le navigateur transmet automatiquement le cookie.

```http
Cookie: session_id=abc123
```

Le serveur retrouve alors la session correspondante.

### Avantages

* révocation immédiate possible ;
* état de session contrôlé côté serveur ;
* pratique pour les applications Web ;
* cookie sécurisé pris en charge par le navigateur.

### Limites

* nécessite un stockage de sessions ;
* nécessite parfois un stockage partagé si plusieurs serveurs sont utilisés ;
* protection contre les attaques CSRF à prévoir selon la configuration.

---

## Authentification par jeton

Une autre approche consiste à fournir un jeton à l’utilisateur après sa connexion.

```text
Utilisateur → Connexion → Serveur
Utilisateur ← Jeton ← Serveur
```

Le client transmet ensuite le jeton à chaque requête :

```http
Authorization: Bearer <token>
```

Le jeton peut être opaque ou autoportant.

---

## Jeton opaque

Un jeton opaque est une valeur sans signification directe pour le client.

```text
f5d8e4e2a1c9...
```

Le serveur doit vérifier ce jeton auprès d’un stockage ou d’un serveur d’authentification.

### Avantages

* facilement révocable ;
* aucune information métier exposée dans le jeton ;
* contrôle centralisé.

### Limites

* nécessite une vérification serveur ;
* peut nécessiter un appel supplémentaire.

---

## JWT

Un **JWT**, pour *JSON Web Token*, est un jeton signé contenant des informations appelées **claims**.

Exemple de contenu :

```json
{
  "sub": "user-42",
  "email": "christophe@example.com",
  "role": "ADMIN",
  "iat": 1784534400,
  "exp": 1784538000
}
```

Un JWT comporte généralement trois parties :

```text
Header.Payload.Signature
```

### Important

Un JWT est généralement **signé**, mais pas forcément chiffré.

Cela signifie que son contenu peut souvent être lu par le client, mais qu’il ne peut pas être modifié sans invalider la signature.

### Avantages

* validation rapide ;
* pratique pour les API distribuées ;
* limite les consultations d’un stockage de session ;
* contient des informations utiles à l’autorisation.

### Limites

* révocation plus complexe ;
* permissions potentiellement obsolètes jusqu’à expiration ;
* risque si le jeton est conservé de manière non sécurisée ;
* tendance à mettre trop d’informations dans le jeton.

### Bonne pratique

Les JWT devraient généralement être :

* de courte durée ;
* transmis uniquement par HTTPS ;
* vérifiés strictement ;
* associés à un mécanisme de renouvellement ;
* limités aux informations nécessaires.

---

## Access token et refresh token

Un système peut utiliser deux jetons.

### Access token

Jeton de courte durée utilisé pour appeler l’API.

```text
Durée possible : 5 à 30 minutes
```

### Refresh token

Jeton plus durable permettant d’obtenir un nouvel access token.

```text
Client → refresh token → serveur d’authentification
Client ← nouvel access token
```

Le refresh token doit être conservé avec davantage de précautions.

---

## OAuth 2.0

**OAuth 2.0** est un cadre d’autorisation permettant à une application d’obtenir un accès limité à un service au nom d’un utilisateur.

Exemple :

```text
Application Tubert
    ↓
Demande l’accès au compte Microsoft
    ↓
L’utilisateur autorise l’accès
    ↓
Microsoft retourne un jeton
```

OAuth 2.0 ne définit pas directement l’identité de l’utilisateur.

Pour l’authentification, il est souvent associé à **OpenID Connect**.

---

## OpenID Connect

**OpenID Connect**, ou **OIDC**, ajoute une couche d’identité au-dessus d’OAuth 2.0.

Il permet notamment de savoir :

* quel utilisateur s’est connecté ;
* son identifiant ;
* son adresse e-mail ;
* éventuellement son nom ou son organisation.

Exemples de fournisseurs :

* Google ;
* Microsoft Entra ID ;
* Auth0 ;
* Keycloak ;
* Okta.

---

## Authentification multifacteur

L’authentification multifacteur, ou **MFA**, demande plusieurs preuves différentes.

Exemple :

```text
Mot de passe
    +
Code temporaire dans une application
```

Les facteurs peuvent appartenir à plusieurs catégories :

* quelque chose que l’on connaît : mot de passe ;
* quelque chose que l’on possède : téléphone ou clé de sécurité ;
* quelque chose que l’on est : empreinte ou visage.

Le MFA réduit fortement le risque lié au vol d’un mot de passe.

---

# Autorisation

## Définition

L’**autorisation** consiste à décider si une identité authentifiée peut effectuer une action.

Elle répond à la question :

> Cet utilisateur a-t-il le droit de faire cela ?

Exemples :

* consulter une tournée ;
* modifier un camion ;
* supprimer un utilisateur ;
* créer une facture ;
* voir les salaires ;
* valider une collecte ;
* accéder à un site particulier.

---

## Permission

Une **permission** représente généralement une action autorisée.

Exemples :

```text
vehicle.read
vehicle.create
vehicle.update
vehicle.delete

user.read
user.invite
user.disable

temperature.read
temperature.export
temperature.configure-alert
```

Une permission doit idéalement exprimer :

```text
ressource.action
```

ou :

```text
domaine.action
```

Exemples :

```text
collection.validate
invoice.generate
site.manage
```

---

## Rôle

Un **rôle** est un regroupement de permissions.

Exemple :

```text
ADMIN
├── user.read
├── user.invite
├── user.disable
├── vehicle.read
├── vehicle.update
└── vehicle.delete
```

Un rôle permet d’éviter d’attribuer les permissions une par une à chaque utilisateur.

---

# RBAC — Role-Based Access Control

## Définition

Le **RBAC**, ou contrôle d’accès fondé sur les rôles, attribue des permissions à des rôles, puis attribue les rôles aux utilisateurs.

```text
Utilisateur
    ↓ possède
Rôle
    ↓ contient
Permissions
```

Exemple :

```text
Jean
    ↓
EXPLOITANT
    ↓
vehicle.read
vehicle.update
collection.read
collection.validate
```

La décision d’autorisation repose principalement sur le rôle de l’utilisateur.

---

## Modèle RBAC simple

```text
Utilisateur ── possède ──> Rôle ── possède ──> Permission
```

Exemple de tables :

```text
users
roles
permissions
user_roles
role_permissions
```

### Exemple de données

```text
Utilisateur : Jean

Rôle :
- EXPLOITANT

Permissions :
- vehicle.read
- route.read
- route.update
- collection.validate
```

---

## Exemple de vérification RBAC

```typescript
if (!currentUser.permissions.includes("vehicle.update")) {
  throw new ForbiddenError();
}
```

Ou à travers une politique :

```typescript
permissionService.assertCan(
  currentUser,
  "vehicle.update",
);
```

---

## Hiérarchie des rôles

Certains systèmes organisent les rôles de façon hiérarchique.

```text
ADMIN
  ↓ hérite de
MANAGER
  ↓ hérite de
OPERATOR
```

L’administrateur possède alors automatiquement toutes les permissions des rôles inférieurs.

### Avantage

Cela évite de répéter certaines permissions.

### Risque

Les hiérarchies peuvent devenir difficiles à comprendre.

```text
SUPER_ADMIN
    ↓
ADMIN
    ↓
REGIONAL_MANAGER
    ↓
SITE_MANAGER
    ↓
OPERATOR
```

Il devient alors difficile de savoir exactement pourquoi un utilisateur possède une permission.

Une composition explicite des permissions est souvent plus claire qu’une hiérarchie profonde.

---

## RBAC avec rôles contextuels

Un rôle peut être attribué dans un contexte précis.

Exemple :

```text
Jean est MANAGER sur le site de Perpignan
Jean est OBSERVER sur le site de Narbonne
```

Le rôle n’est donc pas global.

```text
UserRole
- userId
- roleId
- siteId
```

Cela permet d’exprimer :

```text
Jean peut modifier les véhicules du site de Perpignan
mais seulement consulter ceux du site de Narbonne
```

On reste dans une logique RBAC, mais avec une portée.

---

## Avantages du RBAC

* simple à comprendre ;
* facile à administrer ;
* adapté à une organisation structurée ;
* cohérent avec les fonctions de l’entreprise ;
* facile à expliquer à des responsables métier ;
* pratique pour les applications internes.

---

## Limites du RBAC

Le RBAC devient moins adapté lorsque les règles dépendent fortement du contexte.

Exemple :

```text
Un responsable peut modifier une tournée uniquement si :
- elle appartient à son site ;
- elle n’a pas encore commencé ;
- il est affecté à cette zone ;
- le montant est inférieur à un seuil.
```

Créer un rôle pour chaque combinaison serait impossible :

```text
SITE_MANAGER_PERPIGNAN_BEFORE_START_UNDER_5000
```

Le RBAC ne doit donc pas essayer de représenter toutes les règles métier.

---

## Quand utiliser RBAC

Le RBAC est pertinent lorsque les permissions dépendent principalement :

* du métier de l’utilisateur ;
* de sa fonction ;
* de son niveau de responsabilité ;
* de son équipe ;
* de son périmètre organisationnel.

Exemples :

```text
ADMINISTRATEUR
RESPONSABLE_SITE
EXPLOITANT
CHAUFFEUR
COMPTABLE
LECTEUR
```

---

# ABAC — Attribute-Based Access Control

## Définition

L’**ABAC**, ou contrôle d’accès fondé sur les attributs, prend une décision à partir de plusieurs informations appelées attributs.

Ces attributs peuvent concerner :

* l’utilisateur ;
* la ressource ;
* l’action ;
* le contexte.

```text
Décision =
attributs utilisateur
+ attributs ressource
+ action demandée
+ contexte
```

---

## Attributs de l’utilisateur

Exemples :

```text
user.id
user.role
user.siteId
user.department
user.region
user.clearanceLevel
user.certifications
user.employmentStatus
```

---

## Attributs de la ressource

Exemples :

```text
vehicle.siteId
route.status
invoice.amount
document.confidentialityLevel
temperatureReading.deviceId
collection.ownerId
```

---

## Attributs du contexte

Exemples :

```text
heure actuelle
adresse IP
localisation
type d’appareil
réseau utilisé
niveau de risque
jour de la semaine
état du système
```

---

## Attribut de l’action

L’action fait également partie de la décision.

Exemples :

```text
read
create
update
delete
validate
export
approve
```

---

## Exemple ABAC simple

Règle :

> Un responsable de site peut modifier un véhicule uniquement si le véhicule appartient à son site.

```typescript
const canUpdateVehicle =
  user.role === "SITE_MANAGER" &&
  user.siteId === vehicle.siteId;
```

La décision ne dépend pas uniquement du rôle.

Elle dépend aussi :

* du site de l’utilisateur ;
* du site du véhicule.

---

## Exemple ABAC métier

Règle :

> Un responsable peut valider une collecte si elle appartient à son site, si elle est terminée et si elle n’a pas déjà été facturée.

```typescript
const canValidateCollection =
  user.siteId === collection.siteId &&
  collection.status === "COMPLETED" &&
  collection.invoiceId === null;
```

On voit ici que l’autorisation dépend directement de l’état métier de la ressource.

---

## Exemple ABAC plus complet

```typescript
const canExportSensitiveReport =
  user.department === "MANAGEMENT" &&
  user.clearanceLevel >= report.requiredClearanceLevel &&
  request.ipAddress.startsWith("10.") &&
  currentTime.getHours() >= 7 &&
  currentTime.getHours() <= 20;
```

La décision dépend :

* du département ;
* du niveau d’habilitation ;
* de la classification du rapport ;
* du réseau utilisé ;
* de l’heure.

---

## Politique ABAC

Une règle ABAC peut être exprimée sous forme de politique.

```text
Autoriser l’action vehicle.update si :

user.siteId == vehicle.siteId
ET user.status == ACTIVE
ET vehicle.status != ARCHIVED
```

Exemple TypeScript :

```typescript
class UpdateVehiclePolicy {
  can(user: AuthenticatedUser, vehicle: Vehicle): boolean {
    return (
      user.status === "ACTIVE" &&
      user.siteId === vehicle.siteId &&
      vehicle.status !== "ARCHIVED"
    );
  }
}
```

---

## Avantages de l’ABAC

* très flexible ;
* adapté aux règles métier complexes ;
* permet des décisions contextuelles ;
* évite de multiplier les rôles ;
* permet une autorisation très fine ;
* adapté aux environnements multisites ou multi-entités.

---

## Limites de l’ABAC

* plus complexe à comprendre ;
* plus difficile à administrer ;
* risque de règles dispersées ;
* tests indispensables ;
* débogage parfois difficile ;
* peut créer une logique d’autorisation illisible si elle n’est pas structurée.

---

## Quand utiliser ABAC

L’ABAC est pertinent lorsque l’autorisation dépend :

* du propriétaire de la ressource ;
* du site ;
* de la région ;
* de l’état métier ;
* de l’heure ;
* du niveau de confidentialité ;
* de la relation entre l’utilisateur et la ressource ;
* de conditions dynamiques.

---

# Différence entre RBAC et ABAC

## RBAC

Le RBAC répond principalement à cette question :

> Quel rôle possède l’utilisateur ?

```text
L’utilisateur est ADMIN
    ↓
Il peut supprimer un véhicule
```

---

## ABAC

L’ABAC répond à une question plus riche :

> Les attributs de l’utilisateur, de la ressource et du contexte permettent-ils cette action ?

```text
L’utilisateur est responsable
ET le véhicule appartient à son site
ET le véhicule n’est pas archivé
    ↓
Il peut modifier le véhicule
```

---

## Comparaison

| Critère                | RBAC                       | ABAC                                  |
| ---------------------- | -------------------------- | ------------------------------------- |
| Base de la décision    | Rôle                       | Attributs                             |
| Complexité             | Faible à moyenne           | Moyenne à élevée                      |
| Lisibilité métier      | Bonne                      | Bonne si bien structurée              |
| Gestion administrative | Simple                     | Plus complexe                         |
| Règles contextuelles   | Limitée                    | Très adaptée                          |
| Multisite              | Possible avec portée       | Très adaptée                          |
| Risque principal       | Multiplication des rôles   | Multiplication des règles             |
| Cas typique            | Fonction dans l’entreprise | Relation dynamique avec une ressource |

---

# RBAC et ABAC ne s’opposent pas

Dans la pratique, on utilise souvent un modèle hybride.

Le RBAC sert à vérifier qu’un utilisateur possède une capacité générale.

L’ABAC vérifie ensuite si cette capacité peut être exercée dans le contexte précis.

Exemple :

```text
Étape 1 — RBAC
L’utilisateur possède vehicle.update

Étape 2 — ABAC
Le véhicule appartient à son site
ET il n’est pas archivé
```

En code :

```typescript
const canUpdateVehicle =
  user.permissions.includes("vehicle.update") &&
  user.siteId === vehicle.siteId &&
  vehicle.status !== "ARCHIVED";
```

C’est souvent la meilleure approche pour une application métier.

```text
RBAC
    ↓
Définit les grandes capacités

ABAC
    ↓
Applique les contraintes contextuelles et métier
```

---

# Permission, règle métier et autorisation

Il faut distinguer plusieurs niveaux.

## Permission technique

Exemple :

```text
vehicle.update
```

Elle signifie que l’utilisateur possède globalement la capacité de modifier un véhicule.

---

## Règle de périmètre

Exemple :

```text
L’utilisateur ne peut modifier que les véhicules de son site.
```

---

## Règle métier

Exemple :

```text
Un véhicule archivé ne peut plus être modifié.
```

Ces trois éléments peuvent être combinés :

```text
Autoriser si :

permission vehicle.update
ET véhicule dans le périmètre de l’utilisateur
ET véhicule non archivé
```

---

# Authentification dans une architecture hexagonale

## Principe

Dans une architecture hexagonale, le domaine ne doit pas dépendre directement :

* d’un framework d’authentification ;
* d’un cookie ;
* d’un JWT ;
* d’une session Better Auth ;
* d’un guard NestJS ;
* d’un fournisseur OAuth.

```text
Framework d’authentification
    ↓
Adaptateur
    ↓
Port applicatif
    ↓
Cas d’utilisation
```

Le cœur de l’application doit recevoir une identité déjà interprétée.

Exemple :

```typescript
type AuthenticatedUser = {
  id: string;
  email: string;
  roles: string[];
  permissions: string[];
  siteIds: string[];
};
```

---

## Adaptateur entrant

Le contrôleur, le guard ou le middleware récupère la session ou le jeton.

```text
Requête HTTP
    ↓
Guard / middleware
    ↓
Vérification de la session
    ↓
Création d’un AuthenticatedUser
    ↓
Appel du cas d’utilisation
```

Exemple :

```typescript
const currentUser: AuthenticatedUser = {
  id: session.user.id,
  email: session.user.email,
  roles: session.user.roles,
  permissions: session.user.permissions,
  siteIds: session.user.siteIds,
};

await updateVehicleUseCase.execute({
  actor: currentUser,
  vehicleId,
  data,
});
```

L’adaptateur transforme donc un objet propre au framework en objet compris par l’application.

---

## Port d’authentification

Si l’application doit elle-même vérifier ou charger une identité, elle peut dépendre d’un port.

```typescript
interface IdentityProvider {
  authenticate(accessToken: string): Promise<AuthenticatedUser>;
}
```

Les implémentations possibles peuvent être :

```text
BetterAuthIdentityProvider
KeycloakIdentityProvider
MicrosoftIdentityProvider
FakeIdentityProvider
```

Le cas d’utilisation dépend de l’interface, pas du fournisseur concret.

---

# Où placer les permissions dans une architecture hexagonale ?

Il n’existe pas une seule réponse valable pour toutes les permissions.

La bonne place dépend de la nature de la règle.

---

## Niveau 1 — Contrôleur, middleware ou guard

Le guard peut vérifier des règles très générales.

Exemples :

* l’utilisateur est authentifié ;
* l’utilisateur possède une permission globale ;
* l’utilisateur appartient à un groupe général ;
* la route est réservée aux administrateurs.

```typescript
@RequirePermission("vehicle.read")
@Get(":id")
getVehicle() {}
```

### Ce niveau est adapté pour

```text
Être connecté
Avoir une permission générale
Bloquer rapidement une requête
```

### Ce niveau n’est pas adapté pour

```text
Vérifier que le véhicule appartient au site de l’utilisateur
Vérifier que la tournée est encore modifiable
Vérifier qu’une facture est sous un certain seuil
```

Le guard ne possède généralement pas tout le contexte métier nécessaire.

---

## Niveau 2 — Couche application

La couche application orchestre le cas d’utilisation.

C’est souvent le meilleur endroit pour vérifier :

* les permissions nécessaires au cas d’utilisation ;
* le périmètre de l’utilisateur ;
* les politiques d’autorisation ;
* la relation entre l’acteur et la ressource.

Exemple :

```typescript
class UpdateVehicleUseCase {
  constructor(
    private readonly vehicleRepository: VehicleRepository,
    private readonly authorizationService: AuthorizationService,
  ) {}

  async execute(command: UpdateVehicleCommand): Promise<void> {
    const vehicle = await this.vehicleRepository.findById(
      command.vehicleId,
    );

    this.authorizationService.assertCan(
      command.actor,
      "vehicle.update",
      vehicle,
    );

    vehicle.update(command.data);

    await this.vehicleRepository.save(vehicle);
  }
}
```

La couche application connaît :

* l’acteur ;
* l’action ;
* la ressource ;
* le cas d’utilisation en cours.

Elle est donc bien placée pour prendre une décision d’autorisation.

---

## Niveau 3 — Domaine

Certaines règles ne sont pas réellement des permissions utilisateur.

Ce sont des invariants métier.

Exemple :

```text
Une tournée terminée ne peut plus être modifiée.
```

Cette règle doit être protégée dans le domaine.

```typescript
class Route {
  updateSchedule(schedule: Schedule): void {
    if (this.status === "COMPLETED") {
      throw new RouteAlreadyCompletedError();
    }

    this.schedule = schedule;
  }
}
```

Le domaine ne devrait pas nécessairement connaître :

```text
role = ADMIN
permission = route.update
JWT
session
HTTP
```

Mais il doit protéger les règles toujours vraies, quel que soit l’utilisateur.

---

## Répartition recommandée

```text
Guard / middleware
    ↓
Authentification et permissions globales simples

Couche application
    ↓
Autorisation du cas d’utilisation
RBAC, périmètre, politiques ABAC

Domaine
    ↓
Invariants métier
Règles toujours vraies
```

---

# Exemple complet

Règle :

> Un responsable de site peut modifier un camion de son site, sauf si ce camion est archivé.

Cette règle contient trois parties.

## Authentification

```text
L’utilisateur doit être connecté.
```

À vérifier dans un middleware ou un guard.

---

## Permission RBAC

```text
L’utilisateur doit posséder vehicle.update.
```

À vérifier dans un guard ou dans la couche application.

---

## Règle ABAC

```text
user.siteId == vehicle.siteId
```

À vérifier dans une politique d’autorisation de la couche application.

---

## Invariant métier

```text
vehicle.status != ARCHIVED
```

À protéger dans le domaine.

---

## Flux complet

```text
Requête HTTP
    ↓
Guard d’authentification
    ↓
Utilisateur authentifié
    ↓
Cas d’utilisation UpdateVehicle
    ↓
Vérification de vehicle.update
    ↓
Chargement du véhicule
    ↓
Vérification du site
    ↓
Appel de vehicle.update()
    ↓
Le domaine refuse si le véhicule est archivé
    ↓
Sauvegarde
```

---

# Exemple TypeScript

## Cas d’utilisation

```typescript
type UpdateVehicleCommand = {
  actor: AuthenticatedUser;
  vehicleId: string;
  data: {
    registration?: string;
    label?: string;
  };
};

class UpdateVehicleUseCase {
  constructor(
    private readonly vehicleRepository: VehicleRepository,
    private readonly vehiclePolicy: VehiclePolicy,
  ) {}

  async execute(command: UpdateVehicleCommand): Promise<void> {
    const vehicle = await this.vehicleRepository.findById(
      command.vehicleId,
    );

    if (!vehicle) {
      throw new VehicleNotFoundError();
    }

    this.vehiclePolicy.assertCanUpdate(command.actor, vehicle);

    vehicle.update(command.data);

    await this.vehicleRepository.save(vehicle);
  }
}
```

---

## Politique d’autorisation

```typescript
class VehiclePolicy {
  assertCanUpdate(
    actor: AuthenticatedUser,
    vehicle: Vehicle,
  ): void {
    const hasPermission =
      actor.permissions.includes("vehicle.update");

    const belongsToAllowedSite =
      actor.siteIds.includes(vehicle.siteId);

    if (!hasPermission || !belongsToAllowedSite) {
      throw new ForbiddenError();
    }
  }
}
```

---

## Domaine

```typescript
class Vehicle {
  constructor(
    readonly id: string,
    readonly siteId: string,
    private status: VehicleStatus,
    private registration: string,
  ) {}

  update(data: {
    registration?: string;
  }): void {
    if (this.status === "ARCHIVED") {
      throw new ArchivedVehicleCannotBeUpdatedError();
    }

    if (data.registration) {
      this.registration = data.registration;
    }
  }
}
```

La politique vérifie :

```text
Qui peut agir ?
```

Le domaine vérifie :

```text
L’action est-elle métierment valide ?
```

---

# Clean Architecture

Dans une Clean Architecture, les mêmes principes s’appliquent.

```text
Frameworks et drivers
    ↓
Interface adapters
    ↓
Use cases
    ↓
Entities
```

---

## Frameworks et drivers

On y trouve :

* Better Auth ;
* Keycloak ;
* OAuth ;
* cookies ;
* JWT ;
* NestJS ;
* middleware ;
* guards ;
* base de données d’authentification.

Ces éléments sont des détails techniques externes.

---

## Interface adapters

Cette couche transforme les informations techniques en objets applicatifs.

Exemple :

```text
Session Better Auth
    ↓
AuthenticatedUser
```

Elle peut contenir :

* contrôleurs ;
* guards ;
* presenters ;
* adaptateurs d’identité ;
* mapping entre session et acteur applicatif.

---

## Use cases

La couche des cas d’utilisation applique :

* les permissions nécessaires ;
* les politiques d’autorisation ;
* les restrictions de périmètre ;
* la coordination entre utilisateur et ressource.

```typescript
authorizationService.assertCan(
  actor,
  "collection.validate",
  collection,
);
```

---

## Entities

Les entités protègent :

* les invariants métier ;
* les transitions autorisées ;
* les règles qui doivent toujours être respectées.

Exemple :

```text
Une facture payée ne peut pas être supprimée.
```

Cette règle reste vraie même pour un administrateur.

---

# Service d’autorisation

Une application peut centraliser une partie des décisions dans un service.

```typescript
interface AuthorizationService {
  can(
    actor: AuthenticatedUser,
    action: Permission,
    resource?: unknown,
  ): boolean;

  assertCan(
    actor: AuthenticatedUser,
    action: Permission,
    resource?: unknown,
  ): void;
}
```

Exemple :

```typescript
class DefaultAuthorizationService
  implements AuthorizationService
{
  can(
    actor: AuthenticatedUser,
    action: Permission,
    resource?: unknown,
  ): boolean {
    return actor.permissions.includes(action);
  }

  assertCan(
    actor: AuthenticatedUser,
    action: Permission,
    resource?: unknown,
  ): void {
    if (!this.can(actor, action, resource)) {
      throw new ForbiddenError();
    }
  }
}
```

---

# Politiques spécialisées

Pour des règles plus riches, il est préférable de créer des politiques par domaine.

```text
VehiclePolicy
RoutePolicy
InvoicePolicy
UserPolicy
TemperaturePolicy
```

Exemple :

```typescript
class InvoicePolicy {
  canApprove(
    actor: AuthenticatedUser,
    invoice: Invoice,
  ): boolean {
    return (
      actor.permissions.includes("invoice.approve") &&
      actor.siteIds.includes(invoice.siteId) &&
      invoice.amount <= actor.approvalLimit
    );
  }
}
```

Cette approche évite un énorme service central contenant toutes les règles de l’application.

---

# Policy pattern

Le **Policy Pattern** consiste à encapsuler une règle de décision dans un objet dédié.

```typescript
interface Policy<TActor, TResource> {
  allows(actor: TActor, resource: TResource): boolean;
}
```

Exemple :

```typescript
class CanUpdateVehiclePolicy
  implements Policy<AuthenticatedUser, Vehicle>
{
  allows(
    actor: AuthenticatedUser,
    vehicle: Vehicle,
  ): boolean {
    return (
      actor.permissions.includes("vehicle.update") &&
      actor.siteIds.includes(vehicle.siteId)
    );
  }
}
```

Utilisation :

```typescript
if (!policy.allows(actor, vehicle)) {
  throw new ForbiddenError();
}
```

### Avantages

* règles isolées ;
* facilement testables ;
* réutilisables ;
* lisibilité ;
* adaptation naturelle à l’ABAC.

---

# Specification pattern

Le **Specification Pattern** permet de composer plusieurs règles.

```typescript
interface Specification<T> {
  isSatisfiedBy(candidate: T): boolean;
}
```

Exemple conceptuel :

```text
HasPermission(vehicle.update)
ET BelongsToSameSite
ET UserIsActive
```

En code :

```typescript
const specification =
  new HasPermission("vehicle.update")
    .and(new BelongsToSameSite())
    .and(new UserIsActive());
```

Cette approche est utile pour des politiques complexes, mais elle peut être excessive dans une petite application.

---

# Où stocker les rôles et permissions ?

Les rôles et permissions peuvent être stockés en base de données.

Structure fréquente :

```text
User
Role
Permission
UserRole
RolePermission
```

Dans un contexte multisite :

```text
UserRole
- userId
- roleId
- siteId
```

---

## Permissions codées en dur

Les identifiants de permissions devraient généralement être définis dans le code.

```typescript
export const Permissions = {
  VehicleRead: "vehicle.read",
  VehicleUpdate: "vehicle.update",
  VehicleDelete: "vehicle.delete",
  CollectionValidate: "collection.validate",
} as const;
```

Cela évite les fautes de frappe.

```typescript
Permissions.VehicleUpdate
```

plutôt que :

```typescript
"vehicule.updtae"
```

---

## Permissions en base

La base peut contenir :

```text
vehicle.read
vehicle.update
vehicle.delete
```

Elle permet d’associer dynamiquement les permissions aux rôles.

Mais les permissions disponibles restent idéalement définies et contrôlées par l’application.

Un administrateur ne devrait pas inventer arbitrairement une permission inconnue du code.

---

# Permissions directes par utilisateur

On peut permettre des exceptions individuelles.

```text
UserPermission
- userId
- permissionId
- effect
```

L’effet peut être :

```text
ALLOW
DENY
```

Exemple :

```text
Le rôle COMPTABLE ne possède pas invoice.delete

Mais Marie reçoit exceptionnellement :
invoice.delete
```

### Risque

Les exceptions individuelles compliquent fortement la compréhension du système.

Il devient difficile de répondre à :

> Pourquoi cet utilisateur possède-t-il cette permission ?

Il est préférable de limiter ces exceptions ou de créer un rôle adapté.

---

# Deny explicite

Certains systèmes permettent de refuser explicitement une permission.

```text
Rôle ADMIN
    ↓
vehicle.delete autorisé

Exception utilisateur
    ↓
vehicle.delete refusé
```

Il faut alors définir une priorité claire.

Exemple :

```text
DENY explicite
    >
ALLOW explicite
    >
permission du rôle
    >
refus par défaut
```

Mais cette logique peut rapidement devenir difficile à maintenir.

---

# Refus par défaut

Le principe recommandé est :

> Tout ce qui n’est pas explicitement autorisé est interdit.

```text
Permission absente
    ↓
Accès refusé
```

Il ne faut pas écrire :

```typescript
if (user) {
  allow();
}
```

mais plutôt :

```typescript
if (!authorizationService.can(user, permission)) {
  throw new ForbiddenError();
}
```

---

# 401 et 403

Ces deux erreurs HTTP sont différentes.

## 401 Unauthorized

Malgré son nom, cette erreur signifie généralement :

```text
Utilisateur non authentifié
```

Exemples :

* session absente ;
* session expirée ;
* jeton invalide ;
* jeton manquant.

---

## 403 Forbidden

Cette erreur signifie :

```text
Utilisateur authentifié mais non autorisé
```

Exemple :

```text
L’utilisateur est connecté,
mais il ne possède pas vehicle.delete.
```

---

# Authentification des objets connectés

Les objets connectés doivent également être authentifiés.

Exemple :

```text
Capteur de température
    ↓
Certificat ou identifiant propre
    ↓
Broker MQTT
```

Chaque appareil devrait disposer de sa propre identité.

À éviter :

```text
Tous les capteurs partagent le même mot de passe.
```

À privilégier :

```text
device-001 → certificat 001
device-002 → certificat 002
device-003 → certificat 003
```

Cela permet de révoquer un seul appareil compromis.

---

## Permissions MQTT

Un appareil ne devrait pouvoir publier que sur ses propres topics.

```text
device-042 peut publier :
vehicles/TRUCK-042/temperature

device-042 ne peut pas publier :
vehicles/TRUCK-043/temperature
```

Le broker applique des règles d’accès appelées souvent **ACL**.

```text
Identité
    ↓
Topics autorisés
    ↓
Actions autorisées
```

Exemples d’actions :

```text
publish
subscribe
```

---

# Authentification entre services

Deux services peuvent communiquer avec :

* une clé d’API ;
* OAuth 2.0 client credentials ;
* un certificat client ;
* un jeton signé ;
* une identité réseau ;
* mTLS.

---

## mTLS

Avec le **mutual TLS**, le serveur présente un certificat au client, mais le client présente également un certificat au serveur.

```text
Client vérifie le serveur
ET
Serveur vérifie le client
```

C’est adapté aux communications sensibles entre services ou appareils.

---

# Erreurs fréquentes

## Vérifier uniquement les permissions dans le frontend

Cacher un bouton ne protège pas une API.

```text
Frontend
    ↓
Masque le bouton Supprimer
```

Un utilisateur pourrait appeler directement l’API.

La permission doit toujours être vérifiée côté serveur.

Le frontend peut masquer ou désactiver les actions pour améliorer l’expérience utilisateur, mais cela ne constitue pas une protection.

---

## Mettre toute l’autorisation dans les contrôleurs

Exemple à éviter :

```typescript
if (
  user.role === "ADMIN" ||
  (
    user.role === "MANAGER" &&
    user.siteId === vehicle.siteId &&
    vehicle.status !== "ARCHIVED"
  )
) {
  // ...
}
```

Le contrôleur devient :

* difficile à lire ;
* difficile à tester ;
* dépendant du métier ;
* rempli de duplications.

Il vaut mieux déléguer à une politique ou au cas d’utilisation.

---

## Mettre toute l’autorisation dans les guards

Un guard est pratique pour des règles générales.

Mais il est souvent mal adapté aux règles dépendant d’une entité chargée depuis la base.

Exemple :

```text
L’utilisateur peut modifier ce véhicule précis.
```

Pour répondre, il faut souvent charger le véhicule, connaître son site et son statut.

Cette logique appartient davantage au cas d’utilisation ou à une politique d’autorisation.

---

## Utiliser uniquement les rôles

Code fragile :

```typescript
if (user.role === "ADMIN") {
  // autoriser
}
```

Si les rôles évoluent, il faut modifier de nombreuses parties du code.

Il est préférable de vérifier une capacité :

```typescript
if (user.permissions.includes("vehicle.delete")) {
  // autoriser
}
```

Le rôle reste une manière d’attribuer les permissions.

---

## Placer les permissions dans le domaine de manière technique

Le domaine ne devrait généralement pas dépendre directement de chaînes comme :

```text
ADMIN
vehicle.update
JWT
BetterAuthSession
```

Cela couplerait le métier à un système d’autorisation particulier.

Le domaine peut toutefois connaître des concepts métier pertinents.

Exemple :

```text
Un validateur habilité
Un responsable affecté au site
Un approbateur disposant d’un plafond
```

Si ces notions font réellement partie du métier, elles peuvent être modélisées dans le domaine.

---

# Recommandation pour Tubert

Pour une application interne de gestion, une approche hybride est adaptée.

## RBAC pour les capacités générales

Exemples de rôles :

```text
ADMINISTRATEUR
RESPONSABLE_SITE
EXPLOITANT
CHAUFFEUR
COMPTABLE
LECTEUR
```

Exemples de permissions :

```text
vehicle.read
vehicle.update
route.read
route.plan
collection.validate
invoice.read
invoice.generate
user.invite
```

---

## ABAC pour le contexte

Exemples de règles :

```text
L’utilisateur ne voit que les sites auxquels il est affecté.

Un chauffeur ne voit que ses tournées.

Un responsable ne modifie que les véhicules de son site.

Un comptable ne valide qu’une facture de son entité.

Un utilisateur ne peut exporter que les données de son périmètre.
```

---

## Domaine pour les invariants

Exemples :

```text
Une tournée terminée ne peut plus être supprimée.

Une facture payée ne peut plus être modifiée.

Une mesure de température déjà certifiée ne peut plus être altérée.

Un véhicule archivé ne peut pas être affecté à une nouvelle tournée.
```

---

# Architecture recommandée

```text
Infrastructure
├── Better Auth
├── OAuth
├── sessions
├── JWT
├── base des rôles
└── fournisseur d’identité

Adapters entrants
├── middleware d’authentification
├── guards
├── contrôleurs
└── mapping Session → AuthenticatedUser

Application
├── cas d’utilisation
├── PermissionService
├── policies
├── contrôle du périmètre
└── décisions RBAC + ABAC

Domaine
├── entités
├── value objects
├── invariants métier
└── transitions d’état
```

---

# Exemple de structure de dossiers

```text
src/
├── domain/
│   ├── vehicle/
│   │   ├── Vehicle.ts
│   │   ├── VehicleStatus.ts
│   │   └── errors/
│   └── shared/
│
├── application/
│   ├── auth/
│   │   ├── AuthenticatedUser.ts
│   │   ├── Permission.ts
│   │   └── AuthorizationService.ts
│   │
│   ├── vehicle/
│   │   ├── UpdateVehicleUseCase.ts
│   │   └── VehiclePolicy.ts
│   │
│   └── ports/
│       ├── IdentityProvider.ts
│       └── VehicleRepository.ts
│
├── infrastructure/
│   ├── auth/
│   │   ├── BetterAuthIdentityAdapter.ts
│   │   └── DatabaseAuthorizationService.ts
│   │
│   └── database/
│
└── interfaces/
    └── http/
        ├── guards/
        ├── decorators/
        └── controllers/
```

---

# Synthèse

## Authentification

```text
Qui es-tu ?
```

Elle repose sur :

* session ;
* mot de passe ;
* OAuth ;
* OIDC ;
* JWT ;
* certificat ;
* MFA.

---

## Autorisation

```text
Que peux-tu faire ?
```

Elle repose sur :

* rôles ;
* permissions ;
* attributs ;
* politiques ;
* règles métier.

---

## RBAC

```text
Utilisateur → rôle → permissions
```

Adapté aux capacités générales et aux fonctions dans l’entreprise.

---

## ABAC

```text
Utilisateur + ressource + action + contexte
```

Adapté aux règles fines, dynamiques et liées au périmètre.

---

## Architecture hexagonale ou Clean Architecture

```text
Guard
    ↓
Authentification et filtre simple

Cas d’utilisation
    ↓
Autorisation RBAC et ABAC

Domaine
    ↓
Invariants métier
```

La règle générale est donc :

> L’infrastructure identifie l’utilisateur, la couche application décide s’il peut exécuter le cas d’utilisation, et le domaine protège les règles métier qui doivent toujours être respectées.
