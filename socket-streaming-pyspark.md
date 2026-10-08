# Socket Streaming in PySpark

Socket Streaming is one of the simplest ways to understand **real-time data processing in PySpark**.

Instead of reading a complete file like a CSV, Spark continuously receives data from a source and processes it as the data arrives.

---

## 1. What is Streaming?

In normal batch processing, we already have all the data.

```text
CSV File
   ↓
Read entire file
   ↓
Process data
   ↓
Output
```

But in **streaming**, data keeps arriving continuously.

For example:

- Website clicks
- Sensor data
- Bank transactions
- Network logs
- Application logs
- Telecom data

The data may look like:

```text
Arun, Bangalore, 15.5, 420
Rahul, Mumbai, 20.2, 350
Priya, Delhi, 10.8, 500
...
```

We don't want to wait until all the data is available.

Instead:

```text
New data arrives
      ↓
Process it
      ↓
Generate output
      ↓
More data arrives
      ↓
Process it again
```

This is called **stream processing**.

---

# 2. What is Socket Streaming?

A **socket** allows two programs to communicate with each other over a network.

In our example, we have two programs:

```text
Socket Server
     ↓
sends data
     ↓
Socket Client / Spark
     ↓
processes data
```

The server continuously sends data.

Spark continuously receives and processes that data.

So we can use a socket as a simple **real-time data source** for learning Spark Streaming.

---

# 3. Socket Streaming Data Flow

The complete flow is:

```text
Live Data Source
       |
       ↓
   readStream
       |
       ↓
 Streaming DataFrame
       |
       ↓
 Transformation
(filter / select / groupBy / window)
       |
       ↓
   writeStream
       |
       ↓
    Output
       |
       ├── Console
       ├── File
       ├── Kafka
       └── Database
```

### In simple words:

```text
Data comes in
     ↓
Spark reads it
     ↓
Spark creates a Streaming DataFrame
     ↓
We transform the data
     ↓
Spark writes the result somewhere
```

---

# 4. Unbounded Table

One important concept in Structured Streaming is an **unbounded table**.

Imagine data is continuously arriving:

```text
Time →

Data:
[Row 1]
[Row 2]
[Row 3]
[Row 4]
[Row 5]
...
```

There is no fixed ending point.

So Spark treats the streaming data like an:

> **Unbounded Table**

It can keep receiving new rows.

For example:

```text
Initial data:

[ Arun  15GB ]
[ Rahul 20GB ]
[ Priya 10GB ]
```

Then new data arrives:

```text
[ Kiran 25GB ]
```

The streaming table conceptually becomes:

```text
[ Arun   15GB ]
[ Rahul  20GB ]
[ Priya  10GB ]
[ Kiran  25GB ]
```

Then another record arrives:

```text
[ Manoj  18GB ]
```

Spark continues processing the incoming data.

The table is therefore **unbounded** because we don't know when the incoming data will stop.

---

# 5. What is a Streaming DataFrame?

A normal DataFrame contains data that already exists.

For example:

```python
df = spark.read.csv("telecom.csv")
```

Here, Spark reads an existing CSV file.

But for streaming, we use:

```python
df = spark.readStream
```

This tells Spark:

> "Don't just read the data once. Keep watching for incoming data."

The resulting DataFrame is called a:

**Streaming DataFrame**

Example:

```python
stream_df = (
    spark.readStream
    .format("socket")
    .option("host", "localhost")
    .option("port", 9999)
    .load()
)
```

Here Spark connects to:

```text
localhost:9999
```

and waits for incoming data.

---

# 6. `readStream`

`readStream` is used to **read continuously arriving data**.

Example:

```python
spark.readStream
```

Think of it as:

```text
read()
     ↓
Read data once

readStream()
     ↓
Keep reading incoming data
```

For socket streaming:

```python
stream_df = (
    spark.readStream
    .format("socket")
    .option("host", "localhost")
    .option("port", 9999)
    .load()
)
```

### Explanation

```python
spark.readStream
```

Starts a streaming read.

```python
.format("socket")
```

Tells Spark that the source is a socket.

```python
.option("host", "localhost")
```

Tells Spark where the socket server is running.

```python
.option("port", 9999)
```

Tells Spark which port to connect to.

```python
.load()
```

Creates the streaming DataFrame.

---

# 7. Socket Server

Before Spark can receive data, we need a program that sends data.

A simple Python socket server can look like this:

```python
import socket
import time

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

server.bind(("localhost", 9999))
server.listen(1)

print("Waiting for connection...")

conn, addr = server.accept()

print("Connected:", addr)

while True:
    data = "Arun,Bangalore,15.5,420\n"

    conn.send(data.encode())

    time.sleep(2)
```

The server continuously sends data.

For example:

```text
Arun,Bangalore,15.5,420
Arun,Bangalore,15.5,420
Arun,Bangalore,15.5,420
...
```

Spark receives these records.

---

# 8. Spark Socket Client

Our Spark application connects to the server:

```python
stream_df = (
    spark.readStream
    .format("socket")
    .option("host", "localhost")
    .option("port", 9999)
    .load()
)
```

The connection looks like:

```text
┌──────────────────┐
│  Python Server   │
│                  │
│ localhost:9999   │
└────────┬─────────┘
         │
         │ Live data
         ↓
┌──────────────────┐
│      Spark       │
│   readStream     │
└──────────────────┘
```

---

# 9. Data Transformation

After Spark receives the data, we can perform transformations.

For example:

```text
Streaming DataFrame
        ↓
     filter()
        ↓
     select()
        ↓
     groupBy()
        ↓
      window()
```

Some common transformations are:

## `filter()`

Used to select specific records.

```python
df.filter(df.value == "Arun")
```

---

## `select()`

Used to select columns.

```python
df.select("name", "city")
```

---

## `groupBy()`

Used to group data.

```python
df.groupBy("city").count()
```

---

## `window()`

Used to analyze data over a time period.

For example:

```text
10:00 - 10:05
10:05 - 10:10
10:10 - 10:15
```

This is useful in real-time applications.

For example:

> How many transactions happened in the last 5 minutes?

---

# 10. `writeStream`

After processing the data, we need to send the result somewhere.

That's where `writeStream` is used.

Example:

```python
query = (
    stream_df
    .writeStream
    .format("console")
    .outputMode("append")
    .start()
)
```

Think of it like:

```text
readStream
    ↓
Process
    ↓
writeStream
```

`writeStream` continuously writes the streaming results.

---

# 11. Output

The output can go to different destinations.

```text
                 writeStream
                     |
       ┌─────────────┼─────────────┐
       ↓             ↓             ↓
    Console        File          Kafka
                                    |
                                    ↓
                                Database
```

### Console

Useful for learning and debugging.

```python
.format("console")
```

### File

The output can be written to files.

### Kafka

Useful for production streaming architectures.

### Database

Processed streaming data can also be stored in a database.

---

# 12. `outputMode`

`outputMode` tells Spark **what data should be written to the output**.

There are three important modes:

```text
append
complete
update
```

## Append

```python
.outputMode("append")
```

Only newly added rows are written.

Example:

```text
First batch:
Arun

Output:
Arun

Second batch:
Rahul

Output:
Rahul
```

This is commonly used when records are simply being added.

---

## Complete

The entire result table is written every time.

For example:

```text
Bangalore → 10
Mumbai    → 15
Delhi     → 8
```

The complete result is output again when the result changes.

---

## Update

Only rows whose values have changed are written.

This is useful for aggregations where results are continuously updated.

---

# 13. Complete Example

Here is a simple socket streaming program:

```python
from pyspark.sql import SparkSession

spark = (
    SparkSession.builder
    .appName("SocketStreaming")
    .getOrCreate()
)

stream_df = (
    spark.readStream
    .format("socket")
    .option("host", "localhost")
    .option("port", 9999)
    .load()
)

query = (
    stream_df
    .writeStream
    .format("console")
    .outputMode("append")
    .start()
)

query.awaitTermination()
```

The flow is:

```text
Socket Server
      ↓
localhost:9999
      ↓
readStream
      ↓
Streaming DataFrame
      ↓
writeStream
      ↓
Console
```

---

# 14. Why Do We Need `awaitTermination()`?

When we call:

```python
.start()
```

Spark starts the streaming query.

But Python could finish executing the program immediately.

So we use:

```python
query.awaitTermination()
```

This tells Spark:

> "Keep this streaming application running."

Without it, our streaming application may terminate.

---

# 15. Socket Streaming vs Batch Processing

| Batch Processing | Socket Streaming |
|---|---|
| Data already exists | Data continuously arrives |
| Uses `read()` | Uses `readStream()` |
| Processes fixed data | Processes incoming data |
| Has a defined dataset | Conceptually unbounded |
| Uses `write()` | Uses `writeStream()` |
| Example: CSV processing | Example: live socket data |

Simple way to remember:

```text
Batch:

Read → Process → Write → Stop


Streaming:

Read → Process → Write
  ↑                 |
  └─────────────────┘
       continues
```

---

# 16. Socket Streaming Architecture

Our complete architecture looks like this:

```text
                 REAL-TIME DATA
                       │
                       ↓
              ┌─────────────────┐
              │  Socket Server  │
              │   Port: 9999    │
              └────────┬────────┘
                       │
                       │ incoming data
                       ↓
              ┌─────────────────┐
              │    readStream   │
              └────────┬────────┘
                       │
                       ↓
              ┌─────────────────┐
              │    Streaming    │
              │    DataFrame    │
              └────────┬────────┘
                       │
                       ↓
              ┌─────────────────┐
              │ Transformation  │
              │                 │
              │ filter()        │
              │ select()        │
              │ groupBy()       │
              │ window()        │
              └────────┬────────┘
                       │
                       ↓
              ┌─────────────────┐
              │   writeStream   │
              └────────┬────────┘
                       │
             ┌─────────┼─────────┐
             ↓         ↓         ↓
          Console     File      Kafka
                                  │
                                  ↓
                              Database
```

---

# 17. Real-World Example

Imagine a telecom company receiving customer data in real time.

```text
Customer
   ↓
Network
   ↓
Streaming data
   ↓
Socket / Kafka
   ↓
Spark Streaming
```

Suppose data arrives like:

```text
107,Arun,Kolkata,15.5,420,449,4G
108,Rahul,Mumbai,20.2,350,599,5G
109,Priya,Delhi,10.8,500,399,4G
```

Spark can process this data continuously.

For example:

```text
Filter 5G users
       ↓
Calculate average data usage
       ↓
Group by city
       ↓
Write result
```

This can help with real-time monitoring and analytics.

---

# 18. Important Terms to Remember

### Socket

A communication channel between two programs.

### Streaming

Continuously processing data as it arrives.

### `readStream`

Reads continuously arriving data.

### Streaming DataFrame

A DataFrame representing continuously arriving data.

### Unbounded Table

A conceptual table that keeps receiving new rows.

### Transformation

Operations performed on streaming data.

Examples:

```text
filter
select
groupBy
window
```

### `writeStream`

Continuously writes processed results.

### `outputMode`

Controls what results Spark sends to the output.

### `awaitTermination()`

Keeps the streaming application running.

---

# 19. The Main Concept

The entire concept can be remembered with just this:

```text
SOURCE
  ↓
readStream
  ↓
STREAMING DATAFRAME
  ↓
TRANSFORMATION
  ↓
writeStream
  ↓
OUTPUT
```

Or in one sentence:

> **Socket Streaming allows Spark to continuously receive data from a socket, process it using Streaming DataFrames and transformations, and continuously write the results to an output such as the console, file, Kafka, or database.**

---

# 20. Final Takeaway

Socket Streaming is mainly useful for **learning how Spark Structured Streaming works**.

The most important things to understand are:

```text
readStream  → Read live data

Streaming DataFrame → Represents incoming data

Transformation → Process the data

writeStream → Send results somewhere

outputMode → Decide what results to output

awaitTermination() → Keep streaming alive
```

Once this flow is clear, technologies such as **Kafka + Spark Streaming** become easier to understand because the basic idea remains the same:

```text
Data Source
    ↓
readStream
    ↓
Streaming DataFrame
    ↓
Transform
    ↓
writeStream
    ↓
Destination
```

> **Think of Socket Streaming as a simple classroom version of a real-time data pipeline.**
