---
title: Over herstel na nood gevallen voor on-premises apps met Azure Site Recovery
description: Hierin worden de workloads beschreven die met behulp van herstel na noodgevallen kunnen worden beveiligd met de service Azure Site Recovery.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 2b901425a0020c0ccc7b834ee36d965910028018
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80062843"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>Over herstel na noodgevallen voor on-premises apps

In dit artikel worden on-premises workloads en apps beschreven die u kunt beveiligen voor herstel na nood gevallen met de [Azure site Recovery](site-recovery-overview.md) -service.

## <a name="overview"></a>Overzicht

Organisaties hebben een strategie voor bedrijfs continuïteit en nood herstel (BCDR) nodig om workloads en gegevens veilig en beschikbaar te blijven tijdens geplande en ongeplande uitval. En herstellen naar normale arbeids voorwaarden.

Site Recovery is een Azure-service die deel uitmaakt van uw BCDR-strategie. Met Site Recovery kunt u de toepassingsbewuste replicatie naar de cloud of naar een secundaire site implementeren. U kunt Site Recovery gebruiken voor het beheren van replicatie, het testen van nood herstel uitvoeren en failovers en failback uitvoeren. Uw apps kunnen worden uitgevoerd op Windows-of Linux-computers, fysieke servers, VMware of Hyper-V.

Site Recovery integreert met micro soft-toepassingen, zoals share point, Exchange, Dynamics, SQL Server en Active Directory. Micro soft werkt nauw samen met toonaangevende leveranciers zoals Oracle, SAP en Red Hat. U kunt replicatie-oplossingen per app aanpassen.

## <a name="why-use-site-recovery-for-application-replication"></a>Waarom u Site Recovery moet gebruiken voor het repliceren van toepassingen

Site Recovery biedt als volgt beveiliging op toepassingsniveau en herstel:

- App-neutraal en biedt replicatie voor alle workloads die worden uitgevoerd op een ondersteunde computer.
- Bijna-synchrone replicatie met herstel punt doelstellingen (RPO) die 30 seconden lang zijn om te voldoen aan de behoeften van de meest essentiële zakelijke apps.
- Toepassingsconsistente momentopnamen voor toepassingen met één of meer lagen.
- Integratie met SQL Server AlwaysOn en samen werking met andere replicatie technologieën op toepassings niveau. Bijvoorbeeld Active Directory replicatie, SQL AlwaysOn en beschikbaarheids groepen van Exchange-data bases (Dag's).
- Flexibele herstel plannen waarmee u een volledige toepassings stack kunt herstellen met één klik en voor het toevoegen van externe scripts en hand matige acties in het plan.
- Geavanceerd netwerk beheer in Site Recovery en Azure om de vereisten van het app-netwerk te vereenvoudigen. Netwerk beheer, zoals de mogelijkheid om IP-adressen te reserveren, taak verdeling te configureren en integratie met Azure Traffic Manager voor een lage herstel tijd (RTO) netwerk overschakelingen.
- Een uitgebreide Automation-bibliotheek met toepassingsspecifieke scripts die klaar zijn voor gebruik, en kunnen worden gedownload en geïntegreerd met herstelplannen.

## <a name="workload-summary"></a>Workloadoverzicht

Met Site Recovery kan elke app die wordt uitgevoerd op een ondersteunde machine, worden gerepliceerd. We hebben een partnerschap met product teams gedaan om extra tests uit te voeren voor de apps die zijn opgegeven in de volgende tabel.

| **Workload** |**Virtuele Azure-machines repliceren naar Azure** |**Virtuele Hyper-V-machines repliceren naar een secundaire site** | **Virtuele Hyper-V-machines repliceren naar Azure** | **Virtuele VMware-machines repliceren naar een secundaire site** | **Virtuele VMware-machines repliceren naar Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |Ja |Ja |Ja |Ja |Ja|
| Web-apps (IIS, SQL) |Ja |Ja |Ja |Ja |Ja|
| System Center Operations Manager |Ja |Ja |Ja |Ja |Ja|
| SharePoint |Ja |Ja |Ja |Ja |Ja|
| SAP<br/><br/>SAP-site repliceren naar Azure voor niet-cluster |Ja (getest door micro soft) |Ja (getest door micro soft) |Ja (getest door micro soft) |Ja (getest door micro soft) |Ja (getest door micro soft)|
| Exchange (niet-DAG) |Ja |Ja |Ja |Ja |Ja|
| Extern bureaublad/VDI |Ja |Ja |Ja |Ja |Ja|
| Linux (besturingssysteem en apps) |Ja (getest door micro soft) |Ja (getest door micro soft) |Ja (getest door micro soft) |Ja (getest door micro soft) |Ja (getest door micro soft)|
| Dynamics AX |Ja |Ja |Ja |Ja |Ja|
| Windows-bestandsserver |Ja |Ja |Ja |Ja |Ja|
| Citrix XenApp en XenDesktop |Ja|N.v.t. |Ja |N.v.t. |Ja |

## <a name="replicate-active-directory-and-dns"></a>Active Directory en DNS repliceren

Voor de meeste zakelijke apps zijn een Active Directory- en DNS-infrastructuur essentieel. Tijdens het herstel na nood gevallen moet u deze infrastructuur onderdelen beveiligen en herstellen voordat u workloads en apps herstelt.

U kunt Site Recovery gebruiken om een plan voor volledig geautomatiseerd noodherstel te maken voor Active Directory en DNS. Als u bijvoorbeeld een failover wilt uitvoeren voor share point en SAP van een primaire naar een secundaire site, kunt u een herstel plan instellen dat voor het eerst een failover van Active Directory heeft. Gebruik vervolgens een extra app-specifiek herstel plan voor failover van de andere apps die afhankelijk zijn van Active Directory.

Meer [informatie](site-recovery-active-directory.md) over herstel na nood gevallen voor Active Directory en DNS.

## <a name="protect-sql-server"></a>SQL Server beveiligen

SQL Server biedt een Data Services-basis voor veel zakelijke apps in een on-premises Data Center. Site Recovery kan met SQL Server HA/DR-technologieën worden gebruikt om zakelijke apps met meerdere lagen te beveiligen die gebruikmaken van SQL Server.

Site Recovery biedt:

- Een eenvoudige en voordelige oplossing voor herstel na noodgevallen voor SQL Server. Replicatie van meerdere versies en edities van zelfstandige SQL Server-servers en -clusters naar Azure of een secundaire site.
- Integratie met SQL AlwaysOn-beschikbaarheidsgroepen voor het beheren van failovers en failbacks met Azure Site Recovery-herstelplannen.
- End-to-endherstelplannen voor alle lagen in een toepassing, met inbegrip van de SQL Server-databases.
- Het schalen van SQL Server voor piek belasting met Site Recovery, door _ze te_ splitsen in grotere IaaS-VM-grootten in Azure.
- Eenvoudig testen van SQL Server-noodherstel. U kunt testfailovers uitvoeren om gegevens te analyseren en om nalevingscontroles uit te voeren zonder dat dit invloed heeft op de productieomgeving.

Meer [informatie](site-recovery-sql.md) over herstel na nood gevallen voor SQL Server.

## <a name="protect-sharepoint"></a>SharePoint beveiligen

Met Azure Site Recovery kunnen SharePoint-implementaties als volgt worden beveiligd:

- Er is geen stand-byfarm voor noodherstel meer nodig, zodat u ook geen kosten meer maakt voor de bijbehorende infrastructuur. Gebruik Site Recovery om een volledige Farm (Web-, app-en database lagen) te repliceren naar Azure of naar een secundaire site.
- Site Recovery vereenvoudigt het beheer en de implementatie van toepassingen. Updates die zijn geïmplementeerd op de primaire site worden automatisch gerepliceerd. De updates zijn beschikbaar na de failover en het herstel van een farm in een secundaire site. Verlaagt de complexiteit en kosten van beheer die zijn gekoppeld aan het actueel houden van een stand-by-Farm up-to-date.
- Site Recovery vereenvoudigt het ontwikkelen en testen van SharePoint-toepassingen, omdat er een op de productieomgeving lijkende replica-omgeving wordt gemaakt, die naar wens kan worden gebruikt voor testen en foutopsporing.
- Door Site Recovery te gebruiken om SharePoint-implementaties te migreren naar Azure, wordt de overstap naar de cloud vereenvoudigd.

Meer [informatie](site-recovery-sharepoint.md) over herstel na nood gevallen voor share point.

## <a name="protect-dynamics-ax"></a>Dynamics AX beveiligen

Met Azure Site Recovery kunt u op de volgende manier uw Dynamics AX ERP-oplossing beveiligen:

- Het beheren van de replicatie van uw volledige Dynamics AX-omgeving (web-en AOS-lagen, database lagen, share point) naar Azure of een secundaire site.
- Site Recovery vereenvoudigt de migratie van Dynamics AX-implementaties naar de cloud (Azure).
- Site Recovery vereenvoudigt de ontwikkeling en het testen van Dynamics AX-toepassingen door een op de productieomgeving lijkende replica-omgeving te bieden, zodat u naar wens kunt testen en fouten kunt opsporen.

[Meer informatie](site-recovery-dynamicsax.md) over herstel na nood gevallen voor dynamische AX.

## <a name="protect-remote-desktop-services"></a>Extern bureaublad-services beveiligen

Extern bureaublad-services (RDS) maakt VDI (Virtual Desktop Infrastructure), op sessies gebaseerde Bureau bladen en toepassingen mogelijk, waarmee gebruikers overal kunnen werken.

Met Azure Site Recovery kunt u de volgende services repliceren:

- Beheerde of niet-beheerde gegroepeerde virtuele Bureau bladen repliceren naar een secundaire site.
- Externe toepassingen en sessies repliceren naar een secundaire site of Azure.

In de volgende tabel worden de replicatie opties weer gegeven:

| **RDS** |**Virtuele Azure-machines repliceren naar Azure** | **Virtuele Hyper-V-machines repliceren naar een secundaire site** | **Virtuele Hyper-V-machines repliceren naar Azure** | **Virtuele VMware-machines repliceren naar een secundaire site** | **Virtuele VMware-machines repliceren naar Azure** | **Fysieke servers repliceren naar een secundaire site** | **Fysieke servers repliceren naar Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Gepoold virtueel bureaublad (onbeheerd)** |Nee|Ja |Nee |Ja |Nee |Ja |Nee |
| **Gepoold virtueel bureaublad (beheerd en zonder UDP)** |Nee|Ja |Nee |Ja |Nee |Ja |Nee |
| **Externe toepassingen en bureaubladsessies (zonder UDP)** |Ja|Ja |Ja |Ja |Ja |Ja |Ja |

Meer [informatie](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) over herstel na nood gevallen voor RDS.

## <a name="protect-exchange"></a>Exchange beveiligen

Met Site Recovery wordt Exchange als volgt beveiligd:

- Bij kleine implementaties van Exchange, met één server of met zelfstandige servers, kan Site Recovery een replicatie verzorgen en een failover uitvoeren naar Azure of een secundaire site.
- Bij grotere implementaties kan Site Recovery worden geïntegreerd met Exchange DAG’s.
- Exchange DAG's zijn de aanbevolen oplossing voor Exchange-noodherstel binnen grote bedrijven.  De Site Recovery-herstelplannen kunnen DAG’s bevatten, zodat er DAG-failovers kunnen worden uitgevoerd tussen verschillende sites.

Zie [Exchange dag's](/Exchange/high-availability/database-availability-groups/database-availability-groups) en [Exchange-nood herstel](/Exchange/high-availability/disaster-recovery/disaster-recovery)voor meer informatie over herstel na nood gevallen voor Exchange.

## <a name="protect-sap"></a>SAP beveiligen

Gebruik Site Recovery om uw SAP-implementatie als volgt te beveiligen:

- Schakel beveiliging van SAP NetWeaver en niet-NetWeaver-productietoepassingen die on-premises worden uitgevoerd in door onderdelen naar Azure te repliceren.
- Schakel beveiliging van SAP NetWeaver en niet-NetWeaver-productietoepassingen die in Azure worden uitgevoerd in door onderdelen naar een ander Azure-datacenter te repliceren.
- Vereenvoudig de cloudmigratie door Site Recovery te gebruiken om uw SAP-implementatie te migreren naar Azure.
- Vereenvoudig SAP-projectupgrades, tests en het maken van prototypen door een on-demand een productiekloon te maken voor het testen van SAP-toepassingen.

Meer [informatie](site-recovery-sap.md) over herstel na nood gevallen voor SAP.

## <a name="protect-internet-information-services"></a>Internet Information Services beveiligen

Gebruik Site Recovery om uw Internet Information Services (IIS)-implementatie als volgt te beveiligen:

Azure Site Recovery biedt herstel na noodgevallen door de belangrijke onderdelen in uw omgeving te repliceren naar een koude externe site of een openbare cloud, zoals Microsoft Azure. Omdat de virtuele machines met de webserver en de Data Base worden gerepliceerd naar de herstel site, is er geen afzonderlijke back-up voor configuratie bestanden of certificaten. De toepassingstoewijzingen en -bindingen die afhankelijk zijn van omgevingsvariabelen die na failover zijn gewijzigd, kunnen worden bijgewerkt via scripts die zijn geïntegreerd in de plannen voor herstel na noodgevallen. Virtuele machines worden alleen tijdens een failover op de herstel site gebracht. Azure Site Recovery helpt u ook de end-to-end failover te organiseren door de volgende mogelijkheden te bieden:

- Sequentiëring van het afsluiten en opstarten van virtuele machines in de verschillende categorieën.
- Het toevoegen van scripts voor het toestaan van updates van toepassings afhankelijkheden en bindingen op de virtuele machines nadat deze zijn gestart. De scripts kunnen ook worden gebruikt om de DNS-server zo bij te werken dat deze naar de herstelsite wijst.
- Wijs IP-adressen toe aan virtuele machines vóór de failover door de primaire en herstel netwerken toe te wijzen en scripts te gebruiken die na failover niet hoeven te worden bijgewerkt.
- De mogelijkheid voor een failover met één klik voor meerdere webtoepassingen die het bereik voor Verwar ring tijdens een nood geval elimineren.
- De mogelijkheid om de herstelplannen in een geïsoleerde omgeving te testen voor details voor DR.

Meer [informatie](site-recovery-iis.md) over herstel na nood gevallen voor IIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Citrix XenApp en XenDesktop beveiligen

Gebruik Site Recovery om de Citrix XenApp- en XenDesktop-implementaties te beveiligen. Dit werkt als volgt:

- Schakel de beveiliging van de Citrix XenApp-en XenDesktop-implementatie in. Repliceer de verschillende implementatie lagen naar Azure: Active Directory, DNS-server, SQL database server, Citrix Delivery controller, winkel server, XenApp Master (VDA), Citrix XenApp-licentie server.
- Vereenvoudig de cloudmigratie door Site Recovery te gebruiken om de Citrix XenApp- en XenDesktop-implementaties te migreren naar Azure.
- Vereenvoudig Citrix XenApp-/XenDesktop-tests door een op de productieomgeving lijkende replica-omgeving op aanvraag te maken voor tests en foutopsporing.
- Deze oplossing is alleen van toepassing op virtuele Windows Server-Bureau bladen en niet op virtuele Bureau bladen van de client. Virtuele Bureau bladen van de client worden nog niet ondersteund voor licentie verlening in Azure. [Meer informatie](https://azure.microsoft.com/pricing/licensing-faq/) over licentieverlening voor clientbureaubladen en serverdesktops in Azure.

Meer [informatie](site-recovery-citrix-xenapp-and-xendesktop.md) over herstel na nood gevallen voor Citrix XenApp-en XenDesktop-implementaties. U kunt ook verwijzen naar het technisch document van [Citrix](https://aka.ms/citrix-xenapp-xendesktop-with-asr).

## <a name="next-steps"></a>Volgende stappen

Meer [informatie](azure-to-azure-quickstart.md) over herstel na nood gevallen voor een Azure VM.
