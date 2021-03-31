---
title: VMware-VM's via Azure Migrate:Server Migration migreren op basis van een agent
description: Informatie over het uitvoeren van een migratie van VMware-VM’s op basis van een agent met Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: MVC
ms.openlocfilehash: 15bf8f4fde2128181664fa7b94f2479bac7ad5b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98881514"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>VMware-VM’s migreren naar Azure (op basis van een agent)

In dit artikel wordt beschreven hoe u on-premises VMware-VM’s migreert naar Azure, met behulp van het hulpprogramma [Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool), met migratie op basis van een agent.  U kunt VMware-VM's ook migreren met behulp van migratie zonder agent. [Vergelijk](server-migrate-overview.md#compare-migration-methods) de methoden.


 In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Azure voorbereiden om te werken met Azure Migrate.
> * Voorbereiden op migratie op basis van een agent. Stel een VMware-account in zodat Azure Migrate machines kan detecteren voor migratie. Stel een account in zodat u de agent Mobility-service kunt installeren op machines die u wilt migreren en een machine kunt voorbereiden om te fungeren als replicatieapparaat.
> * Het Azure Migrate: Server Migration-hulpprogramma toevoegen
> * Stel het replicatieapparaat in.
> * Repliceer VM's.
> * Voer een testmigratie uit om te controleren of alles goed werkt.
> * Voer een volledige migratie naar Azure uit.

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een haalbaarheidstest kunt instellen. Waar mogelijk maken zelfstudies gebruik van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, moet u de architectuur voor migratie op basis van een agent voor VMware [controleren](./agent-based-migration-architecture.md).

## <a name="prepare-azure"></a>Azure voorbereiden

Voer de taken uit in de tabel om Azure voor te bereiden voor migratie op basis van een agent.

**Taak** | **Details**
--- | ---
**Maak een Azure Migrate-project** | Uw Azure-account heeft inzender- of eigenaarsmachtigingen nodig om een project te maken.
**Controleer de machtigingen voor het Azure-account** | U hebt voor uw Azure-account machtigingen nodig om een virtuele machine te maken en naar een beheerde Azure-schijf te schrijven.
**Een Azure-netwerk instellen** | Stel een netwerk in waaraan Azure VM's na de migratie worden toegevoegd.

### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen voor het maken van een project
Als u geen Azure Migrate-project hebt, controleert u de machtigingen om er eentje te maken.


1. Open in de Azure-portal het abonnement en selecteer **Toegangsbeheer (IAM)** .
2. In **Toegang controleren**, zoekt u het relevante account en klikt u hierop om machtigingen weer te geven.
3. Controleer of u de machtigingen **Inzender** of **Eigenaar** hebt.

    - Als u net pas een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samenwerken om de rol toe te wijzen.
    
### <a name="assign-azure-account-permissions"></a>Machtigingen voor Azure-accounts toewijzen

Wijs de rol Inzender voor virtuele machines toe aan het account, zodat u gemachtigd bent voor het volgende:

- Het maken van een VM in de geselecteerde resourcegroep.
- Het maken van een VM in het geselecteerde virtuele netwerk.
- Schrijf naar een door Azure beheerde schijf. 


### <a name="set-up-an-azure-network"></a>Een Azure-netwerk instellen

[Stel een Azure-netwerk in](../virtual-network/manage-virtual-network.md#create-a-virtual-network). On-premises machines worden gerepliceerd naar beheerde Azure-schijven. Wanneer u een failover naar Azure voor migratie uitvoert, worden er virtuele Azure-machines gemaakt op basis van deze beheerde schijven en gekoppeld aan het Azure-netwerk dat u hebt ingesteld.

## <a name="prepare-for-migration"></a>Voorbereiden op migratie

Controleer de ondersteuningsvereisten en -machtigingen en bereid de implementatie voor van een replicatieapparaat. 

### <a name="prepare-an-account-to-discover-vms"></a>Een account voorbereiden voor het detecteren van VM's

Azure Migrate Server Migration moet toegang krijgen tot VMware-servers om VM's te detecteren die u wilt migreren. Ga als volgt te werk om het account te maken:

1. Als u een vast account wilt gebruiken, maakt u een rol op vCenter-niveau. Geef de rol een naam, bijvoorbeeld **Azure_Migrate**.
2. Wijs de rol de rechten toe die worden samengevat in onderstaande tabel.
3. Maak een gebruiker op de vCenter-server of vSphere-host. Wijs de rol toe aan de gebruiker.

#### <a name="vmware-account-permissions"></a>Machtigingen voor VMware-account

**Taak** | **Rol/machtigingen** | **Details**
--- | --- | ---
**VM-detectie** | Ten minste een alleen-lezen-gebruiker<br/><br/> Datacentrumobject –> doorgeven naar onderliggend object, rol=Alleen-lezen | Gebruiker wordt toegewezen op datacentrumniveau, en heeft toegang tot alle objecten in het datacentrum.<br/><br/> Wijs om de toegang te beperken de rol **Geen toegang** met het object **Doorgeven aan onderliggend object** toe aan de onderliggende objecten (vSphere-hosts, gegevensopslag, VM's en netwerken).
**Replicatie** |  Maak een rol (Azure_Site_Recovery) met de vereiste machtigingen en wijs de rol toe aan een VMware-gebruiker of -groep<br/><br/> Datacentrumobject –> Doorgeven naar onderliggend object, rol=Azure_Site_Recovery<br/><br/> Gegevensopslag –> Ruimte toewijzen, browsen in de gegevensopslag, bestandsbewerkingen op laag niveau, bestand verwijderen, bestanden van virtuele machines bijwerken<br/><br/> Netwerk –> Netwerk toewijzen<br/><br/> Resource –> VM toewijzen aan resourcegroep, uitgeschakelde VM migreren, ingeschakelde VM migreren<br/><br/> Taken –> Taak maken, taak bijwerken<br/><br/> Virtuele machine –> Configuratie<br/><br/> Virtuele machine –> Interactie –> vraag beantwoorden, apparaatverbinding, CD's configureren, diskettes configureren, uitschakelen, inschakelen, VMware-hulpprogramma's installeren<br/><br/> Virtuele machine -> Inventaris –> Maken, registreren, registratie opheffen<br/><br/> Virtuele machine –> Inrichten –> Downloaden van virtuele machine toestaan, toestaan dat bestanden van virtuele machines worden geüpload<br/><br/> Virtuele machine –> Momentopnamen -> Momentopnamen verwijderen | Gebruiker wordt toegewezen op datacentrumniveau, en heeft toegang tot alle objecten in het datacentrum.<br/><br/> Wijs om de toegang te beperken de rol **Geen toegang** met het object **Doorgeven aan onderliggend object** toe aan de onderliggende objecten (vSphere-hosts, gegevensopslag, VM's en netwerken).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Een account voorbereiden voor installatie van de Mobility-service

De Mobility-service moet zijn geïnstalleerd op de machines die u wilt repliceren.

- Met het replicatieapparaat van Azure Migrate kunt u een push-installatie van deze service uitvoeren wanneer u replicatie voor een machine inschakelt. U kunt deze ook handmatig installeren of een installatieprogramma gebruiken.
- In deze zelfstudie gaat u de Mobility-service installeren met een push-installatie.
- Voor de push-installatie moet u een account voorbereiden dat door Azure Migrate Server Migration kan worden gebruikt om toegang te krijgen tot de VM. Dit account wordt alleen gebruikt voor de push-installatie, als u de Mobility-service niet handmatig installeert.

U bereidt het account als volgt voor:

1. Bereid een domein of lokaal account met machtigingen voor om op de VM te installeren.
2. Als u geen domeinaccount voor Windows-VM's gebruikt, schakelt u de optie voor het externe gebruikersbeheer uit op de lokale machine door de DWORD-vermelding **LocalAccountTokenFilterPolicy** in het register toe te voegen met de waarde 1 onder **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. Voor Linux-VM’s bereidt u een hoofdaccount voor op de Linux-bronserver.


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Een machine voorbereiden voor het replicatieapparaat

Het apparaat wordt gebruikt om machines te repliceren naar Azure. Het apparaat is een enkelvoudige, maximaal beschikbare, on-premises VMware-VM die als host fungeert voor deze onderdelen:

- **Configuratieserver**: De configuratieserver coördineert de communicatie tussen on-premises en Azure, en beheert de gegevensreplicatie.
- **Processerver**: De processerver fungeert als replicatiegateway. Deze ontvangt replicatiegegevens, optimaliseert de gegevens met caching, compressie en versleuteling, en verzendt ze naar het account voor cacheopslag in Azure. De processerver installeert ook de agent Mobility-service op VM’s die u wilt repliceren en detecteert automatisch on-premises VMware-VM’s.

Bereid het apparaat als volgt voor:

- [Bekijk de vereisten voor het apparaat](migrate-replication-appliance.md#appliance-requirements). Over het algemeen stelt u het replicatieapparaat in op een VMware-VM met behulp van een gedownload OVA-bestand. Met de sjabloon maakt u een apparaat dat voldoet aan alle vereisten.
- MySQL moet op het apparaat zijn geïnstalleerd. [Bekijk](migrate-replication-appliance.md#mysql-installation) installatiemethoden.
- Controleer de [URL's voor openbare cloud](migrate-replication-appliance.md#url-access) en [Azure Government-URL's](migrate-replication-appliance.md#azure-government-url-access) waartoe het replicatieapparaat toegang moet hebben.
- [Controleer de poorten](migrate-replication-appliance.md#port-access) waartoe de replicatieapparaatmachine toegang moet hebben.



### <a name="check-vmware-requirements"></a>Vereisten voor VMware controleren

Controleer of de VMware-servers en VM's aan de vereisten voor migratie naar Azure voldoen. 

1. [Verifieer](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) de vereisten voor VMware-servers.
2. [Controleer](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) de VM-vereisten voor migratie.
3. Controleer de Azure-instellingen. De on-premises VM's die u naar Azure repliceert, moeten overeenstemmen met [vereisten voor Azure-VM's](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).
4. U moet enkele wijzigingen doorvoeren aan virtuele machines voordat u ze naar Azure migreert.
    - Het is belangrijk dat u deze wijzigingen aanbrengt voordat u begint met de migratie. Als u de virtuele machine migreert voordat u de wijzigingen doorvoert, start de VM mogelijk niet op in Azure.
    - Controleer de voor [Windows](prepare-for-migration.md#windows-machines) en [Linux](prepare-for-migration.md#linux-machines) vereiste wijzigingen.

> [!NOTE]
> Migratie op basis van een agent met Azure Migrate Server Migration is gebaseerd op de functies van de Azure Site Recovery-service. Bepaalde vereisten worden mogelijk gekoppeld aan Site Recovery-documentatie.

## <a name="set-up-the-replication-appliance"></a>Het replicatieapparaat instellen

In deze procedure wordt beschreven hoe u het apparaat instelt met een gedownloade OVA-sjabloon (OVA: Open Virtualization Application). Als u deze methode niet kunt gebruiken, kunt u het apparaat instellen [met behulp van een script](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance). 

### <a name="download-the-replication-appliance-template"></a>De sjabloon voor het replicatieapparaat downloaden

Download de sjabloon als volgt:

1. Klik in het Azure Migrate-project op **Servers** onder **Migratiedoelen**.
2. Klik op de pagina **Azure Migrate - Servers** > **Azure Migrate: Server Migration**, klikt u op **Ontdekken**.

    ![VM's detecteren](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. In **Machines ontdekken** > **Zijn de machines gevirtualiseerd?** klikt u op **Ja, met VMware vSphere-hypervisor**.
4. Selecteer in **Hoe wilt u de migratie uitvoeren?** **Met een op een agent gebaseerde replicatie**.
5. Selecteer in **Doelregio** de Azure-regio waarnaar u de machines wilt migreren.
6. Selecteer **Bevestig dat de doelregio voor migratie regionaam is**.
7. Klik op **Resources maken**. Hiermee maakt u een Azure Site Recovery-kluis op de achtergrond. U kunt de doelregio voor dit project niet wijzigen nadat u op deze knop hebt geklikt, en alle volgende migraties gaan naar deze regio.

    ![Een Recovery Services-kluis maken](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. Selecteer in **Wilt u een nieuw replicatieapparaat installeren?** **Een replicatieapparaat installeren**.
9. Klik op **Downloaden**. Hiermee wordt een OVF-sjabloon gedownload.
    ![OVA downloaden](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Noteer de naam van de resourcegroep en de Recovery Services-kluis. U hebt deze nodig tijdens de implementatie van het apparaat.


### <a name="import-the-template-in-vmware"></a>De sjabloon in VMware importeren

Nadat u de OVF-sjabloon hebt gedownload, importeert u deze in VMware om de replicatietoepassing te maken op een VMware-VM met Windows Server 2016.

1. Meld u aan bij de VMware vCenter-server of vSphere ESXi-host met behulp van de VMware vSphere-client.
2. Selecteer in het menu **Bestand** de optie **OVF-sjabloon implementeren** om de **wizard voor het implementeren van OVF-sjablonen** te starten. 
3. Voer in **Bron selecteren** de locatie van de gedownloade OVF in.
4. Selecteer **Volgende** in **Details bekijken**.
5. Accepteer de standaardinstellingen in **Naam en map selecteren** en **Configuratie selecteren**.
6. Selecteer in **Opslag selecteren** > **Indeling virtuele schijf selecteren** **Thick Provision Eager Zeroed**.
7. Accepteer de standaardinstellingen in de rest van de wizardpagina's.
8. Bij **Gereed om te voltooien** selecteert u **Inschakelen na de implementatie** > **Voltooien** om de virtuele machine in te stellen met de standaardinstellingen.

   > [!TIP]
   > Als u een extra NIC wilt toevoegen, wist u **Inschakelen na de implementatie** > **Voltooien**. De sjabloon bevat standaard één NIC. Na de implementatie kunt u meer NIC’s toevoegen.

### <a name="start-appliance-setup"></a>Installatie van apparaat starten

1. Schakel de VM in vanuit de VMware vSphere Client-console. De VM wordt opgestart in een Windows Server 2016-installatie-ervaring.
2. Accepteer de gebruiksrechtovereenkomst en voer een Administrator-wachtwoord in.
3. Meld u nadat de installatie is voltooid bij de virtuele machine aan als beheerder met behulp van het beheerderswachtwoord. De eerste keer dat u zich aanmeldt, wordt het installatieprogramma voor het replicatieapparaat (het configuratieprogramma van Azure Site Recovery) binnen enkele seconden gestart.
5. Voer een naam in die moet worden gebruikt voor het registreren van het apparaat met Server Migration. Klik op **Volgende**.
6. Het hulpprogramma controleert of de VM verbinding kan maken met Azure. Nadat de verbinding tot stand is gebracht, selecteert u **Aanmelden** om u aan te melden bij uw Azure-abonnement.
7. Wacht totdat het hulpprogramma de registratie van een Azure AD-app heeft voltooid om het apparaat te identificeren. Het apparaat wordt opnieuw opgestart.
1. Meld u opnieuw aan bij de machine. Na enkele seconden wordt de wizard voor het beheer van de configuratieserver automatisch gestart.

### <a name="register-the-replication-appliance"></a>Het replicatieapparaat registreren

Voltooi het instellen en registreren van het replicatieapparaat.

1. Selecteer in de apparaatinstallatie de optie **Connectiviteit instellen**.
2. Selecteer de NIC (standaard is er slechts één NIC) die het replicatieapparaat gebruikt voor het detecteren van VM's en voor het uitvoeren van een push-installatie van de Mobility-service op bronmachines.
3. Selecteer de NIC die het replicatieapparaat gebruikt voor de connectiviteit met Azure. Selecteer vervolgens **Opslaan**. U kunt deze instelling niet wijzigen nadat deze is geconfigureerd.
4. Als het apparaat zich achter een proxyserver bevindt, moet u proxy-instellingen opgeven.
    - Geef de proxynaam op als **http://ip-address** of **http://FQDN** . HTTPS-proxyservers worden niet ondersteund.
5. Wanneer u wordt gevraagd om het abonnement, de resourcegroepen en de kluisgegevens, voegt u de gegevens toe die u hebt genoteerd tijdens het downloaden van de sjabloon voor het apparaat.
6. Accepteer de gebruiksrechtovereenkomst in **Software van derden installeren**. Selecteer **Downloaden en installeren** om MySQL Server te installeren.
7. Selecteer **VMware PowerCLI installeren**. Zorg ervoor dat alle browservensters zijn gesloten voordat u dit doet. Selecteer vervolgens **Doorgaan**.
8. In **De configuratie van het apparaat valideren** worden de vereisten gecontroleerd voordat u doorgaat.
9. In **vCenter Server vSphere/ESXi-server configureren** voert u de FQDN of het IP-adres van de vCenter-server, of vSphere-host, in waar de VM's die u wilt repliceren zich bevinden. Voer de poort in waarop de server luistert. Voer een beschrijvende naam in voor de VMware-server in de kluis.
10. Voer de referenties in voor het account dat u [hebt gemaakt](#prepare-an-account-to-discover-vms) voor VMware-detectie. Selecteer **Toevoegen** > **Doorgaan**.
11. In **Referenties voor de virtuele machine configureren** voert u de referenties in die u [hebt gemaakt](#prepare-an-account-for-mobility-service-installation) voor de push-installatie van de Mobility-service, toen u replicatie voor virtuele machines hebt ingeschakeld.  
    - Voor Windows-machines moet het account lokale administrator-machtigingen hebben op de machines die u wilt repliceren.
    - Geef voor Linux de details voor de superuser op.
12. Selecteer **Configuratie voltooien** om de registratie te voltooien.


Nadat het replicatieapparaat is geregistreerd, maakt Azure Migrate Server Assessment verbinding met VMware-servers met behulp van de opgegeven instellingen, en detecteert VM's. U kunt gedetecteerde VM's weergeven in **Beheren** > **Gedetecteerde items** op het tabblad **Overige**.



## <a name="replicate-vms"></a>VM's repliceren

Selecteer VM's voor migratie.

> [!NOTE]
> U kunt in de portal maximaal 10 machines tegelijk selecteren voor replicatie. Als u er meer moet repliceren, kunt u ze groeperen in batches van 10.

1. In het Azure Migrate-project > **Servers**, **Azure Migrate: Servermigratie** klikt u op **Repliceren**.

    ![Schermafbeelding van het venster Servers in Azure Migrate. De knop Repliceren is geselecteerd in Azure Migrate: Servermigratie onder Hulpprogramma voor migratie.](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. In **Repliceren** > **Broninstellingen** > **Zijn uw machines gevirtualiseerd?** selecteert u **Ja, met VMware vSphere**.
3. In **On-premises apparaat** selecteert u de naam van het Azure Migrate-apparaat dat u instelt.
4. Geef in **vCenter-Server** de naam op van de vCenter-Server die de VM's beheert of de vSphere-server waarop de VM's worden gehost.
5. Selecteer in **Processerver** de naam van het replicatieapparaat.
6. Geef in **Gastreferenties** het VM-beheerdersaccount op dat wordt gebruikt voor de push-installatie van de Mobility-service. Klik vervolgens op **Volgende: Virtuele machines**.

    ![Schermafbeelding van het tabblad Broninstellingen in het venster Repliceren. Het veld Gastreferenties is gemarkeerd en de waarde is ingesteld op VM-beheerdersaccount.](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. Selecteer in **Virtuele machines** de machines die u wilt repliceren.

    - Als u een evaluatie voor de VM's hebt uitgevoerd, kunt u aanbevelingen voor de VM-grootte en het schijftype (premium/standard) toepassen vanuit de resultaten. Als u dit wilt doen, selecteert u in **Wilt u de migratie-instellingen van een Azure Migrate-evaluatie importeren?** de optie **Ja**.
    - Als u geen evaluatie hebt uitgevoerd of als u de instellingen daarvan niet wilt gebruiken, selecteert u de optie **Nee**.
    - Als u hebt geselecteerd dat u de evaluatie wilt gebruiken, selecteert u de VM-groep en vervolgens de naam van de evaluatie.
8. Selecteer in **Beschikbaarheidsopties**:
    -  Beschikbaarheidszone, om de gemigreerde computer vast te maken aan een specifieke beschikbaarheidszone in de regio. Gebruik deze optie om servers te distribueren die een toepassingslaag met meerdere knooppunten in de beschikbaarheidszones vormen. Als u deze optie selecteert, moet u op het tabblad Compute de beschikbaarheidszone opgeven die moet worden gebruikt voor elk van de geselecteerde computers. Deze optie is alleen beschikbaar als de doelregio die voor de migratie is geselecteerd, ondersteuning biedt voor beschikbaarheidszones
    -  Beschikbaarheidsset, om de gemigreerde machine in een beschikbaarheidsset te plaatsen. De doelresourcegroep die is geselecteerd, moet een of meer beschikbaarheidssets bevatten om deze optie te kunnen gebruiken.
    - Er is geen optie voor infrastructuurredundantie vereist als u geen van deze beschikbaarheidsconfiguraties nodig hebt voor de gemigreerde computers.
9. Controleer elke virtuele machine die u wilt migreren. Klik vervolgens op **Volgende: Doelinstellingen**.
10. Selecteer in **Doelinstellingen** het abonnement en de doelregio waarnaar u migreert en geef de resourcegroep op waarin de Azure-VM's na de migratie moeten worden geplaatst.
11. Selecteer in **Virtual Network** het Azure VNet/subnet waaraan de Azure-VM's na migratie worden toegevoegd.
12. Selecteer in **Beschikbaarheidsopties**:
    -  Beschikbaarheidszone, om de gemigreerde computer vast te maken aan een specifieke beschikbaarheidszone in de regio. Gebruik deze optie om servers te distribueren die een toepassingslaag met meerdere knooppunten in de beschikbaarheidszones vormen. Als u deze optie selecteert, moet u op het tabblad Compute de beschikbaarheidszone opgeven die moet worden gebruikt voor elk van de geselecteerde computers. Deze optie is alleen beschikbaar als de doelregio die voor de migratie is geselecteerd, ondersteuning biedt voor beschikbaarheidszones
    -  Beschikbaarheidsset, om de gemigreerde machine in een beschikbaarheidsset te plaatsen. De doelresourcegroep die is geselecteerd, moet een of meer beschikbaarheidssets bevatten om deze optie te kunnen gebruiken.
    - Er is geen optie voor infrastructuurredundantie vereist als u geen van deze beschikbaarheidsconfiguraties nodig hebt voor de gemigreerde computers.
13. Selecteer in **Type schijfversleuteling**:
    - Versleuteling at-rest van gegevens met door platform beheerde sleutel
    - Versleuteling at-rest van gegevens met door klant beheerde sleutel
    - Dubbele versleuteling met door platform en door klant beheerde sleutels

   > [!NOTE]
   > Als u VM's met CMK wilt repliceren, moet u [een schijfversleutelingsset maken](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) in de doelresourcegroep. Met een schijfversleutelingssetobject worden beheerde schijven toegewezen aan een sleutelkluis die de CMK bevat die moet worden gebruikt voor SSE.
  
14. In **Azure Hybrid Benefit**:

    - Selecteer **Nee** als u Azure Hybrid Benefit niet wilt toepassen. Klik op **Volgende**.
    - Selecteer **Ja** als u Windows Server-computers hebt die worden gedekt met actieve softwareverzekering of Windows Server-abonnementen en u het voordeel wilt toepassen op de machines die u migreert. Klik op **Volgende**.

    ![Doelinstellingen](./media/tutorial-migrate-vmware/target-settings.png)

15. Controleer bij **Compute** naam, grootte, type besturingssysteemschijf en beschikbaarheidsconfiguratie van de VM (indien geselecteerd in de vorige stap). VM's moeten voldoen aan de [Azure-vereisten](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

   - **VM-grootte**: Als u evaluatie-aanbevelingen gebruikt, bevat het vervolgkeuzemenu voor de VM-grootte de aanbevolen grootte. Anders kiest Azure Migrate een grootte op basis van de dichtstbijzijnde overeenkomst in het Azure-abonnement. U kunt ook handmatig een grootte kiezen in **Azure VM-grootte**. 
    - **Besturingssysteemschijf**: Geef de besturingssysteemschijf (opstarten) voor de VM op. De besturingssysteemschijf is de schijf die de bootloader en het installatieprogramma van het besturingssysteem bevat. 
    - **Beschikbaarheidszone**: Geef de beschikbaarheidszone op die moet worden gebruikt.
    - **Beschikbaarheidsset**: Geef de beschikbaarheidsset op die moet worden gebruikt.

16. Geef in **Schijven** op of de VM-schijven moeten worden gerepliceerd in Azure en selecteer het schijftype (standaard SSD/HDD of premium beheerde schijven) in Azure. Klik op **Volgende**.
    - U kunt schijven uitsluiten van replicatie.
    - Als u schijven uitsluit, zijn deze na migratie niet beschikbaar in de Azure-VM. 

17. Controleer in **Replicatie controleren en beginnen** de instellingen en klik op **Repliceren** om de eerste replicatie van de servers te beginnen.

> [!NOTE]
> U kunt de replicatie-instellingen op elk gewenst moment bijwerken voordat de replicatie begint, **Beheren** > **Machines repliceren**. De instellingen kunnen niet meer worden gewijzigd nadat de replicatie is begonnen.


## <a name="track-and-monitor"></a>Bijhouden en controleren

1. Houd de taakstatus bij in de portalmeldingen. 

    ![Taak bijhouden](./media/tutorial-migrate-vmware-agent/jobs.png)
    
2. U kunt de replicatiestatus controleren door te klikken op **Replicerende servers** in **Azure Migrate: Server Migration**.

    ![Replicatie controleren](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

Replicatie vindt als volgt plaats:
- Wanneer deze taak is voltooid, beginnen de machines hun initiële replicatie naar Azure.
- Nadat de initiële replicatie is voltooid, begint de deltareplicatie. Incrementele wijzigingen van on-premises schijven worden periodiek gerepliceerd naar de replicaschijven in Azure.


## <a name="run-a-test-migration"></a>Een testmigratie uitvoeren


Wanneer de deltareplicatie begint, kunt u een testmigratie voor de virtuele machines uitvoeren voordat u een volledige migratie naar Azure uitvoert. We raden u ten zeerste aan om dit ten minste één keer te doen voor elke machine voordat u deze migreert.

- Bij het uitvoeren van een testmigratie wordt gecontroleerd of de migratie werkt zoals verwacht, zonder dat dit van invloed is op de on-premises machines - die operationeel blijven - en u door kunt gaan met repliceren. 
- Met een testmigratie wordt de migratie gesimuleerd door een Azure-VM te maken met behulp van gerepliceerde gegevens (die meestal worden gemigreerd naar een niet-productie-VNet in uw Azure-abonnement).
- U kunt de gerepliceerde Azure-VM gebruiken om de migratie te valideren, apps te testen en problemen op te lossen voordat u de volledige migratie uitvoert.

Ga als volgt te werk om een testmigratie uit te voeren:


1. In **Migratiedoelen** > **Servers** > **Azure Migrate: Servermigratie** klikt u op **Gemigreerde servers testen**.

     ![Gemigreerde servers testen](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Klik met de rechtermuisknop op de te testen VM en klik vervolgens op **Migratie testen**.

    ![Migratie testen](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. Selecteer in **Migratie testen** het Azure Vnet waarin de Azure-VM zich na migratie bevindt. We raden u aan geen productie-VNet te gebruiken.
4. De taak **Migratie testen** wordt gestart. Houd de taak in portalmeldingen in de gaten.
5. Nadat de migratie is voltooid, bekijkt u de gemigreerde Azure-VM in **Virtuele machines** in de Azure-portal. De machinenaam heeft het achtervoegsel **-Test**.
6. Nadat de test is afgerond, klikt u met de rechtermuisknop op de Azure-VM in **Machines repliceren** en klikt u op **Testmigratie opschonen**.

    ![Migratie opschonen](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Virtuele machines migreren

Nadat u hebt geverifieerd dat de testmigratie naar verwachting werkt, kunt u de on-premises machines migreren.

1. In het Azure Migrate-project > **Servers** > **Azure Migrate: Servermigratie** klikt u op **Servers repliceren**.

    ![Servers repliceren](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. Klik in **Machines repliceren** met de rechtermuisknop op de VM > **Migreren**.
3. In **Migreren** > **Virtuele machines afsluiten en geplande migratie uitvoeren zonder gegevensverlies** selecteert u **Ja** > **OK**.
    - Standaard sluit Azure Migrate de on-premises VM af, zodat er minimaal gegevensverlies optreedt. 
    - Als u de VM niet wilt afsluiten, selecteert u **Nee**
4. Er wordt een migratietaak gestart voor de VM. Volg de taak in Azure-meldingen.
5. Nadat de taak is afgerond, kunt u de VM bekijken en beheren vanaf de pagina **Virtuele machines**.

## <a name="complete-the-migration"></a>Migratie voltooien

1. Nadat de migratie is voltooid, klikt u met de rechtermuisknop op de VM > **Migratie stoppen**. Er gebeurt nu het volgende:
    - De replicatie van de on-premises machine wordt gestopt.
    - De machine wordt verwijderd uit het aantal **Replicerende servers** in Azure Migrate: Server Migration.
    - De informatie over de replicatiestatus voor de virtuele machine wordt opgeschoond.
2. Installeer de Azure VM [Windows](../virtual-machines/extensions/agent-windows.md)- of [Linux](../virtual-machines/extensions/agent-linux.md)-agent op de gemigreerde computers.
3. Voer correcties van de app uit na de migratie, zoals updates van de databaseverbindingsreeksen en webserverconfiguraties.
4. Voer acceptatietesten van de toepassing en de migratie uit op de gemigreerde toepassing die nu wordt uitgevoerd in Azure.
5. Leid het verkeer naar het gemigreerde Azure VM-exemplaar.
6. Verwijder de on-premises VM's uit uw lokale VM-inventaris.
7. Verwijder de on-premises VM's uit de lokale back-ups.
8. Werk eventuele interne documentatie bij met de nieuwe locatie en het nieuwe IP-adres van de Azure VM's. 

## <a name="post-migration-best-practices"></a>Best practices na de migratie

- On-premises
    - Leid appverkeer via de app die wordt uitgevoerd op het gemigreerde Azure VM-exemplaar.
    - Verwijder de on-premises VM's uit uw lokale VM-inventaris.
    - Verwijder de on-premises VM's uit de lokale back-ups.
    - Werk eventuele interne documentatie bij met de nieuwe locatie en het nieuwe IP-adres van de Azure VM's.
- Pas de VM-instellingen voor Azure aan na migratie:
    - De [Azure VM-agent](../virtual-machines/extensions/agent-windows.md) beheert de interactie van de VM met de Azure-Infrastructuurcontroller. Dit is vereist voor sommige Azure-services, zoals Azure Backup, Azure Site Recovery en Azure-beveiliging. Bij het migreren van VMare VM's met migratie op basis van een agent installeert het installatieprogramma van de Mobility-service de Azure VM-agent op Windows-computers. Voor Linux VM‘s wordt aanbevolen de agent te installeren na de migratie.
    - Verwijder de Mobility-service handmatig van de Azure-VM na de migratie.
    - Verwijder de VMware-hulpprogramma's handmatig na de migratie.
- Doe het volgende in Azure:
    - Voer correcties van de app uit na de migratie, zoals updates van de databaseverbindingsreeksen en webserverconfiguraties.
    - Voer acceptatietesten van de toepassing en de migratie uit op de gemigreerde toepassing die nu wordt uitgevoerd in Azure.
- Bedrijfscontinuïteit/herstel na noodgevallen
    - Houd uw gegevens veilig door back-ups van virtuele Azure VM‘s te maken met behulp van de Azure Backup-service. [Meer informatie](../backup/quick-backup-vm-portal.md).
    - Houd workloads continu beschikbaar door Azure VM‘s naar een secundaire regio te repliceren met Site Recovery. [Meer informatie](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Voor betere beveiliging:
    - Vergrendel en beperk de toegang van binnenkomend verkeer met [Just-in-time-beheer van Azure Security Center](../security-center/security-center-just-in-time.md).
    - Beperk het netwerkverkeer naar beheereindpunten met [Netwerkbeveiligingsgroepen](../virtual-network/network-security-groups-overview.md).
    - Implementeer [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) om schijven te beveiligen en gegevens te beschermen tegen diefstal en onbevoegde toegang.
    - Lees meer informatie over [IaaS-resources beveiligen](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) en bezoek het [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Voor controle en beheer:
    - Overweeg de implementatie van [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) om uw resourcegebruik en uitgaven te bewaken.




 ## <a name="next-steps"></a>Volgende stappen

Onderzoek de [cloudmigratiereis](/azure/architecture/cloud-adoption/getting-started/migrate) in het Azure Cloud Adoption Framework.