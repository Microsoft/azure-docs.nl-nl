---
title: Routerings voorkeur configureren voor een openbaar IP-adres-Azure PowerShell
titlesuffix: Azure Virtual Network
description: Meer informatie over het configureren van routerings voorkeur voor een openbaar IP-adres met behulp van Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 5fac791247dbb785e087ac21f3fd5532064c6c8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667892"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-powershell"></a>Routerings voorkeur configureren voor een openbaar IP-adres met behulp van Azure PowerShell

In dit artikel wordt beschreven hoe u routerings voorkeur configureert via ISP Network (**Internet** optie) voor een openbaar IP-adres met behulp van Azure PowerShell. Nadat u het open bare IP-adres hebt gemaakt, kunt u dit koppelen aan de volgende Azure-resources voor binnenkomend en uitgaand verkeer naar Internet:

* Virtuele machine
* Schaalset voor virtuele machines
* Azure Kubernetes Service (AKS)
* Internet gerichte load balancer
* Application Gateway
* Azure Firewall

De routerings voorkeur voor openbaar IP-adres is standaard ingesteld op het micro soft Global Network voor alle Azure-Services en kan worden gekoppeld aan elke Azure-service.

Als u nog geen abonnement op Azure hebt, maak dan nu een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
Als u Power shell lokaal wilt installeren en gebruiken, moet u voor dit artikel gebruikmaken van de Azure PowerShell module versie 6.9.0 of hoger. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). In dit voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *US - oost*:

```azurepowershell
$rg = New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-public-ip-with-internet-routing-preference"></a>Een openbaar IP-adres met Internet routering voorkeur maken

Met de volgende opdracht maakt u een nieuw openbaar IP-adres met een routerings voorkeur voor het *Internet* in de Azure-regio *VS-Oost* :

```azurepowershell
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

U kunt het hierboven gemaakte open bare IP-adres koppelen aan een virtuele [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -of [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -machine. Gebruik de CLI-sectie op de pagina zelf studie: [een openbaar IP-adres koppelen aan een virtuele machine](associate-public-ip-address-vm.md#azure-cli) om de open bare IP aan uw VM te koppelen. U kunt ook het open bare IP-adres koppelen dat is gemaakt met met een [Azure Load Balancer](../load-balancer/load-balancer-overview.md)door het toe te wijzen aan de Load Balancer front- **End** -configuratie. Het openbare IP-adres doet dienst als een virtueel IP-adres (VIP) met taakverdeling.

## <a name="clean-up-resources"></a>Resources opschonen

Als u dit niet meer nodig hebt, kunt u de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resource groep, de VM en alle gerelateerde resources te verwijderen.

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [routerings voorkeur in open bare IP-adressen](routing-preference-overview.md).
- [Configureer routerings voorkeur voor een virtuele machine met behulp van de Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md).
