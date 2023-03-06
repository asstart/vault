---

layout: default
title: m001_basics
parent: mongodb

---

# Mongo Basics course summary

## Tools

**mongosh** - access to mongo with termnial

Intallation: `brew install mongosh`

Example of connection string: `mongosh "mongodb+srv://sandbox.c8phbll.mongodb.net/myFirstDatabase" --apiVersion 1 --username m001-student`

## Mongo Database Tools

[Documentation](https://www.mongodb.com/docs/database-tools/)

[Installation](https://www.mongodb.com/docs/database-tools/installation/installation-macos/)

### Import/Export

#### JSON

`mongoimport`\
`mongoexport`

#### BSON

`mongorestore`\
`mongodump`

mongosh "mongodb+srv://m001-student:<m001-mongodb-basics@sandbox.c8phbll.mongodb.net>/myFirstDatabase" --apiVersion 1

## Basic commands

[Mongo CRUD](https://www.mongodb.com/docs/manual/crud/)

### Explore DB

in a mongoshell:

    show dbs

    show collections

    use db

### Search data

in a mongoshell:

    db.collection.find({"field":"value"})

    db.collection.findOne()

### Insert data

in a mongoshell:

    db.collection.insert({"field":"value"})

    db.collection.insert([{"field":"value"}, {"field":"value2"}])

    db.collection.insert([{"field":"value"}, {"field":"value2"}], {"ordered":false})

If during insertion collection doesn't exists, it will be created automaticaly

### Update data

in a mongoshell:

```

db.collections.updateMany({"field":"search_value"}, {"$inc":{"pop":10}})

```

[More update operators](https://www.mongodb.com/docs/manual/reference/operator/update/?&_ga=2.38330595.114112862.1667323576-1663210444.1666003266#id1)

### Delete data

deleteOne possibly may delete more than one document if filter will return more than one document. So it's better to user with "\_id" field

To delete entire collection use `db.collection.drop()`

## Operators

### Comparison operators

List of operators:

*   $eq
*   $ne
*   $gt
*   $lt
*   $gte
*   $lte

Syntax: `{ <field>: { <operator>: <value> } }`

Example in mongoshell: `db.collection.find({"count":{"$gt":100}})`

If operator isn't specified -> $eq is a default operator

### [Logic operators](https://www.mongodb.com/docs/manual/reference/operator/query-logical/?_ga=2.76164053.114112862.1667323576-1663210444.1666003266)

List of operatos:

*   $and
*   $or
*   $nor

Syntax: `{<operator>:[ {<statement1>}, {<statement2>}, ...]}`

*   $not

Syntax: `{"$not":{<statement>}}`
