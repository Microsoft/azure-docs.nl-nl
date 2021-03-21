---
title: 'Quickstart: een Azure Dedicated HSM maken met de Azure CLI'
description: U kunt Azure Dedicated HSM's maken, weergeven, vermelden, bijwerken en verwijderen met behulp van de Azure CLI.
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurecli
ms.date: 01/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: e07bc758b1ef86b3d8c605cbce72f6db564a355f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020853"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-by-using-the-azure-cli"></a>Quickstart: een Azure Dedicated HSM maken met de Azure CLI

In dit artikel wordt beschreven hoe u een Azure Dedicated HSM maakt en beheert met behulp van de Azure CLI-extensie [az dedicated-hsm](/cli/azure/ext/hardware-security-modules/dedicated-hsm).

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen account hebt, kunt u [een gratis account maken](https://azure.microsoft.com/free/).
  
  Als u meer dan één Azure-abonnement hebt, stelt u het abonnement dat u voor facturering wilt gebruiken in met de Azure CLI-opdracht [az account set](/cli/azure/account#az_account_set).
  
  ```azurecli-interactive
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]  
  
- Aan alle vereisten wordt voldaan voor een toegewezen HSM, waaronder registratie, goedkeuring en een virtueel netwerk en virtuele machine die voor het inrichten moeten worden gebruikt. Zie voor meer informatie over de vereisten voor een toegewezen HSM: [Zelfstudie: HSM's implementeren in een bestaand virtueel netwerk met behulp van de Azure CLI](tutorial-deploy-hsm-cli.md).
  

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) is een logische container voor het implementeren en beheren van Azure-resources als groep. Als u nog geen resourcegroep voor de toegewezen HSM hebt, maakt u er een met de opdracht [az group create](/cli/azure/group#az_group_create). In het volgende voorbeeld wordt een resourcegroep gemaakt met de naam `myRG` in de Azure-regio `westus`:

```azurecli-interactive
az group create --name myRG --location westus
```

## <a name="create-a-dedicated-hsm"></a>Een toegewezen HSM maken

Als u een toegewezen HSM wilt maken, gebruikt u de opdracht [az dedicated-hsm create](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_create). In het volgende voorbeeld wordt een toegewezen HSM met de naam `hsm1` ingericht in de regio `westus`, resourcegroep `myRG` en het opgegeven abonnement, virtuele netwerk en subnet. De vereiste parameters zijn `name`, `location` en `resource group`.

```azurecli-interactive
az dedicated-hsm create \
   --resource-group myRG \
   --name "hsm1" \
   --location "westus" \
   --network-profile-network-interfaces private-ip-address="1.0.0.1" \
   --subnet id="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/hsm-group/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet" \
   --stamp-id "stamp1" \
   --sku name="SafeNet Luna Network HSM A790" \
   --tags resourceType="hsm" Environment="test" \
   --zones "AZ1"
```

Het implementeren duurt ongeveer 25 tot 30 minuten.

## <a name="get-a-dedicated-hsm"></a>Verkrijg een toegewezen HSM

Als u een huidige toegewezen HSM wilt hebben, voert u de opdracht [az dedicated-hsm show](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_show) uit. In het volgende voorbeeld wordt de toegewezen HSM, `hsm1`, opgehaald in resourcegroep `myRG`.

```azurecli-interactive
az dedicated-hsm show --resource-group myRG --name hsm1
```

## <a name="update-a-dedicated-hsm"></a>Een toegewezen HSM bijwerken

Gebruik de opdracht [az dedicated-hsm update](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_update) om een toegewezen HSM bij te werken. In het volgende voorbeeld wordt de toegewezen HSM, `hsm1`, samen met de tags bijgewerkt in resourcegroep `myRG`:

```azurecli-interactive
az dedicated-hsm update --resource-group myRG –-name hsm1 --tags resourceType="hsm" Environment="prod" Slice="A"
```

## <a name="list-dedicated-hsms"></a>Toegewezen HSM's vermelden

Voer de opdracht [az dedicated-hsm list](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_list) uit om informatie over de huidige toegewezen HSM's op te halen. In het volgende voorbeeld worden de toegewezen HSM's vermeld in resourcegroep `myRG`:

```azurecli-interactive
az dedicated-hsm list --resource-group myRG
```

## <a name="remove-a-dedicated-hsm"></a>Een toegewezen HSM verwijderen

Als u een toegewezen HSM wilt verwijderen, gebruikt u de opdracht [az dedicated-hsm delete](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_delete). In het volgende voorbeeld wordt de toegewezen HSM, `hsm1`, verwijderd uit resourcegroep `myRG`:

```azurecli-interactive
az dedicated-hsm delete --resource-group myRG –-name hsm1
```

## <a name="delete-the-resource-group"></a>De resourcegroep verwijderen

Als u de resourcegroep die u voor een toegewezen HSM hebt gemaakt, niet meer nodig hebt, kunt u deze verwijderen door de opdracht [az group delete](/cli/azure/group#az_group_delete) uit te voeren. Met deze opdracht worden de groep en alle resources erin verwijderd, inclusief resources die geen verband houden met de toegewezen HSM. In het volgende voorbeeld wordt resourcegroep `myRG` en alles wat erin zit verwijderd:

```azurecli-interactive
az group delete --name myRG
```

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Dedicated HSM](overview.md) voor meer informatie over Azure Dedicated HSM.
