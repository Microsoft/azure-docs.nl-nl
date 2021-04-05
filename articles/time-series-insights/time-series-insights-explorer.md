---
title: Gegevens verkennen met behulp van de Explorer-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over het gebruik van de Azure Time Series Insights Explorer om uw IoT-gegevens weer te geven.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 639f2ef12d190a56e04a9b48d96ea0a6537d243c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95020109"
---
# <a name="azure-time-series-insights-gen1-explorer"></a>Azure Time Series Insights gen1 Explorer

> [!CAUTION]
> Dit is een Gen1-artikel.

In dit artikel worden de functies en opties beschreven voor de [Web-app](https://insights.timeseries.azure.com/)Azure time series Insights gen1 Explorer. De Azure Time Series Insights Explorer demonstreert de krachtige functies voor gegevens visualisatie die door de service worden geboden en kunnen worden geopend in uw eigen omgeving.

Azure Time Series Insights is een volledig beheerde service voor analyse, opslag en visualisatie die het eenvoudig maakt om miljarden IoT-gebeurtenissen tegelijkertijd te verkennen en te analyseren. Het biedt u een wereld wijde weer gave van uw gegevens, waarmee u uw IoT-oplossing snel kunt valideren en dure uitval tijd kunt vermijden voor bedrijfskritische apparaten. U kunt verborgen trends ontdekken, afwijkings afwijkingen en hoofd oorzaken van analyses in vrijwel realtime uitvoeren.

> [!TIP]
> Lees de [Azure time series Insights Snelstartgids](time-series-quickstart.md)voor een rond leiding door de demonstratie omgeving.

## <a name="video"></a>Video

### <a name="learn-about-querying-data-by-using-the-azure-time-series-insights-explorer-br"></a>Meer informatie over het opvragen van gegevens met behulp van de Azure Time Series Insights Explorer. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Speel de voor gaande video <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">' aan de slag met Azure time series Insights met een Azure IOT-oplossings versneller '.</a>

## <a name="prerequisites"></a>Vereisten

Voordat u Azure Time Series Insights Explorer kunt gebruiken, moet u het volgende doen:

- Een Azure Time Series Insights-omgeving maken. Lees voor meer informatie [hoe u aan de slag gaat met Azure time series Insights](./time-series-insights-get-started.md).
- [Bied toegang](./concepts-access-policies.md) tot uw account in de omgeving.
- Voeg een [IOT-hub](./how-to-ingest-data-iot-hub.md) of [Event hub](./how-to-ingest-data-event-hub.md) gebeurtenis bron toe.

## <a name="explore-and-query-data"></a>Gegevens verkennen en opvragen

Binnen enkele minuten nadat u uw gebeurtenis bron hebt verbonden met uw Azure Time Series Insights-omgeving, kunt u uw time series-gegevens verkennen en opvragen.

1. Open de [Azure time series Insights Explorer](https://insights.timeseries.azure.com/) in uw webbrowser om te starten. Selecteer een omgeving aan de linkerkant van het venster. Alle omgevingen waartoe u toegang hebt, worden in alfabetische volg orde weer gegeven.

1. Nadat u een omgeving hebt geselecteerd, gebruikt u de configuraties **van** en **tot aan** de bovenkant of selecteert u de gewenste tijds periode en sleept u deze. Selecteer het vergroot glas in de rechter bovenhoek of klik met de rechter muisknop op de geselecteerde time span en selecteer **zoeken**.

1. U kunt de beschik baarheid ook elke minuut automatisch vernieuwen door de knop **automatisch aan** te selecteren. De knop **automatisch aan** is alleen van toepassing op de beschik baarheids grafiek, niet op de inhoud van de hoofd visualisatie.

1. Met het pictogram van de Azure-Cloud gaat u naar uw omgeving in het Azure Portal.

   [![Selectie van Azure Time Series Insights omgeving](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. Vervolgens wordt een grafiek weer gegeven met een telling van alle gebeurtenissen tijdens de geselecteerde tijds periode. Hier hebt u een aantal besturings elementen:

    - **Paneel voor termen editor**: de term ruimte is de locatie waar u een query op uw omgeving uitvoert. Deze bevindt zich aan de linkerkant van het scherm:
      - **Meting**: in deze vervolg keuzelijst worden alle numerieke kolommen (**dubbele** waarden) weer gegeven.
      - **Splitsen op**: deze vervolg keuzelijst bevat categorische kolommen (**teken reeksen**).
      - U kunt de stap interpolatie inschakelen, het minimum en maximum weer geven en de y-as van het configuratie scherm naast **meting** aanpassen. U kunt ook aanpassen of de weer gegeven gegevens een aantal, een gemiddelde of een som van de gegevens zijn.
      - U kunt Maxi maal vijf termen toevoegen die op dezelfde x-as worden weer gegeven. Selecteer **toevoegen** om een nieuwe term toe te voegen of gebruik de knop **deze term klonen** om een kopie van een bestaande term toe te voegen.

        [![Voor waarden selecteren, filteren en query paneel](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **Predikaat**: gebruik het predicaat om snel uw gebeurtenissen te filteren met behulp van de set operands die in de volgende tabel worden weer gegeven. Als u een zoek opdracht uitvoert door te selecteren of te klikken, wordt het predikaat automatisch bijgewerkt op basis van die zoek opdracht. Ondersteunde typen operanden zijn onder andere:

         |Bewerking  |Ondersteunde typen  |Notities  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Double**, **DateTime**, **time span**       |         |
         |**=**, **!=**, **<>**     | **Teken reeks**, **BOOL**, **Double**, **DateTime**, **time span**, **Null**        |         |
         |**Naast**     | **Teken reeks**, **BOOL**, **Double**, **DateTime**, **time span**, **Null**        |  Alle operanden moeten van hetzelfde type zijn of **Null** -constante zijn.        |
         |**BIEDT**     | **Tekenreeks**        |  Aan de rechter kant zijn alleen letterlijke teken reeksen van constanten toegestaan. Lege teken reeks en **Null** zijn niet toegestaan.       |

      - **Voorbeelden van query's**

         [![Voor beeld van gen1-query's](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. U kunt de schuif regelaar voor **interval grootte** gebruiken om in en uit te zoomen op dezelfde tijds duur. De schuif regelaar biedt een nauw keurigere controle over de verplaatsing tussen grote tijd segmenten die soepele trends weer geven naar segmenten zo klein als de milliseconde, waarmee u nauw keurige delen van uw gegevens met hoge resolutie kunt weer geven en analyseren. Het standaard start punt van de schuif regelaar is ingesteld als de meest optimale weer gave van de gegevens uit uw selectie om de balans resolutie, de query snelheid en de granulatie te bekijken.

1. Met het gereedschap **tijd penseel** kunt u eenvoudig navigeren van de ene span naar een andere.

1. Selecteer het pictogram **Opslaan** om de huidige query op te slaan en deze te delen met andere gebruikers van de omgeving. Wanneer u het pictogram **openen** selecteert, kunt u al uw opgeslagen query's en eventuele gedeelde query's van andere gebruikers bekijken in omgevingen waar u toegang tot hebt.

   [![Query's](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>Gegevens visualiseren

1. Gebruik het hulp programma **perspectief weergave** voor een gelijktijdige weer gave van Maxi maal vier unieke query's. De **perspectief weergave** knop bevindt zich in de rechter bovenhoek van de grafiek.

   [![Query's selecteren om toe te voegen aan het deel venster perspectief](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. Bekijk een grafiek om uw gegevens visueel te verkennen en gebruik de hulp middelen voor **grafieken** :

    - **Selecteer** of **Klik op** een specifieke time span of een enkele gegevens reeks.
    - Binnen een time span-selectie kunt u gebeurtenissen in-en uitzoomen of bekijken.
    - Binnen een gegevens reeks kunt u de reeks opsplitsen in een andere kolom, de reeks toevoegen als nieuwe term, alleen de geselecteerde reeks weer geven, de geselecteerde reeks uitsluiten, de reeks pingen of de gebeurtenissen uit de geselecteerde reeks verkennen.
    - In het filter gebied links van de grafiek kunt u alle weer gegeven gegevens reeksen bekijken en de volg orde wijzigen op basis van waarde of naam. U kunt ook alle gegevens reeksen of vastgemaakte of niet-vastgemaakte reeksen weer geven. U kunt één gegevens reeks selecteren en de reeks splitsen op basis van een andere kolom, de reeks toevoegen als nieuwe term, alleen de geselecteerde reeks weer geven, de geselecteerde reeks uitsluiten, de reeks vastmaken of gebeurtenissen uit de geselecteerde reeks verkennen.
    - Wanneer u meerdere voor waarden tegelijk bekijkt, kunt u de stack, de extra gegevens van een gegevens reeks stapelen, weer geven en dezelfde y-as voor alle termen gebruiken. Gebruik de knoppen in de rechter bovenhoek van de grafiek.

    [![Opties voor de rechter bovenhoek van het hulp programma grafieken](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. Gebruik de **heatmap** om snel unieke of afwijkende gegevens reeksen in een bepaalde query op te sporen. Er kan slechts één zoek term worden gevisualiseerd als een heatmap.

    [Time Series Insights Explorer heatmap grafieken](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)] (media/Time-Series-Insights-Explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. Wanneer u gebeurtenissen bekijkt door te selecteren of met de rechter muisknop te klikken, wordt het deel venster **gebeurtenissen** beschikbaar gesteld. Hier kunt u al uw onbewerkte gebeurtenissen controleren en uw gebeurtenissen exporteren als JSON-of CSV-bestanden. Azure Time Series Insights worden alle onbewerkte gegevens opgeslagen.

    [![Gebeurtenissen](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. Selecteer het tabblad **Statistieken** nadat u gebeurtenissen hebt bekeken om patronen en kolom statistieken weer te geven.

    - **Patronen**: met deze functie worden de meest statistische significante patronen in een geselecteerd gegevens gebied proactief geoppereerd. U hoeft geen duizenden gebeurtenissen te bekijken om te begrijpen welke patronen de meeste tijd en energie vereisen. Met Azure Time Series Insights kunt u rechtstreeks naar deze statistisch significante patronen gaan om een analyse te blijven uitvoeren. Deze functie is ook handig voor het naslachten van historische gegevens.
    - **Kolom statistieken**: kolom statistieken bieden grafieken en tabellen die gegevens opsplitsen van elke kolom van de geselecteerde gegevens reeks over de geselecteerde time span.

      [![Kolom diagram en opties voor statistieken](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

U hebt nu geleerd over de belangrijkste functies, configuratie-instellingen en weergave opties die beschikbaar zijn in de Web-App van Azure Time Series Insights Explorer.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [vaststellen en oplossen van problemen](time-series-insights-diagnose-and-solve-problems.md) in uw Azure time series Insights omgeving.

- Volg de rond leiding door de begeleide [Azure time series Insights Snelstartgids](time-series-quickstart.md) .