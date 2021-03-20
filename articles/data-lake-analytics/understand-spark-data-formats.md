---
title: Meer informatie over Apache Spark gegevens indelingen voor Azure Data Lake Analytics U-SQL-ontwikkel aars.
description: In dit artikel worden Apache Spark concepten beschreven om U_SQL ontwikkel aars inzicht te geven in de verschillen tussen U-SQL-en Spark-gegevens indelingen.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 399914186ce9de62ef46b682c8d4a6e51426cc26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221108"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>Verschillen tussen de U-SQL-en Spark-gegevens indelingen begrijpen

Als u [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) of [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md)wilt gebruiken, raden we u aan om uw gegevens te migreren van [Azure data Lake Storage gen1](../data-lake-store/data-lake-store-overview.md) naar [Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Naast het verplaatsen van uw bestanden, wilt u uw gegevens ook opslaan in U-SQL-tabellen, die toegankelijk zijn voor Spark.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Gegevens die zijn opgeslagen in Azure Data Lake Storage Gen1 bestanden verplaatsen

Gegevens die zijn opgeslagen in bestanden kunnen op verschillende manieren worden verplaatst:

- Een [Azure Data Factory](../data-factory/introduction.md) pijp lijn schrijven om de gegevens van [Azure data Lake Storage gen1](../data-lake-store/data-lake-store-overview.md) account te kopiëren naar het [Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) -account.
- Schrijf een Spark-taak die de gegevens van het [Azure data Lake Storage gen1](../data-lake-store/data-lake-store-overview.md) -account leest en schrijft deze naar het [Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) -account. Op basis van uw use-case wilt u deze mogelijk in een andere indeling schrijven, zoals Parquet als u de oorspronkelijke bestands indeling niet hoeft te behouden.

We raden u aan om het artikel [een upgrade uit te voeren van uw Big Data Analytics-oplossingen van Azure data Lake Storage gen1 naar Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)

## <a name="move-data-stored-in-u-sql-tables"></a>Gegevens die zijn opgeslagen in U-SQL-tabellen verplaatsen

U-SQL-tabellen worden niet begrepen door Spark. Als u gegevens hebt opgeslagen in U-SQL-tabellen, voert u een U-SQL-taak uit waarmee de tabel gegevens worden geëxtraheerd en opgeslagen in een indeling die Spark begrijpt. De meest geschikte indeling is het maken van een set Parquet-bestanden die volgt op de indeling van de Hive-metastore map.

De uitvoer kan in U-SQL worden bereikt met de ingebouwde Parquet-outputter en met behulp van de dynamische uitvoer partitionering met bestands sets om de partitie mappen te maken. [Verwerk meer bestanden dan ooit en gebruik Parquet](/archive/blogs/azuredatalake/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) voor een voor beeld van hoe u dergelijke Spark-gegevens kunt maken.

Na deze trans formatie kopieert u de gegevens zoals beschreven in het hoofd stuk [gegevens verplaatsen die zijn opgeslagen in azure data Lake Storage gen1 bestanden](#move-data-stored-in-azure-data-lake-storage-gen1-files).

## <a name="caveats"></a>Waarschuwingen

- Gegevens semantiek bij het kopiëren van bestanden vindt de kopie op het niveau van de byte. Daarom moeten dezelfde gegevens worden weer gegeven in het [Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) -account. Opmerking: Spark kan echter een andere teken reeks interpreteren. Het kan bijvoorbeeld een andere standaard waarde voor een scheidings teken voor rijen in een CSV-bestand gebruiken.
    Bovendien, als u getypte gegevens kopieert (uit tabellen), kunnen Parquet en Spark een andere precisie hebben en schalen voor sommige getypte waarden (bijvoorbeeld een float) en kunnen Null-waarden anders worden behandeld. U-SQL heeft bijvoorbeeld de C#-semantiek voor Null-waarden, terwijl Spark een logische Logic-waarde heeft voor Null-waarden.

- Gegevens organisatie (partitionen) U-SQL-tabellen bieden twee niveau partities. Het buitenste niveau ( `PARTITIONED BY` ) bevindt zich op waarde en wijst voornamelijk toe aan het Hive/Spark-partitie schema met behulp van mapstructuren. U moet ervoor zorgen dat de Null-waarden worden toegewezen aan de juiste map. Het binnenste niveau ( `DISTRIBUTED BY` ) in U-SQL biedt 4 distributie schema's: Round Robin, Range, hash en direct hash.
    Hive/Spark-tabellen bieden alleen ondersteuning voor het partitioneren van waarden of hash-partitionering, met een andere hash-functie dan U-SQL. Wanneer u uw U-SQL-tabel gegevens uitvoert, kunt u waarschijnlijk alleen toewijzen in de waarde partitioneren voor Spark en moet u de indeling van uw gegevens mogelijk verder afstemmen op basis van uw definitieve Spark-query's.

## <a name="next-steps"></a>Volgende stappen

- [Informatie over Spark-code concepten voor U-SQL-ontwikkel aars](understand-spark-code-concepts.md)
- [Upgrade uw big data Analytics-oplossingen van Azure Data Lake Storage Gen1 naar Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [.NET voor Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [Gegevens transformeren met behulp van Spark-activiteit in Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Gegevens transformeren met behulp van Hadoop Hive-activiteit in Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Wat is Apache Spark in Azure HDInsight?](../hdinsight/spark/apache-spark-overview.md)