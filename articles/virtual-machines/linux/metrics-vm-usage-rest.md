---
title: Azure virtual machine-gebruiks gegevens ophalen met behulp van de REST API
description: Gebruik de Azure REST Api's voor het verzamelen van metrische gegevens over gebruik voor een virtuele machine.
author: rloutlaw
ms.service: virtual-machines
ms.subservice: monitoring
ms.custom: REST
ms.topic: how-to
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: a7237bfc82a932b774b4b6ef293c242a84fd75af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100591210"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Metrische gegevens over het gebruik van virtuele machines ophalen met behulp van de REST API

In dit voor beeld ziet u hoe u het CPU-gebruik voor een virtuele Linux-machine ophaalt met behulp van de [Azure-rest API](/rest/api/azure/).

Volledige referentie documentatie en aanvullende voor beelden voor de REST API zijn beschikbaar in de [Azure monitor rest-referentie](/rest/api/monitor). 

## <a name="build-the-request"></a>De aanvraag maken

Gebruik de volgende GET-aanvraag voor het verzamelen van het [percentage CPU-metriek](../../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachines) van een virtuele machine

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
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
| resourceGroupName | De naam van de Azure-resource groep die is gekoppeld aan de resource. U kunt deze waarde ophalen uit de Azure Resource Manager-API, CLI of de portal. |
| vmname | De naam van de virtuele machine van Azure. |
| metricnames | Een door komma's gescheiden lijst met geldige  [Load Balancer metrische gegevens](../../load-balancer/load-balancer-standard-diagnostics.md). |
| api-versie | De API-versie die voor de aanvraag moet worden gebruikt.<br /><br /> In dit document wordt de API-versie beschreven `2018-01-01` die is opgenomen in de bovenstaande URL.  |
| tijdsbestek | Teken reeks met de volgende indeling `startDateTime_ISO/endDateTime_ISO` die het tijds bereik van de geretourneerde metrische gegevens definieert. Deze optionele para meter is zo ingesteld dat de gegevens van een dag in het voor beeld worden geretourneerd. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Aanvraagbody

Er is geen aanvraag tekst nodig voor deze bewerking.

## <a name="handle-the-response"></a>Het antwoord verwerken

De status code 200 wordt geretourneerd wanneer de lijst met metrische waarden is geretourneerd. In de [referentie documentatie](/rest/api/monitor/metrics/list#errorresponse)vindt u een volledige lijst met fout codes.

## <a name="example-response"></a>Voorbeeld van een antwoord 

```json
{
    "cost": 0,
    "timespan": "2018-06-08T23:48:10Z/2018-06-09T00:48:10Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "Percentage CPU",
                "localizedValue": "Percentage CPU"
            },
            "unit": "Percent",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-08T23:48:00Z",
                            "average": 0.44
                        },
                        {
                            "timeStamp": "2018-06-08T23:49:00Z",
                            "average": 0.31
                        },
                        {
                            "timeStamp": "2018-06-08T23:50:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:51:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:52:00Z",
                            "average": 0.285
                        } ]
                } ]
        } ]
}
```
