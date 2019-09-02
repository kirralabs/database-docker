# database-docker
rapid database setup for faster development 


## Postgresql
#### Server :
- https://hub.docker.com/_/postgres/
- run server

```commandline
sudo docker run --name some-postgres -e POSTGRES_PASSWORD=mysecretpassword -v /home/<your-user>/PycharmProjects/clustering/data_db/postgresql:/var/lib/postgresql/data -e POSTGRES_PASSWORD=mysecretpassword -p 5432:5432 -d postgres

# or

sudo docker run --name some-postgres --add-host=database:172.17.0.1 -e POSTGRES_PASSWORD=mysecretpassword -v /home/<your-user>/PycharmProjects/clustering/data_db/postgresql:/var/lib/postgresql/data -e POSTGRES_PASSWORD=mysecretpassword -p 5432:5432 -d postgres
```

- run client

```commandline
# publish on port 9990 in computer host

sudo docker run -p 9990:80 -e "PGADMIN_DEFAULT_EMAIL=user@domain.com" -e "PGADMIN_DEFAULT_PASSWORD=SuperSecret" --link some-postgres:postgres -d dpage/pgadmin4
```

#### How to :

```text
# run with docker client server
# go to 127.0.0.1
# login >> username:user@domain.com; pass: SuperSecret
# set DB
    # host=postgres
    # db=postgres
    # user=postgres
    # pass=mysecretpassword
```

#### [#Sample Code](sample_postgresql.md) 

___
___

## MongoDB
#### Server :
- https://hub.docker.com/_/mongo/
- run server with client

```commandline
sudo docker run --name some-mongo -p 27017:27017 -v /home/<your-user>/PycharmProjects/clustering/data_db/mongo:/data/db -d mongo
sudo docker run -it --rm -p 8081:8081 --link some-mongo:mongo mongo-express

# or

sudo docker run --name some-mongo -v /home/<your-user>/mongo:/data/db -d mongo
sudo docker run -it --rm -p 8081:8081 --link some-mongo:mongo mongo-express
```

#### How to :

#### [#Sample Code](sample_mongo.md) 

___
___

## SqLite
#### Sever :

#### Client :
- https://sqlitebrowser.org/

#### How to :

#### [#Sample Code](sample_sqlite.md) 

___
___

## Sql Alchemy
#### [#Sample Code](sample_sqlalchemy.md) 

___
___

## ElasticSearch
#### Sever :
- run server 

```commandline
docker run -d -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch
```

#### Client :
- install client: https://chrome.google.com/webstore/detail/elasticsearch-head/ffmkiejjmecolpfloofpjologoblkegm

#### How to :
- run elastic server 
- cek connection with elasticsearch on google chrome
- green button >>> successful connected

#### [#Sample Code](sample_elasticsearch.md) 

___
___

## Redis
#### Server :
 - run server
 
```commandline
sudo docker run --name some-redis -d -h redis -p 6379 redis
```

#### Client :
```commandline
sudo npm install -g redis-commander

redis-commander -p 32768
```

#### How to :
- run redis server
- run client app
- access on webbrowser



## Cassandra
### Server :
- run server
```commandline
docker run -d --rm --name cassandra cassandra
```

### Client :
```commandline
docker run -d -e CASSANDRA_HOST_IP=$(docker inspect --format '{{.NetworkSettings.IPAddress}}' cassandra) -e CASSANDRA_PORT=9042 -p 3000:3000 --name cassandra-web delermando/docker-cassandra-web:v0.4.0
```

### Ref
- [#](https://medium.com/@saurabhg.engineer/how-to-run-cassandra-including-web-interface-locally-in-two-simple-steps-6c9449defb97)
- [#](https://github.com/avalanche123/cassandra-web)
