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
List<Integer> list1 = stream.filter(i -> i > 3).collect(Collectors.toList());
List<Integer> list2 = stream.filter(i -> i < 3).collect(Collectors.toList());
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

### 1.3 Atomic vs block

### 1.4 DB Isolation level

### 1.5 How to indexing
