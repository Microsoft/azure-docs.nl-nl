---
title: StorSimple file share DR automatiseren met Azure Site Recovery | Microsoft Docs
description: Hierin worden de stappen en aanbevolen procedures beschreven voor het maken van een nood herstel oplossing voor bestands shares die worden gehost op Microsoft Azure StorSimple opslag.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: alkohli
ms.openlocfilehash: c6152d4b9ee28554efcb5b08b7a2d161a0723852
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670901"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Geautomatiseerde oplossing voor herstel na nood gevallen met Azure Site Recovery voor bestands shares die worden gehost op StorSimple

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht
Microsoft Azure StorSimple is een Hybrid Cloud Storage oplossing voor het oplossen van de complexiteit van ongestructureerde gegevens die vaak zijn gekoppeld aan bestands shares. StorSimple maakt gebruik van Cloud opslag als een uitbrei ding van de on-premises oplossing, waarbij gegevens in on-premises opslag en Cloud opslag automatisch worden gelaagd. Met geïntegreerde gegevens bescherming, met lokale en Cloud momentopnamen, is de nood zaak van een sprawling-opslag infrastructuur overbodig.

[Azure site Recovery](../site-recovery/site-recovery-overview.md) is een Azure-service die mogelijkheden voor herstel na nood gevallen (Dr) biedt door de replicatie, failover en het herstel van virtuele machines te organiseren. Azure Site Recovery ondersteunt een aantal replicatie technologieën voor het consistent repliceren, beveiligen en probleemloze failover van virtuele machines en toepassingen naar privé-of open bare of gehoste Clouds.

U kunt de volledige bestands server omgeving beveiligen met behulp van Azure Site Recovery, replicatie van virtuele machines en StorSimple voor Cloud momentopnamen. In het geval van een onderbreking kunt u één klik gebruiken om uw bestands shares in een paar minuten online te brengen in Azure.

In dit document wordt uitgelegd hoe u een oplossing voor herstel na nood geval kunt maken voor uw bestands shares die worden gehost op StorSimple-opslag, en om geplande, ongeplande en testfailover uit te voeren met een herstel plan met één klik. In essentie ziet u hoe u het herstel plan in uw Azure Site Recovery kluis kunt wijzigen om StorSimple-failovers tijdens nood scenario's in te scha kelen. Daarnaast worden de ondersteunde configuraties en vereisten beschreven. In dit document wordt ervan uitgegaan dat u bekend bent met de basis principes van Azure Site Recovery-en StorSimple-architecturen.

## <a name="supported-azure-site-recovery-deployment-options"></a>Ondersteunde Azure Site Recovery implementatie opties
Klanten kunnen bestands servers implementeren als fysieke servers of virtuele machines (Vm's) die worden uitgevoerd op Hyper-V of VMware, en vervolgens bestands shares maken op basis van volumes gehaald uit de StorSimple-opslag. Azure Site Recovery kunt zowel fysieke als virtuele implementaties beveiligen op een secundaire site of in Azure. Dit document bevat informatie over een DR-oplossing met Azure als de herstel site voor een bestands Server-VM die wordt gehost op Hyper-V en met bestands shares op StorSimple-opslag. Andere scenario's waarin de VM van de bestands server zich op een virtuele VMware-machine bevindt of een fysieke machine kan op dezelfde manier worden geïmplementeerd.

## <a name="prerequisites"></a>Vereisten
Het implementeren van een oplossing voor nood herstel met één klik die gebruikmaakt van Azure Site Recovery voor bestands shares die worden gehost op StorSimple Storage heeft de volgende vereisten:

   - On-premises Windows Server 2012 R2 bestands Server-VM gehost op Hyper-V of VMware of een fysieke computer
   - StorSimple-opslag apparaat is on-premises geregistreerd bij Azure StorSimple Manager
   - StorSimple Cloud Appliance gemaakt in azure StorSimple Manager. Het apparaat kan worden bewaard in een afsluit status.
   - Bestands shares die worden gehost op de volumes die zijn geconfigureerd op het StorSimple-opslag apparaat
   - [Azure site Recovery Services-kluis](../site-recovery/hyper-v-vmm-azure-tutorial.md) die is gemaakt in een Microsoft Azure-abonnement

Als Azure uw herstel site is, voert u het [hulp programma Azure virtual machine Readiness Assessment](https://azure.microsoft.com/downloads/vm-readiness-assessment/) uit op virtuele machines om ervoor te zorgen dat ze compatibel zijn met Azure-vm's en Azure site Recovery Services.

Om latentie problemen te voor komen (wat kan leiden tot hogere kosten), moet u ervoor zorgen dat u uw StorSimple Cloud Appliance, Automation-account en opslag account (s) in dezelfde regio maakt.

## <a name="enable-dr-for-storsimple-file-shares"></a>DR inschakelen voor StorSimple-bestands shares
Elk onderdeel van de on-premises omgeving moet worden beveiligd om volledige replicatie en herstel mogelijk te maken. In deze sectie wordt beschreven hoe u:
    
   - Active Directory-en DNS-replicatie instellen (optioneel)
   - Azure Site Recovery gebruiken om beveiliging van de bestands Server-VM in te scha kelen
   - Beveiliging van StorSimple-volumes inschakelen
   - Het netwerk configureren

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Active Directory-en DNS-replicatie instellen (optioneel)
Als u de machines met Active Directory en DNS wilt beveiligen zodat ze beschikbaar zijn op de DR-site, moet u deze expliciet beveiligen (zodat de bestands servers toegankelijk zijn na een failover met verificatie). Er zijn twee aanbevolen opties op basis van de complexiteit van de on-premises omgeving van de klant.

#### <a name="option-1"></a>Optie 1
Als de klant een klein aantal toepassingen heeft, een enkele domein controller voor de hele on-premises site en failover voor de hele site, kunt u het beste Azure Site Recovery replicatie gebruiken om de domein controller machine te repliceren naar een secundaire site (dit geldt voor site-naar-site en site-naar-Azure).

#### <a name="option-2"></a>Optie 2
Als de klant een groot aantal toepassingen heeft, een Active Directory-forest wordt uitgevoerd en failover van een aantal toepassingen tegelijk mislukt, wordt u aangeraden een extra domein controller in te stellen op de DR-site (een secundaire site of in Azure).

Raadpleeg de [geautomatiseerde oplossing voor nood herstel voor Active Directory en DNS met behulp van Azure site Recovery](../site-recovery/site-recovery-active-directory.md) voor instructies bij het maken van een domein controller op de Dr-site. Voor de rest van dit document wordt ervan uitgegaan dat er een domein controller beschikbaar is op de DR-site.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Azure Site Recovery gebruiken om beveiliging van de bestands Server-VM in te scha kelen
Voor deze stap is het vereist dat u de on-premises Bestands server omgeving voorbereidt, een Azure Site Recovery kluis maakt en voorbereidt en de bestands beveiliging van de virtuele machine inschakelt.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>De on-premises Bestands server omgeving voorbereiden
1. Stel het **Gebruikersaccountbeheer zo in** dat **nooit wordt gewaarschuwd**. Dit is vereist zodat u Azure Automation-scripts kunt gebruiken om de iSCSI-doelen na een failover te verbinden door Azure Site Recovery.
   
   1. Druk op de Windows-toets + Q en zoek naar **UAC**.  
   1. Selecteer **instellingen** voor Gebruikersaccountbeheer wijzigen.  
   1. Sleep de balk naar beneden om **nooit te waarschuwen**.  
   1. Klik op **OK** en selecteer vervolgens **Ja** wanneer u hierom wordt gevraagd.  
   
      ![Instellingen voor Gebruikersaccountbeheer](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png) 

1. Installeer de VM-agent op elk van de bestands server-Vm's. Dit is vereist zodat u Azure Automation-scripts kunt uitvoeren op de virtuele machines waarvoor een failover is uitgevoerd.
   
   1. [Down load de agent](https://aka.ms/vmagentwin) naar `C:\\Users\\<username>\\Downloads` .
   1. Open Windows Power shell in de Administrator-modus (als administrator uitvoeren) en voer de volgende opdracht in om naar de download locatie te gaan:  
         `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
         
         > [!NOTE]
         > De bestands naam kan variëren afhankelijk van de versie.
      
1. Klik op **Volgende**.
1. Ga akkoord **met de gebruiksrecht overeenkomst** en klik op **volgende**.
1. Klik op **Voltooien**.
1. Maak bestands shares met behulp van volumes gehaald uit de StorSimple-opslag. Zie [de StorSimple Manager-service gebruiken voor het beheren van volumes](./index.yml)voor meer informatie.
   
   1. Druk op uw on-premises Vm's op de Windows-toets + Q en zoek naar **iSCSI**.
   1. Selecteer **iSCSI-initiator**.
   1. Selecteer het tabblad **configuratie** en kopieer de naam van de initiator.
   1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
   1. Selecteer het tabblad **StorSimple** en selecteer vervolgens de StorSimple Manager service die het fysieke apparaat bevat.
   1. Maak een of meer volume containers en maak vervolgens een of meer volumes. (Deze volumes zijn voor de bestands share (s) op de bestands server-Vm's). Kopieer de naam van de initiator en geef de juiste naam op voor de Access Control records wanneer u de volumes maakt.
   1. Selecteer het tabblad **configureren** en noteer het IP-adres van het apparaat.
   1. Ga op uw on-premises Vm's opnieuw naar de **iSCSI-initiator** en voer het IP-adres in het gedeelte snelle verbinding in. Klik op **snelle verbinding** (het apparaat moet nu zijn verbonden).
   1. Open de Azure Portal en selecteer het tabblad **volumes en apparaten** . Klik op **automatisch configureren**. Het volume dat u hebt gemaakt, wordt weer gegeven.
   1. Selecteer in de portal het tabblad **apparaten** en selecteer vervolgens **een nieuw virtueel apparaat maken.** (Dit virtuele apparaat wordt gebruikt als er een failover wordt uitgevoerd). Dit nieuwe virtuele apparaat kan offline worden bewaard om extra kosten te voor komen. Als u het virtuele apparaat offline wilt halen, gaat u naar de sectie **virtual machines** op de portal en sluit u deze af.
   1. Ga terug naar de on-premises Vm's en open schijf beheer (druk op de Windows-toets + X en selecteer **schijf beheer**).
   1. U ziet dat er extra schijven worden weer gegeven (afhankelijk van het aantal volumes dat u hebt gemaakt). Klik met de rechter muisknop op de eerste, selecteer **schijf initialiseren** en selecteer **OK**. Klik met de rechter muisknop op de sectie niet- **toegewezen** , selecteer **Nieuw eenvoudig volume**, wijs er een stationsletter aan toe en voltooi de wizard.
   1. Herhaal stap l voor alle schijven. U kunt nu alle schijven op **deze PC** weer geven in Windows Verkenner.
   1. Gebruik de functie bestands-en opslag Services om bestands shares op deze volumes te maken.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Een Azure Site Recovery kluis maken en voorbereiden
Raadpleeg de [documentatie van Azure site Recovery](../site-recovery/index.yml) om aan de slag te gaan met Azure site Recovery voordat u de bestands Server-VM beveiligt.

#### <a name="to-enable-protection"></a>Beveiliging inschakelen
1. Ontkoppel de iSCSI-doel (en) van de on-premises Vm's die u wilt beveiligen via Azure Site Recovery:
   
   1. Druk op Windows-toets + Q en zoek naar **iSCSI**.
   1. Selecteer **iSCSI-initiator instellen**.
   1. Verbreek de verbinding met het StorSimple-apparaat dat u eerder hebt verbonden. U kunt de bestands server echter een paar minuten uitschakelen bij het inschakelen van beveiliging.
      
   > [!NOTE]
   > Dit zorgt ervoor dat de bestands shares tijdelijk niet beschikbaar zijn.
   
1. [Schakel de beveiliging van de virtuele machine](../site-recovery/hyper-v-azure-tutorial.md) van de bestands Server-VM in via de Azure site Recovery Portal.
1. Wanneer de initiële synchronisatie begint, kunt u het doel opnieuw verbinding maken. Ga naar de iSCSI-initiator, selecteer het StorSimple-apparaat en klik op **verbinden**.
1. Wanneer de synchronisatie is voltooid en de status van de virtuele machine is **beveiligd**, selecteert u de virtuele machine, selecteert u het tabblad **configureren** en werkt u het netwerk van de virtuele machine dienovereenkomstig bij (dit is het netwerk waarvan de failover-VM (s) een deel van heeft). Als het netwerk niet wordt weer gegeven, betekent dit dat de synchronisatie nog steeds gaat plaatsvinden.

### <a name="enable-protection-of-storsimple-volumes"></a>Beveiliging van StorSimple-volumes inschakelen
Als u de optie **een standaard back-up voor dit volume inschakelen** voor de StorSimple-volumes niet hebt geselecteerd, gaat u naar **back-upbeleid** in de StorSimple Manager-service en maakt u een geschikt back-upbeleid voor alle volumes. We raden u aan de frequentie van back-ups in te stellen op de Recovery Point Objective (RPO) die u wilt zien voor de toepassing.

### <a name="configure-the-network"></a>Het netwerk configureren
Configureer voor de virtuele machine van de bestands Server netwerk instellingen in Azure Site Recovery zodat de VM-netwerken na een failover aan het juiste DR-netwerk zijn gekoppeld.

U kunt de virtuele machine selecteren op het tabblad **gerepliceerde items** om de netwerk instellingen te configureren, zoals wordt weer gegeven in de volgende afbeelding.

![Compute en netwerk](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Een herstelplan maken
U kunt een herstel plan maken in ASR om het proces voor het uitvoeren van de failover van de bestands shares te automatiseren. Als er een onderbreking optreedt, kunt u de bestands shares in een paar minuten met slechts één klik meenemen. Als u deze automatisering wilt inschakelen, hebt u een Azure Automation-account nodig.

#### <a name="to-create-an-automation-account"></a>Een Automation-account maken
1. Ga naar de sectie Azure Portal &gt; **Automation** .
1. Klik op de knop **toevoegen** en wordt onder Blade geopend.
   
   ![Automation-account toevoegen](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)
   
   - Naam: Voer een nieuw Automation-account in
   - Abonnement: Kies abonnement
   - Resource groep: nieuwe maken/bestaande resource groep kiezen
   - Locatie: Kies locatie, bewaar deze in hetzelfde geo/dezelfde regio waarin de StorSimple Cloud Appliance en opslag accounts zijn gemaakt.
   - Uitvoeren als-account voor Azure maken-optie **Ja** selecteren.
   
1. Ga naar het Automation-account en klik op **Runbooks** &gt; **Bladeren galerie** om alle vereiste Runbooks te importeren in het Automation-account.
1. Voeg de volgende runbooks toe door de tag voor **nood herstel** te vinden in de galerie:
   
   - Opschonen van StorSimple-volumes na een testfailover (TFO)
   - Volume containers voor failover-StorSimple
   - Volumes op StorSimple-apparaat koppelen na een failover
   - Aangepaste script extensie in azure VM verwijderen
   - Virtueel StorSimple-apparaat starten
   
      ![Bladeren in galerie](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)
   
1. Publiceer alle scripts door het runbook te selecteren in het Automation-account en op **bewerken** &gt; **publiceren** en vervolgens op **Ja** te klikken in het verificatie bericht. Na deze stap wordt het tabblad **Runbooks** als volgt weer gegeven:
   
   ![Runbooks](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)
   
1. Klik in het Automation-account op **variabelen** &gt; **een variabele toevoegen** en voeg de volgende variabelen toe. U kunt ervoor kiezen om deze assets te versleutelen. Deze variabelen zijn een specifiek herstel plan. Als uw herstel plan, dat u in de volgende stap gaat maken, naam is TestPlan, moeten de variabelen TestPlan-StorSimRegKey, TestPlan-AzureSubscriptionName, enzovoort zijn.

   - **BaseUrl**: de URL van de Resource Manager voor de Azure-Cloud. Gebruik **Get-AzEnvironment | Select-Object name, ResourceManagerUrl** cmdlet.
   - _RecoveryPlanName_**-ResourceGroupName**: de Resource Manager-groep die de StorSimple-resource heeft.
   - _RecoveryPlanName_**-managernaam**: de StorSimple-resource met het StorSimple-apparaat.
   - _RecoveryPlanName_**-DeviceName**: het StorSimple-apparaat waarvoor een failover moet worden uitgevoerd.
   - _RecoveryPlanName_**-DeviceIpAddress**: het IP-adres van het apparaat (dit kunt u vinden op het tabblad **apparaten** onder StorSimple Apparaatbeheer sectie &gt;  &gt; -instellingen groep **netwerk** &gt; **-DNS-instellingen** ).
   - _RecoveryPlanName_**-VolumeContainers**: een door komma's gescheiden teken reeks van volume containers die aanwezig moeten zijn op het apparaat waarvoor een failover moet worden uitgevoerd; bijvoorbeeld: volcon1, volcon2, volcon3.
   - _RecoveryPlanName_**-TargetDeviceName**: de StorSimple Cloud Appliance waarop de containers moeten worden gefailovert.
   - _RecoveryPlanName_**-TargetDeviceIpAddress**: het IP-adres van het doel apparaat (dit is te vinden op het tabblad instellingen van de **virtuele machine** in de &gt;  groep &gt; **netwerk** ).
   - _RecoveryPlanName_**-StorageAccountName**: de naam van het opslag account waarin het script (dat moet worden uitgevoerd op de virtuele machine die is mislukt) wordt opgeslagen. Dit kan elk opslag account zijn met ruimte om het script tijdelijk op te slaan.
   - _RecoveryPlanName_**-StorageAccountKey**: de toegangs sleutel voor het bovenstaande opslag account.
   - _RecoveryPlanName_**-VMGUIDS**: bij het beveiligen van een virtuele machine wijst Azure site Recovery elke virtuele machine een unieke id toe die de details van de virtuele machine waarvoor een failover is uitgevoerd. Als u de VMGUID wilt ophalen, selecteert u het tabblad **Recovery Services** en klikt u op Eigenschappen van **beveiligde item** &gt; **beveiligings groepen** &gt; **machines** &gt; . Als u meerdere Vm's hebt, voegt u de GUID'S toe als een door komma's gescheiden teken reeks.

     Als de naam van het herstel plan bijvoorbeeld fileServerpredayRP is, moeten uw **variabelen**, het tabblad **verbindingen** en **certificaten** als volgt worden weer gegeven nadat u alle assets hebt toegevoegd.

      ![Assets](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

1. De Runbook-module van de StorSimple 8000-serie uploaden in uw Automation-account. Gebruik de onderstaande stappen om een module toe te voegen:
   
   1. Open Power shell, maak een nieuwe map & map naar de map te wijzigen.
      
      ```
            mkdir C:\scripts\StorSimpleSDKTools
            cd C:\scripts\StorSimpleSDKTools
      ```
   1. Down load nuget CLI onder dezelfde map in stap 1-4.
      Er zijn verschillende versies van nuget.exe beschikbaar op [nuget-down loads](https://www.nuget.org/downloads). Elke download koppeling verwijst rechtstreeks naar een exe-bestand. Zorg er dus voor dat u met de rechter muisknop klikt en het bestand op uw computer opslaat in plaats van het uit te voeren vanuit de browser.
      
      ```
            wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
      ```
      
   1. De afhankelijke SDK downloaden
      
      ```
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
      ```
      
   1. Een Azure Automation Runbook-module maken voor StorSimple van de 8000-serie. Gebruik de onderstaande opdrachten om een zip-bestand van de Automation-module te maken.
         
      ```powershell
            # set path variables
            $downloadDir = "C:\scripts\StorSimpleSDKTools"
            $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

            #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
            mkdir "$moduleDir"

            copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
            copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

            #Don't change the name of the Archive
            compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
      ```
         
   1. Importeer de Azure Automation module zip-bestand (Microsoft.Azure.Management.StorSimple8000Series.zip) die in de bovenstaande stap is gemaakt. U kunt dit doen door het Automation-account te selecteren, op **modules** onder gedeelde resources te klikken en vervolgens op **een module toevoegen** te klikken.
   
   Nadat u de module StorSimple 8000 Series hebt geïmporteerd, moet het tabblad **modules** er als volgt uitzien:
   
      ![Modules](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

1. Ga naar de sectie **Recovery Services** en selecteer de Azure site Recovery kluis die u eerder hebt gemaakt.
1. Selecteer de optie **herstel plannen (site Recovery)** in groep **beheren** en maak een nieuw herstel plan als volgt:
   
   - Klik op **+ plan herstellen** knop, wordt onder Blade geopend.
      
      ![Herstelplan maken](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)
      
   - Voer een naam voor het herstel plan in, Kies bron, doel & implementatie model waarden.
   
   - Selecteer de virtuele machines uit de beveiligings groep die u wilt toevoegen in het herstel plan en klik op de knop **OK** .
   
   - Selecteer een herstel plan dat u eerder hebt gemaakt, klik op de knop **aanpassen** om de weer gave aanpassing van het herstel plan te openen.
   
   - Klik met de rechter muisknop op **alle groepen afsluiten** en klik op voorbereidende **actie toevoegen**.
   
   - Hiermee opent u de Blade actie invoegen, voert u een naam in, selecteert u de optie **primaire zijde** in de optie waar moet worden uitgevoerd, selecteert u het Automation-account (waarin u de runbooks hebt toegevoegd) en selecteert u vervolgens het runbook **failover-StorSimple-volume-containers** .
   
   - Klik met de rechter muisknop op **groep 1: start** en klik op de optie **beveiligde items toevoegen** en selecteer vervolgens de vm's die moeten worden beveiligd in het herstel plan en klik op de knop **OK** . Optioneel, als het al geselecteerde Vm's is.
   
   - Klik met de rechter muisknop op **groep 1: start** en klik vervolgens op **actie** kiezen en voeg alle volgende scripts toe:  
      
      - Start-StorSimple-virtueel-apparaat-runbook  
      - Failover-failover van StorSimple-volume-containers-runbook  
      - Mount-volumes-na failover-runbook  
      - Verwijderen-aangepast script-extensie runbook  
        
   - Voeg een hand matige actie toe na de bovenstaande vier scripts in dezelfde sectie van de **groep 1: na de stap** . Deze actie is het punt waarop u kunt controleren of alles goed werkt. Deze actie moet alleen worden toegevoegd als onderdeel van een testfailover (Selecteer alleen het **selectie vakje testfailover** ).
    
   - Voeg na de hand matige actie het **opschoon** script toe met behulp van dezelfde procedure die u hebt gebruikt voor de andere runbooks. **Sla** het herstel plan op.
    
   > [!NOTE]
   > Wanneer u een testfailover uitvoert, moet u alles controleren bij de stap hand matige actie, omdat de StorSimple-volumes die zijn gekloond op het doel apparaat, worden verwijderd als onderdeel van het opschonen nadat de hand matige actie is voltooid.
       
      ![Herstelplan](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Een testfailover uitvoeren
Raadpleeg de Active Directory hand leiding voor de [oplossing van Dr-oplossingen](../site-recovery/site-recovery-active-directory.md) voor overwegingen die specifiek zijn voor Active Directory tijdens de testfailover. De on-premises installatie wordt op dit moment niet verstoord wanneer de testfailover plaatsvindt. De StorSimple-volumes die zijn gekoppeld aan de on-premises VM, worden gekloond naar de StorSimple Cloud Appliance op Azure. Een VM voor test doeleinden wordt weer in azure gebracht en de gekloonde volumes zijn gekoppeld aan de virtuele machine.

#### <a name="to-perform-the-test-failover"></a>De testfailover uitvoeren
1. Selecteer uw Site Recovery kluis in de Azure Portal.
1. Klik op het herstel plan dat is gemaakt voor de bestands Server-VM.
1. Klik op **Testfailover testen**.
1. Selecteer het virtuele netwerk van Azure waaraan de Azure-Vm's moeten worden gekoppeld nadat de failover is uitgevoerd.
   
   ![Failover starten](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
   
1. Klik op **OK** om te beginnen met de failover. U kunt de voortgang volgen door op de virtuele machine te klikken om de eigenschappen ervan te openen of op de **taak testfailover** in kluis naam &gt; **taken** &gt; **site Recovery taken**.
1. Nadat de failover is voltooid, kunt u ook zien dat de replica Azure-machine wordt weer gegeven in de Azure Portal &gt; **virtual machines**. U kunt uw validaties uitvoeren.
1. Nadat de validaties zijn uitgevoerd, klikt u op **validaties voltooid**. Hiermee worden de StorSimple-volumes verwijderd en worden de StorSimple Cloud Appliance afgesloten.
1. Wanneer u klaar bent, klikt u op **testfailover opschonen** in het herstel plan. In Notes record en sla de opmerkingen op die zijn gekoppeld aan de testfailover. Hiermee wordt de virtuele machine verwijderd die tijdens de testfailover is gemaakt.

## <a name="perform-a-planned-failover"></a>Een geplande failover uitvoeren
   Tijdens een geplande failover wordt de on-premises bestands Server-VM zonder problemen afgesloten en wordt er een back-upmomentopname voor de cloud van de volumes op het StorSimple-apparaat gemaakt. Voor de StorSimple-volumes is een failover naar het virtuele apparaat uitgevoerd, een replica-VM wordt op Azure gebracht en de volumes zijn gekoppeld aan de virtuele machine.

#### <a name="to-perform-a-planned-failover"></a>Een geplande failover uitvoeren
1. Selecteer in de Azure Portal **Recovery Services** -kluis &gt; **herstel plannen (site Recovery)** &gt; **recoveryplan_name** gemaakt voor de bestands Server-VM.
1. Klik op de Blade herstel plan op **meer** &gt; **geplande failover**.  

   ![Scherm opname van de opties voor de geplande failover en failover.](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
1. Kies op de Blade **geplande failover bevestigen** de bron-en doel locaties en selecteer doelnet werk en klik op het selectie pictogram ✓ om het failoverproces te starten.
1. Nadat de virtuele machines van de replica zijn gemaakt, hebben ze de status in behandeling. Klik op **door voeren** om de failover door te voeren.
1. Nadat de replicatie is voltooid, worden de virtuele machines op de secundaire locatie gestart.

## <a name="perform-a-failover"></a>Een failover uitvoeren
Tijdens een niet-geplande failover worden de StorSimple-volumes naar het virtuele apparaat geleid, wordt een replica-VM op Azure gezet en worden de volumes aan de virtuele machine gekoppeld.

#### <a name="to-perform-a-failover"></a>Een failover uitvoeren
1. Selecteer in de Azure Portal **Recovery Services** -kluis &gt; **herstel plannen (site Recovery)** &gt; **recoveryplan_name** gemaakt voor de bestands Server-VM.
1. Klik op de Blade herstel plan op **meer** &gt; **failover**.  
1. Kies op de Blade **failover bevestigen** de bron-en doel locaties.
1. Selecteer **virtuele machines afsluiten en synchroniseer de meest recente gegevens** om op te geven dat site Recovery moet proberen de beveiligde virtuele machine af te sluiten en de gegevens te synchroniseren, zodat er een failover wordt uitgevoerd voor de nieuwste versie van de gegevens.
1. Na de failover hebben de virtuele machines de status in behandeling door voeren. Klik op **door voeren** om de failover door te voeren.


## <a name="perform-a-failback"></a>Failback uitvoeren
Tijdens een failback worden er na het maken van een back-up een failover naar het fysieke apparaat uitgevoerd voor StorSimple-volume containers.

#### <a name="to-perform-a-failback"></a>Een failback uitvoeren
1. Selecteer in de Azure Portal **Recovery Services** -kluis &gt; **herstel plannen (site Recovery)** &gt; **recoveryplan_name** gemaakt voor de bestands Server-VM.
1. Klik op de Blade herstel plan op **meer** &gt; **geplande failover**.  
1. Kies de bron-en doel locatie, selecteer de juiste opties voor gegevens synchronisatie en VM maken.
1. Klik op de knop **OK** om het failbackproces te starten.
   
   ![Failback starten](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Beste praktijken
### <a name="capacity-planning-and-readiness-assessment"></a>Capaciteits planning en gereedheids beoordeling
#### <a name="hyper-v-site"></a>Hyper-V-site
Gebruik het [hulp programma](https://www.microsoft.com/download/details.aspx?id=39057) voor het plannen van de gebruikers capaciteit om de server, de opslag en de netwerk infrastructuur voor uw Hyper-V replica-omgeving te ontwerpen.

#### <a name="azure"></a>Azure
U kunt het [hulp programma Azure virtual machine Readiness Assessment](https://azure.microsoft.com/downloads/vm-readiness-assessment/) uitvoeren op virtuele machines om ervoor te zorgen dat ze compatibel zijn met Azure-vm's en Azure site Recovery Services. Het hulp programma gereedheids beoordeling controleert VM-configuraties en waarschuwt wanneer configuraties niet compatibel zijn met Azure. Er wordt bijvoorbeeld een waarschuwing gegeven als een station met C: groter is dan 127 GB.

Capaciteits planning bestaat uit ten minste twee belang rijke processen:

   - On-premises virtuele Hyper-V-machines toewijzen aan Azure VM-grootten (zoals A6, A7, A8 en A9).
   - De vereiste Internet bandbreedte bepalen.

## <a name="limitations"></a>Beperkingen
- Op dit moment kan slechts één StorSimple-apparaat worden overgenomen (op een enkele StorSimple Cloud Appliance). Het scenario van een bestands server dat meerdere StorSimple-apparaten omvat, wordt nog niet ondersteund.
- Als er een fout optreedt bij het inschakelen van de beveiliging voor een VM, moet u ervoor zorgen dat u de verbinding met de iSCSI-doelen hebt verbroken.
- Alle volume containers die zijn gegroepeerd vanwege een back-upbeleid dat meerdere volume containers omvat, worden bij elkaar opgeteld.
- Er wordt een failover uitgevoerd voor alle volumes in de volume containers die u hebt gekozen.
- Voor volumes die Maxi maal 64 TB toevoegen, kan geen failover worden uitgevoerd omdat de maximale capaciteit van een enkele StorSimple Cloud Appliance 64 TB is.
- Als de geplande/niet-geplande failover mislukt en de virtuele machines worden gemaakt in azure, kunt u de virtuele machines niet opschonen. Voer in plaats daarvan een failback uit. Als u de Vm's verwijdert, kunnen de on-premises Vm's niet opnieuw worden ingeschakeld.
- Als u na een failover de volumes niet kunt zien, gaat u naar de virtuele machines, opent u schijf beheer, controleert u de schijven opnieuw en brengt u deze vervolgens online.
- In sommige gevallen kunnen de stationsletters in de DR-site afwijken van de letters on-premises. Als dit het geval is, moet u het probleem hand matig oplossen nadat de failover is voltooid.
- Time-out van failover-taak: er is een time-out opgetreden voor het StorSimple-script als de failover van volume containers meer tijd in beslag neemt dan de Azure Site Recovery limiet per script (momenteel 120 minuten).
- Time-out van back-uptaak: het StorSimple-script treedt op als de back-up van volumes meer tijd in beslag neemt dan de Azure Site Recovery limiet per script (momenteel 120 minuten).
   
  > [!IMPORTANT]
  > Voer de back-up hand matig uit vanuit de Azure Portal en voer het herstel plan opnieuw uit.
   
- Time-out van de kloon taak: in het StorSimple-script wordt een time-out opgetreden als het klonen van volumes meer tijd kost dan de Azure Site Recovery limiet per script (momenteel 120 minuten).
- Fout bij het synchroniseren van de tijd: de StorSimple-scripts hebben gefoutd met de melding dat de back-ups zijn mislukt, zelfs als de back-up is geslaagd in de portal. Een mogelijke oorzaak hiervoor is dat de tijd van het StorSimple-apparaat mogelijk niet synchroon is met de huidige tijd in de tijd zone.
   
  > [!IMPORTANT]
  > De toestel tijd synchroniseren met de huidige tijd in de tijd zone.
   
- Failover-fout van het apparaat: het StorSimple-script kan mislukken als er een failover van het apparaat is wanneer het herstel plan wordt uitgevoerd.
   
  > [!IMPORTANT]
  > Voer het herstel plan opnieuw uit nadat de failover van het apparaat is voltooid.


## <a name="summary"></a>Samenvatting
Met Azure Site Recovery kunt u een volledig geautomatiseerd plan voor herstel na nood gevallen maken voor een bestands Server-VM met bestands shares die worden gehost op StorSimple Storage. U kunt de failover binnen enkele seconden starten vanaf elke locatie in het geval van een onderbreking en de toepassing binnen een paar minuten actief maken en uitvoeren.