---
title: Maak uw eerste API-oproep voor het verkrijgen van toegang tot commerciële Marketplace Analytics-gegevens
description: Voor beelden voor meer informatie over het gebruik van de API voor toegang tot de analyse gegevens van commerciële Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 2d0c0e7322ecb92fd371f5bf7924a370dd29fe85
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583815"
---
# <a name="make-your-first-api-call-to-access-commercial-marketplace-analytics-data"></a>Maak uw eerste API-oproep voor het verkrijgen van toegang tot commerciële Marketplace Analytics-gegevens

Zie [api's voor toegang tot commerciële Marketplace Analytics-gegevens](analytics-available-apis.md)voor een lijst met api's voor het verkrijgen van toegang tot commerciële Marketplace Analytics-gegevens. Voordat u uw eerste API-oproep maakt, moet u ervoor zorgen dat u voldoet aan de [vereisten](analytics-prerequisites.md) om via een programma toegang te krijgen tot Analytics-gegevens van commerciële Marketplace.

## <a name="token-generation"></a>Genereren van tokens

Voordat u een van de methoden aanroept, moet u eerst een toegangs token van Azure Active Directory (Azure AD) verkrijgen. U moet het Azure AD-toegangs token door geven aan de autorisatie-header van elke methode in de API. Nadat u een toegangs token hebt verkregen, hebt u 60 minuten om het te gebruiken voordat het verloopt. Nadat het token is verlopen, kunt u het token vernieuwen en blijven gebruiken voor verdere aanroepen naar de API.

Raadpleeg hieronder een voor beeld van een aanvraag voor het genereren van een token. De drie waarden die nodig zijn voor het genereren van het token zijn `clientId` , `clientSecret` , en `tenantId` . De `resource` para meter moet worden ingesteld op `https://graph.windows.net` .

***Voor beeld van aanvraag***:

```json
curl --location --request POST 'https://login.microsoftonline.com/{TenantId}/oauth2/token' \
--header 'return-client-request-id: true' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'resource=https://graph.windows.net' \
--data-urlencode 'client_id={client_id}' \
--data-urlencode 'client_secret={client_secret}' \
--data-urlencode 'grant_type=client_credentials'
```

***Antwoord voorbeeld***:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1612794445",
    "not_before": "1612790545",
    "resource": "https://graph.windows.net",
    "access_token": {Token}
}
```

Voor meer informatie over het verkrijgen van een Azure AD-token voor uw toepassing raadpleegt [u toegang tot Analytics-gegevens met behulp van Store-Services](https://docs.microsoft.com/windows/uwp/monetize/access-analytics-data-using-windows-store-services#step-2-obtain-an-azure-ad-access-token).

## <a name="programmatic-api-call"></a>Programmatische API-aanroep

Nadat u het Azure AD-token hebt verkregen, zoals beschreven in de vorige sectie, volgt u deze stappen om uw eerste programmatische Access-rapport te maken.

Gegevens kunnen worden gedownload uit de volgende gegevens sets (datasetnaam):

- ISVCustomer
- ISVMarketplaceInsights
- ISVUsage
- ISVOrder

In de volgende secties ziet u voor beelden van hoe u via een programma toegang hebt tot `OrderId` de ISVOrder-gegevensset.

### <a name="step-1-make-a-rest-call-using-the-get-datasets-api"></a>Stap 1: Maak een REST-aanroep met de API data sets ophalen

De API-respons geeft de naam van de gegevensset van waaruit u het rapport kunt downloaden. Voor de specifieke gegevensset biedt de API-reactie ook de lijst met geselecteerde kolommen die kunnen worden gebruikt voor uw aangepaste rapport sjabloon. U kunt ook de volgende tabellen raadplegen om de namen van de kolommen op te halen:

- [Tabel met Order Details](orders-dashboard.md#orders-details-table)
- [Tabel gebruiks gegevens](usage-dashboard.md#usage-details-table)
- [Tabel Klant gegevens](customer-dashboard.md#customer-details-table)
- [Informatie tabel voor Marketplace Insights](insights-dashboard.md#marketplace-insights-details-table)

***Voor beeld van aanvraag***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset ' \ 
--header 'Authorization: Bearer <AzureADToken>'
```

***Antwoord voorbeeld***:

```json
{
    "value": [
        {
            "datasetName": "ISVOrder",
            "selectableColumns": [
                "MarketplaceSubscriptionId",
                "MonthStartDate",
                "OfferType",
                "AzureLicenseType",
                "Sku",
                "CustomerCountry",
                "IsPreviewSKU",
                "OrderId",
                "OrderQuantity",
                "CloudInstanceName",
                "IsNewCustomer",
                "OrderStatus",
                "OrderCancelDate",
                "CustomerCompanyName",
                "CustomerName",
                "OrderPurchaseDate",
                "OfferName",
                "TrialEndDate",
                "CustomerId",
                "BillingAccountId"
            ],
            "availableMetrics": [],
            "availableDateRanges": [
                "LAST_MONTH",
                "LAST_3_MONTHS",
                "LAST_6_MONTHS",
                "LIFETIME"
            ]
        },
    ],
    "totalCount": 1,
    "message": "Dataset fetched successfully",
    "statusCode": 200
}
```

### <a name="step-2-create-the-custom-query"></a>Stap 2: de aangepaste query maken

In deze stap gebruiken we de order-ID uit het rapport orders om een aangepaste query voor het gewenste rapport te maken. De standaard waarde `timespan` als deze niet is opgegeven in de query, is zes maanden.

***Voor beeld van aanvraag***:

```json
curl 
--location 
--request POST ' https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries' \ 
--header ' Authorization: Bearer <AzureAD_Token>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
            '{ 
                "Query": "SELECT OrderId from ISVOrder", 
                "Name": "ISVOrderQuery1", 
                "Description": "Get a list of all Order IDs" 
             }'
```

***Antwoord voorbeeld***:

```json
{
    "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": "ISVOrderQuery1",
            "description": "Get a list of all Order IDs”,
            "query": "SELECT OrderId from ISVOrder",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34",
            "modifiedTime": null
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

Wanneer de query is uitgevoerd, wordt er een `queryId` gegenereerd dat moet worden gebruikt om het rapport te genereren.

### <a name="step-3-execute-test-query-api"></a>Stap 3: test query-API uitvoeren

In deze stap gebruiken we de query-API testen om de bovenste 100 rijen te verkrijgen voor de query die is gemaakt.

***Voor beeld van aanvraag***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery=SELECT%20OrderId%20from%20ISVOrder' \ 
--header ' Authorization: Bearer <AzureADToken>'
```

***Antwoord voorbeeld***:

```json
{
    "value": [
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2ba8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bb8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bc8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bd8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2be8"
        },
               .
               .
               .

        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf0"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf1"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf2"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf3"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf4"
        }
    ],
    "totalCount": 100,
    "message": null,
    "statusCode": 200
}
```

### <a name="step-4-create-the-report"></a>Stap 4: het rapport maken

In deze stap gebruiken we de eerder gegenereerde bewerking `QueryId` om het rapport te maken.

***Voor beeld van aanvraag***:

```json
curl 
--location 
--request POST 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport' \ 
--header ' Authorization: Bearer <AzureADToken>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
                 '{
                   “ReportName": "ISVReport1",
                   "Description": "Report for getting list of Order Ids",
                   "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
                   "StartTime": "2021-01-06T19:00:00Z”,
                   "RecurrenceInterval": 48,
                   "RecurrenceCount": 20,
                    "Format": "csv"
                  }'
```

<br>

_**Tabel 1: beschrijving van de para meters die worden gebruikt in dit voor beeld van een aanvraag**_

| Parameter | Beschrijving |
| ------------ | ------------- |
| `Description` | Geef een korte beschrijving van het rapport dat wordt gegenereerd. |
| `QueryId` | Dit is het `queryId` gegenereerd toen de query werd gemaakt in stap 2: een aangepaste query maken. |
| `StartTime` | Tijd waarop de eerste uitvoering van het rapport is gestart. |
| `RecurrenceInterval` | Het terugkeer interval dat is gegeven tijdens het maken van het rapport. |
| `RecurrenceCount` | Aantal herhalingen dat is gegeven tijdens het maken van het rapport. |
| `Format` | De bestands indelingen CSV en TSV worden ondersteund. |
|||

***Antwoord voorbeeld***:

```json
{
    "value": [
        {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVReport1",
            "description": "Report for getting list of Order Ids",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT OrderId from ISVOrder",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv"
        }
    ],
    "totalCount": 1,
    "message": "Report created successfully",
    "statusCode": 200
}
```

Als de uitvoering is geslaagd, wordt er een `reportId` gegenereerd dat moet worden gebruikt om een down load van het rapport te plannen.

### <a name="step-5-execute-report-executions-api"></a>Stap 5: de API voor rapport uitvoeringen uitvoeren

Om de beveiligde locatie (URL) van het rapport op te halen, wordt nu de API voor rapport uitvoeringen uitgevoerd.

***Voor beeld van aanvraag***:

```json
Curl
--location
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/72fa95ab-35f5-4d44-a1ee-503abbc88003' \
--header ' Authorization: Bearer <AzureADToken>' \
```

***Antwoord voorbeeld***:

```json
{
    "value": [
        {
            "executionId": "1f18b53b-df30-4d98-85ee-e6c7e687aeed",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Pending",
            "reportAccessSecureLink": null,
            "reportExpiryTime": null,
            "reportGeneratedTime": null
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

## <a name="next-steps"></a>Volgende stappen

- U kunt de Api's uitproberen via de [URL van SWAGGER API](https://partneranalytics-api.azure-api.net/analytics/cmp/swagger/index.html)
- [Paradigma voor toegang tot software](analytics-programmatic-access.md)
