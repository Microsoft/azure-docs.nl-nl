---
title: MapReduce met Apache Hadoop op HDInsight
description: Meer informatie over het uitvoeren van Apache MapReduce-taken op Apache Hadoop in HDInsight-clusters.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 33d96262ca2e2ff3003fbf2b40ce9ceb496337ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944258"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>MapReduce gebruiken in Apache Hadoop in HDInsight

Meer informatie over het uitvoeren van MapReduce-taken op HDInsight-clusters.

## <a name="example-data"></a>Voorbeeldgegevens

HDInsight biedt diverse voor beelden van gegevens sets, die worden opgeslagen in de `/example/data` `/HdiSamples` map en. Deze directory's bevinden zich in de standaard opslag voor uw cluster. In dit document gebruiken we het `/example/data/gutenberg/davinci.txt` bestand. Dit bestand bevat de notebooks van Leonardo da Vinci.

## <a name="example-mapreduce"></a>Voor beeld MapReduce

Het HDInsight-cluster bevat een voor beeld van een toepassing met het aantal woorden MapReduce. Dit voor beeld bevindt zich op `/example/jars/hadoop-mapreduce-examples.jar` de standaard opslag voor uw cluster.

De volgende Java-code is de bron van de MapReduce-toepassing die zich in het bestand bevindt `hadoop-mapreduce-examples.jar` :

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

Zie [Java MapReduce-toepassingen voor HDInsight ontwikkelen](apache-hadoop-develop-deploy-java-mapreduce-linux.md)voor instructies voor het schrijven van uw eigen MapReduce-toepassingen.

## <a name="run-the-mapreduce"></a>De MapReduce uitvoeren

HDInsight kan HiveQL-taken uitvoeren met behulp van verschillende methoden. Gebruik de volgende tabel om te bepalen welke methode het meest geschikt is voor u en volg de koppeling voor een overzicht.

| **Gebruik dit**... | **... om dit te doen** |  ... van dit **besturings systeem** van de client |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |De Hadoop-opdracht via **SSH** gebruiken |Linux, UNIX, Mac OS X of Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |De taak extern verzenden met behulp van **rest** |Linux, UNIX, Mac OS X of Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |De taak extern verzenden met behulp van **Windows Power shell**  |Windows |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende documenten voor meer informatie over het werken met gegevens in HDInsight:

* [Java MapReduce-Program ma's ontwikkelen voor HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Apache Hive gebruiken met HDInsight](./hdinsight-use-hive.md)
