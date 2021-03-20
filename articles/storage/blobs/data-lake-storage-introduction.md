---
title: Inleiding in Azure Data Lake Storage Gen2
description: Lees een inleiding tot Azure Data Lake Storage Gen2. Meer informatie over belangrijke functies. Bekijk de ondersteunde Blob-opslagfuncties, Azure-service-integraties en -platforms.
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 1c4d04e25bf8f7d981c998baafb468f04b66eaf1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879895"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Inleiding in Azure Data Lake Storage Gen2

‎Azure Data Lake Storage Gen2 is een reeks mogelijkheden die is toegewezen aan analyse van big data, gebouwd op [Azure Blob Storage](storage-blobs-introduction.md). 

Data Lake Storage Gen2 convergeert de mogelijkheden van [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml) met Azure Blob Storage. Data Lake Storage Gen2 biedt bijvoorbeeld semantiek van bestandssystemen, beveiliging op bestandsniveau en schaal. Omdat deze mogelijkheden zijn gebouwd op Blob Storage, krijgt u ook gelaagde opslag met mogelijkheden tot hoge beschikbaarheid en herstel na een noodgeval, tegen een scherpe prijs.

## <a name="designed-for-enterprise-big-data-analytics"></a>Ontworpen voor big data-analyse door grote ondernemingen

Data Lake Storage Gen2 maakt van Azure Storage de basis voor het bouwen van zakelijke data lakes op Azure. Data Lake Storage Gen2 is vanaf het begin ontworpen om meerdere petabytes aan gegevens te kunnen bieden met honderden gigabits aan doorvoer en stelt u in staat om eenvoudig enorme hoeveelheden gegevens te beheren.

De toevoeging van een [hiërarchische naamruimte](data-lake-storage-namespace.md) aan Blob Storage vormt een fundamenteel onderdeel van Data Lake Storage Gen2. Met de hiërarchische naamruimte worden objecten/bestanden georganiseerd in een hiërarchie met mappen voor efficiënte toegang tot de gegevens. In een gewone naamconventie voor objectopslag wordt gebruik gemaakt van slashes om een hiërarchische mapstructuur na te bootsen. Deze structuur wordt echt in Data Lake Storage Gen2. Bewerkingen (zoals het wijzigen van een naam of het verwijderen van een map) worden één atomische bewerking van metagegevens in de map. Het is niet nodig om alle objecten te inventariseren en te verwerken die het naamvoorvoegsel van de map delen.

Data Lake Storage Gen2 bouwt voort op Blob Storage en verbetert de prestaties, het beheer en de beveiliging op de volgende manieren:

-   De **prestaties** worden geoptimaliseerd omdat u geen gegevens hoeft te kopiëren of transformeren als voorwaarde voor analyse. In vergelijking met de ongestructureerde naamruimte in Blob Storage verbetert de hiërarchische naamruimte de prestaties tijdens het beheren van de structuur aanzienlijk, waardoor de algehele prestaties worden verbeterd.

-   Het **beheer** is eenvoudiger omdat u bestanden kunt ordenen en bewerken met behulp van mappen en submappen.

-   De **beveiliging** is afdwingbaar omdat u POSIX-machtigingen kunt definiëren voor structuren of afzonderlijke bestanden.

Data Lake Storage Gen2 is ook heel kosteneffectief, omdat de oplossing is gebouwd op basis van de goedkope [Azure Blob-opslag](storage-blobs-introduction.md). De extra functies verlagen de totale beheerkosten voor het uitvoeren van big data-analyses in Azure nog verder.

## <a name="key-features-of-data-lake-storage-gen2"></a>Belangrijkste functies van Data Lake Storage Gen2

-   **Hadoop-compatibele toegang**: Met Data Lake Storage Gen2 kunt u gegevens op dezelfde manier beheren en openen als met een [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Het nieuwe [ABFS-stuurprogramma](data-lake-storage-abfs-driver.md) (gebruikt voor toegang tot gegevens) is beschikbaar in alle Apache Hadoop-omgevingen. Deze omgevingen bevatten [Azure HDInsight](../../hdinsight/index.yml) *,* [Azure Databricks](/azure/databricks/) en [Azure Synapse Analytics](../../synapse-analytics/index.yml).

-   **Een superset met POSIX-machtigingen**: Het beveiligingsmodel voor Data Lake Gen2 ondersteunt ACL- en POSIX-machtigingen en extra granulariteit die specifiek is voor Data Lake Storage Gen2. Instellingen kunnen worden geconfigureerd via Storage Explorer of door middel van frameworks zoals Hive en Spark.

-   **Rendabel**: Data Lake Storage Gen2 biedt goedkope opslagcapaciteit en -transacties. Functies zoals [Azure Blob Storage-levenscyclus](storage-lifecycle-management-concepts.md) optimaliseren de kosten tijdens de levenscyclus van gegevens.

-   **Geoptimaliseerd stuurprogramma**: Het ABFS-stuurprogramma is [specifiek geoptimaliseerd](data-lake-storage-abfs-driver.md) voor big data-analyse. De bijbehorende REST API's worden aan het oppervlak gebracht via het eindpunt `dfs.core.windows.net`.

### <a name="scalability"></a>Schaalbaarheid

Azure Storage is inherent schaalbaar - of u nu toegang hebt via Data Lake Storage Gen2 of via Blob Storage-interfaces. De service kan *veel exabytes aan gegevens* opslaan en verwerken. Deze hoeveelheid opslagruimte is beschikbaar met een doorvoer in gigabits per seconde (Gbps) bij hoge aantallen invoer-/uitvoerbewerkingen per seconde (IOPS). De verwerking wordt uitgevoerd op bijna constante latenties per aanvraag, die worden gemeten op service-, account- en bestandsniveaus.

### <a name="cost-effectiveness"></a>Voordelig

Omdat Data Lake Storage Gen2 op de Azure Blob-opslag is gebouwd, zijn de opslagcapaciteit en de transactiekosten lager. In tegenstelling tot bij andere cloudopslagservices, hoeft u uw gegevens niet te verplaatsen of te transformeren voordat u deze kunt analyseren. Zie de [Prijzen voor Azure Storage](https://azure.microsoft.com/pricing/details/storage) voor meer informatie over prijzen.

Daarnaast kunnen functies, zoals de [hiërarchische naamruimte](data-lake-storage-namespace.md), de algehele prestaties van veel analysetaken aanzienlijk verbeteren. Door deze prestatieverbetering hebt u minder rekenkracht nodig om dezelfde hoeveelheid gegevens te verwerken, wat resulteert in lagere totale beheerkosten voor de analysetaak van begin tot eind.

### <a name="one-service-multiple-concepts"></a>Eén service, meerdere concepten

Omdat Data Lake Storage Gen2 is gebouwd op basis van Azure Blob-opslag, kunnen meerdere concepten dezelfde, gedeelde dingen beschrijven.

Hieronder ziet u de equivalente entiteiten, zoals beschreven in verschillende concepten. Tenzij anders aangegeven, zijn deze entiteiten direct synoniem:

| Concept                                | Organisatie op het hoogste niveau | Organisatie op lager niveau                                            | Gegevenscontainer |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobs - Objectopslag voor algemeen gebruik | Container              | Virtuele map (alleen SDK: biedt geen atomische manipulatie) | Blob           |
| Azure Data Lake Storage Gen2 - Analytics Storage          | Container            | Directory                                                           | File           |

## <a name="supported-blob-storage-features"></a>Ondersteunde Blob Storage-functies

Blob-opslagfuncties zoals [Diagnostische logboekregistratie](../common/storage-analytics-logging.md), [Toegangslagen](storage-blob-storage-tiers.md) en [Beheerbeleid van Blob Storage-levenscycli](storage-lifecycle-management-concepts.md) zijn beschikbaar voor uw account. 

Zie [Blob Storage-functies die beschikbaar zijn in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md) voor een lijst met ondersteunde Blob Storage-functies.

## <a name="supported-azure-service-integrations"></a>Ondersteunde integraties met Azure-services

Data Lake Storage Gen2 ondersteunt verschillende Azure-Services. U kunt ze gebruiken om gegevens op te nemen, analyses uit te voeren en visuele weergaven te maken. Zie [Azure-services die ondersteuning bieden voor Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md) voor een lijst met ondersteunde Azure-services.

## <a name="supported-open-source-platforms"></a>Ondersteunde open source-platforms

Verschillende open source-platformen ondersteunen Data Lake Storage Gen2. Zie [Open source-platformen die ondersteuning bieden voor Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md) voor een volledig overzicht.

## <a name="see-also"></a>Zie ook

- [Bekende problemen met Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Toegang met meerdere protocollen in Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)