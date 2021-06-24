# elasticsearch

es & kibana quick guide
```
docker network create elastic

#es (single node)
docker pull docker.elastic.co/elasticsearch/elasticsearch:7.13.2
docker run --name es01-test --net elastic -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:7.13.2
curl localhost:9200


#kibana
docker pull docker.elastic.co/kibana/kibana:7.13.2
docker run --name kib01-test --net elastic -p 5601:5601 -e "ELASTICSEARCH_HOSTS=http://es01-test:9200" docker.elastic.co/kibana/kibana:7.13.2
curl localhost:5601
```










---
```
// docker pull centos:7
// docker run -i -t --name centos-containert centos:7 /bin/bash

```
