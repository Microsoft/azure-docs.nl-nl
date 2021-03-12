---
title: Key Vault instellen met behulp van Power shell
description: Key Vault instellen voor gebruik met een virtuele machine met behulp van Power shell.
author: mimckitt
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/24/2017
ms.author: mimckitt
ms.openlocfilehash: 124ab121186ff7c9047515f2ee1ff51cee1114b8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557485"
---
# <a name="set-up-key-vault-for-virtual-machines-using-azure-powershell"></a>Key Vault instellen voor virtuele machines met behulp van Azure PowerShell

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

In Azure Resource Manager stack worden geheimen/certificaten gemodelleerd als bronnen die worden verschaft door de resource provider van Key Vault. Zie [Wat is Azure Key Vault?](../../key-vault/general/overview.md) voor meer informatie over Key Vault.

> [!NOTE]
> 1. Als Key Vault moet worden gebruikt met Azure Resource Manager virtuele machines, moet de eigenschap **EnabledForDeployment** op Key Vault worden ingesteld op True. U kunt dit doen in verschillende clients.
> 2. De Key Vault moet worden gemaakt in hetzelfde abonnement en dezelfde locatie als de virtuele machine.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Power shell gebruiken voor het instellen van Key Vault
Zie [een geheim instellen en ophalen van Azure Key Vault met behulp van Power shell](../../key-vault/secrets/quick-create-powershell.md)om een sleutel kluis te maken met behulp van Power shell.

Voor nieuwe sleutel kluizen kunt u deze Power shell-cmdlet gebruiken:

```azurepowershell
New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment
```

Voor bestaande sleutel kluizen kunt u deze Power shell-cmdlet gebruiken:

```azurepowershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment
```

## <a name="use-cli-to-set-up-key-vault"></a>CLI gebruiken om Key Vault in te stellen
Zie [Key Vault beheren met CLI](../../key-vault/general/manage-with-cli2.md#create-a-key-vault)voor informatie over het maken van een sleutel kluis met behulp van de opdracht regel interface (CLI).

Voor CLI moet u de sleutel kluis maken voordat u het implementatie beleid toewijst. U gebruikt hiervoor de volgende opdracht:

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
```

Voer vervolgens de volgende opdracht uit om Key Vault in te scha kelen voor gebruik met sjabloon implementatie:

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
```

## <a name="use-templates-to-set-up-key-vault"></a>Sjablonen gebruiken om Key Vault in te stellen
Wanneer u een sjabloon gebruikt, moet u de eigenschap instellen `enabledForDeployment` op `true` voor de Key Vault resource.

```config
{
  "type": "Microsoft.KeyVault/vaults",
  "name": "ContosoKeyVault",
  "apiVersion": "2015-06-01",
  "location": "<location-of-key-vault>",
  "properties": {
    "enabledForDeployment": "true",
    ....
    ....
  }
}
```

Zie [een sleutel kluis maken](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)voor andere opties die u kunt configureren wanneer u een sleutel kluis maakt met behulp van sjablonen.
