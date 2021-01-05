---
title: Openbaar IP-adres upgraden
titleSuffix: Azure Virtual Network
description: Voer een upgrade uit van de open bare IP-adressen van Basic naar Standard.
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
ms.openlocfilehash: 3e2905019244279129528c177a76291cb7d75e11
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825778"
---
# <a name="upgrade-public-ip-addresses"></a>Openbaar IP-adres upgraden

Open bare IP-adressen van Azure worden gemaakt met een SKU: Basic of Standard, waarmee de aspecten van hun functionaliteit worden bepaald (inclusief toewijzings methode, gebruik in verschillende beschikbaarheids zones en aan welke resources ze kunnen worden gekoppeld). 

De volgende scenario's worden in dit artikel gecontroleerd:
* Een upgrade uitvoeren van een basis-SKU openbaar IP-adres naar een standaard-SKU openbaar IP-adres (met behulp van portal, Power shell of CLI)
* Een klassieke Azure-Gereserveerd IP migreren naar een Azure Resource Manager Basic SKU open bare IP

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>Het open bare IP-adres van de Basic-naar de standaard-SKU bijwerken

Als u een upgrade van een openbaar IP-adres wilt uitvoeren, moet dit niet aan een resource zijn gekoppeld (Zie [Deze pagina](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#view-modify-settings-for-or-delete-a-public-ip-address) voor meer informatie over het ontkoppelen van open bare ip's).

>[!IMPORTANT]
>Open bare Ip's die zijn bijgewerkt van Basic naar Standard SKU, blijven geen [beschikbaarheids zones](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).  Dit betekent dat ze niet kunnen worden gekoppeld aan een Azure-resource die zone-redundant is of is gekoppeld aan een vooraf opgegeven zone in regio's waar deze wordt aangeboden.

---
# <a name="basic-to-standard---powershell"></a>[**Basic naar Standard-Power shell**](#tab/option-upgrade-powershell)

In het volgende voor beeld wordt ervan uitgegaan dat u een basis-SKU open bare IP-adres hebt gemaakt, met behulp van het voor beeld op [Deze pagina](https://docs.microsoft.com/azure/virtual-network/create-public-ip-powershell?tabs=option-create-public-ip-basic) met een open bare basis-IP- **myBasicPublicIP** in **myResourceGroup**.

Als u het IP-adres wilt bijwerken, voert u de onderstaande opdrachten uit met behulp van Power shell.  Opmerking Als het IP-adres al statisch is toegewezen, kan dat gedeelte worden overgeslagen.

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

# <a name="basic-to-standard---cli"></a>[**Basic naar Standard-CLI**](#tab/option-upgrade-cli)

In het volgende voor beeld wordt ervan uitgegaan dat u een basis-SKU open bare IP-adres hebt gemaakt, met behulp van het voor beeld op [Deze pagina](https://docs.microsoft.com/azure/virtual-network/create-public-ip-cli?tabs=option-create-public-ip-basic) met een open bare basis-IP- **myBasicPublicIP** in **myResourceGroup**.

Als u het IP-adres wilt bijwerken, voert u de onderstaande opdrachten uit met behulp van de Azure CLI.  Opmerking Als het IP-adres al statisch is toegewezen, kan dat gedeelte worden overgeslagen.

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

Als u wilt profiteren van de nieuwe mogelijkheden van Azure Resource Manager, kunt u bestaand openbaar statisch IP-adres (gereserveerde Ip's) migreren van het klassieke model naar het moderne Azure Resource Manager model.  Het gemigreerde open bare IP-adres is een basis-SKU-type.


---

# <a name="reserved-to-basic---powershell"></a>[**Gereserveerd voor Basic-Power shell**](#tab/option-migrate-powershell)

In het volgende voor beeld wordt ervan uitgegaan dat er eerder een klassieke Azure Gereserveerd IP **myReservedIP** in **myResourceGroup** is gemaakt. Een andere vereiste voor migratie is om ervoor te zorgen dat het Azure Resource Manager-abonnement is geregistreerd voor migratie. Dit wordt uitvoerig besproken op stap 3 en 4 van [Deze pagina](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-ps).

Als u de Gereserveerd IP wilt migreren, voert u de onderstaande opdrachten uit met behulp van Power shell.  Opmerking Als het IP-adres niet is gekoppeld aan een service (onder er bevindt zich een service met de naam **myService**), kan deze stap worden overgeslagen.

```azurepowershell-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
Met de vorige opdracht worden eventuele waarschuwingen en fouten die de migratie blok keren weer gegeven. Als de validatie is geslaagd, kunt u door gaan met de volgende stappen om de migratie voor te bereiden en door te voeren:
```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
Er wordt een nieuwe resource groep in Azure Resource Manager gemaakt met behulp van de naam van de gemigreerde Gereserveerd IP (in het bovenstaande voor beeld is de resource groep **myReservedIP-gemigreerd**).

# <a name="reserved-to-basic---cli"></a>[**Gereserveerd voor Basic-CLI**](#tab/option-migrate-cli)

In het volgende voor beeld wordt ervan uitgegaan dat er eerder een klassieke Azure Gereserveerd IP **myReservedIP** in **myResourceGroup** is gemaakt. Een andere vereiste voor migratie is om ervoor te zorgen dat het Azure Resource Manager-abonnement is geregistreerd voor migratie. Dit wordt uitvoerig besproken op stap 3 en 4 van [Deze pagina](https://docs.microsoft.com/azure/virtual-machines/linux/migration-classic-resource-manager-cli).

Als u de Gereserveerd IP wilt migreren, voert u de onderstaande opdrachten uit met behulp van de Azure CLI.  Opmerking Als het IP-adres niet is gekoppeld aan een service (onder een service met de naam **myService** en Deployment **myDeployment**), kan deze stap worden overgeslagen.

```azurecli-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$service = 'myService'
$deployment = 'myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```
Met de vorige opdracht worden eventuele waarschuwingen en fouten die de migratie blok keren weer gegeven. Als de validatie is geslaagd, kunt u door gaan met de volgende stappen om de migratie voor te bereiden en door te voeren:
```azurecli-interactive
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
Er wordt een nieuwe resource groep in Azure Resource Manager gemaakt met behulp van de naam van de gemigreerde Gereserveerd IP (in het bovenstaande voor beeld is de resource groep **myReservedIP-gemigreerd**).

---

## <a name="limitations"></a>Beperkingen

* Als u een standaard open bare IP-adres wilt bijwerken, kan deze niet worden gekoppeld aan een Azure-resource.  Raadpleeg [Deze pagina](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#view-modify-settings-for-or-delete-a-public-ip-address) voor meer informatie over het ontkoppelen van open bare ip's.  En als u een Gereserveerd IP wilt migreren, kan het niet worden gekoppeld aan een Cloud service.  Raadpleeg [Deze pagina](https://docs.microsoft.com/azure/virtual-network/remove-public-ip-address-vm) voor meer informatie over het ontkoppelen van gereserveerde ip's.  
* Open bare Ip's die zijn bijgewerkt van Basic naar Standard SKU blijven geen [beschikbaarheids zones](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones) en kunnen daarom niet worden gekoppeld aan een Azure-resource die een zone-redundante of zonegebonden is.  Opmerking: dit is alleen van toepassing op regio's die beschikbaarheids zones bieden.
* U kunt niet overdowngradeen van Standard naar Basic.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [open bare IP-adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in azure, met inbegrip van het verschil tussen de typen sku's en [open bare IP-adres instellingen](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Meer informatie over het [upgraden van open bare Azure-load balancers van Basic naar Standard](https://docs.microsoft.com/azure/load-balancer/upgrade-basic-standard).
- Meer informatie over [klassieke gereserveerde ip's in azure](https://docs.microsoft.com/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) en de [migratie van klassieke resources naar Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).
