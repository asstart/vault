---

layout: default
title: examples
parent: mongodb

---

# Collection of mongodb receipts

## Merge fields to subdocument

Document has following structure:

```json

{
    "_id":"",
    "sid":"",
    "data":{
        "field":"value"
    }
}

```

### Mongosh

```sh

db.runCommand(
     {
          update: "session",
          updates: [
               {
                    q: { sid: "42d70a68-ba38-43f1-9d6c-f48ecdd76862" },
                    u : [
                         { "$set": { "data": { $mergeObjects: [ "$data", {"f3":"23"} ] } } }  
                    ]
               }
          ]
     }
)

```

### Golang with RunCommand

```go

database.RunCommand(
		context.Background(),
		bson.D{
			{"update", "session"},
			{"updates", bson.A{
				bson.D{
					{"q", bson.D{{"sid", "e292b973-2abb-4873-92d4-36f452561b2f"}}},
					{"u", bson.A{
						bson.D{
							{"$set", bson.D{
								{"data", bson.D{
									{"$mergeObjects", bson.A{
										"$data", bson.D{{"f4", "22"}},
									}},
								}},
							}},
						},
					}},
				}},
			},
		},
	)

```

### Golang with UpdateOne

FindOneAndUpdate also suitable. It's important to use bson.A for update statement, otherwise refs won't be resolved

```golang

	db.Collection("session").UpdateOne(
		context.Background(),
		bson.D{{"sid", "e292b973-2abb-4873-92d4-36f452561b2f"}},
		bson.A{
			bson.D{{"$set", bson.D{
				{"data", bson.D{
					{"$mergeObjects", bson.A{
						"$data", bson.D{{"f25", "22"}},
					}},
				}},
			}}},
		},
	)

```
