---
title: De prestaties van een Sharddatabase met meerdere tenants controleren
description: Prestaties van sharded Azure SQL Database met meerdere tenant in een SaaS-app met meerdere tenants bewaken en beheren
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
ms.openlocfilehash: d37bf2c84b74dba76e5d1921ed67072af7f6c328
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790896"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Prestaties van sharded Azure SQL Database met meerdere tenant in een SaaS-app met meerdere tenants bewaken en beheren
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze zelfstudie worden verschillende scenario's voor prestatiebeheer in SaaS-toepassingen besproken. Het gebruik van een load-generator voor het simuleren van activiteit in sharded databases met meerdere tenants, de ingebouwde bewakings- en waarschuwingsfuncties van Azure SQL Database worden gedemonstreerd.

De Wingtip Tickets SaaS-database-app met meerdere tenants maakt gebruik van een sharded gegevensmodel, waarbij locatiegegevens (tenant) worden verspreid op tenant-ID over (mogelijk) meerdere databases. Net als bij veel andere SaaS-toepassingen, is het verwachte tenantworkloadpatroon onvoorspelbaar en sporadisch. Met andere woorden, kaartverkoop kan op ieder moment plaatsvinden. Om gebruik te maken van dit typische gebruikspatroon voor databases, kunnen databases omhoog en omlaag worden geschaald om de kosten van een oplossing te optimaliseren. Bij dit type patroon is het belangrijk om het gebruik van databaseresources te controleren om ervoor te zorgen dat loads goed over mogelijk meerdere databases zijn verdeeld. Ook moet u ervoor zorgen dat individuele databases voldoende resources hebben en hun [eDTU](purchasing-models.md#dtu-based-purchasing-model)-limieten niet overschrijden. In deze zelfstudie worden manieren toegelicht om databases te controleren en beheren. Ook wordt uitgelegd hoe u corrigerende maatregelen kunt nemen als reactie op wisselingen in de workload.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> 
> * Gebruik op de sharded databases met meerdere tenants simuleren door het uitvoeren van een geleverde load-generator
> * De database bewaken terwijl deze reageert op de toegenomen belasting
> * De database omhoog schalen als reactie op de verhoogde databaseload
> * Een tenant inrichten in een database met één tenant

U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

* De Wingtip Tickets SaaS Multi-tenant Database-app wordt geïmplementeerd. Zie [De Wingtip Tickets SaaS Multi-tenant Database-toepassing implementeren en verkennen](./saas-multitenantdb-get-started-deploy.md) om deze binnen vijf minuten te implementeren
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie.

## <a name="introduction-to-saas-performance-management-patterns"></a>Kennismaking met prestatiebeheerpatronen voor SaaS

Het beheren van de databaseprestaties bestaat uit het verzamelen en analyseren van prestatiegegevens en het reageren op deze gegevens door parameters aan te passen om een acceptabele reactietijd voor de toepassing te behouden. 

### <a name="performance-management-strategies"></a>Strategieën voor prestatiebeheer

* Als u wilt voorkomen dat u handmatig prestaties moet bewaken, is **waarschuwingen instellen die worden geactiveerd wanneer databases van het normale bereik afwijken** het effectiefst.
* Het **eDTU-niveau kan omhoog of omlaag worden geschaald** om te reageren op fluctuaties op de korte termijn in de rekenkracht van een database. Als deze fluctuatie regelmatig of op voorspelbare momenten optreedt, **kan het schalen van de database op automatisch worden ingesteld**. U kunt bijvoorbeeld omlaag schalen wanneer de workload licht is, zoals 's nachts of tijdens het weekend.
* U kunt ook **afzonderlijke tenants naar andere databases verplaatsen** bij fluctuaties op de langere termijn of wijzigingen in de tenants.
* *Afzonderlijke* tenants kunnen uit een database worden gehaald. Hieraan kan een **afzonderlijke rekenkracht worden toegewezen als reactie op verhogingen op de korte termijn in de load van afzonderlijke tenants**. Wanneer de load is verlaagd, kan de tenant naar de database met meerdere tenants worden teruggezet. Als dit vooraf bekend is, kunnen tenants uit voorzorg worden verplaatst om ervoor te zorgen dat ze altijd over de benodigde resources beschikken en om gevolgen voor de andere tenants in de database met meerdere tenants te voorkomen. Als het om een voorspelbare vereiste gaat, zoals bij een locatie die met een toename in kaartverkoop te maken krijgt voor een populair evenement, kan dit beheergedrag in de toepassing worden geïntegreerd.

[Azure Portal](https://portal.azure.com) biedt ingebouwde functionaliteit voor bewaking en waarschuwingen voor de meeste resources. Voor SQL Database zijn bewaking en waarschuwingen beschikbaar voor databases. De ingebouwde functionaliteit voor bewaking en waarschuwingen is resource-gebonden. Daardoor is het handig voor een klein aantal resources, maar niet handig als u met veel resources werkt.

Voor scenario's met grote volumes, waar u met veel resources werkt, kunnen [Azure Monitor-logboeken](https://azure.microsoft.com/services/log-analytics/) worden gebruikt. Dit is een afzonderlijke Azure-service die analyse van verzonden logboeken biedt in een Log Analytics-werkruimte. Azure Monitor-logboeken kunnen telemetriegegevens verzamelen van veel services en worden gebruikt om waarschuwingen op te vragen en in te stellen.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>De broncode en scripts van de Wingtip Tickets SaaS-databasetoepassing downloaden

De Wingtip Tickets SaaS-multitenantdatabasescripts en broncode van de toepassing zijn beschikbaar in de GitHub-opslagplaats [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB). Bekijk de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen voor het downloaden en het deblokkeren van de Wingtip Tickets-SaaS-scripts.

## <a name="provision-additional-tenants"></a>Extra tenants inrichten

U hebt voor deze zelfstudie meerdere tenants in een sharded database met meerdere tenants nodig om een goed inzicht te krijgen in de werking van het bewaken en beheren van prestaties op schaal.

Als u al een batch tenants hebt ingericht in een eerdere zelfstudie, slaat u het gedeelte [Gebruik simuleren op alle tenantdatabases](#simulate-usage-on-all-tenant-databases) over.

1. Open in de **Power shell ISE** \\Leermodules\\Prestatiebewaking en -beheer\\*Demo-PerformanceMonitoringAndManagement.ps1*. Houd dit script open; u gaat verschillende scenario's uitvoeren tijdens deze zelfstudie.
1. Stel het volgende in: **$DemoScenario** = **1**, _Batch met tenants inrichten_
1. Druk op **F5** om het script uit te voeren.

Het script implementeert 17 tenants in een paar minuten in de databases met meerdere tenants. 

Met het script *New-TenantBatch* maakt u nieuwe tenants met unieke tenantsleutels in de sharded database met meerdere tenants en initialiseert u deze met de tenantnaam en het locatietype. Dit komt overeen met de manier waarop de app een nieuwe tenant inricht. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Gebruik simuleren op alle tenantdatabases

Het script *Demo-PerformanceMonitoringAndManagement.ps1* is bedoeld voor het simuleren van een workload die wordt uitgevoerd op alle databases met meerdere tenants. De load wordt gegenereerd met behulp van een van de beschikbare load-scenario's:

| Demo | Scenario |
|:--|:--|
| 2 | Load genereren met normale intensiteit (ongeveer 30 DTU's) |
| 3 | Load genereren met langere pieken per tenant|
| 4 | Load genereren met meer DTU-bursts per tenant (ongeveer 70 DTU's)|
| 5 | Een load met hoge intensiteit genereren (ongeveer 90 DTU) op één tenant plus een load met een normale intensiteit op alle andere tenants |

De load-generator past een *synthetische* load alleen voor CPU toe op elke tenantdatabase. De generator start voor elke tenantdatabase een taak, die periodiek een opgeslagen procedure aanroept die de load genereert. De load-niveaus (in DTU's), duur en intervallen zijn verschillend voor alle databases. Dit simuleert onvoorspelbare tenantactiviteit.

1. Open in de **Power shell ISE** \\Leermodules\\Prestatiebewaking en -beheer\\*Demo-PerformanceMonitoringAndManagement.ps1*. Houd dit script open; u gaat verschillende scenario's uitvoeren tijdens deze zelfstudie.
1. Stel het volgende in: **$DemoScenario** = **2**, _Load van normale intensiteit genereren_
1. Druk op **F5** om een load toe te passen op al uw tenants.

Wingtip Tickets SaaS-database met meerdere tenants is een SaaS-app; de werkelijke workload van een SaaS-app is meestal sporadisch en onvoorspelbaar. Om dit te simuleren, produceert de load-generator een willekeurige workload die over alle tenants wordt verdeeld. Aangezien het een paar minuten duurt voordat er een load-patroon ontstaat, moet u de load-generator 3-5 minuten uitvoeren voordat u de workload in de volgende secties gaat volgen.

> [!IMPORTANT]
> De load-generator wordt uitgevoerd als een reeks taken in een nieuw PowerShell-venster. Als u de sessie sluit, stopt de loadgenerator. De load-generator bevindt zich in een status *die taken aanroept* waarbij de load wordt gegenereerd voor eventuele nieuwe tenants die zijn ingericht nadat de generator is gestart. Gebruik *Ctrl-C* om het aanroepen van nieuwe taken te stoppen en het script af te sluiten. De load-generator blijft actief, maar alleen op bestaande tenants.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Gebruik van resources bewaken met Azure Portal

Open het portaal naar de database met meerdere tenants, **tenants1**, die de tenants bevat om het resourcegebruik bij te houden dat ontstaat door het toepassen van de load:

1. Open [Azure Portal](https://portal.azure.com) en blader naar de server *tenants1-mt-&lt;USER&gt;* .
1. Schuif omlaag, zoek naar databases en klik op **tenants1**. Deze sharded database met meerdere tenants bevat alle tenants die tot nu toe zijn gemaakt.

![databasediagram](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Bekijk de **DTU**-grafiek.

## <a name="set-performance-alerts-on-the-database"></a>Prestatiewaarschuwingen voor de database instellen

Stel een waarschuwing in voor de database die wordt geactiveerd bij een gebruik van \>75%:

1. Open de database *tenants1* (op de server *tenants1-mt-&lt;USER&gt;* ) in het [Azure-portaal](https://portal.azure.com).
1. Klik op **Waarschuwingsregels** en klik vervolgens op **+ Waarschuwing toevoegen**:

   ![waarschuwing toevoegen](./media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Geef een naam op, bijvoorbeeld **hoog DTU**.
1. Stel de volgende waarden in:
   * **Meetwaarde = DTU-percentage**
   * **Voorwaarde = groter dan**
   * **Drempel = 75**.
   * **Periode = In de afgelopen 30 minuten**
1. Voeg een e-mailadres toe aan het vak *Aanvullende e-mailadressen voor de administrator* en klik op **OK**.

   ![waarschuwing instellen](./media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Een zwaar belaste database omhoog schalen

Als het loadniveau voor een database in een dergelijke mate toeneemt dat de database volledig wordt gebruikt en 100% eDTU-gebruik wordt bereikt, worden de prestaties van databases beïnvloed, met mogelijk tragere query-reactietijden tot gevolg.

Overweeg op **korte termijn** om de database omhoog te schalen om aanvullende resources te voorzien, of om tenants te verwijderen uit de database met meerdere tenants (door ze te verwijderen uit de database met meerdere tenants naar een zelfstandige database).

Op de **langere termijn** kunt u query's of indexgebruik optimaliseren voor betere databaseprestaties. Afhankelijk van de gevoeligheid voor prestatieproblemen van de toepassing, is de aanbevolen procedure om een database op te schalen voordat 100% DTU-gebruik wordt bereikt. Gebruik een waarschuwing zodat u tijdig wordt gewaarschuwd.

U kunt een zwaar belaste database simuleren door de load die de generator produceert, te verhogen. Doordat de tenants vaker en langer maximaal worden gebruikt, wordt de load voor de database met meerdere tenants verhoogd zonder dat de vereisten van de afzonderlijke tenants worden gewijzigd. U kunt de database eenvoudig opschalen in de portal of vanuit PowerShell. In deze oefening wordt de portal gebruikt.

1. Stel *$DemoScenario* = **3**, _Load genereren met langere en frequentere bursts per database_ in om de intensiteit van de cumulatieve load voor de database te verhogen zonder de piekbelasting die is vereist voor elke tenant te wijzigen.
1. Druk op **F5** om een load toe te passen op al uw tenantdatabases.
1. Ga naar de database **tenants1** in het Azure-portaal.

Controleer het verhoogde DTU-verbruik voor de database in de bovenste grafiek. Het duurt enkele minuten voordat de nieuwe, hogere load wordt geactiveerd, maar de database moet vrij snel maximaal gebruik bereiken. Zodra de load een nieuw patroon vormt, wordt de database snel overbelast.

1. Als u de database wilt opschalen, klikt u op **Prijscategorie (DTU's omhoog schalen)** op de blade Instellingen.
1. Stel de **DTU** in op **100**. 
1. Klik op **Toepassen** om de aanvraag te verzenden om de database te schalen.

Ga terug naar het **tenants1** > **Overzicht** om de bewakingsgrafieken weer te geven. Bewaak het effect van de toevoeging van meer resources aan de database (met weinig tenants en een willekeurige load is het niet altijd duidelijk te bepalen totdat u al enige tijd bezig bent met de uitvoering). Houd er rekening mee dat 100% bij de bovenste grafiek nu staat voor 100 DTU's, terwijl voor de onderste grafiek 100% nog steeds 50 DTU's is.

Databases blijven gedurende het proces online en volledig beschikbaar. Om verbroken verbindingen opnieuw te proberen, moet altijd een toepassingscode worden geschreven zodat er opnieuw verbinding wordt gemaakt met de database.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Een nieuwe tenant in de eigen database inrichten 

Met het sharded model voor meerdere tenants kunt u kiezen of u een nieuwe tenant wilt inrichten in een database met meerdere tenants naast andere tenants, of om de tenant in te richten in een eigen database. Door een tenant in een eigen database in te richten, profiteert u van de isolatie van de afzonderlijke database, zodat u de prestaties van die tenant onafhankelijk van anderen kunt beheren, die tenant onafhankelijk van anderen kunt herstellen, enzovoort. U kunt bijvoorbeeld kiezen om klanten met een gratis proefversie of gewone klanten in een database met meerdere tenants te plaatsen, en Premium-klanten in afzonderlijke databases.  Als er geïsoleerde database voor één tenant worden gemaakt, kunnen ze gezamenlijk worden beheerd in een elastische pool om resourcekosten te optimaliseren.

Als u al een nieuwe tenant in een eigen database hebt ingericht, kunt u de volgende stappen overslaan.

1. Open in **PowerShell ISE** ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*. 
1. Wijzig **$TenantName = "Salix Salsa"** en **$VenueType  = "dance"**
1. Stel **$Scenario** = **2**, _Richt een tenant in met een nieuwe database voor één tenant_
1. Druk op **F5** om het script uit te voeren.

Het script richt deze tenant in met een afzonderlijke database, registreert de database en de tenant met de catalogus en opent vervolgens de Gebeurtenispagina van de tenant in de browser. Vernieuw de pagina Event Hub en u ziet dat  "Salix Salsa" is toegevoegd als locatie.

## <a name="manage-performance-of-an-individual-database"></a>Prestaties van een afzonderlijke database beheren

Als één tenant in een database met meerdere tenants voortdurend zwaar belast wordt, is het mogelijk dat het de resources van de databases domineert en dat andere tenants in dezelfde database hinder ondervinden; Als de activiteit mogelijk nog enige tijd voortduurt, kan de tenant tijdelijk uit de database worden verplaatst, naar zijn eigen database voor één tenant. Op deze manier kan de tenant de extra resources bevatten die nodig zijn en deze volledig isoleren van de andere tenants.

Deze oefening simuleert het effect van een hoge load voor Salix Salsa wanneer kaartjes voor een populair evenement in de verkoop gaan.

1. Open het script …\\*Demo-PerformanceMonitoringAndManagement.ps1*.
1. Stel het volgende in: **$DemoScenario = 5**, _Een normale load en hoge load genereren op één tenant (ongeveer 90 DTU's)._
1. Stel **$SingleTenantName = Salix Salsa**
1. Voer het script uit met **F5**.

Ga naar het portaal en navigeer naar **salixsalsa** > **Overzicht** om de controlegrafieken weer te geven. 

## <a name="other-performance-management-patterns"></a>Andere patronen voor prestatiebeheer

**Selfservice omhoog schalen voor tenants**

Omdat schalen een taak is die eenvoudig via de Management-API kan worden aangeroepen, kunt u gemakkelijk de mogelijkheid om tenantdatabases te schalen inbouwen in uw tenantgerichte toepassing en deze als een functie van uw SaaS-service aanbieden. Laat tenants bijvoorbeeld zelf omhoog en omlaag schalen beheren, mogelijk gekoppeld aan hun facturering.

**Een database omhoog en omlaag schalen volgens een schema om aan gebruikspatronen te voldoen**

Wanneer het cumulatieve tenantgebruik een voorspelbaar patroon volgt, kunt u Azure Automation gebruiken om een database volgens een schema omhoog en omlaag te schalen. Schaal een database bijvoorbeeld omlaag na 18:00 uur en weer omhoog vóór 06:00 op dagen waarvan u weet dat er een afname in de benodigde resources is.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gebruik op de sharded databases met meerdere tenants simuleren door het uitvoeren van een geleverde load-generator
> * De database bewaken terwijl deze reageert op de toegenomen belasting
> * De database omhoog schalen als reactie op de verhoogde databaseload
> * Een tenant inrichten in een database met één tenant

## <a name="additional-resources"></a>Aanvullende bronnen

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure Automation](../../automation/automation-intro.md)