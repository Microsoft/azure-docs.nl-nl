---
title: Uw Gen2-omgeving plannen-Azure Time Series Insights | Microsoft Docs
description: Aanbevolen procedures voor het configureren, beheren, plannen en implementeren van uw Azure Time Series Insights Gen2-omgeving.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: c8d96d7b5952c020493af278ee1ea8ad5ff46716
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95016782"
---
# <a name="plan-your-azure-time-series-insights-gen2-environment"></a>Uw Azure Time Series Insights Gen2-omgeving plannen

In dit artikel worden aanbevolen procedures beschreven voor het plannen en snel aan de slag met Azure Time Series Insights Gen2.

## <a name="best-practices-for-planning-and-preparation"></a>Aanbevolen procedures voor planning en voor bereiding

De aanbevolen procedures voor het plannen en voorbereiden van uw omgeving worden verder beschreven in de volgende artikelen:

* Wat u krijgt wanneer u [een Azure time series Insights Gen2-omgeving inricht](#the-gen2-environment).
* Wat uw [tijd reeks-id's en time stamp-eigenschappen zijn](#configure-time-series-ids-and-timestamp-properties).
* Wat het nieuwe [Time Series-model is](#understand-the-time-series-model)en hoe u er een kunt maken.
* Het [efficiënt verzenden van gebeurtenissen in JSON](#shape-your-events).
* Azure Time Series Insights [Opties voor herstel na nood](#business-disaster-recovery)geval.

Azure Time Series Insights maakt gebruik van een zakelijk model voor betalen per gebruik. Lees [Azure time series Insights prijzen](https://azure.microsoft.com/pricing/details/time-series-insights/)voor meer informatie over kosten en capaciteit.

## <a name="the-gen2-environment"></a>De Gen2 omgeving

Wanneer u een Azure Time Series Insights Gen2-omgeving inricht, maakt u twee Azure-resources:

* Een Azure Time Series Insights Gen2-omgeving
* Een Azure Storage-account

Als onderdeel van het inrichtings proces geeft u op of u een warme Store wilt inschakelen. Warme Store biedt u een gelaagde query-ervaring. Wanneer deze functie is ingeschakeld, moet u een Bewaar periode tussen 7 en 30 dagen opgeven. Query's die in de warme Bewaar periode worden uitgevoerd, bieden doorgaans snellere reactie tijden. Wanneer een query de Bewaar periode van het warme archief overschrijdt, wordt deze uit de koude Store bediend.

Query's in de warme Store zijn gratis, terwijl query's voor de koude opslag kosten in rekening worden gebracht. Het is belang rijk om inzicht te krijgen in uw query patronen en uw warme archief configuratie dienovereenkomstig te plannen. We raden u aan om interactieve analyses uit te voeren op de meest recente gegevens in uw warme archief-en patroon analyse en op lange termijn trends.

> [!NOTE]
> Lees de [API-verwijzing](/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters)voor meer informatie over het uitvoeren van een query op uw warme gegevens.

Als u wilt beginnen, hebt u drie extra items nodig:

* Een [Time Series-model](./concepts-model-overview.md)
* Een [gebeurtenis bron die is verbonden met Time Series Insights](./concepts-streaming-ingestion-event-sources.md)
* [Gebeurtenissen die in de gebeurtenis bron stromen](./time-series-insights-send-events.md) die beide zijn toegewezen aan het model en in een geldige JSON-indeling zijn

## <a name="review-azure-time-series-insights-gen2-limits"></a>Azure Time Series Insights limieten voor Gen2 controleren

[!INCLUDE [Review Azure Time Series Insights Gen2 limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Time Series-Id's en time stamp-eigenschappen configureren

Selecteer een tijd reeks-ID om een nieuwe Azure Time Series Insights omgeving te maken. Als u dit doet, fungeert dit als een logische partitie voor uw gegevens. Zorg ervoor dat uw time series-Id's gereed zijn.

> [!IMPORTANT]
> Time Series-Id's *kunnen later niet worden gewijzigd*. Verifieer elk één voor de uiteindelijke selectie en het eerste gebruik.

U kunt Maxi maal drie sleutels selecteren om uw resources uniek te onderscheiden. Lees [Aanbevolen procedures voor het kiezen van een tijd reeks-id](./how-to-select-tsid.md) en [opname regels](concepts-json-flattening-escaping-rules.md)voor meer informatie.

De **tijds tempel** eigenschap is ook belang rijk. U kunt deze eigenschap aanwijzen wanneer u gebeurtenis bronnen toevoegt. Elke gebeurtenis bron heeft een optionele time stamp-eigenschap die wordt gebruikt om gebeurtenis bronnen na verloop van tijd bij te houden. Time Stamp-waarden zijn hoofdletter gevoelig en moeten worden opgemaakt als afzonderlijke specificatie van elke bron van de gebeurtenis.

Als u niets opgeeft, wordt het tijdstip waarop de gebeurtenis in de IoT Hub of event hub in de wachtrij is geplaatst, gebruikt als tijds tempel van de gebeurtenis. Over het algemeen moeten gebruikers ervoor kiezen om de tijds tempel eigenschap aan te passen en de tijd te gebruiken waarop de sensor of tag het lezen heeft gegenereerd, in plaats van de hub in de tijd. Lees voor meer informatie over tijd zone-offsets de [tijds tempel](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp)van de gebeurtenis.

## <a name="understand-the-time-series-model"></a>Meer informatie over het time series-model

U kunt nu het time series-model van uw Azure Time Series Insights omgeving configureren. Het nieuwe model maakt het eenvoudig om IoT-gegevens te vinden en analyseren. Hiermee wordt de inschakeling, het onderhoud en de verrijking van tijdreeks gegevens mogelijk en helpt u gegevens sets te voorbereiden. Het model maakt gebruik van Time Series-Id's, die zijn toegewezen aan een exemplaar dat de unieke resource koppelt aan variabelen, zoals typen en hiërarchieën. Lees meer informatie over het overzicht van [Time Series-modellen](./concepts-model-overview.md) .

Het model is dynamisch, zodat het op elk gewenst moment kan worden gebouwd. Als u snel aan de slag wilt gaan, bouwt en uploadt u deze voordat u gegevens naar Azure Time Series Insights pusht. Lees [het time series-model gebruiken](./concepts-model-overview.md)om uw model te bouwen.

Voor veel klanten is het time series-model gekoppeld aan een bestaand Asset model of ERP-systeem dat al aanwezig is. Als u geen bestaand model hebt, wordt een vooraf ontwikkelde gebruikers ervaring [gegeven](https://github.com/Microsoft/tsiclient) om snel aan de slag te gaan. Als u wilt bepalen hoe een model u kan helpen, bekijkt u de voor [beeld-demo omgeving](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Uw gebeurtenissen vorm geven

U kunt controleren hoe u gebeurtenissen naar Azure Time Series Insights verzendt. In het ideale geval zijn uw gebeurtenissen ongebruikelijk en efficiënt.

Een goede vuist regel:

* Sla meta gegevens op in uw time series-model.
* Zorg ervoor dat de modus Time Series, instance Fields en Events alleen de benodigde informatie bevat, zoals een tijd reeks-ID of eigenschap time stamp.

Lees de [regels voor json-afvlakking en-Escapes](./concepts-json-flattening-escaping-rules.md)voor meer informatie en om te begrijpen hoe gebeurtenissen worden afgevlakt en opgeslagen.

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Volgende stappen

* Bekijk [Azure Advisor](../advisor/advisor-overview.md) om de configuratie opties voor uw bedrijfs herstel te plannen.
* Bekijk [Azure Advisor](../advisor/advisor-overview.md) om de configuratie opties voor uw bedrijfs herstel te plannen.
* Meer informatie over [gegevens opname](./concepts-ingestion-overview.md) vindt u in azure time series Insights Gen2.
* Raadpleeg het artikel over [gegevens opslag](./concepts-storage.md) in azure time series Insights Gen2.
* Meer informatie over [gegevens modellering](./concepts-model-overview.md) in azure time series Insights Gen2.