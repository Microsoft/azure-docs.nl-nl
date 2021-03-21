---
title: Dash board bestellingen van partner centrum in de commerciële Marketplace-analyse | Microsoft AppSource en Azure Marketplace
description: Meer informatie over het openen van analytische rapporten over uw commerciële Marketplace-bestellingen in een grafische en download bare indeling.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: ed12e470f3f3d8c1035c1e4e2e0fa7a3b33e2369
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561378"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Dashboard Bestellingen in Commerciële marketplace-analyses

Dit artikel bevat informatie over het dash board orders in partner centrum. Dit dash board geeft informatie weer over uw orders, inclusief groei trends, die worden weer gegeven in een grafische en download bare indeling.

Als u toegang wilt krijgen tot het dash board orders in het partner centrum, **[](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** selecteert u onder **commerciële Marketplace** de optie  >  **Orders** analyseren.

>[!NOTE]
> Zie voor gedetailleerde definities van analyse terminologie [Commercial Marketplace Analytics terminologie en common questions](./analytics-faq.md)(Engelstalig).

## <a name="orders-dashboard"></a>Dashboard voor orders

In het dash board orders worden de huidige orders voor al uw SaaS-aanbiedingen (Software as a Service) weer gegeven. U kunt grafische weer gaven van de volgende items bekijken:

- Trend van orders
- Bestellingen per seat-en site trend
- Orders per aanbiedingen en Sku's
- Orders op geografie
- Tabel met gedetailleerde orders
- Pagina filters voor orders

> [!NOTE]
> De maximale latentie tussen de aanschaf en rapportage van klanten in het partner centrum is 48 uur.

## <a name="elements-of-the-orders-dashboard"></a>Elementen van het dash board orders

In de volgende secties wordt beschreven hoe u het dash board orders gebruikt en hoe u de gegevens kunt lezen.

### <a name="month-range"></a>Maand bereik

U vindt de selectie van een maand bereik in de rechter bovenhoek van elke pagina. Pas de uitvoer van de pagina **Orders** aan door een maand bereik te selecteren op basis van de afgelopen 6 of 12 maanden of door een aangepast maand bereik te selecteren met een maximale duur van 12 maanden. Het standaard bereik van de maand (reken periode) is zes maanden.

:::image type="content" source="./media/orders-dashboard/month-filters.png" alt-text="Illustreert de maand filters op het dash board orders.":::

> [!NOTE]
> Alle metrische gegevens in de visualisatie widgets en export rapporten voldoen aan de reken periode die door de gebruiker is geselecteerd.

### <a name="orders-trend"></a>Trend van orders

In deze sectie vindt u de grafiek **Orders** waarin de trend van uw actieve en geannuleerde orders voor de geselecteerde reken periode wordt weer gegeven. Metrische gegevens en groei trends worden aangegeven door een lijn diagram en de waarde voor elke maand wordt weer gegeven door de muis aanwijzer boven de lijn in de grafiek te plaatsen. De percentage waarde onder de metrische gegevens voor orders in de widget vertegenwoordigt de hoeveelheid groei of afwijzing tijdens de geselecteerde reken periode.

Er zijn twee orders tellers: _actief_ en _geannuleerd_.

- **Actief** is gelijk aan het aantal orders dat momenteel wordt gebruikt door klanten tijdens het geselecteerde datum bereik.
- **Geannuleerd** is gelijk aan het aantal orders dat eerder is gekocht en vervolgens geannuleerd tijdens het geselecteerde datum bereik.

[![Illustreert de object orders in het dash board Orders waarin de trend van actieve en geannuleerde orders wordt weer gegeven.](./media/orders-dashboard/orders-trend.png)](./media/orders-dashboard/orders-trend.png#lightbox)

### <a name="orders-by-per-seat-and-site-trend"></a>Bestellingen per seat-en site trend

Het lijn diagram **Orders per seat en op basis** van een site geeft de metrische gegevens en trend van de per-site SaaS-en per seat SaaS-orders die door klanten zijn aangeschaft (deze grafiek bevat geannuleerde orders).

[![Illustreert de object orders in het dash board Orders waarin de orders per seat en site trend worden weer gegeven.](./media/orders-dashboard/seats-sites.png)](./media/orders-dashboard/seats-sites.png#lightbox)

SaaS-aanbiedingen kunnen gebruikmaken van een van de twee prijs modellen voor elk abonnement: een vast tarief (op site gebaseerd) of per gebruiker (op basis van een seat).

- **Vast tarief**: Stel toegang tot uw aanbieding in met één maandelijks of jaarlijks vast tarief. Dit wordt soms ook wel site-gebaseerde prijzen genoemd.
- **Per gebruiker**: Schakel toegang tot uw aanbieding in met een prijs op basis van het aantal gebruikers dat toegang heeft tot de aanbieding of stoelen kan innemen. Met dit model op basis van gebruik kunt u het minimale en maximale aantal gebruikers instellen dat door het plan wordt ondersteund. U kunt meerdere plannen maken om verschillende prijs punten te configureren op basis van het aantal gebruikers. Deze velden zijn optioneel. Als deze optie niet is geselecteerd, wordt het aantal gebruikers geïnterpreteerd als een limiet van Mini maal 1 en Maxi maal zo lang uw service kan ondersteunen. Deze velden kunnen worden bewerkt als onderdeel van een update voor uw plan.
- **Factuur met data limiet**: bovenop de prijs van een vast tarief. Met dit prijs model kunt u optioneel plannen voor data limieten definiëren die gebruikmaken van de API voor de Marketplace-meter service om klanten in rekening te brengen voor gebruik dat niet wordt gedekt door het vaste tarief.

Zie [een SaaS-aanbieding plannen voor de commerciële Marketplace](plan-saas-offer.md)voor meer informatie over de locatie van de Seat, site en op basis van een Data limiet.

### <a name="orders-by-offers-and-skus"></a>Orders per aanbiedingen en Sku's

In het diagram orders per aanbiedingen en SKU worden de metingen en trends van alle aanbiedingen weer gegeven:

- De beste aanbiedingen worden weer gegeven in de grafiek en de overige aanbiedingen worden als **rest** gegroepeerd.
- U kunt specifieke aanbiedingen in de legenda selecteren om alleen die aanbieding en de bijbehorende Sku's in de grafiek weer te geven.
- Als u de muis aanwijzer boven een segment in de grafiek houdt, wordt het aantal orders en percentage van die aanbieding vergeleken met het totale aantal orders in alle aanbiedingen.
- De **trend orders per aanbiedingen** worden weer gegeven per maand trends in groei. De kolom maand vertegenwoordigt het aantal orders per aanbiedings naam. In het lijn diagram wordt de trend van het groei percentage weer gegeven die op een z-as is getekend.

:::image type="content" source="./media/orders-dashboard/orders-by-offers.png" alt-text="Illustreert het diagram orders per aanbiedingen in het dash board orders.":::

U kunt een wille keurige aanbieding en Maxi maal drie Sku's van de aanbieding selecteren om de trend van de maand over de maand voor de aanbieding, Sku's en seats weer te geven.

### <a name="orders-by-geography"></a>Orders op geografie

Voor de geselecteerde berekenings periode geeft de heatmap het totale aantal orders weer, en het groei percentage van nieuw toegevoegde orders tegen een geografie.  Het licht tot donkerere kleur op de kaart vertegenwoordigt de laag tot hoogste waarde van het aantal van de klant. Selecteer een record in de tabel om in te zoomen op een bepaald land of bepaalde regio.

[![Illustreert het geografische sprei ding diagram op het dash board orders.](./media/orders-dashboard/geographical-spread.png)](./media/orders-dashboard/geographical-spread.png#lightbox)

Houd rekening met het volgende:

- U kunt de kaart verplaatsen om de exacte locatie weer te geven.
- U kunt inzoomen op een specifieke locatie.
- De heatmap heeft een aanvullend raster voor het weer geven van de details van het aantal klanten, het aantal orders en de genormaliseerde gebruiks uren voor de specifieke locatie.
- U kunt zoeken en een land/regio in het raster selecteren om in te zoomen op de locatie in de kaart. Keer terug naar de oorspronkelijke weer gave door de knop **Start** te selecteren in de kaart.

### <a name="orders-details-table"></a>Tabel met Order Details

In de tabel Order Details wordt een genummerde lijst met de 1.000 belangrijkste orders gesorteerd op datum van aanschaf.

- Elke kolom in het raster is sorteerbaar.
- De gegevens kunnen worden geëxtraheerd naar een. CSV of. TSV-bestand als het aantal records kleiner is dan 1.000.
- Als records een getal van meer dan 1.000, worden geëxporteerde gegevens asynchroon in een download pagina voor de volgende 30 dagen geplaatst.
- Filters toep assen op de tabel **Order Details** om alleen de gegevens weer te geven waarin u bent geïnteresseerd. Filteren op land/regio, type Azure-licentie, licentie type voor commerciële Marketplace, type aanbieding, Bestel status, gratis sporen, abonnements-ID van de commerciële Marketplace, klant-ID en bedrijfs naam.
- Wanneer een bestelling door een beveiligde klant wordt aangeschaft, worden de gegevens in de **gedetailleerde gegevens** van de orders gemaskeerd (* * * * * * * * * * * *).

***Tabel 1: woorden lijst met gegevens termen***

| Kolom naam in<br>gebruikersinterface | Kenmerknaam | Definitie | Kolom naam in programma code<br>Access-rapporten |
| ------------ | ------------- | ------------- | ------------- |
| Marketplace-abonnements-ID | Marketplace-abonnements-ID | De unieke id die is gekoppeld aan het Azure-abonnement dat de klant heeft gebruikt om uw aanbieding voor commerciële Marketplace aan te schaffen. Voor de infra structuur biedt dit de Azure-abonnements-GUID van de klant. Voor SaaS-aanbiedingen wordt dit weer gegeven als nullen aangezien SaaS-aankopen geen Azure-abonnement nodig hebben. | Marketplace-abonnements-ID |
| MonthStartDate | Begin datum maand | De begin datum van de maand vertegenwoordigt de maand van de aankoop. De indeling is jjjj-mm-dd. | MonthStartDate |
| Type aanbieding | Type aanbieding | Het soort commerciële Marketplace-aanbieding. | Offer type |
| Azure-licentie type | Azure-licentie type | Het type licentie overeenkomst dat door klanten wordt gebruikt om Azure aan te schaffen. Ook wel bekend als Channel. De mogelijke waarden zijn:<ul><li>Cloud Solution Provider</li><li>Enterprise</li><li>Enter prise via wederverkoper</li><li>Betalen naar gebruik</li></ul> | AzureLicenseType |
| Marketplace-licentie type | Marketplace-licentie type | De facturerings methode van de aanbieding voor commerciële Marketplace. De verschillende waarden zijn:<ul><li>Gefactureerd via Azure</li><li>Neem uw eigen licentie mee</li><li>Gratis</li><li>Micro soft als wederverkoper</li></ul> | MarketplaceLicenseType |
| SKU | SKU | Het plan dat is gekoppeld aan de aanbieding | SKU |
| Klant land | Land/regio van klant | De land/regio naam die door de klant wordt verschaft. Land/regio kan afwijken van het land/de regio in het Azure-abonnement van een klant. | CustomerCountry |
| Is preview-SKU | Is preview-SKU | De waarde laat u weten of u de SKU hebt gelabeld als ' preview '. De waarde is "ja" als de SKU dienovereenkomstig is gelabeld en alleen Azure-abonnementen die door u zijn geautoriseerd, kunnen deze installatie kopie implementeren en gebruiken. De waarde is ' nee ' als de SKU niet is geïdentificeerd als ' preview '. | IsPreviewSKU |
| Order-id | Order-id | De unieke id van de klant bestelling voor uw commerciële Marketplace-service. Aanbiedingen op basis van het gebruik van virtuele machines zijn niet gekoppeld aan een order. | OrderId |
| Orderhoeveelheid | Orderhoeveelheid | Aantal activa dat is gekoppeld aan de order-ID voor actieve orders | OrderQuantity |
| Naam van Cloud exemplaar | Naam van Cloud exemplaar | De Microsoft Cloud waarin een VM-implementatie is opgetreden. | CloudInstanceName |
| Is nieuwe klant | Is nieuwe klant | De waarde geeft aan of een nieuwe klant voor de eerste keer een of meer van uw aanbiedingen heeft aangeschaft. De waarde is ' ja ' in dezelfde kalender maand voor ' verworven datum '. De waarde is "nee" als de klant een van de aanbiedingen vóór de gemelde kalender maand heeft gekocht. | IsNewCustomer |
| Orderstatus | Orderstatus | De status van een commerciële Marketplace-bestelling op het moment dat de gegevens voor het laatst zijn vernieuwd. | OrderStatus |
| Annulerings datum van bestelling | Annulerings datum van bestelling | De datum waarop de commerciële Marketplace-bestelling is geannuleerd. | OrderCancelDate |
| Bedrijfs naam van klant | Bedrijfs naam van klant | De bedrijfs naam die door de klant is verschaft. De naam kan afwijken van de plaats in het Azure-abonnement van een klant. | CustomerCompanyName |
| Aankoop datum van bestelling | Aankoop datum van bestelling | De datum waarop de commerciële Marketplace-order is gemaakt. De indeling is jjjj-mm-dd. | OrderPurchaseDate |
| Naam van aanbieding | Naam van aanbieding | De naam van de commerciële Marketplace-aanbieding. | OfferName |
| Eind datum van proef versie | Eind datum van proef versie | De datum waarop de proef periode voor deze order wordt beëindigd of beëindigd. | TrialEndDate |
| Klant-ID | Klant-ID | De unieke id die aan een klant is toegewezen. Een klant heeft mogelijk geen of meer Azure Marketplace-abonnementen. | CustomerId |
| Facturerings account-ID | Facturerings account-ID | De id van het account waarop de facturering wordt gegenereerd. Wijs de **facturerings account-id** toe aan **CustomerID** om uw rapport met de uitbetalings transacties te koppelen aan de klant-, order-en gebruiks rapporten. | BillingAccountId |
| AssetCount | Aantal assets | Het aantal assets dat is gekoppeld aan de order-ID. | Afgeschaft |
|||||

### <a name="orders-page-filters"></a>Pagina filters voor orders

De pagina met **Orders** wordt toegepast op het pagina niveau orders. U kunt een of meer filters selecteren om de grafiek te genereren voor de criteria die u wilt weer geven en de gegevens die u wilt zien in het raster Details van gedetailleerde orders/export. Filters worden toegepast op de gegevens die zijn geëxtraheerd voor het maand bereik dat u hebt geselecteerd in de rechter bovenhoek van de pagina Orders.

> [!TIP]
> U kunt het Download pictogram in de rechter bovenhoek van een wille keurige widget gebruiken om de gegevens te downloaden. U kunt feedback geven voor elk van de widgets door te klikken op het pictogram duim omhoog of duim omlaag.

## <a name="next-steps"></a>Volgende stappen

- Voor een overzicht van analyse rapporten die beschikbaar zijn in de commerciële Marketplace raadpleegt u [analytische rapporten voor de commerciële Marketplace in partner centrum](./partner-center-portal/analytics.md).
- Zie [Summary dash board in Commercial Marketplace Analytics](./summary-dashboard.md)voor grafieken, trends en waarden van statistische gegevens die de Marketplace-activiteiten voor uw aanbieding samenvatten.
- Voor informatie over uw orders in een grafische en download bare indeling raadpleegt u het [dash board orders in Commercial Marketplace Analytics](orders-dashboard.md).
- Voor virtuele machine (VM) zijn metrische gegevens over gebruik en gefactureerde facturering, Zie [gebruiks dashboard in commerciële Marketplace-analyses](./usage-dashboard.md).
- Zie [dash board downloaden in Commercial Marketplace Analytics](./partner-center-portal/downloads-dashboard.md)voor een lijst met Download aanvragen voor de afgelopen 30 dagen.
- Voor een geconsolideerde weer gave van feedback van klanten voor aanbiedingen op Azure Marketplace en AppSource raadpleegt u [beoordelingen & beoordelingen Analytics dash board in partner centrum](./partner-center-portal/ratings-reviews.md).
- Zie voor veelgestelde vragen over de analyse van commerciële Marketplace en voor een uitgebreid woorden boek met informatie over de [terminologie van commerciële Marketplace en veelgestelde vragen](./analytics-faq.md).