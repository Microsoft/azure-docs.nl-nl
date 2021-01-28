---
title: Apache Hive als ETL-hulp programma gebruiken-Azure HDInsight
description: Gebruik Apache Hive om ETL-gegevens in azure HDInsight uit te pakken, te transformeren en te laden.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: e2b76fbc2afaad465773860ff45f18261edad138
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933435"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Apache Hive gebruiken als een hulp programma voor uitpakken, transformeren en laden (ETL)

Normaal gesp roken moet u inkomende gegevens opschonen en transformeren voordat u ze laadt in een doel dat geschikt is voor analyse. Uitpakken, transformeren en laden (ETL)-bewerkingen worden gebruikt om gegevens voor te bereiden en te laden in een gegevens bestemming.  Apache Hive op HDInsight kan in ongestructureerde gegevens worden gelezen, de gegevens naar behoefte verwerken en vervolgens de gegevens in een relationeel Data Warehouse laden voor systemen voor de beslissings ondersteuning. In deze benadering worden gegevens opgehaald uit de bron. Vervolgens opgeslagen in aanpas bare opslag, zoals Azure Storage blobs of Azure Data Lake Storage. De gegevens worden vervolgens getransformeerd met behulp van een reeks Hive-query's. Vervolgens wordt de module in de voor bereiding geladen om bulksgewijs te laden in het doel gegevens archief.

## <a name="use-case-and-model-overview"></a>Overzicht van use-cases en modellen

In de volgende afbeelding ziet u een overzicht van de use-case en het model voor ETL-automatisering. Invoer gegevens worden getransformeerd om de juiste uitvoer te genereren.  Tijdens die trans formatie worden de gegevens vorm, het gegevens type en de even taal gewijzigd.  ETL-processen kunnen Britse naar metriek converteren, tijd zones wijzigen en de nauw keurigheid verbeteren om te uitlijnen met bestaande gegevens in het doel. ETL-processen kunnen ook nieuwe gegevens combi neren met bestaande gegevens om de rapportage up-to-date te houden of om meer inzicht te krijgen in bestaande gegevens. Toepassingen zoals rapportage hulpprogramma's en-services kunnen deze gegevens vervolgens in de gewenste indeling gebruiken.

![Apache Hive als ETL-architectuur](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

Hadoop wordt doorgaans gebruikt in ETL-processen die een groot aantal tekst bestanden importeren (zoals Csv's). Of een kleiner, maar vaak veranderende hoeveelheid tekst bestanden, of beide.  Hive is een uitstekend hulp programma dat u kunt gebruiken om de gegevens voor te bereiden voordat u deze in de gegevens bestemming laadt.  Met hive kunt u een schema maken over het CSV en een SQL-achtige taal gebruiken om MapReduce-Program ma's te genereren die met de gegevens werken.

De volgende stappen voor het gebruik van Hive om ETL uit te voeren zijn:

1. Gegevens laden in Azure Data Lake Storage of Azure Blob Storage.
2. Maak een meta gegevens archief-data base (met behulp van Azure SQL Database) voor gebruik door Hive in het opslaan van uw schema's.
3. Maak een HDInsight-cluster en verbind het gegevens archief.
4. Definieer het schema dat moet worden toegepast op de Lees tijd van gegevens in het gegevens archief:

    ```hql
    DROP TABLE IF EXISTS hvac;

    --create the hvac table on comma-separated sensor data stored in Azure Storage blobs

    CREATE EXTERNAL TABLE hvac(`date` STRING, time STRING, targettemp BIGINT,
        actualtemp BIGINT,
        system BIGINT,
        systemage BIGINT,
        buildingid BIGINT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    STORED AS TEXTFILE LOCATION 'wasbs://{container}@{storageaccount}.blob.core.windows.net/HdiSamples/SensorSampleData/hvac/';
    ```

5. Transformeer de gegevens en laad deze in het doel.  Er zijn verschillende manieren om Hive tijdens de trans formatie te gebruiken en te laden:

    * Query's uitvoeren op gegevens en deze voorbereiden met hive en deze opslaan als een CSV in Azure Data Lake Storage of Azure Blob-opslag.  Gebruik vervolgens een hulp programma als SQL Server Integration Services (SSIS) om deze Csv's te verkrijgen en de gegevens in een relationele doel database, zoals SQL Server, te laden.
    * Query's uitvoeren op de gegevens rechtstreeks vanuit Excel of C# met behulp van het Hive ODBC-stuur programma.
    * Gebruik [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) om de voor bereide platte CSV-bestanden te lezen en te laden in de relationele doel database.

## <a name="data-sources"></a>Gegevensbronnen

Gegevens bronnen zijn meestal externe gegevens die kunnen worden vergeleken met bestaande gegevens in uw gegevens opslag, bijvoorbeeld:

* Gegevens over sociale media, logboek bestanden, Sens oren en toepassingen die gegevens bestanden genereren.
* Gegevens sets die zijn verkregen door data providers, zoals weer statistieken of leveranciers verkoop cijfers.
* Streaming gegevens die zijn vastgelegd, gefilterd en verwerkt via een geschikt hulp programma of Framework.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Uitvoer doelen

U kunt Hive gebruiken om gegevens naar verschillende soorten doelen uit te voeren, waaronder:

* Een relationele data base, zoals SQL Server of Azure SQL Database.
* Een Data Warehouse, zoals Azure Synapse Analytics.
* Er.
* Azure Table-en Blob-opslag.
* Toepassingen of services waarvoor gegevens moeten worden verwerkt in specifieke indelingen of als bestanden die specifieke typen gegevens structuur bevatten.
* Een JSON-document archief zoals Azure Cosmos DB.

## <a name="considerations"></a>Overwegingen

Het ETL-model wordt doorgaans gebruikt wanneer u het volgende wilt doen:

`*` Laad stroom gegevens of grote volumes met semi-gestructureerde of ongestructureerde gegevens uit externe bronnen in een bestaand data base-of informatie systeem.
`*` Reinig, Transformeer en valideer de gegevens voordat u deze laadt, mogelijk door meer dan één trans formatie door middel van het cluster te gebruiken.
`*` Genereer rapporten en visualisaties die regel matig worden bijgewerkt. Als het rapport bijvoorbeeld te lang duurt om te genereren gedurende de dag, kunt u het rapport plannen om 's nachts uit te voeren. Als u automatisch een Hive-query wilt uitvoeren, kunt u [Azure Logic apps](../../logic-apps/logic-apps-overview.md) en Power shell gebruiken.

Als het doel voor de gegevens geen data base is, kunt u een bestand genereren in de juiste indeling in de query, bijvoorbeeld een CSV. Dit bestand kan vervolgens worden geïmporteerd in Excel of Power BI.

Als u verschillende bewerkingen wilt uitvoeren op de gegevens als onderdeel van het ETL-proces, kunt u overwegen hoe u deze beheert. Bepaal met bewerkingen die worden beheerd door een extern programma, in plaats van een werk stroom binnen de oplossing, of sommige bewerkingen parallel kunnen worden uitgevoerd. En om te detecteren wanneer elke taak is voltooid. Het gebruik van een werk stroom mechanisme zoals Oozie binnen Hadoop kan eenvoudiger zijn dan een reeks bewerkingen proberen te organiseren met externe scripts of aangepaste Program ma's.

## <a name="next-steps"></a>Volgende stappen

* [ETL op schaal](apache-hadoop-etl-at-scale.md)
* [`Operationalize a data pipeline`](../hdinsight-operationalize-data-pipeline.md)
