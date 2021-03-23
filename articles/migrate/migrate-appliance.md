---
title: Azure Migrate-apparaat
description: Biedt een samen vatting van de ondersteuning voor het Azure Migrate apparaat.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: dadca1fadef9d2967f20cae13e40d01de73d39e4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778335"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-apparaat

In dit artikel vindt u een overzicht van de vereisten en ondersteuning voor het Azure Migrate apparaat.

## <a name="deployment-scenarios"></a>Implementatiescenario's

Het Azure Migrate apparaat wordt gebruikt in de volgende scenario's.

**Scenario** | **Hulpprogramma** | **Gebruikt om**
--- | --- | ---
**Detectie en evaluatie van servers die worden uitgevoerd in de VMware-omgeving** | Azure Migrate: detectie en evaluatie | Servers detecteren die in uw VMware-omgeving worden uitgevoerd<br/><br/> Voer de detectie van de geïnstalleerde software-inventarisatie, afhankelijkheids analyse zonder agent en detectie van SQL Server instanties en data bases.<br/><br/> Verzamelen van meta gegevens voor de server configuratie en prestaties voor evaluaties.
**Migratie van servers die in VMware-omgeving worden uitgevoerd zonder agent** | Azure Migrate: Server migratie | Ontdek servers die worden uitgevoerd in uw VMware-omgeving. <br/><br/> Servers repliceren zonder agents te installeren.
**Detectie en evaluatie van servers die worden uitgevoerd in de Hyper-V-omgeving** | Azure Migrate: detectie en evaluatie | Detecteer servers die worden uitgevoerd in uw Hyper-V-omgeving.<br/><br/> Verzamelen van meta gegevens voor de server configuratie en prestaties voor evaluaties.
**Detectie en evaluatie van fysieke of gevirtualiseerde on-premises servers** |  Azure Migrate: detectie en evaluatie |  Fysieke of gevirtualiseerde on-premises servers detecteren.<br/><br/> Verzamelen van meta gegevens voor de server configuratie en prestaties voor evaluaties.

## <a name="deployment-methods"></a>Implementatie methoden

Het apparaat kan worden geïmplementeerd met een aantal methoden:

- Het apparaat kan worden geïmplementeerd met een sjabloon voor servers die worden uitgevoerd in de VMware-of Hyper-V-omgeving ([eicellen-sjabloon voor VMware](how-to-set-up-appliance-vmware.md) of [VHD voor hyper-v](how-to-set-up-appliance-hyper-v.md)).
- Als u geen sjabloon wilt gebruiken, kunt u het apparaat voor de VMware-of Hyper-V-omgeving implementeren met behulp van een [Power shell-installatie script](deploy-appliance-script.md).
- In Azure Government moet u het apparaat implementeren met behulp van een Power shell-installatie script. Raadpleeg [hier](deploy-appliance-script-government.md)de stappen voor de implementatie.
- Voor fysieke of gevirtualiseerde servers, on-premises of een andere Cloud, implementeert u het apparaat altijd met behulp van een Power shell-installatie script. Raadpleeg [hier](how-to-set-up-appliance-physical.md)de stappen voor de implementatie.
- Download koppelingen zijn beschikbaar in de onderstaande tabellen.

## <a name="appliance---vmware"></a>Apparaat-VMware

De volgende tabel bevat een overzicht van de Azure Migrate vereisten voor VMware.

> [!Note]
> Detectie en evaluatie van SQL Server instanties en data bases die worden uitgevoerd in uw VMware-omgeving is nu beschikbaar als preview-versie. Als u deze functie wilt proberen, gebruikt u [**deze koppeling**](https://aka.ms/AzureMigrate/SQL) om een project te maken in de regio **Australië - oost**. Als u al een project in Australië-oost hebt en u deze functie wilt proberen, zorgt u ervoor dat u aan deze [**vereisten**](how-to-discover-sql-existing-project.md) voldoet in de portal.

**Vereiste** | **VMware**
--- | ---
**Machtigingen** | Als u lokaal of op afstand toegang wilt krijgen tot de configuratie beheer van het apparaat, moet u een lokaal of domein gebruikers account met beheerders bevoegdheden op de toestel server hebben.
**Toestel Services** | Het apparaat heeft de volgende services:<br/><br/> - Apparaatconfiguratie voor **Apparaatbeheer**: dit is een webtoepassing die kan worden geconfigureerd met bron Details om de detectie en evaluatie van servers te starten.<br/> - **VMware-detectie agent**: de agent verzamelt meta gegevens van de server configuratie die kunnen worden gebruikt voor het maken van on-premises evaluaties.<br/>- **VMware-evaluatie agent**: de agent verzamelt meta gegevens van de server prestaties die kunnen worden gebruikt voor het maken van evaluaties op basis van prestaties.<br/>- **Service voor automatische updates**: de service houdt alle agents die op het apparaat worden uitgevoerd up-to-date. Het wordt automatisch elke 24 uur uitgevoerd.<br/>- **DRA-agent**: organiseert Server replicatie en coördineert de communicatie tussen gerepliceerde servers en Azure. Wordt alleen gebruikt wanneer servers naar Azure worden gerepliceerd met behulp van migratie zonder agent.<br/>- **Gateway**: verstuurt gerepliceerde gegevens naar Azure. Wordt alleen gebruikt wanneer servers naar Azure worden gerepliceerd met behulp van migratie zonder agent.<br/>- **SQL-detectie en-evaluatie agent**: Hiermee worden de configuratie-en prestatie gegevens van SQL Server instanties en data bases naar Azure verzonden.
**Project limieten** |  Een apparaat kan alleen worden geregistreerd met een enkel project.<br/> Eén project kan meerdere geregistreerde apparaten hebben.
**Detectie limieten** | Een apparaat kan tot 10.000 servers die worden uitgevoerd op een vCenter Server detecteren.<br/> Een apparaat kan verbinding maken met één vCenter Server.
**Ondersteunde implementatie** | Implementeren als nieuwe server die wordt uitgevoerd op vCenter Server met behulp van een eicellen-sjabloon.<br/><br/> Implementeren op een bestaande server met Windows Server 2016 met behulp van het Power shell-installatie script.
**EICELLEN-sjabloon** | Downloaden van project of [hier](https://go.microsoft.com/fwlink/?linkid=2140333)<br/><br/> De download grootte is 11,9 GB.<br/><br/> De sjabloon voor het gedownloade apparaat wordt geleverd met een Windows Server 2016-evaluatie licentie, die voor 180 dagen geldig is.<br/>Als de evaluatie periode bijna is verlopen, raden wij aan dat u een nieuw apparaat downloadt en implementeert met behulp van de eicellen-sjabloon, of u activeert de licentie van het besturings systeem van de toestel server.
**EICELLEN verifiëren** | [Controleer](tutorial-discover-vmware.md#verify-security) de eicellen-sjabloon die is gedownload van het project door de hash-waarden te controleren.
**PowerShell-script** | Raadpleeg dit [artikel](./deploy-appliance-script.md#set-up-the-appliance-for-vmware) over het implementeren van een apparaat met het Power shell-installatie script.<br/><br/> 
**Hardware-en netwerk vereisten** |  Het apparaat moet worden uitgevoerd op een server met Windows Server 2016, 32-GB RAM, 8 Vcpu's, ongeveer 80 GB aan schijf opslag en een externe virtuele switch.<br/> Voor het apparaat is toegang tot internet vereist, hetzij rechtstreeks hetzij via een proxy.<br/><br/> Als u het apparaat implementeert met behulp van de eicellen-sjabloon, hebt u voldoende resources op de vCenter Server nodig om een server te maken die voldoet aan de hardwarevereisten.<br/><br/> Als u het apparaat op een bestaande server uitvoert, moet u ervoor zorgen dat Windows Server 2016 wordt uitgevoerd en voldoet aan de hardwarevereisten.<br/>_(Momenteel wordt de implementatie van apparaten alleen ondersteund voor Windows Server 2016.)_
**VMware-vereisten** | Als u het apparaat als een server op vCenter Server implementeert, moet het worden geïmplementeerd op een vCenter Server met 5,5, 6,0, 6,5 of 6,7 en een ESXi-host waarop versie 5,5 of hoger wordt uitgevoerd.<br/><br/> 
**VDDK (migratie zonder agent)** | Als u gebruik wilt maken van het apparaat voor de migratie van servers zonder agent, moet de VMware vSphere VDDK worden geïnstalleerd op de toestel server.

## <a name="appliance---hyper-v"></a>Apparaat-Hyper-V

**Vereiste** | **Hyper-V**
--- | ---
**Machtigingen** | Als u lokaal of op afstand toegang wilt krijgen tot de configuratie beheer van het apparaat, moet u een lokaal of domein gebruikers account met beheerders bevoegdheden op de toestel server hebben.
**Toestel Services** | Het apparaat heeft de volgende services:<br/><br/> - Apparaatconfiguratie voor **Apparaatbeheer**: dit is een webtoepassing die kan worden geconfigureerd met bron Details om de detectie en evaluatie van servers te starten.<br/> - **Detectie agent**: de agent verzamelt meta gegevens van de server configuratie die kunnen worden gebruikt voor het maken van on-premises evaluaties.<br/>- **Beoordelings agent**: de agent verzamelt meta gegevens van de server prestaties die kunnen worden gebruikt voor het maken van evaluaties op basis van prestaties.<br/>- **Service voor automatische updates**: de service houdt alle agents die op het apparaat worden uitgevoerd up-to-date. Het wordt automatisch elke 24 uur uitgevoerd.
**Project limieten** |  Een apparaat kan alleen worden geregistreerd met een enkel project.<br/> Eén project kan meerdere geregistreerde apparaten hebben.
**Detectie limieten** | Een apparaat kan Maxi maal 5000 servers met Hyper-V-omgeving detecteren.<br/> Een apparaat kan verbinding maken met Maxi maal 300 Hyper-V-hosts.
**Ondersteunde implementatie** | Implementeer als server die wordt uitgevoerd op een Hyper-V-host met behulp van een VHD-sjabloon.<br/><br/> Implementeren op een bestaande server met Windows Server 2016 met behulp van het Power shell-installatie script.
**VHD-sjabloon** | Zip-bestand dat een VHD bevat. Down load van project of [hier](https://go.microsoft.com/fwlink/?linkid=2140422).<br/><br/> De download grootte is 8,91 GB.<br/><br/> De sjabloon voor het gedownloade apparaat wordt geleverd met een Windows Server 2016-evaluatie licentie, die voor 180 dagen geldig is.<br/> Als de evaluatie periode bijna is verlopen, raden wij aan dat u een nieuw apparaat downloadt en implementeert, of dat u de licentie voor het besturings systeem van de toestel server activeert.
**VHD-verificatie** | [Controleer](tutorial-discover-hyper-v.md#verify-security) de VHD-sjabloon die is gedownload van het project door de hash-waarden te controleren.
**PowerShell-script** | Raadpleeg dit [artikel](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v) over het implementeren van een apparaat met het Power shell-installatie script.<br/>
**Hardware-en netwerk vereisten**  |  Het apparaat moet worden uitgevoerd op een server met Windows Server 2016, 16 GB RAM, 8 Vcpu's, ongeveer 80 GB aan schijf opslag en een externe virtuele switch.<br/> Het apparaat heeft een statisch of dynamisch IP-adres nodig en vereist Internet toegang, hetzij rechtstreeks hetzij via een proxy.<br/><br/> Als u het apparaat uitvoert als een server die op een Hyper-V-host wordt uitgevoerd, hebt u voldoende resources op de host nodig om een server te maken die voldoet aan de hardwarevereisten.<br/><br/> Als u het apparaat op een bestaande server uitvoert, moet u ervoor zorgen dat Windows Server 2016 wordt uitgevoerd en voldoet aan de hardwarevereisten.<br/>_(Momenteel wordt de implementatie van apparaten alleen ondersteund voor Windows Server 2016.)_
**Vereisten voor Hyper-V** | Als u het apparaat met de VHD-sjabloon implementeert, is het apparaat dat wordt meegeleverd door Azure Migrate Hyper-V VM versie 5,0.<br/><br/> Op de Hyper-V-host moet Windows Server 2012 R2 of later worden uitgevoerd.

## <a name="appliance---physical"></a>Apparaat-fysiek

**Vereiste** | **Fysiek**
--- | ---
**Machtigingen** | Als u lokaal of op afstand toegang wilt krijgen tot de configuratie beheer van het apparaat, moet u een lokaal of domein gebruikers account met beheerders bevoegdheden op de toestel server hebben.
**Toestel Services** | Het apparaat heeft de volgende services:<br/><br/> - Apparaatconfiguratie voor **Apparaatbeheer**: dit is een webtoepassing die kan worden geconfigureerd met bron Details om de detectie en evaluatie van servers te starten.<br/> - **Detectie agent**: de agent verzamelt meta gegevens van de server configuratie die kunnen worden gebruikt voor het maken van on-premises evaluaties.<br/>- **Beoordelings agent**: de agent verzamelt meta gegevens van de server prestaties die kunnen worden gebruikt voor het maken van evaluaties op basis van prestaties.<br/>- **Service voor automatische updates**: de service houdt alle agents die op het apparaat worden uitgevoerd up-to-date. Het wordt automatisch elke 24 uur uitgevoerd.
**Project limieten** |  Een apparaat kan alleen worden geregistreerd met een enkel project.<br/> Eén project kan meerdere geregistreerde apparaten hebben.<br/>
**Detectie limieten** | Een apparaat kan Maxi maal 1000 fysieke servers detecteren.
**Ondersteunde implementatie** | Implementeren op een bestaande server met Windows Server 2016 met behulp van het Power shell-installatie script.
**PowerShell-script** | Down load het script (AzureMigrateInstaller.ps1) in een zip-bestand uit het project of van [hier](https://go.microsoft.com/fwlink/?linkid=2140334). [Meer informatie](tutorial-discover-physical.md).<br/><br/> De download grootte is 85,8 MB.
**Script verificatie** | [Controleer](tutorial-discover-physical.md#verify-security) het Power shell-installatie script dat is gedownload van het project door de hash-waarden te controleren.
**Hardware-en netwerk vereisten** |  Het apparaat moet worden uitgevoerd op de server met Windows Server 2016, 16 GB RAM, 8 Vcpu's, ongeveer 80 GB aan schijf opslag.<br/> Het apparaat heeft een statisch of dynamisch IP-adres nodig en vereist Internet toegang, hetzij rechtstreeks hetzij via een proxy.<br/><br/> Als u het apparaat op een bestaande server uitvoert, moet u ervoor zorgen dat Windows Server 2016 wordt uitgevoerd en voldoet aan de hardwarevereisten.<br/>_(Momenteel wordt de implementatie van apparaten alleen ondersteund voor Windows Server 2016.)_

## <a name="url-access"></a>URL-toegang

Het Azure Migrate-apparaat heeft verbinding met internet nodig.

- Wanneer u het apparaat implementeert, controleert Azure Migrate een connectiviteits controle op de vereiste Url's.
- U moet toegang tot alle Url's in de lijst toestaan. Als u alleen analyses uitvoert, kunt u de Url's overs laan die zijn gemarkeerd als vereist voor VMware-agentloze migratie.
- Als u een op URL gebaseerde proxy gebruikt om verbinding te maken met internet, moet u ervoor zorgen dat de proxy alle CNAME-records verhelpt die zijn ontvangen tijdens het opzoeken van de Url's.

### <a name="public-cloud-urls"></a>Url's voor open bare Clouds

**URL** | **Details**  
--- | --- |
*.portal.azure.com  | Navigeer naar de Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *. live.com <br/> *. office.com | Meld u aan bij uw Azure-abonnement.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Maak Azure Active Directory (AD)-apps voor het apparaat om te communiceren met Azure Migrate.
management.azure.com | Maak Azure AD-apps voor het apparaat om te communiceren met de Azure Migrate.
*.services.visualstudio.com | Laad apparaat logboeken die worden gebruikt voor interne bewaking.
*.vault.azure.net | Geheimen beheren in de Azure Key Vault.<br/> Opmerking: Zorg ervoor dat servers voor replicatie toegang hebben.
aka.ms/* | Toegang tot ook wel-koppelingen toestaan; gebruikt voor het downloaden en installeren van de meest recente updates voor de apparaat-services.
download.microsoft.com/download | Down loads van het micro soft Download centrum toestaan.
*.servicebus.windows.net | Communicatie tussen het apparaat en de Azure Migrate service.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Verbinding maken met Azure Migrate service-Url's.
*.hypervrecoverymanager.windowsazure.com | **Gebruikt voor VMware-migratie zonder agent**<br/><br/> Verbinding maken met Azure Migrate service-Url's.
*.blob.core.windows.net |  **Gebruikt voor VMware-migratie zonder agent**<br/><br/>Gegevens uploaden naar de opslag voor migratie.

### <a name="government-cloud-urls"></a>Cloud-Url's voor de overheid

**URL** | **Details**  
--- | --- |
*. portal.azure.us  | Navigeer naar de Azure Portal.
graph.windows.net | Meld u aan bij uw Azure-abonnement.
login.microsoftonline.us  | Maak Azure Active Directory (AD)-apps voor het apparaat om te communiceren met Azure Migrate.
management.usgovcloudapi.net | Maak Azure AD-apps voor het apparaat om te communiceren met de Azure Migrate-service.
*.services.visualstudio.com | Laad apparaat logboeken die worden gebruikt voor interne bewaking.
*. vault.usgovcloudapi.net | Geheimen beheren in de Azure Key Vault.
aka.ms/* | Toegang tot ook wel-koppelingen toestaan; gebruikt voor het downloaden en installeren van de meest recente updates voor de apparaat-services.
download.microsoft.com/download | Down loads van het micro soft Download centrum toestaan.
*. servicebus.usgovcloudapi.net  | Communicatie tussen het apparaat en de Azure Migrate service.
*. discoverysrv.windowsazure.us <br/> *. migration.windowsazure.us | Verbinding maken met Azure Migrate service-Url's.
*. hypervrecoverymanager.windowsazure.us | **Gebruikt voor VMware-migratie zonder agent**<br/><br/> Verbinding maken met Azure Migrate service-Url's.
*. blob.core.usgovcloudapi.net  |  **Gebruikt voor VMware-migratie zonder agent**<br/><br/>Gegevens uploaden naar de opslag voor migratie.
*. applicationinsights.us | Laad apparaat logboeken die worden gebruikt voor interne bewaking.

## <a name="collected-data---vmware"></a>Verzamelde gegevens-VMware

Het apparaat verzamelt meta gegevens van de configuratie, meta gegevens over prestaties en server afhankelijkheden (als [afhankelijkheids analyse](concepts-dependency-visualization.md) zonder agent wordt gebruikt).

### <a name="metadata"></a>Metagegevens

Met meta gegevens die door het Azure Migrate-apparaat worden gedetecteerd, kunt u nagaan of servers gereed zijn voor migratie naar Azure, servers op de juiste grootte, plan kosten en toepassings afhankelijkheden analyseren. Micro soft gebruikt deze gegevens niet in een controle op de naleving van licenties.

Hier volgt de volledige lijst met meta gegevens van de server die door het apparaat worden verzameld en naar Azure worden verzonden.

**GEGEVENS** | **ITEM**
--- | ---
**Servergegevens** |
Server-ID | vm.Config. InstanceUuid
Servernaam | vm.Config. Naam
vCenter Server-ID | VMwareClient. instance. uuid
Server beschrijving | vm.Summary.Config. Aantekening
Licentie product naam | VM. client. ServiceContent. about. LicenseProductName
Besturingssysteemtype | VM. SummaryConfig. GuestFullName
Opstarttype | vm.Config. Firmware
Aantal kerngeheugens | vm.Config. Hardware. NumCPU
Geheugen (MB) | vm.Config. Hardware. MemoryMB
Aantal schijven | vm.Config. Hardware. device. ToList (). FindAll (x => is VirtualDisk). Count
Lijst met schijf grootte | vm.Config. Hardware. device. ToList (). FindAll (x => is VirtualDisk)
Lijst met netwerk adapters | vm.Config. Hardware. device. ToList (). FindAll (x => is VirtualEthernet). Count
CPU-gebruik | CPU. usage. Average
Geheugen gebruik |mem. usage. Average
**Details per schijf** |
Waarde van schijf sleutel | schijf. Prestatie
Dikunit-nummer | schijf. UnitNumber
Sleutel waarde van schijf controller | schijf. ControllerKey. waarde
Gigabytes ingericht | virtualDisk. DeviceInfo. summary
Schijf naam | De waarde die is gegenereerd met de schijf. UnitNumber, schijf. Sleutel, schijf. ControllerKey. waarde
Lees bewerkingen per seconde | virtualDisk. numberReadAveraged. Average
Schrijf bewerkingen per seconde | virtualDisk. numberWriteAveraged. Average
Lees doorvoer (MB per seconde) | virtualDisk. Read. Average
Schrijf doorvoer (MB per seconde) | virtualDisk. write. Average
**Details van de NIC** |
Naam van netwerk adapter | adapter. Prestatie
MAC-adres | ((VirtualEthernetCard) NIC). MacAddress
IPv4-adressen | vm.Guest.Net
IPv6-adressen | vm.Guest.Net
Lees doorvoer (MB per seconde) | net. received. Average
Schrijf doorvoer (MB per seconde) | net. verzonden. gemiddeld
**Details van configuratiepad** |
Name | verpakking. GetType (). Naam
Type onderliggend object | verpakking. ChildType
Referentie Details | verpakking. MoRef
Details van bovenliggend item | Container. Parent
Details van map per server | ((Map) container). ChildEntity. type
Details van Data Center per server | (Container (Data Center)). VmFolder
Details van Data Center per host-map | (Container (Data Center)). HostFolder
Cluster Details per host | ((ClusterComputeResource) container). Hostsite
Details van host per server | ((HostSystem) container). VM

### <a name="performance-data"></a>Prestatiegegevens

Dit zijn de prestatie gegevens die een apparaat verzamelt voor een server die wordt uitgevoerd op VMware en die wordt verzonden naar Azure.

**Gegevens** | **Item** | **Beoordelings impact**
--- | --- | ---
CPU-gebruik | CPU. usage. Average | Aanbevolen server grootte/kosten
Geheugen gebruik | mem. usage. Average | Aanbevolen server grootte/kosten
Lees doorvoer schijf (MB per seconde) | virtualDisk. Read. Average | Berekening voor schijf grootte, opslag kosten, Server grootte
Schrijf doorvoer schijf (MB per seconde) | virtualDisk. write. Average | Berekening voor schijf grootte, opslag kosten, Server grootte
Lees bewerkingen op de schijf per seconde | virtualDisk. numberReadAveraged. Average | Berekening voor schijf grootte, opslag kosten, Server grootte
Schrijf bewerkingen per seconde van schijf | virtualDisk. numberWriteAveraged. Average  | Berekening voor schijf grootte, opslag kosten, Server grootte
Lees doorvoer van NIC (MB per seconde) | net. received. Average | Berekening voor Server grootte
Doorvoer capaciteit van NIC (MB per seconde) | net. verzonden. gemiddeld  |Berekening voor Server grootte

### <a name="installed-software-inventory"></a>Geïnstalleerde software-inventaris

Het apparaat verzamelt gegevens over geïnstalleerde software-inventaris op servers.

#### <a name="windows-server-software-inventory-data"></a>Software-inventarisatie gegevens van Windows Server

Hier vindt u de software-inventarisatie gegevens die het apparaat verzamelt van elke Windows-Server die in uw VMware-omgeving wordt gedetecteerd.

**Gegevens** | **Registerlocatie** | **Sleutel**
--- | --- | ---
Naam van de toepassing  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Versie  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion
Provider  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-server-features-data"></a>Gegevens van Windows Server-functies

Hier vindt u de functies die het apparaat verzamelt van elke Windows-Server die in uw VMware-omgeving wordt gedetecteerd.

**Gegevens**  | **Power shell-cmdlet** | **Eigenschap**
--- | --- | ---
Name  | Get-WindowsFeature  | Name
Onderdeel type | Get-WindowsFeature  | FeatureType
Bovenliggend  | Get-WindowsFeature  | Bovenliggend

#### <a name="sql-server-metadata"></a>Meta gegevens SQL Server

Hier vindt u de SQL Server gegevens die het apparaat verzamelt van elke Windows-Server die in uw VMware-omgeving wordt gedetecteerd.

**Gegevens**  | **Registerlocatie**  | **Sleutel**
--- | --- | ---
Name  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server\Instance Names\SQL  | installedInstance
Editie  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | Editie
Service Pack  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | SP
Versie  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | Versie

#### <a name="windows-server-operating-system-data"></a>Gegevens van het Windows Server-besturings systeem

Dit zijn de gegevens van het besturings systeem die het apparaat verzamelt van elke Windows-Server die in uw VMware-omgeving wordt gedetecteerd.

**Gegevens**  | **WMI-klasse**  | **WMI-klasse-eigenschap**
--- | --- | ---
Name  | Win32_operatingsystem  | Caption
Versie  | Win32_operatingsystem  | Versie
Architectuur  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-server-software-inventory-data"></a>Software-inventaris gegevens voor Linux-server

Dit zijn de software-inventarisatie gegevens die het apparaat verzamelt van elke Linux-server die in uw VMware-omgeving wordt gedetecteerd. Op basis van het besturings systeem van de server worden een of meer van de opdrachten uitgevoerd.

**Gegevens**  | **Opdrachten**
--- | ---
Name | rpm, met dpkg-query, uitlijnen
Versie | rpm, met dpkg-query, uitlijnen
Provider | rpm, met dpkg-query, uitlijnen

#### <a name="linux-server-operating-system-data"></a>Gegevens van besturings systeem van Linux-server

Dit zijn de gegevens van het besturings systeem die het apparaat verzamelt van elke Linux-server die in uw VMware-omgeving wordt gedetecteerd.

**Gegevens**  | **Opdrachten**
--- | ---
Name <br/> versie | Verzameld van een of meer van de volgende bestanden:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version
Architectuur | uname

### <a name="sql-server-instances-and-databases-data"></a>SQL Server instanties en data base-gegevens

Het apparaat verzamelt gegevens over SQL Server instanties en data bases.

> [!Note]
> Detectie en evaluatie van SQL Server instanties en data bases die worden uitgevoerd in uw VMware-omgeving is nu beschikbaar als preview-versie. Als u deze functie wilt proberen, gebruikt u [**deze koppeling**](https://aka.ms/AzureMigrate/SQL) om een project te maken in de regio **Australië - oost**. Als u al een project in Australië-oost hebt en u deze functie wilt proberen, zorgt u ervoor dat u aan deze [**vereisten**](how-to-discover-sql-existing-project.md) voldoet in de portal.

#### <a name="sql-database-metadata"></a>Meta gegevens SQL database

**Data base-meta gegevens** | **Eigenschappen van weer gaven/SQL Server**
--- | ---
De unieke id van de data base | sys.databases
Door de server gedefinieerde data base-ID | sys.databases
De naam van de data base | sys.databases
Compatibiliteits niveau van data base | sys.databases
Sorteer naam van data base | sys.databases
Status van de data base | sys.databases
Grootte van de data base (in MB) | sys.master_files
Stationsletter van locatie met gegevens bestanden | Server Property en Software\Microsoft\MSSQLServer\MSSQLServer
Lijst met database bestanden | sys. data bases, sys.master_files
Service Broker is ingeschakeld of niet | sys.databases
De data base is ingeschakeld voor change data capture | sys.databases

#### <a name="sql-server-metadata"></a>Meta gegevens SQL Server

**Meta gegevens van server** | **Weer gaven/SQL Server-eigenschappen**
--- | ---
Servernaam |Server Property
FQDN | Verbindings reeks die is afgeleid van de detectie van geïnstalleerde toepassingen
Installatie-ID | sys.dm_server_registry
Serverversie | Server Property
Server-editie | Server Property
Server host platform (Windows/Linux) | Server Property
Product niveau van de server (RTM SP CTP) | Server Property
Standaardpad voor back-ups | Server Property
Standaardpad van de gegevens bestanden | Server Property en Software\Microsoft\MSSQLServer\MSSQLServer
Het standaardpad van de logboek bestanden | Server Property en Software\Microsoft\MSSQLServer\MSSQLServer
Nee. van kernen op de server | sys.dm_os_schedulers, sys.dm_os_sys_info
Naam van server sortering | Server Property
Nee. van kernen op de server met de status zichtbaar ONLINE | sys.dm_os_schedulers
Unieke Server-ID | sys.dm_server_registry
HA ingeschakeld of niet | Server Property
De buffergroepuitbreiding is ingeschakeld of niet | sys.dm_os_buffer_pool_extension_configuration
Het failovercluster is geconfigureerd of niet | Server Property
Server met alleen Windows-verificatie modus | Server Property
Server installeert poly base | Server Property
Nee. van logische Cpu's op het systeem | sys.dm_server_registry, sys.dm_os_sys_info
Verhouding van het aantal logische of fysieke kernen dat door één fysiek processor pakket wordt weer gegeven | sys.dm_os_schedulers, sys.dm_os_sys_info
Aantal fysieke Cpu's op het systeem | sys.dm_os_schedulers, sys.dm_os_sys_info
Datum en tijd waarop de server voor het laatst is gestart | sys.dm_server_registry
Maxi maal gebruik van Server geheugen (in MB) | sys.dm_os_process_memory
Totaal aantal van gebruikers in alle data bases | sys. data bases, sys. aanmeldingen
Totale grootte van alle gebruikers databases | sys.databases
Grootte van de tijdelijke data base | sys.master_files, sys.configurations, sys.dm_os_sys_info
Nee. van aanmeldingen | sys. logins
Lijst met gekoppelde servers | sys. servers
Lijst met Agent taken | [msdb]. [dbo]. [sysjobs], [sys]. [syslogins], [msdb]. [dbo]. [syscategories]

### <a name="performance-metadata"></a>Meta gegevens voor prestaties

**Prestaties** | **Weer gaven/SQL Server-eigenschappen** | **Beoordelings impact**
--- | --- | ---
CPU-gebruik SQL Server| sys.dm_os_ring_buffers| Aanbevolen SKU-grootte (CPU-dimensie)
Aantal logische SQL-CPU'S| sys.dm_os_sys_info| Aanbevolen SKU-grootte (CPU-dimensie)
Fysiek in gebruik zijnde SQL-geheugen| sys.dm_os_process_memory| Vrij
Percentage van het gebruik van het SQL-geheugen| sys.dm_os_process_memory | Vrij
CPU-gebruik van data base| sys.dm_exec_query_stats, sys.dm_exec_plan_attributes| Aanbevolen SKU-grootte (CPU-dimensie)
Database geheugen in gebruik (buffer groep)| sys.dm_os_buffer_descriptors| Aanbevolen SKU-grootte (geheugen dimensie)
IO voor lezen/schrijven bestand| sys.dm_io_virtual_file_stats, sys.master_files| Aanbevolen SKU-grootte (IO-dimensie)
Bestands aantal lees-en schrijf bewerkingen| sys.dm_io_virtual_file_stats, sys.master_files| Aanbevolen SKU-grootte (doorvoer dimensie)
Lezen/schrijven bestand IO-cabine (MS)| sys.dm_io_virtual_file_stats, sys.master_files| Aanbevolen SKU-grootte (dimensie IO-latentie)
Bestandsgrootte| sys.master_files| Aanbevolen SKU-grootte (opslag dimensie)


### <a name="application-dependency-data"></a>Afhankelijkheids gegevens voor toepassingen

Bij een afhankelijkheids analyse zonder agent worden de verbindings-en proces gegevens verzameld.

#### <a name="windows-server-dependencies-data"></a>Windows Server-afhankelijkheids gegevens

Hier vindt u de verbindings gegevens die het apparaat verzamelt van elke Windows-Server, die is ingeschakeld voor analyse van agentloze afhankelijkheden.

**Gegevens** | **Opdrachten**
--- | ---
Lokale poort | netstat
Lokaal IP-adres | netstat
Externe poort | netstat
Extern IP-adres | netstat
TCP-verbindings status | netstat
Proces-id | netstat
Aantal actieve verbindingen | netstat

Hier vindt u de verbindings gegevens die het apparaat verzamelt van elke Windows-Server, die is ingeschakeld voor analyse van agentloze afhankelijkheden.

**Gegevens** | **WMI-klasse** | **WMI-klasse-eigenschap**
--- | --- | ---
Procesnaam | Win32_Process | ExecutablePath
Proces argumenten | Win32_Process | CommandLine
De naam van de toepassing | Win32_Process | De para meter VersionInfo. ProductName van de eigenschap ExecutablePath

#### <a name="linux-server-dependencies-data"></a>Afhankelijkheids gegevens voor Linux-server

Hier vindt u de verbindings gegevens die het apparaat verzamelt van elke Linux-server, die is ingeschakeld voor analyse van agentloze afhankelijkheden.

**Gegevens** | **Opdrachten**
--- | ---
Lokale poort | netstat
Lokaal IP-adres | netstat
Externe poort | netstat
Extern IP-adres | netstat
TCP-verbindings status | netstat
Aantal actieve verbindingen | netstat
Proces-id  | netstat
Procesnaam | ps
Proces argumenten | ps
De naam van de toepassing | met dpkg of rpm

## <a name="collected-data---hyper-v"></a>Verzamelde gegevens-Hyper-V

Het apparaat verzamelt meta gegevens over de configuratie en prestaties van servers die worden uitgevoerd in de Hyper-V-omgeving.

### <a name="metadata"></a>Metagegevens
Met de meta gegevens die door het Azure Migrate-apparaat zijn gedetecteerd, kunt u nagaan of servers gereed zijn voor migratie naar Azure, servers in de juiste grootte en abonnementen op de kosten. Micro soft gebruikt deze gegevens niet in een controle op de naleving van licenties.

Hier volgt de volledige lijst met meta gegevens van de server die door het apparaat worden verzameld en naar Azure worden verzonden.

**Gegevens** | **WMI-klasse** | **WMI-klasse-eigenschap**
--- | --- | ---
**Servergegevens** | 
Serie nummer van BIOS | Msvm_BIOSElement | BIOSSerialNumber
Server type (gen 1 of 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Weergave naam van server | Msvm_VirtualSystemSettingData | ElementName
Serverversie | Msvm_ProcessorSettingData | VirtualQuantity
Geheugen (bytes) | Msvm_MemorySettingData | VirtualQuantity
Maximale hoeveelheid geheugen die kan worden gebruikt door de server | Msvm_MemorySettingData | Limiet
Dynamisch geheugen ingeschakeld | Msvm_MemorySettingData | DynamicMemoryEnabled
Naam/versie/FQDN van het besturings systeem | Msvm_KvpExchangeComponent | Naam gegevens GuestIntrinsicExchangeItems
Energie status van de server | Msvm_ComputerSystem | EnabledState
**Details per schijf** |
Schijf-id | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Type virtuele harde schijf | Msvm_VirtualHardDiskSettingData | Type
Grootte van virtuele harde schijf | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Bovenliggende virtuele harde schijf | Msvm_VirtualHardDiskSettingData | ParentPath
**Details van de NIC** |
IP-adressen (synthetische Nic's) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP ingeschakeld (synthetische Nic's) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC-ID (synthetische Nic's) | Msvm_SyntheticEthernetPortSettingData | InstanceID
MAC-adres van NIC (synthetische Nic's) | Msvm_SyntheticEthernetPortSettingData | Adres
NIC-ID (verouderde Nic's) | MsvmEmulatedEthernetPortSetting-gegevens | InstanceID
MAC-ID van NIC (verouderde Nic's) | MsvmEmulatedEthernetPortSetting-gegevens | Adres

### <a name="performance-data"></a>Prestatiegegevens

Dit zijn de prestatie gegevens van de server die door het apparaat worden verzameld en naar Azure worden verzonden.

**Klasse prestatie meter** | **Item** | **Beoordelings impact**
--- | --- | ---
Virtuele processor van Hyper-V Hyper Visor | % Gast-uitvoerings tijd | Aanbevolen server grootte/kosten
Hyper-V-dynamisch geheugen server | Huidige belasting (%)<br/> Zichtbaar fysiek geheugen voor gast (MB) | Aanbevolen server grootte/kosten
Virtuele Hyper-V-opslag apparaat | Gelezen bytes per seconde | Berekening voor schijf grootte, opslag kosten, Server grootte
Virtuele Hyper-V-opslag apparaat | Geschreven bytes per seconde | Berekening voor schijf grootte, opslag kosten, Server grootte
Virtual Network Adapter voor Hyper-V | Ontvangen bytes per seconde | Berekening voor Server grootte
Virtual Network Adapter voor Hyper-V | Verzonden bytes per seconde | Berekening voor Server grootte

- CPU-gebruik is de som van alle gebruik voor alle virtuele processors die zijn gekoppeld aan een server.
- Geheugen gebruik is (huidige druk * gast zichtbaar fysiek geheugen)/100.
- De waarden voor de schijf-en netwerk gebruik worden verzameld uit de vermelde Hyper-V-prestatie meter items.

## <a name="collected-data---physical"></a>Verzamelde gegevens-fysiek

Het apparaat verzamelt meta gegevens over de configuratie en prestaties van fysieke of virtuele servers die on-premises worden uitgevoerd.

### <a name="metadata"></a>Metagegevens

Met de meta gegevens die door het Azure Migrate-apparaat zijn gedetecteerd, kunt u nagaan of servers gereed zijn voor migratie naar Azure, servers in de juiste grootte en abonnementen op de kosten. Micro soft gebruikt deze gegevens niet in een controle op de naleving van licenties.

### <a name="windows-server-metadata"></a>Meta gegevens van Windows Server

Hier volgt de volledige lijst met meta gegevens van Windows Server die door het apparaat worden verzameld en naar Azure worden verzonden.

**Gegevens** | **WMI-klasse** | **WMI-klasse-eigenschap**
--- | --- | ---
FQDN | Win32_ComputerSystem | Domein, naam, PartOfDomain
Aantal processor kernen | Win32_PRocessor | NumberOfCores
Toegewezen geheugen | Win32_ComputerSystem | TotalPhysicalMemory
BIOS-serie nummer | Win32_ComputerSystemProduct | Nummer
BIOS-GUID | Win32_ComputerSystemProduct | UUID
Opstarttype | Win32_DiskPartition | Controleren op partitie met type = **GPT: systeem** voor EFI/BIOS
Naam van besturingssysteem | Win32_OperatingSystem | Caption
Besturingssysteemversie |Win32_OperatingSystem | Versie
Architectuur van besturingssysteem | Win32_OperatingSystem | OSArchitecture
Aantal schijven | Win32_DiskDrive | Model, grootte, DeviceID, media type, naam
Schijfgrootte | Win32_DiskDrive | Grootte
NIC-lijst | Win32_NetworkAdapterConfiguration | Beschrijving, index
IP-adres van NIC | Win32_NetworkAdapterConfiguration | IPAddress
MAC-adres van NIC | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-server-metadata"></a>Meta gegevens van Linux-server

Hier volgt de volledige lijst met meta gegevens van de Linux-server die het apparaat verzamelt en verzendt naar Azure.

**Gegevens** | **Opdrachten**
--- | ---
FQDN | kat/proc/sys/kernel/hostname, hostnaam-f
Aantal processor kernen |  /proc/cpuinfo \| awk '/^ processor/{print $3} ' \| wc-l
Toegewezen geheugen | kat/proc/meminfo \| grep MemTotal \| awk {printf "%. 0f", $2/1024}
BIOS-serie nummer | lshw \| grep "serieel:" \| Head-N1 \| awk {print $2} " <br/> /usr/sbin/dmidecode-t 1 \| grep ' serie ' \| awk ' {$1 = ""; $2 = ""; afdrukken} '
BIOS-GUID | kat/sys/class/DMI/id/product_uuid
Opstarttype | [-d/sys/firmware/EFI]  && ECHO EFI- \| \| echo BIOS
Naam/versie van besturings systeem | We hebben toegang tot deze bestanden voor de versie van het besturings systeem en de naam:<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
Architectuur van besturingssysteem | Uname-m
Aantal schijven | fdisk-l \| egrep ' Disk. * bytes ' \| awk ' {Print $2} ' \| knippen-F1-d ': '
Opstart schijf | DF/boot \| sed-n 2p \| awk {print $1}
Schijfgrootte | fdisk-l \| egrep ' Disk. * bytes ' \| egrep $disk: \| awk ' {Print $5} '
NIC-lijst | IP-o-4 adres show \| awk {print $2}
IP-adres van NIC | IP-adres geven $nic \| grep inet \| awk ' {Print $2} ' \| knippen-F1-d '/' 
MAC-adres van NIC | IP-adres geeft $nic \| grep-  \| awk ' {Print $2} ' weer

### <a name="windows-performance-data"></a>Prestatie gegevens van Windows

Dit zijn de prestatie gegevens van de Windows-Server die het apparaat verzamelt en verzendt naar Azure.

**Gegevens** | **WMI-klasse** | **WMI-klasse-eigenschap**
--- | --- | ---
CPU-gebruik | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
Geheugengebruik | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
Aantal NIC'S | Win32_PerfFormattedData_Tcpip_NetworkInterface | Het aantal netwerk apparaten ophalen.
Ontvangen gegevens per NIC | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
Gegevens die per NIC worden verzonden | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
Aantal schijven | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | Aantal schijven
Details van schijf | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Linux-prestatie gegevens

Dit zijn de prestatie gegevens van de Linux-server die het apparaat verzamelt en verzendt naar Azure.

**Gegevens** | **Opdrachten**
--- | ---
CPU-gebruik | kat/proc/stat/| grep ' CPU '/proc/stat
Geheugengebruik | gratis \| grep \| -mem awk ' {Print $3/$ 2 * 100,0} '
Aantal NIC'S | lshw-class Network \| grep ETH [0-60] \| wc-l
Ontvangen gegevens per NIC | kat/sys/class/net/ETH $ NIC/statistieken/rx_bytes
Gegevens die per NIC worden verzonden | kat/sys/class/net/ETH $ NIC/statistieken/tx_bytes
Aantal schijven | fdisk-l \| egrep ' Disk. * bytes ' \| awk ' {Print $2} ' \| knippen-F1-d ': '
Details van schijf | kat/proc/diskstats


## <a name="appliance-upgrades"></a>Toestel-upgrades

Het apparaat wordt bijgewerkt wanneer de Azure Migrate services die op het apparaat worden uitgevoerd, worden bijgewerkt. Dit gebeurt automatisch, omdat automatisch bijwerken standaard is ingeschakeld op het apparaat. U kunt deze standaard instelling wijzigen om de toestel Services hand matig bij te werken.

### <a name="turn-off-auto-update"></a>Automatisch bijwerken uitschakelen

1. Open de REGI ster-editor op de server waarop het apparaat wordt uitgevoerd.
2. Navigeer naar **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Als u automatisch bijwerken wilt uitschakelen, maakt u een register sleutel auto **Update** -sleutel met DWORD-waarde 0.

    ![Register sleutel instellen](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Automatisch bijwerken inschakelen

U kunt automatisch bijwerken inschakelen met een van de volgende methoden:

- Door de register sleutel AutoUpdate van HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance te verwijderen.
- Klik op **apparaat-services weer geven** van de laatste update controles in het paneel **vereisten instellen** om automatisch bijwerken in te scha kelen.

De register sleutel verwijderen:

1. Open de REGI ster-editor op de server waarop het apparaat wordt uitgevoerd.
2. Navigeer naar **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Verwijder de register sleutel auto **Update** die eerder is gemaakt om automatisch bijwerken uit te scha kelen.

Als u van apparaat Configuration Manager wilt inschakelen, nadat de detectie is voltooid:

1. Ga in het configuratie beheer van het apparaat naar het paneel **vereisten instellen**
2. Klik in de laatste updates controle op **apparaat-services weer geven** en klik op de koppeling om automatisch bijwerken in te scha kelen.

    ![Automatische updates inschakelen](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>Controleer de versie van het toestel Services

U kunt de versie van het toestel nummer controleren aan de hand van een van de volgende methoden:

- Ga in configuratie beheer voor apparaten naar het paneel **vereisten instellen** .
- Op het apparaat in de   >  **Program ma's en onderdelen** van het configuratie scherm.

Controleren van de configuratie manager van het toestel:

1. Ga in het configuratie beheer van het apparaat naar het paneel **vereisten instellen**
2. Klik in de laatste updates controle op **apparaat-services weer geven**.

    ![Versie controleren](./media/migrate-appliance/versions.png)

Controleren in het configuratie scherm:

1. Klik op het apparaat op   >  **configuratie scherm**  >  **Program ma's en onderdelen** starten.
2. Controleer de versies van de toestel Services in de lijst.

    ![Controleer de versie in het configuratie scherm](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Een oudere versie hand matig bijwerken

Als u een oudere versie gebruikt voor een van de services, moet u de service verwijderen en hand matig bijwerken naar de nieuwste versie.

1. Als u de meest recente versie van de service voor apparaten wilt controleren, moet u de LatestComponents.jsin het bestand [downloaden](https://aka.ms/latestapplianceservices) .
2. Nadat u hebt gedownload, opent u het LatestComponents.jsbestand in Klad blok.
3. Zoek de nieuwste service versie in het bestand en de download koppeling hiervoor. Bijvoorbeeld:

    "Naam": "ASRMigrationWebApp", "DownloadLink": " https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi ", "versie": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4. Down load de nieuwste versie van een verouderde service met behulp van de download koppeling in het bestand.
5. Nadat u hebt gedownload, voert u de volgende opdracht uit in een Administrator-opdracht venster om de integriteit van de gedownloade MSI te controleren.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` Bijvoorbeeld: C: \> certutil-HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Controleer of de uitvoer van de opdracht overeenkomt met de invoer van de hashwaarde voor de service in het bestand (bijvoorbeeld de bovenstaande MD5-hash-waarde).
6. Voer nu het MSI-bestand uit om de service te installeren. Het is een stille installatie en het installatie venster wordt gesloten nadat het is uitgevoerd.
7. Nadat de installatie is voltooid, controleert u de versie van de service in de  >  **Program ma's en onderdelen** van het configuratie scherm. De service versie moet nu worden bijgewerkt naar de meest recente weer gave in het JSON-bestand.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over](how-to-set-up-appliance-vmware.md) het instellen van het apparaat voor VMware.
- [Meer informatie over](how-to-set-up-appliance-hyper-v.md) het instellen van het apparaat voor Hyper-V.
- [Meer informatie over](how-to-set-up-appliance-physical.md) het instellen van het apparaat voor fysieke servers.