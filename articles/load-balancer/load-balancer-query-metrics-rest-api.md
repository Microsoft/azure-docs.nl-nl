---
title: Metrische gegevens ophalen met de REST API
titleSuffix: Azure Load Balancer
description: In dit artikel kunt u aan de slag met de Azure REST Api's voor het verzamelen van metrische gegevens over de status en het gebruik van Azure Load Balancer.
services: sql-database
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: how-to
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 391be596d890e05e6a8fdaf35d2cade371e468d6
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213180"
---
# <a name="get-load-balancer-usage-metrics-using-the-rest-api"></a>Load Balancer metrische gegevens over het gebruik van de REST API ophalen

Het aantal bytes dat is verwerkt door een [Standard Load Balancer](./load-balancer-overview.md) , verzamelen gedurende een tijds interval met behulp van de [Azure rest API](/rest/api/azure/).

Volledige referentie documentatie en aanvullende voor beelden voor de REST API zijn beschikbaar in de [Azure monitor rest-referentie](/rest/api/monitor). 

## <a name="build-the-request"></a>De aanvraag maken

Gebruik de volgende GET-aanvraag om de [ByteCount-metriek](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics) van een Standard Load Balancer te verzamelen. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Aanvraagheaders

De volgende headers zijn vereist: 

|Aanvraagheader|Beschrijving|  
|--------------------|-----------------|  
|*Content-Type:*|Vereist. Ingesteld op `application/json`.|  
|*Authorization:*|Vereist. Ingesteld op een geldig `Bearer` [toegangstoken](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>URI-para meters

| Naam | Beschrijving |
| :--- | :---------- |
| subscriptionId | De abonnements-ID waarmee een Azure-abonnement wordt geïdentificeerd. Als u meerdere abonnementen hebt, raadpleegt u [werken met meerdere abonnementen](/cli/azure/manage-azure-subscriptions-azure-cli). |
| resourceGroupName | De naam van de resource groep die de resource bevat. U kunt deze waarde verkrijgen via de Azure Resource Manager-API, CLI of de portal. |
| loadBalancerName | De naam van de Azure Load Balancer. |
| metrische namen | Een door komma's gescheiden lijst met geldige  [Load Balancer metrische gegevens](./load-balancer-standard-diagnostics.md). |
| api-versie | De API-versie die voor de aanvraag moet worden gebruikt.<br /><br /> In dit document wordt de API-versie beschreven `2018-01-01` die is opgenomen in de bovenstaande URL.  |
| tijdsbestek | De time span van de query. Het is een teken reeks met de volgende indeling `startDateTime_ISO/endDateTime_ISO` . Deze optionele para meter is zo ingesteld dat de gegevens van een dag in het voor beeld worden geretourneerd. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Aanvraagbody

Er is geen aanvraag tekst nodig voor deze bewerking.

## <a name="handle-the-response"></a>Het antwoord verwerken

De status code 200 wordt geretourneerd wanneer de lijst met metrische waarden is geretourneerd. In de [referentie documentatie](/rest/api/monitor/metrics/list#errorresponse)vindt u een volledige lijst met fout codes.

## <a name="example-response"></a>Voorbeeld van een antwoord 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```