---
title: Problemen met VNET-gateway en-verbindingen oplossen-Azure REST API
titleSuffix: Azure Network Watcher
description: Op deze pagina wordt uitgelegd hoe u Virtual Network gateways en verbindingen met Azure Network Watcher kunt oplossen met behulp van REST
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 72f279d90d071b0e5fe7f552a05474bc3b37a82e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98011030"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Problemen met Virtual Network-gateway en-verbindingen met Azure Network Watcher oplossen

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure-CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher biedt veel mogelijkheden voor het koppelen van uw netwerk bronnen in Azure. Een van deze mogelijkheden is het oplossen van problemen met resources. Het oplossen van resources kan worden aangeroepen via de portal, Power shell, CLI of REST API. Als Network Watcher wordt aangeroepen, wordt de status van een Virtual Network gateway of een verbinding gecontroleerd en worden de bevindingen daarvan geretourneerd.

In dit artikel vindt u informatie over de verschillende beheer taken die momenteel beschikbaar zijn voor het oplossen van problemen met resources.

- [**Problemen met een Virtual Network gateway oplossen**](#troubleshoot-a-virtual-network-gateway)
- [**Problemen met een verbinding oplossen**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Voordat u begint

ARMclient wordt gebruikt om de REST API aan te roepen met behulp van Power shell. ARMClient is in Choco lade op [ARMClient op Choco lade](https://chocolatey.org/packages/ARMClient) gevonden

In dit scenario wordt ervan uitgegaan dat u de stappen in [Create a Network Watcher](network-watcher-create.md) voor het maken van een Network Watcher, al hebt gevolgd.

Voor een lijst met ondersteunde gateway typen gaat u naar [ondersteunde gateway typen](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Overzicht

Network Watcher probleem oplossing biedt de mogelijkheid om problemen op te lossen die zich voordoen met Virtual Network gateways en verbindingen. Wanneer er een aanvraag wordt gedaan voor het oplossen van problemen met resources, worden logboeken query's uitgevoerd en geïnspecteerd. Wanneer de inspectie is voltooid, worden de resultaten geretourneerd. De API-aanvragen voor het oplossen van problemen zijn langlopende aanvragen. Dit kan enkele minuten duren voordat een resultaat wordt geretourneerd. Logboeken worden opgeslagen in een container op een opslag account.

## <a name="log-in-with-armclient"></a>Aanmelden met ARMClient

```powershell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>Problemen met een Virtual Network gateway oplossen


### <a name="post-the-troubleshoot-request"></a>De aanvraag voor probleem oplossing plaatsen

In het volgende voor beeld wordt de status van een Virtual Network gateway opgevraagd.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$vnetGatewayName = "ContosoVNETGateway"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @"
{
'TargetResourceId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/virtualNetworkGateways/${vnetGatewayName}',
'Properties': {
'StorageId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}',
'StoragePath': 'https://${storageAccountName}.blob.core.windows.net/${containerName}'
}
}
"@


armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30" $requestBody -verbose
```

Omdat deze bewerking langdurig wordt uitgevoerd, wordt de URI voor het opvragen van de bewerking en de URI voor het resultaat geretourneerd in de reactie header, zoals weer gegeven in het volgende antwoord:

**Belang rijke waarden**

* **Azure-AsyncOperation** : deze eigenschap bevat de URI voor het opvragen van de asynchrone probleemoplossings bewerking
* **Locatie** : deze eigenschap bevat de URI waar de resultaten zijn wanneer de bewerking is voltooid

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Een query uitvoeren op de asynchrone bewerking voor voltooiing

Gebruik de operations-URI om te zoeken naar de voortgang van de bewerking, zoals in het volgende voor beeld wordt weer gegeven:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Terwijl de bewerking wordt uitgevoerd, wordt in het antwoord de **voortgang** weer gegeven, zoals in het volgende voor beeld wordt weer gegeven:

```json
{
  "status": "InProgress"
}
```

Wanneer de bewerking is voltooid, wordt de status gewijzigd in **geslaagd**.

```json
{
  "status": "Succeeded"
}
```

### <a name="retrieve-the-results"></a>De resultaten ophalen

Zodra de **status is geretourneerd, roept** u een Get-methode aan op de kan OPERATIONRESULT niet-URI om de resultaten op te halen.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

De volgende antwoorden zijn voor beelden van een typische, gedegradeerde reactie die wordt geretourneerd bij het uitvoeren van een query op de resultaten van het oplossen van problemen met een gateway. Zie wat [de resultaten zijn](#understanding-the-results) als u wilt weten wat de eigenschappen in het antwoord betekenen.

```json
{
  "startTime": "2017-01-12T10:31:41.562646-08:00",
  "endTime": "2017-01-12T18:31:48.677Z",
  "code": "Degraded",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN Gateway"
        },
        {
          "actionText": "If your VPN gateway isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN gateway is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```


## <a name="troubleshoot-connections"></a>Problemen met verbindingen oplossen

In het volgende voor beeld wordt de status van een verbinding opgevraagd.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$connectionName = "VNET2toVNET1Connection"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @{
"TargetResourceId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/connections/${connectionName}",
"Properties": {
"StorageId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}",
"StoragePath": "https://${storageAccountName}.blob.core.windows.net/${containerName}"
}

}
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30 $requestBody"
```

> [!NOTE]
> De probleemoplossings bewerking kan niet parallel worden uitgevoerd op een verbinding en de bijbehorende gateways. De bewerking moet worden voltooid voordat deze op de vorige resource kan worden uitgevoerd.

Omdat dit een langlopende trans actie is, wordt in de antwoord header de URI voor het opvragen van de bewerking en de URI voor het resultaat geretourneerd, zoals wordt weer gegeven in het volgende antwoord:

**Belang rijke waarden**

* **Azure-AsyncOperation** : deze eigenschap bevat de URI voor het opvragen van de asynchrone probleemoplossings bewerking
* **Locatie** : deze eigenschap bevat de URI waar de resultaten zijn wanneer de bewerking is voltooid

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Een query uitvoeren op de asynchrone bewerking voor voltooiing

Gebruik de operations-URI om te zoeken naar de voortgang van de bewerking, zoals in het volgende voor beeld wordt weer gegeven:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Terwijl de bewerking wordt uitgevoerd, wordt in het antwoord de **voortgang** weer gegeven, zoals in het volgende voor beeld wordt weer gegeven:

```json
{
  "status": "InProgress"
}
```

Wanneer de bewerking is voltooid, verandert de status in **geslaagd**.

```json
{
  "status": "Succeeded"
}
```

De volgende antwoorden zijn voor beelden van een typische reactie die wordt geretourneerd bij het uitvoeren van een query op de resultaten van het oplossen van problemen met een verbinding.

### <a name="retrieve-the-results"></a>De resultaten ophalen

Zodra de **status is geretourneerd, roept** u een Get-methode aan op de kan OPERATIONRESULT niet-URI om de resultaten op te halen.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

De volgende antwoorden zijn voor beelden van een typische reactie die wordt geretourneerd bij het uitvoeren van een query op de resultaten van het oplossen van problemen met een verbinding.

```json
{
  "startTime": "2017-01-12T14:09:19.1215346-08:00",
  "endTime": "2017-01-12T22:09:23.747Z",
  "code": "UnHealthy",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This 
is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN gateway"
        },
        {
          "actionText": "If your VPN Connection isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN Connection is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```

## <a name="understanding-the-results"></a>Uitleg over de resultaten

De actie tekst bevat algemene richt lijnen voor het oplossen van het probleem. Als er een actie kan worden uitgevoerd voor het probleem, wordt er een koppeling met aanvullende richt lijnen gegeven. In het geval dat er geen aanvullende richt lijnen zijn, geeft het antwoord de URL om een ondersteunings aanvraag te openen.  Ga voor meer informatie over de eigenschappen van de reactie en wat is opgenomen naar [Network Watcher probleemoplossings overzicht](network-watcher-troubleshoot-overview.md)

Raadpleeg aan de [slag met Azure Blob Storage met .net](../storage/blobs/storage-quickstart-blobs-dotnet.md)voor instructies voor het downloaden van bestanden van Azure Storage-accounts. Een ander hulp programma dat kan worden gebruikt, is Storage Explorer. Meer informatie over Storage Explorer kunt u vinden op de volgende koppeling: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>Volgende stappen

Als de instellingen zijn gewijzigd waardoor de VPN-verbinding wordt verbroken, raadpleegt u [netwerk beveiligings groepen beheren](../virtual-network/manage-network-security-group.md) om de netwerk beveiligings groep en beveiligings regels te traceren die mogelijk in aanmerking komen.