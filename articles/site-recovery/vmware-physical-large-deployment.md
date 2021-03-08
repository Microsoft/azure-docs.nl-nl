---
title: VMware/fysieke nood herstel met Azure Site Recovery schalen
description: Meer informatie over het instellen van herstel na nood gevallen voor Azure voor een groot aantal on-premises virtuele VMware-machines of fysieke servers met Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: cc87429f269fba5083b87e2c328f0e21de9707ff
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454344"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Herstel na nood geval op schaal instellen voor virtuele VMware-machines/fysieke servers

In dit artikel wordt beschreven hoe u herstel na nood gevallen instelt voor Azure voor grote aantallen (> 1000) van on-premises virtuele VMware-machines of fysieke servers in uw productie omgeving, met behulp van de [Azure site Recovery](site-recovery-overview.md) -service.


## <a name="define-your-bcdr-strategy"></a>Uw BCDR-strategie definiëren

Als onderdeel van uw strategie voor bedrijfs continuïteit en herstel na nood gevallen (BCDR) kunt u herstel punt doelstellingen (Rpo's) en de beoogde tijdstippen (Rto's) voor uw zakelijke apps en werk belastingen definiëren. RTO meet de duur van tijd en service niveau waarbinnen een zakelijke app of proces moet worden hersteld en beschikbaar moeten zijn om continuïteits problemen te voor komen.
- Site Recovery biedt continue replicatie voor virtuele VMware-machines en fysieke servers en een [Sla](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) voor RTO.
- Bij het plannen van een grootschalig herstel na nood gevallen voor VMware-Vm's en het afhandelen van de Azure-resources die u nodig hebt, kunt u een RTO-waarde opgeven die wordt gebruikt voor de berekening van capaciteit.


## <a name="best-practices"></a>Aanbevolen procedures

Enkele algemene aanbevolen procedures voor grootschalig herstel na nood gevallen. Deze aanbevolen procedures worden uitgebreid beschreven in de volgende secties van het document.

- **Doel vereisten identificeren**: geschatte capaciteit en resource behoeften in azure voordat u herstel na nood gevallen instelt.
- **Site Recovery onderdelen plannen**: bepaal welke site Recovery onderdelen (configuratie server, proces servers) u nodig hebt om te voldoen aan de geschatte capaciteit.
- **Stel een of meer scale-out proces servers** in: gebruik niet de proces server die standaard op de configuratie server wordt uitgevoerd. 
- **Voer de nieuwste updates uit**: het site Recovery-team brengt regel matig nieuwe versies van site Recovery onderdelen uit en u moet ervoor zorgen dat u de nieuwste versies uitvoert. Houd bij het bijhouden van [wat er nieuw](site-recovery-whats-new.md) is voor updates en [Schakel updates in en installeer](service-updates-how-to.md) deze.
- **Proactief controleren**: wanneer u herstel na nood gevallen uitvoert, moet u de status en status van gerepliceerde machines en infrastructuur resources proactief controleren.
- **Inzoomen voor herstel na nood gevallen**: u moet regel matig herstel na nood geval uitvoeren. Dit is niet van invloed op uw productie omgeving, maar helpt ervoor te zorgen dat failover naar Azure naar behoren werkt als dat nodig is.



## <a name="gather-capacity-planning-information"></a>Informatie over capaciteits planning verzamelen

Verzamel informatie over uw on-premises omgeving, om u te helpen bij het beoordelen en ramen van de capaciteits behoeften van uw doel (Azure).
- Voor VMware voert u de Deployment Planner voor virtuele VMware-machines uit.
- Verzamel de informatie voor fysieke servers hand matig.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>De Deployment Planner uitvoeren voor VMware-Vm's

De Deployment Planner helpt u bij het verzamelen van informatie over uw VMware on-premises omgeving.

- Voer de Deployment Planner uit tijdens een periode die een typisch verloop vormt voor uw Vm's. Hierdoor worden nauw keurige schattingen en aanbevelingen gegenereerd.
- U wordt aangeraden de Deployment Planner op de computer van de configuratie server uit te voeren, omdat de planner de door Voer berekent van de server waarop deze wordt uitgevoerd. Meer [informatie](site-recovery-vmware-deployment-planner-run.md#get-throughput) over het meten van door voer.
- Als u nog geen configuratie server hebt ingesteld:
    - [Bekijk een overzicht](vmware-physical-azure-config-process-server-overview.md) van site Recovery onderdelen.
    - [Stel een configuratie server](vmware-azure-deploy-configuration-server.md)in om de Deployment planner erop uit te voeren.

Voer de planner vervolgens als volgt uit:

1. [Meer informatie over](site-recovery-deployment-planner.md) de Deployment planner. U kunt de nieuwste versie downloaden van de portal of [deze rechtstreeks downloaden](https://aka.ms/asr-deployment-planner).
2. Bekijk de [vereisten](site-recovery-deployment-planner.md#prerequisites) en de [meest recente updates](site-recovery-deployment-planner-history.md) voor de Deployment planner en [down load en pak](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) het hulp programma uit.
3. [Voer de Deployment planner](site-recovery-vmware-deployment-planner-run.md) uit op de configuratie server.
4. [Genereer een rapport](site-recovery-vmware-deployment-planner-run.md#generate-report) om ramingen en aanbevelingen samen te vatten.
5. Analyseer de [aanbevelingen](site-recovery-vmware-deployment-planner-analyze-report.md) van het rapport en de [kosten ramingen](site-recovery-vmware-deployment-planner-cost-estimation.md).

>[!NOTE]
> Het hulp programma is standaard geconfigureerd voor het profiel en genereert een rapport voor Maxi maal 1000 Vm's. U kunt deze limiet wijzigen door de sleutel waarde MaxVMsSupported in het ASRDeploymentPlanner.exe.config-bestand te verhogen.

## <a name="plan-target-azure-requirements-and-capacity"></a>Vereisten en capaciteit van doel (Azure) plannen

Met uw verzamelde schattingen en aanbevelingen kunt u de doel resources en-capaciteit plannen. Als u de Deployment Planner voor VMware-Vm's hebt uitgevoerd, kunt u een aantal van de aanbevelingen van het [rapport](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) gebruiken om u te helpen.

- **Compatibele vm's**: gebruik dit nummer om het aantal vm's te identificeren dat gereed is voor herstel na nood gevallen naar Azure. Aanbevelingen over netwerk bandbreedte en Azure cores zijn gebaseerd op dit aantal.
- **Vereiste netwerk bandbreedte**: Noteer de band breedte die u nodig hebt voor de Delta replicatie van compatibele vm's. 
    - Wanneer u de planner uitvoert, geeft u de gewenste RPO in minuten op. De aanbevelingen tonen u de band breedte die nodig is om te voldoen aan de RPO 100% en 90% van de tijd. 
    - Bij de aanbevelingen voor netwerk bandbreedte wordt rekening gehouden met de band breedte die nodig is voor het totale aantal configuratie servers en proces servers dat wordt aanbevolen in de planner.
- **Vereiste Azure-kernen**: Noteer het aantal kern geheugens dat u nodig hebt in de Azure-doel regio, op basis van het aantal compatibele vm's. Als u niet genoeg kernen hebt, kunt u bij failover Site Recovery de vereiste Azure-Vm's niet maken.
- **Aanbevolen VM-Batch grootte**: de aanbevolen Batch grootte is gebaseerd op de mogelijkheid om de initiële replicatie voor de batch binnen 72 uur standaard te volt ooien, terwijl een RPO van 100% wordt voltooid. De waarde van het uur kan worden gewijzigd.

U kunt deze aanbevelingen gebruiken om Azure-resources, netwerk bandbreedte en VM-batch verwerking te plannen.

## <a name="plan-azure-subscriptions-and-quotas"></a>Azure-abonnementen en-quota's plannen

We willen er zeker van zijn dat beschik bare quota's in het doel abonnement voldoende zijn voor het verwerken van de failover.

**Taak** | **Details** | **Actie**
--- | --- | ---
**Kern geheugens controleren** | Als de kernen in het beschik bare quotum niet gelijk zijn aan of groter zijn dan het totale aantal doelen op het moment van failover, mislukken failovers. | Voor virtuele VMware-machines controleert u of er voldoende kern geheugens zijn in het doel abonnement om te voldoen aan de Deployment Planner core-aanbeveling.<br/><br/> Controleer voor fysieke servers of Azure-kernen voldoen aan uw hand matige schattingen.<br/><br/> Als u quota's wilt controleren, klikt u in het Azure Portal >- **abonnement** op **gebruik en quota's**.<br/><br/> Meer [informatie](../azure-portal/supportability/resource-manager-core-quotas-request.md) over het verhogen van quota's.
**Failover-limieten controleren** | Het aantal failovers mag overschrijdt Site Recovery failover-limieten. |  Als failovers de limieten overschrijden, kunt u abonnementen toevoegen en failover uitvoeren naar meerdere abonnementen of het quotum verhogen voor een abonnement. 


### <a name="failover-limits"></a>Failover-limieten

Met de limieten wordt het aantal failovers aangegeven dat wordt ondersteund door Site Recovery binnen één uur, uitgaande van drie schijven per computer.

Wat betekent er in overeenstemming? Voor het starten van een virtuele Azure-machine moeten sommige Stuur Programma's worden gestart in de opstart status van Azure en kunnen services zoals DHCP zo worden ingesteld dat deze automatisch worden gestart.
- Computers die voldoen aan deze instellingen, zijn al aanwezig.
- Voor computers waarop Windows wordt uitgevoerd, kunt u proactief controleren op naleving en deze indien nodig in overeenstemming brengen. [Meer informatie](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- Linux-machines worden alleen in overeenstemming gebracht op het moment van failover.

**Machine voldoet aan Azure?** | **Limieten voor Azure-VM'S (failover van beheerde schijf)**
--- | --- 
Ja | 2000
Nee | 1000

- Bij limieten wordt ervan uitgegaan dat er mini maal andere taken worden uitgevoerd in de doel regio voor het abonnement.
- Sommige Azure-regio's zijn kleiner en hebben mogelijk iets lagere limieten.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Infra structuur-en VM-connectiviteit plannen

Na een failover naar Azure hebt u uw workloads nodig om te werken zoals ze on-premises zijn en om gebruikers toegang te geven tot werk belastingen die worden uitgevoerd op de virtuele machines van Azure.

- Meer [informatie](site-recovery-active-directory.md#test-failover-considerations) over het mislukken van uw Active Directory of een on-premises infra structuur van DNS naar Azure.
- [Meer informatie](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) over het voorbereiden van verbinding met virtuele Azure-machines na een failover.



## <a name="plan-for-source-capacity-and-requirements"></a>De bron capaciteit en-vereisten plannen

Het is belang rijk dat u voldoende configuratie servers en scale-out proces servers hebt om te voldoen aan de capaciteits vereisten. Wanneer u begint met grootschalige implementatie, start u met één configuratie server en één scale-out proces server. Voeg extra servers toe als u de vereiste limieten hebt bereikt.

>[!NOTE]
> Voor virtuele VMware-machines worden met de Deployment Planner enkele aanbevelingen gedaan over de configuratie-en proces servers die u nodig hebt. U wordt aangeraden de tabellen die zijn opgenomen in de volgende procedures te gebruiken, in plaats van de Deployment Planner aanbeveling te volgen. 


## <a name="set-up-a-configuration-server"></a>Een configuratie server instellen
 
De capaciteit van de configuratie server wordt beïnvloed door het aantal computers dat repliceert, en niet op basis van het verloop tempo van gegevens. Als u wilt weten of u aanvullende configuratie servers nodig hebt, gebruikt u deze gedefinieerde VM-limieten.

**CPU** | **Geheugen** | **Cacheschijf** | **Limiet van gerepliceerde machines**
 --- | --- | --- | ---
8 vCPU's<br> 2 sockets * 4 kernen @ 2,5 GHz | 16 GB | 600 GB | Maxi maal 550 computers<br> Er wordt van uitgegaan dat elke machine drie schijven van 100 GB elk heeft.

- Deze limieten zijn gebaseerd op een configuratie server die is ingesteld met behulp van een OVF-sjabloon.
- Bij de limieten wordt ervan uitgegaan dat u niet de proces server gebruikt die standaard op de configuratie server wordt uitgevoerd.

Als u een nieuwe configuratie server moet toevoegen, volgt u deze instructies:

- [Stel een configuratie server](vmware-azure-deploy-configuration-server.md) in voor nood herstel voor VMware-vm's met behulp van een OVF-sjabloon.
- [Stel een configuratie server](physical-azure-set-up-source.md) hand matig in voor fysieke servers of voor VMware-implementaties die geen OVF-sjabloon kunnen gebruiken.

Houd bij het instellen van een configuratie server rekening met het volgende:

- Wanneer u een configuratie server instelt, is het belang rijk dat u rekening houdt met het abonnement en de kluis waarin deze zich bevindt, omdat deze niet mogen worden gewijzigd na de installatie. Als u de kluis wilt wijzigen, moet u de koppeling tussen de configuratie server en de kluis ongedaan maken. Hiermee wordt de replicatie van virtuele machines in de kluis gestopt.
- Als u een configuratie server met meerdere netwerk adapters wilt instellen, moet u dit doen tijdens het instellen. U kunt dit niet doen na de registratie van de configuratie server in de kluis.

## <a name="set-up-a-process-server"></a>Een proces server instellen

De capaciteit van de proces server wordt beïnvloed door de gegevens verloop tarieven en niet op het aantal machines dat is ingeschakeld voor replicatie.

- Voor grote implementaties moet u altijd ten minste één scale-out proces server hebben.
- Als u wilt weten of u extra servers nodig hebt, gebruikt u de volgende tabel.
- U wordt aangeraden een server met de hoogste specificatie toe te voegen. 


**CPU** | **Geheugen** | **Cacheschijf** | **Verloop frequentie**
 --- | --- | --- | --- 
12 vCPU's<br> 2 sockets * 6 kernen @ 2,5 GHz | 24 GB | 1 GB | Tot 2 TB per dag

Stel de proces server als volgt in:

1. Controleer de [vereisten](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Installeer de server in de [Portal](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)of vanaf de [opdracht regel](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Configureer gerepliceerde machines voor het gebruik van de nieuwe server. Als u al computers hebt gerepliceerd:
    - U kunt een volledige werk belasting van de proces server [verplaatsen](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) naar de nieuwe proces server.
    - U kunt ook specifieke Vm's [verplaatsen](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) naar de nieuwe proces server.



## <a name="enable-large-scale-replication"></a>Grootschalige replicatie inschakelen

Nadat u de capaciteit hebt gepland en de vereiste onderdelen en infra structuur hebt geïmplementeerd, schakelt u de replicatie in voor een groot aantal virtuele machines.

1. Machines in batches sorteren. U schakelt replicatie in voor Vm's binnen een batch en gaat vervolgens verder met de volgende batch.

    - Voor virtuele VMware-machines kunt u de [Aanbevolen VM-Batch grootte](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) in het Deployment planner-rapport gebruiken.
    - Voor fysieke machines raden we u aan om batches te identificeren op basis van machines met een vergelijk bare grootte en hoeveelheid gegevens en over de beschik bare netwerk doorvoer. Het doel is om batch machines te maken die hun initiële replicatie waarschijnlijk in ongeveer dezelfde hoeveelheid tijd kunnen volt ooien.
    
2. Als het schijf verloop voor een machine hoog is of limieten in de implementatie thePlanner overschrijdt, kunt u niet-essentiële bestanden verplaatsen die u niet hoeft te repliceren (zoals logboek dumps of tijdelijke bestanden) van de computer. Voor virtuele VMware-machines kunt u deze bestanden verplaatsen naar een afzonderlijke schijf en vervolgens [die schijf uitsluiten](vmware-azure-exclude-disk.md) van replicatie.
3. Voordat u replicatie inschakelt, controleert u of de computers voldoen aan de [replicatie vereisten](vmware-physical-azure-support-matrix.md#replicated-machines).
4. Configureer een replicatie beleid voor [virtuele VMware-machines](vmware-azure-set-up-replication.md#create-a-policy) of [fysieke servers](physical-azure-disaster-recovery.md#create-a-replication-policy).
5. Schakel replicatie in voor [virtuele VMware-machines](vmware-azure-enable-replication.md) of [fysieke servers](physical-azure-disaster-recovery.md#enable-replication). Dit is de initiële replicatie voor de geselecteerde machines.

## <a name="monitor-your-deployment"></a>Uw implementatie bewaken

Nadat u de replicatie voor de eerste batch met Vm's hebt uitgeschakeld, start u uw implementatie als volgt:  

1. Een nood herstel beheerder toewijzen om de status van gerepliceerde machines te controleren.
2. [Gebeurtenissen bewaken](site-recovery-monitor-and-troubleshoot.md) voor gerepliceerde items en de infra structuur.
3. [De status](vmware-physical-azure-monitor-process-server.md) van uw scale-out proces servers bewaken.
4. Meld u aan om [e-mail meldingen](./site-recovery-monitor-and-troubleshoot.md#subscribe-to-email-notifications) voor gebeurtenissen op te halen, zodat u gemakkelijker kunt controleren.
5. Voer regel matig [nood herstel analyses](site-recovery-test-failover-to-azure.md)uit om ervoor te zorgen dat alles werkt zoals verwacht.


## <a name="plan-for-large-scale-failovers"></a>Plannen voor grootschalige failovers

Bij een nood geval moet u mogelijk een failover uitvoeren van een groot aantal machines/workloads naar Azure. Dit type gebeurtenis wordt als volgt voor bereid.

Ga als volgt te voor bereiden voor failover:

- [Bereid uw infra structuur en vm's](#plan-infrastructure-and-vm-connectivity) voor, zodat uw workloads na een failover beschikbaar zijn, zodat gebruikers toegang hebben tot de Azure-vm's.
- Let op de [failover-limieten](#failover-limits) eerder in dit document. Zorg ervoor dat de failover binnen deze limieten valt.
- Voer regel matig [nood herstel oefeningen](site-recovery-test-failover-to-azure.md)uit. Zoomt in op de volgende onderwerpen:
    - Zoek hiaten in uw implementatie voor een failover.
    - U kunt end-to-end-RTO voor uw apps ramen.
    - Een schatting maken van end-to-end RPO voor uw workloads.
    - Conflicten met het IP-adres bereik identificeren.
    - Bij het uitvoeren van oefeningen raden wij u aan om geen productie netwerken voor boren te gebruiken en testfailover na elke detail analyse op te schonen.

Als u een grootschalige failover wilt uitvoeren, raden we het volgende aan:

1. Herstel plannen maken voor failover van werk belasting.
    - Elk herstel plan kan failover van Maxi maal 100 computers activeren.
    - [Meer informatie](recovery-plan-overview.md) over herstel plannen.
2. Azure Automation runbook-scripts toevoegen aan herstel plannen om hand matige taken in azure te automatiseren. Veelvoorkomende taken zijn onder andere het configureren van load balancers, het bijwerken van DNS, enzovoort. [Meer informatie](site-recovery-runbook-automation.md)
2. Voor failover moet u Windows-computers voorbereiden zodat deze voldoen aan de Azure-omgeving. [Failover-limieten](#plan-azure-subscriptions-and-quotas) zijn hoger voor computers die voldoen aan het oog. Meer [informatie](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) over runbooks.
4.  Activeer failover met de Power shell [-cmdlet start-AzRecoveryServicesAsrPlannedFailoverJob](/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob) , samen met een herstel plan.



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Site Recovery bewaken](site-recovery-monitor-and-troubleshoot.md)
