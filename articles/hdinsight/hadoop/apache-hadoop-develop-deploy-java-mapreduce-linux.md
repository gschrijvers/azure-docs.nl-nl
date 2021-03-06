---
title: Java-MapReduce maken voor Apache Hadoop-Azure HDInsight
description: Leer hoe u Apache Maven gebruikt om een op Java gebaseerde MapReduce-toepassing te maken en deze vervolgens uit te voeren met Hadoop op Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/16/2020
ms.openlocfilehash: a37a8bb45c11d5b74f3059a153806e3d083cf452
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76311951"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Java MapReduce-programma's ontwikkelen voor Apache Hadoop in HDInsight

Leer hoe u Apache Maven gebruikt om een op Java gebaseerde MapReduce-toepassing te maken en deze vervolgens uit te voeren met Apache Hadoop op Azure HDInsight.

## <a name="prerequisites"></a>Vereisten

* [Jdk-versie 8 (Java Developer Kit)](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) is op de juiste wijze [geïnstalleerd](https://maven.apache.org/install.html) volgens Apache.  Maven is een project voor het maken van een systeem voor Java-projecten.

## <a name="configure-development-environment"></a>De ontwikkelomgeving configureren

De omgeving die voor dit artikel wordt gebruikt, is een computer met Windows 10. De opdrachten zijn uitgevoerd in een opdracht prompt en de verschillende bestanden zijn bewerkt met Klad blok. Wijzig dienovereenkomstig voor uw omgeving.

Voer vanaf een opdracht prompt de onderstaande opdrachten in om een werk omgeving te maken:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Een Maven-project maken

1. Voer de volgende opdracht in om een Maven-project met de naam **wordcountjava**te maken:

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    Met deze opdracht maakt u een map met de naam die `artifactID` is opgegeven door de para meter (**wordcountjava** in dit voor beeld). Deze map bevat de volgende items:

    * `pom.xml`-Het [object model van het project (POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) met informatie en configuratie details die zijn gebruikt om het project te bouwen.
    * src\main\java\org\apache\hadoop\examples: bevat de code van uw toepassing.
    * src\test\java\org\apache\hadoop\examples: bevat tests voor uw toepassing.

1. Verwijder de gegenereerde voorbeeld code. Verwijder de gegenereerde test-en `AppTest.java`toepassings bestanden `App.java` en voer de volgende opdrachten in:

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Het object model van het project bijwerken

Zie https://maven.apache.org/pom.htmlvoor een volledige referentie van het bestand pom. XML. Open `pom.xml` door de volgende opdracht in te voeren:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Afhankelijkheden toevoegen

Voeg `pom.xml`in de `<dependencies>` sectie de volgende tekst toe:

```xml
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-examples</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-client-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
```

Hiermee definieert u de vereiste bibliotheken ( &lt;vermeld\>in artifactId) met een specifieke versie ( &lt;vermeld\>in versie). Tijdens het compileren worden deze afhankelijkheden gedownload uit de standaard-maven-opslag plaats. U kunt de [maven-opslag plaats zoeken](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) gebruiken om meer weer te geven.

Het `<scope>provided</scope>` geeft maven aan dat deze afhankelijkheden niet moeten worden verpakt met de toepassing, omdat ze tijdens runtime worden geleverd door het HDInsight-cluster.

> [!IMPORTANT]
> De versie die wordt gebruikt, moet overeenkomen met de versie van de Hadoop aanwezig in uw cluster. Zie het document [versie van HDInsight-onderdeel](../hdinsight-component-versioning.md) voor meer informatie over versies.

### <a name="build-configuration"></a>Configuratie maken

Met maven-invoeg toepassingen kunt u de build-fasen van het project aanpassen. Deze sectie wordt gebruikt om invoeg toepassingen, resources en andere configuratie opties voor het maken van een toepassing toe te voegen.

Voeg de volgende code toe aan `pom.xml` het bestand, sla het bestand op en sluit het. Deze tekst moet zich in de `<project>...</project>` Tags in het bestand bevindt, bijvoorbeeld `</dependencies>` tussen `</project>`en.

```xml
<build>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
        </configuration>
        <executions>
        <execution>
            <phase>package</phase>
                <goals>
                <goal>shade</goal>
                </goals>
        </execution>
        </executions>
        </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.6.1</version>
        <configuration>
        <source>1.8</source>
        <target>1.8</target>
        </configuration>
    </plugin>
    </plugins>
</build>
```

In deze sectie worden de invoeg toepassing Apache Maven compiler en Apache Maven Shad geconfigureerd. De compiler-invoeg toepassing wordt gebruikt voor het compileren van de topologie. De arcerings-invoeg toepassing wordt gebruikt om te voor komen dat licentie duplicatie in het JAR-pakket dat is gebouwd door maven. Deze invoeg toepassing wordt gebruikt om te voor komen dat een ' dubbele licentie bestanden ' tijdens runtime op het HDInsight-cluster wordt uitgevoerd. Het gebruik van Maven-Shad-plugin `ApacheLicenseResourceTransformer` met de implementatie verhindert de fout.

De Maven-Shade-invoeg toepassing produceert ook een uber jar dat alle afhankelijkheden bevat die vereist zijn voor de toepassing.

Sla het bestand `pom.xml` op.

## <a name="create-the-mapreduce-application"></a>De MapReduce-toepassing maken

1. Voer de onderstaande opdracht in om een nieuw bestand `WordCount.java`te maken en te openen. Selecteer **Ja** wanneer u wordt gevraagd een nieuw bestand te maken.

    ```cmd
    notepad src\main\java\org\apache\hadoop\examples\WordCount.java
    ```

2. Kopieer en plak de Java-code hieronder in het nieuwe bestand. Sluit het bestand.

    ```java
    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.util.StringTokenizer;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

        public static class TokenizerMapper
            extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
                        ) throws IOException, InterruptedException {
            StringTokenizer itr = new StringTokenizer(value.toString());
            while (itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            context.write(word, one);
            }
        }
    }

    public static class IntSumReducer
            extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                            Context context
                            ) throws IOException, InterruptedException {
            int sum = 0;
            for (IntWritable val : values) {
            sum += val.get();
            }
            result.set(sum);
            context.write(key, result);
        }
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
        if (otherArgs.length != 2) {
            System.err.println("Usage: wordcount <in> <out>");
            System.exit(2);
        }
        Job job = new Job(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
        }
    }
    ```

    U ziet dat de naam `org.apache.hadoop.examples` van het pakket en de `WordCount`naam van de klasse. U gebruikt deze namen wanneer u de MapReduce-taak verzendt.

## <a name="build-and-package-the-application"></a>De toepassing bouwen en inpakken

Gebruik in `wordcountjava` de map de volgende opdracht om een jar-bestand te maken dat de toepassing bevat:

```cmd
mvn clean package
```

Met deze opdracht worden eerdere builds artefacten opgeschoond, worden eventuele afhankelijkheden gedownload die nog niet zijn geïnstalleerd en wordt de toepassing vervolgens gebouwd en verpakt.

Zodra de opdracht is voltooid, bevat `wordcountjava/target` de map een bestand met `wordcountjava-1.0-SNAPSHOT.jar`de naam.

> [!NOTE]
> Het `wordcountjava-1.0-SNAPSHOT.jar` bestand is een uberjar die niet alleen de WordCount-taak bevat, maar ook afhankelijkheden die de taak vereist tijdens runtime.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>De JAR en run Jobs uploaden (SSH)

De volgende stappen worden `scp` gebruikt om het jar naar het primaire hoofd knooppunt van uw Apache HBase in HDInsight-cluster te kopiëren. De `ssh` opdracht wordt vervolgens gebruikt om verbinding te maken met het cluster en het voor beeld rechtstreeks op het hoofd knooppunt uit te voeren.

1. Upload het jar naar het cluster. Vervang `CLUSTERNAME` door de naam van uw HDInsight-cluster en voer de volgende opdracht in:

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Maak verbinding met het cluster. Vervang `CLUSTERNAME` door de naam van uw HDInsight-cluster en voer de volgende opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Gebruik in de SSH-sessie de volgende opdracht om de MapReduce-toepassing uit te voeren:

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    Met deze opdracht start u de WordCount MapReduce-toepassing. Het invoer bestand is `/example/data/gutenberg/davinci.txt`en de uitvoermap `/example/data/wordcountout`. Zowel het invoer bestand als de uitvoer worden opgeslagen in de standaard opslag voor het cluster.

1. Wanneer de taak is voltooid, gebruikt u de volgende opdracht om de resultaten weer te geven:

   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    U ontvangt een lijst met woorden en aantallen met waarden die vergelijkbaar zijn met de volgende tekst:

    ```output
    zeal    1
    zelus   1
    zenith  2
    ```

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u een Java MapReduce-taak kunt ontwikkelen. Raadpleeg de volgende documenten voor andere manieren om met HDInsight te werken.

* [Apache Hive gebruiken met HDInsight](hdinsight-use-hive.md)
* [MapReduce gebruiken met HDInsight](hdinsight-use-mapreduce.md)
* [Java Developer Center](https://azure.microsoft.com/develop/java/)
