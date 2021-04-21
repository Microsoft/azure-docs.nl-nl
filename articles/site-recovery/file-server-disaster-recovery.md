---
title: Een bestandsserver beveiligen met behulp van Azure Site Recovery
description: In dit artikel wordt beschreven hoe u een bestandsserver beveiligt met behulp van Azure Site Recovery
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: sharrai
ms.custom: mvc
ms.openlocfilehash: 5209e715fab422a50e31810b5eb0d370d5fc61cd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792521"
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Een bestandsserver beveiligen met behulp van Azure Site Recovery 

[Azure Site Recovery](site-recovery-overview.md) draagt bij aan uw strategie voor zakelijke continuïteit en noodherstel (BCDR) door te zorgen dat uw zakelijke apps actief blijven tijdens geplande en ongeplande uitval. Site Recovery beheert en orkestreert herstel na noodgeval van on-premises computers en Azure-VM’s (virtuele machines). Herstel na noodgeval omvat replicatie, failover en herstel voor verschillende workloads.

In dit artikel wordt beschreven hoe u een bestandsserver beveiligt met behulp van Site Recovery. Ook worden aanbevelingen gedaan die geschikt zijn voor verschillende omgevingen. 

- [Azure IaaS-bestandsservers repliceren](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Een on-premises bestandsserver repliceren met behulp van Site Recovery](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>Architectuur van bestandsserver
Het doel van een openbaar gedistribueerd systeem voor het delen van bestanden is om een omgeving te bieden waar een groep geografisch gedistribueerde gebruikers efficiënt kan samenwerken aan bestanden, met de garantie dat hun integriteitsvereisten worden afgedwongen. Een typisch on-premises ecosysteem voor bestandsservers dat ondersteuning biedt voor een hoog aantal gelijktijdige gebruikers en een groot aantal inhoudsitems, maakt gebruik van een DFSR (Distributed File System Replication) voor het plannen van replicaties en het beperken van de bandbreedte. 

DFSR maakt gebruik van een compressiealgoritme dat bekend staat als RDC (Remote Differential Compression). RDC kan worden gebruikt om bestanden efficiënt bij te werken over een netwerk met beperkte bandbreedte. RDC kan invoegingen, verwijderingen en nieuwe indelingen van gegevens in bestanden detecteren. DFSR is ingeschakeld om alleen de gewijzigde bestandsblokken te repliceren wanneer bestanden worden bijgewerkt. Er zijn ook bestandsserveromgevingen waarin dagelijkse back-ups worden gemaakt tijden niet-piekuren. Dit gebeurt om te voldoen aan de behoeften tijdens noodgevallen. DFSR is niet geïmplementeerd.

In het volgende diagram wordt de omgeving van de bestandsserver getoond met DFSR geïmplementeerd.
        
![DFSR-architectuur](media/site-recovery-file-server/dfsr-architecture.JPG)

In het vorige diagram nemen meerdere bestandsservers - leden genoemd - actief deel aan het repliceren van bestanden in een replicatiegroep. De inhoud van de gerepliceerde map is beschikbaar voor alle clients via welke aanvragen worden verzonden naar een van de leden, zelfs als een lid offline is.

## <a name="disaster-recovery-recommendations-for-file-servers"></a>Aanbevelingen voor herstel na noodgeval voor bestandsservers

* **Een bestandsserver repliceren met behulp van een Site Recovery**: bestandsservers kunnen worden gerepliceerd in Azure met behulp van Site Recovery. Wanneer een of meer on-premises bestandsserver niet toegankelijk zijn, kunnen de herstel-VM’s naar Azure worden gebracht. De VM’s kunnen vervolgens, on-premises, aanvragen van clients verwerken, mits er sprake is van site-to-site VPN-connectiviteit, en Active Directory is geconfigureerd in Azure. U kunt deze methode gebruiken bij een omgeving waarin DFSR is geconfigureerd of in een bestandsserveromgeving zonder DFSR. 

* **DFSR uitbreiden naar een Azure IaaS-VM**: in een geclusterde bestandsserveromgeving waarin DFSR is geïmplementeerd, kunt u de on-premises DFSR uitbreiden naar Azure. Er wordt vervolgens een Azure-VM ingeschakeld om de bestandsserverfunctie uit te voeren. 

    * Nadat de afhankelijkheden van site-to-site VPN-connectiviteit en Active Directory zijn verwerkt en DFSR is ingesteld, kunnen clients, wanneer een of meer on-premises bestandsservers niet toegankelijk zijn, verbinding maken met de Azure-VM die de aanvragen verwerkt.

    * U kunt deze aanpak gebruiken als uw VM’s configuraties hebben die niet worden ondersteund met Site Recovery. Een voorbeeld is een gedeelde clusterschijf. Deze wordt soms gebruikt in bestandsserveromgevingen. DFSR werkt ook goed met omgevingen met een lage bandbreedte met gemiddeld verloop. Houd rekening met extra kosten wanneer een Azure-VM de hele tijd actief is. 

* **Gebruik Azure File Sync om uw bestanden** te repliceren: als u van plan bent om de cloud te gebruiken of al een Azure-VM wilt gebruiken, kunt u Azure File Sync. Azure File Sync biedt synchronisatie van volledig beheerde bestands shares in de cloud die toegankelijk zijn via het industriestandaard [Server Message Block](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) (SMB)-protocol. Azure-bestandsshares kunnen vervolgens gelijktijdig worden gekoppeld met on-premises implementaties of cloudimplementaties van Windows, Linux en macOS. 

De volgende diagrammen helpen u te bepalen welke strategie u moet gebruiken voor uw bestandsserveromgeving.

![Beslissingsstructuur](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>Factoren om in overweging te nemen bij uw beslissingen over herstel na noodgeval in Azure

|Omgeving  |Aanbeveling  |Punten om in overweging te nemen |
|---------|---------|---------|
|Bestandsserveromgeving met of zonder DFSR|   [Site Recovery gebruiken voor replicatie](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    Site Recovery biedt geen ondersteuning voor schijfclusters of NAS (Network Attached Storage). Als deze configuraties worden gebruikt in uw omgeving, kiest u een van de andere methoden, zoals toepasselijk is. <br> Site Recovery biedt geen ondersteuning voor SMB 3.0. De gerepliceerde VM bevat alleen wijzigingen in bestanden die zijn bijgewerkt op de oorspronkelijke locatie van de bestanden.<br>  Site Recovery biedt een proces voor bijna synchrone gegevensreplicatie. In het geval van een ongeplande failover-scenario kan er mogelijk gegevensverlies zijn en kan dit leiden tot problemen met niet-overeenkomende USN-gegevens.
|Bestandsserveromgeving met DFSR     |  [DFSR uitbreiden naar een virtuele Azure IaaS-machine](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |    DFSR werkt goed in omgevingen waarin de bandbreedte extreem is beperkt. Voor deze aanpak is vereist dat een Azure-VM de hele tijd actief is. U moet in uw planning rekening houden met de kosten van de VM.         |
|Azure IaaS-VM     |     File Sync    |     Als u File Sync gebruikt in een noodherstelscenario, moet u tijdens failover handmatige acties uitvoeren om ervoor te zorgen dat de bestandsshares op een transparante manier toegankelijk zijn voor de clientcomputer. Voor File Sync is vereist dat poort 445 open is op de clientcomputer.     |


### <a name="site-recovery-support"></a>Ondersteuning voor Site Recovery
Omdat Site Recovery-replicatie toepassingsagnostisch is, is de verwachting dat deze aanbevelingen gelden voor de volgende scenario’s.

| Bron  |Op een secundaire site  |In Azure
|---------|---------|---------|
|Azure|  -|Yes|
|Hyper-V|  Ja  |Ja
|VMware  |Ja|  Ja
|Fysieke server|  Ja  |Ja
 

> [!IMPORTANT]
> Voordat u verdergaat met een van de volgende drie methoden, moet u ervoor zorgen dat deze afhankelijkheden zijn afgehandeld.



**Connectiviteit tussen sites**: er moet een directe verbinding tussen de on-premises site en het Azure-netwerk tot stand worden gebracht om communicatie tussen servers toe te staan. Gebruik een beveiligde site-to-site VPN-verbinding met een virtueel Azure-netwerk die wordt gebruikt als noodherstelsite. Zie [Een site-to-site VPN-verbinding tot stand brengen tussen een on-premises site en een virtueel Azure-netwerk](../vpn-gateway/tutorial-site-to-site-portal.md).

**Active Directory**: DFSR is afhankelijk van Active Directory. Dit betekent dat de Active Directory-forest met lokale domeincontrollers is uitgebreid naar de noodherstelsite in Azure. Zelfs als u niet gebruikmaakt van DFSR, moet u deze stappen nemen als de beoogde gebruiker toegang moet krijgen of geverifieerd moet worden voor toegang. Zie [On-premises Active Directory uitbreiden naar Azure](./site-recovery-active-directory.md) voor meer informatie.

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Aanbevelingen voor herstel na noodgeval voor virtuele Azure IaaS-machines

Als u herstel na noodgeval configureert en beheert voor bestandsservers die worden gehost op Azure IaaS-VM’s, kunt u kiezen uit twee opties, afhankelijk van of u wilt migreren naar [Azure Files](../storage/files/storage-files-introduction.md):

* [File Sync gebruiken](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [Site Recovery gebruiken](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>File Sync gebruiken om bestanden te repliceren die worden gehost op een virtuele IaaS-machine

Azure Files kan worden gebruikt om bestandsshares op traditionele on-premises bestandsservers of NAS-apparaten volledig te vervangen of aan te vullen. Azure-bestandsshares kunnen ook met File Sync worden gerepliceerd naar Windows-servers - on-premises of in de cloud - voor een krachtige en gedistribueerde opslag in de cache van gegevens waar deze worden gebruikt. De volgende stappen beschrijven de aanbeveling voor herstel na noodgeval voor Azure-VM’s waarop dezelfde functies worden uitgevoerd als op traditionele bestandsservers:
* Computers beveiligen met behulp van Site Recovery. Volg de stappen in [Een Azure-VM repliceren naar een andere Azure-regio](azure-to-azure-quickstart.md).
* Gebruik File Sync om bestanden van de VM die fungeert als de bestandsserver, te repliceren naar de cloud.
* Gebruik de Site Recovery-functie voor [herstelplannen](site-recovery-create-recovery-plans.md) om scripts toe te voegen om [de Azure-bestandsshare te koppelen](../storage/files/storage-how-to-use-files-windows.md) en toegang te krijgen tot de share op de virtuele machine.

In de volgende stappen wordt kort beschreven hoe u File Sync gebruikt:

1. [Maak een opslagaccount in Azure](../storage/common/storage-account-create.md?toc=/azure/storage/files/toc.json). Als u geografisch redundante opslag met leestoegang kiest voor uw opslagaccounts, krijgt u, bij een noodgeval, leestoegang tot uw gegevens vanuit de secundaire regio. Zie Herstel na noodherstel en [failover van opslagaccount voor meer informatie.](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json)
2. [Maak een bestands share](../storage/files/storage-how-to-create-file-share.md).
3. [Start File Sync](../storage/file-sync/file-sync-deployment-guide.md) op de Azure-bestandsserver.
4. Maak een synchronisatiegroep. Eindpunten binnen een synchronisatiegroep worden onderling synchroon gehouden. Een synchronisatiegroep moet minstens één cloudeindpunt bevatten, dat een Azure-bestandsshare en representeert. Een synchronisatiegroep moet ook één servereindpunt bevatten, dat een pad op een Windows-server representeert.
5. Uw bestanden worden nu synchroon gehouden met uw Azure-bestandsshare en on-premises server.
6. Als er sprake is van een noodgeval in uw on-premises omgeving, voert u een failover uit met behulp van een [herstelplan](site-recovery-create-recovery-plans.md). Voeg het script toe om [de Azure-bestandsshare te koppelen](../storage/files/storage-how-to-use-files-windows.md) en toegang te krijgen tot de share op de virtuele machine.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>Een IaaS-bestandsserver repliceren met behulp van Site Recovery

Als u on-premises clients hebt die toegang hebben tot de virtuele IaaS-bestandsserver, voert u hierop volgende stappen uit. Zo niet, ga dan direct naar stap 3.

1. Breng een site-to-site VPN-verbinding tot stand tussen een on-premises site en het Azure-netwerk.
2. Breid de on-premises Active Directory uit.
3. [Herstel na noodgeval instellen](azure-to-azure-tutorial-enable-replication.md) voor de IaaS-bestandsserver naar een secundaire regio.


Lees [dit artikel](./azure-to-azure-architecture.md) voor meer informatie over herstel na noodgeval naar een secundaire regio.


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>Een on-premises bestandsserver repliceren met behulp van Site Recovery

In de volgende stappen wordt replicatie beschreven voor een VMware-VM. Zie [deze zelfstudie](./hyper-v-azure-tutorial.md) voor stappen voor het repliceren van een Hyper-V-VM.

1. [Bereid Azure-resources](tutorial-prepare-azure.md) voor op replicatie van on-premises machines.
2. Breng een site-to-site VPN-verbinding tot stand tussen een on-premises site en het Azure-netwerk. 
3. Breid de on-premises Active Directory uit.
4. [Bereid on-premises VMware-servers voor.](./vmware-azure-tutorial-prepare-on-premises.md)
5. [Herstel na noodgeval instellen](./vmware-azure-tutorial.md) naar Azure voor on-premises VM’s.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>DFSR uitbreiden naar een virtuele Azure IaaS-machine

1. Breng een site-to-site VPN-verbinding tot stand tussen een on-premises site en het Azure-netwerk. 
2. Breid de on-premises Active Directory uit.
3. [Een bestandsserver-VM maken en inrichten](../virtual-machines/windows/quick-create-portal.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) in het virtuele Azure-netwerk.
Zorg ervoor dat de virtuele machine is toegevoegd aan hetzelfde virtuele Azure-netwerk dat een kruislingse connectiviteit met de on-premises omgeving heeft. 
4. Installeer en [configureer DFSR](https://techcommunity.microsoft.com/t5/storage-at-microsoft/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of/ba-p/424877) in Windows Server.
5. [Implementeer een DFS-naamruimte](/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6. Wanneer de DFS-naamruimte is geïmplementeerd, kan failover van gedeelde mappen van productiesites naar noodherstelsites worden uitgevoerd door de mapdoelen voor de DFS-naamruimte bij te werken. Nadat deze wijzigingen in de DFS-naamruimte zijn gerepliceerd via Active Directory, zijn gebruikers transparant verbonden met de juiste mapdoelen.

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>File Sync gebruiken om uw on-premises bestanden te repliceren
U kunt File Sync gebruiken om bestanden te repliceren naar de cloud. Als er sprake is van een noodgeval en de on-premises bestandsserver niet beschikbaar is, kunt u de gewenste bestandslocaties koppelen vanuit de cloud en doorgaan met het verwerken van aanvragen van clientcomputers.
File Sync integreren met Site Recovery:

* Beveilig de bestandsservers met behulp van Site Recovery. Volg de stappen in [deze zelfstudie](./vmware-azure-tutorial.md).
* Gebruik File Sync om bestanden van de computer die fungeert als bestandsserver, te repliceren naar de cloud.
* Gebruik de functie voor herstelplannen in Site Recovery om scripts toe te voegen om de Azure-bestandsshare te koppelen op de bestandsserver-VM in Azure waarvoor de failover is uitgevoerd.

Volg deze stappen om File Sync te gebruiken:

1. [Maak een opslagaccount in Azure](../storage/common/storage-account-create.md?toc=/azure/storage/files/toc.json). Als u geografisch redundante opslag met leestoegang kiest (aanbevolen) voor uw opslagaccounts, krijgt u, bij een noodgeval, leestoegang tot uw gegevens vanuit de secundaire regio. Zie Herstel na noodherstel en [failover van opslagaccount voor meer](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json)informatie.
2. [Maak een bestands share](../storage/files/storage-how-to-create-file-share.md).
3. [Implementeer File Sync](../storage/file-sync/file-sync-deployment-guide.md) op de on-premises bestandsserver.
4. Maak een synchronisatiegroep. Eindpunten binnen een synchronisatiegroep worden onderling synchroon gehouden. Een synchronisatiegroep moet minstens één cloudeindpunt bevatten, dat een Azure-bestandsshare en representeert. De synchronisatiegroep moet ook één servereindpunt bevatten, dat een pad op de on-premises server representeert.
5. Uw bestanden worden nu synchroon gehouden met uw Azure-bestandsshare en on-premises server.
6. Als er sprake is van een noodgeval in uw on-premises omgeving, voert u een failover uit met behulp van een [herstelplan](site-recovery-create-recovery-plans.md). Voeg het script toe om de Azure-bestandsshare te koppelen en toegang te krijgen tot de share op de virtuele machine.

> [!NOTE]
> Zorg ervoor dat poort 445 is geopend. Azure Files maakt gebruik van het SMB-protocol. SMB communiceert via TCP-poort 445. Controleer of de TCP-poort 445 op een clientcomputer niet wordt geblokkeerd vanwege uw firewall.


## <a name="do-a-test-failover"></a>Een testfailover uitvoeren

1. Ga naar de Azure-portal en selecteer uw Recovery Services-kluis.
2. Selecteer het herstelplan dat is gemaakt voor de bestandsserveromgeving.
3. Selecteer **Failover testen**.
4. Selecteer het herstelpunt en het virtuele Azure-netwerk om de testfailover te starten.
5. Nadat de secundaire omgeving actief is, voert u de validaties uit.
6. Nadat de validaties zijn voltooid, selecteert u in het herstelplan de optie **Testfailover opschonen**, waarna de testfailoveromgeving wordt opgeschoond.

Zie [Failover testen in Site Recovery](site-recovery-test-failover-to-azure.md) voor meer informatie over het uitvoeren van een testfailover.

Zie [Overwegingen voor testfailovers voor Active Directory en DNS](site-recovery-active-directory.md) voor hulp bij het uitvoeren van testfailovers voor Active Directory en DNS.

## <a name="do-a-failover"></a>Een failover uitvoeren

1. Ga naar de Azure-portal en selecteer uw Recovery Services-kluis.
2. Selecteer het herstelplan dat is gemaakt voor de bestandsserveromgeving.
3. Selecteer **Failover**.
4. Selecteer het herstelpunt om de failover te starten.

Zie [Failover uitvoeren in Site Recovery](site-recovery-failover.md) voor meer informatie over het uitvoeren van een failover.