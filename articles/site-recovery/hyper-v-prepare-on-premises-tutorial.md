---
title: Hyper-V-VM’s voorbereiden op herstel naar Azure na een noodgeval, met behulp van Azure Site Recovery
description: Leer hoe u on-premises Hyper-V-VM’s kunt voorbereiden op herstel naar Azure na een noodgeval, met behulp van Azure Site Recovery.
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.custom: MVC
ms.openlocfilehash: 8704dd17f0386254f65d1c0e783c5f8786b8ae98
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580312"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>On-premises Hyper-V-servers voorbereiden op herstel na noodgeval in Azure

In dit artikel wordt beschreven hoe u de on-premises Hyper-V-infrastructuur voorbereidt, wanneer u herstel naar Azure na een noodgeval wilt instellen voor Hyper-VM's met behulp van [Azure Site Recovery](site-recovery-overview.md).


Dit is de tweede zelfstudie in een reeks waarin u ziet hoe u herstel naar Azure na een noodgeval kunt instellen voor on-premises Hyper-V-VM’s. In de eerste zelfstudie hebben we de [Azure-onderdelen ingesteld](tutorial-prepare-azure.md) die nodig zijn voor herstel na noodgeval voor Hyper-V.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Bekijk de vereisten voor Hyper-V en VMM als uw Hyper-V-hosts worden beheerd met System Center VMM.
> * Bereid VMM voor, indien van toepassing.
> * Controleer de internettoegang tot Azure-locaties.
> * Bereid VM's voor, zodat u na een failover naar Azure toegang tot deze VM’s hebt.

> [!NOTE]
> In zelfstudies ziet u steeds het eenvoudigste implementatiepad voor een scenario. Waar mogelijk wordt gebruikgemaakt van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Voor gedetailleerde instructies bekijkt u het artikel in de instructiesectie van de Site Recovery-inhoudsopgave.

## <a name="before-you-start"></a>Voordat u begint

Zorg ervoor dat u Azure hebt voorbereid zoals beschreven in de [eerste zelfstudie van deze reeks](tutorial-prepare-azure.md).

## <a name="review-requirements-and-prerequisites"></a>Vereisten beoordelen

Controleer of de Hyper-V-hosts en VM's voldoen aan de vereisten.

1. [Controleer](hyper-v-azure-support-matrix.md#on-premises-servers) de vereisten voor on-premises servers.
2. [Controleer de vereisten](hyper-v-azure-support-matrix.md#replicated-vms) voor Hyper-V-VM’s die u wilt repliceren naar Azure.
3. Controleer het Hyper-V-host-[netwerk](hyper-v-azure-support-matrix.md#hyper-v-network-configuration), en de ondersteuning voor [host- en gastopslag](hyper-v-azure-support-matrix.md#hyper-v-host-storage) voor on-premises Hyper-V-hosts.
4. Controleer wat er na failover wordt ondersteund voor [Azure-netwerken](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [-opslag](hyper-v-azure-support-matrix.md#azure-storage) en [-rekenkracht](hyper-v-azure-support-matrix.md#azure-compute-features).
5. De on-premises VM's die u voor Azure repliceert, moeten overeenstemmen met [Azure VM-vereisten](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>VMM voorbereiden (optioneel)

Als Hyper-V-hosts worden beheerd met VMM, moet u de on-premises VMM-server voorbereiden. 

- Zorg ervoor dat de VMM-server minstens één cloud heeft, met een of meer hostgroepen. De Hyper-V-host waarop de VM’s worden uitgevoerd, moeten zich in de cloud bevinden.
- Bereid de VMM-servers voor op netwerktoewijzing.

### <a name="prepare-vmm-for-network-mapping"></a>VMM voorbereiden op netwerktoewijzing

Als u gebruikmaakt van VMM, wordt [netwerktoewijzing](./hyper-v-vmm-network-mapping.md) toegewezen tussen on-premises VMM-VM-netwerken en virtuele Azure-netwerken. Toewijzing zorgt ervoor dat Azure-VM's zijn verbonden met het juiste netwerk wanneer ze worden gemaakt na een failover.

Bereid VMM als volgt voor op netwerktoewijzing:

1. Zorg ervoor dat u beschikt over een logisch [VMM-netwerk](/system-center/vmm/network-logical) dat is gekoppeld aan de cloud, waarin de Hyper-V-hosts zich bevinden.
2. Zorg ervoor dat u beschikt over een [VM-netwerk](/system-center/vmm/network-virtual) dat is gekoppeld aan het logische netwerk.
3. Maak in VMM verbinding tussen de VM’s en het VM-netwerk.

## <a name="verify-internet-access"></a>Internettoegang controleren

1. Voor de doeleinden van deze zelfstudie is de eenvoudigste configuratie om de Hyper-V-hosts en VMM-server directe toegang tot internet te verlenen, zonder gebruik van een proxy. 
2. Zorg ervoor dat de Hyper-V-hosts, en de VMM-server indien van toepassing, toegang hebben tot de vereiste URL's hieronder.   
3. Als u de toegang beheert met behulp van een IP-adres, moet u ervoor zorgen dat:
    - Op een IP-adres gebaseerde firewallregels verbinding kunnen maken met [IP-bereiken van Azure Datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653) en de HTTPS-poort (443).
    - De IP-adresbereiken voor de Azure-regio van uw abonnement zijn toegestaan.
    
### <a name="required-urls"></a>Vereiste URL's


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Voorbereiden op het verbinden met virtuele Azure-machines na een failover

Tijdens een failover-scenario kunt u het best verbinding maken met uw gerepliceerde on-premises netwerk.

Als u na een failover verbinding wilt maken met Windows-VM’s met behulp van RDP, staat u als volgt toegang toe:

1. Om toegang te krijgen via het internet, schakelt u voor de failover RDP in op de on-premises VM. Zorg ervoor dat TCP- en UDP-regels zijn toegevoegd voor het profiel **Openbaar** en dat RDP is toegestaan in **Windows Firewall** > **Toegestane apps** voor alle profielen.
2. Voor toegang via VPN tussen sites schakelt u RDP in op de on-premises computer. RDP moet toegestaan zijn in de **Windows Firewall** -> **Toegestane apps en onderdelen** voor **Domein en Privé**-netwerken.
   Controleer of het SAN-beleid van het besturingssysteem is ingesteld op **OnlineAll**. [Meer informatie](https://support.microsoft.com/kb/3031135). Er mogen geen Windows-updates in behandeling zijn op de VM wanneer u een failover activeert. Als die er zijn, kunt u zich pas weer bij de virtuele machine aanmelden zodra de update is voltooid.
3. Controleer na een failover **Diagnostische gegevens over opstarten** op de Windows Azure VM om een schermopname van de VM weer te geven. Als u geen verbinding kunt maken, controleer dan of de VM actief is en bekijk deze [tips voor het oplossen van problemen](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Na een failover kunt u toegang krijgen tot Azure-VM's met hetzelfde IP-adres als de gerepliceerde on-premises VM, of met een ander IP-adres. [Meer informatie](concepts-on-premises-to-azure-networking.md) over het instellen van IP-adressering voor failover.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Herstel naar Azure na een noodgeval voor Hyper-V-VM’s](./hyper-v-azure-tutorial.md)
> [Herstel naar Azure na een noodgeval voor Hyper-V-VM;s in VMM-clouds](./hyper-v-vmm-azure-tutorial.md)
