---
title: Verbinding maken met een on-premises failover van Azure Vm's met Azure Site Recovery
description: Hierin wordt beschreven hoe u verbinding maakt met virtuele Azure-machines na een failover van on-premises naar Azure met Azure Site Recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: harshacs
ms.openlocfilehash: 4b88ed44632aa255837d8fb499782e11c716d443
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048184"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>Verbinding maken met virtuele Azure-machines na een failover van on-premises 


In dit artikel wordt beschreven hoe u connectiviteit instelt zodat u na een failover verbinding kunt maken met virtuele Azure-machines.

Wanneer u herstel na nood gevallen van on-premises virtuele machines (Vm's) en fysieke servers naar Azure instelt, begint [Azure site Recovery](site-recovery-overview.md) met het repliceren van machines naar Azure. Wanneer er storingen optreden, kunt u een failover uitvoeren naar Azure vanaf uw on-premises site. Wanneer een failover optreedt, maakt Site Recovery Azure-Vm's met behulp van gerepliceerde on-premises gegevens. Als onderdeel van het plannen van nood herstel moet u nagaan hoe u na een failover verbinding maakt met apps die op deze Azure-Vm's worden uitgevoerd.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * On-premises machines voorbereiden voor failover.
> * Virtuele Azure-machines voorbereiden na een failover. 
> * IP-adressen op Azure-Vm's behouden na een failover.
> * Nieuwe IP-adressen toewijzen aan virtuele Azure-machines na een failover.

## <a name="prepare-on-premises-machines"></a>On-premises machines voorbereiden

Bereid uw on-premises machines voor op de failover om verbinding te kunnen maken met Azure-Vm's.

### <a name="prepare-windows-machines"></a>Windows-computers voorbereiden

Ga als volgt te werk op on-premises Windows-computers:

1. Configureer Windows-instellingen. Dit geldt ook voor het verwijderen van statische permanente routes of WinHTTP-proxy en het instellen van het SAN-beleid van de schijf in **OnlineAll**. [Volg](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure) deze instructies.

2. Zorg ervoor dat [deze services](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) worden uitgevoerd.

3. Schakel extern bureaublad (RDP) in om externe verbindingen met de on-premises machine mogelijk te maken. [Meer informatie over het](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) inschakelen van RDP met Power shell.

4. Als u na een failover toegang wilt krijgen tot een Azure-VM via internet, kunt u in Windows Firewall op de on-premises computer TCP en UDP toestaan in het open bare profiel en RDP als een toegestane app instellen voor alle profielen.

5. Als u na een failover toegang wilt krijgen tot een Azure-VM via een site-naar-site-VPN, moet u in Windows Firewall op de on-premises computer RDP toestaan voor het domein en het persoonlijke profiel. [Meer informatie](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) over het toestaan van RDP-verkeer.
6. Zorg ervoor dat er geen Windows-updates in behandeling zijn op de on-premises VM wanneer u een failover triggert. Als dat het geval is, kunnen updates na een failover worden geïnstalleerd op de virtuele machine van Azure en kunt u zich pas aanmelden bij de VM nadat de updates zijn voltooid.

### <a name="prepare-linux-machines"></a>Linux-machines voorbereiden

Ga als volgt te werk op on-premises Linux-machines:

1. Controleer of de Secure Shell-service zo is ingesteld dat deze automatisch wordt gestart bij het opstarten van het systeem.
2. Controleer of SSH-verbindingen zijn toegestaan door de firewallregels.


## <a name="configure-azure-vms-after-failover"></a>Virtuele Azure-machines configureren na een failover

Na een failover voert u de volgende stappen uit op de virtuele Azure-machines die worden gemaakt.

1. Wijs een openbaar IP-adres toe aan de VM om verbinding te maken met de VM via internet. U kunt niet hetzelfde open bare IP-adres gebruiken voor de Azure-VM die u hebt gebruikt voor uw on-premises machine. [Meer informatie](../virtual-network/virtual-network-public-ip-address.md)
2. Controleer of de regels van de netwerkbeveiligingsgroep (NSG) op de VM inkomende verbindingen naar de RDP- of SSH-poort toestaan.
3. Controleer de [Diagnostische gegevens over opstarten](/troubleshoot/azure/virtual-machines/boot-diagnostics#enable-boot-diagnostics-on-existing-virtual-machine) om de virtuele machine weer te geven.


> [!NOTE]
> De Azure Bastion-service biedt persoonlijke RDP-en SSH-toegang tot Azure-Vm's. Meer [informatie](../bastion/bastion-overview.md) over deze service.

## <a name="set-a-public-ip-address"></a>Een openbaar IP-adres instellen

Als alternatief voor het hand matig toewijzen van een openbaar IP-adres aan een Azure-VM, kunt u het adres toewijzen tijdens een failover met behulp van een script of Azure Automation-runbook in een Site Recovery [herstel plan](site-recovery-create-recovery-plans.md), of u kunt route ring op DNS-niveau instellen met behulp van Azure Traffic Manager. Meer [informatie](concepts-public-ip-address-with-site-recovery.md) over het instellen van een openbaar adres.


## <a name="assign-an-internal-address"></a>Een intern adres toewijzen

Als u na een failover het interne IP-adres van een virtuele Azure-machine wilt instellen, hebt u een aantal opties:

- **Hetzelfde IP-adres behouden**: u kunt hetzelfde IP-adres gebruiken op de Azure-VM als de virtuele machine die is toegewezen aan de on-premises computer.
- **Gebruik een ander IP-adres**: u kunt een ander IP-adres gebruiken voor de Azure-VM.


## <a name="retain-ip-addresses"></a>IP-adressen behouden

Met Site Recovery kunt u dezelfde IP-adressen behouden wanneer er een failover wordt uitgevoerd naar Azure. Het behouden van hetzelfde IP-adres voor komt mogelijke netwerk problemen na een failover, maar biedt een zekere complexiteit.

- Als de Azure-doel-VM gebruikmaakt van hetzelfde IP-adres/subnet als uw on-premises site, kunt u geen verbinding maken met een site-naar-site-VPN-verbinding of ExpressRoute, omdat het adres overlapt. Subnetten moeten uniek zijn.
- U hebt na een failover een verbinding nodig van on-premises naar Azure, zodat apps beschikbaar zijn op virtuele machines in Azure. Azure biedt geen ondersteuning voor uitgerekte VLAN'S, dus als u IP-adressen wilt behouden, moet u de IP-ruimte op Azure afleiden door failover van het hele subnet en de on-premises machine uit te voeren.
- Met een subnet-failover zorgt u ervoor dat een specifiek subnet niet tegelijkertijd on-premises en in azure beschikbaar is.

Voor het bewaren van IP-adressen zijn de volgende stappen vereist:

- Stel in de berekening & netwerk eigenschappen van het gerepliceerde item netwerk-en IP-adres Sering in voor de doel-Azure-VM om de on-premises instelling te spie gelen.
- Subnetten moeten worden beheerd als onderdeel van het nood herstel proces. U moet een Azure VNet hebben dat overeenkomt met het on-premises netwerk en nadat de failover-netwerk routes moeten worden gewijzigd om aan te geven dat het subnet is verplaatst naar Azure en nieuwe IP-adres locaties.  

### <a name="failover-example"></a>Voor beeld van failover

We kijken naar een voorbeeld.

- De fictieve onderneming Woodgrove Bank fungeert als host voor hun zakelijke apps on-premises, die hun mobiele apps in Azure.
- Ze maken verbinding vanaf on-premises met Azure via site-naar-site-VPN. 
- Woodgrove maakt gebruik van Site Recovery om on-premises machines te repliceren naar Azure.
- De on-premises apps gebruiken hardcoded IP-adressen, zodat ze dezelfde IP-adressen in azure willen behouden.
- On-premises de computers waarop de apps worden uitgevoerd, worden uitgevoerd in drie subnetten:
    - 192.168.1.0/24
    - 192.168.2.0/24
    - 192.168.3.0/24
- Hun apps die worden uitgevoerd in azure, bevinden zich in het Azure VNet **Azure-netwerk** in twee subnetten:
    - 172.16.1.0/24
    - 172.16.2.0/24

Als u de adressen wilt behouden, gaat u als volgt te werk.

1. Wanneer replicatie is ingeschakeld, geven ze op dat computers moeten worden gerepliceerd naar het **Azure-netwerk**.
2. Ze maken het **herstel netwerk** in Azure. Dit VNet komt overeen met het subnet 192.168.1.0/24 in hun on-premises netwerk.
3. Woodgrove stelt een [vnet-naar-vnet-verbinding](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) tussen de twee netwerken in. 

    > [!NOTE]
    > Afhankelijk van de toepassings vereisten, kan een VNet-naar-VNet-verbinding worden ingesteld voordat een failover wordt uitgevoerd, als een hand matige stap/script stap/Azure Automation-runbook in een Site Recovery [herstel plan](site-recovery-create-recovery-plans.md), of nadat de failover is voltooid.

4. Voordat failover wordt ingesteld op de computer eigenschappen in Site Recovery, stellen ze het doel-IP-adres in op het adres van de on-premises computer, zoals wordt beschreven in de volgende procedure.
5. Na een failover worden de virtuele Azure-machines gemaakt met hetzelfde IP-adres. Woodgrove maakt verbinding vanaf **Azure Network** met het **Recovery Network** vnet met behulp van vnet-peering (waarbij Transit connectiviteit is ingeschakeld).
6. Op locatie moet Woodgrove netwerk wijzigingen aanbrengen, met inbegrip van het wijzigen van routes om aan te geven dat 192.168.1.0/24 is verplaatst naar Azure.  

**Infra structuur vóór failover**

![Voor failover van subnet](./media/site-recovery-network-design/network-design7.png)


**Infra structuur na failover**

![Na failover van subnet](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>Instellingen voor het doelnet netwerk instellen

Geef vóór failover de netwerk instellingen en het IP-adres op voor de doel-Azure-VM.

1.  Selecteer de on-premises computer in de Recovery Services kluis-> **gerepliceerde items**.
2. Klik op de pagina **Compute and Network** voor de machine op **bewerken** om netwerk-en adapter instellingen te configureren voor de Azure-doel-VM.
3. Selecteer in het **dialoog netwerk eigenschappen** het doelnet doel waarin de virtuele Azure-machine zich bevindt wanneer deze wordt gemaakt na een failover.
4. Configureer in **netwerk interfaces** de netwerk adapters in het doelnet-netwerk. Standaard Site Recovery worden alle gedetecteerde Nic's op de on-premises computer weer gegeven.
    - In het **interface type** van het doelnet werk kunt u elke NIC instellen als **primair**, **secundair** of **niet maken** als u die specifieke NIC niet nodig hebt in het doelnet werk. Eén netwerk adapter moet worden ingesteld als primair voor failover. Houd er rekening mee dat het wijzigen van het doelnet netwerk van invloed is op alle Nic's voor de virtuele Azure-machine.
    - Klik op de NIC-naam om het subnet op te geven waarin de Azure VM wordt geïmplementeerd.
    - Overschrijf **dynamische** met het privé-IP-adres dat u wilt toewijzen aan de doel-Azure-VM. Als er geen IP-adres is opgegeven Site Recovery wordt het volgende beschik bare IP-adres in het subnet aan de NIC toegewezen bij een failover.
    - Meer [informatie](site-recovery-manage-network-interfaces-on-premises-to-azure.md) over het beheren van nic's voor on-premises failover naar Azure.


## <a name="get-new-ip-addresses"></a>Nieuwe IP-adressen ophalen

In dit scenario krijgt de Azure VM na een failover een nieuw IP-adres. Als u een nieuw IP-adres wilt instellen voor de virtuele machine die is gemaakt na een failover, kunt u de volgende stappen uitvoeren:

1. Ga naar **gerepliceerde items**.
2. Selecteer de gewenste virtuele machine van Azure.
3. Selecteer **Compute en netwerk** en selecteer **bewerken**.

     ![De failover-netwerk configuraties aanpassen](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Als u de instellingen van het failover-netwerk wilt bijwerken, selecteert u **bewerken** voor de NIC die u wilt configureren. Op de volgende pagina die wordt geopend, geeft u het bijbehorende vooraf gemaakte IP-adres op in de testfailover en failover-locatie.

    ![De NIC-configuratie bewerken](media/azure-to-azure-customize-networking/nic-drilldown.png)

5. Selecteer **OK**.

Site Recovery gaat nu door met deze instellingen en zorg ervoor dat de virtuele machine op failover is verbonden met de geselecteerde bron via het bijbehorende IP-adres, als deze beschikbaar is in het doel-IP-bereik. In dit scenario is het niet nodig om het hele subnet te failoveren. Er is een DNS-update vereist om records bij te werken voor een failover van een machine om naar het nieuwe IP-adres van de virtuele machine te verwijzen.

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over](site-recovery-active-directory.md) het repliceren van on-premises Active Directory en DNS naar Azure.