---
title: Learning Apache Wayang(1)
date: 2023-09-24
tags:
---

Project Link: [GitHub - apache/incubator-wayang: Apache Wayang(incubating) is the first cross-platform data processing system.](https://github.com/apache/incubator-wayang)

# Idea
Wayang integrate multiple execution engines(e.g. Spark, Flink, simple java stream) to perform computation task. It provides uses with its customized API and it will choose the engine that best fits the application. The key value of Wayang is that it hides the complexity of learning APIs of different big data systems. Instead, users only needs to utilize Wayang API. 

# My Thoughts
IMO, Wayang is like the planner of big data systems, which translates Wayang jobs to Spark/Flink/... jobs. Some questions that may be interesting:
1. SQL may be the easier way to run Spark/Flink jobs. Why should I learn Wayang instead of directly using Spark SQL and Flink SQL?
2. How does Wayang make the decision of choosing the execution engine?
3. How does Wayang manage the jobs? Does it only take care of batch jobs? How about long running streaming jobs?
4. How does Wayang handle failover?
5. Wayang may act as a benchmark framework for different engines.

# Build Locally
```
mvn clean install -DskipTests
```

# Step-by-step Learning with Java Executor
Example code: org.apache.wayang.tests.WordCountIT
1. Set context with chose plugin --> Java Plugin
2. Define operators and connect them to build the job graph
	1. source (read file) -> flatMap (word->1) -> map (lower()) -> reduceBy (sum()) -> sink (write to a Java list)
3. Execute

Some details:
1. Wayang's type system
2. Wayang's API