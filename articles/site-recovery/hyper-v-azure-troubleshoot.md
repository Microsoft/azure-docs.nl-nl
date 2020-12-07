---
title: Problemen met herstel na nood gevallen voor Hyper-V oplossen met Azure Site Recovery
description: Hierin wordt beschreven hoe u problemen met nood herstel kunt oplossen met Hyper-V naar Azure-replicatie met behulp van Azure Site Recovery
services: site-recovery
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: sharrai
ms.openlocfilehash: c804e13029dcec42a43885cbf0d9b227b3d0338f
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750799"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Problemen oplossen met replicatie en failover voor Hyper-V naar Azure

In dit artikel worden veelvoorkomende problemen beschreven die zich kunnen voordoen bij het repliceren van on-premises virtuele Hyper-V-machines naar Azure, met behulp van [Azure site Recovery](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Beveiligings problemen inschakelen

Als u problemen ondervindt bij het inschakelen van beveiliging voor virtuele Hyper-V-machines, raadpleegt u de volgende aanbevelingen:

1. Controleer of uw Hyper-V-hosts en-Vm's voldoen aan alle [vereisten en voor waarden](hyper-v-azure-support-matrix.md).
2. Als Hyper-V-servers zich in System Center Virtual Machine Manager-Clouds (VMM) bevinden, controleert u of u de [VMM-server](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional)hebt voor bereid.
3. Controleer of de Hyper-V virtual machine Management-service wordt uitgevoerd op Hyper-V-hosts.
4. Controleer op problemen die worden weer gegeven in het Hyper-V-VMMS\Admin-aanmelden bij de virtuele machine. Dit logboek bevindt zich in de **Logboeken toepassingen en services**  >  **micro soft**  >  **Windows**.
5. Controleer op de gast-VM of WMI is ingeschakeld en toegankelijk is.
   - [Meer informatie over](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf) Basic WMI-tests.
   - [Problemen oplossen](/windows/win32/wmisdk/wmi-troubleshooting) WMI.
   - [Los](/previous-versions/tn-archive/ff406382(v=msdn.10)#H22) problemen met WMI-scripts en-services op.
6. Zorg ervoor dat op de gast-VM de meest recente versie van Integration Services wordt uitgevoerd.
    - [Controleer](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) of u de meest recente versie hebt.
    - [Blijven](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) Integratie Services zijn bijgewerkt.

### <a name="cannot-enable-protection-as-the-virtual-machine-is-not-highly-available-error-code-70094"></a>Kan de beveiliging niet inschakelen omdat de virtuele machine niet Maxi maal beschikbaar is (fout code 70094)

Wanneer u replicatie voor een machine inschakelt en er zich een fout voordoet met de melding dat de replicatie niet kan worden ingeschakeld omdat de computer niet Maxi maal beschikbaar is, voert u de volgende stappen uit om dit probleem op te lossen:

- Start de VMM-service op de VMM-server opnieuw op.
- Verwijder de virtuele machine uit het cluster en voeg deze opnieuw toe.

### <a name="the-vss-writer-ntds-failed-with-status-11-and-writer-specific-failure-code-0x800423f4"></a>De VSS Writer NTDS is mislukt met de status 11 en Writer-specifieke fout code 0x800423F4

Wanneer u probeert replicatie in te scha kelen, kunt u een fout melding krijgen dat het inschakelen van replicatie is mislukt AST NTDS is mislukt. Een van de mogelijke oorzaken voor dit probleem is dat het besturings systeem van de virtuele machine in Windows Server 2012 en niet Windows Server 2012 R2. Voer de onderstaande stappen uit om dit probleem op te lossen:

- Voer een upgrade uit naar Windows Server R2 met 4072650.
- Zorg ervoor dat Hyper-V host ook Windows 2016 of hoger is.

## <a name="replication-issues"></a>Replicatieproblemen

Problemen met eerste en doorlopende replicatie kunt u als volgt oplossen:

1. Zorg ervoor dat u de [nieuwste versie](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) van site Recovery Services uitvoert.
2. Controleer of de replicatie is onderbroken:
   - Controleer de status van de virtuele machine in de console van Hyper-V-beheer.
   - Als het kritiek is, klikt u met de rechter muisknop op de virtuele **machine > replicatie**  >  **weergave replicatie status**.
   - Als de replicatie is onderbroken, klikt u op **replicatie hervatten**.
3. Controleer of de vereiste services worden uitgevoerd. Als dat niet het geval is, start u deze opnieuw.
    - Als u Hyper-V zonder VMM repliceert, controleert u of deze services worden uitgevoerd op de Hyper-V-host:
        - Virtual Machine Management
        - Microsoft Azure Recovery Services Agent
        - Microsoft Azure Site Recovery
        - WMI Provider Host
    - Als u repliceert met VMM in de omgeving, controleert u of deze services worden uitgevoerd:
        - Controleer op de Hyper-V-host of de Virtual Machine Management-service, de Microsoft Azure Recovery Services agent en de WMI provider host-service worden uitgevoerd.
        - Zorg ervoor dat de System Center Virtual Machine Manager-service wordt uitgevoerd op de VMM-server.
4. Controleer de connectiviteit tussen de Hyper-V-server en Azure. Als u de connectiviteit wilt controleren, opent u taak beheer op de Hyper-V-host. Klik op het tabblad **prestaties** op **Broncontrole openen**. Controleer op het tabblad **netwerk** > **proces met netwerk activiteit** of cbengine.exe bezig is met het verzenden van grote hoeveel heden gegevens.
5. Controleer of de Hyper-V-hosts verbinding kunnen maken met de URL van de Azure Storage-blob. Selecteer en controleer de **cbengine.exe** om te controleren of de hosts verbinding kunnen maken. **TCP-verbindingen** weer geven om de connectiviteit van de host naar de Azure Storage-BLOB te controleren.
6. Controleer de prestatie problemen, zoals hieronder wordt beschreven.
    
### <a name="performance-issues"></a>Prestatieproblemen

Beperkingen voor netwerk bandbreedte kunnen van invloed zijn op replicatie. Problemen als volgt oplossen:

1. [Controleer](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) of er band breedte-of beperkings beperkingen in uw omgeving zijn.
2. Voer de [Deployment planner Profiler](hyper-v-deployment-planner-run.md)uit.
3. Nadat u de Profiler hebt uitgevoerd, volgt u de aanbevelingen voor [band breedte](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) en [opslag](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) .
4. Controleer de [beperkingen voor gegevens verloop](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Ga als volgt te werk als u een hoog verloop van gegevens op een virtuele machine ziet:
   - Controleer of de virtuele machine is gemarkeerd voor hersynchronisatie.
   - Volg [deze stappen](https://techcommunity.microsoft.com/t5/virtualization/bg-p/Virtualization) om de bron van het verloop te onderzoeken.
   - Verloop kan optreden wanneer de HRL-logboek bestanden groter zijn dan 50% van de beschik bare schijf ruimte. Als dit het probleem is, voorziet u meer opslag ruimte voor alle Vm's waarop het probleem zich voordoet.
   - Controleer of de replicatie niet is onderbroken. Als dat het geval is, wordt de wijzigingen doorgevoerd naar het HRL-bestand. Dit kan bijdragen aan de verhoogde grootte.
 

## <a name="critical-replication-state-issues"></a>Problemen met de kritieke replicatie status

1. Als u de replicatie status wilt controleren, maakt u verbinding met de on-premises Hyper-V-beheer console, selecteert u de virtuele machine en controleert u de status.

    ![Replicatie status](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Klik op **replicatie status weer geven** om de details te bekijken:

    - Als de replicatie is onderbroken, klikt u met de rechter muisknop op de virtuele machine > **replicatie**  >  **hervatten**.
    - Als een virtuele machine op een Hyper-V-host die is geconfigureerd in Site Recovery wordt gemigreerd naar een andere Hyper-V-host in hetzelfde cluster of op een zelfstandige computer, heeft dit geen invloed op de replicatie voor de virtuele machine. U kunt alleen controleren of de nieuwe Hyper-V-host voldoet aan alle vereisten en is geconfigureerd in Site Recovery.

## <a name="app-consistent-snapshot-issues"></a>Problemen met app-consistente moment opnamen

Een app-consistente momentopname is een momentopname van de toepassingsgegevens in de VM op een bepaald tijdstip. Volume Shadow Copy Service (VSS) zorgt ervoor dat apps op de virtuele machine een consistente status hebben wanneer de moment opname wordt gemaakt.  In deze sectie vindt u enkele veelvoorkomende problemen die u kunt voordoen.

### <a name="vss-failing-inside-the-vm"></a>VSS in de virtuele machine mislukt

1. Controleer of de meest recente versie van Integration Services is geïnstalleerd en wordt uitgevoerd.  Controleer of er een update beschikbaar is door de volgende opdracht uit te voeren vanaf een Power shell-prompt met verhoogde bevoegdheid op de Hyper-V-host: **Get-VM | Select name, status, IntegrationServicesState**.
2. Controleer of de VSS-services worden uitgevoerd en in orde zijn:
   - Als u de services wilt controleren, meldt u zich aan bij de gast-VM. Open vervolgens een opdracht prompt van de beheerder en voer de volgende opdrachten uit om te controleren of alle VSS-schrijvers in orde zijn.
       - **Vssadmin list writers**
       - **Vssadmin list shadows**
       - **Vssadmin list-providers**
   - Controleer de uitvoer. Als schrijvers de status mislukt hebben, doet u het volgende:
       - Controleer het toepassings gebeurtenis logboek op de VM voor VSS-bewerkings fouten.
   - Probeer deze services opnieuw te starten die zijn gekoppeld aan de mislukte schrijver:
     - Volume Shadow Copy
       - Azure Site Recovery VSS-provider
   - Nadat u dit hebt gedaan, moet u een paar uur wachten om te controleren of de app-consistente moment opnamen zijn gegenereerd.
   - Als laatste redmiddel probeert u de virtuele machine opnieuw op te starten. Dit kan leiden tot het oplossen van services die niet reageren.
3. Controleer of er geen dynamische schijven in de VM zijn. Dit wordt niet ondersteund voor app-consistente moment opnamen. U kunt schijf beheer (diskmgmt. msc) controleren.

    ![Dynamische schijf](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Controleer of er geen iSCSI-schijf is gekoppeld aan de virtuele machine. Nee, dit wordt niet ondersteund.
5. Controleer of de back-upservice is ingeschakeld. Controleer of de functie is ingeschakeld in de integratie services van **Hyper-V-instellingen**  >  **Integration Services**.
6. Zorg ervoor dat er geen conflicten zijn met apps die VSS-moment opnamen maken. Als meerdere apps op hetzelfde moment een VSS-moment opname proberen te maken, kunnen er conflicten optreden. Als een back-uptoepassing bijvoorbeeld VSS-moment opnamen maakt wanneer Site Recovery door het replicatie beleid wordt gepland om een moment opname te maken.   
7. Controleer of de virtuele machine een hoog verloop tempo ondervindt:
    - U kunt de dagelijkse gegevens wijzigings snelheid voor de gast-Vm's meten, met behulp van prestatie meter items op de Hyper-V-host. Schakel de volgende teller in om de wijzigings frequentie van gegevens te meten. Verzamel een voor beeld van deze waarde op de VM-schijven gedurende 5-15 minuten om het verloop van de VM op te halen.
        - Categorie: "Hyper-V virtuele-opslag apparaat"
        - Teller: schrijf bewerkingen in bytes per seconde</br>
        - Dit verloop snelheid van gegevens wordt verhoogd of verloopt op hoog niveau, afhankelijk van de manier waarop de virtuele machine of de apps actief zijn.
        - Het gemiddelde gegevens verloop van de bron schijf is 2 MB/s voor de standaard opslag voor Site Recovery. [Meer informatie](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Daarnaast kunt u de [schaalbaarheids doelen voor opslag controleren](../storage/common/scalability-targets-standard-account.md).
8. Zorg ervoor dat als u een op Linux gebaseerde server gebruikt, u app-consistentie hebt ingeschakeld. [Meer informatie](./site-recovery-faq.md#replication)
9. Voer de [Deployment planner](hyper-v-deployment-planner-run.md)uit.
10. Bekijk de aanbevelingen voor het [netwerk](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) en de [opslag](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input).


### <a name="vss-failing-inside-the-hyper-v-host"></a>VSS mislukt in de Hyper-V-host

1. Controleer de gebeurtenis logboeken op VSS-fouten en aanbevelingen:
    - Open op de hyper-v-Hostserver het gebeurtenis logboek van Hyper-v-beheer in **Logboeken**  >  **toepassingen en services**  >  **micro soft**  >  **Windows**  >  **hyper-v-**  >  **beheer** registreert.
    - Controleer of er gebeurtenissen zijn die fouten in de app-consistente moment opnamen aangeven.
    - Een typische fout is: ' Hyper-V kan geen VSS-momentopnameset genereren voor de virtuele machine ' XYZ ': er is een niet-tijdelijke fout opgetreden in de schrijver. Het opnieuw starten van de VSS-service kan problemen oplossen als de service niet reageert. "

2. Als u VSS-moment opnamen voor de virtuele machine wilt genereren, controleert u of Hyper-V-integratie Services zijn geïnstalleerd op de virtuele machine en of de integratie service Backup (VSS) is ingeschakeld.
    - Controleer of de VSS-service/-daemons van Integration Services worden uitgevoerd op de gast en de status **OK** hebben.
    - U kunt dit controleren vanuit een Power shell-sessie met verhoogde bevoegdheid op de Hyper-V-host met de opdracht **Get-VMIntegrationService-VMName \<VMName> -name VSS** u kunt deze informatie ook ophalen door u aan te melden bij de gast-VM. [Meer informatie](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Zorg ervoor dat de back-up/VSS-integratie Services op de virtuele machine worden uitgevoerd en de status in orde hebben. Als dat niet het geval is, start u deze services en de Hyper-V Volume Shadow Copy Request service op de Hyper-V-hostserver.

### <a name="common-errors"></a>Algemene fouten

**Foutcode** | **Bericht** | **Details**
--- | --- | ---
**0x800700EA** | Hyper-V kan geen VSS-momentopnameset genereren voor de virtuele machine: er zijn meer gegevens beschikbaar. (0x800700EA). Het genereren van VSS-moment opnamen kan mislukken als er een back-upbewerking wordt uitgevoerd.<br/><br/> De replicatie bewerking voor de virtuele machine is mislukt: er zijn meer gegevens beschikbaar. | Controleer of er een dynamische schijf is ingeschakeld op de virtuele machine. Nee, dit wordt niet ondersteund.
**0x80070032** | "Hyper-V-aanvrager van volume schaduw kopie kan geen verbinding maken met de virtuele machine <./VMname> omdat de versie niet overeenkomt met de versie die wordt verwacht door Hyper-V | Controleer of de meest recente Windows-updates zijn geïnstalleerd.<br/><br/> Voer een [upgrade uit](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) naar de nieuwste versie van Integration Services.



## <a name="collect-replication-logs"></a>Replicatie logboeken verzamelen

Alle replicatie gebeurtenissen van Hyper-V worden vastgelegd in het Hyper-V-VMMS\Admin-logboek in **toepassingen en services**  >  **micro soft**  >  **Windows**. Daarnaast kunt u een analytisch logboek inschakelen voor de Hyper-V virtual machine Management-service, als volgt:

1. Maak de logboeken voor analyse en fout opsporing zichtbaar in de Logboeken. Als u de logboeken beschikbaar wilt maken, klikt u in het logboeken op **weer** geven  >  **analyse logboeken en fout opsporing.**. Het analytische logboek wordt weer gegeven onder **Hyper-V-VMMS**.
2. Klik in het deel venster **acties** op **logboek inschakelen**. 

    ![Logboek inschakelen](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Nadat de functie is ingeschakeld, wordt deze weer gegeven in **prestatie meter** **als een gebeurtenistraceersessie** onder **gegevens verzamelaarsets**. 
4. Als u de verzamelde gegevens wilt weer geven, stopt u de traceer sessie door het logboek uit te scha kelen. Sla het logboek op en open het opnieuw in Logboeken, of gebruik andere hulpprogram ma's om het te converteren naar vereist.


### <a name="event-log-locations"></a>Locaties van gebeurtenis logboeken

**Gebeurtenis logboek** | **Details** |
--- | ---
**Toepassingen en service logboeken/micro soft/VirtualMachineManager/server/admin** (VMM-server) | Logboeken voor het oplossen van problemen met VMM.
**Toepassingen en service logboeken/MicrosoftAzureRecoveryServices/replicatie** (Hyper-V-host) | Logboeken voor het oplossen van problemen met Microsoft Azure Recovery Services agents. 
**Toepassingen en service logboeken/micro soft/Azure site Recovery/provider/operationeel** (Hyper-V-host)| Logboeken voor het oplossen van problemen met de Microsoft Azure Site Recovery-service.
**Toepassingen en service logboeken/micro soft/Windows/hyper-v-VMMS/admin** (Hyper-V-host) | Logboeken voor het oplossen van problemen met Hyper-V-VM-beheer.

### <a name="log-collection-for-advanced-troubleshooting"></a>Logboek verzameling voor geavanceerde probleem oplossing

Deze hulpprogram ma's kunnen u helpen bij geavanceerde probleem oplossing:

-   Voor VMM voert u Site Recovery logboek verzameling uit met behulp van de [SDP-tool (ondersteunings platform voor diagnostische gegevens)](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx).
-   Voor Hyper-V zonder VMM [downloadt u dit hulp programma](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)en voert u het uit op de hyper-v-host om de logboeken te verzamelen.