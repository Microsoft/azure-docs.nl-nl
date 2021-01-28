---
title: Door de gebruiker gedefinieerde Java-functie (UDF) met Apache Hive Azure HDInsight
description: Meer informatie over het maken van een op Java gebaseerde, door de gebruiker gedefinieerde functie (UDF) die samenwerkt met Apache Hive. In dit voor beeld wordt een tabel met tekst teken reeksen omgezet in kleine letters.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-java
ms.date: 11/20/2019
ms.openlocfilehash: bf9c2ea544c6b510a0507b6b020f0eae0b101dc7
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946560"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Een Java UDF gebruiken met Apache Hive in HDInsight

Meer informatie over het maken van een op Java gebaseerde, door de gebruiker gedefinieerde functie (UDF) die samenwerkt met Apache Hive. Met de Java UDF in dit voor beeld wordt een tabel met tekst teken reeksen geconverteerd naar alle-kleine letters.

## <a name="prerequisites"></a>Vereisten

* Een Hadoop-cluster in HDInsight. Zie aan de [slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).
* [Java Developer Kit (JDK) versie 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* [Apache Maven](https://maven.apache.org/download.cgi) correct [geïnstalleerd](https://maven.apache.org/install.html) volgens Apache.  Maven is een systeem voor het bouwen van Java-projecten.
* Het [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) voor de primaire opslag voor uw clusters. Dit is wasb://voor Azure Storage, abfs://voor Azure Data Lake Storage Gen2 of adl://voor Azure Data Lake Storage Gen1. Als beveiligde overdracht is ingeschakeld voor Azure Storage, wordt de URI `wasbs://`.  Zie ook [beveiligde overdracht](../../storage/common/storage-require-secure-transfer.md).

* Een tekst editor of Java IDE

    > [!IMPORTANT]  
    > Als u de python-bestanden op een Windows-client maakt, moet u een editor gebruiken die gebruikmaakt van LF als een lijn die eindigt. Als u niet zeker weet of uw editor LF of CRLF gebruikt, raadpleegt u de sectie [probleem oplossing](#troubleshooting) voor de stappen voor het verwijderen van het CR-teken.

## <a name="test-environment"></a>Test omgeving

De omgeving die voor dit artikel wordt gebruikt, is een computer met Windows 10.  De opdrachten zijn uitgevoerd in een opdracht prompt en de verschillende bestanden zijn bewerkt met Klad blok. Wijzig dienovereenkomstig voor uw omgeving.

Voer vanaf een opdracht prompt de onderstaande opdrachten in om een werk omgeving te maken:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Een voor beeld maken Java UDF

1. Maak een nieuw Maven-project door de volgende opdracht in te voeren:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Met deze opdracht maakt u een map `exampleudf` met de naam, die het Maven-project bevat.

2. Zodra het project is gemaakt, verwijdert u de `exampleudf/src/test` map die is gemaakt als onderdeel van het project door de volgende opdracht in te voeren:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Open `pom.xml` door de volgende opdracht in te voeren:

    ```cmd
    notepad pom.xml
    ```

    Vervang vervolgens de bestaande `<dependencies>` vermelding door de volgende XML:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    Deze vermeldingen geven de versie van Hadoop en Hive op die is opgenomen in HDInsight 3,6. Informatie over de versies van Hadoop en Hive in HDInsight vindt u in het document [versie van hdinsight-onderdelen](../hdinsight-component-versioning.md) .

    Een `<build>` sectie toevoegen vóór de `</project>` regel aan het einde van het bestand. Deze sectie moet de volgende XML bevatten:

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
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
        </plugins>
    </build>
    ```

    Deze vermeldingen bepalen hoe het project moet worden gebouwd. Met name de versie van Java die het project gebruikt en hoe u een uberjar maakt voor implementatie naar het cluster.

    Sla het bestand op nadat de wijzigingen zijn aangebracht.

4. Voer de onderstaande opdracht in om een nieuw bestand te maken en te openen `ExampleUDF.java` :

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Kopieer en plak de Java-code hieronder in het nieuwe bestand. Sluit het bestand.

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    Met deze code wordt een UDF geïmplementeerd die een teken reeks waarde accepteert, en wordt een kleine versie van de teken reeks geretourneerd.

## <a name="build-and-install-the-udf"></a>De UDF maken en installeren

Vervang in de onderstaande opdrachten door `sshuser` de daad werkelijke gebruikers naam als deze niet overeenkomt. Vervang door `mycluster` de daad werkelijke cluster naam.

1. Compileer de UDF en verpak deze door de volgende opdracht in te voeren:

    ```cmd
    mvn compile package
    ```

    Met deze opdracht bouwt en verpakt de UDF in het `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` bestand.

2. Gebruik de `scp` opdracht om het bestand te kopiëren naar het HDInsight-cluster door de volgende opdracht in te voeren:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Maak verbinding met het cluster via SSH door de volgende opdracht in te voeren:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. Kopieer vanuit de open SSH-sessie het jar-bestand naar HDInsight-opslag.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>De UDF van Hive gebruiken

1. Start de Beeline-client vanuit de SSH-sessie door de volgende opdracht in te voeren:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Bij deze opdracht wordt ervan uitgegaan dat u de standaard **beheerder** hebt gebruikt voor het aanmeldings account voor uw cluster.

2. Zodra u de prompt hebt ontvangen `jdbc:hive2://localhost:10001/>` , voert u het volgende in om de UDF toe te voegen aan Hive en deze beschikbaar te maken als een functie.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Gebruik de UDF om waarden die zijn opgehaald uit een tabel te converteren naar kleine letters.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Met deze query wordt de status uit de tabel geselecteerd, wordt de teken reeks geconverteerd naar kleine letters en vervolgens weer gegeven samen met de naam die niet is gewijzigd. De uitvoer ziet er ongeveer als volgt uit:

    ```output
    +---------------+---------------+--+
    |  exampleudf   |     state     |
    +---------------+---------------+--+
    | california    | California    |
    | pennsylvania  | Pennsylvania  |
    | pennsylvania  | Pennsylvania  |
    | pennsylvania  | Pennsylvania  |
    | colorado      | Colorado      |
    | colorado      | Colorado      |
    | colorado      | Colorado      |
    | utah          | Utah          |
    | utah          | Utah          |
    | colorado      | Colorado      |
    +---------------+---------------+--+
    ```

## <a name="troubleshooting"></a>Problemen oplossen

Bij het uitvoeren van de Hive-taak kan er een fout melding worden weer gegeven die vergelijkbaar is met de volgende tekst:

```output
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
```

Dit probleem kan worden veroorzaakt door de regel die in het python-bestand wordt beëindigd. Veel Windows-editors maken standaard gebruik van CRLF als lijn einde, maar Linux-toepassingen verwachten meestal LF.

U kunt de volgende Power shell-instructies gebruiken om de CR-tekens te verwijderen voordat u het bestand uploadt naar HDInsight:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Volgende stappen

Zie [Apache Hive gebruiken met HDInsight](hdinsight-use-hive.md)voor andere manieren om met hive te werken.

Zie voor meer informatie over Hive-User-Defined functies [Apache Hive Opera tors en de sectie User-Defined functies](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) van de Hive-wiki op Apache.org.