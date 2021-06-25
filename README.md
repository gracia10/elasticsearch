# elasticsearch

- 검색 엔진
- 여러 노드로 구성된 분산 시스템.
- apache lucine (java기반 검색 라이브러리) 기반
- 실시간 검색. 저장과 동시에 별도 작업없이 조회가능
- 높은 가용성. 노드별 원본,복사본존재. 노드 이상 감시 및 데이터이동 수행.
- 역색인구조(v로 k를 찾음) 로 빠른 full text search 제공
- rest API 로 접근.
- json 구조로 데이터 저장. 동적 매핑.
- Java, JavaScript, Go, .NET, PHP, Perl, Python, Ruby 지원

---

cluster

- 물리적 서버. node 묶음 (클러스터 1 - 서버N (노드N))

node

- 논리적 서버(인스턴스). 노드를 늘려 시스템 확장.
- 노드별 샤드들이 분산 저장됨. 복사본 유지해 데이터 유실 방지
- 노드 추가,삭제시 샤드 재분배
- 클러스터 내 노드는 동일 네트워크를 지향 -> 샤드재분배시 영향 느려짐 방지
- 클러스터로 묶인 노드는 데이터 교환 가능

index : indexing된 document 묶음. 논리적 DB  
document : 데이터 한개.  
fileld : 컬럼. k-v 구조. 모든 필드는 색인화  
type : table. 7.x 부터 제거됨

shard

- 물리적 DB. 인덱스 내 document 를 여러개의 샤드로 분산 저장.
- 프라이머리 샤드 (기본 샤드), replica 샤드(복제샤드) 존재
- 샤드가 작고 많은수록, 쿼리 속도는 빠르나 오버헤드 발생.

- 샤드가 디스크를 읽고 저장하는 단위는 segment
- segment의 수가 커지면 큰 세그먼트로 병합합. (merging)
- 도큐먼트 삭제는 실제 삭제가 아닌 삭제대상표시. 병합전까지 디스크차지

* merging : 리소스에 민감. 디스크 I/O에 큰영향 받음.
* indexing : 검색 가능하도록 document를 토큰으로 변환 저장(BKD Tree)

---

## CCR Cross-cluster replication

- 기존 클러스터의 인덱스를 백업한 보조 원격 클러스터 생성 및 동기화 제공
- 기존 클러스터 실패시, 보조 클러스터가 인계받아 수행. (read-only)
- 지리적으로 가까운 사용자의 읽기요청 대리 수행.

## index별 shard 수와 크기

- JVM Heap 기본 설정은 30GB. (현재 메모리 120GB 장비에 3개 씀)
- 갯수 : JVM heap 의 1GB 당 20개 미만을 권장
- 사이즈 : 서칭 환경 20GB~ 40GB. (저장환경 40~50GB)

- 루씬은 색인처리시 외부 프로세스를 사용해 별도 메모리 약 2GB 남겨야함
- ES 버전 업데이트에 따라 약 2GB 뺀 28GB가 안정적

https://www.elastic.co/kr/elasticon/conf/2016/sf/quantitative-cluster-sizing

---

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

https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html

https://www.elastic.co/guide/en/kibana/current/docker.html

---

```
// docker pull centos:7
// docker run -i -t --name centos-containert centos:7 /bin/bash

```
