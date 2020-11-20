---
title: Problemen met Azure VNet-gateway en-verbindingen oplossen-Azure PowerShell
titleSuffix: Azure Network Watcher
description: Op deze pagina wordt uitgelegd hoe u de Azure Network Watcher problemen met de Power shell-cmdlet kunt gebruiken
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: 2c394eeef746db978360e4434295520536833558
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960509"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Problemen met Virtual Network-gateway en-verbindingen met Azure Network Watcher Power shell oplossen

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure-CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher biedt veel mogelijkheden voor het koppelen van uw netwerk bronnen in Azure. Een van deze mogelijkheden is het oplossen van problemen met resources. Het oplossen van resources kan worden aangeroepen via de portal, Power shell, CLI of REST API. Als Network Watcher wordt aangeroepen, wordt de status van een Virtual Network gateway of een verbinding gecontroleerd en worden de bevindingen daarvan geretourneerd.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Voordat u begint

In dit scenario wordt ervan uitgegaan dat u de stappen in [Create a Network Watcher](network-watcher-create.md) voor het maken van een Network Watcher, al hebt gevolgd.

Voor een lijst met ondersteunde gateway typen gaat u naar [ondersteunde gateway typen](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Overzicht

Probleem oplossing voor bronnen biedt de mogelijkheid problemen op te lossen die zich voordoen met Virtual Network gateways en verbindingen. Wanneer er een aanvraag wordt gedaan om problemen op te lossen met resources, worden er door Logboeken query's uitgevoerd en geïnspecteerd. Wanneer de inspectie is voltooid, worden de resultaten geretourneerd. Aanvragen voor het oplossen van problemen zijn langlopende aanvragen. Dit kan enkele minuten duren voordat een resultaat wordt geretourneerd. De logboeken van het oplossen van problemen worden opgeslagen in een container op een opslag account dat is opgegeven.

## <a name="retrieve-network-watcher"></a>Network Watcher ophalen

De eerste stap bestaat uit het ophalen van het Network Watcher-exemplaar. De `$networkWatcher` variabele wordt door gegeven aan de `Start-AzNetworkWatcherResourceTroubleshooting` cmdlet in stap 4.

```powershell
$networkWatcher = Get-AzNetworkWatcher -Location "WestCentralUS" 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Een Virtual Network gateway verbinding ophalen

In dit voor beeld wordt het oplossen van resources uitgevoerd op een verbinding. U kunt ook een Virtual Network gateway door geven.

```powershell
$connection = Get-AzVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>Create a storage account

Resource-probleem oplossing retourneert gegevens over de status van de resource. logboeken worden ook opgeslagen in een opslag account om te worden gecontroleerd. In deze stap maken we een opslag account. als er een bestaand opslag account bestaat, kunt u dit gebruiken.

```powershell
$sa = New-AzStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Probleem oplossing voor Network Watcher resource uitvoeren

U kunt resources oplossen met de `Start-AzNetworkWatcherResourceTroubleshooting` cmdlet. De cmdlet wordt door gegeven aan het Network Watcher-object, de id van de verbinding of Virtual Network gateway, de opslag account-id en het pad voor het opslaan van de resultaten.

> [!NOTE]
> De `Start-AzNetworkWatcherResourceTroubleshooting` cmdlet is lang actief. het kan enkele minuten duren voordat deze is voltooid.

```powershell
Start-AzNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

Zodra u de cmdlet hebt uitgevoerd, controleert Network Watcher de resource om de status te controleren. De resultaten worden geretourneerd naar de shell en de logboeken van de resultaten worden opgeslagen in het opgegeven opslag account.

## <a name="understanding-the-results"></a>Uitleg over de resultaten

De actie tekst bevat algemene richt lijnen voor het oplossen van het probleem. Als er een actie kan worden uitgevoerd voor het probleem, wordt er een koppeling met aanvullende richt lijnen gegeven. In het geval dat er geen aanvullende richt lijnen zijn, geeft het antwoord de URL om een ondersteunings aanvraag te openen.  Ga voor meer informatie over de eigenschappen van de reactie en wat is opgenomen naar [Network Watcher probleemoplossings overzicht](network-watcher-troubleshoot-overview.md)

Raadpleeg aan de [slag met Azure Blob Storage met .net](../storage/blobs/storage-quickstart-blobs-dotnet.md)voor instructies voor het downloaden van bestanden van Azure Storage-accounts. Een ander hulp programma dat kan worden gebruikt, is Storage Explorer. Meer informatie over Storage Explorer kunt u vinden op de volgende koppeling: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>Volgende stappen

Als de instellingen zijn gewijzigd waardoor de VPN-verbinding wordt verbroken, raadpleegt u [netwerk beveiligings groepen beheren](../virtual-network/manage-network-security-group.md) om de netwerk beveiligings groep en beveiligings regels te traceren die mogelijk in aanmerking komen.