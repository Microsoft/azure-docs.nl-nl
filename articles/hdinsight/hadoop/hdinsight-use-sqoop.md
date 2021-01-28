---
title: Apache Sqoop-taken uitvoeren met Azure HDInsight (Apache Hadoop)
description: Informatie over het gebruik van Azure PowerShell van een werk station voor het uitvoeren van Sqoop importeren en exporteren tussen een Hadoop-cluster en een Azure SQL database.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/06/2019
ms.openlocfilehash: 1c34b673cd970a9e7577b7ff01d27eb0e4cc1ac1
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946435"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Apache Sqoop gebruiken met Hadoop in HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Meer informatie over het gebruik van Apache Sqoop in HDInsight voor het importeren en exporteren van gegevens tussen een HDInsight-cluster en Azure SQL Database.

Hoewel Apache Hadoop een natuurlijke keus is voor het verwerken van niet-gestructureerde en semi-gestructureerde gegevens, zoals Logboeken en bestanden, kan het ook nodig zijn om gestructureerde gegevens te verwerken die zijn opgeslagen in relationele data bases.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) is een hulp programma dat is ontworpen om gegevens over te dragen tussen Hadoop-clusters en relationele data bases. U kunt dit gebruiken om gegevens te importeren uit een relationele Database Management System (RDBMS), zoals SQL Server, MySQL of Oracle in het Hadoop Distributed File System (HDFS), de gegevens in Hadoop te transformeren met MapReduce of Apache Hive en vervolgens de gegevens terug te exporteren naar een RDBMS. In dit artikel gebruikt u Azure SQL Database voor uw relationele data base.

> [!IMPORTANT]  
> In dit artikel wordt een test omgeving ingesteld voor het uitvoeren van de gegevens overdracht. Vervolgens kiest u een methode voor gegevens overdracht voor deze omgeving van een van de methoden in de sectie [Sqoop-taken uitvoeren](#run-sqoop-jobs), verder hieronder.

Zie [Wat is er nieuw in de cluster versies van hdinsight?](../hdinsight-component-versioning.md) voor Sqoop-versies die worden ondersteund op hdinsight-clusters?

## <a name="understand-the-scenario"></a>Inzicht in het scenario

HDInsight-cluster wordt geleverd met een aantal voorbeeld gegevens. U gebruikt de volgende twee voor beelden:

* Een Apache Log4j-logboek bestand, dat zich bevindt in `/example/data/sample.log` . De volgende logboeken worden opgehaald uit het bestand:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Een Hive-tabel `hivesampletable` met de naam, die verwijst naar het gegevens bestand dat zich bevindt in `/hive/warehouse/hivesampletable` . De tabel bevat enkele gegevens van mobiele apparaten.
  
  | Veld | Gegevenstype |
  | --- | --- |
  | clientid |tekenreeks |
  | querytime |tekenreeks |
  | markt |tekenreeks |
  | deviceplatform |tekenreeks |
  | devicemake |tekenreeks |
  | devicemodel |tekenreeks |
  | staat |tekenreeks |
  | country |tekenreeks |
  | querydwelltime |double |
  | SessionID |bigint |
  | sessionpagevieworder |bigint |

In dit artikel gebruikt u deze twee gegevens sets om het importeren en exporteren van Sqoop te testen.

## <a name="set-up-test-environment"></a><a name="create-cluster-and-sql-database"></a>Test omgeving instellen

Het cluster, SQL database en andere objecten worden via de Azure Portal gemaakt met behulp van een Azure Resource Manager sjabloon. De sjabloon is te vinden in [Azure Quick](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/)start-sjablonen. Met de Resource Manager-sjabloon wordt een Bacpac-pakket aangeroepen om de tabel schema's te implementeren in een SQL database.  Het Bacpac-pakket bevindt zich in een open bare BLOB-container https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac . Als u een persoonlijke container wilt gebruiken voor de Bacpac-bestanden, gebruikt u de volgende waarden in de sjabloon:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Importeren met behulp van een sjabloon of de Azure Portal ondersteunt alleen het importeren van een BACPAC-bestand uit Azure Blob-opslag.

1. Selecteer de volgende afbeelding om de Resource Manager-sjabloon te openen in de Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Voer de volgende eigenschappen in:

    |Veld |Waarde |
    |---|---|
    |Abonnement |Selecteer uw Azure-abonnement in de vervolg keuzelijst.|
    |Resourcegroep |Selecteer uw resource groep in de vervolg keuzelijst of maak een nieuwe.|
    |Locatie |Selecteer een regio in de vervolg keuzelijst.|
    |Clusternaam |Voer een naam in voor het Hadoop-cluster. Alleen kleine letters gebruiken.|
    |Gebruikersnaam voor clusteraanmelding |Behoud de vooraf ingevulde waarde `admin` .|
    |Wachtwoord voor clusteraanmelding |Voer een wachtwoord in.|
    |Ssh-gebruikersnaam |Behoud de vooraf ingevulde waarde `sshuser` .|
    |Ssh-wachtwoord |Voer een wachtwoord in.|
    |Aanmelding voor SQL-beheerder |Behoud de vooraf ingevulde waarde `sqluser` .|
    |Wacht woord voor SQL-beheerder |Voer een wachtwoord in.|
    |_artifacts locatie | Gebruik de standaard waarde tenzij u uw eigen Bacpac-bestand op een andere locatie wilt gebruiken.|
    |SAS-token _artifacts locatie |Leeg laten.|
    |Bacpac-bestands naam |Gebruik de standaard waarde tenzij u uw eigen Bacpac-bestand wilt gebruiken.|
    |Locatie |Gebruik de standaardwaarde.|

    De naam van de [logische SQL-Server](../../azure-sql/database/logical-servers.md) is  `<ClusterName>dbserver` . De naam van de data base is `<ClusterName>db` . De standaard naam van het opslag account is `e6qhezrh2pdqu` .

3. Selecteer **Ik ga akkoord met de bovenstaande voorwaarden**.

4. Selecteer **Aankoop**. U ziet een nieuwe tegel met de titel verzenden van implementatie voor Sjabloonimlementatie. Het duurt ongeveer 20 minuten om het cluster en de SQL-database te maken.

## <a name="run-sqoop-jobs"></a>Sqoop-taken uitvoeren

HDInsight kan Sqoop-taken uitvoeren met behulp van verschillende methoden. Gebruik de volgende tabel om te bepalen welke methode het meest geschikt is voor u en volg de koppeling voor een overzicht.

| **Gebruik deze** als u wilt... | ... een **interactieve** shell | ... **batch** verwerking | ... van dit **besturings systeem** van de client |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, UNIX, Mac OS X of Windows |
| [.NET-SDK voor Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (nu) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Beperkingen

* Bulk export-met HDInsight op basis van Linux, de Sqoop-connector die wordt gebruikt voor het exporteren van gegevens naar Microsoft SQL Server of SQL Database momenteel geen bulk toevoegingen ondersteunt.
* Batch verwerking: met HDInsight op basis van Linux, worden bij het gebruik `-batch` van de switch tijdens het invoegen meerdere toevoegingen uitgevoerd in plaats van de invoeg bewerkingen batch-Sqoop.

## <a name="next-steps"></a>Volgende stappen

U hebt nu geleerd hoe u Sqoop kunt gebruiken. Raadpleeg voor meer informatie:

* [Apache Hive gebruiken met HDInsight](./hdinsight-use-hive.md)
* [Gegevens uploaden naar hdinsight](../hdinsight-upload-data.md): andere methoden voor het uploaden van gegevens naar Hdinsight/Azure Blob Storage zoeken.
* [Apache Sqoop gebruiken voor het importeren en exporteren van gegevens tussen Apache Hadoop in HDInsight en SQL Database](./apache-hadoop-use-sqoop-mac-linux.md)