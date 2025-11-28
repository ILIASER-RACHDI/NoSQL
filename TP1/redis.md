# Introduction à Redis et aux Bases de Données NoSQL

## Qu’est-ce que le NoSQL ?

Le terme **NoSQL** désigne une famille de systèmes de gestion de bases de données qui ne reposent pas sur le modèle relationnel classique. Par opposition aux bases de données relationnelles (SQL), les bases NoSQL sont pensées pour :

- Offrir une **grande flexibilité** dans la structure et l’organisation des données.  
- Faciliter la **scalabilité horizontale** (on ajoute des machines pour absorber plus de charge).  
- Manipuler aisément des volumes importants de données **non structurées** ou **semi-structurées**.

### Grandes catégories de bases NoSQL

1. **Clé–Valeur**  
   - Les données sont enregistrées sous forme de paires `clé → valeur`.  
   - Particulièrement adapté lorsque chaque clé unique renvoie à une valeur précise.  
   - **Exemples :** Redis, Amazon DynamoDB.

2. **Orientées colonnes**  
   - Les informations sont organisées par colonnes plutôt que par lignes.  
   - Très efficaces pour les traitements analytiques à grande échelle.  
   - **Exemples :** Apache Cassandra, HBase.

3. **Orientées documents**  
   - Les données sont stockées sous forme de documents (JSON, BSON, XML, etc.).  
   - Fréquemment utilisées dans les applications web et mobiles.  
   - **Exemples :** MongoDB, Couchbase.

4. **Orientées graphes**  
   - Les données sont modélisées sous forme de graphes (nœuds, arêtes, relations).  
   - Idéales lorsque les liens entre entités sont au cœur du problème (réseaux sociaux, moteurs de recommandation, etc.).  
   - **Exemples :** Neo4j, ArangoDB.

---

## Qu’est-ce que Redis ?

**Redis** (REmote DIctionary Server) est une base de données NoSQL en mémoire, très rapide et open source. On l’utilise principalement comme :

- **Cache** pour accélérer les réponses,  
- **Base clé–valeur** hautes performances,  
- **Système de messagerie** (via Pub/Sub).

Redis est apprécié pour :

- sa rapidité,  
- sa simplicité d’utilisation,  
- ses structures de données riches (listes, sets, hash, etc.).

Ce document présente les commandes essentielles de Redis au travers d’exemples pratiques, afin de pouvoir l’intégrer efficacement dans vos projets.

---

## 1. Démarrage de Redis

### Lancer le serveur Redis (`redis-server`)

~~~bash
redis-server
~~~

### Lancer un client Redis (`redis-cli`)

~~~bash
redis-cli
~~~

📝 **Remarque :** Par défaut, Redis écoute sur le port **6379**. Il existe aussi des clients graphiques qui permettent d’interagir avec Redis sans passer par la ligne de commande.

---

## 2. Gestion des clés (CRUD)

### Créer une clé : `SET clé valeur`

~~~bash
SET demo "Bonjour"
~~~

Ici, la clé `demo` est créée avec la valeur `"Bonjour"`.

### Lire une valeur : `GET clé`

~~~bash
GET demo
# Résultat : "Bonjour"
~~~

### Supprimer une clé : `DEL clé`

~~~bash
DEL demo
~~~

La commande :

- renvoie **1** si la clé a bien été supprimée,  
- renvoie **0** si la clé n’existe pas.

### Incrémenter une valeur

~~~bash
SET compteur 0
INCR compteur   # Résultat : 1
~~~

### Décrémenter une valeur

~~~bash
DECR compteur   # Résultat : 0
~~~

---

## 3. Durée de vie d’une clé

### Vérifier la durée de vie : `TTL clé`

~~~bash
TTL macle
~~~

`TTL` renvoie :

- **-1** si la clé n’a pas de durée de vie (elle n’expire pas),  
- un nombre positif : le nombre de secondes restantes avant l’expiration.

### Définir une durée de vie : `EXPIRE clé secondes`

~~~bash
EXPIRE macle 120
~~~

La clé `macle` sera supprimée automatiquement après **120 secondes**.

---

## 4. Structures de données Redis

Redis fournit plusieurs types de structures adaptées à différents besoins :

- **Listes**,  
- **Ensembles (sets)**,  
- **Ensembles ordonnés (sorted sets)**,  
- **Hachages (hashes)**,  
- ainsi que Pub/Sub pour la messagerie.

---

### 4.1 Listes

Les listes gèrent des suites ordonnées d’éléments (les doublons sont autorisés).  
Elles sont notamment utilisées pour :

- des files d’attente,  
- des journaux d’événements,  
- des historiques de messages (ex. chat).

#### Ajouter des éléments

~~~bash
RPUSH mesCours "BDA"
LPUSH mesCours "NoSQL"
~~~

- `RPUSH` : ajoute un élément **à droite** de la liste.  
- `LPUSH` : ajoute un élément **à gauche** de la liste.

#### Afficher les éléments

~~~bash
LRANGE mesCours 0 -1  # Tous les éléments
LRANGE mesCours 0 0   # Premier élément uniquement
~~~

#### Supprimer des éléments

~~~bash
LPOP mesCours  # Supprime le premier élément (gauche)
RPOP mesCours  # Supprime le dernier élément (droite)
~~~

---

### 4.2 Ensembles (Sets)

Les ensembles stockent des éléments **uniques**, sans notion d’ordre.  
Ils sont utiles pour :

- suivre une liste d’utilisateurs distincts,  
- gérer des tags,  
- supprimer automatiquement les doublons.

#### Ajouter des éléments

~~~bash
SADD utilisateurs "Alice"
SADD utilisateurs "Bob"
~~~

#### Afficher les éléments

~~~bash
SMEMBERS utilisateurs
~~~

#### Supprimer un élément

~~~bash
SREM utilisateurs "Augustin"
~~~

#### Union de deux ensembles : `SUNION ensemble1 ensemble2`

~~~bash
SADD autresUtilisateurs "Ilias"
SUNION utilisateurs autresUtilisateurs
~~~

---

### 4.3 Ensembles ordonnés (Sorted Sets)

Les ensembles ordonnés combinent :

- l’unicité des éléments (comme pour les sets),  
- un **score numérique** qui permet de trier les éléments.

Ils sont parfaits pour :

- des classements (scores de jeux),  
- des listes triées par popularité ou pertinence,  
- des systèmes de recommandation.

#### Ajouter des données : `ZADD nom_du_set score valeur`

~~~bash
ZADD scores 10 "Alice"
ZADD scores 15 "Bob"
~~~

#### Afficher les données : `ZRANGE nom_du_set premier_indice dernier_indice`

~~~bash
ZRANGE scores 0 -1      # Ordre croissant : ["Alice", "Bob"]
ZREVRANGE scores 0 -1   # Ordre décroissant : ["Bob", "Alice"]
~~~

#### Position d’un élément : `ZRANK nom_du_set valeur`

~~~bash
ZRANK scores "Alice"
~~~

- Renvoie l’indice de `"Alice"` (en commençant à 0).  
- Si `"Alice"` n’est pas présente, le résultat est `nil`.

---

### 4.4 Hachages (Hashes)

Les hachages permettent de regrouper plusieurs paires `champ → valeur` sous une même clé.  
C’est très pratique pour représenter :

- un profil utilisateur,  
- un objet structuré,  
- une configuration.

#### Ajouter des champs : `HSET hash champ valeur`

~~~bash
HSET utilisateur:1 nom "Alice"
HSET utilisateur:1 age "30"
~~~

#### Afficher tous les champs : `HGETALL hash`

~~~bash
HGETALL utilisateur:1
~~~

Résultat typique :

~~~bash
1) "nom"
2) "Alice"
3) "age"
4) "30"
~~~

---

## 5. Pub/Sub (Publication / Souscription)

Le mécanisme **Pub/Sub** de Redis permet d’assurer une communication en temps réel entre des émetteurs et des abonnés.  
C’est adapté à :

- des systèmes de chat,  
- des notifications instantanées,  
- des échanges de messages entre services.

#### S’abonner à un canal : `SUBSCRIBE canal`

~~~bash
SUBSCRIBE canal
~~~

#### Publier un message : `PUBLISH canal "Message"`

~~~bash
PUBLISH canal "Message"
~~~

---

### Exemple complet avec Pub/Sub

#### 1. Démarrer le serveur et deux clients

1. Lancer le serveur Redis.  
2. Ouvrir deux terminaux avec `redis-cli` :

   - **Client 1** : reçoit les messages.  
   - **Client 2** : envoie les messages.

---

#### 2. Abonnement à un canal (`mesCours`) sur le Client 1

~~~bash
SUBSCRIBE mesCours
~~~

Le client reste en écoute sur le canal `mesCours`.

---

#### 3. Publication d’un message sur `mesCours` depuis le Client 2

~~~bash
PUBLISH mesCours "Un nouveau cours sur NoSQL"
~~~

Sur le Client 1, on verra quelque chose comme :

~~~bash
1) "message"
2) "mesCours"
3) "Un nouveau cours sur NoSQL"
~~~

---

#### 4. Message ciblé pour un utilisateur (`user:1`)

~~~bash
PUBLISH user:1 "Bonjour user 1!"
~~~

Si `user:1` est abonné au canal `user:1`, il reçoit immédiatement :

~~~bash
1) "message"
2) "user:1"
3) "Bonjour user 1!"
~~~

---

#### 5. Abonnement avec motif : `PSUBSCRIBE`

Pour écouter tous les canaux dont le nom commence par `mes` :

~~~bash
PSUBSCRIBE mes*
~~~

Cela permet de recevoir, par exemple, les messages publiés sur :

- `mesCours`  
- `mesNotifications`  
- etc.

---

## Fonctions utilitaires de Redis

Redis propose plusieurs commandes pratiques pour explorer, gérer et nettoyer vos données.

---

### 1. Lister les clés de la base active : `KEYS *`

~~~bash
SET nom "Alice"
SET age "30"
KEYS *
~~~

Résultat :

~~~bash
1) "nom"
2) "age"
~~~

> ⚠️ À utiliser avec précaution en production, car `KEYS *` peut être coûteuse sur de très grandes bases.

---

### 2. Bases de données Redis (`SELECT`)

Par défaut, Redis met à disposition **16 bases de données**, numérotées de **0** à **15**.  
La base **0** est utilisée par défaut.

#### Changer de base : `SELECT <numéro>`

~~~bash
SELECT 1
~~~

Vous travaillez désormais sur la base de données numéro 1.

#### Isolation des bases

Chaque base est indépendante : une clé créée dans une base n’apparaît pas dans une autre.

~~~bash
SELECT 0
SET cleBase0 "valeur"
KEYS *
# ["cleBase0"]

SELECT 1
KEYS *
# aucune clé (base vide)
~~~

---

### 3. Vider la base de données courante : `FLUSHDB`

Pour supprimer toutes les clés **de la base active uniquement** :

~~~bash
SET test "données"
KEYS *       # ["test"]

FLUSHDB
KEYS *       # []
~~~

---

### 4. Vider toutes les bases : `FLUSHALL`

Pour effacer toutes les données présentes dans **toutes** les bases Redis :

~~~bash
SET test "données"
SELECT 1
SET exemple "données2"
FLUSHALL
~~~

Après l’exécution de `FLUSHALL`, plus aucune clé n’est présente dans aucune des bases de données Redis.

---
