---
title: Uw gen1-omgeving plannen-Azure Time Series Insights | Microsoft Docs
description: Aanbevolen procedures voor het voorbereiden, configureren en implementeren van uw Azure Time Series Insights gen1-omgeving.
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 5e0f1ea42aa2ba888b89dd652d3397a3a2163a3e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016204"
---
# <a name="plan-your-azure-time-series-insights-gen1-environment"></a>Uw Azure Time Series Insights gen1-omgeving plannen

> [!CAUTION]
> Dit is een Gen1-artikel.

In dit artikel wordt beschreven hoe u uw Azure Time Series Insights gen1-omgeving plant op basis van uw verwachte Ingangs frequentie en de vereisten voor het bewaren van gegevens.

## <a name="video"></a>Video

**Bekijk deze video voor meer informatie over het bewaren van gegevens in azure time series Insights en hoe u deze kunt plannen**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Aanbevolen procedures

Om aan de slag te gaan met Azure Time Series Insights, is het het beste als u weet hoeveel gegevens u naar verwachting wilt pushen per minuut en hoe lang uw gegevens moeten worden opgeslagen.  

Lees [Azure time series Insights prijzen](https://azure.microsoft.com/pricing/details/time-series-insights/)voor meer informatie over capaciteit en retentie voor beide Azure time series Insights sku's.

Houd rekening met de volgende kenmerken als u uw Azure Time Series Insights omgeving voor een lange termijn wilt best uren:

- [Opslagcapaciteit](#storage-capacity)
- [Bewaarperiode van gegevens](#data-retention)
- [Ingangs capaciteit](#ingress-capacity)
- [Uw evenementen omvormen](#shape-your-events)
- [Controleren of er referentie gegevens aanwezig zijn](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>Opslagcapaciteit

Standaard worden gegevens door Azure Time Series Insights bewaard op basis van de hoeveelheid opslag ruimte die u hebt ingericht (eenheden &#215; de hoeveelheid opslag per eenheid) en binnenhalen.

## <a name="data-retention"></a>Bewaartijd voor gegevens

U kunt de instelling voor de **Bewaar tijd van gegevens** in uw Azure time series Insights omgeving wijzigen. U kunt Maxi maal 400 dagen retentie inschakelen.

Azure Time Series Insights heeft twee modi:

- De ene modus optimaliseert voor de meest actuele gegevens. Er wordt een beleid afgedwongen om **oude gegevens te verwijderen** die recente gegevens verlaten die beschikbaar zijn bij het exemplaar. Deze modus is standaard ingeschakeld.
- De overige optimaliseert gegevens zodat deze onder de geconfigureerde Bewaar limieten blijven. Door **ingangen te onderbreken** voor komt u dat nieuwe gegevens worden uitgeschakeld wanneer de **opslag limiet is overschreden**.

U kunt de Bewaar periode aanpassen en scha kelen tussen de twee modi op de configuratie pagina van de omgeving in het Azure Portal.

> [!IMPORTANT]
> U kunt Maxi maal 400 dagen aan gegevens bewaaring configureren in uw Azure Time Series Insights gen1-omgeving.

### <a name="configure-data-retention"></a>Gegevensretentie configureren

1. Selecteer uw Time Series Insights omgeving in het [Azure Portal](https://portal.azure.com).

1. Selecteer in het deel venster **Time Series Insights omgeving** onder **instellingen** de optie **opslag configuratie**.

1. Voer in het vak **tijdstip van bewaren van gegevens (in dagen)** een waarde in tussen 1 en 400.

   [![Bewaar periode configureren](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> Lees voor meer informatie over het implementeren van een geschikt Bewaar beleid voor gegevens [het configureren van Bewaar periode](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Ingangs capaciteit

[!INCLUDE [Azure Time Series Insights Gen1 limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>Omgeving plannen

Het tweede gebied waarop u zich kunt richten voor het plannen van uw Azure Time Series Insights-omgeving, is de capaciteit van ingang. De dagelijkse ingang van de opslag en de gebeurtenis capaciteit wordt gemeten per minuut, in blokken van 1 KB. De Maxi maal toegestane pakket grootte is 32 KB. Gegevens pakketten groter dan 32 KB worden afgekapt.

U kunt de capaciteit van een S1-of S2-SKU verhogen tot 10 eenheden in één omgeving. U kunt niet migreren van een S1-omgeving naar een S2. U kunt niet migreren van een S2-omgeving naar een S1.

Voor ingangs capaciteit bepaalt u eerst het totale aantal binnenkomend dat u per maand nodig hebt. Bepaal vervolgens wat uw behoeften per minuut zijn.

Beperking en latentie spelen een rol in capaciteit per minuut. Als u een Prikker hebt in uw gegevens die minder dan 24 uur duren, kan Azure Time Series Insights een ingangs snelheid hebben van twee keer de tarieven die in de voor gaande tabel worden weer gegeven.

Als u bijvoorbeeld een enkele S1-SKU hebt, hebt u gegevens binnenkomen met een snelheid van 720 gebeurtenissen per minuut, en de gegevens snelheids pieken voor minder dan een uur met een snelheid van 1.440 gebeurtenissen of minder, er is geen merkbaar latentie in uw omgeving. Als u echter meer dan één uur 1.440 gebeurtenissen per minuut overschrijdt, zal er waarschijnlijk een latentie optreden in gegevens die worden gevisualiseerd en beschikbaar zijn voor query's in uw omgeving.

Mogelijk weet u niet vooraf hoeveel gegevens u naar verwachting pusht. In dit geval kunt u gegevens-telemetrie voor [azure IOT hub](../iot-hub/monitor-iot-hub.md) en [Azure Event hubs](/archive/blogs/cloud_solution_architect/using-the-azure-rest-apis-to-retrieve-event-hub-metrics) vinden in uw Azure Portal-abonnement. U kunt de telemetrie gebruiken om te bepalen hoe uw omgeving moet worden ingericht. Gebruik het deel venster **metrische gegevens** in de Azure portal voor de betreffende gebeurtenis bron om de telemetrie weer te geven. Als u de metrische gegevens van uw gebeurtenis bron begrijpt, kunt u uw Azure Time Series Insights omgeving effectiever plannen en inrichten.

### <a name="calculate-ingress-requirements"></a>Ingangs vereisten berekenen

U kunt als volgt uw ingangs vereisten berekenen:

- Controleer of uw ingangs capaciteit hoger is dan het gemiddelde per minuut en dat uw omgeving groot genoeg is voor het afhandelen van uw verwachte ingangen die gelijk zijn aan die van twee maal de capaciteit van minder dan een uur.

- Als ingangs pieken optreden die langer dan 1 uur duren, gebruik dan het piek getal als uw gemiddelde. Richt een omgeving in met de capaciteit voor het afhandelen van het piek tempo.

### <a name="mitigate-throttling-and-latency"></a>Beperkende beperking en latentie

Lees [beperkende latentie en beperking](time-series-insights-environment-mitigate-latency.md)voor informatie over het voor komen van beperking en latentie.

## <a name="shape-your-events"></a>Uw gebeurtenissen vorm geven

Het is belang rijk om ervoor te zorgen dat de manier waarop u gebeurtenissen naar Azure Time Series Insights verzendt, de grootte van de omgeving die u wilt inrichten ondersteunt. (U kunt de grootte van de omgeving daarentegen toewijzen aan het aantal gebeurtenissen Azure Time Series Insights Lees bewerkingen en de grootte van elke gebeurtenis.) Het is ook belang rijk om na te denken over de kenmerken die u wilt gebruiken om te segmenteren en te filteren wanneer u een query op uw gegevens uitvoert.

> [!TIP]
> Raadpleeg de documentatie over JSON-vormgeving bij het [verzenden van gebeurtenissen](time-series-insights-send-events.md).

## <a name="ensure-that-you-have-reference-data"></a>Controleer of u referentie gegevens hebt

Een *referentie gegevensset* is een verzameling items waarmee de gebeurtenissen van uw gebeurtenis bron worden uitgebreid. De Azure Time Series Insights ingangs engine voegt elke gebeurtenis uit uw gebeurtenis bron samen met de overeenkomstige gegevensrij in uw referentie gegevensset. De uitgebreide gebeurtenis is vervolgens beschikbaar voor query's. De koppeling is gebaseerd op de **primaire-sleutel** kolommen die in de referentie gegevensset zijn gedefinieerd.

> [!NOTE]
> Referentie gegevens worden niet met terugwerkende kracht samengevoegd. Alleen huidige en toekomstige ingangs gegevens worden vergeleken en samengevoegd met de referentie gegevensset nadat deze is geconfigureerd en geüpload. Als u van plan bent om een grote hoeveelheid historische gegevens naar Azure Time Series Insights te verzenden en niet eerst te uploaden of referentie gegevens in Azure Time Series Insights te maken, moet u uw werk mogelijk opnieuw uitvoeren (Hint: niet leuk).  

Lees onze [documentatie over referentie gegevensset](time-series-insights-add-reference-data-set.md)voor meer informatie over het maken, uploaden en beheren van uw referentie gegevens in azure time series Insights.

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag door [een nieuwe Azure time series Insights-omgeving te maken in de Azure Portal](time-series-insights-get-started.md).

- Meer informatie over het [toevoegen van een event hubs gebeurtenis bron](./how-to-ingest-data-event-hub.md) aan Azure time series Insights.

- Meer informatie over het [configureren van een IOT hub gebeurtenis bron](./how-to-ingest-data-iot-hub.md).