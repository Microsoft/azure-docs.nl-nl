---
title: Problemen met Azure VNET-gateway en-verbindingen oplossen-Azure CLI
titleSuffix: Azure Network Watcher
description: Op deze pagina wordt uitgelegd hoe u de Azure-Network Watcher gebruikt voor het oplossen van problemen met Azure CLI
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
ms.openlocfilehash: d04ba6cc5f8527f8c05fbeb323c65230447e1e2e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019700"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli"></a>Problemen met Virtual Network-gateway en-verbindingen met Azure Network Watcher Azure CLI oplossen

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure-CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher biedt veel mogelijkheden voor het koppelen van uw netwerk bronnen in Azure. Een van deze mogelijkheden is het oplossen van problemen met resources. Het oplossen van resources kan worden aangeroepen via de portal, Power shell, CLI of REST API. Als Network Watcher wordt aangeroepen, wordt de status van een Virtual Network gateway of een verbinding gecontroleerd en worden de bevindingen daarvan geretourneerd.

Als u de stappen in dit artikel wilt uitvoeren, moet u [de Azure-opdracht regel interface voor Mac, Linux en Windows (CLI) installeren](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Voordat u begint

In dit scenario wordt ervan uitgegaan dat u de stappen in [Create a Network Watcher](network-watcher-create.md) voor het maken van een Network Watcher, al hebt gevolgd.

Voor een lijst met ondersteunde gateway typen gaat u naar [ondersteunde gateway typen](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Overzicht

Probleem oplossing voor bronnen biedt de mogelijkheid problemen op te lossen die zich voordoen met Virtual Network gateways en verbindingen. Wanneer er een aanvraag wordt gedaan om problemen op te lossen met resources, worden er door Logboeken query's uitgevoerd en geïnspecteerd. Wanneer de inspectie is voltooid, worden de resultaten geretourneerd. Aanvragen voor het oplossen van problemen zijn langlopende aanvragen. Dit kan enkele minuten duren voordat een resultaat wordt geretourneerd. De logboeken van het oplossen van problemen worden opgeslagen in een container op een opslag account dat is opgegeven.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Een Virtual Network gateway verbinding ophalen

In dit voor beeld wordt het oplossen van resources uitgevoerd op een verbinding. U kunt ook een Virtual Network gateway door geven. Met de volgende cmdlet worden de VPN-verbindingen in een resource groep weer gegeven.

```azurecli
az network vpn-connection list --resource-group resourceGroupName
```

Zodra u de naam van de verbinding hebt, kunt u deze opdracht uitvoeren om de resource-id op te halen:

```azurecli
az network vpn-connection show --resource-group resourceGroupName --ids vpnConnectionIds
```

## <a name="create-a-storage-account"></a>Een opslagaccount maken

Resource-probleem oplossing retourneert gegevens over de status van de resource. logboeken worden ook opgeslagen in een opslag account om te worden gecontroleerd. In deze stap maken we een opslag account. als er een bestaand opslag account bestaat, kunt u dit gebruiken.

1. Het opslagaccount maken

    ```azurecli
    az storage account create --name storageAccountName --location westcentralus --resource-group resourceGroupName --sku Standard_LRS
    ```

1. De sleutels voor het opslag account ophalen

    ```azurecli
    az storage account keys list --resource-group resourcegroupName --account-name storageAccountName
    ```

1. De container maken

    ```azurecli
    az storage container create --account-name storageAccountName --account-key {storageAccountKey} --name logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Probleem oplossing voor Network Watcher resource uitvoeren

U kunt resources oplossen met de `az network watcher troubleshooting` cmdlet. We geven de cmdlet door aan de resource groep, de naam van de Network Watcher, de id van de verbinding, de id van het opslag account en het pad naar de BLOB voor het opslaan van de problemen met de probleem oplossing in.

```azurecli
az network watcher troubleshooting start --resource-group resourceGroupName --resource resourceName --resource-type {vnetGateway/vpnConnection} --storage-account storageAccountName  --storage-path https://{storageAccountName}.blob.core.windows.net/{containerName}
```

Zodra u de cmdlet hebt uitgevoerd, controleert Network Watcher de resource om de status te controleren. De resultaten worden geretourneerd naar de shell en de logboeken van de resultaten worden opgeslagen in het opgegeven opslag account.

## <a name="understanding-the-results"></a>Uitleg over de resultaten

De actie tekst bevat algemene richt lijnen voor het oplossen van het probleem. Als er een actie kan worden uitgevoerd voor het probleem, wordt er een koppeling met aanvullende richt lijnen gegeven. In het geval dat er geen aanvullende richt lijnen zijn, geeft het antwoord de URL om een ondersteunings aanvraag te openen.  Ga voor meer informatie over de eigenschappen van de reactie en wat is opgenomen naar [Network Watcher probleemoplossings overzicht](network-watcher-troubleshoot-overview.md)

Raadpleeg aan de [slag met Azure Blob Storage met .net](../storage/blobs/storage-quickstart-blobs-dotnet.md)voor instructies voor het downloaden van bestanden van Azure Storage-accounts. Een ander hulp programma dat kan worden gebruikt, is Storage Explorer. Meer informatie over Storage Explorer kunt u vinden op de volgende koppeling: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>Volgende stappen

Als de instellingen zijn gewijzigd waardoor de VPN-verbinding wordt verbroken, raadpleegt u [netwerk beveiligings groepen beheren](../virtual-network/manage-network-security-group.md) om de netwerk beveiligings groep en beveiligings regels te traceren die mogelijk in aanmerking komen.