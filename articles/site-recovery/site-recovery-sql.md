---
title: Herstel na nood geval instellen voor SQL Server met Azure Site Recovery
description: In dit artikel wordt beschreven hoe u herstel na nood gevallen instelt voor SQL Server met behulp van SQL Server en Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 1b02b089fea7e883bdc6c58c7a2845af12b50a37
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011945"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Herstel na nood geval instellen voor SQL Server

In dit artikel wordt beschreven hoe u de SQL Server back-end van een toepassing kunt beveiligen. U kunt dit doen met een combi natie van SQL Server technologieën voor bedrijfs continuïteit en herstel na nood gevallen (BCDR) en [Azure site Recovery](site-recovery-overview.md).

Voordat u begint, moet u de SQL Server mogelijkheden voor herstel na nood gevallen begrijpen. Deze mogelijkheden omvatten:

* Failoverclustering
* AlwaysOn-beschikbaarheids groepen
* Databasespiegeling
* Back-upfunctie voor logboekbestanden
* Actieve geo-replicatie
* Automatische failover-groepen

## <a name="combining-bcdr-technologies-with-site-recovery"></a>BCDR Technologies combi neren met Site Recovery

Uw keuze van een BCDR-technologie om SQL Server-exemplaren te herstellen, moet zijn gebaseerd op uw herstel tijd (RTO) en Recovery Point Objective (RPO), zoals beschreven in de volgende tabel. Combi neer Site Recovery met de failoverbewerking van uw gekozen technologie om de herstel bewerking van uw hele toepassing te organiseren.

Implementatie type | BCDR-technologie | Verwacht RTO voor SQL Server | Verwachte RPO voor SQL Server |
--- | --- | --- | ---
SQL Server op een Azure-infra structuur als een service (IaaS) virtuele machine (VM) of on-premises.| [AlwaysOn-beschikbaarheidsgroep](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | De tijd die nodig is om de secundaire replica als primair te maken. | Omdat replicatie naar de secundaire replica asynchroon is, zijn er gegevens verloren gegaan.
SQL Server op een Azure IaaS-VM of on-premises.| [Failover Clustering (altijd op FCI)](/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | De tijd die nodig is voor failover tussen de knoop punten. | Omdat FCI altijd gebruikmaakt van gedeelde opslag, is dezelfde weer gave van het opslag exemplaar beschikbaar voor failover.
SQL Server op een Azure IaaS-VM of on-premises.| [Database spiegeling (modus met hoge prestaties)](/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | De tijd die nodig is om de service af te dwingen, waarbij gebruik wordt gemaakt van de spiegel server als een warme server voor stand-by. | Replicatie is asynchroon. De spiegel database kan enigszins vertraging oplopen achter de hoofd database. De vertraging is doorgaans klein. Maar het kan groot worden als het systeem van de hoofd-of spiegel server zwaar belast is.<br/><br/>De back-upfunctie voor logboeken kan een aanvulling zijn op database spiegeling. Het is een gunstig alternatief voor het asynchroon spie gelen van data bases.
SQL as a Service (PaaS) in Azure.<br/><br/>Dit implementatie type omvat afzonderlijke data bases en elastische Pools. | Actieve geo-replicatie | 30 seconden nadat de failover is geactiveerd.<br/><br/>Wanneer failover wordt geactiveerd voor een van de secundaire data bases, worden alle andere secundairen automatisch gekoppeld aan de nieuwe primaire. | RPO van vijf seconden.<br/><br/>Actieve geo-replicatie maakt gebruik van de always on-technologie van SQL Server. Hiermee worden doorgevoerde trans acties op de primaire data base asynchroon gerepliceerd naar een secundaire data base met behulp van snap shot-isolatie.<br/><br/>De secundaire gegevens worden gegarandeerd nooit gedeeltelijk trans acties.
SQL als PaaS geconfigureerd met actieve geo-replicatie in Azure.<br/><br/>Dit implementatie type bevat beheerde instanties, elastische Pools en afzonderlijke data bases. | Groep voor automatische failover | RTO van één uur. | RPO van vijf seconden.<br/><br/>Groepen voor automatische failover bieden de groeps semantiek boven op actieve geo-replicatie. Maar hetzelfde mechanisme voor asynchrone replicatie wordt gebruikt.
SQL Server op een Azure IaaS-VM of on-premises.| Replicatie met Azure Site Recovery | RTO is doorgaans minder dan 15 minuten. Lees voor meer informatie de [RTO-sla van site Recovery](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). | Een uur voor toepassings consistentie en vijf minuten voor crash consistentie. Als u op zoek bent naar een lagere RPO, moet u andere BCDR-technologieën gebruiken.

> [!NOTE]
> Enkele belang rijke overwegingen bij het beveiligen van SQL-workloads met Site Recovery:
> * Site Recovery is neutraal van toepassing. Site Recovery kunt elke versie van SQL Server beveiligen die is geïmplementeerd op een ondersteund besturings systeem. Zie de [ondersteunings matrix voor het herstellen](vmware-physical-azure-support-matrix.md#replicated-machines) van gerepliceerde machines voor meer informatie.
> * U kunt ervoor kiezen om Site Recovery te gebruiken voor elke implementatie op Azure, Hyper-V, VMware of fysieke infra structuur. Volg de richt lijnen aan het einde van dit artikel voor informatie over het [beveiligen van een SQL Server cluster](#how-to-help-protect-a-sql-server-cluster) met site Recovery.
> * Zorg ervoor dat de wijzigings frequentie voor gegevens die op de computer is waargenomen, binnen [site Recovery limieten](vmware-physical-azure-support-matrix.md#churn-limits)valt. De wijzigings hoeveelheid wordt gemeten in geschreven bytes per seconde. Voor computers waarop Windows wordt uitgevoerd, kunt u dit wijzigings aantal weer geven door het tabblad **prestaties** in taak beheer te selecteren. Bekijk de schrijf snelheid voor elke schijf.
> * Site Recovery ondersteunt replicatie van exemplaren van failoverclusters op Opslagruimten Direct. Zie [opslagruimten direct-replicatie inschakelen](azure-to-azure-how-to-enable-replication-s2d-vms.md)voor meer informatie.
> 
> Wanneer u uw SQL-workload naar Azure migreert, is het raadzaam om de [richt lijnen voor prestaties voor SQL Server op azure virtual machines](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)toe te passen.

## <a name="disaster-recovery-of-an-application"></a>Herstel na nood geval van een toepassing

Site Recovery organiseert de testfailover en de failover van uw hele toepassing met behulp van herstel plannen.

Er zijn enkele vereisten om ervoor te zorgen dat uw herstel plan volledig kan worden aangepast op basis van uw behoeften. Voor alle SQL Server implementaties is doorgaans een Active Directory-implementatie vereist. U hebt ook verbinding nodig voor uw toepassingslaag.

### <a name="step-1-set-up-active-directory"></a>Stap 1: Active Directory instellen

Stel Active Directory op de secundaire herstel site in voor SQL Server om goed te kunnen worden uitgevoerd.

* **Kleine onderneming**: er zijn een klein aantal toepassingen en één domein controller voor de on-premises site. Als u een failover wilt uitvoeren voor de hele site, gebruikt u Site Recovery-replicatie. Met deze service wordt de domein controller gerepliceerd naar het secundaire Data Center of naar Azure.
* **Gemiddeld naar groot bedrijf**: u moet mogelijk extra domein controllers instellen.
  - Als u een groot aantal toepassingen hebt, een Active Directory forest hebt en een failover wilt uitvoeren op basis van een toepassing of werk belasting, stelt u een andere domein controller in het secundaire Data Center of in Azure in.
  -  Als u AlwaysOn-beschikbaarheids groepen gebruikt om naar een externe site te herstellen, stelt u een andere domein controller in op de secundaire site of in Azure. Deze domein controller wordt gebruikt voor het herstelde SQL Server-exemplaar.

In de instructies in dit artikel wordt ervan uitgegaan dat er een domein controller beschikbaar is op de secundaire locatie. Zie de procedures voor het [beveiligen van Active Directory met site Recovery](site-recovery-active-directory.md)voor meer informatie.

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>Stap 2: zorg voor de connectiviteit met andere lagen

Wanneer de databaserol actief is in de Azure-doel regio, moet u verbinding hebben met de toepassing en de weblaags. Neem de nodige stappen vooraf om de connectiviteit met de testfailover te valideren.

Zie de volgende voor beelden om te begrijpen hoe u toepassingen kunt ontwerpen voor connectiviteits overwegingen:

* [Een toepassing ontwerpen voor nood herstel in de Cloud](../azure-sql/database/designing-cloud-solutions-for-disaster-recovery.md)
* [Strategieën voor nood herstel voor elastische Pools](../azure-sql/database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>Stap 3: samenwerkt met Always on, actieve geo-replicatie en groepen van automatische failover

BCDR Technologies always on, actieve groepen met geo-replicatie en automatische failover hebben secundaire replica's van SQL Server uitgevoerd in de Azure-doel regio. De eerste stap voor de failover van de toepassing is om deze replica als primair op te geven. Bij deze stap wordt ervan uitgegaan dat u al een domein controller in de secundaire hebt. De stap is mogelijk niet nodig als u ervoor kiest om een automatische failover uit te voeren. Failover van uw web-en toepassings lagen alleen als de data base-failover is voltooid.

> [!NOTE]
> Als u de SQL-machines met Site Recovery wilt beveiligen, hoeft u alleen maar een herstel groep van deze machines te maken en de failover in het herstel plan toe te voegen.

[Maak een herstel plan](site-recovery-create-recovery-plans.md) met virtuele machines voor de toepassing en de weblaag. De volgende stappen laten zien hoe u een failover van de gegevenslaag kunt toevoegen:

1. Importeer de scripts voor een failover van de SQL-beschikbaarheids groep in zowel een [Resource Manager-virtuele machine](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) als een [klassieke virtuele machine](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1). Importeer de scripts in uw Azure Automation-account.

    [![Afbeelding van het logo ' implementeren naar Azure '](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Voeg het ASR-SQL-FailoverAG-script toe als een pre-actie van de eerste groep van het herstel plan.

1. Volg de instructies in het script om een Automation-variabele te maken. Deze variabele bevat de naam van de beschikbaarheids groepen.

### <a name="step-4-conduct-a-test-failover"></a>Stap 4: een testfailover uitvoeren

Sommige BCDR-technologieën zoals SQL always on bieden geen systeem eigen ondersteuning voor de testfailover. We raden u aan de volgende aanpak *alleen te gebruiken bij het gebruik van dergelijke technologieën*.

1. Stel [Azure backup](../backup/backup-azure-vms-first-look-arm.md) in op de virtuele machine die als host fungeert voor de replica van de beschikbaarheids groep in Azure.

1. Voordat de testfailover van het herstel plan wordt geactiveerd, herstelt u de virtuele machine op basis van de back-up die in de vorige stap is gemaakt.

    ![Scherm opname van een venster voor het herstellen van een configuratie uit Azure Backup](./media/site-recovery-sql/restore-from-backup.png)

1. [Dwing een quorum](/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) af in de VM die is teruggezet vanuit een back-up.

1. Werk het IP-adres van de listener bij als een adres dat beschikbaar is in het netwerk van de testfailover.

    ![Dialoog venster scherm opname van regel venster en eigenschappen van IP-adres](./media/site-recovery-sql/update-listener-ip.png)

1. De listener online zetten.

    ![Scherm opname van het venster met de naam Content_AG server namen en-statussen worden weer gegeven](./media/site-recovery-sql/bring-listener-online.png)

1. Zorg ervoor dat de load balancer in het failover-netwerk één IP-adres heeft, van de front-end-IP-adres groep die overeenkomt met elke beschikbaarheids groep-listener en met de SQL Server virtuele machine in de back-end-pool.

     ![Scherm afbeelding van het venster met de titel ' SQL-AlwaysOn-LB-front-end IP-adres groep](./media/site-recovery-sql/create-load-balancer1.png)

    ![Scherm afbeelding van het venster met de titel ' SQL-AlwaysOn-LB-back-end-IP-pool](./media/site-recovery-sql/create-load-balancer2.png)

1. Voeg in latere herstel groepen de failover van uw toepassingslaag toe, gevolgd door uw weblaag voor dit herstel plan.

1. Voer een testfailover van het herstel plan uit om end-to-end failover van uw toepassing te testen.

## <a name="steps-to-do-a-failover"></a>Stappen voor het uitvoeren van een failover

Nadat u het script in stap 3 hebt toegevoegd en in stap 4 hebt gevalideerd, kunt u een failover van het herstel plan uitvoeren dat is gemaakt in stap 3.

De stappen voor het uitvoeren van de failover voor de toepassing en de weblaag moeten hetzelfde zijn in zowel de testfailover als voor failover-herstel plannen.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>Een SQL Server cluster helpen beveiligen

Voor een cluster met SQL Server Standard Edition of SQL Server 2008 R2 wordt u aangeraden Site Recovery-replicatie te gebruiken om SQL Server te helpen beveiligen.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure naar Azure en on-premises naar Azure

Site Recovery biedt geen ondersteuning voor gast clusters bij het repliceren naar een Azure-regio. SQL Server Standard Edition biedt ook geen voordelige oplossing voor herstel na nood gevallen. In dit scenario raden we u aan om het SQL Server-cluster te beveiligen met een zelfstandig SQL Server-exemplaar op de primaire locatie en te herstellen in het secundaire.

1. Een extra zelfstandig SQL Server-exemplaar configureren op de primaire Azure-regio of op een on-premises site.

1. Configureer het exemplaar om als een mirror te fungeren voor de data bases die u wilt beveiligen. Configureer mirroring in de modus voor hoge beveiliging.

1. Configureer Site Recovery op de primaire site voor [Azure](azure-to-azure-tutorial-enable-replication.md)-, [Hyper-V](./hyper-v-azure-tutorial.md)-of [VMware-vm's en fysieke servers](./vmware-azure-tutorial.md).

1. Gebruik Site Recovery replicatie om het nieuwe SQL Server exemplaar te repliceren naar de secundaire site. Omdat het een mirror-kopie met hoge beveiliging is, wordt deze gesynchroniseerd met het primaire cluster, maar gerepliceerd met Site Recovery replicatie.

   ![Afbeelding van een standaard cluster waarin de relatie en stroom wordt weer gegeven tussen een primaire site, Site Recovery en Azure](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Overwegingen voor failback

Voor SQL Server Standard clusters vereist failback na een niet-geplande failover een SQL Server back-up en herstel. Deze bewerking wordt uitgevoerd vanaf het spiegel exemplaar naar het oorspronkelijke cluster, waarbij de mirror opnieuw wordt ingesteld.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>Hoe krijgt SQL Server een licentie wanneer deze met Site Recovery wordt gebruikt?

Site Recovery replicatie voor SQL Server valt onder het voor deel van het Software Assurance-herstel na nood gevallen. Deze dekking is van toepassing op alle Site Recovery scenario's: on-premises naar Azure nood herstel en cross-Region Azure IaaS-herstel na nood gevallen. Zie [Azure site Recovery prijzen](https://azure.microsoft.com/pricing/details/site-recovery/) voor meer informatie.

### <a name="will-site-recovery-support-my-sql-server-version"></a>Site Recovery ondersteuning voor mijn SQL Server versie?

Site Recovery is neutraal van toepassing. Site Recovery kunt elke versie van SQL Server beveiligen die is geïmplementeerd op een ondersteund besturings systeem. Zie de [ondersteunings matrix voor het herstellen](vmware-physical-azure-support-matrix.md#replicated-machines) van gerepliceerde machines voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [site Recovery architectuur](./azure-to-azure-architecture.md).
* Meer informatie over [oplossingen met hoge Beschik baarheid](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions) voor herstel in een secundaire Azure-regio vindt u in azure. SQL Server
* Voor SQL Database leert u meer over de opties voor [bedrijfs continuïteit](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) en [hoge Beschik baarheid](../azure-sql/database/high-availability-sla.md) voor herstel in een secundaire Azure-regio.
* Meer informatie over de [Opties voor hoge Beschik baarheid](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions) voor herstel in azure virtual machines vindt u in het on-premises SQL Server.