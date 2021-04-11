---
title: Een NHyper-V-nood herstel analyse uitvoeren op een secundaire site met Azure Site Recovery
description: Meer informatie over het uitvoeren van een DR-analyse voor virtuele Hyper-V-machines in VMM-Clouds naar een secundair on-premises Data Center met behulp van Azure Site Recovery.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sideeksh
ms.openlocfilehash: 675190bee674c745494c2378712afa89bd43cbe4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727872"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Een DR-analyse uitvoeren voor virtuele Hyper-V-machines naar een secundaire site


In dit artikel wordt beschreven hoe u een DR-analyse (nood herstel) uitvoert voor Hyper-V-Vm's die worden beheerd in System Center Virtual Machine Manager V (MM)-Clouds, naar een secundaire on-premises site, met behulp van [Azure site Recovery](site-recovery-overview.md).

U voert een testfailover uit om uw replicatie strategie te valideren en een DR-analyse uit te voeren zonder gegevens verlies of downtime. Een testfailover heeft geen invloed op de doorlopende replicatie of in uw productie omgeving. 

## <a name="how-do-test-failovers-work"></a>Hoe werkt testfailover?

U voert een testfailover uit van de primaire naar de secundaire site. Als u alleen wilt controleren of een virtuele machine is gefailovert, kunt u een testfailover uitvoeren zonder dat u iets hoeft in te stellen op de secundaire site. Als u wilt controleren of de failover van de app werkt zoals verwacht, moet u netwerken en infra structuur op de secundaire locatie instellen.
- U kunt een testfailover uitvoeren op één virtuele machine of in een [herstel plan](site-recovery-create-recovery-plans.md).
- U kunt een testfailover zonder een netwerk uitvoeren, met een bestaand netwerk of met een automatisch gemaakt netwerk. Meer informatie over deze opties vindt u in de volgende tabel.
    - U kunt een testfailover zonder een netwerk uitvoeren. Deze optie is handig als u alleen wilt controleren of een failover van een virtuele machine is gelukt, maar u geen netwerk configuratie kunt controleren.
    - Voer de failover uit met een bestaand netwerk. U wordt aangeraden geen productie netwerk te gebruiken.
    - Voer de failover uit en laat Site Recovery automatisch een test netwerk maken. In dit geval wordt het netwerk door Site Recovery automatisch gemaakt en opschoont wanneer de testfailover is voltooid.
- U moet een herstel punt voor de testfailover selecteren: 
    - **Laatst verwerkte**: met deze optie mislukt een VM naar het laatste herstel punt dat is verwerkt door site Recovery. Deze optie heeft een lage RTO (Recovery Time Objective), omdat er geen tijd wordt besteed aan het verwerken van niet-verwerkte gegevens.
    - **Nieuwste app-consistent**: deze optie is een failover van een virtuele machine naar het nieuwste toepassings consistente herstel punt dat is verwerkt door site Recovery. 
    - **Nieuwste**: met deze optie worden eerst alle gegevens verwerkt die zijn verzonden naar site Recovery service, om een herstel punt te maken voor elke virtuele machine voordat er een failover wordt uitgevoerd. Deze optie biedt de laagste RPO (beoogd herstel punt), omdat de VM die is gemaakt na de failover, alle gegevens die worden gerepliceerd naar Site Recovery wanneer de failover werd geactiveerd.
    - **Laatste verwerkte multi-VM**: beschikbaar voor herstel plannen die een of meer vm's bevatten waarvoor multi-VM-consistentie is ingeschakeld. Vm's waarvoor de instelling is ingeschakeld, kunnen failover uitvoeren naar het meest recente, veelvoorkomende herstel punt met meerdere VM'S. Er wordt een failover uitgevoerd voor andere Vm's naar het meest recente verwerkte herstel punt.
    - **Nieuwste multi-VM-app-consistent**: deze optie is beschikbaar voor herstel plannen met een of meer vm's waarvoor multi-VM-consistentie is ingeschakeld. Voor virtuele machines die deel uitmaken van een replicatie groep, wordt een failover uitgevoerd naar het meest recente algemene herstel punt van een toepassings consistente multi-VM. Er wordt een failover uitgevoerd voor andere Vm's naar het nieuwste toepassings consistente herstel punt.
    - **Aangepast**: gebruik deze optie om een failover van een specifieke virtuele machine naar een bepaald herstel punt uit te kunnen geven.



## <a name="prepare-networking"></a>Netwerken voorbereiden

Wanneer u een testfailover uitvoert, wordt u gevraagd om de netwerk instellingen voor de test replica-machines te selecteren, zoals in de tabel wordt beschreven.

| **Optie** | **Details** |
| --- | --- |
| **Geen** | De test-VM wordt gemaakt op de host waarop de replica-VM zich bevindt. Deze is niet toegevoegd aan de Cloud en is niet verbonden met een netwerk.<br/><br/> U kunt de computer verbinden met een VM-netwerk nadat deze is gemaakt.|
| **Bestaande gebruiken** | De test-VM wordt gemaakt op de host waarop de replica-VM zich bevindt. Deze wordt niet toegevoegd aan de Cloud.<br/><br/>Maak een VM-netwerk dat is geïsoleerd van uw productie netwerk.<br/><br/>Als u een netwerk op basis van VLAN gebruikt, raden we u aan om voor dit doel een afzonderlijk logisch netwerk (niet gebruikt in productie) te maken in VMM. Dit logische netwerk wordt gebruikt voor het maken van VM-netwerken voor testfailover.<br/><br/>Het logische netwerk moet worden gekoppeld aan ten minste een van de netwerk adapters van alle Hyper-V-servers die als host fungeren voor virtuele machines.<br/><br/>Voor logische VLAN-netwerken moeten de netwerk sites die u aan het logische netwerk toevoegt, worden geïsoleerd.<br/><br/>Als u een logisch netwerk op basis van Windows-Netwerkvirtualisatie gebruikt, maakt Azure Site Recovery automatisch geïsoleerde VM-netwerken. |
| **Een netwerk maken** | Er wordt automatisch een tijdelijk test netwerk gemaakt op basis van de instelling die u opgeeft in **logisch netwerk** en de gerelateerde netwerk sites.<br/><br/> Failover controleert of Vm's zijn gemaakt.<br/><br/> Gebruik deze optie als een herstel plan meer dan één VM-netwerk gebruikt.<br/><br/> Als u Windows Network Virtualization-netwerken gebruikt, kunt u met deze optie automatisch VM-netwerken maken met dezelfde instellingen (subnetten en IP-adres groepen) in het netwerk van de virtuele replica machine. Deze VM-netwerken worden automatisch opgeruimd nadat de testfailover is voltooid.<br/><br/> De test-VM wordt gemaakt op de host waarop de virtuele replica machine zich bevindt. Deze wordt niet toegevoegd aan de Cloud.|

### <a name="best-practices"></a>Aanbevolen procedures

- Het testen van een productie netwerk resulteert in downtime voor de werk belasting van de productie. Vraag uw gebruikers om geen gebruik te maken van gerelateerde apps wanneer het nood herstel niveau wordt uitgevoerd.

- Het test netwerk hoeft niet overeen te komen met het logische netwerk type van VMM dat wordt gebruikt voor de testfailover. Maar sommige combi Naties werken niet:

     - Als de replica gebruikmaakt van DHCP-en VLAN-isolatie, heeft het VM-netwerk voor de replica geen vaste IP-adres groep nodig. Het gebruik van Windows-Netwerkvirtualisatie voor de testfailover werkt dus niet omdat er geen adres groepen beschikbaar zijn. 
        
     - De testfailover werkt niet als het replica netwerk geen isolatie gebruikt en het test netwerk maakt gebruik van Windows-Netwerkvirtualisatie. Dit komt doordat het netwerk zonder isolatie niet over de subnetten beschikt die vereist zijn voor het maken van een Windows-Netwerkvirtualisatie netwerk.
        
- U wordt aangeraden het netwerk dat u hebt geselecteerd voor netwerk toewijzing niet te gebruiken voor een testfailover.

- Hoe replica virtuele machines zijn verbonden met toegewezen VM-netwerken na een failover, is afhankelijk van hoe het VM-netwerk is geconfigureerd in de VMM-console.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Het VM-netwerk is geconfigureerd zonder isolatie of VLAN-isolatie

Als er een VM-netwerk is geconfigureerd in VMM zonder isolatie of VLAN-isolatie, let dan op het volgende:

- Als DHCP voor het VM-netwerk is gedefinieerd, wordt de virtuele replica machine verbonden met de VLAN-ID via de instellingen die zijn opgegeven voor de netwerk site in het gekoppelde logische netwerk. De virtuele machine ontvangt zijn IP-adres van de beschik bare DHCP-server.
- U hoeft geen vaste IP-adres groep te definiëren voor het doel-VM-netwerk. Als er een statische IP-adres groep voor het VM-netwerk wordt gebruikt, wordt de virtuele replica machine verbonden met de VLAN-ID via de instellingen die zijn opgegeven voor de netwerk site in het gekoppelde logische netwerk.
- De virtuele machine ontvangt zijn IP-adres uit de groep die is gedefinieerd voor het VM-netwerk. Als er geen groep met vaste IP-adressen is gedefinieerd in het doelnet werk van de doel-VM, mislukt de toewijzing van IP-adressen. Maak de IP-adres groep op zowel de bron-als de doel-VMM-servers die u wilt gebruiken voor beveiliging en herstel.

### <a name="vm-network-with-windows-network-virtualization"></a>VM-netwerk met Windows-Netwerkvirtualisatie

Als er een VM-netwerk is geconfigureerd in VMM met Windows-Netwerkvirtualisatie, let dan op het volgende:

- U moet een statische groep definiëren voor het doel-VM-netwerk, ongeacht of het bron-VM-netwerk is geconfigureerd voor het gebruik van DHCP of een groep met vaste IP-adressen. 
- Als u DHCP definieert, fungeert de doel-VMM-server als een DHCP-server en levert een IP-adres uit de groep die is gedefinieerd voor het doelnet werk van de virtuele machine.
- Als het gebruik van een groep met vaste IP-adressen is gedefinieerd voor de bron server, wijst de doel-VMM-server een IP-adres uit de groep toe. In beide gevallen mislukt de toewijzing van IP-adressen als er geen groep met vaste IP-adressen is gedefinieerd.



## <a name="prepare-the-infrastructure"></a>De infrastructuur voorbereiden

Als u alleen wilt controleren of een failover van een virtuele machine kan worden uitgevoerd, kunt u een testfailover zonder een infra structuur uitvoeren. Als u een volledige DR-analyse wilt uitvoeren om de app-failover te testen, moet u de infra structuur op de secundaire site voorbereiden:

- Als u een testfailover uitvoert met behulp van een bestaand netwerk, bereidt u Active Directory, DHCP en DNS voor in dat netwerk.
- Als u een testfailover uitvoert met de optie om automatisch een VM-netwerk te maken, moet u infrastructuur resources toevoegen aan het automatisch gemaakte netwerk, voordat u de testfailover uitvoert. In een herstel plan kunt u dit vereenvoudigen door een hand matige stap toe te voegen vóór groep-1 in het herstel plan dat u voor de testfailover wilt gebruiken. Voeg vervolgens de infrastructuur resources toe aan het automatisch gemaakte netwerk voordat u de testfailover uitvoert.


### <a name="prepare-dhcp"></a>DHCP voorbereiden
Als de virtuele machines die deel uitmaken van de testfailover DHCP gebruiken, maakt u een DHCP-test server in het geïsoleerde netwerk voor het doel van de testfailover.


### <a name="prepare-active-directory"></a>Active Directory voorbereiden
Als u een testfailover wilt uitvoeren voor het testen van toepassingen, hebt u een kopie nodig van de productie-Active Directory omgeving in uw test omgeving. Raadpleeg de [overwegingen voor testfailover voor Active Directory](site-recovery-active-directory.md#test-failover-considerations)voor meer informatie.

### <a name="prepare-dns"></a>DNS voorbereiden
Maak als volgt een DNS-server voor de testfailover:

* **DHCP**: als virtuele machines DHCP gebruiken, moet het IP-adres van de test-DNS worden bijgewerkt op de DHCP-test server. Als u een netwerk type van Windows-Netwerkvirtualisatie gebruikt, fungeert de VMM-server als de DHCP-server. Daarom moet het IP-adres van DNS worden bijgewerkt in het netwerk van de testfailover. In dit geval registreren de virtuele machines zich bij de relevante DNS-server.
* **Statisch adres**: als virtuele machines een statisch IP-adres gebruiken, moet het IP-adres van de test-DNS-server worden bijgewerkt in het netwerk van de test-failover. Mogelijk moet u DNS bijwerken met het IP-adres van de virtuele test machines. U kunt het volgende voorbeeld script gebruiken voor dit doel:

  ```powershell
  Param(
  [string]$Zone,
  [string]$name,
  [string]$IP
  )
  $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
  $newrecord = $record.clone()
  $newrecord.RecordData[0].IPv4Address  =  $IP
  Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
  ```

## <a name="run-a-test-failover"></a>Een testfailover uitvoeren

In deze procedure wordt beschreven hoe u een testfailover uitvoert voor een herstel plan. U kunt ook de failover uitvoeren voor één virtuele machine op het tabblad **virtual machines** .

1. Selecteer **herstel plannen**  >  *recoveryplan_name*. Klik **op failover testfailover**  >  .
2. Geef op de Blade **testfailover** op hoe replica-vm's moeten worden verbonden met netwerken na de testfailover.
3. Volg de voortgang van de failover op het tabblad **taken** .
4. Nadat de failover is voltooid, controleert u of de Vm's zijn gestart.
5. Wanneer u klaar bent, klikt u op **testfailover opschonen** in het herstel plan. Leg in **Notities** eventuele opmerkingen over de testfailover vast en sla deze op. Met deze stap worden alle Vm's en netwerken verwijderd die tijdens de testfailover zijn gemaakt door Site Recovery. 

![Testfailover](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> Het IP-adres dat is toegewezen aan een virtuele machine tijdens de testfailover is hetzelfde IP-adres dat de virtuele machine zou ontvangen voor een geplande of niet-geplande failover (ervan uitgaande dat het IP-adres beschikbaar is in het netwerk van de testfailover). Als hetzelfde IP-adres niet beschikbaar is in het testfailover, ontvangt de virtuele machine een ander IP-adres dat beschikbaar is in het netwerk van de testfailover.



### <a name="run-a-test-failover-to-a-production-network"></a>Een testfailover uitvoeren naar een productie netwerk

U wordt aangeraden geen testfailover uit te voeren op uw productie herstel site netwerk dat u hebt opgegeven tijdens de netwerk toewijzing. Als u echter de end-to-end-netwerk verbinding in een failover-VM wilt valideren, moet u rekening houden met de volgende punten:

* Zorg ervoor dat de primaire virtuele machine wordt afgesloten wanneer u de testfailover uitvoert. Als dat niet het geval is, worden twee virtuele machines met dezelfde identiteit tegelijkertijd uitgevoerd in hetzelfde netwerk. Deze situatie kan leiden tot ongewenste gevolgen.
* Eventuele wijzigingen die u aanbrengt in de Vm's van de testfailover, gaan verloren wanneer u de virtuele machines van de testfailover opschoont. Deze wijzigingen worden niet gerepliceerd naar de primaire Vm's.
* Testen zoals dit leidt tot uitval tijd voor uw productie toepassing. Vraag gebruikers van de toepassing de toepassing niet te gebruiken wanneer de DR-oefeningen worden uitgevoerd.  


## <a name="next-steps"></a>Volgende stappen
Nadat u een DR-analyse hebt uitgevoerd, kunt u [een volledige failover uitvoeren](site-recovery-failover.md).



