---
title: C#, Apache Hive & Apache-Pig op Apache Hadoop-Azure HDInsight
description: Meer informatie over het gebruik van door de gebruiker gedefinieerde C#-functies (UDF) met Apache Hive en Apache Pig-streaming in azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/06/2019
ms.openlocfilehash: 97b23a2b9e3b95a5ea0efcd27d0ec185b1c456f1
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946541"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Door de gebruiker gedefinieerde C#-functies gebruiken met Apache Hive en Apache varken op Apache Hadoop in HDInsight

Meer informatie over het gebruik van door de gebruiker gedefinieerde C#-functies (UDF) met [Apache Hive](https://hive.apache.org) en [Apache varken](https://pig.apache.org) op HDInsight.

> [!IMPORTANT]
> De stappen in dit document werken met HDInsight-clusters op basis van Linux. Linux is het enige besturingssysteem dat wordt gebruikt in HDInsight-versie 3.4 of hoger. Zie [versie beheer van HDInsight-onderdelen](../hdinsight-component-versioning.md)voor meer informatie.

Zowel Hive als Pig kunnen gegevens door geven aan externe toepassingen voor verwerking. Dit proces wordt ook wel _streaming_ genoemd. Wanneer u een .NET-toepassing gebruikt, worden de gegevens door gegeven aan de toepassing op STDIN en de toepassing retourneert de resultaten op STDOUT. Voor het lezen en schrijven van STDIN en STDOUT kunt u `Console.ReadLine()` en `Console.WriteLine()` vanuit een console toepassing gebruiken.

## <a name="prerequisites"></a>Vereisten

* Een vertrouwdheid met het schrijven en bouwen van C#-code die gericht is op .NET Framework 4,5.

    Gebruik de gewenste IDE. We raden [Visual Studio](https://www.visualstudio.com/vs) of [Visual Studio code](https://code.visualstudio.com/)aan. De stappen in dit document gebruiken Visual Studio 2019.

* Een manier om exe-bestanden naar het cluster te uploaden en Pig-en Hive-taken uit te voeren. U kunt het beste [Data Lake-Hulpprogram ma's voor Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md), [Azure POWERSHELL](/powershell/azure)en [Azure cli](/cli/azure/install-azure-cli). In de stappen in dit document worden de Data Lake-Hulpprogram Ma's voor Visual Studio gebruikt om de bestanden te uploaden en de voorbeeld query van de Hive uit te voeren.

    Zie [Wat is Apache Hive en HiveQL in azure HDInsight?](hdinsight-use-hive.md)voor meer informatie over andere manieren om Hive-query's uit te voeren.

* Een Hadoop in HDInsight-cluster. Zie [HDInsight-clusters maken](../hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie over het maken van een cluster.

## <a name="net-on-hdinsight"></a>.NET in HDInsight

*HDInsight-clusters op basis van Linux* gebruiken [mono ( https://mono-project.com)](https://mono-project.com) om .NET-toepassingen uit te voeren. Mono versie 4.2.1 is opgenomen in HDInsight-versie 3,6.

Zie [mono-compatibiliteit](https://www.mono-project.com/docs/about-mono/compatibility/)voor meer informatie over de compatibiliteit van mono met .NET Framework versies.

Zie [hdinsight-onderdeel versies](../hdinsight-component-versioning.md)voor meer informatie over de versie van de .NET Framework en mono, opgenomen in hdinsight-versies.

## <a name="create-the-c-projects"></a>De C- \# projecten maken

In de volgende secties wordt beschreven hoe u een C#-project in Visual Studio maakt voor een Apache Hive UDF en een Apache-Pig UDF.

### <a name="apache-hive-udf"></a>Apache Hive UDF

Een C#-project maken voor een Apache Hive UDF:

1. Start Visual Studio.

2. Selecteer **Een nieuw project maken**.

3. Kies in het venster **een nieuw project maken** de sjabloon **console-app (.NET Framework)** (de C#-versie). Selecteer vervolgens **Volgende**.

4. Voer in het venster **uw nieuwe project configureren** de **project naam** *HiveCSharp* in en navigeer naar of maak een **locatie** om het nieuwe project in op te slaan. Selecteer vervolgens **Maken**.

5. Vervang in de Visual Studio IDE de inhoud van *Program.cs* door de volgende code:

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

6. Selecteer in de menu **balk build**  >  **Build Solution** om het project te bouwen.

7. Sluit de oplossing.

### <a name="apache-pig-udf"></a>Apache-Pig UDF

Een C#-project maken voor een Apache Hive UDF:

1. Open Visual Studio.

2. Selecteer in het **Startvenster** de optie **Een nieuw project maken**.

3. Kies in het venster **een nieuw project maken** de sjabloon **console-app (.NET Framework)** (de C#-versie). Selecteer vervolgens **Volgende**.

4. Voer in het venster **uw nieuwe project configureren** de **project naam** *PigUDF* in en ga naar of maak een **locatie** om het nieuwe project in op te slaan. Selecteer vervolgens **Maken**.

5. Vervang in de Visual Studio IDE de inhoud van *Program.cs* door de volgende code:

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Met deze code worden de regels geparseerd die zijn verzonden van Pig en worden de regels die beginnen met `java.lang.Exception` .

6. **Kies Build**  >  **Build Solution** in de menu balk om het project te bouwen.

7. Sluit de oplossing niet open.

## <a name="upload-to-storage"></a>Uploaden naar opslag

Upload vervolgens de Hive-en Pig-toepassingen naar opslag op een HDInsight-cluster.

1. Ga in Visual Studio naar Server Explorer **weer geven**  >  .

1. Klik vanuit **Server Explorer** met de rechter muisknop op **Azure**, selecteer **verbinding maken met Microsoft Azure abonnement** en voltooi het aanmeldings proces.

1. Vouw het HDInsight-cluster uit dat u wilt gebruiken om deze toepassing te implementeren. Een vermelding met de tekst **(standaard opslag account)** wordt weer gegeven.

    ![Standaard opslag account, HDInsight-cluster, Server Explorer](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * Als dit item kan worden uitgevouwen, gebruikt u een **Azure Storage-account** als standaard opslag voor het cluster. Als u de bestanden op de standaard opslag voor het cluster wilt weer geven, vouwt u de vermelding uit en dubbelklikt u vervolgens op de **(standaard container)**.

    * Als dit item niet kan worden uitgevouwen, gebruikt u **Azure data Lake Storage** als de standaard opslag voor het cluster. Als u de bestanden op de standaard opslag voor het cluster wilt weer geven, dubbelklikt u op de vermelding **(standaard opslag account)** .

1. Gebruik een van de volgende methoden om de exe-bestanden te uploaden:

    * Als u een Azure Storage- **account** gebruikt, selecteert u het pictogram **BLOB uploaden** .

        ![Pictogram voor het uploaden van HDInsight voor nieuw project](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)

        Selecteer in het dialoog venster **nieuw bestand uploaden** onder **Bestands naam** de optie **Bladeren**. Ga in het dialoog venster **BLOB uploaden** naar de map *bin\debug* voor het project *HiveCSharp* en kies vervolgens het *HiveCSharp.exe* bestand. Selecteer ten slotte **openen** en klik vervolgens op **OK** om het uploaden te volt ooien.

    * Als u **Azure data Lake Storage** gebruikt, klikt u met de rechter muisknop op een leeg gebied in de bestands vermelding en selecteert u vervolgens **uploaden**. Kies ten slotte het *HiveCSharp.exe* bestand en selecteer **openen**.

    Wanneer het uploaden van *HiveCSharp.exe* is voltooid, herhaalt u het upload proces voor het *PigUDF.exe* bestand.

## <a name="run-an-apache-hive-query"></a>Een Apache Hive query uitvoeren

U kunt nu een Hive-query uitvoeren die gebruikmaakt van uw Hive UDF-toepassing.

1. Ga in Visual Studio naar Server Explorer **weer geven**  >  .

2. Vouw **Azure** uit en vouw vervolgens **HDInsight** uit.

3. Klik met de rechter muisknop op het cluster waarop u de *HiveCSharp* -toepassing hebt geïmplementeerd en selecteer vervolgens **een Hive-query schrijven**.

4. Gebruik de volgende tekst voor de Hive-query:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasbs:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Verwijder de opmerking `add file` bij de instructie die overeenkomt met het type standaard opslag dat voor uw cluster wordt gebruikt.

    Met deze query selecteert `clientid` `devicemake` u de velden, en en `devicemodel` `hivesampletable` vervolgens geeft u de velden door aan de *HiveCSharp.exe* -toepassing. De query verwacht dat de toepassing drie velden retourneert, die zijn opgeslagen als `clientid` , `phoneLabel` , en `phoneHash` . De query verwacht ook *HiveCSharp.exe* te vinden in de hoofdmap van de standaard-opslag container.

5. Schakel de standaard instelling **interactief** naar **batch** uit en selecteer vervolgens **verzenden** om de taak naar het HDInsight-cluster te verzenden. Het venster **samen vatting van Hive-taak** wordt geopend.

6. Selecteer **vernieuwen** om de samen vatting te vernieuwen totdat de **taak status** is gewijzigd in **voltooid**. Als u de taak uitvoer wilt weer geven, selecteert u **taak uitvoer**.

## <a name="run-an-apache-pig-job"></a>Een Apache Pig-taak uitvoeren

U kunt ook een Pig-taak uitvoeren die gebruikmaakt van uw toepassing voor het Toep assen van een Pig-UDF.

1. Gebruik SSH om verbinding te maken met uw HDInsight-cluster. (Voer bijvoorbeeld de opdracht uit `ssh sshuser@<clustername>-ssh.azurehdinsight.net` .) Zie [SSH-WithHDInsight gebruiken](../hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie.

2. Gebruik de volgende opdracht om de Pig-opdracht regel te starten:

    ```shell
    pig
    ```

    Er `grunt>` wordt een prompt weer gegeven.

3. Voer het volgende in om een Pig-taak uit te voeren die gebruikmaakt van de .NET Framework-toepassing:

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    De `DEFINE` instructie maakt een alias van `streamer` voor de *PigUDF.exe* -toepassing en `CACHE` laadt deze vanuit de standaard opslag voor het cluster. Later `streamer` wordt met de operator gebruikt `STREAM` om de enkele regels in te verwerken `LOG` en de gegevens als een reeks kolommen te retour neren.

    > [!NOTE]
    > De naam van de toepassing die wordt gebruikt voor streaming moet tussen het \` (apostroffen)-teken worden geplaatst wanneer het alias is en het teken ' (enkele aanhalings tekens) wanneer het wordt gebruikt met `SHIP` .

4. Nadat de laatste regel is ingevoerd, wordt de taak gestart. Het resultaat van de uitvoer is vergelijkbaar met de volgende tekst:

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. Gebruiken `exit` om Pig af te sluiten.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u een .NET Framework-toepassing kunt gebruiken uit Hive en Pig op HDInsight. Als u wilt weten hoe u python met hive en Pig kunt gebruiken, raadpleegt u [python gebruiken met Apache Hive en Apache varken in HDInsight](python-udf-hdinsight.md).

Raadpleeg de volgende artikelen voor meer informatie over het gebruik van Hive en het gebruik van MapReduce:

* [Apache Hive gebruiken met HDInsight](hdinsight-use-hive.md)
* [MapReduce gebruiken met HDInsight](hdinsight-use-mapreduce.md)
* [Basis beginselen van Pig Latijn](https://pig.apache.org/docs/latest/basic.html)
