---
title: Integratie-runtime
description: Meer informatie over Integration Runtime in Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: 911674a80b531a50cfb429c5dc0ff41f1aaceb08
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389940"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration Runtime in Azure Data Factory 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

De Integratie Runtime (IR) is de rekeninfrastructuur die Azure Data Factory gebruikt om de volgende mogelijkheden voor gegevensintegratie in verschillende netwerkomgevingen te bieden:

- **Gegevens stroom**: een [gegevens stroom](concepts-data-flow-overview.md) uitvoeren in een beheerde Azure Compute-omgeving.  
- **Gegevens verplaatsing**: gegevens kopiëren tussen gegevens archieven in openbaar netwerk en gegevens archieven in een particulier netwerk (on-premises of een virtueel particulier netwerk). Deze optie biedt ondersteuning voor ingebouwde connectors, indelingsconversie, kolomtoewijzing en hoogwaardige en schaalbare gegevensoverdracht.
- **Activiteit verzending**: trans formatie-activiteiten voor verzen ding verzenden en bewaken die worden uitgevoerd op diverse reken Services, zoals Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server en meer.
- **SSIS-pakketuitvoering**: systeemeigen SSIS-pakketten (SQL Server Integration Services) uitvoeren in een beheerde Azure-rekenomgeving.

In de Data Factory definieert een activiteit de actie die moet worden uitgevoerd. Een gekoppelde service definieert een doelgegevensarchief of een rekenservice. Een Integration Runtime vormt de brug tussen de activiteit en de gekoppelde services.  Er wordt naar verwezen door de gekoppelde service of activiteit, en biedt de reken omgeving waar de activiteit wordt uitgevoerd of verzonden. Op deze manier kan de activiteit optimaal worden uitgevoerd in de regio die het dichtst mogelijk bij het doelgegevensarchief of de rekenservice ligt, terwijl wordt voldaan aan vereisten rondom beveiliging en naleving.

Integration Runtimes kunnen worden gemaakt in de Azure Data Factory UX via de [beheer hub](author-management-hub.md) en alle activiteiten, gegevens sets of data stromen die ernaar verwijzen.

## <a name="integration-runtime-types"></a>Typen Integration Runtime

Data Factory biedt drie typen Integration Runtime (IR) en u moet het type kiezen dat het beste past bij de mogelijkheden voor gegevens integratie en de netwerk omgeving die u zoekt.  Deze drie typen zijn:

- Azure
- Zelf-hostend
- Azure-SSIS

De volgende tabel beschrijft de mogelijkheden en de netwerkondersteuning voor de drie typen Integration Runtime:

IR-type | Openbaar netwerk | Particulier netwerk
------- | -------------- | ---------------
Azure | Gegevensstroom<br/>Gegevensverplaatsing<br/>Verzending van de activiteit | Gegevensstroom<br/>Gegevensverplaatsing<br/>Verzending van de activiteit
Zelf-hostend | Gegevensverplaatsing<br/>Verzending van de activiteit | Gegevensverplaatsing<br/>Verzending van de activiteit
Azure-SSIS | Uitvoering van SSIS-pakket | Uitvoering van SSIS-pakket


## <a name="azure-integration-runtime"></a>Azure Integration Runtime

Een Azure Integration runtime kan:

- Gegevens stromen uitvoeren in azure 
- Kopieer activiteit uit te voeren tussen Cloud gegevens archieven
- Het verzenden van de volgende transformatie activiteiten in het open bare netwerk: Databricks notebook/jar/python activity, hdinsight component activity, hdinsight varken-activiteit, hdinsight MapReduce activity, hdinsight Spark-activiteit, hdinsight streaming activity, Azure machine learning Studio (klassiek) batch Execution-activiteit, Azure machine learning Studio (klassiek) activiteiten voor het uitvoeren van taken, de opgeslagen procedure activiteit, data Lake Analytics U-SQL-activiteit, .net aangepaste activiteit,

### <a name="azure-ir-network-environment"></a>Azure IR-netwerkomgeving

Azure Integration Runtime ondersteunt het verbinden met data stores en reken Services met open bare eind punten. Het inschakelen van beheerde Virtual Network, Azure Integration Runtime ondersteunt het verbinding maken met gegevens archieven met behulp van een persoonlijke koppelings service in een particuliere netwerk omgeving.

### <a name="azure-ir-compute-resource-and-scaling"></a>Azure IR-rekenresource en -schalen
Azure Integration Runtime biedt een volledig beheerde, serverloze rekenresource in Azure.  U hoeft zich geen zorgen te maken over het inrichten van de infra structuur, het installeren van software, patches of het schalen van de capaciteit.  Bovendien betaalt u alleen voor het werkelijke gebruik.

Azure Integration Runtime biedt de systeemeigen rekenkracht om gegevens te verplaatsen tussen gegevensarchieven in de cloud op een veilige, betrouwbare en krachtige manier.  U kunt instellen hoeveel eenheden voor gegevensintegratie worden gebruikt in de kopieeractiviteit. De rekenkracht van de Azure IR wordt flexibel opgeschaald om aan uw behoeften te voldoen, zonder dat u de grootte van de Azure Integration Runtime expliciet hoeft aan te passen. 

Activiteiten verzen ding is een licht gewicht bewerking voor het routeren van de activiteit naar de doel compute-service, waardoor de berekenings grootte voor dit scenario niet hoeft te worden geschaald.

Zie [Azure Integration runtime maken en configureren](create-azure-integration-runtime.md)voor meer informatie over het maken en configureren van een Azure IR. 

> [!NOTE] 
> Azure Integration runtime heeft eigenschappen die betrekking hebben op Data flow runtime, waarmee de onderliggende Compute-infra structuur wordt gedefinieerd die wordt gebruikt om de gegevens stromen uit te voeren. 

## <a name="self-hosted-integration-runtime"></a>Zelf-hostende Integration Runtime

Een zelf-hostende IR is geschikt voor:

- Het uitvoeren van kopieeractiviteit tussen een gegevensarchief in de cloud en een gegevensarchief in een privénetwerk.
- Het verzenden van de volgende trans formatie activiteiten op basis van reken resources in on-premises of Azure Virtual Network: HDInsight Hive-activiteit (BYOC: uw eigen cluster meenemen), HDInsight Pig-activiteit (BYOC), HDInsight MapReduce activity (BYOC), HDInsight Spark-activiteit (BYOC), hdinsight streaming activity (BYOC), Azure machine learning Studio (klassiek) uitvoering van activiteiten, data Lake Analytics U-SQL-activiteit Azure machine learning Studio , Aangepaste activiteit (wordt uitgevoerd op Azure Batch), opzoek activiteit en ophalen van meta gegevens.

> [!NOTE] 
> Gebruik zelf-hostende Integration runtime voor de ondersteuning van gegevens archieven waarvoor u een eigen stuur programma nodig hebt, zoals SAP Hana, MySQL, enzovoort.  Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor meer informatie.

> [!NOTE] 
> Java Runtime Environment (JRE) is een afhankelijkheid van zelf-Hostende IR. Zorg ervoor dat JRE is geïnstalleerd op dezelfde host.

### <a name="self-hosted-ir-network-environment"></a>Zelf-hostende Azure IR-netwerkomgeving

Als u gegevens integratie veilig wilt uitvoeren in een particuliere netwerk omgeving, die geen directe regel van het zicht heeft vanuit de open bare cloud omgeving, kunt u een zelf-hostende IR-omgeving installeren achter de firewall van uw bedrijf of in een virtueel particulier netwerk.  De zelf-hostende Integration Runtime maakt alleen uitgaande HTTP-gebaseerde verbindingen met het openbare internet.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Zelf-hostende IR-rekenresource en -schalen

Installeer zelf-hostende IR op een on-premises computer of een virtuele machine in een particulier netwerk. Op dit moment ondersteunen we alleen zelf-hostende IR op een Windows-besturingssysteem.  

Voor hoge beschikbaarheid en schaalbaarheid kunt u de zelf-hostende IR uitbreiden door het logische exemplaar te koppelen aan meerdere on-premises computers in de modus actief-actief.  Zie voor meer informatie het [maken en configureren van een zelf-hostend IR](create-self-hosted-integration-runtime.md) -artikel onder How to guides for details.

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime

Als u de bestaande SSIS-werkbelasting wilt opheffen of verplaatsen, kunt u een Azure-SSIS IR maken voor het uitvoeren van systeemeigen SSIS-pakketten.

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS IR-netwerkomgeving

Azure-SSIS IR kan worden ingericht in een openbaar netwerk of privénetwerk.  Toegang tot on-premises gegevens wordt ondersteund door Azure SSIS IR te koppelen aan een virtueel netwerk dat is verbonden met uw on-premises netwerk.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Azure-SSIS IR-rekenresource en -schalen

Azure-SSIS IR is een volledig beheerd cluster met virtuele Azure-machines die uw SSIS-pakketten uitvoeren. U kunt uw eigen Azure SQL Database of SQL Managed instance meenemen voor de catalogus van SSIS-projecten/-pakketten (SSISDB). U kunt de rekenkracht opschalen door de grootte van het knooppunt op te geven en opschalen door het aantal knooppunten in het cluster aan te geven. U kunt de kosten van het uitvoeren van de uw Azure-SSIS Integration Runtime beheren door naar wens te stoppen en starten.

Zie voor meer informatie het artikel 'Azure-SSIS IR maken en configureren' bij de gidsen.  Wanneer u de Azure SSIS IR hebt gemaakt, kunt u uw bestaande SSIS-pakketten implementeren en beheren met weinig of geen wijzigingen met behulp van bekende hulpprogramma's zoals SQL Server Data Tools (SSDT) en SQL Server Management Studio (SSMS), net als bij on-premises gebruik van SSIS.

Zie de volgende artikelen voor meer informatie over Azure-SSIS Runtime: 

- [Zelfstudie: SSIS-pakketten implementeren in Azure](./tutorial-deploy-ssis-packages-azure.md). In dit artikel vindt u stapsgewijze instructies voor het maken van een Azure-SSIS IR en het gebruik van een Azure SQL Database voor het hosten van de SSIS-catalogus. 
- [Procedure: Een Azure SSIS Integration Runtime maken](create-azure-ssis-integration-runtime.md). In dit artikel wordt de zelf studie uitgebreid en vindt u instructies voor het gebruik van SQL Managed instance en het toevoegen van de IR aan een virtueel netwerk. 
- [Een Azure-SSIS IR controleren](monitor-integration-runtime.md#azure-ssis-integration-runtime). In dit artikel leest u hoe u informatie over een Azure-SSIS IR ophaalt. Daarnaast bevat het artikel beschrijvingen van statuswaarden die worden gebruikt in de geretourneerde informatie. 
- [Een Azure-SSIS IR beheren](manage-azure-ssis-integration-runtime.md). In dit artikel leest u hoe u een Azure-SSIS IR stopt, start of verwijdert. Er wordt ook uitgelegd hoe u een Azure-SSIS IR kunt uitschalen door meer knooppunten toe te voegen aan de IR. 
- [Een Azure-SSIS-integratieruntime toevoegen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md). Dit artikel bevat algemene informatie over het toevoegen van een Azure-SSIS IR aan een virtueel netwerk van Azure. Er wordt ook beschreven hoe u Azure Portal gebruikt om een virtueel netwerk te configureren voor het deelnemen van Azure-SSIS IR aan het virtueel netwerk. 

## <a name="integration-runtime-location"></a>Locatie van Integration Runtime

### <a name="relationship-between-factory-location-and-ir-location"></a>Relatie tussen fabrieks locatie en IR-locatie

Wanneer een klant een data factory-exemplaar maakt, moet deze de locatie voor de data factory opgeven. De Data Factory-locatie is waar de metagegevens van de data factory worden opgeslagen en waar van het activeren van de pijplijn wordt gestart. Meta gegevens voor de Factory worden alleen opgeslagen in de regio van de keuze van de klant en worden niet opgeslagen in andere regio's.

Ondertussen heeft een data factory wel toegang tot gegevensarchieven en Compute Services in andere Azure-regio’s om gegevens te verplaatsen tussen gegevensarchieven of om gegevens te verwerken middels Compute Services. Dit gedrag wordt gerealiseerd via de [IR die algemeen beschikbaar](https://azure.microsoft.com/global-infrastructure/services/) is om de gegevensnaleving, efficiëntie en verminderde kosten voor uitgaand netwerkverkeer te realiseren.

De locatie van de IR definieert de locatie van de back-end rekenkracht en in wezen de locatie waar de verplaatsing van gegevens, het verzenden van activiteit en de uitvoering van het SSIS-pakket worden uitgevoerd. De locatie van de IR kan afwijken van de locatie van de data factory waar hij bij hoort. 

### <a name="azure-ir-location"></a>Locatie van Azure IR

U kunt een bepaalde locatie van een Azure IR instellen, in welk geval de uitvoering van de activiteit of verzen ding plaatsvindt in die specifieke regio.

Als u ervoor kiest om de Azure IR automatisch oplossen te gebruiken in het open bare netwerk, is dit de standaard instelling.

- Voor kopieer activiteiten maakt ADF een beste manier om de locatie van uw Sink-gegevens opslag automatisch te detecteren. vervolgens gebruikt u de IR in dezelfde regio, indien beschikbaar of het dichtstbijzijnde deel van hetzelfde Geografie; Als de regio van de Sink-gegevens opslag niet kan worden gedetecteerd, wordt IR in de data factory regio als alternatief gebruikt.

  Stel dat u uw fabriek hebt gemaakt in VS-Oost, 
  
  - Als u gegevens naar Azure-Blob in West-VS kopieert en als ADF is gedetecteerd dat de BLOB zich in VS West bevindt, wordt de Kopieer activiteit uitgevoerd op IR in VS-West. Als de detectie van de regio mislukt, wordt de Kopieer activiteit uitgevoerd op IR in VS-Oost.
  - Wanneer u gegevens kopieert naar Sales Force waarvan de regio niet kan worden gedetecteerd, wordt de Kopieer activiteit uitgevoerd op IR in VS-Oost.

  >[!TIP] 
  >Als u strikte gegevensnalevingsvereisten hebt en u ervoor moet zorgen moet dat gegevens een bepaalde geografie niet verlaten, kunt u expliciet een Azure IR maken in een bepaalde regio en de gekoppelde service naar deze IR laten wijzen met behulp van de eigenschap ConnectVia. Als u bijvoorbeeld gegevens wilt kopiëren van BLOB in UK-zuid naar Azure Synapse Analytics in UK-zuid en wilt ervoor zorgen dat gegevens niet in het VK worden gelaten, maakt u een Azure IR in UK-zuid en koppelt u beide gekoppelde services aan deze IR.

- Voor het uitvoeren van opzoek-GetMetadata/het verwijderen van de activiteit (ook wel pijplijn activiteiten genoemd), het verzenden van trans formatie-activiteiten (ook wel externe activiteiten genoemd) en het maken van bewerkingen (verbinding testen, bladeren in mappen lijst en tabel lijst, preview-gegevens), ADF gebruikt de IR in de regio data factory.

- Voor de gegevens stroom maakt ADF gebruik van de IR in de regio data factory. 

  > [!TIP] 
  > Het is een goed idee om ervoor te zorgen dat de gegevens stroom wordt uitgevoerd in dezelfde regio als de bijbehorende gegevens archieven (indien mogelijk). U kunt dit doen door Azure IR automatisch op te lossen (als de locatie van de gegevens opslag hetzelfde is als Data Factory locatie) of door een nieuw Azure IR exemplaar te maken in dezelfde regio als uw gegevens archieven en vervolgens de gegevens stroom op het bestand uit te voeren. 

Als u beheerde Virtual Network voor het automatisch oplossen van Azure IR inschakelt, gebruikt ADF de IR in de data factory regio. 

U kunt controleren welke IR-locatie van kracht wordt tijdens het uitvoeren van activiteiten in de weergave voor het controleren van de pijplijnactiviteit in de gebruikersinterface of nettolading voor het controleren van activiteiten.

### <a name="self-hosted-ir-location"></a>Locatie zelf-hostende IR

De zelf-hostende IR is logisch geregistreerd bij de Data Factory en de rekenkracht die wordt gebruikt ter ondersteuning van de functionaliteiten die u hebt opgegeven. Er bestaat daarom geen expliciete locatie-eigenschap voor de zelf-hostende IR. 

Wanneer de zelf-hostende IR wordt gebruikt voor het uitvoeren van de gegevensverplaatsing, haalt deze gegevens uit de bron en schrijft naar de bestemming.

### <a name="azure-ssis-ir-location"></a>Locatie Azure-SSIS IR

Het selecteren van de juiste locatie voor uw Azure-SSIS IR is essentieel voor het bereiken van hoge prestaties in uw ETL-werkstromen (extract-transform-load).

- De locatie van uw Azure-SSIS IR hoeft niet hetzelfde te zijn als de locatie van uw data factory, maar moet hetzelfde zijn als de locatie van uw eigen Azure SQL Database of SQL Managed instance waarbij SSISDB. Op deze manier heeft uw Azure-SSIS Integration Runtime eenvoudig toegang tot SSISDB, zonder overmatig verkeer tussen verschillende locaties.
- Als u geen bestaande SQL Database of SQL Managed instance hebt, maar u on-premises gegevens bronnen/-bestemmingen hebt, moet u een nieuw Azure SQL Database of SQL Managed instance maken op dezelfde locatie als een virtueel netwerk dat is verbonden met uw on-premises netwerk.  Op deze manier kunt u uw Azure-SSIS IR maken met behulp van het nieuwe Azure SQL Database of SQL Managed instance en dat virtuele netwerk samen voegen, allemaal op dezelfde locatie, waardoor gegevens verkeer tussen verschillende locaties effectief wordt geminimaliseerd.
- Als de locatie van uw bestaande Azure SQL Database of SQL Managed instance niet hetzelfde is als de locatie van een virtueel netwerk dat is verbonden met uw on-premises netwerk, maakt u uw Azure-SSIS IR eerst met behulp van een bestaande Azure SQL Database of SQL Managed instance en verbindt u een virtueel netwerk op dezelfde locatie. vervolgens configureert u een virtueel netwerk naar een virtueel netwerk tussen verschillende locaties.

Het volgende diagram toont de locatie-instellingen van Data Factory en het aantal keren dat de integratie wordt uitgevoerd:

![Locatie van Integration Runtime](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Bepalen welke IR u moet gebruiken

### <a name="copy-activity"></a>Kopieeractiviteit

Voor de kopieeractiviteit, zijn de gekoppelde bron- en sinkservices vereist voor het definiëren van de richting van de gegevensstroom. De volgende logica wordt gebruikt om te bepalen welk exemplaar van Integration Runtime wordt gebruikt voor het uitvoeren van de kopieeractiviteit: 

- **Kopiëren tussen twee gegevens bronnen** in de Cloud: wanneer de gekoppelde services van de bron-en Sink-service gebruikmaken van Azure IR, gebruikt ADF de regionale Azure IR als u hebt opgegeven, of automatisch een locatie van Azure IR bepalen als u de optie IR (standaard) voor de [locatie van Integration runtime](#integration-runtime-location) kiest.
- **Kopiëren tussen een gegevensbron in de cloud en een gegevensbron in een privénetwerk**: als de gekoppelde bron- of sinkservice verwijst naar een zelf-hostende IR, wordt de kopieerbewerking uitgevoerd op die zelf-hostende Integration Runtime.
- **Kopiëren tussen twee gegevens bronnen in een particulier netwerk**: zowel de gekoppelde bron-als Sink-service moeten verwijzen naar hetzelfde exemplaar van Integration runtime en die Integration runtime wordt gebruikt om de Kopieer activiteit uit te voeren.

### <a name="lookup-and-getmetadata-activity"></a>Activiteit Lookup en GetMetadata

De activiteit Lookup en GetMetadata wordt uitgevoerd voor de integratieruntime die is gekoppeld aan de service die aan de gegevensopslag is gekoppeld.

### <a name="external-transformation-activity"></a>Externe trans formatie-activiteit

Elke externe transformatie activiteit die gebruikmaakt van een externe Compute-engine heeft een gekoppelde doel compute-service, die verwijst naar een Integration runtime. Dit exemplaar van Integration runtime bepaalt de locatie waar de externe hand code van de transformatie activiteit wordt verzonden.

### <a name="data-flow-activity"></a>Activiteit gegevens stroom

Gegevens stroom activiteiten worden uitgevoerd op de Azure Integration runtime die eraan is gekoppeld. De mousserende Compute die wordt gebruikt door gegevens stromen, wordt bepaald door de eigenschappen van de gegevens stroom in uw Azure Integration Runtime en worden volledig beheerd door ADF.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen:

- [Azure Integration runtime maken](create-azure-integration-runtime.md)
- [Een zelf-hostende integratieruntime maken](create-self-hosted-integration-runtime.md)
- [Maak een Azure SSIS Integration runtime](create-azure-ssis-integration-runtime.md). In dit artikel wordt de zelf studie uitgebreid en vindt u instructies voor het gebruik van SQL Managed instance en het toevoegen van de IR aan een virtueel netwerk.