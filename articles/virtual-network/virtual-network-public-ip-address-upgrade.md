---
title: Openbaar IP-adres upgraden
titleSuffix: Azure Virtual Network
description: Werk openbare IP-adressen bij van Basic naar Standard.
services: virtual-network
documentationcenter: na
author: blehr
editor: ''
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2020
ms.author: blehr
ms.custom: references_regions
ms.openlocfilehash: 74df338fd888bd7f654ddfc2fc5f9dddf10e84ab
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598411"
---
# <a name="upgrade-public-ip-addresses"></a>Openbaar IP-adres upgraden

Openbare IP-adressen van Azure worden gemaakt met een SKU (Basic of Standard), waarmee aspecten van hun functionaliteit worden bepaald (inclusief toewijzingsmethode, functieondersteuning en aan welke resources ze kunnen worden gekoppeld). 

De volgende scenario's worden in dit artikel besproken:
* Een openbaar IP-adres van een Basic-SKU upgraden naar een openbaar IP-adres van een Standard-SKU (met behulp van PowerShell of CLI)
* Een klassieke Azure-Gereserveerd IP migreren naar een openbaar IP Azure Resource Manager basis-SKU

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>Openbaar IP-adres upgraden van Basic naar Standard-SKU

Als u een openbaar IP-adres wilt upgraden, [](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) mag het niet worden gekoppeld aan een resource (zie deze pagina voor meer informatie over het loskoppelen van openbare IP-adressen).

>[!IMPORTANT]
>Openbare IP's die zijn bijgewerkt van Basic naar Standard SKU hebben nog steeds geen gegarandeerde [beschikbaarheidszones.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)  Zorg ervoor dat u hiermee rekening houdt wanneer u kiest aan welke resources u het IP-adres wilt koppelen.

---
# <a name="basic-to-standard---powershell"></a>[**Basic naar Standard - PowerShell**](#tab/option-upgrade-powershell)

In het volgende voorbeeld wordt ervan uitgenomen dat u eerder [](./create-public-ip-powershell.md?tabs=option-create-public-ip-basic) een openbaar IP-adres van een Basic-SKU hebt gemaakt, met behulp van het voorbeeld op deze pagina met een openbaar BASIS-IP-adres **myBasicPublicIP** in **myResourceGroup.**

Als u het IP-adres wilt bijwerken, voert u de onderstaande opdrachten uit met behulp van PowerShell.  Als het IP-adres al statisch is toegewezen, kan die sectie worden overgeslagen.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'
$newsku = 'Standard'
$pubIP = Get-AzPublicIpAddress -name $name -ResourceGroupName $rg

## This section is only needed if the Basic IP is not already set to Static ##
$pubIP.PublicIpAllocationMethod = 'Static'
Set-AzPublicIpAddress -PublicIpAddress $pubIP

## This section is for conversion to Standard ##
$pubIP.Sku.Name = $newsku
Set-AzPublicIpAddress -PublicIpAddress $pubIP
```

# <a name="basic-to-standard---cli"></a>[**Basic naar Standard - CLI**](#tab/option-upgrade-cli)

In het volgende voorbeeld wordt ervan uitgenomen dat u eerder [](./create-public-ip-cli.md?tabs=option-create-public-ip-basic) een openbaar IP-adres van een Basic-SKU hebt gemaakt, met behulp van het voorbeeld op deze pagina met een openbaar BASIS-IP-adres **myBasicPublicIP** in **myResourceGroup.**

Als u het IP-adres wilt bijwerken, voert u de onderstaande opdrachten uit met behulp van de Azure CLI.  Als het IP-adres al statisch is toegewezen, kan die sectie worden overgeslagen.

```azurecli-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'

## This section is only needed if the Basic IP is not already set to Static ##
az network public-ip update \
--resource-group $rg \
--name $name \
--allocation-method Static 

## This section is for conversion to Standard ##
az network public-ip update \
--resource-group $rg \
--name $name \
--sku Standard
```
---

## <a name="upgrade-migrate-a-classic-reserved-ip-to-a-static-public-ip"></a>Een klassieke Gereserveerd IP upgraden (migreren) naar een statisch openbaar IP-adres

Als u wilt profiteren van de nieuwe mogelijkheden in Azure Resource Manager, kunt u een bestaand openbaar statisch IP-adres( gereserveerde IP-adressen genoemd) migreren van het klassieke model naar het moderne Azure Resource Manager model.  Het gemigreerde openbare IP-adres is een basic SKU-type.


---

# <a name="reserved-to-basic---powershell"></a>[**Gereserveerd voor Basic - PowerShell**](#tab/option-migrate-powershell)

In het volgende voorbeeld wordt ervan uitgenomen dat u eerder een klassieke Azure-Gereserveerd IP **myReservedIP** in **myResourceGroup hebt gemaakt.** Een andere vereiste voor migratie is ervoor te zorgen dat Azure Resource Manager abonnement is geregistreerd voor migratie. Dit wordt gedetailleerd behandeld in stap 3 en 4 [van deze pagina.](../virtual-machines/migration-classic-resource-manager-ps.md)

Als u de Gereserveerd IP, voert u de onderstaande opdrachten uit met behulp van PowerShell.  Als het IP-adres niet is gekoppeld aan een service (hieronder ziet u een service met de naam **myService),** kan die stap worden overgeslagen.

```azurepowershell-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
Met de vorige opdracht worden eventuele waarschuwingen en fouten weergegeven die de migratie blokkeren. Als de validatie is geslaagd, kunt u de volgende stappen uitvoeren om de migratie voor te bereiden en door te voeren:
```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
Er wordt een nieuwe resourcegroep in Azure Resource Manager gemaakt met behulp van de naam van de gemigreerde Gereserveerd IP (in het bovenstaande voorbeeld is dit de resourcegroep **myReservedIP-Migrated).**

# <a name="reserved-to-basic---cli"></a>[**Gereserveerd voor Basic - CLI**](#tab/option-migrate-cli)

In het volgende voorbeeld wordt ervan uitgenomen dat u eerder een klassieke Azure-Gereserveerd IP **myReservedIP** in **myResourceGroup hebt gemaakt.** Een andere vereiste voor migratie is ervoor te zorgen dat Azure Resource Manager abonnement is geregistreerd voor migratie. Dit wordt gedetailleerd behandeld in stap 3 en 4 [van deze pagina.](../virtual-machines/migration-classic-resource-manager-cli.md)

Voor het migreren van de Gereserveerd IP voert u de onderstaande opdrachten uit met behulp van de Azure CLI.  Als het IP-adres niet is gekoppeld aan een service (hieronder ziet u een service met de naam **myService** en implementatie **myDeployment**), die stap kan worden overgeslagen.

```azurecli-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$service = 'myService'
$deployment = 'myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```
Met de vorige opdracht worden eventuele waarschuwingen en fouten weergegeven die de migratie blokkeren. Als de validatie is geslaagd, kunt u de volgende stappen uitvoeren om de migratie voor te bereiden en door te voeren:
```azurecli-interactive
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
Er wordt een nieuwe resourcegroep in Azure Resource Manager gemaakt met behulp van de naam van de gemigreerde Gereserveerd IP (in het bovenstaande voorbeeld is dit de resourcegroep **myReservedIP-Migrated).**

---

## <a name="limitations"></a>Beperkingen

* Als u een basic openbaar IP-adres wilt upgraden, kan het niet worden gekoppeld aan een Azure-resource.  Raadpleeg deze [pagina voor](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) meer informatie over het loskoppelen van openbare IP's.  Op dezelfde manier kan het niet worden gekoppeld aan een cloudservice om Gereserveerd IP te migreren.  Raadpleeg deze [pagina voor](./remove-public-ip-address-vm.md) meer informatie over het loskoppelen van gereserveerde IP's.  
* Openbare IP's die zijn bijgewerkt van Basic naar Standard SKU hebben nog steeds geen [gegarandeerde beschikbaarheidszones.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)  Zorg ervoor dat u hiermee rekening houdt wanneer u kiest aan welke resources u het IP-adres wilt koppelen.
* U kunt niet downgraden van Standard naar Basic.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [openbare IP-adressen](./public-ip-addresses.md#public-ip-addresses) in Azure, waaronder het verschil tussen de SKU-typen en instellingen [voor openbare IP-adressen.](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Meer informatie over het [upgraden van openbare Load Balancers van Azure van Basic naar Standard.](../load-balancer/upgrade-basic-standard.md)
- Informatie [over klassieke gereserveerde Azure-IP's](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) [en de migratie van klassieke resources naar Azure Resource Manager.](../virtual-machines/migration-classic-resource-manager-overview.md)
