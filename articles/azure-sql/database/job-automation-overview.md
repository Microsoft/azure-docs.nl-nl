---
title: Overzicht van taakautomatisering met elastische taken
description: Gebruik Elastische taken voor Taakautomatisering om Transact-SQL-scripts (T-SQL) uit te voeren in een set van een of meer databases
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1, contperf-fy21q3
ms.devlang: ''
dev_langs:
- TSQL
ms.topic: conceptual
author: williamdassafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 2/1/2021
ms.openlocfilehash: 295889cf64d27761021dd09549a3366ea142516e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752026"
---
# <a name="automate-management-tasks-using-elastic-jobs-preview"></a>Beheertaken automatiseren met elastische taken (preview)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

U kunt elastische taken maken en plannen die periodiek kunnen worden uitgevoerd op een of meer Azure SQL-databases om Transact-SQL-query's (T-SQL) uit te voeren en onderhoudstaken uit te voeren. 

U kunt een doeldatabase of groepen databases definiëren waarin de taak wordt uitgevoerd, en ook schema's voor het uitvoeren van een taak definiëren.
Een taak handelt het aanmelden bij de doeldatabase af. Ook definieert, onderhoudt en handhaaft u Transact-SQL-scripts die in een groep databases moeten worden uitgevoerd.

Door elke taak wordt de uitvoeringsstatus in een logboek vastgelegd en wordt ook geprobeerd de bewerkingen opnieuw uit te voeren als er een fout optreedt.

## <a name="when-to-use-elastic-jobs"></a>Wanneer elastische taken gebruiken

Er zijn verschillende scenario's waarin u elastische taakautomatisering kunt gebruiken:

- Beheertaken automatiseren en deze vervolgens plannen voor uitvoering op elke weekdag, na kantooruren enzovoort.
  - Implementeer schemawijzigingen, referentiebeheer, het verzamelen van prestatiegegevens of telemetrie van tenant (klant).
  - Referentiegegevens (algemene informatie voor alle databases) bijwerken, gegevens laden uit Azure-blobopslag.
- Configureer taken die op terugkerende basis, bijvoorbeeld tijdens daluren, moeten worden uitgevoerd.
  - Verzamel op continue basis queryresultaten uit een reeks databases in een centrale tabel. Prestatiequery's kunnen voortdurend worden uitgevoerd en geconfigureerd om de uitvoering van aanvullende taken te activeren.
- Gegevens verzamelen voor rapportage
  - Verzamel gegevens uit een verzameling databases in één doeltabel.
  - Voer langer durende gegevensverwerkingsquery's uit voor een groot aantal databases, zoals het verzamelen van klanttelemetrie. Resultaten worden in één doeltabel verzameld voor verdere analyse.
- Gegevensverplaatsingen 

### <a name="automation-on-other-platforms"></a>Automatisering op andere platforms

Houd rekening met de volgende technologieën voor taakplanning op verschillende platforms:

- **Elastische taken** zijn taakplanningsservices die aangepaste taken uitvoeren op een of meer databases in Azure SQL Database.
- **SQL Agent-taken** worden uitgevoerd door de SQL Agent-service die nog steeds wordt gebruikt voor taakautomatisering in SQL Server en die ook is opgenomen in Azure SQL Managed Instances. SQL Agent-taken zijn niet beschikbaar in Azure SQL Database.

Elastische taken kunnen worden [Azure SQL databases,](sql-database-paas-overview.md) [Azure SQL Database elastische pools](elastic-pool-overview.md)en Azure SQL databases in [shard-kaarten.](elastic-scale-shard-map-management.md)

Voor T-SQL-script jobautomatisering in SQL Server en Azure SQL Managed Instance, kunt u [SQL Agent overwegen.](job-automation-managed-instances.md) 

Voor T-SQL-script jobautomatisering in Azure Synapse Analytics, kunt u pijplijnen met terugkerende [triggers](../../synapse-analytics/data-integration/concepts-data-factory-differences.md)overwegen, die [zijn gebaseerd op Azure Data Factory](../../synapse-analytics/data-integration/concepts-data-factory-differences.md).

De verschillen tussen SQL Agent (beschikbaar in SQL Server en als onderdeel van SQL Managed Instance) en de elastische database-taakagent (die T-SQL kan uitvoeren op Azure SQL Databases of databases in SQL Server en Azure SQL Managed Instance, Azure Synapse Analytics).

| |Elastische taken |SQL Agent |
|---------|---------|---------|
|**Bereik** | Elk gewenst aantal databases in Azure SQL Database en/of datawarehouses in dezelfde Azure-cloud als de taakagent. Doelen kunnen zich op verschillende servers en in verschillende abonnementen en/of regio's bevinden. <br><br>Doelgroepen kunnen bestaan uit afzonderlijke databases of datawarehouses, of alle databases in een server, pool of shard-kaart (dynamisch geïndexeerd tijdens het uitvoeren van de taak). | Een afzonderlijke database in hetzelfde exemplaar als de SQL Agent. Met de functie Multi Server Administration van SQL Server Agent kunnen master-/doel-exemplaren de taakuitvoering coördineren, hoewel deze functie niet beschikbaar is in SQL Managed Instance. |
|**Ondersteunde API's en hulpprogramma's** | Portal, PowerShell, T-SQL, Azure Resource Manager | T-SQL, SQL Server Management Studio (SSMS) |
 
## <a name="elastic-job-targets"></a>Doelen voor elastische taak

**Elastische** taken bieden de mogelijkheid om een of meer T-SQL-scripts parallel, op een groot aantal databases, volgens een schema of op aanvraag uit te voeren.

U kunt geplande taken uitvoeren voor elke combinatie van databases: een of meer afzonderlijke databases, alle databases op een server, alle databases in een elastische pool of shard-kaart, met de extra flexibiliteit om een specifieke database op te nemen of uit te sluiten. Taken kunnen worden uitgevoerd op meerdere servers en meerdere pools, en kunnen zelfs worden uitgevoerd voor databases in verschillende abonnementen. Servers en pools worden dynamisch opgesomd tijdens runtime, zodat taken worden uitgevoerd voor alle databases die op het moment van de uitvoering in de doelgroep aanwezig zijn.

De volgende afbeelding toont hoe een taakagent taken uitvoert op verschillende soorten doelgroepen:

![Conceptueel model van elastische taakagent](./media/job-automation-overview/conceptual-diagram.png)

### <a name="elastic-job-components"></a>Onderdelen van elastische taak

|Onderdeel | Beschrijving (aanvullende gegevens onder de tabel) |
|---------|---------|
|[**Elastische-taakagent**](#elastic-job-agent) | De Azure-resource die u maakt om taken uit te voeren en te beheren. |
|[**Taakdatabase**](#elastic-job-database) | Een database in Azure SQL die de taakagent gebruikt om taakgerelateerde gegevens, taakdefinities enzovoort op te slaan. |
|[**Doelgroep**](#target-group) | De verzameling servers, pools, databases en shardkaarten waarvoor een taak moet worden uitgevoerd. |
|[**Taak**](#elastic-jobs-and-job-steps) | Een taak is een werkeenheid die uit een of meer taakstappen bestaat. Taakstappen bepalen welk T-SQL-script moet worden uitgevoerd en andere details die nodig zijn voor het uitvoeren van het script. |

#### <a name="elastic-job-agent"></a>Elastische-taakagent

Een elastische-taakagent is de Azure-resource voor het maken, uitvoeren en beheren van taken. De elastische-taakagent is een Azure-resource die u maakt in de portal ([PowerShell](elastic-jobs-powershell-create.md) en REST worden ook ondersteund).

Voor het maken van een **elastische-taakagent** hebt u een bestaande database in Azure SQL Database nodig. De agent configureert deze bestaande Azure SQL Database als de [*taakdatabase*](#elastic-job-database).

De elastische-taakagent is gratis. De taakdatabase wordt tegen hetzelfde tarief gefactureerd als een database in Azure SQL Database.

#### <a name="elastic-job-database"></a>Elastische taakdatabase

De *taakdatabase* wordt gebruikt voor het definiëren van taken en het bijhouden van de status en geschiedenis van taakuitvoeringen. De *taakdatabase* wordt ook gebruikt voor het opslaan van metagegevens van de agent, logboeken, resultaten en taakdefinities. De database bevat ook veel nuttige opgeslagen procedures en andere databaseobjecten voor het maken, uitvoeren en beheren van taken met T-SQL.

Voor de huidige preview is een bestaande database in Azure SQL Database (S0 of hoger) vereist om een elastische-taakagent te kunnen maken.

De *taakdatabase moet* een schone, lege, S0- of hogere servicedoelstelling Azure SQL Database. De aanbevolen servicedoelstelling van de *taakdatabase* is S1 of hoger, maar de optimale keuze hangt af van de prestatiebehoeften van uw taken: het aantal taakstappen, het aantal taakdoelen en met welke frequentie de taken worden uitgevoerd. 

Als bewerkingen voor de taakdatabase langzamer zijn dan verwacht, [bewaak](monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring) dan de databaseprestaties en het resourcegebruik in de taakdatabase tijdens trage periodes met behulp van Azure Portal of de DMV [sys. dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database). Als het gebruik van een resource, zoals CPU, gegevens-IO of logboekschrijfbewerkingen bijna 100% is en overeenkomt met trage perioden, kunt u overwegen om de database stapsgewijs te schalen naar hogere servicedoelstellingen (in het [DTU-model](service-tiers-dtu.md) of het [vCore-model](service-tiers-vcore.md)) totdat de prestaties van de taakdatabase voldoende zijn verbeterd.

##### <a name="elastic-job-database-permissions"></a>Databasemachtigingen voor elastische taak

Tijdens het maken van een taakagent worden er een schema, tabellen en een rol met de naam *jobs_reader* gemaakt in de *taakdatabase*. De rol is gemaakt met de volgende machtigingen en is ontworpen om beheerders gedetailleerder toegangsbeheer te geven voor het bewaken van de taak:

|Rolnaam |Machtigingen voor schema 'jobs' |Machtigingen voor schema 'jobs_internal' |
|---------|---------|---------|
|**jobs_reader** | SELECT | Geen |

> [!IMPORTANT]
> Houd rekening met de beveiligingsaspecten voordat u iemand als een databasebeheerder toegang verleent tot de *taakdatabase*. Een kwaadwillende gebruiker met machtigingen voor het maken of bewerken van taken kan een taak maken of bewerken die gebruikmaakt van een opgeslagen referentie om verbinding te maken met een database onder het beheer van de kwaadwillende gebruiker, waardoor de kwaadwillende gebruiker het wachtwoord van de referentie kan bepalen.

#### <a name="target-group"></a>Doelgroep

Een *doelgroep* definieert de verzameling databases waarvoor een taakstap wordt uitgevoerd. Een doelgroep kan een willekeurig aantal en een willekeurige combinatie van de volgende elementen bevatten:

- **Logische SQL-server**: als een server is opgegeven, maken alle databases op de server op het moment waarop de taak wordt uitgevoerd, deel uit van de groep. De referenties van de hoofddatabase moeten worden opgegeven zodat de groep kan worden opgesomd en worden bijgewerkt voordat de taak wordt uitgevoerd. Zie What is a server in Azure SQL Database and Azure Synapse Analytics? (Wat is een server in Azure SQL Database [en Azure Synapse Analytics? voor meer informatie over logische servers.](logical-servers.md)
- **Elastische pool**: als een elastische pool is opgegeven, maken alle databases die zich in de elastische pool bevinden op het moment dat de taak wordt uitgevoerd, deel uit van de groep. Wat de server betreft, moeten de referenties van de hoofddatabase worden opgegeven zodat de groep kan worden bijgewerkt voordat de taak wordt uitgevoerd.
- **Individuele database**: geef een of meer afzonderlijke databases op als onderdeel van de groep.
- **Shard-kaart:** databases van een shard-kaart.

> [!TIP]
> Op het moment dat de taak wordt uitgevoerd, wordt de reeks databases in de doelgroepen die servers of pools bevatten, opnieuw geëvalueerd met behulp van *dynamische opsomming*. Dynamische opsomming zorgt ervoor dat **taken worden uitgevoerd voor alle databases die in de server of de pool bestaan op het moment dat de taak wordt uitgevoerd**. Een herevaluatie van de lijst met databases tijdens runtime is met name nuttig als het lidmaatschap van de pool of server regelmatig verandert.

Pools en individuele databases kunnen worden opgegeven als ingesloten in of uitgesloten van de groep. Zo kunt u een doelgroep maken met een willekeurige combinatie van databases. Zo kunt u bijvoorbeeld een server toevoegen aan een doelgroep, maar specifieke databases in een elastische pool (of een hele pool) uitsluiten.

Een doelgroep kan databases in meerdere abonnementen en uit meerdere regio's bevatten. Regio-overschrijdende uitvoeringen hebben echter wel een hogere latentie dan uitvoeringen binnen dezelfde regio.

In de volgende voorbeelden ziet u hoe verschillende doelgroepdefinities tijdens de uitvoering van de taak dynamisch worden geïnventariseerd om te bepalen in welke databases de taak wordt uitgevoerd:

![Voorbeelden van doelgroep](./media/job-automation-overview/targetgroup-examples1.png)

In **Voorbeeld 1** ziet u een doelgroep dat bestaat uit een lijst met afzonderlijke databases. Wanneer een taakstap wordt uitgevoerd met behulp van deze doelgroep, wordt de actie van de taakstap uitgevoerd in elk van deze databases.<br>
In **Voorbeeld 2** ziet u een doelgroep die een server als doel heeft. Wanneer een taakstap wordt uitgevoerd met behulp van deze doelgroep, wordt de server dynamisch geïnventariseerd om de actuele lijst met databases op de server te bepalen. De actie van de taakstap wordt uitgevoerd in elk van deze databases.<br>
In **Voorbeeld 3** ziet u een vergelijkbare doelgroep als in *Voorbeeld 2*, maar een afzonderlijke database wordt uitdrukkelijk uitgesloten. De actie van de taakstap wordt *niet* uitgevoerd in de uitgesloten database.<br>
In **Voorbeeld 4** ziet u een doelgroep die een elastische pool als doel heeft. De pool wordt, vergelijkbaar met in *Voorbeeld 2*, dynamisch geïnventariseerd tijdens het uitvoeren van de taak om de lijst met databases in de pool te bepalen.
<br><br>

![Aanvullende voorbeelden van doelgroepen](./media/job-automation-overview/targetgroup-examples2.png)

In **Voorbeeld 5** en **Voorbeeld 6** ziet u geavanceerde scenario's waarin servers, elastische pools en databases kunnen worden gecombineerd met behulp van regels voor opnemen en uitsluiten.<br>
In **Voorbeeld 7** ziet u dat de shards in een shardkaart ook kunnen worden geëvalueerd tijden het uitvoeren van de taak.

> [!NOTE]
> De taakdatabase zelf kan het doel van een taak zijn. In dit scenario wordt de taakdatabase net als elke andere doeldatabase behandeld. De taakgebruiker moet zijn gemaakt en voldoende machtigingen hebben in de taakdatabase en de databasereferentie voor de taakgebruiker moet ook bestaan in de taakdatabase, net als bij elke andere doeldatabase.

#### <a name="elastic-jobs-and-job-steps"></a>Elastische taken en taakstappen

Een *taak* is een werkeenheid die wordt uitgevoerd volgens een schema of als een eenmalige taak. Een taak bestaat uit een of meer *taakstappen*.

Elke taakstap bevat een uit te voeren T-SQL-script, een of meer doelgroepen waarvoor het T-SQL-script moet worden uitgevoerd en de referenties die de agent nodig heeft om verbinding te maken met de doeldatabase. Elke taakstap heeft een instelbaar beleid voor time-out en nieuwe pogingen, en kan optioneel uitvoerparameters bevatten.

#### <a name="job-output"></a>Taakuitvoer

Het resultaat van de stappen van een taak op elke doeldatabase wordt gedetailleerd geregistreerd en scriptuitvoer kan worden vastgelegd in een opgegeven tabel. U kunt een database opgeven om de resultaatgegevens van een taak vast te leggen.

#### <a name="job-history"></a>Jobgeschiedenis

Bekijk de uitvoeringsgeschiedenis van elastische taak in de *taakdatabase door* een query uit te voeren [op de tabel jobs.job_executions](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status). Met een systeemopschoontaak wordt uitvoergeschiedenis verwijderd die ouder is dan 45 dagen. Als u de geschiedenis van minder dan  45 dagen oud wilt verwijderen, roept u de sp_purge_jobhistory opgeslagen procedure in de *taakdatabase aan.*

#### <a name="job-status"></a>Taakstatus

U kunt de uitvoering van elastische taak in de *taakdatabase* controleren door een query uit te voeren [op de tabel jobs.job_executions](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status). 

### <a name="agent-performance-capacity-and-limitations"></a>Agentprestaties, - capaciteit en -beperkingen

Elastische taken gebruiken minimale rekenresources tijdens het wachten tot langlopende taken zijn voltooid.

Afhankelijk van de grootte van de doelgroep van databases en de gewenste uitvoeringstijd voor een taak (aantal gelijktijdige werkrollen), vraagt de agent meer of minder rekenkracht en prestaties van de *taakdatabase* (hoe meer doelen en taken, hoe hoger de vereiste hoeveelheid rekenkracht).

Momenteel is de limiet 100 gelijktijdige taken.

#### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Voorkomen dat taken de prestaties van de doeldatabase doen afnemen

Om ervoor te zorgen dat resources niet worden overbelast tijdens het uitvoeren van taken voor databases in een elastische SQL-pool, kunnen taken zo worden geconfigureerd dat het aantal databases waarvoor een taak tegelijkertijd wordt uitgevoerd, wordt beperkt.

## <a name="next-steps"></a>Volgende stappen

- [Elastische taken maken en beheren](elastic-jobs-overview.md)
- [Elastische taken maken en beheren met PowerShell](elastic-jobs-powershell-create.md)
- [Elastische taken maken en beheren met Transact-SQL (T-SQL)](elastic-jobs-tsql-create-manage.md)
