---
title: IP-groepen maken in Azure Firewall
description: Met IP-groepen kunt u IP-adressen voor Azure Firewall regels groeperen en beheren.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c53b35351108717c7b597e052a66e9902be5ec6c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93394523"
---
# <a name="create-ip-groups"></a>IP-groepen maken

Met IP-groepen kunt u IP-adressen voor Azure Firewall regels groeperen en beheren. Ze kunnen één IP-adres, meerdere IP-adressen of een of meer IP-adresbereiken hebben.

## <a name="create-an-ip-group---azure-portal"></a>Een IP-groep maken-Azure Portal

Een IP-groep maken met behulp van de Azure Portal:

1. Op de start pagina van Azure Portal selecteert u **een resource maken**.
1. Voer in het zoekvak **IP-groepen** in en selecteer vervolgens **IP-groepen**.
1. Selecteer **Maken**.
1. Selecteer uw abonnement.
1. Selecteer een resource groep of maak een nieuwe.
1. Voer een unieke naam in voor de IP-groep en selecteer vervolgens een regio.
1. Selecteer **Volgende: IP-adressen**.
1. Typ een IP-adres, meerdere IP-adressen of IP-adresbereiken.

   Er zijn twee manieren om IP-adressen in te voeren:
   - U kunt deze hand matig invoeren
   - U kunt ze uit een bestand importeren

   Als u wilt importeren uit een bestand, selecteert u **importeren uit een bestand**. U kunt uw bestand naar het vak slepen of bladeren naar **bestanden** selecteren. Indien nodig kunt u uw geüploade IP-adressen bekijken en bewerken.

   Wanneer u een IP-adres typt, wordt het door de portal gecontroleerd om te controleren of er overlappende, dubbele en opmaak problemen zijn.

1. Wanneer u klaar bent, selecteert u **controleren + maken**.
1. Selecteer **Maken**.

## <a name="create-an-ip-group---azure-powershell"></a>Een IP-groep maken-Azure PowerShell

In dit voor beeld wordt een IP-groep met een adres voorvoegsel en een IP-adres gemaakt met behulp van Azure PowerShell:

```azurepowershell
$ipGroup = @{
    Name              = 'ipGroup'
    ResourceGroupName = 'ipGroupRG'
    Location          = 'West US'
    IpAddress         = @('10.0.0.0/24', '192.168.1.10') 
}

New-AzIpGroup @ipGroup
```

## <a name="create-an-ip-group---azure-cli"></a>Een IP-groep maken-Azure CLI

In dit voor beeld wordt een IP-groep met een adres voorvoegsel en een IP-adres gemaakt met behulp van de Azure CLI:

```azurecli-interactive
az network ip-group create \
    --name ipGroup \ 
    --resource-group ipGroupRG \
    --location westus \
    --ip-addresses '10.0.0.0/24' '192.168.1.10'
```

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over IP-groepen](ip-groups.md)
