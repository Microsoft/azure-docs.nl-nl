---
title: Gen2 use cases-Azure Time Series Insights Gen2 | Microsoft Docs
description: Meer informatie over Azure Time Series Insights use cases van Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: seodec18
ms.openlocfilehash: 7d97958c5fd1274495da88c064b63e59e354f691
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606960"
---
# <a name="azure-time-series-insights-gen2-use-cases"></a>Gebruikscases van Azure Time Series Insights Gen2

Dit artikel bevat een overzicht van een aantal algemene gebruiks voorbeelden voor Azure Time Series Insights Gen2. De aanbevelingen in dit artikel fungeren als uitgangs punt voor het ontwikkelen van uw toepassingen en oplossingen met Azure Time Series Insights Gen2.

In dit artikel worden met name de volgende vragen beantwoord:

* Wat zijn de algemene use cases voor Azure Time Series Insights Gen2?
* Wat zijn de voor delen van het gebruik van Azure Time Series Insights Gen2 voor het [verkennen van gegevens en de detectie van visuele afwijkingen](#data-exploration-and-visual-anomaly-detection)?
* Wat zijn de voor delen van het gebruik van Azure Time Series Insights Gen2 voor [operationele analyses en de efficiëntie van processen](#operational-analysis-and-driving-process-efficiency)?
* Wat zijn de voor delen van het gebruik van Azure Time Series Insights Gen2 for [Advanced Analytics](#advanced-analytics)?

Een overzicht van deze gebruiks scenario's wordt beschreven in de volgende secties.

## <a name="introduction"></a>Introductie

Azure Time Series Insights Gen2 is een end-to-end-, platform-as-a-service-aanbieding. Het wordt gebruikt voor het verzamelen, verwerken, opslaan, analyseren en opvragen van zeer contextuele, time-series geoptimaliseerde gegevens voor IoT-schaal. Het is ideaal voor het verkennen van ad hoc gegevens en operationele analyses. Azure Time Series Insights Gen2 is een unieke uitbreid bare, aangepaste service aanbieding die voldoet aan de uitgebreide behoeften van industriële IoT-implementaties.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Gegevens verkennen en visuele anomalie detectie

Verken en analyseer miljarden gebeurtenissen om afwijkingen op te sporen en de verborgen trends in uw gegevens te ontdekken. Azure Time Series Insights Gen2 biedt bijna realtime prestaties voor uw IoT-en DevOps-analyse werk belastingen.

[![Data Explorer](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

De meeste klanten komen overeen dat de minimale hoeveelheid tijd die nodig is om inzicht te krijgen, een van de belangrijkste-functies van Azure Time Series Insights Gen2 is:

* Azure Time Series Insights Gen2 vereist geen voor bereiding op gegevens vooraf.
* Het werkt snel om u te verbinden met miljarden gebeurtenissen in uw Azure IoT Hub-of Azure Event Hubs-instanties in enkele minuten.
* Zodra u verbinding hebt gemaakt, kunt u miljarden gebeurtenissen visualiseren en analyseren om afwijkingen op te sporen en verborgen trends in uw gegevens te ontdekken.

Azure Time Series Insights Gen2 is intuïtief en eenvoudig te gebruiken. U kunt met uw gegevens werken zonder ook maar één regel code te schrijven. Er is ook geen nieuwe taal die u nodig hebt om te leren, hoewel Azure Time Series Insights Gen2 een gedetailleerde Zoek taal op basis van tekst biedt voor ervaren gebruikers die bekend zijn met SQL. U kunt er ook voor kiezen om te verkennen.

Klanten kunnen profiteren van de snelheid voor het snel diagnosticeren van problemen met activa. Ze kunnen DevOps-analyses uitvoeren om de hoofd oorzaak van een bug in een IoT-oplossing te verkrijgen. Ze kunnen ook gebieden identificeren die moeten worden gemarkeerd voor nader onderzoek als onderdeel van hun data Science-initiatieven.

Er zijn drie manieren om te communiceren met gegevens die zijn opgeslagen in Azure Time Series Insights Gen2:

* De eerste en eenvoudigste manier om aan de slag te gaan is met de Azure Time Series Insights Gen2 Explorer. U kunt deze gebruiken om snel al uw IoT-gegevens op één plek te visualiseren. Het bevat hulpprogram ma's zoals de heatmap waarmee u afwijkingen in uw gegevens kunt herkennen. Het biedt ook een perspectief weergave. Gebruik it om Maxi maal vier weer gaven te vergelijken van een of meer Azure Time Series Insights Gen2 omgevingen in één dash board. Het dash board geeft u een overzicht van de gegevens van uw tijd reeks op al uw locaties. Meer informatie over de [Azure time series Insights Gen2 Explorer](./concepts-ux-panels.md). Lees [Azure time series Insights Gen2-planning](./how-to-plan-your-environment.md)als u uw omgeving wilt plannen.

* De tweede manier om te beginnen is met behulp van de Java script SDK om snel krachtige grafieken en grafieken in uw webtoepassing in te sluiten. Met slechts een paar regels code kunt u krachtige query's ontwerpen. Gebruik deze om lijn diagrammen, cirkel diagrammen, staaf diagrammen, heatmap, gegevens rasters en meer te vullen. Al deze elementen zijn out-of-the-box met behulp van de SDK. De SDK is ook abstracties Azure Time Series Insights Gen2-query-Api's. U kunt ze gebruiken om SQL-achtige predikaten te schrijven om de gegevens op te vragen die u op een dash board wilt weer geven. Azure Time Series Insights Gen2 biedt voor hybride oplossingen voor presentaties met para meters Url's. Ze bieden naadloze verbindings punten met de Azure Time Series Insights Gen2 Explorer voor diepe Dives in gegevens.

  * Meer informatie over de [js-client bibliotheek](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) en de voor [beeld-client](https://github.com/Microsoft/tsiclient) documentatie voor meer info over de Java script SDK.

  * Meer informatie over het delen van Url's en de nieuwe gebruikers interface vindt u [in de Azure time series Insights Gen2 Explorer om visualiseren van gegevens](./concepts-ux-panels.md)te bekijken.

* De derde manier om te beginnen is het gebruik van de krachtige Api's om gegevens op te vragen die zijn opgeslagen in Azure Time Series Insights Gen2. Azure time series Insights Gen2 heeft tijdelijke Opera tors zoals `from` ,, en `to` `first` `last` . Het bevat aggregaties en trans formaties zoals,,,,, `average` `sum` `min` `max` `time-weighted average` `time-weighted sum` enzovoort. Daarnaast kunt u filteren, reken kundige en Booleaanse Opera Tors, scalaire functies, enzovoort. Al deze opera tors bieden downstream-toepassingen de mogelijkheid om snel interessante trends en patronen in uw gegevens te vinden. Gebruik ze om Homegrown-visualisaties te vullen om afwijkingen op te sporen.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Operationele analyse en efficiëntie van het proces

Gebruik Azure Time Series Insights Gen2 om de status, het gebruik en de prestaties van apparatuur op schaal te controleren en operationele efficiëntie te meten. Azure Time Series Insights Gen2 helpt bij het beheren van uiteenlopende en onvoorspelbare IoT-workloads zonder dat er opname-of query prestaties worden geoffert.

[![In de scherm afbeelding ziet u welke apparaten/toepassings gegevens, stroom verwerking, operationele efficiëntie, intelligentie/inzichten en geavanceerde analyses in Azure Time Series Insights Gen2 worden weer gegeven.](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

Streaming en doorlopende verwerking van gegevens die afkomstig zijn van operationele processen kunnen alle bedrijven omzetten als deze worden gecombineerd met de juiste technologie of oplossing. Vaak zijn deze oplossingen een combi natie van meerdere systemen. Ze maken het mogelijk om gegevens te verkennen en te analyseren die voortdurend worden gewijzigd, met name in de IoT-realm en een gemeen schappelijk patroon delen.

Deze patronen beginnen vaak met IoT-platformen die miljarden gebeurtenissen van apparaten en Sens oren die verschillende land instellingen omvatten. Deze systemen verwerken en analyseren streaminggegevens om real-time inzichten en acties af te leiden. Gegevens worden meestal opgeslagen in warme en koude opslag voor bijna realtime-en batch analyse.

Gegevens die worden verzameld, passeren een serie verwerkingen om deze op te schonen en waarmee voor downstream-query's en analyse scenario's. Azure biedt uitgebreide services die kunnen worden toegepast op IoT-scenario's zoals onderhoud en productie van activa. Deze services omvatten Azure Time Series Insights Gen2, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning en Power BI.

De oplossings architectuur kan op de volgende manier worden bereikt:

* Gegevens opnemen via IoT Hub of Event Hubs voor de beste beveiliging, door Voer en latentie.
* Gegevens verwerking en-berekeningen uitvoeren. Trechter opgenomen gegevens via Services als Stream Analytics, Logic Apps en Azure Functions. De service die u gebruikt, is afhankelijk van de specifieke behoeften voor gegevens verwerking.
* Berekende signalen van de verwerkings pijplijn worden gepusht naar Azure Time Series Insights Gen2 voor het opslaan en analyseren.

Azure Time Series Insights Gen2 biedt bijna realtime gegevens onderzoek en op activa gebaseerde inzichten op historische gegevens. Afhankelijk van de behoeften van uw bedrijf, kunnen MapReduce en Hive-taken worden uitgevoerd op gegevens die zijn opgeslagen in Azure Time Series Insights Gen2 door Azure Time Series Insights Gen2 te verbinden met Azure HDInsight. Gegevens die zijn opgeslagen in Azure Time Series Insights Gen2 zijn beschikbaar voor Power BI en andere klant toepassingen via de Azure Time Series Insights Gen2 open bare Surface-query-Api's. Deze gegevens kunnen worden gebruikt voor uitgebreide bedrijfs-en operationele informatie scenario's.

## <a name="advanced-analytics"></a>Geavanceerde analyse

Integreer met geavanceerde analyse services zoals Machine Learning en Azure Databricks. Azure Time Series Insights Gen2 ingresses onbewerkte gegevens van miljoenen apparaten. Het voegt contextuele gegevens toe die naadloos kunnen worden gebruikt door een suite van Azure Analytics Services.

[![Analyse](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

Geavanceerde analyses en machine learning grote hoeveel heden gegevens gebruiken en verwerken. Deze gegevens worden gebruikt om op gegevens gebaseerde beslissingen te nemen en voorspellende analyses uit te voeren. In IoT use cases leren geavanceerde analyse algoritmen van de gegevens die zijn verzameld van miljoenen apparaten. Met deze apparaten worden gegevens meerdere keren per seconde verzonden. De gegevens die zijn verzameld uit IoT-apparaten zijn onbewerkt. Er zijn geen contextuele informatie, zoals de locatie van het apparaat en de eenheid van het lezen van de sensor. Als gevolg hiervan zijn onbewerkte gegevens moeilijk te gebruiken voor geavanceerde analyses.

Azure Time Series Insights Gen2 overbrugt de kloof tussen IoT-gegevens en geavanceerde analyses op twee eenvoudige en rendabele manieren:

* Eerst verzamelt Azure Time Series Insights Gen2 ruwe telemetriegegevens van miljoenen apparaten met behulp van IoT Hub. Het verrijkt gegevens met contextuele informatie en transformeert gegevens in een Parquet-indeling. Deze indeling kan eenvoudig worden geïntegreerd met andere geavanceerde analyse Services, zoals Machine Learning, Azure Databricks en toepassingen van derden.

    Azure Time Series Insights Gen2 kan fungeren als de bron van waarheid voor alle gegevens in een organisatie. Hiermee maakt u een centrale opslag plaats voor downstream Analytics-workloads die moeten worden gebruikt. Omdat Azure Time Series Insights Gen2 een nabije real-time Storage-service is, kunnen geavanceerde analyse modellen zich continu leren van inkomende IoT-telemetriegegevens. Als gevolg hiervan kunnen de modellen nauw keurige voor spellingen maken.

* Ten tweede kan de uitvoer van machine learning en Voorspellings modellen worden ingevoerd in Azure Time Series Insights Gen2 om hun resultaten te visualiseren en op te slaan. Deze procedure helpt organisaties om hun modellen te optimaliseren en te verfijnen. Azure Time Series Insights Gen2 maakt het eenvoudig om gegevensstromende telemetriegegevens op hetzelfde vlak te visualiseren als de getrainde model uitvoer. Op deze manier helpt IT bij het herkennen van problemen met gegevens wetenschappen teams en het identificeren van patronen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Azure time series Insights Gen2 Explorer](./concepts-ux-panels.md).
* Lees [Azure time series Insights best practices voor Gen2](./how-to-plan-your-environment.md) om uw omgeving te plannen.
* Lees de documentatie van de [voorbeeld client](https://github.com/Microsoft/tsiclient) .
