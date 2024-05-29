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

the insert method can still be used, it's just not recommended. it can be insert both one and manny documents. but this command not return the genarated _id, so if you're building an application where you insert some data into the database, then often you want to get that _id back of that insert operation and immediately use it in your app, then you have to find that _id manually. So this is also another reason why insert is not that great.

- Ordered Inserts:

first lets insert some hobbies data with our custom _ids. so now i got these three entries in there and i got my own _id.
```
db.hobbies.insertMany([{_id: "sports", name: "Sports"}, {_id: "cooking", name: "Cooking"}, {_id: "cars", name: "Cars"}])
```

Now let's say I repeat that operation, so I add more hobbies. but this time leave cooking as it is. so I try to enter data with an _id that already exists in the database. What happens if I now hit enter? Well if I do this, I get an error and its says 'E11000 duplicate key error collection: test.hobbies index: _id_ dup key: { _id: "cooking" }'

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
- The \_id is a special field in your data, by default it's always included even if you use projection as we do it with the second argument, the \_id is always included too.

```
 db.passengers.find({}, {name: 1, _id: 0})
```

- the \_id is also always included, you have to explicitly exclude it if you don't want to add it. Now you exclude something by simply specifying its name and then using 0 instead of 1.

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
