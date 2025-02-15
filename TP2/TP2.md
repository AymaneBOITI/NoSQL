# Rapport Détaillé - Manipulation de MongoDB

## Introduction

MongoDB est un système de gestion de base de données NoSQL orienté documents qui se distingue des bases de données relationnelles traditionnelles par plusieurs aspects clés :

1. **Structure des données** : 
   - Les données sont stockées dans des documents BSON (Binary JSON)
   - Pas de schéma fixe comme dans les bases SQL
   - Les documents peuvent avoir des structures différentes dans une même collection

2. **Flexibilité** :
   - Ajout dynamique de champs
   - Pas besoin de migrations complexes pour modifier la structure
   - Support natif des tableaux et documents imbriqués

3. **Performance** :
   - Indexation puissante
   - Mise à l'échelle horizontale native
   - Optimisé pour les lectures et écritures fréquentes

## 1. Préparation de l'environnement

### Installation
Suivez les guides d'installation officiels pour votre système d'exploitation :

- Windows : [Guide d'installation MongoDB pour Windows](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-windows/)
- macOS : [Guide d'installation MongoDB pour macOS](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-os-x/)
- Ubuntu : [Guide d'installation MongoDB pour Ubuntu](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/)

### Données du TP
Téléchargez le fichier des films pour le TP :
[films.json](https://moodle.univ-spn.fr/pluginfile.php/818630/mod_resource/content/1/films.json)

### Structure des données du TP
Le fichier JSON contient une collection de films avec la structure suivante :
```javascript
{
    "_id": ObjectId(),
    "title": String,
    "year": Number,
    "genre": String,
    "country": String,
    "grades": [{
        "grade": String,
        "note": Number
    }],
    "actors": [{
        "first_name": String,
        "last_name": String
    }],
    "summary": String
}
```

**Documentation de référence** :
- [Introduction à MongoDB](https://www.mongodb.com/docs/manual/introduction/)
- [Types de données BSON](https://www.mongodb.com/docs/manual/reference/bson-types/)

## 2. Importation et vérification des données

### Commande d'importation
```bash
./mongoimport --db lesfilms --collection films films.json --jsonArray
```

**Explication détaillée** :
- `mongoimport` : Utilitaire de MongoDB pour l'importation de données
- `--db lesfilms` : Crée/utilise la base de données "lesfilms"
- `--collection films` : Crée/utilise la collection "films"
- `--jsonArray` : Indique que le fichier contient un tableau JSON

**Fonctionnement interne** :
1. MongoDB analyse le fichier JSON
2. Convertit chaque objet en document BSON
3. Génère un `_id` unique si non spécifié
4. Insère les documents dans la collection

**Documentation de référence** :
- [mongoimport](https://www.mongodb.com/docs/database-tools/mongoimport/)
- [Importation de données](https://www.mongodb.com/docs/manual/tutorial/insert-documents/)

### Vérification de l'importation
```javascript
db.films.countDocuments()
```

**Explication détaillée** :
- Compte le nombre exact de documents dans la collection
- Plus précis que `count()` car vérifie chaque document
- Retourne un nombre entier

**Documentation de référence** :
- [db.collection.countDocuments()](https://www.mongodb.com/docs/manual/reference/method/db.collection.countDocuments/)

## 3. Analyse de la structure

```javascript
db.films.findOne()
```

**Explication détaillée** :
- Récupère un document aléatoire de la collection
- Ne nécessite pas de parcourir toute la collection
- Utile pour comprendre la structure des documents

**Utilisation avancée** :
```javascript
db.films.findOne({}, { projection: { _id: 0 } })  // Sans l'ID
db.films.findOne({ genre: "Action" })  // Avec filtre
```

**Documentation de référence** :
- [db.collection.findOne()](https://www.mongodb.com/docs/manual/reference/method/db.collection.findOne/)
- [Projections in Queries](https://www.mongodb.com/docs/manual/tutorial/project-fields-from-query-results/)

## 4. Requêtes de base

### Films d'action
```javascript
db.films.find({ "genre": "Action" })
```

**Fonctionnement détaillé** :
1. MongoDB parcourt la collection
2. Compare le champ "genre" de chaque document
3. Retourne les documents correspondants
4. Utilise les index si disponibles

**Optimisation** :
- Création d'index : `db.films.createIndex({ "genre": 1 })`
- Améliore les performances des recherches fréquentes

**Documentation de référence** :
- [db.collection.find()](https://www.mongodb.com/docs/manual/reference/method/db.collection.find/)
- [Query and Projection Operators](https://www.mongodb.com/docs/manual/reference/operator/query/)

### Comptage avec filtres
```javascript
db.films.countDocuments({ "genre": "Action" })
```

**Processus interne** :
1. Applique le filtre sur la collection
2. Compte uniquement les documents correspondants
3. Utilise les index disponibles
4. Plus efficace que `find().count()`

## 5. Requêtes avec critères multiples

### Films d'action français
```javascript
db.films.find({ 
    "genre": "Action", 
    "country": "FR" 
})
```

**Fonctionnement** :
- Opérateur implicite `$and`
- Tous les critères doivent être satisfaits
- Ordre d'évaluation optimisé par MongoDB

**Version explicite équivalente** :
```javascript
db.films.find({
    $and: [
        { "genre": "Action" },
        { "country": "FR" }
    ]
})
```

**Documentation de référence** :
- [Logical Query Operators](https://www.mongodb.com/docs/manual/reference/operator/query-logical/)

### Filtrage par année
```javascript
db.films.find({ 
    "genre": "Action", 
    "country": "FR",
    "year": 1963
})
```

**Optimisation des performances** :
- Index composé recommandé :
```javascript
db.films.createIndex({ "genre": 1, "country": 1, "year": 1 })
```

## 6. Projections avancées

### Exclusion des grades
```javascript
db.films.find(
    { "genre": "Action" },
    { "grades": 0 }
)
```

**Fonctionnement des projections** :
1. Sélection des documents par le filtre
2. Application de la projection sur chaque document
3. Retour des documents modifiés

**Règles importantes** :
- Impossible de mélanger inclusion (1) et exclusion (0)
- Exception pour `_id` qui peut être exclu avec d'autres inclusions

**Documentation de référence** :
- [Projection Operators](https://www.mongodb.com/docs/manual/reference/operator/projection/)

### Projections complexes
```javascript
db.films.find(
    { "genre": "Action" },
    { 
        "title": 1,
        "year": 1,
        "grades.note": 1,
        "_id": 0 
    }
)
```

**Cas des tableaux** :
- Projection possible sur des éléments spécifiques
- Filtrage possible avec `$slice`, `$elemMatch`

## 7. Requêtes sur les tableaux

### Notes supérieures à 10
```javascript
db.films.find({
    "grades.note": { $gt: 10 }
})
```

**Comportement avec les tableaux** :
1. MongoDB examine chaque élément du tableau
2. Retourne le document si AU MOINS UN élément correspond
3. L'opérateur `$elemMatch` permet un contrôle plus précis

**Documentation de référence** :
- [Array Query Operators](https://www.mongodb.com/docs/manual/reference/operator/query-array/)

### Toutes les notes supérieures à 10
```javascript
db.films.find({
    "grades": { 
        $not: { 
            $elemMatch: { 
                note: { $lte: 10 } 
            } 
        } 
    }
})
```

**Explication détaillée de la logique** :
1. `$elemMatch` : cherche un élément correspondant aux critères
2. `$not` : inverse la condition
3. `$lte` : less than or equal
4. Résultat : aucune note ≤ 10 autorisée

## 8. Opérateurs de comparaison

### Utilisation de $exists
```javascript
db.films.find({
    "summary": { $exists: false }
})
```

**Fonctionnement** :
- Vérifie la présence/absence du champ
- Ne vérifie pas la valeur (null est considéré comme existant)
- Utile pour la validation de données

**Documentation de référence** :
- [Existence Check Operators](https://www.mongodb.com/docs/manual/reference/operator/query/exists/)

### Opérateur $in
```javascript
db.films.find({
    "artists": { 
        $in: ["artist:4", "artist:18", "artist:11"]
    }
})
```

**Optimisation** :
- Plus efficace que `$or` pour les comparaisons simples
- Utilise les index efficacement
- Équivalent à : value = x OR value = y OR value = z

## 9. Requêtes complexes

### Recherche DiCaprio 1997
```javascript
db.films.find({
    "actors.first_name": "Leonardo",
    "actors.last_name": "DiCaprio",
    "year": 1997
})
```

**Points clés** :
- Recherche dans des documents imbriqués
- Utilisation de la notation point
- Matching sur plusieurs critères dans un tableau

### Opérateur $or
```javascript
db.films.find({
    $or: [
        { 
            "actors.first_name": "Leonardo",
            "actors.last_name": "DiCaprio"
        },
        { "year": 1997 }
    ]
})
```

**Fonctionnement de $or** :
1. Évalue chaque condition indépendamment
2. Combine les résultats
3. Peut utiliser différents index
4. Plus coûteux qu'une requête simple

**Documentation de référence** :
- [Query Operators](https://www.mongodb.com/docs/manual/reference/operator/query/)
- [Array Query Operators](https://www.mongodb.com/docs/manual/reference/operator/query-array/)

## 10. Bonnes pratiques et optimisations

### Indexation
```javascript
// Index simple
db.films.createIndex({ "genre": 1 })

// Index composé
db.films.createIndex({ "genre": 1, "country": 1, "year": 1 })

// Index sur tableau
db.films.createIndex({ "grades.note": 1 })
```

**Documentation de référence** :
- [Index Types](https://www.mongodb.com/docs/manual/indexes/#index-types)
- [Index Properties](https://www.mongodb.com/docs/manual/core/index-properties/)

### Analyse des performances
```javascript
// Explication de l'exécution d'une requête
db.films.find({ "genre": "Action" }).explain("executionStats")
```

### Limiter les résultats
```javascript
// Pagination
db.films.find().skip(20).limit(10)

// Tri
db.films.find().sort({ "year": -1 })
```

**Documentation de référence** :
- [Query Plans](https://www.mongodb.com/docs/manual/core/query-plans/)
- [Query Optimization](https://www.mongodb.com/docs/manual/core/query-optimization/)

## Conclusion

Ce rapport détaillé couvre les aspects fondamentaux et avancés de la manipulation de données avec MongoDB. Les concepts clés à retenir sont :

1. La flexibilité du schéma documentaire
2. L'importance de l'indexation pour les performances
3. La puissance des opérateurs de requête
4. La gestion efficace des tableaux et documents imbriqués

## Références

- [Documentation officielle MongoDB](https://www.mongodb.com/docs/)
- [Guide des opérateurs](https://www.mongodb.com/docs/manual/reference/operator/)
- [Bonnes pratiques d'indexation](https://www.mongodb.com/docs/manual/core/indexes-introduction/)
- [Optimisation des performances](https://www.mongodb.com/docs/manual/core/query-optimization/)
- [Guide de démarrage MongoDB](https://www.mongodb.com/docs/manual/tutorial/getting-started/)
- [Documentation des agrégations](https://www.mongodb.com/docs/manual/aggregation/)
- [Gestion des index](https://www.mongodb.com/docs/manual/indexes/)
- [Sécurité MongoDB](https://www.mongodb.com/docs/manual/security/)