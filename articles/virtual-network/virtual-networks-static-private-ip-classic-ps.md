---
title: Privé IP-adressen voor virtuele machines (klassiek) configureren-Azure PowerShell | Microsoft Docs
description: Meer informatie over het configureren van privé-IP-adressen voor virtuele machines (klassiek) met behulp van Power shell.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
tags: azure-service-management
ms.assetid: 60c7b489-46ae-48af-a453-2b429a474afd
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2e798f81102017b4d34c4b1b219a9f23035b0359
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222833"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-powershell"></a>Privé-IP-adressen configureren voor een virtuele machine (klassiek) met behulp van Power shell

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Dit artikel is van toepassing op het klassieke implementatiemodel. U kunt ook [een statisch privé-IP-adres beheren in het Resource Manager-implementatie model](virtual-networks-static-private-ip-arm-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

In de onderstaande voor beelden van Power shell-opdrachten wordt verwacht dat er al een eenvoudige omgeving is gemaakt. Als u de opdrachten wilt uitvoeren zoals ze worden weer gegeven in dit document, bouwt u eerst de test omgeving op die wordt beschreven in [een VNet maken](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-netcfg-ps).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Controleren of een specifiek IP-adres beschikbaar is
Als u wilt controleren of het IP-adres *192.168.1.101* beschikbaar is in een VNet met de naam *TestVNet*, voert u de volgende Power shell-opdracht uit en controleert u de waarde voor *IsAvailable*:

```azurepowershell
Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 
```

Verwachte uitvoer:

```output
IsAvailable          : True
AvailableAddresses   : {}
OperationDescription : Test-AzureStaticVNetIP
OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
OperationStatus      : Succeeded
```

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Een statisch privé-IP-adres opgeven bij het maken van een VM
Het Power shell-script hieronder maakt een nieuwe Cloud service met de naam *TestService*, haalt een installatie kopie op uit Azure, maakt een virtuele machine met de naam *DNS01* in de nieuwe Cloud service met behulp van de opgehaalde installatie kopie, stelt de virtuele machine in een subnet met de naam front- *End* en stelt *192.168.1.7* in als een statisch privé-IP-adres voor de virtuele machine:

```azurepowershell
New-AzureService -ServiceName TestService -Location "Central US"
$image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
    Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
    Set-AzureSubnet –SubnetNames FrontEnd |
    Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
    New-AzureVM -ServiceName TestService –VNetName TestVNet
```

Verwachte uitvoer:

```output
WARNING: No deployment found in service: 'TestService'.
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  
```

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Informatie over statisch privé-IP-adres ophalen voor een virtuele machine
Als u de gegevens van het statische privé-IP-adres voor de virtuele machine wilt weer geven die zijn gemaakt met het bovenstaande script, voert u de volgende Power shell-opdracht uit en bekijkt u de waarden voor *IpAddress*:

```azurepowershell
Get-AzureVM -Name DNS01 -ServiceName TestService
```

Verwachte uitvoer:

```output
DeploymentName              : TestService
Name                        : DNS01
Label                       : 
VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
InstanceStatus              : Provisioning
IpAddress                   : 192.168.1.7
InstanceStateDetails        : Windows is preparing your computer for first use...
PowerState                  : Started
InstanceErrorCode           : 
InstanceFaultDomain         : 0
InstanceName                : DNS01
InstanceUpgradeDomain       : 0
InstanceSize                : Small
HostName                    : rsR2-797
AvailabilitySetName         : 
DNSName                     : http://testservice000.cloudapp.net/
Status                      : Provisioning
GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
PublicIPAddress             : 
PublicIPName                : 
NetworkInterfaces           : {}
ServiceName                 : TestService
OperationDescription        : Get-AzureVM
OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
OperationStatus             : OK
```

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Een statisch privé-IP-adres uit een virtuele machine verwijderen
Voer de volgende Power shell-opdracht uit om het statische privé-IP-adres dat aan de virtuele machine is toegevoegd, te verwijderen uit het bovenstaande script:

```azurepowershell
Get-AzureVM -ServiceName TestService -Name DNS01 |
    Remove-AzureStaticVNetIP |
    Update-AzureVM
```

Verwachte uitvoer:

```output
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded
```

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Een statisch privé-IP-adres toevoegen aan een bestaande virtuele machine
Voer de volgende opdracht uit om een statisch privé-IP-adres toe te voegen aan de virtuele machine die is gemaakt met behulp van het bovenstaande script:

```azurepowershell
Get-AzureVM -ServiceName TestService -Name DNS01 |
    Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
    Update-AzureVM
```

Verwachte uitvoer:

```output
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 
```

## <a name="set-ip-addresses-within-the-operating-system"></a>IP-adressen in het besturings systeem instellen

Het is raadzaam dat u het privé-IP-adres dat is toegewezen aan de virtuele machine van Azure niet statisch toewijst in het besturings systeem van een VM, tenzij dat nodig is. Als u het privé-IP-adres hand matig instelt in het besturings systeem, moet u ervoor zorgen dat het hetzelfde adres is als het privé-IP-adres dat is toegewezen aan de Azure VM, of u kunt de verbinding met de virtuele machine verliezen. U moet het openbare IP-adres dat is toegewezen aan een virtuele machine van Azure in het besturingssysteem van de virtuele machine nooit handmatig toewijzen.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [gereserveerde open bare IP-](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) adressen.
* Meer informatie over [open bare IP-adressen op exemplaar niveau (ILPIP)](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) .
* Raadpleeg de [gereserveerd IP rest-api's](/previous-versions/azure/reference/dn722420(v=azure.100)).