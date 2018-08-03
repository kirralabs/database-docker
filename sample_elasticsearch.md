# Sample code ElasticSearch

- define config
```python
# Create index (~table in RDBMS) in Elastic
index_name="twitter"
docType="tweets" # Warning!, "doc_type" removed in next version
nShards = 2
nReplicas = 0
# https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-similarity.html
similarity="BM25"
```

- open connection
```python
# run elastic server (better run with docker for learning) 
# docker run -d -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch
# Open Chrome extension:  Elastic - "Head" 
# https://chrome.google.com/webstore/detail/elasticsearch-head/ffmkiejjmecolpfloofpjologoblkegm

# https://elasticsearch-py.readthedocs.io/en/master/
# https://towardsdatascience.com/getting-started-with-elasticsearch-in-python-c3598e718380
from elasticsearch import Elasticsearch
server, port, timeout = '10.226.174.30', 9200, 30
try:
    conEs = Elasticsearch([{'host':server,'port':port,'timeout':timeout}])
    if conEs.ping():
        print('Sip! Elastic Server Connect')
    else:
        print('Error, not connect to ElasticSearch. please check your server, ip, and port')
except:
    print('Appications error')
```

- settings config index

```python
# Create setting index
dSetting={
        "settings":{
            "index":{
                "number_of_shards":nShards,
                "number_of_replicas":nReplicas
            }
        },
        "analysis":{
            "analyzer":{
                "english":{
                    "filter":["lowercase","indonesian_stop","indonesian_stemmer"]
                }
            }
        }
}
# ElasticSearch support Bahasa Indonesia
# https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-lang-analyzer.html
print('Done!')


# Oleh karena itu, "best practice"-nya tetap kita membutuhkan "mapping setting"
# Mapping setting bisa dilakukan ke sebagian field, lalu sisanya automatic by default
mapping={docType:{
    "properties":{
        "full_text":{
            "type":"text",
            "store":True,
            "term_vector":"yes",
            "similarity":"BM25"
        },
        "created_at":{
            "type": "date", 
            "store":True, 
            "format": "EEE MMM dd HH:mm:ss Z YYYY"
        },
        "screen_name":{
            "type":"text"
        }
    }}}
# term_vector not default, so we need to explain explicitly in mapping index
```

- create field

```python
try:
    # create new index in Elastic
    conEs.indices.delete(index=index_name,ignore=[400, 404]) # Deleted index if exist
    # Ignore 400 means to ignore "Index Already Exist" error.
    conEs.indices.create(index=index_name,body=dSetting,ignore=400) # create  the index
    conEs.indices.put_mapping(index=idx,doc_type=docType,body=mapping)
    # refresh elastic head in chrome extentions
    print('Index created')
except Exception as ex:
    print(str(ex))
    print('Error! Index not created')
```

- insert data to elasticsearch
```python
for item in tqdm(Tweets):
    doc = {'created_at':item['created_at'], 'screen_name':item['user']['screen_name'], 'full_text':item['full_text']}
    conEs.index(index=idx,doc_type=docType,body=doc)
 # check the data in plugin in chrome
```

- getData by query (single)
```python
qry = 'premi'
hsl = conEs.search(index=idx, body={"query": {"match": {'full_text':qry}}})
print('Total terdapat {} tweet yang relevan'.format(len(hsl['hits']['hits'])))
hsl['hits']['hits'][0] # first data in query result
```

- getData by query (multi field)

```python
# multi Fields Query
qry = 'asuransi justdayi'
hsl = conEs.search(index=idx, body={"query":  {'multi_match':{'query':qry, "type":"cross_fields", 'fields':['full_text','screen_name']}} })
print('Total terdapat {} tweet yang relevan'.format(len(hsl['hits']['hits'])))
hsl['hits']['hits'][0] # first data in query result
```

- getData by query (batch query)

```python
# Batch Query : For the best performance
request = []
Queries = ['aku','suka']
for i,qry in enumerate(Queries):
    req_body ={'size':nResults,'query':{'multi_match':{'query':qry,"type":"cross_fields", 'fields':['full_text','screen_name']}}} 
    request.extend([{'index': idx, 'type': docType}, req_body])
res = conEs.msearch(body=request,index=idx,doc_type=docType)['responses'] 
#Hasilnya List dengan panjang sebanyak requestnya.
len(res)
```

- getData by query (advance query)

```python
# limiting results: increasing performance
qry = 'aku'
nResults = 7 # Maksimum sebanyak ini

hsl = conEs.search(index=idx, size = nResults, body={"query": {"match": {'full_text':qry}}})
print('Total terdapat {} tweet yang relevan'.format(len(hsl['hits']['hits'])))
hsl['hits']['hits'][0] # contoh hasil query yang pertama


# limit by date: 
# analize the data between time
qry = 'aku'
dateStart, dateEnd = "16/07/2018", "17/07/2018" # "format": "dd/MM/yyyy||yyyy"
dateField = 'created_at'
Q = {"bool":{"must":{'multi_match':{'query':qry,"type":"cross_fields",'fields':['full_text']}},"filter": {"range":{dateField:{"gte":dateStart,"lte":dateEnd,"format":"dd/MM/yyyy||yyyy"}}}}}
hsl = conEs.search(index=idx,doc_type=docType,body={"query":Q})['hits']['hits']
hsl
```

- get Term vectors by id

```python
# Show term vectors
dID = hsl['hits']['hits'][0]['_id']
print(dID)

termVec=conEs.termvectors(index=idx,doc_type=docType,id=dID,fields = ['full_text'],term_statistics=True)
termVec
# https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-termvectors.html
```