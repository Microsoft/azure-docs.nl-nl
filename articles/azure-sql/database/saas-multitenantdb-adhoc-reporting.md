---
title: Ad-hocrapportagequery's over meerdere databases
description: Voorbeeld van het uitvoeren van ad-hocrapportagequery's over meerdere Azure SQL-databases in een toepassing met meerdere tenants.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/30/2018
ms.openlocfilehash: 800592b7a8b263fea2883fdd3e030f78f72647dd
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459926"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-databases-azure-sql-database"></a>Ad-hoc analysequery's uitvoeren over meerdere databases (Azure SQL-database)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze zelfstudie voert u gedistribueerde query's over de hele verzameling tenantdatabases uit om interactieve ad hocrapportage mogelijk te maken. Met deze query's kunt u inzichten ophalen die schuilgaan in de dagelijkse operationele gegevens van de Wingtip Tickets SaaS-app. Om deze extracties uit te voeren, implementeert u een aanvullende analysedatabase in de catalogusserver en gebruikt u Elastische query om gedistribueerde query's mogelijk te maken.


In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> 
> * Een ad-hocrapportagedatabase implementeren
> * Gedistribueerde query's uitvoeren over alle tenantdatabases


U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

* De Wingtip Tickets SaaS Multi-tenant Database-app wordt geïmplementeerd. Zie [De Wingtip Tickets SaaS Multi-tenant Database-toepassing implementeren en verkennen](saas-multitenantdb-get-started-deploy.md) om deze binnen vijf minuten te implementeren
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie.
* SQL Server Management Studio (SSMS) is geïnstalleerd. Zie [SQL Server Management Studio (SSMS) downloaden](/sql/ssms/download-sql-server-management-studio-ssms) om SSMS te downloaden en installeren.


## <a name="ad-hoc-reporting-pattern"></a>Ad-hocrapportagepatroon

![ad-hocrapportagepatroon](./media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

SaaS-toepassingen kunnen de grote hoeveelheid tenantgegevens die centraal in de cloud worden opgeslagen, analyseren. De analyses geven inzicht in de werking en het gebruik van uw toepassing. Met deze inzichten kunt u functies verder ontwikkelen, bruikbaarheid verbeteren en andere investeringen doen in uw apps en services.

U kunt gemakkelijk toegang tot deze gegevens krijgen in een database met meerdere tenants, maar dit is niet zo gemakkelijk als de gegevens op schaal zijn verdeeld over misschien wel duizenden databases. Eén aanpak is het gebruik van [Elastische query](elastic-query-overview.md), wat het uitvoeren van query's mogelijk maakt in een gedistribueerde verzameling databases met hetzelfde schema. Deze databases kunnen over verschillende resourcegroepen en abonnementen zijn gedistribueerd. Maar een gemeenschappelijke aanmelding moet toegang hebben om gegevens uit alle databases te extraheren. Elastische query maakt gebruik van één *hoofddatabase* waarin externe tabellen zijn gedefinieerd die tabellen of weergaven in de gedistribueerde (tenant)databases weerspiegelen. Query's die naar deze hoofddatabase worden verzonden, worden gecompileerd tot een gedistribueerd queryplan, waarbij delen van de query, wanneer nodig, naar de onderliggende tenantdatabases worden gepusht. Elastische query maakt gebruik van de shard-kaart in de catalogusdatabase om de locatie van alle tenantdatabases te bepalen. Installatie en query-uitvoering zijn eenvoudig. Er wordt gebruikgemaakt van standaard [Transact-SQL](/sql/t-sql/language-reference) en ondersteuning geboden voor het uitvoeren van ad-hocquery's vanuit hulpprogramma's zoals Power BI en Excel.

Door query's te distribueren over alle tenantdatabases, geeft Elastische query onmiddellijk inzicht in live productiegegevens. Maar aangezien Elastische query gegevens ophaalt uit potentieel veel databases, kan de querylatentie soms hoger zijn dan die van equivalente query's die worden verzonden naar één database met meerdere tenants. Ontwerp query's om zo min mogelijk gegevens te retourneren. Elastische query is vaak het meest geschikt voor het uitvoeren van query's op kleine hoeveelheden realtime gegevens, in tegenstelling tot het ontwikkelen van veelgebruikte of ingewikkelde analysequery's of -rapporten. Als query's niet goed presteren, bekijk dan het [uitvoeringsplan](/sql/relational-databases/performance/display-an-actual-execution-plan) om te zien welk deel van de query naar de externe database omlaag wordt geduwd. En evalueer de hoeveelheid gegevens die wordt geretourneerd. Query's die ingewikkelde analyseverwerking vereisen, kunnen wellicht beter tenantgegevens extraheren naar een database die is geoptimaliseerd voor analysequery's. SQL Database en Azure Synapse Analytics kunnen de analysedatabase hosten.

Dit patroon voor analyse wordt uitgelegd in de [zelfstudie Tenantanalyses](saas-multitenantdb-tenant-analytics.md).

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>De broncode en scripts van de Wingtip Tickets SaaS-databasetoepassing downloaden

De Wingtip Tickets SaaS-multitenantdatabasescripts en broncode van de toepassing zijn beschikbaar in de GitHub-opslagplaats [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB). Bekijk de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen om de Wingtip Tickets SaaS-scripts te downloaden en deblokkeren.

## <a name="create-ticket-sales-data"></a>Kaartverkoopgegevens maken

Als u query's wilt uitvoeren op een interessantere gegevensverzameling, maakt u kaartverkoopgegevens door de ticketgenerator uit te voeren.

1. Open in de *PowerShell ISE* het ...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReporting.ps1*-script en stel de volgende waarden in:
   * **$DemoScenario** = 1, **Tickets kopen voor voorstellingen bij alle theaters**.
2. Druk op **F5** om het script uit te voeren en kaartverkoop te genereren. Terwijl het script wordt uitgevoerd, gaat u verder met de stappen in deze zelfstudie. De query's op de kaartgegevens worden uitgevoerd in de sectie *Ad-hoc gedistribueerde query's uitvoeren*, dus wacht tot de ticketgenerator klaar is.

## <a name="explore-the-tenant-tables"></a>De tenanttabellen verkennen 

In de Wingtip Tickets SaaS multitenant-databasetoepassing worden tenants opgeslagen in een hybride tenantbeheermodel waarbij gegevens worden opgeslagen in een multitenant-database of in een één-tenantdatabase en tussen de twee kunnen worden verplaatst. Wanneer query's op alle tenantdatabases worden uitgevoerd, is het belangrijk dat Elastische query de gegevens kan behandelen alsof ze deel uitmaken van één logische database die door de tenant wordt geshard. 

Om dit patroon te bereiken, bevatten alle tenanttabellen een *VenueId*-kolom die identificeert tot welke tenant de gegevens behoren. De *VenueId* wordt berekend als een hash van de locatienaam, maar elke aanpak kan worden gebruikt om een unieke waarde voor deze kolom te introduceren. Deze aanpak is vergelijkbaar met de manier waarop de tenantsleutel wordt berekend voor gebruik in de catalogus. Tabellen die *VenueId* bevatten worden door Elastische query gebruikt om query's te parallelliseren en naar de toepasselijke externe tenantdatabase omlaag te duwen. Dit reduceert de hoeveelheid gegevens die wordt geretourneerd aanzienlijk, en resulteert in een toename van de prestaties, vooral als er meerdere tenants zijn waarvan de gegevens worden opgeslagen in één-tenantdatabases.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>De database implementeren die voor ad hoc gedistribueerde query's wordt gebruikt

In deze oefening wordt de database *adhocreporting* geïmplementeerd. Dit is de hoofddatabase die het schema bevat dat wordt gebruikt voor het uitvoeren van query's in alle tenantdatabases. De database wordt geïmplementeerd in de bestaande catalogusserver, wat de server is die wordt gebruikt voor alle beheergerelateerde databases in de voorbeeld-app.

1. Open in de *PowerShell ISE* ...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReporting.ps1* en stel de volgende waarden in:
   * **$DemoScenario** = 2, **Ad-hoc Analytics-database implementeren**.

2. Druk op **F5** om het script uit te voeren en de database *adhocreporting* te maken.

In de volgende sectie voegt u een schema aan de database toe, zodat deze kan worden gebruikt om gedistribueerde query's uit te voeren.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>De 'hoofddatabase' configureren voor het uitvoeren van gedistribueerde query's

In deze oefening voegt u een schema (de externe gegevensbron en externe tabeldefinities) aan de adhocreporting-database toe die het mogelijk maakt query's uit te voeren in alle tenantdatabases.

1. Open SQL Server Management Studio en maak verbinding met de ad-hoc rapportagedatabase die u in de vorige stap hebt gemaakt. De naam van de database is *adhocreporting*.
2. Open ...\Learning Modules\Operational Analytics\Adhoc Reporting\ *Initialize-AdhocReportingDB.sql* in SSMS.
3. Controleer het SQL-script en merk het volgende op:

   Elastische query maakt gebruik van referenties in het databasebereik voor toegang tot elk van de tenantdatabases. Deze referenties moeten in alle databases beschikbaar zijn en moeten normaal gesproken de minimumrechten worden verleend die nodig zijn om deze ad-hocquery's mogelijk te maken.

    ![referentie maken](./media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Door de catalogusdatabase als de externe gegevensbron te gebruiken, worden query's gedistribueerd naar alle databases die in de catalogus zijn geregistreerd wanneer de query wordt uitgevoerd. Aangezien servernamen voor elke implementatie anders zijn, vraagt het initialisatiescript de locatie van de catalogusdatabase op door de huidige server (@@servername) op te halen waar het script wordt uitgevoerd.

    ![externe gegevensbron maken](./media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   De externe tabellen die verwijzen naar tenanttabellen worden gedefinieerd met **DISTRIBUTION = SHARD (VenueId)** . Hiermee wordt een query voor een bepaalde *VenueId* naar de juiste database gerouteerd en worden de prestaties voor veel scenario's verbeterd, zoals te zien in de volgende sectie.

    ![externe tabellen maken](./media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   De lokale tabel *VenueTypes* die wordt gemaakt en gevuld. Deze tabel met verwijzingsgegevens is hetzelfde in alle tenantdatabases en kan hier dus als een lokale tabel worden vertegenwoordigd en met de gemeenschappelijke gegevens worden gevuld. Voor sommige query's kan dit de hoeveelheid gegevens die tussen de tenantdatabases en de *adhocreporting*-database wordt verplaatst verminderen.

    ![tabel maken](./media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Als u verwijzingstabellen op deze manier opneemt, zorg er dan voor dat u het tabelschema en de tabelgegevens bijwerkt telkens wanneer u de tenantdatabases bijwerkt.

4. Druk op **F5** om het script uit te voeren en de database *adhocreporting* te initialiseren. 

U kunt nu gedistribueerde query's uitvoeren en inzichten verzamelen in alle tenants!

## <a name="run-ad-hoc-distributed-queries"></a>Gedistribueerde ad-hocquery's uitvoeren

Nu de database *adhocreporting* is ingesteld, kunt u wat gedistribueerde query's gaan uitvoeren. Neem het uitvoeringsplan op om beter te begrijpen waar de queryverwerking plaatsvindt. 

Wanneer u het uitvoeringsplan bekijkt, kunt u uw muisaanwijzer op de planpictogrammen plaatsen voor details. 

1. Open in *SSMS* ...\\Learning Modules\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReportingQueries.sql*.
2. Zorg ervoor dat u bent verbonden met de database **adhocreporting**.
3. Selecteer het menu **Query** en klik op **Werkelijk uitvoeringsplan opnemen**.
4. Markeer de query *Welke venues zijn momenteel geregistreerd?* en druk op **F5**.

   De query retourneert de hele lijst met locaties, wat laat zien hoe snel en gemakkelijk het is om een query uit te voeren op alle tenants en gegevens uit elke tenant te retourneren.

   Inspecteer het plan. U ziet dat de enige kosten worden veroorzaakt door de externe query, omdat we alleen naar elke tenantdatabase gaan een de locatiegegevens retourneren.

   ![SELECTEER * UIT dbo.Venues](./media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Selecteer de volgende query en druk op **F5**.

   Deze query koppelt gegevens uit de tenantdatabases en de lokale tabel *VenueTypes* (lokaal omdat het een tabel in de database *adhocreporting* is).

   Inspecteer het plan. U ziet dat de meeste kosten worden veroorzaakt door de externe query, omdat we de locatiegegevens van elke tenant opvragen (dbo.Venues) en vervolgens een snelle lokale join uitvoeren met de lokale *VenueTypes*-tabel om de beschrijvende naam weer te geven.

   ![Koppeling op externe en lokale gegevens](./media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Selecteer nu de query *Op welke dag werden de meeste kaarten verkocht?* en druk op **F5**.

   Deze query voert een wat ingewikkeldere koppeling en aggregatie uit. Wat belangrijk is om op te merken is dat het grootste deel van de verwerking op afstand gebeurt, en dat we ook hier alleen de rijen terugbrengen die we nodig hebben, zodat er slechts één rij wordt geretourneerd voor het totale aantal verkochte tickets per dag van elke locatie.

   ![query](./media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> 
> * Gedistribueerde query's uitvoeren voor alle databases van de tenant
> * Implementeer een ad hocrapportagedatabase en voeg er een schema aan toe om gedistribueerde query's uit te voeren.

Probeer nu de [zelfstudie Tenantanalyses](saas-multitenantdb-tenant-analytics.md) om het extraheren van gegevens naar een aparte analysedatabase te verkennen voor ingewikkeldere analyseverwerking.

## <a name="additional-resources"></a>Aanvullende bronnen

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Elastische query](elastic-query-overview.md)