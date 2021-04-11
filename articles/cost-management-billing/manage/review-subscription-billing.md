---
title: Factureringsgegevens van het Azure-abonnement controleren met REST API
description: Meer informatie over het gebruik van Azure REST API's om de factureringsgegevens van het abonnement te controleren. U kunt filters gebruiken om de resultaten aan te passen.
author: lleonard-msft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: article
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: f23beeb735c83cdf2d42a01ff684510dbd0d9feb
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058830"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Facturering van abonnementen controleren met behulp van REST-API's

Met de rapportage-API van Azure kunt u uw Azure-kosten controleren en beheren.

Filters helpen u bij het aanpassen van de resultaten zodat deze voldoen aan uw behoeften.

Hier krijgt u informatie over het gebruik van een REST API voor het retourneren van de factureringsgegevens van een abonnement voor een opgegeven datumbereik.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>De aanvraag maken

De parameter `{subscriptionID}` is vereist. Hiermee wordt het doelabonnement geïdentificeerd.

De parameter `{billingPeriod}` is vereist. Hiermee wordt een [actuele factureringsperiode](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) opgegeven.

De parameters `${startDate}` en `${endDate}` zijn vereist voor dit voorbeeld, maar zijn optioneel voor het eindpunt. Hiermee wordt het datumbereik opgegeven als tekenreeksen in de notatie jjjj-mm-dd (voorbeelden: `'20180501'` en `'20180615'`).

De volgende headers zijn vereist:

|Aanvraagheader|Beschrijving|
|--------------------|-----------------|
|*Content-Type:*|Vereist. Ingesteld op `application/json`.|
|*Authorization:*|Vereist. Ingesteld op een geldig `Bearer` [toegangstoken](/rest/api/azure/#authorization-code-grant-interactive-clients). |

## <a name="response"></a>Antwoord

Statuscode 200 (OK) wordt geretourneerd bij een geslaagd antwoord dat een lijst met gedetailleerde kosten voor uw account bevat.

``` json
{
  "value": [
    {
      "id": "/subscriptions/{$subscriptionID}/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/{$detailsID}",
      "name": "{$detailsID}",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}",
        "invoiceId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/invoices/${invoiceID}",
        "usageStart": "${startDate}}",
        "usageEnd": "${endDate}",
        "currency": "USD",
        "usageQuantity": "${usageQuantity}",
        "billableQuantity": "${billableQuantity}",
        "pretaxCost": "${cost}",
        "meterId": "${meterID}",
        "meterDetails": "${meterDetails}"
      }
    }
  ],
  "nextLink": "${nextLinkURL}"
}
```

Elk item in **waarde** vertegenwoordigt een detail over het gebruik van een service:

|Eigenschap van antwoord|Beschrijving|
|----------------|----------|
|**subscriptionGuid** | Wereldwijde unieke id voor het abonnement. |
|**startDate** | Datum waarop het gebruik is gestart. |
|**endDate** | Datum waarop het gebruik is beëindigd. |
|**useageQuantity** | Gebruikte eenheid. |
|**billableQuantity** | Werkelijk gefactureerd aantal. |
|**pretaxCost** | Gefactureerde kosten, vóór toepasselijke belastingen. |
|**meterDetails** | Gedetailleerde informatie over het gebruik. |
|**nextLink**| Als deze instelling is ingesteld, wordt er een URL opgegeven voor de volgende pagina met details. Leeg wanneer de pagina de laatste is. |

Dit voorbeeld is afgekort. Zie [Gebruiksdetails voor lijst](/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod-legacy) voor een volledige beschrijving van elk antwoordveld.

Andere statuscodes wijzen op een fout. In deze gevallen wordt in het antwoordobject uitgelegd waarom de aanvraag is mislukt.

``` json
{
  "error": [
    {
      "code": "Error type.",
      "message": "Error response describing why the operation failed."
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen
- Bekijk [Overzicht van Enterprise Reporting](./enterprise-api.md)
- Onderzoek [REST API voor Enterprise-facturering](/rest/api/billing/)
- [Aan de slag gaan met Azure REST API](/rest/api/azure/)