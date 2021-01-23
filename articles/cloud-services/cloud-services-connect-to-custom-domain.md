---
title: Een Cloud service (klassiek) verbinden met een aangepaste domein controller | Microsoft Docs
description: Meer informatie over hoe u uw web-en werk rollen verbindt met een aangepast AD-domein met behulp van Power shell en AD-domein extensie
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 8c2c8377944caa7ad28f6b379531e6d5bf44c9e7
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742502"
---
# <a name="connecting-azure-cloud-services-classic-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Azure Cloud Services-rollen (klassiek) verbinden met een aangepaste AD-domein controller die wordt gehost in azure

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

We gaan eerst een Virtual Network (VNet) in azure instellen. We gaan vervolgens een Active Directory-domein controller (gehost op een virtuele machine van Azure) toevoegen aan het VNet. Daarna gaan we bestaande Cloud service rollen toevoegen aan het vooraf gemaakte VNet en vervolgens verbinden met de domein controller.

Voordat we aan de slag gaan, moet u zich op een paar dingen herinneren:

1. In deze zelf studie wordt Power shell gebruikt. Zorg er dus voor dat Azure PowerShell is geïnstalleerd en gereed is voor gebruik. Zie [Azure PowerShell installeren en configureren](/powershell/azure/)voor hulp bij het instellen van Azure PowerShell.
2. De AD-domein controller en Web/Worker-rollen moeten zich in het VNet bevindt.

Volg deze stapsgewijze hand leiding en als u problemen ondervindt, kunt u aan het einde van het artikel een opmerking ontvangen. Iemand krijgt een antwoord (Ja, we lezen opmerkingen).

Het netwerk waarnaar wordt verwezen door de Cloud service moet een **klassiek virtueel netwerk** zijn.

## <a name="create-a-virtual-network"></a>Een Virtual Network maken
U kunt een Virtual Network in azure maken met behulp van de Azure Portal of Power shell. Voor deze zelf studie wordt Power shell gebruikt. Zie [een virtueel netwerk maken](../virtual-network/quick-create-portal.md)voor het maken van een virtueel netwerk met behulp van de Azure Portal. In het artikel wordt beschreven hoe u een virtueel netwerk (Resource Manager) maakt, maar u moet een virtueel netwerk (klassiek) maken voor Cloud Services. Als u dit wilt doen, selecteert u in de Portal de optie **een resource maken**, type *virtueel netwerk* in het **zoekvak** en drukt u vervolgens op **Enter**. In de zoek resultaten, onder **Alles**, selecteert u **virtueel netwerk**. Selecteer onder **een implementatie model selecteren de** optie **klassiek** en selecteer vervolgens **maken**. U kunt vervolgens de stappen in het artikel volgen.

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken
Nadat u de Virtual Network hebt ingesteld, moet u een AD-domein controller maken. Voor deze zelf studie wordt een AD-domein controller ingesteld op een virtuele machine van Azure.

Hiervoor maakt u een virtuele machine via Power shell met behulp van de volgende opdrachten:

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Uw virtuele machine promo veren naar een domein controller
Als u de virtuele machine wilt configureren als een AD-domein controller, moet u zich aanmelden bij de VM en configureren.

Als u zich wilt aanmelden bij de virtuele machine, kunt u het RDP-bestand ophalen via Power shell, met behulp van de volgende opdrachten:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Zodra u bent aangemeld bij de VM, moet u de virtuele machine instellen als een AD-domein controller door de stapsgewijze hand leiding voor het instellen van [de AD-domein controller van uw klanten](https://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx)in te stellen.

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Voeg uw Cloud service toe aan de Virtual Network
Vervolgens moet u uw Cloud service-implementatie toevoegen aan het nieuwe VNet. Als u dit wilt doen, wijzigt u uw Cloud service cscfg door de relevante secties toe te voegen aan uw cscfg met Visual Studio of de editor van uw keuze.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

Bouw vervolgens uw Cloud Services-project en implementeer het in Azure. Zie [een Cloud service maken en implementeren](cloud-services-how-to-create-deploy-portal.md) voor hulp bij het implementeren van uw cloud services-pakket in azure

## <a name="connect-your-webworker-roles-to-the-domain"></a>Uw web-en werk rollen koppelen aan het domein
Als uw Cloud service project is geïmplementeerd op Azure, verbindt u uw rolinstanties met de AD-domein extensie met het aangepaste AD-domein. Als u de AD-domein extensie wilt toevoegen aan uw bestaande implementatie van Cloud Services en lid wilt worden van het aangepaste domein, voert u de volgende opdrachten uit in Power shell:

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

Dat is alles.

Uw Cloud Services moeten worden toegevoegd aan uw aangepaste domein controller. Als u meer wilt weten over de verschillende beschik bare opties voor het configureren van de AD-domein extensie, gebruikt u de Power shell-Help. Enkele voor beelden:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```



