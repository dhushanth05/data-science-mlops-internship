# Apache Spark RDD — Complete Learning Article

## Introduction

**Apache Spark** is a fast big-data processing framework designed for distributed and cluster computing.

One of its foundational data structures is the **RDD (Resilient Distributed Dataset)**. RDDs provide distributed storage and processing, in-memory computation, partitioning, fault tolerance, lazy evaluation, and parallel processing.

This article covers:

- Why RDDs are needed
- What an RDD is
- Key features of RDDs
- Ways to create RDDs
- Transformations and actions
- Narrow and wide transformations
- Practical RDD operations
- IPL data example
- Pokémon data example

---

## 1. Why Do We Need RDDs?

Modern applications such as machine learning and recommendation systems work with very large datasets. Many of these workloads involve **distributed and iterative computations**.

Traditional MapReduce-based processing can become inefficient for iterative workloads because intermediate results often need to be written to and read from storage. This introduces additional latency.

Spark addresses this problem through **in-memory processing**.

Instead of repeatedly moving intermediate data between processing and storage, Spark can keep frequently used data in memory. RDDs were a fundamental abstraction that enabled this style of distributed processing.

---

## 2. What Is an RDD?

**RDD stands for Resilient Distributed Dataset.**

An RDD is a distributed collection of data that is split across multiple partitions and processed across nodes in a cluster.

### Meaning of RDD

| Term | Meaning |
|---|---|
| **Resilient** | Can recover lost data using lineage |
| **Distributed** | Data is distributed across multiple partitions/nodes |
| **Dataset** | Represents a collection of data |

### Important characteristics

- RDDs are distributed.
- RDDs are partitioned.
- RDDs are immutable.
- RDDs support parallel processing.
- RDDs provide fault tolerance through lineage.
- RDDs can be persisted in memory.
- RDD operations are evaluated lazily.

An RDD is **not a distributed file system**. It is a distributed data abstraction used by Spark for processing data.

---

## 3. Features of RDDs

### 3.1 In-Memory Computation

Spark can keep data in memory, which can significantly reduce the repeated disk I/O associated with traditional processing.

This is especially useful for iterative workloads such as machine learning.

### 3.2 Lazy Evaluation

Transformations are not executed immediately.

Spark records the transformations and executes them only when an **action** is called.

For example:

```python
filtered_rdd = rdd.filter(lambda x: x > 10)
```

The filtering operation is not necessarily executed at this point.

When an action is called:

```python
filtered_rdd.collect()
```

Spark executes the required computation.

### 3.3 Fault Tolerance

RDDs maintain information about how they were created through **lineage**.

If a partition is lost, Spark can recompute the lost partition using the transformations that produced it.

### 3.4 Immutability

RDDs are immutable.

A transformation does not modify the original RDD. Instead, it creates a new RDD.

```python
rdd2 = rdd1.map(lambda x: x * 2)
```

Here, `rdd1` remains unchanged and `rdd2` represents the transformed data.

### 3.5 Partitioning

RDD data is divided into partitions.

Partitions allow Spark to process different parts of the data in parallel.

You can inspect the number of partitions using:

```python
rdd.getNumPartitions()
```

### 3.6 Persistence

An RDD can be persisted so that its computed data can be reused.

```python
rdd.persist()
```

This can avoid recomputing the same transformations repeatedly.

### 3.7 Coarse-Grained Operations

RDD operations are applied to collections or partitions of data rather than individual records through an independent task model.

---

# 4. Creating RDDs

There are three common approaches covered in the source material.

## Method 1: Parallelized Collections

A local Python collection can be converted into an RDD.

```python
from pyspark import SparkContext

sc = SparkContext.getOrCreate()

days = [
    "Monday",
    "Tuesday",
    "Wednesday",
    "Thursday",
    "Friday",
    "Saturday"
]

days_rdd = sc.parallelize(days)

print(days_rdd.collect())
```

---

## Method 2: External Storage

Data can be loaded from external storage such as HDFS.

```python
spark_file = sc.textFile("path/to/file.txt")

print(spark_file.collect())
```

For example:

```python
spark_file = sc.textFile("hdfs://path/to/file.txt")
```

The exact path depends on the environment.

---

## Method 3: Creating an RDD from an Existing RDD

A new RDD can be created by applying a transformation to an existing RDD.

```python
words = sc.parallelize([
    "Spark",
    "is",
    "a",
    "very",
    "powerful",
    "language"
])

word_pairs = words.map(lambda word: (word, word[0]))

print(word_pairs.collect())
```

The original `words` RDD is unchanged.

---

# 5. RDD Operations

RDD operations are broadly divided into:

1. **Transformations**
2. **Actions**

---

## 5.1 Transformations

Transformations create a new RDD from an existing RDD.

Examples:

- `map()`
- `filter()`
- `flatMap()`
- `mapPartitions()`
- `reduceByKey()`
- `union()`

Transformations are **lazy**.

### Example

```python
numbers = sc.parallelize([1, 2, 3, 4, 5])

squared = numbers.map(lambda x: x * x)

print(squared.collect())
```

---

# 6. Narrow Transformations

A **narrow transformation** can process data without requiring data to be shuffled across multiple parent partitions.

Examples include:

- `map()`
- `filter()`
- `flatMap()`
- `mapPartitions()`

### Example

```python
rdd = sc.parallelize([1, 2, 3, 4, 5])

result = rdd.filter(lambda x: x % 2 == 0)

print(result.collect())
```

Output:

```text
[2, 4]
```

---

# 7. Wide Transformations

A **wide transformation** can require data to be redistributed or shuffled across partitions.

Examples include:

- `reduceByKey()`
- `groupByKey()`
- `sortByKey()`

### Example

```python
data = sc.parallelize([
    ("A", 10),
    ("B", 20),
    ("A", 30)
])

result = data.reduceByKey(lambda x, y: x + y)

print(result.collect())
```

Output:

```text
[("A", 40), ("B", 20)]
```

---

# 8. Actions

Actions trigger execution and return a result or perform an output operation.

Common actions include:

- `collect()`
- `count()`
- `take()`
- `first()`
- `max()`
- `min()`

### `collect()`

```python
rdd.collect()
```

Returns all elements to the driver.

> Avoid using `collect()` on very large RDDs because all returned data is brought to the driver.

### `count()`

```python
rdd.count()
```

Returns the number of elements.

### `take()`

```python
rdd.take(10)
```

Returns the first 10 elements.

### `first()`

```python
rdd.first()
```

Returns the first element.

---

# 9. Practical Example — IPL Match Data

The source material uses IPL match data to demonstrate RDD operations.

The dataset contains IPL match records from **2008 to 2017**.

A CSV file can be loaded as text:

```python
matches_rdd = sc.textFile("matches.csv")
```

To inspect the first line:

```python
print(matches_rdd.first())
```

To inspect all records:

```python
matches_rdd.collect()
```

---

## Finding Cities Where Matches Were Conducted

Suppose the city is stored in the required CSV column.

A transformation can extract the city:

```python
states_rdd = matches_rdd.map(
    lambda line: line.split(",")[2]
)
```

Then:

```python
print(states_rdd.collect())
```

> The exact column index depends on the CSV structure. For production data, prefer a CSV parser rather than splitting raw CSV text with `split(",")`.

---

## Finding the City With the Maximum Number of Matches

Create key-value pairs:

```python
city_count = states_rdd.map(lambda city: (city, 1))
```

Then aggregate:

```python
city_count = city_count.reduceByKey(lambda x, y: x + y)
```

Sort by count:

```python
sorted_city_count = city_count.sortBy(
    lambda x: x[1],
    ascending=False
)
```

Get the top result:

```python
print(sorted_city_count.take(1))
```

The source example reports **Mumbai** as the city with the highest number of matches in the referenced dataset.

---

# 10. Filtering Data

The `filter()` transformation keeps records that satisfy a condition.

### Matches from 2017

```python
matches_2017 = matches_rdd.filter(
    lambda line: line.split(",")[1] == "2017"
)

print(matches_2017.collect())
```

### Matches from 2016

```python
matches_2016 = matches_rdd.filter(
    lambda line: line.split(",")[1] == "2016"
)
```

---

# 11. Union of Two RDDs

The `union()` transformation combines two RDDs.

```python
union_rdd = matches_2017.union(matches_2016)

print(union_rdd.collect())
```

This produces an RDD containing records from both RDDs.

---

# 12. Finding the Player With the Most Man-of-the-Match Awards

The IPL example also demonstrates key-value operations.

Suppose the man-of-the-match value is extracted from the required column:

```python
man_of_match = matches_rdd.map(
    lambda line: line.split(",")[13]
)
```

Convert each player into a key-value pair:

```python
man_of_match_count = man_of_match.map(
    lambda player: (player, 1)
)
```

Aggregate:

```python
man_of_match_count = man_of_match_count.reduceByKey(
    lambda x, y: x + y
)
```

Sort the results:

```python
result = man_of_match_count.sortBy(
    lambda x: x[1],
    ascending=False
)

print(result.take(10))
```

The source example reports **AB de Villiers** with the highest number of awards in that dataset, with **15**.

---

# 13. Pokémon RDD Use Case

The source material also uses a Pokémon CSV dataset.

The workflow is:

1. Load the Pokémon CSV.
2. Remove the header.
3. Check partitions.
4. Find Water-type Pokémon.
5. Find Fire-type Pokémon.
6. Count each type.
7. Find the maximum defense.
8. Find Pokémon with maximum defense.
9. Find Pokémon with minimum defense.

---

## Loading the Pokémon Data

```python
pokemon_rdd = sc.textFile("Pokemon.csv")

print(pokemon_rdd.collect())
```

The referenced dataset contains **721 rows**.

---

# 14. Removing the Header

The first row contains column names.

A common approach is:

```python
header = pokemon_rdd.first()

no_header = pokemon_rdd.filter(
    lambda line: line != header
)
```

Now the header is excluded from processing.

---

# 15. Checking Number of Partitions

```python
print(no_header.getNumPartitions())
```

The source example reports **2 partitions** for its execution environment.

The exact number of partitions can vary depending on how the RDD is created and the Spark environment.

---

# 16. Finding Water-Type Pokémon

```python
water_rdd = no_header.filter(
    lambda line: line.split(",")[2] == "Water"
)
```

Display the results:

```python
print(water_rdd.collect())
```

Count them:

```python
print(water_rdd.count())
```

The source example reports:

```text
112 Water-type Pokémon
```

---

# 17. Finding Fire-Type Pokémon

```python
fire_rdd = no_header.filter(
    lambda line: line.split(",")[2] == "Fire"
)
```

Count:

```python
print(fire_rdd.count())
```

The source example reports:

```text
52 Fire-type Pokémon
```

---

# 18. Finding the Highest Defense

The source dataset stores defense in the required defense column.

Extract the defense values:

```python
defense_list = no_header.map(
    lambda line: float(line.split(",")[6])
)
```

Find the maximum:

```python
maximum_defense = defense_list.max()

print(maximum_defense)
```

The source example reports:

```text
230
```

as the highest defense value.

---

# 19. Finding Pokémon With Maximum Defense

Filter records where defense is 230:

```python
maximum_defense_pokemon = no_header.filter(
    lambda line: float(line.split(",")[6]) == 230
)
```

Display the names:

```python
for row in maximum_defense_pokemon.collect():
    print(row.split(",")[1])
```

The source example reports:

- Steelix
- Steelix Mega
- Shuckle
- Aggron
- Aggron Mega

as Pokémon with the maximum defense value of 230.

---

# 20. Finding Minimum Defense

First find the minimum defense value:

```python
minimum_defense = defense_list.min()

print(minimum_defense)
```

The source example reports:

```text
5
```

Then filter the records:

```python
minimum_defense_pokemon = no_header.filter(
    lambda line: float(line.split(",")[6]) == 5
)

for row in minimum_defense_pokemon.collect():
    print(row.split(",")[1])
```

The source example reports:

- Chansey
- Happiny

as the Pokémon with the minimum defense value of 5.

---

# 21. RDD Cheat Sheet

| Operation | Type | Purpose |
|---|---|---|
| `parallelize()` | Creation | Create RDD from local collection |
| `textFile()` | Creation | Read text data |
| `map()` | Transformation | Transform each element |
| `filter()` | Transformation | Keep matching elements |
| `flatMap()` | Transformation | Transform and flatten |
| `mapPartitions()` | Transformation | Process partition-wise |
| `reduceByKey()` | Transformation | Aggregate values by key |
| `groupByKey()` | Transformation | Group values by key |
| `sortByKey()` | Transformation | Sort key-value RDD |
| `union()` | Transformation | Combine RDDs |
| `collect()` | Action | Return all elements |
| `count()` | Action | Count elements |
| `take()` | Action | Return first N elements |
| `first()` | Action | Return first element |
| `max()` | Action | Find maximum |
| `min()` | Action | Find minimum |
| `getNumPartitions()` | Information | Check partition count |
| `persist()` | Persistence | Keep computed data for reuse |

---

# 22. Key Takeaways

### RDD

An **RDD is Spark's fundamental distributed collection abstraction** that provides partitioning, immutability, lazy evaluation, and fault tolerance.

### Transformations

Transformations create new RDDs and are evaluated lazily.

```text
RDD → Transformation → New RDD
```

### Actions

Actions trigger execution and produce a result.

```text
RDD → Transformation → Action → Result
```

### Narrow vs Wide

```text
Narrow Transformation
       ↓
Limited dependency between partitions
       ↓
map / filter / flatMap


Wide Transformation
       ↓
Data may need to be shuffled
       ↓
reduceByKey / groupByKey / sortByKey
```

### Fault Tolerance

RDD lineage allows Spark to recompute lost partitions rather than requiring the entire dataset to be processed again.

### In-Memory Processing

Keeping reusable data in memory can make iterative processing much faster than repeatedly reading intermediate data from disk.

---

# Conclusion

RDDs provide the foundation for understanding how Spark performs distributed data processing.

The most important concepts to remember are:

1. **RDD = Resilient Distributed Dataset**
2. RDDs are **distributed and partitioned**.
3. RDDs are **immutable**.
4. Transformations are **lazy**.
5. Actions **trigger execution**.
6. Narrow transformations generally avoid a full shuffle.
7. Wide transformations can require **data shuffling**.
8. RDD lineage provides **fault tolerance**.
9. RDDs can be **persisted/reused**.
10. RDDs support **parallel processing** across partitions.

These concepts form a strong foundation for moving from basic Spark programming to more advanced **PySpark DataFrame, Spark SQL, optimization, and distributed data-engineering workflows**.
