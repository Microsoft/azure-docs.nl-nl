---
title: Over herstel na noodherstel voor on-premises apps met Azure Site Recovery
description: Hierin worden de workloads beschreven die met behulp van herstel na noodgevallen kunnen worden beveiligd met de service Azure Site Recovery.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 1a5d20e6feacfe72052142c07dc45753b9bc3138
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599108"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>Over herstel na noodgevallen voor on-premises apps

In dit artikel worden on-premises workloads en apps [](site-recovery-overview.md) beschreven die u kunt beveiligen voor noodherstel met de Azure Site Recovery service.

## <a name="overview"></a>Overzicht

Organisaties hebben een BCDR-strategie (strategie voor bedrijfscontinuïteit en noodherstel) nodig om workloads en gegevens veilig en beschikbaar te houden tijdens geplande en ongeplande downtime. En herstellen naar normale werkomstandigheden.

Site Recovery is een Azure-service die deel uitmaakt van uw BCDR-strategie. Met Site Recovery kunt u de toepassingsbewuste replicatie naar de cloud of naar een secundaire site implementeren. U kunt deze Site Recovery om replicatie te beheren, noodhersteltests uit te voeren en failovers en failbacks uit te voeren. Uw apps kunnen worden uitgevoerd op Windows- of Linux-computers, fysieke servers, VMware of Hyper-V.

Site Recovery kan worden geïntegreerd met Microsoft-toepassingen zoals SharePoint, Exchange, Dynamics, SQL Server en Active Directory. Microsoft werkt nauw samen met toonaangevende leveranciers, waaronder Oracle, SAP en Red Hat. U kunt replicatie-oplossingen per app aanpassen.

## <a name="why-use-site-recovery-for-application-replication"></a>Waarom u Site Recovery moet gebruiken voor het repliceren van toepassingen

Site Recovery biedt als volgt beveiliging op toepassingsniveau en herstel:

- App-agnostisch en biedt replicatie voor alle workloads die worden uitgevoerd op een ondersteunde machine.
- Bijna-synchrone replicatie, met herstelpuntdoelstellingen (RPO) van maar 30 seconden om te voldoen aan de behoeften van de meest kritieke zakelijke apps.
- Toepassingsconsistente momentopnamen voor toepassingen met één of meer lagen.
- Integratie met SQL Server AlwaysOn en samenwerking met andere replicatietechnologieën op toepassingsniveau. Bijvoorbeeld Active Directory-replicatie, SQL AlwaysOn en Exchange Database Availability Groups (DAG's).
- Flexibele herstelplannen waarmee u een volledige toepassingsstack met één klik kunt herstellen en externe scripts en handmatige acties in het plan kunt opnemen.
- Geavanceerd netwerkbeheer in Site Recovery azure om de vereisten voor het app-netwerk te vereenvoudigen. Netwerkbeheer, zoals de mogelijkheid om IP-adressen te reserveren, taakverdeling te configureren en integratie met Azure Traffic Manager voor lage RTO-netwerks switchovers (Recovery Time Objectives).
- Een uitgebreide Automation-bibliotheek met toepassingsspecifieke scripts die klaar zijn voor gebruik, en kunnen worden gedownload en geïntegreerd met herstelplannen.

## <a name="workload-summary"></a>Workloadoverzicht

Met Site Recovery kan elke app die wordt uitgevoerd op een ondersteunde machine, worden gerepliceerd. We werken samen met productteams om aanvullende tests uit te voeren voor de apps die in de volgende tabel zijn opgegeven.

| **Workload** |**Virtuele Azure-machines repliceren naar Azure** |**Virtuele Hyper-V-machines repliceren naar een secundaire site** | **Virtuele Hyper-V-machines repliceren naar Azure** | **Virtuele VMware-machines repliceren naar een secundaire site** | **Virtuele VMware-VM's repliceren naar Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |Ja |Ja |Ja |Ja |Ja|
| Web-apps (IIS, SQL) |Ja |Ja |Ja |Ja |Ja|
| System Center Operations Manager |Ja |Ja |Ja |Ja |Ja|
| SharePoint |Ja |Ja |Ja |Ja |Ja|
| SAP<br/><br/>SAP-site repliceren naar Azure voor niet-cluster |Ja (getest door Microsoft) |Ja (getest door Microsoft) |Ja (getest door Microsoft) |Ja (getest door Microsoft) |Ja (getest door Microsoft)|
| Exchange (niet-DAG) |Ja |Ja |Ja |Ja |Ja|
| Extern bureaublad/VDI |Ja |Ja |Ja |Ja |Ja|
| Linux (besturingssysteem en apps) |Ja (getest door Microsoft) |Ja (getest door Microsoft) |Ja (getest door Microsoft) |Ja (getest door Microsoft) |Ja (getest door Microsoft)|
| Dynamics AX |Ja |Ja |Ja |Ja |Ja|
| Windows-bestandsserver |Ja |Ja |Ja |Ja |Ja|
| Citrix XenApp en XenDesktop |No|N.v.t. |No |N.v.t. |No |

## <a name="replicate-active-directory-and-dns"></a>Active Directory en DNS repliceren

Voor de meeste zakelijke apps zijn een Active Directory- en DNS-infrastructuur essentieel. Tijdens herstel na noodherstel moet u deze infrastructuuronderdelen beveiligen en herstellen voordat u workloads en apps herstelt.

U kunt Site Recovery gebruiken om een plan voor volledig geautomatiseerd noodherstel te maken voor Active Directory en DNS. Als u bijvoorbeeld een fail over SharePoint en SAP wilt van een primaire naar een secundaire site, kunt u een herstelplan instellen dat eerst een fail over Active Directory ondergaat. Gebruik vervolgens een extra app-specifiek herstelplan om een fail over te voeren voor de andere apps die afhankelijk zijn van Active Directory.

[Meer informatie over](site-recovery-active-directory.md) herstel na noodherstel voor Active Directory en DNS.

## <a name="protect-sql-server"></a>SQL Server beveiligen

SQL Server biedt een basis voor gegevensservices voor veel zakelijke apps in een on-premises datacenter. Site Recovery kunnen worden gebruikt met SQL Server HA/DR-technologieën om zakelijke apps met meerdere lagen te beveiligen die gebruikmaken van SQL Server.

Site Recovery biedt:

- Een eenvoudige en voordelige oplossing voor herstel na noodgevallen voor SQL Server. Replicatie van meerdere versies en edities van zelfstandige SQL Server-servers en -clusters naar Azure of een secundaire site.
- Integratie met SQL AlwaysOn-beschikbaarheidsgroepen voor het beheren van failovers en failbacks met Azure Site Recovery-herstelplannen.
- End-to-endherstelplannen voor alle lagen in een toepassing, met inbegrip van de SQL Server-databases.
- Het schalen van SQL Server voor piekbelastingen met  Site Recovery, door ze uit te bursten in grotere virtuele IaaS-machines in Azure.
- Eenvoudig testen van SQL Server-noodherstel. U kunt testfailovers uitvoeren om gegevens te analyseren en om nalevingscontroles uit te voeren zonder dat dit invloed heeft op de productieomgeving.

[Meer informatie over](site-recovery-sql.md) herstel na noodherstel voor SQL Server.

## <a name="protect-sharepoint"></a>SharePoint beveiligen

Met Azure Site Recovery kunnen SharePoint-implementaties als volgt worden beveiligd:

- Er is geen stand-byfarm voor noodherstel meer nodig, zodat u ook geen kosten meer maakt voor de bijbehorende infrastructuur. Gebruik Site Recovery om een hele farm (web-, app- en databaselagen) te repliceren naar Azure of naar een secundaire site.
- Site Recovery vereenvoudigt het beheer en de implementatie van toepassingen. Updates die op de primaire site worden geïmplementeerd, worden automatisch gerepliceerd. De updates zijn beschikbaar na failover en herstel van een farm op een secundaire site. Verlaagt de complexiteit van het beheer en de kosten die gepaard gaan met het up-to-date houden van een stand-byfarm.
- Site Recovery vereenvoudigt het ontwikkelen en testen van SharePoint-toepassingen, omdat er een op de productieomgeving lijkende replica-omgeving wordt gemaakt, die naar wens kan worden gebruikt voor testen en foutopsporing.
- Door Site Recovery te gebruiken om SharePoint-implementaties te migreren naar Azure, wordt de overstap naar de cloud vereenvoudigd.

[Meer informatie over](site-recovery-sharepoint.md) herstel na noodherstel voor SharePoint.

## <a name="protect-dynamics-ax"></a>Dynamics AX beveiligen

Met Azure Site Recovery kunt u op de volgende manier uw Dynamics AX ERP-oplossing beveiligen:

- Replicatie van uw gehele Dynamics AX-omgeving beheren (web- en AOS-lagen, databaselagen, SharePoint) naar Azure of naar een secundaire site.
- Site Recovery vereenvoudigt de migratie van Dynamics AX-implementaties naar de cloud (Azure).
- Site Recovery vereenvoudigt de ontwikkeling en het testen van Dynamics AX-toepassingen door een op de productieomgeving lijkende replica-omgeving te bieden, zodat u naar wens kunt testen en fouten kunt opsporen.

[Meer informatie over](site-recovery-dynamicsax.md) herstel na noodherstel voor Dynamic AX.

## <a name="protect-remote-desktop-services"></a>Uw Extern bureaublad-services

Extern bureaublad-services (RDS) maakt VDI (Virtual Desktop Infrastructure), sessiegebaseerde bureaubladen en toepassingen mogelijk, zodat gebruikers overal kunnen werken.

Met Azure Site Recovery kunt u de volgende services repliceren:

- Beheerde of niet-beheerde pool-virtuele bureaubladen repliceren naar een secundaire site.
- Externe toepassingen en sessies repliceren naar een secundaire site of Azure.

In de volgende tabel ziet u de replicatieopties:

| **RDS** |**Virtuele Azure-machines repliceren naar Azure** | **Virtuele Hyper-V-machines repliceren naar een secundaire site** | **Virtuele Hyper-V-machines repliceren naar Azure** | **Virtuele VMware-machines repliceren naar een secundaire site** | **Virtuele VMware-VM's repliceren naar Azure** | **Fysieke servers repliceren naar een secundaire site** | **Fysieke servers repliceren naar Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Gepoold virtueel bureaublad (onbeheerd)** |Nee|Ja |Nee |Ja |Nee |Ja |Nee |
| **Gepoold virtueel bureaublad (beheerd en zonder UDP)** |Nee|Ja |Nee |Ja |Nee |Ja |Nee |
| **Externe toepassingen en bureaubladsessies (zonder UDP)** |Ja|Ja |Ja |Ja |Ja |Ja |Ja |

[Meer informatie over](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) herstel na noodherstel voor RDS.

## <a name="protect-exchange"></a>Exchange beveiligen

Met Site Recovery wordt Exchange als volgt beveiligd:

- Bij kleine implementaties van Exchange, met één server of met zelfstandige servers, kan Site Recovery een replicatie verzorgen en een failover uitvoeren naar Azure of een secundaire site.
- Bij grotere implementaties kan Site Recovery worden geïntegreerd met Exchange DAG’s.
- Exchange DAG's zijn de aanbevolen oplossing voor Exchange-noodherstel binnen grote bedrijven.  De Site Recovery-herstelplannen kunnen DAG’s bevatten, zodat er DAG-failovers kunnen worden uitgevoerd tussen verschillende sites.

Zie Exchange-DAG's en Exchange-noodherstel voor meer informatie over herstel na [noodherstel voor](/Exchange/high-availability/disaster-recovery/disaster-recovery) [Exchange.](/Exchange/high-availability/database-availability-groups/database-availability-groups)

## <a name="protect-sap"></a>SAP beveiligen

Gebruik Site Recovery om uw SAP-implementatie als volgt te beveiligen:

- Schakel beveiliging van SAP NetWeaver en niet-NetWeaver-productietoepassingen die on-premises worden uitgevoerd in door onderdelen naar Azure te repliceren.
- Schakel beveiliging van SAP NetWeaver en niet-NetWeaver-productietoepassingen die in Azure worden uitgevoerd in door onderdelen naar een ander Azure-datacenter te repliceren.
- Vereenvoudig de cloudmigratie door Site Recovery te gebruiken om uw SAP-implementatie te migreren naar Azure.
- Vereenvoudig SAP-projectupgrades, tests en het maken van prototypen door een on-demand een productiekloon te maken voor het testen van SAP-toepassingen.

[Meer informatie over](site-recovery-sap.md) herstel na noodherstel voor SAP.

## <a name="protect-internet-information-services"></a>Uw Internet Information Services

Gebruik Site Recovery als volgt om uw Internet Information Services (IIS) te beveiligen:

Azure Site Recovery biedt herstel na noodgevallen door de belangrijke onderdelen in uw omgeving te repliceren naar een koude externe site of een openbare cloud, zoals Microsoft Azure. Omdat de virtuele machines met de webserver en de database worden gerepliceerd naar de herstelsite, is er geen afzonderlijke back-up vereist voor configuratiebestanden of certificaten. De toepassingstoewijzingen en -bindingen die afhankelijk zijn van omgevingsvariabelen die na failover zijn gewijzigd, kunnen worden bijgewerkt via scripts die zijn geïntegreerd in de plannen voor herstel na noodgevallen. Virtuele machines worden alleen tijdens een failover naar de herstelsite gebracht. Azure Site Recovery kunt u ook de end-to-end-failover ins manen door u de volgende mogelijkheden te bieden:

- Sequentiëring van het afsluiten en opstarten van virtuele machines in de verschillende categorieën.
- Scripts toevoegen om updates van toepassingsafhankelijkheden en bindingen toe te staan op de virtuele machines nadat deze zijn gestart. De scripts kunnen ook worden gebruikt om de DNS-server zo bij te werken dat deze naar de herstelsite wijst.
- Wijs IP-adressen toe aan virtuele machines vóór de failover door de primaire netwerken en herstelnetwerken toe te wijzen en scripts te gebruiken die na een failover niet hoeven te worden bijgewerkt.
- Mogelijkheid voor een failover met één klik voor meerdere webtoepassingen die de verwarring tijdens een noodlot weglaten.
- De mogelijkheid om de herstelplannen in een geïsoleerde omgeving te testen voor details voor DR.

[Meer informatie over](site-recovery-iis.md) herstel na noodherstel voor IIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Citrix XenApp en XenDesktop beveiligen

Vanaf maart 2020 heeft Citrix afschaffing en end-of-support aangekondigd voor workloads die in de openbare cloud worden gehost. Daarom raden we u niet aan om Site Recovery citrix-workloads te beveiligen.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](azure-to-azure-quickstart.md) herstel na noodherstel voor een Azure-VM.
