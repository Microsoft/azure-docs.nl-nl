---
title: Problemen oplossen bij het gebruik van Azure Functions trigger voor Cosmos DB
description: Veelvoorkomende problemen, tijdelijke oplossingen en diagnostische stappen bij het gebruik van de Azure Functions trigger voor Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 12/29/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 1b7b82ea07b7e00d281739011c9c9f83ab4dff73
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97825624"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Problemen vaststellen en oplossen bij het gebruik van Azure Functions trigger voor Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel worden veelvoorkomende problemen, tijdelijke oplossingen en diagnostische stappen behandeld wanneer u de [Azure functions trigger voor Cosmos DB](change-feed-functions.md)gebruikt.

## <a name="dependencies"></a>Afhankelijkheden

De Azure Functions trigger en bindingen voor Cosmos DB zijn afhankelijk van de extensie pakketten in de basis Azure Functions runtime. Zorg ervoor dat deze pakketten altijd worden bijgewerkt, omdat deze mogelijk oplossingen en nieuwe functies bevatten die kunnen leiden tot mogelijke problemen die optreden:

* Zie voor Azure Functions v2 [micro soft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Zie [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB)voor Azure functions v1.

In dit artikel wordt altijd verwezen naar Azure Functions v2 wanneer de runtime wordt vermeld, tenzij expliciet is opgegeven.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>De Azure Cosmos DB SDK onafhankelijk gebruiken

De belangrijkste functionaliteit van het uitbreidings pakket is om ondersteuning te bieden voor de Azure Functions trigger en bindingen voor Cosmos DB. Het bevat ook de [Azure Cosmos db .NET SDK](sql-api-sdk-dotnet-core.md). Dit is handig als u via een programma wilt communiceren met Azure Cosmos DB zonder de trigger en bindingen te gebruiken.

Als u de Azure Cosmos DB SDK wilt gebruiken, moet u ervoor zorgen dat u niet nog een NuGet-pakket verwijzing aan uw project toevoegt. In plaats daarvan **kunt u de SDK-referentie oplossen via het uitbreidings pakket van de Azure functions**. De Azure Cosmos DB SDK afzonderlijk van de trigger en bindingen gebruiken

Als u hand matig uw eigen exemplaar van de [Azure Cosmos DB SDK-client](./sql-api-sdk-dotnet-core.md)maakt, moet u ook het patroon volgen van slechts één exemplaar van de client [met behulp van een singleton patroon benadering](../azure-functions/manage-connections.md#documentclient-code-example-c). Dit proces voor komt de potentiële socket problemen in uw bewerkingen.

## <a name="common-scenarios-and-workarounds"></a>Algemene scenario's en tijdelijke oplossingen

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>De Azure-functie is mislukt omdat de verzameling voor het fout bericht niet bestaat

De Azure-functie is mislukt met het fout bericht: de verzamelings naam van de bron verzameling (in de data base-naam van de database) of de lease verzameling ' Collection2-name ' (in de data base ' Database2-name ') bestaat niet. Beide verzamelingen moeten bestaan voordat de listener wordt gestart. Als u de lease verzameling automatisch wilt maken, stelt u ' CreateLeaseCollectionIfNotExists ' in op ' True '

Dit betekent dat een of beide Azure Cosmos-containers die vereist zijn voor de trigger voor werk, niet bestaan of niet bereikbaar zijn voor de Azure-functie. **De fout zelf vertelt u welke Azure Cosmos-data base en-container de trigger is die** op basis van uw configuratie moet worden gezocht.

1. Controleer het `ConnectionStringSetting` kenmerk en of dit **verwijst naar een instelling die voor komt in uw Azure functie-app**. De waarde van dit kenmerk mag niet de verbindings reeks zelf zijn, maar de naam van de configuratie-instelling.
2. Controleer of de `databaseName` en `collectionName` aanwezig zijn in uw Azure Cosmos-account. Als u automatische vervanging van waarden (met `%settingName%` patronen) gebruikt, moet u ervoor zorgen dat de naam van de instelling bestaat in uw Azure functie-app.
3. Als u geen opgeeft `LeaseCollectionName/leaseCollectionName` , wordt de standaard waarde leases. Controleer of deze container bestaat. U kunt desgewenst het `CreateLeaseCollectionIfNotExists` kenmerk in de trigger instellen om `true` het automatisch te maken.
4. Controleer de [firewall configuratie van uw Azure Cosmos-account](how-to-configure-firewall.md) om te zien of deze niet de Azure-functie blokkeert.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>De Azure-functie kan niet worden gestart met de verzameling gedeelde door voer moet een partitie sleutel hebben.

De vorige versies van de Azure Cosmos DB-extensie bieden geen ondersteuning voor het gebruik van een lease-container die is gemaakt in een [gedeelde doorvoer database](./set-throughput.md#set-throughput-on-a-database). Als u dit probleem wilt oplossen, werkt u de extensie [micro soft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) bij om de nieuwste versie te downloaden.

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>De Azure-functie kan niet worden gestart met ' PartitionKey moet worden opgegeven voor deze bewerking. '

Deze fout betekent dat u momenteel een gepartitioneerde lease verzameling gebruikt met een oude [extensie afhankelijkheid](#dependencies). Voer een upgrade uit naar de meest recente beschik bare versie. Als u momenteel werkt op Azure Functions v1, moet u een upgrade uitvoeren naar Azure Functions v2.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>De Azure-functie kan niet worden gestart met ' de lease-verzameling, indien gepartitioneerd, moet een partitie sleutel hebben die gelijk is aan id. '

Deze fout houdt in dat uw huidige leases-container is gepartitioneerd, maar het pad naar de partitie sleutel niet `/id` . Om dit probleem op te lossen, moet u de leases-container opnieuw maken met `/id` als de partitie sleutel.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>U ziet een ' waarde kan niet null zijn. Parameter naam: o "in uw Azure Functions-Logboeken wanneer u probeert de trigger uit te voeren

Dit probleem wordt weer gegeven als u de Azure Portal gebruikt en u probeert de knop **uitvoeren** op het scherm te selecteren bij het inspecteren van een Azure-functie die gebruikmaakt van de trigger. Het is niet nodig om uitvoeren te selecteren om te starten. de trigger wordt automatisch gestart wanneer de Azure-functie wordt geïmplementeerd. Als u de logboek stroom van de Azure function wilt controleren op het Azure Portal, gaat u naar de bewaakte container en voegt u enkele nieuwe items in, dan wordt de trigger die wordt uitgevoerd automatisch weer geven.

### <a name="my-changes-take-too-long-to-be-received"></a>Het duurt te lang om mijn wijzigingen te ontvangen

Dit scenario kan meerdere oorzaken hebben en moeten allemaal worden gecontroleerd:

1. Wordt de Azure-functie geïmplementeerd in dezelfde regio als het Azure Cosmos-account? Voor een optimale netwerklatentie moeten de Azure-functie en het Azure Cosmos-account zich in dezelfde Azure-regio bevinden.
2. Vinden de wijzigingen in uw Azure Cosmos-container continu of sporadisch plaats?
In het laatste geval kan er enige vertraging optreden tussen het moment dat de wijzigingen worden opgeslagen en het moment dat de Azure-functie deze ophaalt. Dit komt doordat wanneer tijdens het intern controleren op wijzigingen in de Azure Cosmos-container geen wijzigingen worden aangetroffen die in behandeling zijn om te worden gelezen, de Azure-container gedurende een configureerbare periode (standaard vijf seconden) in de slaapstand geraakt voordat er op nieuwe wijzigingen wordt gecontroleerd (om een hoog RU-verbruik te voorkomen). U kunt deze periode in de slaapstand configureren via de `FeedPollDelay/feedPollDelay`-instelling in de [configuratie](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) van de trigger (de waarde bedraagt naar verwachting enkele milliseconden).
3. Uw Azure Cosmos-container kan een [beperkt aantal](./request-units.md)zijn.
4. U kunt het `PreferredLocations` kenmerk in de trigger gebruiken om een door komma's gescheiden lijst van Azure-regio's op te geven voor het definiëren van een aangepaste voorkeurs volgorde voor verbinding.

### <a name="some-changes-are-repeated-in-my-trigger"></a>Sommige wijzigingen worden herhaald in mijn trigger

Het concept van een ' wijziging ' is een bewerking in een document. De meest voorkomende scenario's waarin gebeurtenissen voor hetzelfde document worden ontvangen, zijn:
* Het account maakt gebruik van uiteindelijke consistentie. Bij het gebruik van de wijzigings feed in een mogelijk consistentie niveau, kunnen er dubbele gebeurtenissen optreden in de volgende Lees bewerkingen voor de invoer van wijzigingen (de laatste gebeurtenis van één Lees bewerking wordt weer gegeven als de eerste van de volgende).
* Het document wordt bijgewerkt. De wijzigings feed kan meerdere bewerkingen voor dezelfde documenten bevatten, als dat document updates ontvangt, kunnen er meerdere gebeurtenissen worden opgehaald (één voor elke update). Een eenvoudige manier om onderscheid te maken tussen verschillende bewerkingen voor hetzelfde document is het bijhouden `_lsn` [van de eigenschap voor elke wijziging](change-feed.md#change-feed-and-_etag-_lsn-or-_ts). Als ze niet overeenkomen, zijn dit verschillende wijzigingen ten opzichte van hetzelfde document.
* Als u documenten wilt identificeren, moet u `id` er rekening mee houden dat de unieke id voor een document de `id` en de bijbehorende partitie sleutel is (er kunnen twee documenten zijn met dezelfde `id` maar een andere partitie sleutel).

### <a name="some-changes-are-missing-in-my-trigger"></a>Er ontbreken enkele wijzigingen in de trigger

Als u merkt dat sommige wijzigingen die zijn opgetreden in de Azure Cosmos-container, niet worden opgehaald door de Azure-functie of omdat er geen wijzigingen in de bestemming ontbreken, kunt u de onderstaande stappen volgen.

Als uw Azure-functie de wijzigingen ontvangt, worden deze vaak verwerkt en kunnen eventueel het resultaat naar een andere bestemming worden verzonden. Wanneer u ontbrekende wijzigingen onderzoekt, moet u **meten welke wijzigingen worden ontvangen op het opname punt** (wanneer de Azure-functie wordt gestart), niet op de bestemming.

Als sommige wijzigingen op de bestemming ontbreken, kan dit betekenen dat er een fout optreedt tijdens de uitvoering van de Azure-functie nadat de wijzigingen zijn ontvangen.

In dit scenario is het de beste manier om blokken toe te voegen `try/catch` in uw code en binnen de lussen die de wijzigingen kunnen verwerken, om eventuele fouten voor een bepaalde subset van items te detecteren en ze dienovereenkomstig te verwerken (stuur ze naar een andere opslag voor verdere analyse of probeer het opnieuw).

> [!NOTE]
> Standaard wordt er met de Azure Functions-trigger niet opnieuw een batch met wijzigingen geprobeerd als er een niet-afgehandelde uitzondering tijdens het uitvoeren van de code is opgetreden. Dit betekent dat de wijzigingen niet zijn doorgevoerd op de bestemming omdat u deze niet kunt verwerken.

Als de bestemming een andere Cosmos-container is en u Upsert-bewerkingen uitvoert om de items te kopiëren, **controleert u of de partitie sleutel definitie op zowel de bewaakte als de doel container hetzelfde zijn**. Upsert-bewerkingen kunnen meerdere bron items opslaan als één in het doel vanwege dit verschil in de configuratie.

Als u merkt dat sommige wijzigingen niet door de trigger zijn ontvangen, is het meest voorkomende scenario dat er **een andere Azure-functie wordt uitgevoerd**. Het kan een andere Azure-functie zijn die is geïmplementeerd in azure of een Azure-functie die lokaal wordt uitgevoerd op de computer van een ontwikkelaar en die **exact dezelfde configuratie** heeft (dezelfde bewaakte en lease-containers). deze Azure-functie stelen een subset van de wijzigingen die u verwacht dat uw Azure-functie wordt verwerkt.

Daarnaast kunt u het scenario valideren als u weet hoeveel exemplaren van Azure functie-app u uitvoert. Als u uw leases-container inspecteert en het aantal lease-items binnen hebt geteld, moeten de afzonderlijke waarden van de `Owner` eigenschap in deze worden ingesteld op het aantal exemplaren van uw functie-app. Als er meer eigen aren zijn dan de instanties van de bekende Azure-functie-app, betekent dit dat deze extra eigen aren de wijzigingen ' stelen ' hebben.

Een eenvoudige manier om deze situatie te omzeilen, is door een `LeaseCollectionPrefix/leaseCollectionPrefix` toe te passen op uw functie met een nieuwe/andere waarde of door te testen met een nieuwe lease-container.

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>De items in mijn container moeten opnieuw worden opgestart en opnieuw worden verwerkt vanaf het begin 
Alle items in een container opnieuw verwerken vanaf het begin:
1. Stop uw Azure-functie als deze momenteel wordt uitgevoerd. 
1. De documenten in de lease verzameling verwijderen (of de lease verzameling verwijderen en opnieuw maken, zodat deze leeg is)
1. Stel het kenmerk [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDBTrigger in uw functie in op waar. 
1. Start de Azure-functie opnieuw. Er worden nu alle wijzigingen van het begin gelezen en verwerkt. 

Als u [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) instelt op True, wordt de Azure-functie gestart met het lezen van wijzigingen aan het begin van de geschiedenis van de verzameling in plaats van de huidige tijd. Dit werkt alleen wanneer er geen leases zijn (dat wil zeggen, documenten in de leases-verzameling). Als u deze eigenschap instelt op waar wanneer er al leases zijn gemaakt, heeft dit geen effect. Wanneer een functie wordt gestopt en opnieuw wordt gestart, wordt in dit scenario begonnen met het lezen van het laatste controle punt, zoals gedefinieerd in de verzameling leases. Volg de bovenstaande stappen 1-4 om vanaf het begin opnieuw te verwerken.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>Binding kan alleen worden uitgevoerd met IReadOnlyList \<Document> of JArray

Deze fout treedt op als uw Azure Functions project (of een project waarnaar wordt verwezen) een hand matige NuGet-verwijzing naar de Azure Cosmos DB SDK bevat met een andere versie dan die van de [uitbrei ding Azure Functions Cosmos DB](./troubleshoot-changefeed-functions.md#dependencies).

U kunt dit probleem omzeilen door de hand matige NuGet-verwijzing die is toegevoegd, te verwijderen en de Azure Cosmos DB SDK-referentie op te lossen via het Azure Functions Cosmos DB-uitbreidings pakket.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Het polling-interval van Azure function wijzigen voor de detectie van wijzigingen

Zoals eerder is uitgelegd, [duurt het te lang voordat mijn wijzigingen worden ontvangen](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received). in de Azure-functie wordt gedurende een Configureer bare hoeveelheid tijd (standaard 5 seconden) geslapend voordat wordt gecontroleerd op nieuwe wijzigingen (om te voor komen dat het gebruik van hoge ru wordt gebruikt). U kunt deze periode in de slaapstand configureren via de `FeedPollDelay/feedPollDelay`-instelling in de [configuratie](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) van de trigger (de waarde bedraagt naar verwachting enkele milliseconden).

## <a name="next-steps"></a>Volgende stappen

* [Controle inschakelen voor uw Azure Functions](../azure-functions/functions-monitoring.md)
* [Problemen met Azure Cosmos DB .NET SDK oplossen](./troubleshoot-dot-net-sdk.md)
