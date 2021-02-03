---
title: Een open bare IP-Azure PowerShell maken
description: Meer informatie over het maken van een openbaar IP-adres met behulp van Azure PowerShell
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: ff768bceaba57c119aa88d5d4d99b11608917695
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492022"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-powershell"></a>Snelstartgids: een openbaar IP-adres maken met behulp van Azure PowerShell

In dit artikel wordt beschreven hoe u een open bare IP-adres bron maakt met behulp van Azure PowerShell. Zie [open bare IP-adressen](./public-ip-addresses.md)voor meer informatie over de resources waaraan dit kan worden gekoppeld, het verschil tussen de basis-en standaard-SKU en andere gerelateerde informatie.  In dit voor beeld richten we zich alleen op IPv4-adressen. Zie voor meer informatie over IPv6-adressen [IPv6 voor Azure VNet](./ipv6-overview.md).

## <a name="prerequisites"></a>Vereisten

- Azure PowerShell lokaal geïnstalleerd of Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor dit artikel versie 5.4.1 of hoger van de Azure PowerShell-module vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resource groep met [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) met de naam **myResourceGroup** op de locatie **eastus2** .

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'

New-AzResourceGroup -Name $rg -Location $loc
```
## <a name="create-public-ip"></a>Openbaar IP-adres maken

---
# <a name="standard-sku---using-zones"></a>[**Standaard-SKU-zones gebruiken**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>De volgende opdracht werkt voor AZ. network module versie 4.5.0 of hoger.  Raadpleeg de [PowerShellGet-documentatie](https://docs.microsoft.com/powershell/module/powershellget/?view=powershell-7.1)voor meer informatie over de Power shell-modules die momenteel worden gebruikt.

Gebruik [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) voor het maken van een standaard zone-redundant openbaar IP-adres met de naam **myStandardZRPublicIP** in **myResourceGroup**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZRPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 1,2,3

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```
> [!IMPORTANT]
> Voor AZ. Network-modules ouder dan 4.5.0 voert u de bovenstaande opdracht uit zonder een zone parameter op te geven om een zone-redundant IP-adres te maken. 
>

Gebruik de volgende opdracht om een openbaar IP-adres voor de standaard zonegebonden te maken in Zone 2 met de naam **myStandardZonalPublicIP** in **myResourceGroup**:

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZonalPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 2

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```

Houd er rekening mee dat de bovenstaande opties voor zones alleen geldige selecties in regio's met [Beschikbaarheidszones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)zijn.

# <a name="standard-sku---no-zones"></a>[**Standaard-SKU-geen zones**](#tab/option-create-public-ip-standard)

>[!NOTE]
>De volgende opdracht werkt voor AZ. network module versie 4.5.0 of hoger.  Raadpleeg de [PowerShellGet-documentatie](https://docs.microsoft.com/powershell/module/powershellget/?view=powershell-7.1)voor meer informatie over de Power shell-modules die momenteel worden gebruikt.

Gebruik [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) voor het maken van een standaard openbaar IP-adres als een niet-zonegebonden resource met de naam **myStandardPublicIP** in **myResourceGroup**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardPublicIP'
$sku = 'Standard'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```

Deze selectie is geldig in alle regio's en is de standaard selectie voor standaard open bare IP-adressen in regio's zonder [Beschikbaarheidszones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="basic-sku"></a>[**Basis-SKU**](#tab/option-create-public-ip-basic)

Gebruik [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) om een eenvoudig, statisch openbaar IP-adres te maken met de naam **myBasicPublicIP** in **myResourceGroup**.  Algemene open bare Ip's hebben niet het concept van beschikbaarheids zones.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myBasicPublicIP'
$sku = 'Basic'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```
Als het IP-adres acceptabel is om na verloop van tijd te veranderen, kan de **dynamische** IP-toewijzing worden geselecteerd door de AllocationMethod te wijzigen in dynamisch.

---

## <a name="additional-information"></a>Aanvullende informatie 

Zie [open bare IP-adressen beheren](./virtual-network-public-ip-address.md#create-a-public-ip-address)voor meer informatie over de afzonderlijke variabelen die hierboven worden vermeld.

## <a name="next-steps"></a>Volgende stappen
- Een [openbaar IP-adres koppelen aan een virtuele machine](./associate-public-ip-address-vm.md#azure-portal)
- Meer informatie over [open bare IP-adressen](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Meer informatie over alle [open bare IP-adres instellingen](virtual-network-public-ip-address.md#create-a-public-ip-address).
