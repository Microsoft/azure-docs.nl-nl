---
title: Samenvattings dashboard voor partner centrum-analyses in commerciële Marketplace
description: Meer informatie over het openen van grafieken, trends en waarden van statistische gegevens die Marketplace-activiteiten samenvatten in het dash board samen vatting van het partner centrum.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: c02ba12c790d745904d241f121e269aac3ed12f6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462983"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Dashboard Samenvatting in Commerciële marketplace-analyses

Dit artikel bevat informatie over het dash board samen vatting in partner centrum. In dit dash board worden grafieken, trends en waarden van statistische gegevens weer gegeven waarin Marketplace-activiteiten voor uw aanbiedingen worden samenvatten.

Voor toegang tot het dash board samen vatting in Partner Center, onder **commerciële Marketplace** , selecteert u **[Analyze](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **samen vatting** analyseren.

>[!NOTE]
> Zie voor gedetailleerde definities van analyse terminologie [Commercial Marketplace Analytics terminologie en common questions](./analytics-faq.md)(Engelstalig).

## <a name="summary-dashboard"></a>Dashboard voor samenvattingen

Het dash board samen vatting biedt een overzicht van de bedrijfs prestaties van Azure Marketplace en Microsoft AppSource. Het dash board biedt een breed overzicht van het volgende:

- Orders van klanten
- Customers
- Gebruik door klanten van de aanbiedingen
- Bezoeken aan de pagina van klanten in azure Marketplace en AppSource

## <a name="elements-of-the-summary-dashboard"></a>Elementen van het dash board samen vatting

In de volgende secties wordt beschreven hoe u het dash board samen vatting gebruikt en hoe u de gegevens kunt lezen.

### <a name="month-range"></a>Maand bereik

U vindt de selectie van een maand bereik in de rechter bovenhoek van elke pagina. De uitvoer van de **overzichts** pagina grafieken aanpassen door een maand bereik te selecteren op basis van de afgelopen 3, 6 of 12 maanden of door een aangepast maand bereik te selecteren met een maximale duur van 12 maanden. Het standaard bereik van de maand (reken periode) is zes maanden.

:::image type="content" source="./media/summary-dashboard/summary-dashboard.png" alt-text="Illustreert de opties voor het maandelijkse bereik in het dash board samen vatting.":::

> [!NOTE]
> Alle metrische gegevens in de visualisatie widgets en export rapporten voldoen aan de reken periode die door de gebruiker is geselecteerd.

### <a name="orders-widget"></a>De widget orders

De object orders in het dash board samen vatting * * geeft de huidige orders voor al uw op Transact gebaseerde aanbiedingen. De object orders bevat een aantal en trend van alle aangeschafte orders (met uitzonde ring van geannuleerde orders) voor de geselecteerde reken periode. De percentage waarde **Orders** vertegenwoordigt de groei hoeveelheid tijdens de geselecteerde reken periode.

[![Illustreert de object orders in het dash board samen vatting.](./media/summary-dashboard/orders-widget.png)](./media/summary-dashboard/orders-widget.png#lightbox)


U kunt ook naar het rapport Orders gaan door in de linkerbenedenhoek van de widget de koppeling orders van het **dash board** te selecteren.

### <a name="customers-widget"></a>Widget klanten

De widget **klanten** van het dash board **samen vatting** toont het totale aantal klanten dat uw aanbiedingen voor de geselecteerde reken periode heeft aangeschaft. De widget klanten geeft een telling en trend weer van het totale aantal actieve (inclusief nieuwe en bestaande) klanten (met uitzonde ring van klanten met de gebruikers ervaring) voor de geselecteerde reken periode. De percentage waarde onder **klanten** vertegenwoordigt de groei hoeveelheid tijdens de geselecteerde reken periode.

[![Illustreert de widget klanten op het dash board samen vatting.](./media/summary-dashboard/customers-widget.png)](./media/summary-dashboard/customers-widget.png#lightbox)

U kunt ook naar het rapport gedetailleerde klanten gaan door de **Dashboard koppeling klanten** te selecteren in de linkerbenedenhoek van de widget.

### <a name="usage-widget"></a>Widget gebruik

De widget **Usage** van het dash board **samen vatting** bevat de totale genormaliseerde en onbewerkte gebruiks uren voor alle Azure Virtual Machine-aanbiedingen (VM). De widget Usage geeft een aantal en trend van het totale gebruik voor de geselecteerde reken periode weer.

De tabel gebruiks overzicht bevat de gebruiks uren van de klant voor alle aanbiedingen die ze hebben gekocht.

- Genormaliseerde gebruiks uren worden gedefinieerd als het gebruik van uren die zijn genormaliseerd voor het aantal VM-kernen ([aantal VM-kernen] x [uur van onbewerkt gebruik]). Vm's die zijn aangewezen als "SHAREDCORE" gebruiken 1/6 (of 0,1666) als [aantal VM-kernen] vermenigvuldiger.
- Onbewerkte gebruiks uren worden gedefinieerd als de hoeveelheid tijd waarop Vm's zijn uitgevoerd in het kader van uren.

De percentage waarde onder het totale gebruiks uren vertegenwoordigt de hoeveelheid groei in gebruiks uren tijdens de geselecteerde reken periode.

[![Illustreert de widget Usage in het dash board samen vatting.](./media/summary-dashboard/usage-widget.png)](./media/summary-dashboard/usage-widget.png#lightbox)

U kunt ook naar het gebruiks rapport gaan door in de linkerbenedenhoek van de widget de koppeling **gebruiks dashboard** te selecteren.

### <a name="marketplace-insights"></a>Marketplace Insights

Marketplace Insights toont het aantal gebruikers dat de pagina's van uw aanbiedingen heeft bezocht in azure Marketplace en AppSource. **Aantal bezoeken op pagina** geeft een overzicht van de webanalyses voor commerciële Marketplace waarmee uitgevers de klant betrokkenheid kunnen meten voor hun respectievelijke product detail pagina's die worden vermeld op de online winkels voor commerciële marketplace: Microsoft AppSource en Azure Marketplace. Deze widget geeft een telling en trend van de totale pagina bezoeken tijdens de geselecteerde reken periode.

[![Illustreert het object aantal bezoeken van de pagina in het dash board samen vatting.](./media/summary-dashboard/page-visit-count.png)](./media/summary-dashboard/page-visit-count.png#lightbox)

U kunt ook naar het Marketplace Insights-rapport gaan door de koppeling **Marketplace Insights-dash board** te selecteren in de linkerbenedenhoek van de widget.

### <a name="geographical-spread"></a>Geografische sprei ding

Voor de geselecteerde berekenings periode geeft de heatmap het totale aantal klanten, orders en genormaliseerde gebruiks tijden weer op geografische dimensie.

:::image type="content" source="./media/summary-dashboard/geo-spread.png" alt-text="Illustreert de widget landen verspreiden in het dash board samen vatting.":::

en let op het volgende:

- U kunt de kaart verplaatsen om de exacte locatie weer te geven.
- U kunt inzoomen op een specifieke locatie.
- De heatmap heeft een aanvullend raster voor het weer geven van de details van het aantal klanten, het aantal orders en de genormaliseerde gebruiks uren voor de specifieke locatie.
- U kunt zoeken en een land/regio in het raster selecteren om in te zoomen op de locatie in de kaart. Keer terug naar de oorspronkelijke weer gave door de knop **Start** te selecteren in de kaart.

> [!TIP]
> U kunt het Download pictogram in de rechter bovenhoek van een wille keurige widget gebruiken om de gegevens te downloaden. U kunt feedback geven voor elk van de widgets door het pictogram ' duim omhoog ' of ' duim omlaag ' te selecteren.

## <a name="next-steps"></a>Volgende stappen

- Voor een overzicht van analyse rapporten die beschikbaar zijn in de commerciële Marketplace raadpleegt u [analytische rapporten voor de commerciële Marketplace in partner centrum](./partner-center-portal/analytics.md).
- Voor informatie over uw orders in een grafische en download bare indeling raadpleegt u het [dash board orders in Commercial Marketplace Analytics](orders-dashboard.md).
- Voor virtuele machine (VM) zijn metrische gegevens over gebruik en gefactureerde facturering, Zie [gebruiks dashboard in commerciële Marketplace-analyses](usage-dashboard.md).
- Zie [klanten dashboard in Commercial Marketplace Analytics](customer-dashboard.md)voor gedetailleerde informatie over uw klanten, waaronder groei trends.
- Zie [dash board downloaden in Commercial Marketplace Analytics](./partner-center-portal/downloads-dashboard.md)voor een lijst met Download aanvragen voor de afgelopen 30 dagen.
- Voor een geconsolideerde weer gave van feedback van klanten voor aanbiedingen op Azure Marketplace en AppSource raadpleegt u [beoordelingen & beoordelingen Analytics dash board in partner centrum](./partner-center-portal/ratings-reviews.md).
- Zie voor veelgestelde vragen over de analyse van commerciële Marketplace en voor een uitgebreid woorden boek met informatie over de [terminologie van commerciële Marketplace en veelgestelde vragen](./analytics-faq.md).