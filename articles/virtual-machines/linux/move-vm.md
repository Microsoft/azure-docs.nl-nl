---
title: Een virtuele machine verplaatsen met behulp van de Azure CLI
description: Verplaats een virtuele machine naar een ander Azure-abonnement of resource groep met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: db4c7e0126616e2d8bd120e7430c70b89c5cf36d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87291102"
---
# <a name="move-a-vm-to-another-subscription-or-resource-group"></a>Een VM verplaatsen naar een ander abonnement of een andere resource groep
In dit artikel wordt uitgelegd hoe u een virtuele machine (VM) verplaatst tussen resource groepen of abonnementen. Het verplaatsen van een virtuele machine tussen abonnementen kan handig zijn als u een virtuele machine in een persoonlijk abonnement hebt gemaakt en u deze nu wilt verplaatsen naar het abonnement van uw bedrijf.

> [!IMPORTANT]
>Nieuwe resource-Id's worden gemaakt als onderdeel van de verplaatsing. Nadat de VM is verplaatst, moet u uw hulp middelen en scripts bijwerken om de nieuwe resource-Id's te gebruiken.
>


## <a name="use-the-azure-cli-to-move-a-vm"></a>De Azure CLI gebruiken om een virtuele machine te verplaatsen


Voordat u uw virtuele machine kunt verplaatsen met behulp van de Azure CLI, moet u ervoor zorgen dat de bron-en doel abonnementen binnen dezelfde Tenant bestaan. Gebruik [AZ account show](/cli/azure/account)om te controleren of beide abonnementen dezelfde Tenant-id hebben.

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Als de Tenant-Id's voor de bron-en doel abonnementen niet hetzelfde zijn, moet u contact opnemen met de [ondersteuning](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) om de resources te verplaatsen naar een nieuwe Tenant.

Als u een virtuele machine wilt verplaatsen, moet u de virtuele machine en alle ondersteunende bronnen verplaatsen. Gebruik de opdracht [AZ Resource List](/cli/azure/resource) om alle resources in een resource groep en de bijbehorende id's weer te geven. Het helpt om de uitvoer van deze opdracht door te sluizen naar een bestand, zodat u de Id's in latere opdrachten kunt kopiëren en plakken.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Als u een virtuele machine en de bijbehorende resources wilt verplaatsen naar een andere resource groep, gebruikt u [AZ resource Move](/cli/azure/resource). In het volgende voor beeld ziet u hoe u een virtuele machine verplaatst en de meest voorkomende bronnen die nodig zijn. Gebruik de para meter **-id's** en geef een door komma's gescheiden lijst (zonder spaties) van id's door om de resources te verplaatsen.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Als u de virtuele machine en de bijbehorende resources naar een ander abonnement wilt verplaatsen, voegt u de para meter **--Destination-subscriptionId** toe om het doel abonnement op te geven.

Wanneer u wordt gevraagd om te bevestigen dat u de opgegeven resources wilt verplaatsen, voert u **Y** om te bevestigen.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Volgende stappen
U kunt verschillende soorten resources verplaatsen tussen resource groepen en abonnementen. Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)voor meer informatie.    
