---
title: Cassandra-API tabel gegevens lezen met behulp van Spark
titleSufix: Azure Cosmos DB
description: In dit artikel wordt beschreven hoe u gegevens uit Cassandra-API tabellen in Azure Cosmos DB kunt lezen.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 01a9582062d8eb0d039473a03901fc83fe179020
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "60893387"
---
# <a name="read-data-from-azure-cosmos-db-cassandra-api-tables-using-spark"></a>Gegevens uit Azure Cosmos DB Cassandra-API tabellen lezen met Spark

 In dit artikel wordt beschreven hoe u gegevens kunt lezen die zijn opgeslagen in Azure Cosmos DB Cassandra-API vanuit Spark.

## <a name="cassandra-api-configuration"></a>Cassandra-API configuratie
```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## <a name="dataframe-api"></a>Data frame-API

### <a name="read-table-using-sessionreadformat-command"></a>Tabel lezen met behulp van Session. Read. Format, opdracht

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>Tabel lezen met behulp van Spark. Read. cassandraFormat 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>Specifieke kolommen in de tabel lezen

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### <a name="apply-filters"></a>Filters toepassen

Het huidige predicaat pushdown wordt niet ondersteund. de onderstaande voor beelden zijn gebaseerd op client filters. 

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")
  .filter("book_pub_year > 1891")
//.filter("book_name IN ('A sign of four','A study in scarlet')")
//.filter("book_name='A sign of four' OR book_name='A study in scarlet'")
//.filter("book_author='Arthur Conan Doyle' AND book_pub_year=1890")
//.filter("book_pub_year=1903")  

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

## <a name="rdd-api"></a>RDD-API

### <a name="read-table"></a>Tabel lezen
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>Specifieke kolommen in de tabel lezen

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>SQL-weer gaven 

### <a name="create-a-temporary-view-from-a-dataframe"></a>Een tijdelijke weer gave maken op basis van een data frame

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>Query's uitvoeren op de weer gave

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>Volgende stappen

Hieronder vindt u meer artikelen over het werken met Azure Cosmos DB Cassandra-API van Spark:
 
 * [Upsert bewerkingen](cassandra-spark-upsert-ops.md)
 * [Bewerkingen verwijderen](cassandra-spark-delete-ops.md)
 * [Aggregatiebewerkingen](cassandra-spark-aggregation-ops.md)
 * [Tabel Kopieer bewerkingen](cassandra-spark-table-copy-ops.md)

