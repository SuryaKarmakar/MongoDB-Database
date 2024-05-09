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
```
db.flights.find()
db.flights.find({})
```
without any arguments or empty curly braces find return all the documents in the collection.
```
db.flights.find({intercontinental:true})
```
if we want some specific documents then we need to use filters.
```
db.flights.find({distance: {$gt: 1000}})
db.flights.find({distance: {$lt: 1000}})
```
$gt and $lt is special operators provided by mongodb and this means greater than($gt) and less than($lt).

- findOne(filter, options)
```
db.flights.findOne({distance: {$gt: 500}})
```
findOne is same like find but its only retun first matching element of the collection.

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

- update(filter, data, options)
```
db.flights.update({_id: ObjectId('66348627b26c361323c8edb2')}, {$set:{delayed: true}})
```
update works a bit like updateMany, updateMany was used to update all matching elements and update would also update all matching elements. 

- replaceOne(filter, data, options)
```
db.flights.replaceOne({_id: ObjectId('66348627b26c361323c8edb2')}, {delayed: false})
```
replaceOne use to replace the existing document to new document. replaceOne dont need $set. it overwrote all the other key value pairs and that is the thing about replaceOne. replaceOne does accept this syntax with just an object and it will then take this object and basically replace the existing object with this new object, with this new document, it will only keep the ID.

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

## find() & the Cursor Object :
```
 db.passengers.insertMany ([{ "name": "Max Schwarzmueller", "age": 29 }, { "name": "Manu Lorenz", "age": 30 }, { "name": "Chris Hayton", "age": 35 }, { "name": "Sandeep Kumar", "age": 28 }, { "name": "Maria Jones", "age": 30 }, { "name": "Alexandra Maier", "age": 27 }, { "name": "Dr. Phil Evans", "age": 47 }, { "name": "Sandra Brugge", "age": 33 }, { "name": "Elisabeth Mayr", "age": 29 }, { "name": "Frank Cube", "age": 41 }, { "name": "Karandeep Alun", "age": 48 }, { "name": "Michaela Drayer", "age": 39 }, { "name": "Bernd Hoftstadt", "age": 22 }, { "name": "Scott Tolib", "age": 44 }, { "name": "Freddy Melver", "age": 41 }, { "name": "Alexis Bohed", "age": 35 }, { "name": "Melanie Palace", "age": 27 }, { "name": "Armin Glutch", "age": 35 }, { "name": "Klaus Arber", "age": 53 }, { "name": "Albert Twostone", "age": 68 }, { "name": "Gordon Black", "age": 38 } ])
```
- find command in general no matter on which collection you use it gives you back a cursor object and not all the data.
- Find does not give us an array of all the documents in a collection and that makes a lot of sense because that collection could be very big and if find would immediately send us back all documents and you think about a collection with let's say 20 million documents, then this would take super long but most importantly, it would send a lot of data over the wire, so instead of that, it gives us back cursor object which is an object with a lot of metadata behind it that allows us to cycle through the results and that is what that it command did, it basically used that cursor to fetch the next bunch of data.

```
db.passengers.find().toArray()
```
toArray go through the entire list and fetch all the documents and not stop after the first 20, which by the way is simply a feature by the mongodb shell, it gives you the first 20 documents automatically but then stops, toArray simply gets them all and gives you an array.

```
db.passengers.find().forEach((item) => {printjson(item)})
```
you can use forEach to loop through the collection

## Understanding Projection : 
- projection is use to filter out the data in server before sending the responce. sending all data would impact your bandwidth, you would fetch unnecessary data and you want to prevent this, so it would be better to filter this out on the mongodb server and this is exactly what projection can help you with.

```
 db.passengers.find({}, {name: 1})
```
- we are not filtering the ducuments, the first argument use to filtering ducuments and projection use to filtering the keys present in ducuments.
- projection use second argument, so that first argument now is an empty document because I want to find all passengers and I don't want to pass a filter and the second argument now allows us to project.
- here(second argument) you simply specify which key value pairs you want to get. so let's say in our passengers, we have a name and we want to get that name, so we type name here and then as a value, simply a 1, this means include it in the data you're returning to me.
- The _id is a special field in your data, by default it's always included even if you use projection as we do it with the second argument, the _id is always included too.

```
 db.passengers.find({}, {name: 1, _id: 0})
```
- the _id is also always included, you have to explicitly exclude it if you don't want to add it. Now you exclude something by simply specifying its name and then using 0 instead of 1.

## Embedded Documents & Arrays :

- Adding nested documents :
```
db.flights.updateMany({}, {$set: {status: {description : "on-time", lastUpdated: "1 hour ago"}}})
db.flights.updateMany({}, {$set: {status: {description : "on-time", lastUpdated: "1 hour ago", details: {responsible: "Surya Karmakar"}}}})

db.passengers.updateOne({_id: ObjectId('663c9efc62ddf771fbc0b008')}, {$set: {hobbies: ["sports", "cooking"]}})
```
you can have multiple such documents and these documents can have other sub documents which can have other sub documents, so you can nest your documents all in one overarching document in one collection.

- Accessing Structured Data :
```
db.passengers.findOne({_id: ObjectId('663c9efc62ddf771fbc0b008')})
db.passengers.findOne({_id: ObjectId('663c9efc62ddf771fbc0b008')}).hobbies
```
We can now access hobbies array using findOne({}).hobbies. find will give us that person and .hobbies should give us access to the array.

```
 db.passengers.findOne({hobbies: "sports"})
```
if we want to find all passengers with a hobby of sports then we can search like this ({hobbies: "sports"}). mongodb is clever enough to see that hobbies actually is an array so it will simply look if that array has one element named sports and then it gives us the entire document as a result.

```
db.flights.find({'status.description': 'on-time'})
```
