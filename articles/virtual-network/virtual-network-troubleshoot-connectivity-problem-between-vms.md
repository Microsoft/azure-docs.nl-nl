---
title: Connectiviteits problemen tussen virtuele Azure-machines oplossen | Microsoft Docs
description: Meer informatie over het oplossen van problemen met de connectiviteit tussen Azure-Vm's en het oplossen van problemen die zich kunnen voordoen.
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 5e602f8bbdf6ad92acacac6688326a555a0420b6
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105042863"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Problemen met de connectiviteit tussen virtuele Azure-machines oplossen

U kunt connectiviteits problemen ondervinden tussen virtuele machines van Azure (Vm's). Dit artikel bevat stappen voor probleem oplossing om dit probleem op te lossen. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Symptoom

Een Azure-VM kan geen verbinding maken met een andere virtuele machine van Azure.

## <a name="troubleshooting-guidance"></a>Hulp bij het oplossen van problemen 

1. [Controleren of de NIC onjuist is geconfigureerd](#step-1-check-whether-nic-is-misconfigured)
2. [Controleer of netwerk verkeer wordt geblokkeerd door NSG of UDR](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Controleer of het netwerk verkeer wordt geblokkeerd door de VM-firewall](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Controleren of de VM-app of service luistert op de poort](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Controleren of het probleem wordt veroorzaakt door SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Controleren of het verkeer wordt geblokkeerd door Acl's voor de klassieke VM](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Controleer of het eind punt is gemaakt voor de klassieke virtuele machine](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Probeer verbinding te maken met een VM-netwerk share](#step-8-try-to-connect-to-a-vm-network-share)
9. [Inter-Vnet connectiviteit controleren](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

Volg deze stappen om het probleem op te lossen. Nadat u elke stap hebt voltooid, controleert u of het probleem is opgelost. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Stap 1: controleren of de NIC onjuist is geconfigureerd

Volg de stappen in de [netwerk interface opnieuw instellen voor Azure Windows VM](/troubleshoot/azure/virtual-machines/reset-network-interface). 

Als het probleem optreedt nadat u de netwerk interface (NIC) hebt gewijzigd, voert u de volgende stappen uit:

**Vm's met meerdere NIC'S**

1. Voeg een NIC toe.
2. Los de problemen in de beschadigde NIC op of verwijder de beschadigde NIC.  Voeg de NIC vervolgens opnieuw toe.

Zie [netwerk interfaces toevoegen aan of verwijderen uit virtuele machines](virtual-network-network-interface-vm.md)voor meer informatie.

**Virtuele machine met één NIC** 

- [Windows-VM opnieuw implementeren](/troubleshoot/azure/virtual-machines/redeploy-to-new-node-windows)
- [Linux-VM opnieuw implementeren](/troubleshoot/azure/virtual-machines/redeploy-to-new-node-linux)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Stap 2: controleren of het netwerk verkeer wordt geblokkeerd door NSG of UDR

Gebruik [Network Watcher IP-stroom controle](../network-watcher/network-watcher-ip-flow-verify-overview.md) en [NSG stroom logboek registratie](../network-watcher/network-watcher-nsg-flow-logging-overview.md) om te bepalen of er een netwerk beveiligings groep (NSG) of User-Defined route (UDR) is die de verkeers stroom verstoort.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Stap 3: controleren of het netwerk verkeer wordt geblokkeerd door de VM-firewall

Schakel de firewall uit en test vervolgens het resultaat. Als het probleem is opgelost, controleert u de firewall instellingen en schakelt u de firewall opnieuw in.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Stap 4: controleren of de VM-app of service luistert op de poort

U kunt een van de volgende methoden gebruiken om te controleren of de VM-app of service luistert op de poort.

- Voer de volgende opdrachten uit om te controleren of de server op die poort luistert.

**Windows-VM**

```console
netstat –ano
```

**Linux-VM**

```console
netstat -l
```

- Voer de **Telnet** -opdracht op de virtuele machine zelf uit om de poort te testen. Als de test mislukt, is de toepassing of service niet geconfigureerd om op die poort te Luis teren.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Stap 5: controleren of het probleem wordt veroorzaakt door SNAT

In sommige gevallen wordt de virtuele machine achter een oplossing voor taak verdeling geplaatst die een afhankelijkheid heeft van resources buiten Azure. Als u in deze scenario's problemen ondervindt met een terugkerende verbinding, kan het probleem worden veroorzaakt door een [SNAT-poort uitgeput](../load-balancer/load-balancer-outbound-connections.md). Om het probleem op te lossen, maakt u een VIP (of ILPIP voor klassiek) voor elke VM die zich achter de load balancer bevindt en die u met NSG of ACL kunt beveiligen. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Stap 6: controleren of het verkeer wordt geblokkeerd door Acl's voor de klassieke VM

Een toegangs beheer lijst (ACL) biedt de mogelijkheid om selectief verkeer toe te staan of te weigeren voor een eind punt van een virtuele machine. Zie [Manage the ACL to a endpoint](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint)(Engelstalig) voor meer informatie.

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Stap 7: controleren of het eind punt is gemaakt voor de klassieke VM

Alle Vm's die u in azure maakt met behulp van het klassieke implementatie model kunnen automatisch communiceren via een particulier netwerk kanaal met andere virtuele machines in dezelfde Cloud service of een virtueel netwerk. Computers in andere virtuele netwerken vereisen echter eind punten om het inkomende netwerk verkeer naar een virtuele machine te sturen. Zie [eind punten instellen](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints)voor meer informatie.

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Stap 8: verbinding maken met een VM-netwerk share

Als u geen verbinding kunt maken met een VM-netwerk share, kan het probleem worden veroorzaakt door niet-beschik bare Nic's in de VM. Als u de niet-beschik bare Nic's wilt verwijderen, raadpleegt [u de niet-beschik bare nic's verwijderen](/troubleshoot/azure/virtual-machines/reset-network-interface#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>Stap 9: Inter-Vnet connectiviteit controleren

Gebruik [Network Watcher IP-stroom controle](../network-watcher/network-watcher-ip-flow-verify-overview.md) en [NSG flow logboek registratie](../network-watcher/network-watcher-nsg-flow-logging-overview.md) om te bepalen of er een NSG of UDR is die de verkeers stroom verstoort. U kunt uw Inter-Vnet configuratie [hier](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections)ook controleren.

### <a name="need-help-contact-support"></a>Hebt u hulp nodig? Neem contact op met ondersteuning.
Als u nog steeds hulp nodig hebt, neemt u [contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.