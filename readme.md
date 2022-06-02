# Bson vs Json

https://www.mongodb.com/json-and-bson

Json is Javascript Object Notation. And we can use json to store data but there are some problems with it

1. JSON is a text-based format, and text parsing is very slow
2. JSON’s readable format is far from space-efficient, another database concern
3. JSON only supports a limited number of basic data types

Bson is just similar Json but still different in many ways. Bson stands for Binary JSON
Its same as Json but it stores data binary format also provides more data structres by default
MongoDb internally or over network uses BSON to store data but we can view and add data in JSON format
Basically Anything you can represent in JSON can be natively stored in MongoDB, and retrieved just as easily in JSON.

# IMPORTING AND EXPORTING DATA

https://www.mongodb.com/docs/database-tools/mongoimport/#compatibility

We can export or import to/from both JSON and BSON formats

To export:

1. to BSON format:  
   `mongodump --uri "mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/sample_supplies"`

2. to JSON format:  
   `mongoexport --uri="mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/sample_supplies" --collection=sales --out=sales.json`

To import:

1. from BSON format:  
   `mongorestore --uri "mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/sample_supplies" --drop dump`
2. from JSON format:
   `mongoimport --uri="mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/sample_supplies" --drop sales.json`

# QUERYING DOCUMENTS

1. You can query databases using Data Explorer in Atlas and use JSON object to filter the data based on key value pairs

2. Or we can use mongo shell to query the database

- `show dbs` -> shows all databases present in the cluster
- `use sample_training` -> selects the database to be used for further commands
- `show collections` -> shows all the collections present inside the selected database
- `db.zips.find({"state": "NY"})` -> returns the entries where state is NY

  # it iterates through the cursor

  `db.zips.find({"state": "NY"}).count()`

  `db.zips.find({"state": "NY", "city": "ALBANY"})`

  `db.zips.find({"state": "NY", "city": "ALBANY"}).pretty()`

# Mongo Shell

1. mongo shell is a fully functioning JavaScript interpreter, which means that you can create things like JavaScript functions and variables in it.
2. It allows you to interact with your MongoDB instance without using a Graphical User Interface.

# INSERTING DOCUMENTS

1. Data can be inserted using the Data Explorer or Mongo shell.

- "\_id" is a unique and required identifier for a document in a mongodb collection

- Default value for this field is "ObjectId()"

2. To insert the document using the shell:

- `mongoimport --uri="mongodb+srv://<username>:<password>@<cluster>.mongodb.net/sample_supplies" sales.json` -> importing the data in mongoDb database
- `mongo "mongodb+srv://<username>:<password>@<cluster>.mongodb.net/admin"` -> connect to the cluster
- `use sample_training` -> select the particular database
- `db.inspections.findOne()` -> gets a random document from collection ( to get familiar the object structure before inserting a new value )

```
   db.inspections.insert({
      "_id" : ObjectId("56d61033a378eccde8a8354f"),
      "id" : "10021-2015-ENFO",
      "certificate_number" : 9278806,
      "business_name" : "ATLIXCO DELI GROCERY INC.",
      "date" : "Feb 20 2015",
      "result" : "No Violation Issued",
      "sector" : "Cigarette Retail Dealer - 127",
      "address" : {
              "city" : "RIDGEWOOD",
              "zip" : 11385,
              "street" : "MENAHAN ST",
              "number" : 1712
         }
   )

   db.inspections.insert({
      "id" : "10021-2015-ENFO",
      "certificate_number" : 9278806,
      "business_name" : "ATLIXCO DELI GROCERY INC.",
      "date" : "Feb 20 2015",
      "result" : "No Violation Issued",
      "sector" : "Cigarette Retail Dealer - 127",
      "address" : {
              "city" : "RIDGEWOOD",
              "zip" : 11385,
              "street" : "MENAHAN ST",
              "number" : 1712
         }
   })
```

- `db.inspections.find({"id" : "10021-2015-ENFO", "certificate_number" : 9278806}).pretty()` -> checking if the insertion happened succesfully in the above step

- MongoDb allows you to have documents that are identical as long as their "\_id" values are different
- However one can use MongoDb schemas to define additional rules to decide if a document can be inserted or Notation
- To insert multiple documents in single query just replace the object inside the insert method with an array of objects

- `db.inspections.insert([ { "test": 1 }, { "test": 2 }, { "test": 3 } ])` -> inserting 3 documents in single query

- `db.inspections.insert([{ "_id": 1, "test": 1 },{ "_id": 1, "test": 2 },{ "_id": 3, "test": 3 }])` -> trying to insert two documents with same
  "\_id" which gives an array as it cannot be same for two documents and once this query is executed, it gives an error and also does not insert the 3rd document which
  is correct.

- This happens because insert query is ordered which means all the documents are inserted sequentially and if an error is encountered all the documents after that
  will not be inserted

- You can pass { ordered : false } inside the insert statement to ensure that documents are inserted in unordered manner
  `db.inspections.insert([{ "_id": 1, "test": 1 },{ "_id": 1, "test": 2 },{ "_id": 3, "test": 3 }],{ "ordered": false })`

# UPDATING DOCUMENTS

1. Updating from the Data Explorer is pretty straight forward
2. Updating using Mongo Shell using two operations

- updateOne( selector, whatever update needs to be done )
- updateMany( selector, whatever update needs to be done )

3. Update operators

- $inc -> increments the value by specified number

  `db.zips.updateMany({ "city": "HUDSON" }, { "$inc": { "pop": 10 } })`

- $set -> sets the value to new value or if doesnot already exists adds it to the document

  `db.zips.updateOne({ "zip": "12534" }, { "$set": { "pop": 17630 } })`

- $push -> pushes the value to the specified array
  ```
  db.grades.updateOne({
     "student_id": 250,
     "class_id": 339
     },
     {
        "$push": {
           "scores": {
              "type": "extra credit",
              "score": 100
              }
           }
     })
  ```

# DELETING DOCUMENTS

1. Updating from the Data Explorer is pretty straight forward
2. Updating using Mongo Shell using two operations

- deleteOne()
- deleteMany()

3. Always ensure while deleting that the field u are using in delete statement is a unique value or try to use "\_id" field

- `db.inspections.deleteMany({ "test": 1 })` -> deletes the documents where test is 1
- `db.inspections.deleteOne({ "test": 3 })` -> deletes first document where test is 3

4. To drop a collection one can use drop()

   `db.inspection.drop()`

5. removing all the collections in a database also removes the database itself

# QUERY OPERTAORS

# Comparison Operators

1.  $eq -> value equal to

    `db.trips.find({ "tripduration": { "$lte" : 70 },"usertype": { "$ne": "Subscriber" } }).pretty()`

2.  $lte -> value less than or equal to

    `db.trips.find({ "tripduration": { "$lte" : 70 },"usertype": { "$eq": "Customer" }}).pretty()`
    `db.trips.find({ "tripduration": { "$lte" : 70 },"usertype": "Customer" }).pretty()`

# Logical Operators

$and -> Match all specified query clauses
$or -> Atleast one query clause is matched
$nor -> Fail to match both clauses
$not -> negates the query clause

Find all documents where airplanes CR2 or A81 left or landed in the KZN airport:

```
db.routes.find({ "$and": [
                     { "$or" : [
                           { "dst_airport": "KZN" },
                           { "src_airport": "KZN" }
                        ]
                     },
                     { "$or" : [
                           { "airplane": "CR2" },
                           { "airplane": "A81" } ] }
                        ]
               }).pretty()
```

Find all companies where founded_year is 2004 and (category_code is social or web) or founded_month is 10 and (category_code is social or web)

```
db.companies.find({
   $and: [
      { $or: [ {founded_year: 2004}, {founded_month: 10} ]},
      { $or: [ {category_code: "social"}, {category_code: "web"} ]}
   ]
}).count()
```

# Expression Query Operator

1. $expr -> Match the expression

Find all documents where the trip started and ended at the same station

```
db.trips.find({ "$expr":
                  {
                     "$eq": [ "$end station id", "$start station id"]
                  }
              }).count()
```

Find all documents where the trip lasted longer than 1200 seconds, and started and ended at the same station:

```
db.trips.find({ "$expr": {
                           "$and": [
                              { "$gt": [ "$tripduration", 1200 ]},
                              { "$eq": [ "$end station id", "$start station id" ]}
                           ]
                     }
               }).count()
```

- $ sign in the above expression is used before the variable names to denote that we actually want to compare the value and not the
  variable name

How many companies in the sample_training.companies collection have the same permalink as their twitter_username?

```
db.companies.find({
   $expr: { $eq: [ "$permalink", "$twitter_username" ] }
}).count()
```

# Array Operators

$all -> returns a cursor with all documents where specified array field contains all the elements regardless of the order
$size -> returns a cursor with documents where specified array field is exactly the given length

Find all documents with exactly 20 amenities which include all the amenities listed in the query array:

```
db.listingsAndReviews.find({
   "amenities": {
      "$size": 20,
      "$all": [
         "Internet", "Wifi",  "Kitchen", "Heating", "Family/kid friendly", "Washer", "Dryer",
         "Essentials", "Shampoo", "Hangers", "Hair dryer", "Iron", "Laptop friendly workspace"
      ]
   }
}).pretty()
```

What is the name of the listing in the sample_airbnb.listingsAndReviews dataset that accommodates more than 6 people and has exactly 50 reviews?

```
db.listingsAndReviews.find({
   accommodates: { $gt : 6 },
   reviews: {
      $size: 50
   }
}).count()
```

Using the sample_airbnb.listingsAndReviews collection find out how many documents have the "property_type" "House", and include "Changing table" as one of the "amenities"?

```
db.listingAndReviews.find({
   property_type: "House",
   amenities: {
      $all: ["Changing table"]
   }
}).count()
```
