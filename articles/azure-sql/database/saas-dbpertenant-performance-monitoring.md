---
title: 'SaaS-app: Prestaties van veel databases bewaken'
description: Prestaties van Azure SQL Database in een multi-tenant SaaS-app bewaken en beheren
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 21c0a7a3fe6d5be9d99ea53dbfa74cf72e163272
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92780662"
---
# <a name="monitor-and-manage-performance-of-azure-sql-database-in-a-multi-tenant-saas-app"></a>Prestaties van Azure SQL Database in een multi-tenant SaaS-app bewaken en beheren
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze zelfstudie worden verschillende scenario's voor prestatiebeheer in SaaS-toepassingen besproken. Het gebruik van een load-generator voor het simuleren van activiteit in alle tenant-databases, de ingebouwde bewakings- en waarschuwingsfuncties van SQL Database en elastische pools worden gedemonstreerd.

De Wingtip Tickets SaaS Database Per Tenant-app gebruikt een gegevensmodel met één tenant, waarbij elke locatie (tenant) een eigen database heeft. Net als bij veel andere SaaS-toepassingen, is het verwachte tenantworkloadpatroon onvoorspelbaar en sporadisch. Met andere woorden, kaartverkoop kan op ieder moment plaatsvinden. Tenantdatabases worden geïmplementeerd in elastische pools om te profiteren van dit typerende patroon voor databasegebruik. Elastische pools verlagen de kosten van een oplossing door resources over veel databases te verdelen. Bij dit type patroon is het belangrijk om het gebruik van database- en poolresources te controleren om ervoor te zorgen dat loads goed over pools zijn verdeeld. Ook moet u ervoor zorgen dat individuele databases voldoende resources hebben en dat pools niet hun [eDTU](purchasing-models.md#dtu-based-purchasing-model)-limieten overschrijden. In deze zelfstudie worden manieren toegelicht om databases en pools te controleren en beheren. Ook wordt uitgelegd hoe u corrigerende maatregelen kunt nemen als reactie op wisselingen in de workload.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> 
> * Gebruik op de tenantdatabases simuleren door het uitvoeren van een geleverde load-generator
> * De tenant-databases bewaken terwijl ze reageren op de toename in load
> * Omhoog schalen van de elastische pool als reactie op de verhoogde databaseload
> * Een tweede elastische pool inrichten om taken te verdelen voor de databaseactiviteit


U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

* De Wingtip Tickets SaaS Database Per Tenant-app wordt geïmplementeerd. Zie [De Wingtip Tickets SaaS Database Per Tenant implementeren en verkennen per Tenanttoepassing](./saas-dbpertenant-get-started-deploy.md) om in minder dan vijf minuten te implementeren
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie.

## <a name="introduction-to-saas-performance-management-patterns"></a>Kennismaking met prestatiebeheerpatronen voor SaaS

Het beheren van de databaseprestaties bestaat uit het verzamelen en analyseren van prestatiegegevens en het reageren op deze gegevens door parameters aan te passen om een acceptabele reactietijd voor de toepassing te behouden. Wanneer u meerdere tenants host, zijn Elastische pools een rendabele manier om resources te bieden en te beheren voor een groep databases met onvoorspelbare workloads. Met bepaalde workloadpatronen kan het al bij twee S3-databases voordelig zijn om ze door een pool te laten beheren.

![diagram van de toepassing](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Pools en de databases in pools moeten worden gecontroleerd om ervoor te zorgen dat ze binnen een acceptabel prestatiebereik blijven. Stel de configuratie van de pool af op de vereisten van de cumulatieve workload van alle databases om ervoor te zorgen dat de eDTU's van de pools geschikt zijn voor de algemene workload. Pas de minimale en maximale eDTU-waarden per database aan naar de geschikte waarden voor de vereisten van uw specifieke toepassing.

### <a name="performance-management-strategies"></a>Strategieën voor prestatiebeheer

* Als u wilt voorkomen dat u handmatig prestaties moet bewaken, is **waarschuwingen instellen die worden geactiveerd wanneer databases of pools van het normale bereik afwijken** het effectiefst.
* Het **eDTU-niveau van een pool kan omhoog of omlaag worden geschaald** om te reageren op fluctuaties op de korte termijn in de cumulatieve rekenkracht van een pool. Als deze fluctuatie regelmatig of op voorspelbare momenten optreedt, **kan het schalen van de pool op automatisch worden ingesteld**. U kunt bijvoorbeeld omlaag schalen wanneer de workload licht is, zoals 's nachts of tijdens het weekend.
* U kunt ook **afzonderlijke databases naar andere pools verplaatsen** bij fluctuaties op de langere termijn of wijzigingen in het aantal databases.
* *Afzonderlijke* databases kunnen uit een pool worden gehaald. Hieraan kan een **afzonderlijke rekenkracht worden toegewezen als reactie op verhogingen op de korte termijn in de load van afzonderlijke databases**. Wanneer de load is verlaagd, kan de database naar de pool worden teruggezet. Als dit vooraf bekend is, kunnen databases uit voorzorg worden verplaatst om ervoor te zorgen dat ze altijd over de benodigde resources beschikken en om gevolgen voor de andere databases in de pool te voorkomen. Als het om een voorspelbare vereiste gaat, zoals bij een locatie die met een toename in kaartverkoop te maken krijgt voor een populair evenement, kan dit beheergedrag in de toepassing worden geïntegreerd.

[Azure Portal](https://portal.azure.com) biedt ingebouwde functionaliteit voor bewaking en waarschuwingen voor de meeste resources. Bewaking en waarschuwingen zijn beschikbaar voor databases en pools. De ingebouwde functionaliteit voor bewaking en waarschuwingen is resource-gebonden. Daardoor is het handig voor een klein aantal resources, maar niet erg handig als u met veel resources werkt.

Voor scenario's met grote volumes, waar u met veel resources werkt, kunnen [Azure Monitor-logboeken](./saas-dbpertenant-log-analytics.md) worden gebruikt. Dit is een afzonderlijke Azure-service die analyse van verzonden logboeken biedt in een Log Analytics-werkruimte. Azure Monitor-logboeken kunnen telemetriegegevens verzamelen van veel services en worden gebruikt om waarschuwingen op te vragen en in te stellen.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>De Wingtip Tickets SaaS Database Per Tenant-toepassingsscripts ophalen

De Wingtip Tickets SaaS-multitenantdatabasescripts en broncode van de toepassing zijn beschikbaar in de [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-opslagplaats. Bekijk de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen voor het downloaden en het deblokkeren van de Wingtip Tickets SaaS-scripts.

## <a name="provision-additional-tenants"></a>Extra tenants inrichten

Hoewel pools al met twee S3-databases rendabel kunnen zijn, geldt dat hoe meer databases er in de pool zitten, hoe rendabeler het berekenen van gemiddelden wordt. U hebt voor deze zelfstudie ten minste 20 geïmplementeerde databases nodig om een goed inzicht te krijgen in de werking van het bewaken en beheren van prestaties op schaal.

Als u al een batch tenants hebt ingericht in een eerdere zelfstudie, slaat u het gedeelte [Gebruik simuleren op alle tenantdatabases](#simulate-usage-on-all-tenant-databases) over.

1. Open in de **Power shell ISE** \\Leermodules\\Prestatiebewaking en -beheer\\*Demo-PerformanceMonitoringAndManagement.ps1*. Houd dit script open; u gaat verschillende scenario's uitvoeren tijdens deze zelfstudie.
1. Stel het volgende in: **$DemoScenario** = **1**, **Batch met tenants inrichten**
1. Druk op **F5** om het script uit te voeren.

Het script implementeert 17 tenants in minder dan vijf minuten.

Het nieuwe script *New-TenantBatch* gebruikt een geneste of gekoppelde set [Resource Manager](../../azure-resource-manager/index.yml)-sjablonen waarmee die een batch tenants wordt gemaakt. Hierbij wordt standaard de database **basetenantdb** gekopieerd op de catalogusserver om nieuwe tenantdatabases te maken. Vervolgens worden deze geregistreerd in de catalogus en ten slotte geïnitialiseerd met de tenantnaam en het locatietype. Dit komt overeen met de manier waarop de app een nieuwe tenant inricht. Wijzigingen die in *basetenantdb* worden aangebracht, worden toegepast op alle nieuwe tenants die hierna worden ingericht. Zie de zelfstudie [Schema Management](saas-tenancy-schema-management.md) (Schemabeheer) voor het aanbrengen van schemawijzigingen in *bestaande* tenantdatabases (inclusief de *basetenantdb* database).

## <a name="simulate-usage-on-all-tenant-databases"></a>Gebruik simuleren op alle tenantdatabases

Het script *Demo-PerformanceMonitoringAndManagement.ps1* is bedoeld voor het simuleren van een workload die wordt uitgevoerd op alle tenantdatabases. De load wordt gegenereerd met behulp van een van de beschikbare load-scenario's:

| Demo | Scenario |
|:--|:--|
| 2 | Load genereren met normale intensiteit (ongeveer 40 DTU's) |
| 3 | Load genereren met langere en frequentere bursts per database|
| 4 | Load genereren met meer DTU-bursts per database (ongeveer 80 DTU's)|
| 5 | Een normale load en hoge load genereren op één tenant (ongeveer 95 DTU's)|
| 6 | Niet-gebalanceerde load genereren voor meerdere pools|

De load-generator past een *synthetische* load alleen voor CPU toe op elke tenantdatabase. De generator start voor elke tenantdatabase een taak, die periodiek een opgeslagen procedure aanroept die de load genereert. De load-niveaus (in eDTU's), duur en intervallen zijn verschillend voor alle databases. Dit simuleert onvoorspelbare tenantactiviteit.

1. Open in de **Power shell ISE** \\Leermodules\\Prestatiebewaking en -beheer\\*Demo-PerformanceMonitoringAndManagement.ps1*. Houd dit script open; u gaat verschillende scenario's uitvoeren tijdens deze zelfstudie.
1. Stel het volgende in: **$DemoScenario** = **2**, *Load van normale intensiteit genereren*.
1. Druk op **F5** om een load toe te passen op al uw tenantdatabases.

Wingtip Tickets SaaS Database Per Tenant is een SaaS-app; de werkelijke workload van een SaaS-app is meestal sporadisch en onvoorspelbaar. Om dit te simuleren, produceert de load-generator een willekeurige workload die over alle tenants wordt verdeeld. Aangezien het een paar minuten duurt voordat er een load-patroon ontstaat, moet u de load-generator 3-5 minuten uitvoeren voordat u de workload in de volgende secties gaat volgen.

> [!IMPORTANT]
> De load-generator wordt uitgevoerd als een reeks taken in uw lokale PowerShell-sessie. Houd het tabblad *Demo-PerformanceMonitoringAndManagement.ps1* geopend. Als u het tabblad sluit of de computer onderbreekt, stopt de load-generator. De load-generator bevindt zich in een status *die taken aanroept* waarbij de load wordt gegenereerd voor eventuele nieuwe tenants die zijn ingericht nadat de generator is gestart. Gebruik *Ctrl-C* om het aanroepen van nieuwe taken te stoppen en het script af te sluiten. De load-generator blijft actief, maar alleen op bestaande tenants.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Gebruik van resources bewaken met Azure Portal

Open de portal naar de pool die de tenantdatabases bevat om het resourcegebruik bij te houden dat ontstaat door het toepassen van de load:

1. Open [Azure Portal](https://portal.azure.com) en blader naar de server *tenants1-dpt-&lt;USER&gt;* .
1. Scrol naar beneden, zoek de elastische pools en klik op **Pool1**. Deze pool bevat alle tenantdatabases die tot nu toe zijn gemaakt.

Bekijk de grafieken van **monitor van elastische pools** en **monitor van elastische databases**.

Het resourcegebruik van de pool is het cumulatieve databasegebruik voor alle databases in de pool. In het databasediagram worden de vijf meest belaste databases weergegeven:

![databasediagram](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Omdat er naast de top vijf nog extra databases in de pool zitten, toont het poolgebruik activiteit die niet wordt weergegeven in de grafiek voor de vijf meest belaste databases. Klik op **Gebruik van de databasebron** om meer gegevens te zien:

![Gebruik van de databasebron](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Prestatiewaarschuwingen voor de pool instellen

Stel een waarschuwing in voor de pool die wordt geactiveerd bij een gebruik van \>75%:

1. Open *Pool1* (op de server *tenants1-dpt-\<user\>* ) in [Azure Portal](https://portal.azure.com).
1. Klik op **Waarschuwingsregels** en klik vervolgens op **+ Waarschuwing toevoegen**:

   ![waarschuwing toevoegen](./media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Geef een naam op, bijvoorbeeld **hoog DTU**.
1. Stel de volgende waarden in:
   * **Metriek = eDTU-percentage**
   * **Voorwaarde = groter dan**
   * **Drempel = 75**
   * **Periode = In de afgelopen 30 minuten**
1. Voeg een e-mailadres toe aan het vak *Aanvullende e-mailadressen voor de administrator* en klik op **OK**.

   ![waarschuwing instellen](./media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Een zwaar belaste pool omhoog schalen

Als het cumulatieve loadniveau voor een pool in een dergelijke mate toeneemt dat de pool volledig wordt gebruikt en 100% eDTU-gebruik wordt bereikt, worden de prestaties van afzonderlijke databases beïnvloed. Hierdoor wordt de reactietijd voor query's mogelijk vertraagd voor alle databases in de pool.

Op de **korte termijn** kunt u de pool opschalen om extra resources te bieden of databases uit de pool verwijderen (door ze naar andere pools te verplaatsen of ze vanuit de pool in een zelfstandige servicelaag te plaatsen).

Op de **langere termijn** kunt u query's of indexgebruik optimaliseren voor betere databaseprestaties. Afhankelijk van de gevoeligheid voor prestatieproblemen van de toepassing, is de aanbevolen procedure om een pool op te schalen voordat 100% eDTU-gebruik wordt bereikt. Gebruik een waarschuwing zodat u tijdig wordt gewaarschuwd.

U kunt een zwaar belaste pool simuleren door de load die de generator produceert, te verhogen. Doordat de databases vaker en langer maximaal worden gebruikt, wordt de cumulatieve load voor de pool verhoogd zonder dat de vereisten van de afzonderlijke databases worden gewijzigd. U kunt de pool eenvoudig opschalen in de portal of vanuit PowerShell. In deze oefening wordt de portal gebruikt.

1. Stel *$DemoScenario* = **3**, _Load genereren met langere en frequentere bursts per database_ in om de intensiteit van de cumulatieve load voor de pool te verhogen zonder de piekbelasting die is vereist voor elke database te wijzigen.
1. Druk op **F5** om een load toe te passen op al uw tenantdatabases.

1. Ga in Azure Portal naar **Pool1**.

Controleer het verhoogde eDTU-verbruik voor de pool in de bovenste grafiek. Het duurt enkele minuten voordat de nieuwe, hogere load wordt geactiveerd, maar de pool moet vrij snel maximaal gebruik bereiken. Zodra de load een nieuw patroon vormt, wordt de pool snel overbelast.

1. Als u de pool omhoog wilt schalen, klikt u op **Pool configureren** bovenaan de pagina **Pool1**.
1. Stel de **Pool-eDTU** in op **100**. Als u de groeps-eDTU wijzigt, wordt de instelling per database niet gewijzigd (deze is nog steeds maximaal 50 eDTU's per database). De instellingen per database staan aan de rechterkant van de pagina **Pool configureren**.
1. Klik op **Opslaan** om de aanvraag te verzenden om de pool te schalen.

Ga terug naar **Pool1** > **Overzicht** om de bewakingsgrafieken weer te geven. Bewaak het effect van de toevoeging van meer resources aan de pool (met weinig databases en een willekeurige load is het niet altijd duidelijk te bepalen totdat u al enige tijd bezig bent met de uitvoering). Houd er rekening mee dat 100% bij de bovenste grafiek nu staat voor 100 eDTU's, terwijl voor de onderste grafiek 100% nog steeds 50 eDTU's betekent, omdat het maximum per database 50 eDTU's is.

Databases blijven gedurende het proces online en volledig beschikbaar. Op het laatste moment, wanneer elke database gereed is om te worden ingeschakeld met de nieuwe pool-eDTU's, worden alle actieve verbindingen verbroken. Om verbroken verbindingen opnieuw te proberen, moet altijd een toepassingscode worden geschreven zodat er opnieuw verbinding wordt gemaakt met de database in de omhoog geschaalde pool.

## <a name="load-balance-between-pools"></a>Taakverdeling tussen pools

In plaats van de pool omhoog te schalen, kunt u ook een tweede pool maken en databases hiernaartoe verplaatsen om de load tussen de twee pools te verdelen. Om dit te doen, moet de nieuwe pool op dezelfde server als de eerste pool worden gemaakt.

1. In [Azure Portal](https://portal.azure.com) opent u de server **tenants1-dpt-&lt;USER&gt;** .
1. Klik op **+ Nieuwe pool** om een pool op de huidige server te maken.
1. Op de sjabloon **Elastische pool**:

   1. Stel **Naam** in als *Pool2*.
   1. Laat de prijscategorie op **Standaardpool** staan.
   1. Klik op **Groep configureren**.
   1. Stel **Pool-eDTU** in op *50 eDTU*.
   1. Klik op **Databases toevoegen** om een lijst met databases op de server weer te geven die kunnen worden toegevoegd aan *Pool2*.
   1. Selecteer tien databases om deze naar de nieuwe groep te verplaatsen en klik vervolgens op **Selecteren**. Als u de load-generator hebt uitgevoerd, weet de service al dat voor uw prestatieprofiel een grotere groep is vereist dan de standaardgrootte van 50 eDTU en wordt u aangeraden te beginnen met een instelling van 100 eDTU.

      ![aanbeveling](./media/saas-dbpertenant-performance-monitoring/configure-pool.png)

   1. Voor deze zelfstudie houdt u de standaardwaarde op 50 eDTU en klikt u opnieuw op **Selecteren**.
   1. Selecteer **OK** om de nieuwe pool te maken en de geselecteerde databases ernaar te verplaatsen.

Het maken van de pool en het verplaatsen van de databases duurt enkele minuten. Databases die worden verplaatst, blijven online en volledig toegankelijk tot op het allerlaatste moment. Hierna worden alle open verbindingen gesloten. Als u Logica voor opnieuw proberen hebt, worden clients vervolgens verbonden met de database in de nieuwe pool.

Blader naar **Pool2** (op de server *tenants1-dpt-\<user\>* ) om de pool te openen en de prestaties te monitoren. Als u deze niet ziet, wacht u totdat de nieuwe pool is ingericht.

U ziet nu dat het resourcegebruik voor *Pool1* is gedaald en dat *Pool2* evenveel is belast.

## <a name="manage-performance-of-an-individual-database"></a>Prestaties van een afzonderlijke database beheren

Als een individuele database in een pool langere tijd een hogere load heeft, kan het de resources in de pool overheersen en andere databases beïnvloeden, afhankelijk van de poolconfiguratie. Als de activiteit mogelijk nog enige tijd voortduurt, kan de database tijdelijk uit de pool worden geplaatst. Op deze manier kan de database de extra resources bevatten die nodig zijn en deze isoleren van de andere databases.

Deze oefening simuleert het effect van een hoge load voor Contoso Concert Hall wanneer kaartjes voor een populair concert in de verkoop gaan.

1. Open het script **Demo-PerformanceMonitoringAndManagement.ps1** in \\*PowerShell ISE*.
1. Stel het volgende in: **$DemoScenario = 5, Een normale load en hoge load genereren op één tenant (ongeveer 95 DTU's).**
1. Stel **$SingleTenantDatabaseName = contosoconcerthall** in
1. Voer het script uit met **F5**.


1. Ga in [Azure Portal](https://portal.azure.com) naar de lijst met databases op de server *tenants1-dpt-\<user\>* . 
1. Klik op de database **contosoconcerthall**.
1. Klik op de pool waarin **contosoconcerthall** zich bevindt. Zoek de pool op in de sectie **Elastische pool**.

1. Inspecteer de grafiek **Elastische pool-bewaking** en zoek het toegenomen eDTU-gebruik van de pool. Na een paar minuten moet de hogere load actief worden en zou de pool snel een gebruik van 100% moeten weergeven.
2. Controleer de weergave **Elastische database-bewaking**, waarin de meest belaste databases van het afgelopen uur worden weergegeven. De database *contosoconcerthall* moet snel worden weergegeven als een van de vijf meest belaste databases.
3. Klik op het **diagram** **Bewaking van elastische database**. De pagina **Gebruik van de databasebron** wordt geopend, waarop u de databases kunt bewaken. Hiermee kunt u de weergave voor de database *contosoconcerthall* isoleren.
4. Klik in de lijst met databases op **contosoconcerthall**.
5. Klik op **Prijscategorie (DTU's schalen)** om de pagina **Prestaties configureren** te openen, waar u een op zichzelf staande rekenkracht voor de database kunt instellen.
6. Klik op het tabblad **Standard** om de schaalopties in de categorie Standard te openen.
7. Schuif de schuifregelaar **DTU** naar rechts om **100** DTU's te selecteren. Opmerking: dit komt overeen met de servicedoelstelling **S3**.
8. Klik op **Toepassen** om de database uit de pool te plaatsen en er een *Standard S3*-database van te maken.
9. Wanneer het schalen is voltooid, controleert u het effect op de contosoconcerthall-database en Pool1 op de blades elastische pool en database.

Zodra de hoge load van de database contosoconcerthall afneemt, plaatst u deze direct terug in de pool om de kosten te beperken. Als het onduidelijk is wanneer dit gaat gebeuren, kunt u een waarschuwing voor de database instellen die wordt geactiveerd wanneer het DTU-verbruik onder het maximum per database voor de pool zakt. Het verplaatsen van een database naar een pool wordt beschreven in oefening 5.

## <a name="other-performance-management-patterns"></a>Andere patronen voor prestatiebeheer

**Preventief schalen** In de oefening hierboven, waarin u hebt geleerd hoe u een geïsoleerde database kunt schalen, wist u welke database u moest zoeken. Als het management van Contoso Concert Hall Wingtips op de hoogte had gesteld van de aanstaande kaartverkoop, had de database preventief uit de pool kunnen worden geplaatst. Anders was er waarschijnlijk een waarschuwing voor de pool of de database nodig geweest om te zien wat er aan de hand was. U wilt dit liever niet te weten komen doordat andere tenants in de pool klagen over verminderde prestaties. Als de tenant kan voorspellen hoe lang deze aanvullende resources nodig heeft, kunt u een Azure Automation-runbook instellen om de database uit de pool te plaatsen en later weer terug de plaatsen volgens een ingesteld schema.

**Self-service schalen voor tenants** Omdat schalen een taak is die eenvoudig via de Management-API kan worden aangeroepen, kunt u gemakkelijk de mogelijkheid om tenantdatabases te schalen inbouwen in uw tenantgerichte toepassing en deze als een functie van uw SaaS-service aanbieden. Laat tenants bijvoorbeeld zelf omhoog en omlaag schalen beheren, mogelijk gekoppeld aan hun facturering.

**Een pool omhoog en omlaag schalen volgens een schema om aan gebruikspatronen te voldoen**

Wanneer het cumulatieve tenantgebruik een voorspelbaar patroon volgt, kunt u Azure Automation gebruiken om een pool volgens een schema omhoog en omlaag te schalen. Schaal een pool bijvoorbeeld omlaag na 18:00 uur en weer omhoog vóór 06:00 op dagen waarvan u weet dat er een afname in de benodigde resources is.



## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gebruik op de tenantdatabases simuleren door het uitvoeren van een geleverde load-generator
> * De tenant-databases bewaken terwijl ze reageren op de toename in load
> * Omhoog schalen van de elastische pool als reactie op de verhoogde databaseload
> * Een tweede elastische pool inrichten om taken te verdelen voor de databaseactiviteit

[Zelfstudie Een individuele tenant herstellen](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>Aanvullende bronnen

* Aanvullende [zelfstudies over de Wingtip Tickets SaaS Database Per Tenant-toepassingsimplementatie](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastische SQL-pools](elastic-pool-overview.md)
* [Azure Automation](../../automation/automation-intro.md)
* [Azure Monitor-logboeken](./saas-dbpertenant-log-analytics.md) - Azure Monitor-logboeken instellen en gebruiken, zelfstudie