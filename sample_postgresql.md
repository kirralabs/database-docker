# Sample code Postgresql


- open connection
```python
import psycopg2
import uuid
import time
def openDB():
    try:
        conn = psycopg2.connect(database='postgres', user = "postgres", password = "mysecretpassword", host = "127.0.0.1", port = "5432")
        print ("Opened database successfully")
        return conn
    except (Exception, psycopg2.DatabaseError) as error:
        print(error)
        print("Error! Open database.")

    return None
```

- close connection 
```python
def closeDB(conn):
    try:
        conn.close()
        print ("Closed database successfully")
    except:
        print("Error! Close database.")
```

- create new table
```python
conn = openDB()

cur = conn.cursor()
if conn is not None:
    sql_create_entry_table = """ CREATE TABLE IF NOT EXISTS news (
        _id text PRIMARY KEY,
        title text NOT NULL,
        datatext text NOT NULL,
        source text NOT NULL,
        created_at timestamp  NOT NULL  DEFAULT current_timestamp,
        not_deleted  boolean DEFAULT true,
        deleted_at   timestamp
        ); """
    cur.execute(sql_create_entry_table)
    # close communication with the PostgreSQL database server
    cur.close()
    # commit the changes
    conn.commit()
    print("Table successful created.")
else:
    print("Error! cannot create table .")
closeDB(conn)
```

- drop table
```python
def dropTable(conn):
    cur = conn.cursor()
    try:
        cur.execute("DROP TABLE IF EXISTS news")
        # commit the changes to the database
        conn.commit()
        # close communication with the database
        cur.close()
        print("Drop table, successful!")
        return True
    except (Exception, psycopg2.DatabaseError) as error:
        print(error)
        print("Error! cannot drop the table.")
        return False

conn = openDB()
dropTable(conn)
closeDB(conn)
```

- insert data 
```python
def insertData(conn, ids, title, desc, source):
    cur = conn.cursor()
    sql2 = """INSERT INTO news  (
                                _id,
                                title, 
                                datatext, 
                                source
                                ) VALUES (%s, %s, %s, %s)  ON CONFLICT DO NOTHING"""
    task = (ids, title, desc, source)
    vendor_id = None
    try:
        cur.execute(sql2, task)
        # get the generated id back
        #vendor_id = cur.fetchone()[0]
        # commit the changes to the database
        conn.commit()
        # close communication with the database
        cur.close()
        print("Success insert data.")
    except (Exception, psycopg2.DatabaseError) as error:
        print(error)
        print("Error! insert data.")
    return vendor_id

conn = openDB()
ids = str(uuid.uuid4())+str(time.time())
ids
insertData(conn, ids, "title", "desc", "asal")
closeDB(conn)
```

- insert bulk data
```python
def insertListData(conn, listdata):
    """ insert multiple news into the news table  """
    sql = "INSERT INTO news(_id, title, datatext, source) VALUES(%s, %s, %s, %s)"
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
    except (Exception, psycopg2.DatabaseError) as error:
        print(error)
        print("Error! insert list data.")


conn = openDB()
insertListData(conn, [
        (str(uuid.uuid4())+str(time.time()), "title1", "desc1", "asal1"),
        (str(uuid.uuid4())+str(time.time()), "title2", "desc2", "asal2"),
        (str(uuid.uuid4())+str(time.time()), "title3", "desc3", "asal3"),
        (str(uuid.uuid4())+str(time.time()), "title4", "desc4", "asal4"),
        (str(uuid.uuid4())+str(time.time()), "title5", "desc5", "asal5")
    ])

closeDB(conn)
```

- get row data
```python
def getData(conn):
    cur = conn.cursor()
    sql_entry_select_by_data = "SELECT * FROM news"
    data = cur.execute(sql_entry_select_by_data)

    hasil = []
    for row in cur.fetchall():
        hasil.append(row)
    cur.close()
    return hasil
conn = openDB()
data = getData(conn)
closeDB(conn)
data
```

- get row data by id
```python
def getDataById(conn, ids):
    cur = conn.cursor()
    sql_entry_select_by_data = "SELECT * FROM news WHERE _id ILIKE %s"
    data = cur.execute(sql_entry_select_by_data,(ids,))

    hasil = []
    for row in cur.fetchall():
        hasil.append(row)
    cur.close()
    return hasil
conn = openDB()
data = getDataById(conn, 'd198bd14-81ce-4d84-bc8c-c6ee00e895191532508482.41')
closeDB(conn)
data
```

- update data by id
```python
def updateData(conn, ids, title, desc, sources):
    cur = conn.cursor()
    """ update news name based on the news id """
    sql = """ UPDATE news SET title = %s WHERE _id ILIKE %s"""
    updated_rows = 0
    try:
        # execute the UPDATE  statement
        cur.execute(sql, (title, ids))
        # get the number of updated rows
        updated_rows = cur.rowcount
        # commit the changes to the database
        conn.commit()
        # close communication with the database
        cur.close()
        
        print("Successful update data.")
    except (Exception, psycopg2.DatabaseError) as error:
        print(error)
        print("Error! updated data.")
 
    return ids, updated_rows

conn = openDB()

data = getData(conn)
print(data[0][0],data[0][1],data[0][2],data[0][3])

ids, numDataUpdated = updateData(conn,data[0][0],"data[0]assdda[1]aku",data[0][2],data[0][3])
data = getDataById(conn, ids)
print(data[0][0],data[0][1],data[0][2],data[0][3])

closeDB(conn)
```

- delete data by id
```python
def deleteData(conn, part_id):
    """ delete news by news id """
    sql = "DELETE FROM news WHERE _id = %s"
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
    except (Exception, psycopg2.DatabaseError) as error:
        print(error)
 
    return part_id, rows_deleted

conn = openDB()

ids, numDataUpdated = deleteData(conn,"4c668f59-b445-4c13-b78f-91c6ec5665a41532508482.41")
print(ids, numDataUpdated)
closeDB(conn)
```