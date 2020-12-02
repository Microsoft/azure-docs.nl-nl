---
title: Klanten dashboard in micro soft Commercial Marketplace Analytics op partner centrum, Azure Marketplace en Microsoft AppSource
description: Lees hoe u toegang krijgt tot informatie over uw klanten, met inbegrip van groei trends, met behulp van het dash board klanten in commerciële Marketplace-analyses.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 878fa181ff09d547ef6594a238e9c1d883cc89b7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460488"
---
# <a name="customers-dashboard-in-commercial-marketplace-analytics"></a>Dash board klanten in commerciële Marketplace-analyse

Dit artikel bevat informatie over het dash board klanten in partner centrum. Dit dash board geeft informatie weer over uw klanten, met inbegrip van groei trends, die worden weer gegeven in een grafische en download bare indeling.

Voor toegang tot het dash board klanten in Partner Center, onder **commerciële Marketplace** , selecteert u **[Analyze](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **klanten** analyseren.

>[!NOTE]
> Zie voor gedetailleerde definities van analyse terminologie [Commercial Marketplace Analytics terminologie en common questions](./analytics-faq.md)(Engelstalig).

## <a name="customers-dashboard"></a>Klantendashboard

Het dash board klanten geeft gegevens weer voor klanten die uw aanbiedingen hebben aangeschaft. U kunt grafische weer gaven van de volgende items bekijken:

- Actieve en geverloopde trend van klanten
- Trend van klant groei, inclusief bestaande, nieuwe en geafnemerde klanten
- Klanten per orders en gebruik
- Klant percentiel 
- Klant type per orders en gebruik
- Klanten op geografie
- Tabel met details van klanten
- Pagina filters voor klanten

> [!NOTE]
> De maximale latentie tussen de aanschaf en rapportage van klanten in het partner centrum is 48 uur.

## <a name="elements-of-the-customers-dashboard"></a>Elementen van het dash board klanten

In de volgende secties wordt beschreven hoe u het dash board klanten gebruikt en hoe u de gegevens kunt lezen.

### <a name="month-range"></a>Maand bereik

U vindt de selectie van een maand bereik in de rechter bovenhoek van elke pagina. Pas de uitvoer van de grafieken van de **klanten** pagina aan door een maand bereik te selecteren op basis van de afgelopen 6 of 12 maanden of door een aangepast maand bereik te selecteren met een maximale duur van 12 maanden. Het standaard bereik van de maand (reken periode) is zes maanden.

:::image type="content" source="./media/customer-dashboard/month-range-filters.png" alt-text="Illustreert de maand filters op de pagina klanten.":::

> [!NOTE]
> Alle metrische gegevens in de visualisatie widgets en export rapporten voldoen aan de reken periode die door de gebruiker is geselecteerd.

### <a name="active-and-churned-customers-trend"></a>Actieve en geverloopde trend van klanten

In deze sectie vindt u de groei trend van uw klanten voor de geselecteerde reken periode. Metrische gegevens en groei trends worden weer gegeven met een lijn diagram en de waarde voor elke maand wordt weer gegeven door de muis aanwijzer boven de lijn in de grafiek te plaatsen. De percentage waarde onder **actieve klanten** in de widget vertegenwoordigt de groei hoeveelheid tijdens de geselecteerde reken periode. In de volgende scherm afbeelding ziet u bijvoorbeeld een groei van 0,92% voor de geselecteerde reken periode.

[![Illustreert de widget klanten op de pagina klanten.](./media/customer-dashboard/customers-widget.png)](./media/customer-dashboard/customers-widget.png#lightbox)

Er zijn drie _typen klanten_: nieuw, bestaand en overlopend.

- Een nieuwe klant heeft een of meer van uw aanbiedingen voor de eerste keer in de geselecteerde maand opgehaald.
- Een bestaande klant heeft een of meer van uw aanbiedingen aangeschaft vóór de maand geselecteerd.
- Een bestaande klant heeft alle aanbiedingen die eerder zijn gekocht, geannuleerd. Gebruikers met een verloop datum worden weer gegeven op de negatieve as in de object trend.

### <a name="customer-growth-trend-including-existing-new-and-churned-customers"></a>Trend van klant groei, inclusief bestaande, nieuwe en geafnemerde klanten

In deze sectie vindt u de trend en het aantal klanten, met inbegrip van nieuwe, bestaande en geverloopte, met een groei trend per maand.

- Het lijn diagram geeft het totale percentage aan klant groei.
- De kolom month vertegenwoordigt het aantal klanten dat is gestapeld door nieuwe, bestaande en geafnemerde klanten.
- Het aantal aangestuurde klanten wordt weer gegeven op de negatieve richting van de X-as.
- U kunt specifieke legenda-items selecteren om meer gedetailleerde weer gaven weer te geven. Selecteer bijvoorbeeld nieuwe klanten in de legenda om alleen nieuwe klanten weer te geven.
- Bij het aanwijzen van een kolom in het diagram worden alleen Details voor die maand weer gegeven.

[![Illustreert de widget voor trend van klanten op de pagina klanten.](./media/customer-dashboard/customers-trend.png)](./media/customer-dashboard/customers-trend.png#lightbox)

### <a name="customers-by-ordersusage"></a>Klanten op orders/gebruik

De tabel **klanten per orders/gebruik** heeft drie tabbladen: orders, genormaliseerd gebruik en onbewerkt gebruik. Selecteer het tabblad **Orders** om de details van de order weer te geven.

:::image type="content" source="./media/customer-dashboard/customers-by-orders-usage.png" alt-text="Illustreert het tabblad orders van de widget klanten per orders en gebruik op de pagina klanten.":::

en let op het volgende:

- Het Leader Board geeft details weer van de klanten gerangschikt op Bestel aantal. Nadat u een klant hebt geselecteerd, worden de details van de klant weer gegeven in de secties ' Details ', ' orders per Sku's ' en ' Sku's per seat '.
- De details van het klant profiel worden in deze ruimte weer gegeven wanneer uitgevers zijn aangemeld met een rol van eigenaar. Als uitgevers zijn aangemeld met een rol Inzender, is de informatie in deze sectie niet beschikbaar.
- In de grafiek **Orders per sku's** ring wordt de uitsplitsing weer gegeven van de orders die zijn gekocht voor plannen. De vijf meest voorkomende plannen met het hoogste aantal orders worden weer gegeven, terwijl de rest van de bestellingen worden gegroepeerd onder **rest all**.
- In de grafiek **sku's per seat** ring wordt de uitsplitsing van de bestelde stoelen voor plannen weer gegeven. De vijf meest voorkomende plannen met de hoogste seats worden weer gegeven, terwijl de rest van de orders worden gegroepeerd onder **rest all**.

U kunt ook het tabblad **genormaliseerd gebruik** of **onbewerkte** gebruik selecteren om gebruiks gegevens weer te geven.

- Het Leader Board geeft details weer van de klanten die zijn gerangschikt op het aantal gebruiks tijden. Nadat u een klant hebt geselecteerd, worden de details van de klant weer gegeven in de sectie naastgelegen "Details", "genormaliseerd gebruik door aanbiedingen" en "genormaliseerd gebruik door virtuele machine (VM) grootte (s)".
- De details van het klant profiel worden in deze ruimte weer gegeven wanneer uitgevers zijn aangemeld met een rol van eigenaar. Als uitgevers zijn aangemeld met een rol Inzender, is de informatie in deze sectie niet beschikbaar.
- Het **genormaliseerde gebruik per biedt** ring diagram toont de uitsplitsing van het gebruik dat door de aanbiedingen wordt verbruikt. De vijf meest voorkomende plannen met het hoogste gebruiks aantal worden weer gegeven, terwijl de rest van de aanbiedingen worden gegroepeerd onder **rest all**.
- In de grafiek **genormaliseerd gebruik door VM-grootte (s)** is de uitsplitsing van het gebruik dat wordt verbruikt door verschillende VM-grootte (s) weer gegeven. De bovenste vijf VM-grootten met het hoogste genormaliseerde gebruik worden weer gegeven, terwijl de rest van het gebruik wordt gegroepeerd onder **rest all**.

### <a name="top-customers-percentile"></a>Belangrijkste klanten percentiel

Het percentiel diagram voor de **beste klanten** heeft drie tabbladen: ' orders ', ' genormaliseerd gebruik ' en ' onbewerkt gebruik '. Het _bovenste klant percentiel_ wordt weer gegeven langs de x-as, zoals wordt bepaald door het aantal orders. Op de y-as wordt het aantal orders van de klant weer gegeven. Op de secundaire y-as (lijn diagram) wordt het cumulatieve percentage van het totale aantal orders weer gegeven. U kunt details weer geven door boven punten in het lijn diagram te bewegen.

[![Illustreert het tabblad orders van het bovenste object percentiel van de klant op de pagina klanten.](./media/customer-dashboard/top-customer-percentile.png)](./media/customer-dashboard/top-customer-percentile.png#lightbox)

### <a name="customer-type-by-orders-and-usage"></a>Klant type per orders en gebruik

In het diagram **Orders/gebruik per klant type** wordt het aantal orders, genormaliseerd gebruik en de openstaande verbruiks uren gesplitst tussen nieuwe klanten en bestaande klanten. op basis van de geselecteerde orders, genormaliseerd en onbewerkt gebruik in de grafiek.

In dit diagram ziet u het volgende:

- Een nieuwe klant heeft voor het eerst binnen dezelfde kalender maand (y-as) een of meer van uw aanbiedingen of gerapporteerd gebruik verkregen.
- Een bestaande klant heeft eerder een aanbieding van u of gerapporteerd gebruik aangeschaft vóór de kalender maand gerapporteerd (op de y-as).

[![Illustreert het tabblad orders van de widget orders per klant type op de pagina klanten.](./media/customer-dashboard/orders-by-customer-type.png)](./media/customer-dashboard/orders-by-customer-type.png#lightbox)

### <a name="customers-by-geography"></a>Klanten op geografie

Voor de geselecteerde berekenings periode geeft de heatmap het totale aantal klanten en het percentage recent toegevoegde klanten af op geografische dimensie. Het licht tot donkerere kleur op de kaart vertegenwoordigt de laag tot hoogste waarde van het aantal van de klant. Selecteer een record in de tabel om in te zoomen op een land of regio.

[![Illustreert het tabblad orders van de widget orders per geografie op de pagina klanten.](./media/customer-dashboard/customers-by-geography.png)](./media/customer-dashboard/customers-by-geography.png#lightbox)

en let op het volgende:

- U kunt de kaart verplaatsen om de exacte locatie weer te geven.
- U kunt inzoomen op een specifieke locatie.
- De heatmap heeft een aanvullend raster voor het weer geven van de details van het aantal klanten, het aantal orders, genormaliseerde gebruiks uren op de specifieke locatie.
- U kunt zoeken en een land/regio in het raster selecteren om in te zoomen op de locatie in de kaart. Keer terug naar de oorspronkelijke weer gave door de knop **Start** te selecteren in de kaart.

### <a name="customer-details-table"></a>Tabel Klant gegevens

In de tabel **klant gegevens** wordt een genummerde lijst met de top 1.000-klanten weer gegeven, gesorteerd op de datum waarop ze voor het eerst een van uw aanbiedingen hebben verkregen. U kunt een sectie uitbreiden door het uitbreidings pictogram op het lint details te selecteren.

en let op het volgende:

- Persoonlijke gegevens van de klant zijn alleen beschikbaar als de klant toestemming heeft gegeven. U kunt deze informatie alleen weer geven als u bent aangemeld met een eigenaars bevoegdheids niveau.
- Elke kolom in het raster is sorteerbaar.
- De gegevens kunnen worden geëxtraheerd naar een. CSV of. TSV-bestand als het aantal records kleiner is dan 1.000.
- Als het record nummer meer is dan 1.000, worden geëxporteerde gegevens asynchroon in een download pagina voor de volgende 30 dagen geplaatst.
- Filters toep assen op de tabel om alleen de gegevens weer te geven waarin u bent geïnteresseerd. Gegevens filteren op bedrijfs naam, klant-ID, Marketplace-abonnements-ID, Azure-licentie type, datum verkregen, verloren datum, klant-e-mail, land/regio/staat/plaats/post code, klant taal, enzovoort.
- Wanneer een aanbieding wordt gekocht door een beveiligde klant, worden de gegevens van de **gedetailleerde gegevens** van de klant gemaskeerd (* * * * * * * * * * * *).
- Details van klant dimensies, zoals de bedrijfs naam, de klant naam en het e-mail adres van de klant, bevinden zich op het niveau van de organisatie-ID en niet op Azure Marketplace of Microsoft AppSource transactie niveau.

_**Tabel 1: woorden lijst met gegevens termen**_

| Kolomnaam | Kenmerk naam | Definitie |
| ------------ | ------------- | ------------- |
| Marketplace-abonnements-id | Marketplace-abonnements-ID | De unieke id die is gekoppeld aan het Azure-abonnement dat de klant heeft gebruikt om uw aanbieding voor commerciële Marketplace aan te schaffen. De ID was voorheen de GUID van het Azure-abonnement. |
| DateAcquired | Aanschaf datum | De eerste datum waarop de klant een aanbieding heeft gekocht die u hebt gepubliceerd. |
| DateLost | Datum verloren | De laatste datum waarop de klant de laatste van alle eerder gekochte aanbiedingen heeft geannuleerd. |
| Naam van provider | Naam van provider | De naam van de provider die deel uitmaakt van de relatie tussen micro soft en de klant. Als de klant een onderneming is via wederverkoper, is dit de wederverkoper. Als er een Cloud Solution Provider (CSP) betrokken is, wordt dit de CSP. |
| E-mail van provider | E-mail van provider | Het e-mail adres van de provider die deel uitmaakt van de relatie tussen micro soft en de klant. Als de klant een onderneming is via wederverkoper, is dit de wederverkoper. Als er een Cloud Solution Provider (CSP) betrokken is, wordt dit de CSP. |
| FirstName | Voor naam van klant | De voor naam van de klant. De naam kan afwijken van de naam die is opgenomen in het Azure-abonnement van de klant. |
| LastName | Achternaam van klant | De achternaam van de klant. De naam kan afwijken van de naam die is opgenomen in het Azure-abonnement van de klant. |
| E-mail | E-mail van klant | Het e-mail adres van de eind gebruiker. E-mail kan afwijken van het e-mail adres in het Azure-abonnement van een klant. |
| Bedrijfs naam van klant | Bedrijfs naam van klant | De bedrijfs naam die door de klant is verschaft. De naam kan afwijken van de plaats in het Azure-abonnement van een klant. |
| CustomerCity | Klant plaats | De naam van de plaats van de klant. De stad kan afwijken van de plaats in het Azure-abonnement van een klant. |
| Post code van de klant | Post code van de klant | De post code die de klant heeft ontvangen. De code kan afwijken van de post code die is opgenomen in het Azure-abonnement van een klant. |
| CustomerCommunicationCulture | Taal van klant communicatie | De taal die door de klant wordt aanbevolen voor communicatie. |
| CustomerCountryRegion | Land/regio van klant | De land/regio naam die door de klant wordt verschaft. Land/regio kan afwijken van het land/de regio in het Azure-abonnement van een klant. |
| AzureLicenseType | Azure-licentie type | Het type licentie overeenkomst dat door klanten wordt gebruikt om Azure aan te schaffen. Ook wel bekend als het _kanaal_. De mogelijke waarden zijn:<ul><li>Cloud Solution Provider</li><li>Enterprise</li><li>Enter prise via wederverkoper</li><li>Betalen naar gebruik</li></ul> |
| PromotionalCustomers | Is de contact persoon voor de reclame in | De waarde laat u weten of de klant proactief heeft gekozen voor een contact persoon van uitgevers. Op dit moment bieden we geen optie voor klanten, dus hebben we "no" aangegeven op het bord. Nadat deze functie is geïmplementeerd, gaan we dienovereenkomstig met bijwerken. |
| CustomerState | Klant status | De staat van de woon plaats van de klant. De status kan afwijken van de status die is opgenomen in het Azure-abonnement van de klant. |
| CommerceRootCustomer | Commerce root-klant | Een facturerings account-ID kan worden gekoppeld aan meerdere klant-Id's.<br>Een combi natie van een facturerings account-ID en een klant-ID kan worden gekoppeld aan meerdere commerciële Marketplace-abonnementen.<br>De commerce root-klant geeft de naam aan van de klant van het abonnement. |
| Klant-id | Klant-ID | De unieke id die aan een klant is toegewezen. Een klant heeft mogelijk geen of meer Azure Marketplace-abonnementen. |
| Facturerings account-id | Facturerings account-ID | De id van het account waarop de facturering wordt gegenereerd. Wijs de **facturerings account-id** toe aan **CustomerID** om uw rapport met de uitbetalings transacties te koppelen aan de klant-, order-en gebruiks rapporten. |
||||

### <a name="customers-page-filters"></a>Pagina filters voor klanten

De pagina filters van de klant worden toegepast op het pagina niveau van de klant. U kunt meerdere filters selecteren om de grafiek weer te geven voor de criteria die u wilt bekijken en de gegevens die u wilt zien in het raster ' gedetailleerde orders gegevens '/exporteren. Filters worden toegepast op de gegevens die zijn geëxtraheerd voor het maand bereik dat u hebt geselecteerd in de rechter bovenhoek van de pagina klanten.

> [!TIP]
> U kunt het Download pictogram in de rechter bovenhoek van een wille keurige widget gebruiken om de gegevens te downloaden. U kunt feedback geven voor elk van de widgets door te klikken op het pictogram duim omhoog of duim omlaag.

## <a name="next-steps"></a>Volgende stappen

- Voor een overzicht van analyse rapporten die beschikbaar zijn in de commerciële Marketplace raadpleegt u [analytische rapporten voor de commerciële Marketplace in partner centrum](./partner-center-portal/analytics.md).
- Zie [Summary dash board in Commercial Marketplace Analytics](./summary-dashboard.md)voor grafieken, trends en waarden van statistische gegevens die de Marketplace-activiteiten voor uw aanbieding samenvatten.
- Voor informatie over uw orders in een grafische en download bare indeling raadpleegt u het [dash board orders in Commercial Marketplace Analytics](./orders-dashboard.md).
- Voor virtuele machine (VM) zijn metrische gegevens over gebruik en gefactureerde facturering, Zie [gebruiks dashboard in commerciële Marketplace-analyses](./usage-dashboard.md).
- Zie [dash board downloaden in Commercial Marketplace Analytics](./partner-center-portal/downloads-dashboard.md)voor een lijst met Download aanvragen voor de afgelopen 30 dagen.
- Zie [beoordeling & revisies Analytics dash board in partner centrum](./partner-center-portal/ratings-reviews.md)voor een geconsolideerde weer gave van feedback van klanten voor aanbiedingen op Azure Marketplace en Microsoft AppSource.
- Zie voor veelgestelde vragen over de analyse van commerciële Marketplace en voor een uitgebreid woorden boek met informatie over de [terminologie van commerciële Marketplace en veelgestelde vragen](./analytics-faq.md).