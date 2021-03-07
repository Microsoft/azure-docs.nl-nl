---
title: Door Voer in te richten op Azure Cosmos-containers en-data bases
description: Meer informatie over het instellen van ingerichte door Voer voor uw Azure Cosmos-containers en-data bases.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/25/2021
ms.openlocfilehash: 1d5fba12ce9b1ce8d30c59a08aa36e1222abe3c7
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102433927"
---
# <a name="introduction-to-provisioned-throughput-in-azure-cosmos-db"></a>Inleiding tot ingerichte door Voer in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Met Azure Cosmos DB kunt u een ingerichte door Voer instellen voor uw data bases en containers. Er zijn twee soorten ingerichte door Voer, standaard (hand matig) of automatisch schalen. Dit artikel geeft een overzicht van de manier waarop de ingerichte door Voer werkt. 

Een Azure Cosmos-database is een beheereenheid voor een set containers. Een database bestaat uit een set schema-agnostische containers. Een Azure Cosmos-container is de eenheid van schaalbaarheid voor zowel de doorvoer als de opslag. Een container is horizontaal gepartitioneerd over een set machines binnen een Azure-regio, en wordt gedistribueerd in alle Azure-regio's die zijn gekoppeld aan uw Azure Cosmos-account.

Met Azure Cosmos DB kunt u de door Voer op twee nauw keurigheid inrichten:
 
- Azure Cosmos-containers
- Azure Cosmos-databases

## <a name="set-throughput-on-a-container"></a>Door Voer voor een container instellen  

De door Voer die is ingericht voor een Azure Cosmos-container is exclusief gereserveerd voor die container. De container ontvangt de ingerichte door Voer altijd. De ingerichte door Voer voor een container wordt financieel ondersteund door service overeenkomsten. Zie de [door Voer inrichten op een Azure Cosmos-container](how-to-provision-container-throughput.md)voor meer informatie over het configureren van de standaard doorvoer (hand matig) voor een container. Zie automatisch [schalen door Voer inrichten](how-to-provision-autoscale-throughput.md)voor meer informatie over het configureren van de door Voer van automatisch schalen op een container.

Het instellen van een ingerichte door Voer voor een container is de meest gebruikte optie. U kunt de door Voer voor een container elastisch schalen door een wille keurige hoeveelheid door Voer in te stellen met behulp van [aanvraag eenheden (RUs)](request-units.md). 

De door Voer die is ingericht voor een container wordt gelijkmatig verdeeld over de fysieke partities en er wordt ervan uitgegaan dat er een goede partitie sleutel is die de logische partities gelijkmatig verdeelt tussen de fysieke partities. de door Voer wordt ook gelijkmatig verdeeld over alle logische partities van de container. U kunt de door Voer voor logische partities niet selectief opgeven. Omdat een of meer logische partities van een container worden gehost door een fysieke partitie, behoren de fysieke partities uitsluitend tot de container en ondersteunen ze de door Voer ingericht op de container. 

Als de werk belasting die wordt uitgevoerd op een logische partitie, groter is dan de door Voer die is toegewezen aan de onderliggende fysieke partitie, is het mogelijk dat uw bewerkingen een beperkt aantal zijn. Wat bekend staat als een _hete partitie_ , treedt op wanneer één logische partitie onevenredig meer aanvragen heeft dan andere partitie sleutel waarden.

Wanneer de snelheids beperking optreedt, kunt u de ingerichte door Voer voor de gehele container verhogen of de bewerking opnieuw uitvoeren. U moet er ook voor zorgen dat u een partitie sleutel kiest waarmee de opslag en het aanvraag volume gelijkmatig worden verdeeld. Zie [partitioneren en horizon taal schalen in azure Cosmos DB](partitioning-overview.md)voor meer informatie over partitioneren.

We raden u aan om door voer te configureren bij de container granulatie wanneer u voorspel bare prestaties voor de container wilt.

In de volgende afbeelding ziet u hoe een fysieke partitie als host fungeert voor een of meer logische partities van een container:

:::image type="content" source="./media/set-throughput/resource-partition.png" alt-text="Fysieke partitie die als host fungeert voor een of meer logische partities van een container" border="false":::

## <a name="set-throughput-on-a-database"></a>Doorvoer instellen voor een database

Wanneer u de door Voer inricht in een Azure Cosmos-data base, wordt de door Voer gedeeld via alle containers (shared data base-containers genoemd) in de-data base. Een uitzondering hierop is als u ingerichte doorvoer hebt opgegeven voor specifieke containers in de database. Het delen van de ingerichte door Voer op database niveau tussen de containers is vergelijkbaar met het hosten van een Data Base op een cluster machines. Omdat alle containers in een Data Base de resources delen die beschikbaar zijn op een computer, kunt u natuurlijk geen voorspel bare prestaties op een specifieke container krijgen. Zie [ingerichte door Voer configureren voor een Azure Cosmos-data base](how-to-provision-database-throughput.md)voor meer informatie over het configureren van een ingerichte door Voer voor een Data Base. Zie automatisch [schalen door Voer inrichten](how-to-provision-autoscale-throughput.md)voor meer informatie over het configureren van de door Voer van automatisch schalen op een Data Base.

Omdat alle containers in de data base de ingerichte door voer hebben gedeeld, biedt Azure Cosmos DB geen voorspel bare doorvoer garanties voor een bepaalde container in die data base. Het gedeelte van de door voer dat een specifieke container kan ontvangen, is afhankelijk van het volgende:

* Het aantal containers.
* De keuze van partitie sleutels voor verschillende containers.
* De verdeling van de werk belasting over verschillende logische partities van de containers. 

We raden u aan de door Voer voor een Data Base te configureren als u de door voer wilt delen in meerdere containers, maar niet de door voer wilt toewijzen aan een bepaalde container. 

De volgende voor beelden laten zien waar het voor keur is om de door Voer in te richten op het niveau van de Data Base:

* Het delen van de ingerichte door Voer van een data base in een set containers is handig voor een multi tenant-toepassing. Elke gebruiker kan worden vertegenwoordigd door een afzonderlijke Azure Cosmos-container.

* Het delen van de ingerichte door Voer van een data base in een set met containers is handig wanneer u een NoSQL-data base, zoals MongoDB of Cassandra, migreert die wordt gehost op een cluster van virtuele machines of van on-premises fysieke servers naar Azure Cosmos DB. Denk na over de ingerichte door Voer die op uw Azure Cosmos-data base is geconfigureerd als een logische equivalent, maar rendabeler en elastisch, tot die van de reken capaciteit van uw MongoDB-of Cassandra-cluster.  

Alle containers die in een Data Base zijn gemaakt met een ingerichte door Voer, moeten worden gemaakt met een [partitie sleutel](partitioning-overview.md). Op een bepaald moment wordt de door Voer toegewezen aan een container in een Data Base verdeeld over alle logische partities van die container. Wanneer u containers hebt waarin de ingerichte door Voer is geconfigureerd voor een Data Base, kunt u de door Voer niet selectief Toep assen op een specifieke container of een logische partitie. 

Als de werk belasting op een logische partitie meer gebruikt dan de door Voer die is toegewezen aan een specifieke logische partitie, zijn uw bewerkingen een beperkt aantal. Wanneer de snelheids beperking optreedt, kunt u de door Voer voor de gehele data base verg Roten of de bewerkingen opnieuw proberen. Zie [logische partities](partitioning-overview.md)voor meer informatie over partitioneren.

Containers in een gedeelde doorvoerdatabase delen de doorvoer (RU/s) die zijn toegewezen aan deze database. Met Standard (hand matig) ingerichte door Voer, kunt u Maxi maal 25 containers hebben met mini maal 400 RU/s op de data base. Met de ingerichte door Voer voor automatisch schalen kunt u Maxi maal 25 containers in een Data Base hebben met automatisch schalen Max. 4000 RU/s (schalen tussen 400-4000 RU/s).

> [!NOTE]
> In februari 2020 hebben we een wijziging geïntroduceerd waarmee u Maxi maal 25 containers in een gedeelde doorvoer database kunt hebben, waardoor het delen van de door Voer via de containers beter wordt. Na de eerste 25 containers kunt u meer containers toevoegen aan de Data Base als ze zijn [ingericht met een specifieke door Voer](#set-throughput-on-a-database-and-a-container), die los is van de gedeelde door Voer van de data base.<br>
Als uw Azure Cosmos DB-account al een gedeelde doorvoerdatabase bevat met minimaal 25 containers, zijn het account en alle overige accounts in hetzelfde Azure-abonnement uitgesloten van deze wijziging. [Neem contact op met product ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) als u feedback of vragen hebt. 

Als uw workloads het verwijderen en opnieuw maken van alle verzamelingen in een Data Base vereisen, is het raadzaam om de lege data base weg te halen en opnieuw een nieuwe Data Base te maken voordat u de verzameling maakt. In de volgende afbeelding ziet u hoe een fysieke partitie een of meer logische partities kan hosten die deel uitmaken van verschillende containers in een Data Base:

:::image type="content" source="./media/set-throughput/resource-partition2.png" alt-text="Fysieke partitie die als host fungeert voor een of meer logische partities die deel uitmaken van verschillende containers " border="false":::

## <a name="set-throughput-on-a-database-and-a-container"></a>Door Voer instellen voor een Data Base en een container

U kunt de twee modellen combi neren. De door Voer voor de data base en de container inrichten is toegestaan. In het volgende voor beeld ziet u hoe u standaard (hand matige) ingerichte door Voer kunt inrichten voor een Azure Cosmos-data base en een container:

* U kunt een Azure Cosmos-data base met de naam *Z* maken met de standaard (hand matig) ingerichte door Voer van *"K"* RUs. 
* Maak vervolgens vijf containers met de naam *A*, *B*, *C*, *D* en *E* in de-data base. Zorg ervoor dat u bij het maken van container B een **specifieke door Voer inrichten voor deze container** optie inschakelt en expliciet *' P '* RUs van ingerichte door Voer voor deze container configureert. U kunt gedeelde en toegewezen door Voer alleen configureren bij het maken van de data base en container. 

   :::image type="content" source="./media/set-throughput/coll-level-throughput.png" alt-text="De door Voer op container niveau instellen":::

* De *"K"* RUs-door Voer wordt gedeeld in de vier containers *A*, *C*, *D* en *E*. De exacte hoeveelheid door Voer die beschikbaar is voor *A*, *C*, *D* of *E* , varieert. Er zijn geen service overeenkomsten voor de door Voer van elke afzonderlijke container.
* De container met de naam *B* is gegarandeerd dat de *"P"* RUs-door Voer altijd wordt opgehaald. Er wordt een back-up gemaakt van service overeenkomsten.

> [!NOTE]
> Een container met ingerichte door Voer kan niet worden geconverteerd naar een gedeelde database container. Een gedeelde database container kan niet worden geconverteerd naar een specifieke door voer.

## <a name="update-throughput-on-a-database-or-a-container"></a>De door Voer van een Data Base of container bijwerken

Nadat u een Azure Cosmos-container of een-Data Base hebt gemaakt, kunt u de ingerichte door Voer bijwerken. Er is geen limiet voor de Maxi maal ingerichte door Voer die u kunt configureren voor de data base of de container.

### <a name="current-provisioned-throughput"></a><a id="current-provisioned-throughput"></a> Huidige ingerichte door Voer

U kunt de ingerichte door Voer van een container of een Data Base ophalen in de Azure Portal of door gebruik te maken van de Sdk's:

* [Container. ReadThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.readthroughputasync) in de .NET SDK.
* [CosmosContainer. readThroughput](/java/api/com.azure.cosmos.cosmosasynccontainer.readthroughput) op de Java-SDK.

Het antwoord van deze methoden bevat ook de [minimale ingerichte door Voer](concepts-limits.md#storage-and-database-operations) voor de container of Data Base:

* [ThroughputResponse. MinThroughput](/dotnet/api/microsoft.azure.cosmos.throughputresponse.minthroughput) in de .NET SDK.
* [ThroughputResponse. getMinThroughput ()](/java/api/com.azure.cosmos.models.throughputresponse.getminthroughput) in de Java-SDK.

De werkelijke minimale RU/s kan variëren, afhankelijk van de configuratie van uw account. Maar in het algemeen is het het maximum van:

* 400 RU/s 
* Huidige opslag in GB * 10 RU/s (deze beperking kan in sommige gevallen ongeforceerd worden weer geven in het [programma voor hoge opslag/laag door Voer](#high-storage-low-throughput-program))
* De hoogste RU/s die zijn ingericht voor de data base of container/100

### <a name="changing-the-provisioned-throughput"></a>De ingerichte door Voer wijzigen

U kunt de ingerichte door Voer van een container of een Data Base schalen via de Azure Portal of door gebruik te maken van de Sdk's:

* [Container. ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync) in de .NET SDK.
* [CosmosContainer. replaceThroughput](/java/api/com.azure.cosmos.cosmosasynccontainer.replacethroughput) op de Java-SDK.

Als u **de ingerichte door Voer verkort**, kunt u dit doen tot het [minimum](#current-provisioned-throughput).

Als u **de ingerichte door Voer toeneemt**, wordt de bewerking meestal onmiddellijk uitgevoerd. Er zijn echter gevallen waarin de bewerking langer kan duren vanwege de systeem taken om de vereiste bronnen in te richten. In dit geval wordt een poging om de ingerichte door voer te wijzigen terwijl deze bewerking wordt uitgevoerd, een HTTP 423-antwoord met een fout bericht weer gegeven waarin wordt uitgelegd dat er nog een andere schaal bewerking wordt uitgevoerd.

> [!NOTE]
> Als u van plan bent een zeer grote opname werk belasting te maken die een grote toename van de ingerichte door Voer vereist, moet u er rekening mee houden dat de schaal bewerking geen SLA heeft en, zoals vermeld in de vorige alinea, het lang kan duren wanneer de toename groot is. U kunt het beste plannen voordat de werk belasting begint en de volgende methoden gebruiken om de voortgang te controleren.

U kunt de voortgang van het schalen programmatisch controleren door de [huidige ingerichte door Voer](#current-provisioned-throughput) te lezen en te gebruiken:

* [ThroughputResponse. IsReplacePending](/dotnet/api/microsoft.azure.cosmos.throughputresponse.isreplacepending) in de .NET SDK.
* [ThroughputResponse. isReplacePending ()](/java/api/com.azure.cosmos.models.throughputresponse.isreplacepending) in de Java-SDK.

U kunt [Azure monitor metrische gegevens](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) gebruiken om de geschiedenis van ingerichte door Voer (ru/s) en opslag voor een bron weer te geven.

## <a name="high-storage--low-throughput-program"></a><a id="high-storage-low-throughput-program"></a> Programma voor hoge opslag/laag door Voer

Zoals beschreven in de sectie [huidige ingerichte door Voer](#current-provisioned-throughput) , de minimale door Voer die u kunt inrichten voor een container of Data Base is afhankelijk van een aantal factoren. Een van beide is de hoeveelheid gegevens die momenteel is opgeslagen, omdat Azure Cosmos DB een minimale door Voer van 10 RU/s per GB opslag afdwingt.

Dit kan een probleem zijn in situaties waarin u grote hoeveel heden gegevens moet opslaan, maar een lage doorvoer vereisten in vergelijking hebt. Voor een beter begrip van deze scenario's heeft Azure Cosmos DB een **' hoge opslag/lage door Voer '** geïntroduceerd waarmee de beperking van ru/s per GB voor in aanmerking komende accounts wordt verlaagd.

Als u wilt meedoen aan dit programma en uw volledige geschiktheid wilt beoordelen, moet u dit doen door [deze enquête](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRzBPrdEMjvxPuDm8fCLUtXpUREdDU0pCR0lVVFY5T1lRVEhWNUZITUJGMC4u)in te vullen. Het Azure Cosmos DB-team wordt vervolgens opvolgd en gaat door met uw onboarding.

## <a name="comparison-of-models"></a>Vergelijking van modellen
In deze tabel ziet u een vergelijking tussen het inrichten van de standaard doorvoer (hand matig) voor een Data Base versus een container. 

|**Parameter**  |**Standaard doorvoer (hand matig) voor een Data Base**  |**Standaard (hand matig) door Voer voor een container**|**Door Voer automatisch schalen voor een Data Base** | **Door Voer automatisch schalen op een container**|
|---------|---------|---------|---------|---------|
|Ingangs punt (mini maal RU/s) |400 RU/s. Kan Maxi maal 25 containers bevatten zonder het minimum aantal RU/s per container.</li> |400| Automatisch schalen tussen 400-4000 RU/s. Kan Maxi maal 25 containers bevatten zonder het minimum aantal RU/s per container.</li> | Automatisch schalen tussen 400-4000 RU/s.|
|Mini maal RU/s per container|--|400|--|Automatisch schalen tussen 400-4000 RU/s|
|Maximum RUs|Onbeperkt, op de data base.|Onbeperkt, op de container.|Onbeperkt, op de data base.|Onbeperkt, op de container.
|RUs toegewezen of beschikbaar voor een specifieke container|Geen garanties. RUs toegewezen aan een bepaalde container is afhankelijk van de eigenschappen. Eigenschappen kunnen de keuze zijn van partitie sleutels van containers die de door Voer, de distributie van de werk belasting en het aantal containers delen. |Alle RUs-instellingen die in de container zijn geconfigureerd, worden uitsluitend gereserveerd voor de container.|Geen garanties. RUs toegewezen aan een bepaalde container is afhankelijk van de eigenschappen. Eigenschappen kunnen de keuze zijn van partitie sleutels van containers die de door Voer, de distributie van de werk belasting en het aantal containers delen. |Alle RUs-instellingen die in de container zijn geconfigureerd, worden uitsluitend gereserveerd voor de container.|
|Maximale opslag voor een container|Onbeperkt.|Onbeperkt|Onbeperkt|Onbeperkt|
|Maximale door Voer per logische partitie van een container|10.000 RU/s|10.000 RU/s|10.000 RU/s|10.000 RU/s|
|Maximale opslag (data + index) per logische partitie van een container|20 GB|20 GB|20 GB|20 GB|

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [logische partities](partitioning-overview.md).
* Meer informatie over het [inrichten van Standard (hand matig) in een Azure Cosmos-container](how-to-provision-container-throughput.md).
* Meer informatie over het [inrichten van een standaard doorvoer (hand matig) in een Azure Cosmos-data base](how-to-provision-database-throughput.md).
* Meer informatie over het [inrichten van de door Voer voor automatisch schalen in een Azure Cosmos-data base of-container](how-to-provision-autoscale-throughput.md).
