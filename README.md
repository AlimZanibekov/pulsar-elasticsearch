# Pulsar -> Elasticsearch

Start pulsar and elasticsearch
```sh
$ docker-compose up -d
```

Create new tennant
```sh
$ curl --header "Content-Type: application/json" \
  --request PUT \
  --data '{ "allowedClusters": ["standalone"]}' \
  http://localhost:8080/admin/v2/tenants/tenant-1
```

Create new namespace
```sh
$ curl --header "Content-Type: application/json" \
  --request PUT \
  --data '{}' \
  http://localhost:8080/admin/v2/namespaces/tenant-1/ns-1  
```

Create Elasticsearch sink
```sh 
$ curl --header "Content-Type: multipart/form-data" \
  --request POST \
  -F url='file:///pulsar/connectors/pulsar-io-elastic-search-2.7.0-SNAPSHOT.nar;type=text/plain' \
  -F sinkConfig='{ "className": "org.apache.pulsar.io.elasticsearch.ElasticSearchSink", "archive": "/pulsar/connectors/pulsar-io-elastic-search-2.7.0-SNAPSHOT.nar", "inputs": ["persistent://tenant-1/ns-1/elastic-test"], "processingGuarantees": "EFFECTIVELY_ONCE", "parallelism": 1, "configs": {"elasticSearchUrl": "http://elasticsearch:9200", "indexName": "test_index" } };type=application/json' \
  http://localhost:8080/admin/v3/sinks/tenant-1/ns-1/elasticsearch
```

Sink config class
[https://github.com/apache/pulsar/blob/master/pulsar-common/src/main/java/org/apache/pulsar/common/io/SinkConfig.java](https://github.com/apache/pulsar/blob/master/pulsar-common/src/main/java/org/apache/pulsar/common/io/SinkConfig.java)

Endpoint
[https://github.com/apache/pulsar/blob/master/pulsar-functions/worker/src/main/java/org/apache/pulsar/functions/worker/rest/api/v3/SinksApiV3Resource.java](https://github.com/apache/pulsar/blob/master/pulsar-functions/worker/src/main/java/org/apache/pulsar/functions/worker/rest/api/v3/SinksApiV3Resource.java)


Sink manageement
```sh
$ curl --request POST http://localhost:8080/admin/v3/sinks/tenant-1/ns-1/elasticsearch/start
$ curl --request POST http://localhost:8080/admin/v3/sinks/tenant-1/ns-1/elasticsearch/restart
$ curl --request GET http://localhost:8080/admin/v3/sinks/tenant-1/ns-1/elasticsearch/status
$ curl --request DELETE http://localhost:8080/admin/v3/sinks/tenant-1/ns-1/elasticsearch
```

On MacOS
```sh
$ brew install libpulsar
```
Run producer
```sh
$ npm install
$ node ./index.js
```

Check documents in Elasticsearch
```sh
$ curl -s http://localhost:9200/test_index/_refresh
$ curl -s http://localhost:9200/test_index/_search
```
