---
title: Een bastion-host maken met behulp van Azure CLI | Azure-Bastion
description: In dit artikel leest u hoe u een bastion-host maakt en verwijdert
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/14/2020
ms.author: cherylmc
ms.openlocfilehash: 8654b557de8242910c665596ca8bfd29fe86d1ff
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106578825"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Een Azure Bastion-host maken met behulp van Azure CLI

In dit artikel wordt beschreven hoe u een Azure bastion-host maakt met behulp van Azure CLI. Zodra u de Azure Bastion-service in uw virtuele netwerk hebt ingericht, is de naadloze RDP/SSH-ervaring beschikbaar voor alle virtuele machines in hetzelfde virtuele netwerk. De Azure Bastion-implementatie vindt plaats per virtueel netwerk, niet per abonnement/account of virtuele machine.

U kunt desgewenst een Azure bastion-host maken met behulp van de [Azure Portal](./tutorial-create-host-portal.md), of [Azure PowerShell](bastion-create-host-powershell.md)gebruiken.

## <a name="prerequisites"></a>Vereisten

Controleer of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [Cloud Shell CLI](../../includes/vpn-gateway-cloud-shell-cli.md)]

 >[!NOTE]
 >Het gebruik van Azure Bastion met Azure Privé-DNS zones wordt momenteel niet ondersteund. Voordat u begint, moet u ervoor zorgen dat het virtuele netwerk waar u de Bastion-resource wilt implementeren, niet is gekoppeld aan een privé-DNS-zone.
 >

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Een Bastion-host maken

Deze sectie helpt u bij het maken van een nieuwe Azure Bastion-resource met behulp van Azure CLI.

> [!NOTE]
> Zoals in de voor beelden wordt weer gegeven, gebruikt `--location` u de para meter met `--resource-group` voor elke opdracht om ervoor te zorgen dat de resources samen worden geïmplementeerd.

1. Maak een virtueel netwerk en een Azure Bastion-subnet. U moet het Azure Bastion-subnet maken met de naam waarde **AzureBastionSubnet**. Deze waarde geeft Azure aan op welk subnet de Bastion-resources moeten worden geïmplementeerd. Dit wijkt af van een gatewaysubnet. U moet een subnet van Mini maal/27 of groter subnet (/27,/26, enzovoort) gebruiken. Maak de **AzureBastionSubnet** zonder route tabellen of delegaties. Als u netwerk beveiligings groepen op de **AzureBastionSubnet** gebruikt, raadpleegt u het artikel [over het werken met nsg's](bastion-nsg.md) .

   ```azurecli-interactive
   az network vnet create --resource-group MyResourceGroup --name MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet --subnet-prefix 10.0.0.0/24 --location northeurope
   ```

2. Maak een openbaar IP-adres voor Azure Bastion. De open bare IP is het open bare IP-adres van de Bastion-bron waarop RDP/SSH wordt gebruikt (via poort 443). Het openbare IP-adres moet zich in dezelfde regio bevinden als de Bastion-resource die u aan het maken bent.

   ```azurecli-interactive
   az network public-ip create --resource-group MyResourceGroup --name MyIp --sku Standard --location northeurope
   ```

3. Maak een nieuwe Azure Bastion-resource in de AzureBastionSubnet van uw virtuele netwerk. Het duurt ongeveer vijf minuten voordat de Bastion-resource wordt gemaakt en geïmplementeerd.

   ```azurecli-interactive
   az network bastion create --name MyBastion --public-ip-address MyIp --resource-group MyResourceGroup --vnet-name MyVnet --location northeurope
   ```

## <a name="next-steps"></a>Volgende stappen

* Lees de [Veelgestelde vragen over Bastion](bastion-faq.md) voor meer informatie.
* Zie [Werken met NSG's](bastion-nsg.md) als u netwerkbeveiligingsgroepen wilt gebruiken met het Azure Bastion-subnet.
