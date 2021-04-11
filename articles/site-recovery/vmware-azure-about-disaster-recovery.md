---
title: VMware-nood herstel met Azure Site Recovery
description: Dit artikel bevat een overzicht van nood herstel van virtuele VMware-machines naar Azure met behulp van de Azure Site Recovery-service.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 8e72d66bcf8398946b8901ef86666aa9aba34105
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579074"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>Over nood herstel van virtuele VMware-machines naar Azure

Dit artikel bevat een overzicht van herstel na nood gevallen voor on-premises virtuele VMware-machines naar Azure met behulp van de [Azure site Recovery](site-recovery-overview.md) -service.

## <a name="what-is-bcdr"></a>Wat is BCDR?

Een strategie voor bedrijfs continuïteit en herstel na nood gevallen (BCDR) zorgt ervoor dat uw bedrijf actief blijft. Tijdens geplande uitval tijd en onverwachte storingen houdt BCDR gegevens veilig en beschikbaar en zorgt u ervoor dat apps blijven werken. Naast platform BCDR-functies, zoals regionale koppeling en opslag met hoge Beschik baarheid, biedt Azure Recovery Services als een integraal onderdeel van uw BCDR-oplossing. Recovery Services omvatten: 

- [Azure backup](../backup/backup-overview.md) maakt back-ups van uw on-premises en Azure VM-gegevens. U kunt een back-up maken van een bestand en mappen, specifieke workloads of een volledige VM. 
- [Azure site Recovery](site-recovery-overview.md) biedt flexibiliteit en herstel na een nood geval voor apps en workloads die worden uitgevoerd op on-premises machines of Azure IaaS-vm's. Site Recovery organiseert de replicatie en verwerkt de failover naar Azure wanneer er storingen optreden. Ook wordt het herstel van Azure naar uw primaire site verwerkt. 

> [!NOTE]
> Met Site Recovery worden klant gegevens niet uit de doel regio verplaatst of opgeslagen, waarin nood herstel voor de bron machines is ingesteld. Klanten kunnen een Recovery Services kluis uit een andere regio selecteren als ze dat doen. De Recovery Services kluis bevat meta gegevens, maar geen werkelijke klant gegevens.

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Hoe werkt Site Recovery herstel na nood gevallen?

1. Nadat u Azure en uw on-premises site hebt voor bereid, kunt u replicatie voor uw on-premises machines instellen en inschakelen.
2. Site Recovery is de initiële replicatie van de computer in overeenstemming met uw beleids instellingen.
3. Na de eerste replicatie repliceert Site Recovery Delta wijzigingen naar Azure. 
4. Wanneer alles naar verwachting wordt gerepliceerd, voert u een nood herstel analyse uit.
    - De analyse helpt ervoor te zorgen dat failover werkt zoals verwacht, wanneer er echt behoefte ontstaat.
    - De analyse voert een testfailover uit zonder dat dit van invloed is op uw productie omgeving.
5. Als er een storing optreedt, voert u een volledige failover uit naar Azure. U kunt een failover uitvoeren voor een afzonderlijke computer of u kunt een herstel plan maken dat op hetzelfde moment een failover voor meerdere machines tot gevolg heeft.
6. Bij failover worden virtuele Azure-machines gemaakt op basis van de VM-gegevens in beheerde schijven of opslag accounts. Gebruikers kunnen apps en workloads van de Azure-VM blijven gebruiken
7. Als uw on-premises site weer beschikbaar is, kunt u een failback uitvoeren van Azure.
8. Nadat u eenmaal een failback hebt uitgevoerd en vanaf uw primaire site hebt gewerkt, kunt u de on-premises Vm's opnieuw repliceren naar Azure.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Hoe kan ik weet of mijn omgeving geschikt is voor herstel na nood gevallen naar Azure?

Site Recovery kunt elke werk belasting repliceren die wordt uitgevoerd op een ondersteunde VMware-VM of fysieke server. Hier vindt u de dingen die u nodig hebt om uw omgeving te controleren:

- Als u virtuele VMware-machines repliceert, voert u de juiste versies van VMware Virtualization-servers uit? [Hier controleren](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
- Zijn de machines die u wilt repliceren met een ondersteund besturings systeem? [Hier controleren](vmware-physical-azure-support-matrix.md#replicated-machines).
- Voor Linux-herstel na nood gevallen zijn machines met een ondersteund bestands systeem/gast opslag? [Hier controleren](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- Moeten de computers die u wilt repliceren voldoen aan de vereisten van Azure? [Hier controleren](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
- Wordt de netwerk configuratie ondersteund? [Hier controleren](vmware-physical-azure-support-matrix.md#network).
- Wordt uw opslag configuratie ondersteund? [Hier controleren](vmware-physical-azure-support-matrix.md#storage).


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>Wat moet ik instellen in azure voordat ik start?

In azure moet u het volgende voorbereiden:

1. Controleer of uw Azure-account machtigingen heeft voor het maken van Vm's in Azure.
2. Maak een Azure-netwerk waaraan Azure Vm's worden toegevoegd wanneer ze na een failover vanuit opslag accounts of beheerde schijven worden gemaakt.
3. Stel een Azure Recovery Services kluis in voor Site Recovery. De kluis bevindt zich in de Azure Portal en wordt gebruikt om uw Site Recovery-implementatie te implementeren, te configureren, te organiseren, te controleren en problemen op te lossen.

*Meer hulp nodig?*

Meer informatie over het instellen van Azure door [uw account te verifiëren](tutorial-prepare-azure.md#verify-account-permissions), een [netwerk](tutorial-prepare-azure.md#set-up-an-azure-network)te maken en [een kluis](tutorial-prepare-azure.md#create-a-recovery-services-vault)in te stellen.



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>Wat moet ik on-premises instellen voordat ik aan het begin ben?

On-premises is wat u moet doen:

1. U moet een aantal accounts instellen:

    - Als u virtuele VMware-machines repliceert, is er een account nodig om Site Recovery toegang te krijgen tot vCenter Server-of vSphere ESXi-hosts om Vm's automatisch te detecteren.
    - Er is een account nodig om de Site Recovery Mobility Service-agent te installeren op elke fysieke machine of VM die u wilt repliceren.

2. Als u dit nog niet hebt gedaan, moet u de compatibiliteit van uw VMware-infra structuur controleren.
3. Zorg ervoor dat u na een failover verbinding kunt maken met virtuele Azure-machines. U kunt RDP instellen op on-premises Windows-computers of SSH op Linux-machines.

*Meer hulp nodig?*
- Accounts voorbereiden voor [Automatische detectie](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) en voor [de installatie van de Mobility-service](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation).
- [Controleer of](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) uw VMware-instellingen compatibel zijn.
- [Bereid](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) u voor op om verbinding te maken in azure na een failover.
- [Lees dit artikel](concepts-on-premises-to-azure-networking.md)als u meer gedetailleerde informatie wilt over het instellen van IP-adres sering voor Azure-vm's na een failover.

## <a name="how-do-i-set-up-disaster-recovery"></a>Hoe kan ik herstel na nood gevallen instellen?

Nadat u uw Azure-en on-premises infra structuur hebt geïmplementeerd, kunt u herstel na nood gevallen instellen.

1. Bekijk de [architectuur van VMware naar Azure](vmware-azure-architecture.md)en de [fysieke naar Azure-architectuur](physical-azure-architecture.md)om inzicht te krijgen in de onderdelen die u moet implementeren. Er zijn een aantal onderdelen, dus is het belang rijk om te begrijpen hoe deze allemaal bij elkaar passen.
2. **Bron omgeving**: als eerste stap in de implementatie stelt u de replicatie bron omgeving in. U geeft op wat u wilt repliceren en waar u naar wilt repliceren.
3. **Configuratie server**: u moet een configuratie server instellen in uw on-premises bron omgeving:
    - De configuratie server is één on-premises computer. Voor nood herstel van VMware raden wij aan dat u deze implementeert als een VMware-VM die kan worden geïmplementeerd vanuit een download bare OVF-sjabloon.
    - De configuratie server coördineert de communicatie tussen on-premises en Azure
    - Een aantal andere onderdelen wordt uitgevoerd op de computer van de configuratie server.
        - De proces server ontvangt, optimaliseert en verzendt replicatie gegevens naar het cache-opslag account in Azure. Het behandelt ook de automatische installatie van de Mobility-service op computers die u wilt repliceren en voert automatische detectie van Vm's op VMware-servers uit.
        - Op de hoofddoelserver worden de replicatiegegevens tijdens de failback vanuit Azure afgehandeld.
    - Instellen omvat het registreren van de configuratie server in de kluis, het downloaden van MySQL-server en VMware-PowerCLI en het opgeven van de accounts die zijn gemaakt voor de automatische detectie en de installatie van de Mobility-service.
4. **Doel omgeving**: u kunt uw doel-Azure-omgeving instellen door uw Azure-abonnement en netwerk instellingen op te geven.
5. **Replicatie beleid**: u geeft op hoe replicatie moet plaatsvinden. Instellingen omvatten hoe vaak herstel punten worden gemaakt en opgeslagen, en of er app-consistente moment opnamen moeten worden gemaakt.
6. **Schakel replicatie in**. U schakelt replicatie in voor on-premises machines. Als u een account hebt gemaakt voor het installeren van de Mobility-service, wordt deze geïnstalleerd wanneer u replicatie voor een machine inschakelt. 

*Meer hulp nodig?*

- Voor een kort overzicht van deze stappen kunt u onze [VMware-zelf studie](vmware-azure-tutorial.md)en [fysieke server-stapsgewijze instructies](physical-azure-disaster-recovery.md)uitproberen.
- Meer [informatie](vmware-azure-set-up-source.md) over het instellen van uw bron omgeving.
- [Meer informatie over](vmware-azure-deploy-configuration-server.md) de vereisten voor de configuratie server en het instellen van de configuratie server met een OVF-sjabloon voor VMware-replicatie. Als u om een of andere reden geen sjabloon kunt gebruiken of als u fysieke servers wilt repliceren, [gebruikt u deze instructies](physical-azure-set-up-source.md#set-up-the-source-environment).
- Meer [informatie](vmware-azure-set-up-target.md) over doel instellingen.
- [Meer informatie](vmware-azure-set-up-replication.md) over het instellen van een replicatie beleid.
- [Meer informatie](vmware-azure-enable-replication.md) over het inschakelen van replicatie en het [uitsluiten](vmware-azure-exclude-disk.md) van schijven van replicatie.


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Er is iets fout gegaan, hoe kan ik problemen oplossen?

- Als eerste stap kunt u [de implementatie](site-recovery-monitor-and-troubleshoot.md) controleren om de status van gerepliceerde items, taken en infrastructuur problemen te controleren en eventuele fouten te identificeren.
- [Raadpleeg dit artikel](vmware-azure-troubleshoot-replication.md) voor veelvoorkomende fouten en tips voor probleem oplossing als u de initiële replicatie niet kunt volt ooien of de doorlopende replicatie werkt niet zoals verwacht.
- Als u problemen ondervindt met de automatische installatie van de Mobility-service op de computers die u wilt repliceren, raadpleegt u veelvoorkomende fouten in [dit artikel](vmware-azure-troubleshoot-push-install.md).
- Als failover niet werkt zoals verwacht, controleert u veelvoorkomende fouten in [dit artikel](site-recovery-failover-to-azure-troubleshoot.md).
- Als failback niet werkt, controleert u of het probleem in [dit artikel](vmware-azure-troubleshoot-failback-reprotect.md)wordt weer gegeven.



## <a name="next-steps"></a>Volgende stappen

Nu de replicatie is uitgevoerd, moet u [een nood herstel analyse uitvoeren](tutorial-dr-drill-azure.md) om ervoor te zorgen dat failover werkt zoals verwacht. 
