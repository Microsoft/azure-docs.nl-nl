---
title: Schema beheren in een app met meerdere tenants
description: Het schema voor meerdere tenants beheren in een toepassing met meerdere tenants die gebruikmaakt van Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: d222234cd6ff3d910e6dbc51a394695ce467edce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011848"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-databases"></a>Schema beheren in een SaaS-toepassing die gebruikmaakt van Shard-databases met meerdere tenants
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze zelfstudie wordt besproken welke de uitdagingen zijn om een reeks databases te onderhouden in een SaaS-toepassing (Software as a Service). Oplossingen worden gedemonstreerd om schemawijzigingen uit te waaieren voor de reeks databases.

Net als elke toepassing verandert de Wingtip Tickets SaaS-app na verloop van tijd, en moeten er wijzigingen worden aangebracht aan de database. Wijzigingen kunnen van invloed zijn op schema- of referentiegegevens of om onderhoudstaken voor de database toe te passen. Met een SaaS-toepassing die gebruikmaakt van een database-per-tenant-patroon, moeten de wijzigingen mogelijk worden gecoördineerd voor een enorm aantal tenantdatabases. Daarnaast moet u deze wijzigingen opnemen in het inrichtingsproces voor de database om ervoor ze zorgen dat ze worden opgenomen in nieuwe databases wanneer deze worden aangemaakt.

#### <a name="two-scenarios"></a>Twee scenario's

In deze zelfstudie worden de volgende twee scenario's besproken:
- Updates voor referentiegegevens implementeren voor alle tenants.
- Een index opnieuw samenstellen voor de tabel die de referentiegegevens bevat.

De functie [Elastische taken](./elastic-jobs-overview.md) van Azure SQL Database wordt gebruikt om deze bewerkingen uit te voeren in de tenantdatabases. De taken worden ook toegepast op de 'sjabloon'-tenantdatabase. In de Wingtip Tickets-voorbeeldapp wordt deze sjabloondatabase gekopieerd om een nieuwe tenantdatabase in te richten.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een taakagent maken.
> * Een T-SQL-query uitvoeren op meerdere tenantdatabases.
> * Referentiegegevens in alle tenantdatabases bijwerken.
> * Een index in een tabel maken in alle tenantdatabases.

## <a name="prerequisites"></a>Vereisten

- De Wingtip Tickets SaaS-database-app voor meerdere tenants moet al geïmplementeerd zijn:
    - Raadpleeg voor instructies de eerste zelfstudie, waarin de Wingtip Tickets SaaS-database-app wordt geïntroduceerd:<br />[Een shardtoepassing voor meerdere tenants implementeren en verkennen die gebruikmaakt van Azure SQL Database](./saas-multitenantdb-get-started-deploy.md).
        - Het implementatieproces wordt minder dan vijf minuten uitgevoerd.
    - U moet de *shard-versie voor meerdere tenants* van Wingtip geïnstalleerd hebben. De versies voor *Zelfstandig gebruik* en *Database-per-tenant* ondersteunen deze zelfstudie niet.

- De laatste versie van SQL Server Management Studio (SSMS) moet zijn geïnstalleerd. [SSMS downloaden en installeren](/sql/ssms/download-sql-server-management-studio-ssms).

- Azure PowerShell moet geïnstalleerd zijn. Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie.

> [!NOTE]
> In deze zelfstudie wordt gebruikgemaakt van de functies van de Azure SQL Database-service die deel uitmaken van een beperkte preview ([Elastische databasetaken](elastic-database-client-library.md)). Als u deze zelfstudie wilt volgen, stuurt u uw abonnements-id naar *SaaSFeedback\@microsoft.com*, met als onderwerp 'Elastic Jobs Preview'. Wanneer u de bevestiging hebt ontvangen dat uw abonnement is ingeschakeld, [downloadt en installeert u de taak-cmdlets van de voorlopige versie](https://github.com/jaredmoo/azure-powershell/releases). Deze preview is beperkt, neem dus contact op met *SaaSFeedback\@microsoft.com* voor gerelateerde vragen of ondersteuning.

## <a name="introduction-to-saas-schema-management-patterns"></a>Kennismaking met patronen voor SaaS-schemabeheer

Met het het sharddatabasemodel voor meerdere tenants dat in dit voorbeeld wordt gebruikt, kan een tenantsdatabase een of meer tenants bevatten. In dit voorbeeld wordt gebruikgemaakt van een combinatie van databases met veel tenants en databases met één tenant, waardoor een *hybride* model voor tenantbeheer mogelijk is. Wijzigingen beheren in deze databases kan moeilijk zijn. [Elastische taken](./elastic-jobs-overview.md) vereenvoudigt het onderhoud en het beheer van grote aantallen databases. Met Elastische taken kunt u veilig en betrouwbaar Transact-SQL-scripts als taken uitvoeren voor een groep tenantdatabases. De taken staan los van gebruikersinteractie of invoer. Deze methode kan worden gebruikt om wijzigingen in het schema of in de algemene referentiegegevens te implementeren voor alle tenants in een toepassing. Elastische taken kan ook worden gebruikt voor het onderhouden van een gouden sjabloon voor de database. De sjabloon wordt gebruikt om nieuwe tenants te maken, zodat altijd het meest recente schema- en de referentiegegevens worden gebruikt.

![scherm](./media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Beperkte preview voor Elastische taken

Er is een nieuwe versie beschikbaar van Elastische taken; dit is nu een ingebouwde functie in Azure SQL Database. Van deze nieuwe versie van Elastische taken is momenteel een beperkte preview beschikbaar. Deze beperkte preview biedt ondersteuning voor PowerShell voor het maken van een taakagent en voor T-SQL voor het maken en beheren van taken.
> [!NOTE]
> In deze zelfstudie wordt gebruikgemaakt van de functies van de SQL Database-service die deel uitmaken van een beperkte preview (elastische databasetaken). Als u deze zelfstudie wilt volgen, stuurt u uw abonnements-id naar SaaSFeedback@microsoft.com, met als onderwerp 'Elastic Jobs Preview'. Wanneer u de bevestiging hebt ontvangen dat uw abonnement is ingeschakeld, downloadt en installeert u de taak-cmdlets van de voorlopige versie. Deze preview is beperkt, neem dus contact op met SaaSFeedback@microsoft.com voor gerelateerde vragen of ondersteuning.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>De broncode en scripts van de Wingtip Tickets SaaS-databasetoepassing downloaden

De Wingtip Tickets SaaS-multitenantdatabasescripts en broncode van de toepassing zijn beschikbaar in de [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB)-opslagplaats in GitHub. Raadpleeg de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen voor het downloaden en deblokkeren van de scripts voor de SaaS-app Wingtip Tickets.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Een taakagentdatabase en een nieuwe taakagent maken

In deze zelfstudie moet u PowerShell gebruiken om de taakagentdatabase en het taakagent te maken. Net als de MSDB-data base die wordt gebruikt door SQL-Agent, gebruikt een taakagent een database in Azure SQL Database om taakdefinities, taakstatus en geschiedenis op te slaan. Nadat de taakagent is gemaakt, kunt u direct aan de slag met het maken en bewaken van taken.

1. Open in **PowerShell ISE** *Learning Modules\\\\Schema Management\\Demo-SchemaManagement.ps1*.
2. Druk op **F5** om het script uit te voeren.

Met het script *Demo-SchemaManagement.ps1* wordt het script *Deploy-SchemaManagement.ps1* aangeroepen om op de catalogusserver een database te maken met de naam _jobagent_. Vervolgens maakt het script de taakagent met behulp van de _jobagent_-database als een parameter.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Een taak maken om nieuwe referentiegegevens te implementeren voor alle tenants

#### <a name="prepare"></a>Voorbereiden

De database van elke tenant bevat een set locatietypen in de tabel **VenueTypes**. Elk locatietype definieert het soort gebeurtenissen dat kan worden gehouden op een locatie. Deze locatietypen sluiten aan op de achtergrondafbeeldingen die u in de app voor tenantevenementen ziet.  In deze oefening implementeert u een update voor alle databases waarbij twee nieuwe locatietypen worden toegevoegd: *Motorcycle Racing* en *Swimming Club*.

Controleer eerst de locatietypen die zijn opgenomen in elke tenantdatabase. Maak verbinding met een van de tenantdatabases in SQL Server Management Studio (SSMS) en inspecteer de tabel VenueTypes.  U kunt ook een query uitvoeren op deze tabel in de Query-editor in het Azure-portaal, toegankelijk vanaf de databasepagina.

1. Open SSMS en maak verbinding met de tenantserver: *tenants1-dpt-&lt;user&gt;.database.windows.net*
1. Om te bevestigen dat *Motorcycle Racing* en *Swimming Club* **momenteel niet** zijn opgenomen, gaat u naar de database *contosoconcerthall* op de server *tenants1-dpt-&lt;user&gt;* en voert u een query uit op de tabel *VenueTypes*.



#### <a name="steps"></a>Stappen

U maakt nu een taak om de tabel **VenueTypes** in elke tenantdatabase bij te werken door de twee nieuwe locatietypen toe te voegen.

Voor het maken van een nieuwe taak gebruikt u een reeks in het systeem opgeslagen procedures uit de _taakagent_-database die is gemaakt. De opgeslagen procedures zijn aangemaakt toen de taakagent werd gemaakt.

1. Maak in SSMS verbinding met deze tenantserver: tenants1-mt-&lt;user&gt;.database.windows.net

2. Ga naar de database *tenants1*.

3. Voer een query uit op de tabel *VenueTypes* om te bevestigen dat *Motorcycle Racing* en *Swimming Club* nog niet in de lijst met resultaten staan.

4. Maak verbinding met de catalogusserver: *catalog-mt-&lt;user&gt;.database.windows.net*.

5. Maak verbinding met de database _jobagent_ in de catalogusserver.

6. Open in SSMS het bestand *…\\Learning Modules\\Schema Management\\DeployReferenceData.sql*.

7. Wijzig de instructie: stel @User = &lt;user&gt; in en vervang de Gebruikerswaarde die u hebt gebruikt bij het implementeren van de Wingtip Tickets SaaS-databasetoepassing voor meerdere tenants.

8. Druk op **F5** om het script uit te voeren.

#### <a name="observe"></a>Bekijk

Bekijk de volgende items in het script *DeployReferenceData.sql*:

- Met **sp\_add\_target\_group** maakt u de doelgroepnaam *DemoServerGroup* en voegt de doelgroepleden toe aan de groep.

- **sp\_add\_target\_group\_member** voegt de volgende items toe:
    - Een doelgroeplid an het type *server*.
        - Dit is de server *tenants1-mt-&lt;user&gt;* die de tenantdatabases bevat.
        - Door de server op te nemen, neemt u ook de tenantdatabases op die bestaan op het moment dat de taak wordt uitgevoerd.
    - Een doelgroeptype *database* voor de sjabloon database (*basetenantdb*) die zich bevindt op de server *catalog-mt-&lt;user&gt;* ,
    - Een doelgroeptype *database* dat de database *adhocreporting* bevat die in een latere zelfstudie wordt gebruikt.

- Met **sp\_add\_job** maakt u een taak die ook wel *Implementatie van referentiegegevens* heet.

- Met **sp\_add\_jobstep** maakt u de taakstap met de T-SQL-opdracht om de referentietabel VenueTypes bij te werken.

- De resterende weergaven in het script tonen het bestaan van de objecten en controleren de taakuitvoering. Gebruik deze query's om de statuswaarde in de kolom **levenscyclus** te controleren om te bepalen wanneer de taak is voltooid. Met de taak wordt de tenantsdatabase bijgewerkt en worden de twee aanvullende databases die de referentietabel bevatten bijgewerkt.

Ga in SSMS naar de tenantdatabase op de server *tenants1-mt-&lt;user&gt;* . Voer een query uit op de tabel *VenueTypes* om te bevestigen dat *Motorcycle Racing* en *Swimming Club* nu zijn toegevoegd aan de tabel. Het totale aantal locatietypen moet met twee worden verhoogd.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Een taak maken voor het beheren van de referentietabelindex

In deze oefening wordt een taak gemaakt om de index opnieuw samen te stellen op de primaire sleutel van de referentietabel voor alle tenantdatabases. Het opnieuw opbouwen van een index is een typische bewerking voor databasebeheer die een beheerder kan uitvoeren nadat een grote hoeveelheid gegevens zijn geladen om de prestaties te verbeteren.

1. Maak in SSMS verbinding met de database op de server _jobagent_ in *catalog-mt-&lt;User&gt;.database.windows.net*.

2. Open in SSMS *...\\Learning Modules\\Schema Management\\OnlineReindex.sql*.

3. Druk op **F5** om het script uit te voeren.

#### <a name="observe"></a>Bekijk

Bekijk de volgende items in het script *OnlineReindex.sql*:

* Met **sp\_add\_job** maakt u een nieuwe taak met de naam *Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885*.

* Met **sp\_add\_jobstep** maakt u de taakstap met de T-SQL-opdracht om de index bij te werken.

* De resterende weergaven van de script controleren taakuitvoering. Gebruik deze query's om de statuswaarde in de kolom **levenscyclus** te controleren om te bepalen wanneer de taak is voltooid voor alle groepsleden.

## <a name="additional-resources"></a>Aanvullende bronnen

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Uitgeschaalde clouddatabases beheren](./elastic-jobs-overview.md)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een taakagent maken om T-SQL-taken uit te voeren op meerdere databases
> * Referentiegegevens in alle tenantdatabases bijwerken
> * Een index in een tabel maken in alle tenantdatabases

Probeer vervolgens de [zelfstudie Ad-hoc](./saas-multitenantdb-adhoc-reporting.md) uit om te ontdekken hoe u gedistribueerde query's kunt uitvoeren op tenantdatabases.