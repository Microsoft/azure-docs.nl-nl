---
title: Python UDF met Apache Hive en Apache varken-Azure HDInsight
description: Meer informatie over het gebruik van python door de gebruiker gedefinieerde functies (UDF) van Apache Hive en Apache varken in HDInsight, de Apache Hadoop technologie stack op Azure.
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive, devx-track-python
ms.openlocfilehash: 593b809813f949cd1d0bcc17e1d1b7255ea19130
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944268"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Met python door de gebruiker gedefinieerde functies (UDF) met Apache Hive en Apache varken in HDInsight gebruiken

Meer informatie over het gebruik van python door de gebruiker gedefinieerde functies (UDF) met Apache Hive en Apache varken in Apache Hadoop op Azure HDInsight.

## <a name="python-on-hdinsight"></a><a name="python"></a>Python in HDInsight

Python 2.7 wordt standaard geïnstalleerd op HDInsight 3,0 en hoger. Apache Hive kan worden gebruikt met deze versie van python voor het verwerken van streams. De verwerking van streams maakt gebruik van STDOUT en STDIN voor het door geven van gegevens tussen de Hive en de UDF.

HDInsight omvat ook jython, een python-implementatie die is geschreven in Java. Jython wordt rechtstreeks uitgevoerd op het Java Virtual Machine en maakt geen gebruik van streaming. Jython is de aanbevolen Python-interpreter bij het gebruik van python met Pig.

## <a name="prerequisites"></a>Vereisten

* **Een Hadoop-cluster in HDInsight**. Zie aan de [slag met HDInsight op Linux](apache-hadoop-linux-tutorial-get-started.md).
* **Een SSH-client**. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* Het [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) voor de primaire opslag voor uw clusters. Dit is `wasb://` voor Azure Storage voor `abfs://` Azure Data Lake Storage Gen2 of adl://voor Azure data Lake Storage gen1. Als beveiligde overdracht is ingeschakeld voor Azure Storage, zou de URI wasbs://zijn.  Zie ook [beveiligde overdracht](../../storage/common/storage-require-secure-transfer.md).
* **Mogelijke wijziging van de opslag configuratie.**  Zie [opslag configuratie](#storage-configuration) als u een type opslag account gebruikt `BlobStorage` .
* Optioneel.  Als u Power shell wilt gebruiken, hebt u de [AZ-module](/powershell/azure/new-azureps-module-az) geïnstalleerd.

> [!NOTE]  
> Het opslag account dat in dit artikel wordt gebruikt, is Azure Storage met [beveiligde overdracht](../../storage/common/storage-require-secure-transfer.md) ingeschakeld en `wasbs` wordt dus in het hele artikel gebruikt.

## <a name="storage-configuration"></a>Opslagconfiguratie

U hoeft geen actie te ondernemen als het gebruikte opslag account van soort `Storage (general purpose v1)` of is `StorageV2 (general purpose v2)` .  Het proces in dit artikel produceert een uitvoer naar ten minste `/tezstaging` .  Een standaard configuratie voor Hadoop bevat `/tezstaging` in de `fs.azure.page.blob.dir` configuratie variabele `core-site.xml` voor service `HDFS` .  Deze configuratie zorgt ervoor dat de uitvoer naar de Directory wordt pagina-blobs, die niet worden ondersteund voor het type opslag account `BlobStorage` .  Als u `BlobStorage` dit artikel wilt gebruiken, verwijdert u `/tezstaging` uit de `fs.azure.page.blob.dir` configuratie variabele.  De configuratie kan worden geopend vanuit de [Ambari-gebruikers interface](../hdinsight-hadoop-manage-ambari.md).  Anders wordt het volgende fout bericht weer gegeven: `Page blob is not supported for this account type.`

> [!WARNING]  
> De stappen in dit document maken de volgende veronderstellingen:  
>
> * U maakt de python-scripts in uw lokale ontwikkel omgeving.
> * U uploadt de scripts naar HDInsight met behulp van de `scp` opdracht of het Power shell-script.
>
> Als u de [Azure Cloud shell (bash)](../../cloud-shell/overview.md) wilt gebruiken om met HDInsight te werken, moet u het volgende doen:
>
> * Maak de scripts in de Cloud shell-omgeving.
> * Gebruiken `scp` om de bestanden te uploaden van de Cloud shell naar HDInsight.
> * Gebruik `ssh` vanuit de Cloud shell om verbinding te maken met HDInsight en de voor beelden uit te voeren.

## <a name="apache-hive-udf"></a><a name="hivepython"></a>Apache Hive UDF

Python kan worden gebruikt als een UDF-onderdeel van de HiveQL- `TRANSFORM` instructie. De volgende HiveQL roept bijvoorbeeld het `hiveudf.py` bestand op dat is opgeslagen in het standaard Azure Storage account voor het cluster.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Dit voor beeld doet er als volgt uit:

1. De `add file` instructie aan het begin van het bestand voegt het `hiveudf.py` bestand toe aan de gedistribueerde cache, zodat het toegankelijk is voor alle knoop punten in het cluster.
2. De `SELECT TRANSFORM ... USING` instructie selecteert gegevens van de `hivesampletable` . Ook worden de waarden voor ClientID, devicemake en devicemodel door gegeven aan het `hiveudf.py` script.
3. De `AS` component beschrijft de velden die worden geretourneerd door `hiveudf.py` .

<a name="streamingpy"></a>

### <a name="create-file"></a>Bestand maken

Maak in uw ontwikkel omgeving een tekst bestand met de naam `hiveudf.py` . Gebruik de volgende code als de inhoud van het bestand:

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

Met dit script worden de volgende acties uitgevoerd:

1. Hiermee wordt een gegevens regel gelezen uit STDIN.
2. Het afsluitende nieuwe regel teken wordt verwijderd met `string.strip(line, "\n ")` .
3. Bij het verwerken van streams bevat één regel alle waarden met een tabteken tussen elke waarde. Daarom `string.split(line, "\t")` kan worden gebruikt om de invoer op elk tabblad te splitsen en alleen de velden te retour neren.
4. Wanneer de verwerking is voltooid, moet de uitvoer naar STDOUT worden geschreven als één regel, met een tab tussen de velden. Bijvoorbeeld `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. De `while` lus wordt herhaald totdat er geen `line` wordt gelezen.

De script uitvoer is een samen voeging van de invoer waarden voor `devicemake` en en `devicemodel` een hash van de samengevoegde waarde.

### <a name="upload-file-shell"></a>Bestand uploaden (shell)

Vervang in de onderstaande opdrachten door `sshuser` de daad werkelijke gebruikers naam als deze niet overeenkomt.  Vervang door `mycluster` de daad werkelijke cluster naam.  Zorg ervoor dat het bestand zich in de werkmap bevindt.

1. Gebruiken `scp` om de bestanden te kopiëren naar uw HDInsight-cluster. Bewerk en voer de volgende opdracht in:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Gebruik SSH om verbinding te maken met het cluster.  Bewerk en voer de volgende opdracht in:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Voeg vanuit de SSH-sessie de python-bestanden toe die eerder zijn geüpload naar de opslag voor het cluster.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Hive UDF gebruiken (shell)

1. Als u verbinding wilt maken met Hive, gebruikt u de volgende opdracht vanuit uw open SSH-sessie:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Met deze opdracht wordt de Beeline-client gestart.

2. Voer bij de prompt de volgende query in `0: jdbc:hive2://headnodehost:10001/>` :

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Nadat de laatste regel is ingevoerd, wordt de taak gestart. Zodra de taak is voltooid, wordt de uitvoer weer gegeven zoals in het volgende voor beeld:

    ```output
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    ```

4. Als u Beeline wilt afsluiten, voert u de volgende opdracht in:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Bestand uploaden (Power shell)

Power shell kan ook worden gebruikt om Hive-query's extern uit te voeren. Zorg ervoor dat de werkmap zich `hiveudf.py` bevindt.  Gebruik het volgende Power shell-script om een Hive-query uit te voeren die gebruikmaakt van het `hiveudf.py` script:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Revise file path as needed
$pathToStreamingFile = ".\hiveudf.py"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToStreamingFile `
    -Blob "hiveudf.py" `
    -Container $container `
    -Context $context
```

> [!NOTE]  
> Voor meer informatie over het uploaden van bestanden, zie de [Upload gegevens voor Apache Hadoop taken in HDInsight](../hdinsight-upload-data.md) -document.

#### <a name="use-hive-udf"></a>Hive UDF gebruiken

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"

$HiveQuery = "add file wasbs:///hiveudf.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python hiveudf.py' AS " +
                "(clientid string, phoneLabel string, phoneHash string) " +
                "FROM hivesampletable " +
                "ORDER BY clientid LIMIT 50;"

# Create Hive job object
$jobDefinition = New-AzHDInsightHiveJobDefinition `
    -Query $HiveQuery

# For status bar updates
$activity="Hive query"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting query..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting on query to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
   -Clustername $clusterName `
   -JobId $job.JobId `
   -HttpCredential $creds `
   -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

De uitvoer voor de **Hive** -taak moet er ongeveer uitzien als in het volgende voor beeld:

```output
100041    RIM 9650    d476f3687700442549a83fac4560c51c
100041    RIM 9650    d476f3687700442549a83fac4560c51c
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
```

## <a name="apache-pig-udf"></a><a name="pigpython"></a>Apache-Pig UDF

Een python-script kan worden gebruikt als een UDF van varken via de- `GENERATE` instructie. U kunt het script uitvoeren met behulp van jython of C python.

* Jython wordt uitgevoerd op de JVM en kan systeem eigen van varken worden aangeroepen.
* C python is een extern proces, zodat de gegevens van varken op het JVM worden verzonden naar het script dat wordt uitgevoerd in een python-proces. De uitvoer van het python-script wordt weer teruggestuurd naar Pig.

Als u de Python-interpreter wilt opgeven, gebruikt u `register` bij het verwijzen naar het python-script. De volgende voor beelden registreren scripts met varkens als `myfuncs` :

* **Jython gebruiken**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **C Python gebruiken**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> Wanneer u jython gebruikt, kan het pad naar het pig_jython bestand een lokaal pad of een WASBS://-pad zijn. Bij gebruik van C python moet u echter verwijzen naar een bestand op het lokale bestands systeem van het knoop punt dat u gebruikt om de Pig-taak te verzenden.

Als de vorige registratie is uitgevoerd, is de Pig-Latijns voor dit voor beeld hetzelfde voor beide:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Dit voor beeld doet er als volgt uit:

1. De eerste regel laadt het voorbeeld gegevensbestand `sample.log` in `LOGS` . Het definieert ook elke record als een `chararray` .
2. De volgende regel filtert alle Null-waarden, waarbij het resultaat van de bewerking wordt opgeslagen in `LOG` .
3. Vervolgens wordt de query herhaald voor de records in `LOG` en gebruikt `GENERATE` om de methode aan te roepen `create_structure` die in het python/jython-script is geladen als `myfuncs` . `LINE` wordt gebruikt om de huidige record door te geven aan de functie.
4. Ten slotte worden de uitvoer naar STDOUT gedumpt met behulp van de `DUMP` opdracht. Met deze opdracht worden de resultaten weer gegeven nadat de bewerking is voltooid.

### <a name="create-file"></a>Bestand maken

Maak in uw ontwikkel omgeving een tekst bestand met de naam `pigudf.py` . Gebruik de volgende code als de inhoud van het bestand:

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema


@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

In het Latijnse voor beeld van het varken `LINE` wordt de invoer gedefinieerd als een chararray, omdat er geen consistent schema is voor de invoer. Het python-script transformeert de gegevens naar een consistent schema voor uitvoer.

1. De `@outputSchema` instructie definieert de indeling van de gegevens die worden geretourneerd naar varken. In dit geval is het een **gegevens verzameling**, een Pig-gegevens type. De Bag bevat de volgende velden, die allemaal chararray (teken reeksen) zijn:

   * datum: de datum waarop de logboek vermelding is gemaakt
   * tijdstip: de tijd waarop de logboek vermelding is gemaakt
   * ClassName-de naam van de klasse waarin de vermelding is gemaakt
   * niveau-het logboek niveau
   * Details-uitgebreide Details voor de logboek vermelding

2. Vervolgens definieert de `def create_structure(input)` functie waarmee Pig regel items worden door gegeven.

3. De voorbeeld gegevens, `sample.log` , voornamelijk conform de datum, tijd, klassenaam, het niveau en het detail schema. Het bevat echter een paar regels die beginnen met `*java.lang.Exception*` . Deze regels moeten worden aangepast zodat ze overeenkomen met het schema. `if`Met de instructie wordt gecontroleerd op die, vervolgens worden de invoer gegevens gemassaged om de `*java.lang.Exception*` teken reeks naar het einde te verplaatsen, waarbij de gegevens in line worden gebracht met het verwachte uitvoer schema.

4. Vervolgens wordt de `split` opdracht gebruikt om de gegevens te splitsen bij de eerste vier spatie tekens. De uitvoer wordt toegewezen aan `date` , `time` , `classname` , `level` en `detail` .

5. Ten slotte worden de waarden geretourneerd naar varken.

Wanneer de gegevens worden geretourneerd naar varken, heeft het een consistent schema zoals gedefinieerd in de `@outputSchema` instructie.

### <a name="upload-file-shell"></a>Bestand uploaden (shell)

Vervang in de onderstaande opdrachten door `sshuser` de daad werkelijke gebruikers naam als deze niet overeenkomt.  Vervang door `mycluster` de daad werkelijke cluster naam.  Zorg ervoor dat het bestand zich in de werkmap bevindt.

1. Gebruiken `scp` om de bestanden te kopiëren naar uw HDInsight-cluster. Bewerk en voer de volgende opdracht in:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Gebruik SSH om verbinding te maken met het cluster.  Bewerk en voer de volgende opdracht in:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Voeg vanuit de SSH-sessie de python-bestanden toe die eerder zijn geüpload naar de opslag voor het cluster.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```

### <a name="use-pig-udf-shell"></a>Pig-UDF gebruiken (shell)

1. Als u verbinding wilt maken met Pig, gebruikt u de volgende opdracht vanuit uw open SSH-sessie:

    ```bash
    pig
    ```

2. Voer de volgende instructies in bij de `grunt>` prompt:

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Nadat u de volgende regel hebt ingevoerd, wordt de taak gestart. Zodra de taak is voltooid, wordt de uitvoer weer gegeven die vergelijkbaar is met de volgende gegevens:

    ```output
    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))
    ```

4. Gebruik `quit` om de grunt-shell af te sluiten en gebruik vervolgens het volgende om het pigudf.py-bestand op het lokale bestands systeem te bewerken:

    ```bash
    nano pigudf.py
    ```

5. Verwijder een opmerking in de volgende regel in de editor door het teken te verwijderen `#` uit het begin van de regel:

    ```bash
    #from pig_util import outputSchema
    ```

    Deze regel wijzigt het python-script om te werken met C python in plaats van jython. Nadat de wijziging is aangebracht, gebruikt u **CTRL + X** om de editor af te sluiten. Selecteer **Y** en **Voer** deze in om de wijzigingen op te slaan.

6. Gebruik de `pig` opdracht om de shell opnieuw te starten. Als u zich bij de prompt bevindt `grunt>` , gebruikt u de volgende opdracht om het python-script uit te voeren met de C Python-interpreter.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Zodra deze taak is voltooid, ziet u dezelfde uitvoer als wanneer u het script eerder hebt uitgevoerd met behulp van jython.

### <a name="upload-file-powershell"></a>Bestand uploaden (Power shell)

Power shell kan ook worden gebruikt om Hive-query's extern uit te voeren. Zorg ervoor dat de werkmap zich `pigudf.py` bevindt.  Gebruik het volgende Power shell-script om een Hive-query uit te voeren die gebruikmaakt van het `pigudf.py` script:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Revise file path as needed
$pathToJythonFile = ".\pigudf.py"


# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToJythonFile `
    -Blob "pigudf.py" `
    -Container $container `
    -Context $context
```

### <a name="use-pig-udf-powershell"></a>Pig UDF gebruiken (Power shell)

> [!NOTE]  
> Bij het extern verzenden van een taak met behulp van Power shell is het niet mogelijk om C python als de interpreter te gebruiken.

Power shell kan ook worden gebruikt om Latijnse taken uit te voeren. `pigudf.py`Gebruik het volgende Power shell-script om een Pig-taak uit te voeren die gebruikmaakt van het script:

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"


$PigQuery = "Register wasbs:///pigudf.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
            "LOG = FILTER LOGS by LINE is not null;" +
            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
            "DUMP DETAILS;"

# Create Pig job object
$jobDefinition = New-AzHDInsightPigJobDefinition -Query $PigQuery

# For status bar updates
$activity="Pig job"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting job..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting for the Pig job to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -Job $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds `
    -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

De uitvoer voor de **Pig** -taak moet er ongeveer uitzien als de volgende gegevens:

```output
((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))
```

## <a name="troubleshooting"></a><a name="troubleshooting"></a>Problemen oplossen

### <a name="errors-when-running-jobs"></a>Fouten bij het uitvoeren van taken

Bij het uitvoeren van de Hive-taak kan er een fout optreden die vergelijkbaar is met de volgende tekst:

```output
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
```

Dit probleem kan worden veroorzaakt door de regel die in het python-bestand wordt beëindigd. Veel Windows-editors maken standaard gebruik van CRLF als lijn einde, maar Linux-toepassingen verwachten meestal LF.

U kunt de volgende Power shell-instructies gebruiken om de CR-tekens te verwijderen voordat u het bestand uploadt naar HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>PowerShell-scripts

Beide Power shell-voorbeeld scripts die worden gebruikt om de voor beelden uit te voeren, bevatten een opmerkings regel die de fout uitvoer voor de taak weergeeft. Als de verwachte uitvoer van de taak niet wordt weer gegeven, maakt u een opmerking bij de volgende regel en bekijkt u of de fout gegevens duiden op een probleem.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

De fout informatie (STDERR) en het resultaat van de taak (STDOUT) worden ook geregistreerd in uw HDInsight-opslag.

| Voor deze taak... | Bekijk deze bestanden in de BLOB-container |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next-steps"></a><a name="next"></a>Volgende stappen

Zie [een module implementeren in azure HDInsight](/archive/blogs/benjguin/how-to-deploy-a-python-module-to-windows-azure-hdinsight)als u python-modules wilt laden die niet standaard worden meegeleverd.

Raadpleeg de volgende documenten voor andere manieren om Pig en Hive te gebruiken en om meer te leren over het gebruik van MapReduce:

* [Apache Hive gebruiken met HDInsight](hdinsight-use-hive.md)
* [MapReduce gebruiken met HDInsight](hdinsight-use-mapreduce.md)