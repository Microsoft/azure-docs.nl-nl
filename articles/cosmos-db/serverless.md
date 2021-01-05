---
title: Serverloze aanbieding op basis van verbruik in Azure Cosmos DB
description: Meer informatie over de aanbieding op verbruik gebaseerde servers op basis van Azure Cosmos DB.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/23/2020
ms.openlocfilehash: c5086eee805ffbcdf0741eae4db405b1bcbe8692
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760356"
---
# <a name="azure-cosmos-db-serverless-preview"></a>Azure Cosmos DB serverloze (preview-versie)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!IMPORTANT]
> Azure Cosmos DB server is momenteel beschikbaar als preview-versie. Deze preview-versie wordt zonder Service Level Agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB serverloos kunt u uw Azure Cosmos-account gebruiken op basis van verbruik, waarbij alleen kosten worden berekend voor de aanvraag eenheden die worden verbruikt door uw database bewerkingen en de opslag die wordt gebruikt door uw gegevens. Serverloze containers kunnen duizenden aanvragen per seconde aanbieden zonder minimale kosten en geen capaciteits planning vereist.

> [!IMPORTANT] 
> Hebt u feedback over serverloos? We willen horen! U kunt een bericht weghalen naar het Azure Cosmos DB serverloze team: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

Wanneer u Azure Cosmos DB gebruikt, worden voor elke database bewerking de kosten in [aanvraag eenheden](request-units.md)weer gegeven. Hoe u in rekening wordt gebracht voor deze kosten, is afhankelijk van het type Azure Cosmos-account dat u gebruikt:

- In de [ingerichte doorvoer](set-throughput.md) modus moet u een bepaalde hoeveelheid door Voer (uitgedrukt in aanvraag eenheden per seconde) door voeren die is ingericht voor uw data bases en containers. De kosten van uw database bewerkingen worden vervolgens afgetrokken van het aantal beschik bare aanvraag eenheden per seconde. Aan het einde van de facturerings periode wordt u gefactureerd voor de hoeveelheid door Voer die u hebt ingericht.
- In de serverloze modus hoeft u geen door Voer in te richten bij het maken van containers in uw Azure Cosmos-account. Aan het einde van de facturerings periode wordt u gefactureerd voor het aantal aanvraag eenheden dat door uw database bewerkingen is verbruikt.

## <a name="use-cases"></a>Use-cases

Azure Cosmos DB server het beste geschikt is voor scenario's waar u verwacht:

- **Laag, onregelmatig en onvoorspelbaar verkeer**: omdat het inrichten van capaciteit in dergelijke situaties niet vereist is en het mogelijk is dat de kosten worden verboden
- **Matige prestaties**: omdat containers van serverloze [specifieke prestatie kenmerken](#performance) hebben

Om deze redenen moet Azure Cosmos DB serverloze worden overwogen in de volgende situaties:

- Aan de slag met Azure Cosmos DB
- Ontwikkeling, testen en prototypen van nieuwe toepassingen
- Kleine tot middel grote toepassingen uitvoeren met onregelmatige verkeer dat moeilijk te voors pellen is
- Integreren met serverloze Compute-services zoals [Azure functions](../azure-functions/functions-overview.md)

Zie de [keuze tussen ingerichte door Voer en serverloos](throughput-serverless.md) artikel voor meer informatie over het kiezen van de aanbieding die het beste bij uw gebruik past.

## <a name="using-serverless-resources"></a>Serverloze bronnen gebruiken

Serverloos is een nieuw Azure Cosmos-account type. Dit betekent dat u moet kiezen tussen **ingerichte door Voer** en **serverloos** bij het maken van een nieuw account. U moet een nieuw serverloze account maken om aan de slag te gaan met serverloos. Tijdens de preview-versie is de enige ondersteunde manier om een nieuw serverloze account te maken [met behulp van de Azure Portal](create-cosmosdb-resources-portal.md). Het migreren van bestaande accounts naar/van de serverloze modus wordt momenteel niet ondersteund.

Een container die is gemaakt in een serverloze account, is een serverloze container. Serverloze containers bieden dezelfde mogelijkheden als containers die zijn gemaakt in de ingerichte doorvoer modus, zodat u uw gegevens op exact dezelfde manier kunt lezen, schrijven en doorzoeken. Serverloze accounts en containers hebben echter ook specifieke kenmerken:

> [!IMPORTANT]
> Sommige van deze beperkingen kunnen worden vergemakkelijkt of verwijderd wanneer de server niet algemeen beschikbaar is en **uw feedback** helpt ons te beslissen. Neem contact op met uw ervaring en vertel ons meer over uw serverloos: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

- Een serverloze account kan alleen in één Azure-regio worden uitgevoerd. Het is niet mogelijk om extra Azure-regio's toe te voegen aan een serverloze account nadat u deze hebt gemaakt.
- Het is niet mogelijk om de preview-functie van de [Synapse-koppeling](synapse-link.md) in te scha kelen op een serverloze account.
- De inrichtings doorvoer is niet vereist voor serverloze containers, dus de volgende instructies zijn van toepassing:
    - U kunt geen door voer door geven bij het maken van een serverloze container en er wordt een fout geretourneerd.
    - U kunt de door Voer niet lezen of bijwerken op een serverloze container en er wordt een fout geretourneerd.
    - U kunt geen gedeelde doorvoer database maken in een serverloze account en daarom wordt er een fout geretourneerd.
- Serverloze containers kunnen Maxi maal 50 GB aan gegevens en indexen opslaan.

## <a name="monitoring-your-consumption"></a>Uw verbruik bewaken

Als u eerder Azure Cosmos DB in de ingerichte doorvoer modus hebt gebruikt, zult u merken dat serverloos rendabeler is wanneer uw verkeer de ingerichte capaciteit niet uitgeeft. De afweging is dat uw kosten minder voorspelbaar worden, omdat u wordt gefactureerd op basis van het aantal aanvragen dat uw data base heeft verwerkt. Daarom is het belang rijk om uw huidige verbruik te blijven gebruiken.

Wanneer u door het deel venster met **metrische gegevens** van uw account bladert, ziet u een grafiek met de naam **aanvraag eenheden** die op het tabblad **overzicht** zijn gebruikt. In dit diagram ziet u hoeveel aanvraag eenheden uw account heeft verbruikt:

:::image type="content" source="./media/serverless/request-units-consumed.png" alt-text="Diagram waarin de verbruikte aanvraag eenheden worden weer gegeven" border="false":::

U kunt dezelfde grafiek vinden wanneer u Azure Monitor gebruikt, zoals [hier](monitor-request-unit-usage.md)wordt beschreven. Houd er rekening mee dat Azure Monitor [waarschuwingen](../azure-monitor/platform/alerts-metric-overview.md)kunt instellen, die kunnen worden gebruikt om u te waarschuwen wanneer het verbruik van de aanvraag eenheid een bepaalde drempel waarde heeft door gegeven.

## <a name="performance"></a><a id="performance"></a>Prestaties

Serverloze resources geven specifieke prestatie kenmerken door die afwijken van de ingerichte doorvoer resources:

- **Beschik baarheid**: nadat de serverloze aanbieding algemeen beschikbaar is, wordt de beschik baarheid van serverloze containers gedekt door een Service Level Agreement (Sla) van 99,9% wanneer Beschikbaarheidszones (zone redundantie) niet wordt gebruikt. De SLA is 99,99% wanneer Beschikbaarheidszones worden gebruikt.
- **Latentie**: nadat de serverloze aanbieding algemeen beschikbaar is, wordt de latentie van serverloze containers gedekt door een serviceniveau doelstelling (SLO) van 10 milliseconden of minder voor punt-en 30 milliseconden of minder voor schrijf bewerkingen. Een lees bewerking voor een punt bestaat uit het ophalen van één item met de ID en partitie sleutel waarde.
- **Burstie**: nadat de serverloze aanbieding algemeen beschikbaar is, wordt de burstie van serverloze containers gedekt door een serviceniveau doelstelling (SLO) van 95%. Dit betekent dat de maximale burstie ten minste 95% van de tijd kan worden bereikt.

> [!NOTE]
> Als Azure-Preview wordt Azure Cosmos DB serverloos uitgesloten van service overeenkomsten (SLA). De prestatie-eigenschappen die hierboven worden vermeld, worden geleverd als een preview van wat deze aanbieding zal leveren wanneer algemeen beschikbaar is.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met serverloos met de volgende artikelen:

- [Aanvraageenheden in Azure Cosmos DB](request-units.md)
- [Kies tussen ingerichte doorvoer en serverloos](throughput-serverless.md)
- [Prijsmodel in Azure Cosmos DB](how-pricing-works.md)
