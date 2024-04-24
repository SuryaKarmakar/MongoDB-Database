# MongoDB
- mongodb does not use json but bson on which stands for binary json for storing data in your database.
- mongodb supports multiple storage engines but wired tiger is the default one is a high performant one is a really good storage engine, also we can switch the storage engine if we want to

> show dbs
- its show existing databases

> use flights
- you can connect to a database with the "use command" and you can connect to a brand new database by simply typing its name even if this doesn't exist yet, it will be created on the fly once you start inserting data.
- we can switch to a database with the use command though and you can even switch to databases which don't exist yet.

> db.flights.insertOne({})

> db.flights.insertOne({"departureAirport": "MUC", arrivalAirport: "SFO", aircraft: "Airbus A380", distance: 12000, intercontinental: true})
- db.<collection_name>.<command_name>()

> _id: ObjectId('_id')
- _id which was added automatically by mongodb and this ObjectId is simply a special type of data provided by mongodb which is a unique id.

> db.flights.insertOne({departureAirport: 'TXT', _id: 'test_id_1'})
- we can override default id provided by mongodb, using adding a extra _id field. we can use number or string as a id. but we cant use same id again it will be show a error,

> db.flights.find()
- it will simply return all the documents in this collection.

# CRUD Operations

1. Create
- insertOne(data, options)
- insertMany(data, options)

2. Read
- find(filter, options)
- findOne(filter, options)

3. Update
- updateOne(filter, data, options)
- updateMany(filter, data, options)
- replaceOne(filter, data, options)

4. Delete
- deleteOne(filter, options)
- deleteMany(filter, options)
