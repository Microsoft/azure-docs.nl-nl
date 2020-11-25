---
title: 'Overzicht: Wat is Azure Time Series Insights? - Azure Time Series Insights | Microsoft Docs'
description: Inleiding tot Azure Time Series Insights, een nieuwe service voor de analyse van tijdreeksgegevens en IoT-oplossingen.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: overview
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: cebe0e018b4483a9de1771fb3171effbbd5ca59c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020058"
---
# <a name="what-is-azure-time-series-insights-gen1"></a>Wat is Azure Time Series Insights Gen1?

> [!CAUTION]
> Dit is een Gen1-artikel.

Azure Time Series Insights is gebouwd om grote volumes gegenereerde tijdreeksgegevens van IoT-apparaten op te slaan en te visualiseren of er query's op uit te voeren. Als u tijdreeksgegevens wilt opslaan, beheren, opvragen of visualiseren in de cloud, is Azure Time Series Insights waarschijnlijk de juiste keuze voor u.

[![Stroomdiagram van Azure Time Series Insights](media/overview/time-series-insights-flowchart.png)](media/overview/time-series-insights-flowchart.png#lightbox)

Azure Time Series Insights heeft vier belangrijke taken:

- De service is volledig geïntegreerd met cloudgateways zoals Azure IoT Hub en Azure Event Hubs. Hierdoor kan er eenvoudig verbinding worden maakt met deze gebeurtenisbronnen en kan er JSON worden geparseerd uit berichten en structuren met gegevens in overzichtelijke rijen en kolommen. Metagegevens worden samengevoegd met telemetriegegevens en de gegevens worden geïndexeerd in een columnar store.
- Azure Time Series Insights beheert de opslag van uw gegevens. Om te zorgen dat gegevens altijd eenvoudig toegankelijk zijn, worden uw gegevens gedurende maximaal 400 dagen opgeslagen in het geheugen en op SSD's. U kunt binnen enkele seconden interactief miljarden gebeurtenissen opvragen.
- Azure Time Series Insights biedt een out-of-the-box visualisatie via de Azure Time Series Insights Explorer.
- Azure Time Series Insights biedt een queryservice, zowel in de Azure Time Series Insights Explorer die eenvoudig kunnen worden geïntegreerd voor het insluiten van tijdreeksgegevens in aangepaste toepassingen.

Als u een toepassing bouwt voor intern verbruik of voor gebruik door externe klanten, kunt u Azure Time Series Insights als een back-end gebruiken. U kunt deze gebruiken om tijdreeksgegevens te indexeren, op te slaan en samen te voegen. Om een aangepaste visualisatie en gebruikerservaring samen te stellen, gebruikt u de [Client-SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). Azure Time Series Insights is ook voorzien van verschillende [Query-Api's](./concepts-json-flattening-escaping-rules.md) om deze aangepaste scenario's mogelijk te maken.

Tijdreeksgegevens tonen aan hoe een asset of proces verandert in de loop der tijd. Tijdreeksgegevens worden geïndexeerd door timestamps en tijd is de meest relevante as waarmee dergelijke gegevens zijn ingedeeld. Tijdreeksgegevens komen meestal binnen op volgorde en worden daarom doorgaans behandeld als een invoeging in plaats van een update van de database.

Het kan een uitdaging zijn om tijdreeksgegevens in grote volumes op te slaan, te indexeren, op te vragen, te analyseren en te visualiseren.
Azure Time Series Insights legt een nieuwe gebeurtenis vast en slaat deze op als een rij en veranderingen worden efficiënt bijgehouden. Als gevolg hiervan kunt u in het verleden achterwaarts zoeken naar inzichten om toekomstige wijzigingen te voorspellen.

## <a name="video"></a>Video

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>Meer informatie over Azure Time Series Insights, het op de cloud gebaseerde IoT Analytics-platform.</br>

[![VIDEO](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Primaire scenario's

- Tijdreeksgegevens opslaan op een schaalbare manier.

   De basis van Azure Time Series Insights is een database die speciaal is ontworpen voor het werken met tijdreeksgegevens. Omdat de database schaalbaar en volledig beheerd is, verzorgt Azure Time Series Insights het opslaan en beheren van gebeurtenissen.

- Gegevens bijna in realtime verkennen.

   Azure Time Series Insights biedt een verkenner die alle gegevens visualiseert die in een omgeving binnenkomen. Kort nadat u verbinding hebt gemaakt met een gebeurtenisbron, kunt u gebeurtenisgegevens bekijken, verkennen en opvragen binnen Azure Time Series Insights. De gegevens helpt u om te valideren of een apparaat op de verwachte manier gegevens uitzendt en om een IoT-asset te controleren op status, productiviteit en algehele effectiviteit.

- Analyse van de hoofdoorzaak uitvoeren en afwijkingen detecteren.

   Azure Time Series Insights bevat hulpprogramma's zoals patronen en perspectiefweergaven voor het uitvoeren en opslaan van een hoofdoorzaakanalyse (root-cause analysis) die uit meerdere stappen bestaat. Azure Time Series Insights werkt ook met waarschuwingsservices zoals Azure Stream Analytics, zodat waarschuwingen en gedetecteerde afwijkingen in bijna realtime kunnen worden weergegeven in de Azure Time Series Insights Explorer.

- Verkrijg een globale weergave van tijdreeksgegevens die vanaf verschillende locaties binnenkomen voor vergelijking van meerdere assets of sites.

   U kunt meerdere bronnen van gebeurtenissen verbinden met een Azure Time Series Insights-omgeving. Op deze manier kunt u gegevens weergeven die in vrijwel realtime worden gestreamd vanaf meerdere, ongelijksoortige locaties. Gebruikers kunnen profiteren van deze zichtbaarheid om gegevens met bedrijfsleiders te delen. Ze kunnen beter samenwerken met domeinexperts die hun expertise kunnen helpen bij het oplossen van problemen, het toepassen van aanbevolen procedures en het delen van informatie.

- Klanttoepassing bouwen op basis van Azure Time Series Insights.

   Azure Time Series Insights beschikt over REST-query-API's, zodat u toepassingen kunt ontwikkelen die gebruikmaken van tijdreeksgegevens.

## <a name="capabilities"></a>Functionaliteit

- **Snel aan de slag**: Voor Azure Time Series Insights is geen gegevensvoorbereiding vooraf vereist, zodat u snel verbinding kunt maken met miljoenen gebeurtenissen in uw IoT-hub of Event Hub. Na hu de verbinding tot stand hebt gebracht, kunt u sensorgegevens visualiseren en bewerken om uw IoT-oplossingen snel te valideren. U kunt met uw gegevens werken zonder code te schrijven en u hoeft geen nieuwe taal te leren. Azure Time Series Insights biedt een gedetailleerde queryruimte voor vrije tekst voor ervaren gebruikers, evenals een wijs-en-klik-mogelijkheid.

- **Inzichten in bijna realtime**: Azure Time Series Insights kan miljoenen sensorgebeurtenissen per dag opnemen, met slechts één minuut latentie. Azure Time Series Insights helpt u bij het verkrijgen van inzicht in uw sensorgegevens. Gebruik het om trends en afwijkingen op te sporen, de analyses van hoofdoorzaken uit te voeren en dure downtime te voorkomen. Kruiscorrelatie tussen realtime en historische gegevens, stelt u in staat om verborgen trends in uw gegevens te vinden.

- **Aangepaste oplossingen ontwikkelen**: Azure Time Series Insights-gegevens insluiten in uw bestaande toepassingen. U kunt ook nieuwe aangepaste oplossingen maken met de Azure Time Series Insights REST API's. U kunt bovendien persoonlijke weergaven maken die u kunt delen met anderen om zo uw inzichten breder beschikbaar te maken.

- **Schaalbaarheid**: Azure Time Series Insights kan zeer grote hoeveelheden IoT-gegevens opnemen. De service kan 1 miljoen tot 100 miljoen gebeurtenissen per dag worden opnemen, met een standaardbewaarperiode van 31 dagen. U hebt de mogelijkheid om live gegevensstromen bijna in realtime te visualiseren en te analyseren, naast historische gegevens.

## <a name="get-started"></a>Aan de slag

Volg deze stappen om aan de slag te gaan.

1. Een Azure Time Series Insights-omgeving in de Azure-portal inrichten.
1. Maak verbinding met een gebeurtenisbron zoals Azure-IoT Hub of een Event Hub.
1. Verwijzingsgegevens uploaden. Dit is geen aanvullende service.
1. Bekijk en geef uw gegevens snel weer met de Azure Time Series Insights Explorer.

## <a name="explorer"></a>Explorer

Bekijk, analyseer en ontdek trends in uw gegevens met behulp van de Azure Time Series Insights Explorer.

![Azure Time Series Insights Explorer](media/overview/time-series-insights-explorer-panel.png)

Meer informatie over het gebruik van de [Azure Time Series Insights Explorer](time-series-insights-explorer.md) en het verkrijgen van inzichten uit uw gegevens.

## <a name="next-steps"></a>Volgende stappen

- Verken de [demo-omgeving](./time-series-quickstart.md) van Azure Time Series Insights Gen1.

- Meer informatie over het [plannen van uw Azure Time Series Insights](time-series-insights-environment-planning.md)-omgeving.