---
title: Veelgestelde vragen
titleSuffix: Azure SQL Managed Instance
description: Veelgestelde vragen over Azure SQL Managed Instance (FAQ)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 09/21/2020
ms.openlocfilehash: 42e263a47c6c771d2b1fef6586468cfc5a698e28
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047912"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Veelgestelde vragen over Azure SQL Managed Instance (FAQ)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Dit artikel bevat de meest voorkomende vragen over [Azure SQL Managed instance](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Ondersteunde functies

**Waar vind ik een lijst met functies die worden ondersteund in een SQL Managed instance?**

Zie [functies van Azure SQL Managed instance](../database/features-comparison.md)voor een lijst met ondersteunde functies in SQL Managed instance.

Zie voor verschillen in de syntaxis en het gedrag tussen Azure SQL Managed instance en SQL Server [T-SQL-verschillen van SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="technical-specification-resource-limits-and-other-limitations"></a>Technische specificatie, resource limieten en andere beperkingen
 
**Waar vind ik technische kenmerken en resource limieten voor SQL Managed instance?**

Zie [technische verschillen in hardware generaties](resource-limits.md#hardware-generation-characteristics)voor de beschik bare kenmerken voor het genereren van hardware.
Zie [technische verschillen tussen service lagen](resource-limits.md#service-tier-characteristics)voor de beschik bare service lagen en hun kenmerken.

**Van welke servicelaag ben ik in aanmerking?**

Elke klant komt in aanmerking voor een servicelaag. Als u echter uw bestaande licenties wilt uitwisselen voor kortings tarieven op Azure SQL Managed instance met behulp van [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/), moet u er rekening mee houdt dat SQL Server Enterprise Edition-klanten met Software Assurance in aanmerking komen voor de [Algemeen](../database/service-tier-general-purpose.md) -of [bedrijfskritiek](../database/service-tier-business-critical.md) prestatie lagen en SQL Server Standard Edition-klanten met software assurance in aanmerking komen voor de algemeen-laag voor prestaties. Zie [specifieke rechten van de Ahb](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server)voor meer informatie.

**Welke typen abonnementen worden ondersteund voor een SQL Managed instance?**

Zie [ondersteunde abonnements typen](resource-limits.md#supported-subscription-types)voor een lijst met ondersteunde typen abonnementen. 

**Welke Azure-regio's worden ondersteund?**

Beheerde exemplaren kunnen worden gemaakt in de meeste Azure-regio's. Zie [ondersteunde regio's voor SQL Managed instance](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Als u een beheerd exemplaar nodig hebt in een regio die momenteel niet wordt ondersteund, [verzendt u een ondersteunings aanvraag via de Azure Portal](../database/quota-increase-request.md).

**Zijn er quota beperkingen voor implementaties van SQL Managed instances?**

Het beheerde exemplaar heeft twee standaard limieten: de limiet voor het aantal subnetten dat u kunt gebruiken en een limiet voor het aantal vCores dat u kunt inrichten. De limieten zijn afhankelijk van de typen abonnementen en regio's. Zie voor de lijst met regionale resource beperkingen per abonnements type tabel van [regionale resource beperking](resource-limits.md#regional-resource-limitations). Dit zijn zachte limieten die op aanvraag kunnen worden verhoogd. Als u meer beheerde exemplaren in uw huidige regio's wilt inrichten, verzendt u een ondersteunings aanvraag om het quotum te verhogen met behulp van de Azure Portal. Zie [aanvraag quotum verhogingen voor Azure SQL database](../database/quota-increase-request.md)voor meer informatie.

**Kan ik het aantal data bases limiet (100) op mijn beheerde exemplaar op aanvraag verhogen?**

Nee, en op dit moment zijn er geen vastgelegde plannen om het aantal data bases op een SQL Managed instance te verg Roten. 

**Waar kan ik migreren als ik meer dan 8TB aan gegevens heb?**
U kunt overwegen om te migreren naar andere Azure-versies die geschikt zijn voor uw workload: [Azure SQL database grootschalige](../database/service-tier-hyperscale.md) of [SQL Server op Azure virtual machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

**Waar kan ik migreren als ik specifieke hardwarevereisten, zoals grotere RAM, voor een vCore-verhouding of meer Cpu's?**
U kunt overwegen om te migreren naar [SQL Server op Azure virtual machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) of [Azure SQL database](../database/sql-database-paas-overview.md) geheugen/CPU geoptimaliseerd.

## <a name="known-issues-and-defects"></a>Bekende problemen en defecten

**Waar vind ik bekende problemen en defecten?**

Zie [bekende problemen](../database/doc-changes-updates-release-notes.md#known-issues)voor product fouten en bekende problemen.

## <a name="new-features"></a>Nieuwe functies

**Waar vind ik de nieuwste functies en de functies van de open bare preview?**

Zie [release opmerkingen](../database/doc-changes-updates-release-notes.md?tabs=managed-instance)voor nieuwe en preview-functies.

## <a name="create-update-delete-or-move-sql-managed-instance"></a>SQL Managed instance maken, bijwerken, verwijderen of verplaatsen

**Hoe kan ik een SQL Managed instance inrichten?**

U kunt een exemplaar inrichten vanuit [Azure Portal](instance-create-quickstart.md), [Power shell](scripts/create-configure-managed-instance-powershell.md), [Azure cli](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281) en [arm-sjablonen](/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates).

**Kan ik beheerde exemplaren inrichten in een bestaand abonnement?**

Ja, u kunt een beheerd exemplaar inrichten in een bestaand abonnement als dat abonnement deel uitmaakt van de [ondersteunde typen abonnementen](resource-limits.md#supported-subscription-types).

**Waarom kan ik geen beheerd exemplaar inrichten in het subnet waarvan de naam begint met een cijfer?**

Dit is een huidige beperking voor het onderliggende onderdeel dat de subnetnaam verifieert met de reguliere expressie ^ [a-za-Z_] [^ \\ \/ \: \* \? \" \<\> \| \` \' \^ ] * (? <! [ \. \s]) $. Alle namen die de regex door geven en geldige subnet-namen zijn, worden momenteel ondersteund.

**Hoe kan ik het formaat van mijn beheerde exemplaar schalen?**

U kunt uw beheerde instantie schalen vanuit [Azure Portal](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation), [Power shell](/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell), [Azure cli](/cli/azure/sql/mi#az-sql-mi-update) of [arm-sjablonen](/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates).

**Kan ik mijn beheerde exemplaar verplaatsen van de ene regio naar een andere?**

Ja, dat kunt u. Zie [resources verplaatsen tussen regio's](../database/move-resources-across-regions.md)voor instructies.

**Hoe kan ik mijn beheerde exemplaar verwijderen?**

U kunt beheerde exemplaren verwijderen via Azure Portal, [Power shell](/powershell/module/az.sql/remove-azsqlinstance), [Azure cli](/cli/azure/sql/mi#az-sql-mi-delete) of [Resource Manager rest-api's](/rest/api/sql/managedinstances/delete).

**Hoe lang duurt het om een instantie te maken of bij te werken of om een Data Base te herstellen?**

De verwachte tijd voor het maken van een nieuw beheerd exemplaar of het wijzigen van service lagen (vCores, opslag) is afhankelijk van verschillende factoren. Zie [beheer bewerkingen](sql-managed-instance-paas-overview.md#management-operations).
 
## <a name="naming-conventions"></a>Naamconventies

**Kan een beheerd exemplaar dezelfde naam hebben als een SQL Server on-premises exemplaar?**

Het wijzigen van de naam van een beheerd exemplaar wordt niet ondersteund.

**Kan ik het voor voegsel van de DNS-zone wijzigen?**

Ja, de standaard DNS-zone van het beheerde exemplaar *. database.Windows.net* kan worden gewijzigd. 

Als u een andere DNS-zone wilt gebruiken in plaats van de standaard instelling, bijvoorbeeld *. contoso.com*: 
- Gebruik CliConfig voor het definiëren van een alias. Het hulp programma is slechts een wrapper voor register instellingen, zodat het kan worden uitgevoerd met behulp van groeps beleid of een script.
- Gebruik *CNAME* met de optie *TrustServerCertificate = True* .

## <a name="migration-options"></a>Migratieopties

**Hoe kan ik migreren van Azure SQL Database enkele of elastische pool naar een beheerd exemplaar van SQL?**

Beheerde instantie biedt dezelfde prestatie niveaus per Compute en opslag grootte als andere implementatie opties van Azure SQL Database. Als u gegevens wilt consolideren voor één exemplaar of als u alleen een functie nodig hebt die uitsluitend wordt ondersteund in het beheerde exemplaar, kunt u uw gegevens migreren met behulp van de functionaliteit voor exporteren/importeren (BACPAC). Hier volgen andere manieren om te overwegen om SQL Database migratie naar een door SQL beheerd exemplaar: 
- [Externe gegevens bron](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-129-using-data-source-external-from-azure-sql/ba-p/1443210) gebruiken
- [SQLPackage](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-migrate-azure-sql-database-to-azure-sql-managed-instance/ba-p/369182) gebruiken
- [Bcp](https://medium.com/azure-sqldb-managed-instance/migrate-from-azure-sql-managed-instance-using-bcp-674c92efdca7) gebruiken

**Hoe kan ik mijn exemplaar database migreren naar een enkele Azure SQL Database?**

U kunt ook [een Data Base exporteren naar BACPAC](../database/database-export.md) en vervolgens [het BACPAC-bestand importeren](../database/database-import.md). Dit is de aanbevolen benadering als uw data base kleiner is dan 100 GB.

[Transactionele replicatie](replication-two-instances-and-sql-server-configure-tutorial.md?view=sql-server-2017&preserve-view=true) kan worden gebruikt als alle tabellen in de Data Base *primaire* sleutels hebben en er geen in-Memory OLTP-objecten in de Data Base zijn.

Systeem eigen COPY_ONLY back-ups die zijn gemaakt van het beheerde exemplaar, kunnen niet worden hersteld naar SQL Server omdat het beheerde exemplaar een hogere database versie heeft dan SQL Server. Zie [kopie-only backup](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15)voor meer informatie.

**Hoe kan ik mijn SQL Server-exemplaar migreren naar een SQL Managed instance?**

Als u uw SQL Server-exemplaar wilt migreren, raadpleegt [u SQL Server exemplaar migratie naar Azure SQL Managed instance](migrate-to-instance-from-sql-server.md).

**Hoe kan ik migreren van andere platforms naar een SQL Managed instance?**

Raadpleeg de [migratie handleiding voor Azure data base](https://datamigration.microsoft.com/)voor migratie-informatie over het migreren van andere platforms.

## <a name="switch-hardware-generation"></a>Hardware-generatie scha kelen 

**Kan ik de hardware-generatie van beheerde exemplaren tussen gen 4 en Gen 5 online veranderen?**

Automated online overschakelen van Gen4 naar GEN5 is mogelijk als GEN5 hardware beschikbaar is in de regio waar uw beheerde exemplaar is ingericht. In dit geval kunt u de [overzichts pagina](../database/service-tiers-vcore.md) van het vCore-model controleren, waarin wordt uitgelegd hoe u tussen de hardware gegenereerd.

Dit is een langlopende bewerking als een nieuw beheerd exemplaar wordt ingericht op de achtergrond en data bases worden automatisch overgebracht tussen het oude en het nieuwe exemplaar met een snelle failover aan het einde van het proces.

Opmerking: Gen4-hardware wordt gefaseerd uitgevoerd en is niet meer beschikbaar voor nieuwe implementaties. Alle nieuwe data bases moeten worden geïmplementeerd op GEN5-hardware. Het is ook niet mogelijk om over te scha kelen van GEN5 naar Gen4.

## <a name="performance"></a>Prestaties 

**Hoe kan ik de prestaties van het beheerde exemplaar vergelijken met de prestaties van SQL Server?**

Voor een vergelijking van de prestaties tussen het beheerde exemplaar en SQL Server, is een goed uitgangs punt [Aanbevolen procedures voor de prestatie vergelijking tussen Azure SQL Managed instance en SQL Server](https://techcommunity.microsoft.com/t5/azure-sql-database/the-best-practices-for-performance-comparison-between-azure-sql/ba-p/683210) artikel.

**Wat veroorzaakt prestatie verschillen tussen het beheerde exemplaar en het SQL Server?**

Zie de [belangrijkste oorzaken van prestatie verschillen tussen het beheerde exemplaar van SQL en SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/). Zie [impact van de grootte van het logboek bestand op algemeen](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)voor meer informatie over de invloed van de grootte van het logboek bestand op de prestaties van algemeen Managed instance.

**De prestaties van mijn beheerde exemplaar Hoe kan ik afstemmen?**

U kunt de prestaties van uw beheerde exemplaar optimaliseren door:
- [Automatisch afstemmen](../database/automatic-tuning-overview.md) waarmee de prestaties en stabiele workloads met een voortdurende prestaties worden afgestemd op basis van AI en machine learning.
-   [In-Memory OLTP](../in-memory-oltp-overview.md) waarmee de door Voer en latentie van transactionele werk belastingen worden verbeterd en waarmee zakelijke inzichten sneller worden geleverd. 

Als u de prestaties nog verder wilt afstemmen, kunt u overwegen enkele van de *Aanbevolen procedures* voor het [afstemmen van toepassingen en data bases](../database/performance-guidance.md#tune-your-database)toe te passen.
Als uw werk belasting uit veel kleine trans acties bestaat, kunt u overwegen om [het verbindings type van de proxy naar de omleidings modus te scha kelen voor een](connection-types-overview.md#changing-connection-type) lagere latentie en een hogere door voer.

## <a name="monitoring-metrics-and-alerts"></a>Bewaking, metrische gegevens en waarschuwingen

**Wat zijn de opties voor het bewaken en waarschuwen van mijn beheerde exemplaar?**

Zie het [blog bericht bewakings opties voor Azure SQL Managed instance](https://techcommunity.microsoft.com/t5/azure-sql-database/monitoring-options-available-for-azure-sql-managed-instance/ba-p/1065416)voor alle mogelijke opties voor het bewaken en waarschuwen van het verbruik en de prestaties van het beheerde exemplaar van SQL. Zie [realtime prestatie bewaking voor het beheerde exemplaar van Azure SQL data base](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)voor de realtime prestatie bewaking voor SQL mi.

**Kan ik SQL Profiler gebruiken voor het bijhouden van prestaties?**

Ja, SQL Profiler wordt ondersteund of SQL Managed instance. Zie [SQL](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15)Profiler voor meer informatie.

**Worden Database Advisor en Query Performance Insight ondersteund voor beheerde exemplaar databases?**

Nee, ze worden niet ondersteund. U kunt [dmv's](../database/monitoring-with-dmvs.md) en [query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?preserve-view=true&view=sql-server-ver15) samen met [SQL Profiler](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15) en [XEvents](/sql/relational-databases/extended-events/extended-events?preserve-view=true&view=sql-server-ver15) gebruiken om uw data bases te bewaken.

**Kan ik metrische waarschuwingen maken op een SQL Managed instance?**

Ja. Zie [Create Alerts for SQL Managed instance](alerts-create.md)(Engelstalig) voor instructies.

**Kan ik metrische waarschuwingen maken voor een data base in een beheerd exemplaar?**

U kunt geen waarschuwingen voor metrische gegevens beschikbaar maken voor alleen beheerde exemplaren. Er zijn geen waarschuwings gegevens voor de afzonderlijke data bases in het beheerde exemplaar beschikbaar.

## <a name="storage-size"></a>Opslag grootte

**Wat is de maximale opslag grootte voor een SQL Managed instance?**

De opslag grootte voor het SQL Managed instance is afhankelijk van de geselecteerde servicelaag (Algemeen of Bedrijfskritiek). Zie [kenmerken](../database/service-tiers-general-purpose-business-critical.md)van de servicelaag voor opslag beperkingen van deze service lagen.

**Wat is de minimale opslag grootte die beschikbaar is voor een beheerd exemplaar?**

De minimale hoeveelheid beschik bare opslag ruimte in een exemplaar is 32 GB. Opslag kan worden toegevoegd in stappen van 32 GB tot aan de maximale opslag grootte. De eerste 32 GB zijn gratis.

**Kan ik de opslag ruimte die is toegewezen aan een exemplaar, onafhankelijk van reken bronnen verg Roten?**

Ja, u kunt extra opslag aanschaffen, onafhankelijk van compute, tot op zekere hoogte. Bekijk de *maximale instantie gereserveerde opslag* in de [tabel](resource-limits.md#hardware-generation-characteristics).

**Hoe kan ik mijn opslag prestaties in Algemeen servicelaag optimaliseren?**

Zie [Aanbevolen procedures voor opslag in algemeen](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)voor het optimaliseren van opslag prestaties.

## <a name="backup-and-restore"></a>Back-ups en herstellen

**Wordt de back-upopslag afgetrokken van mijn beheerde exemplaar opslag?**

Nee, back-upopslag wordt niet afgetrokken van de opslag ruimte voor uw beheerde exemplaar. De back-upopslag is onafhankelijk van de opslag ruimte van het exemplaar en is niet beperkt. Back-upopslag wordt beperkt door de tijds periode voor het bewaren van de back-up van uw exemplaar databases en kan Maxi maal 35 dagen worden geconfigureerd. Zie [automatische back-ups](../database/automated-backups-overview.md)voor meer informatie.

**Hoe kan ik zien wanneer automatische back-ups worden gemaakt op mijn beheerde exemplaar?**

Zie [de geautomatiseerde back-up voor een Azure SQL Managed instance volgen](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-128-how-to-track-the-automated-backup-for-an/ba-p/1442355)om bij te houden wanneer geautomatiseerde back-ups zijn uitgevoerd op een beheerd exemplaar.

**Wordt back-up op aanvraag ondersteund?**

Ja, u kunt een volledige back-up in hun Azure-Blob Storage maken, maar deze kan alleen worden vrijgemaakt in een beheerd exemplaar. Zie [kopie-only backup](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15)voor meer informatie. Alleen back-ups kopiëren is niet mogelijk als de data base is versleuteld met TDE die door de service worden beheerd, omdat het certificaat dat voor versleuteling wordt gebruikt, niet toegankelijk is. In dat geval kunt u de functie voor het herstellen van een punt gebruiken om de data base te verplaatsen naar een ander SQL-beheerd exemplaar of over te scha kelen naar een door de klant beheerde sleutel.

**Wordt systeem eigen herstel (van. bak-bestanden) naar een beheerd exemplaar ondersteund?**

Ja, het wordt ondersteund en is beschikbaar voor SQL Server 2005 en versies.  Als u systeem eigen herstel wilt gebruiken, uploadt u uw. bak-bestand naar Azure Blob-opslag en voert u T-SQL-opdrachten uit. Zie [systeem eigen herstel van URL](./migrate-to-instance-from-sql-server.md#native-restore-from-url)voor meer informatie.

## <a name="business-continuity"></a>Bedrijfscontinuïteit

**Worden mijn systeem databases gerepliceerd naar het secundaire exemplaar in een failover-groep?**

Systeem databases worden niet gerepliceerd naar het secundaire exemplaar in een failovergroep. Daarom zijn scenario's die afhankelijk zijn van objecten van de systeem databases niet mogelijk op het secundaire exemplaar, tenzij de objecten hand matig op de secundaire instantie worden gemaakt. Zie scenario's die afhankelijk zijn van [het object van de systeem databases inschakelen](../database/auto-failover-group-overview.md?tabs=azure-powershell#enable-scenarios-dependent-on-objects-from-the-system-databases)voor tijdelijke oplossingen.
 
## <a name="networking-requirements"></a>Netwerkvereisten 

**Wat zijn de huidige binnenkomende/uitgaande NSG-beperkingen op het subnet van het beheerde exemplaar?**

De vereiste NSG-en UDR-regels worden [hier](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)gedocumenteerd en automatisch ingesteld door de service.
Houd er rekening mee dat deze regels alleen de voor waarden hebben die we nodig hebben om de service te onderhouden. Als u verbinding wilt maken met een beheerd exemplaar en andere functies wilt gebruiken, moet u aanvullende, specifieke regels voor de functie opgeven die u wilt behouden.

**Hoe kan ik binnenkomende NSG-regels instellen voor beheer poorten?**

SQL Managed instance is verantwoordelijk voor het instellen van regels voor beheer poorten. Dit wordt bereikt via de functionaliteit met de naam [service-aided subnet-configuratie](connectivity-architecture-overview.md#service-aided-subnet-configuration).
Dit is om te zorgen voor een ononderbroken stroom van beheer verkeer om te voldoen aan een SLA.

**Kan ik de bron-IP-adresbereiken ophalen die worden gebruikt voor het inkomende beheer verkeer?**

Ja. U kunt het verkeer dat afkomstig is van uw netwerk beveiligings groep analyseren door [Network Watcher flow logboeken te configureren](../../network-watcher/network-watcher-monitoring-overview.md#analyze-traffic-to-or-from-a-network-security-group).

**Kan ik NSG instellen om de toegang tot het gegevens eindpunt te beheren (poort 1433)?**

Ja. Nadat een beheerd exemplaar is ingericht, kunt u NSG instellen die de toegang tot de poort 1433 beheren. Het is raadzaam het IP-bereik zo veel mogelijk te beperken.

**Kan ik de NVA of on-premises firewall zo instellen dat het uitgaande beheer verkeer wordt gefilterd op basis van FQDN-namen?**

Nee. Dit kan om verschillende redenen niet worden ondersteund:
-   Routerings verkeer dat antwoord geeft op inkomende beheer aanvragen, is asymmetrisch en kan niet worden gebruikt.
-   Route ring van verkeer dat naar de opslag gaat, wordt beïnvloed door doorvoer beperkingen en latentie, zodat we de verwachte kwaliteit en beschik baarheid van de service niet kunnen leveren.
-   Op basis van de ervaring zijn deze configuraties gevoelig en niet-ondersteund.

**Kan ik de NVA of firewall instellen voor het uitgaande niet-beheer verkeer?**

Ja. De eenvoudigste manier om dit te doen is door 0/0-regel toe te voegen aan een UDR die is gekoppeld aan het subnet van het beheerde exemplaar om verkeer door te sturen via NVA.
 
**Hoeveel IP-adressen heb ik nodig voor een beheerd exemplaar?**

Het subnet moet voldoende beschik bare [IP-adressen](connectivity-architecture-overview.md#network-requirements)hebben. Zie de [vereiste subnet grootte en het bereik voor een beheerd exemplaar bepalen](./vnet-subnet-determine-size.md)om de VNet-subnet grootte voor het beheerde exemplaar van SQL te bepalen. 

**Wat gebeurt er als er onvoldoende IP-adressen zijn voor het uitvoeren van een update bewerking voor een instantie?**

Als er onvoldoende [IP-adressen](connectivity-architecture-overview.md#network-requirements) zijn in het subnet waar uw beheerde exemplaar is ingericht, moet u een nieuw subnet en een nieuw beheerd exemplaar maken. Het wordt ook aanbevolen het nieuwe subnet te maken met meer toegewezen IP-adressen zodat toekomstige updatebewerkingen dergelijke situaties voorkomen. Nadat het nieuwe exemplaar is ingericht, kunt u hand matig een back-up maken van gegevens en deze herstellen tussen de oude en nieuwe instanties of het [herstel punt in de tijd](point-in-time-restore.md?tabs=azure-powershell)van meerdere exemplaren uitvoeren.

**Heb ik een leeg subnet nodig om een beheerd exemplaar te maken?**

Nee. U kunt een leeg subnet of een subnet gebruiken dat al een beheerd exemplaar (en) bevat. 

**Kan ik het adres bereik van het subnet wijzigen?**

Niet als er beheerde exemplaren in zijn. Dit is een beperking van de Azure-netwerk infrastructuur. U mag alleen [extra adres ruimte toevoegen aan een leeg subnet](../../virtual-network/virtual-network-manage-subnet.md#change-subnet-settings). 

**Kan ik mijn beheerde exemplaar verplaatsen naar een ander subnet?**

Nee. Dit is een huidig ontwerp beperking voor het beheerde exemplaar. U kunt echter een nieuw exemplaar in een ander subnet inrichten en hand matig back-ups maken en gegevens herstellen tussen het oude en het nieuwe exemplaar, of een [herstel punt voor meerdere tijdstippen](point-in-time-restore.md?tabs=azure-powershell)uitvoeren.

**Heb ik een leeg virtueel netwerk nodig om een beheerd exemplaar te maken?**

Dit is niet vereist. U kunt ofwel [een virtueel netwerk maken voor Azure SQL Managed instance](./virtual-network-subnet-create-arm-template.md) of [een bestaand virtueel netwerk configureren voor Azure SQL Managed instance](./vnet-existing-add-subnet.md).

**Kan ik een beheerd exemplaar met andere services in een subnet plaatsen?**

Nee. Momenteel wordt het plaatsen van beheerde exemplaren in een subnet dat al andere bron typen bevat, niet ondersteund.

## <a name="connectivity"></a>Connectiviteit 

**Kan ik verbinding maken met mijn beheerde exemplaar met behulp van een IP-adres?**

Nee, dit wordt niet ondersteund. De hostnaam van een beheerd exemplaar wordt toegewezen aan de load balancer vóór het virtuele cluster van het beheerde exemplaar. Als één virtueel cluster meerdere beheerde exemplaren kan hosten, kan er geen verbinding worden doorgestuurd naar het juiste beheerde exemplaar zonder de naam op te geven.
Zie [connectiviteits architectuur voor virtuele clusters](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)voor meer informatie over de architectuur van een virtueel cluster voor SQL-beheerde exemplaren.

**Kan mijn beheerde exemplaar een statisch IP-adres hebben?**

Dit wordt momenteel niet ondersteund.

In zeldzame maar nood zakelijke situaties moet u mogelijk een online migratie van een beheerd exemplaar uitvoeren naar een nieuw virtueel cluster. Als dat nodig is, wordt deze migratie veroorzaakt door wijzigingen in onze technologie stack, die gericht is op het verbeteren van de beveiliging en betrouw baarheid van de service. Migreren naar een nieuw virtueel cluster resulteert in het wijzigen van het IP-adres dat is toegewezen aan de hostnaam van het beheerde exemplaar. De service Managed instance claimt geen ondersteuning voor statische IP-adressen en behoudt zich het recht voor om deze te wijzigen zonder kennisgeving als onderdeel van normale onderhouds cycli.

Daarom raden wij u ten zeerste aan om te vertrouwen op Onveranderbaarheid van het IP-adres, omdat dit onnodig downtime kan veroorzaken.

**Heeft een beheerd exemplaar een openbaar eind punt?**

Ja. Het beheerde exemplaar heeft een openbaar eind punt dat standaard alleen wordt gebruikt voor Service beheer, maar een klant kan dit ook inschakelen voor toegang tot gegevens. Zie [SQL Managed instance gebruiken met open bare eind punten](./public-endpoint-overview.md)voor meer informatie. Als u een openbaar eind punt wilt configureren, gaat u naar het [configureren van het open bare eind punt in SQL Managed instance](public-endpoint-configure.md)

**Hoe beheert Managed instance de toegang tot het open bare eind punt?**

Beheerd exemplaar beheert de toegang tot het open bare eind punt op het niveau van het netwerk en de toepassing.

Beheer-en implementatie services maken verbinding met een beheerd exemplaar met behulp van een [beheer eindpunt](./connectivity-architecture-overview.md#management-endpoint) dat is toegewezen aan een externe Load Balancer. Verkeer wordt alleen gerouteerd naar de knoop punten als het wordt ontvangen op een vooraf gedefinieerde set poorten die alleen worden gebruikt door de beheer onderdelen van het beheerde exemplaar. Een ingebouwde firewall op de knoop punten is ingesteld om alleen verkeer vanaf micro soft IP-bereiken toe te staan. Certificaten verifiëren wederzijds alle communicatie tussen beheer onderdelen en het beheer vlak. Zie [connectiviteits architectuur voor SQL Managed instance](./connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)voor meer informatie.

**Kan ik het open bare eind punt gebruiken om toegang te krijgen tot de gegevens in de data bases van het beheerde exemplaar?**

Ja. De klant moet toegang tot open bare eindpunt gegevens inschakelen vanuit [Azure Portal](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal)  /  [Power shell](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) /arm en NSG configureren om de toegang tot de gegevens poort (poort nummer 3342) te vergren delen. Zie voor meer informatie het [configureren van een openbaar eind punt in Azure SQL Managed instance](public-endpoint-configure.md) en het [gebruik van Azure SQL Managed instance veilig met een openbaar eind punt](public-endpoint-overview.md). 

**Kan ik een aangepaste poort opgeven voor SQL data endpoint (s)?**

Nee, deze optie is niet beschikbaar.  Voor het eind punt van een persoonlijke gegevens gebruikt het beheerde exemplaar het standaard poort nummer 1433 en voor het open bare gegevens eindpunt. het beheerde exemplaar gebruikt standaard poort nummer 3342.

**Wat is de aanbevolen manier om een verbinding te maken tussen beheerde instanties die in verschillende regio's zijn geplaatst?**

De peering voor Express route-circuits is de beste manier om dat te doen. Globale Virtual Network-peering wordt ondersteund met de beperking die wordt beschreven in de onderstaande opmerking.  

> [!IMPORTANT]
> [Op 9/22/2020 zijn wereld wijde virtuele netwerk peering aangekondigd voor nieuwe virtuele clusters](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Dit betekent dat de peering van globale virtuele netwerken wordt ondersteund voor SQL-beheerde instanties die zijn gemaakt in lege subnetten na de aankondigings datum, en ook voor alle daaropvolgende beheerde exemplaren die in deze subnetten zijn gemaakt. Voor alle andere SQL Managed instances-ondersteuning voor peering is beperkt tot de netwerken in dezelfde regio vanwege de [beperkingen van globale virtuele netwerk peering](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Zie ook de relevante sectie van het artikel Veelgestelde [vragen over virtuele netwerken van Azure](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) voor meer informatie. 

Als multi route-Circuit peering en globale virtuele netwerk peering niet mogelijk is, is de enige andere optie het maken van een site-naar-site-VPN-verbinding ([Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md), [Power shell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure cli](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)).

## <a name="mitigate-data-exfiltration-risks"></a>Gegevens exfiltration Risico's beperken  

**Hoe kan ik de gegevens exfiltration Risico's beperken?**

Klanten wordt aangeraden een aantal beveiligings instellingen en-besturings elementen toe te passen om eventuele gegevens exfiltration Risico's te beperken:

- Schakel [transparent Data Encryption (TDE)](../database/transparent-data-encryption-tde-overview.md) in voor alle data bases.
- Common language runtime (CLR) uitschakelen. Dit wordt ook aanbevolen voor on-premises.
- Gebruik alleen Azure Active Directory-verificatie (Azure AD).
- Toegang tot het exemplaar met een account met beperkte bevoegdheden.
- Configureer JIT JumpBox-toegang voor het account sysadmin.
- Schakel [SQL auditing](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)in en integreer deze met waarschuwings mechanismen.
- Schakel de [detectie van bedreigingen](../database/threat-detection-configure.md) in van de [Azure Defender voor SQL](../database/azure-defender-for-sql.md) -Suite.

## <a name="dns"></a>DNS

**Kan ik een aangepaste DNS voor een SQL-beheerd exemplaar configureren?**

Ja. Zie [How to configure a Custom DNS for Azure SQL Managed instance](./custom-dns-configure.md)(Engelstalig).

**Kan ik DNS vernieuwen?**

Ja. Zie de [instelling DNS-servers voor het virtuele netwerk synchroniseren op het virtuele cluster van SQL Managed instance](./synchronize-vnet-dns-servers-setting-on-virtual-cluster.md).

## <a name="change-time-zone"></a>Tijd zone wijzigen

**Kan ik de tijd zone wijzigen voor een bestaande beheerde instantie?**

De configuratie van de tijd zone kan worden ingesteld wanneer een beheerd exemplaar voor de eerste keer wordt ingericht. Het wijzigen van de tijd zone van een bestaand beheerd exemplaar wordt niet ondersteund. Zie [beperkingen voor tijd zones](timezones-overview.md#limitations)voor meer informatie.

Tijdelijke oplossingen zijn onder andere het maken van een nieuw beheerd exemplaar met de juiste tijd zone en vervolgens het uitvoeren van een hand matige back-up en herstel bewerking, of voor de aanbevolen procedure voor het uitvoeren [van een cross-instance-in-time herstel bewerking](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance).


## <a name="security-and-database-encryption"></a>Beveiliging en database versleuteling

**Is de serverrol sysadmin beschikbaar voor SQL Managed instance?**

Ja, klanten kunnen aanmeldingen maken die lid zijn van de rol sysadmin.  Klanten die ervan uitgaan dat de sysadmin-bevoegdheid ook verantwoordelijk is voor de werking van het exemplaar, waardoor de SLA-toezeg ging nadelig kan worden beïnvloed. Zie [Azure AD-verificatie](./aad-security-configure-tutorial.md#azure-ad-authentication)voor het toevoegen van aanmelding aan de sysadmin-server functie.

**Wordt Transparent Data Encryption ondersteund voor SQL Managed instance?**

Ja, Transparent Data Encryption wordt ondersteund voor SQL Managed instance. Zie [transparent Data Encryption voor SQL Managed instance](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal)(Engelstalig) voor meer informatie.

**Kan ik gebruikmaken van het model ' uw eigen sleutel meenemen ' voor TDE?**

Ja, Azure Key Vault voor het BYOK-scenario is beschikbaar voor Azure SQL Managed instance. Zie [transparent Data Encryption met door de klant beheerde sleutel](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key)voor meer informatie.

**Kan ik een versleutelde SQL Server-Data Base migreren?**

Ja, dat kunt u. Als u een versleutelde SQL Server-Data Base wilt migreren, moet u uw bestaande certificaten exporteren en importeren naar een beheerd exemplaar, vervolgens een volledige back-up van de data base maken en deze herstellen in een beheerd exemplaar. 

U kunt ook [Azure database Migration service](https://azure.microsoft.com/services/database-migration/) gebruiken om de versleutelde TDe-data bases te migreren.

**Hoe kan ik de rotatie van TDE-beveiliging voor een SQL Managed instance configureren?**

U kunt TDE-Protector voor een beheerd exemplaar draaien met behulp van Azure Cloud Shell. Zie [transparent Data Encryption in SQL Managed instance met uw eigen sleutel vanuit Azure Key Vault](scripts/transparent-data-encryption-byok-powershell.md)voor instructies.

**Kan ik mijn versleutelde data base herstellen naar een SQL Managed instance?**

Ja, u hoeft uw data base niet te ontsleutelen om deze te herstellen naar een SQL-beheerd exemplaar. U moet een certificaat/sleutel opgeven die wordt gebruikt als de versleutelings sleutel beveiliging op het bron systeem naar SQL Managed instance om gegevens van het versleutelde back-upbestand te kunnen lezen. Er zijn twee manieren waarop u dit kunt doen:

- *Upload certificaat beveiliging naar een beheerd exemplaar van SQL*. Dit kan alleen worden gedaan met behulp van Power shell. In het [voorbeeld script](./tde-certificate-migrate.md) wordt het hele proces beschreven.
- De *asymmetrische sleutel beveiliging uploaden naar Azure Key Vault en naar de SQL-beheerde instantie verwijzen*. Deze benadering lijkt op het gebruik van uw BYOK-TDE-toepassing (your-own-Key) die ook Key Vault-integratie gebruikt om de versleutelings sleutel op te slaan. Als u de sleutel niet wilt gebruiken als een versleutelings sleutel beveiliging en alleen de sleutel beschikbaar wilt maken voor SQL Managed instance om versleutelde data bases te herstellen, volgt u de instructies voor het [instellen van BYOK TDe](../database/transparent-data-encryption-tde-overview.md#manage-transparent-data-encryption)en schakelt u het selectie vakje de **geselecteerde sleutel de standaard TDe-Protector maken** in.

Zodra u de versleutelings beveiliging beschikbaar maakt voor een SQL-beheerd exemplaar, kunt u door gaan met de standaard procedure voor het herstellen van data bases.

## <a name="purchasing-models-and-benefits"></a>Modellen en voor delen kopen

**Welke inkoop modellen zijn er beschikbaar voor SQL Managed instance?**

SQL Managed instance biedt [vCore-gebaseerd aankoop model](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model).

**Welke kosten voordelen zijn beschikbaar voor SQL Managed instance?**

U kunt kosten besparen met de voor delen van Azure SQL op de volgende manieren:
-   Maximaliseer bestaande investeringen in on-premises licenties en Bespaar tot 55 procent met [Azure Hybrid Benefit](../azure-hybrid-benefit.md?tabs=azure-powershell). 
-   Leg een reserve ring toe voor reken resources en Bespaar tot 33 procent met een [gereserveerde instantie voordelen](../database/reserved-capacity-overview.md). Combi neer dit met Azure Hybrid voordelen voor een besparing van Maxi maal 82 procent. 
-   Bespaar tot 55 procent tegenover lijst prijzen met de [prijs voordelen van Azure dev/test](https://azure.microsoft.com/pricing/dev-test/) , die kortings tarieven bieden voor uw lopende ontwikkelings-en test werkbelastingen.

**Wie komt in aanmerking voor het voor deel van een gereserveerde instantie?**

Als u in aanmerking wilt komen voor een gereserveerde instantie, moet uw abonnements type een Enter prise Agreement zijn (nummer van de aanbieding: MS-AZR-0017P of MS-AZR-0148P) of een afzonderlijke overeenkomst met betalen per gebruik-prijs (aanbiedings nummers: MS-AZR-0003P of MS-AZR-0023P). Zie voor [delen van gereserveerde instanties](../database/reserved-capacity-overview.md)voor meer informatie over reserve ringen. 

**Is het mogelijk om reserve ringen te annuleren, te ruilen of terug te betalen?**

U kunt reserve ringen annuleren, uitwisselen of terugbetalen met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) voor meer informatie.

## <a name="billing-for-managed-instance-and-backup-storage"></a>Facturering voor beheerd exemplaar en back-upopslag

**Wat zijn de prijs opties voor SQL Managed instance?**

Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)als u de opties voor het Managed instance-exemplaar wilt verkennen.

**Hoe kan ik de facturerings kosten voor mijn beheerde exemplaar volgen?**

U kunt dit doen met behulp van de [Azure Cost Management-oplossing](../../cost-management-billing/index.yml). Navigeer naar **abonnementen** in het [Azure Portal](https://portal.azure.com) en selecteer **kosten analyse**. 

Gebruik de optie **geaccumuleerde kosten** en filter vervolgens op het **resource type** als `microsoft.sql/managedinstances` .

**Hoeveel automatische back-ups worden kosten?**

U krijgt de gelijke hoeveelheid beschik bare opslag ruimte voor back-ups als de gereserveerde opslag ruimte die is aangeschaft, ongeacht de Bewaar periode van de back-up. Als het verbruik van de back-upopslag binnen de toegewezen vrije back-upopslag ruimte ligt, zullen automatische back-ups op het beheerde exemplaar geen extra kosten voor u hebben, dus gratis zijn. Als u het gebruik van back-upopslag boven de beschik bare ruimte overschrijdt, worden de kosten van $0,20 tot $0,24 per GB/maand in Amerikaanse regio's, of raadpleegt u de pagina met prijzen voor meer informatie over uw regio. Zie [back-upopslag verbruik wordt uitgelegd](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923)voor meer informatie.

**Hoe kan ik de facturerings kosten voor mijn back-upopslags volgen?**

U kunt de kosten voor back-upopslag bewaken via Azure Portal. Zie [kosten bewaken voor automatische back-ups](../database/automated-backups-overview.md?tabs=managed-instance#monitor-costs)voor instructies. 

**Hoe kan ik mijn back-upopslagkosten op het beheerde exemplaar optimaliseren?**

Zie [fijnafstelling van nauw keurigheid van SQL Managed instance](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935)voor meer informatie over het optimaliseren van de opslag kosten voor back-ups.

## <a name="cost-saving-use-cases"></a>Gebruiks voorbeelden voor kosten besparing

**Waar vind ik use cases en resulterende kosten besparingen met SQL Managed instance?**

Case-study's voor SQL Managed instance:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

Als u een beter inzicht wilt krijgen in de voor delen, kosten en risico's die zijn gekoppeld aan het implementeren van Azure SQL Managed instance, is er ook een Forrester-studie: [de totale economische impact van Microsoft Azure SQL database Managed instance](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).

## <a name="password-policy"></a>Wachtwoord beleid 

**Welke beleids regels voor wacht woorden worden toegepast op SQL-aanmeldingen voor SQL Managed instance?**

Het wachtwoord beleid voor SQL-beheerde exemplaren voor SQL-aanmeldingen neemt het Azure-platform beleid over dat wordt toegepast op de virtuele machines die het beheerde exemplaar hebben. Op het moment is het niet mogelijk om een van deze instellingen te wijzigen, omdat deze instellingen worden gedefinieerd door Azure en worden overgenomen door een beheerd exemplaar.

 > [!IMPORTANT]
 > Het Azure-platform kan beleids vereisten wijzigen zonder dat u op de hoogte hoeft te zijn van de services die op dat beleid zijn gebaseerd.

**Wat is het huidige Azure-platform beleid?**

Bij elke aanmelding moet het wacht woord bij aanmelding worden ingesteld en het bijbehorende wacht woord worden gewijzigd nadat het maximum aantal leeftijden is bereikt.

| **Beleid** | **Beveiligingsinstelling** |
| --- | --- |
| Maximale gebruiksduur wachtwoord | 42 dagen |
| Minimale gebruiksduur wachtwoord | 1 dag |
| Minimale wachtwoordlengte | 10 tekens |
| Wachtwoord moet voldoen aan complexiteitsvereisten | Ingeschakeld |

**Is het mogelijk om wachtwoord complexiteit en verloop tijd in SQL Managed instance op aanmeldings niveau uit te scha kelen?**

Ja, u kunt CHECK_POLICY en CHECK_EXPIRATION velden op aanmeldings niveau beheren. U kunt de huidige instellingen controleren door de volgende T-SQL-opdracht uit te voeren:

```sql
SELECT *
FROM sys.sql_logins
```

Daarna kunt u de opgegeven aanmeldings instellingen wijzigen door het volgende uit te voeren:

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(Vervang ' test ' door de gewenste aanmeldings naam en pas het beleid en de verval waarden aan)


## <a name="service-updates"></a>Service-updates

**Wat is de wijziging van de basis-CA voor Azure SQL Database & SQL Managed instance?**

Zie de [draaiing van certificaten voor Azure SQL Database & SQL Managed instance](../updates/ssl-root-certificate-expiring.md). 

**Wat is een gepland onderhouds gebeurtenis voor een SQL Managed instance?**

Zie [Azure-onderhouds gebeurtenissen plannen in SQL Managed instance](../database/planned-maintenance.md). 


## <a name="azure-feedback-and-support"></a>Feedback en ondersteuning van Azure

**Waar kan ik mijn ideeën voor verbeteringen van SQL Managed instance verlaten?**

U kunt stemmen voor een nieuwe functie van een beheerd exemplaar of een nieuw idee voor verbetering nemen over stemmen op het [Feedback forum van SQL Managed instance](https://feedback.azure.com/forums/915676-sql-managed-instance). Op deze manier kunt u bijdragen aan de ontwikkeling van het product en ons helpen om onze mogelijke verbeteringen te bepalen.

**Hoe kan ik een ondersteunings aanvraag voor Azure maken?**

Zie [Azure-ondersteunings aanvraag maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie over het maken van een ondersteunings aanvraag voor Azure.
