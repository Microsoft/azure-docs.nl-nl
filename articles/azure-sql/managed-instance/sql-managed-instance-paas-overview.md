---
title: Wat is Azure SQL Managed Instance?
description: Meer informatie over hoe Azure SQL Managed Instance bijna 100% compatibiliteit biedt met de meest recente SQL Server-database-engine (Enterprise Edition)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: overview
author: bonova
ms.author: bonova
ms.reviewer: sstein, vanto
ms.date: 08/14/2020
ms.openlocfilehash: 83f38797e406ff7e62503f59ef979b9ce4f07f97
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917933"
---
# <a name="what-is-azure-sql-managed-instance"></a>Wat is Azure SQL Managed Instance?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance is de intelligente, schaalbare clouddatabaseservice die de breedste compatibiliteit met SQL Server-database-engine biedt met alle voordelen van een volledig beheerd en 'evergreen' platform-as-a-service. MeSQL Managed Instance is vrijwel 100% compatibel met de meest recente (Enterprise Edition) SQL Server-database-engine. Het biedt een systeemeigen implementatie van een [virtueel netwerk (VNet)](../../virtual-network/virtual-networks-overview.md) die veelvoorkomende beveiligingskwesties aanpakt, en een [bedrijfsmodel](https://azure.microsoft.com/pricing/details/sql-database/) dat gunstig is voor klanten die al SQL Server hebben. Met SQL Managed Instance kunnen bestaande SQL Server-klanten hun on-premises toepassingen naar de cloud tillen en naar een andere database verplaatsen met minimale toepassings- en databasewijzigingen. Tegelijkertijd behoudt SQL Managed Instance alle PaaS-mogelijkheden (automatische patches en versie-updates, [automatische back-ups](../database/automated-backups-overview.md), [hoge beschikbaarheid](../database/high-availability-sla.md)) waarmee de overhead en TCO van het beheer drastisch worden verminderd.

Als u geen ervaring hebt met Azure SQL Managed Instance, bekijk dan de video *Azure SQL Managed Instance* uit onze diepgaande [Azure SQL-videoserie](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Managed-Instance-Overview-6-of-61/player]

> [!IMPORTANT]
> Zie [Ondersteunde regio's](resource-limits.md#supported-regions) voor een lijst met regio's waar SQL Managed Instance momenteel beschikbaar is.

In de volgende grafiek worden de belangrijkste functies van SQL Managed Instance beschreven:

![Belangrijke functies](./media/sql-managed-instance-paas-overview/key-features.png)

Azure SQL Managed Instance is ontworpen voor klanten die een groot aantal apps willen migreren van een on-premises of IaaS, zelfgebouwde of ISV-omgeving naar een volledig beheerde PaaS-cloudomgeving, met zo weinig mogelijk migratie-inspanningen. Met behulp van de volledig geautomatiseerde [Azure Data Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) kunnen klanten hun bestaande SQL Server-instantie naar SQL Managed Instance overzetten, wat compatibiliteit biedt met SQL Server en volledige isolatie van klantinstanties met systeemeigen VNet-ondersteuning. Voor meer informatie over opties en hulpprogramma's voor migratie, raadpleegt u [Migratieoverzicht: SQL Server naar Azure SQL Managed Instance](../migration-guides/managed-instance/sql-server-to-managed-instance-overview.md).</br> Met Software Assurance kan Contoso haar bestaande licenties inwisselen tegen kortingstarieven op SQL Managed Instance met behulp van [Azure Hybrid Benefit voor SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). SQL Managed Instance is de beste migratiebestemming in de Cloud voor SQL Server-instanties die hoge beveiliging en een groot programmeerbaarheidsoppervlak nodig hebben.

## <a name="key-features-and-capabilities"></a>Belangrijkste functies en mogelijkheden

SQL Managed Instance combineert de beste functies die beschikbaar zijn in Azure SQL Database met de SQL Server-database-engine.

> [!IMPORTANT]
> SQL Managed Instance wordt uitgevoerd met alle functies van de meest recente versie van SQL Server, waaronder online bewerkingen, Automatische planningscorrecties en andere verbeteringen voor bedrijfsprestaties. Een vergelijking van de beschikbare functies is te zien bij [Functievergelijking: Azure SQL Managed Instance versus SQL Server](../database/features-comparison.md).

| **Voordelen van PaaS** | **Bedrijfscontinuïteit** |
| --- | --- |
|Geen hardware aankopen of beheren <br>Geen overhead voor het beheren van de onderliggende infrastructuur <br>Snelle inrichting en opschaling van services <br>Automatische patching en versie-upgrade <br>Integratie met andere PaaS-gegevensservices |SLA van 99,99% uptime  <br>Ingebouwde [hoge beschikbaarheid](../database/high-availability-sla.md) <br>Gegevens beveiligd met [automatische back-ups](../database/automated-backups-overview.md) <br>Door de klant configureerbare bewaarperiode voor back-ups <br>Door de gebruiker geïnitieerde [back-ups](/sql/t-sql/statements/backup-transact-sql?preserve-view=true&view=azuresqldb-mi-current) <br>Mogelijkheid om [een database te herstellen naar een bepaald tijdstip](../database/recovery-using-backups.md#point-in-time-restore) |
|**Beveiliging en naleving** | **Beheer**|
|Geïsoleerde omgeving ([VNet-integratie](connectivity-architecture-overview.md), single tenant service, toegewijde rekenkracht en opslag) <br>[TDE (Transparent Data Encryption)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure Active Directory (Azure AD)-verificatie](../database/authentication-aad-overview.md), ondersteuning voor eenmalige aanmelding <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD-server-principals (aanmeldingen)</a>  <br>Voldoet aan dezelfde nalevingsnormen als Azure SQL Database <br>[SQL-controle](auditing-configure.md) <br>[Advanced Threat Protection](threat-detection-configure.md) |Azure Resource Manager-API voor het automatiseren van het inrichten en schalen van services <br>Azure-portaalfunctionaliteit om handmatig services in te richten en te schalen <br>Data Migration Service

> [!IMPORTANT]
> Azure SQL Managed Instanced is gecertificeerd volgens een aantal nalevingsnormen. Raadpleeg voor meer informatie [Microsoft Azure Compliance-aanbiedingen](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers), waar u de meest recente lijst met nalevingscertificeringen voor SQL Managed Instance vindt onder **SQL Database**.

In de volgende tabel worden de belangrijkste functies van SQL Managed Instance beschreven:

|Functie | Beschrijving|
|---|---|
| Versie/build van SQL Server | SQL Server-database-engine (laatste stabiele versie) |
| Beheerde automatische back-ups | Ja |
| Ingebouwde instantie- en databasecontrole en metrische gegevens | Ja |
| Automatische software-patching | Ja |
| De nieuwste database-engine-functies | Ja |
| Aantal gegevensbestanden (ROWS) per database | Meerdere |
| Aantal logboekbestanden (LOG) per database | 1 |
| VNet - Implementatie van Azure Resource Manager | Ja |
| VNet - Klassiek implementatiemodel | Nee |
| Ondersteuning van portaal | Ja|
| Ingebouwde integratieservice (SSIS) | No - SSIS maakt deel uit van [Azure Data Factory PaaS](../../data-factory/tutorial-deploy-ssis-packages-azure.md) |
| Ingebouwde analyseservice (SSAS) | Nee - SSAS is een aparte [PaaS](../../analysis-services/analysis-services-overview.md) |
| Ingebouwde rapportageservice (SSRS) | Nee - gebruik in de plaats [Gepagineerde rapporten van Power BI](/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) of host SSRS op een Azure VM. SQL Managed Instance kan SSRS niet als service uitvoeren, maar kan [SSRS-catalogusdatabases](/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements) hosten voor een rapportserver die is geïnstalleerd op een virtuele Azure-machine met behulp van SQL Server-verificatie. |
|||

## <a name="vcore-based-purchasing-model"></a>Aankoopmodel op basis van vCore

Het op [vCore gebaseerde aankoopmodel](../database/service-tiers-vcore.md) voor SQL Managed Instance biedt u flexibiliteit, controle, transparantie en een eenvoudige manier om vereisten voor on-premises werkbelasting te vertalen naar de cloud. Met dit model kunt u de rekencapaciteit, het geheugen en de opslag aanpassen op basis van uw behoeften op het vlak van werkbelasting. Het vCore-model komt ook in aanmerking voor een korting van maximaal 55 procent met de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) voor SQL Server.

In het vCore-model kunt u kiezen tussen generaties van hardware.

- Logische **Gen4**-CPU's zijn gebaseerd op Intel® E5-2673 v3 (Haswell) 2,4 GHz-processors, gekoppelde SSD, fysieke kernen, 7 GB RAM per kern en rekengrootten tussen 8 en 24 vCores.
- Logische **GEN5**-CPU's zijn gebaseerd op Intel® E5-2673 v4 (Broadwell) 2,3 GHz, Intel® SP-8160 (Skylake) en Intel® 8272CL (Cascade Lake) 2,5 GHz-processors, snelle NVMe SSD, hyper-threaded logische kern en rekengrootten tussen 4 en 80 kernen.

Meer informatie over het verschil tussen hardwaregeneraties in [SQL Managed Instance-resourcelimieten](resource-limits.md#hardware-generation-characteristics).

## <a name="service-tiers"></a>Servicelagen

SQL Managed Instance is beschikbaar in twee servicelagen:

- **Algemeen doel**: Ontworpen voor toepassingen met typische prestatie- en I/O-latentievereisten.
- **Bedrijfskritiek**: Ontworpen voor toepassingen met lage I/O-latentievereisten en minimale gevolgen van onderliggende onderhoudsbewerkingen op de werkbelasting.

Beide servicelagen garanderen een beschikbaarheid van 99,99% en bieden u de mogelijkheid om de opslaggrootte en de rekencapaciteit onafhankelijk te selecteren. Zie [Hoge beschikbaarheid en Azure SQL Managed Instance](../database/high-availability-sla.md)voor meer informatie over de architectuur voor hoge beschikbaarheid van Azure SQL Managed Instance.

### <a name="general-purpose-service-tier"></a>Servicelaag voor Algemeen gebruik

In de volgende lijst worden de belangrijkste kenmerken van de servicelaag voor Algemeen gebruik:

- Ontworpen voor het merendeel van zakelijke toepassingen met typische prestatievereisten
- Azure Blob-opslag met hoge prestaties (8 TB)
- Ingebouwde [hoge beschikbaarheid](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) op basis van betrouwbare Azure Blob-opslag en [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)

Zie voor meer informatie [Opslaglaag in de laag voor Algemeen gebruik](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) en [Best practices en overwegingen over opslagprestaties voor SQL Managed Instance (Algemeen gebruik)](/archive/blogs/sqlcat/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose).

Meer informatie over het verschil tussen servicelagen in [SQL Managed Instance-resourcelimieten](resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Servicelaag Bedrijfskritiek

De servicelaag Bedrijfskritiek is ontworpen voor toepassingen met hoge I/O-vereisten. Deze biedt de hoogste tolerantie voor fouten bij het gebruik van verschillende geïsoleerde replica's.

De volgende lijst geeft een overzicht van de belangrijkste kenmerken van de servicelaag Bedrijfskritiek:

- Ontworpen voor zakelijke toepassingen met de hoogste  prestatie- en HA-vereisten
- Wordt geleverd met supersnelle lokale SSD-opslag (tot 1 TB op Gen4 en tot 4 TB op GEN5)
- Ingebouwde [hoge beschikbaarheid](../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) op basis van [Always On-beschikbaarheidsgroepen](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) en [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)
- Ingebouwde aanvullende [alleen-lezen databasereplica](../database/read-scale-out.md) die kunnen worden gebruikt voor rapportage en andere alleen-lezen workloads
- [In-Memory OLTP](../in-memory-oltp-overview.md) dat kan worden gebruikt voor de workloads met hoge prestatievereisten  

Meer informatie over het verschil tussen servicelagen in [SQL Managed Instance-resourcelimieten](resource-limits.md#service-tier-characteristics).

## <a name="management-operations"></a>Beheerbewerkingen

Azure SQL Managed Instance biedt beheerbewerkingen die u kunt gebruiken om automatisch nieuwe beheerde exemplaren te implementeren, eigenschappen van exemplaren bij te werken en exemplaren te verwijderen als ze niet meer nodig zijn. Gedetailleerde uitleg van beheerbewerkingen vindt u op de pagina [Overzicht van beheerbewerkingen voor Managed Instance](management-operations-overview.md).

## <a name="advanced-security-and-compliance"></a>Geavanceerde beveiliging en naleving van voorschriften

SQL Managed Instance wordt geleverd met geavanceerde beveiligingsfuncties die worden geleverd door het Azure-platform en de SQL Server-database-engine.

### <a name="security-isolation"></a>Beveiligingsisolatie

SQL Managed Instance biedt extra beveiligingsisolatie van andere tenants op het Azure-platform. Beveiligings isolatieomvat:

- [Systeemeigen implementatie van virtuele netwerken](connectivity-architecture-overview.md) en connectiviteit met uw on-premises omgeving met behulp van Azure Express Route of VPN Gateway.
- In een standaard implementatie is het SQL-eindpunt enkel blootgesteld via een privé-IP-adres, waardoor er veilige verbinding mogelijk is vanaf persoonlijke Azure- of hybride netwerken.
- Eén tenant met toegewezen onderliggende infrastructuur (rekenen en opslag).

Het volgende diagram geeft een overzicht van de verschillende verbindingsopties voor uw toepassingen:

![Hoge beschikbaarheid](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

Voor meer informatie over VNet-integratie en het handhaven van netwerkbeleid op subnetniveau, raadpleegt u [VNet-architectuur voor beheerde instanties](connectivity-architecture-overview.md) en [Uw toepassing verbinden met een beheerde instantie](connect-application-instance.md).

> [!IMPORTANT]
> Plaats meerdere beheerde instanties in hetzelfde subnet, overal waar uw beveiligingsvereisten dat toestaan, aangezien dit u extra voordelen oplevert. Door instanties in hetzelfde subnet te plaatsen, wordt het beheer van de netwerkinfrastructuur aanzienlijk vereenvoudigd en wordt de inrichtingstijd van de instantie teruggebracht, omdat een lange inrichtingsduur te maken heeft met de kosten voor het implementeren van de eerste beheerde instantie in een subnet.

### <a name="security-features"></a>Beveiligingsfuncties

Azure SQL Managed Instance biedt een aantal geavanceerde beveiligingsfuncties die kunnen worden gebruikt om uw gegevens te beveiligen.

- [SQL Managed Instance-controle](auditing-configure.md) volgt gebeurtenissen in de database op en schrijft ze naar een auditlogboekbestand in uw Azure-opslagaccount. Dankzij controles kunt u zorgen voor naleving van wet- en regelgeving, krijgt u inzicht in de activiteit in uw database en in de afwijkingen en discrepanties die kunnen wijzen op problemen voor het bedrijf of vermoedelijke schendingen van de beveiliging.
- Gegevensversleuteling onderweg - SQL Managed Instance beveiligt uw gegevens door gegevens onderweg te versleutelen met Transport Layer Security. Naast Transport Layer Security biedt SQL Managed Instance beveiliging van gevoelige gegevens in flight, at rest en tijdens de verwerking van query's met [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted biedt ongeëvenaarde beveiliging tegen diefstal van kritieke gegevens. Met Always Encrypted worden bijvoorbeeld de creditcardnummers altijd versleuteld opgeslagen in de database, ook tijdens queryverwerking. Ontsleuteling vindt plaats op het moment dat de gegevens moeten worden gebruikt en kan alleen worden uitgevoerd door gemachtigde medewerkers of toepassingen die de gegevens moeten verwerken.
- [Advanced Threat Protection](threat-detection-configure.md) biedt aanvullende [controle](auditing-configure.md) door een extra laag beveiligingsintelligentie toe te voegen aan de service, die ongebruikelijke en mogelijk schadelijke pogingen om databases te openen of te misbruiken. U wordt gewaarschuwd bij verdachte activiteiten, potentiële kwetsbaarheden, SQL-injectieaanvallen en afwijkende patronen voor databasetoegang. Waarschuwingen van Azure Threat Protection vindt u in het [Azure Security Center](https://azure.microsoft.com/services/security-center/). Ze bieden details over verdachte activiteiten en geven aanbevelingen voor het onderzoeken en tegenhouden ervan.  
- [Met dynamische gegevensmaskering](/sql/relational-databases/security/dynamic-data-masking) wordt de blootstelling van gevoelige gegevens beperkt door deze gegevens te maskeren voor niet-gemachtigde gebruikers. Dynamische gegevensmaskering helpt onbevoegde toegang tot gevoelige gegevens te voorkomen, doordat u kunt aangeven hoeveel van de gevoelige gegevens mag worden vrijgegeven, met minimale gevolgen voor de toepassingslaag. Dit is een beveiligingsfunctie op basis van beleid. De gevoelige gegevens in de resultatenset van een query die is uitgevoerd op toegewezen databasevelden worden verborgen, terwijl de gegevens in de database niet worden gewijzigd.
- Met [beveiliging op rijniveau](/sql/relational-databases/security/row-level-security) (RLS) kunt u de toegang tot rijen in een databasetabel beheren op basis van de kenmerken van de gebruiker die een query uitvoert (zoals groepslidmaatschap of uitvoeringscontext). RLS vereenvoudigt het ontwerp en de code van de beveiliging in uw toepassing. Met RLS kunt u beperkingen instellen voor de toegang tot gegevens in rijen. U kunt bijvoorbeeld bepalen dat werkrollen alleen toegang hebben tot de rijen met gegevens die relevant zijn voor hun afdeling, of de toegang beperken tot de relevante gegevens.
- [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) versleutelt gegevensbestanden van SQL Managed Instance, ook wel de versleuteling van gegevens at rest. TDE voert realtime I/O-versleuteling en -ontsleuteling van de gegevens en logboekbestanden uit. De versleuteling maakt gebruik van een database encryption key (DEK), die is opgeslagen in de bootrecord van de database zodat hij beschikbaar is voor hestel. U kunt al uw databases in een beheerde instantie beveiligen met transparante gegevensversleuteling. TDE is een beproefde technologie voor versleuteling in SQL Server van inactieve gegevens, die verplicht is volgens veel nalevingsstandaarden voor de bescherming tegen diefstal van opslagmedia.

Migratie van een versleutelde database naar SQL Managed Instance wordt ondersteund via Azure Database Migration Service of systeemeigen herstel. Als u van plan bent om een versleutelde database te migreren met behulp van systeemeigen herstel, is de migratie van het bestaande TDE-certificaat van de SQL Server-instantie naar een SQL Managed Instance een vereiste stap. Zie [Migratie van SQL Server naar SQL Managed Instance](migrate-to-instance-from-sql-server.md) voor meer informatie over de migratie-opties.

## <a name="azure-active-directory-integration"></a>Integratie van Azure Active Directory

SQL Managed Instance ondersteunt traditionele aanmeldingen met SQL Server-database-engine en aanmeldingen die zijn geïntegreerd met Azure AD. Azure AD server-principals (aanmeldingen) (**openbare preview**) zijn een Azure-cloudversie van on-premises database-aanmeldingen die u in uw on-premises omgeving gebruikt. Met Azure AD-server-principals (aanmeldingen) kunt u gebruikers en groepen van uw Azure AD-tenant opgeven als echte instantie-principals, waardoor elke bewerking op instantieniveau kan worden uitgevoerd, inclusief query's tussen databases binnen hetzelfde beheerde exemplaar.

Er bestaat een nieuwe syntaxis voor het maken van Azure AD-server-principals (aanmeldingen) **VANUIT EXTERNE PROVIDER**. Voor meer informatie over de syntaxis raadpleegt u <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">AANMELDING MAKEN</a> en leest u het artikel [Een Azure Active Directory-beheerder inrichten voor SQL Managed Instance](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integratie en meervoudige verificatie

Dankzij [Azure Active Directory-integratie](../database/authentication-aad-overview.md) kunt u in SQL Managed Instance de identiteit van databasegebruikers en andere Microsoft-services centraal beheren. Deze mogelijkheid vereenvoudigt het beheer van machtigingen en verbetert de beveiliging. Azure Active Directory ondersteunt [meervoudige verificatie](../database/authentication-mfa-ssms-configure.md) voor betere beveiliging van gegevens en toepassingen, en ondersteunt ook een proces voor eenmalige aanmelding.

### <a name="authentication"></a>Verificatie

Verificatie van SQL Managed Instance verwijst naar hoe gebruikers hun identiteit bewijzen wanneer ze verbinding maken met de database. SQL Managed Instance ondersteunt twee typen verificatie:  

- **SQL-verificatie**:

  Bij deze verificatiemethode wordt een combinatie van gebruikersnaam en wachtwoord gebruikt.
- **Azure Active Directory-verificatie**:

  Deze verificatiemethode gebruikt identiteiten die worden beheerd in Azure Active Directory. Deze methode wordt ondersteund voor beheerde en geïntegreerde domeinen. Gebruik [waar mogelijk](/sql/relational-databases/security/choose-an-authentication-mode) Active Directory-verificatie (geïntegreerde beveiliging).

### <a name="authorization"></a>Autorisatie

Autorisatie verwijst naar wat een gebruiker kan doen binnen een database in Azure SQL Managed Instance. Dit wordt bepaald door de rollidmaatschappen en objectmachtigingen voor de database van uw gebruikersaccount. SQL Managed Instance heeft dezelfde autorisatiemogelijkheden als SQL Server 2017.

## <a name="database-migration"></a>Databasemigratie

SQL Managed Instance streeft naar gebruikersscenario's met massale databasemigratie van on-premises of IaaS-database-implementaties. SQL Managed Instance biedt ondersteuning voor verschillende opties voor databasemigratie. Deze opties worden besproken in de migratiehandleidingen. Raadpleeg [Migratieoverzicht: SQL Server naar Azure SQL Managed Instance](../migration-guides/managed-instance/sql-server-to-managed-instance-overview.md) voor meer informatie.

### <a name="backup-and-restore"></a>Back-ups en herstellen  

De benadering voor de migratie maakt gebruik van SQL-back-ups naar Azure Blob-opslag. Back-ups die zijn opgeslagen in een Azure Storage-blob, kunnen rechtstreeks worden hersteld in een beheerde instantie met behulp van de [T-SQL RESTORE-opdracht](/sql/t-sql/statements/restore-statements-transact-sql?preserve-view=true&view=azuresqldb-mi-current).

- Zie [Een back-upbestand herstellen naar een beheerde instantie](restore-sample-database-quickstart.md)voor een snelstartgids waarin wordt getoond hoe u de Wide World Importers herstelt, het standaard back-upbestand van de database. In deze snelstartgids ziet u dat u een back-upbestand moet uploaden naar Azure Blob-opslag en het kunt beveiligen met behulp van een sleutel voor Shared Access Signature (SAS).
- Zie [Systeemeigen HERSTEL vanaf URL](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md#backup-and-restore) voor meer informatie over herstel vanaf een URL.

> [!IMPORTANT]
> Back-ups vanaf een beheerde instantie kunnen alleen worden hersteld naar een andere beheerde instantie. Ze kunnen niet worden hersteld naar een SQL Server-instantie of Azure SQL Database.

### <a name="database-migration-service"></a>Database Migration Service

Azure Database Migration Service is een volledig beheerde service die is ontworpen voor naadloze migratie van meerdere databasebronnen naar Azure-gegevensplatforms met slechts minimale downtime. Deze service stroomlijnt de taken die nodig zijn voor het verplaatsen van bestaande databases van derden en SQL Server naar Azure SQL Database, Azure SQL Managed instance en SQL Server op Azure VM. Zie [Migreren van uw on-premises database naar een SQL Managed Instance met behulp van Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).

## <a name="sql-features-supported"></a>Ondersteunde SQL-functies

SQL Managed instance is erop gericht om 100% compatibiliteit te bieden met de meest recente versie van SQL Server via een gefaseerd releaseplan. Zie voor een overzicht van de functies en een vergelijking [Functievergelijking van SQL Managed Instance](../database/features-comparison.md) en voor een lijst met T-SQL-verschillen in SQL Managed Instance versus SQL Server [SQL Managed Instance T-SQL-verschillen van SQL Server](transact-sql-tsql-differences-sql-server.md).

SQL Managed Instance ondersteunt achterwaartse compatibiliteit voor SQL Server 2008-databases. Directe migratie van SQL Server 2005-databaseservers wordt ondersteund en het compatibiliteitsniveau voor gemigreerde SQL Server 2005-databases wordt bijgewerkt naar SQL Server 2008.
  
Het volgende diagram geeft een overzicht van de oppervlaktecompatibiliteit in SQL Managed Instance:  

![oppervlaktecompatibiliteit](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-sql-managed-instance"></a>De belangrijkste verschillen tussen on-premises SQL Server en SQL Managed Instance

SQL Managed Instance heeft als voordeel dat het altijd up-to-date is in de cloud, wat betekent dat sommige functies in SQL Server verouderd zijn, buiten gebruik gesteld werden of dat er alternatieven bestaan. Er zijn specifieke gevallen waarin hulpprogramma's moeten herkennen dat een bepaalde functie op een iets andere manier werkt of dat de service wordt uitgevoerd in een omgeving die u niet volledig beheert.

Enkele belangrijke verschillen:

- Hoge beschikbaarheid is ingebouwd en vooraf geconfigureerd met technologie die vergelijkbaar is met [Always On-beschikbaarheidsgroepen](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Er zijn geautomatiseerde back-ups en herstel naar een eerder tijdstip. Klanten kunnen `copy-only`-back-ups initiëren waarvoor geen conflicten met de automatische back-upketen hebben.
- Het opgeven van volledige fysieke paden wordt niet ondersteund, dus alle bijbehorende scenario's moeten anders worden ondersteund: RESTORE DB biedt geen ondersteuning voor WITH MOVE. CREATE DB staat geen fysieke paden toe, BULK INSERT werkt alleen met Azure-blobs, enzovoort.
- SQL Managed Instance ondersteunt [Azure AD-verificatie](../database/authentication-aad-overview.md) als een alternatief in de cloud voor Windows-verificatie.
- SQL Managed Instance beheert automatisch XTP-bestandsgroepen en -bestanden voor databases die OLTP-objecten in het geheugen bevatten.
- SQL Managed Instance ondersteunt SQL Server Integration Services (SSIS) en kan een SSIS-catalogus (SSISDB) hosten waarmee SSIS-pakketten worden opgeslagen, maar ze worden uitgevoerd op een beheerde Azure-SSIS Integration Runtime (IR) in Azure Data Factory. Zie [Azure-SSIS IR maken in Data Factory](../../data-factory/create-azure-ssis-integration-runtime.md). Zie [SQL Database vergelijken met SQL Managed Instance](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance) als u de SSIS-functies wilt vergelijken.

### <a name="administration-features"></a>Beheerfuncties

Met SQL Managed Instance kunnen systeembeheerders minder tijd besteden aan beheertaken, omdat deze door de service worden uitgevoerd of de taken aanzienlijk vereenvoudigd worden. Bijvoorbeeld [OS/RDBMS-installatie en patches](../database/high-availability-sla.md), [dynamische instantiegrootte en -configuratie](../database/single-database-scale.md), [back-ups](../database/automated-backups-overview.md), [databasereplicatie](replication-between-two-instances-configure-tutorial.md) (inclusief systeem databases), [configuratie van hoge beschikbaarheid](../database/high-availability-sla.md) en configuratie van de status en gegevensstromen voor [prestatiebewaking](../../azure-monitor/insights/azure-sql.md).

Zie voor meer informatie [een lijst met ondersteunde en niet-ondersteunde functies van SQL Managed Instance](../database/features-comparison.md) en [T-SQL-verschillen tussen SQL Managed Instance en SQL Server](transact-sql-tsql-differences-sql-server.md).

### <a name="programmatically-identify-a-managed-instance"></a>Een beheerde instantie programmatisch identificeren

De volgende tabel bevat verschillende eigenschappen, die toegankelijk zijn via Transact-SQL, die u kunt gebruiken om te detecteren of uw toepassing werkt met SQL Managed Instance en om belangrijke eigenschappen op te halen.

|Eigenschap|Waarde|Opmerking|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Deze waarde is dezelfde als in SQL Database. Deze geeft SQL-engine versie 12 (SQL Server 2014) **niet** aan. SQL Managed Instance voert altijd de nieuwste stabiele versie van SQL-engine uit, die gelijk is aan of hoger is dan de laatste beschikbare RTM-versie van SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Deze waarde is dezelfde als in SQL Database.|
|`SERVERPROPERTY('EngineEdition')`|8|Deze waarde is een unieke aanduiding voor een beheerde instantie.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|De volledige DNS-naam van de instantie in de volgende indeling: `<instanceName>`.`<dnsPrefix>`.database.windows.net, waarbij `<instanceName>` de naam is van de klant, en `<dnsPrefix>` een automatisch gegenereerd deel van de naam is dat garandeert dat de globale DNS-naam uniek blijft (bijvoorbeeld "wcus17662feb9ce98")|Voorbeeld: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Volgende stappen

- Zie de [Quickstart-gids](instance-create-quickstart.md) voor meer informatie over het maken van uw eerste beheerde exemplaar.
- Zie [Veelvoorkomende SQL-functies](../database/features-comparison.md) voor een lijst met functies en vergelijkingen.
- Zie [VNet-configuratie van SQL Managed Instance](connectivity-architecture-overview.md) voor meer informatie over VNet-configuratie.
- Zie [Beheerd exemplaar maken](instance-create-quickstart.md) voor een quickstart waarmee u een beheerd exemplaar kunt maken en een database vanuit een back-upbestand kunt herstellen.
- Zie [Migratie van SQL Managed Instance met behulp van Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md) voor een zelfstudie over Azure Database Migration Service voor migratie.
- Zie [Azure SQL Managed Instance bewaken met Azure SQL-analyse](../../azure-monitor/insights/azure-sql.md) voor geavanceerde bewaking van de databaseprestaties in SQL Managed Instance, met ingebouwde intelligentie voor het oplossen van problemen.
- Zie [Prijs van SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/) voor informatie over prijzen.