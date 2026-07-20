Voici une fiche Markdown prête à être intégrée à ta documentation.

# Les principaux protocoles et modes de communication

Dans une application professionnelle, les données peuvent être échangées de nombreuses façons : appel d’API, messages asynchrones, connexion temps réel, fichiers, réseau industriel ou communication avec des objets connectés.

Il est important de distinguer :

* le **protocole de transport**, comme TCP ou UDP ;
* le **protocole applicatif**, comme HTTP, MQTT ou SOAP ;
* le **style d’architecture**, comme REST ;
* le **format de données**, comme JSON, XML ou CSV ;
* le **support physique ou réseau**, comme Ethernet, Wi-Fi, 4G ou Bluetooth.

Une même communication combine généralement plusieurs de ces éléments.

```text
Support réseau
    ↓
Protocole de transport
    ↓
Protocole applicatif
    ↓
Format des données
```

Exemple :

```text
Wi-Fi
└── TCP
    └── HTTPS
        └── API REST
            └── JSON
```

---

## HTTP et HTTPS

### Définition

**HTTP**, pour *HyperText Transfer Protocol*, est le protocole principalement utilisé pour les échanges sur le Web.

Il suit généralement un modèle requête-réponse :

```text
Client ── requête HTTP ──> Serveur
Client <── réponse HTTP ── Serveur
```

Exemple :

```http
GET /api/vehicles/42 HTTP/1.1
Host: api.example.com
```

Le serveur répond avec un statut et éventuellement des données :

```http
HTTP/1.1 200 OK
Content-Type: application/json
```

```json
{
  "id": 42,
  "registration": "AB-123-CD"
}
```

### HTTPS

**HTTPS** correspond à HTTP sécurisé par **TLS**.

TLS apporte notamment :

* le chiffrement des données ;
* l’authentification du serveur ;
* la protection contre la modification des échanges.

```text
HTTP  : données potentiellement lisibles sur le réseau
HTTPS : données chiffrées
```

### Ports habituels

```text
HTTP  : port 80
HTTPS : port 443
```

### Cas d’utilisation

* sites Web ;
* API REST ;
* API GraphQL ;
* webhooks ;
* téléchargement de fichiers ;
* communications entre applications.

---

## REST

### Définition

**REST**, pour *Representational State Transfer*, est un style d’architecture permettant de construire des API autour de ressources.

REST n’est pas strictement un protocole. Une API REST utilise généralement HTTP ou HTTPS.

Une ressource est identifiée par une URL :

```text
/api/vehicles
/api/vehicles/42
/api/vehicles/42/temperature-readings
```

Les opérations utilisent les méthodes HTTP.

| Méthode  | Utilisation habituelle                   |
| -------- | ---------------------------------------- |
| `GET`    | Lire une ressource                       |
| `POST`   | Créer une ressource ou lancer une action |
| `PUT`    | Remplacer complètement une ressource     |
| `PATCH`  | Modifier partiellement une ressource     |
| `DELETE` | Supprimer une ressource                  |

### Exemple

```http
POST /api/temperature-readings
Content-Type: application/json
Authorization: Bearer <token>
```

```json
{
  "vehicleId": "TRUCK-042",
  "temperature": 3.7,
  "measuredAt": "2026-07-20T08:30:00Z"
}
```

### Avantages

* simple à comprendre ;
* très répandu ;
* facilement utilisable depuis un navigateur, un backend ou une application mobile ;
* compatible avec de nombreux outils ;
* souvent documenté avec OpenAPI et Swagger.

### Limites

* les conventions peuvent varier selon les API ;
* les appels peuvent devenir nombreux ;
* moins adapté que certains protocoles aux échanges continus ou très fréquents ;
* le client doit souvent interroger régulièrement le serveur pour détecter les changements.

### Cas d’utilisation

* API métier ;
* application Web ou mobile ;
* intégration avec un logiciel tiers ;
* récupération de données depuis une plateforme fournisseur.

---

## SOAP

### Définition

**SOAP**, pour *Simple Object Access Protocol*, est un protocole d’échange de messages structurés entre applications.

Les messages SOAP utilisent généralement le format XML.

```xml
<soap:Envelope
  xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <GetVehicle>
      <VehicleId>42</VehicleId>
    </GetVehicle>
  </soap:Body>
</soap:Envelope>
```

SOAP utilise souvent HTTP comme moyen de transport, mais il ne se limite pas théoriquement à HTTP.

### WSDL

Un service SOAP est généralement décrit par un fichier **WSDL**, pour *Web Services Description Language*.

Le WSDL indique précisément :

* les opérations disponibles ;
* les paramètres attendus ;
* les types de données ;
* les réponses ;
* les erreurs possibles ;
* l’adresse du service.

Le WSDL constitue donc un contrat technique très formel.

### Avantages

* contrat strict ;
* typage précis ;
* outils de génération automatique de clients ;
* gestion standardisée de certaines erreurs et fonctionnalités de sécurité ;
* fréquent dans les systèmes d’entreprise historiques.

### Limites

* XML verbeux ;
* messages plus lourds ;
* intégration généralement plus complexe qu’une API REST ;
* documentation parfois difficile à lire manuellement ;
* forte dépendance aux outils de génération.

### Cas d’utilisation

* logiciels de gestion historiques ;
* ERP ;
* systèmes bancaires ;
* logiciels de transport, de logistique ou de collecte ;
* intégration avec certains services Kerlog ou systèmes similaires.

---

## GraphQL

### Définition

**GraphQL** est un langage de requête et un système d’API permettant au client de demander précisément les données dont il a besoin.

Contrairement à REST, les opérations passent souvent par une seule URL :

```text
POST /graphql
```

Exemple de requête :

```graphql
query {
  vehicle(id: "42") {
    registration
    latestTemperature {
      value
      measuredAt
    }
  }
}
```

Réponse :

```json
{
  "data": {
    "vehicle": {
      "registration": "AB-123-CD",
      "latestTemperature": {
        "value": 3.7,
        "measuredAt": "2026-07-20T08:30:00Z"
      }
    }
  }
}
```

### Avantages

* le client choisit précisément les champs retournés ;
* évite parfois de multiplier les appels ;
* schéma fortement typé ;
* documentation et exploration facilitées par le schéma.

### Limites

* mise en cache HTTP moins naturelle ;
* gestion des autorisations parfois complexe ;
* risque de requêtes coûteuses ;
* souvent inutile pour une API métier simple ;
* nécessite une surveillance de la profondeur et de la complexité des requêtes.

### Cas d’utilisation

* interfaces riches consommant de nombreuses ressources liées ;
* applications ayant plusieurs clients avec des besoins différents ;
* agrégation de données provenant de plusieurs services.

---

## gRPC

### Définition

**gRPC** est un système de communication performant entre applications.

Il repose généralement sur :

* HTTP/2 ;
* des contrats définis avec Protocol Buffers ;
* des messages binaires compacts ;
* la génération automatique du code client et serveur.

Exemple de contrat :

```proto
service VehicleService {
  rpc GetVehicle(GetVehicleRequest) returns (VehicleResponse);
}

message GetVehicleRequest {
  string id = 1;
}

message VehicleResponse {
  string id = 1;
  string registration = 2;
}
```

### Avantages

* très performant ;
* messages compacts ;
* contrat fortement typé ;
* génération automatique des clients ;
* prise en charge des flux continus ;
* adapté aux communications internes entre services.

### Limites

* moins simple à tester manuellement qu’une API REST ;
* données binaires moins lisibles ;
* prise en charge directe limitée dans les navigateurs sans adaptation ;
* souvent excessif pour une petite application monolithique.

### Cas d’utilisation

* communication entre microservices ;
* systèmes nécessitant beaucoup de performances ;
* échanges fréquents entre backends ;
* streaming bidirectionnel.

---

## Webhook

### Définition

Un **webhook** est un mécanisme par lequel une application appelle automatiquement une autre application lorsqu’un événement se produit.

Ce n’est pas un protocole indépendant : un webhook utilise généralement une requête HTTP ou HTTPS.

```text
Événement chez le fournisseur
            ↓
POST envoyé vers l’application cliente
            ↓
Traitement de l’événement
```

Exemple :

```http
POST /webhooks/temperature-alert
Content-Type: application/json
X-Signature: ...
```

```json
{
  "deviceId": "TRUCK-042",
  "temperature": 9.2,
  "event": "TEMPERATURE_THRESHOLD_EXCEEDED"
}
```

### Avantages

* réception presque immédiate ;
* évite d’interroger constamment le fournisseur ;
* simple à mettre en place ;
* adapté aux notifications d’événements.

### Limites

* le serveur destinataire doit être accessible ;
* nécessité de vérifier l’authenticité de l’appel ;
* il faut gérer les doublons et les nouvelles tentatives ;
* l’ordre des événements n’est pas toujours garanti ;
* une indisponibilité temporaire doit être anticipée.

### Précautions

Un webhook doit généralement comporter :

* une signature cryptographique ;
* une date ou un délai de validité ;
* un identifiant unique d’événement ;
* une protection contre les rejeux ;
* une gestion idempotente ;
* une journalisation.

### Cas d’utilisation

* confirmation de paiement ;
* notification de nouvelle mesure ;
* alerte de température ;
* changement d’état d’une tournée ;
* synchronisation avec un logiciel tiers.

---

## WebSocket

### Définition

**WebSocket** établit une connexion persistante et bidirectionnelle entre un client et un serveur.

```text
Client <====================> Serveur
        connexion permanente
```

Une fois la connexion ouverte, le client et le serveur peuvent envoyer des messages à tout moment.

### Avantages

* communication en temps réel ;
* communication bidirectionnelle ;
* évite d’ouvrir une nouvelle requête HTTP pour chaque message ;
* faible latence.

### Limites

* gestion des reconnexions ;
* consommation de connexions serveur ;
* mise à l’échelle plus complexe ;
* nécessité de gérer les connexions interrompues ;
* pas idéal pour garantir durablement la livraison d’un événement.

### Cas d’utilisation

* tableau de bord en temps réel ;
* messagerie instantanée ;
* suivi en direct d’un véhicule ;
* affichage immédiat des nouvelles températures ;
* notifications interactives.

---

## Server-Sent Events

### Définition

Les **Server-Sent Events**, ou **SSE**, permettent au serveur d’envoyer continuellement des événements vers un navigateur par une connexion HTTP persistante.

La communication est unidirectionnelle :

```text
Client <──────── événements ──────── Serveur
```

Le client ouvre la connexion, puis le serveur lui envoie les nouvelles informations.

Exemple :

```text
event: temperature
data: {"vehicleId":"42","value":3.7}
```

### Avantages

* simple à utiliser dans un navigateur ;
* repose sur HTTP ;
* reconnexion automatique généralement prise en charge ;
* bien adapté aux notifications serveur vers client.

### Limites

* uniquement du serveur vers le client ;
* moins adapté aux échanges bidirectionnels ;
* pas destiné aux communications directes entre objets IoT et serveurs.

### Cas d’utilisation

* tableau de bord actualisé en direct ;
* progression d’un traitement ;
* flux de notifications ;
* suivi de l’état d’une opération.

---

## MQTT

### Définition

**MQTT**, pour *Message Queuing Telemetry Transport*, est un protocole de messagerie léger, très utilisé dans l’IoT.

Il suit un modèle publication-abonnement.

```text
Producteur
    ↓ publie
Broker MQTT
    ↓ distribue
Consommateurs abonnés
```

Les messages sont envoyés dans des canaux appelés **topics**.

Exemple :

```text
vehicles/TRUCK-042/temperature
```

Message :

```json
{
  "value": 3.7,
  "measuredAt": "2026-07-20T08:30:00Z"
}
```

Une application peut s’abonner à :

```text
vehicles/+/temperature
```

Le symbole `+` représente ici un niveau variable du topic.

### Broker MQTT

Le **broker** est le serveur central qui :

* reçoit les messages ;
* vérifie les autorisations ;
* distribue les messages aux abonnés ;
* peut conserver certains messages ;
* gère les connexions des clients.

Exemples de brokers :

* Mosquitto ;
* EMQX ;
* HiveMQ ;
* AWS IoT Core.

### Qualité de service

MQTT définit plusieurs niveaux de qualité de service.

| QoS | Signification                                                        |
| --- | -------------------------------------------------------------------- |
| `0` | Le message est envoyé au mieux, sans confirmation                    |
| `1` | Le message est livré au moins une fois                               |
| `2` | Le message est livré exactement une fois au niveau du protocole MQTT |

Avec le QoS 1, des doublons sont possibles. L’application doit donc savoir les détecter.

### Avantages

* léger ;
* adapté aux petits appareils ;
* faible consommation de bande passante ;
* efficace sur les réseaux instables ;
* communication asynchrone ;
* adapté à de nombreux capteurs.

### Limites

* nécessite un broker ;
* configuration des droits parfois complexe ;
* MQTT seul ne chiffre pas les messages ;
* il faut ajouter TLS et une authentification ;
* le stockage métier doit être géré séparément.

### Sécurité

MQTT devrait être utilisé avec :

```text
MQTT non chiffré : port 1883
MQTT avec TLS    : port 8883
```

Il faut également prévoir :

* une identité par appareil ;
* des droits précis par topic ;
* des certificats ou secrets sécurisés ;
* la révocation des appareils compromis ;
* la rotation des identifiants.

### Cas d’utilisation

* capteurs de température ;
* télémétrie de véhicules ;
* machines industrielles ;
* systèmes de géolocalisation ;
* objets connectés.

---

## AMQP

### Définition

**AMQP**, pour *Advanced Message Queuing Protocol*, est un protocole de messagerie destiné aux échanges fiables entre applications.

Il est notamment associé à RabbitMQ.

```text
Producteur
    ↓
Exchange
    ↓
File de messages
    ↓
Consommateur
```

Les messages peuvent être conservés dans une file jusqu’à ce qu’un consommateur les traite.

### Avantages

* livraison fiable ;
* accusés de réception ;
* files persistantes ;
* routage puissant ;
* gestion des erreurs et des nouvelles tentatives ;
* adapté aux traitements asynchrones.

### Limites

* plus complexe que MQTT ;
* plus lourd pour de petits objets connectés ;
* nécessite une infrastructure de messagerie ;
* surveillance et exploitation supplémentaires.

### Cas d’utilisation

* tâches asynchrones ;
* génération de documents ;
* traitement d’imports ;
* envoi d’e-mails ;
* communication entre services ;
* intégration de systèmes métier.

### Différence avec MQTT

```text
MQTT : optimisé pour les appareils et la télémétrie
AMQP : optimisé pour les échanges fiables entre applications
```

---

## Apache Kafka

### Définition

**Kafka** est une plateforme distribuée de diffusion et de conservation d’événements.

Les événements sont écrits dans des **topics** et conservés pendant une durée déterminée.

```text
Producteurs → Topic Kafka → Consommateurs
```

Contrairement à une file classique, la consommation d’un événement ne le supprime pas immédiatement. Plusieurs consommateurs peuvent le relire indépendamment.

### Avantages

* très grand volume d’événements ;
* conservation et relecture ;
* forte capacité de traitement ;
* adapté à l’architecture événementielle ;
* plusieurs groupes de consommateurs indépendants.

### Limites

* infrastructure complexe ;
* exploitation plus coûteuse ;
* peu adaptée à une petite application ;
* nécessite une bonne maîtrise des partitions, offsets et garanties de livraison.

### Cas d’utilisation

* très gros volumes de télémétrie ;
* centralisation des événements métier ;
* analyse en temps réel ;
* synchronisation de nombreux systèmes ;
* journal d’événements à grande échelle.

---

## CoAP

### Définition

**CoAP**, pour *Constrained Application Protocol*, est un protocole destiné aux appareils très limités en puissance, en mémoire ou en énergie.

Il ressemble conceptuellement à HTTP, mais fonctionne généralement au-dessus d’UDP.

Il propose notamment des opérations similaires à :

```text
GET
POST
PUT
DELETE
```

### Avantages

* très léger ;
* adapté aux microcontrôleurs ;
* faible consommation de ressources ;
* efficace pour les petits messages.

### Limites

* moins courant dans les applications Web classiques ;
* nécessite souvent une passerelle vers HTTP ou MQTT ;
* sécurité et fiabilité à configurer soigneusement ;
* UDP ne garantit pas naturellement la livraison.

### Cas d’utilisation

* petits capteurs ;
* bâtiments connectés ;
* équipements fonctionnant sur batterie ;
* réseaux à faible bande passante.

---

## TCP

### Définition

**TCP**, pour *Transmission Control Protocol*, est un protocole de transport fiable.

Il garantit notamment :

* la remise des données dans l’ordre ;
* la détection de certaines pertes ;
* la retransmission des paquets manquants ;
* le contrôle de la connexion.

Une connexion TCP commence généralement par un échange appelé **three-way handshake** :

```text
Client → Serveur : SYN
Client ← Serveur : SYN-ACK
Client → Serveur : ACK
```

### Avantages

* fiable ;
* ordonné ;
* largement utilisé ;
* adapté aux échanges où aucune donnée ne doit être perdue silencieusement.

### Limites

* plus lourd qu’UDP ;
* nécessite une connexion ;
* des pertes réseau peuvent ralentir l’ensemble du flux.

### Protocoles utilisant généralement TCP

* HTTP/1.1 ;
* HTTP/2 ;
* HTTPS ;
* MQTT ;
* SOAP sur HTTP ;
* FTP ;
* SSH ;
* SMTP.

---

## UDP

### Définition

**UDP**, pour *User Datagram Protocol*, est un protocole de transport sans connexion.

Il envoie des datagrammes sans garantir :

* leur livraison ;
* leur ordre ;
* l’absence de doublons.

```text
Émetteur ── message ──> Destinataire
```

L’émetteur n’attend pas nécessairement de confirmation.

### Avantages

* rapide ;
* faible surcharge ;
* adapté aux messages courts ;
* adapté aux situations où la rapidité est plus importante que la livraison parfaite.

### Limites

* messages potentiellement perdus ;
* ordre non garanti ;
* fiabilité à gérer au niveau applicatif si nécessaire.

### Cas d’utilisation

* DNS ;
* streaming ;
* voix sur IP ;
* jeux en ligne ;
* CoAP ;
* certains protocoles industriels ;
* QUIC, qui ajoute ses propres mécanismes de fiabilité au-dessus d’UDP.

---

## FTP

### Définition

**FTP**, pour *File Transfer Protocol*, est un protocole de transfert de fichiers.

Il permet notamment :

* l’envoi de fichiers ;
* le téléchargement ;
* la navigation dans des répertoires distants.

### Limite principale

FTP classique ne chiffre généralement pas correctement les identifiants et les données.

Il est donc déconseillé pour des données sensibles.

---

## FTPS

### Définition

**FTPS** correspond à FTP sécurisé par TLS.

Il conserve le fonctionnement général de FTP, mais ajoute une couche de chiffrement.

### Attention

FTPS et SFTP sont deux protocoles différents.

---

## SFTP

### Définition

**SFTP**, pour *SSH File Transfer Protocol*, permet de transférer des fichiers à travers une connexion SSH sécurisée.

```text
Client ── connexion SSH chiffrée ──> Serveur SFTP
```

### Avantages

* chiffrement ;
* authentification par mot de passe ou clé SSH ;
* largement utilisé dans les échanges interentreprises ;
* automatisable.

### Limites

* échange souvent différé plutôt qu’en temps réel ;
* nécessité de gérer les fichiers déjà importés ;
* traitement des fichiers partiels ou mal formés ;
* format parfois peu documenté.

### Cas d’utilisation

* dépôt quotidien de fichiers CSV ;
* export depuis un logiciel historique ;
* échange comptable ;
* échange avec un prestataire ;
* intégration ne disposant pas d’API.

---

## Échange de fichiers CSV, XML ou JSON

Toutes les intégrations ne passent pas par une API.

Un logiciel peut déposer périodiquement un fichier :

```text
export_vehicles_2026-07-20.csv
```

Exemple CSV :

```csv
vehicle_id,temperature,measured_at
TRUCK-042,3.7,2026-07-20T08:30:00Z
TRUCK-043,4.1,2026-07-20T08:30:00Z
```

### Avantages

* simple ;
* compatible avec les systèmes anciens ;
* facile à inspecter manuellement ;
* adapté aux traitements par lot.

### Limites

* pas nécessairement en temps réel ;
* gestion des doublons ;
* fichiers incomplets ou corrompus ;
* évolution difficile du format ;
* encodage, séparateur et fuseau horaire parfois ambigus.

### Points à définir

* nommage des fichiers ;
* fréquence de dépôt ;
* format et encodage ;
* séparateur CSV ;
* schéma des données ;
* gestion des erreurs ;
* dossier d’archive ;
* reprise après incident ;
* suppression ou conservation du fichier source.

---

## EDI

### Définition

**EDI**, pour *Electronic Data Interchange*, désigne des échanges de documents commerciaux structurés entre entreprises.

Il peut concerner :

* commandes ;
* factures ;
* bons de livraison ;
* avis d’expédition ;
* informations logistiques.

Les formats EDI peuvent être très normalisés, par exemple EDIFACT.

### Avantages

* standardisation des échanges interentreprises ;
* automatisation de processus métier ;
* réduction des saisies manuelles.

### Limites

* formats complexes ;
* intégrations coûteuses ;
* nombreuses variantes selon les partenaires ;
* dépendance possible à un prestataire EDI.

---

## SMTP

### Définition

**SMTP**, pour *Simple Mail Transfer Protocol*, est le protocole utilisé pour envoyer des e-mails.

```text
Application → serveur SMTP → serveur du destinataire
```

### Cas d’utilisation

* notifications ;
* alertes de température ;
* confirmations ;
* rapports automatiques ;
* invitations.

### Limites

Un e-mail ne constitue pas toujours un mécanisme fiable pour transporter des données métier :

* délai possible ;
* classement en spam ;
* destinataire indisponible ;
* difficulté à automatiser les réponses ;
* contenu parfois modifié ou transféré.

Pour une intégration machine à machine, une API ou un système de messages est généralement préférable.

---

# Protocoles et technologies liés à l’IoT

## Bluetooth et Bluetooth Low Energy

### Définition

**Bluetooth** permet une communication radio à courte portée.

**Bluetooth Low Energy**, ou **BLE**, est conçu pour réduire la consommation énergétique.

```text
Capteur BLE → téléphone ou passerelle → Internet
```

### Avantages

* faible consommation ;
* matériel répandu ;
* connexion directe avec un téléphone ;
* adapté aux petits capteurs.

### Limites

* portée limitée ;
* nécessite souvent une passerelle ;
* interférences possibles ;
* pas de connexion directe à Internet.

### Cas d’utilisation

* capteur proche d’un téléphone ;
* sonde de température ;
* balise ;
* équipement portable.

---

## Zigbee

### Définition

**Zigbee** est un protocole radio à faible consommation, souvent utilisé pour créer des réseaux maillés.

Dans un réseau maillé, certains appareils peuvent relayer les messages des autres.

```text
Capteur → appareil relais → passerelle → serveur
```

### Cas d’utilisation

* domotique ;
* bâtiments intelligents ;
* capteurs fixes ;
* éclairage connecté.

### Limites

* nécessite généralement une passerelle ;
* moins adapté à un camion se déplaçant loin du réseau Zigbee.

---

## LoRaWAN

### Définition

**LoRaWAN** est un protocole réseau longue portée et basse consommation.

Il est adapté aux petits messages envoyés peu fréquemment.

```text
Capteur LoRa
    ↓
Passerelle LoRaWAN
    ↓
Serveur réseau
    ↓
Application
```

### Avantages

* longue portée ;
* faible consommation ;
* adapté aux appareils sur batterie ;
* couverture de zones étendues.

### Limites

* faible débit ;
* messages limités en taille et en fréquence ;
* pas adapté aux flux vidéo ou volumineux ;
* dépendance à une couverture ou à des passerelles privées.

### Cas d’utilisation

* capteurs environnementaux ;
* compteurs ;
* suivi de conteneurs ;
* capteurs industriels ;
* collecte de petites mesures.

---

## Réseau mobile : 4G, 5G, LTE-M et NB-IoT

### Définition

Un objet connecté peut utiliser une carte SIM ou une eSIM pour se connecter à Internet.

### 4G et 5G

Adaptées aux équipements nécessitant un accès Internet classique et des volumes de données importants.

### LTE-M

Conçu pour les objets connectés mobiles avec une consommation réduite.

### NB-IoT

Conçu pour de petits messages et des objets consommant peu d’énergie.

### Cas d’utilisation

* véhicules ;
* dispositifs de suivi GPS ;
* capteurs frigorifiques ;
* équipements isolés ;
* objets se déplaçant sur un vaste territoire.

### Points de vigilance

* couverture réseau ;
* coût de l’abonnement ;
* itinérance ;
* volume de données ;
* reconnexion après perte de signal ;
* stockage local hors connexion.

---

# Protocoles industriels

## Modbus

### Définition

**Modbus** est un protocole de communication industriel ancien mais encore très répandu.

Il permet de lire ou d’écrire des valeurs dans des registres d’un équipement.

Exemples de valeurs :

* température ;
* pression ;
* état d’un moteur ;
* compteur ;
* code d’erreur.

### Variantes

```text
Modbus RTU : communication série
Modbus TCP : communication sur TCP/IP
```

### Avantages

* simple ;
* très répandu ;
* compatible avec de nombreux équipements industriels.

### Limites

* faible sémantique métier ;
* adresses de registres à documenter séparément ;
* sécurité historiquement limitée ;
* absence fréquente de chiffrement et d’authentification ;
* données parfois difficiles à interpréter sans documentation constructeur.

### Cas d’utilisation

* automates ;
* capteurs industriels ;
* équipements de traitement ;
* machines ;
* systèmes de supervision.

---

## OPC UA

### Définition

**OPC UA**, pour *Open Platform Communications Unified Architecture*, est un standard de communication industrielle moderne.

Il permet d’exposer des données industrielles sous une forme structurée et typée.

Contrairement à Modbus, OPC UA peut fournir davantage de sens :

```text
Machine
└── Groupe frigorifique
    ├── Température actuelle
    ├── Température de consigne
    ├── État du compresseur
    └── Liste des alarmes
```

### Avantages

* modèle de données riche ;
* découverte des données ;
* typage ;
* sécurité intégrée ;
* abonnements aux changements ;
* adapté à l’industrie moderne.

### Limites

* plus complexe que Modbus ;
* configuration parfois difficile ;
* bibliothèques et certificats à gérer.

### Cas d’utilisation

* usines ;
* chaînes de production ;
* supervision industrielle ;
* machines connectées ;
* systèmes SCADA.

---

## CAN bus

### Définition

Le **CAN bus** est un réseau de communication très utilisé dans les véhicules et les machines.

Les différents calculateurs échangent des messages sur un bus commun.

```text
Calculateur moteur
       │
Système de freinage ── Bus CAN ── Tableau de bord
       │
Capteur frigorifique
```

### Avantages

* robuste ;
* adapté aux environnements automobiles ;
* communication en temps réel ;
* largement utilisé dans les véhicules.

### Limites

* messages bas niveau ;
* interprétation dépendante du constructeur ;
* pas directement connecté à Internet ;
* sécurité historique limitée ;
* nécessite une passerelle pour envoyer les données vers un serveur.

### Cas d’utilisation

* véhicules ;
* engins industriels ;
* machines mobiles ;
* télématique embarquée.

---

## Communication série

### Définition

Une communication série transmet les données successivement sur une liaison physique.

Exemples :

* RS-232 ;
* RS-485 ;
* UART.

### Cas d’utilisation

* anciens équipements ;
* automates ;
* balances ;
* capteurs ;
* terminaux ;
* équipements industriels.

### Limites

* faible portée selon la technologie ;
* formats souvent propriétaires ;
* nécessite parfois un adaptateur ou une passerelle ;
* configuration manuelle de la vitesse et des paramètres de communication.

Exemple de paramètres :

```text
9600 bauds
8 bits
aucune parité
1 bit de stop
```

---

# Formats de données courants

## JSON

Format très courant dans les API REST et les messages modernes.

```json
{
  "vehicleId": "TRUCK-042",
  "temperature": 3.7
}
```

### Avantages

* lisible ;
* léger ;
* facile à utiliser en JavaScript et TypeScript ;
* largement pris en charge.

---

## XML

Format fréquent avec SOAP et certains logiciels historiques.

```xml
<temperatureReading>
  <vehicleId>TRUCK-042</vehicleId>
  <temperature>3.7</temperature>
</temperatureReading>
```

### Avantages

* schémas et validation puissants ;
* espaces de noms ;
* adapté aux documents complexes.

### Limites

* plus verbeux que JSON ;
* traitement parfois plus complexe.

---

## CSV

Format tabulaire destiné aux échanges de fichiers.

```csv
vehicleId,temperature
TRUCK-042,3.7
```

### Avantages

* simple ;
* utilisable dans un tableur ;
* compact.

### Limites

* peu adapté aux structures imbriquées ;
* typage absent ;
* conventions variables.

---

## Protocol Buffers

Format binaire notamment utilisé par gRPC.

### Avantages

* compact ;
* rapide ;
* fortement typé ;
* compatible avec plusieurs langages.

### Limites

* non lisible directement ;
* nécessite un schéma `.proto`.

---

# Comparaison synthétique

| Technologie | Modèle                            |     Temps réel | Lisibilité | Usage principal                   |
| ----------- | --------------------------------- | -------------: | ---------: | --------------------------------- |
| REST        | Requête-réponse                   | Faible à moyen |     Élevée | API métier                        |
| SOAP        | Requête-réponse                   |         Faible |    Moyenne | Systèmes d’entreprise historiques |
| GraphQL     | Requête flexible                  |          Moyen |     Élevée | Interfaces riches                 |
| gRPC        | Appel distant                     |          Élevé |     Faible | Communication entre services      |
| Webhook     | Événement poussé                  |          Élevé |     Élevée | Notification entre applications   |
| WebSocket   | Connexion bidirectionnelle        |     Très élevé |   Variable | Interface temps réel              |
| SSE         | Serveur vers client               |          Élevé |     Élevée | Tableau de bord en direct         |
| MQTT        | Publication-abonnement            |          Élevé |   Variable | IoT et télémétrie                 |
| AMQP        | Files de messages                 |          Élevé |   Variable | Traitement asynchrone fiable      |
| Kafka       | Flux d’événements                 |     Très élevé |   Variable | Très gros volumes                 |
| CoAP        | Requête légère                    |          Moyen |   Variable | Petits objets connectés           |
| SFTP        | Transfert de fichiers             |         Faible |     Élevée | Intégrations par lots             |
| Modbus      | Lecture de registres              |          Élevé |     Faible | Machines industrielles            |
| OPC UA      | Données industrielles structurées |          Élevé |    Moyenne | Industrie moderne                 |
| CAN bus     | Messages embarqués                |     Très élevé |     Faible | Véhicules et machines             |

---

# Polling, push et publication-abonnement

## Polling

Le client interroge régulièrement le serveur.

```text
Application → As-tu de nouvelles données ?
Serveur     → Non

Application → As-tu de nouvelles données ?
Serveur     → Oui
```

### Avantages

* simple ;
* contrôlé par le client.

### Limites

* appels inutiles ;
* délai entre l’événement et sa détection ;
* charge supplémentaire.

REST est souvent utilisé de cette façon.

---

## Push

Le serveur envoie l’information lorsqu’un événement se produit.

```text
Événement → Webhook → Application
```

### Avantages

* réactif ;
* pas d’appels inutiles.

### Limites

* nécessite un destinataire accessible ;
* gestion des erreurs et des tentatives.

Les webhooks et les SSE suivent ce principe.

---

## Publication-abonnement

Un producteur publie un message sans connaître directement les consommateurs.

```text
Producteur → Broker → Consommateur A
                   → Consommateur B
                   → Consommateur C
```

### Avantages

* découplage ;
* plusieurs consommateurs ;
* architecture évolutive ;
* communication asynchrone.

MQTT, AMQP et Kafka utilisent différentes formes de ce modèle.

---

# Communication synchrone et asynchrone

## Communication synchrone

Le client attend directement une réponse.

```text
Client → Requête → Serveur
Client ← Réponse ← Serveur
```

Exemples :

* REST ;
* SOAP ;
* GraphQL ;
* gRPC classique.

### Risque

Si le serveur distant est lent ou indisponible, le client reste bloqué ou reçoit une erreur.

---

## Communication asynchrone

L’émetteur dépose un message et poursuit son travail sans attendre nécessairement le traitement final.

```text
Émetteur → File de messages → Traitement ultérieur
```

Exemples :

* MQTT ;
* AMQP ;
* Kafka ;
* webhook traité par une file interne.

### Avantages

* meilleure tolérance aux pannes ;
* absorption des pics de charge ;
* découplage des systèmes.

### Limites

* traitement différé ;
* gestion des doublons ;
* cohérence éventuelle ;
* débogage plus complexe.

---

# Points de sécurité à vérifier

Quel que soit le protocole, il faut vérifier les points suivants.

## Chiffrement

Les données doivent être chiffrées pendant leur transport.

Exemples :

```text
HTTPS
MQTT avec TLS
SFTP
OPC UA sécurisé
VPN
```

## Authentification

Le système doit vérifier l’identité du client ou de l’équipement.

Mécanismes possibles :

* identifiant et mot de passe ;
* clé d’API ;
* jeton Bearer ;
* OAuth 2.0 ;
* certificat client ;
* clé SSH ;
* identité propre à chaque appareil.

## Autorisation

Une identité authentifiée ne doit accéder qu’aux ressources autorisées.

Exemple :

```text
Le capteur TRUCK-042 peut publier :
vehicles/TRUCK-042/temperature

Il ne peut pas publier :
vehicles/TRUCK-043/temperature
```

## Intégrité

Le destinataire doit pouvoir vérifier que le message n’a pas été modifié.

Mécanismes possibles :

* TLS ;
* signature numérique ;
* HMAC ;
* somme de contrôle ;
* certificat.

## Protection contre les rejeux

Un attaquant ne doit pas pouvoir réutiliser une ancienne requête valide.

Il faut prévoir :

* un identifiant unique ;
* une date ;
* un numéro de séquence ;
* une durée de validité ;
* une détection des doublons.

## Journalisation

Il faut conserver des informations comme :

* l’émetteur ;
* l’heure d’envoi ;
* l’heure de réception ;
* le résultat du traitement ;
* les erreurs ;
* l’identifiant du message.

---

# Points à demander à un fournisseur

Lorsqu’un fournisseur propose une intégration, il faut obtenir les informations suivantes.

## Accès

* Existe-t-il une API ?
* S’agit-il de REST, SOAP, MQTT ou d’un autre protocole ?
* Existe-t-il un environnement de test ?
* Comment obtient-on les identifiants ?
* Une adresse IP doit-elle être autorisée ?

## Documentation

* Existe-t-il une documentation technique ?
* Existe-t-il un fichier OpenAPI ?
* Existe-t-il un WSDL ?
* Existe-t-il un schéma JSON, XML ou CSV ?
* Les exemples de requêtes sont-ils disponibles ?

## Authentification et sécurité

* Clé d’API, OAuth, certificat ou mot de passe ?
* TLS est-il obligatoire ?
* Les secrets peuvent-ils être renouvelés ?
* Un identifiant distinct est-il fourni à chaque équipement ?
* Comment révoquer un équipement compromis ?

## Données

* Quel est le format des dates ?
* Quel est le fuseau horaire ?
* Quelles sont les unités ?
* Les valeurs peuvent-elles être absentes ?
* Comment sont identifiés les appareils et véhicules ?
* Les données sont-elles brutes ou agrégées ?

## Fréquence et volume

* À quelle fréquence les données sont-elles produites ?
* Existe-t-il des limites d’appels ?
* Combien de temps les données sont-elles conservées ?
* Existe-t-il une pagination ?
* Les historiques sont-ils disponibles ?

## Fiabilité

* Les messages peuvent-ils être envoyés plusieurs fois ?
* L’ordre est-il garanti ?
* Que se passe-t-il en cas de perte de réseau ?
* Le fournisseur effectue-t-il de nouvelles tentatives ?
* Comment récupérer les événements manqués ?

---

# Choix typiques pour une application comme Tubert

## API métier classique

```text
REST + HTTPS + JSON
```

Adapté pour :

* les applications Web ;
* les applications mobiles ;
* les échanges avec des logiciels modernes ;
* les opérations de gestion classiques.

## Intégration avec un logiciel historique

```text
SOAP + HTTPS + XML
```

ou :

```text
SFTP + fichiers CSV
```

## Réception d’événements depuis un fournisseur

```text
Webhook HTTPS + signature
```

## Capteurs connectés

```text
MQTT + TLS
```

avec éventuellement :

```text
Capteur → plateforme du fabricant → API REST ou webhook → Tubert
```

## Tableau de bord en direct

```text
SSE
```

ou :

```text
WebSocket
```

## Traitements internes asynchrones

```text
File de messages avec RabbitMQ
```

ou une solution plus simple selon le volume.

## Équipement industriel

```text
Modbus TCP
```

ou :

```text
OPC UA
```

avec un adaptateur chargé de convertir les données industrielles vers le modèle métier interne.

---

# Principe architectural recommandé

Le cœur de l’application ne devrait pas dépendre directement du protocole ou du fournisseur.

```text
REST ───────┐
SOAP ───────┤
MQTT ───────┤
CSV/SFTP ───┼──> Adaptateurs ──> Modèle métier interne
OPC UA ─────┤
Modbus ─────┘
```

Chaque adaptateur traduit le format externe vers un modèle stable.

Exemple :

```typescript
type TemperatureReading = {
  externalId: string;
  deviceId: string;
  vehicleId?: string;
  temperatureCelsius: number;
  measuredAt: Date;
  receivedAt: Date;
  source: string;
};
```

Le domaine manipule ensuite uniquement ce modèle, sans savoir si la mesure provenait :

* d’une API REST ;
* d’un service SOAP ;
* d’un message MQTT ;
* d’un fichier CSV ;
* d’un équipement Modbus ;
* d’une plateforme externe.

Cette séparation permet :

* de remplacer un fournisseur ;
* d’ajouter un nouveau protocole ;
* de tester le métier indépendamment ;
* de limiter le couplage technique ;
* de rendre l’application plus durable et maintenable.

Cette fiche peut aussi être découpée en deux documents distincts : « protocoles Web et applicatifs » et « protocoles IoT et industriels ».
