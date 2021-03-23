---
title: OPC UA-gegevens visualiseren in Azure Time Series Insights
description: In deze zelf studie leert u hoe u gegevens kunt visualiseren met Time Series Insights.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 5bd218c0d94922b6137a964e3993f516216ca4b7
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787718"
---
# <a name="tutorial-visualize-data-with-time-series-insights-tsi"></a>Zelf studie: gegevens visualiseren met Time Series Insights (TSI)

De OPC Publisher-module maakt verbinding met OPC UA-servers en publiceert gegevens van deze servers naar IoT Hub. De telemetrie-processor in het industrieel IoT-platform verwerkt deze gebeurtenissen en stuurt contextuele voor beelden naar TSI en andere consumenten.  

In deze hand leiding wordt uitgelegd hoe u de OPC UA-telemetrie kunt visualiseren en analyseren met behulp van deze Time Series Insights-omgeving.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Alle zelf studies bevatten een lijst met een overzicht van de stappen die moeten worden voltooid
> * Elk van deze opsommings tekens wordt uitgelijnd op een belang rijke H2
> * Gebruik deze groene selectie vakjes in een zelf studie

## <a name="prerequisite"></a>Vereiste

* Implementeer het IIoT-platform om een Time Series Insights omgeving automatisch te maken
* De gegevens worden gepubliceerd naar IoT Hub

## <a name="time-series-insights-explorer"></a>Verkenner van Time Series Insights

De Time Series Insights Explorer is een web-app die u kunt gebruiken voor het visualiseren van uw telemetrie. Als u de URL van de toepassing wilt ophalen, opent u het bestand dat is `.env` opgeslagen als gevolg van de implementatie.  Open een browser naar de URL in de `PCS_TSI_URL` variabele.  

Voordat u de Time Series Insights Explorer kunt gebruiken, moet u toegang tot de TSI-gegevens verlenen aan de gebruikers die recht hebben op het visualiseren van de gegevens. Houd er rekening mee dat er bij een nieuwe implementatie geen beleid voor gegevens toegang is ingesteld, dus niemand kan de gegevens zien. Het beleid voor gegevens toegang moet worden ingesteld in de Azure Portal, in de Time Series Insights omgeving die is geïmplementeerd in de door het platform geïmplementeerde resource groep van IIoT, als volgt:

   ![Time Series Insights Explorer 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-1.png)

Selecteer het beleid voor gegevens toegang:

   ![Time Series Insights Explorer 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-2.png)

Wijs de vereiste gebruikers toe:

   ![Time Series Insights Explorer 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-3.png)


Bekijk in de TSI-Verkenner de niet-toegewezen time series-exemplaren. Een TSI-instantie komt overeen met de tijd/waarde-reeks voor een specifiek gegevens punt dat afkomstig is van een gepubliceerd knoop punt in een OPC-server. De TSI instantie, respectievelijk het OPC UA-gegevens punt, is uniek geïdentificeerd door de EndpointId, SubscriptionId en NodeId. De TSI-instantie modellen worden automatisch gedetecteerd en weer gegeven in de Explorer op basis van de telemetriegegevens die zijn opgenomen in de Event Hub van de telemetrie van het IIoT-platform.

   ![Time Series Insights Explorer 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-0.png)

De telemetriegegevens kunnen in het diagram worden weer gegeven door met de rechter muisknop op het TSI-exemplaar te klikken en de waarde te selecteren. Het tijds bestek dat in de grafiek moet worden gebruikt, kan worden aangepast in de rechter bovenhoek. De waarde van meerdere instanties kan worden gevisualiseerd op basis van de geselecteerde tijd.

Zie [Quick Start: Verken de Azure time series Insights preview](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart) voor meer informatie

## <a name="define-and-apply-a-new-model"></a>Een nieuw model definiëren en Toep assen

Omdat de telemetrie-instanties nu alleen in de RAW-indeling zijn, moeten ze worden gecontextd met de juiste 

Zie voor meer informatie over TSI-modellen [Time Series-model in azure time series Insights preview](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm)

1. Stap 1: Definieer een nieuwe hiërarchie voor de gegevensgestuurde telemetriegegevens op het tabblad model van de Explorer. Een hiërarchie is de logische boom structuur die de gebruiker in staat stelt de meta gegevens in te voegen die vereist zijn voor een meer intuïtieve navigatie via de TSI-instanties. een gebruiker kan hiërarchie sjablonen maken/verwijderen/wijzigen die later kunnen worden gemaakt voor de verschillende instanties van de TSI.

   ![Stap 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-1.png)

2. Stap 2: een nieuw type voor de waarden definiëren. In ons voor beeld verwerken we alleen numerieke gegevens typen

   ![Stap 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-2.png)

3. Stap 3: Selecteer de nieuwe TSI-instantie die moet worden gecategoriseerd in de eerder gedefinieerde hiërarchie

   ![Stap 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-3.png)

4. Stap 4: Vul de eigenschappen voor de instanties-naam, beschrijving, gegevens waarde en de hiërarchie velden in om de logische structuur te vergelijken 

   ![Stap 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-4.png)

5. Stap 5: Herhaal stap 5 voor alle niet-gecategoriseerde TSI-instanties

   ![Stap 5](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-5.png)

6. Stap 6-terug in de hoofd pagina van de TSI Explorer, door loop de hiërarchie voor gecategoriseerde instanties en selecteer de waarden voor de gegevens punten die moeten worden geanalyseerd

   ![Step6](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-6.png)

## <a name="connect-time-series-insights-to-power-bi"></a>Time Series Insights verbinden met Power BI

U kunt de Time Series Insights omgeving ook verbinden met Power BI.  Zie voor meer informatie [TSI koppelen om](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi) gegevens van de TSI te Power bi en te [visualiseren in Power bi](https://docs.microsoft.com/azure/time-series-insights/concepts-power-bi).


## <a name="next-steps"></a>Volgende stappen
Nu u hebt geleerd hoe u gegevens in TSI visualiseren, kunt u de industriële IoT GitHub-opslag plaats bekijken:

> [!div class="nextstepaction"]
> [IIoT platform GitHub-opslag plaats](https://github.com/Azure/iot-edge-opc-publisher)