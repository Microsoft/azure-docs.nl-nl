---
title: Nieuwe tenants inrichten in een multitenant-app
description: Meer informatie over het inrichten en catalogiseren van nieuwe tenants in een Azure SQL Database-multitenant-SaaS-app
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 26add03929551c912b4d7b7cf10741d53333689a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780560"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Meer informatie over hoe u nieuwe tenants inricht en deze registreert in de catalogus
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze zelfstudie leert u hoe u SaaS-patronen kunt inrichten en catalogiseren. U leert ook hoe deze worden geïmplementeerd in de Wingtip Tickets-SaaS-database-per-tenant-toepassing. U maakt en initialiseert nieuwe tenantdatabases en registreert deze in de tenantcatalogus van de toepassing. De catalogus is een database waarin de toewijzing tussen de vele tenants van de SaaS-toepassing en hun gegevens wordt onderhouden. De catalogus speelt een belangrijke rol bij het doorsturen van toepassings- en beheeraanvragen naar de juiste database.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Eén nieuwe tenant inrichten.
> * Een batch met aanvullende tenants inrichten.


U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

* De Wingtip Tickets-SaaS-database-per-tenant-app is geïmplementeerd. Zie [De Wingtip Tickets-SaaS-database-per-tenant-toepassing implementeren en verkennen](./saas-dbpertenant-get-started-deploy.md) om de toepassing in minder dan vijf minuten te implementeren.
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie.

## <a name="introduction-to-the-saas-catalog-pattern"></a>Informatie over het SaaS-cataloguspatroon

In een multitenant-SaaS-toepassing met databases is het belangrijk om te weten waar informatie voor elke tenant wordt opgeslagen. In het SaaS-cataloguspatroon wordt een catalogusdatabase gebruikt om informatie te onderhouden over de toewijzing tussen tenants en de databases waarin hun gegevens worden opgeslagen. Dit patroon is van toepassing wanneer tenantgegevens over meerdere databases worden verdeeld.

Elke tenant wordt geïdentificeerd aan de hand van een sleutel in de catalogus die wordt toegewezen aan de locatie van de betreffende database. In de Wingtip Tickets-app wordt de sleutel gevormd op basis van een hash van de naam van de tenant. Met dit schema kan de app de sleutel maken aan de hand van de tenantnaam die is opgenomen in de toepassings-URL. Er kunnen ook andere tenantsleutelschema's worden gebruikt.

Dankzij de catalogus kan de naam of locatie van de database worden gewijzigd met minimale gevolgen voor de toepassing. In een model voor multitenantdatabases is deze mogelijkheid ook geschikt voor het verplaatsen van een tenant tussen databases. De catalogus kan ook worden gebruikt om aan te geven of een tenant of database offline is voor onderhoud of andere acties. U kunt deze mogelijkheid verkennen in de [zelfstudie Een individuele tenant terugzetten](saas-dbpertenant-restore-single-tenant.md).

In de catalogus kunnen ook aanvullende tenant- of databasemetagegevens worden opgeslagen, zoals de schemaversie, het serviceplan of de SLA's die aan tenants worden aangeboden. De catalogus kan andere informatie bevatten voor het beheer van toepassingen, klantondersteuning of DevOps.

Met de catalogus kunnen naast de SaaS-toepassing ook databasehulpprogramma's worden ingeschakeld. In het voorbeeld Wingtip Tickets-SaaS-database-per-tenant wordt de catalogus gebruikt voor het inschakelen van query's voor meerdere tenants. U kunt deze mogelijkheid verkennen in de [zelfstudie Ad-hocrapportage](saas-tenancy-cross-tenant-reporting.md). In de zelfstudies [Schemabeheer](saas-tenancy-schema-management.md) en [Tenantanalyses](saas-tenancy-tenant-analytics.md) verkent u hoe taken voor meerdere databases worden beheerd.

In de Wingtip Tickets-SaaS-voorbeelden wordt de catalogus geïmplementeerd met behulp van de Shard Management-functies van [Elastic Database Client Library (EDCL)](elastic-database-client-library.md). EDCL is beschikbaar in Java en .NET Framework. Een toepassing kan met EDCL een shard-toewijzing met databases maken, beheren en gebruiken.

Een shard-toewijzing bevat een lijst met shards (databases) en de toewijzing tussen sleutels (tenants) en shards. EDCL-functies worden gebruikt tijdens de inrichting van tenants om de vermeldingen in de shard-toewijzing te maken. Ze worden tijdens runtime gebruikt door toepassingen om verbinding te maken met de juiste database. EDCL slaat verbindingsgegevens op in de cache om het verkeer naar de catalogusdatabase tot een minimum te beperken en de toepassing sneller te maken.

> [!IMPORTANT]
> De toewijzingsgegevens zijn toegankelijk in de catalogusdatabase, maar u *mag ze niet bewerken*. Wijzig de toewijzingsgegevens alleen via API's van Elastic Database Client Library. Als u de toewijzingsgegevens rechtstreeks bewerkt, kan de catalogus worden beschadigd. Deze manier van bewerken wordt daarom niet ondersteund.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Informatie over het SaaS-inrichtingspatroon

Wanneer u een nieuwe tenant toevoegt in een SaaS-toepassing die gebruikmaakt van een één-tenant-databasemodel, moet u een nieuwe tenantdatabase inrichten. De database moet worden gemaakt op de juiste locatie en in de juiste servicelaag. Ook moet deze worden geïnitialiseerd met de juiste schema- en referentiegegevens. En de database moet worden geregistreerd in de catalogus onder de juiste tenantsleutel.

Er kunnen verschillende benaderingen voor het inrichten van databases worden gebruikt. U kunt SQL-scripts uitvoeren, een bacpac implementeren of een sjabloondatabase kopiëren.

De inrichting van de database moet deel uitmaken van uw schemabeheerstrategie. U moet ervoor zorgen dat nieuwe databases worden ingericht met het meest recente schema. Deze vereiste wordt verkend in de [zelfstudie Schemabeheer](saas-tenancy-schema-management.md).

Met de Wingtip Tickets-database-per-tenant-app worden nieuwe tenants ingericht door een sjabloondatabase met de naam _basetenantdb_ te kopiëren die op de catalogusserver wordt geïmplementeerd. Het inrichten kan worden geïntegreerd in de toepassing als onderdeel van een registratie-ervaring. Het kan ook offline worden ondersteund met behulp van scripts. In deze zelfstudie wordt het inrichten met behulp van PowerShell verkend.

De _basetenantdb_-database wordt met de inrichtingsscripts gekopieerd om een nieuwe tenantdatabase in een elastische pool te maken. De tenantdatabase wordt gemaakt in de tenantserver die is toegewezen aan de _newtenant_-DNS-alias. Deze alias onderhoudt een verwijzing naar de server die wordt gebruikt voor het inrichten van nieuwe tenants, en wordt bijgewerkt om te verwijzen naar een hersteltenantserver in de zelfstudies voor herstel na noodgevallen ([DR met georestore](./saas-dbpertenant-dr-geo-restore.md), [DR met georeplicatie](./saas-dbpertenant-dr-geo-replication.md)). De scripts initialiseren vervolgens de database met tenantspecifieke gegevens en registreren deze in de catalogus-shard-toewijzing. Tenantdatabases krijgen namen die zijn gebaseerd op de tenantnaam. Dit naamgevingsschema is geen essentieel onderdeel van het patroon. De catalogus wijst de tenantsleutel toe aan de databasenaam, zodat elke naamconventie kan worden gebruikt.


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>De Wingtip Tickets-SaaS-database-per-tenant-toepassingsscripts ophalen

De Wingtip Tickets-SaaS-scripts en broncode van de toepassing zijn beschikbaar in de GitHub-opslagplaats [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Bekijk de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen voor het downloaden en het deblokkeren van de Wingtip Tickets-SaaS-scripts.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Gedetailleerd overzicht van inrichten en catalogiseren

Als u wilt weten hoe met de toepassing Wingtip Tickets inrichting van nieuwe tenants wordt geïmplementeerd, voegt u een onderbrekingspunt toe en volgt u de werkstroom terwijl u een tenant inricht.

1. Open in PowerShell ISE ...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ en stel de volgende parameters in:

   * **$TenantName** = de naam van de nieuwe venue (bijvoorbeeld *Bushwillow Blues*).
   * **$VenueType** = een van de vooraf gedefinieerde typen venues: _blues, classicalmusic, dance, jazz, judo, motor racing, multipurpose, opera, rockmusic, soccer_.
   * **$DemoScenario** = **1**, *richt één tenant in*.

2. Als u een onderbrekingspunt wilt toevoegen, plaatst u de cursor ergens op de regel met de tekst *New-Tenant `* . Druk vervolgens op F9.

   ![Schermopname met een script waarin New-Tenant is gemarkeerd voor het toevoegen van een onderbrekingspunt.](./media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Druk op F5 om het script uit te voeren.

4. Wanneer de uitvoering van het script stopt bij het onderbrekingspunt, drukt u op F11 om de code te bekijken.

   ![Foutopsporing](./media/saas-dbpertenant-provision-and-catalog/debug.png)



Traceer de uitvoering van het script met behulp van de opties van het menu **Fouten opsporen**. Druk op F10 en F11 om de aangeroepen functies over te slaan of weer te geven. Zie [Tips voor het werken met en opsporen van fouten in PowerShell-scripts](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise) voor meer informatie over foutopsporing in PowerShell-scripts.


U hoeft deze werkstroom niet expliciet te volgen. Hierin wordt uitgelegd hoe u fouten in het script oplost.

* **Importeer de CatalogAndDatabaseManagement.psm1-module.** Deze module zorgt met behulp van de [Shard Management](elastic-scale-shard-map-management.md)-functies voor een abstractie op het niveau van catalogus en tenant. In deze module komen veel elementen van het cataloguspatroon aan bod. Het is zeker de moeite waard om de module goed te bestuderen.
* **Importeer de SubscriptionManagement.psm1-module.** Deze bevat functies voor het aanmelden bij Azure en het selecteren van het Azure-abonnement waarmee u wilt werken.
* **Haal configuratiedetails op**. Vraag de details van Get-Configuration op met behulp van F11 en kijk hoe de app-configuratie wordt opgegeven. Resourcenamen en andere app-specifieke waarden worden hier gedefinieerd. Wijzig deze waarden alleen wanneer u bekend bent met de scripts.
* **Haal het catalogusobject op.** Bekijk de details van Get-Catalog. Hiermee wordt een catalogusobject samengesteld en geretourneerd dat wordt gebruikt in het script op een hoger niveau. Deze functie maakt gebruik van Shard Management-functies die worden geïmporteerd vanuit **AzureShardManagement.psm1**. Het catalogusobject bestaat uit de volgende elementen:

   * $catalogServerFullyQualifiedName wordt samengesteld aan de hand van de standaardstam plus uw gebruikersnaam: _catalog-\<user\>.database.windows .net_.
   * $catalogDatabaseName wordt opgehaald uit de configuratie *tenantcatalog*.
   * Het object $shardMapManager wordt geïnitialiseerd uit de catalogusdatabase.
   * Het object $shardMap wordt geïnitialiseerd uit de shard map _tenantcatalog_ in de catalogusdatabase. Er wordt een catalogusobject gevormd en geretourneerd. Dit object wordt gebruikt in het script op een hoger niveau.
* **Bereken de nieuwe tenantsleutel.** Er wordt een hash-functie gebruikt om de tenant-sleutel te maken op basis van de naam van de tenant.
* **Controleer of de tenantsleutel al bestaat.** De catalogus wordt gecontroleerd om er zeker van te zijn dat de sleutel beschikbaar is.
* **De tenant-database wordt ingericht met behulp van New-TenantDatabase.** Gebruik F11 om te zien hoe de database wordt ingericht met behulp van een [Azure Resource Manager-sjabloon](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

    De databasenaam wordt samengesteld aan de hand van de naam van de tenant om duidelijk aan te geven welke shard bij welke tenant hoort. U kunt ook andere naamconventies voor databases gebruiken. Er wordt met een Resource Manager-sjabloon een tenantdatabase gemaakt door een sjabloondatabase (_baseTenantDB_) op de catalogusserver te kopiëren. Als alternatief kunt u een database maken en deze initialiseren door een bacpac te importeren. U kunt ook een initialisatiescript uitvoeren vanaf een bekende locatie.

    De Resource Manager-sjabloon bevindt zich in de map …\Learning Modules\Common\: *tenantdatabasecopytemplate.json*

* **De tenantdatabase wordt verder geïnitialiseerd.** De naam van de venue (tenant) en het type venue worden toegevoegd. U kunt hier ook andere initialisaties uitvoeren.

* **De tenantdatabase wordt geregistreerd in de catalogus.** Deze wordt geregistreerd met *Add-TenantDatabaseToCatalog* met behulp van de tenantsleutel. Gebruik F11 om de details van deze bewerkingen te bekijken:

    * De catalogusdatabase wordt toegevoegd aan de shard map (de lijst met bekende databases).
    * De toewijzing tussen de sleutelwaarde en de shard wordt gemaakt.
    * Aanvullende metagegevens over de tenant (de naam van de venue) worden toegevoegd aan de tabel Tenants in de catalogus. De tabel Tenants maakt geen deel uit van het Shard Management-schema en wordt niet geïnstalleerd door EDCL. In deze tabel ziet u hoe de catalogusdatabase kan worden uitgebreid om aanvullende toepassingsspecifieke gegevens te ondersteunen.


Als het inrichten is voltooid, wordt de uitvoering teruggegeven aan het oorspronkelijke *Demo-ProvisionAndCatalog*-script. De pagina **Gebeurtenissen** wordt geopend voor de nieuwe tenant in de browser.

   ![Pagina Gebeurtenissen](./media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Een batch met tenants inrichten

In deze oefening gaat u een batch met 17 tenants inrichten. U wordt aangeraden deze batch met tenants in te richten voordat u andere Wingtip Tickets-SaaS-database-per-tenant-zelfstudies start. Er zijn meerdere databases waarmee u kunt werken.

1. Open in PowerShell ISE ...\\Learning Modules\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1*. Wijzig de parameter *$DemoScenario* in 3:

   * **$DemoScenario** = **3**, *richt een batch met tenants in*.
2. Druk op F5 om het script uit te voeren.

Met het script worden verschillende tenants tegelijk ingericht. Voor het script wordt gebruik gemaakt van een [Azure Resource Manager-sjabloon](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) om de batch met tenants te bepalen en wordt de inrichting gedelegeerd van elke database aan een gekoppelde sjabloon. Door op deze manier sjablonen in te zetten, kan Azure Resource Manager als broker optreden voor het inrichtingsproces voor het script. Met de sjablonen worden databases parallel ingericht. Wanneer dit nodig is, worden er nieuwe pogingen gedaan. Het script is idempotent, dus als het om een bepaalde reden mislukt of stopt, wordt het opnieuw uitgevoerd.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Controleren of de batch met tenants is geïmplementeerd

* Ga in [Azure Portal](https://portal.azure.com) naar uw lijst met servers en open de *tenants1*-server. Selecteer **SQL databases** en controleer of de batch met 17 aanvullende databases nu in de lijst staat.

   ![Databaselijst](./media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Andere inrichtingspatronen

Andere inrichtingspatronen die niet aan bod zijn gekomen in deze zelfstudie:

**Databases vooraf inrichten**: Bij het patroon voor vooraf inrichten wordt gebruikgemaakt van het feit dat met databases in een elastische pool geen extra kosten worden toegevoegd. Er wordt gefactureerd voor de elastische pool, niet voor de databases. Niet-actieve databases verbruiken geen resources. Door databases vooraf in te richten in een pool en ze toe te wijzen wanneer dat nodig is, kunt u de tijd voor het toevoegen van tenants beperken. Het aantal databases dat vooraf wordt ingericht, kan naar wens worden aangepast om zo te beschikken over een buffer die overeenkomt met de verwachte vraag naar nieuwe tenants.

**Automatisch inrichten**: In het patroon voor automatisch inrichten worden met een inrichtingsservice automatisch zo nodig servers, pools en databases ingericht. Als u wilt, kunt u vooraf ingerichte databases in elastische pools opnemen. Als databases buiten gebruik worden gesteld en verwijderd, kunnen hiaten in elastische pools met de inrichtingsservice worden opgevuld. Een dergelijke service kan eenvoudig of complex zijn, zoals het afhandelen van inrichting in meerdere geografieën en het instellen van geo-replicatie voor herstel na noodgevallen.

In het geval van het patroon voor automatische inrichting wordt door een clienttoepassing of -script een aanvraag voor inrichting in de wachtrij gezet voor verwerking door de inrichtingsservice. Vervolgens wordt de service regelmatig bevraagd om te bepalen of de inrichting is voltooid. Als vooraf inrichten wordt gebruikt, worden aanvragen snel afgehandeld. De service richt een vervangende database op de achtergrond in.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
>
> * Eén nieuwe tenant inrichten.
> * Een batch met aanvullende tenants inrichten.
> * De details van het inrichten van tenants en het registreren in de catalogus bekijken.

Ga naar de [zelfstudie Prestatiebewaking](./saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Aanvullende bronnen

* Aanvullende [zelfstudies over de Wingtip Tickets-SaaS-database-per-tenant-toepassing](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Clientbibliotheek voor Elastic Database](elastic-database-client-library.md)
* [Fouten opsporen in scripts in Windows PowerShell ISE](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)