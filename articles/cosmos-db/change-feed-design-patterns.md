---
title: Ontwerp patronen voor feeds wijzigen in Azure Cosmos DB
description: Overzicht van algemene ontwerp patronen voor wijzigingen in feeds
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 443d00e61e593daacca04a4451b90bb78cc7d854
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93334579"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Ontwerp patronen voor feeds wijzigen in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

De Azure Cosmos DB Change feed maakt efficiënte verwerking van grote gegevens sets mogelijk met een groot aantal schrijf bewerkingen. Change feed biedt ook een alternatief voor het uitvoeren van een query op een hele gegevensset om te bepalen wat er is gewijzigd. Dit document richt zich op veelvoorkomende ontwerp patronen voor veranderingen in de feed, ontwerp-en wijzigings beperkingen.

Azure Cosmos DB is goed geschikt voor IoT-, gaming-, retail-en operationele logboek toepassingen. Een gemeen schappelijk ontwerp patroon in deze toepassingen is het gebruik van wijzigingen in de gegevens om aanvullende acties te activeren. Voor beelden van aanvullende acties zijn:

* Het activeren van een melding of een aanroep van een API wanneer een item wordt ingevoegd of bijgewerkt.
* Realtime-verwerking van streams voor IoT of realtime analyse verwerking op operationele gegevens.
* Gegevens verplaatsing zoals synchronisatie met een cache, een zoek machine, een Data Warehouse of koude opslag.

Met de wijzigings feed in Azure Cosmos DB kunt u efficiënte en schaal bare oplossingen voor elk van deze patronen bouwen, zoals wordt weer gegeven in de volgende afbeelding:

:::image type="content" source="./media/change-feed/changefeedoverview.png" alt-text="Azure Cosmos DB Change feed gebruiken om realtime analyse en op gebeurtenissen gebaseerde computer scenario's uit te voeren" border="false":::

## <a name="event-computing-and-notifications"></a>Gebeurtenis computing en meldingen

De Azure Cosmos DB Change feed kan scenario's vereenvoudigen die een melding moeten activeren of een aanroep naar een API moeten verzenden op basis van een bepaalde gebeurtenis. U kunt de bibliotheek voor het wijzigen van het [feedproces](change-feed-processor.md) gebruiken om automatisch uw container te controleren op wijzigingen en een externe API aan te roepen wanneer er een schrijf-of update procedure is.

U kunt ook selectief een melding activeren of een aanroep naar een API verzenden op basis van specifieke criteria. Als u bijvoorbeeld van de wijzigings feed met behulp van [Azure functions](change-feed-functions.md)leest, kunt u logica in de functie plaatsen om alleen een melding te verzenden als aan een bepaald criterium is voldaan. Hoewel de functie code van Azure tijdens elke schrijf bewerking zou worden uitgevoerd, wordt de melding alleen verzonden als aan specifieke criteria is voldaan.

## <a name="real-time-stream-processing"></a>Realtime-verwerking van streams

De Azure Cosmos DB wijzigings feed kan worden gebruikt voor realtime-stroom verwerking voor IoT of realtime analyse verwerking op operationele gegevens.
U kunt bijvoorbeeld gebeurtenis gegevens van apparaten, Sens oren, infra structuur en toepassingen ontvangen en opslaan en deze gebeurtenissen in realtime verwerken met [Spark](../hdinsight/spark/apache-spark-overview.md). De volgende afbeelding laat zien hoe u een lambda-architectuur kunt implementeren met behulp van de Azure Cosmos DB via een wijzigings feed:

:::image type="content" source="./media/change-feed/lambda.png" alt-text="Lambda-pijp lijn op basis van Azure Cosmos DB voor opname en query" border="false":::

In veel gevallen ontvangen implementaties voor stroom verwerking eerst een groot aantal inkomende gegevens in een tijdelijke berichten wachtrij, zoals Azure Event hub of Apache Kafka. De wijzigings feed is een uitstekend alternatief als gevolg van de mogelijkheden van Azure Cosmos DB om een aanhoudende hoge frequentie van gegevens opname te ondersteunen met gegarandeerde lage lees-en schrijf latentie. De voor delen van de Azure Cosmos DB wijzigings feed voor een berichten wachtrij zijn onder andere:

### <a name="data-persistence"></a>Gegevenspersistentie

Gegevens die naar Azure Cosmos DB zijn geschreven, worden weer gegeven in de wijzigings feed en blijven bewaard totdat ze worden verwijderd. Berichten wachtrijen hebben doorgaans een maximale Bewaar periode. [Azure Event hub](https://azure.microsoft.com/services/event-hubs/) biedt bijvoorbeeld een maximale gegevens retentie van 90 dagen.

### <a name="querying-ability"></a>Query mogelijkheid

Naast het lezen van de wijzigings feed van een Cosmos-container, kunt u ook SQL-query's uitvoeren op de gegevens die zijn opgeslagen in Azure Cosmos DB. De wijzigings feed is geen duplicatie van de gegevens die zich al in de container bevinden, maar in plaats daarvan alleen een ander mechanisme voor het lezen van de gegevens. Als u dus gegevens van de wijzigings feed leest, is deze altijd consistent met query's van dezelfde Azure Cosmos DB-container.

### <a name="high-availability"></a>Hoge beschikbaarheid

Azure Cosmos DB biedt Maxi maal 99,999% Beschik baarheid voor lezen en schrijven. In tegens telling tot veel bericht wachtrijen kunnen Azure Cosmos DB gegevens gemakkelijk wereld wijd worden gedistribueerd en geconfigureerd met een [RTO (Recovery Time doelstelling)](./consistency-levels.md#rto) van nul.

Na het verwerken van items in de wijzigings feed kunt u een gerealiseerde weer gave maken en de geaggregeerde waarden weer in Azure Cosmos DB. Als u Azure Cosmos DB gebruikt voor het bouwen van een spelletje, kunt u bijvoorbeeld Change feed gebruiken om realtime klassementen te implementeren op basis van scores van voltooide games.

## <a name="data-movement"></a>Gegevensverplaatsing

U kunt ook lezen uit de wijzigings feed voor realtime gegevens verplaatsing.

De wijzigings feed helpt u bijvoorbeeld bij het efficiënt uitvoeren van de volgende taken:

* Een cache, zoek index of Data Warehouse bijwerken met gegevens die zijn opgeslagen in Azure Cosmos DB.

* U kunt niet meer dan nul migraties uitvoeren naar een ander Azure Cosmos-account of een andere Azure Cosmos-container met een andere logische partitie sleutel.

* Implementeer een gegevenslaagder en-archivering op toepassings niveau. U kunt bijvoorbeeld ' dynamische gegevens ' opslaan in Azure Cosmos DB en ' koude gegevens ' naar andere opslag systemen zoals [Azure Blob Storage](../storage/common/storage-introduction.md).

Wanneer u [gegevens in partities en containers moet denormaliseren](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
), kunt u de wijziging van de invoer van de container als bron voor deze gegevens replicatie lezen. Realtime gegevens replicatie met de wijzigings feed kan de uiteindelijke consistentie alleen garanderen. U kunt [bewaken hoe ver de lags van de wijzigings verwerker achter bij het](how-to-use-change-feed-estimator.md) verwerken van wijzigingen in de Cosmos-container.

## <a name="event-sourcing"></a>Gebeurtenis bronnen

Het [gebeurtenis bronnen patroon](/azure/architecture/patterns/event-sourcing) omvat het gebruik van een archief alleen toevoegen voor het vastleggen van de volledige reeks acties voor die gegevens. De wijzigings feed van Azure Cosmos DB is een uitstekende keuze als een centrale gegevens opslag in Event sourcing-architecturen waarbij alle gegevens opname wordt gemodelleerd als schrijf bewerkingen (geen updates of verwijderingen). In dit geval is elke schrijf bewerking naar Azure Cosmos DB een ' gebeurtenis '. u hebt dan een volledige record van achterstallige gebeurtenissen in de wijzigings feed. Typisch gebruik van de gebeurtenissen die door het centrale gebeurtenis archief worden gepubliceerd, zijn voor het onderhouden van gerealiseerde weer gaven of voor de integratie met externe systemen. Omdat er geen tijds limiet is voor het bewaren van de wijzigings feed, kunt u alle gebeurtenissen in het verleden herhalen door te lezen vanaf het begin van de wijzigings feed van uw Cosmos-container.

U kunt [meerdere changes-gebruikers abonneren op de wijzigings feed van dezelfde container](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers). Afgezien van de ingerichte door Voer van de [lease container](change-feed-processor.md#components-of-the-change-feed-processor) zijn er geen kosten voor het gebruik van de wijzigings feed. De wijzigings feed is beschikbaar in elke container, ongeacht of deze wordt gebruikt.

Azure Cosmos DB is een fantastische, alleen permanente alleen-lezen gegevens opslag in het gebeurtenis bronnen patroon vanwege de sterke punten in horizontale schaal baarheid en hoge Beschik baarheid. Daarnaast biedt de processor bibliotheek voor wijzigings invoer een garantie [van ten minste eenmaal](change-feed-processor.md#error-handling) , zodat u geen gebeurtenissen meer kunt verwerken.

## <a name="current-limitations"></a>Huidige beperkingen

De wijzigings feed heeft belang rijke beperkingen die u moet begrijpen. Terwijl items in een Cosmos-container altijd in de wijzigings feed blijven, is de wijzigings feed geen volledig bewerkings logboek. Er zijn belang rijke gebieden waarmee u rekening moet houden bij het ontwerpen van een toepassing die gebruikmaakt van de wijzigings feed.

### <a name="intermediate-updates"></a>Tussenliggende updates

Alleen de meest recente wijziging voor een bepaald item is opgenomen in de wijzigings feed. Wanneer de wijzigingen worden verwerkt, leest u de meest recente versie van het item. Als er in korte tijd meerdere updates voor hetzelfde item zijn, is het mogelijk om de verwerking van tussenliggende updates te missen. Als u updates wilt bijhouden en eerdere updates voor een item opnieuw wilt kunnen afspelen, raden we u aan om deze updates te model leren als een reeks schrijf bewerkingen.

### <a name="deletes"></a>Delete

De wijzigings feed legt geen verwijderingen vast. Als u een item uit uw container verwijdert, wordt het ook uit de wijzigings feed verwijderd. De meest voorkomende methode voor het verwerken hiervan is het toevoegen van een zachte markering voor de items die worden verwijderd. U kunt een eigenschap met de naam ' verwijderd ' toevoegen en deze instellen op ' True ' op het moment van verwijdering. Deze document update wordt weer gegeven in de wijzigings feed. U kunt een TTL instellen voor dit item zodat het later automatisch kan worden verwijderd.

### <a name="guaranteed-order"></a>Gegarandeerde volg orde

Er is een gegarandeerde volg orde in de wijzigings feed binnen een partitie sleutel waarde, maar niet op de partitie sleutel waarden. U moet een partitie sleutel selecteren die u een zinvolle order garantie geeft.

Denk bijvoorbeeld aan een retail-toepassing met behulp van het ontwerp patroon Event sourcing. In deze toepassing zijn verschillende gebruikers acties elk "gebeurtenissen" die als schrijf bewerkingen naar Azure Cosmos DB worden gemodelleerd. Stel dat er een aantal voor beelden is opgetreden in de volgende volg orde:

1. Klant voegt item A toe aan hun winkel wagentje
2. Klant voegt item B toe aan hun winkel wagentje
3. Klant verwijdert item A uit hun winkel wagen
4. De uitchecken van de klant en de inhoud van de winkel wagen worden verzonden

Er wordt voor elke klant een gerealiseerde weer gave van de huidige inhoud van de winkel wagen onderhouden. Deze toepassing moet ervoor zorgen dat deze gebeurtenissen worden verwerkt in de volg orde waarin ze plaatsvinden. Als bijvoorbeeld de kassa van de winkel wagen moet worden verwerkt voordat item A wordt verwijderd, zou de klant waarschijnlijk een item hebben verzonden, in plaats van het gewenste item B. Om ervoor te zorgen dat deze vier gebeurtenissen worden verwerkt in volg orde van het voorval, moeten ze binnen dezelfde partitie sleutel waarde vallen. Als u **gebruikers naam** selecteert (elke klant heeft een unieke gebruikers naam) als de partitie sleutel, kunt u garanderen dat deze gebeurtenissen in de wijzigings feed worden weer gegeven in dezelfde volg orde als waarin ze naar Azure Cosmos DB zijn geschreven.

## <a name="examples"></a>Voorbeelden

Hier volgen enkele voor beelden van de invoer code van een andere wereld, die buiten het bereik van de voor beelden in micro soft docs valt:

- [Inleiding tot de wijzigings feed](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [IoT use-case gecentreerd rond de wijzigings feed](https://github.com/AzureCosmosDB/scenario-based-labs)
- [Use-case voor detail handel gecentreerd rond de wijzigings feed](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>Volgende stappen

* [Overzicht wijzigingenfeed](change-feed.md)
* [Opties voor het lezen van een wijzigings feed](read-change-feed.md)
* [Change feed gebruiken met Azure Functions](change-feed-functions.md)