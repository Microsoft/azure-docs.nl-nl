---
title: Tips voor het gebruik van Hadoop op op Linux gebaseerde HDInsight-Azure
description: Krijg implementatie tips voor het gebruik van op Linux gebaseerde HDInsight-clusters (Hadoop) in een vertrouwde Linux-omgeving die wordt uitgevoerd in de Azure-Cloud.
ms.service: hdinsight
ms.custom: hdinsightactive,seoapr2020
ms.topic: conceptual
ms.date: 04/29/2020
ms.openlocfilehash: 2d2619c7bd7bc09eeab3845599758db7134b4134
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945652"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informatie over het gebruik van HDInsight in Linux

Azure HDInsight-clusters bieden Apache Hadoop in een vertrouwde Linux-omgeving, die wordt uitgevoerd in de Azure-Cloud. Voor de meeste zaken moet het werken op dezelfde manier als elke andere Hadoop-on-Linux-installatie. Dit document roept specifieke verschillen aan waarvan u rekening moet houden.

## <a name="prerequisites"></a>Vereisten

Veel van de stappen in dit document gebruiken de volgende hulpprogram ma's, die mogelijk op uw systeem moeten worden geïnstalleerd.

* [krul](https://curl.haxx.se/) : wordt gebruikt voor communicatie met op internet gebaseerde services.
* **JQ**, een JSON-processor op de opdracht regel.  Zie [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
* [Azure cli](/cli/azure/install-azure-cli) : wordt gebruikt voor het extern beheren van Azure-Services.
* **Een SSH-client**. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Gebruikers

Tenzij een [domein is toegevoegd](./domain-joined/hdinsight-security-overview.md), moet HDInsight worden beschouwd als een systeem **met één gebruiker** . Er wordt één SSH-gebruikers account met het cluster gemaakt met machtigingen op beheerders niveau. Extra SSH-accounts kunnen worden gemaakt, maar ze hebben ook beheerders toegang tot het cluster.

HDInsight die lid is van een domein ondersteunt meerdere gebruikers en gedetailleerde instellingen voor machtigingen en rollen. Zie [HDInsight-clusters die zijn gekoppeld](./domain-joined/apache-domain-joined-manage.md)aan een domein voor meer informatie.

## <a name="domain-names"></a>Domeinnamen

De Fully Qualified Domain Name (FQDN) die moet worden gebruikt bij het maken van verbinding met het cluster via internet is `CLUSTERNAME.azurehdinsight.net` of `CLUSTERNAME-ssh.azurehdinsight.net` (alleen voor SSH).

Intern heeft elk knoop punt in het cluster een naam die is toegewezen tijdens de cluster configuratie. Zie de pagina **hosts** in de Ambari-webinterface om de cluster namen te vinden. U kunt ook het volgende gebruiken om een lijst met hosts te retour neren uit de Ambari-REST API:

```console
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'
```

Vervang `CLUSTERNAME` door de naam van uw cluster. Wanneer u hierom wordt gevraagd, voert u het wacht woord voor het beheerders account in. Met deze opdracht wordt een JSON-document geretourneerd dat een lijst bevat met de hosts in het cluster. [JQ](https://stedolan.github.io/jq/) wordt gebruikt om de `host_name` element waarde voor elke host op te halen.

Als u de naam van het knoop punt voor een specifieke service wilt zoeken, kunt u Ambari voor dat onderdeel opvragen. Gebruik bijvoorbeeld de volgende opdracht om de hosts voor het HDFS-naam knooppunt te vinden:

```console
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'
```

Met deze opdracht wordt een JSON-document geretourneerd dat de service beschrijft en vervolgens [JQ](https://stedolan.github.io/jq/) alleen de `host_name` waarde voor de hosts opgehaald.

## <a name="remote-access-to-services"></a>Externe toegang tot services

* **Ambari (web)** - `https://CLUSTERNAME.azurehdinsight.net`

    Verificatie met behulp van de gebruiker en het wacht woord van de Cluster beheerder en meld u vervolgens aan bij Ambari.

    Verificatie is in tekst zonder opmaak: gebruik altijd HTTPS om ervoor te zorgen dat de verbinding beveiligd is.

    > [!IMPORTANT]  
    > Sommige van de Web-UIs die beschikbaar zijn via Ambari-toegangs knooppunten met een interne domein naam. Interne domein namen zijn niet openbaar toegankelijk via internet. Het is mogelijk dat de fout server niet gevonden wordt weer gegeven wanneer u probeert toegang te krijgen tot sommige functies via internet.
    >
    > Als u de volledige functionaliteit van de Ambari-webgebruikersinterface wilt gebruiken, gebruikt u een SSH-tunnel om webverkeer te proxy naar het hoofd knooppunt van het cluster. Zie [ssh-tunneling gebruiken om toegang te krijgen tot Apache Ambari Web UI, Resource Manager, JobHistory, NameNode, Oozie en andere web-UIs](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - `https://CLUSTERNAME.azurehdinsight.net/ambari`

    > [!NOTE]  
    > Verificatie met behulp van de gebruikers naam en het wacht woord van de Cluster beheerder.
    >
    > Verificatie is in tekst zonder opmaak: gebruik altijd HTTPS om ervoor te zorgen dat de verbinding beveiligd is.

* **WebHCat (Templeton)** - `https://CLUSTERNAME.azurehdinsight.net/templeton`

    > [!NOTE]  
    > Verificatie met behulp van de gebruikers naam en het wacht woord van de Cluster beheerder.
    >
    > Verificatie is in tekst zonder opmaak: gebruik altijd HTTPS om ervoor te zorgen dat de verbinding beveiligd is.

* **SSH** -CLUSTERNAME-SSH.azurehdinsight.net op poort 22 of 23. Poort 22 wordt gebruikt om verbinding te maken met de primaire hoofd knooppunt, terwijl 23 wordt gebruikt om verbinding te maken met de secundaire. Zie [Beschik baarheid en betrouw baarheid van Apache Hadoop clusters in HDInsight](./hdinsight-business-continuity.md)voor meer informatie over de hoofd knooppunten.

    > [!NOTE]  
    > U kunt alleen vanaf een client computer toegang krijgen tot de hoofd knooppunten van het cluster via SSH. Nadat de verbinding tot stand is gebracht, kunt u de worker-knoop punten openen met behulp van SSH vanuit een hoofd knooppunt.

Zie voor meer informatie de [poorten die worden gebruikt door Apache Hadoop Services in HDInsight](hdinsight-hadoop-port-settings-for-services.md) -document.

## <a name="file-locations"></a>Bestandslocaties

Hadoop-gerelateerde bestanden kunnen worden gevonden op de cluster knooppunten op `/usr/hdp` . Deze map bevat de volgende submappen:

* **2.6.5.3009-43**: de directory naam is de versie van het Hadoop-platform dat wordt gebruikt door HDInsight. Het nummer op uw cluster kan afwijken van het aantal dat hier wordt vermeld.
* **huidige**: deze map bevat koppelingen naar submappen onder de Directory **2.6.5.3009-43** . Deze map bestaat, zodat u het versie nummer niet hoeft te onthouden.

Voor beelden van gegevens-en JAR-bestanden vindt u op Hadoop Distributed File System op `/example` en `/HdiSamples` .

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, Azure Storage en Data Lake Storage

In de meeste Hadoop-distributies worden de gegevens opgeslagen in HDFS. HDFS wordt ondersteund door de lokale opslag op de computers in het cluster. Het gebruik van lokale opslag kan kostenbesparend zijn voor een Cloud oplossing waarbij u per uur of per minuut voor reken resources in rekening wordt gebracht.

Wanneer u HDInsight gebruikt, worden de gegevens bestanden opgeslagen in een aanpas bare en robuuste manier in de Cloud met behulp van Azure Blob Storage en optioneel Azure Data Lake Storage Gen1/Gen2. Deze services bieden de volgende voor delen:

* Lange termijn voor goedkope opslag.
* Toegankelijkheid van externe services, zoals websites, hulpprogram ma's voor het uploaden/downloaden van bestanden, diverse taal-Sdk's en webbrowsers.
* Grote bestands capaciteit en grote aanpas bare opslag.

Zie [Azure Blob Storage](../storage/common/storage-introduction.md), [Azure data Lake Storage gen1](../data-lake-store/data-lake-store-overview.md)of [Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)voor meer informatie.

Wanneer u Azure Blob-opslag of Data Lake Storage Gen1-Gen2 gebruikt, hoeft u niets te doen van HDInsight om toegang te krijgen tot de gegevens. Met de volgende opdracht worden bijvoorbeeld bestanden in de map weer gegeven, `/example/data` ongeacht of deze zijn opgeslagen op Azure Storage of Data Lake Storage:

```console
hdfs dfs -ls /example/data
```

In HDInsight worden de gegevens opslag bronnen (Azure Blob Storage en Azure Data Lake Storage) losgekoppeld van reken resources. U kunt HDInsight-clusters maken voor de reken activiteiten en deze later verwijderen wanneer het werk is voltooid. Ondertussen blijven uw gegevens bestanden veilig bewaard in de Cloud opslag, zolang u dat nodig hebt.

### <a name="uri-and-scheme"></a><a name="URI-and-scheme"></a>URI en schema

Voor sommige opdrachten moet u mogelijk het schema opgeven als onderdeel van de URI bij het openen van een bestand. Voor de Storm-HDFS-component moet u bijvoorbeeld het schema opgeven. Wanneer u niet-standaard opslag gebruikt (opslag toegevoegd als ' extra ' opslag aan het cluster), moet u altijd het schema gebruiken als onderdeel van de URI.

Gebruik een van de volgende URI-schema's wanneer u [**Azure Storage**](./hdinsight-hadoop-use-blob-storage.md)gebruikt:

* `wasb:///`: Toegang tot de standaard opslag met niet-versleutelde communicatie.

* `wasbs:///`: Toegang tot de standaard opslag met versleutelde communicatie.  Het wasbs-schema wordt alleen ondersteund vanuit versie 3,6 van HDInsight.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Wordt gebruikt bij het communiceren met een niet-standaard-opslag account. Bijvoorbeeld wanneer u een extra opslag account hebt of gegevens die zijn opgeslagen in een openbaar toegankelijk opslag account.

Gebruik het volgende URI-schema wanneer u [**Azure data Lake Storage Gen2**](./hdinsight-hadoop-use-data-lake-storage-gen2.md)gebruikt:

* `abfs://`: Toegang tot de standaard opslag met versleutelde communicatie.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Wordt gebruikt bij het communiceren met een niet-standaard-opslag account. Bijvoorbeeld wanneer u een extra opslag account hebt of gegevens die zijn opgeslagen in een openbaar toegankelijk opslag account.

Gebruik een van de volgende URI-schema's wanneer u [**Azure data Lake Storage gen1**](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)gebruikt:

* `adl:///`: Toegang tot de standaard Data Lake Storage voor het cluster.

* `adl://<storage-name>.azuredatalakestore.net/`: Wordt gebruikt bij de communicatie met een niet-standaard Data Lake Storage. Wordt ook gebruikt voor toegang tot gegevens buiten de hoofdmap van uw HDInsight-cluster.

> [!IMPORTANT]  
> Wanneer u Data Lake Storage als de standaard opslag voor HDInsight gebruikt, moet u in de Store een pad opgeven dat moet worden gebruikt als basis van HDInsight-opslag. Het standaardpad is `/clusters/<cluster-name>/` .
>
> Wanneer `/` u of `adl:///` toegang hebt tot gegevens, kunt u alleen toegang krijgen tot gegevens die zijn opgeslagen in de hoofdmap (bijvoorbeeld `/clusters/<cluster-name>/` ) van het cluster. Als u overal in de Store toegang wilt krijgen tot gegevens, gebruikt u de `adl://<storage-name>.azuredatalakestore.net/` indeling.

### <a name="what-storage-is-the-cluster-using"></a>Welke opslag is het cluster met

U kunt Ambari gebruiken om de standaard opslag configuratie voor het cluster op te halen. Gebruik de volgende opdracht om informatie over de HDFS-configuratie op te halen met behulp van krul en deze te filteren met [JQ](https://stedolan.github.io/jq/):

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> Met deze opdracht wordt de eerste configuratie geretourneerd die is toegepast op de server ( `service_config_version=1` ) die deze informatie bevat. Mogelijk moet u alle configuratie versies weer geven om de nieuwste te vinden.

Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met de volgende Uri's:

* `wasb://<container-name>@<account-name>.blob.core.windows.net` Als u een Azure Storage-account gebruikt.

    De account naam is de naam van het Azure Storage-account. De container naam is de BLOB-container die de hoofdmap van de cluster opslag vormt.

* `adl://home` Als Azure Data Lake Storage wordt gebruikt. Als u de naam van de Data Lake Storage wilt ophalen, gebruikt u de volgende REST-aanroep:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    Met deze opdracht wordt de volgende hostnaam geretourneerd: `<data-lake-store-account-name>.azuredatalakestore.net` .

    Als u de map in de Store wilt ophalen die de hoofdmap voor HDInsight is, gebruikt u de volgende REST-aanroep:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    Met deze opdracht wordt een pad geretourneerd dat lijkt op het volgende pad: `/clusters/<hdinsight-cluster-name>/` .

U kunt de opslag informatie ook vinden met behulp van de Azure Portal door de volgende stappen uit te voeren:

1. Selecteer uw HDInsight-cluster in de [Azure Portal](https://portal.azure.com/).

2. Selecteer **opslag accounts** in de sectie **Eigenschappen** . De opslag gegevens voor het cluster worden weer gegeven.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Hoe kan ik toegang tot bestanden uit externe HDInsight

Er zijn verschillende manieren om toegang te krijgen tot gegevens van buiten het HDInsight-cluster. Hier volgen enkele koppelingen naar hulpprogram ma's en Sdk's die kunnen worden gebruikt voor het werken met uw gegevens:

Als u __Azure Blob Storage__ gebruikt, raadpleegt u de volgende koppelingen voor manieren waarop u toegang hebt tot uw gegevens:

* [Azure cli](/cli/azure/install-az-cli2): Command-Line interface opdrachten voor het werken met Azure. Na de installatie gebruikt u de `az storage` opdracht voor hulp bij het gebruik van opslag of `az storage blob` voor BLOB-specifieke opdrachten.
* [blobxfer.py](https://github.com/Azure/blobxfer): een python-script voor het werken met blobs in azure Storage.
* Diverse Sdk's:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Storage REST API](/rest/api/storageservices/Blob-Service-REST-API)

Als u __Azure data Lake Storage gen1__ gebruikt, raadpleegt u de volgende koppelingen voor manieren waarop u toegang hebt tot uw gegevens:

* [Webbrowser](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure-CLI](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Data Lake-Hulpprogram Ma's voor Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling-your-cluster"></a><a name="scaling"></a>Uw cluster schalen

Met de functie voor het schalen van clusters kunt u het aantal gegevens knooppunten dat door een cluster wordt gebruikt, dynamisch wijzigen. U kunt bewerkingen schalen terwijl andere taken of processen op een cluster worden uitgevoerd.  Zie [HDInsight-clusters schalen](./hdinsight-scaling-best-practices.md)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Hoe kan ik tint installeren (of een ander Hadoop-onderdeel)?

HDInsight is een beheerde service. Als Azure een probleem met het cluster detecteert, kan het het knoop punt dat niet werkt, verwijderen en een knoop punt maken om dit te vervangen. Wanneer u hand matig dingen op het cluster installeert, worden deze niet behouden wanneer deze bewerking wordt uitgevoerd. Gebruik in plaats daarvan [HDInsight-script acties](hdinsight-hadoop-customize-cluster-linux.md). Een script actie kan worden gebruikt om de volgende wijzigingen aan te brengen:

* Een service of website installeren en configureren.
* Installeer en configureer een onderdeel waarvoor configuratie wijzigingen moeten worden aangebracht op meerdere knoop punten in het cluster.

Script acties zijn bash-scripts. De scripts worden uitgevoerd tijdens het maken van het cluster en worden gebruikt voor het installeren en configureren van extra onderdelen. Zie [Ontwikkeling van scriptacties met HDInsight](hdinsight-hadoop-script-actions-linux.md) voor informatie over het ontwikkelen van uw eigen scriptacties.

### <a name="jar-files"></a>Jar-bestanden

Sommige Hadoop-technologieën bieden op zichzelf staande jar-bestanden. Deze bestanden bevatten functies die deel uitmaken van een MapReduce-taak, of van binnen varken of Hive. Ze vereisen vaak geen Setup en kunnen worden geüpload naar het cluster nadat het is gemaakt en rechtstreeks is gebruikt. Sla het jar-bestand op in de standaard opslag van het cluster als u er zeker van wilt zijn dat het onderdeel het herstellen van het cluster overlaat.

Als u bijvoorbeeld de meest recente versie van [Apache DataFu](https://datafu.incubator.apache.org/)wilt gebruiken, kunt u een jar met het project downloaden en uploaden naar het HDInsight-cluster. Volg vervolgens de DataFu-documentatie voor informatie over het gebruik van varken of Hive.

> [!IMPORTANT]  
> Sommige onderdelen die zelfstandige jar-bestanden zijn, worden meegeleverd met HDInsight, maar bevinden zich niet in het pad. Als u op zoek bent naar een specifiek onderdeel, kunt u het volgende gebruiken om het te zoeken in uw cluster:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Met deze opdracht wordt het pad naar overeenkomende jar-bestanden geretourneerd.

Als u een andere versie van een onderdeel wilt gebruiken, uploadt u de versie die u nodig hebt en gebruikt u deze in uw taken.

> [!IMPORTANT]
> Onderdelen die worden meegeleverd met het HDInsight-cluster, worden volledig ondersteund en Microsoft Ondersteuning helpt bij het isoleren en oplossen van problemen met betrekking tot deze onderdelen.
>
> Aangepaste onderdelen ontvangen commercieel redelijke ondersteuning om u te helpen het probleem verder op te lossen. Dit kan leiden tot het oplossen van het probleem of het vragen om beschik bare kanalen te benaderen voor de open source-technologieën waar diep gaande expertise voor die technologie wordt gevonden. Er zijn bijvoorbeeld veel community-sites die kunnen worden gebruikt, zoals: [micro soft Q&een vraag pagina voor HDInsight](/answers/topics/azure-hdinsight.html) [https://stackoverflow.com](https://stackoverflow.com) . Ook Apache-projecten hebben project sites op [https://apache.org](https://apache.org) , bijvoorbeeld: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Volgende stappen

* [HDInsight-clusters beheren met behulp van de Apache Ambari REST API](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
* [MapReduce-taken gebruiken met HDInsight](hadoop/hdinsight-use-mapreduce.md)