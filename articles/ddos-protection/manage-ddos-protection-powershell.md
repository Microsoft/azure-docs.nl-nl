---
title: Een Azure DDoS Protection Plan maken en configureren met behulp van Azure PowerShell
description: Meer informatie over het maken van een DDoS Protection-abonnement met behulp van Azure PowerShell
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 69f9b5a74566879ecf8f15f23e689ebb731da45a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97814139"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-powershell"></a>Snelstartgids: Azure DDoS Protection standaard maken en configureren met behulp van Azure PowerShell

Ga aan de slag met Azure DDoS Protection Standard met behulp van Azure PowerShell. 

In een DDoS-beschermings plan wordt een set virtuele netwerken gedefinieerd waarvoor DDoS-beschermings standaard is ingeschakeld, via abonnementen. U kunt voor uw organisatie één DDoS-beschermingsplan configureren en virtuele netwerken vanuit meerdere abonnementen aan hetzelfde plan koppelen. 

In deze Quick Start maakt u een DDoS-beschermings plan en koppelt u het aan een virtueel netwerk. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure PowerShell lokaal geïnstalleerd of Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-ddos-protection-plan"></a>Een DDoS Protection Plan maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. U kunt een bestaande resourcegroep gebruiken of een nieuwe maken.

Gebruik [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)om een resource groep te maken. In dit voor beeld noemen we de _MyResourceGroup_ van de resource groep en gebruiken we de locatie _VS-Oost_ :

```azurepowershell-interactive
New-AzResourceGroup -Name MyResourceGroup -Location "East US"
```

Maak nu een DDoS-beveiligings plan met de naam _MyDdosProtectionPlan_:

```azurepowershell-interactive
New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"
```

## <a name="enable-ddos-for-a-virtual-network"></a>DDoS inschakelen voor een virtueel netwerk

### <a name="enable-ddos-for-a-new-virtual-network"></a>DDoS inschakelen voor een nieuw virtueel netwerk

U kunt DDoS-beveiliging inschakelen bij het maken van een virtueel netwerk. In dit voor beeld noemen we onze virtuele netwerk _MyVnet_: 

```azurepowershell-interactive
New-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup -Location "East US" -AddressPrefix 10.0.0.0/16
```

### <a name="enable-ddos-for-an-existing-virtual-network"></a>DDoS inschakelen voor een bestaand virtueel netwerk

U kunt een bestaand virtueel netwerk koppelen bij het maken van een DDoS-beschermings plan:

```azurepowershell-interactive
# Creates the DDoS protection plan
$ddosProtectionPlan = New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"

# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = New-Object Microsoft.Azure.Commands.Network.Models.PSResourceId

# Update the properties and enable DDoS protection
$vnet.DdosProtectionPlan.Id = $ddosProtectionPlan.Id
$vnet.EnableDdosProtection = $true
$vnet | Set-AzVirtualNetwork
``` 

## <a name="validate-and-test"></a>Valideren en testen

Controleer eerst de details van uw DDoS-beveiligings plan:

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan
```

Controleer of de opdracht de juiste gegevens van uw DDoS-beveiligings plan retourneert.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt uw resources voor de volgende zelf studie blijven gebruiken. Als u deze niet meer nodig hebt, verwijdert u de resource groep _MyResourceGroup_ . Wanneer u de resource groep verwijdert, verwijdert u ook het DDoS-beschermings plan en alle gerelateerde resources. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

DDoS-beveiliging uitschakelen voor een virtueel netwerk: 

```azurepowershell-interactive
# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = $null
$vnet.EnableDdosProtection = $false
$vnet | Set-AzVirtualNetwork
```

Als u een DDoS-beschermings plan wilt verwijderen, moet u eerst alle virtuele netwerken loskoppelen.

## <a name="next-steps"></a>Volgende stappen

Ga door naar de zelfstudies voor meer informatie over het weergeven en configureren van telemetrie voor uw DDoS-beschermingsplan.

> [!div class="nextstepaction"]
> [DDoS-beschermingstelemetrie bekijken en configureren](telemetry.md)