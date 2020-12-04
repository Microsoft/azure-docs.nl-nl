---
title: Analysequery's uitvoeren
description: Analysequery's voor verschillende tenants waarbij gegevens van meerdere Azure SQL Database-databases worden geëxtraheerd in een app voor meerdere tenants.
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
ms.openlocfilehash: f12c823f609ac309d4b5ddbbaa7d5a076a7bb9ad
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447288"
---
# <a name="cross-tenant-analytics-using-extracted-data---multi-tenant-app"></a>Analysequery's voor verschillende tenant waarbij geëxtraheerde gegevens worden gebruikt - app voor meerdere tenants
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
In deze zelfstudie doorloopt u een volledig analysescenario voor een implementatie voor meerdere tenants. Het scenario laat zien hoe bedrijven dankzij analyse slimme beslissingen kunnen maken. Met behulp van gegevens die uit elke shard-database zijn geëxtraheerd, gebruikt u Analyse om inzicht te krijgen in het gedrag van de tenant, inclusief het gebruik van de Wingtip Tickets SaaS-voorbeeldtoepassing. Dit scenario omvat drie stappen: 

1.  **Gegevens extraheren** vanuit elke tenantdatabase in een analysearchief.
2.  **De geëxtraheerde gegevens optimaliseren** voor analytische verwerking.
3.  Gebruik **Business Intelligence**-hulpprogramma's om nuttige inzichten te verkrijgen, waarmee u beslissingen kunt nemen. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Maak de tenantanalyse-opslag om de gegevens in te extraheren.
> - Elastische taken gebruiken om gegevens te extraheren vanuit elke tenantdatabase in een analysearchief.
> - De geëxtraheerde gegevens (opnieuw in een stervormig schema ondergebracht) optimaliseren.
> - Een query uitvoeren op de analysedatabase.
> - Power BI gebruiken voor gegevensvisualisatie om trends in tenantgegevens aan te geven en aanbevelingen voor verbeteringen te doen.

![Diagram toont een overzicht van de architectuur die wordt gebruikt voor dit artikel.](./media/saas-multitenantdb-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Patroon voor offline tenantanalyse

SaaS-toepassingen die u ontwikkelt, hebben toegang tot een grote hoeveelheid tenantgegevens die zijn opgeslagen in de cloud. Deze gegevens bieden een uitgebreide bron van inzichten over de werking en het gebruik van uw toepassing en over het gedrag van uw tenants. Met deze inzichten kunt u functies verder ontwikkelen, bruikbaarheid verbeteren en andere investeringen doen in de app en het platform.

Het is gemakkelijk om toegang te krijgen tot de gegevens voor alle tenants wanneer alle gegevens zich in slechts één database met meerdere tenants bevinden. De toegang is echter ingewikkelder wanneer ze op schaal zijn gedistribueerd in duizenden databases. Eén manier om de complexiteit te beheersen en is door de gegevens te extraheren naar een analysedatabase of een datawarehouse. Vervolgens voert u een query uit op het datawarehouse om inzichten te verkrijgen uit de ticketgegevens van alle tenants.

Deze zelfstudie presenteert een volledig analysescenario voor dit voorbeeld van een SaaS-toepassing. Eerst worden elastische taken gebruikt voor het plannen van het ophalen van gegevens uit elke tenantdatabase. De gegevens worden verzonden naar een analyse-archief. De analyseopslag kan een SQL Database of een Azure Synapse Analytics zijn. Voor grootschalige gegevensextractie raden we [Azure Data Factory](../../data-factory/introduction.md) aan.

Vervolgens worden de geaggregeerde gegevens verdeeld in een set van tabellen van een [stervormig schema](https://www.wikipedia.org/wiki/Star_schema). De tabellen bestaan uit een centrale feitentabel plus gerelateerde dimensietabellen:

- De centrale feitentabel in het stervormige schema bevat gegevens over tickets.
- De dimensietabellen bevatten gegevens over locaties, evenementen, klanten en aankoopdatums.

De centrale en dimensietabellen maken gezamenlijk een efficiënte analyseverwerking mogelijk. Het stervormige schema dat in deze zelfstudie wordt gebruikt, wordt weergegeven in de volgende afbeelding:
 
![Databasediagram toont vier database-objecten die verbonden zijn met een centraal databaseobject.](./media/saas-multitenantdb-tenant-analytics/StarSchema.png)

Ten slotte wordt er een query uitgevoerd op de tabellen in het stervormige schema. De queryresultaten worden visueel weergegeven om inzichten in het tenantgedrag en het gebruik van de toepassing te verduidelijken. Met dit stervormig schema kunt u query's uitvoeren om items te detecteren, zoals de volgende:

- Wie kopen er tickets en op welke locaties.
- Verborgen patronen en trends op de volgende gebieden:
    - De verkoop van tickets.
    - De relatieve populariteit van elke locatie.

Begrijpen hoe consistent elke tenant de service gebruikt, biedt de gelegenheid om serviceplannen te maken om in te spelen op hun behoeften. In deze zelfstudie vindt u eenvoudige voorbeelden van inzichten die kunnen worden afgelezen uit de tenantgegevens.

## <a name="setup"></a>Instellen

### <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet u ervoor zorgen dat aan de volgende vereisten is voldaan:

- De Wingtip Tickets SaaS-database voor meerdere tenants wordt geïmplementeerd. Zie [De Wingtip Tickets SaaS Multi-tenant Database-toepassing implementeren en verkennen](./saas-multitenantdb-get-started-deploy.md) om deze binnen vijf minuten te implementeren
- De [broncode](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) en de scripts van Wingtip SaaS worden gedownload van GitHub. Vergeet niet *de blokkering van het zip-bestand ongedaan te maken* voordat u de inhoud ervan uitpakt. Bekijk de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen voor het downloaden en het deblokkeren van de Wingtip Tickets-SaaS-scripts.
- Power BI Desktop is geïnstalleerd. [Power BI Desktop downloaden](https://powerbi.microsoft.com/downloads/)
- De batch met aanvullende tenants is ingericht; zie de [**Zelfstudie voor het inrichten van tenants**](./saas-multitenantdb-provision-and-catalog.md).
- Er zijn een taakagent en taakagentdatabase aangemaakt. Bekijk de juiste stappen in de zelfstudie [**Schemabeheer**](./saas-multitenantdb-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Gegevens maken voor de demo

In deze zelfstudie wordt analyse uitgevoerd op gegevens over de ticketverkoop. In deze stap genereert u ticketgegevens voor alle tenants.  Later worden deze gegevens geëxtraheerd voor analyse. *Zorg ervoor dat u de batch met tenants hebt ingericht zoals eerder beschreven, zodat u voldoende gegevens hebt*. Uit een voldoende grote hoeveelheid gegevens kunt u verschillende aankooppatronen voor tickets afleiden.

1. In **PowerShell ISE** opent u *…\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* en stelt u de volgende waarde in:
    - **$DemoScenario** = **1** Tickets kopen voor evenementen op alle locaties
2. Druk op **F5** om het script uit te voeren en een geschiedenis van kaartverkoop voor elke gebeurtenis op elke locaties te maken.  Het script wordt gedurende enkele minuten uitgevoerd om tienduizenden tickets te genereren.

### <a name="deploy-the-analytics-store"></a>De analyse-opslag implementeren
Vaak zijn er verschillende transactionele shard-databases die alle tenantgegevens samenhouden. U moet de tenantgegevens van de shard-database samenbrengen in één analyseopslag. Door ze samen te brengen kunt u efficiënt een query uitvoeren op de gegevens. In deze zelfstudie wordt een Azure SQL Database-database gebruikt om de geaggregeerde gegevens op te slaan.

In de volgende stappen implementeert u de analyseopslag, die **tenantanalytics** heet. U implementeert ook voorgedefinieerde tabellen die verderop in de zelfstudie worden ingevuld:
1. In PowerShell ISE opent u *…\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Stel de variabele $DemoScenario in het script in zodat deze overeenkomt met uw gekozen analyse-opslag. We raden u aan om de database zonder columnstore te gebruiken voor leerdoeleinden.
    - Als u SQL Database wilt gebruiken zonder columnstore, stelt u **$DemoScenario** = **2**
    - Als u SQL Database wilt gebruiken met columnstore, stelt u **$DemoScenario** = **3**  
3. Druk op **F5** om het demoscript uit te voeren (dat het script *Deploy-TenantAnalyticsDB\<XX>.ps1* aanroept) dat de tenant analytics-gegevensopslag aanmaakt. 

Nu dat u de toepassing heeft geïmplementeerd en deze hebt gevuld met interessante tenantgegevens, gebruikt u [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) om de servers **tenants1-mt-\<User\>** en **catalog-mt-\<User\>** te verbinden met Login = *developer*, Password = *P\@ssword1*.

![architectureOverView](./media/saas-multitenantdb-tenant-analytics/ssmsSignIn.png)

Voer de volgende stappen uit in de Objectverkenner:

1. Vouw de server *tenants1-mt-\<User\>* uit.
2. Vouw het Databaseknooppunt uit en bekijk de database *tenants1* die meerdere tenants bevat.
3. Vouw de server *catalog-dpt-\<User\>* uit.
4. Controleer of u de analyseopslag en de jobaccount-database ziet.

Bekijk de volgende database-items in de Objectverkenner van SSMS door het knooppunt van de analyseopslag uit te vouwen:

- De tabellen **TicketsRawData** en **EventsRawData** bevatten onbewerkte gegevens uit de tenantdatabases.
- De tabellen in het stervormige schema zijn **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** en **dim_Dates**.
- De opgeslagen procedure **sp_ShredRawExtractedData** wordt gebruikt om stervormige tabellen te vullen vanuit de onbewerkte gegevenstabellen.

![Schermafbeelding met S S M S Objectverkenner voor het knooppunt van de analyseopslag, inclusief Tabellen, Weergaven en knooppunten.](./media/saas-multitenantdb-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Gegevensextractie 

### <a name="create-target-groups"></a>Doelgroepen maken 

Voordat u doorgaat, moet u ervoor zorgen dat u het taakaccount en de jobaccount-database. In de volgende reeks stappen wordt Elastische taken gebruikt om gegevens te extraheren uit de shard-tenantsdatabase en om de gegevens op te slagen in de analyseopslag. De tweede taak verdeelt de gegevens en slaat die op in tabellen in het stervormige schema. Deze twee taken worden uitgevoerd voor twee verschillende doelgroepen, met name **TenantGroup** en **AnalyticsGroup**. De extractietaak wordt uitgevoerd op de TenantGroup, die alle tenantdatabases bevat. De verdeeltaak wordt uitgevoerd op de AnalyticsGroup, die enkel de analyseopslag bevat. Maak de doelgroepen met behulp van de volgende stappen:

1. In SSMS maakt u verbinding met de database **jobaccount** in catalog-mt-\<User\>.
2. Open in SSMS *…\Learning Modules\Operational Analytics\Tenant Analytics\TargetGroups.sql* 
3. Wijzig de @User-variabele bovenaan het script en vervang `<User>` door de gebruikerswaarde die u hebt gebruikt bij het implementeren van de Wingtip Tickets SaaS-databasetoepassing voor meerdere tenants.
4. Druk op **F5** om het script dat de twee doelgroepen maakt uit te voeren.

### <a name="extract-raw-data-from-all-tenants"></a>Onbewerkte gegevens uit alle tenants ophalen

Transacties kunnen vaker plaatsvinden voor *ticket- en klant* gegevens dan voor *gebeurtenis- en locatie* gegevens. Overweeg daarom om ticket- en klantgegevens apart te extraheren en dat vaker te doen dan dat u gebeurtenis- en locatiegegevens extraheert. In deze sectie definieert en plant u twee afzonderlijke taken:

- Ticket- en klantgegevens extraheren.
- Gebeurtenis- en locatiegegevens extraheren.

Elke taak extraheert de gegevens en plaatst deze in de analyseopslag. Er is een afzonderlijke taak die de geëxtraheerde gegevens verdeelt in het stervormige analyseschema.

1. In SSMS maakt u verbinding met de database **jobaccount** in de server catalog-mt-\<User\>.
2. Open in SSMS *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*.
3. Wijzig de @User bovenaan het script en vervang `<User>` door de gebruikersnaam die u hebt gebruikt bij het implementeren van de Wingtip Tickets SaaS-databasetoepassing voor meerdere tenants. 
4. Druk op **F5** om het script uit te voeren waarmee ticket- en klantgegevens worden geëxtraheerd uit elke tenantdatabase. De taak slaat de gegevens op in de analyseopslag.
5. Voeg een query uit op de TicketsRawData-tabel in de tenantanalytics-database om ervoor te zorgen dat de tabel gevuld wordt met ticketgegevens van alle tenants.

![Schermafbeelding met de ExtractTickets-database waarbij de TicketsRawData d b o geselecteerd is in Objectverkenner.](./media/saas-multitenantdb-tenant-analytics/ticketExtracts.png)

Herhaal de voorgaande stappen, maar vervang ditmaal **\ExtractTickets.sql** door **\ExtractVenuesEvents.sql** in stap 2.

Als de taak wordt uitgevoerd, wordt de tabel EventsRawData in de analyseopslag ingevuld met nieuwe gebeurtenis- en locatiegegevens van alle tenants. 

## <a name="data-reorganization"></a>Gegevensreorganisatie

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Verdeel geëxtraheerde gegevens onder stervormige schematabellen

De volgende stap is om de geëxtraheerde gegevens te verdelen in een reeks tabellen die geoptimaliseerd zijn voor analysequery's. Er wordt een stervormig schema gebruikt. Een centrale feitentabel bevat informatie over de verkoop van afzonderlijke tickets. Dimensietabellen bevatten gegevens over locaties, gebeurtenissen, klanten en aankoopdatums. 

In dit deel van de zelfstudie definieert en voert u een taak uit die de geëxtraheerde onbewerkte gegevens samenvoegt met de gegevens in stervormige tabellen. Wanneer u klaar bent met samenvoegen, worden de onbewerkte gegevens verwijderd zodat de tabellen ingevuld kunnen worden door de volgende extractietaak voor tenantgegevens.

1. In SSMS maakt u verbinding met de database **jobaccount** in catalog-mt-\<User\>.
2. Open in SSMS *…\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*.
3. Druk op **F5** om het script uit te voeren om een taak te definiëren die de opgeslagen sp_ShredRawExtractedData-procedure aanroept in de analyseopslag.
4. Geef de taak voldoende tijd om uitgevoerd te worden.
    - Controleer de kolom **Levenscyclus** van de tabel jobs.jobs_execution voor de status van de taak. Zorg ervoor dat de taak **Geslaagd** is voordat u doorgaat. Bij een geslaagde uitvoering worden gegevens zoals in de volgende grafiek:

![Schermafbeelding met het geslaagde resultaat van de uitvoering van de procedure sp_ShredRawExtractedData.](./media/saas-multitenantdb-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Gegevens verkennen

### <a name="visualize-tenant-data"></a>Tenantgegevens visualiseren

De gegevens in de stervormige schematabel bevatten alle gegevens over de ticketverkoop die voor uw analyse nodig zijn. Om sneller trends te herkennen in grote gegevenssets, moet u deze grafisch visualiseren.  In dit onderdeel leert u **Power BI** gebruiken om de tenantgegevens die u hebt geëxtraheerd en georganiseerd te manipuleren en te visualiseren.

Gebruik de volgende stappen om verbinding te maken met Power BI en om de weergaven te importeren die u eerder hebt gemaakt:

1. Start Power BI Desktop.
2. Selecteer op het Start-lint de optie **Gegevens ophalen** en selecteer **Meer…** in het menu.
3. In het venster **Gegevens ophalen** selecteert u Azure SQL Database.
4. In het aanmeldvenster van de database voert u de naam in van uw server (catalog-mt-\<User\>.database.windows.net). Selecteer **Importeren** voor **Gegevensverbindingsmodus** en klik op OK. 

    ![Schermafbeelding met het dialoogvenster SQL Server-database waar u de Server en de Database kunt invoeren.](./media/saas-multitenantdb-tenant-analytics/powerBISignIn.PNG)

5. Selecteer **Database** in het linkerdeelvenster en voer vervolgens voor gebruikersnaam *developer* in en voor wachtwoord *P\@ssword1*. Klik op **Verbinding maken**.  

    ![Schermafbeelding met het dialoogvenster SQL Server-database waar u een Gebruikersnaam en een Wachtwoord kunt invoeren.](./media/saas-multitenantdb-tenant-analytics/databaseSignIn.PNG)

6. In het deelvenster **Navigator**, onder de analysedatabase, selecteert u de tabellen in het stervormige schema: fact_Tickets, dim_Events, dim_Venues, dim_Customers en dim_Dates. Selecteer vervolgens **Laden**. 

Gefeliciteerd! U hebt de gegevens geladen in Power BI. Nu kunt u interessante visualisaties verkennen om inzicht te krijgen in uw tenants. U gaat kijken hoe u met analyses gegevensgestuurde aanbevelingen kunt krijgen voor het zakelijke team van Wingtip Tickets. Aan de hand van de aanbevelingen kunt u het bedrijfsmodel en de klantervaring optimaliseren.

U begint met het analyseren van de gegevens van de ticketverkoop om de verkoopcijfers per locatie te bekijken. Selecteer de volgende opties in Power BI om een staafdiagram uit te zetten van het totale aantal verkochte tickets per locatie. Vanwege willekeurige variatie in de ticketgenerator, kunnen de resultaten afwijken.
 
![Schermafbeelding met een Power BI-visualisatie en en besturingselementen voor de gegevensvisualisatie aan de rechterkant.](./media/saas-multitenantdb-tenant-analytics/TotalTicketsByVenues.PNG)

In de bovenstaande grafiek kunt u zien dat het aantal verkochte tickets per locatie inderdaad varieert. Locaties waar meer tickets worden verkocht, maken meer gebruik van uw service dan locaties die minder tickets verkopen. Er is een kans om de toewijzing van resources aan te passen op basis van de verschillende behoeften van de tenant.

U kunt de gegevens verder analyseren om te zien hoe de ticketverkoop gedurende een periode kan variëren. Selecteer de volgende opties in Power BI om het totale aantal verkochte tickets dat dagelijks gedurende een periode van zestig dagen grafisch uit te zetten.
 
![Schermafbeelding met Power BI-visualisatie getiteld Distributie ticketverkoop vs. Verkoopsdag.](./media/saas-multitenantdb-tenant-analytics/SaleVersusDate.PNG)

In de bovenstaande grafiek ziet u dat er voor sommige locaties een piek in de ticketverkoop zit. Deze pieken versterken het idee dat in sommige locaties mogelijk onevenredig gebruikgemaakt wordt van systeemresources. Tot nu toe zit er geen duidelijk patroon in het optreden van de pieken.

U gaat de betekenis van deze piekdagen in de verkoop derder onderzoeken. Wanneer treden deze pieken op nadat de tickets in de verkoop gaan? Als u het aantal tickets dat per dag verkocht wordt grafisch wilt weergeven, selecteert u de volgende opties in Power BI.

![SaleDayDistribution](./media/saas-multitenantdb-tenant-analytics/SaleDistributionPerDay.PNG)

De voorgaande grafiek laat zien dat op sommige locaties op de eerste dag van verkoop een veel tickets wordt verkocht. Zodra de tickets op deze locaties in de verkoop gaan, lijkt het alsof er een koopkoorts is uitgebroken. Deze verhoogde activiteit bij enkele locaties kan van invloed zijn op de service voor andere tenants.

U kunt opnieuw inzoomen op de gegevens om te zien of deze koopkoorts ook geldt voor alle evenementen die op deze locaties plaatsvinden. In voorgaande grafieken hebt u gezien dat Contoso Concert Hall heel wat tickets verkoopt en dat Contoso op bepaalde dagen ook een piek in de ticketverkoop laat zien. Speel met de opties in Power BI om de cumulatieve cijfers voor de ticketverkoop voor Contoso Concert Hall uit te zetten, zodat u zich kunt richten op verkooptrends voor alle evenementen die hier plaatsvinden. Volgen alle evenementen hetzelfde verkooppatroon?

![ContosoSales](./media/saas-multitenantdb-tenant-analytics/EventSaleTrends.PNG)

In de voorgaande grafiek voor Contoso Concert Hall ziet u dat deze stormloop niet voor alle evenementen gebeurt. Speel met de filteropties om de verkooptrends voor andere evenementen te zien.

De inzichten in de patronen van de ticketverkoop kunnen ertoe leiden dat Wingtip Tickets zijn bedrijfsmodel moet optimaliseren. In plaats van alle tenants hetzelfde te laten betalen, kan Wingtip wellicht servicelagen introduceren van verschillende rekenkracht. Grotere locaties die meer tickets per dag moeten verkopen, kan een laag van een hoger niveau met een hoger service level agreement (SLA) worden aangeboden. Voor deze locaties kunnen de databases in een pool worden geplaatst met hogere resourcelimieten per database. Aan elke servicelaag kan een verkoopcijfer per uur worden toegewezen, waarbij extra kosten in rekening worden gebracht als de toewijzing wordt overschreden. Grotere locaties met periodieke piekverkopen zouden profiteren van de hogere lagen en Wingtip Tickets kan zijn service efficiënter benutten.

De klanten van Wingtip Tickets kunnen daarentegen een klacht indienen omdat ze moeite hebben genoeg tickets te verkopen die de servicekosten rechtvaardigen. Wellicht bieden deze inzichten een kans de ticketverkoop voor slecht presterende locaties te stimuleren. Een hogere verkoop kan de vermeende waarde van de service verbeteren. Klik met de rechtermuisknop op fact_Tickets en selecteer **Nieuwe meting**. Voer de volgende expressie in voor de nieuwe meting met de naam **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Selecteer de volgende visualisatieopties om het percentage verkochte tickets per locatie af te zetten en het relatieve succes vast te stellen.

![analyticsViews](./media/saas-multitenantdb-tenant-analytics/AvgTicketsByVenues.PNG)

Hoewel de meeste locaties meer dan 80% van hun tickets verkopen, ziet u in de voorgaande grafiek dat sommige moeite moeten doen om meer dan de helft van hun plaatsen gevuld te krijgen. Wijzig de selecties in het waardengebied om het maximum- of minimumpercentage tickets te selecteren dat bij elke locatie wordt verkocht.

Eerder hebt u uw analyse uitgewerkt om te ontdekken dat de ticketverkoop voorspelbare patronen volgt. Met deze ontdekking kan Wingtip Tickets u helpen om de ticketverkoop voor slecht presterende locaties te stimuleren door dynamische prijzen aan te bevelen. Deze ontdekking kan erop wijzen dat het mogelijk opportuun is om machine learning-technieken te gebruiken om de verkoop van tickets elk evenement te voorspellen. U kunt ook voorspellingen maken voor de impact die kortingen bij de ticketverkoop hebben op de omzet. Power BI Embedded kan worden geïntegreerd in een toepassing voor evenementbeheer. De integratie kan helpen bij het visualiseren van voorspelde verkoop en het effect van verschillende kortingen. De toepassing kan u helpen een optimale korting te berekenen en die rechtstreeks vanuit de analyseweergave toe te passen.

U hebt trends bekeken in tenantgegevens van de Wingtip Tickets SaaS-databasetoepassing voor meerdere tenants. U kunt andere manieren bedenken waarop de app informatie kan verschaffen om SaaS-toepassingsverkopers te helpen zakelijke beslissingen te nemen. Verkopers kunnen beter voldoen aan de behoeften van hun tenants. Hopelijk heeft deze zelfstudie u de nodige tools gegeven om analyses uit te voeren op tenantgegevens zodat uw bedrijven gegevensgestuurde beslissingen kunnen nemen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> - Een database voor tenantanalyse implementeren met voorgedefinieerde stervormige schematabellen
> - Elastische taken gebruiken om gegevens te extraheren uit alle tenantdatabases
> - De geëxtraheerde gegevens samenvoegen in tabellen in een stervormig schema ontworpen voor analyse
> - Een query uitvoeren op een analysedatabase 
> - Power BI gebruiken voor gegevensvisualisatie om trends in tenantgegevens te observeren 

Gefeliciteerd!

## <a name="additional-resources"></a>Aanvullende bronnen

Aanvullende [zelfstudies waarbij wordt voortgebouwd op de Wingtip-SaaS-toepassing](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials). 
- [Elastische taken](./elastic-jobs-overview.md).
- [Analyses voor verschillende tenants waarbij geëxtraheerde gegevens worden gebruikt - app voor één tenant](saas-tenancy-tenant-analytics.md)