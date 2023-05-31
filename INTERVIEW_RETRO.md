# Interview retro spec

## 1. Noveo

### 1.1 Generic

- Q: What is the difference of 

```java

List<? extends Number> list1 = new ArrayList<>();

List<Number> list2 = new ArrayList<>();

```

- A: 
  - Use list1 when we want to constraint specific type to list1
  - Use list2 when we want multiple data type can be add to list2 


### 1.2 Stream in depth


- Q: what is wrong with this code and fix this:

```java
Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5);
List<Integer> list1 = stream.filter(i -> i > 3)
  .collect(Collectors.toList());
List<Integer> list2 = stream.filter(i -> i < 3)
  .collect(Collectors.toList());
```

- A: Stream has already been linked or consumed
```java
Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5);
List<Integer> list1 = stream.filter(i -> i > 3).collect(Collectors.toList());
// Stream has already been linked or consumed
List<Integer> list2 = stream.filter(i -> i < 3).collect(Collectors.toList());


// FIX:

Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5);
Map<Boolean, List<Integer>> partitions = stream.collect(Collectors.partitioningBy(n -> (n > 3)));
List<Integer> list1 = new ArrayList<>(partitions.get(true));
List<Integer> list2 = new ArrayList<>(partitions.get(false));

System.out.println(list1); // [4, 5]
System.out.println(list2); // [1, 2, 3]

```

### 1.3 Atomic vs block vs volatile

- Block: 
  - Provides mutual exclusion
  - Provides visibility guaranteed - updated values of variables modified inside synchronized context will be visible to all thread
- Volatile:
  - Volatile keyword ensure about the visibility of variables across threads. But not ensure race condition.
- Atomic variables:
  - Using CAS operation

```c
function cas(p: pointer to int, old: int, new: int) is
    if *p ≠ old
        return false
    *p ← new
    return true

function add(p: pointer to int, a: int) returns int
    done ← false
    while not done
        value ← *p  
        // Even this operation doesn't need to be atomic.
        done ← cas(p, value, value + a)
    return value + a
```

## 2. V net

- **Prometheus**: 
  - Pull metrics from exporter
  - Find target from servcice discovery
  - Push alert to alert manager
  - Configure Prometheus to find metrics:
    
```yaml
scrape_configs:
  - job_name: 'spring-boot-app'
    static_configs:
      - targets: ['localhost:8080']
```
  - Prometheus store in its own custom data layer, but store index in LevelDB
  - Fine tune Prometheus:
    - Reduce scrape interval 
    - Configure appropriate retention policies
    - Utilize federation and remote write
    - Optimize memory usage: storage.local.target-heap-size
    - Chunk encoding is used default in Prometheus, Prometheus use delta-delta encoding.
    - Crash recovery: storage.local.checkpoint-interval
    - Using prometheus federation (pull metrics from other path)


- **Kafka**:
  - Partitions: represent the unit of parallelism in Kafka

- Design, Configuration, Tuning of Apache Kafka Event Streaming Platform for high volume, and secure target architecture

- Spark, Hadoop