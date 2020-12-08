---
title: Migreren van Enterprise Reporting naar Azure Resource Manager API’s
description: Dit artikel helpt u de verschillen te begrijpen tussen de Reporting API's en de Azure Resource Manager API's, wat u kunt verwachten wanneer u migreert naar de Azure Resource Manager API's, en welke nieuwe mogelijkheden beschikbaar zijn voor de nieuwe Azure Resource Manager API's.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 11/19/2020
ms.author: banders
ms.openlocfilehash: 93dda4fc3a152b0a07a95ff327c9ea619f25787c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355813"
---
# <a name="migrate-from-enterprise-reporting-to-azure-resource-manager-apis"></a>Migreren van Enterprise Reporting naar Azure Resource Manager API’s

Dit artikel helpt ontwikkelaars die aangepaste oplossingen hebben gebouwd met behulp van de [Azure Reporting API's voor Enterprise-klanten](../manage/enterprise-api.md), om te migreren naar de Azure Resource Manager API's voor Cost Management. Service-principal-ondersteuning voor de nieuwere Azure Resource Manager API's is nu algemeen beschikbaar. Azure Resource Manager API's worden momenteel ontwikkeld. U kunt overwegen te migreren naar deze API's in plaats van gebruik te maken van de oudere Azure Reporting API's voor Enterprise-klanten. De oudere API's worden afgeschaft. Dit artikel helpt u de verschillen te begrijpen tussen de Reporting API's en de Azure Resource Manager API's, wat u kunt verwachten wanneer u migreert naar de Azure Resource Manager API's, en welke nieuwe mogelijkheden beschikbaar zijn voor de nieuwe Azure Resource Manager API's.

## <a name="api-differences"></a>API-verschillen

De volgende informatie beschrijft de verschillen tussen de oudere Reporting API's voor Enterprise-klanten en de nieuwere Azure Resource Manager API's.

| **Gebruiken** | **Enterprise Agreement API's** | **Azure Resource Manager API's** |
| --- | --- | --- |
| Verificatie | API-sleutel ingericht in de EA-portal (Enterprise Agreement) | Azure AD-verificatie (Active Directory) met behulp van gebruikerstokens of service-principals. Service-principals komen in de plaats van API-sleutels. |
| Bereiken en machtigingen | Alle aanvragen bevinden zich in het bereik Inschrijving. Met de machtigingstoewijzingen van de API-sleutel wordt bepaald of gegevens worden geretourneerd voor de volledige inschrijving, voor een afdeling, of voor een specifiek account. Geen gebruikersverificatie. | Aan gebruikers of service-principals wordt toegang toegewezen tot het bereik Inschrijving, Afdeling of Account. |
| URI-eindpunt | https://consumption.azure.com | https://management.azure.com |
| Ontwikkelingsstatus | In onderhoudsmodus. Wordt binnenkort afgeschaft. | Wordt actief ontwikkeld |
| Beschikbare API's | Beperkt tot wat momenteel beschikbaar is | Er zijn equivalente API's beschikbaar om elke EA API te vervangen. <p> Er zijn ook aanvullende [Cost Management API's](/rest/api/cost-management/) voor u beschikbaar, waaronder: <p> <ul><li>Budgetten</li><li>Waarschuwingen<li>Exports</li></ul> |

## <a name="migration-checklist"></a>Migratiecontrolelijst

- Maak u vertrouwd met de [Azure Resource Manager REST API's](/rest/api/azure).
- Bepaal welke EA API's u gebruikt en kijk naar welke Azure Resource Manager API's u moet overstappen op [EA API-toewijzing aan nieuwe Azure Resource Manager API's](#ea-api-mapping-to-new-azure-resource-manager-apis).
- Serviceautorisatie en -verificatie configureren voor de Azure Resource Manager API's
  - Als u nog geen Azure Resource Manager-API's gebruikt, [moet u uw client-app registreren bij Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad). Bij de registratie wordt een service-principal gemaakt die u kunt gebruiken om de API's aan te roepen.
  - Wijs aan de service-principal toegang toe tot de benodigde bereiken, zoals hieronder beschreven.
  - Werk eventuele programmeercode bij voor het gebruik van [Azure Active Directory-verificatie](/rest/api/azure/#create-the-request) met uw service-principal.
- Test de API's en werk vervolgens eventuele programmeercode bij om EA API-aanroepen te vervangen door Azure Resource Manager API-aanroepen.
- Foutafhandeling bijwerken om nieuwe foutcodes te gebruiken. Enkele overwegingen:
  - Azure Resource Manager API's hebben een time-outperiode van 60 seconden.
  - Voor Azure Resource Manager API's geldt een frequentiebeperking. Dit resulteert in een 429-beperkingsfout, wanneer deze frequentie wordt overschreden. Bouw uw oplossingen zodat u niet binnen een kort tijdsbestek te veel API-aanroepen plaatst.
- Bekijk de andere Cost Management API's die beschikbaar zijn via Azure Resource Manager, en beoordeel ze voor later gebruik. Raadpleeg [Aanvullende Cost Management API's gebruiken](#use-additional-cost-management-apis) voor meer informatie.

## <a name="assign-service-principal-access-to-azure-resource-manager-apis"></a>Aan service-principal toegang tot Azure Resource Manager API's toewijzen

Nadat u een service-principal hebt gemaakt om via een programma de Azure Resource Manager API's aan te roepen, moet u aan deze service-principal de juiste machtigingen toewijzen voor autorisatie en om aanvragen uit te voeren in Azure Resource Manager. Er zijn twee frameworks voor machtigingen, voor verschillende scenario's.

### <a name="azure-billing-hierarchy-access"></a>Hiërarchietoegang tot Azure-facturering

Gebruik de API's voor [Factureringsmachtigingen](/rest/api/billing/2019-10-01-preview/billingpermissions), [Roldefinities voor facturering](/rest/api/billing/2019-10-01-preview/billingroledefinitions) en [Toewijzingen voor facturering](/rest/api/billing/2019-10-01-preview/billingroleassignments) om service-principal-machtigingen toe te wijzen aan het bereik Enterprise-factureringsaccount, Afdelingen, of Inschrijvingsaccount.

- Gebruik de API's voor Factureringsmachtigingen om de machtigingen te identificeren die een service-principal al heeft in een bepaald bereik, zoals een Factureringsaccount of Afdeling.
- Gebruik de API's voor Roldefinities voor facturering om de beschikbare rollen op te sommen die kunnen worden toegewezen aan uw service-principal.
  - De rollen Alleen-lezen EA-beheerder en Alleen-lezen Afdelingsbeheerder kunnen op dit moment worden toegewezen aan service-principals.
- Gebruik de API's voor Roltoewijzingen voor facturering om een rol toe te wijzen aan uw service-principal.

In het volgende voorbeeld ziet u hoe u de API voor roltoewijzingen aanroept om een service-principal toegang te verlenen tot uw factureringsaccount. We raden u aan om [PostMan](https://postman.com) te gebruiken om deze configuraties voor eenmalige machtiging uit te voeren.

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/createBillingRoleAssignment?api-version=2019-10-01-preview
```

#### <a name="request-body"></a>Aanvraagtekst

```json
{
  "principalId": "00000000-0000-0000-0000-000000000000",
  "billingRoleDefinitionId": "/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.Billing/billingRoleDefinition/10000000-aaaa-bbbb-cccc-100000000000"
}

```

### <a name="azure-role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer voor Azure

Nieuwe ondersteuning voor service-principals is uitgebreid naar Azure-specifieke bereiken, zoals beheergroepen, abonnementen, en resourcegroepen. U kunt rechtstreeks [in de Azure-portal](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) service-principal-machtigingen toewijzen aan deze bereiken, of [Azure PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#assign-the-application-to-a-role) gebruiken.

## <a name="ea-api-mapping-to-new-azure-resource-manager-apis"></a>EA API-toewijzing aan nieuwe Azure Resource Manager API's

Gebruik de onderstaande tabel om de EA API's te identificeren die u momenteel gebruikt, en te zien welke vervangende Azure Resource Manager API's u in plaats hiervan moet gebruiken.

| **Scenario** | **EA API's** | **Azure Resource Manager API's** |
| --- | --- | --- |
| Saldo-overzicht | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) |[Microsoft.Consumption/balances](/rest/api/consumption/balances/getbybillingaccount) |
| Prijzenoverzicht | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | [Microsoft.Consumption/pricesheets/default](/rest/api/consumption/pricesheet): gebruiken voor overeengekomen prijzen <p> [API voor Verkoopprijzen](/rest/api/cost-management/retail-prices/azure-retail-prices): gebruiken voor verkoopprijzen |
| Details van gereserveerde instantie | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.CostManagement/generateReservationDetailsReport](/rest/api/cost-management/generatereservationdetailsreport) |
| Overzicht van gereserveerde instanties | [/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid) |
| Aanbevelingen voor gereserveerde instanties | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)<p>[/SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Kosten voor gereserveerde instanties | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | [Microsoft.Consumption/reservationTransactions](/rest/api/consumption/reservationtransactions/list) |

## <a name="migration-details-by-api"></a>Migratiegegevens per API

In de volgende secties ziet u voorbeelden van oude API-aanvragen naast voorbeelden van de nieuwe vervangende API's.

### <a name="balance-summary-api"></a>API voor Saldo-overzicht

Gebruik de volgende aanvraag-URI's wanneer u de nieuwe API voor Saldo-overzicht aanroept. Uw inschrijvingsnummer moet worden gebruikt als billingAccountId.

#### <a name="supported-requests"></a>Ondersteunde aanvragen

[Ontvangen voor Inschrijving](/rest/api/consumption/balances/getbybillingaccount)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/balances?api-version=2019-10-01
```

### <a name="response-body-changes"></a>Wijzigingen in antwoordtekst

_Oude antwoordtekst_:

```json
{
        "id": "enrollments/100/billingperiods/201507/balancesummaries",
          "billingPeriodId": 201507,
          "currencyCode": "USD",
          "beginningBalance": 0,
          "endingBalance": 1.1,
          "newPurchases": 1,
          "adjustments": 1.1,
          "utilized": 1.1,
          "serviceOverage": 1,
          "chargesBilledSeparately": 1,
          "totalOverage": 1,
          "totalUsage": 1.1,
          "azureMarketplaceServiceCharges": 1,
          "newPurchasesDetails": [
            {
              "name": "",
              "value": 1
            }
          ],
          "adjustmentDetails": [
            {
              "name": "Promo Credit",
              "value": 1.1
            },
            {
              "name": "SIE Credit",
              "value": 1.0
            }
          ]
    }

```

_Nieuwe antwoordtekst_:

Dezelfde gegevens zijn nu beschikbaar in het veld `properties` van het nieuwe API-antwoord. Er kunnen kleine wijzigingen zijn in de spelling van sommige veldnamen.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/balances/balanceId1",
  "name": "balanceId1",
  "type": "Microsoft.Consumption/balances",
  "properties": {
    "currency": "USD  ",
    "beginningBalance": 3396469.19,
    "endingBalance": 2922371.02,
    "newPurchases": 0,
    "adjustments": 0,
    "utilized": 474098.17,
    "serviceOverage": 0,
    "chargesBilledSeparately": 0,
    "totalOverage": 0,
    "totalUsage": 474098.17,
    "azureMarketplaceServiceCharges": 609.82,
    "billingFrequency": "Month",
    "priceHidden": false,
    "newPurchasesDetails": [
      {
        "name": "Promo Purchase",
        "value": 1
      }
    ],
    "adjustmentDetails": [
      {
        "name": "Promo Credit",
        "value": 1.1
      },
      {
        "name": "SIE Credit",
        "value": 1
      }
    ]
  }
}

```

### <a name="price-sheet"></a>Prijzenoverzicht

Gebruik de volgende aanvraag-URI's wanneer u de nieuwe API voor Prijzenoverzicht aanroept.

#### <a name="supported-requests"></a>Ondersteunde aanvragen

 U kunt de API aanroepen met behulp van de volgende bereiken:

- Inschrijving: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- Abonnement: `subscriptions/{subscriptionId}`

[_Ontvangen voor de huidige factureringsperiode_](/rest/api/consumption/pricesheet/get)


```json
https://management.azure.com/{scope}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

[_Ontvangen voor de huidige factureringsperiode_](/rest/api/consumption/pricesheet/getbybillingperiod)

```json
https://management.azure.com/{scope}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Wijzigingen in antwoordtekst

_Oud antwoord_:

```json
      [
        {
              "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
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
              "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
              "billingPeriodId": "201404",
            "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
              "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
              "unitOfMeasure": "100 GB",
              "includedQuantity": 0,
              "partNumber": "N9H-00402",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        ...
    ]

```

_Nieuw antwoord_:

Oude gegevens staan nu in het veld `pricesheets` van het nieuwe API-antwoord. Er worden ook meterdetails geboden.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/pricesheets/default",
  "name": "default",
  "type": "Microsoft.Consumption/pricesheets",
  "properties": {
    "nextLink": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/microsoft.consumption/pricesheets/default?api-version=2018-01-31&$skiptoken=AQAAAA%3D%3D&$expand=properties/pricesheets/meterDetails",
    "pricesheets": [
      {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "meterId": "00000000-0000-0000-0000-000000000000",
        "unitOfMeasure": "100 Hours",
        "includedQuantity": 100,
        "partNumber": "XX-11110",
        "unitPrice": 0.00000,
        "currencyCode": "EUR",
        "offerId": "OfferId 1",
        "meterDetails": {
          "meterName": "Data Transfer Out (GB)",
          "meterCategory": "Networking",
          "unit": "GB",
          "meterLocation": "Zone 2",
          "totalIncludedQuantity": 0,
          "pretaxStandardRate": 0.000
        }
      }
    ]
  }
}

```

### <a name="reserved-instance-usage-details"></a>Gebruiksgegevens voor gereserveerde instanties

Microsoft werkt niet actief aan op synchronisatie gebaseerde API's voor Reserveringsdetails. We raden u aan om, als onderdeel van de migratie, over te stappen op het nieuwere met SPN ondersteunde asynchrone API-aanroeppatroon. Met asynchrone aanvragen worden grote hoeveelheden gegevens beter verwerkt en treden er minder time-outfouten op.

#### <a name="supported-requests"></a>Ondersteunde aanvragen

Gebruik de volgende aanvraag-URI's wanneer u de nieuwe API voor Asynchrone reserveringsdetails aanroept. Uw inschrijvingsnummer moet worden gebruikt als `billingAccountId`. U kunt de API aanroepen met behulp van de volgende bereiken:

- Inschrijving: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

#### <a name="sample-request-to-generate-a-reservation-details-report"></a>Voorbeeldaanvraag voor het genereren van een rapport met reserveringsdetails

```json
POST 
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/generateReservationDetailsReport?startDate={startDate}&endDate={endDate}&api-version=2019-11-01

```

#### <a name="sample-request-to-poll-report-generation-status"></a>Voorbeeldaanvraag voor het opvragen van de generatiestatus van het rapport

```json
GET
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/reservationDetailsOperationResults/{operationId}?api-version=2019-11-01

```

#### <a name="sample-poll-response"></a>Voorbeeldantwoord voor poll

```json
{
  "status": "Completed",
  "properties": {
    "reportUrl": "https://storage.blob.core.windows.net/details/20200911/00000000-0000-0000-0000-000000000000?sv=2016-05-31&sr=b&sig=jep8HT2aphfUkyERRZa5LRfd9RPzjXbzB%2F9TNiQ",
    "validUntil": "2020-09-12T02:56:55.5021869Z"
  }
}

```

#### <a name="response-body-changes"></a>Wijzigingen in antwoordtekst

Hieronder ziet u het antwoord van de oudere op synchronisatie gebaseerde API voor Reserveringsdetails.

_Oud antwoord_:

```json
{
    "reservationOrderId": "00000000-0000-0000-0000-000000000000",
    "reservationId": "00000000-0000-0000-0000-000000000000",
    "usageDate": "2018-02-01T00:00:00",
    "skuName": "Standard_F2s",
    "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/resourvegroup1/providers/microsoft.compute/virtualmachines/VM1",
    "totalReservedQuantity": 18.000000000000000,
    "reservedHours": 432.000000000000000,
    "usedHours": 400.000000000000000
}

```

_Nieuw antwoord_:

Met de nieuwe API wordt een CSV-bestand gemaakt. Bekijk de volgende velden in het bestand.

| **Oude eigenschap** | **Nieuwe eigenschap** | **Opmerkingen** |
| --- | --- | --- |
|  | InstanceFlexibilityGroup | Nieuwe eigenschap voor flexibiliteit van instantie. |
|  | InstanceFlexibilityRatio | Nieuwe eigenschap voor flexibiliteit van instantie. |
| instanceId | InstanceName |  |
|  | Soort | Het is een nieuwe eigenschap. Waarde is `None`, `Reservation` of `IncludedQuantity`. |
| reservationId | ReservationId |  |
| reservationOrderId | ReservationOrderId |  |
| reservedHours | ReservedHours |  |
| skuName | SkuName |  |
| totalReservedQuantity | TotalReservedQuantity |  |
| usageDate | UsageDate |  |
| usedHours | UsedHours |  |

### <a name="reserved-instance-usage-summary"></a>Samenvatting van het gebruik van gereserveerde instanties

Gebruik de volgende aanvraag-URI's voor het aanroepen van de nieuwe API voor Reserveringsoverzichten.

#### <a name="supported-requests"></a>Ondersteunde aanvragen

 Roep de API aan met behulp van de volgende bereiken:

- Inschrijving: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

[_Dagelijks een reserveringsoverzicht ontvangen_](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

[_Maandelijks een reserveringsoverzicht ontvangen_](/rest/api/consumption/reservationssummaries/list#reservationsummariesmonthlywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Wijzigingen in antwoordtekst

_Oud antwoord_:

```json
[
     {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_F1s",
        "reservedHours": 24,
        "usageDate": "2018-05-01T00:00:00",
        "usedHours": 23,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 95.83,
        "maxUtilizationPercentage": 100
    }
]

```

_Nieuw antwoord_:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/12345/providers/Microsoft.Consumption/reservationSummaries/reservationSummaries_Id1",
      "name": "reservationSummaries_Id1",
      "type": "Microsoft.Consumption/reservationSummaries",
      "tags": null,
      "properties": {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_B1s",
        "reservedHours": 720,
        "usageDate": "2018-09-01T00:00:00-07:00",
        "usedHours": 0,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 0,
        "maxUtilizationPercentage": 0
      }
    }
  ]
}

```

### <a name="reserved-instance-recommendations"></a>Aanbevelingen voor gereserveerde instanties

Gebruik de volgende aanvraag-URI's voor het aanroepen van de nieuwe API voor Reserveringsaanbevelingen.

#### <a name="supported-requests"></a>Ondersteunde aanvragen

 Roep de API aan met behulp van de volgende bereiken:

- Inschrijving: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- Abonnement: `subscriptions/{subscriptionId}`
- Resourcegroepen: `subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`

[_Aanbevelingen ontvangen_](/rest/api/consumption/reservationrecommendations/list)

Via deze API zijn aanbevelingen beschikbaar voor zowel gedeelde als enkelvoudige bereiken. U kunt ook filteren op het bereik als een optionele API-parameter.

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Wijzigingen in antwoordtekst

Aanbevelingen voor gedeelde en enkelvoudige bereiken zijn gecombineerd in één API.

_Oud antwoord_:

```json
[{
    "subscriptionId": "1111111-1111-1111-1111-111111111111",
    "lookBackPeriod": "Last7Days",
    "meterId": "2e3c2132-1398-43d2-ad45-1d77f6574933",
    "skuName": "Standard_DS1_v2",
    "term": "P1Y",
    "region": "westus",
    "costWithNoRI": 186.27634908960002,
    "recommendedQuantity": 9,
    "totalCostWithRI": 143.12931642978083,
    "netSavings": 43.147032659819189,
    "firstUsageDate": "2018-02-19T00:00:00"
}
]

```

_Nieuw antwoord_:

```json
{
  "value": [
    {
      "id": "billingAccount/123456/providers/Microsoft.Consumption/reservationRecommendations/00000000-0000-0000-0000-000000000000",
      "name": "00000000-0000-0000-0000-000000000000",
      "type": "Microsoft.Consumption/reservationRecommendations",
      "location": "westus",
      "sku": "Standard_DS1_v2",
      "kind": "legacy",
      "properties": {
        "meterId": "00000000-0000-0000-0000-000000000000",
        "term": "P1Y",
        "costWithNoReservedInstances": 12.0785105,
        "recommendedQuantity": 1,
        "totalCostWithReservedInstances": 11.4899644807748,
        "netSavings": 0.588546019225182,
        "firstUsageDate": "2019-07-07T00:00:00-07:00",
        "scope": "Shared",
        "lookBackPeriod": "Last7Days",
        "instanceFlexibilityRatio": 1,
        "instanceFlexibilityGroup": "DSv2 Series",
        "normalizedSize": "Standard_DS1_v2",
        "recommendedQuantityNormalized": 1,
        "skuProperties": [
          {
            "name": "Cores",
            "value": "1"
          },
          {
            "name": "Ram",
            "value": "1"
          }
        ]
      }
    },
   ]
}

```

### <a name="reserved-instance-charges"></a>Kosten voor gereserveerde instanties

Gebruik de volgende aanvraag-URI's voor het aanroepen van de nieuwe API voor Kosten voor gereserveerde instanties.

#### <a name="supported-requests"></a>Ondersteunde aanvragen

[_Reserveringskosten ophalen op datumbereik_](/rest/api/consumption/reservationtransactions/list)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/reservationTransactions?$filter=properties/eventDate+ge+2020-05-20+AND+properties/eventDate+le+2020-05-30&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Wijzigingen in antwoordtekst

_Oud antwoord_:

```json
[
    {
        "purchasingEnrollment": "string",
        "armSkuName": "Standard_F1s",
        "term": "P1Y",
        "region": "eastus",
        "PurchasingsubscriptionGuid": "00000000-0000-0000-0000-000000000000",
        "PurchasingsubscriptionName": "string",
        "accountName": "string",
        "accountOwnerEmail": "string",
        "departmentName": "string",
        "costCenter": "",
        "currentEnrollment": "string",
        "eventDate": "string",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_F1s eastus 1 Year",
        "eventType": "Purchase",
        "quantity": int,
        "amount": double,
        "currency": "string",
        "reservationOrderName": "string"
    }
]

```
_Nieuw antwoord_:

```json
{
  "value": [
    {
      "id": "/billingAccounts/123456/providers/Microsoft.Consumption/reservationtransactions/201909091919",
      "name": "201909091919",
      "type": "Microsoft.Consumption/reservationTransactions",
      "tags": {},
      "properties": {
        "eventDate": "2019-09-09T19:19:04Z",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_DS1_v2 westus 1 Year",
        "eventType": "Cancel",
        "quantity": 1,
        "amount": -21,
        "currency": "USD",
        "reservationOrderName": "Transaction-DS1_v2",
        "purchasingEnrollment": "123456",
        "armSkuName": "Standard_DS1_v2",
        "term": "P1Y",
        "region": "westus",
        "purchasingSubscriptionGuid": "11111111-1111-1111-1111-11111111111",
        "purchasingSubscriptionName": "Infrastructure Subscription",
        "accountName": "Microsoft Infrastructure",
        "accountOwnerEmail": "admin@microsoft.com",
        "departmentName": "Unassigned",
        "costCenter": "",
        "currentEnrollment": "123456",
        "billingFrequency": "recurring"
      }
    },
  ]
}

```

## <a name="use-additional-cost-management-apis"></a>Aanvullende Cost Management API's gebruiken

Nadat u bent gemigreerd naar Azure Resource Manager API's voor uw bestaande rapportagescenario's, zijn er ook nog veel andere API's die u kunt gebruiken. De API's zijn ook beschikbaar via Azure Resource Manager, en kunnen worden geautomatiseerd met behulp van verificatie op basis van een service-principal. Hier volgt een kort overzicht van de nieuwe mogelijkheden die u kunt gebruiken.

- [Budgetten](/rest/api/consumption/budgets/createorupdate): om drempelwaarden in te stellen om uw kosten proactief te bewaken, relevante belanghebbenden te waarschuwen, en acties te automatiseren als reactie op schendingen van de drempelwaarden.

- [Waarschuwingen](/rest/api/cost-management/alerts): om waarschuwingsgegevens te bekijken, inclusief, maar niet beperkt tot, waarschuwingen voor budgetten, facturen, tegoeden en quota.

- [Exports](/rest/api/cost-management/exports): om terugkerende gegevensexports van uw kosten te plannen naar een Azure Storage-account van uw keuze. Dit is de aanbevolen oplossing voor klanten met een grote Azure-aanwezigheid die hun gegevens wil analyseren en gebruiken in hun eigen interne systemen.

## <a name="next-steps"></a>Volgende stappen

- Maak u vertrouwd met de [Azure Resource Manager REST API's](/rest/api/azure).
- Bepaal, indien nodig, welke EA API's u gebruikt en kijk naar welke Azure Resource Manager API's u moet overstappen op [EA API-toewijzing aan nieuwe Azure Resource Manager API's](#ea-api-mapping-to-new-azure-resource-manager-apis).
- Als u nog geen Azure Resource Manager-API's gebruikt, [moet u uw client-app registreren bij Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Werk, indien nodig, uw programmeercode bij voor het gebruik van [Azure Active Directory-verificatie](/rest/api/azure/#create-the-request) met uw service-principal.