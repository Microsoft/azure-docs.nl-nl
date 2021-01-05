---
title: Azure VMware-oplossing door CloudSimple-werk belasting Vm's migreren naar Privécloud
description: Hierin wordt beschreven hoe u virtuele machines migreert van on-premises vCenter naar CloudSimple Private Cloud vCenter
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 851305775d14ed371440434b2fbe9334ec0a21b9
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899317"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>Werkbelasting-Vm's migreren van on-premises vCenter naar een Privécloud-vCenter-omgeving

Er zijn verschillende opties beschikbaar om Vm's van een on-premises Data Center naar uw CloudSimple-privécloud te migreren.  De Privécloud biedt systeem eigen toegang tot VMware vCenter en hulpprogram ma's die door VMware worden ondersteund, kunnen worden gebruikt voor de migratie van werk belastingen. In dit artikel worden enkele van de vCenter-migratie opties beschreven.

## <a name="prerequisites"></a>Vereisten

De migratie van Vm's en gegevens van uw on-premises Data Center vereist een netwerk verbinding van het Data Center naar uw Privécloud.  Gebruik een van de volgende methoden om netwerk verbinding tot stand te brengen:

* [Site-naar-site-VPN-verbinding](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) tussen uw on-premises omgeving en uw privécloud.
* ExpressRoute Global Reach verbinding tussen uw on-premises ExpressRoute-circuit en een CloudSimple ExpressRoute-circuit.

Het netwerkpad van uw on-premises vCenter-omgeving naar uw Privécloud moet beschikbaar zijn voor de migratie van Vm's met behulp van vMotion.  Het netwerk van vMotion in uw on-premises vCenter moet routerings vaardigheden hebben.  Controleer of uw firewall al het verkeer van vMotion toestaat tussen uw on-premises vCenter en de vCenter van Privécloud. (In de Privécloud wordt route ring in het netwerk van vMotion standaard geconfigureerd.)

## <a name="migrate-isos-and-templates"></a>Iso's en sjablonen migreren

Gebruik Iso's-en VM-sjablonen om nieuwe virtuele machines in uw Privécloud te maken.  Als u de Iso's en sjablonen naar uw Privécloud wilt uploaden en deze beschikbaar wilt maken, gebruikt u de volgende methode.

1. Upload de ISO naar de vCenter van de Privécloud vanuit de vCenter-gebruikers interface.
2. [Een inhouds bibliotheek publiceren](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) op uw privécloud:

    1. Publiceer uw on-premises inhouds bibliotheek.
    2. Maak een nieuwe inhouds bibliotheek op de vCenter van de Privécloud.
    3. Abonneer u op de gepubliceerde on-premises inhouds bibliotheek.
    4. Synchroniseer de inhouds bibliotheek voor toegang tot geabonneerde inhoud.

## <a name="migrate-vms-using-powercli"></a>Vm's migreren met behulp van PowerCLI

Als u Vm's van de on-premises vCenter wilt migreren naar de Privécloud, gebruikt u VMware PowerCLI of het hulp programma migratie van cross vCenter-workloads dat beschikbaar is vanuit VMware Labs.  In het volgende voorbeeld script ziet u de PowerCLI-migratie-opdrachten.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> Als u de namen van de doel-vCenter-Server en ESXi-hosts wilt gebruiken, configureert u DNS-door sturen van on-premises naar uw Privécloud.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>Vm's migreren met NSX Layer 2 VPN

Met deze optie wordt Livemigratie van werk belastingen van uw on-premises VMware-omgeving naar de Privécloud in azure mogelijk.  Met dit uitgerekte Layer 2-netwerk is het subnet van on-premises beschikbaar in de Privécloud.  Na de migratie is nieuwe IP-adres toewijzing niet vereist voor de Vm's.

[Werk belastingen migreren met laag 2 uitgerekte netwerken](migration-layer-2-vpn.md) hier wordt beschreven hoe u een laag 2-VPN gebruikt om een laag 2-netwerk uit te breiden van uw on-premises omgeving naar uw privécloud.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Vm's migreren met back-ups en hulpprogram ma's voor herstel na nood gevallen

Migratie van Vm's naar Privécloud kan worden uitgevoerd met back-up-en herstel Programma's en hulpprogram ma's voor herstel na nood gevallen.  Gebruik de Privécloud als doel voor het terugzetten van back-ups die zijn gemaakt met een hulp programma van derden.  De Privécloud kan ook worden gebruikt als een doel voor nood herstel met behulp van VMware-beveiligings groep of een hulp programma van derden.

Zie de volgende onderwerpen voor meer informatie over het gebruik van deze hulpprogram ma's:

* [Back-ups maken van virtuele workload-machines in CloudSimple Privécloud met Veeam B&R](backup-workloads-veeam.md)
* [CloudSimple Private Cloud instellen als een nood herstel site voor on-premises VMware-workloads](disaster-recovery-zerto.md)
