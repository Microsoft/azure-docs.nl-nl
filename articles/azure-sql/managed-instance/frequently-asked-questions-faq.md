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
ms.openlocfilehash: 16f937286b967aaea8ec6a16e97835b2de5a0331
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765493"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Veelgestelde vragen over Azure SQL Managed Instance (FAQ)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Dit artikel bevat de meest voorkomende vragen over [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Ondersteunde functies

**Waar vind ik een lijst met functies die worden ondersteund op SQL Managed Instance?**

Zie Voor een lijst met ondersteunde functies in SQL Managed Instance, [Azure SQL Managed Instance functies.](../database/features-comparison.md)

Zie T-SQL differences from [SQL Server (T-SQL-verschillen](transact-sql-tsql-differences-sql-server.md)met SQL Server) voor verschillen in syntaxis en gedrag tussen Azure SQL Managed Instance en SQL Server.


## <a name="technical-specification-resource-limits-and-other-limitations"></a>Technische specificatie, resourcelimieten en andere beperkingen
 
**Waar vind ik technische kenmerken en resourcelimieten voor SQL Managed Instance?**

Zie Technische verschillen in hardwaregeneraties voor beschikbare kenmerken van [hardwaregeneratie.](resource-limits.md#hardware-generation-characteristics)
Zie Technische verschillen tussen servicelagen voor beschikbare servicelagen en hun [kenmerken.](resource-limits.md#service-tier-characteristics)

**Voor welke servicelaag kom ik in aanmerking?**

Elke klant komt in aanmerking voor elke servicelaag. Als u uw bestaande licenties echter wilt inruilen tegen kortingstarieven op Azure SQL Managed Instance met behulp van [Azure Hybrid Benefit,](https://azure.microsoft.com/pricing/hybrid-benefit/)moet u er rekening mee houden dat SQL Server Enterprise Edition-klanten met Software Assurance in aanmerking komen voor de [Algemeen-](../database/service-tier-general-purpose.md) of [Bedrijfskritiek-prestatielagen](../database/service-tier-business-critical.md) en dat SQL Server Standard Edition-klanten met Software Assurance alleen in aanmerking komen voor de Algemeen-prestatielaag. Zie Specifieke rechten van de [AHB voor meer informatie.](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server)

**Welke abonnementstypen worden ondersteund voor SQL Managed Instance?**

Zie Ondersteunde abonnementstypen voor de lijst [met ondersteunde abonnementstypen.](resource-limits.md#supported-subscription-types) 

**Welke Azure-regio's worden ondersteund?**

Beheerde exemplaren kunnen worden gemaakt in de meeste Azure-regio's; Zie [Ondersteunde regio's voor SQL Managed Instance](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Als u een beheerd exemplaar nodig hebt in een regio die momenteel niet wordt ondersteund, verzendt u een [ondersteuningsaanvraag via de Azure Portal](../database/quota-increase-request.md).

**Zijn er quotumbeperkingen voor SQL Managed Instance implementaties?**

Het beheerde exemplaar heeft twee standaardlimieten: limiet voor het aantal subnetten dat u kunt gebruiken en een limiet voor het aantal vCores dat u kunt inrichten. Limieten variëren per abonnementstype en per regio. Zie de tabel in Beperking van regionale resources voor de lijst met regionale resourcebeperkingen per [abonnementstype.](resource-limits.md#regional-resource-limitations) Dit zijn zachte limieten die op aanvraag kunnen worden verhoogd. Als u meer beheerde exemplaren in uw huidige regio's wilt inrichten, stuurt u een ondersteuningsaanvraag om het quotum te verhogen met behulp van Azure Portal. Zie Verhogingen van [quotums aanvragen voor Azure SQL Database.](../database/quota-increase-request.md)

**Kan ik het aantal databases (100) op mijn beheerde exemplaar op aanvraag verhogen?**

Nee, en er zijn momenteel geen vastgelegde plannen om het aantal databases op SQL Managed Instance. 

**Waar kan ik migreren als ik meer dan 8 TB aan gegevens heb?**
U kunt overwegen om te migreren naar andere Azure-varianten die geschikt zijn voor uw workload: [Azure SQL Database Hyperscale](../database/service-tier-hyperscale.md) of [SQL Server op Azure Virtual Machines.](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

**Waar kan ik migreren als ik specifieke hardwarevereisten heb, zoals een grotere RAM-naar-vCore-verhouding of meer CPU's?**
U kunt overwegen om te migreren naar [SQL Server azure-Virtual Machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) of [Azure SQL Database](../database/sql-database-paas-overview.md) geheugen/cpu geoptimaliseerd.

## <a name="known-issues-and-defects"></a>Bekende problemen en defecten

**Waar vind ik bekende problemen en defecten?**

Zie Bekende problemen voor productdefecten [en bekende problemen.](../database/doc-changes-updates-release-notes.md#known-issues)

## <a name="new-features"></a>Nieuwe functies

**Waar vind ik de nieuwste functies en de functies in de openbare preview?**

Zie Release notes (Opmerkingen bij de release) [voor nieuwe functies en preview-functies.](../database/doc-changes-updates-release-notes.md?tabs=managed-instance)

## <a name="create-update-delete-or-move-sql-managed-instance"></a>Een app maken, bijwerken, verwijderen of SQL Managed Instance

**Hoe kan ik SQL Managed Instance?**

U kunt een exemplaar inrichten [vanuit Azure Portal,](instance-create-quickstart.md) [PowerShell,](scripts/create-configure-managed-instance-powershell.md) [Azure CLI](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281) en [ARM-sjablonen.](/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates)

**Kan ik beheerde exemplaren inrichten in een bestaand abonnement?**

Ja, u kunt een beheerd exemplaar inrichten in een bestaand abonnement als dat abonnement tot de [ondersteunde abonnementstypen behoort.](resource-limits.md#supported-subscription-types)

**Waarom kan ik geen beheerd exemplaar inrichten in het subnet, welke naam begint met een cijfer?**

Dit is een huidige beperking voor het onderliggende onderdeel dat subnetnaam verifieert op basis van de regex ^[a-zA-Z_][^ \\ \/ \: \* \? \" \<\> \| \` \' \^ ]*(?<![ \. \s])$. Alle namen die de regex doorgeven en geldige subnetnamen zijn, worden momenteel ondersteund.

**Hoe kan ik mijn beheerde exemplaar schalen?**

U kunt uw beheerde exemplaar schalen [vanuit Azure Portal,](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation) [PowerShell,](/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell) [Azure CLI](/cli/azure/sql/mi#az_sql_mi_update) of [ARM-sjablonen.](/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates)

**Kan ik mijn beheerde exemplaar van de ene regio naar de andere verplaatsen?**

Ja, dat kunt u. Zie Resources verplaatsen tussen [regio's voor instructies.](../database/move-resources-across-regions.md)

**Hoe kan ik mijn beheerde exemplaar verwijderen?**

U kunt beheerde exemplaren verwijderen via Azure Portal, [PowerShell,](/powershell/module/az.sql/remove-azsqlinstance) [Azure CLI](/cli/azure/sql/mi#az_sql_mi_delete) of [Resource Manager REST API's.](/rest/api/sql/managedinstances/delete)

**Hoeveel tijd kost het om een exemplaar te maken of bij te werken of om een database te herstellen?**

De verwachte tijd voor het maken van een nieuw beheerd exemplaar of het wijzigen van servicelagen (vCores, opslag), is afhankelijk van verschillende factoren. Zie [Beheerbewerkingen.](sql-managed-instance-paas-overview.md#management-operations)
 
## <a name="naming-conventions"></a>Naamconventies

**Kan een beheerd exemplaar dezelfde naam hebben als een SQL Server on-premises exemplaar?**

Het wijzigen van de naam van een beheerd exemplaar wordt niet ondersteund.

**Kan ik het voorvoegsel van de DNS-zone wijzigen?**

Ja, de standaard-DNS-zone *van database.windows.net* managed instance kan worden gewijzigd. 

Als u een andere DNS-zone wilt gebruiken in plaats van de standaardwaarde, bijvoorbeeld *. contoso.com*: 
- Gebruik CliConfig om een alias te definiëren. Het hulpprogramma is slechts een wrapper voor registerinstellingen, dus het kan ook worden uitgevoerd met behulp van groepsbeleid of een script.
- Gebruik *CNAME* met de *optie TrustServerCertificate=true.*

## <a name="migration-options"></a>Migratieopties

**Hoe kan ik migreren van Azure SQL Database individuele of elastische pool naar SQL Managed Instance?**

Managed Instance biedt dezelfde prestatieniveaus per reken- en opslaggrootte als andere implementatieopties van Azure SQL Database. Als u gegevens op één exemplaar wilt consolideren of als u alleen een functie nodig hebt die uitsluitend wordt ondersteund in een beheerd exemplaar, kunt u uw gegevens migreren met behulp van de functionaliteit voor exporteren/importeren (BACPAC). Hier zijn andere manieren om een migratie naar SQL Database te SQL Managed Instance: 
- Gegevensbron [extern gebruiken](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-129-using-data-source-external-from-azure-sql/ba-p/1443210)
- [SQLPackage gebruiken](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-migrate-azure-sql-database-to-azure-sql-managed-instance/ba-p/369182)
- [BCP gebruiken](https://medium.com/azure-sqldb-managed-instance/migrate-from-azure-sql-managed-instance-using-bcp-674c92efdca7)

**Hoe kan ik mijn exemplaardatabase migreren naar één Azure SQL Database?**

U kunt een [database exporteren naar BACPAC en](../database/database-export.md) vervolgens het [BACPAC-bestand importeren.](../database/database-import.md) Dit is de aanbevolen methode als uw database kleiner is dan 100 GB.

[Transactionele replicatie](replication-two-instances-and-sql-server-configure-tutorial.md) kan worden gebruikt als alle tabellen in de database *primaire* sleutels hebben en er geen IN-memory OLTP-objecten in de database zijn.

Systeemeigen COPY_ONLY back-ups van beheerde exemplaren kunnen niet worden hersteld naar SQL Server omdat het beheerde exemplaar een hogere databaseversie heeft dan SQL Server. Zie Alleen-kopiëren [back-up voor meer informatie.](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15)

**Hoe kan ik mijn exemplaar SQL Server migreren naar SQL Managed Instance?**

Als u uw SQL Server wilt migreren, gaat [u naar SQL Server exemplaarmigratie naar Azure SQL Managed Instance](migrate-to-instance-from-sql-server.md).

**Hoe kan ik migreren van andere platforms naar SQL Managed Instance?**

Zie Azure Database Migration Guide (Handleiding voor databasemigratie) voor [migratie-informatie over](https://datamigration.microsoft.com/)migratie vanaf andere platforms.

## <a name="switch-hardware-generation"></a>Hardwaregeneratie van switch 

**Kan ik online overschakelen van de hardwaregeneratie van mijn beheerde exemplaar tussen Gen 4 en Gen 5?**

Automatische onlineschakeling van Gen4 naar Gen5 is mogelijk als Gen5-hardware beschikbaar is in de regio waar uw beheerde exemplaar is ingericht. In dit geval kunt u de overzichtspagina van [het vCore-model bekijken](../database/service-tiers-vcore.md) waarin wordt uitgelegd hoe u kunt schakelen tussen hardware-generaties.

Dit is een langdurige bewerking omdat een nieuw beheerd exemplaar op de achtergrond wordt ingericht en databases automatisch worden overgedragen tussen het oude en het nieuwe exemplaar met een snelle failover aan het einde van het proces.

Opmerking: Gen4-hardware wordt geleidelijk buiten gebruik gesteld en is niet meer beschikbaar voor nieuwe implementaties. Alle nieuwe databases moeten worden geïmplementeerd op Gen5-hardware. Overstappen van Gen5 naar Gen4 is ook niet beschikbaar.

## <a name="performance"></a>Prestaties 

**Hoe kan ik de prestaties van managed instances vergelijken met SQL Server prestaties?**

Voor een prestatievergelijking tussen beheerde exemplaren en SQL Server is het artikel Best practices for performance comparison between Azure SQL managed instance en SQL Server best practices for performance comparison (Best practices voor het vergelijken van prestaties tussen Azure SQL managed instance en [SQL Server).](https://techcommunity.microsoft.com/t5/azure-sql-database/the-best-practices-for-performance-comparison-between-azure-sql/ba-p/683210)

**Wat veroorzaakt prestatieverschillen tussen managed instance en SQL Server?**

Zie [Belangrijke oorzaken van prestatieverschillen tussen SQL Managed Instance en SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/). Zie Impact of log file size on Algemeen (Impact van de grootte van logboekbestanden op de prestaties Algemeen Managed Instance) voor meer informatie over de invloed van het [logboekbestand op de prestaties Algemeen](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

**Hoe kan ik prestaties van mijn beheerde exemplaar afstemmen?**

U kunt de prestaties van uw beheerde exemplaar optimaliseren door:
- [Automatisch afstemmen](../database/automatic-tuning-overview.md) die piekprestaties en stabiele workloads biedt door continue afstemming van prestaties op basis van AI en machine learning.
-    [In-memory OLTP](../in-memory-oltp-overview.md) die de doorvoer en latentie van transactionele verwerkingsworkloads verbetert en snellere zakelijke inzichten biedt. 

Als u de prestaties nog verder wilt afstemmen, kunt u enkele van de *best practices voor* toepassings- en [databaseafstemming toepassen.](../database/performance-guidance.md#tune-your-database)
Als uw workload uit veel kleine transacties bestaat, kunt u overwegen om het verbindingstype over te schakelen van [proxy](connection-types-overview.md#changing-connection-type) naar omleidingsmodus voor een lagere latentie en hogere doorvoer.

## <a name="monitoring-metrics-and-alerts"></a>Bewaking, metrische gegevens en waarschuwingen

**Wat zijn de opties voor bewaking en waarschuwingen voor mijn beheerde exemplaar?**

Zie de blogpost bewakingsopties voor alle mogelijke opties SQL Managed Instance en waarschuwingen over het verbruik en de prestaties Azure SQL Managed Instance [bewakingsopties.](https://techcommunity.microsoft.com/t5/azure-sql-database/monitoring-options-available-for-azure-sql-managed-instance/ba-p/1065416) Zie Realtime prestatiebewaking voor Azure SQL DB Managed Instance voor de realtime [prestatiebewaking voor SQL MI.](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)

**Kan ik SQL Profiler gebruiken voor het bijhouden van prestaties?**

Ja, SQL Profiler wordt ondersteund of SQL Managed Instance. Zie [SQL Profiler voor meer informatie.](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15)

**Worden Database Advisor en Query Performance Insight ondersteund voor beheerde exemplaardatabases?**

Nee, ze worden niet ondersteund. U kunt [DMV's](../database/monitoring-with-dmvs.md) en [Query Store samen](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?preserve-view=true&view=sql-server-ver15) met SQL [Profiler](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15) en [XEvents gebruiken om](/sql/relational-databases/extended-events/extended-events?preserve-view=true&view=sql-server-ver15) uw databases te bewaken.

**Kan ik waarschuwingen voor metrische gegevens maken SQL Managed Instance?**

Ja. Zie Waarschuwingen maken [voor SQL Managed Instance](alerts-create.md).

**Kan ik waarschuwingen voor metrische gegevens maken voor een database in een beheerd exemplaar?**

Dat kan niet. Metrische waarschuwingsgegevens zijn alleen beschikbaar voor beheerde exemplaren. Metrische waarschuwingsgegevens voor afzonderlijke databases in een beheerd exemplaar zijn niet beschikbaar.

## <a name="storage-size"></a>Opslaggrootte

**Wat is de maximale opslaggrootte voor SQL Managed Instance?**

De opslaggrootte voor SQL Managed Instance is afhankelijk van de geselecteerde servicelaag (Algemeen of Bedrijfskritiek). Zie Kenmerken van servicelagen voor opslagbeperkingen [van deze servicelagen.](../database/service-tiers-general-purpose-business-critical.md)

**Wat is de minimale opslaggrootte die beschikbaar is voor een beheerd exemplaar?**

De minimale hoeveelheid opslagruimte die beschikbaar is in een exemplaar is 32 GB. Opslag kan worden toegevoegd in stappen van 32 GB tot de maximale opslaggrootte. De eerste 32 GB zijn gratis.

**Kan ik de opslagruimte vergroten die aan een exemplaar is toegewezen, onafhankelijk van rekenbronnen?**

Ja, u kunt invoegopslag aanschaffen, onafhankelijk van rekenkracht, tot op zekere hoogte. Zie *Maximale gereserveerde opslag voor instanties* in de [tabel](resource-limits.md#hardware-generation-characteristics).

**Hoe kan ik mijn opslagprestaties optimaliseren in Algemeen servicelaag?**

Zie Best practices voor opslag in Algemeen om de [opslagprestaties te Algemeen.](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)

## <a name="backup-and-restore"></a>Back-ups en herstellen

**Wordt de back-upopslag in mindering gebracht op de opslag van mijn beheerde exemplaar?**

Nee, back-upopslag wordt niet in mindering gebracht op de opslagruimte van uw beheerde exemplaar. De back-upopslag is onafhankelijk van de opslagruimte van het exemplaar en is niet beperkt in grootte. Back-upopslag wordt beperkt door de periode voor het bewaren van de back-up van uw exemplaardatabases, die maximaal 35 dagen kan worden geconfigureerd. Zie Automatische [back-ups voor meer informatie.](../database/automated-backups-overview.md)

**Hoe kan ik zien wanneer automatische back-ups worden gemaakt op mijn beheerde exemplaar?**

Zie How to track the automated backup [for an Azure SQL Managed Instance](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-128-how-to-track-the-automated-backup-for-an/ba-p/1442355)als u wilt bijhouden wanneer automatische back-ups zijn uitgevoerd op Managed Instance.

**Wordt back-up op aanvraag ondersteund?**

Ja, u kunt een alleen-kopiëren volledige back-up maken in de Azure Blob Storage, maar deze kan alleen worden teruggeplaatst in managed instance. Zie [Alleen-kopiëren back-up voor meer informatie.](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15) Back-up met alleen-kopiëren is echter onmogelijk als de database wordt versleuteld door een door de service beheerde TDE, omdat het certificaat dat wordt gebruikt voor versleuteling niet toegankelijk is. In dat geval gebruikt u de functie voor herstel naar een bepaald tijdstip om de database naar een andere SQL Managed Instance verplaatsen of om over te schakelen naar een door de klant beheerde sleutel.

**Wordt systeemeigen herstel (van BAK-bestanden) naar managed instance ondersteund?**

Ja, het wordt ondersteund en beschikbaar voor SQL Server 2005+-versies.  Als u systeemeigen herstel wilt gebruiken, uploadt u het BAK-bestand naar Azure Blob Storage en voert u T-SQL-opdrachten uit. Zie Systeemeigen herstel vanuit [URL voor meer informatie.](./migrate-to-instance-from-sql-server.md#native-restore-from-url)

## <a name="business-continuity"></a>Bedrijfscontinuïteit

**Worden mijn systeemdatabases gerepliceerd naar het secundaire exemplaar in een failovergroep?**

Systeemdatabases worden niet gerepliceerd naar het secundaire exemplaar in een failovergroep. Daarom zijn scenario's die afhankelijk zijn van objecten van de systeemdatabases onmogelijk op het secundaire exemplaar, tenzij de objecten handmatig op de secundaire worden gemaakt. Zie Scenario's inschakelen die afhankelijk zijn [van het object van de systeemdatabases voor een tijdelijke oplossing.](../database/auto-failover-group-overview.md?tabs=azure-powershell#enable-scenarios-dependent-on-objects-from-the-system-databases)
 
## <a name="networking-requirements"></a>Netwerkvereisten 

**Wat zijn de huidige inkomende/uitgaande NSG-beperkingen voor het subnet van het beheerde exemplaar?**

De vereiste NSG- en UDR-regels worden hier [beschreven](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)en automatisch ingesteld door de service.
Houd er rekening mee dat deze regels alleen de regels zijn die we nodig hebben voor het onderhouden van de service. Als u verbinding wilt maken met een beheerd exemplaar en verschillende functies wilt gebruiken, moet u aanvullende, functiespecifieke regels instellen die u moet onderhouden.

**Hoe kan ik inkomende NSG-regels instellen voor beheerpoorten?**

SQL Managed Instance is verantwoordelijk voor het instellen van regels voor beheerpoorten. Dit wordt bereikt via functionaliteit met de [naam subnetconfiguratie met service-aided](connectivity-architecture-overview.md#service-aided-subnet-configuration).
Dit is om een ononderbroken stroom van beheerverkeer te garanderen om te voldoen aan een SLA.

**Kan ik de bron-IP-adresbereiken krijgen die worden gebruikt voor het inkomende beheerverkeer?**

Ja. U kunt verkeer dat afkomstig is van uw netwerkbeveiligingsgroep analyseren door [de Network Watcher stroomlogboeken te configureren.](../../network-watcher/network-watcher-monitoring-overview.md#analyze-traffic-to-or-from-a-network-security-group)

**Kan ik NSG instellen om de toegang tot het gegevenseindpunt te controleren (poort 1433)?**

Ja. Nadat een beheerd exemplaar is ingericht, kunt u NSG instellen die de inkomende toegang tot poort 1433 beheert. Het is raadzaam om het IP-bereik zo veel mogelijk te beperken.

**Kan ik de NVA of on-premises firewall instellen om het uitgaande beheerverkeer te filteren op basis van FQDN's?**

Nee. Dit wordt om verschillende redenen niet ondersteund:
-    Het routeren van verkeer dat het antwoord op de inkomende beheeraanvraag vertegenwoordigt, is asymmetrisch en werkt niet.
-    Het routeren van verkeer dat naar de opslag gaat, wordt beïnvloed door doorvoerbeperkingen en latentie, zodat we op deze manier geen verwachte servicekwaliteit en -beschikbaarheid kunnen bieden.
-    Op basis van ervaring zijn deze configuraties foutgevoelig en kunnen ze niet worden ondersteund.

**Kan ik de NVA of firewall instellen voor uitgaand niet-beheerverkeer?**

Ja. De eenvoudigste manier om dit te bereiken, is door een 0/0-regel toe te voegen aan een UDR die is gekoppeld aan het subnet van het beheerde exemplaar om verkeer via NVA te geleiden.
 
**Hoeveel IP-adressen heb ik nodig voor een beheerd exemplaar?**

Subnet moet voldoende beschikbare [IP-adressen hebben.](connectivity-architecture-overview.md#network-requirements) Zie Vereiste subnetgrootte en -bereik bepalen voor Managed Instance om de grootte van het VNet-subnet voor SQL Managed Instance [te bepalen.](./vnet-subnet-determine-size.md) 

**Wat gebeurt er als er onvoldoende IP-adressen zijn voor het uitvoeren van de bewerking voor het bijwerken van exemplaren?**

Als er onvoldoende [IP-adressen](connectivity-architecture-overview.md#network-requirements) zijn in het subnet waarin uw beheerde exemplaar is ingericht, moet u een nieuw subnet en een nieuw beheerd exemplaar in het subnet maken. Het wordt ook aanbevolen het nieuwe subnet te maken met meer toegewezen IP-adressen zodat toekomstige updatebewerkingen dergelijke situaties voorkomen. Nadat het nieuwe exemplaar is ingericht, kunt u handmatig een [back-up](point-in-time-restore.md?tabs=azure-powershell)maken van gegevens en deze herstellen tussen de oude en nieuwe exemplaren of herstel naar een bepaald tijdstip uitvoeren.

**Heb ik een leeg subnet nodig om een beheerd exemplaar te maken?**

Nee. U kunt een leeg subnet of een subnet gebruiken dat al beheerde exemplaren bevat. 

**Kan ik het adresbereik van het subnet wijzigen?**

Niet als er beheerde exemplaren in zitten. Dit is een beperking voor azure-netwerkinfrastructuur. U mag alleen extra [adresruimte toevoegen aan een leeg subnet.](../../virtual-network/virtual-network-manage-subnet.md#change-subnet-settings) 

**Kan ik mijn beheerde exemplaar verplaatsen naar een ander subnet?**

Nee. Dit is een huidige ontwerpbeperking voor beheerde exemplaren. U kunt echter een nieuw exemplaar inrichten in een ander subnet en handmatig een back-up maken van gegevens en deze herstellen tussen het oude en het nieuwe exemplaar of herstel naar een bepaald tijdstip [uitvoeren.](point-in-time-restore.md?tabs=azure-powershell)

**Heb ik een leeg virtueel netwerk nodig om een beheerd exemplaar te maken?**

Dit is niet vereist. U kunt een [virtueel netwerk maken voor Azure SQL Managed Instance](./virtual-network-subnet-create-arm-template.md) of een bestaand virtueel netwerk [configureren voor Azure SQL Managed Instance.](./vnet-existing-add-subnet.md)

**Kan ik een beheerd exemplaar met andere services in een subnet plaatsen?**

Nee. Momenteel bieden we geen ondersteuning voor het plaatsen van een beheerd exemplaar in een subnet dat al andere resourcetypen bevat.

## <a name="connectivity"></a>Connectiviteit 

**Kan ik verbinding maken met mijn beheerde exemplaar met behulp van een IP-adres?**

Nee, dit wordt niet ondersteund. De hostnaam van een beheerd exemplaar wordt load balancer het virtuele cluster van het beheerde exemplaar. Omdat één virtueel cluster meerdere beheerde exemplaren kan hosten, kan een verbinding niet worden gerouteerd naar het juiste beheerde exemplaar zonder de naam op te geven.
Zie Architectuur voor virtuele clusterconnectiviteit SQL Managed Instance meer informatie over de architectuur [van virtuele clusters.](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)

**Kan mijn beheerde exemplaar een statisch IP-adres hebben?**

Dit wordt momenteel niet ondersteund.

In zeldzame maar noodzakelijke situaties moeten we mogelijk een onlinemigratie van een beheerd exemplaar naar een nieuw virtueel cluster maken. Indien nodig is deze migratie het gevolg van wijzigingen in onze technologiestack die erop zijn gericht om de beveiliging en betrouwbaarheid van de service te verbeteren. Migreren naar een nieuw virtueel cluster resulteert in het wijzigen van het IP-adres dat is toe te staan aan de hostnaam van het beheerde exemplaar. De service voor beheerde exemplaren claimt geen ondersteuning voor statische IP-adressen en behoudt zich het recht voor om dit zonder kennisgeving te wijzigen als onderdeel van normale onderhoudscycli.

Daarom raden we het gebruik van onveranderbaarheid van het IP-adres ten zeerste af, omdat dit onnodige downtime kan veroorzaken.

**Heeft het beheerde exemplaar een openbaar eindpunt?**

Ja. Managed Instance heeft een openbaar eindpunt dat standaard alleen wordt gebruikt voor Service Management, maar een klant kan dit ook inschakelen voor gegevenstoegang. Zie Use [SQL Managed Instance with public endpoints (Een SQL Managed Instance gebruiken met openbare eindpunten) voor meer informatie.](./public-endpoint-overview.md) Als u een openbaar eindpunt wilt configureren, gaat u naar [Openbaar eindpunt configureren in SQL Managed Instance.](public-endpoint-configure.md)

**Hoe wordt de toegang tot het openbare eindpunt beheerd door managed instance?**

Managed Instance beheert de toegang tot het openbare eindpunt op netwerk- en toepassingsniveau.

Beheer- en implementatieservices maken verbinding met een beheerd exemplaar met behulp van een [beheer-eindpunt](./connectivity-architecture-overview.md#management-endpoint) dat is load balancer. Verkeer wordt alleen naar de knooppunten gerouteerd als het wordt ontvangen op een vooraf gedefinieerde set poorten die alleen door de beheeronderdelen van het beheerde exemplaar worden gebruikt. Er wordt een ingebouwde firewall op de knooppunten ingesteld om alleen verkeer van Microsoft IP-adresbereiken toe te staan. Certificaten verifiëren wederzijds alle communicatie tussen beheeronderdelen en het beheervlak. Zie Connectiviteitsarchitectuur voor [SQL Managed Instance.](./connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)

**Kan ik het openbare eindpunt gebruiken voor toegang tot de gegevens in beheerde exemplaardatabases?**

Ja. De klant moet toegang tot openbare eindpuntgegevens inschakelen [vanuit Azure Portal](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal)PowerShell/ARM en NSG configureren om de toegang tot de gegevenspoort  /  [](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) (poortnummer 3342) te vergrendelen. Zie Configure public [endpoint in Azure SQL Managed Instance](public-endpoint-configure.md) and Use Azure SQL Managed Instance [securely with public endpoint](public-endpoint-overview.md)voor meer informatie. 

**Kan ik een aangepaste poort opgeven voor een of meer EINDPUNTen voor SQL-gegevens?**

Nee, deze optie is niet beschikbaar.  Voor het eindpunt van privégegevens gebruikt Managed Instance het standaardpoortnummer 1433 en voor het eindpunt van openbare gegevens gebruikt Managed Instance het standaardpoortnummer 3342.

**Wat is de aanbevolen manier om beheerde exemplaren te verbinden die in verschillende regio's zijn geplaatst?**

Peering van Express Route-circuits is de voorkeursmethode om dat te doen. Wereldwijde peering voor virtuele netwerken wordt ondersteund met de beperking die wordt beschreven in de opmerking hieronder.  

> [!IMPORTANT]
> [Op 22-9-2020 hebben we globale peering van virtuele netwerken aangekondigd voor nieuwe virtuele clusters](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Dit betekent dat de globale peering van virtuele netwerken wordt ondersteund voor met SQL beheerde instanties die na de aankondigingsdatum zijn gemaakt in lege subnetten, en ook voor alle daaropvolgende beheerde instanties die in deze subnetten zijn gemaakt. Voor alle andere SQL Managed Instances is peeringondersteuning beperkt tot de netwerken in dezelfde regio vanwege de beperkingen van wereldwijde [peering voor virtuele netwerken.](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) Zie ook de relevante sectie van het artikel Veelgestelde vragen [over Azure Virtual Networks](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) voor meer informatie. 

Als Peering van Express Route-circuits en wereldwijde peering voor virtuele netwerken niet mogelijk is, is de enige andere optie om een site-naar-site-VPN-verbinding te maken[( Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md), [PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI).](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="mitigate-data-exfiltration-risks"></a>Risico's van gegevens exfiltratie beperken  

**Hoe kan ik gegevens exfiltratierisico's beperken?**

Klanten wordt aangeraden een set beveiligingsinstellingen en -besturingselementen toe te passen om risico's op gegevens exfiltratie te beperken:

- Schakel Transparent Data Encryption [(TDE) in](../database/transparent-data-encryption-tde-overview.md) voor alle databases.
- Schakel Common Language Runtime (CLR) uit. Dit wordt ook on-premises aanbevolen.
- Gebruik Azure Active Directory (Azure AD)-verificatie.
- Toegang tot het exemplaar met een DBA-account met lage bevoegdheden.
- JIT-jumpboxtoegang configureren voor het sysadmin-account.
- Schakel [SQL-controle in](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)en integreer deze met waarschuwingsmechanismen.
- Schakel Detectie [van bedreigingen](../database/threat-detection-configure.md) in vanuit [Azure Defender sql-suite.](../database/azure-defender-for-sql.md)

## <a name="dns"></a>DNS

**Kan ik een aangepaste DNS configureren voor SQL Managed Instance?**

Ja. Zie [How to configure a Custom DNS for Azure SQL Managed Instance](./custom-dns-configure.md).

**Kan ik DNS vernieuwen?**

Ja. Zie [synchronisatie virtuele netwerk DNS-servers instelling op SQL Managed Instance virtuele cluster.](./synchronize-vnet-dns-servers-setting-on-virtual-cluster.md)

## <a name="change-time-zone"></a>Tijdzone wijzigen

**Kan ik de tijdzone voor een bestaand beheerd exemplaar wijzigen?**

De tijdzoneconfiguratie kan worden ingesteld wanneer een beheerd exemplaar voor de eerste keer wordt ingericht. Het wijzigen van de tijdzone van een bestaand beheerd exemplaar wordt niet ondersteund. Zie [Tijdzonebeperkingen voor meer informatie.](timezones-overview.md#limitations)

Tijdelijke oplossingen zijn onder andere het maken van een nieuw beheerd exemplaar met de juiste tijdzone en vervolgens het uitvoeren van een handmatige [back-up](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance)en herstel, of wat we aanbevelen, het uitvoeren van een herstel naar een bepaald tijdstip.


## <a name="security-and-database-encryption"></a>Beveiliging en databaseversleuteling

**Is de serverfunctie sysadmin beschikbaar voor SQL Managed Instance?**

Ja, klanten kunnen aanmeldingen maken die lid zijn van de rol sysadmin.  Klanten die uitgaan van de sysadmin-bevoegdheid, nemen ook de verantwoordelijkheid voor het gebruik van de instantie op zich, wat een negatieve invloed kan hebben op de SLA-toezegging. Zie [Azure AD-verificatie](./aad-security-configure-tutorial.md#azure-ad-authentication)als u aanmelding wilt toevoegen aan de serverrol sysadmin.

**Wordt Transparent Data Encryption ondersteund voor SQL Managed Instance?**

Ja, Transparent Data Encryption wordt ondersteund voor SQL Managed Instance. Zie voor meer [informatie Transparent Data Encryption voor SQL Managed Instance.](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal)

**Kan ik gebruikmaken van het 'Bring Your Own Key'-model voor TDE?**

Ja, Azure Key Vault byok-scenario is beschikbaar voor Azure SQL Managed Instance. Zie Voor meer informatie Transparent Data Encryption [met door de klant beheerde sleutel.](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key)

**Kan ik een versleutelde database SQL Server migreren?**

Ja, dat kunt u. Als u een versleutelde SQL Server-database wilt migreren, moet u uw bestaande certificaten exporteren en importeren in Managed Instance. Vervolgens moet u een volledige databaseback-up maken en deze herstellen in Managed Instance. 

U kunt ook [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) de versleutelde TDE-databases te migreren.

**Hoe kan ik TDE-wisseling van beveiliging configureren voor SQL Managed Instance?**

U kunt TDE-beveiliging voor managed instance roteren met behulp Azure Cloud Shell. Zie in Transparent Data Encryption in SQL Managed Instance using your own key from Azure Key Vault (Uw eigen sleutel [gebruiken vanuit Azure Key Vault) voor instructies.](scripts/transparent-data-encryption-byok-powershell.md)

**Kan ik mijn versleutelde database herstellen naar SQL Managed Instance?**

Ja, u hoeft uw database niet te ontsleutelen om deze te herstellen naar SQL Managed Instance. U moet een certificaat/sleutel die wordt gebruikt als de beveiliging van de versleutelingssleutel op het bronsysteem om SQL Managed Instance kunnen lezen van het versleutelde back-upbestand. Er zijn twee manieren om dit te doen:

- *Upload certificaatbeveiliging naar SQL Managed Instance*. U kunt dit alleen doen met Behulp van PowerShell. In [het voorbeeldscript](./tde-certificate-migrate.md) wordt het hele proces beschreven.
- *Upload asymmetrische sleutelbeveiliging naar Azure Key Vault en wijs SQL Managed Instance naar deze sleutel.* Deze benadering lijkt op de TDE-use-case bring-your-own-key (BYOK) die ook gebruikmaakt van Key Vault-integratie voor het opslaan van de versleutelingssleutel. Als u de sleutel niet wilt gebruiken als een versleutelingssleutelbeveiliging en u alleen de sleutel beschikbaar wilt maken voor SQL Managed Instance om versleutelde databases te herstellen, volgt u de instructies voor het instellen van [BYOK TDE](../database/transparent-data-encryption-tde-overview.md#manage-transparent-data-encryption)en vink u het selectievakje De geselecteerde sleutel de standaard TDE-beveiliging maken **niet in.**

Zodra u de versleutelingsbeveiliging beschikbaar hebt gemaakt SQL Managed Instance, kunt u doorgaan met de standaardprocedure voor het herstellen van de database.

## <a name="purchasing-models-and-benefits"></a>Aankoopmodellen en voordelen

**Welke aankoopmodellen zijn beschikbaar voor SQL Managed Instance?**

SQL Managed Instance biedt een [aankoopmodel op basis van vCore.](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model)

**Welke kostenvoordelen zijn er beschikbaar voor SQL Managed Instance?**

U kunt kosten besparen met de Azure SQL voordelen op de volgende manieren:
-    Maximaliseer bestaande investeringen in on-premises licenties en bespaar tot 55 procent met [Azure Hybrid Benefit](../azure-hybrid-benefit.md?tabs=azure-powershell). 
-    U kunt een reservering maken voor rekenbronnen en tot 33 procent besparen met [Reserved Instance Benefit.](../database/reserved-capacity-overview.md) Combineer dit met Azure Hybrid Benefit voor een besparing van maximaal 82 procent. 
-    Bespaar maximaal 55 procent ten opzichte van de lijstprijzen met [Azure Dev/Test Pricing Benefit,](https://azure.microsoft.com/pricing/dev-test/) dat kortingstarieven biedt voor uw doorlopende ontwikkel- en testworkloads.

**Wie komt in aanmerking voor het voordeel van een gereserveerde instantie?**

Als u in aanmerking wilt komen voor gereserveerde instantievoordeel, moet uw abonnementstype een Enterprise Agreement zijn (aanbiedingsnummers: MS-AZR-0017P of MS-AZR-0148P) of een afzonderlijke overeenkomst met betalen per gebruik-prijzen (aanbiedingsnummers: MS-AZR-0003P of MS-AZR-0023P). Zie Reserved Instance Benefit voor meer informatie [over reserveringen.](../database/reserved-capacity-overview.md) 

**Is het mogelijk om reserveringen te annuleren, in te wisselen of te restituties?**

U kunt reserveringen annuleren, inruilen of restituties aanvragen met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) voor meer informatie.

## <a name="billing-for-managed-instance-and-backup-storage"></a>Facturering voor beheerd exemplaar en back-upopslag

**Wat zijn de SQL Managed Instance prijsopties?**

Zie de pagina Prijzen om de prijsopties voor beheerde [exemplaren te verkennen.](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)

**Hoe kan ik de factureringskosten voor mijn beheerde exemplaar bijhouden?**

U kunt dit doen met behulp van [Azure Cost Management oplossing](../../cost-management-billing/index.yml). **Navigeer naar Abonnementen** in [Azure Portal](https://portal.azure.com) selecteer **Kostenanalyse.** 

Gebruik de **optie Geaccumuleerde** kosten en filter vervolgens op **resourcetype** als `microsoft.sql/managedinstances` .

**Hoeveel geautomatiseerde back-ups kosten?**

U krijgt dezelfde hoeveelheid vrije ruimte voor back-upopslag als de gereserveerde gegevensopslagruimte die u hebt aangeschaft, ongeacht de periode voor het bewaren van back-ups. Als het verbruik van uw back-upopslag binnen de toegewezen vrije opslagruimte voor back-ups ligt, hebben automatische back-ups op het beheerde exemplaar geen extra kosten voor u. Daarom zijn er geen kosten. Als u het gebruik van back-upopslag overschrijdt boven de beschikbare ruimte, resulteert dit in kosten van ongeveer $ 0,20 - $ 0,24 per GB/maand in de Regio's van de VS of bekijkt u de pagina met prijzen voor meer informatie over uw regio. Zie Verbruik van back-upopslag voor [meer informatie.](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923)

**Hoe kan ik de factureringskosten voor het verbruik van mijn back-upopslag bewaken?**

U kunt de kosten voor back-upopslag bewaken via Azure Portal. Zie Kosten voor automatische [back-ups](../database/automated-backups-overview.md?tabs=managed-instance#monitor-costs)bewaken voor instructies. 

**Hoe kan ik mijn kosten voor back-upopslag optimaliseren voor het beheerde exemplaar?**

Zie Fine backup tuning on SQL Managed Instance (Afstemmen van back-ups voor het optimaliseren van uw kosten [voor back SQL Managed Instance.](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935)

## <a name="cost-saving-use-cases"></a>Kostenbesparende gebruiksgevallen

**Waar vind ik use cases en resulterende kostenbesparingen met SQL Managed Instance?**

SQL Managed Instance casestudies:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [Kmd](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

Voor een beter begrip van de voordelen, kosten en risico's die gepaard gaan met het implementeren van Azure SQL Managed Instance, is er ook een Forrester-studie: [The Total Economic Impact of Microsoft Azure SQL Database Managed Instance](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).

## <a name="password-policy"></a>Wachtwoordbeleid 

**Welk wachtwoordbeleid wordt toegepast voor SQL Managed Instance SQL-aanmeldingen?**

SQL Managed Instance wachtwoordbeleid voor SQL-aanmeldingen neemt het Azure-platformbeleid over dat wordt toegepast op de virtuele machines die een virtueel cluster vormen met het beheerde exemplaar. Op dit moment is het niet mogelijk om een van deze instellingen te wijzigen, omdat deze instellingen worden gedefinieerd door Azure en worden overgenomen door een beheerd exemplaar.

 > [!IMPORTANT]
 > Het Azure-platform kan beleidsvereisten wijzigen zonder services te informeren die afhankelijk zijn van dat beleid.

**Wat zijn huidige beleidsregels voor Het Azure-platform?**

Elke aanmelding moet het wachtwoord instellen bij aanmelding en het wachtwoord wijzigen nadat de maximale leeftijd is bereikt.

| **Beleid** | **Beveiligingsinstelling** |
| --- | --- |
| Maximale gebruiksduur wachtwoord | 42 dagen |
| Minimale gebruiksduur wachtwoord | 1 dag |
| Minimale wachtwoordlengte | 10 tekens |
| Wachtwoord moet voldoen aan complexiteitsvereisten | Ingeschakeld |

**Is het mogelijk om wachtwoordcomplexiteit en verloop in de SQL Managed Instance op aanmeldingsniveau uit te schakelen?**

Ja, het is mogelijk om de CHECK_POLICY en CHECK_EXPIRATION op aanmeldingsniveau te bepalen. U kunt de huidige instellingen controleren door de volgende T-SQL-opdracht uit te voeren:

```sql
SELECT *
FROM sys.sql_logins
```

Daarna kunt u opgegeven aanmeldingsinstellingen wijzigen door uit te voeren:

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(vervang 'test' door de gewenste aanmeldingsnaam en pas de waarden voor beleid en verloop aan)


## <a name="service-updates"></a>Service-updates

**Wat is de basis-CA-wijziging voor Azure SQL Database & SQL Managed Instance?**

Zie [Certificaatrotatie voor Azure SQL Database & SQL Managed Instance](../updates/ssl-root-certificate-expiring.md). 

**Wat is een geplande onderhoudsgebeurtenis voor SQL Managed Instance?**

Zie [Plan for Azure maintenance events in SQL Managed Instance](../database/planned-maintenance.md). 


## <a name="azure-feedback-and-support"></a>Azure-feedback en -ondersteuning

**Waar kan ik mijn ideeën voor SQL Managed Instance laten?**

U kunt stemmen op een nieuwe managed instance-functie of een nieuw idee voor verbetering geven bij het stemmen [op SQL Managed Instance Feedbackforum.](https://feedback.azure.com/forums/915676-sql-managed-instance) Op deze manier kunt u bijdragen aan de productontwikkeling en ons helpen bij het prioriteren van onze potentiële verbeteringen.

**Hoe kan ik een ondersteuning voor Azure maken?**

Zie How to create ondersteuning voor Azure request (Een aanvraag voor ondersteuning voor Azure maken) voor meer informatie over het maken [ondersteuning voor Azure aanvraag.](../../azure-portal/supportability/how-to-create-azure-support-request.md)
