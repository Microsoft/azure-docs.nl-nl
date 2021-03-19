---
title: IP-adres Sering instellen na een failover naar een secundaire site met Azure Site Recovery
description: Hierin wordt beschreven hoe u IP-adres Sering instelt voor het maken van verbinding met Vm's op een secundaire on-premises site na herstel na nood gevallen en failover met Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 43942c20a353ff69383f3e721679e4c95ab9d230
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87495940"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>IP-adres Sering instellen om verbinding te maken met een secundaire on-premises site na een failover

Nadat u een failover hebt uitgevoerd voor virtuele Hyper-V-machines in System Center Virtual Machine Manager-Clouds (VMM) naar een secundaire site, moet u verbinding kunnen maken met de replica-Vm's. Dit artikel helpt u om dit te doen. 

## <a name="connection-options"></a>Verbindings opties

Na een failover zijn er een aantal manieren om IP-adres Sering te verwerken voor replica-Vm's: 

- **Hetzelfde IP-adres behouden na een failover**: in dit scenario heeft de gerepliceerde VM hetzelfde IP-adres als de primaire virtuele machine. Dit vereenvoudigt netwerk problemen na een failover, maar vereist een goed functioneren van de infra structuur.
- **Een ander IP-adres gebruiken na een failover**: in dit scenario krijgt de VM na een failover een nieuw IP-adres. 
 

## <a name="retain-the-ip-address"></a>Het IP-adres behouden

Als u de IP-adressen van de primaire site wilt behouden na een failover naar de secundaire site, kunt u het volgende doen:

- Implementeer een uitgerekt subnet tussen de primaire en secundaire sites.
- Voer een volledige subnet-failover uit vanaf de primaire naar de secundaire site. U moet routes bijwerken om de nieuwe locatie van de IP-adressen aan te geven.


### <a name="deploy-a-stretched-subnet"></a>Een uitgerekt subnet implementeren

In een uitgerekte configuratie is het subnet tegelijkertijd beschikbaar op zowel de primaire als de secundaire site. Wanneer u in een uitgerekt subnet een machine en de IP-adres configuratie (laag 3) naar de secundaire site verplaatst, stuurt het netwerk het verkeer automatisch naar de nieuwe locatie. 

- Vanuit een perspectief van laag 2 (Data Link Layer) hebt u netwerk apparatuur nodig waarmee een uitgerekt VLAN kan worden beheerd.
- Door het VLAN te spreiden, wordt het mogelijke fout domein uitgebreid naar beide sites. Dit wordt een Single Point of Failure. Het kan voor komen dat u in een dergelijk scenario mogelijk geen incidenten kunt isoleren zoals een uitzend Storm. 


### <a name="fail-over-a-subnet"></a>Failover van een subnet

U kunt een failover uitvoeren voor het hele subnet om de voor delen van het uitgerekte subnet te verkrijgen, zonder dat u het werkelijk uitrekt. In deze oplossing is een subnet beschikbaar op de bron-of doel site, maar niet in beide tegelijk.

- Als u de IP-adres ruimte in het geval van een failover wilt behouden, kunt u de router-infra structuur via een programma rangschikken om subnetten van de ene site naar de andere te verplaatsen.
- Wanneer een failover optreedt, worden subnetten verplaatst met de bijbehorende Vm's.
- Het belangrijkste nadeel van deze benadering is dat in het geval van een storing het hele subnet moet worden verplaatst.

#### <a name="example"></a>Voorbeeld

Hier volgt een voor beeld van een volledige subnet-failover. 

- Voor failover heeft de primaire site toepassingen die worden uitgevoerd in subnet 192.168.1.0/24.
- Tijdens de failover worden alle virtuele machines in dit subnet overgeschakeld naar de secundaire site en blijven ze hun IP-adressen behouden. 
- Routes tussen alle sites moeten worden aangepast om aan te geven dat alle virtuele machines in subnet 192.168.1.0/24 nu zijn verplaatst naar de secundaire site.

De volgende afbeeldingen illustreren de subnetten voor en na een failover.


**Voor failover**

![Diagram waarin de subnetten voor failover worden weer gegeven.](./media/hyper-v-vmm-networking/network-design2.png)

**Na een failover**

![Diagram waarin de subnetten worden weer gegeven na een failover.](./media/hyper-v-vmm-networking/network-design3.png)

Na een failover wijst Site Recovery een IP-adres toe voor elke netwerk interface op de VM. Het adres wordt toegewezen vanuit de groep met vaste IP-adressen in het relevante netwerk, voor elk VM-exemplaar.

- Als de IP-adres groep op de secundaire site gelijk is aan die op de bron site, Site Recovery het hetzelfde IP-adres (van de bron-VM) toewijzen aan de replica-VM. Het IP-adres is gereserveerd in VMM, maar dit is niet ingesteld als het IP-adres van de failover op de Hyper-V-host. Het IP-adres van de failover op een hyper-v-host is ingesteld net vóór de failover.
- Als hetzelfde IP-adres niet beschikbaar is, wijst Site Recovery een andere beschik bare IP-adres uit de groep toe.
- Als Vm's DHCP gebruiken, beheert Site Recovery de IP-adressen niet. U moet controleren of de DHCP-server op de secundaire site adressen kan toewijzen van hetzelfde bereik als de bron site.

### <a name="validate-the-ip-address"></a>Het IP-adres valideren

Wanneer u de beveiliging voor een virtuele machine inschakelt, kunt u het volgende voorbeeld script gebruiken om het adres te controleren dat is toegewezen aan de virtuele machine. Dit IP-adres wordt ingesteld als het failover-IP-adres en wordt toegewezen aan de virtuele machine op het moment van de failover:

```powershell
$vm = Get-SCVirtualMachine -Name <VM_NAME>
$na = $vm[0].VirtualNetworkAdapters>
$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
$ip.address
```

## <a name="use-a-different-ip-address"></a>Een ander IP-adres gebruiken

In dit scenario worden de IP-adressen van Vm's waarvoor een failover is uitgevoerd, gewijzigd. Het nadeel van deze oplossing is het vereiste onderhoud.  DNS-en cache vermeldingen moeten mogelijk worden bijgewerkt. Dit kan leiden tot uitval tijd, die als volgt kan worden verholpen:

- Gebruik lage TTL-waarden voor intranet toepassingen.
- Gebruik het volgende script in een Site Recovery herstel plan voor een tijdige update van de DNS-server. U hebt het script niet nodig als u dynamische DNS-registratie gebruikt.

    ```powershell
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>Voorbeeld 

In dit voor beeld hebben we verschillende IP-adressen voor de primaire en secundaire sites, en is er een derde site van waaruit toepassingen worden gehost op de primaire of herstel site.

- Voor failover worden apps gehost subnet 192.168.1.0/24 op de primaire site.
- Na een failover worden apps geconfigureerd in subnet 172.16.1.0/24 op de secundaire site.
- Alle drie de sites hebben toegang tot elkaar.
- Na een failover worden apps teruggezet in het herstel subnet.
- In dit scenario is het niet nodig om een failover uit te brengen voor het hele subnet. er zijn geen wijzigingen nodig om VPN-of netwerk routes opnieuw te configureren. De failover en enkele DNS-updates, zorg ervoor dat toepassingen toegankelijk blijven.
- Als DNS is geconfigureerd voor het toestaan van dynamische updates, registreert de Vm's zichzelf met het nieuwe IP-adres wanneer ze worden gestart na een failover.

**Voor failover**

![Diagram met verschillende IP-adressen vóór de failover.](./media/hyper-v-vmm-networking/network-design10.png)

**Na een failover**

![Diagram met verschillende IP-adressen na een failover.](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Volgende stappen

[Een failover uitvoeren](hyper-v-vmm-failover-failback.md)

