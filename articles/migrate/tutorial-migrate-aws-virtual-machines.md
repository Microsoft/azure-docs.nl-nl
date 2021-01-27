---
title: VM's met Amazon Web Services (AWS) EC2 ontdekken, beoordelen en migreren naar Azure
description: In dit artikel wordt beschreven hoe AWS-VM's naar Azure migreert met Azure Migrate.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: MVC
ms.openlocfilehash: 430ece58bd3dc1651ac391ba0e29515085ee507b
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878186"
---
# <a name="discover-assess-and-migrate-amazon-web-services-aws-vms-to-azure"></a>Amazon Web Services-VM's (AWS) ontdekken, beoordelen en migreren naar Azure

In deze zelfstudie ziet u hoe u AWS-VM’s (Amazon Web Services) ontdekt, beoordeelt en naar Azure migreert met behulp van Azure Migrate: Server Assessment en Azure Migrate: Servicemigratiehulpprogramma's.

> [!NOTE]
> U migreert AWS-VM's naar Azure door ze te behandelen als fysieke servers.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Controleer de vereisten voor migratie.
> * Zo bereidt u Azure-resources voor met Azure Migrate: Server Migration. Stel machtigingen in voor uw Azure-account en -resources om met Azure Migrate te werken.
> * Bereid AWS EC2-instanties voor op migratie.
> * Voeg het hulpprogramma Azure Migrate: Server Migration toe in de Azure Migrate-hub.
> * Configureer het replicatieapparaat en implementeer de configuratie.
> * Installeer de Mobility-service op de AWS-VM's die u wilt migreren.
> * Schakel replicatie in voor VM's.
> * Traceer en bewaak de replicatiestatus. 
> * Voer een testmigratie uit om te controleren of alles goed werkt.
> * Voer een volledige migratie naar Azure uit.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.

## <a name="discover-and-assess"></a>Detecteren en evalueren

Voordat u naar Azure migreert, raden we u aan een evaluatie van de VM-detectie en -migratie uit te voeren. Deze evaluatie helpt u bij een correcte instelling van de grootte van de AWS-VM's voor migratie naar Azure en bij het inschatten van de potentiële Azure-uitvoeringskosten.

U stelt als volgt een evaluatie in:

1. Volg de [zelfstudie](./tutorial-discover-physical.md) om Azure in te stellen en uw AWS-VM's voor te bereiden voor een evaluatie. Opmerking:

    - Azure Migrate gebruikt wachtwoordverificatie wanneer u AWS-instanties detecteert. AWS-instanties ondersteunen niet standaard wachtwoordverificatie. Voordat u een instantie kunt detecteren, moet u wachtwoordverificatie inschakelen.
        - Sta voor Windows-machines WinRM-poort 5985 (HTTP) toe. Zo kunt u externe WMI aanroepen.
        - Voor Linux-machines:
            1. Aanmelden bij elke Linux-machine.
            2. Open het bestand sshd_config: vi /etc/ssh/sshd_config
            3. Ga in het bestand naar de regel **PasswordAuthentication** en wijzig de waarde in **yes**.
            4. Sla het bestand op en sluit het. Start de ssh-service opnieuw.
    - Als u een rootgebruiker gebruikt voor het detecteren van uw Linux-VM's, moet u ervoor zorgen dat rootaanmelding is toegestaan op de VM's.
        1. Aanmelden bij elke Linux-machine
        2. Open het bestand sshd_config: vi /etc/ssh/sshd_config
        3. Ga in het bestand naar de regel **PermitRootLogin** en wijzig de waarde in **yes**.
        4. Sla het bestand op en sluit het. Start de ssh-service opnieuw.

2. Volg vervolgens deze [zelfstudie](./tutorial-assess-physical.md) om een Azure Migrate-project en -apparaat in te stellen om uw AWS-VM's te ontdekken en te evalueren.

We raden u aan om een evaluatie uit te voeren, maar het uitvoeren van een evaluatie is geen verplichte stap voor het migreren van VM's.



## <a name="prerequisites"></a>Vereisten 

- Zorg ervoor dat de AWS-VM’s die u wilt migreren, worden uitgevoerd met een ondersteunde versie van het besturingssysteem. Voor het doel van de migratie worden AWS-VM's behandeld als fysieke machines. Bekijk de [ondersteunde besturingssystemen en kernelversies](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) voor de migratiewerkstroom voor fysieke servers. U kunt standaardopdrachten zoals *hostnamectl* of *uname -a* gebruiken om de OS- en kernelversies voor uw Linux-VM's te controleren.  U wordt aangeraden een testmigratie (testfailover) uit te voeren om te controleren of de virtuele machine werken zoals verwacht, voordat u doorgaat met de daadwerkelijke migratie.
- Zorg ervoor dat de AWS-VM's voldoen aan de [ondersteunde configuraties](./migrate-support-matrix-physical-migration.md#physical-server-requirements) voor migratie naar Azure.
- Controleer of de AWS-VM's die u naar Azure repliceert, voldoen aan de [VM-vereisten voor Azure](./migrate-support-matrix-physical-migration.md#azure-vm-requirements).
- U moet enkele wijzigingen doorvoeren in de virtuele machines voordat u ze naar Azure migreert.
    - Voor sommige besturingssystemen worden deze wijzigingen automatisch door Azure Migrate aangebracht.
    - Het is belangrijk dat u deze wijzigingen aanbrengt voordat u begint met de migratie. Als u de VM migreert voordat u de wijzigingen doorvoert, start de VM mogelijk niet op in Azure.
Controleer de voor [Windows](prepare-for-migration.md#windows-machines) en [Linux](prepare-for-migration.md#linux-machines) vereiste wijzigingen.

### <a name="prepare-azure-resources-for-migration"></a>Azure-resources voorbereiden op migratie

Zo bereidt u Azure voor op migratie met Azure Migrate: tool Server Migration van Azure Migrate.

**Taak** | **Details**
--- | ---
**Maak een Azure Migrate-project** | Uw Azure-account heeft inzender- of eigenaarsmachtigingen nodig [om een nieuw project te maken](./create-manage-projects.md).
**Machtigingen verifiëren voor uw Azure-account** | U hebt voor uw Azure-account machtigingen nodig om een virtuele machine te maken en naar een beheerde Azure-schijf te schrijven.

### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen voor het maken van een project

1. Open in de Azure-portal het abonnement en selecteer **Toegangsbeheer (IAM)** .
2. In **Toegang controleren**, zoekt u het relevante account en klikt u hierop om machtigingen weer te geven.
3. U moet de machtigingen **Inzender** of **Eigenaar** hebben.
    - Als u net pas een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samenwerken om de rol toe te wijzen.

### <a name="assign-azure-account-permissions"></a>Machtigingen voor Azure-accounts toewijzen

Wijs de rol van Inzender voor virtuele machines toe aan het Azure-account. Deze biedt machtigingen voor het volgende:

- Het maken van een VM in de geselecteerde resourcegroep.
- Het maken van een VM in het geselecteerde virtuele netwerk.
- Schrijf naar een door Azure beheerde schijf. 

### <a name="create-an-azure-network"></a>Een Azure-netwerk maken

[Stel](../virtual-network/manage-virtual-network.md#create-a-virtual-network) een virtueel Azure-netwerk (VNet) in. Wanneer u repliceert naar Azure, worden de Azure-VM's gemaakt en gekoppeld aan het Azure VNet dat u opgeeft wanneer u de migratie instelt.

## <a name="prepare-aws-instances-for-migration"></a>AWS-instanties voorbereiden voor migratie

Om AWS voor te bereiden op migratie naar Azure, moet u een replicatieapparaat voorbereiden en implementeren voor migratie.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Een computer voorbereiden voor het replicatieapparaat

Azure Migrate: Server Migration gebruikt het replicatieapparaat om machines naar Azure te repliceren. Met het replicatieapparaat worden de volgende onderdelen uitgevoerd.

- **Configuratieserver**: De configuratieserver coördineert de communicatie tussen de AWS-omgeving en Azure, en beheert de gegevensreplicatie.
- **Processerver**: De processerver fungeert als replicatiegateway. Deze ontvangt replicatiegegevens, optimaliseert de gegevens met caching, compressie en versleuteling, en verzendt ze naar het account voor cacheopslag in Azure.

Bereid de implementatie van het apparaat als volgt voor:

- Stel een afzonderlijke EC2-VM in om het replicatieapparaat te hosten. Op deze instantie moet Windows Server 2012 R2 of Windows Server 2016 worden uitgevoerd. [Bekijk](./migrate-replication-appliance.md#appliance-requirements) de hardware-, software- en netwerkvereisten voor het apparaat.
- Het apparaat mag niet worden geïnstalleerd op een bron-VM die u wilt repliceren of op het detectie- en beoordelingsapparaat voor Azure Migrate dat u eerder hebt geïnstalleerd. Het moet op een andere VM worden geïmplementeerd.
- De bron-AWS-VM's die u wilt migreren, moeten een netwerkregel hebben die is gericht op het replicatieapparaat. Configureer de noodzakelijke beveiligingsgroepsregels om dit in te schakelen. Het is raadzaam dat het replicatieapparaat wordt geïmplementeerd in dezelfde VPC als de bron-VM's die moeten worden gemigreerd. Als het replicatie apparaat zich in een andere VPC moet bevinden, moeten de VPC's worden verbonden via VPC-peering.
- De bron-AWS-VM's communiceren met het replicatieapparaat via de poorten HTTPS 443 (indeling van controlekanaal) en TCP 9443 (gegevenstransport) inkomend voor replicatiebeheer en de gegevensoverdracht voor replicatie. Het replicatieapparaat organiseert en verzendt replicatiegegevens naar Azure via poort HTTPS 443 uitgaand. Als u deze regels wilt configureren, bewerkt u de regels voor inkomende/uitgaande regels van de beveiligingsgroep met de juiste poorten en de bron-IP-gegevens.

   ![AWS-beveiligingsgroepen ](./media/tutorial-migrate-aws-virtual-machines/aws-security-groups.png)
     
 
   ![Beveiligingsinstellingen bewerken ](./media/tutorial-migrate-aws-virtual-machines/edit-security-settings.png)

- Het replicatieapparaat maakt gebruik van MySQL. Bekijk de [opties](migrate-replication-appliance.md#mysql-installation) voor het installeren van MySQL op het apparaat.
- Controleer de vereiste Azure-URL's voor het replicatieapparaat om toegang te krijgen tot [openbare](migrate-replication-appliance.md#url-access) clouds en [overheidsclouds](migrate-replication-appliance.md#azure-government-url-access).

## <a name="set-up-the-replication-appliance"></a>Het replicatieapparaat instellen

De eerste stap van de migratie is het instellen van het replicatieapparaat. Als u het apparaat voor de migratie van AWS-VM's wilt instellen, downloadt u het installatiebestand voor het apparaat. Vervolgens voert u het uit op de [VM die u hebt voorbereid](#prepare-a-machine-for-the-replication-appliance).

### <a name="download-the-replication-appliance-installer"></a>Het installatieprogramma voor het replicatieapparaat downloaden

1. In het Azure Migrate-project > **Servers**, in **Azure Migrate: Server Migration**, klikt u op **Ontdekken**.

    ![VM's detecteren](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. In **Machines ontdekken** > **Zijn uw machines gevirtualiseerd?** selecteert u **Niet gevirtualiseerd/Anders**.
3. Selecteer in **Doelregio** de Azure-regio waarnaar u de machines wilt migreren.
4. Selecteer **Bevestig dat de doelregio voor migratie <regionaam> is**.
5. Klik op **Resources maken**. Hiermee maakt u een Azure Site Recovery-kluis op de achtergrond.
    - Als u migratie al hebt ingesteld met Azure Migrate Server Migration, kunt u deze doeloptie niet configureren, omdat er eerder resources zijn ingesteld.
    - U kunt de doelregio voor dit project niet wijzigen nadat u op deze knop hebt geklikt.
    - Als u uw virtuele machines naar een andere regio wilt migreren, moet u een nieuw/ander Azure Migrate-project maken.

6. Selecteer in **Wilt u een nieuw replicatieapparaat installeren?** **Een replicatieapparaat installeren**.
7. Download in **De software voor het replicatie apparaat downloaden en installeren** het installatieprogramma voor het apparaat en de registratiesleutel. U moet de sleutel opgeven om het apparaat te kunnen registreren. De sleutel blijft na de download vijf dagen geldig.

    ![Provider downloaden](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. Kopieer het installatiebestand voor het apparaat en het sleutelbestand naar de Windows Server 2016-machine of Windows Server 2012 AWS-VM die u voor het replicatieapparaat hebt gemaakt.
9. Voer het installatiebestand voor het replicatieapparaat uit, zoals is beschreven in de volgende procedure.  
    9.1. Selecteer onder **Voordat u begint** de optie **De configuratieserver en processerver installeren**. Selecteer vervolgens **Volgende**.   
    9.2 Selecteer in **Softwarelicentie van derden** de optie **Ik ga akkoord met de licentieovereenkomst van de derde partij**, en selecteer vervolgens **Volgende**.   
    9.3 Selecteer in **Registratie** de optie **Bladeren** en ga naar de locatie waar u het registratiesleutelbestand voor de kluis hebt opgeslagen. Selecteer **Next**.  
    9.4 Selecteer in **Internetinstellingen** de optie **Rechtstreeks verbinding maken met Azure Site Recovery zonder proxyserver**. Selecteer vervolgens **Volgende**.  
    9.5 Op de pagina **Controle op vereisten** worden controles voor verschillende items uitgevoerd. Wanneer dit is voltooid, selecteert u **Volgende**.  
    9.6 Geef in **MySQL-configuratie** een wachtwoord op voor de MySQL-database en selecteer **Volgende**.  
    9.7 Selecteer in **Details van omgeving** de optie **Nee**. U hoeft uw VM's niet te beveiligen. Selecteer vervolgens **Volgende**.  
    9.8 Selecteer in **Installatielocatie** de optie **Volgende** om de standaardinstelling te accepteren.  
    9.9 Selecteer in **Netwerkselectie** de optie **Volgende** om de standaardinstelling te accepteren.  
    9.10 Selecteer in **Samenvatting** de optie **Installeren**.   
    9.11 Bij **Voortgang van de installatie** ziet u informatie over hoe de installatie vordert. Wanneer dit is voltooid, selecteert u **Voltooien**. Er wordt een venster weergegeven met daarin een bericht over opnieuw opstarten. Selecteer **OK**.   
    9.12 Vervolgens wordt een bericht weergegeven over de wachtwoordzin voor de configuratieserververbinding. Kopieer de wachtwoordzin naar het klembord en sla de wachtwoordzin op in een tijdelijk tekst bestand op de bron-VM's. U hebt deze wachtwoordzin later nodig tijdens het installatieproces van de Mobility-service.
10. Wanneer de installatie is voltooid, wordt de wizard Apparaat configureren automatisch gestart (u kunt de wizard ook handmatig starten met behulp van de cspsconfigtool-snelkoppeling die op het bureaublad van het apparaat is gemaakt). Gebruik het tabblad Accounts beheren van de wizard om accountgegevens toe te voegen die moeten worden gebruikt voor de push-installatie van de Mobility-service. In deze zelfstudie wordt de Mobility-service handmatig geïnstalleerd op de te repliceren bron-VM's. Daarom moet u in deze stap een dummyaccount maken en doorgaan. U kunt de volgende gegevens opgeven voor het maken van het dummy-account: 'guest' als de beschrijvende naam, 'username' als de gebruikersnaam en 'password' als het wachtwoord voor het account. U gebruikt dit dummy-account in de fase waarin u de replicatie gaat inschakelen. 
11. Nadat het apparaat na de installatie opnieuw is opgestart in **Machines detecteren**, selecteert u het nieuwe apparaat in **Configuratieserver selecteren** en klikt u op **Registratie voltooien**. Bij het voltooien van de registratie worden enkele laatste taken uitgevoerd om het replicatieapparaat voor te bereiden.

    ![Registratie voltooien](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

## <a name="install-the-mobility-service"></a>De Mobility-service installeren

Er moet een Mobility-serviceagent zijn geïnstalleerd op de bron-AWS-VM's die moeten worden gemigreerd. De installatieprogramma's voor de agent zijn beschikbaar op het replicatieapparaat. Na het vinden van het juiste installatie programma installeert u de agent op elke computer die u wilt migreren. Ga als volgt te werk:

1. Meld u aan bij het replicatieapparaat.
2. Ga naar **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Zoek het installatieprogramma voor het besturingssysteem en de versie van de bron-AWS-VM's. Controleer [ondersteunde besturingssystemen](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines).
4. Kopieer het installatiebestand naar de bron-AWS-VM die u wilt migreren.
5. U hebt zo dadelijk het tekstbestand met de wachtwoordzin nodig dat is gemaakt tijdens de installatie van het replicatieapparaat.
    - Als u bent vergeten de wachtwoordzin op te slaan, kunt u de wachtwoordzin voor het replicatieapparaat weergeven met deze stap. Voer vanaf de opdrachtregel **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe-v** uit om de huidige wachtwoordzin weer te geven.
    - Kopieer de wachtwoordzin naar het klembord en sla deze op in een tijdelijk tekstbestand op de bron-VM's.

### <a name="installation-guide-for-windows-aws-vms"></a>Installatiehandleiding voor Windows AWS-VM's

1. Pak de inhoud van het installatiebestand als volgt uit naar een lokale map (bijvoorbeeld C:\Temp) op de AWS-VM:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```  

2. Voer het installatieprogramma van de Mobility-service uit:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```  

3. Registreer de agent bij het replicatieapparaat:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="installation-guide-for-linux-aws-vms"></a>Installatiehandleiding voor Linux AWS VM's

1. Pak de inhoud van de tarball van het installatieprogramma als volgt uit naar een lokale map (bijvoorbeeld /tmp/MobSvcInstaller) op de AWS-VM:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```  

2. Download het script van het installatieprogramma:
    ```
    sudo ./install -r MS -q
    ```  

3. Registreer de agent bij het replicatieapparaat:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="enable-replication-for-aws-vms"></a>Schakel replicatie in voor AWS-VM's

> [!NOTE]
> Via de portal kunt u maximaal 10 VM's voor replicatie tegelijk toevoegen. Als u meer VM's tegelijk wilt repliceren, kunt u ze toevoegen in batches van 10.

1. In het Azure Migrate-project > **Servers**, **Azure Migrate: Servermigratie** klikt u op **Repliceren**.

    ![VM's repliceren](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. Selecteer in **Repliceren** > **Broninstellingen** > **Zijn uw machines gevirtualiseerd?** **Niet gevirtualiseerd/Overige**.
3. In **On-premises apparaat** selecteert u de naam van het Azure Migrate-apparaat dat u instelt.
4. Selecteer in **Processerver** de naam van het replicatieapparaat. 
5. Selecteer bij **Gastreferenties** het dummy-account dat eerder is gemaakt tijdens het [configureren van het installatieprogramma voor de replicatiefunctie](#download-the-replication-appliance-installer) om de Mobility-service handmatig te installeren (push-installatie wordt niet ondersteund). Klik vervolgens op **Volgende: Virtuele machines**.   
 
    ![Instellingen repliceren](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)
6. Houd bij **Virtuele machines** in **Migratie-instellingen importeren uit een evaluatie?** de standaardinstelling **Nee, ik geef de migratie-instellingen handmatig op** aan.
7. Controleer elke virtuele machine die u wilt migreren. Klik vervolgens op **Volgende: Doelinstellingen**.

    ![VM's selecteren](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)

8. Selecteer in **Doelinstellingen** het abonnement en de doelregio waarnaar u migreert en geef de resourcegroep op waarin de Azure-VM's na de migratie moeten worden geplaatst.
9. Selecteer in **Virtual Network** het Azure VNet/subnet waaraan de Azure-VM's na migratie worden toegevoegd.
10. Selecteer in **Beschikbaarheidsopties**:
    -  Beschikbaarheidszone, om de gemigreerde computer vast te maken aan een specifieke beschikbaarheidszone in de regio. Gebruik deze optie om servers te distribueren die een toepassingslaag met meerdere knooppunten in de beschikbaarheidszones vormen. Als u deze optie selecteert, moet u op het tabblad Compute de beschikbaarheidszone opgeven die moet worden gebruikt voor elk van de geselecteerde computers. Deze optie is alleen beschikbaar als de doelregio die voor de migratie is geselecteerd, ondersteuning biedt voor beschikbaarheidszones
    -  Beschikbaarheidsset, om de gemigreerde machine in een beschikbaarheidsset te plaatsen. De doelresourcegroep die is geselecteerd, moet een of meer beschikbaarheidssets bevatten om deze optie te kunnen gebruiken.
    - Er is geen optie voor infrastructuurredundantie vereist als u geen van deze beschikbaarheidsconfiguraties nodig hebt voor de gemigreerde computers.
    
11. Selecteer in **Type schijfversleuteling**:
    - Versleuteling at-rest van gegevens met door platform beheerde sleutel
    - Versleuteling at-rest van gegevens met door klant beheerde sleutel
    - Dubbele versleuteling met door platform en door klant beheerde sleutels

   > [!NOTE]
   > Als u VM's met CMK wilt repliceren, moet u [een schijfversleutelingsset maken](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) in de doelresourcegroep. Met een schijfversleutelingssetobject worden beheerde schijven toegewezen aan een sleutelkluis die de CMK bevat die moet worden gebruikt voor SSE.
  
12. In **Azure Hybrid Benefit**:

    - Selecteer **Nee** als u Azure Hybrid Benefit niet wilt toepassen. Klik op **Volgende**.
    - Selecteer **Ja** als u Windows Server-computers hebt die worden gedekt met actieve softwareverzekering of Windows Server-abonnementen en u het voordeel wilt toepassen op de machines die u migreert. Klik op **Volgende**.

    ![Doelinstellingen](./media/tutorial-migrate-vmware/target-settings.png)

13. Controleer bij **Compute** naam, grootte, type besturingssysteemschijf en beschikbaarheidsconfiguratie van de VM (indien geselecteerd in de vorige stap). VM's moeten voldoen aan de [Azure-vereisten](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **VM-grootte**: Als u evaluatie-aanbevelingen gebruikt, bevat het vervolgkeuzemenu voor de VM-grootte de aanbevolen grootte. Anders kiest Azure Migrate een grootte op basis van de dichtstbijzijnde overeenkomst in het Azure-abonnement. U kunt ook handmatig een grootte kiezen in **Azure VM-grootte**.
    - **Besturingssysteemschijf**: Geef de besturingssysteemschijf (opstarten) voor de VM op. De besturingssysteemschijf is de schijf die de bootloader en het installatieprogramma van het besturingssysteem bevat.
    - **Beschikbaarheidszone**: Geef de beschikbaarheidszone op die moet worden gebruikt.
    - **Beschikbaarheidsset**: Geef de beschikbaarheidsset op die moet worden gebruikt.

![Rekeninstellingen](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

14. Geef in **Schijven** op of de VM-schijven moeten worden gerepliceerd in Azure en selecteer het schijftype (standaard SSD/HDD of premium beheerde schijven) in Azure. Klik op **Volgende**.
    - U kunt schijven uitsluiten van replicatie.
    - Als u schijven uitsluit, zijn deze na migratie niet beschikbaar in de Azure-VM. 

    ![Schijfinstellingen](./media/tutorial-migrate-physical-virtual-machines/disks.png)

15. Controleer in **Replicatie controleren en beginnen** de instellingen en klik op **Repliceren** om de eerste replicatie van de servers te beginnen.

> [!NOTE]
> U kunt de replicatie-instellingen op elk gewenst moment bijwerken voordat de replicatie begint, **Beheren** > **Machines repliceren**. De instellingen kunnen niet meer worden gewijzigd nadat de replicatie is begonnen.

## <a name="track-and-monitor-replication-status"></a>Traceer en bewaak replicatiestatus

- Wanneer u op **Repliceren** klikt, wordt een taak voor het starten van de replicatie gestart.
- Wanneer deze taak is voltooid, beginnen de VM's hun initiële replicatie naar Azure.
- Nadat de initiële replicatie is voltooid, begint de deltareplicatie. Incrementele wijzigingen van AWS-VM-schijven worden periodiek gerepliceerd naar de replicaschijven in Azure.

U kunt de taakstatus volgen in de portalmeldingen.

U kunt de replicatiestatus controleren door te klikken op **Replicerende servers** in **Azure Migrate: Server Migration**.  

![Replicatie controleren](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Een testmigratie uitvoeren

Wanneer de deltareplicatie begint, kunt u een testmigratie voor de virtuele machines uitvoeren voordat u een volledige migratie naar Azure uitvoert. De testmigratie wordt sterk aanbevolen en biedt de mogelijkheid om mogelijke problemen op te sporen en op te lossen voordat u doorgaat met de daadwerkelijke migratie. U wordt geadviseerd om dit ten minste één keer te doen voor elke VM voordat u deze migreert.

- Bij het uitvoeren van een testmigratie wordt gecontroleerd of de migratie werkt zoals verwacht, zonder dat dit van invloed is op de AWS-VM's - die operationeel blijven - en u door kunt gaan met repliceren.
- Met een testmigratie wordt de migratie gesimuleerd door een Azure-VM te maken met behulp van gerepliceerde gegevens (die meestal worden gemigreerd naar een niet-productie-VNet in uw Azure-abonnement).
- U kunt de gerepliceerde Azure-VM gebruiken om de migratie te valideren, apps te testen en problemen op te lossen voordat u de volledige migratie uitvoert.

Ga als volgt te werk om een testmigratie uit te voeren:

1. In **Migratiedoelen** > **Servers** > **Azure Migrate: Servermigratie** klikt u op **Gemigreerde servers testen**.

     ![Gemigreerde servers testen](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Klik met de rechtermuisknop op de te testen VM en klik vervolgens op **Migratie testen**.

    ![Migratie testen](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. Selecteer in **Migratie testen** het Azure Vnet waarin de Azure-VM zich na migratie bevindt. We raden u aan geen productie-VNet te gebruiken.
4. De taak **Migratie testen** wordt gestart. Houd de taak in portalmeldingen in de gaten.
5. Nadat de migratie is voltooid, bekijkt u de gemigreerde Azure-VM in **Virtuele machines** in de Azure-portal. De machinenaam heeft het achtervoegsel **-Test**.
6. Nadat de test is afgerond, klikt u met de rechtermuisknop op de Azure-VM in **Machines repliceren** en klikt u op **Testmigratie opschonen**.

    ![Migratie opschonen](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-aws-vms"></a>AWS-VM's migreren

Nadat u hebt geverifieerd dat de testmigratie naar verwachting werkt, kunt u de AWS-VM's migreren.

1. In het Azure Migrate-project > **Servers** > **Azure Migrate: Servermigratie** klikt u op **Servers repliceren**.

    ![Servers repliceren](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. Klik in **Machines repliceren** met de rechtermuisknop op de VM > **Migreren**.
3. In **Migreren** > **Virtuele machines afsluiten en geplande migratie uitvoeren zonder gegevensverlies** selecteert u **Ja** > **OK**.
    - Als u de VM niet wilt afsluiten, selecteert u **Nee**.
4. Er wordt een migratietaak gestart voor de VM. U kunt de taakstatus weergeven door rechtsboven op de portalpagina op het belpictogram te klikken of door naar de taakpagina van het hulpprogramma Server Migration te gaan (klik op Overzicht op de tegel van het hulpprogramma > selecteer Taken in het linkermenu).
5. Wanneer de taak is afgerond, kunt u de VM bekijken en beheren vanaf de pagina Virtuele machines.

### <a name="complete-the-migration"></a>Migratie voltooien

1. Nadat de migratie is voltooid, klikt u met de rechtermuisknop op de VM > **Migratie stoppen**. Er gebeurt nu het volgende:
    - Stopt de replicatie voor de AWS-VM.
    - Verwijdert de AWS-VM uit de telling van **Replicerende servers** in Azure Migrate: Server Migration.
    - De informatie over de replicatiestatus voor de virtuele machine wordt opgeschoond.
2. Installeer de [Linux](../virtual-machines/extensions/agent-linux.md)-agent op de gemigreerde computers. De Windows-agent van Azure VM is vooraf geïnstalleerd tijdens het migratieproces.
3. Voer correcties van de app uit na de migratie, zoals updates van de databaseverbindingsreeksen en webserverconfiguraties.
4. Voer acceptatietesten van de toepassing en de migratie uit op de gemigreerde toepassing die nu wordt uitgevoerd in Azure.
5. Leid het verkeer naar het gemigreerde Azure VM-exemplaar.
6. Werk eventuele interne documentatie bij met de nieuwe locatie en het nieuwe IP-adres van de Azure VM's. 




## <a name="post-migration-best-practices"></a>Best practices na de migratie

- Voor grotere flexibiliteit:
    - Houd uw gegevens veilig door back-ups van virtuele Azure VM‘s te maken met behulp van de Azure Backup-service. [Meer informatie](../backup/quick-backup-vm-portal.md).
    - Houd workloads continu beschikbaar door Azure VM‘s naar een secundaire regio te repliceren met Site Recovery. [Meer informatie](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Voor betere beveiliging:
    - Vergrendel en beperk de toegang van binnenkomend verkeer met [Just-in-time-beheer van Azure Security Center](../security-center/security-center-just-in-time.md).
    - Beperk het netwerkverkeer naar beheereindpunten met [Netwerkbeveiligingsgroepen](../virtual-network/network-security-groups-overview.md).
    - Implementeer [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) om schijven te beveiligen en gegevens te beschermen tegen diefstal en onbevoegde toegang.
    - Lees meer informatie over [IaaS-resources beveiligen](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) en bezoek het [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Voor controle en beheer:
    - Overweeg de implementatie van [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) om uw resourcegebruik en uitgaven te bewaken.



## <a name="troubleshooting--tips"></a>Probleemoplossing/tips

**Vraag:** Ik kan mijn AWS-VM niet zien in de lijst met gedetecteerde servers voor migratie   
**Antwoord:** Controleer of het replicatieapparaat aan de vereisten voldoet. Controleer of de Mobility-agent is geïnstalleerd op de bron-VM die moet worden gemigreerd en of de configuratieserver is geregistreerd. Controleer de netwerkinstellingen en firewallregels om een netwerkpad in te schakelen tussen het replicatieapparaat en de bron-AWS-VM's.  

**Vraag:** Hoe weet ik of mijn virtuele machine is gemigreerd   
**Antwoord:** Na de migratie kunt u de VM bekijken en beheren vanaf de pagina Virtuele machines. Maak verbinding met de gemigreerde VM om te valideren.  

**Vraag:** Ik kan geen VM's importeren voor migratie vanuit mijn eerder gemaakte serverevaluatieresultaten   
**Antwoord:** Op dit moment bieden we geen ondersteuning voor het importeren van evaluatieresultaten voor deze werkstroom. Als tijdelijke oplossing kunt u de evaluatie exporteren en vervolgens handmatig de VM-aanbeveling selecteren tijdens de stap Replicatie inschakelen.
  
**Vraag:** Ik krijg de fout 'Kan BIOS-GUID niet ophalen' tijdens het detecteren van mijn AWS-VM's   
**Antwoord:** Gebruik altijd de basisaanmelder voor verificatie en geen pseudo-gebruiker. Controleer ook ondersteunde besturingssystemen voor AWS-VM's.  

**Vraag:** Er zit geen voortgang in mijn replicatiestatus   
**Antwoord:** Controleer of het replicatieapparaat aan de vereisten voldoet. Controleer of u op de TCP-poort 9443 en HTTPS 443 van de replicatie-apparaat de vereiste poorten hebt ingeschakeld voor gegevensoverdracht. Controleer of er geen verouderde dubbele versies van het replicatieapparaat zijn verbonden met hetzelfde project.   

**Vraag:** Ik kan geen AWS-instanties detecteren met Azure Migrate als gevolg van een HTTP-statuscode 504 van de externe Windows-beheerservice    
**Antwoord:** Controleer de vereisten van het Azure Migrate-apparaat en de URL-toegangsbehoeften. Zorg ervoor dat er geen proxyinstellingen zijn die de registratie van het apparaat blokkeren.

**Vraag:** Moet ik wijzigingen aanbrengen voordat ik mijn AWS-VM's naar Azure migreer?   
**Antwoord:** Mogelijk moet u deze wijzigingen aanbrengen voordat u uw EC2-VM's naar Azure migreert:

- Als u cloud-init gebruikt voor uw VM-inrichting, wilt u misschien cloud-init op de virtuele machine uitschakelen voordat u deze naar Azure repliceert. De inrichtingsstappen die worden door cloud-init op de VM uitgevoerd, kunnen AWS-specifiek zijn en zijn niet geldig na de migratie naar Azure. 
- Als de VM een PV-VM is (para-gevirtualiseerd) en niet een HVM-VM, kunt u deze mogelijk niet zomaar uitvoeren op Azure, omdat para-gevirtualiseerde VM's een aangepaste opstartvolgorde in AWS gebruiken. U kunt dit mogelijk oplossen door PV-stuurprogramma's te verwijderen voordat u een migratie naar Azure uitvoert.  
- We raden u altijd aan een testmigratie uit te voeren vóór de definitieve migratie.  


**Vraag:** Kan ik AWS-VM's met het Amazon Linux-besturingssysteem migreren  
**Antwoord:** VM's met Amazon Linux kunnen niet ongewijzigd worden gemigreerd, aangezien Amazon Linux OS alleen wordt ondersteund op AWS.
Om workloads die op Amazon Linux draaien te migreren, kunt u een CentOS/RHEL VM in Azure opstarten en de workload die op de AWS Linux-machine draait migreren met een relevante workload-migratiebenadering. Afhankelijk van de werkbelasting kunnen er bijvoorbeeld werkbelastingspecifieke tools zijn om de migratie te ondersteunen, zoals databases of implementatietools in het geval van webservers.

## <a name="next-steps"></a>Volgende stappen

Onderzoek de [cloudmigratiereis](/azure/architecture/cloud-adoption/getting-started/migrate) in het Azure Cloud Adoption Framework.