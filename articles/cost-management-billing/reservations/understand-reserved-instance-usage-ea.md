---
title: Inzicht in het gebruik van Azure-reserveringen voor Enterprise Agreement en Microsoft-klantovereenkomst
description: Meer informatie over het lezen van uw gebruiksgegevens om te begrijpen hoe een Azure-reservering van toepassing is op Enterprise Agreement en Microsoft-klantovereenkomst gebruik.
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: banders
ms.openlocfilehash: edc1a80687f768723a3a218ecaa1f6459cd484ed
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763555"
---
# <a name="get-enterprise-agreement-and-microsoft-customer-agreement-reservation-costs-and-usage"></a>Reserveringskosten Enterprise Agreement Microsoft-klantovereenkomst en gebruiksgegevens op te halen

Verbeterde gegevens voor reserveringskosten en -gebruik zijn beschikbaar voor Enterprise Agreement (EA) en Microsoft-klantovereenkomst (MCA) in Kostenbeheer. In dit artikel worden de volgende onderwerpen besproken:

- Aankoopgegevens van reserveringen ophalen
- Controleren welke abonnementen, resourcegroepen of resources de reservering hebben gebruikt
- Reserveringsgebruik toerekenen
- Reserveringsbesparingen berekenen
- Gegevens over ondergebruikte reserveringen ophalen
- Reserveringskosten afschrijven

Marketplace-kosten worden geconsolideerd in gebruiksgegevens. De kosten voor eigen gebruik, het gebruik van Marketplace en aankopen kunt u op één plek inzien.

## <a name="reservation-charges-in-azure-usage-data"></a>Reserveringskosten in Azure-gebruiksgegevens

Gegevens worden verdeeld over twee afzonderlijke gegevenssets: _werkelijke kosten_ en _afgeschreven kosten_. Dit zijn de verschillen tussen deze twee gegevenssets:

**Werkelijk kosten**: gegevens waarmee u uw maandelijkse factuur kunt afstemmen. Deze gegevens omvatten ook de aanschafkosten van de reservering en details over reserveringstoepassingen. Aan de hand van deze gegevens kunt u vaststellen welk abonnement of welke resourcegroep of resource de reserveringskorting heeft ontvangen op een bepaalde dag. De EffectivePrice voor het gebruik dat de reserveringskorting ontvangt, is nul.

**Afgeschreven kosten**: deze gegevensset is vergelijkbaar met de gegevensset Werkelijke kosten, met als verschil dat de EffectivePrice voor het gebruik dat de reserveringskorting krijgt gelijk is aan de evenredig verdeelde kosten van de reservering (in plaats van nul). Deze gegevens helpen bij het bepalen van de monetaire waarde van reserveringsverbruik door een abonnement, resourcegroep of resource, en kan u helpen om het gebruik van de reservering intern toe te rekenen. De gegevensset heeft ook ongebruikte reserveringsuren. De gegevensset heeft geen records van reserveringsaankopen.

Vergelijking van de twee gegevenssets:

| Gegevens | Gegevensset Werkelijke kosten | Gegevensset Afgeschreven kosten |
| --- | --- | --- |
| Reserveringsaankopen | Beschikbaar in deze weergave.<br><br>  U kunt deze gegevens opvragen door te filteren op ChargeType = &quot;Purchase&quot;. <br><br> Raadpleeg de ReservationID of de ReservationName om vast te stellen voor welke reservering de kosten zijn.  | Niet van toepassing op deze weergave. <br><br> Er worden geen aankoopkosten opgenomen in gegevens van afgeschreven kosten. |
| EffectivePrice | De waarde is nul voor gebruik waarvoor een reserveringskorting geldt. | De waarde bestaat uit de evenredig verdeelde kosten per uur van de reservering voor gebruik waarvoor een reserveringskorting geldt. |
| Ongebruikte reservering (het aantal uren dat de reservering niet is gebruikt op een dag en de monetaire waarde van de verspilling) | Niet van toepassing in deze weergave. | Beschikbaar in deze weergave.<br><br> U kunt deze gegevens opvragen door te filteren op ChargeType = &quot;UnusedReservation&quot;.<br><br>  Raadpleeg de ReservationID of de ReservationName om vast te stellen welke reservering niet maximaal is gebruikt. Geeft aan welk gedeelte van de reservering er die dag is verspild.  |
| UnitPrice (prijs van de resource in uw prijzenoverzicht) | Beschikbaar | Beschikbaar |

Er zijn aanpassingen doorgevoerd in andere gegevens die beschikbaar zijn voor Azure-gebruiksgegevens:

- Product- en metergegevens: de oorspronkelijk gebruikte meter wordt niet meer vervangen door de ReservationId en ReservationName, zoals eerder het geval was.
- ReservationId en ReservationName: dit zijn nu afzonderlijke velden in de gegevens. Eerder was deze informatie alleen beschikbaar onder AdditionalInfo.
- ProductOrderId: de id van de reserveringsorder, die als afzonderlijk veld is toegevoegd.
- ProductOrderName: de productnaam van de gekochte reservering.
- Term: 12 of 36 maanden.
- RINormalizationRatio: beschikbaar onder AdditionalInfo. Dit is de verhouding waarmee de reservering wordt toegepast op de gebruiksrecord. Als flexibiliteit van instantiegrootte is ingeschakeld voor uw reservering, kan dit van toepassing zijn op andere grootten. De waarde toont de verhouding waarmee de reservering is toegepast voor de gebruiksrecord.

[Zie de velddefinitie](/rest/api/consumption/usagedetails/list#definitions)

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Gebruiks- en reserveringsgegevens van Azure opvragen met een API

U kunt de gegevens opvragen met behulp van de API of door ze te downloaden uit de Azure-portal.

Als u nieuwe gegevens wilt opvragen, roept u de [API voor gebruiksgegevens](/rest/api/consumption/usagedetails/list) aan. Zie de [gebruiksvoorwaarden](../understand/understand-usage.md)voor meer informatie over de gebruikte terminologie.

Hier ziet u een voorbeeld van een aanroep naar de API voor gebruiksgegevens:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

Zie het artikel over [de API voor gebruiksgegevens](/rest/api/consumption/usagedetails/list) voor meer informatie over {enrollmentId} en {billingPeriodId}.

De informatie in de onderstaande tabel over metrische gegevens en filters kan helpen bij het oplossen van veelvoorkomende reserveringsproblemen.

| **Type API-gegevens** | Actie van API-aanroep |
| --- | --- |
| **Alle kosten (gebruik en aankopen)** | Vervang {metric} door ActualCost |
| **Gebruik waarvoor een reserveringskorting is ontvangen** | Vervang {metric} door ActualCost<br><br>Vervang {Filter} door: properties/reservationId%20ne%20 |
| **Gebruik waarvoor geen reserveringskorting is ontvangen** | Vervang {metric} door ActualCost<br><br>Vervang {Filter} door: properties/reservationId%20eq%20 |
| **Afgeschreven kosten (gebruik en aankopen)** | Vervang {metric} door AmortizedCost |
| **Rapport ongebruikte reserveringen** | Vervang {metric} door AmortizedCost<br><br>Vervang {filter} door: properties/ChargeType%20eq%20'UnusedReservation' |
| **Reserveringsaankopen** | Vervang {metric} door ActualCost<br><br>Vervang {filter} door: properties/ChargeType%20eq%20'Purchase'  |
| **Restituties** | Vervang {metric} door ActualCost<br><br>Vervang {filter} door: properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>CSV-bestand met nieuwe gebruiksgegevens downloaden

Als u een EA-beheerder bent, kunt u het CSV-bestand met nieuwe gebruiksgegevens downloaden van Azure Portal. Deze gegevens zijn niet beschikbaar vanuit de EA Portal (ea.azure.com). U moet het gebruiksbestand downloaden via de Azure-portal (portal.azure.com) om de nieuwe gegevens te zien.

Ga in de Azure-portal naar [Kostenbeheer en facturering](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Selecteer het factureringsaccount.
2. Klik op **Gebruik en kosten**.
3. Klik op **Downloaden**.  
![Voorbeeld waarin wordt aangegeven waar u het CSV-bestand met gebruiksgegevens kunt downloaden in de Azure-portal](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. Ga naar **Gebruik en kosten downloaden**, selecteer onder **Versie 2 gebruiksdetails** **Alle kosten (gebruik en aankopen)** en klik vervolgens op Downloaden. Herhaal dit voor **Afgeschreven kosten (gebruik en aankopen)** .


## <a name="common-cost-and-usage-tasks"></a>Algemene taken voor kosten en gebruik

De volgende secties bevatten algemene taken die vaak worden gebruikt om reserveringskosten en gebruiksgegevens te bekijken.

### <a name="get-reservation-purchase-costs"></a>Aankoopkosten van reserveringen opvragen

Aankoopkosten voor reserveringen zijn terug te vinden in de gegevens over werkelijke kosten. Filter op _ChargeType = Purchase_. Raadpleeg de ProductOrderID om te bepalen op welke reserveringsorder de aankoop betrekking heeft.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Hoeveelheid en kosten voor niet volledig gebruikte reserveringen opvragen

Vraag gegevens voor afgeschreven kosten op en filter op _ChargeType_ _= UnusedReservation_. U ziet dan de hoeveelheid en kosten per dag voor ongebruikte reserveringen. U kunt de gegevens filteren op een reservering of reserveringsorder door respectievelijk de velden _ReservationId_ en _ProductOrderId_ te gebruiken. Als een reservering 100% is gebruikt, is de hoeveelheid van de record 0.

### <a name="amortize-reservation-costs"></a>Reserveringskosten afschrijven

Vraag gegevens van afgeschreven kosten op en filter op een reserveringsorder met _ProductOrderID_ om per dag de afgeschreven kosten voor een reservering te bekijken.

### <a name="chargeback-for-a-reservation"></a>Toerekenen van een reservering

U kunt reserveringen aan andere organisaties toerekenen per abonnement, resourcegroepen of tags. Gegevens van afgeschreven kosten tonen de monetaire waarde voor het gebruik van een reservering voor de volgende gegevenstypen:

- Resources (zoals een VM)
- Resourcegroep
- Tags
- Abonnement

### <a name="get-the-blended-rate-for-chargeback"></a>Gemengd tarief voor toerekening opvragen

Als u het gemengde tarief wilt bepalen, vraagt u de gegevens van afgeschreven kosten op en aggregeert u de totale kosten. Voor VM's kunt u de gegevens voor MeterName of ServiceType uit de JSON-gegevens AdditionalInfo gebruiken. Deel de totale kosten door de hoeveelheid om het gemengde tarief te bepalen.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Optimaal gebruik van reserveringen controleren voor flexibiliteit van instantiegrootte

Vermenigvuldig de hoeveelheid met de _RINormalizationRatio_, in AdditionalInfo. De resultaten geven aan hoeveel uur van het gebruik van de reservering is toegepast op de gebruiksrecord.

### <a name="determine-reservation-savings"></a>Reserveringsbesparingen bepalen

Vraag gegevens van afgeschreven kosten op en filter de gegevens op een gereserveerde instantie. Daarna kunt u het volgende doen:

1. Geschatte kosten voor betalen naar gebruik opvragen. Vermenigvuldig de waarde voor _UnitPrice_ met de waarden voor _Quantity_ om een schatting te krijgen van de kosten voor betalen naar gebruik, als de reserveringskorting niet is toegepast op het gebruik.
2. De reserveringskosten opvragen. Bereken de som van de waarden voor _Cost_ om de monetaire waarde te bepalen van wat u hebt betaald voor de gereserveerde instantie. De berekende waarde omvat de gebruikte en ongebruikte kosten van de reservering.
3. Reserveringskosten aftrekken van de geschatte kosten voor betalen naar gebruik om de geschatte besparingen te bepalen.

Let op: als u een reservering hebt die te weinig wordt gebruikt, is de vermelding _UnusedReservation_ voor _ChargeType_ een factor om rekening mee te houden. Als u een reservering hebt die volledig wordt gebruikt, bespaart u maximaal. Elke hoeveelheid _UnusedReservation_ vermindert wat u bespaart.

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>Reserveringsaankopen en afschrijving in kostenanalyse

Reserveringskosten zijn beschikbaar in [Kostenanalyse](https://aka.ms/costanalysis). In Kostenanalyse worden standaard de **werkelijke kosten** weergegeven. Dit zijn de kosten die op uw factuur worden vermeld. Als u de reserveringsaankopen wilt uitsplitsen naar de resources die het voordeel hebben gebruikt, schakelt u over naar **Afgeschreven kosten**:

![Voorbeeld waarin wordt aangegeven waar u afgeschreven kosten kunt selecteren in Kostenanalyse](./media/understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Groepeer op kostensoort om een uitsplitsing te zien naar gebruik, aankopen en restituties. Groepeer op reservering voor een uitsplitsing van de kosten voor reserveringen en on-demand. Vergeet niet dat als u naar de werkelijke kosten kijkt, alleen de reserveringskosten voor aankopen worden weergegeven. De kosten worden echter wel toegerekend aan de afzonderlijke resources die het voordeel hebben gebruikt, als u naar de afgeschreven kosten kijkt. U ziet ook een nieuwe kostensoort **UnusedReservation** wanneer u de afgeschreven kosten bekijkt.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

- [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md)
- [Vooruitbetalen voor Virtual Machines met Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Begrijpen hoe de reserveringskorting wordt toegepast](../manage/understand-vm-reservation-charges.md)
- [Kosten van Windows-software zijn niet inbegrepen bij reserveringen](reserved-instance-windows-software-costs.md)
- [Service-principal gebruiken om kostengegevens op te halen](../manage/assign-roles-azure-service-principals.md)
- [Kostenbeheerexports gebruiken](../costs/tutorial-export-acm-data.md)
