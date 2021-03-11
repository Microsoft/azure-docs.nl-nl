---
title: Migreer uw Windows-Vm's naar Azure Premium Storage met Azure Site Recovery
description: Meer informatie over het migreren van uw VM-schijven vanuit een Standard-opslag account naar een Premium Storage-account met behulp van Azure Site Recovery.
author: luywang
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: aeb8028468a1e1944a4fd39275f134bfae00dfa3
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555343"
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Migreren naar Premium Storage met behulp van Azure Site Recovery

[Azure Premium ssd's](../disks-types.md) bieden hoogwaardige schijf ondersteuning met lage latentie voor virtuele machines (vm's) die I/O-intensieve workloads uitvoeren. Deze hand leiding helpt u bij het migreren van uw VM-schijven vanuit een Standard-opslag account naar een Premium Storage-account met behulp van [Azure site Recovery](../../site-recovery/site-recovery-overview.md).

Site Recovery is een Azure-service die bijdraagt aan uw strategie voor bedrijfs continuïteit en herstel na nood gevallen door de replicatie van on-premises fysieke servers en virtuele machines naar de Cloud (Azure) of naar een secundair Data Center te organiseren. Als er uitval optreedt op uw primaire locatie, voert u een failover uit naar de secundaire locatie om toepassingen en workloads beschikbaar te houden. U kunt een failback uitvoeren naar de primaire locatie wanneer deze naar een normale bewerking wordt geretourneerd. 

Site Recovery voorziet in testfailover voor het ondersteunen van herstel na nood gevallen zonder dat dit van invloed is op productie omgevingen. U kunt failovers uitvoeren met mini maal gegevens verlies (afhankelijk van de replicatie frequentie) voor onverwachte rampen. In het scenario voor de migratie naar Premium Storage kunt u de [failover in site Recovery](../../site-recovery/site-recovery-failover.md) gebruiken om doel schijven te migreren naar een Premium Storage-account.

U wordt aangeraden naar Premium Storage te migreren met behulp van Site Recovery, omdat deze optie minimale downtime biedt. Deze optie voor komt ook dat de hand matige uitvoering van het kopiëren van schijven en het maken van nieuwe Vm's wordt voor komen. Site Recovery worden uw schijven systematisch gekopieerd en worden tijdens de failover nieuwe Vm's gemaakt. 

Site Recovery ondersteunt een aantal typen failover met minimale of geen downtime. Als u uw downtime wilt plannen en gegevens verlies wilt schatten, raadpleegt u de [typen failover in site Recovery](../../site-recovery/site-recovery-failover.md). Als u [na een failover verbinding maakt met Azure-vm's](../../site-recovery/vmware-azure-tutorial.md), moet u na de failover verbinding maken met de Azure-VM met behulp van RDP.

![Diagram voor nood herstel][1]

## <a name="azure-site-recovery-components"></a>Azure Site Recovery onderdelen

Deze Site Recovery onderdelen zijn relevant voor dit migratie scenario:

* **Configuratie server** is een Azure-VM die communicatie coördineert en gegevens replicatie en herstel processen beheert. Op deze VM voert u één installatie bestand uit om de configuratie server en een extra onderdeel, een proces server genaamd, te installeren als replicatie gateway. Meer informatie over de vereisten voor de [Configuratie server](../../site-recovery/vmware-azure-tutorial.md). U hoeft de configuratie server slechts één keer in te stellen en u kunt deze gebruiken voor alle migraties naar dezelfde regio.

* De **proces server** is een replicatie gateway die: 

  1. Hiermee worden replicatie gegevens van de bron-Vm's ontvangen.
  2. Optimaliseert de gegevens met caching, compressie en versleuteling.
  3. Hiermee worden de gegevens naar een opslag account verzonden. 

  Ook wordt de push-installatie van de Mobility-service naar de bron-Vm's afgehandeld en wordt automatische detectie van bron-Vm's uitgevoerd. De standaard proces server is geïnstalleerd op de configuratie server. U kunt aanvullende, zelfstandige proces servers implementeren om uw implementatie te schalen. Meer informatie over [Aanbevolen procedures voor de implementatie van proces](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) servers en het [implementeren van extra proces servers](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). U hoeft de proces server slechts één keer in te stellen en u kunt deze gebruiken voor alle migraties naar dezelfde regio.

* **Mobility service** is een onderdeel dat is geïmplementeerd op elke standaard-VM die u wilt repliceren. Hiermee worden gegevens geschreven op de standaard-VM vastgelegd en doorgestuurd naar de proces server. Meer informatie over de vereisten voor de [gerepliceerde machine](../../site-recovery/vmware-azure-tutorial.md).

In deze afbeelding ziet u hoe deze onderdelen communiceren:

![Interactie van Site Recovery onderdelen][15]

> [!NOTE]
> Site Recovery biedt geen ondersteuning voor de migratie van schijven met opslag ruimten.

Zie [scenario Architecture](../../site-recovery/vmware-azure-tutorial.md)voor aanvullende onderdelen voor andere scenario's.

## <a name="azure-essentials"></a>Azure Essentials

Dit zijn de Azure-vereisten voor dit migratie scenario:

* Een Azure-abonnement.
* Een Azure Premium-opslag account voor het opslaan van gerepliceerde gegevens.
* Een virtueel Azure-netwerk waarmee Vm's verbinding maken wanneer ze tijdens de failover worden gemaakt. Het virtuele Azure-netwerk moet zich in dezelfde regio bevinden als waar Site Recovery worden uitgevoerd.
* Een Azure Standard-opslag account voor het opslaan van replicatie Logboeken. Dit kan hetzelfde opslag account zijn voor de VM-schijven die worden gemigreerd.

## <a name="prerequisites"></a>Vereisten

* Meer informatie over de relevante migratie scenario onderdelen in de voor gaande sectie.
* Plan uw downtime door te leren over [failover in site Recovery](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Installatie-en migratie stappen

U kunt Site Recovery gebruiken om virtuele Azure IaaS-machines te migreren tussen regio's of binnen dezelfde regio. De volgende instructies zijn afgestemd op dit migratie scenario van het artikel [replicatie van virtuele VMware-machines of fysieke servers naar Azure](../../site-recovery/vmware-azure-tutorial.md). Volg de koppelingen voor gedetailleerde stappen naast de instructies in dit artikel.

### <a name="step-1-create-a-recovery-services-vault"></a>Stap 1: een Recovery Services kluis maken

1. Open de [Azure Portal](https://portal.azure.com).
2. Selecteer **een resource**  >  **beheer**-  >  **Backup en site Recovery (OMS)** maken. U kunt ook **Bladeren**  >  **Recovery Services kluis**  >  **toevoegen** selecteren.
   >[!NOTE]
   >Backup en Site Recovery waren voorheen onderdeel van het [OMS-pakket](../../azure-monitor/terminology.md#april-2018---retirement-of-operations-management-suite-brand).
1. Geef een regio op waarnaar Vm's worden gerepliceerd. Voor het doel van de migratie in dezelfde regio selecteert u de regio waar uw bron-Vm's en bron opslag accounts zijn. 

### <a name="step-2-choose-your-protection-goals"></a>Stap 2: Kies uw beveiligings doelen 

1. Open de [Azure Portal](https://portal.azure.com)op de virtuele machine waarop u de configuratie server wilt installeren.
2. Ga naar **Recovery Services kluizen**-  >  **instellingen**  >  **site Recovery**  >  **stap 1: infrastructuur**  >  **beveiliging** voorbereiden.

   ![Bladeren naar het deel venster beveiligings doelstelling][2]

3. Selecteer onder **beveiligings doel** in de vervolg keuzelijst de optie **Azure**. Selecteer in de tweede vervolg keuzelijst **niet gevirtualiseerd/Overig** en selecteer vervolgens **OK**.

   ![Deel venster beveiligings doelstelling met ingevulde vakken][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Stap 3: de bron omgeving instellen (configuratie server)

1. Down load **Azure site Recovery Unified Setup** en de kluis registratie sleutel door te gaan naar de deel Vensters voor het voorbereiden van de bron voor het toevoegen van de **infra structuur**  >    >   . 
 
   U hebt de kluis registratie sleutel nodig om de geïntegreerde installatie uit te voeren. De sleutel blijft vijf dagen na het genereren ervan geldig.

   ![Bladeren naar het deel venster server toevoegen][4]

2. Voeg een configuratie server toe in het deel venster **server toevoegen** .

   ![Deel venster server toevoegen met configuratie server geselecteerd][5]

3. Op de virtuele machine die u als configuratie server gebruikt, voert u Unified Setup uit om de configuratie server en de proces server te installeren. U kunt [de scherm afbeeldingen door](../../site-recovery/vmware-azure-tutorial.md) lopen om de installatie te volt ooien. U kunt de volgende scherm afbeeldingen raadplegen voor de stappen die zijn opgegeven voor dit migratie scenario.

   1. Selecteer bij **Voordat u begint** de optie **De configuratieserver en processerver installeren**.

      ![Voordat u begint met de pagina][6]

   2. Blader in **registratie** en selecteer de registratie sleutel die u hebt gedownload van de kluis.

      ![Registratie pagina][7]

   3. Selecteer bij **Details van de omgeving** of u virtuele VMware-machines wilt repliceren. Kies **Nee** in dit migratie scenario.

      ![Pagina omgevings Details][8]

4. Nadat de installatie is voltooid, gaat u als volgt te werk in het venster **Microsoft Azure site Recovery configuratie server** :
 
   1. Gebruik het tabblad **accounts beheren** om het account te maken dat site Recovery kan gebruiken voor automatische detectie. (In het scenario voor het beveiligen van fysieke computers is het instellen van het account niet relevant, maar u hebt ten minste één account nodig om een van de volgende stappen uit te voeren. In dit geval kunt u de naam van het account en het wacht woord opgeven.) 
   2. Gebruik het tabblad **kluis registratie** om het kluis referentie bestand te uploaden.

      ![Tabblad kluis registratie][9]

### <a name="step-4-set-up-the-target-environment"></a>Stap 4: de doel omgeving instellen

Selecteer **infrastructuur**  >  **doel** voorbereiden en geef het implementatie model op dat u wilt gebruiken voor vm's na een failover. U kunt **klassiek** of **Resource Manager** kiezen, afhankelijk van uw scenario.

![Doel venster][10]

Site Recovery controleert of u een of meer compatibele Azure-opslagaccounts en -netwerken hebt. 

> [!NOTE]
> Als u een Premium-opslag account gebruikt voor gerepliceerde gegevens, moet u een extra Standard-opslag account instellen om de replicatie logboeken op te slaan.

### <a name="step-5-set-up-replication-settings"></a>Stap 5: replicatie-instellingen instellen

Als u wilt controleren of uw configuratie server is gekoppeld aan het replicatie beleid dat u hebt gemaakt, volgt u [replicatie-instellingen instellen](../../site-recovery/vmware-azure-tutorial.md).

### <a name="step-6-plan-capacity"></a>Stap 6: capaciteit plannen

1. Gebruik de [capaciteits planner](../../site-recovery/site-recovery-capacity-planner.md) voor een nauw keurige schatting van de netwerk bandbreedte, opslag en andere vereisten om te voldoen aan uw replicatie behoeften. 
2. Als u klaar bent, selecteert u **Ja, ik heb dit gedaan** in **hebt u de capaciteits planning voltooid?**.

   ![Vak om te bevestigen dat u de capaciteits planning hebt voltooid][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Stap 7: de Mobility-service installeren en replicatie inschakelen

1. U kunt ervoor kiezen om de installatie naar de bron-Vm's te [pushen](../../site-recovery/vmware-azure-tutorial.md) of [de Mobility-service hand matig te installeren](../../site-recovery/vmware-azure-install-mobility-service.md) op de bron-vm's. U vindt de vereiste van het pushen van de installatie en het pad van het hand matige installatie programma in de meegeleverde koppeling. Als u een hand matige installatie uitvoert, moet u mogelijk een intern IP-adres gebruiken om de configuratie server te vinden.

   ![Pagina Details van de configuratie server][12]

   De failover-VM heeft twee tijdelijke schijven: een van de primaire virtuele machine en de andere die is gemaakt tijdens het inrichten van de virtuele machine in de herstel regio. Als u de tijdelijke schijf vóór replicatie wilt uitsluiten, installeert u de Mobility-service voordat u replicatie inschakelt. Zie [schijven uitsluiten van replicatie](../../site-recovery/vmware-azure-tutorial.md)voor meer informatie over het uitsluiten van de tijdelijke schijf.

2. Schakel als volgt replicatie in:
   1. Selecteer **toepassings**  >  **bron** repliceren. Nadat u de replicatie voor de eerste keer hebt ingeschakeld, selecteert u **+ repliceren** in de kluis om replicatie in te scha kelen voor aanvullende machines.
   2. In stap 1 stelt u de **bron** in als uw proces server.
   3. Geef in stap 2 het implementatie model na failover op, een Premium-opslag account om te migreren naar, een Standard-opslag account om logboeken op te slaan en een virtueel netwerk te laten mislukken.
   4. Voeg in stap 3 beveiligde Vm's toe op IP-adres. (Mogelijk hebt u een intern IP-adres nodig om ze te vinden.)
   5. Configureer de eigenschappen in stap 4 door de accounts te selecteren die u eerder op de proces server hebt ingesteld.
   6. Kies in stap 5 het replicatie beleid dat u eerder hebt gemaakt in ' stap 5: replicatie-instellingen instellen '.
   7. Selecteer **OK**.

   > [!NOTE]
   > Wanneer de toewijzing van een Azure-VM ongedaan wordt gemaakt en opnieuw wordt gestart, is er geen garantie dat het hetzelfde IP-adres krijgt. Als het IP-adres van de configuratie Server/proces server of de beveiligde Azure-Vm's wordt gewijzigd, werkt de replicatie in dit scenario mogelijk niet goed.

   ![Het deel venster replicatie inschakelen met de geselecteerde bron][13]

Wanneer u uw Azure Storage-omgeving ontwerpt, raden we u aan om afzonderlijke opslag accounts voor elke virtuele machine in een beschikbaarheidsset te gebruiken. U wordt aangeraden de best practice in de opslaglaag te volgen om [meerdere opslag accounts voor elke beschikbaarheidsset te gebruiken](../availability.md). Het distribueren van VM-schijven naar meerdere opslag accounts helpt u bij het verbeteren van de beschik baarheid van opslag en het distribueren van de I/O over de Azure-opslag infrastructuur.

Als uw Vm's zich in een beschikbaarheidsset bevinden, kunt u het beste meerdere Vm's meerdere keren migreren in plaats van schijven van alle Vm's te repliceren naar één opslag account. Op die manier delen de virtuele machines in dezelfde beschikbaarheidsset niet één opslag account. Gebruik het deel venster **replicatie inschakelen** voor het instellen van een bestemmings opslag account voor elke virtuele machine, één per keer.
 
U kunt een implementatie model na failover kiezen op basis van uw behoeften. Als u Azure Resource Manager opgeeft als uw implementatie model na failover, kunt u een failover uitvoeren voor een virtuele machine (Resource Manager) naar een VM (Resource Manager) of kunt u een failover uitvoeren voor een virtuele machine (klassiek) naar een virtuele machine (Resource Manager).

### <a name="step-8-run-a-test-failover"></a>Stap 8: een testfailover uitvoeren

Als u wilt controleren of de replicatie is voltooid, selecteert u uw site Recovery-exemplaar en selecteert u vervolgens **instellingen**  >  **gerepliceerde items**. U ziet de status en het percentage van het replicatie proces. 

Nadat de initiële replicatie is voltooid, voert u een testfailover uit om uw replicatie strategie te valideren. Zie [een Testfailover uitvoeren in site Recovery](../../site-recovery/vmware-azure-tutorial.md)voor gedetailleerde stappen van een testfailover. 

> [!NOTE]
> Voordat u een failover uitvoert, moet u ervoor zorgen dat uw Vm's en replicatie strategie voldoen aan de vereisten. Voor meer informatie over het uitvoeren van een testfailover raadpleegt [u testfailover naar Azure in site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

U kunt de status van uw testfailover bekijken in **instellingen**  >  **taken**  >  *YOUR_FAILOVER_PLAN_NAME*. In het deel venster ziet u een overzicht van de stappen en geslaagde/mislukte resultaten. Als de testfailover in een wille keurige stap mislukt, selecteert u de stap om het fout bericht te controleren. 

### <a name="step-9-run-a-failover"></a>Stap 9: een failover uitvoeren

Nadat de testfailover is voltooid, voert u een failover uit om uw schijven naar Premium Storage te migreren en de VM-exemplaren te repliceren. Volg de gedetailleerde stappen in [een failover uitvoeren](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Zorg ervoor dat u **Afsluiten vm's selecteert en de meest recente gegevens synchroniseert**. Met deze optie geeft u op dat Site Recovery moet proberen om de beveiligde Vm's af te sluiten en de gegevens te synchroniseren, zodat er een failover wordt uitgevoerd voor de nieuwste versie van de gegevens. Als u deze optie niet selecteert of als de poging mislukt, is de failover van het meest recente beschik bare herstel punt voor de virtuele machine. 

Site Recovery maakt een VM-exemplaar waarvan het type hetzelfde is als of vergelijkbaar is met een virtuele machine die Premium Storage ondersteunt. U kunt de prestaties en prijs van verschillende VM-exemplaren controleren door te gaan naar [Windows virtual machines prijzen](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) of [Linux virtual machines prijzen](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Post-migratiestappen

1. **Configureer gerepliceerde vm's naar de beschikbaarheidsset, indien van toepassing**. Site Recovery biedt geen ondersteuning voor het migreren van Vm's samen met de beschikbaarheidsset. Voer een van de volgende handelingen uit, afhankelijk van de implementatie van uw gerepliceerde VM:
   * Voor een virtuele machine die is gemaakt via het klassieke implementatie model: Voeg de virtuele machine toe aan de beschikbaarheidsset in de Azure Portal. Ga voor gedetailleerde stappen naar [een bestaande virtuele machine toevoegen aan een beschikbaarheidsset](/previous-versions/azure/virtual-machines/linux/classic/configure-availability-classic).
   * Voor een virtuele machine die is gemaakt via het Resource Manager-implementatie model: Sla uw configuratie van de virtuele machine op en verwijder de virtuele machines in de beschikbaarheidsset. Gebruik hiervoor het script op [set Azure Resource Manager-beschikbaarheidsset voor de virtuele machine](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Voordat u dit script uitvoert, moet u de beperkingen controleren en uw downtime plannen.

2. **Oude vm's en schijven verwijderen**. Zorg ervoor dat de Premium-schijven consistent zijn met de bron schijven en dat de nieuwe Vm's dezelfde functie uitvoeren als de bron-Vm's. Verwijder de virtuele machine en verwijder de schijven uit uw bron opslag accounts in de Azure Portal. Zie [problemen met het verwijderen van opslag resources oplossen](../troubleshooting/storage-resource-deletion-errors.md)als er een probleem is waarbij de schijf niet wordt verwijderd, ook al hebt u de virtuele machine verwijderd.

3. **Reinig de Azure site Recovery-infra structuur**. Als Site Recovery niet meer nodig is, kunt u de infra structuur ervan opschonen. Verwijder gerepliceerde items, de configuratie server en het herstel beleid en verwijder vervolgens de Azure Site Recovery kluis.

## <a name="troubleshooting"></a>Problemen oplossen

* [Beveiliging van virtuele machines en fysieke servers controleren en problemen oplossen](../../site-recovery/site-recovery-monitor-and-troubleshoot.md)
* [Micro soft Q&een vraag pagina voor Microsoft Azure Site Recovery](/answers/topics/azure-site-recovery.html)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende bronnen voor specifieke scenario's voor het migreren van virtuele machines:

* [Azure Virtual Machines migreren tussen opslag accounts](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Een Windows Server-VHD maken en uploaden naar Azure](upload-generalized-managed.md)
* [Virtual Machines migreren van Amazon AWS naar Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Raadpleeg ook de volgende bronnen voor meer informatie over Azure Storage en Azure Virtual Machines:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)

[1]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png