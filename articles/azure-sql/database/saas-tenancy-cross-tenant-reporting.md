---
title: Rapportagequery's in meerdere databases
description: Rapportage in meerdere tenants met behulp van gedistribueerde query's.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewers: ''
ms.date: 01/25/2019
ms.openlocfilehash: 18a02b81e459217ccca53d48a08e35a706b071b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793259"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Rapportage in meerdere tenants met behulp van gedistribueerde query's
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze zelfstudie voert u gedistribueerde query's over de hele verzameling tenantdatabases uit voor rapportage. Met deze query's kunt u inzichten ophalen uit dagelijkse operationele gegevens van de Wingtip Tickets SaaS-tenants. Hiertoe implementeert u een aanvullende rapportagedatabase in de catalogusserver en gebruikt u Elastische query om gedistribueerde query's mogelijk te maken.


In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> 
> * Een rapportagedatabase implementeren
> * Gedistribueerde query's uitvoeren over alle tenantdatabases
> * Hoe algemene weergaven in elke database kunnen zorgen voor efficiënte uitvoering van query's in meerdere tenants


U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:


* De Wingtip Tickets SaaS Database Per Tenant-app wordt geïmplementeerd. Zie [De Wingtip Tickets SaaS Database Per Tenant implementeren en verkennen per Tenanttoepassing](./saas-dbpertenant-get-started-deploy.md) om in minder dan vijf minuten te implementeren
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie.
* SQL Server Management Studio (SSMS) is geïnstalleerd. Zie [SQL Server Management Studio (SSMS) downloaden](/sql/ssms/download-sql-server-management-studio-ssms) om SSMS te downloaden en installeren.


## <a name="cross-tenant-reporting-pattern"></a>Patroon van rapportage in meerdere tenants

![patroon van gedistribueerde query's in meerdere tenants](./media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

Eén mogelijkheid met SaaS-toepassingen is de enorme hoeveelheid tenantgegevens te gebruiken die in de cloud zijn opgeslagen, om inzicht te krijgen in de werking en het gebruik van uw toepassing. Met deze inzichten kunt u functies verder ontwikkelen, bruikbaarheid verbeteren en andere investeringen doen in uw apps en services.

U kunt gemakkelijk toegang tot deze gegevens krijgen in een database met meerdere tenants, maar dit is niet zo gemakkelijk als de gegevens op schaal zijn verdeeld over misschien wel duizenden databases. Eén aanpak is het gebruik van [Elastische query](elastic-query-overview.md), wat het uitvoeren van query's mogelijk maakt in een gedistribueerde verzameling databases met hetzelfde schema. Deze databases kunnen over verschillende resourcegroepen en abonnementen zijn gedistribueerd, maar moeten dezelfde aanmeldingsgegevens hebben. Elastische query maakt gebruik van één *hoofddatabase* waarin externe tabellen zijn gedefinieerd die tabellen of weergaven in de gedistribueerde (tenant)databases weerspiegelen. Query's die naar deze hoofddatabase worden verzonden, worden gecompileerd tot een gedistribueerd queryplan, waarbij delen van de query, wanneer nodig, naar de onderliggende tenantdatabases worden gepusht. Elastische query maakt gebruik van de shard-kaart in de catalogusdatabase om de locatie van alle tenantdatabases te bepalen. Installatie en query-uitvoering van de hoofddatabase zijn eenvoudig. Er wordt gebruik gemaakt van standaard [Transact-SQL](/sql/t-sql/language-reference) en ondersteuning geboden voor het uitvoeren van query's vanuit hulpprogramma's zoals Power BI en Excel.

Door query's te distribueren over alle tenantdatabases, geeft Elastische query onmiddellijk inzicht in live productiegegevens. Aangezien Elastische query gegevens ophaalt uit potentieel veel databases, kan de querylatentie hoger zijn dan die van equivalente query's die worden verzonden naar één database met meerdere tenants. Ontwerp query's om zo min mogelijk gegevens naar de hoofddatabase te retourneren. Elastische query is vaak het meest geschikt voor het uitvoeren van query's op kleine hoeveelheden realtime gegevens, in tegenstelling tot het ontwikkelen van veelgebruikte of ingewikkelde analysequery's of -rapporten. Als query's niet goed presteren, bekijk dan het [uitvoeringsplan](/sql/relational-databases/performance/display-an-actual-execution-plan) om te zien welk deel van de query naar de externe database omlaag wordt geduwd en hoeveel gegevens er worden geretourneerd. Query's die ingewikkelde aggregatie of analyseverwerking vereisen, kunnen wellicht beter worden behandeld door tenantgegevens te extraheren in een database of datawarehouse die is geoptimaliseerd voor analysequery's. Dit patroon wordt uitgelegd in de [zelfstudie Tenantanalyses](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>De Wingtip Tickets SaaS Database Per Tenant-toepassingsscripts ophalen

De Wingtip Tickets SaaS-multitenantdatabasescripts en broncode van de toepassing zijn beschikbaar in de [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-opslagplaats. Bekijk de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen om de Wingtip Tickets SaaS-scripts te downloaden en deblokkeren.

## <a name="create-ticket-sales-data"></a>Kaartverkoopgegevens maken

Als u query's wilt uitvoeren op een interessantere gegevensverzameling, maakt u kaartverkoopgegevens door de ticketgenerator uit te voeren.

1. Open in de *PowerShell ISE* het ...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReporting.ps1*-script en stel de volgende waarde in:
   * **$DemoScenario** = 1, **Tickets kopen voor voorstellingen bij alle theaters**.
2. Druk op **F5** om het script uit te voeren en kaartverkoop te genereren. Terwijl het script wordt uitgevoerd, gaat u verder met de stappen in deze zelfstudie. De query's op de kaartgegevens worden uitgevoerd in de sectie *Ad-hoc gedistribueerde query's uitvoeren*, dus wacht tot de ticketgenerator klaar is.

## <a name="explore-the-global-views"></a>De algemene weergaven verkennen

In de Wingtip Tickets SaaS Database Per Tenant-toepassing krijgt elke tenant een database. De gegevens in de databasetabellen worden daarom afgestemd op het perspectief van één tenant. Wanneer query's echter op alle databases worden uitgevoerd, is het belangrijk dat Elastische query de gegevens kan behandelen alsof ze deel uitmaken van één logische database die door de tenant wordt geshard. 

Om dit patroon te simuleren, wordt er een verzameling 'algemene' weergaven aan de tenantdatabase toegevoegd die een tenant-ID projecteren in elk van de tabellen waarop query's algemeen worden uitgevoerd. De weergave *VenueEvents* voegt bijvoorbeeld een berekende *VenueId*-kolom toe die wordt geprojecteerd vanuit de tabel *Events*. Op dezelfde manier voegen de weergaven *VenueTicketPurchases* en *VenueTickets* een berekende *VenueId*-kolom toe die wordt geprojecteerd vanuit hun respectieve tabellen. Deze weergaven worden door Elastische query gebruikt om query's te parallelliseren en naar de toepasselijke externe tenantdatabase omlaag te duwen wanneer er een *VenueId*-kolom aanwezig is. Zo worden er veel minder gegevens geretourneerd en presteren veel query's aanzienlijk beter. Deze algemene weergaven zijn vooraf gemaakt in alle tenantdatabases.

1. Open SSMS en [maak verbinding met de tenants1-&lt;USER&gt;-server](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Vouw **Databases** uit, klik met de rechtermuisknop op _contosoconcerthall_ en selecteer **Nieuwe query**.
1. Voer de volgende query's uit om de verschillen te verkennen tussen de tabellen met één tenant en de algemene weergaven:

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

In deze weergaven wordt de *VenueId* berekend als een hash van de venuenaam, maar elke aanpak kan worden gebruikt om een unieke waarde te introduceren. Deze aanpak is vergelijkbaar met de manier waarop de tenantsleutel wordt berekend voor gebruik in de catalogus.

Ga als volgt te werk om de definitie van de weergave *Venues* te bekijken:

1. In **Objectverkenner** vouwt u **contosoconcerthall** > **Weergaven** uit:

   ![Schermopname met de inhoud van het knooppunt Weergaven, inclusief vier soorten Venue-dbo.](./media/saas-tenancy-cross-tenant-reporting/views.png)

2. Klik met de rechtermuisknop op **dbo.Venues**.
3. Selecteer **Script uitvoeren voor weergave als** > **MAKEN NAAR** > **Venster Nieuwe query-editor**

Voer een script uit voor elk van de andere *Venue*-weergaven om te zien hoe ze de *VenueId* toevoegen.

## <a name="deploy-the-database-used-for-distributed-queries"></a>De database implementeren die voor gedistribueerde query's wordt gebruikt

In deze oefening wordt de database _adhocreporting_ geïmplementeerd. Dit is de hoofddatabase die het schema bevat dat wordt gebruikt voor het uitvoeren van query's in alle tenantdatabases. De database wordt geïmplementeerd in de bestaande catalogusserver, wat de server is die wordt gebruikt voor alle beheergerelateerde databases in de voorbeeld-app.

1. In de *PowerShell ISE* opent u ...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReporting.ps1*. 

1. Stel **$DemoScenario = 2**, _Ad-hoc rapportagedatabase implementeren_ in.

1. Druk op **F5** om het script uit te voeren en de database *adhocreporting* te maken.

In de volgende sectie voegt u een schema aan de database toe, zodat deze kan worden gebruikt om gedistribueerde query's uit te voeren.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>De 'hoofddatabase' configureren voor het uitvoeren van gedistribueerde query's

In deze oefening voegt u een schema (de externe gegevensbron en externe tabeldefinities) aan de database _adhocreporting_ toe om het uitvoeren van query's in alle tenantdatabases mogelijk te maken.

1. Open SQL Server Management Studio en maak verbinding met de ad-hoc rapportagedatabase die u in de vorige stap hebt gemaakt. De naam van de database is *adhocreporting*.
2. Open ...\Learning Modules\Operational Analytics\Adhoc Reporting\ _Initialize-AdhocReportingDB.sql_ in SSMS.
3. Controleer het SQL-script en merk het volgende op:

   Elastische query maakt gebruik van referenties in het databasebereik voor toegang tot elk van de tenantdatabases. Deze referenties moeten in alle databases beschikbaar zijn en moeten normaal gesproken de minimumrechten worden verleend die nodig zijn om deze query's mogelijk te maken.

    ![referentie maken](./media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   Met de catalogusdatabase als de externe gegevensbron worden query's gedistribueerd naar alle databases die in de catalogus zijn geregistreerd op het moment dat de query wordt uitgevoerd. Aangezien servernamen voor elke implementatie anders zijn, verkrijgt het script de locatie van de catalogusdatabase van de huidige server (@@servername) waar het script wordt uitgevoerd.

    ![externe gegevensbron maken](./media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   De externe tabellen die verwijzen naar de algemene weergaven die in de vorige sectie worden beschreven, en die worden gedefinieerd met **DISTRIBUTION = SHARDED(VenueId)** . Aangezien elke *VenueId* aan een afzonderlijke database is toegewezen, verbetert dit de prestaties voor veel scenario's, zoals weergegeven in de volgende sectie.

    ![externe tabellen maken](./media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   De lokale tabel _VenueTypes_ die wordt gemaakt en gevuld. Deze tabel met verwijzingsgegevens is hetzelfde in alle tenantdatabases en kan hier dus als een lokale tabel worden vertegenwoordigd en met de gemeenschappelijke gegevens worden gevuld. Voor sommige query's geldt dat als deze tabel in de hoofddatabase wordt gedefinieerd, de hoeveelheid gegevens kan worden verminderd die naar de hoofddatabase moet worden verplaatst.

    ![tabel maken](./media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Als u verwijzingstabellen op deze manier opneemt, zorg er dan voor dat u het tabelschema en de tabelgegevens bijwerkt telkens wanneer u de tenantdatabases bijwerkt.

4. Druk op **F5** om het script uit te voeren en de database *adhocreporting* te initialiseren. 

U kunt nu gedistribueerde query's uitvoeren en inzichten verzamelen in alle tenants!

## <a name="run-distributed-queries"></a>Gedistribueerde query's uitvoeren

Nu de database *adhocreporting* is ingesteld, kunt u wat gedistribueerde query's gaan uitvoeren. Neem het uitvoeringsplan op om beter te begrijpen waar de queryverwerking plaatsvindt. 

Wanneer u het uitvoeringsplan bekijkt, kunt u uw muisaanwijzer op de planpictogrammen plaatsen voor details. 

Het is belangrijk op te merken dat als u **DISTRIBUTION = SHARDED(VenueId)** instelt wanneer de externe gegevensbron is gedefinieerd, de prestaties worden verbeterd voor veel scenario's. Aangezien elke *VenueId* aan een afzonderlijke database is toegewezen, kan er gemakkelijk extern worden gefilterd en worden alleen de benodigde gegevens geretourneerd.

1. Open ...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReportingQueries.sql* in SSMS.
2. Zorg ervoor dat u bent verbonden met de database **adhocreporting**.
3. Selecteer het menu **Query** en klik op **Werkelijk uitvoeringsplan opnemen**.
4. Markeer de query *Welke venues zijn momenteel geregistreerd?* en druk op **F5**.

   De query retourneert de hele lijst met venues, wat laat zien hoe snel en gemakkelijk het is om een query uit te voeren op alle tenants en gegevens uit elke tenant te retourneren.

   Bekijk het plan en zie dat alle kosten in de externe query staan. Elke tenantdatabase voert de query extern uit en retourneert de venue-informatie naar de hoofddatabase.

   ![SELECTEER * UIT dbo.Venues](./media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Selecteer de volgende query en druk op **F5**.

   Deze query koppelt gegevens uit de tenantdatabases en de lokale tabel *VenueTypes* (lokaal omdat het een tabel in de database *adhocreporting* is).

   Bekijk het plan en zie dat de meeste kosten in de externe query staan. Elke tenantdatabase retourneert de venue-informatie en voert een lokale koppeling met de lokale tabel *VenueTypes* uit om de beschrijvende naam weer te geven.

   ![Koppeling op externe en lokale gegevens](./media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Selecteer nu de query *Op welke dag werden de meeste kaarten verkocht?* en druk op **F5**.

   Deze query voert een wat ingewikkeldere koppeling en aggregatie uit. De meeste verwerking vindt extern plaats.  Alleen enkele rijen, die de dagelijkse kaartverkoop van elke venue bevatten, worden geretourneerd naar de hoofddatabase.

   ![query](./media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> 
> * Gedistribueerde query's uitvoeren voor alle databases van de tenant
> * Implementeer een rapportagedatabase en definieer het schema dat vereist is om gedistribueerde query's te kunnen uitvoeren.


Probeer nu de [zelfstudie Tenantanalyses](saas-tenancy-tenant-analytics.md) om het extraheren van gegevens naar een aparte analysedatabase te verkennen voor ingewikkeldere analyseverwerking.

## <a name="additional-resources"></a>Aanvullende bronnen

* Aanvullende [zelfstudies die voortbouwen op de Wingtip Tickets SaaS Database Per Tenant-toepassing](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastische query](elastic-query-overview.md)