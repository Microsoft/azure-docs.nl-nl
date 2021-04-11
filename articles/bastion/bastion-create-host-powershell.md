---
title: Een bastion-host maken met behulp van Azure PowerShell | Microsoft Docs
description: In dit artikel leest u hoe u een Azure bastion-host maakt
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/14/2020
ms.author: cherylmc
ms.openlocfilehash: 8abd4c417181b46fbf4d5c139c157044b329ea2a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580149"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Een Azure Bastion-host maken met behulp van Azure PowerShell

In dit artikel wordt beschreven hoe u een Azure bastion-host maakt met behulp van Power shell. Zodra u de Azure Bastion-service in uw virtuele netwerk hebt ingericht, is de naadloze RDP/SSH-ervaring beschikbaar voor alle virtuele machines in hetzelfde virtuele netwerk. De Azure Bastion-implementatie vindt plaats per virtueel netwerk, niet per abonnement/account of virtuele machine.

U kunt desgewenst een Azure bastion-host maken met behulp van de [Azure Portal](./tutorial-create-host-portal.md).

## <a name="prerequisites"></a>Vereisten

Controleer of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

 >[!NOTE]
 >Het gebruik van Azure Bastion met Azure Privé-DNS zones wordt momenteel niet ondersteund. Voordat u begint, moet u ervoor zorgen dat het virtuele netwerk waar u de Bastion-resource wilt implementeren, niet is gekoppeld aan een privé-DNS-zone.
 >

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Een Bastion-host maken

Deze sectie helpt u bij het maken van een nieuwe Azure Bastion-resource met behulp van Azure PowerShell.

1. Maak een virtueel netwerk en een Azure Bastion-subnet. U moet het Azure Bastion-subnet maken met de naam waarde **AzureBastionSubnet**. Deze waarde geeft Azure aan op welk subnet de Bastion-resources moeten worden geïmplementeerd. Dit wijkt af van een gatewaysubnet. U moet een subnet van Mini maal/27 of groter subnet (/27,/26, enzovoort) gebruiken. Maak de **AzureBastionSubnet** zonder route tabellen of delegaties. Als u netwerk beveiligings groepen op de **AzureBastionSubnet** gebruikt, raadpleegt u het artikel [over het werken met nsg's](bastion-nsg.md) .

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Maak een openbaar IP-adres voor Azure Bastion. De open bare IP is het open bare IP-adres van de Bastion-bron waarop RDP/SSH wordt gebruikt (via poort 443). Het openbare IP-adres moet zich in dezelfde regio bevinden als de Bastion-resource die u aan het maken bent.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Maak een nieuwe Azure Bastion-resource in de AzureBastionSubnet van uw virtuele netwerk. Het duurt ongeveer vijf minuten voordat de Bastion-resource wordt gemaakt en geïmplementeerd.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Volgende stappen

* Lees de [Veelgestelde vragen over Bastion](bastion-faq.md) voor meer informatie.
* Zie [Werken met NSG's](bastion-nsg.md) als u netwerkbeveiligingsgroepen wilt gebruiken met het Azure Bastion-subnet.
