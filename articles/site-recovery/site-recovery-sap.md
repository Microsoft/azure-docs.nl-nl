---
title: Herstel na nood gevallen voor SAP NetWeaver instellen met Azure Site Recovery
description: Meer informatie over het instellen van herstel na nood gevallen voor SAP NetWeaver met Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: e639762cd1adb7bbbc3fb2ec31f4ce52710e46f9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711940"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Herstel na nood geval instellen voor een implementatie van SAP NetWeaver met meerdere lagen

De meeste implementaties met een hoge grootte en middel grote SAP-oplossing gebruiken een vorm van herstel na nood gevallen. Het belang van robuuste en testable nood herstel oplossingen is toegenomen naarmate er meer essentiële bedrijfs processen worden verplaatst naar toepassingen als SAP. Azure Site Recovery is getest en geïntegreerd met SAP-toepassingen. Site Recovery overschrijdt de mogelijkheden van de meeste on-premises oplossingen voor herstel na nood gevallen en op een lagere total cost of ownership dan concurrerende oplossingen.

Met Site Recovery kunt u het volgende doen:
* Schakel de beveiliging in van SAP NetWeaver en niet-NetWeaver-productie toepassingen die on-premises worden uitgevoerd door onderdelen naar Azure te repliceren.
* Schakel de beveiliging in van SAP NetWeaver en niet-NetWeaver-productie toepassingen die worden uitgevoerd op Azure door onderdelen te repliceren naar een ander Azure-Data Center.
* Vereenvoudig de cloudmigratie door Site Recovery te gebruiken om uw SAP-implementatie te migreren naar Azure.
* Vereenvoudig SAP-project upgrades, testen en prototypen door een productie kloon op aanvraag te maken voor het testen van SAP-toepassingen.

U kunt de implementaties van SAP NetWeaver beveiligen met behulp van [Azure site Recovery](site-recovery-overview.md). In dit artikel worden aanbevolen procedures beschreven voor het beveiligen van een SAP NetWeaver-implementatie met drie lagen wanneer u naar een ander Azure-Data Center repliceert met behulp van Site Recovery. In het artikel worden ondersteunde scenario's en configuraties beschreven en wordt uitgelegd hoe u testfailover (nood herstel analyse) en werkelijke failovers uitvoert.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u weet hoe u de volgende taken moet uitvoeren:

* [Een virtuele machine repliceren naar Azure](./azure-to-azure-tutorial-enable-replication.md)
* [Een herstel netwerk ontwerpen](./azure-to-azure-about-networking.md)
* [Een testfailover naar Azure uitvoeren](./azure-to-azure-tutorial-dr-drill.md)
* [Een failover naar Azure uitvoeren](site-recovery-failover.md)
* [Een domein controller repliceren](site-recovery-active-directory.md)
* [Een SQL Server-exemplaar repliceren](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Ondersteunde scenario's

U kunt Site Recovery gebruiken voor het implementeren van een oplossing voor herstel na nood geval in de volgende scenario's:
* U hebt SAP-systemen die worden uitgevoerd in een Azure-Data Center en u repliceert ze naar een ander Azure-Data Center (Azure-naar-Azure-nood herstel). 
   Zie [Azure-to-Azure-replicatie architectuur](./azure-to-azure-architecture.md)voor meer informatie.
* U hebt SAP-systemen die on-premises op VMware-servers (of fysieke) worden uitgevoerd. U repliceert ook de SAP-systemen naar een nood herstel site in een Azure-Data Center (nood herstel van VMware naar Azure). 
   Voor dit scenario zijn enkele extra onderdelen vereist. Zie voor meer informatie [VMware-naar-Azure-replicatie architectuur](./vmware-azure-architecture.md).
* U hebt SAP-systemen die worden uitgevoerd op Hyper-V on-premises. U repliceert ook de SAP-systemen naar een nood herstel site in een Azure-Data Center (Hyper-V-naar-Azure-nood herstel).
   Voor dit scenario zijn enkele extra onderdelen vereist. Zie voor meer informatie [Hyper-V-naar-Azure-replicatie architectuur](./hyper-v-azure-architecture.md).

In dit artikel gebruiken we een scenario voor herstel na nood gevallen van **Azure naar Azure** . In het scenario wordt de SAP-functionaliteit voor herstel na nood gevallen van Site Recovery beschreven. Omdat Site Recovery replicatie niet specifiek is voor een toepassing, wordt verwacht dat het proces dat wordt beschreven ook van toepassing is op andere scenario's.

### <a name="required-foundation-services"></a>Vereiste Foundation-Services
In het scenario dat in dit artikel wordt besproken, worden de volgende Foundation-Services geïmplementeerd:
* Azure ExpressRoute of Azure VPN Gateway
* Ten minste één Azure Active Directory domein controller en DNS-server, die wordt uitgevoerd in azure

U wordt aangeraden deze infra structuur in te stellen voordat u Site Recovery implementeert.

## <a name="reference-sap-application-deployment"></a>Naslag informatie over SAP-toepassingen implementeren

In deze referentie architectuur wordt SAP NetWeaver uitgevoerd in een Windows-omgeving op Azure met hoge Beschik baarheid. Deze architectuur wordt geïmplementeerd met specifieke groottes van virtuele machines (VM) die u kunt wijzigen om tegemoet te komen aan de behoeften van uw organisatie.

![Diagram van een typisch SAP-implementatie patroon](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Overwegingen voor herstel na noodgeval

Voor herstel na nood gevallen moet u een failover naar een secundaire regio kunnen uitvoeren. Voor elke laag wordt een andere strategie gebruikt voor herstel na nood gevallen.

#### <a name="vms-running-sap-web-dispatcher-pools"></a>Vm's met SAP Web dispatcher-Pools

Het onderdeel webdispatcher werkt als load balancer voor SAP-verkeer tussen de SAP-toepassings servers. Azure Load Balancer implementeert de installatie van de parallelle webdispatcher om hoge Beschik baarheid te realiseren voor het onderdeel Web dispatcher. Webdispatcher gebruikt een Round-Robin configuratie voor de distributie van HTTP (S)-verkeer tussen de beschik bare webdispatchers in de groep balancers.

#### <a name="vms-running-application-servers-pools"></a>Vm's die toepassings server groepen uitvoeren
De SMLG-trans actie beheert aanmeldings groepen voor ABAP-toepassings servers. Hierbij wordt gebruikgemaakt van de functie voor taak verdeling in de Message server van de centrale Services om de werk belasting te verdelen over SAP-toepassings server groepen voor SAPGUIs en RFC-verkeer. U kunt dit beheer repliceren met behulp van Site Recovery.

#### <a name="vms-running-sap-central-services-clusters"></a>Vm's met SAP Central Services-clusters
Deze referentie architectuur voert centrale services uit op virtuele machines in de toepassingslaag. Centrale Services is een potentieel Single Point of Failure in een enkele virtuele machine. Typische implementatie en hoge Beschik baarheid zijn niet vereist.

Als u een oplossing met hoge Beschik baarheid wilt implementeren, kunt u een gedeeld schijf cluster of een bestands share-cluster gebruiken. Als u Vm's wilt configureren voor een gedeeld schijf cluster, gebruikt u Windows Server-failovercluster. We raden u aan de cloudwitness als een quorumwitness te gebruiken.

 > [!NOTE]
 > Omdat Site Recovery de cloudwitness niet repliceert, raden we u aan de cloudwitness in het gebied voor nood herstel te implementeren.

Ter ondersteuning van de failovercluster-omgeving, wordt met de [Data keeper-cluster versie van Sios](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) de functie cluster Shared volume gebruikt. In de functie repliceert SIOS data keeper-cluster onafhankelijke schijven die eigendom zijn van de cluster knooppunten. Omdat Azure geen systeem eigen ondersteuning biedt voor gedeelde schijven, zijn er oplossingen vereist die worden geboden door SIOS.

U kunt clustering ook afhandelen door een bestands share cluster te implementeren. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) heeft het implementatie patroon van de centrale Services recent gewijzigd om toegang te krijgen tot de/sapmnt Global-directory's via een UNC-pad. We raden u echter aan om ervoor te zorgen dat de/sapmnt UNC-share Maxi maal beschikbaar is. U kunt uw centrale Services-exemplaar controleren. Gebruik Windows Server-failovercluster met scale-out Bestands server (SOFS) en de functie Opslagruimten Direct (S2D) in Windows Server 2016.

 > [!NOTE]
 > Site Recovery ondersteunt momenteel alleen crash-consistente punt replicatie van virtuele machines die gebruikmaken van Storage Spaces direct en het passieve knoop punt van SIOS data keeper.


## <a name="more-disaster-recovery-considerations"></a>Meer aandachtspunten voor herstel na nood gevallen

U kunt Site Recovery gebruiken om de failover van de volledige SAP-implementatie in azure-regio's te organiseren.
Hieronder vindt u de stappen voor het instellen van herstel na nood gevallen:

1. Virtuele machines repliceren
1. Een herstel netwerk ontwerpen
1. Een domein controller repliceren
1. Data base-laag repliceren
1. Een testfailover uitvoeren
1. Een failover uitvoeren

Hier volgt een aanbeveling voor herstel na nood gevallen van elke laag die in dit voor beeld wordt gebruikt.

 **SAP-lagen** | **Aanbeveling**
 --- | ---
**SAP Web dispatcher-pool** |  Repliceren met behulp van Site Recovery 
**SAP-toepassings server groep** |  Repliceren met behulp van Site Recovery 
**SAP Central Services-cluster** |  Repliceren met behulp van Site Recovery 
**Virtuele machines van Active Directory** |  Active Directory-replicatie gebruiken 
**SQL Database-servers** |  SQL Server always on Replication gebruiken

## <a name="replicate-virtual-machines"></a>Virtuele machines repliceren

Als u de virtuele machines van de SAP-toepassing wilt repliceren naar het Azure nood herstel Data Center, volgt u de richt lijnen in [repliceren van een virtuele machine naar Azure](./azure-to-azure-tutorial-enable-replication.md).

* Meer informatie over het beschermen van Active Directory en DNS vindt [u in het beveiligen van Active Directory en DNS](site-recovery-active-directory.md).

* Meer informatie over [het beveiligen](site-recovery-sql.md)van een database laag die wordt uitgevoerd op SQL Server, is te vinden in de beveiliging van SQL Server.

## <a name="networking-configuration"></a>Netwerkconfiguratie

Als u een statisch IP-adres gebruikt, kunt u het IP-adres opgeven dat u wilt dat de virtuele machine moet overnemen. Als u het IP-adres wilt instellen , gaat u naar de  >  **netwerk interface kaart** reken-en netwerk instellingen.

![Scherm afbeelding van het instellen van een privé-IP-adres in het deel venster Site Recovery netwerk interface kaart](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>Een herstelplan maken

Een herstel plan ondersteunt het sequentiëren van verschillende lagen in een toepassing met meerdere lagen tijdens een failover. Met sequentiëren kunt u de consistentie van toepassingen onderhouden. Wanneer u een herstel plan voor een webtoepassing met meerdere lagen maakt, voert u de stappen uit die worden beschreven in [een herstel plan maken met behulp van site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Virtuele machines toevoegen aan failover-groepen

1. Maak een herstel plan door de Vm's toepassings server, webdispatcher en SAP Central Services toe te voegen.
1. Selecteer **aanpassen** om de vm's te groeperen. Standaard maken alle Vm's deel uit van groep 1.

### <a name="add-scripts-to-the-recovery-plan"></a>Scripts toevoegen aan het herstel plan
Voor een goede werking van uw toepassingen moet u mogelijk bepaalde bewerkingen uitvoeren op de virtuele machines van Azure. Voer deze bewerkingen uit na de failover of tijdens een testfailover. U kunt ook na failover-bewerkingen automatiseren. U kunt de DNS-vermelding bijvoorbeeld bijwerken en de bindingen en verbindingen wijzigen door de bijbehorende scripts toe te voegen aan het herstel plan.

U kunt de meest gebruikte Site Recovery scripts implementeren in uw Azure Automation-account door **implementeren naar Azure** te selecteren. Wanneer u een gepubliceerd script gebruikt, volgt u de instructies in het script.

[![Implementeren in Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Voeg een pre-action script aan groep 1 toe om een failover uit te geven voor de SQL Server-beschikbaarheids groep. Gebruik het ASR-SQL-FailoverAG-script dat is gepubliceerd in de voorbeeld scripts. Volg de instructies in het script en breng de vereiste wijzigingen in het script op de juiste wijze aan.
1. Voeg een script dat volgt op de actie toe om een load balancer toe te voegen aan de virtuele machines met failover van de weblaag (groep 1). Gebruik het ASR-AddSingleLoadBalancer script dat in de voorbeeld scripts is gepubliceerd. Volg de instructies in het script en breng de gewenste wijzigingen aan in het script.

![SAP-herstel plan](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Een testfailover uitvoeren

1. Selecteer uw Recovery Services kluis in de Azure Portal.
1. Selecteer het herstel plan dat u hebt gemaakt voor SAP-toepassingen.
1. Selecteer **Failover testen**.
1. Als u het proces testfailover wilt starten, selecteert u het herstel punt en het virtuele Azure-netwerk.
1. Voer validaties uit wanneer de secundaire omgeving actief is.
1. Wanneer validaties zijn voltooid, reinigt u de failover-omgeving door testfailover **opschonen** te selecteren.

Zie [failover testen naar Azure in site Recovery](site-recovery-test-failover-to-azure.md)voor meer informatie.

## <a name="run-a-failover"></a>Een failover uitvoeren

1. Selecteer uw Recovery Services kluis in de Azure Portal.
1. Selecteer het herstel plan dat u hebt gemaakt voor SAP-toepassingen.
1. Selecteer **Failover**.
1. Als u het failoverproces wilt starten, selecteert u het herstel punt.

Zie [failover in site Recovery](site-recovery-failover.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het bouwen van een oplossing voor herstel na nood gevallen voor SAP NetWeaver-implementaties met behulp van Site Recovery. Zie het Download bare White paper [SAP NetWeaver: een oplossing voor herstel na nood gevallen bouwen met site Recovery](/samples/browse/?redirectedfrom=TechNet-Gallery). In het technisch document worden aanbevelingen besproken voor diverse SAP-architecturen. U kunt ondersteunde toepassingen en VM-typen weer geven voor SAP on Azure. Er zijn ook opties voor het plannen van uw oplossing voor herstel na nood gevallen.
* Meer informatie over het [repliceren van andere werk belastingen](site-recovery-workload.md) met behulp van site Recovery.