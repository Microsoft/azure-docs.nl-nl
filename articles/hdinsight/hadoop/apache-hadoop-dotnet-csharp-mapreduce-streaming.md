---
title: C# gebruiken met MapReduce op Hadoop in HDInsight-Azure
description: Meer informatie over het gebruik van C# voor het maken van MapReduce-oplossingen met Apache Hadoop in azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, seoapr2020, devx-track-csharp
ms.date: 04/28/2020
ms.openlocfilehash: 8093db9c53d9c34014d1b315d53539b3f2cffb30
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866706"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>C# gebruiken met MapReduce streaming op Apache Hadoop in HDInsight

Meer informatie over het gebruik van C# voor het maken van een MapReduce-oplossing op HDInsight.

Met Apache Hadoop streaming kunt u MapReduce-taken uitvoeren met behulp van een script of een uitvoerbaar bestand. Hier wordt .NET gebruikt voor het implementeren van de Mapper en de reminderr voor een oplossing voor het aantal woorden.

## <a name="net-on-hdinsight"></a>.NET in HDInsight

HDInsight-clusters gebruiken [mono https://mono-project.com) (](https://mono-project.com) om .NET-toepassingen uit te voeren. Mono versie 4.2.1 is opgenomen in HDInsight-versie 3,6. Zie [Apache Hadoop-onderdelen die beschikbaar zijn met hdinsight-versies](../hdinsight-component-versioning.md)voor meer informatie over de versie van mono die deel uitmaakt van hdinsight.

Zie [mono-compatibiliteit](https://www.mono-project.com/docs/about-mono/compatibility/)voor meer informatie over de compatibiliteit van mono met .NET Framework versies.

## <a name="how-hadoop-streaming-works"></a>Hoe Hadoop streaming werkt

Het basis proces dat wordt gebruikt voor streaming in dit document, is als volgt:

1. Hadoop geeft gegevens door aan de Mapper (*mapper.exe* in dit voor beeld) op stdin.
2. De toewijzing verwerkt de gegevens en verzendt door tabs gescheiden sleutel/waarde-paren naar STDOUT.
3. De uitvoer wordt door Hadoop gelezen en vervolgens door gegeven aan de versmaller (*reducer.exe* in dit voor beeld) op stdin.
4. De reducer leest de door tabs gescheiden sleutel-waardeparen, verwerkt de gegevens en levert vervolgens het resultaat op als tabgescheiden gescheiden sleutel/waarde-paren op STDOUT.
5. De uitvoer wordt door Hadoop gelezen en naar de uitvoermap geschreven.

Zie [Hadoop streaming](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html)voor meer informatie over streaming.

## <a name="prerequisites"></a>Vereisten

* Visual Studio.

* Een vertrouwdheid met het schrijven en bouwen van C#-code die gericht is op .NET Framework 4,5.

* Een manier om exe-bestanden te uploaden naar het cluster. De stappen in dit document gebruiken de Data Lake-Hulpprogram Ma's voor Visual Studio voor het uploaden van de bestanden naar de primaire opslag voor het cluster.

* Als u Power shell gebruikt, hebt u de [AZ-module](/powershell/azure/)nodig.

* Een Apache Hadoop cluster in HDInsight. Zie aan de [slag met HDInsight op Linux](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* Het URI-schema voor de primaire opslag voor uw clusters. Dit schema is `wasb://` voor Azure Storage, `abfs://` voor Azure data Lake Storage Gen2 of `adl://` voor Azure data Lake Storage gen1. Als beveiligde overdracht is ingeschakeld voor Azure Storage of Data Lake Storage Gen2, is de URI `wasbs://` `abfss://` respectievelijk.

## <a name="create-the-mapper"></a>De toewijzing maken

Maak in Visual Studio een nieuwe .NET Framework-console toepassing met de naam *Mapper*. Gebruik de volgende code voor de toepassing:

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

Nadat u de toepassing hebt gemaakt, bouwt u deze voor het maken van het */bin/Debug/mapper.exe* -bestand in de projectmap.

## <a name="create-the-reducer"></a>De verminderr maken

Maak in Visual Studio een nieuwe .NET Framework-console toepassing met de naam *reducer*. Gebruik de volgende code voor de toepassing:

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

Nadat u de toepassing hebt gemaakt, bouwt u deze voor het maken van het */bin/Debug/reducer.exe* -bestand in de projectmap.

## <a name="upload-to-storage"></a>Uploaden naar opslag

Vervolgens moet u de toewijzing van de *Mapper* en het *verkorten* van toepassingen uploaden naar HDInsight-opslag.

1. Selecteer in Visual Studio Server Explorer **weer geven**  >  .

1. Klik met de rechter muisknop op **Azure**, selecteer **verbinding maken met Microsoft Azure abonnement...** en voltooi het aanmeldings proces.

1. Vouw het HDInsight-cluster uit dat u wilt gebruiken om deze toepassing te implementeren. Een vermelding met de tekst **(standaard opslag account)** wordt weer gegeven.

   :::image type="content" source="./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png" alt-text="Opslag account, HDInsight-cluster, Server Explorer, Visual Studio" border="true":::

   * Als de vermelding **(standaard opslag account)** kan worden uitgevouwen, gebruikt u een **Azure Storage-account** als standaard opslag voor het cluster. Als u de bestanden op de standaard opslag voor het cluster wilt weer geven, vouwt u de vermelding uit en dubbelklikt u vervolgens op **(standaard container)**.

   * Als de vermelding **(standaard opslag account)** niet kan worden uitgevouwen, gebruikt u **Azure data Lake Storage** als de standaard opslag voor het cluster. Als u de bestanden op de standaard opslag voor het cluster wilt weer geven, dubbelklikt u op de vermelding **(standaard opslag account)** .

1. Gebruik een van de volgende methoden om de exe-bestanden te uploaden:

    * Als u een Azure Storage- **account** gebruikt, selecteert u het pictogram **BLOB uploaden** .

        :::image type="content" source="./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png" alt-text="Pictogram voor het uploaden van HDInsight voor Mapper, Visual Studio" border="true":::

        Selecteer in het dialoog venster **nieuw bestand uploaden** onder **Bestands naam** de optie **Bladeren**. Ga in het dialoog venster **BLOB uploaden** naar de map *bin\debug* voor het *toewijzings* project en kies vervolgens het *mapper.exe* bestand. Selecteer ten slotte **openen** en klik vervolgens op **OK** om het uploaden te volt ooien.

    * Voor **Azure data Lake Storage** klikt u met de rechter muisknop op een leeg gebied in de vermelding bestand en selecteert u vervolgens **uploaden**. Selecteer ten slotte het *mapper.exe* bestand en selecteer vervolgens **openen**.

    Wanneer het uploaden van *mapper.exe* is voltooid, herhaalt u het upload proces voor het *reducer.exe* bestand.

## <a name="run-a-job-using-an-ssh-session"></a>Een taak uitvoeren: een SSH-sessie gebruiken

In de volgende procedure wordt beschreven hoe u een MapReduce-taak kunt uitvoeren met behulp van een SSH-sessie:

1. Gebruik de [ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster. Voer vervolgens deze opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Gebruik een van de volgende opdrachten om de MapReduce-taak te starten:

   * Als de standaard opslag **Azure Storage** is:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Als de standaard opslag **Data Lake Storage gen1** is:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Als de standaard opslag **Data Lake Storage Gen2** is:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   In de volgende lijst wordt beschreven wat elke para meter en optie vertegenwoordigt:

   |Parameter | Beschrijving |
   |---|---|
   |Hadoop-streaming. jar|Hiermee geeft u het jar-bestand op dat de functionaliteit van streaming MapReduce bevat.|
   |-bestanden|Hiermee geeft u de *mapper.exe* en *reducer.exe* bestanden voor deze taak. De `wasbs:///` `adl:///` declaratie, of `abfs:///` protocol voor elk bestand is het pad naar de hoofdmap van de standaard opslag voor het cluster.|
   |-Mapper|Hiermee geeft u het bestand op dat de Mapper implementeert.|
   |-reduceerr|Hiermee geeft u het bestand op waarmee de verminderr wordt geïmplementeerd.|
   |-invoer|Hiermee geeft u de invoer gegevens op.|
   |-uitvoer|Hiermee geeft u de uitvoermap op.|

1. Zodra de MapReduce-taak is voltooid, gebruikt u de volgende opdracht om de resultaten weer te geven:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   De volgende tekst is een voor beeld van de gegevens die door deze opdracht worden geretourneerd:

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>Een taak uitvoeren: Power shell gebruiken

Gebruik het volgende Power shell-script om een MapReduce-taak uit te voeren en de resultaten te downloaden.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Dit script vraagt u om de naam en het wacht woord van het cluster aanmeldings account, samen met de naam van het HDInsight-cluster. Zodra de taak is voltooid, wordt de uitvoer gedownload naar een bestand met de naam *output.txt*. De volgende tekst is een voor beeld van de gegevens in het `output.txt` bestand:

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>Volgende stappen

* [Gebruik MapReduce in Apache Hadoop op HDInsight](hdinsight-use-mapreduce.md).
* [Gebruik een door de gebruiker gedefinieerde C#-functie met Apache Hive en Apache Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md).
* [Java MapReduce-programma's ontwikkelen](apache-hadoop-develop-deploy-java-mapreduce-linux.md)