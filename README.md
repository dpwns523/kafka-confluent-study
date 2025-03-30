# kafka-confluent-study
___

해당 리포는 [인프런 강의](https://www.inflearn.com/course/%EC%B9%B4%ED%94%84%EC%B9%B4-%EC%99%84%EB%B2%BD%EA%B0%80%EC%9D%B4%EB%93%9C-%EC%BD%94%EC%96%B4/dashboard)를 보고 실습한 내용을 반영합니다.

___
### 환경 설정
Kafka Server
- 4CPU, Mem 8G, Disk 30G (t2.large)
- openjdk 11
- kafka confluent 7.1.2

[설치가이드 공식문서 참고](https://docs.confluent.io/platform/current/get-started/platform-quickstart.html)

> curl --silent --output docker-compose.yml https://raw.githubusercontent.com/confluentinc/cp-all-in-one/7.1.2-post/cp-all-in-one/docker-compose.yml
___

기본 디렉터리 구조
```
kafka-confluent-study/
├── docker-compose.yml
├── zookeeper-data/
├── zookeeper-datalog/
├── kafka-config/
├── schema-registry-config/
├── connect-config/
├── connect-plugins/
├── connect-logs/
├── control-center-config/
├── control-center-data/
├── control-center-logs/
├── ksqldb-config/
├── ksqldb-data/
├── ksqldb-logs/
├── rest-proxy-config/
└── rest-proxy-logs/
```

Docker 컨테이너 실행 순서 Run: `docker-compose up -d`

```
# 컨테이너 실행 순서 및 healthcheck 대기 과정(각 의존성이 있는 컨테이너 기동 후 기동되어야 함):
# 1. ZooKeeper 컨테이너 실행
#   - healthcheck: nc -zv localhost 2181 (ZooKeeper 포트 연결 확인)

# 2. Kafka Broker 컨테이너 실행 (ZooKeeper 의존성)
#   - healthcheck: kafka-topics --bootstrap-server localhost:9092 --list (Kafka Broker 연결 확인)

# 3. Schema Registry 컨테이너 실행 (Kafka Broker 의존성)
#   - healthcheck: curl -f http://localhost:8081/subjects (Schema Registry API 확인)

# 4. Kafka Connect 컨테이너 실행 (Kafka Broker, Schema Registry 의존성)
#   - healthcheck: curl http://localhost:8083/connectors (Kafka Connect API 확인)

# 5. ksqlDB Server 컨테이너 실행 (Kafka Broker, Kafka Connect 의존성)
#   - healthcheck: curl -f http://localhost:8088/info (ksqlDB Server API 확인)

# 6. Control Center 컨테이너 실행 (Kafka Broker, Schema Registry, Kafka Connect, ksqlDB Server 의존성)
#   - healthcheck: curl -f http://localhost:9021 (Control Center API 확인)

# 7. ksqlDB CLI 컨테이너 실행 (Kafka Broker, Kafka Connect, ksqlDB Server 의존성)
#   - CLI 환경 제공 (tty: true)

# 8. ksqlDB Data Generator 컨테이너 실행 (Kafka Broker, Schema Registry, ksqlDB Server 의존성)
#   - Kafka, Schema Registry 준비 완료 후 데이터 생성 시작

# 9. REST Proxy 컨테이너 실행 (Kafka Broker, Schema Registry 의존성)
#   - healthcheck: curl -f http://localhost:8082/topics (REST Proxy API 확인)
```
