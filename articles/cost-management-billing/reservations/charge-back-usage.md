---
title: Azure-reserveringskosten terugstorten
description: Meer informatie over het weergeven van Azure-reserveringskosten voor terugstorting.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: 4059318e6b8052f3b0221c87e8a357cfc8679e44
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532762"
---
# <a name="charge-back-azure-reservation-costs"></a>Azure-reserveringskosten terugstorten

Enterprise Enrollment-factureringslezers en factureringslezers van een Microsoft-klantovereenkomst kunnen afgeschreven kostengegevens voor reserveringen weergeven. Ze kunnen deze kostengegevens gebruiken om de monetaire waarde voor een abonnement, resourcegroep, resource of een tag terug te storten aan hun partners. In afgeschreven gegevens zijn de effectieve prijs de evenredige reserveringskosten per uur. De kosten zijn de totale kosten voor reserveringsgebruik door de resource op die dag.

Gebruikers met een individueel abonnement kunnen de afgeschreven kostengegevens bekijken in hun gebruiksbestand. Wanneer een resource een reserveringskorting krijgt, bevat het gedeelte *AdditionalInfo* in het gebruiksbestand de reserveringsdetails. Zie [Gebruiksgegevens downloaden vanuit Azure Portal](../understand/download-azure-daily-usage.md#download-usage-from-the-azure-portal-csv) voor meer informatie.

## <a name="see-reservation-usage-data-for-show-back-and-charge-back"></a>Zie reserveringsgebruiksgegevens voor terug- en terugkosten

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Ga naar **Cost Management and Billing** 
3. Kostenanalyse **selecteren** in het linkernavigatievenster 
4. Selecteer onder **Werkelijke kosten** de metriek **Afgeschreven kosten**.
5. Als u wilt zien welke resources door een reservering zijn gebruikt, past u een filter toe voor **Reservering** en selecteert u vervolgens reserveringen.
6. Stel **Granulariteit** in op **Maandelijks** of **Dagelijks**.
7. Stel het grafiektype in op **Tabel**.
8. Stel de optie **Groeperen op** in op **Resource**.

[![Voorbeeld van resources van reserveringskosten die u voor terugstorting kunt gebruiken](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

Hier is een video waarin wordt getoond hoe u de kosten voor reserveringsgebruik op abonnements-, resourcegroep- en resourceniveau in de Azure Portal.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="get-the-data-for-show-back-and-charge-back"></a>De gegevens voor show terug en terug in rekening brengen
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Ga naar **Cost Management and Billing** 
3. Selecteer **Exporteren in** de linkernavigatiebalk 
4. Klik op **de knop** Toevoegen
5. Selecteer Afgeschreven kosten als de knop met metrische gegevens en stel uw export in

De EffectivePrice voor het gebruik dat reserveringskorting krijgt, zijn de pro prognosekosten van de reservering (in plaats van nul). Deze gegevens helpen bij het bepalen van de monetaire waarde van reserveringsverbruik door een abonnement, resourcegroep of resource, en kan u helpen om het gebruik van de reservering intern toe te rekenen. De gegevensset heeft ook ongebruikte reserveringsuren. 

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
4. Selecteer **in Gebruiksgegevens** **de optie Afgeschreven gebruiksgegevens.**

De CSV-bestanden die u downloadt, bevatten de werkelijke kosten en afgeschreven kosten.

## <a name="need-help-contact-us"></a>Hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen
- Zie de volgende artikelen voor meer informatie over gebruiksgegevens van Azure Reservations:
  - [Enterprise Agreement en Microsoft-klantovereenkomst reserveringskosten en -gebruik](understand-reserved-instance-usage-ea.md)
 
