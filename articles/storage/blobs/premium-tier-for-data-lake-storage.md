---
title: Premium-laag voor Azure Data Lake Storage | Microsoft Docs
description: Gebruik de laag Premium-prestaties met Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/30/2020
ms.author: normesta
ms.openlocfilehash: be440407fb6f4d9715ba80c584af023a5f662394
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93324226"
---
# <a name="premium-tier-for-azure-data-lake-storage"></a>Premium-laag voor Azure Data Lake Storage

Azure Data Lake Storage Gen2 ondersteunt nu de [laag Premium-prestaties](storage-blob-performance-tiers.md#premium-performance). De Premium-prestatie-laag is ideaal voor big data Analytics-toepassingen en-workloads die een lage consistente latentie vereisen en een groot aantal trans acties hebben.

## <a name="workloads-that-can-benefit-from-the-premium-performance-tier"></a>Workloads die kunnen profiteren van de Premium-prestatie-laag

Voor beelden van werk belastingen zijn interactieve workloads, IoT, streaming Analytics, kunst matige intelligentie en machine learning. 

**Interactieve werk belastingen** 

Voor deze werk belastingen zijn directe updates en gebruikers feedback vereist, zoals e-commerce en het toewijzen van toepassingen, interactieve video toepassingen, enzovoort. Zo worden in een e-commerce-toepassing minder vaak bekeken items waarschijnlijk niet in de cache opgeslagen. Ze moeten echter direct worden weer gegeven aan de klant op aanvraag. Een ander voor beeld: gegevens wetenschappers, analisten en ontwikkel aars kunnen tijd gevoelige inzichten zelfs sneller afleiden door query's uit te voeren op gegevens die zijn opgeslagen in een account dat gebruikmaakt van de Premium-prestatie-laag. 

**IoT/streaming-analyse** 

In een IoT-scenario kunnen veel kleinere schrijf bewerkingen elke seconde worden gepusht naar de Cloud. Grote hoeveel heden gegevens kunnen worden opgenomen, verzameld voor analyse doeleinden en vervolgens bijna onmiddellijk worden verwijderd. De hoge opname mogelijkheden van de laag Premium-prestaties maken het efficiënt voor dit type werk belasting. 

**Kunst matige intelligentie/machine learning (AI/ML)** 

AI/ML behandelt het gebruik en de verwerking van verschillende gegevens typen, zoals visuele elementen, spraak en tekst. Dit type werk belasting met hoge prestaties wordt behandeld met grote hoeveel heden gegevens waarvoor snelle reacties en efficiënte opname tijden voor gegevens analyse zijn vereist. 

## <a name="cost-effectiveness"></a>Voordelig

De laag Premium-prestaties heeft een hogere opslag kosten, maar een lagere transactie kosten in vergelijking met de laag standaard prestatie. Als uw toepassingen en werk belastingen een groot aantal trans acties uitvoeren, kan de Premium-prestatie laag rendabel zijn.

In de volgende tabel ziet u de kosten effectiviteit van de Premium-laag voor Azure Data Lake Storage. Elke kolom vertegenwoordigt het aantal trans acties in een maand.  Elke rij vertegenwoordigt het percentage trans acties dat lees transacties zijn. Elke cel in de tabel bevat het percentage kosten reductie dat is gekoppeld aan een lees transactie percentage en het aantal uitgevoerde trans acties. 

Ervan uitgaande dat uw account zich in de regio VS Oost 2 bevindt, het aantal trans acties met uw account groter is dan 90 en 70% van die trans acties Lees transacties zijn, is de laag Premium-prestaties rendabeler.

> [!div class="mx-imgBorder"]
> ![afbeelding komt hier](./media/premium-tier-for-data-lake-storage/premium-performance-data-lake-storage-cost-analysis-table.png)

> [!NOTE] 
> Als u de kosten effectiviteit wilt beoordelen op basis van het aantal trans acties per seconde voor elke TB aan gegevens, kunt u de kolom koppen gebruiken die onder aan de tabel worden weer gegeven.

Zie de pagina met [prijzen voor Azure data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/) voor meer informatie over prijzen.

## <a name="feature-availability"></a>Beschikbaarheid van functies 

Sommige functies van de Blob-opslag zijn mogelijk niet beschikbaar of bevatten slechts gedeeltelijk ondersteuning met de laag Premium-prestaties. Zie [Blob Storage-functies die beschikbaar zijn in azure data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)voor een volledige lijst. Bekijk vervolgens een lijst met [bekende problemen](data-lake-storage-known-issues.md) om eventuele hiaten in de functionaliteit te beoordelen.

## <a name="enabling-the-premium-performance-tier"></a>De Premium-laag inschakelen 

U kunt de Premium-laag voor Azure Data Lake Storage gebruiken door een BlockBlobStorage-account te maken met de instelling **hiërarchische naam ruimte** **ingeschakeld**. Zie [een BlockBlobStorage-account account maken](storage-blob-create-account-block-blob.md) voor volledige instructies.

Wanneer u het account maakt, moet u ervoor zorgen dat u de optie **Premium** -prestaties en het account type **BlockBlobStorage** kiest.

> [!div class="mx-imgBorder"]
> ![Blockblobstorageacount maken](./media/premium-tier-for-data-lake-storage/create-block-blob-storage-account.png)

Schakel de instelling **hiërarchische naam ruimte** in op het tabblad **Geavanceerd** van de pagina **opslag account maken** . U moet deze instelling inschakelen wanneer u het account maakt. U kunt deze later niet inschakelen.

In de volgende afbeelding ziet u deze instelling op de pagina **opslag account maken** .

> [!div class="mx-imgBorder"]
> ![Instelling voor hiërarchische naam ruimte](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

## <a name="regional-availability"></a>Regionale beschikbaarheid

De Premium-laag voor Azure Data Lake Storage is beschikbaar in de volgende regio's.

|Region|Redundantie|
|--|--|
|VS - oost|LRS, ZRS|
|VS - oost 2|LRS, ZRS|
|VS - centraal|LRS|
|VS - west|LRS|
|VS - west 2|LRS, ZRS|
|VS - west-centraal|LRS|
|VS - zuid-centraal|LRS|
|Canada - midden|LRS|
|Canada - oost|LRS|
|Europa - noord|LRS, ZRS|
|Europa -west|LRS, ZRS|
|Verenigd Koninkrijk Zuid|LRS|
|Verenigd Koninkrijk West|LRS|
|Frankrijk - centraal|LRS|
|Azië - oost|LRS|
|Korea - centraal|LRS|
|Korea - zuid|LRS|
|India - centraal|LRS|
|India - west|LRS|
|VAE - noord|LRS|
|Japan - oost|LRS|
|Japan - west|LRS|
|Azië - zuidoost|LRS, ZRS|
|Australië - oost|LRS, ZRS|
|Australië - zuidoost|LRS|
|Brazilië - zuid|LRS|

## <a name="next-steps"></a>Volgende stappen

Gebruik de Premium-laag voor Azure Data Lake Storage met uw favoriete analyse service, zoals Azure Databricks, Azure HDInsight en Azure Synapse Analytics. 

- [Zelf studie: Azure Data Lake Storage Gen2, Azure Databricks & Spark](data-lake-storage-use-databricks-spark.md) 
- [Azure data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) HDInsight ondersteunt momenteel een account dat gebruikmaakt van de Premium-prestatie-laag, samen met een HBase-cluster waarvoor versnelde schrijf bewerkingen zijn ingeschakeld.
- [Quickstart: Een Synapse-werkruimte maken](../../synapse-analytics/quickstart-create-workspace.md)

