---
title: SAP LaMa-connector voor Azure | Microsoft Docs
description: SAP-systemen in azure beheren met SAP LaMa
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: sedusch
ms.openlocfilehash: 4772fdae06f23430d829fa411068b7af7a85b3dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101668704"
---
# <a name="sap-lama-connector-for-azure"></a>SAP LaMa-connector voor Azure

[1877727]:https://launchpad.support.sap.com/#/notes/1877727
[2343511]:https://launchpad.support.sap.com/#/notes/2343511
[2350235]:https://launchpad.support.sap.com/#/notes/2350235
[2562184]:https://launchpad.support.sap.com/#/notes/2562184
[2628497]:https://launchpad.support.sap.com/#/notes/2628497
[2445033]:https://launchpad.support.sap.com/#/notes/2445033
[2815988]:https://launchpad.support.sap.com/#/notes/2815988
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Algemene ondersteunings verklaring: open altijd een incident met SAP op onderdeel BC-VCM-LVM-hyper-v als u ondersteuning nodig hebt voor SAP LaMa of de Azure-connector.

SAP LaMa wordt door veel klanten gebruikt om hun SAP-landschap te bedienen en te bewaken. Sinds SAP LaMa 3,0 SP05 wordt het standaard geleverd met een connector naar Azure. U kunt deze connector gebruiken om de toewijzing van virtuele machines te verwijderen en te starten, Managed disks te kopiëren en te verplaatsen, en Managed disks te verwijderen. Met deze basis bewerkingen kunt u SAP-systemen verplaatsen, kopiëren, klonen en vernieuwen met behulp van SAP LaMa.

In deze hand leiding wordt beschreven hoe u de Azure connector voor SAP LaMa instelt, kunt u virtuele machines maken die kunnen worden gebruikt voor het installeren van adaptieve SAP-systemen en het configureren ervan.

> [!NOTE]
> De connector is alleen beschikbaar in de SAP LaMa Enter prise Edition

## <a name="resources"></a>Resources

De volgende SAP-opmerkingen zijn gerelateerd aan het onderwerp van SAP LaMa op Azure:

| Nummer van notitie | Titel |
| --- | --- |
| [2343511] |Microsoft Azure connector voor het beheer van SAP liggend (LaMa) |
| [2350235] |SAP liggend-beheer 3,0-Enter prise Edition |

Lees ook de [SAP-Help Portal voor SAP LaMa](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE).

## <a name="general-remarks"></a>Algemene opmerkingen

* Zorg ervoor dat het *automatisch maken van koppel punt* in Setup-> instellingen->-engine wordt ingeschakeld  
  Als SAP LaMa volumes koppelt met behulp van de SAP-adaptieve extensies op een virtuele machine, moet het koppel punt bestaan als deze instelling niet is ingeschakeld.

* Gebruik een afzonderlijk subnet en gebruik geen dynamische IP-adressen om IP-adres ' stelen ' te voor komen bij het implementeren van nieuwe Vm's en SAP-exemplaren worden niet voor bereid  
  Als u toewijzing van dynamische IP-adressen in het subnet gebruikt, die ook wordt gebruikt door SAP LaMa, kan het voorbereiden van een SAP-systeem met SAP LaMa mislukken. Als een SAP-systeem niet wordt voor bereid, zijn de IP-adressen niet gereserveerd en kunnen ze worden toegewezen aan andere virtuele machines.

* Als u zich aanmeldt bij Managed hosts, moet u ervoor zorgen dat bestands systemen niet worden ontkoppeld  
  Als u zich aanmeldt bij een virtuele Linux-machine en de werkmap wijzigt in een map in een koppel punt, bijvoorbeeld/usr/sap/AH1/ASCS00/exe, kan het volume niet worden ontkoppeld en kan het niet worden verwijderd of worden voor bereid.

* Zorg ervoor dat CLOUD_NETCONFIG_MANAGE is uitgeschakeld op SUSE SLES Linux virtual machines. Zie [SuSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633)voor meer informatie.

## <a name="set-up-azure-connector-for-sap-lama"></a>Azure connector instellen voor SAP LaMa

De Azure-connector wordt geleverd vanaf SAP LaMa 3,0 SP05. U kunt het beste altijd het meest recente ondersteunings pakket en patch voor SAP LaMa 3,0 installeren.

De Azure-connector gebruikt de Azure Resource Manager-API om uw Azure-resources te beheren. SAP LaMa kan gebruikmaken van een service-principal of een beheerde identiteit voor verificatie bij deze API. Als uw SAP-LaMa wordt uitgevoerd op een virtuele machine van Azure, raden we u aan een beheerde identiteit te gebruiken zoals beschreven in hoofd stuk [een beheerde identiteit gebruiken om toegang te krijgen tot de Azure-API](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d). Als u een Service-Principal wilt gebruiken, volgt u de stappen in hoofd stuk [een Service-Principal gebruiken om toegang te krijgen tot de Azure-API](lama-installation.md#913c222a-3754-487f-9c89-983c82da641e).

### <a name="use-a-service-principal-to-get-access-to-the-azure-api"></a><a name="913c222a-3754-487f-9c89-983c82da641e"></a>Een Service-Principal gebruiken om toegang te krijgen tot de Azure API

De Azure-connector kan gebruikmaken van een service-principal voor het autoriseren van Microsoft Azure. Volg deze stappen om een service-principal te maken voor het beheer van SAP liggend (LaMa).

1. Ga naar https://portal.azure.com
1. Open de Blade Azure Active Directory
1. Klik op App-registraties
1. Klik op nieuwe registratie
1. Voer een naam in en klik op registreren
1. Selecteer de nieuwe app en klik op certificaten & geheimen op het tabblad instellingen.
1. Een nieuw client geheim maken, een beschrijving invoeren voor een nieuwe sleutel, selecteren wanneer het geheim moet verlopen en op Opslaan klikken
1. Schrijf de waarde op. Dit wordt gebruikt als het wachtwoord van de service-principal
1. Noteer de toepassings-id. Deze wordt gebruikt als de gebruikers naam van de Service-Principal

De service-principal heeft standaard geen machtigingen voor toegang tot uw Azure-resources. U moet de Service-Principal machtigingen verlenen voor toegang tot de services.

1. Ga naar https://portal.azure.com
1. Open de Blade resource groepen
1. Selecteer de resource groep die u wilt gebruiken
1. Klik op toegangs beheer (IAM)
1. Klik op roltoewijzing toevoegen
1. De rol bijdrager selecteren
1. Voer de naam in van de toepassing die u hierboven hebt gemaakt
1. Klik op Opslaan
1. Herhaal stap 3 tot 8 voor alle resource groepen die u wilt gebruiken in SAP LaMa

### <a name="use-a-managed-identity-to-get-access-to-the-azure-api"></a><a name="af65832e-6469-4d69-9db5-0ed09eac126d"></a>Een beheerde identiteit gebruiken om toegang te krijgen tot de Azure API

Als u een beheerde identiteit wilt gebruiken, moet uw SAP LaMa-exemplaar worden uitgevoerd op een virtuele Azure-machine met een systeem-of gebruiker-toegewezen identiteit. Lees voor meer informatie over beheerde identiteiten [Wat is beheerde identiteiten voor Azure-resources?](../../../active-directory/managed-identities-azure-resources/overview.md) en [Configureer beheerde identiteiten voor Azure-resources op een virtuele machine met behulp van de Azure Portal](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md).

De beheerde identiteit heeft standaard geen machtigingen voor toegang tot uw Azure-resources. U moet deze machtigingen verlenen voor toegang.

1. Ga naar https://portal.azure.com
1. Open de Blade resource groepen
1. Selecteer de resource groep die u wilt gebruiken
1. Klik op toegangs beheer (IAM)
1. Klik op toevoegen-> roltoewijzing toevoegen
1. De rol bijdrager selecteren
1. Selecteer ' virtuele machine ' voor ' toegang toewijzen '
1. Selecteer de virtuele machine waarop uw SAP LaMa-exemplaar wordt uitgevoerd
1. Klik op Opslaan
1. Herhaal de stappen voor alle resource groepen die u wilt gebruiken in SAP LaMa

Selecteer beheerde identiteit gebruiken in de configuratie van uw SAP LaMa Azure-connector om het gebruik van de beheerde identiteit in te scha kelen. Als u een door het systeem toegewezen identiteit wilt gebruiken, zorg er dan voor dat u het veld gebruikers naam leeg laat. Als u een door de gebruiker toegewezen identiteit wilt gebruiken, voert u de gebruiker toegewezen identiteits-id in het veld gebruikers naam in.

### <a name="create-a-new-connector-in-sap-lama"></a>Een nieuwe connector maken in SAP LaMa

Open de SAP LaMa-website en navigeer naar infra structuur. Ga naar tabblad Cloud managers en klik op toevoegen. Selecteer de Microsoft Azure Cloud Adapter en klik op volgende. Voer de volgende informatie in:

* Label: Kies een naam voor de connector instantie
* Gebruikers naam: de Service-Principal-toepassings-ID of ID van de door de gebruiker toegewezen identiteit van de virtuele machine. Zie [een systeem-of door de gebruiker toegewezen identiteit gebruiken] voor meer informatie
* Wacht woord: sleutel/wacht woord voor Service-Principal. U kunt dit veld leeg laten als u een door het systeem of de gebruiker toegewezen identiteit gebruikt.
* URL: standaard blijven `https://management.azure.com/`
* Bewakings interval (seconden): moet ten minste 300 zijn
* Beheerde identiteit gebruiken: SAP LaMa kan gebruikmaken van een door het systeem of de gebruiker toegewezen identiteit voor verificatie bij de Azure API. Zie hoofd stuk [een beheerde identiteit gebruiken om toegang te krijgen tot de Azure API](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d) in deze hand leiding.
* Abonnements-ID: Azure-abonnements-ID
* Azure Active Directory Tenant-ID: ID van de Active Directory Tenant
* Proxy-host: de hostnaam van de proxy als SAP LaMa een proxy nodig heeft om verbinding te maken met Internet
* Proxy poort: TCP-poort van de proxy
* Opslag type wijzigen om kosten op te slaan: Schakel deze instelling in als de Azure-adapter het opslag type van de Managed Disks moet wijzigen om kosten te besparen wanneer de schijven niet worden gebruikt. Voor gegevens schijven waarnaar wordt verwezen in een SAP-exemplaar configuratie, wijzigt de adapter het schijf type in standaard opslag tijdens een instantie, en wordt deze weer voor bereid op het oorspronkelijke opslag type tijdens het voorbereiden van een exemplaar. Als u een virtuele machine in SAP LaMa stopt, wijzigt de adapter het opslag type van alle gekoppelde schijven, met inbegrip van de schijf van het besturings systeem naar de standaard opslag. Als u een virtuele machine in SAP LaMa start, wordt het opslag type door de adapter gewijzigd in het oorspronkelijke opslag type.

Klik op configuratie testen om uw invoer te valideren. U ziet het volgende:

De verbinding is gemaakt: de verbinding met micro soft Cloud is geslaagd. 7 resource groepen gevonden (alleen 10 gevraagde groepen)

onder aan de website.

## <a name="provision-a-new-adaptive-sap-system"></a>Een nieuw adaptief SAP-systeem inrichten

U kunt een nieuwe virtuele machine hand matig implementeren of een van de Azure-sjablonen gebruiken in de Quick Start- [opslag plaats](https://github.com/Azure/azure-quickstart-templates). Het bevat sjablonen voor [SAP NetWeaver ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs), [SAP NetWeaver-toepassings servers](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)en de- [Data Base](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database). U kunt deze sjablonen ook gebruiken om nieuwe hosts in te richten als onderdeel van een systeem kopie/kloon, enzovoort.

We raden u aan om een afzonderlijk subnet te gebruiken voor alle virtuele machines die u wilt beheren met SAP LaMa en geen dynamische IP-adressen gebruiken om te voor komen dat IP-adres ' stelen ' wordt gebruikt bij het implementeren van nieuwe virtuele machines en SAP-exemplaren worden niet voor bereid.

> [!NOTE]
> Als dat mogelijk is, verwijdert u alle extensies van de virtuele machine, omdat deze mogelijk lange Runtimes veroorzaken voor het loskoppelen van schijven van een virtuele machine.

Zorg ervoor dat de gebruiker \<hanasid> adm, \<sapsid> adm en groeps sapsys bestaan op de doel computer met dezelfde id en GID of gebruik LDAP. Schakel de NFS-server in en start deze op de virtuele machines die moeten worden gebruikt om de SAP NetWeaver (A) SCS uit te voeren.

### <a name="manual-deployment"></a>Hand matige implementatie

SAP LaMa communiceert met de virtuele machine met behulp van de SAP host agent. Als u de virtuele machines hand matig implementeert of niet de Azure Resource Manager sjabloon in de Quick Start-opslag plaats gebruikt, moet u ervoor zorgen dat u de nieuwste SAP host agent en de SAP Adaptive Extensions installeert. Zie SAP Note [2343511]voor meer informatie over de vereiste patch niveaus voor Azure.

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Hand matige implementatie van een virtuele Linux-machine

Maak een nieuwe virtuele machine met een van de ondersteunde besturings systemen die worden vermeld in SAP Note [2343511]. Extra IP-configuraties voor de SAP-instanties toevoegen. Elk exemplaar heeft ten minste op het IP-adres nodig en moet worden geïnstalleerd met behulp van een virtuele hostnaam.

Het SAP NetWeaver ASCS-exemplaar heeft schijven nodig voor/sapmnt/ \<SAPSID> ,/usr/sap/ \<SAPSID> ,/usr/sap/trans en/usr/sap/ \<sapsid> adm. De SAP NetWeaver-toepassings servers hebben geen extra schijven nodig. Alles met betrekking tot het SAP-exemplaar moet worden opgeslagen op de ASCS en worden geëxporteerd via NFS. Als dat niet het geval is, is het momenteel niet mogelijk om aanvullende toepassings servers met SAP LaMa toe te voegen.

![SAP NetWeaver ASCS op Linux](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Hand matige implementatie voor SAP HANA

Maak een nieuwe virtuele machine met een van de ondersteunde besturings systemen voor SAP HANA zoals vermeld in SAP Note [2343511]. Voeg een extra IP-configuratie toe voor SAP HANA en één per HANA-Tenant.

SAP HANA heeft schijven nodig voor/Hana/Shared,/Hana/backup,/Hana/data en/Hana/log

![SAP HANA in Linux](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Hand matige implementatie voor Oracle Database in Linux

Maak een nieuwe virtuele machine met een van de ondersteunde besturings systemen voor Oracle-data bases zoals vermeld in SAP Note [2343511]. Voeg een extra IP-configuratie toe voor de Oracle-data base.

De Oracle-data base vereist schijven voor/Oracle,/Home/oraod1 en/Home/Oracle

![Diagram waarin een Oracle-data base in Linux wordt weer gegeven en de schijven die nodig zijn.](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Hand matige implementatie voor Microsoft SQL Server

Maak een nieuwe virtuele machine met een van de ondersteunde besturings systemen voor Microsoft SQL Server zoals vermeld in SAP Note [2343511]. Voeg een extra IP-configuratie toe voor het SQL Server-exemplaar.

De SQL Server-database server vereist schijven voor de database gegevens en logboek bestanden en-schijven voor c:\usr\sap.

![Oracle-Data Base op Linux](media/lama/sap-lama-db-sql.png)

Zorg ervoor dat u een ondersteund micro soft ODBC-stuur programma voor SQL Server installeert op een virtuele machine die u wilt gebruiken om een SAP NetWeaver-toepassings server te verplaatsen naar of als een systeem kopie/kloon doel.

SAP LaMa kan SQL Server zichzelf niet verplaatsen, dus een virtuele machine die u wilt gebruiken om een Data Base-exemplaar te verplaatsen naar of als een systeem kopie/kloon doel vereisten SQL Server vooraf geïnstalleerd.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Een virtuele machine implementeren met behulp van een Azure-sjabloon

Down load de volgende meest recente beschik bare archieven van de [SAP-software Marketplace](https://support.sap.com/swdc) voor het besturings systeem van de virtuele machines:

1. SAPCAR 7,21
1. SAP HOST AGENT 7,21
1. SAP ADAPTIVE EXTENSION 1,0 EXT

Down load ook de volgende onderdelen van het [micro soft Download centrum](https://www.microsoft.com/download)

1. Herdistribueerbaar pakket voor micro soft Visual C++ 2010 (x64) (alleen Windows)
1. Micro soft ODBC-stuur programma voor SQL Server (alleen SQL Server)

De onderdelen zijn vereist voor het implementeren van de sjabloon. De eenvoudigste manier om ze beschikbaar te maken voor de sjabloon is door ze te uploaden naar een Azure-opslag account en een Shared Access Signature (SAS) te maken.

De sjablonen hebben de volgende para meters:

* sapSystemId: de SAP-systeem-ID. Het wordt gebruikt om de schijf indeling te maken (bijvoorbeeld/usr/sap/ \<sapsid> ).

* computername: de computer naam van de nieuwe virtuele machine. Deze para meter wordt ook gebruikt door SAP LaMa. Wanneer u deze sjabloon gebruikt om een nieuwe virtuele machine in te richten als onderdeel van een systeem kopie, wacht SAP LaMa totdat de host met deze computer naam kan worden bereikt.

* osType: het type van het besturings systeem dat u wilt implementeren.

* DBTYPE: het type van de data base. Deze para meter wordt gebruikt om te bepalen hoeveel extra IP-configuraties moeten worden toegevoegd en hoe de schijf indeling eruit moet zien.

* sapSystemSize: de grootte van het SAP-systeem dat u wilt implementeren. Het wordt gebruikt om het type en de grootte van het exemplaar van de virtuele machine te bepalen.

* adminUsername: gebruikers naam voor de virtuele machine.

* adminPassword: wacht woord voor de virtuele machine. U kunt ook een open bare sleutel voor SSH opgeven.

* sshKeyData: open bare SSH-sleutel voor de virtuele machines. Alleen ondersteund voor Linux-besturings systemen.

* subnetId: de ID van het subnet dat u wilt gebruiken.

* deployEmptyTarget: u kunt een leeg doel implementeren als u de virtuele machine als doel wilt gebruiken voor het verplaatsen van een exemplaar. In dit geval worden er geen extra schijven of IP-configuraties gekoppeld.

* sapcarLocation: de locatie voor de SAPCAR-toepassing die overeenkomt met het besturings systeem dat u implementeert. SAPCAR wordt gebruikt om de archieven die u opgeeft in andere para meters uit te pakken.

* sapHostAgentArchiveLocation: de locatie van het SAP host agent-archief. SAP host agent wordt geïmplementeerd als onderdeel van deze sjabloon implementatie.

* sapacExtLocation: de locatie van de SAP-adaptieve extensies. SAP Note [2343511] bevat het minimale patch niveau dat vereist is voor Azure.

* vcRedistLocation: de locatie van de VC runtime die is vereist om de SAP-uitbreidings uitbreidingen te installeren. Deze para meter is alleen vereist voor Windows.

* odbcDriverLocation: de locatie van het ODBC-stuur programma dat u wilt installeren. Alleen het micro soft ODBC-stuur programma voor SQL Server wordt ondersteund.

* sapadmPassword: het wacht woord voor de sapadm-gebruiker.

* sapadmId: de Linux-gebruikers-ID van de sapadm-gebruiker. Niet vereist voor Windows.

* sapsysGid: de Linux-groeps-ID van de sapsys-groep. Niet vereist voor Windows.

* _artifactsLocation: de basis-URI, waar artefacten die door deze sjabloon worden vereist, zich bevinden. Wanneer de sjabloon wordt geïmplementeerd met behulp van de bijbehorende scripts, wordt een persoonlijke locatie in het abonnement gebruikt en wordt deze waarde automatisch gegenereerd. Alleen nodig als u de sjabloon niet implementeert vanuit GitHub.

* _artifactsLocationSasToken: de sasToken die vereist is voor toegang tot _artifactsLocation. Wanneer de sjabloon wordt geïmplementeerd met behulp van de bijbehorende scripts, wordt automatisch een sasToken gegenereerd. Alleen nodig als u de sjabloon niet implementeert vanuit GitHub.

### <a name="sap-hana"></a>SAP HANA

In de onderstaande voor beelden wordt ervan uitgegaan dat u SAP HANA installeert met de systeem-ID HN1 en het SAP NetWeaver-systeem met systeem-ID AH1. De virtuele hostnamen zijn HN1-DB voor het HANA-exemplaar ah1-DB voor de HANA-Tenant die wordt gebruikt door het SAP NetWeaver-systeem, ah1-ascs voor de SAP NetWeaver ASCS en ah1-di-0 voor de eerste SAP NetWeaver-toepassings server.

#### <a name="install-sap-netweaver-ascs-for-sap-hana-using-azure-managed-disks"></a>SAP NetWeaver ASCS voor SAP HANA installeren met behulp van Azure Managed Disks

Voordat u de SAP software Provisioning Manager (SWPM) start, moet u het IP-adres van de virtuele hostnaam van de ASCS koppelen. De aanbevolen manier is om sapacext te gebruiken. Als u het IP-adres koppelt met behulp van sapacext, moet u het IP-adres na het opnieuw opstarten opnieuw koppelen.

![Linux-logo.][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-ascs -n 255.255.255.128
```

![Windows-logo.][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-ascs -n 255.255.255.128
```

Voer SWPM uit en gebruik *ah1-ascs* voor de hostnaam van het *ascs-exemplaar*.

![Linux-logo.][Logo_Linux] Linux  
Voeg de volgende profiel parameter toe aan het SAP host agent-profiel, dat zich bevindt op/usr/sap/hostctrl/exe/host_profile. Zie SAP Note [2628497]voor meer informatie.
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-netweaver-ascs-for-sap-hana-on-azure-netappfiles-anf-beta"></a>SAP NetWeaver ASCS installeren voor SAP HANA op Azure NetAppFiles (ANF) bèta

> [!NOTE]
> Deze functionaliteit is nog niet beschikbaar. Raadpleeg SAP Note [2815988] (alleen zichtbaar voor preview-klanten) voor meer informatie.
Open een SAP-incident op onderdeel BC-VCM-LVM-hyper-v en vraag om deel te nemen aan de LaMa-opslag adapter voor Azure NetApp Files preview

ANF biedt NFS voor Azure. In de context van SAP LaMa Dit vereenvoudigt het maken van de ASCS-instanties (ABAP Central Services) en de volgende installatie van toepassings servers. Voorheen moest het ASCS-exemplaar ook als NFS-server functioneren en moet de para meter acosprep/nfs_paths worden toegevoegd aan de host_profile van de SAP Hostagent.

#### <a name="anf-is-currently-available-in-these-regions"></a>ANF is momenteel beschikbaar in deze regio's:

Australië-oost, Central VS, VS-Oost, VS-Oost 2, Europa-noord, Zuid-Centraal VS, Europa-west en VS-West 2.

#### <a name="network-requirements"></a>Netwerk vereisten

ANF vereist een gedelegeerd subnet dat deel moet uitmaken van hetzelfde VNET als de SAP-servers. Hier volgt een voor beeld van een dergelijke configuratie.
In dit scherm wordt het maken van het VNET en het eerste subnet weer gegeven:

![SAP LaMa virtueel netwerk maken voor Azure ANF ](media/lama/sap-lama-createvn-50.png)

Met de volgende stap maakt u het gedelegeerde subnet voor micro soft. NetApp/volumes.

![SAP LaMa gedelegeerd subnet toevoegen ](media/lama/sap-lama-addsubnet-50.png)

![SAP LaMa-lijst met subnetten ](media/lama/sap-lama-subnets.png)

U moet nu een NetApp-account maken in de Azure Portal:

![SAP LaMa NetApp-account maken ](media/lama/sap-lama-create-netappaccount-50.png)

![SAP LaMa NetApp-account gemaakt ](media/lama/sap-lama-netappaccount.png)

Binnen het NetApp-account specificeert de capaciteits pool de grootte en het type van de schijven voor elke pool:

![SAP LaMa NetApp-capaciteits pool maken ](media/lama/sap-lama-capacitypool-50.png)

![Groep SAP LaMa NetApp-capaciteit gemaakt ](media/lama/sap-lama-capacitypool-list.png)

De NFS-volumes kunnen nu worden gedefinieerd. Omdat er volumes voor meerdere systemen in één pool zijn, moet een zelfuitlegend naamgevings schema worden gekozen. Door de SID toe te voegen, kunt u gerelateerde volumes samen groeperen. De volgende koppels zijn vereist voor de ASCS en het as-exemplaar *: \<SID\> /sapmnt/*, */usr/sap/ \<SID\>* en */Home/ \<sid\> adm*. Optioneel is */usr/sap/trans* vereist voor de centrale transport Directory, die ten minste wordt gebruikt door alle systemen van één land.

> [!NOTE]
> Tijdens de bèta fase moeten de naam van de volumes uniek zijn binnen het abonnement.

![SAP LaMa een volume maken 1 ](media/lama/sap-lama-createvolume-80.png)

![SAP LaMa een volume maken 2 ](media/lama/sap-lama-createvolume2-80.png)

![SAP LaMa een volume maken 3 ](media/lama/sap-lama-createvolume3-80.png)

Deze stappen moeten ook worden herhaald voor de andere volumes.

![SAP LaMa-lijst met gemaakte volumes ](media/lama/sap-lama-volumes.png)

Deze volumes moeten nu worden gekoppeld aan de systemen waarop de eerste installatie met de SAP-SWPM wordt uitgevoerd.

Eerst moeten de koppel punten worden gemaakt. In dit geval is de SID AN1, dus moeten de volgende opdrachten worden uitgevoerd:

```bash
mkdir -p /home/an1adm
mkdir -p /sapmnt/AN1
mkdir -p /usr/sap/AN1
mkdir -p /usr/sap/trans
```
De volgende ANF-volumes worden gekoppeld met de volgende opdrachten:

```bash
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-home-sidadm /home/an1adm
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-sapmnt-sid /sapmnt/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-usr-sap-sid /usr/sap/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/global-usr-sap-trans /usr/sap/trans
```
De koppelings opdrachten kunnen ook worden afgeleid van de portal. De lokale koppel punten moeten worden aangepast.

Gebruik de df-h-opdracht om te controleren.

![OS-niveau SAP LaMa-koppel punten ](media/lama/sap-lama-mounts.png)

Nu moet de installatie met SWPM worden uitgevoerd.

Dezelfde stappen moeten worden uitgevoerd voor ten minste één AS-exemplaar.

Nadat de installatie is voltooid, moet het systeem worden gedetecteerd binnen de SAP-LaMa.

De koppel punten moeten er als volgt uitzien voor het ASCS en het AS-exemplaar:

![SAP-LaMa koppel punten in LaMa ](media/lama/sap-lama-ascs.png) (dit is een voor beeld. De IP-adressen en het exportpad verschillen van de waarden die eerder zijn gebruikt)


#### <a name="install-sap-hana"></a>SAP HANA installeren

Als u SAP HANA installeert met behulp van het opdracht regel programma hdblcm, gebruikt u de para meter--hostname om een virtuele hostnaam op te geven. U moet het IP-adres van de virtuele hostnaam van de data base toevoegen aan een netwerk interface. De aanbevolen manier is om sapacext te gebruiken. Als u het IP-adres koppelt met behulp van sapacext, moet u het IP-adres na het opnieuw opstarten opnieuw koppelen.

Voeg een andere virtuele hostnaam en een ander IP-adres toe voor de naam die wordt gebruikt door de toepassings servers om verbinding te maken met de HANA-Tenant.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Voer de installatie van het data base-exemplaar van SWPM op de virtuele machine van de toepassings server uit en niet op de HANA-virtuele machine. Gebruik *ah1-DB* voor de *Data Base-host* in de dialoog *database voor SAP-systeem*.

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Application Server voor SAP NetWeaver installeren voor SAP HANA

Voordat u de SAP software Provisioning Manager (SWPM) start, moet u het IP-adres van de virtuele hostnaam van de toepassings server koppelen. De aanbevolen manier is om sapacext te gebruiken. Als u het IP-adres koppelt met behulp van sapacext, moet u het IP-adres na het opnieuw opstarten opnieuw koppelen.

![Linux-logo.][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-di-0 -n 255.255.255.128
```

![Windows-logo.][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-di-0 -n 255.255.255.128
```

U kunt het beste de SAP NetWeaver-profiel parameter db's/HDB/hdb_use_ident gebruiken om de identiteit in te stellen die wordt gebruikt om de sleutel in de HDB userstore te vinden. U kunt deze para meter hand matig toevoegen na de installatie van het data base-exemplaar met SWPM of SWPM uitvoeren met

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Als u deze hand matig hebt ingesteld, moet u ook nieuwe HDB userstore-vermeldingen maken.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Gebruik *ah1-di-0* voor de *pas-host-naam* in het dialoog venster *primaire Application Server-instantie*.

#### <a name="post-installation-steps-for-sap-hana"></a>Stappen na de installatie voor SAP HANA

Zorg ervoor dat u een back-up maakt van de SYSTEMDB en alle Tenant databases voordat u een Tenant kopieert, een Tenant verplaatst of een systeem replicatie maakt.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

In de onderstaande voor beelden wordt ervan uitgegaan dat u het SAP NetWeaver-systeem met de systeem-ID AS1 installeert. De virtuele hostnamen zijn AS1-DB voor de SQL Server-instantie die wordt gebruikt door het SAP NetWeaver-systeem, AS1-ascs voor de SAP NetWeaver ASCS en AS1-di-0 voor de eerste SAP NetWeaver-toepassings server.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>SAP NetWeaver ASCS voor SQL Server installeren

Voordat u de SAP software Provisioning Manager (SWPM) start, moet u het IP-adres van de virtuele hostnaam van de ASCS koppelen. De aanbevolen manier is om sapacext te gebruiken. Als u het IP-adres koppelt met behulp van sapacext, moet u het IP-adres na het opnieuw opstarten opnieuw koppelen.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Voer SWPM uit en gebruik *AS1-ascs* voor de hostnaam van het *ascs-exemplaar*.

#### <a name="install-sql-server"></a>SQL Server installeren

U moet het IP-adres van de virtuele hostnaam van de data base toevoegen aan een netwerk interface. De aanbevolen manier is om sapacext te gebruiken. Als u het IP-adres koppelt met behulp van sapacext, moet u het IP-adres na het opnieuw opstarten opnieuw koppelen.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Voer de installatie van het data base-exemplaar van SWPM op de virtuele SQL Server-machine uit. Gebruik SAPINST_USE_HOSTNAME =*AS1-DB* om de hostnaam te overschrijven die wordt gebruikt om verbinding te maken met SQL Server. Als u de virtuele machine hebt geïmplementeerd met behulp van de Azure Resource Manager sjabloon, moet u ervoor zorgen dat de map die wordt gebruikt voor de database gegevens bestanden, wordt ingesteld op *C:\sql\data* en het database logboek bestand op *C:\sql\log*.

Zorg ervoor dat de gebruiker *NT AUTHORITY\SYSTEM* toegang heeft tot de SQL Server en dat de serverrol *sysadmin*. Zie SAP Note [1877727] en [2562184]voor meer informatie.

#### <a name="install-sap-netweaver-application-server"></a>De SAP NetWeaver-toepassings server installeren

Voordat u de SAP software Provisioning Manager (SWPM) start, moet u het IP-adres van de virtuele hostnaam van de toepassings server koppelen. De aanbevolen manier is om sapacext te gebruiken. Als u het IP-adres koppelt met behulp van sapacext, moet u het IP-adres na het opnieuw opstarten opnieuw koppelen.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Gebruik *AS1-di-0* voor de *pas-host-naam* in het dialoog venster *primaire Application Server-instantie*.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="errors-and-warnings-during-discover"></a>Fouten en waarschuwingen tijdens Discover

* De machtiging SELECT is geweigerd
  * Micro soft [ODBC SQL Server-stuur programma] [SQL Server] De machtiging SELECT is geweigerd voor het object log_shipping_primary_databases, de data base msdb, het schema dbo. [SOAPFaultException]  
  De machtiging SELECT is geweigerd voor het object log_shipping_primary_databases, de data base msdb, het schema dbo.
  * Oplossing  
    Zorg ervoor dat *NT AUTHORITY\SYSTEM* toegang kan krijgen tot de SQL Server. Zie SAP Note [2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>Fouten en waarschuwingen voor exemplaar validatie

* Er is een uitzonde ring opgetreden bij het valideren van de HDB userstore  
  * Zie log viewer  
    com. SAP. NW. lm. ACI. monitor. API. Validation. RuntimeValidationException: uitzonde ring in validator met ID RuntimeHDBConnectionValidator (validatie: ' VALIDATION_HDB_USERSTORE '): kan de hdbuserstore niet ophalen  
    HANA-userstore bevindt zich niet op de juiste locatie
  * Oplossing  
    Zorg ervoor dat de/usr/sap/AH1/hdbclient/install/-installation.ini juist is

### <a name="errors-and-warnings-during-a-system-copy"></a>Fouten en waarschuwingen tijdens een systeem kopie

* Er is een fout opgetreden bij het valideren van de stap voor het inrichten van het systeem
  * Veroorzaakt door: com. SAP. NW. lm. ACI. engine. base. API. util. Exception. HAOperationException '/usr/sap/hostctrl/exe/sapacext-a ShowHanaBackups-m aanroepen HN1-f 50-h HN1-DB-o level = 0 \; Status = 5 \; poort = 35013 PF =/usr/sap/hostctrl/exe/Host_profile-r-T dev_lvminfo-u System-p Hook-r |/usr/sap/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o level = 0 \; Status = 5 \; poort = 35013 PF =/usr/sap/hostctrl/exe/Host_profile-r-T dev_lvminfo-u systeem-p Hook-r
  * Oplossing  
    Back-ups maken van alle data bases in het bron HANA-systeem

* Systeem kopie stap *begin* van data base-exemplaar
  * De Hosta Gent-bewerking 000D3A282BC91EE8A1D76CF1F92E2944 is mislukt (OperationException. FaultCode: ' 127 ', bericht: ' de uitvoering van de opdracht is mislukt. : [Micro soft] [ODBC SQL Server Driver] [SQL Server] gebruiker heeft geen machtiging voor het wijzigen van de data base AS2, de data base bestaat niet of de Data Base bevindt zich niet in een status waarin toegangs controles zijn toegestaan. ')
  * Oplossing  
    Zorg ervoor dat *NT AUTHORITY\SYSTEM* toegang kan krijgen tot de SQL Server. Zie SAP Note [2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>Fouten en waarschuwingen tijdens het systeem klonen

* Er is een fout opgetreden bij een poging om de instantie agent te registreren in de stap *geforceerd registreren en de instantie agent* van de toepassings server of het ASCS te starten
  * Er is een fout opgetreden bij het registreren van de exemplaar agent. (RemoteException: kan exemplaar gegevens niet laden uit profiel ' \\ as1-ascs\sapmnt\as1\sys\profile\ AS1_D00_as1-di-0 ': kan geen toegang krijgen tot \\ het profiel ' AS1-ascs\sapmnt\as1\sys\profile\ AS1_D00_as1-di-0 ': dit bestand of deze map bestaat niet. ')
  * Oplossing  
   Zorg ervoor dat de sapmnt-share op de ASCS/SCS volledige toegang heeft tot SAP_AS1_GlobalAdmin

* Fout in stap *opstart beveiliging inschakelen voor klonen*
  * Kan het bestand As1-ascs\sapmnt\as1\sys\profile\ niet openen \\ AS1_D00_as1-di-0-oorzaak: dit bestand of deze map bestaat niet
  * Oplossing  
    Het computer account van de toepassings server moet schrijf toegang hebben tot het profiel

### <a name="errors-and-warnings-during-create-system-replication"></a>Fouten en waarschuwingen tijdens het maken van een systeem replicatie

* Uitzonde ring bij klikken op systeem replicatie maken
  * Veroorzaakt door: com. SAP. NW. lm. ACI. engine. base. API. util. Exception. HAOperationException '/usr/sap/hostctrl/exe/sapacext-a ShowHanaBackups-m aanroepen HN1-f 50-h HN1-DB-o level = 0 \; Status = 5 \; poort = 35013 PF =/usr/sap/hostctrl/exe/Host_profile-r-T dev_lvminfo-u System-p Hook-r |/usr/sap/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o level = 0 \; Status = 5 \; poort = 35013 PF =/usr/sap/hostctrl/exe/Host_profile-r-T dev_lvminfo-u systeem-p Hook-r
  * Oplossing  
    Testen of sapacext kan worden uitgevoerd als `<hanasid`>adm

* Fout bij het inschakelen van de volledige kopie in de stap opslag
  * Er is een fout opgetreden bij het rapporteren van een context kenmerk bericht voor pad IStorageCopyData. storageVolumeCopyList: 1 en veld targetStorageSystemId
  * Oplossing  
    Negeer waarschuwingen in de stap en probeer het opnieuw. Dit probleem wordt opgelost in een nieuw ondersteunings pakket/patch van SAP LaMa.

### <a name="errors-and-warnings-during-relocate"></a>Fouten en waarschuwingen tijdens het verplaatsen

* Het pad '/usr/sap/AH1 ' is niet toegestaan voor NFS-exports.
  * Raadpleeg SAP Note [2628497] voor meer informatie.
  * Oplossing  
    Voeg ASCS-export toe aan het ASCS HostAgent-profiel. Zie SAP Note [2628497]

* De functie is niet geïmplementeerd tijdens het verplaatsen van ASCS
  * Uitvoer van de opdracht: exportfs: host:/usr/sap/AX1: functie niet geïmplementeerd
  * Oplossing  
    Zorg ervoor dat de NFS Server-service is ingeschakeld op de virtuele doel machine verplaatsen

### <a name="errors-and-warnings-during-application-server-installation"></a>Fouten en waarschuwingen tijdens de installatie van de toepassings server

* Fout bij het uitvoeren van SAPinst stap: getProfileDir
  * FOUT: (laatste fout gemeld door de stap: gevangen ESAPinstException in module aanroep: validator van stap | NW_DI | ind | ind | ind | ind | 0 | 0 | NW_GetSidFromProfiles | ind | ind | ind | ind | getSid | 0 | NW_readProfileDir | ind | ind | ind | ind | readProfile | 0 | getProfileDir ' heeft een fout gerapporteerd: er \\ bestaat geen knoop punt \as1-ascs\sapmnt\AS1\SYS\profile. SAPinst in de interactieve modus starten om dit probleem op te lossen)
  * Oplossing  
    Zorg ervoor dat SWPM wordt uitgevoerd met een gebruiker die toegang heeft tot het profiel. Deze gebruiker kan worden geconfigureerd in de installatie wizard voor de toepassings server

* Fout bij het uitvoeren van SAPinst stap: askUnicode
  * FOUT: (laatste fout gemeld door de stap: gevangen ESAPinstException in module aanroep: validator van stap | NW_DI | ind | ind | ind | ind | 0 | 0 | NW_GetSidFromProfiles | ind | ind | ind | ind | getSid | 0 | NW_getUnicode | ind | ind | ind | Unicode | 0 | askUnicode ' heeft een fout gerapporteerd: start SAPinst in de interactieve modus om dit probleem op te lossen)
  * Oplossing  
    Als u een recente SAP-kernel gebruikt, kan SWPM niet bepalen of het systeem meer dan een Unicode-systeem is met behulp van de bericht server van de ASCS. Zie SAP Note [2445033] voor meer informatie.  
    Dit probleem wordt opgelost in een nieuw ondersteunings pakket/patch van SAP LaMa.  
    Stel de profiel parameter OS_UNICODE = UC in het standaard Profiel van uw SAP-systeem in om dit probleem te omzeilen.

* Fout bij het uitvoeren van SAPinst stap: dCheckGivenServer
  * Fout bij het uitvoeren van SAPinst-stap: dCheckGivenServer ' version = ' 1.0 ' fout: (laatste fout gemeld door de stap: de \<p> installatie is geannuleerd door de gebruiker. \</p>
  * Oplossing  
    Zorg ervoor dat SWPM wordt uitgevoerd met een gebruiker die toegang heeft tot het profiel. Deze gebruiker kan worden geconfigureerd in de installatie wizard voor de toepassings server

* Fout bij het uitvoeren van SAPinst stap: checkClient
  * Fout bij het uitvoeren van SAPinst-stap: checkClient ' version = ' 1.0 ' fout: (laatste fout gemeld door de stap: de \<p> installatie is geannuleerd door de gebruiker. \</p>)
  * Oplossing  
    Zorg ervoor dat het micro soft ODBC-stuur programma voor SQL Server is geïnstalleerd op de virtuele machine waarop u de toepassings server wilt installeren

* Fout bij het uitvoeren van SAPinst stap: copyScripts
  * Laatste fout gemeld door de stap: systeem oproep is mislukt. DETAILS: fout 13 (0x0000000d) (toestemming geweigerd) bij de uitvoering van systeem aanroep ' fopenU ' met para meter ( \\ \ AS1-ascs/sapmnt/AS1/sys/exe/UC/NTAMD64/strdbs. cmd, w), regel (494) in bestand (\ bas/bas/749_REL/bc_749_REL/src/ins/sapinst/impl/src/syslib/filesystem/syxxcfstrm2.cpp), Stack tracering:  
  CThrThread. cpp: 85: CThrThread:: threadFunction ()  
  CSiServiceSet. cpp: 63: CSiServiceSet:: executeService ()  
  CSiStepExecute. cpp: 913: CSiStepExecute:: Execute ()  
  EJSController. cpp: 179: EJSControllerImpl:: executeScript ()  
  JSExtension. hpp: 1136: CallFunctionBase:: Call ()  
  iaxxcfile. cpp: 183: iastring CIaOsFileConnect:: callMemberFunction (iastring Const& naam, args_t Const& argumenten)  
  iaxxcfile. cpp: 1849: iastring CIaOsFileConnect:: newFileStream (args_t Const& _args)  
  iaxxbfile. cpp: 773: CIaOsFile:: newFileStream_impl (4)  
  syxxcfile. cpp: 233: CSyFileImpl:: openstream (ISyFile:: eFileOpenMode)  
  syxxcfstrm. cpp: 29: CSyFileStreamImpl:: CSyFileStreamImpl (CSyFileStream *, iastring, ISyFile:: eFileOpenMode)  
  syxxcfstrm. cpp: 265: CSyFileStreamImpl:: Open ()  
  syxxcfstrm2. cpp: 58: CSyFileStream2Impl:: CSyFileStream2Impl (const CSyPath & \\ \ AW1-ascs/sapmnt/AW1/sys/exe/UC/NTAMD64/strdbs. cmd, 0x4)  
  syxxcfstrm2. cpp: 456: CSyFileStream2Impl:: Open ()
  * Oplossing  
    Zorg ervoor dat SWPM wordt uitgevoerd met een gebruiker die toegang heeft tot het profiel. Deze gebruiker kan worden geconfigureerd in de installatie wizard voor de toepassings server

* Fout bij het uitvoeren van SAPinst stap: askPasswords
  * Laatste fout gemeld door de stap: systeem oproep is mislukt. DETAILS: fout 5 (0x00000005) (de toegang is geweigerd.) bij de uitvoering van de systeem aanroep ' NetValidatePasswordPolicy ' met de para meter (...), regel (359) in het bestand (\ bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), Stack tracering:  
  CThrThread. cpp: 85: CThrThread:: threadFunction ()  
  CSiServiceSet. cpp: 63: CSiServiceSet:: executeService ()  
  CSiStepExecute. cpp: 913: CSiStepExecute:: Execute ()  
  EJSController. cpp: 179: EJSControllerImpl:: executeScript ()  
  JSExtension. hpp: 1136: CallFunctionBase:: Call ()  
  CSiStepExecute. cpp: 764: CSiStepExecute:: invokeDialog ()  
  DarkModeGuiEngine. cpp: 56: DarkModeGuiEngine:: showDialogCalledByJs ()  
  DarkModeDialog. cpp: 85: DarkModeDialog:: Submit ()  
  EJSController. cpp: 179: EJSControllerImpl:: executeScript ()  
  JSExtension. hpp: 1136: CallFunctionBase:: Call ()  
  iaxxcaccount. cpp: 107: iastring CIaOsAccountConnect:: callMemberFunction (iastring Const& naam, args_t Const& argumenten)  
  iaxxcaccount. cpp: 1186: iastring CIaOsAccountConnect:: validatePasswordPolicy (args_t Const& _args)  
  iaxxbaccount. cpp: 430: CIaOsAccount:: validatePasswordPolicy_impl ()  
  synxcaccmg. cpp: 297: ISyAccountMgt::P asswordValidationMessage CSyAccountMgtImpl:: validatePasswordPolicy (saponazure, * * * * *) const)
  * Oplossing  
    Zorg ervoor dat u in stap *isolatie* een host-regel toevoegt om communicatie van de virtuele machine naar de domein controller toe te staan

## <a name="next-steps"></a>Volgende stappen
* [Bedieningsgids voor SAP HANA op Azure][hana-ops-guide]
* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
