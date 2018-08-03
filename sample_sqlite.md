# Sample code SqLite


- open connection
```python
import sqlite3
import uuid
import time
DB_NAME = "newsCorpus.db"
DB_VERSION = 1

def openSqliteDB():
    try:
        conn = sqlite3.connect(DB_NAME)
#         conn = sqlite3.connect(':memory:')
        print ("Opened database successfully")
        return conn
    except sqlite3.Error as e:
        print(e)
        print("Error! Open database.")
        return None
```

- close connection
```python
def closeSqliteDB(conn):
    try:
        conn.close()
        print ("Closed database successfully")
    except:
        print("Error! Close database.")
```

- create table
```python
def createSqliteTable(conn):
    sql = ''' CREATE TABLE IF NOT EXISTS news (
        _id text PRIMARY KEY,
        title text NOT NULL,
        datatext text NOT NULL,
        source text NOT NULL,
        created_at timestamp  NOT NULL  DEFAULT current_timestamp,
        not_deleted  boolean DEFAULT true,
        deleted_at   timestamp
        ); '''
    try:
        cur = conn.cursor()
        cur.execute(sql)
        # commit the changes
        conn.commit()
        # close communication with the PostgreSQL database server
        cur.close()
        print("Table successful created.")
    except (Exception, sqlite3.Error) as error:
        print("Error! cannot create table :"+error)

conn = openSqliteDB()
createSqliteTable(conn)
closeSqliteDB(conn)
```

- drop table
```python
def dropSqliteTable(conn):
    try:
        cur = conn.cursor()
        cur.execute('''DROP TABLE IF EXISTS news''')
        # commit the changes to the database
        conn.commit()
        # close communication with the database
        cur.close()
        print("Drop table, successful!")
        return True
    except (Exception, sqlite3.Error) as error:
        print(error)
        print("Error! cannot drop the table.")
        return False

conn = openSqliteDB()
dropSqliteTable(conn)
closeSqliteDB(conn)
```

- insert data
```python
def insertSqliteData(conn, ids, title, desc, source ):
    cur = conn.cursor()
    sql2 = """INSERT INTO {tn}  (
                                {cid},
                                {ct}, 
                                {cdt}, 
                                {cs}
                                ) VALUES (?, ?, ?, ?)"""
    task = (ids, title, desc, source)
    vendor_id = None
    try:
        hasil = cur.execute(sql2.format(tn="news", cid="_id", ct="title", cdt="datatext", cs="source"), task)
        # commit the changes to the database
        conn.commit()
        # close communication with the database
        cur.close()
        print("Success insert data.")
    except (Exception, sqlite3.Error) as error:
        print(error)
        print("Error! insert data.")
    return vendor_id

conn = openSqliteDB()
ids = str(uuid.uuid4())+str(time.time())
ids
insertSqliteData(conn, ids, "title", "desc", "asal")
closeSqliteDB(conn)
```

- bulk insert 
```python
def insertSqliteListData(conn, listdata):
    """ insert multiple news into the news table  """
    sql = "INSERT INTO news(_id, title, datatext, source) VALUES(?, ?, ?, ?)"
    try:
        # create a new cursor
        cur = conn.cursor()
        # execute the INSERT statement
        cur.executemany(sql,listdata)
        # commit the changes to the database
        conn.commit()
        # close communication with the database
        cur.close()
        print("Successful insert list data.")
    except (Exception, sqlite3.Error) as error:
        print(error)
        print("Error! insert list data.")


conn = openSqliteDB()
insertSqliteListData(conn, [
        (str(uuid.uuid4())+str(time.time()), "title1", "desc1", "asal1"),
        (str(uuid.uuid4())+str(time.time()), "title2", "desc2", "asal2"),
        (str(uuid.uuid4())+str(time.time()), "title3", "desc3", "asal3"),
        (str(uuid.uuid4())+str(time.time()), "title4", "desc4", "asal4"),
        (str(uuid.uuid4())+str(time.time()), "title5", "desc5", "asal5")
    ])

closeSqliteDB(conn)
```

- get data
```python
def getSqliteData(conn):
    cur = conn.cursor()
    sql_entry_select_by_data = "SELECT * FROM news"
    data = cur.execute(sql_entry_select_by_data)

    hasil = []
    for row in cur.fetchall():
        hasil.append(row)
    cur.close()
    return hasil

conn = openSqliteDB()
data = getSqliteData(conn)
closeSqliteDB(conn)
data
```

- get data by id
```python
def getSqliteDataById(conn, ids):
    cur = conn.cursor()
    sql_entry_select_by_data = "SELECT * FROM news WHERE _id = ?"
    data = cur.execute(sql_entry_select_by_data,(ids,))

    hasil = []
    for row in cur.fetchall():
        hasil.append(row)
    cur.close()
    return hasil
conn = openSqliteDB()
data = getSqliteDataById(conn, '76d1166a-93d3-4386-ba2b-737831fe0e511532570041.37')
closeSqliteDB(conn)
data
```

- update data 
```python
def updateSqliteData(conn, ids, title, desc, sources):
    cur = conn.cursor()
    """ update news name based on the news id """
    sql = """ UPDATE news SET title = ?, datatext = ?, source = ? WHERE _id = ?"""
    updated_rows = 0
    try:
        # execute the UPDATE  statement
        cur.execute(sql, (title, desc, sources, ids))
        # get the number of updated rows
        updated_rows = cur.rowcount
        # commit the changes to the database
        conn.commit()
        # close communication with the database
        cur.close()
        
        print("Successful update data.")
    except (Exception, sqlite3.Error) as error:
        print(error)
        print("Error! updated data.")
 
    return ids, updated_rows

conn = openSqliteDB()

id = '76d1166a-93d3-4386-ba2b-737831fe0e511532570041.37'
data = getSqliteDataById(conn, id)
try:
    print(data[0][0],data[0][1],data[0][2],data[0][3])
    
    ids, numDataUpdated = updateSqliteData(conn,data[0][0],"data[0]assdda[1]aku",data[0][2],data[0][3])
    data = getSqliteDataById(conn, '76d1166a-93d3-4386-ba2b-737831fe0e511532570041.37')
    print(data[0][0],data[0][1],data[0][2],data[0][3])
except:
    print("no record with id \"{}\" exist...".format(id))


closeSqliteDB(conn)
```

- delete data
```python
def deleteData(conn, part_id):
    """ delete news by news id """
    sql = "DELETE FROM news WHERE _id = ?"
    rows_deleted = 0
    try:
        # create a new cursor
        cur = conn.cursor()
        # execute the UPDATE  statement
        cur.execute(sql, (part_id,))
        # get the number of updated rows
        rows_deleted = cur.rowcount
        # Commit the changes to the database
        conn.commit()
        # Close communication with the PostgreSQL database
        cur.close()
        print("Successful deleted "+part_id+" data.")
    except (Exception, sqlite3.Error) as error:
        print(error)
        print("Error! deleted "+part_id+" data.")
 
    return part_id, rows_deleted

conn = openSqliteDB()

ids, numDataUpdated = deleteData(conn,"63adf535-e61f-4aa3-87c0-6c5837fc5c311532570041.37")
print(ids, numDataUpdated)
data = getSqliteDataById(conn, '63adf535-e61f-4aa3-87c0-6c5837fc5c311532570041.37')
print(data)
closeSqliteDB(conn)
```

