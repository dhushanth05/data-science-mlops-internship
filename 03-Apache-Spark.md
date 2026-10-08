# Apache Spark: Distributed Data Processing

## Introduction

As data becomes larger, processing it on a single computer can become slow and difficult.

Apache Spark is a distributed data-processing engine that allows large amounts of data to be processed across multiple machines in a cluster.

The basic idea is:

```text
Large Data
    ↓
Multiple Machines
    ↓
Parallel Processing
    ↓
Final Result
```

Spark became popular because it provides fast and flexible distributed processing and supports different types of data workloads.

---

# 1. What is Apache Spark?

**Apache Spark** is an open-source, distributed data-processing engine used to process large datasets across multiple machines.

Instead of processing all the data on one computer, Spark divides the work across a cluster.

For example:

```text
                    Large Dataset
                         |
              ---------------------
              |         |          |
           Worker 1  Worker 2   Worker 3
              |         |          |
           Process    Process    Process
              \         |          /
               \        |         /
                  Final Result
```

Spark can be used for:

- Batch processing
- SQL and structured-data processing
- Streaming workloads
- Machine learning
- Data analysis

---

# 2. Why Do We Need Spark?

Suppose a company has several terabytes of data.

Processing all of this data on one computer can take a long time.

Instead, Spark can distribute the data and computation across multiple machines.

This provides:

- Parallel processing
- Scalability
- Faster processing for many workloads
- Support for large datasets
- Flexible data-processing APIs

The main idea is:

```text
One Machine
     ↓
Limited Resources

Cluster
     ↓
Multiple Machines
     ↓
Distributed Processing
```

---

# 3. Spark and Hadoop

Spark and Hadoop are related, but they are not the same thing.

Hadoop is an ecosystem that includes components such as:

```text
Hadoop
  |
  |--- HDFS       → Distributed Storage
  |
  |--- YARN       → Resource Management
  |
  |--- MapReduce  → Processing
```

Spark is primarily a **distributed processing engine**.

Spark can work with storage systems such as HDFS.

For example:

```text
             Apache Spark
                  |
              Processing
                  |
                 HDFS
                  |
               Storage
```

Spark can also run using YARN as a cluster manager.

So Hadoop and Spark can work together rather than being completely separate technologies.

---

# 4. Spark vs Hadoop MapReduce

One of the important differences is how intermediate data is handled.

Traditional Hadoop MapReduce commonly relies heavily on disk for intermediate results.

For example:

```text
Map
 ↓
Write to Disk
 ↓
Read from Disk
 ↓
Reduce
```

Spark can keep intermediate data in memory when appropriate.

```text
Operation 1
    ↓
Memory
    ↓
Operation 2
    ↓
Memory
    ↓
Operation 3
```

This can reduce repeated disk I/O and improve performance for suitable workloads, especially iterative workloads.

However, Spark is not automatically faster for every workload. Actual performance depends on the data, operations, cluster resources, configuration, and workload.

---

# 5. Spark Architecture

A simplified Spark architecture is:

```text
                    Spark Application
                           |
                         Driver
                           |
                    Cluster Manager
                     /           \
                    /             \
             Worker Node       Worker Node
                  |                  |
              Executor           Executor
                  |                  |
                Tasks              Tasks
```

The main components are:

1. **Driver**
2. **Cluster Manager**
3. **Worker Node**
4. **Executor**

---

# 6. Driver

The **Driver** is the main coordinator of a Spark application.

We can think of the Driver as the manager or brain of the application.

The Driver is responsible for tasks such as:

- Creating the Spark application
- Creating the SparkSession
- Understanding the operations requested by the user
- Building the execution plan
- Scheduling work
- Coordinating executors
- Tracking the progress of the application

For example:

```python
df = spark.read.csv("data.csv")

df2 = df.filter(df.age > 25)

df2.show()
```

The Driver coordinates the execution of these operations.

A simple way to remember it is:

```text
Driver = Coordinator of the Spark application
```

---

# 7. Cluster Manager

The **Cluster Manager** is responsible for providing and managing resources for Spark applications.

When the Driver needs resources to run an application, the cluster manager helps allocate those resources.

Common cluster managers include:

- Spark Standalone
- YARN
- Kubernetes

For example:

```text
Driver
  |
  ↓
Cluster Manager
  |
  ↓
Resources for Spark Application
```

If Spark is running in a Hadoop environment, YARN can act as the cluster manager.

---

# 8. Worker Node

A **Worker Node** is a machine in the cluster that provides resources for Spark processing.

For example:

```text
                Driver
              /   |   \
             ↓    ↓    ↓
         Worker Worker Worker
           1      2      3
```

Worker nodes provide resources such as:

- CPU
- Memory

These resources are used to run Spark executors.

So:

```text
Worker Node = Machine that provides resources
```

---

# 9. Executor

An **Executor** is a process that runs on a worker node.

Executors are responsible for:

- Executing tasks
- Processing data
- Storing intermediate data when required
- Returning results or status to the Driver

For example:

```text
Worker Node
     |
     ↓
  Executor
     |
     ↓
   Tasks
```

An important distinction is:

```text
Worker Node → Machine
Executor    → Process running on that machine
```

This distinction is important when understanding Spark architecture.

---

# 10. SparkSession

In modern PySpark applications, **SparkSession** is the main entry point for working with Spark functionality such as DataFrames and Spark SQL.

A SparkSession can be created in Python using:

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("MyApplication") \
    .getOrCreate()
```

After creating the SparkSession, we can use it to read data:

```python
df = spark.read.csv("data.csv")
```

So:

```text
SparkSession = Main entry point for PySpark
```

---

# 11. RDD – Resilient Distributed Dataset

**RDD** stands for **Resilient Distributed Dataset**.

RDD is one of the fundamental abstractions of Apache Spark.

An RDD represents a distributed collection of data that can be processed across a cluster.

The three important ideas in the name are:

- **Resilient** — Spark can recover lost partitions using its fault-tolerance mechanisms.
- **Distributed** — the data is divided across multiple machines.
- **Dataset** — it represents a collection of data.

Conceptually:

```text
RDD
 |
 |---------------------------
 |            |             |
Partition 1 Partition 2  Partition 3
```

RDDs were an important foundation of Spark's original programming model.

Modern Spark applications commonly use higher-level **DataFrames** and **Datasets** because they provide additional optimization and ease of use.

---

# 12. Spark Core

**Spark Core** is the fundamental part of the Spark engine.

It provides the basic functionality required for distributed execution.

Spark Core is responsible for capabilities such as:

- Task scheduling
- Memory management
- Fault recovery
- Basic distributed execution
- RDD support

It forms the foundation on which other Spark components are built.

---

# 13. Spark SQL

**Spark SQL** is used for processing structured data.

It allows users to work with:

- SQL queries
- DataFrames
- Structured data

For example:

```python
df.createOrReplaceTempView("customers")

result = spark.sql("""
    SELECT * FROM customers
""")
```

Spark SQL is useful for people who are familiar with SQL because they can use SQL syntax while benefiting from Spark's distributed execution.

---

# 14. Structured Streaming

**Structured Streaming** is Spark's stream-processing API.

It allows Spark applications to process continuously arriving data.

For example:

```text
Data Sources
     ↓
Streaming Data
     ↓
Spark Structured Streaming
     ↓
Processing
     ↓
Output
```

Streaming can be used for applications such as:

- Monitoring
- Event processing
- Real-time analytics
- Continuous data pipelines

---

# 15. MLlib

**MLlib** is Spark's machine-learning library.

It provides tools for performing machine-learning tasks on large datasets using Spark's distributed processing capabilities.

For example:

```text
Large Dataset
     ↓
Spark
     ↓
MLlib
     ↓
Machine Learning Model
```

---

# 16. Spark Programming Languages

Spark supports multiple programming languages.

Common APIs include:

- Python
- Scala
- Java
- R

This allows developers to use Spark with a language they are familiar with.

For Python developers:

```text
Apache Spark
      ↓
   PySpark
      ↓
    Python
```

PySpark is the Python API for Spark.

---

# 17. PySpark

**PySpark** allows us to use Apache Spark through Python.

It is especially useful for Python developers and data engineers who want to work with large datasets using Spark.

For example:

```python
df = spark.read.csv("customers.csv")

result = df.filter(df.age > 25)

result.show()
```

The Python code is used to describe the processing, while Spark handles the distributed execution.

---

# 18. Spark DataFrames

A **DataFrame** is a distributed collection of data organized into named columns.

For example:

```text
+----+-------+-----+
| id | name  | age |
+----+-------+-----+
|  1 | Alice |  25  |
|  2 | Bob   |  30  |
|  3 | Carol |  28  |
+----+-------+-----+
```

A Spark DataFrame is designed to be processed in a distributed manner.

Conceptually:

```text
Spark DataFrame
       |
       ↓
   Partitions
   /    |    \
  ↓     ↓     ↓
 P1     P2    P3
```

Different partitions can be processed in parallel.

---

# 19. Pandas DataFrame vs Spark DataFrame

A Pandas DataFrame normally operates within the resources of a single Python environment.

A Spark DataFrame is designed for distributed processing.

Conceptually:

```text
Pandas

Computer
   |
DataFrame
```

Whereas:

```text
Spark

             Cluster
          /     |     \
         ↓      ↓      ↓
      Partition Partition Partition
```

This is one reason Spark is useful when working with datasets that are too large for a single machine, provided sufficient cluster resources are available.

---

# 20. Partitions

A **partition** is a logical chunk of distributed data.

Spark divides data into partitions so that multiple tasks can process different portions of the data.

For example:

```text
Data
 |
 |------------------------------
 |              |              |
Partition 1   Partition 2   Partition 3
     ↓             ↓             ↓
   Task 1        Task 2        Task 3
```

More generally:

```text
Partition
    ↓
Task
    ↓
Executor
```

Partitioning is an important part of Spark's parallel-processing model.

---

# 21. Transformations

**Transformations** are operations that describe how we want to modify or derive data.

Common examples include:

```python
filter()
select()
withColumn()
groupBy()
join()
```

For example:

```python
df2 = df.filter(df.age > 25)
```

The transformation describes the operation that should be performed on the data.

Spark generally does not immediately execute the complete computation when a transformation is called.

---

# 22. Actions

**Actions** request a result from Spark and trigger execution of the required computation.

Examples include:

```python
df.show()
df.count()
df.collect()
```

For example:

```python
df2 = df.filter(df.age > 25)

df2.show()
```

Here:

```text
filter() → Transformation
show()   → Action
```

The action causes Spark to execute the required computation.

---

# 23. Lazy Evaluation

Spark uses **lazy evaluation**.

This means Spark waits before executing transformations and builds an execution plan.

For example:

```python
df2 = df.filter(df.age > 25)

df3 = df2.select("name", "age")
```

Spark can build a plan based on these operations.

When we call:

```python
df3.show()
```

the action triggers execution.

The general flow is:

```text
Transformations
      ↓
Execution Plan
      ↓
Action
      ↓
Execution
```

Lazy evaluation allows Spark to analyze the complete sequence of operations and optimize execution.

---

# 24. Spark Execution Hierarchy

Spark organizes work using a hierarchy:

```text
Application
     ↓
    Job
     ↓
   Stage
     ↓
   Task
```

### Application

The complete Spark program is called a Spark application.

### Job

An action can trigger a job.

For example:

```python
df.count()
```

can trigger a job.

### Stage

A job is divided into stages based on dependencies between operations.

### Task

A task is a unit of work executed by an executor, generally for a particular partition.

So the hierarchy is:

```text
Application → Job → Stage → Task
```

---

# 25. Example of Spark Execution

Suppose we have:

```python
df2 = df.filter(df.age > 25)
df3 = df2.select("name", "age")
df3.show()
```

The general flow is:

```text
PySpark Code
     ↓
Driver
     ↓
Execution Plan
     ↓
Job
     ↓
Stages
     ↓
Tasks
     ↓
Executors
     ↓
Partitions
     ↓
Final Result
```

The Driver coordinates the work, while executors perform the tasks.

---

# 26. Real-World Example

Suppose an e-commerce company has billions of transaction records.

The company wants to calculate the total transaction amount for each customer.

The data may be stored in HDFS or another distributed storage system.

PySpark can process the data:

```python
result = df.groupBy("customer_id").sum("amount")
```

Conceptually:

```text
Transaction Data
       ↓
Distributed Storage
       ↓
     PySpark
       ↓
      Driver
       ↓
Cluster Manager
       ↓
   Worker Nodes
       ↓
    Executors
       ↓
      Tasks
       ↓
    Final Result
```

Different partitions of the data can be processed in parallel.

---

# 27. Hadoop and Spark Together

A common architecture can use Hadoop components for storage and resource management while Spark performs the processing.

For example:

```text
                    PySpark
                       |
                     Spark
                       |
                     YARN
                       |
          -------------------------
          |           |           |
       Worker       Worker      Worker
          |           |           |
       Executor    Executor   Executor
                       |
                      HDFS
                       |
              Distributed Storage
```

In this example:

```text
HDFS  → Storage
YARN  → Resource Management
Spark → Processing
PySpark → Python interface to Spark
```

The exact architecture depends on the cluster deployment.

---

# 28. Simple Architecture to Remember

The main Spark architecture can be remembered as:

```text
                 Spark Application
                        |
                      Driver
                        |
                 Cluster Manager
                  /           \
                 /             \
          Worker Node       Worker Node
               |                  |
           Executor           Executor
               |                  |
             Tasks              Tasks
                 \              /
                  \            /
                   Final Result
```

And the processing flow can be remembered as:

```text
Data
 ↓
Partitions
 ↓
Tasks
 ↓
Executors
 ↓
Result
```

---

# 29. Key Takeaways

- Apache Spark is a distributed data-processing engine.
- Spark can process large datasets across multiple machines.
- Spark can work with storage systems such as HDFS.
- Spark can use memory extensively for suitable workloads, reducing repeated disk I/O.
- RDD is one of Spark's fundamental distributed data abstractions.
- Spark Core provides the foundation for distributed execution.
- Spark SQL is used for structured data and SQL queries.
- Structured Streaming is used for continuously arriving data.
- MLlib provides distributed machine-learning capabilities.
- PySpark is the Python API for Spark.
- The Driver coordinates a Spark application.
- The Cluster Manager provides resources.
- Worker Nodes are machines that provide resources.
- Executors are processes running on worker nodes.
- Partitions allow data to be processed in parallel.
- Transformations define data operations.
- Actions trigger execution.
- Spark uses lazy evaluation.
- The execution hierarchy is Application → Job → Stage → Task.

---

# Conclusion

Apache Spark is an important technology for distributed data processing.

The basic idea is to divide large amounts of data into partitions and process those partitions across multiple machines.

The main Spark architecture is:

```text
Driver
   ↓
Cluster Manager
   ↓
Worker Nodes
   ↓
Executors
   ↓
Tasks
```

Spark provides several components for different workloads:

```text
Spark Core        → Core distributed processing
Spark SQL         → Structured data and SQL
Structured Streaming → Streaming data
MLlib             → Machine learning
PySpark           → Python API
```

Spark can also work with Hadoop components.

A common combination is:

```text
HDFS  → Distributed Storage
YARN  → Resource Management
Spark → Distributed Processing
PySpark → Python API for Spark
```

Understanding Spark architecture, RDDs, DataFrames, partitions, transformations, actions, and lazy evaluation provides the foundation for working with PySpark and large-scale data-processing systems.

---

# Learning References

The following videos were used as learning references while studying Apache Spark:

1. Apache Spark learning video:
   https://youtu.be/94w6hPk7nkM?si=NviEDe0NDgoFidO9

2. Apache Spark learning video:
   https://youtu.be/KAuIvccwbPY?si=ev_eiMAy0osNk6mf

3. Apache Spark learning video:
   https://youtu.be/v_uodKAywXA?si=gj0bQJA1dR4hrFgT
