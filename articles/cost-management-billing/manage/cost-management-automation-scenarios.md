---
title: Automatiseringsscenario's voor facturering en kostenbeheer in Azure
description: Lees hoe veelgebruikte scenario's voor facturering en kostenbeheer kunnen worden gekoppeld aan bepaalde API's.
author: bandersmsft
ms.reviewer: adwise
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 01/26/2021
ms.author: banders
ms.openlocfilehash: 12c13b8a65296fb0ee74e0ee0449b604facf2f48
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051258"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Automatiseringsscenario's voor facturering en kostenbeheer

In dit artikel worden veelgebruikte scenario's voor Azure-facturering en kostenbeheer besproken. Deze scenario's worden gekoppeld aan specifieke API's die u kunt gebruiken. Onder elke koppeling van een scenario aan een API vindt u een overzicht van de API's en de functionaliteit die ze bieden.

## <a name="common-scenarios"></a>Algemene scenario's

U kunt de API's voor facturering en kostenbeheer in verschillende scenario's gebruiken om vragen over kosten en gebruik te beantwoorden. Hier volgt een overzicht van veelvoorkomende scenario's:

- **Factuurafstemming**: heeft Microsoft het juiste bedrag in rekening gebracht?  Wat is mijn factuur en kan ik deze zelf berekenen?

- **Doorbelasting van kosten**: ik weet nu hoeveel kosten er in rekening worden gebracht, maar wie in mijn organisatie moet die betalen?

- **Optimalisatie van kosten**: ik weet hoeveel er in rekening is gebracht. Hoe kan ik meer halen uit mijn investering in Azure?

- **Bijhouden van kosten**: ik wil het verloop van mijn Azure-gebruik en de bijbehorende kosten bekijken. Wat zijn de trends? Hoe kan ik het beter doen?

- **Azure-uitgaven gedurende de maand**: Hoeveel is de uitgaven van mijn huidige maand tot nu toe? Moet ik mijn uitgaven en/of gebruik van Azure aanpassen? Wanneer in de maand is mijn Azure-verbruik het hoogst?

- **Waarschuwingen**: Hoe kan ik op resources gebaseerd verbruik instellen of financiële waarschuwingen?

## <a name="scenario-to-api-mapping"></a>Koppeling van scenario aan API

|         API        | Factuurafstemming    | Doorbelasting van kosten    | Kostenoptimalisatie    | Bijhouden van kosten    | Halfmaandelijkse uitgaven    | Waarschuwingen    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budgetten                     |                           |                  |           X          |                  |                    |     X     |
| Marketplace-kosten                |             X             |         X        |           X          |         X        |          X         |     X     |
| Prijzenoverzicht                 |             X             |         X        |           X          |         X        |          X         |           |
| Aanbevelingen voor reserveringen |                           |                  |           X          |                  |                    |           |
| Reserveringsdetails         |                           |                  |           X          |         X        |                    |           |
| Reserveringssamenvattingen       |                           |                  |           X          |         X        |                    |           |
| Gebruiksgegevens               |             X             |         X        |           X          |         X        |          X         |     X     |
| Factureringsperioden             |             X             |         X        |           X          |         X        |                    |           |
| Facturen                    |             X             |         X        |           X          |         X        |                    |           |
| Azure-handels prijzen                    |             X             |                  |           X          |         X        |                    |           |


> [!NOTE]
> De tabel bevat geen API's voor Enterprise-verbruik. Gebruik waar mogelijk de algemene verbruiks-API's voor nieuwe ontwikkelscenario's.

## <a name="api-summaries"></a>Korte API-beschrijvingen

### <a name="consumption"></a>Verbruik
Web Direct-en Enterprise-klanten kunnen alle volgende API's gebruiken, tenzij anders aangegeven:

-    [API voor budgetten](/rest/api/consumption/budgets) (*Alleen voor Enterprise-klanten*): kosten- of gebruiksbudgetten maken voor resources, resourcegroepen of factureringsmeters. Wanneer u budgetten hebt gemaakt, kunt u waarschuwingen configureren om u te waarschuwen wanneer er gedefinieerde budgetdrempels zijn overschreden. U kunt ook acties configureren die moeten worden uitgevoerd wanneer er budgetbedragen zijn bereikt.

-    [API voor Marketplace-kosten](/rest/api/consumption/marketplaces): gegevens van kosten en gebruik opvragen voor alle Azure Marketplace-resources (Azure-partneraanbiedingen). U kunt deze gegevens gebruiken om de totale kosten voor alle Marketplace-resources te berekenen of om de kosten of het gebruik van specifieke resources nader te onderzoeken.

-    [API voor prijzenoverzicht](/rest/api/consumption/pricesheet) (*Alleen voor Enterprise-klanten*): aangepaste prijzen opvragen voor alle meters. Ondernemingen kunnen deze gegevens gebruiken in combinatie met gebruiksgegevens en gegevens van Marketplace-gebruik om kostenberekeningen uit te voeren op basis van gebruiks- en Marketplace-gegevens.

-    [API voor aanbevelingen voor reserveringen](/rest/api/consumption/reservationrecommendations): aanbevelingen opvragen krijgen voor het kopen van gereserveerde instanties van virtuele machines. Aanbevelingen maken het mogelijk om verwachte kostenbesparingen en aankoopbedragen te analyseren. Zie [API's voor automatisering van Azure-reserveringen](../reservations/reservation-apis.md) voor meer informatie.

-    [API voor reserveringsdetails](/rest/api/consumption/reservationsdetails): informatie bekijken over eerder gekochte VM-reserveringen, zoals hoeveel verbruik is gereserveerd en hoeveel er daadwerkelijke wordt gebruikt. U kunt gegevens op VM-niveau weergeven. Zie [API's voor automatisering van Azure-reserveringen](../reservations/reservation-apis.md) voor meer informatie.

-    [API voor reserveringssamenvattingen](/rest/api/consumption/reservationssummaries): geaggregeerde gegevens bekijken van VM-reserveringen die uw organisatie heeft gekocht, zoals hoeveel verbruik is gereserveerd en hoeveel er in totaal wordt gebruikt. Zie [API's voor automatisering van Azure-reserveringen](../reservations/reservation-apis.md) voor meer informatie.

-    [API voor gedetailleerde gebruiksgegevens](/rest/api/consumption/usagedetails): gegevens van kosten en gebruik opvragen voor alle Azure-resources van Microsoft. U ontvangt de informatie in de vorm van records met gedetailleerde gebruiksgegevens, die momenteel één keer per meter per dag worden verzonden. U kunt de informatie gebruiken om de totale kosten voor alle resources te berekenen of om de kosten of het gebruik van specifieke resources nader te onderzoeken.

-    [Azure-handels prijzen](/rest/api/cost-management/retail-prices/azure-retail-prices): meter tarieven ophalen met betalen per gebruik-prijzen. U kunt vervolgens aan de hand van de geretourneerde informatie en de informatie over het resourcegebruik de verwachte factuur handmatig berekenen.

### <a name="billing"></a>Billing
-    [API voor factureringsperioden](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): bepalen welke factureringsperiode u wilt analyseren, samen met de factuur-id's voor die periode. U kunt factuur-id's gebruiken met de API voor facturen.

-    [API voor facturen](/rest/api/billing/2019-10-01-preview/invoices): de download-URL voor een factuur opvragen voor een factureringsperiode, in PDF-formaat.

### <a name="enterprise-consumption"></a>Enterprise-verbruik
De volgende API's zijn alleen voor Enterprise:

-    [API voor verkorte balans](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): een maandelijks overzicht opvragen met informatie over saldi, nieuwe aankopen, Azure Marketplace-servicekosten, correcties en overschrijdingskosten. U kunt deze informatie ophalen voor de huidige factureringsperiode of een periode in het verleden. Ondernemingen kunnen deze gegevens gebruiken voor een vergelijking met handmatig berekende kosten. Deze API biedt geen specifieke informatie over resources en ook geen geaggregeerde weergave van kosten.

-    [API voor gedetailleerde gebruiksgegevens](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): informatie opvragen over Azure-gebruik (van Microsoft-aanbiedingen) voor de huidige maand, een specifieke factureringsperiode of een aangepaste periode. Ondernemingen kunnen deze gegevens gebruiken om facturen handmatig te berekenen op basis van tarief en verbruik. Ondernemingen kunnen ook gegevens van afdelingen en organisaties gebruiken om kosten aan verschillende organisaties toe te rekenen. De gegevens bieden een resourcespecifieke weergave van het gebruik/de kosten.

-    [API voor Marketplace Store-kosten](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): informatie opvragen over Azure-gebruik (van partneraanbiedingen) voor de huidige maand, een specifieke factureringsperiode of een aangepaste periode. Ondernemingen kunnen deze gegevens gebruiken om facturen handmatig te berekenen op basis van tarief en verbruik. Ondernemingen kunnen ook gegevens van afdelingen en organisaties gebruiken om kosten aan verschillende organisaties toe te rekenen. Deze API biedt een resourcespecifieke weergave van het gebruik/de kosten.

-    [API voor prijzenoverzichten](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): het toepasselijke tarief opvragen voor elke meter voor een inschrijvings- en factureringsperiode. U kunt deze tariefgegevens gebruiken in combinatie met gebruiksgegevens en gegevens van het gebruik van Marketplace om de verwachte factuur handmatig te berekenen.

-    [API voor factureringsperioden](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): een lijst met factureringsperioden opvragen. De API biedt ook een eigenschap die verwijst naar de API-route voor de vier sets met Enterprise API-gegevens die betrekking hebben op de factureringsperiode: BalanceSummary, UsageDetails, Marketplace Charges en PriceSheet.

-    [API voor aanbevelingen voor gereserveerde instanties](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): voor een periode van 7 dagen, 30 dagen of 60 dagen gegevens bekijken van het gebruik van virtuele machines en aanbevelingen ontvangen voor enkele en gedeelde aankoop. U kunt deze API gebruiken om verwachte kostenbesparingen en aanbevolen aankoopbedragen te analyseren. Zie [API's voor automatisering van Azure-reserveringen](../reservations/reservation-apis.md) voor meer informatie.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Wat is het verschil tussen de API's voor Enterprise-rapportage en de verbruiks-API's? Wanneer kan ik welke API het beste gebruiken?
Deze API's hebben een vergelijkbare functionaliteit en kunnen dezelfde algemene set vragen beantwoorden op het gebied van facturering en kostenbeheer. Het verschil is dat ze zijn bedoeld voor verschillende doelgroepen:

- API's voor Enterprise-rapportage zijn beschikbaar voor klanten die een Enterprise Agreement met Microsoft hebben ondertekend waarmee ze toegang krijgen tot overeengekomen Azure-vooruitbetaling (voorheen financiële toezegging) en aangepaste prijzen. De API's vereisen een sleutel die u kunt ophalen uit de [Enterprise Portal](https://ea.azure.com). Zie [Overzicht van rapportage-API's voor Enterprise-klanten](enterprise-api.md)voor een beschrijving van deze API's.

- Verbruiks-API's zijn beschikbaar voor alle klanten, met een paar uitzonderingen. Zie [Overzicht van API voor Azure-gebruiksgegevens](consumption-api-overview.md) en het [overzicht van verbruiks-API's](/rest/api/consumption/) voor meer informatie. We raden de meegeleverde API's aan als de oplossing voor scenario's voor de nieuwste ontwikkelingen.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Wat is het verschil tussen de facturerings-API en de API voor gebruiksgegevens?
Deze API's bieden een verschillende weergave van dezelfde gegevens:

- De [facturerings-API](/rest/api/billing/2019-10-01-preview/invoices) is alleen voor Web Direct-klanten. De API biedt een maandelijks aggregatie van uw factuur op basis van de cumulatieve kosten voor elk metertype.

- De [API voor gebruiksgegevens](/rest/api/consumption/usagedetails) biedt een gedetailleerd overzicht van de gebruiks-en kostenrecords voor elke dag. Deze API is beschikbaar voor zowel Enterprise- als Web Direct-klanten.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Wat is het verschil tussen de API voor het prijzenoverzicht en de RateCard-API?
Deze API's bieden vergelijkbare gegevenssets, maar hebben verschillende doelgroepen:

- De [API voor het prijzenoverzicht](/rest/api/consumption/pricesheet) biedt de aangepaste prijzen die zijn onderhandeld voor een Enterprise-klant.

- De [Azure retail-prijs-API](/rest/api/cost-management/retail-prices/azure-retail-prices) biedt open bare tarieven voor betalen per gebruik die van toepassing zijn op Web direct-klanten.

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over het gebruik van REST-Api's prijzen ophalen voor alle Azure-Services [overzicht van Azure retail-prijzen](/rest/api/cost-management/retail-prices/azure-retail-prices).

- Zie [Meer informatie over uw factuur voor Microsoft Azure](../understand/review-individual-bill.md) om uw factuur te vergelijken met het bestand met de gedetailleerde dagelijkse gebruiksgegevens en de kostenbeheerrapporten in de Azure-portal.

- Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).