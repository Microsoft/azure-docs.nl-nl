---
title: Afwijkings detectie service voor facturering met data limiet-Microsoft Azure Marketplace
description: Hierin wordt beschreven hoe afwijkings detectie werkt, wanneer meldingen worden verzonden en wat u ermee kunt doen, en ondersteunings opties.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 06/10/2020
ms.openlocfilehash: 5ab57bcccb6f681f5c9282ef461181952ed5a679
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653071"
---
# <a name="anomaly-detection-service-for-metered-billing"></a>Afwijkingsdetectieservice voor facturering naar gebruik

Met de [Marketplace-meet service](marketplace-metering-service-apis-faq.md) kunt u aanbiedingen maken in het commerciële Marketplace-programma dat wordt gefactureerd op basis van niet-standaard eenheden. Met facturering met data limieten verzendt u gebruiks gebeurtenissen voor het gebruik van uw klant naar micro soft en wordt de facturering voor bereid op basis van het gebruik.

Onjuiste gebruiks gegevens kunnen afkomstig zijn uit verschillende oorzaken, zoals bugs, verkeerde configuratie in uw verbruiks tracking of fraude. Onjuiste gebruiks gegevens leiden tot onjuiste kosten voor klanten en facturerings geschillen.

Om het risico te beperken, past onze afwijkende detectie service machine learning algoritmen toe om het normale facturerings gedrag met data limiet te bepalen, het gebruik van de gefactureerde facturering te analyseren en afwijkingen te detecteren met minimale tussen komst van de gebruiker.

U ontvangt een melding als er sprake is van een afwijkend overzicht in het facturerings gebruik met data limiet. Dit biedt u de mogelijkheid om ons te onderzoeken en hiervan op de hoogte te stellen als er een afwijkend probleem is bevestigd, op welk punt acties kunnen worden uitgevoerd om het probleem van de klant facturering proactief te aanpakken.

Naast plotselinge pieken, spannings dips en trend veranderingen van het gebruik van gefactureerd facturerings model, hebben we ook accounts voor seizoen effecten gebruikt. Omdat facturering via data limiet wordt gecommuniceerd via overschrijding-gegevens, kan ons model ook een lange periode van ontbrekende gegevens verwerken.

Hieronder vindt u voor beelden van anomalie detectie resultaten. Het verwachte bereik wordt weer gegeven als een gele band. Een geaccepteerd factuur gebruik in de data limiet wordt weer gegeven als groene sterren in de band. Facturerings gebruik buiten de band wordt weer gegeven als een rode stip.  

Afwijkingen gedetecteerd buiten een voorspel bare trend:

![Illustreert afwijkingen die buiten een voorspel bare trend zijn gedetecteerd.](media/anomaly-1.png)

Afwijkingen gedetecteerd buiten een terugkerende cyclische trend:

![Illustreert afwijkingen die buiten een terugkerende cyclische trend zijn gedetecteerd.](media/anomaly-2.png)

Afwijkingen gedetecteerd in een opwaartse trend:

![Illustreert afwijkingen die in een opwaartse trend zijn gedetecteerd.](media/anomaly-3.png)

## <a name="how-anomaly-detection-service-works"></a>Hoe afwijkings detectie service werkt

Anomalie detectie wordt automatisch ingeschakeld voor al het facturerings gebruik met data limiet. Wanneer u de gebruiks gebeurtenissen naar micro soft verzendt, maakt de afwijkings detectie service een model van de verwachte waarden op basis van de gegevens in het verleden. Dit model wordt wekelijks uitgevoerd.

Anomalie detectie werkt op een niveau per meter en per klant. Dit betekent dat elke-meter met elke klant een model heeft getraind op basis van het vorige gebruiks patroon van deze klant van deze meter.

Het model werkt door het genereren van interval voor retroactieve vertrouwen. De time series-prognose is een gegeneraliseerd additief model dat bestaat uit een trend Voorspellings onderdeel en een seizoensgebonden onderdeel. Omdat het model is geformuleerd als een regressie taak, kan het een goede periode van ontbrekende gegevens op de juiste manier verwerken. Als een waarneming buiten de voorspelde betrouwbaarheids intervallen valt, betekent dit dat waarneming niet kan worden uitgelegd op basis van historische patronen van de facturering met data limieten. Dit kan daarom een afwijkend probleem zijn.

## <a name="anomaly-detection-notification"></a>Melding over afwijkings detectie

U kunt afwijkingen evalueren, beheren en bevestigen in het partner centrum. Zie [anomalie detectie voor factuur met data limiet voor](../anomaly-detection.md)meer informatie.

Om ervoor te zorgen dat uw klanten niet worden overbelast voor gebruik in de data limiet, moet u onderzoeken of gedetecteerde afwijkingen echte problemen zijn. Als dat het geval is, kunt u het onjuiste gebruik in partner centrum bevestigen.

U wordt aangeraden te bevestigen of gedetecteerde afwijkingen normaal gebruik zijn. Als u dit doet, worden de afwijkende gegevens die we aan u verstrekt, verbeterd. Als een afwijkend financieel risico vormt, kunnen we contact met u opnemen om het gebruik te bevestigen.

## <a name="when-and-how-to-get-support"></a>Wanneer en hoe u ondersteuning krijgt

Als u ons een onjuist gebruik van een Data limiet hebt gestuurd dat voor de klant in rekening is gebracht, zullen we geen factuur voor de klant initiëren voor gebruik onder het oog van het rapport of u betalen voor dat gebruik. Het verlies van inkomsten vanwege het te laag gemelde verbruik is voor uw rekening.

Als een van de volgende gevallen van toepassing is, kunt u het gebruiks bedrag in partner centrum aanpassen, wat leidt tot een terugbetaling of facturerings correctie voor uw klanten:

- U hebt bevestigd dat een van de gevonden afwijkingen een echt probleem is en het onjuiste gebruik zou leiden tot het overladen van de klant.
- U ontdekt dat u onjuist gebruik naar ons hebt verzonden en het onjuiste gebruik zou leiden tot een overbelasting van de klant.

Een ondersteunings ticket verzenden dat betrekking heeft op afwijkingen in de betalings wijze:

1. Meld u met uw werk account aan bij [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) .
1. Selecteer in het menu in de rechter bovenhoek van de pagina het **ondersteunings** pictogram. Het deel venster **Help en ondersteuning** wordt weer gegeven aan de rechter kant van de pagina.
1. Voor hulp bij de commerciële Marketplace selecteert u **commerciële Marketplace**.
   ![Illustreert het ondersteunings venster.](../media/support/commercial-marketplace-support-pane.png)
1. Voer in het vak **samen vatting van probleem** de **commerciële Marketplace in > factuur met data limiet**.
1. Selecteer in het vak **probleem type** een van de volgende opties:
    - **Commerciële Marketplace > factuur met data limiet > onjuist gebruik verzonden voor Azure Applications-aanbieding**
    - **Commerciële Marketplace > facturering via data limiet > onjuist gebruik voor SaaS-aanbieding**
1. Onder **volgende stap** selecteert u **oplossingen controleren**.
1. Bekijk de aanbevolen documenten, indien aanwezig of selecteer **probleem Details opgeven** om een ondersteunings ticket in te dienen.

Voor meer ondersteunings opties voor Publisher raadpleegt u [ondersteuning voor het programma voor commerciële Marketplace in Partner Center](../support.md).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Marketplace meter Service-API](marketplace-metering-service-apis.md).
- [Afwijkings detectie voor facturering met data limiet](../anomaly-detection.md)
