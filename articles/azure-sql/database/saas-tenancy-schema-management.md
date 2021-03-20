---
title: Schema beheren in een app met één tenant
description: Schema voor meerdere tenants beheren in een app met één tenant die gebruikmaakt van Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/19/2018
ms.openlocfilehash: e4328be0aade0658dedb034dbbb6980b810f771a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793191"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Schema beheren in een SaaS-toepassing met behulp van het database-per-tenant-patroon met Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
Naarmate een databasetoepassing evolueert, moeten er onvermijdelijk wijzigingen worden aangebracht in het databaseschema of de referentiegegevens.  Onderhoudstaken voor de database zijn ook regelmatig nodig. Als u een toepassing wilt beheren die gebruikmaakt van het database-per-tenant-patroon, moet u deze wijzigingen of onderhoudstaken toepassen op een fleet van tenantdatabases.

In deze zelfstudie worden twee scenario's beschreven: referentiegegevensupdates implementeren voor alle tenants en een opnieuw index bouwen van de tabel met de referentiegegevens. De functie [Elastische taken](./elastic-jobs-overview.md) wordt gebruikt om deze acties uit te voeren voor alle tenantdatabases, en voor de sjabloondatabase die gebruikt wordt om nieuwe tenantdatabases te maken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> 
> * Een taakagent maken
> * Ervoor zorgen dat T-SQL-taken worden uitgevoerd op alle tenantdatabases
> * Referentiegegevens in alle tenantdatabases bijwerken
> * Een index in een tabel maken in alle tenantdatabases


Voor het voltooien van deze zelfstudie moet u ervoor zorgen dat aan de volgende vereisten is voldaan:

* De Wingtip Tickets SaaS Database Per Tenant-app wordt geïmplementeerd. Zie [De Wingtip Tickets-SaaS-database-per-tenant-toepassing implementeren en verkennen](./saas-dbpertenant-get-started-deploy.md) om in minder dan vijf minuten te implementeren
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie.
* De laatste versie van SQL Server Management Studio (SSMS) moet zijn geïnstalleerd. [SSMS downloaden en installeren](/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="introduction-to-saas-schema-management-patterns"></a>Kennismaking met patronen voor SaaS-schemabeheer

Het database-per-tenant-patroon isoleert tenantgegevens effectief, maar verhoogt het aantal databases dat beheerd en onderhouden moet worden. [Elastische taken](./elastic-jobs-overview.md) vereenvoudigt het onderhoud en het beheer van meerdere databases. Met Elastische taken kunt u veilig en betrouwbaar taken uitvoeren (T-SQL-scripts) voor een groep databases. Taken kunnen wijzigingen in het schema en in de algemene referentiegegevens implementeren voor alle tenantdatabases in een toepassing. Elastische taken kan ook worden gebruikt om een *sjabloon* van de gebruikte database op te slaan voor het maken van nieuwe tenants. Op die manier beschikken deze altijd over het nieuwste schema en de meest recente referentiegegevens.

![scherm](./media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-public-preview"></a>Openbare preview Elastische taken

Er is een nieuwe versie beschikbaar van Elastische taken; dit is nu een ingebouwde functie in Azure SQL Database. Van deze nieuwe versie van Elastische taken is momenteel een openbare preview beschikbaar. Deze openbare preview biedt ondersteuning voor PowerShell voor het maken van een taakagent en voor T-SQL voor het maken en beheren van taken.
Raadpleeg het artikel over [Elastic Database-taken](./elastic-jobs-overview.md) voor meer informatie.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>De Wingtip Tickets-SaaS-database-per-tenant-toepassingsscripts ophalen

De broncode en beheerscripts van de app zijn beschikbaar in de GitHub-opslagplaats [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Bekijk de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen voor het downloaden en het deblokkeren van de Wingtip Tickets-SaaS-scripts.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Een taakagentdatabase en een nieuwe taakagent maken

In deze zelfstudie moet u PowerShell gebruiken om een taakagent en de achterliggende taakagentdatabase te maken. De taakagentdatabase bevat taakdefinities, de taakstatus en de geschiedenis. Wanneer de taakagent en de database zijn gemaakt, kunt u direct aan de slag met het maken en bewaken van taken.

1. Open **in PowerShell ISE** \\Learning Modules\\Schema Management\\*Demo-SchemaManagement.ps1*.
1. Druk op **F5** om het script uit te voeren.

Met het script *Demo-SchemaManagement.ps1* wordt het script *Deploy-SchemaManagement.ps1* aangeroepen om op de catalogusserver een database te maken met de naam *osagent*. Vervolgens wordt de taakagent gemaakt met behulp van de database als een parameter.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Een taak maken om nieuwe referentiegegevens te implementeren voor alle tenants

In de Wingtip tickets-app bevat elke tenantdatabase een set ondersteunde locatietypen. Elke locatie behoort tot een specifiek locatietype, dat bepaalt welke soorten gebeurtenissen er kunnen worden gehouden, en de achtergrondafbeelding gebruikt in de app. Deze referentiegegevens moeten worden bijgewerkt en nieuwe locatietypen worden toegevoegd zodat de toepassing nieuwe soorten gebeurtenissen kan ondersteunen.  In deze oefening implementeert u een update voor alle tenantdatabases waarbij twee nieuwe locatietypen worden toegevoegd: *Motorcycle Racing* en *Swimming Club*.

Controleer eerst de locatietypen die zijn opgenomen in elke tenantdatabase. Maak verbinding met een van de tenantdatabases in SQL Server Management Studio (SSMS) en inspecteer de tabel VenueTypes.  U kunt ook een query uitvoeren op deze tabel in de Query-editor in het Azure-portaal, toegankelijk vanaf de databasepagina. 

1. Open SSMS en maak verbinding met de tenantserver: *tenants1-dpt-&lt;user&gt;.database.windows.net*
1. Om te bevestigen dat *Motorcycle Racing* en *Swimming Club* **momenteel niet** zijn opgenomen, gaat u naar de database _contosoconcerthall_ op de server *tenants1-dpt-&lt;user&gt;* en voert u een query uit op de tabel *VenueTypes*.

U gaat nu een taak maken om de tabel *VenueTypes* in alle tenantdatabases bij te werken om de nieuwe locatietypen toe te voegen.

Voor het maken van een nieuwe taak gebruikt u een reeks in het systeem opgeslagen procedures uit de _taakagent_-database die is gemaakt tijdens het maken van het taakagent.

1. Maak in SSMS verbinding met de catalogusserver: server *catalog-dpt-&lt;user&gt;.database.windows.net* 
1. Open in SSMS het bestand …\\Learning Modules\\Schema Management\\DeployReferenceData.sql
1. Wijzig de instructie: STEL @wtpUser = &lt;user&gt; in en vervang de Gebruikerswaarde die u hebt gebruikt bij het implementeren van de Wingtip Tickets-SaaS-database-per-tenant-app
1. Zorg dat u verbinding hebt met de _jobagent_-database en druk op **F5** om het script uit te voeren

Bekijk de volgende elementen in het script *DeployReferenceData.sql*:
* Met **sp\_add\_target\_group** maakt u de doelgroepnaam DemoServerGroup.
* **sp\_add\_target\_group\_member** wordt gebruikt om de set doeldatabases te definiëren.  Eerst wordt de server _tenants1-dpt-&lt;user&gt;_ toegevoegd.  De server toevoegen als een doel zorgt ervoor de databases op die server op de moment van de taakuitvoering worden opgenomen in de taak. Vervolgens worden de database _basetenantdb_ en de database *adhocreporting* (gebruikt in een latere zelfstudie) als doelen toegevoegd.
* Met **sp\_add\_job** maakt u een taak die ook wel _Implementatie van referentiegegevens_ heet.
* Met **sp\_add\_jobstep** maakt u de taakstap met de T-SQL-opdracht om de referentietabel VenueTypes bij te werken.
* De resterende weergaven in het script tonen het bestaan van de objecten en controleren de taakuitvoering. Gebruik deze query's om de statuswaarde in de kolom **levenscyclus** te controleren om te bepalen wanneer de taak is voltooid voor alle doeldatabases.

Zodra het script is voltooid, kunt u controleren of de referentiegegevens zijn bijgewerkt.  Ga in SSMS naar de database *contosoconcerthall* op de server *tenants1-dpt-&lt;user&gt;* en voer een query uit op de tabel *VenueTypes*.  Controleer of *Motorcycle Racing* en *Swimming Club* nu aanwezig **zijn**.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Een taak maken voor het beheren van de referentietabelindex

In deze oefening wordt een taak gebruikt om de index opnieuw samen te stellen op de primaire sleutel van de referentietabel.  Dit is een typische bewerking voor het onderhoud van een database, die kan plaatsvinden nadat grote hoeveelheden gegevens geladen zijn.

Maak een taak met dezelfde in het systeem opgeslagen procedures.

1. Open SSMS en maak verbinding met de server _catalog-dpt-&lt;user&gt;.database.windows.net_
1. Open het bestand _...\\Learning Modules\\Schema Management\\OnlineReindex.sql_
1. Klik met de rechtermuisknop, selecteer Verbinding en maak verbinding met de server _catalog-&lt;user&gt;.database.windows.net_, als deze verbinding nog niet is gemaakt
1. Zorg dat u verbinding hebt met de _jobagent_-database en druk op **F5** om het script uit te voeren

Bekijk de volgende elementen in het script _OnlineReindex.sql_:
* Met **sp\_addjob\_job** maakt u een nieuwe taak met de naam 'Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885'
* Met **sp\_add\_jobstep** maakt u de taakstap met de T-SQL-opdracht om de index bij te werken
* De resterende weergaven van de script controleren taakuitvoering. Gebruik deze query's om de statuswaarde in de kolom **levenscyclus** te controleren om te bepalen wanneer de taak is voltooid voor alle groepsleden.



## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> 
> * Een taakagent maken om uit te voeren op T-SQL-taken op meerdere databases
> * Referentiegegevens in alle tenantdatabases bijwerken
> * Een index in een tabel maken in alle tenantdatabases

Probeer vervolgens de [zelfstudie Ad-hoc](./saas-tenancy-cross-tenant-reporting.md) uit om te ontdekken hoe u gedistribueerde query's kunt uitvoeren op tenantdatabases.


## <a name="additional-resources"></a>Aanvullende bronnen

* [Aanvullende zelfstudies over de Wingtip Tickets SaaS Database Per Tenant-toepassingsimplementatie](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Uitgeschaalde clouddatabases beheren](./elastic-jobs-overview.md)