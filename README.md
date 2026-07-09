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

## Conception dirigée par le modèle

La programmation orientée objet convient bien à l’implémentation de modèle car ils
sont tous deux basés sur le même paradigme. La programmation orientée objet prévoit
des classes d’objets et leurs associations, des instances d’objets, et l’envoi de messages
entre eux. Les langages de POO rendent possible la création de mappages directs entre
les objets du modèle avec leurs relations, et leurs équivalents en programmation.

Les langages procéduraux offrent un support limité de la conception dirigée par le
Modèle. Ces langages ne proposent pas les briques nécessaires pour implémenter les
composants-clés d’un modèle.

### Les blocs de construction d’une conception orientée Modèle
