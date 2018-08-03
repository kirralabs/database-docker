# sample code SqlAlchemy


- define config
```python
# http://docs.sqlalchemy.org/en/latest/core/engines.html
from sqlalchemy import exc, and_, create_engine, MetaData, event, Column, Table, Integer, String, ForeignKey, DateTime, Text
from sqlalchemy.sql import func
from sqlalchemy.engine import Engine
import logging, uuid, time

SQLITE_DB_NAME = 'sqlite:///corpus.db'

PG_USER = "postgres"
PG_PASS = "mysecretpassword"
PG_HOST = "127.0.0.1"
PG_PORT = "5432"
PG_DB = "postgres"

# https://www.compose.com/articles/using-postgresql-through-sqlalchemy/
# https://suhas.org/sqlalchemy-tutorial/
POSTGRESQL_DB_NAME = 'postgresql://{}:{}@{}:{}/{}'
POSTGRESQL_DB_NAME = POSTGRESQL_DB_NAME.format(PG_USER, PG_PASS, PG_HOST, PG_PORT, PG_DB)

# https://stackoverflow.com/questions/9447629/mongokit-vs-mongoengine-vs-flask-mongoalchemy-for-flask
# https://pythonhosted.org/Flask-MongoAlchemy/



DB_USED = POSTGRESQL_DB_NAME #SQLITE_DB_NAME
```

- define class
```python
#https://www.pythoncentral.io/introductory-tutorial-python-sqlalchemy/

from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import relationship


Base = declarative_base()
 
class News(Base):
    __tablename__ = 'news2'
    # Here we define columns for the table person
    # Notice that each column is also a normal Python instance attribute.
    _id = Column(String(64), primary_key=True, nullable=False)
    title = Column(Text, nullable=False)
    datatext = Column(Text, nullable=False)
    source = Column(String(64), nullable=False)
    aktif = Column(Integer, nullable=False, default=1)
    created_at = Column(DateTime(timezone=True), default=func.now())
    update_at = Column(DateTime(timezone=True), onupdate=func.now(), default=func.now())
    
engine = create_engine(DB_USED)
Base.metadata.create_all(engine)
```

- create session
```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

engine = create_engine(DB_USED)
# Bind the engine to the metadata of the Base class so that the
# declaratives can be accessed through a DBSession instance
Base.metadata.bind = engine
 
DBSession = sessionmaker(bind=engine)
# A DBSession() instance establishes all conversations with the database
# and represents a "staging zone" for all the objects loaded into the
# database session object. Any change made against the objects in the
# session won't be persisted into the database until you call
# session.commit(). If you're not happy about the changes, you can
# revert all of them back to the last commit by calling
# session.rollback()
session = DBSession()
```

- insert data
```python
# Insert a Person in the person table
new_news = News(_id=str(uuid.uuid4())+str(time.time()), title="title", datatext="text", source="source")
session.add(new_news)
session.commit()
```

- get all data
```python
news = session.query(News).all()
news
for x in news:
    n = x._id #news.title
    print (x._id, x.title, x.datatext, x.source)
```

- get data by id
```python
news = session.query(News).filter(News._id == "96fc17b7-3de2-40e1-bf23-2cbea40262a21532572572.03").all()
news
for x in news:
    n = x.title #news.title
    print (n)
```

- update data
```python
session.query(News).filter_by(_id=u"5e8be8c7-9872-4a05-a535-414801e26f1d1532576671.19").update({"title": u"title12"})
```

- delete data
```python
news = session.query(News).filter_by(_id=u"96fc17b7-3de2-40e1-bf23-2cbea40262a21532572572.03").delete()
news
```
