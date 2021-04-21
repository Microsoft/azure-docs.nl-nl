---
title: Een Azure Migrate privé-eindpunten gebruiken
description: Gebruik Azure Migrate private link-ondersteuning om te ontdekken, evalueren en migreren met behulp van private link.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/07/2020
ms.openlocfilehash: e4feaa8f1b30bfe31f4e645943f766b5736150b3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818364"
---
# <a name="using-azure-migrate-with-private-endpoints"></a>Een Azure Migrate privé-eindpunten gebruiken  

In dit artikel wordt beschreven hoe u Azure Migrate gebruiken om servers te ontdekken, te evalueren en te migreren via een particulier netwerk met behulp van [Azure Private Link.](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) 

U kunt de [hulpprogramma's Azure Migrate: Detectie](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-discovery-and-assessment-tool) en evaluatie en [Azure Migrate: Servermigratie](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool) gebruiken om privé en veilig verbinding te maken met de Azure Migrate-service via een persoonlijke ExpressRoute-peering of een site-naar-site-VPN-verbinding, met behulp van azure private link. 

De verbindingsmethode voor privé-eindpunten wordt aanbevolen wanneer er een organisatievereiste is om toegang te krijgen tot de Azure Migrate-service en andere Azure-resources zonder dat deze via openbare netwerken worden uitgevoerd. U kunt ook de private link-ondersteuning gebruiken om uw bestaande expressRoute-privé-peeringcircuits te gebruiken voor betere bandbreedte- of latentievereisten. 

## <a name="support-requirements"></a>Ondersteuningsvereisten 

### <a name="required-permissions"></a>Vereiste machtigingen

**Machtigingen voor Inzender en** **Gebruikerstoegangbeheerder** of Eigenaar voor het abonnement. 

### <a name="supported-scenarios-and-tools"></a>Ondersteunde scenario's en hulpprogramma's

**Implementatie** | **Details** | **Hulpprogramma's** 
--- | --- | ---
**Detectie en evaluatie** | Voer een agentloze detectie en evaluatie op schaal uit van uw servers die worden uitgevoerd op elk platform: hypervisorplatforms zoals [VMware vSphere](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware) [of Microsoft Hyper-V,](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v)openbare clouds zoals [AWS](https://docs.microsoft.com/azure/migrate/tutorial-discover-aws) of [GCP,](https://docs.microsoft.com/azure/migrate/tutorial-discover-gcp)of zelfs [bare-metalservers](https://docs.microsoft.com/azure/migrate/tutorial-discover-physical). | Azure Migrate: Detectie en evaluatie  <br/> 
**Software-inventaris** | Apps, rollen en functies ontdekken die worden uitgevoerd op VMware-VM's. | Azure Migrate: Detectie en evaluatie  
**Visualisatie van afhankelijkheden** | Gebruik de mogelijkheid voor afhankelijkheidsanalyse om afhankelijkheden tussen servers te identificeren en te begrijpen. <br/> [Visualisatie van afhankelijkheid zonder agent](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies-agentless) wordt standaard ondersteund met ondersteuning Azure Migrate private link. <br/>[Voor visualisatie van op agent gebaseerde afhankelijkheden](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) is een internetverbinding vereist. [meer informatie over](https://docs.microsoft.com/azure/azure-monitor/logs/private-link-security) het gebruik van privé-eindpunten voor visualisatie van afhankelijkheden op basis van een agent. | Azure Migrate: Detectie en evaluatie |
**Migratie** | Voer [hyper-V-migraties](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v) zonder agent uit of gebruik de op agent gebaseerde benadering voor het migreren van uw [VMware-VM's,](./tutorial-migrate-vmware-agent.md) [Hyper-V-VM's,](./tutorial-migrate-physical-virtual-machines.md)fysieke [servers,](./tutorial-migrate-physical-virtual-machines.md)VM's die worden uitgevoerd op [AWS,](./tutorial-migrate-aws-virtual-machines.md)VM's die worden uitgevoerd op [GCP](https://docs.microsoft.com/azure/migrate/tutorial-migrate-gcp-virtual-machines)of VM's die worden uitgevoerd op een andere virtualisatieprovider. | Azure Migrate: Server Migration
 
>[!Note]
>
> [Voor VMware-migraties zonder agent](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware) is internettoegang of connectiviteit via ExperessRoute Microsoft-peering vereist. <br/> [Meer informatie over](https://docs.microsoft.com/azure/migrate/replicate-using-expressroute) het gebruik van privé-eindpunten om replicaties uit te voeren via persoonlijke ExpressRoute-peering of een site-naar-site-VPN-verbinding (S2S).  <br/><br/> 
   
#### <a name="other-integrated-tools"></a>Andere geïntegreerde hulpprogramma's

Sommige hulpprogramma's voor migratie kunnen mogelijk geen gebruiksgegevens uploaden naar het Azure Migrate project als openbare netwerktoegang is uitgeschakeld. Het Azure Migrate project moet zo worden geconfigureerd dat verkeer van alle netwerken gegevens kan ontvangen van andere aanbiedingen van Microsoft of externe onafhankelijke [softwareleveranciers (ISV's).](https://docs.microsoft.com/azure/migrate/migrate-services-overview#isv-integration) 


Als u openbare netwerktoegang voor het Azure Migrate-project wilt inschakelen, gaat u naar de pagina Azure Migrate **eigenschappen** op de Azure Portal, selecteert u **Nee** en selecteert u **Opslaan.**

![Diagram waarin wordt weergegeven hoe u de modus voor netwerktoegang kunt wijzigen.](./media/how-to-use-azure-migrate-with-private-endpoints/migration-project-properties.png)

### <a name="other-considerations"></a>Andere overwegingen   

**Overwegingen** | **Details**
--- | --- 
**Prijzen** | Zie Prijzen voor Azure Blob en [Prijzen van Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/) Private Link voor [prijsinformatie.](https://azure.microsoft.com/pricing/details/private-link/)  
**Vereisten voor het virtuele netwerk** | Het ExpressRoute-/VPN-gateway-eindpunt moet zich bevinden in het geselecteerde virtuele netwerk of in een virtueel netwerk dat er verbinding mee maakt. Mogelijk hebt u ~15 IP-adressen in het virtuele netwerk nodig.  

## <a name="create-a-project-with-private-endpoint-connectivity"></a>Een project maken met connectiviteit van privé-eindpunten

Gebruik dit [artikel om](https://docs.microsoft.com/azure/migrate/create-manage-projects#create-a-project-for-the-first-time) een nieuw project Azure Migrate instellen. 

> [!Note]
> U kunt de connectiviteitsmethode niet wijzigen in connectiviteit met privé-eindpunten voor bestaande Azure Migrate projecten.

Geef in **de sectie** Geavanceerde configuratie de onderstaande gegevens op om een privé-eindpunt voor uw Azure Migrate maken.
- Kies **privé-eindpunt** in **Connectiviteitsmethode.** 
- Laat **in Openbare eindpunttoegang uitschakelen** de standaardinstelling Nee **staan.** Sommige hulpprogramma's voor migratie kunnen mogelijk geen gebruiksgegevens uploaden naar het Azure Migrate project als openbare netwerktoegang is uitgeschakeld. [Meer informatie.](#other-integrated-tools)
- Selecteer **in Virtueel netwerkabonnement** het abonnement voor het virtuele netwerk van het privé-eindpunt. 
- Selecteer **in Virtueel** netwerk het virtuele netwerk voor het privé-eindpunt. Het Azure Migrate en andere softwareonderdelen die verbinding moeten maken met het Azure Migrate-project, moeten zich in dit netwerk of een verbonden virtueel netwerk bevindt.
- Selecteer **in Subnet** het subnet voor het privé-eindpunt. 

Selecteer **Maken**. Wacht een paar minuten tot het Azure Migrate-project is geïmplementeerd. Sluit deze pagina niet terwijl het maken van het project wordt uitgevoerd.

![Project maken](./media/how-to-use-azure-migrate-with-private-endpoints/create-project.png)

    
Hiermee maakt u een migratieproject en koppelt u er een privé-eindpunt aan. 

## <a name="discover-and-assess-servers-for-migration-using-azure-private-link"></a>Servers voor migratie ontdekken en evalueren met behulp van Azure Private Link 

### <a name="set-up-the-azure-migrate-appliance"></a>Het Azure Migrate-apparaat instellen 

1. Selecteer **in Machines ontdekken** Zijn uw machines  >  **gevirtualiseerd?** het servertype.
2. Geef **in Azure Migrate projectsleutel** genereren een naam op voor het Azure Migrate apparaat. 
3. Selecteer **Sleutel genereren om** de vereiste Azure-resources te maken. 

    > [!Important]
    > Sluit de pagina Computers detecteren niet tijdens het maken van resources.  
    - Bij deze stap maakt Azure Migrate een sleutelkluis, opslagaccount, Recovery Services-kluis (alleen voor VMware-migraties zonder agent) en enkele interne resources en koppelt een privé-eindpunt aan elke resource. De privé-eindpunten worden gemaakt in het virtuele netwerk dat is geselecteerd tijdens het maken van het project.  
    - Zodra de privé-eindpunten zijn gemaakt, worden de DNS CNAME-resourcerecords voor de Azure Migrate-resources bijgewerkt naar een alias in een subdomein met het voorvoegsel 'privatelink'. Standaard maakt Azure Migrate ook een privé-DNS-zone die overeenkomt met het subdomein 'privatelink' voor elk resourcetype en voegt DNS A-records toe voor de bijbehorende privé-eindpunten. Hierdoor kunnen het Azure Migrate-apparaat en andere softwareonderdelen die zich in het bronnetwerk bevindt, de Azure Migrate-resource-eindpunten op privé-IP-adressen bereiken.  
    - Azure Migrate maakt ook een [](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) beheerde identiteit voor het migratieproject mogelijk en verleent machtigingen aan de beheerde identiteit om veilig toegang te krijgen tot het opslagaccount.  

4. Nadat de sleutel is gegenereerd, kopieert u de sleutelgegevens om het apparaat te configureren en te registreren.   

#### <a name="download-the-appliance-installer-file"></a>Het installatiebestand van het apparaat downloaden  

> [!Note]
> Als u problemen hebt met het downloaden van het installatiebestand van het apparaat, maakt u een ondersteuningscase.

Azure Migrate: Detectie en evaluatie maken gebruik van een lichtgewicht Azure Migrate apparaat. Het apparaat voert serverdetectie uit en verzendt serverconfiguratie- en prestatiemetagegevens naar Azure Migrate.

Als u het apparaat wilt instellen, downloadt u het ingepakte bestand met het installatiescript uit de portal. Kopieer het ingepakte bestand op de server die het apparaat gaat hosten. 

Zorg ervoor dat de server voldoet aan de [hardwarevereisten](https://docs.microsoft.com/azure/migrate/migrate-appliance) voor het gekozen scenario (VMware/Hyper-V/Fysiek of anders) en verbinding kan maken met de vereiste Azure-URL's [:](./migrate-appliance.md#public-cloud-urls-for-private-link-connectivity) openbare [clouds](./migrate-appliance.md#government-cloud-urls-for-private-link-connectivity) en overheids clouds.

Nadat u het ingepakte bestand hebt gedownload, moet u het installatiescript uitvoeren om het apparaat te implementeren.

#### <a name="run-the-script"></a>Het script uitvoeren

1. Pak het zip-bestand uit naar een map op de server die als host moet fungeren voor het apparaat. 
2. Start PowerShell op de computer met beheerdersbevoegdheden (verhoogde bevoegdheden).
3. Wijzig de PowerShell-map in de map met de inhoud die is geëxtraheerd uit het gedownloade ingepakte bestand.
4. Voer het script **alsAzureMigrateInstaller.ps1** uit:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1```
   
5. Nadat het script is uitgevoerd, wordt configuratiebeheer voor het apparaat start, zodat u het apparaat kunt configureren. Als u problemen ondervindt, bekijkt u de scriptlogboeken op C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="configure-the-appliance-and-start-continuous-discovery"></a>Het apparaat configureren en continue detectie starten

Open een browser op een computer die verbinding kan maken met de apparaatserver en open de URL van het apparaatconfiguratiebeheer: `https://appliance name or IP address: 44368` . U kunt configuration manager ook openen vanaf het bureaublad van de apparaatserver door de snelkoppeling voor Configuration Manager te selecteren.

#### <a name="set-up-prerequisites"></a>Vereisten instellen

1. Lees de informatie van derden en accepteer de **licentievoorwaarden.**    
 
2. Ga in het configuratiebeheer-> **vereisten instellen** als volgt te werk:
   - **Connectiviteit:** het apparaat controleert op toegang tot de vereiste URL's. Als de server gebruikmaakt van een proxy:
     - Selecteer **Proxy instellen om het proxyadres** of de `http://ProxyIPAddress` `http://ProxyFQDN` luisterpoort op te geven.
     - Geef referenties op als de proxy verificatie nodig heeft. Alleen HTTP-proxy wordt ondersteund.
     - Als u wilt, kunt u een lijst met URL's/IP-adressen toevoegen die de proxyserver moeten omzeilen. Als u persoonlijke ExpressRoute-peering gebruikt, moet u deze [URL's omzeilen.](https://docs.microsoft.com/azure/migrate/replicate-using-expressroute#configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations)
     - U moet Opslaan selecteren om **de** configuratie te registreren als u de details van de proxyserver of toegevoegde URL's/IP-adressen hebt bijgewerkt om de proxy te omzeilen.
     
        > [!Note]
        > Als er tijdens de connectiviteitscontrole een foutbericht wordt weergegeven met aka.ms/*-koppeling en u niet wilt dat het apparaat via internet toegang heeft tot deze URL, moet u de service voor automatisch bijwerken op het apparaat uitschakelen door de stappen hier te [**volgen.**](https://docs.microsoft.com/azure/migrate/migrate-appliance#turn-off-auto-update) Nadat de automatische update is uitgeschakeld, wordt de aka.ms/* URL-connectiviteitscontrole overgeslagen. 

   - **Tijdsynchronisatie**: de tijd op het apparaat moet zijn gesynchroniseerd met internettijd zodat detectie goed werkt.
   - **Updates installeren**: het apparaat zorgt ervoor dat de meest recente updates zijn geïnstalleerd. Nadat de controle is voltooid, kunt u Apparaatservices weergeven selecteren om de status en versies te zien van de services die op de apparaatserver worden uitgevoerd. 
        > [!Note]
        > Als u ervoor hebt gekozen om de service voor automatische updates op het apparaat uit te schakelen, kunt u de apparaatservices handmatig bijwerken om de nieuwste versies van de services op te halen door de stappen hier [**te volgen.**](https://docs.microsoft.com/azure/migrate/migrate-appliance#manually-update-an-older-version)
   - **VDDK installeren:**( alleen nodig voor _VMware-apparaat)_ Het apparaat controleert of VMware vSphere Virtual Disk Development Kit (VDDK) is geïnstalleerd. Als dat niet het geval is, downloadt u VDDK 6.7 van VMware en extraheert u de inhoud van het gedownloade zipbestand naar de opgegeven locatie op het apparaat. Zie voor meer informatie de **installatie-instructies**.

#### <a name="register-the-appliance-and-start-continuous-discovery"></a>Het apparaat registreren en continue detectie starten

Nadat de controle van vereisten is voltooid, volgt u deze stappen om het apparaat te registreren en continue detectie te starten voor respectieve scenario's: [VMware-VM's,](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware#register-the-appliance-with-azure-migrate) [Hyper-V-VM's,](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v#register-the-appliance-with-azure-migrate)fysieke [servers,](https://docs.microsoft.com/azure/migrate/tutorial-discover-physical#register-the-appliance-with-azure-migrate) [AWS-VM's,](https://docs.microsoft.com/azure/migrate/tutorial-discover-aws#register-the-appliance-with-azure-migrate) [GCP-VM's.](https://docs.microsoft.com/azure/migrate/tutorial-discover-gcp#register-the-appliance-with-azure-migrate)


>[!Note]
> Als u problemen met DNS-resolutie krijgt tijdens de registratie van het apparaat of  op het moment van detectie, moet u ervoor zorgen dat Azure Migrate-resources die zijn gemaakt tijdens de stap Sleutel genereren in de portal bereikbaar zijn vanaf de on-premises server die als host voor het Azure Migrate-apparaat wordt gebruikt. [Meer informatie over het controleren van de netwerkverbinding.](#troubleshoot-network-connectivity)

### <a name="assess-your-servers-for-migration-to-azure"></a>Uw servers evalueren voor migratie naar Azure
Nadat de detectie is voltooid, evalueert u uw servers[(VMware-VM's,](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware-azure-vm) [Hyper-V-VM's,](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v)fysieke [servers,](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware-azure-vm) [AWS-VM's,](https://docs.microsoft.com/azure/migrate/tutorial-assess-aws) [GCP-VM's)](https://docs.microsoft.com/azure/migrate/tutorial-assess-gcp)voor migratie naar Azure-VM's of Azure VMware Solution (AVS), met behulp van het hulpprogramma Azure Migrate: Detectie en evaluatie. 

U kunt uw [on-premises machines](https://docs.microsoft.com/azure/migrate/tutorial-discover-import#prepare-the-csv) ook evalueren met het hulpprogramma Azure Migrate: Detectie en evaluatie met behulp van een geïmporteerd CSV-bestand (door komma's gescheiden waarden).   

## <a name="migrate-servers-to-azure-using-azure-private-link"></a>Servers migreren naar Azure met behulp van Azure Private Link

In de volgende secties worden de stappen beschreven die nodig zijn om Azure Migrate privé-eindpunten te gebruiken voor [migraties](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) met behulp van persoonlijke ExpressRoute-peering of VPN-verbindingen.  

Dit artikel bevat een proof-of-concept-implementatiepad voor replicaties op basis van een agent voor het migreren van uw [VMware-VM's,](./tutorial-migrate-vmware-agent.md) [Hyper-V-VM's,](./tutorial-migrate-physical-virtual-machines.md)fysieke [servers,](./tutorial-migrate-physical-virtual-machines.md)virtuele machines die worden uitgevoerd op [AWS,](./tutorial-migrate-aws-virtual-machines.md)VM's die worden uitgevoerd op [GCP](https://docs.microsoft.com/azure/migrate/tutorial-migrate-gcp-virtual-machines)of VM's die worden uitgevoerd op een andere virtualisatieprovider met behulp van Privé-eindpunten van Azure. U kunt een vergelijkbare benadering gebruiken voor het uitvoeren van [Hyper-V-migraties](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v) zonder agent met behulp van private link.

>[!Note]
>[Voor VMware-migraties zonder agent](https://docs.microsoft.com/azure/migrate/tutorial-assess-physical) is internettoegang of connectiviteit via ExperessRoute Microsoft-peering vereist. 

### <a name="set-up-a-replication-appliance-for-migration"></a>Een replicatieapparaat instellen voor migratie 

Het volgende diagram illustreert de op agent gebaseerde replicatiewerkstroom met privé-eindpunten met behulp van Azure Migrate: Hulpprogramma voor servermigratie.  

![Replicatiearchitectuur](./media/how-to-use-azure-migrate-with-private-endpoints/replication-architecture.png)

Het hulpprogramma gebruikt een replicatieapparaat om uw servers naar Azure te repliceren. Gebruik dit artikel om een machine voor te bereiden [en in te stellen voor het replicatieapparaat. ](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#prepare-a-machine-for-the-replication-appliance)

Nadat u het replicatieapparaat hebt ingesteld, gebruikt u de volgende instructies om de vereiste resources voor migratie te maken. 

1. In **Machines ontdekken** Zijn uw machines  >  **gevirtualiseerd?**, **selecteert u Niet gevirtualiseerd/Over ander.**
2. Selecteer **en bevestig in** Doelregio de Azure-regio waar u de machines wilt migreren.
3. Selecteer **Resources maken om** de vereiste Azure-resources te maken. Sluit de pagina niet tijdens het maken van resources.   
    - Hiermee maakt u een Recovery Services-kluis op de achtergrond en schakelt u een beheerde identiteit voor de kluis in. Een Recovery Services-kluis is een entiteit die de replicatiegegevens van servers bevat en wordt gebruikt om replicatiebewerkingen te activeren.  
    - Als het Azure Migrate privé-eindpuntconnectiviteit heeft, wordt er een privé-eindpunt gemaakt voor de Recovery Services-kluis. Hiermee worden vijf volledig gekwalificeerde privénamen (FQDN's) toegevoegd aan het privé-eindpunt, één voor elke microservice die is gekoppeld aan de Recovery Services-kluis.   
    - De vijf domeinnamen zijn opgemaakt in dit patroon: <br/> _{Vault-ID}-asr-pod01-{type}-. {target-geo-code}_. privatelink.siterecovery.windowsazure.com  
    - Standaard maakt Azure Migrate automatisch een privé-DNS-zone en voegt dns-A-records toe voor de Microservices van de Recovery Services-kluis. De privé-DNS-zone wordt vervolgens gekoppeld aan het virtuele netwerk van het privé-eindpunt. Hierdoor kan het on-premises replicatieapparaat de volledig gekwalificeerde domeinnamen naar hun privé-IP-adressen oplossen.

4. Voordat u het replicatieapparaat registreert, moet u ervoor zorgen dat de FQDN's van de private link van de kluis bereikbaar zijn vanaf de computer die als host voor het replicatieapparaat wordt gebruikt. [Meer informatie over het controleren van de netwerkverbinding.](#troubleshoot-network-connectivity) 

5. Nadat u de connectiviteit hebt gecontroleerd, downloadt u het installatie- en sleutelbestand van het apparaat, het installatieproces en registreert u het apparaat bij Azure Migrate. Bekijk hier [de gedetailleerde stappen.](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#set-up-the-replication-appliance) Nadat u het replicatieapparaat hebt ingesteld, volgt u deze instructies om de [Mobility-service](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#install-the-mobility-service) te installeren op de machines die u wilt migreren. 

### <a name="replicate-servers-to-azure-using-azure-private-link"></a>Servers repliceren naar Azure met behulp van Azure Private Link 

Volg nu deze [stappen om](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#replicate-machines) servers te selecteren voor replicatie.  

Gebruik **in**  >  **Doelinstellingen repliceren** Opslagaccount cache/replicatie in de vervolgkeuzemenu om een opslagaccount te selecteren dat via een  >  privékoppeling moet worden gerepliceerd.  

Als uw Azure Migrate-project verbinding heeft met een privé-eindpunt, moet u machtigingen verlenen aan de beheerde identiteit van de  [Recovery Services-kluis](#grant-access-permissions-to-the-recovery-services-vault) voor toegang tot het opslagaccount dat is vereist Azure Migrate.   

Als u replicaties via een privékoppeling wilt inschakelen, maakt [u bovendien een privé-eindpunt voor het opslagaccount.](#create-a-private-endpoint-for-the-storage-account-optional)

#### <a name="grant-access-permissions-to-the-recovery-services-vault"></a>Toegangsmachtigingen verlenen aan de Recovery Services-kluis

De beheerde identiteit van de Recovery Services-kluis vereist machtigingen voor geverifieerde toegang tot het opslagaccount voor cache/replicatie. 

Gebruik de onderstaande richtlijnen om de Recovery Services-kluis te identificeren die is gemaakt door Azure Migrate en de vereiste machtigingen te verlenen. 

**_De Recovery Services-kluis en de object-id van de beheerde identiteit identificeren_**

U vindt de details van de Recovery Services-kluis op de eigenschappenpagina Azure Migrate: Server **Migration.** 

1. Ga naar de **hub Azure Migrate,** selecteer **Overzicht** op de Azure Migrate: Servermigratie.

    ![Overzichtspagina op Azure Migrate hub](./media/how-to-use-azure-migrate-with-private-endpoints/hub-overview.png)

2. Selecteer Eigenschappen in het **linkerdeelvenster.** Noteer de naam van de Recovery Services-kluis en de id van de beheerde identiteit. De kluis heeft een _privé-eindpunt_ als het **connectiviteitstype** en _Overige_ als **replicatietype.** U hebt deze informatie nodig tijdens het verlenen van toegang tot de kluis.
      
    ![Azure Migrate: eigenschappenpagina servermigratie](./media/how-to-use-azure-migrate-with-private-endpoints/vault-info.png)

**_De vereiste machtigingen verlenen voor toegang tot het opslagaccount_**

 Aan de beheerde identiteit van de kluis moeten de volgende rolmachtigingen worden verleend voor het opslagaccount dat is vereist voor replicatie.  In dit geval moet u het opslagaccount van tevoren maken.

>[!Note]
> Voor het migreren van Hyper-V-VM's naar Azure met behulp van private link moet u toegang verlenen tot zowel het replicatieopslagaccount als het cacheopslagaccount. 

De rolmachtigingen variëren afhankelijk van het type opslagaccount.

- Resource Manager op basis van opslagaccounts (standaardtype):
  - [Inzender](../role-based-access-control/built-in-roles.md#contributor) _en_
  - [Inzender voor Storage Blob-gegevens](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Resource Manager op basis van opslagaccounts (Premium-type):
  - [Inzender](../role-based-access-control/built-in-roles.md#contributor) _en_
  - [Eigenaar van opslagblobgegevens](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

1. Ga naar het replicatie-/cacheopslagaccount dat is geselecteerd voor replicatie. Selecteer **Toegangsbeheer (IAM)** in het linkerdeelvenster. 

1. Selecteer in **de sectie Een roltoewijzing** toevoegen de **optie Toevoegen:**

   ![Een roltoewijzing toevoegen](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment.png)


1. Selecteer op **de pagina Roltoewijzing** toevoegen in het veld **Rol** de juiste rol in de hierboven genoemde lijst met machtigingen. Voer de naam in van de kluis die u eerder hebt genoteerd en selecteer **Opslaan.**

    ![Op rollen gebaseerde toegang bieden](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment-select-role.png)

4. Naast deze machtigingen moet u ook toegang tot vertrouwde Microsoft-services toestaan. Als uw netwerktoegang is beperkt tot geselecteerde netwerken, selecteert u **Microsoft-services** toegang tot dit opslagaccount **toestaan** in de sectie Uitzonderingen op het **tabblad** Netwerken. 

![Vertrouwde toegang Microsoft-services opslagaccount toestaan](./media/how-to-use-azure-migrate-with-private-endpoints/exceptions.png)


### <a name="create-a-private-endpoint-for-the-storage-account-optional"></a>Een privé-eindpunt maken voor het opslagaccount (optioneel)

Als u ExpressRoute wilt repliceren met privé-peering, [](https://docs.microsoft.com/azure/private-link/tutorial-private-endpoint-storage-portal#create-storage-account-with-a-private-endpoint) maakt u een privé-eindpunt voor de opslagaccounts voor cache/replicatie (doelsubresource: **_blob)._** 

>[!Note]
>
> - U kunt alleen privé-eindpunten maken in een Algemeen v2-opslagaccount (GPv2). Zie Prijzen van [Azure Page Blobs](https://azure.microsoft.com/pricing/details/storage/page-blobs/) en Prijzen van Azure Private Link voor [prijsinformatie](https://azure.microsoft.com/pricing/details/private-link/)

Het privé-eindpunt voor het opslagaccount moet worden gemaakt in hetzelfde virtuele netwerk als het privé Azure Migrate-eindpunt van het Azure Migrate-project of een ander virtueel netwerk dat is verbonden met dit netwerk. 

Selecteer **Ja** en integreer met een privé-DNS-zone. De privé-DNS-zone helpt bij het routeren van de verbindingen van het virtuele netwerk naar het opslagaccount via een privékoppeling. Als **u Ja** selecteert, wordt de DNS-zone automatisch aan het virtuele netwerk toegevoegd en worden de DNS-records toegevoegd voor de resolutie van nieuwe IP's en volledig gekwalificeerde domeinnamen die zijn gemaakt. Meer informatie over [privé-DNS-zones.](https://docs.microsoft.com/azure/dns/private-dns-overview)

Als de gebruiker die het privé-eindpunt maakt ook de eigenaar van het opslagaccount is, wordt het privé-eindpunt automatisch goedgekeurd. Anders moet de eigenaar van het opslagaccount het privé-eindpunt goedkeuren voor gebruik. Als u een aangevraagde privé-eindpuntverbinding wilt goedkeuren of afwijzen, gaat u naar **Privé-eindpuntverbindingen** onder **Netwerken** op de pagina van het opslagaccount.

Controleer de status van de verbindingsstatus van het privé-eindpunt voordat u doorgaat. 

![Goedkeuringsstatus van privé-eindpunt](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection-state.png)

Nadat u het privé-eindpunt hebt gemaakt, gebruikt u de vervolgkeuzemenu **in** Doelinstellingen repliceren Cacheopslagaccount om het opslagaccount te selecteren voor replicatie  >    >   via een privékoppeling.  

Zorg ervoor dat het on-premises replicatieapparaat netwerkverbinding heeft met het opslagaccount op het privé-eindpunt. [Meer informatie over het controleren van de netwerkverbinding.](#troubleshoot-network-connectivity)

>[!Note]
>
> - Als het opslagaccount voor replicatie van het _Premium-type_ is, moet u voor migraties van Hyper-V-VM's naar Azure een ander opslagaccount van het type _Standard_ selecteren voor het cacheopslagaccount. In dit geval moet u privé-eindpunten maken voor zowel het replicatie- als het cacheopslagaccount.  

Volg vervolgens deze instructies om de [replicatie te controleren en te starten](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#replicate-machines) en [migraties uit te voeren.](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#run-a-test-migration)  

## <a name="troubleshoot-network-connectivity"></a>Problemen met de netwerkverbinding oplossen 

### <a name="validate-private-endpoints-configuration"></a>Configuratie van privé-eindpunten valideren 

Zorg ervoor dat het privé-eindpunt een goedgekeurde status heeft.  

1. Ga naar Azure Migrate: eigenschappenpagina Detectie en evaluatie en servermigratie.
2. De eigenschappenpagina bevat de lijst met privé-eindpunten en private link-FQDN's die automatisch zijn gemaakt door Azure Migrate.  

3. Selecteer het privé-eindpunt dat u wilt diagnosticeren.  
    1. Controleer of de verbindingstoestand Goedgekeurd is.
    2. Als de verbinding de status In behandeling heeft, moet u deze laten goedkeuren.
    3. U kunt ook naar de privé-eindpuntresource navigeren en controleren of het virtuele netwerk overeenkomt met het virtuele netwerk Privé-eindpunt van project migreren. 

    ![Verbinding met privé-eindpunt weergeven](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection.png)

### <a name="verify-dns-resolution"></a>DNS-resolutie controleren 

Het on-premises apparaat (of de replicatieprovider) heeft toegang tot de Azure Migrate resources met behulp van hun FQDN's (Fully Qualified Private Link Domain Names). Mogelijk hebt u extra DNS-instellingen nodig om het privé-IP-adres van de privé-eindpunten vanuit de bronomgeving om te leiden. [Gebruik dit artikel om inzicht](https://docs.microsoft.com/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder) te krijgen in de DNS-configuratiescenario's die kunnen helpen bij het oplossen van eventuele problemen met de netwerkverbinding.  

Als u de private link-verbinding wilt valideren, voert u een DNS-resolutie uit van de Azure Migrate-resource-eindpunten (private link-resource-FQDN's) vanaf de on-premises server die als host voor het Migrate-apparaat wordt gehost en zorgt u ervoor dat deze wordt opgelost naar een privé-IP-adres. De details van het privé-eindpunt en de informatie over de privékoppelingsresource-FQDN's zijn beschikbaar op de eigenschappenpagina's Detectie en Evaluatie en Servermigratie. Selecteer **DNS-instellingen downloaden om** de lijst te bekijken.   

 ![Azure Migrate: Eigenschappen van detectie en evaluatie](./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png)

 ![Azure Migrate: Eigenschappen van servermigratie](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties.png)

Een illustratief voorbeeld voor DNS-resolutie van de FQDN van de private link van het opslagaccount.  

- Voer _nslookup<storage-account-name in>_.blob.core.windows.net.  Vervang <naam van het opslagaccount> door de naam van het opslagaccount dat wordt gebruikt voor Azure Migrate.  

    U ontvangt een bericht dat er als het volgende uit ziet:  

   ![Voorbeeld van DNS-resolutie](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- Een privé-IP-adres van 10.1.0.5 wordt geretourneerd voor het opslagaccount. Dit adres behoort tot het subnet van het virtuele netwerk van het privé-eindpunt.   

U kunt de DNS-resolutie voor andere Azure Migrate artefacten met behulp van een vergelijkbare benadering.   

Als de DNS-resolutie onjuist is, volgt u deze stappen:  

- Als u een aangepaste DNS gebruikt, controleert u uw aangepaste DNS-instellingen en controleert u of de DNS-configuratie juist is. Zie overzicht van [privé-eindpunten: DNS-configuratie voor hulp.](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration)
- Als u door Azure geleverde DNS-servers gebruikt, raadpleegt u de onderstaande sectie voor verdere probleemoplossing.  

> [!Tip]
> U kunt de DNS-records van uw bronomgeving handmatig bijwerken door het DNS-hostsbestand op uw on-premises apparaat te bewerken met de private link-resource-FQDN's en de bijbehorende privé-IP-adressen. Deze optie wordt alleen aanbevolen voor testen. <br/>  


### <a name="validate-the-private-dns-zone"></a>De zone Privé-DNS valideren   
Als de DNS-resolutie niet werkt zoals beschreven in de vorige sectie, is er mogelijk een probleem met uw Privé-DNS zone.  

#### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Controleer of de vereiste Privé-DNS Zone-resource bestaat  
Standaard maakt Azure Migrate voor elk resourcetype ook een privé-DNS-zone die overeenkomt met het subdomein 'privatelink'. De privé-DNS-zone wordt gemaakt in dezelfde Azure-resourcegroep als de resourcegroep van het privé-eindpunt. De Azure-resourcegroep moet privé-DNS-zoneresources bevatten met de volgende indeling:
- privatelink.vaultcore.azure.net voor de sleutelkluis 
- privatelink.blob.core.windows.net voor het opslagaccount
- privatelink.siterecovery.windowsazure.com voor de Recovery Services-kluis (voor Hyper-V en replicaties op basis van een agent)
- privatelink.prod.migration.windowsazure.com: migreert project, evaluatieproject en detectiesite.   

De privé-DNS-zone wordt automatisch gemaakt door Azure Migrate (met uitzondering van het opslagaccount voor cache/replicatie dat door de gebruiker is geselecteerd). U kunt de gekoppelde privé-DNS-zone vinden door naar de pagina privé-eindpunt te gaan en DNS-configuraties te selecteren. U ziet de privé-DNS-zone onder de sectie privé-DNS-integratie. 

![Schermopname van DNS-configuratie](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration.png)  

Als de DNS-zone niet aanwezig is (zoals hieronder wordt weergegeven), maakt u [een nieuwe Privé-DNS Zone-resource.](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal)  

![Een Privé-DNS maken](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone.png) 

#### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Controleer of de Privé-DNS zone is gekoppeld aan het virtuele netwerk  
De privé-DNS-zone moet worden gekoppeld aan het virtuele netwerk dat het privé-eindpunt voor de DNS-query bevat om het privé-IP-adres van het resource-eindpunt om te zetten. Als de privé-DNS-zone niet is gekoppeld aan de juiste Virtual Network, negeert een DNS-resolutie van dat virtuele netwerk de privé-DNS-zone.   

Navigeer naar de privé-DNS-zoneresource in Azure Portal en selecteer de koppelingen naar het virtuele netwerk in het menu links. Als het goed is, ziet u dat de virtuele netwerken zijn gekoppeld.

![Koppelingen voor virtuele netwerken weergeven](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links.png) 

Hier wordt een lijst met koppelingen weergegeven, elk met de naam van een virtueel netwerk in uw abonnement. Het virtuele netwerk met de privé-eindpuntresource moet hier worden vermeld. Volg anders [dit artikel om](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) de privé-DNS-zone te koppelen aan een virtueel netwerk.    

Zodra de privé-DNS-zone is gekoppeld aan het virtuele netwerk, zoeken DNS-aanvragen die afkomstig zijn van het virtuele netwerk naar DNS-records in de privé-DNS-zone. Dit is vereist voor de juiste adresoplossing voor het virtuele netwerk waar het privé-eindpunt is gemaakt.   

#### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-records"></a>Controleer of de privé-DNS-zone de juiste A-records bevat 

Ga naar de privé-DNS-zone die u wilt oplossen. Op de pagina Overzicht worden alle DNS-records voor die privé-DNS-zone weergegeven. Controleer of er een DNS A-record bestaat voor de resource. De waarde van de A-record (het IP-adres) moet het privé-IP-adres van de resources zijn. Als u de A-record met het verkeerde IP-adres vindt, moet u het verkeerde IP-adres verwijderen en een nieuwe toevoegen. Het is raadzaam om de hele A-record te verwijderen, een nieuwe record toe te voegen en een DNS-flush uit te laten op het on-premises bronapparaat.   

Een illustratief voorbeeld voor de DNS A-record van het opslagaccount in de privé-DNS-zone:

![DNS-records](./media/how-to-use-azure-migrate-with-private-endpoints/dns-a-records.png)   

Een illustratief voorbeeld voor de Microservices-DNS A-records van de Recovery Services-kluis in de privé-DNS-zone: 

![DNS-records voor Recovery Services-kluis](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records.png)   

>[!Note]
> Wanneer u een A-record verwijdert of wijzigt, kan de computer nog steeds worden omgeboekt naar het oude IP-adres, omdat de TTL-waarde (Time To Live) mogelijk nog niet is verlopen.  

#### <a name="other-things-that-may-affect-private-link-connectivity"></a>Andere zaken die van invloed kunnen zijn op private link-connectiviteit  

Dit is een niet-uitputtende lijst met items die u kunt vinden in geavanceerde of complexe scenario's: 

- Firewallinstellingen: de Azure Firewall verbonden met het virtuele netwerk of een aangepaste firewalloplossing die wordt geïmplementeerd op de apparaatmachine.  
- Netwerk-peering, wat van invloed kan zijn op welke DNS-servers worden gebruikt en hoe verkeer wordt gerouteerd.  
- Aangepaste GATEWAY-oplossingen (NAT) kunnen van invloed zijn op de manier waarop verkeer wordt gerouteerd, inclusief verkeer van DNS-query's.

Lees de gids voor probleemoplossing voor verbindingsproblemen met [privé-eindpunten voor meer informatie.](https://docs.microsoft.com/azure/private-link/troubleshoot-private-endpoint-connectivity)  

## <a name="next-steps"></a>Volgende stappen 
- [Voltooi het migratieproces en](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#complete-the-migration) bekijk de [best practices na de migratie.](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#post-migration-best-practices)