---
title: Een virtuele Azure-machine taggen met behulp van de Azure CLI
description: Meer informatie over het taggen van een virtuele machine met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 20bb4ab622a01646bcc61d0f691c514a25a06edc
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502603"
---
# <a name="how-to-tag-a-vm-using-the-azure-cli"></a>Een VM taggen met de Azure CLI

In dit artikel wordt beschreven hoe u een VM tagt met behulp van de Azure CLI. Tags zijn door de gebruiker gedefinieerde sleutel-waardeparen die rechtstreeks op een resource of resourcegroep kunnen worden geplaatst. Azure ondersteunt momenteel maximaal 50 tags per resource en resourcegroep. Tags kunnen worden geplaatst op een resource op het moment dat deze wordt gemaakt of worden toegevoegd aan een bestaande resource. U kunt een virtuele machine ook taggen met behulp van Azure [PowerShell.](tag-powershell.md)


U kunt alle eigenschappen voor een bepaalde VM, inclusief de tags, weergeven met behulp van `az vm show` .

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Als u een nieuwe VM-tag wilt toevoegen via de Azure CLI, kunt u de opdracht `azure vm update` gebruiken, samen met de tagparameter `--set` :

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Als u tags wilt verwijderen, kunt u de `--remove` parameter in de opdracht `azure vm update` gebruiken.

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

Nu we tags hebben toegepast op onze resources Azure CLI en de portal, gaan we de gebruiksgegevens bekijken om de tags in de factureringsportal te bekijken.

### <a name="next-steps"></a>Volgende stappen

- Voor meer informatie over het taggen van uw [Azure-resources,](../azure-resource-manager/management/overview.md) Azure Resource Manager Overzicht en [Tags gebruiken om uw Azure-resources te organiseren.](../azure-resource-manager/management/tag-resources.md)
- Zie Inzicht in uw Azure-factuur voor meer informatie over hoe tags u kunnen helpen bij het beheren van uw [gebruik van Azure-resources.](../cost-management-billing/understand/review-individual-bill.md)
