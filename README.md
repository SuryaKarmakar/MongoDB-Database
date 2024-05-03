# MongoDB
- mongodb does not use json but bson on which stands for binary json for storing data in your database.
- mongodb supports multiple storage engines but wired tiger is the default one is a high performant one is a really good storage engine, also we can switch the storage engine if we want to

```
show dbs
```
- its show existing databases

```
use flights
```
- you can connect to a database with the "use command" and you can connect to a brand new database by simply typing its name even if this doesn't exist yet, it will be created on the fly once you start inserting data.
- we can switch to a database with the use command though and you can even switch to databases which don't exist yet.

```
db.flights.insertOne({})
db.flights.insertOne({"departureAirport": "MUC", arrivalAirport: "SFO", aircraft: "Airbus A380", distance: 12000, intercontinental: true})
```
- db.<collection_name>.<command_name>()

```
_id: ObjectId('_id')
```
- _id which was added automatically by mongodb and this ObjectId is simply a special type of data provided by mongodb which is a unique id.

```
db.flights.insertOne({departureAirport: 'TXT', _id: 'test_id_1'})
```
- we can override default id provided by mongodb, using adding a extra _id field. we can use number or string as a id. but we cant use same id again it will be show a error,

```
db.flights.find()
```
- it will simply return all the documents in this collection.

# CRUD Operations

1. Create
- insertOne(data, options)
```
db.flights.insertOne({"departureAirport": "MUC", arrivalAirport: "SFO", aircraft: "Airbus A380", distance: 12000, intercontinental: true})
```
insertOne insert one data on collection at a time.

- insertMany(data, options)
```
db.flights.insertMany([{"departureAirport": "MUC", "arrivalAirport": "SFO", "aircraft": "Airbus A380", "distance": 12000, "intercontinental": true}, {"departureAirport": "LHR", "arrivalAirport": "TXL", "aircraft": "Airbus A320", "distance": 950, "intercontinental": false}])
```
insertMany now allows you well as the name suggests, to insert many elements by passing an array of objects.

2. Read
- find(filter, options)
- findOne(filter, options)

3. Update
- updateOne(filter, data, options)
```
db.flights.updateOne({distance: 12000}, {$set: {marker:'delete'}})
```
we first of all define a filter where we say which document should be updated. The second argument is what do you want to update, if its not there then it will create one. Now this might add a new field marker with the value delete to that first element. to update or insert new data we have to use a special keyword $set.$set is simply identified by mongodb when used in the update one operation to describe the changes you want to make. 

- updateMany(filter, data, options)
```
db.flights.updateMany({}, {$set: {marker:'toDelete'}})
```
we can simply pass empty curly braces and this will select all documents.

- replaceOne(filter, data, options)

4. Delete
- deleteOne(filter, options)
```
db.flights.deleteOne({departureAirport: 'TXT'})
```
Delete one takes a filter to find out which document to delete and now we could filter for all kinds of things. A filter is defined as a document, so with curly braces and then in its simplest form, you now simply define which key and which value you want to delete or the document with that key and value you want to delete.
this deleteOne find the first document in our database where the departure airport is txl and delete it.

- deleteMany(filter, options)
```
db.flights.deleteMany({marker: 'toDelete'})
```
we could also use delete many and pass an empty pair of curly braces, this should delete all elements. but if we want to delete some specific documents then we have to pass some filter.
