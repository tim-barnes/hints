---
title: "Cheatsheet"
date: 2020-05-17T08:05:18+01:00
draft: False
tags: ["mongodb", "databases"]
---

## General queries

### Find and Project

Return `field==value` without `_id` or other fields.

```
db.collection.find({ "field": "value" }, { "_id": 0, "field": 1 })
```

### Copy all docs into another collection

Ugly `O(n)` collection copy, but sometimes its the simplest thing to do:

```
db.from_collection.find().forEach( function(obj) { db.to_collection.insert(obj) } )
```

Succinct version:

```
db.from_collection.find().forEach( (o) => db.to_collection.insert(o) )
```

### Find Document with array length i

Useful to find documents with specific properties.

```
db.collection.find({ "field": { "$size": i } })
```


## Aggregations

### Count number of documents by a field

For example by locale:

```
db.collection.aggregate([{
    $group: {
        _id: "$locale",
        "locale": { $sum: 1 }
    }
}])
```

### Get N random Documents

```
entities = db.collection.aggregate(
   [ { "$sample": { "size": N } } ]
)
```

## Join and Project

This pipeline:
* Locates a set of source documents
* Matches a field in the sources to a foreign collection field
* Projects the joined documents into a result

```
db.sources_collection.aggregate([
    {
        "$match": {
            "field": "some_value"
        },
    },
    {
        "$lookup": {
            "from": "destination_collection",
            "localField": "match_field",
            "foreignField": "_id",
            "as": "sources"
        },
    },
    {
        "$unwind": "$sources"
    },
    {
        "$project": {
            "_id": "$sources._id",
            "foo": "$sources.foo",
            "bar": "$sources.bar"
        }
    }
])
```
