# MongoDB

- mongodb does not use json but bson on which stands for binary json for storing data in your database.
- mongodb supports multiple storage engines but wired tiger is the default one is a high performant one is a really good storage engine, also we can switch the storage engine if we want to.

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

- \_id which was added automatically by mongodb and this ObjectId is simply a special type of data provided by mongodb which is a unique id.

```
db.flights.insertOne({departureAirport: 'TXT', _id: 'test_id_1'})
```

- we can override default id provided by mongodb, using adding a extra \_id field. we can use number or string as a id. but we cant use same id again it will be show a error,

```
db.flights.find()
```

- it will simply return all the documents in this collection.

```
db.stats()
```

- stats is a utility method provided by the shell which outputs some well, stats about this database. like how many collections we got in there, how many objects and we also see the average object size.

```
typeof db.numbers.findOne().number
```

- You can also check the type of a value as it is stored in mongodb by using the type of command which is provided in the shell.

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

- insert()

```
db.flights.insert([{"departureAirport": "MUC", "arrivalAirport": "SFO", "aircraft": "Airbus A380", "distance": 12000, "intercontinental": true}, {"departureAirport": "LHR", "arrivalAirport": "TXL", "aircraft": "Airbus A320", "distance": 950, "intercontinental": false}])
```

```
db.flights.insert({"departureAirport": "MUC", arrivalAirport: "SFO", aircraft: "Airbus A380", distance: 12000, intercontinental: true})
```

the insert method can still be used, it's just not recommended. it can be insert both one and manny documents. but this command not return the genarated \_id, so if you're building an application where you insert some data into the database, then often you want to get that \_id back of that insert operation and immediately use it in your app, then you have to find that \_id manually. So this is also another reason why insert is not that great.

- Ordered Inserts:

first lets insert some hobbies data with our custom \_ids. so now i got these three entries in there and i got my own \_id.

```
db.hobbies.insertMany([{_id: "sports", name: "Sports"}, {_id: "cooking", name: "Cooking"}, {_id: "cars", name: "Cars"}])
```

Now let's say I repeat that operation, so I add more hobbies. but this time leave cooking as it is. so I try to enter data with an _id that already exists in the database. What happens if I now hit enter? Well if I do this, I get an error and its says 'E11000 duplicate key error collection: test.hobbies index: \_id_ dup key: { \_id: "cooking" }'

```
db.hobbies.insertMany([{_id: "yoga", name: "Yoga"}, {_id: "cooking", name: "Cooking"}, {_id: "hiking", name: "Hiking"}])
```

Now if i see my hobbies collection then i can see only yoga is added then its stop adding hiking. the execution is faield after this error and this is the default behavior of mongodb. and this is called an ordered insert and ordered inserts simply means that every element you insert is processed standalone but if one fails, it cancels the entire insert operation but it does not rollback the elements it already inserted thats why its added the yoga and not hiking.

```
db.hobbies.find()
[
  { _id: 'sports', name: 'Sports' },
  { _id: 'cooking', name: 'Cooking' },
  { _id: 'cars', name: 'Cars' },
  { _id: 'yoga', name: 'Yoga' } <- new added
]

```

we can override this behavior. Well we can pass a second argument separated with a comma to insert many, it is a document that configures this operation. And here we can set some options and the option that helps us in this case here is the ordered option, the ordered option allows us to specify whether mongodb should perform an ordered insert which is the default.

Now if I hit enter, I still get that error but this time execution is not stoping after getting error.

```
 db.hobbies.insertMany([{_id: "yoga", name: "Yoga"}, {_id: "cooking", name: "Cooking"}, {_id: "hiking", name: "Hiking"}], {ordered: false})
```

Now you can see hiking is added successful.

```
db.hobbies.find()
[
  { _id: 'sports', name: 'Sports' },
  { _id: 'cooking', name: 'Cooking' },
  { _id: 'cars', name: 'Cars' },
  { _id: 'yoga', name: 'Yoga' },
  { _id: 'hiking', name: 'Hiking' } <- added after error
]
```

- Write Concern:

Write concern describes the level of acknowledgment requested from MongoDB for write operations

```
{ w: <value>, j: <boolean>, wtimeout: <number> }
db.persons.insertOne({name: "Surya", age: 24}, {writeConcern: {w: 1, j: true, wtimeout: 1}})
```

a. the w option to request acknowledgment that the write operation has propagated to a specified number of mongod instances or to mongod instances with specified tags.
b. the j option to request acknowledgment that the write operation has been written to the on-disk journal.
c. the wtimeout option to specify a time limit to prevent write operations from blocking indefinitely.

- Atomicity:

Let's say we have an insert one operation but this could be any write operation again, now such operation most of the time will of course succeed but it can fail as well.

In database systems, “atomicity” refers to the all-or-nothing nature of transactions. If a transaction consists of multiple operations, either all of them are executed successfully, or none of them are, ensuring the database remains in a consistent state.

Success: If the operation succeeds, the document is saved in the database.
Error: If an error occurs, the operation is rolled back, meaning no changes are made to the database.

---

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

```
db.movie.find({"rating.average": {$gt: 7.0}})
```

we can filter embedded document using dot notation but you have to wrap the full path in string.

```
db.movie.find({genres: "Drama"})
```

this query check is "Drama" is present in genres array or not.

```
db.movie.find({genres: ["Drama"]})
```

but this query get data if genres have only "Drama". its menes its check exact equality not include this item.

- Query Selectors:

query selectors, basically the different operators like $gt which allow us to narrow down the set of documents we retrieve.

1. Comparison:

```
db.movie.find({runtime: {$eq: 60}})
db.movie.find({runtime: {$ne: 60}})
db.movie.find({runtime: {$lt: 40}})
db.movie.find({runtime: {$lte: 40}})
db.movie.find({runtime: {$gt: 40}})
db.movie.find({runtime: {$gte: 40}})
db.movie.find({runtime: {$in: [30, 40]}})
db.movie.find({runtime: {$nin: [30, 40]}})
```

$eq for equal to
$ne for not equal
$lt for less than
$lte for less than equal to
$gt for greater than
$gte for greater than equal to
$in for include
$nin for not include

2. Logical

```
db.movie.find({$or: [{"rating.average": {$lt: 5}}, {"rating.average": {$gt: 9.3}}]})
```

So now I got two checks here and this showed me find all documents with a rating lower down 5 or greater than 9.3 but nothing in-between.

```
db.movie.find({$nor: [{"rating.average": {$lt: 5}}, {"rating.average": {$gt: 9.3}}]})
```

if I add nor here, it simply means return me all documents where neither of the two conditions is met.

```
db.movie.find({$and: [{"rating.average": {$gt: 9}}, {genres: "Drama"}]})
db.movie.find({"rating.average": {$gt: 9}, genres: "Drama"}) // sort form of using multiple and condition
```

$and for need to match all filters.

so why we need $and. because in some driver same key name cant be used multiple time like if want to search {genres: "Drama", genres: "Horro"} then its simple override the first key with only genres: "Horro". its means the second genres of horror will essentially just replace that key because in json documents, you can't have the same key more than once and if you do specify it more than once, the second one will just override the first one. so using same key name we need $and operator.

so then you can simply wrap these queries into separate documents which you passed to that and array and now you will have an approach that will work fine.

{$and: [{genres: "Drama"}, {genres: "Horror"}]

3. Element

```
db.users.insertMany([{name: "surya", age: 24, phone: 9876543210}, {name: "rahul", age: null, phone: "9876543211"}])
```

lets insert some user info,

```
db.users.find({age: {$exists: true}})
```

$exists check the field are exists or not, its not check value of the fields. so here we can see both the user that have age field its dose not matter age have a value or not(null).

```
db.users.find({age: {$exists: true, $ne: null}})
```

we can combine more than one filer to check is age exists but return only those field have value or not equel to null. its quite helpful for making sure you really only have documents where there is a value in that field.

```
db.users.find({phone: {$type: "number"}})
```

$type this for checking data types. its only return maching data types data.

```
db.users.find({phone: {$type: ["number", "string"]}})
```

we can pass multiple types using array.

4. Evaluation

```
db.movie.find({summary: {$regex: /musical/}})
```

$regex - regex stands for regular expression which is a way of searching text for certain patterns. a pattern is always surrounded by forward slashes and in-between you define your pattern.

$expr - expression operator

Expression is useful if you want to compare two fields inside of one document and then find all documents where this comparison returns a certain result.

lets create a sales collection.

```
db.sales.insertMany([{volume: 100, target: 120}, {volume: 89, targat:80}, {volume: 200, targat  : 177}])
```

now we can check the values using expression. if we want to refer to the field names, we have to add a dollar sign at the beginning,
this tells mongodb hey please look at the volume field and use the value of that in this expression.

this return where volume is grater than target.

```
db.sales.find({$expr: {$gt: ["$volume", "$target"]}})
```

5. Array

```
hobbies: [ { title: 'sprots', frequency: 3 } ]
hobbies: [ { title: 'cooking', frequency: 5 } ]
```

lets create a users collection with there hobbies, now let's say we want to find all users who have a hobby of sports. Now one problem we have is of course with that structure, where hobbies are embedded documents and not just strings so how to query a embedded documents.

```
db.users.find({"hobbies.title": "sprots"})
```

we have to use path and wrapping it in double quotation marks with dot notation same like assecing the nested object. always use path embedded approach("hobbies.title.n"), not only on directly embedded documents.

```
db.users.insertOne({name: "gourav", age: 32, phone: 99876543213, hobbies: ["cooking", "game", "codeing"]})

db.users.find({hobbies: {$size: 3}})
```

$size - this operater check the array element count. {hobbies: {$size: 3} this return only those document have 3 hobbies.

lets create a movie collection with genres

```
db.movieGenres.insertMany([{name: "movie 1", genres: [ 'Drama', 'Action']},{name: "movie 1", genres: [ 'Action', 'Drama']}])
```

now i want to find movies that have a genre of exactly Drama and Action but I don't care about the order.

```
db.movieGenres.find({genres: [ 'Drama', 'Action']})
```

if i find like this then i will get only first document because this document is exactly equal to first document, the Drama being the first element and Action being the second element.

But what if I don't care about the order? Well then, the all operator can help you.

```
db.movieGenres.find({genres: {$all : [ 'Drama', 'Action']}})
```

this will do is it will now search genre for these keywords and it will make sure that these items do have to exist in genre and this doesn't care about the order. And therefore now I find both documents even though the order is different within genre.

6. Comments
7. Geospatial

- Projection Operators:

projection operators that allow us to transform or kind of change the data we get back to some extent, these are read related operators.

- project embedded document:

```
db.movie.find({}, {"schedule.time": 1})
```

1. $
2. $elemMatch

now update our hobbies collection.

```
hobbies: [
  { title: 'cooking', frequency: 5 },
  { title: 'cars', frequency: 2 }
]

 hobbies: [
  { title: 'sports', frequency: 2 },
  { title: 'yoga', frequency: 3 }
]
```

Now let's say we want to find all users who have a hobby of "sports" and the frequency should be equal to "2".

```
 db.users.find({$and: [{"hobbies.title": "sports"}, {"hobbies.frequency": {$gte: 2}}]})
```

this returns both surya and rahul because both have hobbies = sports and frequency = gte 2

```
 db.users.find({$and: [{"hobbies.title": "sports"}, {"hobbies.frequency": {$gte: 3}}]})
```

now I'm looking for a frequency greater than or equal to 3, i find surya its ok beasue his frequency = 3, but what's that? I still find rahul even though for his sports hobby, the frequency is 2.

so what's going wrong here?
The thing is with this query, we're basically saying find me all documents where in hobbies, there is a document at least one document with the title sports and a document with the frequency greater than or equal to three, it does not have to be the same document, that is our issue here and indeed for rahul, yoga has a frequency greater than or equal to three.

```
db.users.find({hobbies: {$elemMatch: {title: "sports", frequency: {$gte: 3}}}})
```

Now of course it's not that uncommon that you want to ensure that one and the same element should match your conditions and for that, you have the elemMatch operator. we essentially you could say describe how a single document should look like to match our query and if it finds at least one document in the hobbies array that has a title sports and a frequency greater than or equal than three, it will include the overall document that contains that hobbies array in the result set.

3. $meta
4. $slice
   we can use $slice to slice the array element. we can use either $slice(2) or $slice[1, 2] like this [how many to skip, how many to show]

---

3. Update

- updateOne(filter, data, options)

```
db.flights.updateOne({distance: 12000}, {$set: {marker:'delete'}})
```

we first of all define a filter where we say which document should be updated. The second argument is what do you want to update, if its not there then it will create one. Now this might add a new field marker with the value delete to that first element. to update or insert new data we have to use a special keyword $set. 
$set is simply identified by mongodb when used in the update one operation to describe the changes you want to make.

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

- Update Operators:

which we'll cover in the update module allow you to modify and add additional data and this therefore does of course change the data. Inc, $inc is an example which will increment a field by one or any amount you specify.

$inc operator use for increment and decrement the value. we can use $icn like this.

```
db.users.updateOne({ _id: ObjectId('665ef8fc93092d666d0d56f8')}, {$inc: {age: 1}})
db.users.updateOne({ _id: ObjectId('665ef8fc93092d666d0d56f8')}, {$inc: {age: -1}})
```

by the way you can also combine $inc with $set, you need to pass first argument as $inc then next or second argument for $set like this.

```
db.users.updateOne({ _id: ObjectId('665ef8fc93092d666d0d56f8')}, {$inc: {age: 1}, $set: {isSports: true}})
```

$min - to check minimum value.

$max - to check maximum value.

$mul - use for multiply.

$unset - unset allows you to unset or remove a field or key value pair. its simplely delete that field.

```
db.users.updateOne({ _id: ObjectId('665ef8fc93092d666d0d56f8')}, {$unset: {phone: ""}})
```

the syntax is such that you add a document as a value and then here I want to unset phone and the value you specify here is totally up to you. Typically you use an empty string but this will basically be ignored, the important part is the field name.

$rename - rename operator helps us to rename any field. The rename operator takes a document as a value where you then specify the field name you want to rename. the new field name should be in string.

```
db.users.updateMany({}, {$rename: {age: "newAge"}})
```

upsert - simply is a combination of the word update and insert and means that if the document does not exist, it will be created. And you can do that by passing a third argument to updateOne or updateMany method.

```
db.users.updateOne({name: "john"}, {$set: {age: 29, hobbies: [{title: "gaming", frequency: 3}, {title: "reading", frequency: 5}]}}, {upsert: true})
```

now if john is not present on our collection then it will add or insert that.

- Updating Matched Array Elements:

Let's say we want to update all documents whoever matches, all documents where the person has a hobby of sports with a frequency greater or equal to 3.

```
db.users.find({hobbies: {$elemMatch: {title: "sports", frequency: {$gte: 3}}}})
```

I want to change something in exactly that element which I found in the array. {title: "sports", frequency: {$gte: 3}

I don't want to assign a brand new value to hobbies, I only want to update the element in hobbies which matched my condition here. And to do that, we can use a different syntax, I can enclose it in quotation marks and say hobbies.$ and this will automatically refer to the element matched in our filter, so in our query and then here, I can define the new value.

```
db.users.updateMany({hobbies: {$elemMatch: {title: "sports", frequency: {$gte: 3}}}}, {$set: {"hobbies.$": {title: "new_sports", frequency: 1}}})
```

{ title: 'new_sports', frequency: 1 }

"hobbies.$" -> this will override our old value.

if I don't want to override the entire document, what i want to add a new field then we can define new field name like this "hobbies.$.newFrequency".

```
db.users.updateMany({hobbies: {$elemMatch: {title: "new_sports", frequency: 1}}}, {$set: {"hobbies.$.newFrequency": 4}})
```

{ title: 'new_sports', frequency: 1, newFrequency: 4 }

when you have a query where you do select a specific element in an array and you then want to update exactly that element in your set operation. And just to make it really clear, inside of set, you could of course have updated totally other fields as well.

NOTE - "hobbies.$" this will update only first matching element of the array, if you want to update all matching element on the array then you have to use like this "hobbies.$[]" or "hobbies.$[].newFrequency"

"hobbies.$" -> giving first matching element.
"hobbies.$[]" -> giving all matching element.

- Adding Elements to Arrays:

$push - its push a new element onto the array. i can do that by now specifying a document where I describe first of all the array to which I want to push, hobbies and then the element I want to push.

```
db.users.updateOne({_id: ObjectId('665ef8fc93092d666d0d56f9')}, {$push: {hobbies: {title: "gaming", frequency: 5}}})
```

Push can also be used with more than one document. using $each which then is an array of multiple documents that should be added.

```
db.users.updateOne({_id: ObjectId('665ef8fc93092d666d0d56f9')}, {$push: {hobbies: {$each: [{}, {}]}}})
```

before pushing the element into the array we can sort the element using $sort.

```
db.users.updateOne({_id: ObjectId('665ef8fc93092d666d0d56f9')}, {$push: {hobbies: {$each: [{}, {}], $sort: {frequency: 1}}}})
```

- Removing Elements from Arrays:

```
db.users.updateOne({_id: ObjectId('665ef8fc93092d666d0d56f9')}, {$pull: {hobbies: {title: "sports"}}})
```

$pull are using for remove some spcific element

$pop are using to remove first or last element of the array. 1 = last element and -1 = first element.

```
db.users.updateOne({_id: ObjectId('665ef8fc93092d666d0d56f9')}, {$pop: {hobbies: 1}})
```

$addToSet - this operator also can push single item on the array. The question is what is the difference to push?. Previously with push, we were able to push duplicate values. The difference is that addToSet adds unique values only, so if you try to add a value that's already part of the array, it will not be added again.

```
db.users.updateOne({_id: ObjectId('665ef8fc93092d666d0d56f9')}, {$addToSet: {hobbies: {title: "gaming", frequency: 5}}})
```

---

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
db.flights.deleteMany({marker: 'toDelete', departureAirport: 'TXT'})
```

```
db.flights.deleteMany({})
```

we could also use delete many and pass an empty pair of curly braces, this should delete all elements. but if we want to delete some specific documents then we have to pass some filter.

```
db.flights.drop()
```

you can delete a full collection using drop() command.

```
db.dropDatabase()
```

deleting a database we can use dropDatabase() command.

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
- The \_id is a special field in your data, by default it's always included even if you use projection as we do it with the second argument, the \_id is always included too.

```
 db.passengers.find({}, {name: 1, _id: 0})
```

- the \_id is also always included, you have to explicitly exclude it if you don't want to add it. Now you exclude something by simply specifying its name and then using 0 instead of 1.

```
db.movie.find().count()
```

count simply has a look at the cursor and determines how many elements can I get from the cursor.

```
const dbCursor = db.users.find()
dbCursor.next()
dbCursor.next()
```

next gives me the next document.

```
dbCursor.hasNext()
```

hasNext() indicates there is no data to be fetched.

```
db.movie.find().sort({"rating.average": 1})
```

Now we can sort by adding .sort if we use pretty, before pretty but always after find, now sort takes a document where you describe how to sort.
now the value you specify here describes the direction in which you sort, 1 means ascending, -1 means descending.

```
db.movie.find().sort({"rating.average": 1, runtime: -1})
```

So you can combine multiple, as many as you want sorting criteria.

```
db.movie.find().skip(100)
```

skip() method use for skiping document/results.

```
db.movie.find().limit(2)
```

so limit also allows us to retrieve a certain amount of documents but only the amount of documents we specify.

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

we can use status dot and then the nested field, so you use dot notation to drill into your embedded document. so when having a dot in here, when using such a path to a nested field, you need to wrap the entire term with double quotation marks though otherwise it will fail.

## Data Types:

1. Text : text always uses quotation marks around the value.

Ex -> name: "surya karmakar"

2. Boolean: booleans are simple values true or false.

Ex -> isVisible: true

3. Number : we have 3 type of numbers
   a. Interger(int32) = 55, 60 etc
   b. NumberLong(int64) = 1000000000, 999999999 etc
   c. NumberDecimal = 12.40, 50.44 etc (number decimal is provided by mongodb to store high precision floating point values because normal floating point values also called doubles are basically rounded, so they're not super precise after their decimal place.)

Ex -> employees: 33, funding: 435345345345345345345, price: 12.99
Ex -> age: NumberInt(1)

4. ObjectID : the objectId object is a special object automatically generated by mongodb to give you a unique ID which is not just a unique random string but also a string that contains a temporal component. you are guaranteed to have the right order due to that ID because the older element will have an ID that comes prior to the other one, so there is this sorting built into the objectId because it respects a timestamp.

5. IOSDate : you can also save a date in your database, there is a special ISO date type which you can well use, you can construct the date and save that to your database.

Ex -> foundingDate : new Date()

6. Timestamp : the timestamp is mostly used internally. you can create it automatically, mostly you let mongodb create that for you and that is guaranteed to be unique. so even if you create two documents at the same time, they will not have exactly the same timestamp.

Ex -> insertedAt : new Timestamp()

7.  Array : [1,2,3,5]

Ex -> tags: [{title: "good"}, {title: "perfect"}]

## Schemas & Relations:

- Why Do We Use Schemas?

MongoDB is a NoSQL and document-oriented database. MongoDB stores the records in a document in BSON format. Unlike SQL databases, it allows the documents in the collection to have different structures. There are some scenarios where the user needs to have a specific structure for the data stored in the document. This is a common practice to maintain data integrity and consistency.

- Schema Validation

Schema validation in MongoDB is a feature that allows us to set the structure for the data in the document of a collection. We follow some set of rules, and validation rules, which ensure that the data we insert or update follows a specific predefined schema and ensures the data must have only specific datatypes, required fields, and validation expressions mentioned in the predefined schema.

- Structuring Documents

```
{
  title: 'Book',
  price: 12.99
},
{
  name: 'Bottle',
  available: true
}
```

1. we can use a collection with totally different products in there, so that is possible in mongodb but it's probably not what you need or what you'll use in reality.

```
{
  title: 'Book',
  price: 12.99
},
{
  title: 'Bottle',
  price: 5.99
  available: true
}
```

2. Maybe you're somewhere in between, you got some kind of schema, for example here all my products have a title and a price but you got some extra information on some of your documents, so there is maybe some extra data on some documents but the general schema is the same and there probably are some core fields which exist on every document.

```
{
  title: 'Book',
  price: 12.99
},
{
  title: 'Bottle',
  price: 5.99
}
```

3. Or you got the other extreme where all documents have exactly the same structure, like here.

## Adding Schema Validation:

Now let's create the collection differently, not implicitly once we add something but explicitly using createCollection command. createCollection command is use for to configure your collection.

1. There you first of all as a first argument to the create collection method define the name of the collection.
2. The second argument is a document where you can configure that collection and one important piece of configuration here is the validator.
3. The validator key takes another sub-document where you can now define a schema against which incoming data with insert or with inserts or updates has to validate.
4. the $jsonschema key holds the schema.
5. we can set a required key here which is an array and here, we can define names of fields in the document which will be part of that post collection that are absolutely required and if we try to add data that does not have these fields, we'll get an error or warning depending on our settings.
6. and now we can add a properties key which is another nested document where we can define for every property of every document that gets added to the post collection.

```
db.createCollection('posts', {
  validator: {
    $jsonSchema: {
      bsonType: 'object',
      required: ['title', 'text', 'creator', 'comments'],
      properties: {
        title: {
          bsonType: 'string',
          description: 'must be a string and is required'
        },
        text: {
          bsonType: 'string',
          description: 'must be a string and is required'
        },
        creator: {
          bsonType: 'objectId',
          description: 'must be an objectid and is required'
        },
        comments: {
          bsonType: 'array',
          description: 'must be an array and is required',
          items: {
            bsonType: 'object',
            required: ['text', 'author'],
            properties: {
              text: {
                bsonType: 'string',
                description: 'must be a string and is required'
              },
              author: {
                bsonType: 'objectId',
                description: 'must be an objectid and is required'
              }
            }
          }
        }
      }
    }
  }
})
```

## Changing the Validation Action:

After adding validation if we want to change the validation in letter we can do that using db.runCommand.

1. collMod, that stands for collection modifier and we define the collection which we do want to modify.
2. So now we can pass exactly what we pass to create collection, so we pass validator and then the entire schema below that.
3. now we have to set a validation action here. The default is error which blocks the action and throws an error, I'll set it to warn.

```
db.runCommand({
  collMod: 'posts',
  validator: {
    $jsonSchema: {
      bsonType: 'object',
      required: ['title', 'text', 'creator', 'comments'],
      properties: {
        title: {
          bsonType: 'string',
          description: 'must be a string and is required'
        },
        text: {
          bsonType: 'string',
          description: 'must be a string and is required'
        },
        creator: {
          bsonType: 'objectId',
          description: 'must be an objectid and is required'
        },
        comments: {
          bsonType: 'array',
          description: 'must be an array and is required',
          items: {
            bsonType: 'object',
            required: ['text', 'author'],
            properties: {
              text: {
                bsonType: 'string',
                description: 'must be a string and is required'
              },
              author: {
                bsonType: 'objectId',
                description: 'must be an objectid and is required'
              }
            }
          }
        }
      }
    }
  },
  validationAction: 'warn'
})
```

## Structure & Define Schemas Example 1:

let's say we're building a blog as the title says, so users are able to create posts, edit posts, delete posts, fetch all posts so that we can display a list of posts on a page, fetch a single post so we can read that post and also comment posts.

1. Well a user can create a post or create multiple posts. each post belogn to only one user.
2. A user can also comment a post, so a post can have multiple comments where each comment also knows which user created the comment.

- creating a users collection to store all users data.

```
db.users.insertMany([{name : "Surya Karmakar", age: 24, email: "suryak@gmail.com"}, {name: "Test User", age: 26, email: "test@gmail.com"}])
```

- creating a posts collection to store all posts data embedded with comments and users references.

```
db.posts.insertOne({title: "My first post", text: "This is my first post, I hope you like it", tags: ["new", "tech"], creator: ObjectId('66543999476db14e9969d7eb'), comments: [{ text: "I like this post", author: ObjectId('66543999476db14e9969d7ec')}]})
```

## Data Schemas and Data Modelling :

Suppose we have to store user details with thire favorit books list.

1. we can store favorit books document as a nested document.

```
{
  userName: 'surya',
  favBooks: [{...}, {...}, {...}]
}
```

2. or we can create 2 user and books collection and store books id on user document as a reference.

- Users collection

```
{
  userName: 'surya',
  favBooks: ['id:1', 'id:2', 'id:3']
}
```

- Books collection

```
{
  _id: 'id1',
  name: 'mongoDB for begeners'
}
```

## One To One Relations:

Let's say we're creating our database, our application for a hospital and there, we got patients and there, disease summary. So every patient has one disease summary which belongs to that patient only and there is only one summary per patient. the summary of patient A can never belong to patient B and the other way around.

- Using References

so using reference we can create 2 different collction like patients to store all patients details and diseaseSummary to store thire disease details.

```
db.patients.insertOne({name: "Surya", age: 24, diseaseSummary: "summary-max-1"})
db.diseaseSummary.insertOne({_id: "summary-max-1", diseases:["cold", "broken leg"]})
```

now we have a request where we need that patient and also the disease history. then we have to find both data form separate collection and combine them uisng whatever programing language you are using.

```
const diseaseSummaryID = db.patients.findOne({name: "Surya"}).diseaseSummary
db.diseaseSummary.findOne({_id: diseaseSummaryID})
```

- Using Embedded

the better approach in such a case where we have a strong one-to-one relation would be to use embedded document.

```
db.patients.insertOne({name: "Surya", age: 24, diseaseSummary: {diseases:["cold", "broken leg"]}})
```

## One To Many Relations:

we can store data using embedded document or using references both are fine. but its depends on your project requirement.

- Using Embedded

1. We have question threads and each question thread has a couple of answers, so an answer only belongs to one thread but a thread can have multiple answers, that's a typical one-to-many relationship.

Q1 -> [Ans1, Ans2, Ans3]
Q2 -> [Ans4, Ans5]
Q3 -> [Ans6]

- Using References

1. Lets say we have to store which person living in which city. so in that case city can be common for multiple person and what if we want to fetch only cities data no need the person data, in this case storing cities in a separated collection might work better.

## Many To Many Relations:

So let's say we have customers who can buy products, so we're looking at orders, a customer might buy multiple products and a product might be bought by different customers, so one customer have many products, one product have many customers. So this is a typical many-to-many relationship

## Deploying a MongoDB Server:

- Using MongoDB Atlas and Cluster:

1. Sign in a mongodb Atlas as a free.
2. Create a Organizations.
3. Create a Projects.
4. Now you can create Clusters for you project. you can create free Cluster for learning and testing mongoDB.
5. After creating cluster now you can connect with different types of drivers, compass, shell etc.
6. After connecting cluster you can create, read, update, delete etc in your cloud database.

## MongoDB & Security:

So what is authentication and authorization? These are actually two concepts which are closely related. Authentication is all about identifying users in your database, authorization on the other hand is all about identifying what these users may then actually do in the database.

1. Creating and Editing User -
   Users are created by a user with sufficient permissions with the createUser() command. we also have updateUsers(), Now keep in mind update user basically means that the administrator updates the user.

createUser()

```
db.createUser({user: "surya", pwd: "1234", roles: ["userAdminAnyDatabase"]})
```

the first argument should be user, and the value here will be the username. the second argument is pwd its for password. Now you also need to add the roles and for that, you add a roles key which holds an array of roles.

```
db.auth("surya", "1234")
```

Now what you will need to do is you now need to authenticate and you can do this with the auth command. first argument for username and second argument for password.

```
mongosh -u surya -p 1234 --authenticationDatabase admin
```

this is another way of authenticating and the authentication database part here is really important.

updateUsers()

lets another user with only read access

```
db.createUser({user: "appdev", pwd: "1234appdev", roles: ["read"]})
```

```
db.updateUser("appdev", {roles: ["readWrite"]})
```

Now the db update user command takes as a first argument the name of the user you want the update, and then the second argument is the document where you describe the change to the user.

```
db.updateUser("appdev", {roles: ["readWrite", {role: "read", db: "blog"}]})
```

we can give another database access to a user. now this user have read and write access on movies database and only read access on blog database.

```
db.getUser("appdev")
```

{
\_id: 'movies.appdev',
userId: UUID('9013af45-ed80-42f6-8995-f457288a80dd'),
user: 'appdev',
db: 'movies',
roles: [ { role: 'readWrite', db: 'movies' }, { role: 'read', db: 'blog' } ],
mechanisms: [ 'SCRAM-SHA-1', 'SCRAM-SHA-256' ]
}

using getUser we can get user info like all giving roles, userId etc.

```
db.logout()
```

logout() for user logout form shell.

2. Roles in mongoDB -

a. Database User (read, readWrite) there we got two roles, a read and a readWrite role and these roles are pretty self-explanatory. You either got a role for users who only need to find data, so who can run all these queries, use the aggregation framework but will not be able to insert or update or delete data, you can do that if you gotreadWrite role.

b. Database Admin (dbAdmin, userAdmin, dbOwner)

c. All Database Roles (readAnyDatabase, readWriteAnyDatabase, userAdminAnyDatabase, dbAdminAnyDatabase)

d. Cluster Admin (clusterManager, clusterMonitor, hostManager, clusterAdmin)

e. Backup/Restore (backup, restore)

f. Superuser (dbOwner, userAdmin, userAdminAnyDatabase, root) if you assign the db owner or a user admin role to the admin database this will kind of be a special case because the admin database is a special database and these users are then able to create new users and also change their own role and that's why it's a super user. All these roles here basically allow a user to create new users and change users, so these users can create whatever they need and therefore these are very powerful roles.
The root role is basically the most powerful role, if you assign this to a user, this user can do everything.

## Capped Collections:

Capped collections are a special type of collection which you have to create explicitly where you limit the amount of data or documents that can be stored in there and old documents will simply be deleted when well this size is exceeded. so it's basically a store where oldest data is automatically deleted when new data comes in.

```
db.createCollection("capped", {capped: true, size: 10000, max: 3})
```

Here, the option you want to set is capped to true and this will turn this into a capped collection.

Now by default, it will have a size limit of 4 bytes but you can set size to any other value and it will then automatically be turned into a multiple of 256 bytes.

Max is optional and Max allows you to also define the amount of data that can be stored in there, measured in documents, so I could cap this to three documents at most.

now insert atlest 3 data in there.

```
db.capped.insertMany([{name: "surya"}, {name: "rahul"}, {name: "gourav"}])
```

the important thing is for a capped collection, the order in which we retrieve the documents is always the order in which they were inserted.

```
  { _id: ObjectId('6662a917986dd42040679f78'), name: 'surya' },
  { _id: ObjectId('6662a917986dd42040679f79'), name: 'rahul' },
  { _id: ObjectId('6662a917986dd42040679f7a'), name: 'gourav' }
```

now if i try to insert another data it will clears the oldest document and then its add a new document.

```
 db.capped.find().sort({$natural: -1})
```

if you want to change the order and sort and reverse, there is a special key you can use and that is $natural, the natural order by which it is sorted and then for example use -1 and then it's sorted the other way around.

```
db.capped.insertOne({name: "john"})
```

```
  { _id: ObjectId('6662a917986dd42040679f79'), name: 'rahul' },
  { _id: ObjectId('6662a917986dd42040679f7a'), name: 'gourav' },
  { _id: ObjectId('6662a9e6986dd42040679f7b'), name: 'john' }
```

## Transactions:

NOTE - Transactions work on cloud database. if you want to use Transactions on local db then you have to configure you local database otherwise its wont work.

MongoDB transactions provide the means to execute multiple operations as a single logical unit of work. This ensures that either all operations within the transaction are completed successfully, or none of them take effect, maintaining data consistency and integrity.

```
db.user.insertOne({name: "surya"})

db.post.insertOne({title: "this is my first post", creator: ObjectId('6662df1a986dd42040679f7c')})
```

Now for a transaction, we need a session. session basically means that all our requests are grouped together logically you could say. You create a session and you store it in a constant

```
const session = db.getMongo().startSession()
```

now I have a session stored in there. Now that session again basically is just an object that now allows me to group all requests that I send based on that session together, that is how you can think about it.

```
session.startTransaction()
```

Now you can use that session to start a transaction,

```
const userCol = session.getDatabase("transaction").user
const postCol = session.getDatabase("transaction").post
```

So now we started the transaction, we can now get access to a collection and store them on a const variable.

```
userCol.deleteOne({_id: ObjectId('6662ec244a7b209007b07815')})
{ acknowledged: true, deletedCount: 1 }
```

now we can write all the commands we want to execute against these collections here.

after hititng enter i get the acknowledged but if I repeat db users find, you see the user still is in the database, so it hasn't been deleted yet, it was just saved as a to-do.

```
postCol.deleteOne({creator: ObjectId('6662ec244a7b209007b07815')})
{ acknowledged: true, deletedCount: 1 }
```

now deleting the post that have same user id.

```
session.commitTransaction()

or

session.abortTransaction()
```

to really commit these changes to the real database, we have to run session commitTransaction(), there also would be abortTransaction().

after commiting transaction if you find user and post collection then you can see all data are deleted.

## Nemeric Data:

1. Integers (int32) - these are full numbers, so we have no decimal places here.

```
db.number.insertOne({name: "surya", age: NumberInt("24")})
```

{
age: 24
}

2. Longs (int64) - this also full numbers no decimal places.

```
db.number.insertOne({longInt: NumberLong("99999999999999999")})
```

{
longInt: Long('99999999999999999')
}

3. Doubles (64bit) - this actually store numbers with decimal places like 1.66. that 64 bit double is the default value type mongodb uses if you pass a number with no extra information, no matter, by the way that is important, no matter if that number is theoretically an integer and has no decimal place or not, it will be stored as a 64 bit double when passing in the number through the shell. (Doubles is the default value of javascript and shell is based on Javascript)

```
db.number.insertOne({doubles: 19.99})
```

4. High Precision Doubles (128bit) - We also have a special type, high precision doubles in mongodb, these are also numbers with decimal places but there's one important difference to the normal doubles.

```
db.number.insertOne({a: NumberDecimal("0.3333"), b: NumberDecimal("0.1111")})
```

## Geospatial Data:

```
db.places.insertOne({name: "Rahara Bazar", location: {type: "Point", coordinates: [88.38697257116631, 22.723284221295522]}})
```

{ type: <GeoJSON type> , coordinates: <coordinates> }

- a field named type that specifies the GeoJSON object type
- a field named coordinates that specifies the object's coordinates. If specifying latitude and longitude coordinates, list the longitude first, and then latitude [<longitude>, <latitude> ] and you'll need to remember that to store it correctly in mongodb.

- location: { type: 'Point', coordinates: [ 88.3808289, 22.7205771 ] }
  and this is called GeoJSON object.

- find places that are near my current location using geo query.

```
db.places.find({location: {$near: {$geometry: {type: "Point", coordinates: [88.37977427176402, 22.723750899966575]}}}})
```

$near is one of the operators provided by mongodb for working with geospatial data.

$near requires another document as a value and there, you can now define a geometry for which you want to check if it's near. The $geometry again is a document and this now describes a geo json object.

if you run this query then you will get a error and this said "unable to find index for $geoNear query", so first we need to create a index for geoNear query and the index value should be "2dsphere".

```
db.places.createIndex({location: "2dsphere"})
```

Well near doesn't make much sense unless we restrict it, so typically what you would do is you would not just pass a geometry to near as we're doing it here but you would also pass another argument and define a max and min distance. distance is simply a value in meters.

```
db.places.find({location: {$near: {$geometry: {type: "Point", coordinates: [88.37977427176402, 22.723750899966575]}, $maxDistance: 750, $minDistance: 10}}})
```

- Finding Places Inside a Certain Area:

$geoWithin - This is an operator provided by mongodb that simply can help us find all elements within a certain shape, within a certain object, typically something like polygon. (geoWithin allowed us to find out which places are inside of an area)

Geowithin takes a document as a value and here we can add a geometry object which is just a geo json object.

A polygon actually uses a bit more coordinates, to be precise we need the four corners, for a polygon we add a nested array, so an array in an array and in that array, we then again add more arrays where each array now describes one longitude latitude pair of the corners of our polygon.

So essentially what we added here is p1, P2, p3, P4 to describe the four corners of our polygon. There is one important thing though, I need the p1 again at the end because a polygon has to end with a starting point so that it's treated as complete.

```
const p1 = [88.37977427176410, 22.723750899966550]
const p2 = [88.3797742717620, 22.723750899966560]
const p3 = [88.3797742717630, 22.723750899966570]
const p4 = [88.37977427176340, 22.723750899966580]

db.places.insertOne({name: "Place 1 - inside Polygon", location: {type: "Point", coordinates: [88.38697257116631, 22.723284221295572]}})

db.places.insertOne({name: "Place 2" - outside Polygon, location: {type: "Point", coordinates: [88.38697257116610, 22.723284221295510]}})

db.places.find({location: {$geoWithin: {$geometry: {type: "Polygon", coordinates: [[p1, p2, p3, p4, p1]]}}}})
```

this query returns places those are inside this Polygon.

- Finding Out If a User Is Inside a Specific Area:

So let's first of all store a polygon which we used here to find if a location inside this polygon or not.

```
const p1 = [88.37977427176410, 22.723750899966550]
const p2 = [88.3797742717620, 22.723750899966560]
const p3 = [88.3797742717630, 22.723750899966570]
const p4 = [88.37977427176340, 22.723750899966580]

db.areas.insertOne({name: "golden gate park", area: {type: "Polygon", coordinates: [[p1, p2, p3, p4, p1]]}})
```

after storing our polygon next we have to add one index

```
db.areas.createIndex({area: "2dsphere"})
```

So with the index added, we can write our query and there we want to find out basically if the user is inside of this area, we can do this by checking for geoIntersects. $geoIntersects simply returns all areas that have a common point or a common area.

```
db.areas.find({area: {$geoIntersects: {$geometry: {type: "Point", coordinates: [88.38697257116611, 22.723284221295644]}}}})
```

- Finding Places Within a Certain Radius:

first enter some points then I want to find all elements in an unsorted order that are within a certain radius.

$centerSphere - This is a helpful operator that allows you to quickly get a circle around a point. centerSphere takes an array as a value and that array has two elements. the first element is another array which holds your coordinates, again longitude and then latitude and The second element in this array is the radius then and now this radius needs to be translated manually from meters or miles to radians.

```
db.places.find({location: {$geoWithin: {$centerSphere: [[88.38697257116611, 22.723284221295644], 1 / 6378.1]}}})
```

1 km = 1 / 6378.1

- NOTE:

$near give us a sorted result but geoWithin return unsorted list.

## Aggregation Framework:

- what is the aggregation framework?

Aggregation in MongoDB is a powerful feature that allows for complex data transformations and computations on collections of documents. It enables users to group, filter, and manipulate data to produce summarized results.

It is typically performed using the MongoDB Aggregation Pipeline which is a framework for data aggregation modeled on the concept of data processing pipelines.

You have your collection and now the aggregation framework is all about building a pipeline of steps that runs on the data that is retrieved from your collection and then gives you the output in the form you needed and these steps are sometimes related to what you know from find, match.

- Using aggregate():

The aggregate method takes an array and it takes an array because we define a series of steps that should be run on our data.

The first step will receive the entire data right from the collection you could say and the next step can then do something with the data returned by the first step and so on.

$match stage - match essentially is just a filtering step, so you define some criteria on which you want to filter your data in that persons collection.
so all the things you learn there on how you can query documents, how you can match for greater than values and so on applies here too.

```
db.person.aggregate([ { $match: { gender: "female" } }])
```

$group stage - the group stage allows you to well group your data by a certain field or by multiple fields.

```
db.person.aggregate([
{$match: {gender: "female" }},
{$group: {_id: {state: "$location.state"}, totalPerson: {$sum: 1}}}
])

db.person.aggregate([
{ $match: { gender: "female" } },
{ $group: { _id: "$location.state", totalPerson: { $sum: 1 } } }] )
```

1. the first one always is \_id. Now \_id defines by which fields you want to group. the value for \_id will be a document.
   the group stage, you often see that syntax because that will be interpreted in a special way and it will basically allow you to define multiple fields by which you want to group. in this case i want to group by location state.

2. we will use the sum here by using $sum and then a value you want to add for every document that is grouped together.
   So if we have 3 people from the same location state, sum would be incremented by 1 times 3.

3. It's also important to understand that group does accumulate data, now that simply means that you might have multiple documents with the same state and group will only output one, so three documents with the same state will be merged into one because you are aggregating, you're building a sum in this case.

$sort - the sort stage also takes a document as an input to define how the sorting should happen and you can simply sort as you also sorted before.

NOTE - each pipeline stage passes some output data to the next stage and that output data is the only data that next stage has.

So this sort stage does not have access to the original data as we fetched it from the collection, it only has access to the output data of our group stage. so we can use totalPerson to sort.

```
db.person.aggregate([
{ $match: { gender: "female" } },
{ $group: { _id: "$location.state", totalPerson: { $sum: 1 } } },
{ $sort: {totalPerson: 1} }
])
```

1 = low to high
-1 = hign to low

$project - the project state works in the same way as the projection works in the find method, but its a more powerfull and customizeble.

```
db.person.aggregate([
{$project: {_id: 0, gender: 1}}
])
```

we can project our filed same as projection method. but here We can add new fields with custom value. let's say the name should be one field instead of this embedded document and this is something we can easily do with this project stage.

$concat allows you concatenate two strings and you simply pass an array here which contains the two strings.

```
db.person.aggregate([
{$project: {_id: 0, gender: 1, fullName: {$concat: ["$name.first", " ", "$name.last"]}}}
])
```

$toUpper - its take a string and return a upper case string.

```
db.person.aggregate([{
$project: {
  _id: 0,
  fullName: {
    $concat: [
      {$toUpper: "$name.first"},
      " ",
      {$toUpper: "$name.last"}
    ]
  }
}
}])
```

$substrCP - substrCP operator which returns the substring of well a string, so a part of a string. SubstrCP takes an array, the first argument is the string, The second argument is the starting character of your substring, this will be zero because strings are is zero indexed, hen it asks you for how many characters should be included in the substring and that should be one here.

substrCP: ["string", start index, total char]

```
db.person.aggregate([{
$project: {
  _id: 0,
  fullName: {
    $concat: [
      {$toUpper: "$name.first"},
      " ",
      {$toUpper: "$name.last"}
    ]
  },
  firstCharOfName: {
    $concat: [
      {$toUpper: {$substrCP: ["$name.first", 0, 1]}},
      " ",
     {$toUpper: {$substrCP: ["$name.last", 0, 1]}}
    ]
  }
}
}])
```

$convert - this convert operator are use for converting the data type.

1. the first field is input and there you simply define which value should be converted.
2. The second field and this is the last field that you need to pass is the to field, the to field defines the type you want to convert to.
3. You also can define on error and on null values, so default values that should be returned in case the transformation fails.

NOTE - you can have the same stage multiple times.

```
db.person.aggregate([
  {
    $project: {
      _id: 0,
      location: 1,
      fullName: {
        $concat: [
          { $toUpper: "$name.first" },
          " ",
          { $toUpper: "$name.last" }
        ]
      }
    }
  },
  {
    $project: {
      fullName: 1,
      location: {
        type: "Point",
        coordinates: [
          {
            $convert: {
              input:
                "$location.coordinates.longitude",
              to: "double",
              onError: 0.0,
              onNull: 0.0
            }
          },
          {
            $convert: {
              input:
                "$location.coordinates.latitude",
              to: "double",
              onError: 0.0,
              onNull: 0.0
            }
          }
        ]
      }
    }
  }
])
```

NOTE - we can use specific convert operator like $toDate, $toString etc

```
db.person.aggregate([{
$project: {
  _id: 0,
  dob: {$convert: {input: "$dob.date", to: "date"}}
}
}])

or

db.person.aggregate([{
$project: {
  _id: 0,
  dob: {$toDate: "$dob.date"}
}
}])
```

$isoWeekYear - this operator basically retrieves the year out of a date.

```
db.person.aggregate([{
$project: {
  _id: 0,
  dobYear: {$isoWeekYear: {$toDate: "$dob.date"}}
}
}])
```

$limit - the limit stage is pretty straightforward, we just define how many entries we want to see.

```
db.person.aggregate([
{
  $project: {
    _id: 0,
    dob: {$toDate: "$dob.date"}
  }
},
{$sort: {dob: 1}},
{$limit: 10}
])
```

$skip - skip stage which we have to add prior to limit, so now we skip the first 10 records and show the next 10.

```
db.person.aggregate([
{
  $project: {
    _id: 0,
    dob: {$toDate: "$dob.date"}
  }
},
{$sort: {dob: 1}},
{$skip: 10},
{$limit: 10}
])
```

NOTE - find method ordering dose't matter, but here ordering does matter because your pipeline is processed step by step.

- Writing Pipeline Results Into a New Collection:

the $out stage for output and this will take the result of your operation and write it into a collection, either a new one which is created on the fly or an existing one.

```
db.person.aggregate([
{
  $project: {
    _id: 0,
    dobYear: {$isoWeekYear: {$toDate: "$dob.date"}}
  }
},
{$out: "newCollection"}
])
```

- $push ($group accumulators)

1. grouping arrays

```
db.friend.insertMany([
  {
    "name": "Max",
    "hobbies": ["Sports", "Cooking"],
    "age": 29,
    "examScores": [
      { "difficulty": 4, "score": 57.9 },
      { "difficulty": 6, "score": 62.1 },
      { "difficulty": 3, "score": 88.5 }
    ]
  },
  {
    "name": "Manu",
    "hobbies": ["Eating", "Data Analytics"],
    "age": 30,
    "examScores": [
      { "difficulty": 7, "score": 52.1 },
      { "difficulty": 2, "score": 74.3 },
      { "difficulty": 5, "score": 53.1 }
    ]
  },
  {
    "name": "Maria",
    "hobbies": ["Cooking", "Skiing"],
    "age": 29,
    "examScores": [
      { "difficulty": 3, "score": 75.1 },
      { "difficulty": 8, "score": 44.2 },
      { "difficulty": 6, "score": 61.5 }
    ]
  }
])
```

```
db.friend.aggregate([
{ $group: {_id: "$age", allHobbies: {$push: "$hobbies"}} }
])
```

we want to combine the hobby arrays, so that we know which hobbies exist for this age group and for that age group.
push operator, the push operator allows you to push a new element into the all hobbies array for every incoming document.

$unwind - the unwind stage is always a great stage when you have an array of which you want to pull out the elements.
in prevouse query we push array inside arrary but we want to push all grouped element in the same array, so unwind stage can helps us to do this.

```
db.friend.aggregate([
{ $unwind: "$hobbies"},
{ $group: {_id: "$age", allHobbies: {$push: "$hobbies"}} }
])
```

output ->
[
{ \_id: 29, allHobbies: [ 'Sports', 'Cooking', 'Cooking', 'Skiing' ] },
{ \_id: 30, allHobbies: [ 'Eating', 'Data Analytics' ] }
]

Now we have another problem. duplicate values are pushed, to solve this issue we can use an alternative to push. Instead of push, you can use addToSet, addToSet does almost the same but avoids duplicate values. If it finds that an entry already exists, it just doesn't push the new value.

```
db.friend.aggregate([
{ $unwind: "$hobbies"},
{ $group: {_id: "$age", allHobbies: {$addToSet: "$hobbies"}} }
])
```

output
[
{ \_id: 29, allHobbies: [ 'Cooking', 'Skiing', 'Sports' ] },
{ \_id: 30, allHobbies: [ 'Data Analytics', 'Eating' ] }
]

- Using Projection with Arrays:

now. Let's say you only want to output the first value of that array instead of all the exam scores.

$slice - slice operator allows you to get back the slice of an array, It takes an array itself, the first value is the array you want to slice and the second argument for the amount of elements you want to get out of the array seen from the start.

```
db.friend.aggregate([
{ $project: {_id: 0, examScores: {$slice: ["$examScores", 1]}}},
])
```

this get only first elemnt for the array. but some time you want to last one or last 2 then you have to use -1 or -2.

```
db.friend.aggregate([
{ $project: {_id: 0, examScores: {$slice: ["$examScores", -1]}}},
])
```

$size - the size operator which calculates the length of an array.

```
db.friend.aggregate([
{ $project: {_id: 0, lenghtExamScores: {$size: "$examScores"}}}
])
```

$filter - filter allows you to filter arrays in documents inside of the projection phase.

$geoNear stage - the most important things to remember here is that it has to be the first stage.

```
db.person.aggregate([
{ $geoNear: {
  near:{
    type: "Point",
    coordinates: [-29.8113, -31.0208]
  },
  maxDistance: 1000000,
  query: {age: {$gt: 30}},
  distanceField: "distacnce"
}}
])
```

$lookup - lookup is essentially a helpful tool that allows you to fetch two related documents merged together in one document.

```
db.books.aggregate([
{ $lookup: {from: "authors", localField: "author", foreignField: "_id"}}
])
```

## Indexing:

Indexes are data structures that support the efficient execution of queries in MongoDB. They contain copies of parts of the data in documents to make queries more efficient. Without indexes, MongoDB must scan every document in a collection to find the documents that match each query.

Example:
let's say this is my collection of documents, the products collection, now by default if I don't have an index on seller set, mongodb will go ahead and do a so-called collection scan, now that simply means that mongodb to fulfill this query will go through the entire collection, look at every single document and see if seller equals Max and as you can imagine for very large collections with thousands or millions of documents, this can take a while.

so you would create an index for the seller key of the products collection here and that index then exist additionally to the collection and the index is essentially an ordered list of all the values that are placed or stored in the seller key for all the documents. every item in the index has a pointer to the full document it belongs to. Now this allows mongodb to do a so-called index scan to fulfill this query.

so it doesn't have to look at the first three records if it's only looking for records starting with M or to be precise, records equal to Max. So it can very efficiently go through that index and then find the matching products because of that ordering.

However you also shouldn't overdo it with the indexes, you will pay some performance cost on inserts and update because that extra index that has to be maintained needs to be updated with every insert. if you add a new document, you also have to add a new element to the index.

- create index:

```
db.person.createIndex({"dob.age": 1})
```

you can create indexes on embedded fields and top level fields as well. now values in that age field in an ascending (1) or descending (-1) order.

```
db.person.find({"dob.age": {$gt: 60}})
```

- remove index:

```
db.person.dropIndex({"dob.age": 1})
```

- when to use index?

if you have a query that will return a large portion or the majority of your documents, an index can actually be slower because you then just have an extra step to go through your almost entire index list and then you have to go to the collection and get all these documents,

So if you have queries that regularly return the majority or all of your documents, an index will not really help you there, it might even slow down the execution and that is important to keep in mind as a first restriction that you need to know when planning your queries.

- compound index:

compound index simply is an index with more than one field. this will essentially store one index where each entry in the index is now not on a single value but two combined values. So it does not create two indexes, that's important, it creates one index but one index where every element is a well, connected value.

```
db.person.createIndex({"dob.age": 1, gender: 1})
```

now this will create a index like this 33 male, 34 female and so on. now,

1. if we run query with both dob and gender then its use IXSCAN.

```
db.person.find({"dob.age" : 40, gender: "male"})
db.person.explain().find({"dob.age" : 40, gender: "male"})
```

2. if I just look for the age, it also used an index scan and it used that same compound index even though I never specified the gender in this request here. But the compound index can be used from left to right so to say,

3. gender alone won't work though, so if I try to look for all males with gender male, you'll see that of course works but it uses a collection scan and not an index scan because it can't look into that second value standalone. because you just have to go through all the values because they are sorted by these key value pairs and the first value of course defines the sorting for the overall index, so 33, 33 comes before 34. The male and female parts are then also sorted but only within their category, so only for 33, 34, 35, of course the male and female parts are not sorted on the overall list, makes sense right, because they're grouped together with the age numbers.

```
 db.person.explain().find({gender: "male"})
```

- sorting using index:
  So that's also something important to keep in mind that when you're sorting documents and you have a lot of documents at a given query, you might need an index to be able to sort them at all because mongodb has this threshold of 32 megabytes which it reserves in memory for the fetched documents and sorting them.

```
db.person.find({"dob.age": 35}).sort()
db.person.explain().find({"dob.age": 35}).sort()
```

- get index:

you can check all the created index uisng this query.

```
db.person.getIndexes()
```

output -
[
{ v: 2, key: { _id: 1 }, name: '_id_' },
{
v: 2,
key: { 'dob.age': 1, gender: 1 },
name: 'dob.age_1_gender_1'
}
]

the first one is actually one on the ID field and this is a default index mongodb maintains for you and then come your own indexes but you have this default index out of the box for every collection you create and therefore this is an index that will always be maintained by mongodb here automatically.

- configure the index

createIndex({index name}, {configure index})

```
db.person.createIndex({email: 1}, {unique: true})
```

So unique indexes can help you as a developer ensure data consistency and help you avoid duplicate data for fields that you need to have unique.

- partial index:

lets say you are typically only look for persons older than 60. Having an index on the date of birth age field then might make sense but the problem of course is that you have a lot of values in your index that you never actually query for.

you can actually create a partial index where you only add the values you're regularly going to look at,

```
db.person.createIndex({"dob.age": 1}, {partialFilterExpression: {"dob.age": {$gt: 60}}})
```
in this partialFilterExpression we can use any filters and fileds. like if we want to create a index only for male then we can do that like this,

```
db.person.createIndex({"dob.age": 1}, {partialFilterExpression: {gender: "male"}})
```
this will create a list of male dob list.

Note - default is compound index. 

- time to live index:

that can be very helpful for a lot of applications where you have self-destroying data, let's say sessions of users where you want to clear their data after some duration or anything like that.

```
db.session.createIndex({createdAt: 1}, {expireAfterSeconds: 10})
```
This is a special feature mongodb offers and that only works on date indexes or on date fields, on other fields it will just be ignored, you could add it but it will be ignored and there I could say every element should be removed after 10 seconds.

after adding this index, insert some document and then try to find after 10 second you can see inserted data will be removed after 10 second. you can veryfi this to find the inserted data before 10 sec.

## Query Diagnosis:

```
db.person.explain("queryPlanner").find({"dob.age": 35})
```
1. The important part here is that you can execute it like this or pass query planner as an argument to get that default minimal output where it simply tells you the winning plan and not much else.

```
db.person.explain("executionStats").find({"dob.age": 35})
```
2. you use execution stats, what we did a couple of times in this module already to see detailed summary outputs and see information about the winning plan and possibly rejected plan and also some information about how long it took.

```
db.person.explain("allPlansExecution").find({"dob.age": 35})
```
3. There also is the all plans execution option which also show shows detailed summaries and which also gives you more information about how the winning plan was chosen.

look at the milliseconds process time and also compare this to a solution where you don't use an index, so that you'll also have a look whether index scan really beats a collection scan, and another important measure is that you compare the number of keys in the index, that is what it means in the output are examined, how many documents that are examined and how many documents that are returned.

- Covered query in indexing:

some time we just want the only one filed that is stored as e index, like name or only age then we can only return our index listing key(index value) only not the full document. this make our query super fast.

```
db.customer.find({name: "Max"}, {_id: 0, name: 1})
```

- Winning plan

suppose we have multiple index so mongodb use all approch and who's ever get the value first that approch will be use and rest of the approch will be rejected.

- Multi key index:

```
db.contact.insertOne({name: "Max", hobbies: ["Cokking", "Sports"], address: [{street: "Main Street"}, {street: "Second Street"}]})
```

```
db.contact.createIndex({hobbies: 1})
```

if we create a index with array then multi key indexs are created. multikey indexes are working like normal indexes but they are stored up differently. What mongodb does is it pulls out all the values in your index key, so in hobbies in my case here, so it pulls out all the values in the array I stored in there and stores them as separate elements in an index.

So this is something to keep in mind, multikey indexes are possible but typically are also bigger.

```
db.contact.createIndex({address: 1})
db.contact.explain().find({"address.street": "Main Street"})
```
now if we run the second query then we can see it actually used a collection scan and not our index. The reason for that is that our index of course holds the whole documents and not the fields of the documents, so mongodb does not go so far to pull out the elements of an array and then pull out all field values of a nested document
that array might hold. 

```
db.contact.explain().find({address: {street: "Main Street"}})
```
now if run this then this will use because it is the whole document which is in our index in the end. 

```
db.contact.createIndex({"address.street": 1})
```
you can also use an index on a field in an embedded document which is part of an array with that multikey feature. You should just be aware that of course using multi multikey features.

Note - Still multikey index is super helpful if you have queries that regularly target array values or even nested values or values in an embedded document in arrays.

- restrictions to use multi key index:

we can create single field with multi key index like this,
```
db.contact.createIndex({name: 1, hobbies: 1})
```

but we cant created two or more multikey indexes, so addresses one and hobbies one will not work because you can't index parallel arrays. 

```
db.contact.createIndex({address: 1, hobbies: 1})
```

The reason for that is simple, mongodb would have to store the cartesian product of the values of both indexes, of both arrays, so it would have to pull out all the addresses and for every address, it would have to store all the hobbies. So if you have two addresses and five hobbies, you already have to store 10 values and that of course
becomes even worse the more values you have in addresses, so that is why this is not possible. Compound indexes with multikey indexes are possible as you see here but only with one multikey index, so with one array, not with multiple arrays. You can have multiple multikey indexes as you can see here in separate indexes but in one and the same index, only one array can be included.

- Text index:

text index is a special kind of index supported by mongodb which will essentially turn this text into an array of single words and it will store it as such, so it stores it essentially as if you had an array of these single words, one extra thing it does for you is it removes all the stop words and it stems all words, so that you have an array of keywords essentially and things like is or the or a are not stored there because that is typically something you don't search for because it's all over the place, the keywords are one matter for text searches typically.

```
db.products.insertMany([{title: "A Book", description: "This is a awesome book about young artish!"}, {title: "Red T-Shirt", description:"This T-Shirt is red and it's pretty awesome!"}])

db.products.createIndex({description: "text"})
```
This will create a text index which is a special kind of index where mongodb will go ahead and as I mentioned, remove all the stop words and store all the keywords in an array essentially, so let's have a look at this.

```
db.products.find({$text: {$search: "awesome"}})
```
why don't I have to add description, instead we just add hey I want to search for some text. The reason for that is that you may only have one text index per collection because text indexes are pretty expensive so you only have one text index where this could look into (per collection).

- sorting text index:

```
db.products.find({$text: {$search: "awesome t-shirt"}}).sort({score: {$meta: "textScore"}})
```

you can extract this meta information text score and both these words have to be written like this from mongodb so to say, it manages that for us and we can see how it scores the results and as you can see, we can then use that to order too.

- droping text index:

```
db.products.dropIndex("index_name")
```

you cant drop text index same like normal index, to drop text index you have to use index name and that you can find form getIndexes() method

- creating combine text index:

```
db.products.createIndex({title: "text", description: "text"})
```
Now there is still will only be one text index but it will contain the keywords of both the title and the description field. now i can search title also.

- creating index on background:

```
db.products.createIndex({title: "text"}, {background: true})
```
##




