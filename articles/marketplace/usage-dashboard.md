---
title: Gebruiks dashboard in commerciële Marketplace-analyse | Azure Marketplace
description: Lees hoe u toegang hebt tot alle metrische gegevens over gebruik en gefactureerde facturering voor aanbiedingen die naar Azure Marketplace worden gepubliceerd.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 765f4f358d593cd5fcc021fbf4ec2276c422c4d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555122"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>Dashboard Gebruik in Commerciële marketplace-analyses

Dit artikel bevat informatie over het gebruiks dashboard in partner centrum. In dit dash board wordt alle virtuele machine (VM) weer gegeven met genormaliseerd gebruik, onbewerkt gebruik en metrische facturerings gegevens op drie afzonderlijke tabbladen: genormaliseerd gebruik van de VM, het onbewerkte gebruik van de VM en het facturerings gebruik met data limiet.

Voor toegang tot het gebruiks dashboard in Partner Center, onder **commerciële Marketplace**, **[](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** selecteert u  >  **gebruik** analyseren.

>[!NOTE]
> Zie voor gedetailleerde definities van analyse terminologie [Commercial Marketplace Analytics terminologie en common questions](./analytics-faq.md)(Engelstalig).

## <a name="usage-dashboard"></a>Dashboard voor gebruik

In het **gebruiks** dashboard in het menu **analyseren** worden de huidige orders voor al uw SaaS-aanbiedingen (Software as a Service) weer gegeven. U kunt grafische weer gaven van de volgende items bekijken:

- Gebruiks trend
- Genormaliseerd gebruik per aanbiedingen
- Genormaliseerd gebruik door andere dimensies: VM-grootte, verkoop kanalen en aanbiedings typen
- Gebruik per Geografie
- Gedetailleerde gebruiks tabel
- Pagina filters voor orders

> [!NOTE]
> De maximale latentie tussen het genereren en rapporteren van gebruiks gebeurtenissen in het partner centrum is 48 uur.

## <a name="elements-of-the-usage-dashboard"></a>Elementen van het gebruiks dashboard

In de volgende secties wordt beschreven hoe u het gebruiks dashboard gebruikt en hoe u de gegevens kunt lezen.

### <a name="month-range"></a>Maand bereik

U vindt de selectie van een maand bereik in de rechter bovenhoek van elke pagina. Pas de uitvoer van de grafiek van de **gebruiks** pagina aan door een maand bereik te selecteren op basis van de afgelopen 6 of 12 maanden of door een aangepast maand bereik te selecteren met een maximale duur van 12 maanden. Het standaard bereik van de maand (reken periode) is zes maanden.

:::image type="content" source="./media/usage-dashboard/month-filter.png" alt-text="Illustreert de maand filters op het gebruiks dashboard.":::

### <a name="usage-trend"></a>Gebruiks trend

In deze sectie vindt u het totale gebruiks uren en de trend voor al uw aanbiedingen die door uw klanten worden gebruikt tijdens de geselecteerde reken periode. Metrische gegevens en groei trends worden weer gegeven in een lijn diagram. De waarde voor elke maand weer geven door de muis aanwijzer boven de lijn in de grafiek te plaatsen. De percentage waarde onder de metrische gegevens over gebruik in de widget vertegenwoordigt de hoeveelheid groei of afwijzing tijdens de geselecteerde reken periode.

Er zijn twee representaties van gebruiks uren: genormaliseerd gebruik van virtuele machines en onbewerkte VM-gebruik.

- Genormaliseerde gebruiks uren worden gedefinieerd als het gebruik van uren die zijn genormaliseerd voor het aantal VM-kernen ([aantal VM-kernen] x [uur van onbewerkt gebruik]). Vm's die zijn aangewezen als "SHAREDCORE" gebruiken 1/6 (of 0,1666) als [aantal VM-kernen] vermenigvuldiger.
- Onbewerkte gebruiks uren worden gedefinieerd als de hoeveelheid tijd waarop Vm's zijn uitgevoerd in het kader van uren.

[![Illustreert het genormaliseerde gebruik en de onbewerkte gebruiks gegevens op het gebruiks dashboard.](./media/usage-dashboard/normalized-usage.png)](./media/usage-dashboard/normalized-usage.png#lightbox)

### <a name="normalized-usage-by-offers"></a>Genormaliseerd gebruik per aanbiedingen

In deze sectie vindt u het totale gebruiks uren en de trend voor uw aanbiedingen op basis van gebruik in azure Marketplace. De grafiek genormaliseerd gebruik per aanbiedingen wordt hieronder beschreven.

- Het **genormaliseerde gebruik per biedt** gestapeld kolom diagram geeft een uitsplitsing van genormaliseerde gebruiks uren voor de vijf meest voorkomende aanbiedingen op basis van de geselecteerde reken periode. De vijf belangrijkste aanbiedingen worden weer gegeven in een grafiek, terwijl de rest wordt gegroepeerd in de categorie **rest alle** .
- In het gestapelde kolom diagram wordt een maand per maand weer gegeven voor het geselecteerde datum bereik. De maand kolommen vertegenwoordigen de gebruiks uren van de aanbiedingen met de hoogste gebruiks uren voor de betreffende maand. In het lijn diagram wordt de trend van het groei percentage weer gegeven die op de secundaire Y-as is getekend.
- U kunt specifieke aanbiedingen in de legenda selecteren om alleen die aanbiedingen in de grafiek weer te geven.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers.png" alt-text="Illustreert de genormaliseerde gebruiks gegevens voor het gebruik van het dash board.":::

U kunt een wille keurige aanbieding en Maxi maal drie Sku's van de aanbieding selecteren om de trend over het gebruik van de maand voor de aanbieding en de geselecteerde Sku's weer te geven.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers-sku.png" alt-text="Illustreert de genormaliseerde gebruiks aanbiedingen en SKU-gegevens op het gebruiks dashboard.":::

### <a name="orders-by-offers-and-skus"></a>Orders per aanbiedingen en Sku's

In het diagram **Orders per aanbiedingen en SKU** worden de metingen en trends van alle aanbiedingen weer gegeven. Houd rekening met het volgende:

- De beste aanbiedingen worden weer gegeven in de grafiek en de overige aanbiedingen worden als **rest** gegroepeerd.
- U kunt specifieke aanbiedingen in de legenda selecteren om alleen die aanbiedingen in de grafiek weer te geven.
- Als u de muis aanwijzer boven een segment in de grafiek houdt, wordt het aantal orders en percentage van die aanbieding vergeleken met het totale aantal orders in alle aanbiedingen.
- De **trend orders per aanbiedingen** worden weer gegeven per maand trends in groei. De kolom maand vertegenwoordigt het aantal orders per aanbiedings naam. In het lijn diagram wordt de trend van het groei percentage weer gegeven dat op de z-as is getekend.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus.png" alt-text="Illustreert de grafiek orders per aanbiedingen en Sku's op het gebruiks dashboard.":::

U kunt een wille keurige aanbieding en Maxi maal drie Sku's van de aanbieding selecteren om de trend van de maand over de maand voor de aanbieding, Sku's en seats weer te geven.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus-2.png" alt-text="Illustreert de grafiek orders per aanbiedingen en Sku's op het gebruiks dashboard. De trend van de aanbod trend, Sku's-trend en de stoelen wordt weer gegeven.":::

#### <a name="normalized-usage-by-other-dimensions-vm-size-sales-channels-and-offer-type"></a>Genormaliseerd gebruik door andere dimensies: VM-grootte, verkoop kanalen en aanbiedings type

Er zijn drie tabbladen voor de dimensies: VM-grootte, verkoop kanalen en aanbiedings type. U kunt de metrische gegevens over het gebruik en de trend over maanden bekijken voor elk van deze dimensies.

:::image type="content" source="./media/usage-dashboard/normalized-usage-other-dimensions.png" alt-text="Illustreert het diagram genormaliseerd gebruik van andere dimensies in het gebruiks dashboard.":::

### <a name="usage-by-geography"></a>Gebruik per Geografie

Voor de geselecteerde berekenings periode geeft de heatmap het totale gebruik weer op geografische dimensie. Het licht tot donkerere kleur op de kaart vertegenwoordigt de laag tot hoogste waarde van het aantal van de klant. Selecteer een record in de tabel om in te zoomen op een land/regio.

:::image type="content" source="./media/usage-dashboard/normalized-usage-country.png" alt-text="Illustreert de genormaliseerde grafiek van het gebruik van het land in het gebruiks dashboard.":::

Houd rekening met het volgende:

- U kunt de kaart verplaatsen om de exacte locatie weer te geven.
- U kunt inzoomen op een specifieke locatie.
- De heatmap heeft een aanvullend raster voor het weer geven van de details van het aantal klanten, het aantal orders en de genormaliseerde gebruiks uren op de specifieke locatie.
- U kunt zoeken en een land/regio in het raster selecteren om in te zoomen op de locatie in de kaart. Keer terug naar de oorspronkelijke weer gave door de knop **Start** te selecteren in de kaart.

### <a name="usage-details-table"></a>Tabel gebruiks gegevens

In de tabel **gebruiks gegevens** wordt een genummerde lijst weer gegeven met de bovenste 1.000 gebruiks records gesorteerd op gebruik. Houd rekening met het volgende:

- Elke kolom in het raster is sorteerbaar.
- De gegevens kunnen worden geëxtraheerd naar een. TSV of. CSV-bestand als het aantal records kleiner is dan 1.000.
- Als aantal records groter is dan 1.000, worden de export gegevens asynchroon geplaatst op een pagina down loads die de volgende 30 dagen beschikbaar zullen zijn.
- Filters toep assen op **gedetailleerde gebruiks gegevens** om alleen de gegevens weer te geven waarin u geïnteresseerd bent. Gegevens filteren op land/regio, verkoop kanaal, Marketplace-licentie type, gebruiks type, naam van aanbieding, type aanbieding, gratis proef versies, abonnement-ID van Marketplace, klant-ID en bedrijfs naam.

_**Tabel 1: woorden lijst met gegevens termen**_

| Kolom naam in<br>gebruikersinterface | Kenmerknaam | Definitie | Kolom naam in programma code<br>Access-rapporten |
| ------------ | ------------- | ------------- | ------------- |
| Marketplace-abonnements-ID | Marketplace-abonnements-ID | De unieke id die is gekoppeld aan het Azure-abonnement dat de klant heeft gebruikt om uw aanbieding voor commerciële Marketplace aan te schaffen. De ID was voorheen de GUID van het Azure-abonnement. | MarketplaceSubscriptionId |
| MonthStartDate | Begin datum maand | De begin datum van de maand vertegenwoordigt de maand van de aankoop. | MonthStartDate |
| Type aanbieding | Type aanbieding | Het soort commerciële Marketplace-aanbieding. | Offer type |
| Azure-licentie type | Azure-licentie type | Het type licentie overeenkomst dat door klanten wordt gebruikt om Azure aan te schaffen. Ook wel bekend als het kanaal. De mogelijke waarden zijn:<ui><li>Cloud Solution Provider</li><li>Enterprise</li><li>Enter prise via wederverkoper</li><li>Betalen naar gebruik</li></ul> | AzureLicenseType |
| Marketplace-licentie type | Marketplace-licentie type | De facturerings methode van de aanbieding voor commerciële Marketplace. De mogelijke waarden zijn:<ul><li>Gefactureerd via Azure</li><li>Neem uw eigen licentie mee</li><li>Gratis</li><li>Micro soft als wederverkoper</li></ul> | MarketplaceLicenseType |
| SKU | SKU | Het plan dat is gekoppeld aan de aanbieding. | SKU |
| Klant land | Land/regio van klant | De land/regio naam die door de klant wordt verschaft. Land/regio kan afwijken van het land/de regio in het Azure-abonnement van een klant. | CustomerCountry |
| Is preview-SKU | Is preview-SKU | De waarde geeft aan of u de SKU hebt gelabeld als ' preview '. De waarde is "ja" als de SKU dienovereenkomstig is gelabeld en alleen Azure-abonnementen die door u zijn geautoriseerd, kunnen deze installatie kopie implementeren en gebruiken. De waarde is ' nee ' als de SKU niet is geïdentificeerd als ' preview '. | IsPreviewSKU |
| Facturerings type SKU | Facturerings type SKU | Het facturerings type dat is gekoppeld aan elke SKU in de aanbieding. De mogelijke waarden zijn:<ul><li>Gratis</li><li>Betaald</li></ul> | SKUBillingType |
| Wordt dat IsInternal | Afgeschaft | Afgeschaft | Afgeschaft |
| VM-grootte | Grootte van de virtuele machine | Voor aanbiedings typen op basis van een virtuele machine geeft deze entiteit de grootte aan van de VM die is gekoppeld aan de SKU van de aanbieding. | VMSize |
| Naam van Cloud exemplaar | Naam van Cloud exemplaar | De Microsoft Cloud waarin een VM-implementatie is opgetreden. | CloudInstanceName |
| ServicePlanName | Afgeschaft | Afgeschaft (dezelfde definitie als SKU) | ServicePlanName |
| Naam van aanbieding | Naam van aanbieding | De naam van de commerciële Marketplace-aanbieding. | OfferName |
| DeploymentMethod | Afgeschaft | Afgeschaft (dezelfde definitie als het aanbiedings type) | DeploymentMethod |
| Bedrijfs naam van klant | Bedrijfs naam van klant | De bedrijfs naam die door de klant is verschaft. De naam kan afwijken van de naam in het Azure-abonnement van een klant. | CustomerCompanyName |
| Gebruiksdatum | Gebruiksdatum | De datum van het genereren van de gebruiks gebeurtenis voor op gebruik gebaseerde activa. | UsageDate |
| IsMultisolution | Is multisolution | Geeft aan of de aanbieding een type van een aanbieding voor de oplossing is. | IsMultisolution |
| Is nieuwe klant | Afgeschaft | Afgeschaft | IsNewCustomer |
| Kern grootte | Kern grootte | Het aantal kernen dat is gekoppeld aan de op de virtuele machine gebaseerde aanbieding. | Formaat wijzigen |
| Gebruikstype | Gebruikstype | Geeft aan of de gebruiks gebeurtenis die aan de aanbieding is gekoppeld, een van de volgende is:<ul><li>Genormaliseerd gebruik</li><li>Onbewerkt gebruik</li><li>Gebruik met data limiet</li></ul> | UsageType |
| Eind datum van proef versie | Eind datum van proef versie | De datum waarop de proef periode voor deze order wordt beëindigd of beëindigd. | TrialEndDate |
| Klant valuta (CC) | Valuta van klant | De valuta die de klant voor de trans actie voor commerciële Marketplace gebruikt. | CustomerCurrencyCC |
| Prijs (CC) | Prijs | Eenheids prijs van de SKU die wordt weer gegeven in de valuta van de klant. | PriceCC |
| Valuta voor betaling (PC) | Valuta voor betaling | De uitgever wordt betaald voor de gebruiks gebeurtenissen die zijn gekoppeld aan het activum in de valuta die is geconfigureerd door de uitgever. | PayoutCurrencyPC |
| Geschatte prijs (PC) | Geschatte prijs | Eenheids prijs van de SKU in de valuta die is geconfigureerd door de uitgever. | EstimatedPricePC |
| Gebruiks referentie | Gebruiks referentie | Een samengevoegde GUID die wordt gebruikt om het gebruiks rapport (in commerciële Marketplace-analyse) te verbinden met het rapport uitbetalings transacties. Gebruiks verwijzing is gekoppeld aan OrderId en LineItemId in het rapport uitbetalings transacties. | UsageReference |
| Gebruiks eenheid | Gebruiks eenheid | De verbruiks eenheid die aan de SKU is gekoppeld. | UsageUnit |
| Klant-ID | Klant-ID | De unieke id die aan een klant is toegewezen. Een klant heeft mogelijk geen of meer Azure Marketplace-abonnementen. | CustomerId |
| Facturerings account-ID | Facturerings account-ID | De id van het account waarop de facturering wordt gegenereerd. Wijs de **facturerings account-id** toe aan **CustomerID** om uw rapport met de uitbetalings transacties te koppelen aan de klant-, order-en gebruiks rapporten. | BillingAccountId |
| Gebruiks hoeveelheid | Gebruiks hoeveelheid | De totale verbruikte eenheden die worden gebruikt door de activa die door de klant worden geïmplementeerd.<br>Dit is gebaseerd op het gebruiks type item. Als het gebruiks type bijvoorbeeld genormaliseerd gebruik is, is de hoeveelheid verbruik voor het normale gebruik. | UsageQuantity |
| NormalizedUsage | Genormaliseerd gebruik | Het totale aantal genormaliseerde gebruiks eenheden dat door de klant wordt gebruikt.<br>Genormaliseerde gebruiks uren worden gedefinieerd als het gebruik van uren die zijn genormaliseerd voor het aantal VM-kernen ([aantal VM-kernen] x [uur van onbewerkt gebruik]). Vm's die zijn aangewezen als "SHAREDCORE" gebruiken 1/6 (of 0,1666) als [aantal VM-kernen] vermenigvuldiger. | NormalizedUsage |
| MeteredUsage | Gebruik met data limiet | De totale verbruikte eenheden die worden gebruikt door de meters die zijn geconfigureerd met de aanbieding die door de klant wordt geïmplementeerd. | MeteredUsage |
| RawUsage | Onbewerkt gebruik | Het totale aantal onbewerkte verbruiks eenheden dat is gebruikt door de activa die door de klant worden geïmplementeerd.<br>Onbewerkte gebruiks uren worden gedefinieerd als de hoeveelheid tijd waarop Vm's zijn uitgevoerd in termen van gebruiks eenheden. | RawUsage |
| Geschatte berekende toeslag (CC) | Geschatte berekende toeslag in de klant valuta | Geeft de kosten aan die zijn gekoppeld aan het gebruik. De kolom is het product van de prijs (CC) en het gebruiks aantal. | EstimatedExtendedChargeCC |
| Geschatte uitgebreide lading (PC) | Geschatte berekende kosten in de valuta voor betaling | Geeft de kosten aan die zijn gekoppeld aan het gebruik. De kolom is het product van de geschatte prijs (PC) en het gebruiks aantal. | EstimatedExtended ChargePC |
|||||

### <a name="usage-page-filters"></a>Filters voor gebruiks pagina's

De **gebruiks** pagina filters worden toegepast op het pagina niveau orders. U kunt een of meer filters selecteren om de grafiek te genereren voor de criteria die u wilt weer geven en de gegevens die u wilt zien in het raster van de gegevens over het gebruik van orders/export. Filters worden toegepast op de gegevens die zijn geëxtraheerd voor het maand bereik dat u hebt geselecteerd in de rechter bovenhoek van de gebruiks pagina.

Het rapport widgets en exporteren voor het onbewerkte VM-gebruik zijn vergelijkbaar met het gebruik van genormaliseerde VM'S met de volgende verschillen:

- Genormaliseerde gebruiks uren worden gedefinieerd als het gebruik van uren die zijn genormaliseerd voor het aantal VM-kernen ([aantal VM-kernen] x [uur van onbewerkt gebruik]). Vm's die zijn aangewezen als "SHAREDCORE" gebruiken 1/6 (of 0,1666) als [aantal VM-kernen] vermenigvuldiger.
- Onbewerkte gebruiks uren worden gedefinieerd als de hoeveelheid tijd waarop Vm's zijn uitgevoerd in termen van gebruiks eenheden.

### <a name="metered-billing-usage"></a>Gefactureerd facturerings gebruik

Op het tabblad gebruik in de **Data limiet** worden gebruiks gegevens weer gegeven voor aanbiedings typen waarbij gebruik wordt gemeten door de dimensie per meter. SaaS-aanbieding type overschrijding wordt momenteel weer gegeven. Het tabblad geeft grafische voors tellingen van overschrijding-trends voor het gebruik van SaaS-data limieten:

- **Overschrijding trend per meter dimensie**: geeft de maandelijkse overschrijding trend weer voor de geselecteerde meter dimensie van een aanbieding. De X-as vertegenwoordigt de maand en de Y-as vertegenwoordigt de hoeveelheid van het overschrijding gebruik. De meet eenheid van de aangepaste meter wordt ook weer gegeven op de Y-as.

    :::image type="content" source="./media/usage-dashboard/metered-usage.png" alt-text="Illustreert het genormaliseerde gebruik van de VM-gebruiks grafiek in het gebruiks dashboard.":::

- **Overschrijding trend per SKU**: vertegenwoordigt de trend van het gebruiks aantal van de geselecteerde meter dimensie per SKU/abonnementen. De vijf meest voorkomende plannen met de hoogste hoeveelheid gebruik voor de geselecteerde aanbieding worden weer gegeven.

- **Overschrijding trend per klant**: het leider bord van de klant vertegenwoordigt een gestapelde lijst met klanten met de hoogste gebruiks uren en wordt weer gegeven op een _Leader Board_, gerangschikt op het hoogste gebruik van de aangepaste meter. Selecteer een klant in het prik bord om de overschrijding-gebruiks trend van een geselecteerde meter dimensie weer te geven.

    :::image type="content" source="./media/usage-dashboard/metered-usage-by-customers.png" alt-text="Illustreert het diagram gebruik in de data limiet per klant op het gebruiks dashboard.":::

Als u meerdere aanbiedingen hebt die gebruikmaken van aangepaste meters, worden in het gebruiks rapport van de gefactureerde facturering gebruiks gegevens voor al uw aanbiedingen weer gegeven volgens hun aangepaste meter dimensies.

> [!TIP]
> U kunt het Download pictogram in de rechter bovenhoek van een wille keurige widget gebruiken om de gegevens te downloaden. U kunt feedback geven voor elk van de widgets door te klikken op het pictogram duim omhoog of duim omlaag.

## <a name="next-steps"></a>Volgende stappen

- Voor een overzicht van analyse rapporten die beschikbaar zijn in de commerciële Marketplace raadpleegt u [analytische rapporten voor de commerciële Marketplace in partner centrum](./partner-center-portal/analytics.md).
- Zie [Summary dash board in Commercial Marketplace Analytics](./summary-dashboard.md)voor grafieken, trends en waarden van statistische gegevens die de Marketplace-activiteiten voor uw aanbieding samenvatten.
- Voor informatie over uw orders in een grafische en download bare indeling raadpleegt u het [dash board orders in Commercial Marketplace Analytics](./orders-dashboard.md)
- Voor virtuele machine (VM) zijn metrische gegevens over gebruik en gefactureerde facturering, Zie [gebruiks dashboard in commerciële Marketplace-analyses](usage-dashboard.md).
- Zie [dash board downloaden in Commercial Marketplace Analytics](./partner-center-portal/downloads-dashboard.md)voor een lijst met Download aanvragen voor de afgelopen 30 dagen.
- Zie [beoordeling & revisies Analytics dash board in partner centrum](./partner-center-portal/ratings-reviews.md)voor een geconsolideerde weer gave van feedback van klanten voor aanbiedingen op Azure Marketplace en Microsoft AppSource.
- Zie voor veelgestelde vragen over de analyse van commerciële Marketplace en voor een uitgebreid woorden boek met informatie over de [terminologie van commerciële Marketplace en veelgestelde vragen](./analytics-faq.md).
