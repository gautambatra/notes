# Mongo

```
# Start server:
mongod --dbpath <path> --port <port> --fork --logpath <path>

# Shutdown server:
mongod --dbpath <path> --port <port> --shutdown

 
# write query result to json
mongo --port 27018 myDB --quiet --eval 'DBQuery.shellBatchSize = 2000; db.myCollection.find({}).limit(5000).toArray()' > out.json

 
# 
mongo --port 27018 myDB --quiet --eval 'DBQuery.shellBatchSize = 2000; db.myCollection.find({"fieldName.subFieldName": false}, { arrayName: {$slice: -1}}).limit(5000).toArray()' > out.json


# query
db.tests.find({"fieldName.subFieldName": false}, { status: {$slice: -1}})

# Remove first element from an array
db. myCollection.update({"fieldName ":"some string value"}, {$pop: {"arrayName": -1}})

# Removing element at specific index (eg 7) from an array
db. myCollection.update({"fieldName ":"some string value"}, {$unset: {" arrayName.7" : 1}})

db. myCollection.update({"fieldName ":" some string value "}, {$pull: {" arrayName " : null}})

 
# Remove last element from an array
db. myCollection.update({"fieldName ":" some string value "}, {$pop: {" arrayName ": 1}})

# Add element at specific index:
db. myCollection.update({"fieldName ":" some string value "}, { $push: { arrayName: { $each: [ newval ], $position: 10 }}})

# update field in all elements of an array:
db. myCollection.updateOne({"fieldName ": " some string value "}, { $set: { 'arrayName.$[].elementField': newval } })
```
