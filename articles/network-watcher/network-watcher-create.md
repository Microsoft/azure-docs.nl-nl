---
title: Een Azure Network Watcher-exemplaar maken | Microsoft Docs
description: Meer informatie over het maken van een Azure-Network Watcher in een Azure-regio met behulp van de Azure Portal of andere technologieën en hoe u een Network Watcher kunt verwijderen.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: c308824afdcae5f5c04a316c199bad71ad84a429
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94961988"
---
# <a name="create-an-azure-network-watcher-instance"></a>Een Azure Network Watcher-exemplaar maken

Network Watcher is een regionale service waarmee u voorwaarden op het niveau van netwerkscenario's in, naar en vanaf Azure kunt controleren en onderzoeken. Via controle op het scenarioniveau kunt u problemen analyseren met behulp van een weergave op het niveau van een end-to-end netwerk. U kunt de beschikbare diagnostische en visualisatiehulpprogramma's voor netwerken in Network Watcher gebruiken om uw netwerk in Azure te begrijpen, te analyseren en inzichten voor uw netwerk te verkrijgen. Network Watcher wordt ingeschakeld door het maken van een Network Watcher resource. Met deze resource kunt u Network Watcher mogelijkheden gebruiken.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>Network Watcher wordt automatisch ingeschakeld
Wanneer u een virtueel netwerk in uw abonnement maakt of bijwerkt, wordt Network Watcher automatisch ingeschakeld in de regio van uw virtuele netwerk. Het automatisch inschakelen van Network Watcher heeft geen invloed op uw resources en er worden geen kosten in rekening gebracht.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Opt-out van Network Watcher automatische activering
Als u zich wilt afmelden Network Watcher automatische activering, kunt u dit doen door de volgende opdrachten uit te voeren:

> [!WARNING]
> Het afmelden van Network Watcher automatische activering is een permanente wijziging. Wanneer u opt-out hebt, kunt u zich niet aanmelden zonder [contact op](https://azure.microsoft.com/support/options/) te nemen met de ondersteuning

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Een Network Watcher maken in de portal

Ga naar **alle services**  >  **netwerk**  >  **Network Watcher**. U kunt alle abonnementen selecteren waarvoor u Network Watcher wilt inschakelen. Met deze actie maakt u een Network Watcher in elke regio die beschikbaar is.

![een netwerk-Watcher maken](./media/network-watcher-create/figure1.png)

Als u Network Watcher inschakelt met behulp van de portal, wordt de naam van de Network Watcher instantie automatisch ingesteld op *NetworkWatcher_region_name* waarbij *region_name* overeenkomt met de Azure-regio waar het exemplaar is ingeschakeld. Een Network Watcher die is ingeschakeld in de regio West-Centraal VS heeft bijvoorbeeld de naam *NetworkWatcher_westcentralus*.

Het Network Watcher-exemplaar wordt automatisch gemaakt in een resource groep met de naam *NetworkWatcherRG*. De resource groep wordt gemaakt als deze nog niet bestaat.

Als u de naam van een Network Watcher exemplaar en de resource groep waarin deze is geplaatst, wilt aanpassen, kunt u Power shell, de Azure CLI, de REST API of ARMClient-methoden gebruiken die worden beschreven in de volgende secties. In elke optie moet de resource groep bestaan voordat u er een Network Watcher maakt.  

## <a name="create-a-network-watcher-with-powershell"></a>Een Network Watcher maken met Power shell

Als u een exemplaar van Network Watcher wilt maken, voert u het volgende voor beeld uit:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Een Network Watcher maken met de Azure CLI

Als u een exemplaar van Network Watcher wilt maken, voert u het volgende voor beeld uit:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Een Network Watcher met de REST API maken

De ARMclient wordt gebruikt om de REST API aan te roepen met behulp van Power shell. De ARMClient is in Choco lade op [ARMClient in Choco](https://chocolatey.org/packages/ARMClient) lade gevonden

### <a name="log-in-with-armclient"></a>Aanmelden met ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>De netwerk-Watcher maken

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="create-a-network-watcher-using-azure-quickstart-template"></a>Een Network Watcher maken met behulp van Azure Quick Start-sjabloon

Als u een exemplaar van Network Watcher wilt maken, raadpleegt u deze Quick Start- [sjabloon](https://azure.microsoft.com/resources/templates/101-networkwatcher-create/)

## <a name="delete-a-network-watcher-in-the-portal"></a>Een Network Watcher in de Portal verwijderen

Ga naar **alle services**  >  **netwerk**  >  **Network Watcher**.

Selecteer het tabblad Overzicht als u dat nog niet hebt gedaan. Gebruik de vervolg keuzelijst om het abonnement te selecteren waarvoor u de Network Watcher wilt uitschakelen.
Vouw de lijst met regio's voor het gekozen abonnement uit door op de pijl te klikken. Gebruik voor elke gegeven de drie punten aan de rechter kant om het context menu te openen.
Klik op Network Watcher uitschakelen om het uitschakelen te starten. U wordt gevraagd om deze stap te bevestigen. Klik op Ja om door te gaan.
Op de portal moet u dit afzonderlijk doen voor elke regio in elk abonnement.


## <a name="delete-a-network-watcher-with-powershell"></a>Een Network Watcher verwijderen met Power shell

Als u een exemplaar van Network Watcher wilt verwijderen, voert u het volgende voor beeld uit:

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location westcentralus
New-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG -Location westcentralus
Remove-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG
```

## <a name="next-steps"></a>Volgende stappen

Nu u een exemplaar van Network Watcher hebt, hebt u meer informatie over de beschik bare functies:

* [Topologie](./view-network-topology.md)
* [Pakketopname](network-watcher-packet-capture-overview.md)
* [IP-stroom controleren](network-watcher-ip-flow-verify-overview.md)
* [Volgende hop](network-watcher-next-hop-overview.md)
* [Weergave van de beveiligingsgroep](network-watcher-security-group-view-overview.md)
* [Logboek registratie voor NSG-stroom](network-watcher-nsg-flow-logging-overview.md)
* [Problemen met Virtual Network gateway oplossen](network-watcher-troubleshoot-overview.md)