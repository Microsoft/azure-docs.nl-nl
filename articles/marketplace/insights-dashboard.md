---
title: Dashboard Inzichten marketplace in Commerciële marketplace-analyses
description: Krijg toegang tot een overzicht van Marketplace Web Analytics in Partner Center, waarmee u de betrokkenheid van klanten in Microsoft AppSource en Azure Marketplace kunt meten.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: a8a53a54d30df9a2f7079d705f754e554328c3f7
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553932"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>Dashboard Inzichten marketplace in Commerciële marketplace-analyses

Dit artikel bevat informatie over het Marketplace Insights-dash board in het partner centrum. In dit dash board wordt een samen vatting weer gegeven van webanalysen voor commerciële Marketplace waarmee uitgevers de klant betrokkenheid kunnen meten voor hun respectieve product detail pagina's die worden vermeld in de online winkels voor commerciële Marketplace: Microsoft AppSource en Azure Marketplace.

Voor toegang tot het **Marketplace Insights** -dash board in Partner Center, onder commerciële Marketplace, selecteert u **[](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **Marketplace Insights** analyseren.

Zie voor gedetailleerde definities van analyse terminologie [Commercial Marketplace Analytics terminologie en common questions](./analytics-faq.md)(Engelstalig).

## <a name="marketplace-insights-dashboard"></a>Dashboard voor Marketplace-inzichten

Het Marketplace Insights-dash board biedt een overzicht van de bedrijfs prestaties van Azure Marketplace en AppSource. Dit dash board biedt een breed overzicht van het volgende:

- Trend van pagina bezoekers
- Actie trend aanroepen
- Pagina bezoeken en aanroepen naar acties voor aanbiedingen, referentie domeinen en campagne-Id's
- Marketplace-inzichten op geografie
- Informatie tabel voor Marketplace Insights

Het Marketplace Insights-dash board biedt clickstream-gegevens, die niet moeten worden gecorreleerd met leads die zijn gegenereerd in het eind punt van de lead bestemming.

> [!NOTE]
> De maximale latentie tussen gebruikers die aanbiedingen bezoeken op Azure Marketplace of AppSource en rapportage in het partner centrum is 48 uur.

## <a name="elements-of-the-marketplace-insights-dashboard"></a>Elementen van het Marketplace Insights-dash board

Het Marketplace Insights-dash board geeft details weer voor de Web-telemetrie voor Azure Marketplace en AppSource op twee afzonderlijke tabbladen. In de volgende secties wordt beschreven hoe u het Marketplace Insights-dash board gebruikt en hoe u de gegevens kunt lezen.

### <a name="month-range"></a>Maand bereik

U vindt de selectie van een maand bereik in de rechter bovenhoek van elke pagina. Pas de uitvoer van de pagina grafieken van **Marketplace Insights** aan door een maand bereik te selecteren op basis van de afgelopen 6 of 12 maanden of door een aangepast maand bereik te selecteren met een maximale duur van 12 maanden. Het standaard bereik van de maand (reken periode) is zes maanden.

:::image type="content" source="./media/insights-dashboard/month-filters.png" alt-text="Illustreert de maand filters op het Marketplace Insights-dash board.":::

> [!NOTE]
> Alle metrische gegevens in de visualisatie widgets en export rapporten voldoen aan de reken periode die door de gebruiker is geselecteerd.

### <a name="page-visits-trends"></a>Trends in pagina bezoekers

Het organigram Insights- **bezoekers** diagram geeft een telling weer van de _pagina bezoeken_ en _unieke bezoekers_ voor de geselecteerde reken periode.

**Pagina bezoekers**: dit nummer geeft het aantal afzonderlijke gebruikers sessies op de aanbiedings pagina (Product detail pagina) voor een geselecteerde reken periode aan. De indica toren rood en groen vertegenwoordigen het groei percentage van de pagina bezoeken. Het trend diagram vertegenwoordigt het aantal maanden tot maanden van pagina bezoeken.

**Unieke bezoekers**: dit nummer vertegenwoordigt het aantal afzonderlijke bezoekers tijdens de geselecteerde reken periode voor de aanbieding (en) in azure Marketplace en AppSource. Een bezoeker die een of meer product detail pagina's heeft bezocht, wordt als één unieke bezoeker beschouwd.

[![Illustreert de bezoekers grafiek op het Marketplace Insights-dash board.](./media/insights-dashboard/visitors.png)](./media/insights-dashboard/visitors.png#lightbox)

### <a name="call-to-actions-trend"></a>Actie trend aanroepen

Dit nummer geeft het aantal aanroepen van de knop **actie aan die** is voltooid op de pagina aanbiedings vermelding (Product detail pagina). _Aanroepen naar actie_ worden geteld wanneer gebruikers de knoppen **nu downloaden**, **gratis proef versie**, **contact opnemen** of **station testen** selecteren.

[![Illustreert de oproep naar actie grafiek op het Marketplace Insights-dash board.](./media/insights-dashboard/call-to-actions-trend.png)](./media/insights-dashboard/call-to-actions-trend.png#lightbox)

### <a name="page-visits-and-call-to-actions-against-offers-referral-domains-and-campaign-ids"></a>Pagina bezoeken en aanroepen naar acties voor aanbiedingen, referentie domeinen en campagne-Id's

**Referentie domeinen**: als u een specifiek referentie domein selecteert, ziet u de maandelijkse trend van pagina bezoeken en aanroepen naar actie op de grafiek aan de rechter kant.

:::image type="content" source="./media/insights-dashboard/referral-domain.png" alt-text="Illustreert het overzicht van het domein Referral op het Marketplace Insights-dash board.":::

**Aanbiedingen**: Selecteer een specifieke aanbieding om de maandelijkse trend van de pagina bezoeken te bekijken en aanroepen naar actie op de grafiek aan de rechter kant.

:::image type="content" source="./media/insights-dashboard/offer-alias.png" alt-text="Illustreert het diagram van de aanbiedings alias op het Marketplace Insights-dash board.":::

**Campagne-id's**: door een specifieke campagne-id te selecteren, moet u inzicht hebben in het succes van de campagne. Voor elke campagne moet u de maandelijkse trend van de pagina bezoeken en aanroepen naar actie aan de rechter kant kunnen zien.

:::image type="content" source="./media/insights-dashboard/campaign.png" alt-text="Illustreert het campagne diagram op het Marketplace Insights-dash board.":::

### <a name="marketplace-insights-by-geography"></a>Marketplace-inzichten op geografie

Voor de geselecteerde reken periode toont het heatmap het aantal bezoeken aan de pagina, unieke bezoekers en aanroepen naar actie (CTA). Het licht tot donkerere kleur op de kaart duidt op de laag tot hoge waarde van de unieke bezoekers. Selecteer een record in de tabel om in te zoomen op een land/regio.

:::image type="content" source="./media/insights-dashboard/geographical-spread.png" alt-text="Illustreert het geografische sprei ding diagram op het Marketplace Insights-dash board.":::

Houd rekening met het volgende:

- U kunt de kaart verplaatsen om de exacte locatie weer te geven.
- U kunt inzoomen op een specifieke locatie.
- De heatmap heeft een aanvullend raster voor het weer geven van de details van het aantal klanten, het aantal orders en de genormaliseerde gebruiks uren op de specifieke locatie.
- U kunt zoeken en een land/regio in het raster selecteren om in te zoomen op de locatie in de kaart. Keer terug naar de oorspronkelijke weer gave door de knop **Start** te selecteren in de kaart.

### <a name="marketplace-insights-details-table"></a>Informatie tabel voor Marketplace Insights

Deze tabel bevat een overzicht van de bezoeken van de pagina en de aanroepen naar actie van de geselecteerde aanbiedingen pagina's gesorteerd op datum.

- De gegevens kunnen worden geëxtraheerd naar een. TSV of. CSV-bestand als het aantal records kleiner is dan 1.000.
- Als het aantal records meer dan 1.000 is, worden geëxporteerde gegevens asynchroon in een download pagina voor de volgende 30 dagen geplaatst.
- Filter gegevens op naam van aanbieding en campagne namen om de gegevens weer te geven waarin u geïnteresseerd bent.

> [!TIP]
> U kunt het Download pictogram in de rechter bovenhoek van een wille keurige widget gebruiken om de gegevens te downloaden. U kunt feedback geven voor elk van de widgets door te klikken op het pictogram duim omhoog of duim omlaag.

| Kolom naam in<br>gebruikersinterface | Kenmerknaam | Definitie | Kolom naam in programma code<br>Access-rapporten |
| ------------ | ------------- | ------------- | ------------- |
| Datum | Datum van bezoek | De datum van de pagina ga en/of CTA Klik op gebeurtenis genereren op de pagina van de aanbieding in azure Marketplace en/of AppSource. | Datum |
| Naam van aanbieding | Naam van aanbieding | De naam van de commerciële Marketplace-aanbieding. | OfferName |
| Referentie domein | Referentie domein | De naam van het referentie domein van waaruit de pagina zich voordeed. Als er geen referentie domeinen zijn vastgelegd voor de pagina bezoek, is het bijbehorende item ' referentie domein niet aanwezig '. |  ReferralDomain |
| Naam van land/regio | Naam van land/regio | De naam van het land van waaruit het bezoek aan de pagina is gebeurd. | Landnaam |
| Pagina bezoeken | Pagina bezoeken | Het aantal pagina bezoeken dat is gekoppeld aan de naam van het aanbod voor een bepaalde datum. | PageVisits |
| Nu downloaden | Nu downloaden | Het aantal klikken naar de CTA ' nu downloaden ' op de pagina van de aanbieding voor een bepaalde datum. | GetItNow |
| Contact opnemen | Contact opnemen | Het aantal klikken naar de CTA voor contact persoon op de pagina van de aanbieding voor een bepaalde datum. | ContactMe |
| Test Drive | Test Drive | Het aantal klikken naar de CTA ' test drive ' op de pagina van de aanbieding voor een bepaalde datum. | TestDrive |
| Gratis proefversie | Gratis proefversie | Het aantal klikken naar de CTA voor een gratis proef versie op de pagina van de aanbieding voor een bepaalde datum. | Gratis proef versie |
|||||

## <a name="next-steps"></a>Volgende stappen

- Voor een overzicht van analyse rapporten die beschikbaar zijn in de commerciële Marketplace raadpleegt u [analytische rapporten voor de commerciële Marketplace in partner centrum](./partner-center-portal/analytics.md).
- Voor informatie over uw orders in een grafische en download bare indeling raadpleegt u het [dash board orders in Commercial Marketplace Analytics](./orders-dashboard.md).
- Voor virtuele machine (VM) zijn metrische gegevens over gebruik en gefactureerde facturering, Zie [gebruiks dashboard in commerciële Marketplace-analyses](./usage-dashboard.md).
- Zie [klanten dashboard in Commercial Marketplace Analytics](./customer-dashboard.md)voor gedetailleerde informatie over uw klanten, waaronder groei trends.
- Zie [dash board downloaden in Commercial Marketplace Analytics](./partner-center-portal/downloads-dashboard.md)voor een lijst met Download aanvragen voor de afgelopen 30 dagen.
- Voor een geconsolideerde weer gave van feedback van klanten voor aanbiedingen op Azure Marketplace en AppSource raadpleegt u [beoordelingen & beoordelingen Analytics dash board in partner centrum](./partner-center-portal/ratings-reviews.md).
- Zie voor veelgestelde vragen over de analyse van commerciële Marketplace en voor een uitgebreid woorden boek met informatie over de [terminologie van commerciële Marketplace en veelgestelde vragen](./analytics-faq.md).