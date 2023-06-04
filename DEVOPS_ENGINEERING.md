**KAFKA**

- Kafka message format:
    - Arvo
    - JSON
    - Protobuf
- Kafka entities:
    - Topic
    - Partitioning: Kafka split message into multiple partition place on different nodes, provide kafka with scaling capacity.
    - Kafka hash message to decide which partition it send into.
    - Producer
    - Consumer
    - Schema Registry: 
        - A side server to validate schema of producer
    - Kafka stream: is a Java API that gives you easy access to all of the computational primitives of stream processing: filtering, grouping, aggregating, joining, and more, keeping you from having to write framework code on top of the consumer API to do all those things.
- Zookeeper in Kafka:
    - Zookeeper is basically used to communicate between different nodes in a cluster
    - Maintain offsets
    - Checking node status, leader election, ...
- Avoid duplication in kafka
    - On consumer side: adding UUID in message, consumer can checking uuid
    - Allow single producer per partition
- Kafka topic replication:
    - Control by replication factor, the replication factor value should be greater than 1 always (between 2 or 3). This helps to store a replica of the data in another broker from where the user can access it.
    - Multiple replicas will create confuse for client which does not know specific replicas to write into. For this reason, one replica will be choosen as leader
    , in the presence of leader, only leader can serve request. All follower, these replicas are known as ISR(in-sync-replica) can only perform sync data. The leader handle all read and write request.
- Producer write strategy:
    - Message Key: kafka using message key to hash the message and send it to specific partition. If message key dont specify, round robin will use as mean distributed across partition. 
    - Acknowledgment: 
        - Case 0: producer do not require ack
        - Case 1: producer require broker ack
        - Case all: producer require all follower

- Consumer and consumer group:
    - Parition will be distributed equally among consumer group:
        - Example 1: 3 parition, 1 consumer group with 3 consumer then each one consumer 1 parition
        - Example 2: 2 parition, 1 consumer group with 1 consumer then that one consumer consume from both 2 partitions
        - Example 3: 1 parition, 1 consumer groupe with 2 consumer then one of them will never have new data
    - Consumer offsets: 
        - Kafka store offset of each consumer group at each partition, so when it died, it will continue in current offset after backup.

- **Disadvantage of kafka**:
    - Lack of some message paradigms, certain message paradigms such as point-to-point queues, request/reply, etc. are missing in Kafka for some use cases.

- **Schema Registry**: 
    - Additional server use for contain schema ID and schema definition, when producer send message to consumer it validate the schema first before send it to consumer.

- **Kafke partition replication**
	- Each partition is assigned to be leader, if the leader fail, other will be assigned to be leader. At any given time there is one leader, other replicas is call in-sync replicas.

- **Kafka producer**:
	- Kafka producer have a routing layer, when there is error when write, retry mechanism is used.
	- How producer know message is sent successfully, it used ack-mechanism:
		- ack=0: fire and forget
		- ack=1: fire and wait for replica leader response, however error when broker write to disk is not preventable
		- acks=all: fire and wait for all replicas sync, and broker write to disk.
	- How to round to exact partition:
		- Sending with the same message key
		- Implement partitioner interface
	- Producer can use buffer memory to send multiple message at once.

```java
props.setProperty(
	ProducerConfig.BUFFER_MEMORY_CONFIG, 
	String.valueOf(64 * 1024 * 1024L)
);
```

- **Kafa consumer**:
	- Message ordering is ensure in each partition.
	- Consumer tracking current message using consumer offset.
	- Consumer offset sematic:
		- **At most once**: for unimportant message, consumer offset right after message come.
		- **At least once**: for important message, consumer offset after handle message, when consumer crash while process message, it wake up and rehandle message again.
		- **Exactly once**: using kafka api transaction.

![](img/kafka-1.png)

- **Optimal Kafka partion**:
	- Num of partion = throughput/speed, speed = fix 10Mb/s.


**RABBITMQ**

- RabbitMQ message flow:
    - Publisher send message exchange
    - Exchange route to queue base on exchange type:
        - Direct, topic, fanout, ...

- RABBIT MQ vs Kafka:
    - Similar: they all using async messaging pattern.
    - Difference:
        - RabbitMQ:
            - Architect: producers, exchanges, queues, and consumers. 
            - Routing can very flexible: fanout, direct, header, topic.
            - Message is not default persitence, it need a durable flag
            - Consumer error and re-consume message:
                - Using dead letter exchanges, when message reject by consumer or ttl expire or queue is full. Special consumer will deal with Dead letter exchanges.
            - HA and DR:
                - Rabbit support HA by replica of queue.
            - Consumer receive message that being pushed by broker.
        - Kafka:
            - Architect:  producers, brokers, and consumers. 
            - Message put to topic.
            - Message is default save in broker for specific retentiion days
            - Reliability:
                - Message order
                - Message are commited when it is written into leader and num of replica depend of 'replica factor'
                - Message will not be lost as long as one replica still keep the message
            - Consumer pull message from broker and, consumer group id keep the offset number of consumer on each topic.
            - Easily replay message

**SPARK**

- Spark is depend on hadoop for own cluster management. Hadoop is one way to implement Spark.
- Spark use Hadoop for Storage and Processing.
- The main feature of Spark is its in-memory cluster computing that increases the processing speed of an application.
- HDFS is a distributed file system that handles large data sets running on commodity hardware. 
- Component of Spark:
    - Core
    - ML
    - Grapl
    - Streaming
    - SQL
- Spark 3:
    - Faster 
    - Deprecate python 2
    - GPU support
    - Support k8s scaling
- Spark overview:
    - Fast and general engine for fast data processing.
    - Spark use DAG directed acyclic graph for speed
    - Built around RDD (resilience distributed dataset)
- RDD: abstraction of a datasets.
    - Create and manage by SparkContext
    - Example: sc.textFile("data/ml-100k/u.data")
    - Other datasource such as: cassandra, mysql, s3 can also be used.

**ELK STACK**

- LogStash configure log path and ES server:

```
input { file { path => "/var/log/apache/access.log" 
	start_position => "beginning" } } 

filter { grok { match => { "message" => "%{COMBINEDAPACHELOG}" } } 
	date { match => [ "timestamp" , "dd/MMM/yyyy:HH:mm:ss Z" ] } 
	geoip { source => "clientip" } } 

output { elasticsearch { hosts => ["localhost:9200"] } }

```

- Kibana configure

```

filebeat.prospectors: 
	- type: log enabled: true 
	- paths: - /var/log/puppetlabs/puppetserver/puppetserver.log.json - /var/log/puppetlabs/puppetserver/puppetserver-access.log.json json.keys_under_root: true output.elasticsearch: 
# Array of hosts to connect to. 
hosts: ["localhost:9200"]
```

**KAFKA VS RABBITMQ**

![](img/sw_1.png)


**CI/CD PIPELINE**

- **Continuous integration (CI)** aims to solve this problem, making agile development processes possible. Continuous integration means that every change developers make to their code is immediately integrated into the main branch of their software project. 

- **Continuous delivery (CD)** aims to solve these challenges with automation. In a CD approach, software is packaged and deployed to production as often as possible. A core principle of CD is that every change to the software can be deployed to production with no special effort. 


![](img/sw_2.png)



**TUNNING THE THREAD POOL**
- Formula:

```
Number of threads = Number of Available Cores 
						* Target CPU utilization 
						* (1 + Wait time / Service time)
```

**Step of tunning thread pools:**

- 1. Start at a maximum thread pool size of X.
- 2. Observe the system running with X concurrent threads and gather diagnostics such as throughput, response times, processor usage, monitor contention, and any other relevant resource usage.
- 3. If one of the resources exceeds (or is significantly below) a comfortable utilization level (for example, average CPU more than 90% utilized, or it is only 5%), then perform a binary search on X.



**JMETER TESTING**

- Performance test:
	- Load test: help you understand how a system behaves under an expected load.
	- Stress test: find the maximum load. 

- Step of load test:
	- Create thread group
	- Define path
	- Define parameter like ramp up, number of max user
	- Add a graph

- Assertion:
	- Response 
	- Duration


**GRPC**

gRPC is a high-performance, open-source framework for building distributed systems. Some of the key benefits of using gRPC include:

* Performance: gRPC uses Protocol Buffers, a compact binary format, as the data serialization format which leads to efficient and fast serialization and deserialization of data.

* Interoperability: gRPC can work across different programming languages and platforms, making it ideal for microservice-based architectures.

* Scalability: gRPC has built-in support for load balancing and server-side streaming, making it easier to build scalable systems.
 
* Bi-directional streaming: gRPC supports bi-directional streaming, enabling real-time communication between the client and server.
 
* Strong typing: gRPC uses Protocol Buffers to define the structure of the data being sent, which leads to stronger typing and reduced likelihood of errors.
 
* Improved developer experience: gRPC provides a simpler and more efficient way of defining and consuming APIs, reducing the time and effort required to create and maintain them.
 
* Security: gRPC supports SSL/TLS encryption to secure communication between the client and server, and also supports authentication using OAuth2.
  



**ISTIO**

- Traffic management:
	- Circuit breaker
	- Timeout
	- Canary rollouts 
	- Percentage-based traffic split

- Isito create its own service registry and detect all service in k8s cluster
	- Using this service registry, the Envoy proxies can then direct traffic to the relevant services. 

- Ingress vs Istio gateway

```yaml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx-example
  rules:
  - http:
      paths:
      - path: /testpath
        pathType: Prefix
        backend:
          service:
            name: test
            port:
              number: 80


apiVersion: gateway.networking.k8s.io/v1beta1
kind: Gateway
metadata:
  name: sample-gateway
  namespace: sample-ingress
spec:
  gatewayClassName: istio
  listeners:
  - name: http
    hostname: "*.sample.com"
    port: 80
    protocol: HTTP
    allowedRoutes:
      namespaces:
        from: All

apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: helloworld
spec:
  parentRefs:
  - name: sample-gateway
    namespace: sample-ingress
  hostnames: ["helloworld.sample.com"]
  rules:
  - matches:
    - path:
        type: Exact
        value: /hello
    backendRefs:
    - name: helloworld
      port: 5000

apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: helloworld
spec:
  parentRefs:
  - name: sample-gateway
    namespace: sample-ingress
  hostnames: ["helloworld.sample.com"]
  rules:
  - matches:
    - path:
        type: Exact
        value: /hello
    backendRefs:
    - name: helloworld-v1
      port: 5000
      weight: 90
    - name: helloworld-v2
      port: 5000
      weight: 10

``` 

![](img/istio-1.svg)



**MONGODB VS CASSANDRA**

- Mongo DB:
	- Schemaless
	- Do not support complex query but can use look up
	- Support data sharding and replicas
	- Support ACID
	- Sharding strategy:
		- Hash chunk: data is divided into chunks with responding hash range, suitable for shard key that change monotically.
		- Range shard

![](img/mongo-1.svg)

![](img/mongo-2.svg)








