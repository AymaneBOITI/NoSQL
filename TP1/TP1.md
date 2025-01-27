# Introduction aux bases de données NoSQL et à Redis

## Différences entre les bases de données relationnelles et NoSQL

Les bases de données relationnelles (SGBDR) ont longtemps été la norme pour le stockage et la gestion des données. Elles sont caractérisées par une structure rigide basée sur des tables, des relations et un langage de requête normalisé, SQL. Elles respectent les propriétés **ACID** (Atomicité, Cohérence, Isolation, Durabilité), garantissant la cohérence des transactions même en cas de pannes.

Cependant, avec l'augmentation du volume, de la vitesse et de la variété des données (**les 3V du Big Data**), les SGBDR ont montré leurs limites en termes de scalabilité et de performance. Les bases de données **NoSQL** ("Not Only SQL") sont apparues comme une alternative permettant plus de flexibilité dans la modélisation des données et une meilleure gestion de la distribution et de la réplication des données. Contrairement aux SGBDR, elles adoptent les propriétés **BASE** (Basically Available, Soft state, Eventually consistent), favorisant la disponibilité et la tolérance aux pannes au détriment de la cohérence stricte.

## Les familles de bases de données NoSQL

Les bases de données NoSQL se classent en plusieurs familles, selon leur modèle de stockage et leur mode d'interrogation :

1. **Bases Clé-Valeur** : Stockent les données sous forme de paires clé-valeur, offrant un accès rapide. Exemple : **Redis, DynamoDB**.
2. **Bases Orientées Colonnes** : Organisent les données en colonnes plutôt qu'en lignes, optimisant ainsi les requêtes analytiques. Exemple : **Apache Cassandra, HBase**.
3. **Bases Orientées Documents** : Stockent les données sous forme de documents JSON ou BSON, offrant une grande flexibilité. Exemple : **MongoDB, CouchDB**.
4. **Bases Orientées Graphes** : Conçues pour stocker des relations complexes sous forme de graphes de nœuds et d'arêtes. Exemple : **Neo4j, FlockDB**.
5. **Bases Série Temporelle** : Conçues pour la gestion de données chronologiques. Exemple : **InfluxDB, OpenTSDB**.

---

# Introduction à Redis

**Redis** (*Remote Dictionary Server*) est une base de données NoSQL de type clé-valeur, entièrement en mémoire, offrant une très grande rapidité d'accès aux données. Il est souvent utilisé pour le **caching, la gestion de sessions et les files d'attente distribuées**. Redis supporte plusieurs structures de données comme les **listes, ensembles, hachages et flux**. Sa capacité à assurer la persistance des données et sa compatibilité avec les architectures distribuées en font un choix populaire pour les applications modernes à haute performance.

---

# Tutoriel sur Redis : Manipulation des Clés et Structures de Données

## 1. Installation et Connexion

Avant de commencer, assurez-vous que Redis est installé sur votre machine. Vous pouvez le télécharger depuis le site officiel de Redis ou l'installer via Docker :

```bash
docker run redis
docker exec -it <container_id> redis-cli
```

Pour démarrer le serveur Redis :

```bash
redis-server
```

Pour se connecter au serveur Redis via le client en ligne de commande (CLI) :

```bash
redis-cli
```

Par défaut, Redis fonctionne sur **localhost (127.0.0.1)** et écoute sur le port **6379**.

---

## 2. Commandes de base Redis

### Définir une clé
```bash
SET <clé> <valeur>
```
Exemple :
```bash
SET demo "Bonjour"
```

### Lire une valeur
```bash
GET <clé>
```
Exemple :
```bash
GET demo
```

### Supprimer une clé
```bash
DEL <clé>
```
Exemple :
```bash
DEL demo
```

### Incrémenter une valeur
```bash
INCR <clé>
```
Exemple :
```bash
INCR visite_count
```

### Décrémenter une valeur
```bash
DECR <clé>
```
Exemple :
```bash
DECR visite_count
```

### Définir une durée de vie pour une clé
```bash
EXPIRE <clé> <secondes>
```
Exemple :
```bash
EXPIRE demo 60
```

---

## 3. Manipulation des Structures de Données dans Redis

### Listes
#### Ajouter un élément
```bash
LPUSH <liste> <valeur>
RPUSH <liste> <valeur>
```
Exemple :
```bash
LPUSH cours "BDA"
RPUSH cours "Services Web"
```

#### Afficher les éléments
```bash
LRANGE <liste> 0 -1
```

### Ensembles (Sets)
#### Ajouter un élément
```bash
SADD <set> <valeur>
```
Exemple :
```bash
SADD utilisateurs "Augustin"
```

#### Afficher tous les éléments
```bash
SMEMBERS <set>
```

### Ensembles ordonnés (Sorted Sets)
#### Ajouter un élément avec un score
```bash
ZADD <ensemble> <score> <valeur>
```
Exemple :
```bash
ZADD scores 19 "Augustin"
ZADD scores 18 "Ines"
```

#### Récupérer les éléments triés
```bash
ZRANGE <ensemble> 0 -1
```

---

# Optimisation des accès aux données

Redis stocke toutes les données **en mémoire RAM**, évitant ainsi les latences des bases classiques qui utilisent un stockage disque. Cependant, il est important de configurer **un algorithme d'éviction** pour gérer la saturation de la mémoire.

---

# Pub/Sub et gestion des bases de données

## 1. Système de messagerie avec Redis (Pub/Sub)

#### S'abonner à un canal
```bash
SUBSCRIBE <canal>
```
Exemple :
```bash
SUBSCRIBE mes_cours
```

#### Publier un message
```bash
PUBLISH <canal> <message>
```
Exemple :
```bash
PUBLISH mes_cours "Un nouveau cours sur MongoDB."
```

---

## 2. Gestion des bases de données dans Redis

#### Afficher toutes les clés
```bash
KEYS *
```

#### Changer de base de données
```bash
SELECT <numéro_base>
```
Exemple :
```bash
SELECT 1
```

---

## 3. Persistance des données et reprise après panne

Redis propose **deux modes de persistance** :
1. **RDB (Redis Database File)** : Sauvegarde périodique des données sur disque.
2. **AOF (Append-Only File)** : Enregistre chaque commande exécutée pour une récupération complète.

La configuration de la persistance se fait dans **redis.conf**.

---

## Conclusion
Redis est une base NoSQL rapide et flexible, adaptée au **caching, stockage en mémoire et messagerie en temps réel**. Son utilisation dans des architectures modernes permet **d'améliorer la performance et la scalabilité** des applications.

---

### Auteur :
📌 *Aymane BOITI*
