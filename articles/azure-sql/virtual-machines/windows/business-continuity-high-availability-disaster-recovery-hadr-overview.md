---
title: Hoge Beschik baarheid, herstel na nood gevallen, bedrijfs continuïteit
description: Meer informatie over de opties voor hoge Beschik baarheid, herstel na nood gevallen (HADR) en bedrijfs continuïteit die beschikbaar zijn voor SQL Server op virtuele machines van Azure, zoals AlwaysOn-beschikbaarheids groepen, het failover-cluster exemplaar, het spie gelen van de data base, het registreren van een logboek en back-up & Azure Storage.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2020
ms.author: mathoma
ms.openlocfilehash: 194c6a5cead400e1bac78ba42cb7238b64bd3b7b
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327471"
---
# <a name="business-continuity-and-hadr-for-sql-server-on-azure-virtual-machines"></a>Bedrijfs continuïteit en HADR voor SQL Server op Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Bedrijfs continuïteit betekent dat uw bedrijf wordt voortgezet in het geval van een ramp, het plannen van herstel en het garanderen dat uw gegevens Maxi maal beschikbaar zijn. Met SQL Server op Azure Virtual Machines kunt u de kosten van een Data Base-oplossing met hoge Beschik baarheid en herstel na nood gevallen (HADR) verlagen. 

De meeste SQL Server HADR-oplossingen worden ondersteund op virtuele machines (Vm's), zowel voor zowel Azure-als hybride oplossingen. In een alleen-Azure-oplossing wordt het hele HADR-systeem uitgevoerd in Azure. In een hybride configuratie wordt een deel van de oplossing uitgevoerd in Azure en wordt het andere onderdeel on-premises in uw organisatie uitgevoerd. Dankzij de flexibiliteit van de Azure-omgeving kunt u zich gedeeltelijk of volledig verplaatsen naar Azure om te voldoen aan de budget-en HADR vereisten van uw SQL Server-database systemen.

In dit artikel worden de oplossingen voor bedrijfs continuïteit vergeleken die beschikbaar zijn voor SQL Server op Azure-Vm's. 

## <a name="overview"></a>Overzicht

Het is aan u om ervoor te zorgen dat uw database systeem de HADR-mogelijkheden heeft die de Service Level Agreement (SLA) vereist. Het feit dat Azure voorziet in mechanismen voor hoge Beschik baarheid, zoals service retoucheert voor Cloud Services en detectie van fout herstel voor virtuele machines, garandeert niet dat u kunt voldoen aan de SLA. Hoewel deze mechanismen helpen de hoge Beschik baarheid van de virtuele machine te beveiligen, beveiligen ze de beschik baarheid van SQL Server die in de VM worden uitgevoerd, niet. 

Het is mogelijk dat het SQL Server-exemplaar mislukt als de virtuele machine online en in orde is. Zelfs de mechanismen voor hoge Beschik baarheid die door Azure worden ondersteund, staan de uitval van de Vm's toe vanwege gebeurtenissen zoals herstel van software-of hardwarestoringen en upgrades van besturings systemen.

Geo-redundante opslag (GRS) in azure wordt geïmplementeerd met een functie genaamd geo-replicatie. GRS is mogelijk geen geschikte oplossing voor herstel na nood gevallen voor uw data bases. Omdat geo-replicatie gegevens asynchroon verzendt, kunnen recente updates in een nood geval verloren gaan. Meer informatie over beperkingen van geo-replicatie wordt behandeld in het gedeelte [ondersteuning voor geo-replicatie](#geo-replication-support) .

## <a name="deployment-architectures"></a>Implementatie architecturen
Azure ondersteunt deze SQL Server technologieën voor bedrijfs continuïteit:

* [AlwaysOn-beschikbaarheids groepen](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Altijd op Failoverclusterinstanties (failover cluster instances)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
* [Logboek verzending](/sql/database-engine/log-shipping/about-log-shipping-sql-server)
* [Back-up en herstel SQL Server met Azure Blob Storage](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
* [Data Base mirroring](/sql/database-engine/database-mirroring/database-mirroring-sql-server) -afgeschaft in SQL Server 2016

U kunt de technologieën combi neren om een SQL Server oplossing te implementeren met mogelijkheden voor hoge Beschik baarheid en herstel na een nood geval. Afhankelijk van de technologie die u gebruikt, heeft een hybride implementatie mogelijk een VPN-tunnel met het virtuele Azure-netwerk nodig. In de volgende secties ziet u enkele voor beelden van implementatie architecturen.

## <a name="azure-only-high-availability-solutions"></a>Alleen Azure: oplossingen met hoge Beschik baarheid

U kunt een oplossing met hoge Beschik baarheid hebben voor SQL Server op database niveau met AlwaysOn-beschikbaarheids groepen. U kunt ook een oplossing met hoge Beschik baarheid maken op een instantie niveau met Always on failover cluster instances. Voor extra beveiliging kunt u redundantie op beide niveaus maken door beschikbaarheids groepen te maken op failover-cluster exemplaren. 

| Technologie | Voorbeeld architecturen |
| --- | --- |
| **Beschikbaarheidsgroepen** |Beschikbaarheids replica's die worden uitgevoerd in virtuele Azure-machines in dezelfde regio bieden een hoge Beschik baarheid. U moet een VM van een domein controller configureren, omdat Windows Failover Clustering een Active Directory domein vereist.<br/><br/> Voor hogere redundantie en beschik baarheid, kunnen de virtuele Azure-machines in verschillende [beschikbaarheids zones](../../../availability-zones/az-overview.md) worden geïmplementeerd, zoals wordt beschreven in het overzicht van de [beschikbaarheids groep](availability-group-overview.md). Als de SQL Server Vm's in een beschikbaarheids groep in beschikbaarheids zones worden geïmplementeerd, gebruikt u [Azure Standard Load Balancer](../../../load-balancer/load-balancer-overview.md) voor de listener, zoals beschreven in de artikelen [Azure SQL VM cli](./availability-group-az-commandline-configure.md) en [Azure Quick](availability-group-quickstart-template-configure.md) start-sjablonen.<br/> ![Diagram waarin de ' domein controller ' wordt weer gegeven boven het ' WSFC-cluster ' van de ' primaire replica ', ' secundaire replica ' en ' bestands share-witness '.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-ha-always-on.png)<br/>Zie [Availability groups configureren in azure (GUI)](./availability-group-quickstart-template-configure.md)voor meer informatie. |
| **Exemplaren van failoverclusters** |Failover-cluster exemplaren worden ondersteund op SQL Server Vm's. Omdat voor de functie FCI gedeelde opslag is vereist, zijn er vijf oplossingen mogelijk met SQL Server op Azure-Vm's: <br/><br/> - [Azure gedeelde schijven](failover-cluster-instance-azure-shared-disks-manually-configure.md) gebruiken voor Windows Server 2019. Gedeelde beheerde schijven zijn een Azure-product waarmee u een beheerde schijf gelijktijdig kunt koppelen aan meerdere virtuele machines. Vm's in het cluster kunnen lezen van of schrijven naar uw gekoppelde schijf op basis van de reserve ring die door de geclusterde toepassing is gekozen met behulp van SCSI-permanente reserve ringen (SCSI-PR). SCSI PR is een industrie-standaard oplossing voor opslag die wordt gebruikt door toepassingen die worden uitgevoerd op een on-premises Storage Area Network (SAN). Door SCSI-PR op een beheerde schijf in te scha kelen, kunt u deze toepassingen naar Azure migreren. <br/><br/>-Met behulp van [opslagruimten direct \( S2D \) ](failover-cluster-instance-storage-spaces-direct-manually-configure.md) kunt u een virtueel San op basis van software bieden voor Windows Server 2016 en hoger.<br/><br/>-Gebruik een [Premium-bestands share](failover-cluster-instance-premium-file-share-manually-configure.md) voor Windows Server 2012 en hoger. Premium-bestands shares zijn een SSD-back-up, hebben een consistente lage latentie en worden volledig ondersteund voor gebruik met FCI.<br/><br/>-Opslag gebruiken die wordt ondersteund door een partner oplossing voor clustering. Zie het blog bericht [failover clustering en SIOS data keeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/)voor een specifiek voor beeld dat gebruikmaakt van Sios data keeper.<br/><br/>-Gedeelde blok opslag gebruiken voor een extern iSCSI-doel via Azure ExpressRoute. NetApp Private Storage (NPS) biedt bijvoorbeeld een iSCSI-doel aan voor Azure-VM's via ExpressRoute met Equinix.<br/><br/>In het geval van oplossingen van Microsoft-partners voor gedeelde opslag en gegevensreplicatie neemt u contact op met de leverancier als er problemen zijn met de toegang tot gegevens bij failover.<br/><br/>||

## <a name="azure-only-disaster-recovery-solutions"></a>Alleen Azure: oplossingen voor nood herstel
U kunt een oplossing voor herstel na nood geval hebben voor uw SQL Server-data bases in azure met behulp van beschikbaarheids groepen, database spiegeling of back-up en herstel met opslag-blobs.

| Technologie | Voorbeeld architecturen |
| --- | --- |
| **Beschikbaarheidsgroepen** |Beschikbaarheids replica's die worden uitgevoerd op meerdere data centers in azure-Vm's voor nood herstel. Deze oplossing voor meerdere regio's helpt bij het beveiligen van een volledige site storing. <br/> ![Diagram met twee regio's met een ' primaire replica ' en ' secundaire replica ' die zijn verbonden met een ' asynchrone commit '.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-alwayson.png)<br/>Binnen een regio moeten alle replica's zich binnen dezelfde Cloud service en hetzelfde virtuele netwerk bevinden. Omdat elke regio een apart virtueel netwerk heeft, is voor deze oplossingen netwerk-naar-netwerk-verbinding vereist. Zie [een netwerk-naar-netwerk-verbinding configureren met behulp van de Azure Portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)voor meer informatie. Zie een SQL Server AlwaysOn- [beschikbaarheids groep configureren in verschillende Azure-regio's](availability-group-manually-configure-multiple-regions.md)voor gedetailleerde instructies.|
| **Database spiegeling** |Principal en mirror en servers die worden uitgevoerd in verschillende data centers voor herstel na nood gevallen. U moet deze implementeren met behulp van server certificaten. SQL Server Data Base mirroring wordt niet ondersteund voor SQL Server 2008 of SQL Server 2008 R2 op een virtuele machine van Azure. <br/>![Diagram waarin de ' principal ' wordt weer gegeven in een regio die is verbonden met de ' mirror in een andere regio met ' hoge prestaties '.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-dbmirroring.png) |
| **Back-ups maken en herstellen met Azure Blob Storage** |Productie databases maken rechtstreeks back-ups van Blob Storage in een ander Data Center voor herstel na nood gevallen.<br/>![Diagram waarin een ' data base ' wordt weer gegeven in één regio en waarvan een back-up wordt gemaakt naar ' Blob Storage ' in een andere regio.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-backup-restore.png)<br/>Zie [back-up en herstel voor SQL Server op Azure-vm's](../../../azure-sql/virtual-machines/windows/backup-restore.md)voor meer informatie. |
| **SQL Server repliceren en failover uitvoeren naar Azure met Azure Site Recovery** |De productie SQL Server-exemplaar in een Azure-Data Center wordt rechtstreeks gerepliceerd naar Azure Storage in een ander Azure-Data Center voor herstel na nood gevallen.<br/>![Diagram waarin een ' data base ' wordt weer gegeven in een Azure-Data Center met behulp van ' ASR-replicatie ' voor herstel na nood gevallen in een ander Data Center. ](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-standalone-sqlserver-asr.png)<br/>Zie [SQL Server beveiligen met SQL Server nood herstel en Azure site Recovery](../../../site-recovery/site-recovery-sql.md)voor meer informatie. |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybride IT: oplossingen voor nood herstel
U kunt een oplossing voor herstel na nood geval hebben voor uw SQL Server-data bases in een hybride IT-omgeving met behulp van beschikbaarheids groepen, database spiegeling, logboek verzending en back-up en herstel met Azure Blob Storage.

| Technologie | Voorbeeld architecturen |
| --- | --- |
| **Beschikbaarheidsgroepen** |Sommige beschikbaarheids replica's die worden uitgevoerd in azure-Vm's en andere replica's die on-premises actief zijn voor herstel na nood geval op meerdere locaties. De productie site kan on-premises of in een Azure-Data Center zijn.<br/>![Beschikbaarheidsgroepen](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-alwayson.png)<br/>Omdat alle beschikbaarheids replica's zich in hetzelfde failovercluster moeten bevinden, moet het cluster beide netwerken (een failover-cluster met meerdere subnetten) beslaan. Voor deze configuratie is een VPN-verbinding tussen Azure en het on-premises netwerk vereist.<br/><br/>Voor een succes volle herstel van uw data bases moet u ook een replica domein controller installeren op de site voor nood herstel.|
| **Database spiegeling** |Een partner die wordt uitgevoerd in een Azure-VM en de andere die on-premises wordt uitgevoerd voor herstel na nood geval met behulp van server certificaten. Partners hoeven zich niet in hetzelfde Active Directory domein te bekomen en er is geen VPN-verbinding vereist.<br/>![Database spiegeling](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-dbmirroring.png)<br/>Een ander scenario voor het spie gelen van data bases bestaat uit één partner die wordt uitgevoerd in een Azure-VM en de andere die on-premises wordt uitgevoerd in hetzelfde Active Directory domein voor nood herstel op meerdere locaties. Er is een [VPN-verbinding tussen het virtuele netwerk van Azure en het on-premises netwerk](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) vereist.<br/><br/>Voor een succes volle herstel van uw data bases moet u ook een replica domein controller installeren op de site voor nood herstel. SQL Server Data Base mirroring wordt niet ondersteund voor SQL Server 2008 of SQL Server 2008 R2 op een virtuele machine van Azure. |
| **Logboek verzending** |Een server die wordt uitgevoerd in een Azure-VM en de andere die on-premises wordt uitgevoerd voor nood herstel op meerdere locaties. Logboek verzending is afhankelijk van het delen van Windows-bestanden, dus een VPN-verbinding tussen het virtuele netwerk van Azure en het on-premises netwerk is vereist.<br/>![Back-upfunctie voor logboekbestanden](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-log-shipping.png)<br/>Voor een succes volle herstel van uw data bases moet u ook een replica domein controller installeren op de site voor nood herstel. |
| **Back-ups maken en herstellen met Azure Blob Storage** |On-premises productie databases maken rechtstreeks back-ups van Azure Blob-opslag voor herstel na nood gevallen.<br/>![Back-up en herstel](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-backup-restore.png)<br/>Zie [back-up en herstel voor SQL Server op Azure virtual machines](../../../azure-sql/virtual-machines/windows/backup-restore.md)voor meer informatie. |
| **SQL Server repliceren en failover uitvoeren naar Azure met Azure Site Recovery** |On-premises productie SQL Server exemplaar rechtstreeks gerepliceerd naar Azure Storage voor herstel na nood gevallen.<br/>![Repliceren met Azure Site Recovery](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-standalone-sqlserver-asr.png)<br/>Zie [SQL Server beveiligen met SQL Server nood herstel en Azure site Recovery](../../../site-recovery/site-recovery-sql.md)voor meer informatie. |


## <a name="free-dr-replica-in-azure"></a>Gratis DR-replica in azure

Als u [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)hebt, kunt u hybride plannen voor herstel na nood gevallen (Dr) implementeren met SQL Server zonder extra licentie kosten te betalen voor het passieve nood herstel exemplaar.

In de volgende afbeelding gebruikt de installatie SQL Server uitgevoerd op een virtuele machine van Azure die 12 kernen gebruikt als herstel replica voor nood gevallen voor een on-premises SQL Server implementatie die gebruikmaakt van 12 kernen. In het verleden moet u 12 kernen van SQL Server voor de on-premises implementatie en de implementatie van Azure Virtual Machines. Het nieuwe voor deel biedt voor delen van de passieve replica voor uitvoering op een virtuele machine van Azure. Nu moet u slechts 12 kernen van SQL Server uitvoeren op de on-premises computer, zolang er wordt voldaan aan de criteria voor herstel na nood gevallen voor de passieve replica op Azure Virtual Machines.

![Gratis replica voor herstel na nood gevallen in azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/free-dr-replica-azure.png)

Zie [Licentievoorwaarden voor het product](https://www.microsoft.com/licensing/product-licensing/products) voor meer informatie. 

Als u dit voor deel wilt inschakelen, gaat u naar de resource van uw [SQL Server virtuele machine](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). Selecteer **configureren** onder **instellingen** en kies vervolgens de optie voor **nood herstel** onder **SQL Server-licentie**. Schakel het selectie vakje in om te bevestigen dat deze SQL Server virtuele machine wordt gebruikt als passieve replica en selecteer vervolgens **Toep assen** om uw instellingen op te slaan. 

![Een herstel replica voor nood gevallen in azure configureren](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Belang rijke aandachtspunten voor SQL Server HADR in azure
Azure-Vm's,-opslag en-netwerken hebben verschillende operationele kenmerken dan een on-premises, niet-gevirtualiseerde IT-infra structuur. Een geslaagde implementatie van een HADR-SQL Server oplossing in azure vereist dat u deze verschillen begrijpt en uw oplossing ontwerpt om deze te kunnen ondersteunen.

### <a name="high-availability-nodes-in-an-availability-set"></a>Knoop punten met hoge Beschik baarheid in een beschikbaarheidsset
Met beschikbaarheids sets in azure kunt u de knoop punten met hoge Beschik baarheid plaatsen in afzonderlijke fout domeinen en update domeinen. Het Azure-platform wijst een update domein en een fout domein toe aan elke virtuele machine in uw beschikbaarheidsset. Deze configuratie binnen een Data Center zorgt ervoor dat tijdens een geplande of ongeplande onderhouds gebeurtenis ten minste één virtuele machine beschikbaar is en voldoet aan de Azure-SLA van 99,95 procent. 

Als u een instelling voor maximale Beschik baarheid wilt configureren, plaatst u alle deelnemende SQL Server virtuele machines in dezelfde beschikbaarheidsset om te voor komen dat toepassingen of gegevens verloren gaan tijdens een onderhouds gebeurtenis. Alleen knoop punten in dezelfde Cloud service kunnen deel nemen aan dezelfde beschikbaarheidsset. Zie voor meer informatie [De beschikbaarheid van virtuele machines beheren](../../../virtual-machines/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Knoop punten met hoge Beschik baarheid in een beschikbaarheids zone
Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer data centers die zijn uitgerust met onafhankelijke voeding, koeling en netwerken. De fysieke schei ding van beschikbaarheids zones binnen een regio helpt bij het beveiligen van toepassingen en gegevens van datacenter fouten door ervoor te zorgen dat er ten minste één virtuele machine beschikbaar is en voldoet aan de Azure-SLA van 99,99 procent. 

Als u maximale Beschik baarheid wilt configureren, plaatst u deelnemende SQL Server virtuele machines over de verschillende beschikbaarheids zones in de regio. Er worden extra kosten in rekening gebracht voor netwerk-naar-netwerk overdrachten tussen beschikbaarheids zones. Zie [beschikbaarheids zones](../../../availability-zones/az-overview.md)voor meer informatie. 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Gedrag van failover-clusters in azure-netwerken
De niet-RFC-compatibele DHCP-service in azure kan ertoe leiden dat het maken van bepaalde failover-cluster configuraties mislukt. Dit probleem treedt op omdat aan de naam van het cluster netwerk een dubbel IP-adres is toegewezen, zoals hetzelfde IP-adres als een van de cluster knooppunten. Dit is een probleem bij het gebruik van beschikbaarheids groepen, die afhankelijk zijn van de Windows-functie failover cluster.

Denk na over het scenario wanneer een cluster met twee knoop punten wordt gemaakt en online wordt gebracht:

1. Het cluster is online en vervolgens KNOOPPUNT1 een dynamisch toegewezen IP-adres voor de naam van het cluster netwerk.
2. De DHCP-service geeft geen ander IP-adres dan het eigen IP-adres van NODE1's, omdat de DHCP-service detecteert dat de aanvraag afkomstig is van KNOOPPUNT1 zelf.
3. Windows detecteert dat er een dubbel adres is toegewezen aan KNOOPPUNT1 en aan de netwerk naam van het failovercluster en dat de standaard cluster groep niet online kan worden gebracht.
4. De standaard cluster groep wordt verplaatst naar KNOOPPUNT2. KNOOPPUNT2 behandelt NODE1's IP-adres als het IP-adres van het cluster en brengt de standaard cluster groep online.
5. Wanneer KNOOPPUNT2 probeert verbinding te maken met KNOOPPUNT1, hoeven pakketten die zijn gericht op KNOOPPUNT1 nooit KNOOPPUNT2 uit te voeren, omdat het IP-adres van NODE1's wordt omgezet naar zichzelf. KNOOPPUNT2 kan geen verbinding maken met KNOOPPUNT1, waardoor quorum verloren gaat en het cluster wordt afgesloten.
6. KNOOPPUNT1 kan pakketten verzenden naar KNOOPPUNT2, maar KNOOPPUNT2 kan niet antwoorden. KNOOPPUNT1 verliest quorum en sluit het cluster af.

U kunt dit scenario voor komen door een niet-gebruikt statisch IP-adres toe te wijzen aan de naam van het cluster netwerk om de cluster netwerk naam online te brengen. U kunt bijvoorbeeld een link-local IP-adres gebruiken, zoals 169.254.1.1. Zie [Windows-failovercluster configureren in azure voor beschikbaarheids groepen](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx)om dit proces te vereenvoudigen.

Zie [Availability groups configureren in azure (GUI)](./availability-group-quickstart-template-configure.md)voor meer informatie.

### <a name="support-for-availability-group-listeners"></a>Ondersteuning voor listeners voor beschikbaarheids groep
Listeners voor beschikbaarheids groep worden ondersteund op virtuele Azure-machines met Windows Server 2012 en hoger. Deze ondersteuning wordt mogelijk gemaakt door het gebruik van eind punten met taak verdeling die zijn ingeschakeld op de virtuele Azure-machines die de knoop punten van de beschikbaarheids groep zijn. U moet speciale configuratie stappen uitvoeren om de listeners te laten werken voor beide client toepassingen die worden uitgevoerd in Azure en die on-premises worden uitgevoerd.

Er zijn twee belang rijke opties voor het instellen van uw listener: extern (openbaar) of intern. De externe (open bare) listener maakt gebruik van een Internet gerichte load balancer en is gekoppeld aan een openbaar virtueel IP-adres dat toegankelijk is via internet. Een interne listener maakt gebruik van een interne load balancer en ondersteunt alleen clients binnen hetzelfde virtuele netwerk. Voor beide load balancer type moet u direct server return inschakelen. 

Als de beschikbaarheids groep meerdere Azure-subnetten omvat (zoals een implementatie die Azure-regio's kruist), moet de Client connection string bevatten `MultisubnetFailover=True` . Dit resulteert in een parallelle verbinding met de replica's in de verschillende subnetten. Zie [een ILB-listener configureren voor beschikbaarheids groepen in azure](availability-group-listener-powershell-configure.md)voor instructies voor het instellen van een listener.


U kunt nog steeds afzonderlijk verbinding maken met elke beschikbaarheids replica door rechtstreeks verbinding te maken met het service-exemplaar. Omdat beschikbaarheids groepen achterwaarts compatibel zijn met data base mirroring-clients, kunt u ook verbinding maken met de beschikbaarheids replica's als database spiegeling, zolang de replica's zo zijn geconfigureerd dat ze op een vergelijk bare manier worden gespiegeld:

* Er is één primaire replica en één secundaire replica.
* De secundaire replica is geconfigureerd als niet-lees bare,**secundaire** optie ingesteld op **Nee**.

Hier volgt een voor beeld-Client connection string die overeenkomt met deze gespiegelde configuratie van de data base met behulp van ADO.NET of SQL Server Native Client:

```console
Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;
```

Zie voor meer informatie over client connectiviteit:

* [Tref woorden voor de verbindings reeks gebruiken met SQL Server Native Client](/sql/relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client)
* [Clients verbinden met een sessie voor het spie gelen van data bases (SQL Server)](/sql/database-engine/database-mirroring/connect-clients-to-a-database-mirroring-session-sql-server)
* [Verbinding maken met de beschikbaarheids groep-listener in hybride IT](/archive/blogs/sqlalwayson/connecting-to-availability-group-listener-in-hybrid-it)
* [Listeners voor beschikbaarheids groep, client connectiviteit en failover van toepassing (SQL Server)](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)
* [Database-Mirroring verbindings reeksen gebruiken met beschikbaarheids groepen](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)

### <a name="network-latency-in-hybrid-it"></a>Netwerk latentie in hybride IT
Implementeer uw HADR-oplossing met de veronderstelling dat er Peri Oden met een hoge netwerk latentie tussen uw on-premises netwerk en Azure mogelijk zijn. Wanneer u replica's naar Azure implementeert, gebruikt u asynchrone door Voer in plaats van synchrone door Voer voor de synchronisatie modus. Wanneer u data base mirroring servers zowel on-premises als in azure implementeert, moet u de modus voor hoge prestaties gebruiken in plaats van de modus met hoge beveiliging.

### <a name="geo-replication-support"></a>Ondersteuning van geo-replicatie
Geo-replicatie in azure disks biedt geen ondersteuning voor het gegevens bestand en logboek bestand van dezelfde data base die op afzonderlijke schijven moet worden opgeslagen. GRS repliceert wijzigingen op elke schijf onafhankelijk en asynchroon. Dit mechanisme garandeert de schrijf volgorde binnen één schijf op de geo-gerepliceerde kopie, maar niet via Geo-gerepliceerde kopieën van meerdere schijven. Als u een Data Base configureert om het gegevens bestand en het logboek bestand op afzonderlijke schijven op te slaan, kan het zijn dat de herstelde schijven na een nood herstel een meer bijgewerkte kopie van het gegevens bestand bevatten dan het logboek bestand, waardoor het voorschrijvende logboek in SQL Server en de ACID-eigenschappen (atomiciteit, consistentie, isolatie en duurzaamheid) van trans acties worden verbroken. 

Als u niet de mogelijkheid hebt om geo-replicatie uit te scha kelen op het opslag account, moet u alle gegevens en logboek bestanden voor een Data Base op dezelfde schijf bewaren. Als u meer dan één schijf moet gebruiken vanwege de grootte van de data base, implementeert u een van de eerder genoemde oplossingen voor nood herstel om gegevens redundantie te garanderen.

## <a name="next-steps"></a>Volgende stappen

Bepaal of een [beschikbaarheids groep](availability-group-overview.md) of een [failover-cluster exemplaar](failover-cluster-instance-overview.md) de beste oplossing voor bedrijfs continuïteit is voor uw bedrijf. Bekijk vervolgens de [Aanbevolen procedures](hadr-cluster-best-practices.md) voor het configureren van uw omgeving voor hoge Beschik baarheid en herstel na nood gevallen.