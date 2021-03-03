---
title: Azure Monitoring REST API-overzicht
description: Het verifiëren van aanvragen en het gebruik van de Azure Monitor REST API om beschik bare metrische definities en metrische waarden op te halen.
ms.subservice: metrics
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: has-adal-ref
ms.openlocfilehash: b22387272ebeae7a7db8f0e0940841415e345279
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734210"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Azure Monitoring REST API-overzicht

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

In dit artikel leest u hoe u verificatie kunt uitvoeren, zodat uw code de [Microsoft Azure Monitor rest API verwijzing](/rest/api/monitor/)kan gebruiken.

Met de Azure Monitor-API kunnen de beschik bare standaard waarden voor metrische gegevens, granulariteit en metrische gegevens programmatisch worden opgehaald. De gegevens kunnen worden opgeslagen in een afzonderlijk gegevens archief, zoals Azure SQL Database, Azure Cosmos DB of Azure Data Lake. Van deze aanvullende analyse kan naar behoefte worden uitgevoerd.

Naast het werken met diverse metrische gegevens punten is het ook mogelijk om waarschuwings regels weer te geven, activiteiten logboeken te bekijken en nog veel meer. Voor een volledige lijst met beschik bare bewerkingen raadpleegt u [rest API Reference Microsoft Azure monitor](/rest/api/monitor/).

## <a name="authenticating-azure-monitor-requests"></a>Azure Monitor aanvragen verifiëren

De eerste stap is het verifiëren van de aanvraag.

Alle taken die worden uitgevoerd op de Azure Monitor-API, gebruiken het Azure Resource Manager-verificatie model. Daarom moeten alle aanvragen worden geverifieerd met Azure Active Directory (Azure AD). Een van de manieren om de client toepassing te verifiëren, is een Azure AD-Service-Principal te maken en het authenticatie (JWT)-token op te halen. In het volgende voorbeeld script ziet u hoe u een Azure AD-Service-Principal maakt via Power shell. Raadpleeg de documentatie over het [gebruik van Azure PowerShell om een service-principal te maken voor toegang tot resources](/powershell/azure/create-azure-service-principal-azureps)voor meer gedetailleerde instructies. Het is ook mogelijk om [een service-principal te maken via de Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md).

```powershell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Connect-AzAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"
$secureStringPassword = ConvertTo-SecureString -String $pwd -AsPlainText -Force

# Create a new Azure AD application
$azureAdApplication = New-AzADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $secureStringPassword

# Create a new service principal associated with the designated application
New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Als u een query wilt uitvoeren voor de Azure Monitor-API, moet de client toepassing de eerder gemaakte service-principal gebruiken om te verifiëren. In het volgende voor beeld Power shell-script wordt één benadering weer gegeven, waarbij de [Active Directory Authentication Library](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) wordt gebruikt om het JWT-verificatie token op te halen. De JWT-token wordt door gegeven als onderdeel van een HTTP-autorisatie parameter in aanvragen voor de Azure Monitor REST API.

```powershell
$azureAdApplication = Get-AzADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireTokenAsync("https://management.core.windows.net/", $cred).GetAwaiter().GetResult()

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Na de verificatie kunnen query's worden uitgevoerd op basis van de Azure Monitor REST API. Er zijn twee nuttige query's:

1. De metrische definities voor een resource weer geven
2. De metrische waarden ophalen

> [!NOTE]
> Raadpleeg voor meer informatie over de verificatie met Azure REST API de [referentie voor azure rest API](/rest/api/azure/).
>
>

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Metrische definities ophalen (multi-dimensionale API)

Gebruik de [Azure monitor metrische definities rest API](/rest/api/monitor/metricdefinitions) om toegang te krijgen tot de lijst met metrische gegevens die beschikbaar zijn voor een service.

**Methode**: ophalen

**Aanvraag-URI**: https: \/ \/ Management.Azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}* / *{resource type}* / *{ResourceName*}/providers/Microsoft.Insights/metricDefinitions? API-Version =*{apiVersion}*

Als u bijvoorbeeld de metrische definities voor een Azure Storage account wilt ophalen, ziet de aanvraag er als volgt uit:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```

> [!NOTE]
> Als u metrische definities wilt ophalen met behulp van de multidimensionale Azure Monitor meet waarden REST API, gebruikt u "2018-01-01" als de API-versie.
>
>

De resulterende hoofd tekst van het JSON-antwoord zou er als volgt uitzien: (Houd er rekening mee dat de tweede metriek dimensies heeft)

```json
{
    "value": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/UsedCapacity",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Capacity",
            "name": {
                "value": "UsedCapacity",
                "localizedValue": "Used capacity"
            },
            "isDimensionRequired": false,
            "unit": "Bytes",
            "primaryAggregationType": "Average",
            "supportedAggregationTypes": [
                "Total",
                "Average",
                "Minimum",
                "Maximum"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ]
        },
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/Transactions",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Transaction",
            "name": {
                "value": "Transactions",
                "localizedValue": "Transactions"
            },
            "isDimensionRequired": false,
            "unit": "Count",
            "primaryAggregationType": "Total",
            "supportedAggregationTypes": [
                "Total"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT5M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT15M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT30M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ],
            "dimensions": [
                {
                    "value": "ResponseType",
                    "localizedValue": "Response type"
                },
                {
                    "value": "GeoType",
                    "localizedValue": "Geo type"
                },
                {
                    "value": "ApiName",
                    "localizedValue": "API name"
                }
            ]
        },
        ...
    ]
}
```

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Dimensie waarden ophalen (multi-dimensionale API)

Zodra de beschik bare metrische definities bekend zijn, zijn er mogelijk enkele metrische gegevens die dimensies hebben. Voordat u een query uitvoert voor de metriek, wilt u wellicht weten wat het bereik van waarden van een dimensie is. Op basis van deze dimensie waarden kunt u ervoor kiezen om de metrische gegevens te filteren of te segmenteren op basis van dimensie waarden tijdens het uitvoeren van query's op metrische gegevens.  Gebruik de [Azure monitor meet waarden rest API](/rest/api/monitor/metrics) om dit te verzorgen.

Gebruik de naam waarde van de metriek (niet het ' localizedValue ') voor filter aanvragen. Als er geen filters zijn opgegeven, wordt de standaard waarde geretourneerd. Door het gebruik van deze API kan slechts één dimensie een Joker teken filter hebben.

> [!NOTE]
> Als u dimensie waarden wilt ophalen met behulp van de Azure Monitor REST API, gebruikt u ' 2018-01-01 ' als de API-versie.
>
>

**Methode**: ophalen

**Aanvraag-URI**: https \: //*Management.Azure.com/subscriptions/{abonnements-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-Provider-namespace}* / *{resource-type}* / *{resource-name}*/providers/Microsoft.Insights/Metrics? metricnames =*{metric}*&time span =*{StartTime/EndTime}*&$filter =*{filter}*&resultType = meta gegevens&API-Version =*{apiVersion}*

Als u bijvoorbeeld de lijst met dimensie waarden wilt ophalen die zijn verzonden voor de ' API-naam dimensie ' voor de metriek ' trans acties ', waarbij de geotype dimensie = ' primary ' tijdens het opgegeven tijds bereik, zou de aanvraag er als volgt uitzien:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```

De resulterende hoofd tekst van het JSON-antwoord zou er ongeveer uitzien als in het volgende voor beeld:

```json
{
  "timespan": "2018-03-01T00:00:00Z/2018-03-02T00:00:00Z",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "DeleteBlob"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "SetBlobProperties"
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Metrische waarden ophalen (multi-dimensionale API)

Zodra de beschik bare metrische definities en mogelijke dimensie waarden bekend zijn, is het mogelijk om de gerelateerde metrieke waarden op te halen.  Gebruik de [Azure monitor meet waarden rest API](/rest/api/monitor/metrics) om dit te verzorgen.

Gebruik de naam waarde van de metriek (niet het ' localizedValue ') voor filter aanvragen. Als er geen dimensie filters zijn opgegeven, wordt de samengevouwen geaggregeerde metrische waarde geretourneerd. Als een metrische query meerdere tijds Erie retourneert, kunt u de query parameters ' top ' en ' OrderBy ' gebruiken om een beperkte geordende lijst met tijds Erie te retour neren.

> [!NOTE]
> Als u multi-dimensionale meet waarden wilt ophalen met behulp van de Azure Monitor REST API, gebruikt u ' 2018-01-01 ' als de API-versie.
>
>

**Methode**: ophalen

**Aanvraag-URI**: https \/ :/Management.Azure.com/subscriptions/*{abonnements-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-Provider-namespace}* / *{resource-type}* / *{resource-name}*/providers/Microsoft.Insights/Metrics? metricnames =*{metric}*&time span =*{StartTime/EndTime}*&$filter =*{filter}*&interval =*{timeGrain}*&aggregatie =*{aggreation}*&API-Version =*{apiVersion}*

Als u bijvoorbeeld de top-3 Api's wilt ophalen, in aflopende waarde, door het aantal ' trans acties ' tijdens een periode van 5 minuten, waarbij de GeotType ' primair ' was, zou de aanvraag er als volgt uitzien:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```

De resulterende hoofd tekst van het JSON-antwoord zou er ongeveer uitzien als in het volgende voor beeld:

```json
{
  "cost": 0,
  "timespan": "2018-03-01T02:00:00Z/2018-03-01T02:05:00Z",
  "interval": "PT1M",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "GetBlobProperties"
            }
          ],
          "data": [
            {
              "timeStamp": "2017-09-19T02:00:00Z",
              "total": 2
            },
            {
              "timeStamp": "2017-09-19T02:01:00Z",
              "total": 1
            },
            {
              "timeStamp": "2017-09-19T02:02:00Z",
              "total": 3
            },
            {
              "timeStamp": "2017-09-19T02:03:00Z",
              "total": 7
            },
            {
              "timeStamp": "2017-09-19T02:04:00Z",
              "total": 2
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-definitions"></a>Metrische definities ophalen

Gebruik de [Azure monitor metrische definities rest API](/rest/api/monitor/metricdefinitions) om toegang te krijgen tot de lijst met metrische gegevens die beschikbaar zijn voor een service.

**Methode**: ophalen

**Aanvraag-URI**: https: \/ \/ Management.Azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}* / *{resource type}* / *{ResourceName*}/providers/Microsoft.Insights/metricDefinitions? API-Version =*{apiVersion}*

Als u bijvoorbeeld de metrische definities voor een Azure Logic-app wilt ophalen, ziet de aanvraag er als volgt uit:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosotweets-metricdef-results.json" `
                  -Verbose
```

> [!NOTE]
> Als u metrische definities wilt ophalen met behulp van de Azure Monitor REST API, gebruikt u "2016-03-01" als de API-versie.
>
>

De resulterende hoofd tekst van het JSON-antwoord zou er ongeveer uitzien als in het volgende voor beeld:

```json
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions",
  "value": [
    {
      "name": {
        "value": "RunsStarted",
        "localizedValue": "Runs Started"
      },
      "category": "AllMetrics",
      "startTime": "0001-01-01T00:00:00Z",
      "endTime": "0001-01-01T00:00:00Z",
      "unit": "Count",
      "primaryAggregationType": "Total",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        }
      ],
      "properties": null,
      "dimensions": null,
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions/RunsStarted",
      "supportedAggregationTypes": [ "None", "Average", "Minimum", "Maximum", "Total", "Count" ]
    }
  ]
}
```

Zie de [metrische definities voor een resource in Azure Monitor rest API](/rest/api/monitor/metricdefinitions) documentatie voor meer informatie.

## <a name="retrieve-metric-values"></a>Metrieke waarden ophalen

Zodra de beschik bare metrische definities bekend zijn, is het mogelijk om de gerelateerde metrieke waarden op te halen. Gebruik de naam waarde ' value ' (niet het ' localizedValue ') voor filter aanvragen (bijvoorbeeld de metrische gegevens punten ' CpuTime ' en ' requests '). Als er geen filters zijn opgegeven, wordt de standaard waarde geretourneerd.

> [!NOTE]
> Als u metrische waarden wilt ophalen met behulp van de Azure Monitor REST API, gebruikt u "2016-09-01" als de API-versie.
>
>

**Methode**: `GET`

**Aanvraag-URI**: `https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider-namespace}/{resource-type}/{resource-name}/providers/microsoft.insights/metrics?$filter={filter}&api-version={apiVersion}`

Als u bijvoorbeeld de RunsSucceeded metric data Points wilt ophalen voor het opgegeven tijds bereik en voor een tijd korrel van 1 uur, zou de aanvraag er als volgt uitzien:

```powershell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-results.json" `
    -Verbose
```

De resulterende hoofd tekst van het JSON-antwoord zou er ongeveer uitzien als in het volgende voor beeld:

```json
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T19:00:00Z",
          "total": 0.0
        },
        {
          "timeStamp": "2017-08-18T20:00:00Z",
          "total": 159.0
        },
        {
          "timeStamp": "2017-08-18T21:00:00Z",
          "total": 174.0
        },
        {
          "timeStamp": "2017-08-18T22:00:00Z",
          "total": 97.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

Als u meerdere gegevens of aggregatie punten wilt ophalen, voegt u de metrische definitie namen en aggregatie typen toe aan het filter, zoals in het volgende voor beeld wordt weer gegeven:

```powershell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-multiple-results.json" `
    -Verbose
```

De resulterende hoofd tekst van het JSON-antwoord zou er ongeveer uitzien als in het volgende voor beeld:

```json
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/ActionsCompleted",
      "name": {
        "value": "ActionsCompleted",
        "localizedValue": "Actions Completed "
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

### <a name="use-armclient"></a>ARMClient gebruiken

Een extra aanpak is het gebruik van [ARMClient](https://github.com/projectkudu/armclient) op de Windows-computer. ARMClient verwerkt de Azure AD-verificatie (en de resulterende JWT-token) automatisch. De volgende stappen beschrijven het gebruik van ARMClient voor het ophalen van metrische gegevens:

1. Installeer [chocolade](https://chocolatey.org/) en [ARMClient](https://github.com/projectkudu/armclient).
2. Typ *armclient.exe login* in een Terminal venster. U wordt dan gevraagd u aan te melden bij Azure.
3. Typ *ARMCLIENT Get [your_resource_id]/providers/Microsoft.Insights/metricdefinitions? API-Version = 2016-03-01*
4. Typ *ARMCLIENT Get [your_resource_id]/providers/Microsoft.Insights/Metrics? API-Version = 2016-09-01*

Als u bijvoorbeeld de metrische definities voor een specifieke logische app wilt ophalen, geeft u de volgende opdracht:

```console
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```

## <a name="retrieve-the-resource-id"></a>De resource-ID ophalen

Met behulp van de REST API kunt u de beschik bare metrische definities, granulatie en gerelateerde waarden echt begrijpen. Deze informatie is nuttig bij het gebruik van de [Azure-beheer bibliotheek](/previous-versions/azure/reference/mt417623(v=azure.100)).

Voor de voor gaande code is de resource-ID die moet worden gebruikt het volledige pad naar de gewenste Azure-resource. Als u bijvoorbeeld een query wilt uitvoeren op een Azure-web-app, is de resource-ID:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

De volgende lijst bevat enkele voor beelden van Resource-ID-indelingen voor verschillende Azure-resources:

* **IOT hub** -/Subscriptions/*{abonnements-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.devices/IotHubs/*{IOT-hub-name}*
* **Elastische SQL-pool** -/Subscriptions/*{abonnements-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.SQL/servers/*{pool-DB}*/elasticpools/*{SQL-pool-name}*
* **SQL database (V12)** -/Subscriptions/*{abonnements-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.SQL/servers/*{server-name}*/databases/*{Data Base-name}*
* **Service Bus** -/Subscriptions/*{abonnements-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.ServiceBus/*{Namespace}* / *{ServiceBus-name}*
* **Schaal sets voor virtuele machines** -/Subscriptions/*{abonnements-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{VM-name}*
* **Vm's** -/Subscriptions/*{abonnements-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachines/*{VM-name}*
* **Event hubs** -/Subscriptions/*{abonnements-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.EventHub/namespaces/*{EventHub-namespace}*

Er zijn alternatieve benaderingen voor het ophalen van de resource-ID, inclusief het gebruik van Azure Resource Explorer, het weer geven van de gewenste resource in de Azure Portal en via Power shell of de Azure CLI.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer

U kunt de resource-ID voor een gewenste resource vinden door de [Azure resource Explorer](https://resources.azure.com) tool te gebruiken. Ga naar de gewenste resource en Bekijk de weer gegeven ID, zoals in de volgende scherm afbeelding:

![ALT "Azure Resource Explorer"](./media/rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure Portal

De resource-ID kan ook worden opgehaald uit de Azure Portal. Hiertoe gaat u naar de gewenste resource en selecteert u vervolgens Eigenschappen. De resource-ID wordt weer gegeven in de sectie eigenschappen, zoals u kunt zien in de volgende scherm afbeelding:

![ALT "Resource-ID wordt weer gegeven op de Blade eigenschappen in de Azure Portal"](./media/rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell

De resource-ID kan ook worden opgehaald met behulp van Azure PowerShell-cmdlets. Als u bijvoorbeeld de resource-ID voor een Azure Logic-app wilt ophalen, voert u de cmdlet Get-AzureLogicApp uit, zoals in het volgende voor beeld:

```powershell
Get-AzLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

Het resultaat moet er ongeveer uitzien als in het volgende voor beeld:

```output
Id             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets
Name           : ContosoTweets
Type           : Microsoft.Logic/workflows
Location       : centralus
ChangedTime    : 8/21/2017 6:58:57 PM
CreatedTime    : 8/18/2017 7:54:21 PM
AccessEndpoint : https://prod-08.centralus.logic.azure.com:443/workflows/f3a91b352fcc47e6bff989b85446c5db
State          : Enabled
Definition     : {$schema, contentVersion, parameters, triggers...}
Parameters     : {[$connections, Microsoft.Azure.Management.Logic.Models.WorkflowParameter]}
SkuName        :
AppServicePlan :
PlanType       :
PlanId         :
Version        : 08586982649483762729
```

### <a name="azure-cli"></a>Azure CLI

Als u de resource-ID voor een Azure Storage-account wilt ophalen met behulp van de Azure CLI, voert u de `az storage account show` opdracht uit, zoals in het volgende voor beeld wordt weer gegeven:

```azurecli
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

Het resultaat moet er ongeveer uitzien als in het volgende voor beeld:

```json
{
  "accessTier": null,
  "creationTime": "2017-08-18T19:58:41.840552+00:00",
  "customDomain": null,
  "enableHttpsTrafficOnly": false,
  "encryption": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/contosotweets2017",
  "identity": null,
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "centralus",
  "name": "contosotweets2017",
  "networkAcls": null,
  "primaryEndpoints": {
    "blob": "https://contosotweets2017.blob.core.windows.net/",
    "file": "https://contosotweets2017.file.core.windows.net/",
    "queue": "https://contosotweets2017.queue.core.windows.net/",
    "table": "https://contosotweets2017.table.core.windows.net/"
  },
  "primaryLocation": "centralus",
  "provisioningState": "Succeeded",
  "resourceGroup": "azmon-rest-api-walkthrough",
  "secondaryEndpoints": null,
  "secondaryLocation": "eastus2",
  "sku": {
    "name": "Standard_GRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": "available",
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

> [!NOTE]
> Azure Logic Apps zijn nog niet beschikbaar via de Azure CLI, dus een Azure Storage-account wordt weer gegeven in het vorige voor beeld.
>
>

## <a name="retrieve-activity-log-data"></a>Gegevens van activiteiten logboek ophalen

Naast metrische definities en gerelateerde waarden is het ook mogelijk om de Azure Monitor REST API te gebruiken voor het ophalen van extra interessante inzichten die betrekking hebben op Azure-resources. Zo is het mogelijk om gegevens in een [activiteiten logboek](/rest/api/monitor/activitylogs) op te vragen. De volgende voorbeeld aanvragen gebruiken de Azure Monitor REST API om een query uit te kunnen uitvoeren op het activiteiten logboek.

Activiteiten logboeken ophalen met het filter:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Activiteiten logboeken ophalen met het filter en Select:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Activiteiten logboeken ophalen met Select:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Activiteiten logboeken ophalen zonder filter of selectie:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```

## <a name="next-steps"></a>Volgende stappen

* Bekijk het [overzicht van bewaking](../overview.md).
* Bekijk de [ondersteunde metrische gegevens met Azure monitor](./metrics-supported.md).
* Raadpleeg de [rest API-naslag informatie over Microsoft Azure controle](/rest/api/monitor/).
* Bekijk de [Azure Management Library](/previous-versions/azure/reference/mt417623(v=azure.100)).