# Sample code MongoDB

- create connection to db
```python
from pymongo import MongoClient
from bson.objectid import ObjectId
import datetime

client = MongoClient("mongodb://127.0.0.1:27017/")
print(client)
```

- insert row data 
```python
collection = client['news']['article']

def insertDataMongo(collection, title, desc, source):
    post = {
            "title": title,
            "desc": desc,
            "source": source,
            "validated": False,
            "timestamp": datetime.datetime.utcnow(),
            "updated": datetime.datetime.utcnow()
        }
    
    try:
        key = {'title': title, 'desc': desc, "source": source}
        onedata = collection.find_one(key)
        if onedata:
            #collection.replace_one(key, post, upsert=True) #Use replace_one, update_one or update_many instead.
            print("data not inserted, data exist >>> ", post)
        else:
            post_id = collection.insert_one(post).inserted_id
            print("data inserted >>> ", post_id, post)
    except:
        print("data error >> ", x)  # pass
        
insertDataMongo(collection, "titleasdsd", "desc", "source")
```

- bulk insert to db
```python
def insertListDataMongo(collection, listdata):
    try:
        x = collection.insert_many(listdata)

        #print list of the _id values of the inserted documents:
        print(x.inserted_ids)
    except:
        print("data error list inserted")
        
mylist = [
  { "title": "title1", "desc": "desc1", "source": "source1", "validated": False, "timestamp": datetime.datetime.utcnow(), "updated": datetime.datetime.utcnow()},
  { "title": "title2", "desc": "desc2", "source": "source2", "validated": False, "timestamp": datetime.datetime.utcnow(), "updated": datetime.datetime.utcnow()},
  { "title": "title3", "desc": "desc3", "source": "source3", "validated": False, "timestamp": datetime.datetime.utcnow(), "updated": datetime.datetime.utcnow()},
  { "title": "title4", "desc": "desc4", "source": "source4", "validated": False, "timestamp": datetime.datetime.utcnow(), "updated": datetime.datetime.utcnow()},
  { "title": "title5", "desc": "desc5", "source": "source5", "validated": False, "timestamp": datetime.datetime.utcnow(), "updated": datetime.datetime.utcnow()},
  { "title": "title6", "desc": "desc6", "source": "source6", "validated": False, "timestamp": datetime.datetime.utcnow(), "updated": datetime.datetime.utcnow()},
  { "title": "title7", "desc": "desc7", "source": "source7", "validated": False, "timestamp": datetime.datetime.utcnow(), "updated": datetime.datetime.utcnow()},
  { "title": "title8", "desc": "desc8", "source": "source8", "validated": False, "timestamp": datetime.datetime.utcnow(), "updated": datetime.datetime.utcnow()},
  { "title": "title9", "desc": "desc9", "source": "source9", "validated": False, "timestamp": datetime.datetime.utcnow(), "updated": datetime.datetime.utcnow()},
  { "title": "title10", "desc": "desc10", "source": "source10", "validated": False, "timestamp": datetime.datetime.utcnow(), "updated": datetime.datetime.utcnow()},
  { "title": "title11", "desc": "desc11", "source": "source11", "validated": False, "timestamp": datetime.datetime.utcnow(), "updated": datetime.datetime.utcnow()},
  { "title": "title12", "desc": "desc12", "source": "source12", "validated": False, "timestamp": datetime.datetime.utcnow(), "updated": datetime.datetime.utcnow()},
  { "title": "title13", "desc": "desc13", "source": "source13", "validated": False, "timestamp": datetime.datetime.utcnow(), "updated": datetime.datetime.utcnow()}
]
insertListDataMongo(collection, mylist)
```

- update data 
```python
def updateDataMongo(collection, title, desc, source):
    post = {
            "title": title,
            "desc": desc,
            "source": source,
            "validated": False,
            "timestamp": datetime.datetime.utcnow(),
            "updated": datetime.datetime.utcnow()
        }
    
    try:
        key = {'title': title, 'desc': desc, "source": source}
        onedata = collection.find_one(key)
        if onedata:
            collection.replace_one(key, post, upsert=True) #Use replace_one, update_one or update_many instead.
            print("data updated >>> ", post)
        else:
            print("data not updated, data not exist >>> ", post)
            #post_id = collection.insert_one(post).inserted_id
    except:
        print("data error >> ", x)  # pass
        
updateDataMongo(collection, "titldasdasde", "desc", "source")
```

- get data by id
```python
def getData(collection, ids):
    try:
        key = {'_id': ObjectId(ids)}
        gate = collection.find(key)
        onedata = collection.find_one(key)
        return onedata
    except:
        return {}

getData(collection, "5b584312501d54c3d1abc43f")
```

- get list of data
```python
def getDataList(collection, source):
    try:
        key = {'source': source}
        gate = collection.find(key)
        onedata = collection.find(key)
        return onedata
    except:
        return {}

data = getDataList(collection, "source")
for x in data:
    print (x)
```

- get all data in collection
```python
def getDataAllList(collection):
    try:
        onedata = collection.find()
        return onedata
    except:
        return {}

data = getDataAllList(collection)
for x in data:
    print (x)
```

- delete data by id
```python
def deleteData(collection, ids):
    myquery = { "_id": ObjectId(ids) }

    x = collection.delete_one(myquery)
    print(x.deleted_count, " documents deleted.")

```

- delete data by specific column
```python
def deleteManyData(collection, key, value):
    myquery = { key: value }

    x = collection.delete_one(myquery)
    print(x.deleted_count, " documents deleted.")
```

- delete all data in collection 
```python
def deleteAllData(collection):
    x = collection.delete_many({})

    print(x.deleted_count, " documents deleted.")
deleteAllData(collection)
```