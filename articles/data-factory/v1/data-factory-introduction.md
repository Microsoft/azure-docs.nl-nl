---
title: Inleiding tot Data Factory, een gegevensintegratieservice
description: 'Meer informatie over Azure Data Factory: een cloudgebaseerde service voor gegevensintegratie waarmee de verplaatsing en transformatie van gegevens wordt beheerd en geautomatiseerd.'
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: overview
ms.date: 01/22/2018
ms.openlocfilehash: 220c1969055ffaa5d1bea41ab547efd4596f8a20
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786461"
---
# <a name="introduction-to-azure-data-factory"></a>Inleiding tot Azure Data Factory 
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-introduction.md)
> * [Versie 2 (huidige versie)](../introduction.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Azure Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Inleiding tot Data Factory versie 2](../introduction.md).


## <a name="what-is-azure-data-factory"></a>Wat is Azure Data Factory?
Hoe worden zakelijke gegevens in de wereld van big data hergebruikt? Is het mogelijk om gegevens die in de cloud zijn gegenereerd, waardevoller te maken met behulp van referentiegegevens uit on-premises gegevensbronnen of andere ongelijksoortige gegevensbronnen? 

We nemen een gamingbedrijf als voorbeeld. Het verzamelt logboeken die wordt geproduceerd door games in de cloud. Het bedrijf wil deze logboeken analyseren om inzicht te verkrijgen in de voorkeuren, demografische gegevens, gebruiksgedrag van klanten, enzovoort. Het wil ook kansen voor up-selling en cross-selling identificeren en nieuwe aantrekkelijke functies ontwikkelen om groei te stimuleren en klanten een betere ervaring te bieden. 

Voor het analyseren van deze logboeken moet het bedrijf gebruikmaken van de referentiegegevens, zoals klantgegevens, game-informatie en marketingcampagnegegevens, die zich in een on-premises gegevensarchief bevinden. Daarom wil het bedrijf logboekgegevens opnemen uit het gegevensarchief in de cloud en gegevens uit het on-premises gegevensarchief raadplegen. 

Vervolgens wil het de gegevens verwerken met behulp van Hadoop in de cloud (Azure HDInsight). De resulterende gegevens moeten worden gepubliceerd in een datawarehouse in de cloud, zoals Azure Synapse Analytics, of een on-premises gegevensarchief, zoals SQL Server. Het bedrijf wil dat deze werkstroom één keer per week wordt uitgevoerd. 

Het heeft een platform nodig waar het een werkstroom kan maken dat gegevens kan verwerken van zowel een on-premises gegevensarchief als een gegevensarchief in de cloud. Het bedrijf moet tevens in staat zijn gegevens te transformeren of verwerken met behulp van bestaande computerservices, zoals Hadoop. Het moet de resultaten publiceren naar een on-premises gegevensarchief of een gegevensarchief in de cloud. Deze gegevens moeten door BI-toepassingen kunnen worden gebruikt. 

![Overzicht van Data Factory](media/data-factory-introduction/what-is-azure-data-factory.png) 

Azure Data Factory is het ideale platform voor dit soort scenario's. Het is een *cloud-gebaseerde gegevensintegratieservice waarmee u gegevensgestuurde werkstromen kunt maken in de cloud, waarmee gegevensverplaatsing en -transformatie worden ingedeeld en geautomatiseerd*. Met behulp van Azure Data Factory kunt u de volgende taken uitvoeren: 

- Gegevensgestuurde werkstromen (pijplijnen genoemd) maken en plannen die gegevens uit verschillende gegevensarchieven kunnen opnemen.

- De gegevens verwerken en transformeren met behulp van rekenservices als Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics en Azure Machine Learning.

-  Uitvoergegevens publiceren naar gegevensopslaglocaties als Azure Synapse Analytics, waar BI-toepassingen (business intelligence) er gebruik van kunnen maken.  

Het is eerder een EL-platform (Extract-and-Load) en een TL-platform (Transform-and-Load) dan een traditioneel ETL-platform (Extract-Transform-and-Load). De transformaties verwerken gegevens met behulp van rekenservices in plaats van afgeleide kolommen toe te voegen, het aantal rijen te tellen, gegevens te sorteren, enzovoort. 

De gegevens die in Azure Data Factory in werkstromen worden verwerkt en geproduceerd, zijn momenteel *tijdgesegmenteerd* (per uur, per dag, per week, enzovoort). Een pijplijn kan bijvoorbeeld één keer per dag invoergegevens lezen, gegevens verwerken en uitvoer produceren. U kunt er ook voor kiezen om een werkstroom slechts één keer uit te voeren.  
  

## <a name="how-does-it-work"></a>Hoe werkt het? 
De pijplijnen (gegevensgestuurde werkstromen) in Azure Data Factory voeren normaal gesproken de volgende drie stappen uit:

![Drie fasen van Azure Data Factory](media/data-factory-introduction/three-information-production-stages.png)

### <a name="connect-and-collect"></a>Verbinding maken en verzamelen
Ondernemingen hebben verschillende typen gegevens, die worden opgeslagen op verschillende bronnen. De eerste stap bij het bouwen van een informatieproductiesysteem bestaat uit het verbinding maken met de vereiste gegevens- en verwerkingsbronnen. Deze bronnen omvatten SaaS-services, bestandsshares, FTP en webservices. Vervolgens worden de gegevens verplaatst naar een centrale locatie voor verdere verwerking.

Zonder Data Factory moeten ondernemingen aangepaste onderdelen voor gegevensverplaatsing ontwikkelen of aangepaste services schrijven om deze gegevensbronnen en verwerking te integreren. Het is kostbaar en lastig om dergelijke systemen te integreren en onderhouden. Deze systemen beschikken vaak ook niet over bewakings-, waarschuwings- en beheerfuncties op bedrijfsniveau die een volledig beheerde service kan bieden.

Met Data Factory kunt u de kopieeractiviteit in een pijplijn gebruiken om gegevens van on-premises gegevensarchieven en gegevensarchieven uit de cloud te verplaatsen naar een gecentraliseerd gegevensarchief in de cloud voor verdere analyse. 

Zo kunt u bijvoorbeeld gegevens in Azure Data Lake Store verzamelen en later transformeren met behulp van een Azure Data Lake Analytics-rekenservice. Of gegevens verzamelen in Azure Blob-opslag en later transformeren met behulp van een Hadoop-cluster van Azure HDInsight.

### <a name="transform-and-enrich"></a>Transformeren en verrijken
Als gegevens aanwezig zijn in een gecentraliseerd gegevensarchief in de cloud, kunt u ze verwerken of overbrengen met behulp van rekenservices als HDInsight Hadoop, Spark, Data Lake Analytics of Machine Learning. U wilt dat de gegevens op een betrouwbare manier en volgens een beheersbare en gecontroleerde planning worden geproduceerd om productieomgevingen te voorzien van vertrouwde gegevens. 

### <a name="publish"></a>Publiceren 
Breng de getransformeerde gegevens vanuit de cloud over naar on-premises bronnen, zoals SQL Server. U kunt ze ook in de cloudopslagbronnen bewaren, zodat ze door BI en overige analytische hulpprogramma's en toepassingen kunnen worden gebruikt.

## <a name="key-components"></a>Belangrijkste onderdelen
Een Azure-abonnement kan een of meer Azure Data Factory-exemplaren (oftewel 'data factory's') hebben. Azure Data Factory bestaat uit vier hoofdonderdelen. Deze onderdelen werken samen om een platform te bieden waarop u gegevensgestuurde werkstromen kunt maken met stappen voor de verplaatsing en transformatie van gegevens. 

### <a name="pipeline"></a>Pijplijn
Een gegevensfactory kan één of meer pijplijnen hebben. Een pijplijn is een groep activiteiten. De activiteiten in een pijplijn voeren samen een taak uit. 

Zo kan een pijplijn bijvoorbeeld een groep activiteiten bevatten die gegevens van een Azure-blob opnemen en vervolgens een Hive-query uitvoeren op een HDInsight-cluster om de gegevens te partitioneren. Het voordeel van een pijplijn is dat u de activiteiten kunt beheren als een groep in plaats van afzonderlijke activiteiten. U kunt de pijplijn bijvoorbeeld in zijn geheel implementeren en plannen, in plaats van onafhankelijke activiteiten te plannen. 

### <a name="activity"></a>Activiteit
Een pijplijn kan één of meer activiteiten bevatten. Met activiteiten definieert u welk acties moeten worden uitgevoerd voor uw gegevens. U kunt bijvoorbeeld een kopieeractiviteit gebruiken om gegevens van het ene gegevensarchief naar het andere te kopiëren. U kunt ook een Hive-activiteit gebruiken. Hiermee wordt een Hive-query uitgevoerd voor een Azure HDInsight-cluster om uw gegevens te transformeren of analyseren. Data Factory ondersteunt twee soorten activiteiten: activiteiten voor gegevensverplaatsing en activiteiten voor gegevenstransformatie.

### <a name="data-movement-activities"></a>Activiteiten voor gegevensverplaatsing
De kopieeractiviteit in Data Factory kopieert gegevens van een brongegevensarchief naar een sinkgegevensarchief. Gegevens vanuit elke willekeurige bron kunnen naar een sink worden geschreven. Selecteer een gegevensarchief voor informatie over het kopiëren van gegevens naar en van dat archief. Data Factory ondersteunt de volgende gegevensarchieven:

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Zie [Move data by using Copy Activity](data-factory-data-movement-activities.md) (Gegevens verplaatsen met kopieeractiviteit) voor meer informatie.

### <a name="data-transformation-activities"></a>Activiteiten voor gegevenstransformatie
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Zie [Move data by using Copy Activity](data-factory-data-transformation-activities.md) (Gegevens verplaatsen met kopieeractiviteit) voor meer informatie.

### <a name="custom-net-activities"></a>Aangepaste .NET-activiteiten
Maak een aangepaste .NET-activiteit als u gegevens wilt verplaatsen naar/van een gegevensarchief dat niet wordt ondersteund door de kopieeractiviteit, of gegevens wilt transformeren met behulp van uw eigen logica. Zie [Aangepaste activiteiten gebruiken in een Azure Data Factory-pijplijn](data-factory-use-custom-activities.md) voor meer informatie over het maken en gebruiken van een aangepaste activiteit.

### <a name="datasets"></a>Gegevenssets
Voor een activiteit zijn nul of meer gegevenssets nodig als invoer en één of meer gegevenssets als uitvoer. Gegevenssets vertegenwoordigen gegevensstructuren binnen de gegevensarchieven. Deze structuren wijzen of verwijzen naar de gegevens die u wilt gebruiken voor uw activiteiten (bijvoorbeeld als in- of uitvoer). 

Een Azure Blob-gegevensset benoemt bijvoorbeeld de blobcontainer en -map in de Azure Blob-opslag van waaruit de pijplijn de gegevens moet lezen. Of een Azure SQL-tabelgegevensset bevat de tabel waar de uitvoergegevens worden geschreven door de activiteit. 

### <a name="linked-services"></a>Gekoppelde services
Gekoppelde services zijn te vergelijken met verbindingsreeksen, die de verbindingsinformatie bevatten die Data Factory nodig heeft om verbinding te maken met externe bronnen. Als u het op deze manier bekijkt, vertegenwoordigt de gegevensset in feite de structuur van de gegevens en definieert de gekoppelde service de verbinding met de gegevensbron. 

Een met Azure Storage gekoppelde service biedt bijvoorbeeld een verbindingsreeks waarmee deze verbinding kan maken met het Azure Storage-account. En een Azure Blob-gegevenssets bevat de blobcontainer en de map met de gegevens.   

Gekoppelde services worden voor twee doeleinden gebruikt in Data Factory:

* Als vertegenwoordiging van een *gegevensarchief*, zoals een SQL Server-database, een Oracle-database, een bestandsshare of een Azure Blob Storage-account. Zie het gedeelte [Activiteiten voor gegevensverplaatsing](#data-movement-activities) voor een lijst met ondersteunde gegevensarchieven.

* Ter vertegenwoordiging van een *rekenresource* die de uitvoering van een activiteit kan hosten. De activiteit HDInsightHive wordt bijvoorbeeld uitgevoerd in een HDInsight Hadoop-cluster. Zie de sectie [Activiteiten voor gegevenstransformatie](#data-transformation-activities) voor een lijst met ondersteunde rekenomgevingen.

### <a name="relationship-between-data-factory-entities"></a>Relatie tussen Data Factory-entiteiten

![Diagram: Data Factory, een service voor gegevensintegratie in de cloud - Belangrijkste concepten](./media/data-factory-introduction/data-integration-service-key-concepts.png)

## <a name="supported-regions"></a>Ondersteunde regio’s
U kunt op dit moment gegevensfactory’s maken in de regio’s VS - west, VS - oost en Europa - noord. Een gegevensfactory heeft echter wel toegang tot gegevensarchieven en rekenservices in andere Azure-regio’s om gegevens te verplaatsen tussen gegevensarchieven of om gegevens te verwerken middels rekenservices.

Azure Data Factory zelf slaat geen gegevens op. Hiermee kunt u gegevensgestuurde werkstromen maken om het verplaatsen van gegevens tussen [ondersteunde gegevensarchieven](#data-movement-activities) in te delen. U kunt er ook gegevens mee verwerken met behulp van [rekenservices](#data-transformation-activities) in andere regio's of in een on-premises omgeving. U kunt er ook [werkstromen mee bewaken en beheren](data-factory-monitor-manage-pipelines.md) met zowel programmatische als gebruikersinterfacemechanismen.

Data Factory is alleen beschikbaar in de regio VS - west, VS - oost en Europa - noord. De service die de gegevensverplaatsing in Data Factory verzorgt, is echter [internationaal](data-factory-data-movement-activities.md#global) beschikbaar in meerdere regio's. Als een gegevensarchief zich achter een firewall bevindt, worden de gegevens verplaatst middels een [gegevensbeheergateway](data-factory-move-data-between-onprem-and-cloud.md) die is geïnstalleerd in uw on-premises omgeving.

Stel dat uw rekenomgevingen, bijvoorbeeld een Azure HDInsight-cluster en Azure Machine Learning, worden uitgevoerd in de regio Europa - west. U kunt een exemplaar van Azure Data Factory in Europa -noord maken en gebruiken. Vervolgens kunt u het gebruiken om taken te plannen voor uw rekenomgevingen in Europa - west. Het duurt enkele milliseconden voordat Data Factory de taak in uw rekenomgeving activeert, maar de uitvoertijd van de taak verandert niet in uw rekenomgeving.

## <a name="get-started-with-creating-a-pipeline"></a>Aan de slag met het maken van een pijplijn
U kunt een van deze hulpprogramma's of API's gebruiken om gegevenspijplijnen te maken in Azure Data Factory: 

- Visual Studio
- PowerShell
- .NET API
- REST-API
- Azure Resource Manager-sjabloon

Volg de stapsgewijze instructies in de onderstaande zelfstudies voor informatie over het bouwen van gegevensfactory’s met gegevenspijplijnen:

| Zelfstudie | Beschrijving |
| --- | --- |
| [Gegevens verplaatsen tussen twee cloudlocaties voor gegevensopslag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Maak een gegevensfactory met een pijplijn die gegevens verplaatst van blobopslag naar een SQL-database. |
| [Gegevens transformeren met een Hadoop-cluster](data-factory-build-your-first-pipeline.md) |Bouw uw eerste Azure-gegevensfactory met een gegevenspijplijn die gegevens verwerkt door een Hive-script uit te voeren op een Azure HDInsight-cluster (Hadoop). |
| [Gegevens verplaatsen tussen een on-premises gegevensopslag en een gegevensarchief in de cloud met behulp van een gegevensbeheergateway](data-factory-move-data-between-onprem-and-cloud.md) |Maak een gegevensfactory met een pijplijn die gegevens verplaatst van een SQL Server-database naar een Azure-blob. Als onderdeel van de procedure installeert en configureert u de gegevensbeheergateway op uw computer. |
