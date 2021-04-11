---
title: Migreren van EA naar API’s voor Microsoft-klantovereenkomsten - Azure
description: Dit artikel helpt u inzicht te krijgen in de gevolgen van het migreren van een Microsoft Enterprise Agreement (EA) naar een Microsoft-klantovereenkomst.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.openlocfilehash: 46ad81f6723d160bf1d675b68a8459dd8df32c80
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078346"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migreren vanuit Enterprise Agreement naar API’s voor Microsoft-klantovereenkomst

Dit artikel helpt u inzicht te krijgen in de verschillen in gegevensstructuur, API's en andere verschillen in de systeemintegratie tussen Enterprise Agreement-accounts (EA) en Microsoft-klantovereenkomsten-accounts (MCA). Azure Cost Management biedt ondersteuning voor API's voor beide accounttypen. Raadpleeg het artikel [Uw factureringsrekening configureren voor een](../manage/mca-setup-account.md) Microsoft-klantovereenkomst voordat u verdergaat.

Organisaties met een bestaand EA-account moeten dit artikel raadplegen en tevens een MCA-account instellen. Voorheen bestond het vernieuwen van een EA-account uit een klein aantal handelingen om van een oude inschrijving op een nieuwe over te stappen. Voor de migratie naar een MCA-account zijn echter wat extra handelingen nodig. Dit komt omdat er dingen zijn gewijzigd in het onderliggende subsysteem voor facturering, wat van invloed is op alle API's en serviceaanbiedingen die aan kosten zijn gerelateerd.

## <a name="mca-apis-and-integration"></a>MCA-API's en integratie

Met MCA-API's en de nieuwe integratie:

- Hebt u de volledige beschikking over API's via systeemeigen Azure-API's.
- Kunt u meerdere facturen configureren in één enkel factureringsaccount.
- Hebt u toegang tot een gecombineerd gebruik van API's en de Azure-service, kunt u gebruikmaken van een marktplaats van een derde partij en Marketplace-aankopen doen.
- Kunt u kosten bekijken voor meerdere factureringsprofielen (hetzelfde als inschrijvingen) met behulp van Azure Cost Management.
- Kunt u nieuwe API's gebruiken om kosten weer te geven, meldingen ontvangen als kosten de vooraf gedefinieerde drempelwaarden overschrijden en onbewerkte gegevens automatisch exporteren.

## <a name="migration-checklist"></a>Migratiecontrolelijst

De volgende items helpen u bij de overstap naar MCA-API's.

- Maak u vertrouwd met de nieuwe [factureringsrekening van de Microsoft-klantovereenkomst](../understand/mca-overview.md).
- Bepaal welke API's u gebruikt en bekijk in de volgende sectie welke worden vervangen.
- Maak u vertrouwd met [Azure Resource Manager REST API's](/rest/api/azure).
- Als u nog geen Azure Resource Manager-API's gebruikt, [moet u uw client-app registreren bij Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Werk alle programmeercode bij voor het gebruik van [Azure Active Directory-verificatie](/rest/api/azure/#create-the-request).
- Werk alle programmeercode bij om EA-API-aanroepen te vervangen door MCA-API-aanroepen.
- Foutafhandeling bijwerken om nieuwe foutcodes te gebruiken.
- Bekijk aanvullende integratieaanbiedingen, zoals Cloudyn en Power BI voor aanvullende acties die u moet nemen.

## <a name="ea-apis-replaced-with-mca-apis"></a>EA-API's vervangen door MCA-API's

EA-API's gebruiken een API-sleutel voor verificatie en autorisatie. MCA-API's gebruiken Azure Active Directory-verificatie.

| Doel | EA-API | MCA-API |
| --- | --- | --- |
| Saldo en tegoeden | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Gebruik (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Gebruik (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Marketplace-gebruik (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Factureringsperioden | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Prijzenoverzicht | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Micro soft. billing/billingAccounts/billingProfiles/prijzen overzicht/default/down load format = json \| CSV micro soft. billing/billingAccounts/.../billingProfiles/.../facturen/... /pricesheet/default/Download-indeling = JSON \| CSV micro soft. billing/billingAccounts/.. /billingProfiles/.. /providers/Microsoft.Consumption/pricesheets/download  |
| Reserveringsaankopen | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Aanbevelingen voor reserveringen | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations) [/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) [SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Reserveringsgebruik | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> Azure-service en gebruik van een marktplaats van een derde partij is beschikbaar met de [API voor gedetailleerde gebruiksgegevens](/rest/api/consumption/usagedetails).

De volgende API's zijn beschikbaar voor MCA-factureringsaccounts:

| Doel | MCA-API (Microsoft-klantovereenkomst) |
| --- | --- |
| Factureringsaccounts<sup>2</sup> | Microsoft.Billing/billingAccounts |
| Factureringsprofielen<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Factuursecties<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Facturen | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Factureringsabonnementen | {scope}/billingSubscriptions |

<sup>2</sup> Met API's worden lijsten met objecten geretourneerd. Dit zijn bereiken waar Cost Management-ervaringen in de Azure-portal en API's worden gebruikt. Zie [Understand and work with scopes](understand-work-scopes.md) (Engelstalig) voor meer informatie over bereiken in Cost Management.

Als u bestaande EA-API's gebruikt, moet u deze bijwerken om ondersteuning te kunnen bieden voor MCA-factureringsaccounts. De volgende tabel bevat andere wijzigingen in de integratie:

| Doel | Oude aanbieding | Nieuwe aanbieding |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights)-inhoudspakket en connector |  [Azure Consumption Insights-connector](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>API's om saldo en tegoeden op te halen

De API voor het [ophalen van het saldo-overzicht](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) biedt u een maandelijks overzicht van:

- Tegoeden
- Nieuwe aankopen
- Servicekosten voor Azure Marketplace
- Aanpassingen
- Serviceoverschrijdingskosten

Alle verbruiks-API's worden vervangen door systeemeigen Azure-API's die gebruikmaken van Azure AD voor verificatie en autorisatie. Zie [Aan de slag met REST](/rest/api/azure/#create-the-request) voor meer informatie over het aanroepen van REST API's.

De API voor het ophalen van saldi is vervangen door de API Microsoft.Billing/billingAccounts/billingProfiles/availableBalance.

Beschikbare saldi ophalen met de API voor het ophalen van beschikbare saldi:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>API's voor het ophalen van kosten en gebruik

Ontvang een op dag uitgesplitst overzicht van het gebruik van Azure-services, het gebruik van een marktplaats van een derde partij en andere Marketplace-aankopen door de volgende API's te gebruiken. De volgende afzonderlijke API's zijn samengevoegd zodat ze kunnen worden gebruikt voor Azure-services en voor het gebruik van een marktplaats van een derde partij. De oude API's worden vervangen door de API [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails). Er worden Marketplace-aankopen mee toegevoegd die eerder alleen werden weergegeven in het saldo-overzicht tot dat moment.

- [Gebruiksdetails ophalen/downloaden](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Gebruiksgegevens ophalen/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Gebruiksgegevens ophalen/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Gebruiksgegevens ophalen/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Kosten marketplace store ophalen/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Kosten marketplace store ophalen/charge/marketplacechargesbycustomdates](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Alle verbruiks-API's worden vervangen door systeemeigen Azure-API's die gebruikmaken van Azure AD voor verificatie en autorisatie. Zie [Aan de slag met REST](/rest/api/azure/#create-the-request) voor meer informatie over het aanroepen van REST API's.

Alle voorgaande API's worden vervangen door de API voor verbruik/gedetailleerde gebruiksgegevens.

Voor het ophalen van gebruiksgegevens met de API voor gedetailleerde gebruiksgegevens:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

De API voor gedetailleerde gebruiksgegevens is, net als bij alle API's van Cost Management, beschikbaar bij meerdere bereiken. Gebruik, net als doorgaans bij een inschrijvingsniveau, voor gefactureerde kosten het bereik van het factureringsprofiel.  Zie [Understand and work with scopes](understand-work-scopes.md) (Engelstalig) voor meer informatie over bereiken in Cost Management.

| Type | Id-indeling |
| --- | --- |
| Factureringsaccount | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Factureringsprofiel | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Abonnement | `/subscriptions/{subscriptionId}` |
| Resourcegroep | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Gebruik de volgende querystringparameters om eventuele programmeercode bij te werken.

| Oude parameters | Nieuwe parameters |
| --- | --- |
| `billingPeriod={billingPeriod}` | Niet ondersteund |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

De hoofdtekst van de reactie is ook gewijzigd.

Oude hoofdtekst van de reactie:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Nieuwe hoofdtekst van de reactie:

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

De naam van de eigenschap die de matrix met gebruiksrecords bevat, is gewijzigd van gegevens in _waarden_. Elk record bevatte in het verleden een platte lijst met gedetailleerde eigenschappen. Elk record bevat nu echter alle details in een geneste eigenschap met de naam _eigenschappen_, met uitzondering van tags. De nieuwe structuur is consistent met die van andere Azure-API's. Sommige eigenschapsnamen zijn gewijzigd. In de volgende tabel worden de bijbehorende eigenschappen weergegeven.

| Oude eigenschap | Nieuwe eigenschap | Notities |
| --- | --- | --- |
| AccountId | N.v.t. | De maker van het abonnement wordt niet bijgehouden. Gebruik invoiceSectionId (hetzelfde als departmentId). |
| AccountNameAccountOwnerId en AccountOwnerEmail | N.v.t. | De maker van het abonnement wordt niet bijgehouden. Gebruik invoiceSectionName (hetzelfde als departmentName). |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Houd er rekening mee dat deze eigenschappen tegenovergesteld aan elkaar zijn. Als isAzureCreditEnabled is ingesteld op waar, wordt ChargesBilledSeparately onwaar. |
| ConsumedQuantity | quantity | &nbsp; |
| ConsumedService | consumedService | De exacte tekenreekswaarden kunnen verschillen. |
| ConsumedServiceId | Geen | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Date en usageStartDate | date | &nbsp;  |
| Dag | Geen | Begindatum van parseren dag. |
| DepartmentId | invoiceSectionId | De exacte waarden verschillen. |
| DepartmentName | invoiceSectionName | De exacte tekenreekswaarden kunnen verschillen. Zo nodig kunt u factuursecties configureren, zodat deze overeenkomen met afdelingen. |
| ExtendedCost en Cost | CostInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| Zijn terugkerende kosten | Geen | &nbsp;  |
| Locatie | location | &nbsp;  |
| MeterCategory | meterCategory | De exacte tekenreekswaarden kunnen verschillen. |
| MeterId | meterId | De exacte tekenreekswaarden verschillen. |
| MeterName | meterName | De exacte tekenreekswaarden kunnen verschillen. |
| MeterRegion | meterRegion | De exacte tekenreekswaarden kunnen verschillen. |
| MeterSubCategory | meterSubCategory | De exacte tekenreekswaarden kunnen verschillen. |
| Maand | Geen | Begindatum van parseren maand. |
| Naam van aanbieding | Geen | Gebruik publisherName en productOrderName. |
| OfferID | Geen | &nbsp;  |
| Bestelnummer | Geen | &nbsp;  |
| PartNumber | Geen | Gebruik meterId en productOrderName om prijzen uniek aan te duiden. |
| Naam van het plan | productOrderName | &nbsp;  |
| Product | Product |   |
| ProductId | productId | De exacte tekenreekswaarden verschillen. |
| Naam van de uitgever | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | De exacte tekenreekswaarden verschillen. |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | Geen | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | N.v.t. | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | De exacte tekenreekswaarden kunnen verschillen. |
| ServiceTier | meterSubCategory | De exacte tekenreekswaarden kunnen verschillen. |
| StoreServiceIdentifier | N.v.t. | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Tags | tags | De eigenschap van de tag is van toepassing op het hoofdobject, niet op de eigenschap van geneste eigenschappen. |
| UnitOfMeasure | unitOfMeasure | De exacte tekenreekswaarden verschillen. |
| usageEndDate | date | &nbsp;  |
| Year | Geen | Begindatum van parseren jaar. |
| (nieuw) | billingCurrency | De valuta die wordt gebruikt voor de kosten. |
| (nieuw) | billingProfileId | De unieke id voor het factureringsprofiel (hetzelfde als inschrijving). |
| (nieuw) | billingProfileName | Naam van het factureringsprofiel (hetzelfde als inschrijving). |
| (nieuw) | chargeType | Gebruik deze om onderscheid te maken tussen het gebruik van Azure-services, het gebruik van Marketplace en aankopen. |
| (nieuw) | InvoiceId | De unieke id voor de factuur. Leeg voor de huidige, open maand. |
| (nieuw) | publisherType | Het type uitgever voor aankopen. Leeg voor gebruik. |
| (nieuw) | serviceFamily | Type aankoop. Leeg voor gebruik. |
| (nieuw) | servicePeriodEndDate | Einddatum voor de aangeschafte service. |
| (nieuw) | servicePeriodStartDate | Begindatum voor de aangeschafte service. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>API voor factureringsperioden is vervangen door API voor facturen:

MCA-factureringsaccounts gebruiken geen factureringsperioden. In plaats daarvan worden er facturen gebruikt om het bereik van de kosten te beperken tot specifieke factureringsperioden. De [API voor factureringsperioden](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) is vervangen door de API voor facturen: Alle verbruiks-API's worden vervangen door systeemeigen Azure-API's die gebruikmaken van Azure AD voor verificatie en autorisatie. Zie [Aan de slag met REST](/rest/api/azure/#create-the-request) voor meer informatie over het aanroepen van REST API's.

Facturen ophalen met de API voor facturen:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>API's voor prijzenoverzichten

In deze sectie worden de bestaande API's voor prijzenoverzichten besproken en worden aanbevelingen gedaan voor de overstap naar de API voor prijzenoverzichten voor Microsoft-klantovereenkomsten. Ook wordt de API voor prijzenoverzichten voor Microsoft-klantovereenkomsten beschreven en worden de velden in de prijzenoverzichten uitgelegd. De API's [ophalen van prijzenoverzicht voor Enterprise-klanten](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) en [factureringsperioden ophalen voor Enterpise-klanten](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) worden vervangen door de API voor prijzenoverzichten voor Microsoft-klantovereenkomsten (Microsoft.Billing/billingAccounts/billingProfiles/pricesheet). De nieuwe API biedt zowel ondersteuning voor JSON- als CSV-indelingen, in asynchrone REST-indelingen. Alle verbruiks-API's worden vervangen door systeemeigen Azure-API's die gebruikmaken van Azure AD voor verificatie en autorisatie. Zie [Aan de slag met REST](/rest/api/azure/#create-the-request) voor meer informatie over het aanroepen van REST API's.

### <a name="billing-enterprise-apis"></a>Facturerings-API's voor Enterprise-klanten

U hebt facturerings-API's voor Enterprise-klanten gebruikt met Enterprise-inschrijvingen voor het ophalen van gegevens over de prijs en de factureringsperiode. Voor verificatie en autorisatie werd gebruikgemaakt van Azure Active Directory-webtokens.

De juiste prijzen ophalen voor de opgegeven Enterprise-inschrijving met de AP's voor prijzenoverzichten en de API voor het ophalen van de factureringsperiode:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>API voor prijzenoverzichten voor Microsoft-klantovereenkomst

Gebruik de API voor prijzenoverzichten voor Microsoft-klantovereenkomsten om de prijzen voor alle verbruiksservices van Azure en Marketplace te bekijken. De prijzen die voor het factureringsprofiel worden weergegeven, zijn van toepassing op alle abonnementen die deel uitmaken van het factureringsprofiel.

Gebruik de API voor prijzenoverzichten om alle gegevens van prijzenoverzichten voor alle verbruiksservices van Azure in CSV-indeling weer te geven:

| Methode | Aanvraag-URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Gebruik de API voor prijzenoverzichten om alle gegevens van prijzenoverzichten voor alle verbruiksservices van Azure in JSON-indeling weer te geven:

| Methode | Aanvraag-URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Met de API wordt het prijzenoverzicht voor het hele account geretourneerd. U kunt echter ook een verkorte versie van het prijzenoverzicht in PDF-indeling krijgen. Het overzicht omvat gegevens over verbruiksservices van Azure en Marketplace die met een specifieke factuur worden gefactureerd. De factuur wordt aangeduid met de {invoiceId}, die gelijk is aan het **factuurnummer**, en wordt weergegeven in de PDF-bestanden met de factuuroverzichten. Hier volgt een voorbeeld.

![Voorbeeldafbeelding met het factuurnummer dat overeenkomt met de InvoiceId](./media/migrate-cost-management-api/invoicesummary.png)

Factuurgegevens weergeven in CSV-indeling met de API voor prijzenoverzichten:

| Methode | Aanvraag-URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Factuurgegevens weergeven in JSON-indeling met de API voor prijzenoverzichten:

| Methode | Aanvraag-URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

U kunt ook geschatte prijzen bekijken voor elke verbruiksservice van Azure en Marketplace in de huidige openstaande factureringscyclus of serviceperiode.

Geschatte prijzen voor verbruiksservices weergeven in CSV-indeling met de API voor prijzenoverzichten:

| Methode | Aanvraag-URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Geschatte prijzen voor verbruiksservices weergeven in JSON-indeling met de API voor prijzenoverzichten:

| Methode | Aanvraag-URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

De API's voor prijzenoverzichten voor Microsoft-klantovereenkomsten zijn *asynchrone REST API's*. De reacties voor de API's zijn gewijzigd ten opzichte van de oudere synchrone API's. De hoofdtekst van de reactie van de API is ook gewijzigd.

#### <a name="old-response-body"></a>Oude hoofdtekst van de reactie

Hier volgt een voorbeeld van een reactie van de synchrone REST API:

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
            "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
            "meterName": "A1 VM",
            "unitOfMeasure": "100 Hours",
            "includedQuantity": 0,
            "partNumber": "N7H-00015",
            "unitPrice": 0.00,
            "currencyCode": "USD"
        },
        {
    ]
```

#### <a name="new-response-body"></a>Nieuwe hoofdtekst van de reactie

De API's bieden ondersteuning voor de [asynchrone Azure REST](../../azure-resource-manager/management/async-operations.md)-indeling. Als de API wordt aangeroepen met GET, ontvangt u de volgende reactie:

```
No Response Body

HTTP Status 202 Accepted
```

De volgende headers worden verzonden met de locatie van de uitvoer:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Maak een andere GET-aanroep naar de locatie. De reactie op de GET-aanroep is hetzelfde ongeacht of de bewerking kan worden voltooid of niet. Wanneer deze is voltooid, wordt de download-URL geretourneerd als reactie op de aanroep naar de locatie. Net alsof de bewerking op hetzelfde moment is uitgevoerd. Hier volgt een voorbeeld:

```
HTTP Status 200

{
  "id": "providers/Microsoft.Consumption/operationresults/{operationId}",
  "name": {operationId},
  "type": “Microsoft.Consumption/operationResults",
  "properties" : {
    "downloadUrl": {urltoblob},
    "validTill": "Date"
  }
}
```

De client kan ook een GET-aanroep uitvoeren voor de `Azure-AsyncOperation`. Het eindpunt retourneert de status van de bewerking.

De volgende tabel bevat velden in de oudere API voor het ophalen van prijzenoverzichten voor Enterprise-klanten. De tabel bevat overeenkomende velden in het nieuwe prijzenoverzicht voor Microsoft-klantovereenkomsten:

| Oude eigenschap | Nieuwe eigenschap | Notities |
| --- | --- | --- |
| billingPeriodId  | _Niet van toepassing_ | Niet van toepassing. Voor Microsoft-klantovereenkomsten is het concept van billingPeriodId vervangen door de factuur en het bijbehorende prijzenoverzicht. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | De exacte tekenreekswaarden kunnen verschillen. |
| includedQuantity  | includedQuantity | Niet van toepassing op services in Microsoft-klantovereenkomsten. |
| partNumber  | _Niet van toepassing_ | Gebruik in plaats daarvan een combinatie van productOrderName (hetzelfde als offerID) en meterID. |
| unitPrice  | unitPrice | Eenheidsprijs is niet van toepassing op services die worden verbruikt als onderdeel van Microsoft-klantovereenkomsten. |
| currencyCode  | pricingCurrency | In Microsoft-klantovereenkomsten worden prijzen weergegeven in de prijsvaluta en factureringsvaluta. In Microsoft-klantovereenkomsten komen de currencyCode en de pricingCurrency met elkaar overeen. |
| offerID | productOrderName | In plaats van OfferID kunt u productOrderName gebruiken, maar dit is niet hetzelfde als de OfferID. ProductOrderName en meter bepalen de prijzen in Microsoft-klantovereenkomsten met betrekking tot meterId en OfferID in verouderde inschrijvingen. |

## <a name="consumption-price-sheet-api-operations"></a>Bewerkingen van de API voor prijzenoverzichten voor verbruik

Voor Enterprise-overeenkomsten hebt u de bewerkingen [Ophalen](/rest/api/consumption/pricesheet/get) en [Ophalen per factureringsperiode](/rest/api/consumption/pricesheet/getbybillingperiod) gebruikt voor een bereik op subscriptionId of factureringsperiode. De API maakt gebruik van verificatie door Azure Resource Management.

De informatie van een prijzenoverzicht ophalen voor een bereik met de API voor het prijzenoverzicht:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Informatie van een prijzenoverzicht ophalen per factureringsperiode met de API voor het prijzenoverzicht:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Gebruik voor Microsoft-klantovereenkomsten in plaats van de bovenstaande API-eindpunten de volgende:

**API's voor prijzenoverzichten voor Microsoft-klantovereenkomsten (asynchrone REST API)**

Deze API is voor Microsoft-klantovereenkomsten en biedt extra kenmerken.

**Prijzen overzicht voor een facturerings profiel bereik in een facturerings account**

Deze API is de bestaande API. Deze is bijgewerkt om het prijzenoverzicht voor een factureringsprofiel in een factureringsaccount te verkrijgen.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Prijzenoverzicht voor een bereik per factureringsaccount

Verificatie van Azure Resource Manager wordt gebruikt wanneer u het prijzenoverzicht in het inschrijvingsbereik in een factureringsaccount ophaalt.

Het prijzenoverzicht ophalen bij het inschrijvingsaccount in een factureringsaccount:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Voor een Microsoft-klantovereenkomst gebruikt u de informatie in de volgende sectie. Deze bevat de veldeigenschappen die worden gebruikt voor Microsoft-klantovereenkomsten.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Prijzenoverzicht voor een factureringsprofielbereik in een factureringsaccount

Het bijgewerkte prijzenoverzicht per factureringsaccount-API haalt het prijzenoverzicht op in CSV-indeling. U kunt als volgt het prijzenoverzicht voor het factureringsprofiel voor een MCA ophalen:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Bij het inschrijvingsbereik van EA zijn de API-reactie en eigenschappen identiek. De eigenschappen komen overeen met dezelfde MCA-eigenschappen.

De oudere eigenschappen voor [API's voor prijzenoverzichten van Azure Resource Manager](/rest/api/consumption/pricesheet) en de overeenkomstige nieuwe eigenschappen bevinden zich in de volgende tabel.

| Oude eigenschap van API voor prijzenoverzichten van Azure Resource Manager  | Nieuwe eigenschap van API voor prijzenoverzichten voor Microsoft-klantovereenkomsten   | Description |
| --- | --- | --- |
| Meter ID | _meterId_ | Unieke identificatie voor de meter. Hetzelfde als meterID. |
| Meternaam | meterName | Naam van de meter. De meter vertegenwoordigt de implementeerbare resource van een Azure-service. |
| Metercategorie  | service | De naam van de classificatiecategorie voor de meter. Dezelfde als de service in uw prijzenoverzicht voor een Microsoft-klantovereenkomst. De exacte tekenreekswaarden verschillen. |
| Subcategorie van de meter | meterSubCategory | De naam van de subclassificatiecategorie voor de meter. Op basis van de classificatie van de differentiatie op algemeen niveau van de functieset in de service. Bijvoorbeeld: Basic SQL DB versus Standard SQL DB. |
| De regio van de meter | meterRegion | &nbsp;  |
| Eenheid | _Niet van toepassing_ | Kan worden geparseerd vanuit unitOfMeasure. |
| Meeteenheid | unitOfMeasure | &nbsp;  |
| Onderdeelnummer | _Niet van toepassing_ | Gebruik productOrderName en MeterID in plaats van het onderdeelnummer als unieke identificatie van de prijs van een factureringsprofiel. Velden worden op de MCA-factuur weergegeven in plaats van het onderdeelnummer op MCA-facturen. |
| Eenheidsprijs | unitPrice | Eenheidsprijs in Microsoft-klantovereenkomst. |
| Valutacode | pricingCurrency | In Microsoft-klantovereenkomsten worden prijzen weergegeven in prijsvaluta en factureringsvaluta. In Microsoft-klantovereenkomsten komen de valutacode en de pricingCurrency met elkaar overeen. |
| Inbegrepen hoeveelheid | includedQuantity | Niet van toepassing op services in Microsoft-klantovereenkomsten. Worden weergegeven met waarden van nul. |
|  Aanbiedings-id  | productOrderName | Gebruik productOrderName in plaats van OfferID. Niet hetzelfde als OfferID, maar de productOrderName en meter bepalen prijzen in Microsoft-klantovereenkomsten. Gerelateerd aan meterId en OfferID in verouderde inschrijvingen. |

De prijs voor Microsoft-klantovereenkomsten is anders gedefinieerd dan die voor Enterprise-overeenkomsten. De prijs voor services in de Enterprise-inschrijving is uniek voor het product, het onderdeelnummer, de meter en de aanbieding. Het onderdeelnummer wordt niet gebruikt in Microsoft-klantovereenkomsten.

De prijs van de Azure-verbruiksservice die deel uitmaakt van een Microsoft-klantovereenkomst is uniek voor productOrderName en meterID. Ze vertegenwoordigen de servicemeter en het productabonnement.

Voor afstemming tussen het prijzenoverzicht en het gebruik in de API voor gedetailleerde gebruiksgegevens, kunt u de productOrderName en meterID gebruiken.

Gebruikers met de rechten van een factureringsprofieleigenaar, inzender, lezer en factuurbeheerder kunnen het prijzenoverzicht downloaden.

Het prijzenoverzicht bevat prijzen voor services waarvoor de prijs is gebaseerd op gebruik. De services omvatten Azure-verbruik en Marketplace-verbruik. De meest recente prijs aan het einde van elke serviceperiode is vergrendeld en wordt toegepast op het gebruik in één serviceperiode. Voor Azure-verbruiksservices is de serviceperiode doorgaans een kalendermaand.

### <a name="retired-price-sheet-api-fields"></a>Buiten gebruik gestelde velden in de API voor prijzenoverzichten

De volgende velden zijn niet beschikbaar in de API's voor prijzenoverzichten voor Microsoft-klantovereenkomsten of ze hebben dezelfde velden.

|Buiten gebruik gesteld veld| Description|
|---|---|
| billingPeriodId | Niet van toepassing. Komt overeen met InvoiceId voor MCA. |
| offerID | Niet van toepassing. Komt overeen met productOrderName in MCA. |
| meterCategory  | Niet van toepassing. Komt overeen met Service in MCA. |
| eenheid | Niet van toepassing. Kan worden geparseerd vanuit unitOfMeasure. |
| currencyCode | Hetzelfde als de pricingCurrency in MCA. |
| meterLocation | Hetzelfde als de meterRegion in MCA. |
| partNumber partnumber | Niet van toepassing want het onderdeelnummer wordt niet vermeld op MCA-facturen. Gebruik de combinatie van meterId en productOrderName in plaats van het onderdeelnummer voor het uniek identificeren van prijzen. |
| totalIncludedQuantity | Niet van toepassing. |
| pretaxStandardRate  | Niet van toepassing. |

## <a name="reservation-instance-charge-api-replaced"></a>De API voor kosten van gereserveerde instanties is vervangen

U kunt factureringstransacties ophalen voor reserveringsaankopen met de [API voor kosten van gereserveerde instanties](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Met de nieuwe API kunnen alle aankopen worden opgehaald, inclusief aanbiedingen op een marktplaats van derden. Alle verbruiks-API's worden vervangen door systeemeigen Azure-API's die gebruikmaken van Azure AD voor verificatie en autorisatie. Zie [Aan de slag met REST](/rest/api/azure/#create-the-request) voor meer informatie over het aanroepen van REST API's. De API voor kosten van gereserveerde instanties is vervangen door de API voor transacties.

Reserveringsaankopen ophalen met de API voor transacties:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>API's voor aanbevelingen vervangen

Met API's voor aankoopaanbevelingen voor gereserveerde instanties wordt het gebruik van virtuele machines gedurende de afgelopen 7, 30 of 60 dagen opgehaald. Met API's kunnen ook aanbevelingen voor reserveringsaankopen worden opgehaald. Deze omvatten:

- [API voor aanbevelingen voor gedeelde gereserveerde instanties](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [API voor aanbevelingen voor individuele gereserveerde instanties](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Alle verbruiks-API's worden vervangen door systeemeigen Azure-API's die gebruikmaken van Azure AD voor verificatie en autorisatie. Zie [Aan de slag met REST](/rest/api/azure/#create-the-request) voor meer informatie over het aanroepen van REST API's. De eerder vermelde API's voor aanbevelingen voor reserveringen worden vervangen door de API [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list).

Reserveringsaanbevelingen ophalen met de API voor aanbevelingen voor reserveringen:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>De API voor reserveringsgebruik is vervangen

U kunt het reserveringsgebruik in een inschrijving ophalen met de API voor reserveringsgebruik. Als er meer dan een gereserveerde instantie voorkomt in een inschrijving, kunt u met deze API ook het gebruik van alle aankopen voor gereserveerde instanties ophalen.

Deze omvatten:

- [Gebruik gereserveerde instanties Details](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [Samenvatting van het gebruik van gereserveerde instanties](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Alle verbruiks-API's worden vervangen door systeemeigen Azure-API's die gebruikmaken van Azure AD voor verificatie en autorisatie. Zie [Aan de slag met REST](/rest/api/azure/#create-the-request) voor meer informatie over het aanroepen van REST API's. De eerder vermelde API's voor aanbevelingen voor reserveringen worden vervangen door de API's [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) en [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries).

Details van reserveringen ophalen met de API voor reserveringsdetails:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Samenvattingen van reserveringen ophalen met de API voor reserveringssamenvattingen:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Van Cloudyn overstappen op Cost Management

Organisaties die [Cloudyn](https://cloudyn.com) gebruiken zouden moeten overstappen op [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) voor zaken die te maken hebben met kostenbeheer. Cost Management is met een latentie van acht uur beschikbaar via de Azure-portal en vereist geen onboarding. Raadpleeg de [Documentatie over Cost Management](../index.yml) voor meer informatie.

Met Azure Cost Management kunt u:

- Kosten in de loop van de tijd bekijken afgezet tegen een vooraf gedefinieerd budget. Dagelijkse kostenpatronen analyseren om afwijkingen in uitgaven op te sporen en te beëindigen. Kosten opsplitsen per tag, resourcegroep, service en locatie.
- Budgets maken om limieten in te stellen voor het gebruik en de kosten, en instellen dat u meldingen ontvangt als er belangrijke drempelwaarden worden bereikt. Automatiseringen instellen zodat actiegroepen aangepaste gebeurtenissen activeren en harde limieten voor uw voorwaarden afdwingen.
- Kosten en verbruik optimaliseren met behulp van aanbevelingen van Azure Advisor. Aankoopoptimalisaties opsporen voor reserveringen, onderbezette virtuele machines downsizen en ongebruikte resource verwijderen om binnen budgetten te blijven.
- Plannen dat er een export wordt gemaakt van de kosten en gebruiksgegevens, en dat elke dag een CSV-bestand met deze gegevens in uw opslagaccount wordt gepubliceerd. De integratie met externe systemen automatiseren om ervoor te zorgen dat factureringsgegevens altijd gesynchroniseerd en actueel zijn.

## <a name="power-bi-integration"></a>Integratie met Power BI

U kunt ook Power BI voor het rapporteren van kosten gebruiken. De [Azure Cost Management-connector](/power-bi/desktop-connect-azure-cost-management) voor Power BI Desktop kan worden gebruikt om krachtige, aangepaste rapporten te maken die u helpen om beter zicht te krijgen op uw uitgaven voor Azure. De Azure Cost Management-connector biedt momenteel ondersteuning voor klanten met een Microsoft-klantovereenkomst of een Enterprise Agreement (EA).

## <a name="next-steps"></a>Volgende stappen

- Lees de [documentatie voor Cost Management](../index.yml) voor meer informatie over het bewaken en beheren van uitgaven voor Azure. Of als u uw gebruik van resources wilt optimaliseren met behulp van Cost Management.
