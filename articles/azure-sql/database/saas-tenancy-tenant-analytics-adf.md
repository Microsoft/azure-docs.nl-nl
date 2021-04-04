---
title: Analytische query's uitvoeren op tenantdatabases
description: Analytische query's tussen tenants die gebruikmaken van gegevens die zijn geëxtraheerd uit Azure SQL Database, Azure Synapse Analytics, Azure Data Factory of Power BI.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: dc47c996748b126841cbeff1ea3f6f18f423951f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96457641"
---
# <a name="explore-saas-analytics-with-azure-sql-database-azure-synapse-analytics-data-factory-and-power-bi"></a>SaaS Analytics verkennen met Azure SQL Database, Azure Synapse Analytics, Data Factory en Power BI
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze zelfstudie doorloopt u een end-to-end-analysescenario. In dit scenario ziet u hoe analyse van tenantgegevens softwareleveranciers kan stimuleren om slimme beslissingen te nemen. Met behulp van gegevens die uit elke tenantdatabase zijn geëxtraheerd, gebruikt u Analyse om inzicht te krijgen in het gedrag van de tenant, inclusief het gebruik van de Wingtip Tickets SaaS-voorbeeldtoepassing. Dit scenario omvat drie stappen:

1. **Gegevens extraheren** vanuit elke tenantdatabase in een analysearchief, in dit geval een toegewezen SQL-pool.
2. **De geëxtraheerde gegevens optimaliseren** voor analytische verwerking.
3. Gebruik **Business Intelligence**-hulpprogramma's om nuttige inzichten te verkrijgen, waarmee u beslissingen kunt nemen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Het analysearchief van de tenant maken voor het laden.
> - Azure Data Factory (ADF) gebruiken voor het extraheren van gegevens uit elke tenantdatabase in het datawarehouse voor analyse.
> - De geëxtraheerde gegevens (opnieuw in een stervormig schema ondergebracht) optimaliseren.
> - Query's uitvoeren op het datawarehouse voor analyse.
> - Power BI gebruiken voor gegevensvisualisatie om trends in tenantgegevens aan te geven en aanbevelingen voor verbeteringen te doen.

![architectureOverView](./media/saas-tenancy-tenant-analytics-adf/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Analyse over geëxtraheerde tenantgegevens

SaaS-toepassingen bevatten een potentieel grote hoeveelheid tenantgegevens in de cloud. Deze gegevens kunnen een uitgebreide bron van inzichten bieden over de werking en het gebruik van uw toepassing en het gedrag van uw tenants. Met deze inzichten kunt u functies verder ontwikkelen, bruikbaarheid verbeteren en andere investeringen doen in de apps en het platform.

Het is gemakkelijk om toegang te krijgen tot de gegevens voor alle tenants wanneer alle gegevens zich in slechts één database met meerdere tenants bevinden. De toegang is echter ingewikkelder wanneer ze op schaal zijn gedistribueerd in duizenden databases. Eén manier om de complexiteit te beheersen en is door de gegevens te extraheren naar een analysedatabase of een datawarehouse om er query's op uit te voeren.

Deze zelfstudie bevat een end-to-end-analysescenario voor de Wingtip Tickets-toepassing. Eerst wordt [Azure Data Factory (ADF)](../../data-factory/introduction.md) als het indelingshulpprogramma gebruikt om kaartverkoop en gerelateerde gegevens uit elke tenantdatabase te extraheren. Deze gegevens worden in een analysearchief in faseringstabellen geladen. Het analysearchief kan een SQL-database of een toegewezen SQL-pool zijn. In deze zelfstudie wordt [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) gebruikt als het analysearchief.

Vervolgens worden de geëxtraheerde gegevens getransformeerd en in een verzameling tabellen in het [stervormige schema](https://www.wikipedia.org/wiki/Star_schema) geladen. De tabellen bestaan uit een centrale feitentabel plus gerelateerde dimensietabellen:

- De centrale feitentabel in het stervormige schema bevat gegevens over tickets.
- De dimensietabellen bevatten gegevens over locaties, evenementen, klanten en aankoopdatums.

De centrale en dimensietabellen maken gezamenlijk een efficiënte analyseverwerking mogelijk. Het stervormige schema dat in deze zelfstudie wordt gebruikt, wordt weergegeven in de volgende afbeelding:

![Diagram met het stervormige schema dat in deze zelfstudie wordt gebruikt.](./media/saas-tenancy-tenant-analytics-adf/starschematables.JPG)

Ten slotte wordt er een query uitgevoerd op de tabellen in het stervormige schema. De queryresultaten worden visueel weergegeven met behulp van Power BI om inzichten in het tenantgedrag en het gebruik van de toepassing te verduidelijken. Met dit stervormige schema voert u query's uit die het volgende laten zien:

- Wie kopen er tickets en op welke locaties.
- Patronen en trends in de verkoop van tickets.
- De relatieve populariteit van elke locatie.

In deze zelfstudie vindt u eenvoudige voorbeelden van inzichten die kunnen worden afgelezen uit de Wingtip Tickets-gegevens. Inzicht in de manier waarop elke locatie van de service gebruikmaakt, biedt de Wingtip Tickets-leverancier de mogelijkheid na te denken over verschillende serviceplannen die bedoeld zijn voor bijvoorbeeld meer of minder actieve locaties.

## <a name="setup"></a>Instellen

### <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet u ervoor zorgen dat aan de volgende vereisten is voldaan:

- De toepassing Wingtip Tickets SaaS Database Per Tenant is geïmplementeerd. Zie [De Wingtip SaaS-app implementeren en verkennen](./saas-dbpertenant-get-started-deploy.md) als u de app in minder dan vijf minuten wilt implementeren.
- De [broncode](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) en de scripts van Wingtip Tickets SaaS Database Per Tenant worden gedownload van GitHub. Zie de downloadinstructies. Vergeet niet *de blokkering van het zip-bestand ongedaan te maken* voordat u de inhoud ervan uitpakt.
- Power BI Desktop is geïnstalleerd. [Download Power BI Desktop](https://powerbi.microsoft.com/downloads/).
- De batch met aanvullende tenants is ingericht; zie de [**Zelfstudie voor het inrichten van tenants**](./saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>Gegevens maken voor de demo

In deze zelfstudie worden de analyses van de gegevens over de ticketverkoop verkend. In deze stap genereert u ticketgegevens voor alle tenants. In een latere stap worden deze gegevens geëxtraheerd voor analyse. _Zorg ervoor dat u de batch met tenants hebt ingericht_ (zoals eerder beschreven), zodat u voldoende gegevens hebt om een aantal verschillende inkooppatronen voor tickets beschikbaar te hebben.

1. In PowerShell ISE opent u *…\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* en stelt u de volgende waarde in:
    - **$DemoScenario** = **1** Tickets kopen voor evenementen op alle locaties
2. Druk op **F5** om het script uit te voeren en een geschiedenis van kaartverkoop voor alle locaties te maken. Bij twintig tenants genereert het script tienduizenden tickets. Dit kan tien minuten of langer duren.

### <a name="deploy-azure-synapse-analytics-data-factory-and-blob-storage"></a>Azure Synapse Analytics, Data Factory en Blob Storage implementeren

In de Wingtip Tickets-app worden de transactiegegevens van de tenants over talloze databases gedistribueerd. Azure Data Factory (ADF) wordt gebruikt om het extraheren, laden en transformeren (ELT) van deze gegevens in het datawarehouse in te delen. Voor het zo efficiënt mogelijk laden van gegevens in Azure Synapse Analytics, kunt u gegevens ophalen in tussenliggende blob-bestanden en vervolgens [PolyBase](../../synapse-analytics/sql-data-warehouse/design-elt-data-loading.md) gebruiken om de gegevens in het datawarehouse te laden.

In deze stap implementeert u de extra resources die in de zelfstudie worden gebruikt: een toegewezen SQL-pool met de naam _tenantanalytics_, een Azure Data Factory met de naam _dbtodwload-\<user\>_ en een Azure-opslagaccount met de naam _wingtipstaging\<user\>_ . Het opslagaccount wordt gebruikt voor het tijdelijk bewaren van geëxtraheerde gegevensbestanden in de vorm van blobs voordat ze in het datawarehouse worden geladen. Met deze stap wordt ook het datawarehouse-schema geïmplementeerd en worden de ADF-pijplijnen gedefinieerd waarmee het ELT-proces wordt ingedeeld.

1. In PowerShell ISE opent u *…\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* en stelt u de volgende waarde in:
    - **$DemoScenario** = **2** Datawarehouse voor tenantanalyse, Blob Storage en Data Factory implementeren
1. Druk op **F5** om het demoscript uit te voeren en de Azure-resources te implementeren.

Beoordeel nu de Azure-resources die u hebt geïmplementeerd:

#### <a name="tenant-databases-and-analytics-store"></a>Tenantdatabases en analysearchief

Gebruik [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) om verbinding te maken met **tenants1-dpt-&lt;user&gt;** - en **catalog-dpt-&lt;user&gt;** -servers. Vervang &lt;user&gt; door de waarde die is gebruikt bij het implementeren van de app. Gebruik als gebruikersnaam *developer* en als wachtwoord *P\@ssword1*. Raadpleeg de [inleidende zelfstudie](./saas-dbpertenant-wingtip-app-overview.md) voor meer begeleiding.

![Verbinding maken met SQL Database vanuit SSMS](./media/saas-tenancy-tenant-analytics-adf/ssmsSignIn.JPG)

In de objectverkenner:

1. Vouw de *tenants1-dpt-&lt;user&gt;* -server uit.
1. Vouw het databaseknooppunt uit en bekijk de lijst met tenantdatabases.
1. Vouw de *catalog-dpt-&lt;user&gt;* -server uit.
1. Controleer of u het analysearchief ziet met de volgende objecten:
    1. Tabellen **raw_Tickets**, **raw_Customers**, **raw_Events** en **raw_Venues** met onbewerkte, geëxtraheerde gegevens uit de tenantdatabases.
    1. De tabellen in het stervormige schema zijn **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** en **dim_Dates**.
    1. De opgeslagen procedure **sp_transformExtractedData** wordt gebruikt om de gegevens te transformeren en in de tabellen in het stervormige schema te laden.

![Schermopname met de objectverkenner waarin tabellen zijn uitgevouwen om de verschillende databaseobjecten weer te geven.](./media/saas-tenancy-tenant-analytics-adf/DWtables.JPG)

#### <a name="blob-storage"></a>Blob Storage

1. Ga in [Azure Portal](https://ms.portal.azure.com) naar de resourcegroep die u hebt gebruikt voor het implementeren van de toepassing. Controleer of er een opslagaccount met de naam **wingtipstaging\<user\>** is toegevoegd.

   ![DWtables](./media/saas-tenancy-tenant-analytics-adf/adf-staging-storage.PNG)

1. Klik op opslagaccount **wingtipstaging\<user\>** om de aanwezige objecten te bekijken.
1. Klik op de tegel **Blobs**
1. Klik op de container **configfile**
1. Controleer of **configfile** een JSON-bestand bevat met de naam **TableConfig.json**. Dit bestand bevat de namen van de bron- en doeltabel, kolomnamen en de naam van de tracker-kolom.

#### <a name="azure-data-factory-adf"></a>Azure Data Factory (ADF)

In [Azure Portal](https://ms.portal.azure.com) controleert u in de resourcegroep of een Azure Data Factory met de naam _dbtodwload-\<user\>_ is toegevoegd.

 ![adf_portal](./media/saas-tenancy-tenant-analytics-adf/adf-data-factory-portal.png)

In deze sectie wordt de gemaakte data factory bekeken.
Volg de onderstaande stappen om de data factory te starten:

1. Klik in de portal op de data factory met de naam **dbtodwload-\<user\>** .
2. Klik op de tegel **Maken en controleren** om de Data Factory-ontwerpfunctie op een afzonderlijk tabblad te openen.

## <a name="extract-load-and-transform-data"></a>Gegevens extraheren, laden en transformeren

Azure Data Factory wordt gebruikt voor het indelen van het extraheren, laden en transformeren van gegevens. In deze zelfstudie extraheert u gegevens uit vier verschillende SQL-weergaven van elk van de tenantdatabases: **rawTickets**, **rawCustomers**, **rawEvents** en **rawVenues**. Deze weergaven bevatten de locatie-id, zodat u gegevens van elke locatie in het datawarehouse kunt onderscheiden. De gegevens worden geladen in de bijbehorende faseringstabellen in het datawarehouse: **raw_Tickets**, **raw_customers**, **raw_Events** en **raw_Venue**. In een opgeslagen procedure worden de onbewerkte gegevens getransformeerd en worden de tabellen in het stervormige schema ingevuld: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** en **dim_Dates**.

In de vorige sectie hebt u de benodigde Azure-resources geïmplementeerd en geïnitialiseerd, inclusief de data factory. De geïmplementeerde data factory omvat de pijplijnen, gegevenssets, gekoppelde services, enzovoort, die vereist zijn voor het extraheren, laden en transformeren van de tenantgegevens. U gaat deze objecten verder verkennen en vervolgens de pijplijn activeren om gegevens van de tenantdatabases naar het datawarehouse te verplaatsen.

### <a name="data-factory-pipeline-overview"></a>Overzicht van de data factory-pijplijn

In deze sectie wordt de objecten bekeken die in de data factory zijn gemaakt. In de volgende afbeelding wordt de algehele werkstroom beschreven van de ADF-pijplijn die in deze zelfstudie wordt gebruikt. Als u de pijplijn liever op een later tijdstip wilt bekijken en eerst de resultaten wilt zien, gaat u naar de volgende sectie: **Uitvoering van de pijplijn activeren**.

![adf_overview](./media/saas-tenancy-tenant-analytics-adf/adf-data-factory.PNG)

Ga op de pagina met het overzicht naar het tabblad **Auteur** in het linkerdeelvenster en u ziet dat er drie [pijplijnen](../../data-factory/concepts-pipelines-activities.md) en drie [gegevenssets](../../data-factory/concepts-datasets-linked-services.md) zijn gemaakt.
![adf_author](./media/saas-tenancy-tenant-analytics-adf/adf_author_tab.JPG)

De drie geneste pijplijnen zijn: SQLDBToDW, DBCopy en TableCopy.

**Pijplijn 1 - SQLDBToDW** zoekt de namen van de tenantdatabases die in de catalogusdatabase zijn opgeslagen (tabelnaam: [__ShardManagement].[ShardsGlobal]) en wordt voor elke tenantdatabase de pijplijn **DBCopy** uitgevoerd. Na voltooiing wordt het meegeleverde schema voor de opgeslagen procedure, **sp_TransformExtractedData**, uitgevoerd. Met deze opgeslagen procedure worden de geladen gegevens in de faseringstabellen getransformeerd en worden de tabellen in het stervormige schema ingevuld.

**Pijplijn 2 - DBCopy** zoekt de namen van de brontabellen en -kolommen in een configuratiebestand dat in Blob Storage is opgeslagen.  De pijplijn **TableCopy** wordt vervolgens voor elk van de vier tabellen uitgevoerd: TicketFacts, CustomerFacts, EventFacts en VenueFacts. De activiteit **[Foreach](../../data-factory/control-flow-for-each-activity.md)** wordt parallel uitgevoerd voor alle twintig databases. In ADF kunnen maximaal twintig lus-iteraties parallel worden uitgevoerd. Overweeg meerdere pijplijnen voor meer databases te maken.

**Pijplijn 3 - TableCopy** maakt gebruik van rijversienummers in SQL Database (_rowversion_) om rijen te identificeren die zijn gewijzigd of bijgewerkt. Deze activiteit zoekt naar de begin- en eindversie van de rij voor het extraheren van rijen uit de brontabellen. De tabel **CopyTracker** die in elke tenantdatabase is opgeslagen, houdt de laatste rij bij die uit elke brontabel bij elke uitvoering is geëxtraheerd. Nieuwe of gewijzigde rijen worden in de bijbehorende faseringstabellen in het datawarehouse gekopieerd: **raw_Tickets**, **raw_Customers**, **raw_Venues** en **raw_Events**. Ten slotte wordt de laatste versie van de rij opgeslagen in de tabel **CopyTracker**. Deze moet worden gebruikt als initiële rijversie voor de volgende extractie.

Er zijn ook drie gekoppelde services met parameters die de data factory koppelen aan de SQL-brondatabase, de toegewezen SQL-doelpool en de tussenliggende blob-opslag. Klik in het tabblad **Auteur** op **Verbindingen** om de gekoppelde services te bekijken, zoals weergegeven in de volgende afbeelding:

![adf_linkedservices](./media/saas-tenancy-tenant-analytics-adf/linkedservices.JPG)

Met betrekking tot de drie gekoppelde services zijn er drie gegevenssets die verwijzen naar de gegevens die u in de pijplijnactiviteiten als invoer of uitvoer gebruikt. Bekijk elk van deze gegevenssets om gebruikte verbindingen en parameters te bekijken. _AzureBlob_ verwijst naar het configuratiebestand met de bron- en doeltabellen en de kolommen, evenals de tracker-kolom in elke bron.
  
### <a name="data-warehouse-pattern-overview"></a>Overzicht van het datawarehouse-patroon

Azure Synapse wordt gebruikt als het analysearchief voor het uitvoeren van aggregatie op de tenantgegevens. In dit voorbeeld wordt PolyBase gebruikt voor het laden van gegevens in het datawarehouse. Onbewerkte gegevens worden geladen in faseringstabellen die een identiteitskolom bevatten voor het bijhouden van rijen die in tabellen met een stervormig schema zijn getransformeerd. In de volgende afbeelding wordt het laadpatroon weergegeven: ![Diagram met het laadpatroon van databasetabellen.](./media/saas-tenancy-tenant-analytics-adf/loadingpattern.JPG)

In dit voorbeeld worden SCD-dimensietabellen (langzaam veranderende dimensie) van type 1 gebruikt. Voor elke dimensie is met behulp van een identiteitskolom een surrogaatsleutel gedefinieerd. Als best practice is de datumdimensietabel vooraf ingevuld om tijd te besparen. Voor de andere dimensietabellen wordt de instructie CREATE TABLE AS SELECT... (CTAS) gebruikt om een tijdelijke tabel te maken die de bestaande gewijzigde en niet-gewijzigde rijen bevat, en tevens de surrogaatsleutels. Dit wordt gedaan met IDENTITY_INSERT=ON. Nieuwe rijen worden vervolgens in de tabel ingevoegd met IDENTITY_INSERT=OFF. Voor een eenvoudige roll-back wordt de naam van de bestaande dimensietabel gewijzigd en wordt de naam van de tijdelijke tabel gewijzigd in die van de nieuwe dimensietabel. Voorafgaand aan elke uitvoering wordt de oude dimensietabel verwijderd.

Dimensietabellen worden vóór de feitentabel geladen. Deze volgorde garandeert dat voor elk binnenkomend feit alle dimensies waarnaar wordt verwezen al aanwezig zijn. Terwijl de feiten worden geladen, wordt de bedrijfssleutel voor elke bijbehorende dimensie vergeleken en worden de bijbehorende surrogaatsleutels aan elk feit toegevoegd.

In de laatste stap van de transformatie worden de faseringsgegevens verwijderd die gereed zijn voor de volgende uitvoering van de pijplijn.

### <a name="trigger-the-pipeline-run"></a>De pijplijnuitvoering activeren

Volg de onderstaande stappen om de volledige pijplijn voor het extraheren, laden en transformeren voor alle tenantdatabases uit te voeren:

1. Selecteer op het tabblad **Auteur** van de ADF-gebruikersinterface in het linkerdeelvenster de optie **SQLDBToDW**-pijplijn.
1. Klik op **Activeren** en klik in het vervolgkeuzemenu op **Nu activeren**. Met deze actie wordt de pijplijn onmiddellijk uitgevoerd. In een productiescenario zou u een tijdschema voor het uitvoeren van de pijplijn definiëren om de gegevens op basis van dat schema te vernieuwen.
  ![Schermopname van Factory-resources voor een pijplijn met de naam Q L D B To D W waarin de optie Activeren is uitgevouwen en Nu activeren is geselecteerd.](./media/saas-tenancy-tenant-analytics-adf/adf_trigger.JPG)
1. Klik op de pagina **Pijplijnuitvoering** op **Voltooien**.

### <a name="monitor-the-pipeline-run"></a>De pijplijnuitvoering controleren.

1. In de ADF-gebruikersinterface gaat u via het menu aan de linkerkant naar het tabblad **Monitor**.
1. Klik op **Vernieuwen** totdat de status van pijplijn SQLDBToDW **Geslaagd** is.
  ![Schermopname met pijplijn S Q L D B To D W met status Geslaagd.](./media/saas-tenancy-tenant-analytics-adf/adf_monitoring.JPG)
1. Maak door middel van SSMS verbinding met het datawarehouse en voer een query uit op de tabellen in het stervormige schema om te controleren of de gegevens in deze tabellen zijn geladen.

Zodra de pijplijn is voltooid, bevat de feitentabel gegevens over de ticketverkoop voor alle locaties en worden de dimensietabellen gevuld met de bijbehorende locaties, evenementen en klanten.

## <a name="data-exploration"></a>Gegevens verkennen

### <a name="visualize-tenant-data"></a>Tenantgegevens visualiseren

De gegevens in het stervormige schema bevatten alle gegevens over de ticketverkoop die voor uw analyse nodig zijn. Door gegevens grafisch te visualiseren, kunt u sneller trends in grote gegevenssets bekijken. In deze sectie gebruikt u **Power BI** om de tenantgegevens in het datawarehouse te bewerken en visualiseren.

Gebruik de volgende stappen om verbinding te maken met Power BI en om de weergaven te importeren die u eerder hebt gemaakt:

1. Start Power BI Desktop.
2. Selecteer op het Start-lint de optie **Gegevens ophalen** en selecteer **Meer…** in het menu.
3. In het venster **Gegevens ophalen** selecteert u **Azure SQL Database**.
4. In het aanmeldvenster van de database voert u de naam in van uw server (**catalog-dpt-&lt;User&gt;.database.windows.net**). Selecteer **Importeren** voor **Gegevensverbindingsmodus** en klik op **OK**.

    ![sign-in-to-power-bi](./media/saas-tenancy-tenant-analytics-adf/powerBISignIn.PNG)

5. Selecteer **Database** in het linkerdeelvenster en voer vervolgens voor gebruikersnaam *developer* in en voor wachtwoord *P\@ssword1*. Klik op **Verbinding maken**.  

    ![database-sign-in](./media/saas-tenancy-tenant-analytics-adf/databaseSignIn.PNG)

6. In het deelvenster **Navigator**, onder de analysedatabase, selecteert u de tabellen in het stervormige schema: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** en **dim_Dates**. Selecteer vervolgens **Laden**.

Gefeliciteerd! De gegevens zijn geladen in Power BI. Bekijk nu interessante visualisaties om inzicht te krijgen in uw tenants. U gaat kijken hoe u met analyses een aantal gegevensgestuurde aanbevelingen kunt krijgen voor het zakelijke team van Wingtip Tickets. Aan de hand van de aanbevelingen kunt u het bedrijfsmodel en de klantervaring optimaliseren.

Begin met het analyseren van de gegevens van de ticketverkoop om de verkoopcijfers per locatie te bekijken. Selecteer de opties in Power BI om een staafdiagram uit te zetten van het totale aantal verkochte tickets per locatie. (Vanwege willekeurige variatie in de ticketgenerator, kunnen de resultaten afwijken.)

![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics-adf/TotalTicketsByVenues-DW.PNG)

In de bovenstaande grafiek kunt u zien dat het aantal verkochte tickets per locatie inderdaad varieert. Locaties waar meer tickets worden verkocht, maken meer gebruik van uw service dan locaties die minder tickets verkopen. Er is een kans om de toewijzing van resources aan te passen op basis van de verschillende behoeften van de tenant.

U kunt de gegevens verder analyseren om te zien hoe de ticketverkoop gedurende een periode kan variëren. Selecteer de opties die in de volgende afbeelding in Power BI worden weergegeven om het totale aantal verkochte tickets dat dagelijks gedurende een periode van zestig dagen grafisch uit te zetten.

![SaleVersusDate](./media/saas-tenancy-tenant-analytics-adf/SaleVersusDate-DW.PNG)

In de bovenstaande grafiek ziet u dat er voor sommige locaties een piek in de ticketverkoop zit. Deze pieken versterken het idee dat in sommige locaties mogelijk onevenredig gebruikgemaakt wordt van systeemresources. Tot nu toe zit er geen duidelijk patroon in het optreden van de pieken.

U gaat nu de betekenis van deze piekdagen in de verkoop onderzoeken. Wanneer treden deze pieken op nadat de tickets in de verkoop gaan? Als u het aantal per dag verkochte tickets grafisch wilt uitzetten, selecteert u de opties die in de volgende afbeelding in Power BI worden weergegeven.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics-adf/SaleDistributionPerDay-DW.PNG)

De grafiek laat zien dat op sommige locaties op de eerste dag van verkoop een groot aantal tickets wordt verkocht. Zodra de tickets op deze locaties in de verkoop gaan, lijkt het alsof er een koopkoorts is uitgebroken. Deze verhoogde activiteit bij enkele locaties kan van invloed zijn op de service voor andere tenants.

U kunt opnieuw inzoomen op de gegevens om te zien of deze koopkoorts ook geldt voor alle evenementen die op deze locaties plaatsvinden. In voorgaande grafieken hebt u gezien dat Contoso Concert Hall veel tickets verkoopt en dat Contoso op bepaalde dagen ook een piek in de ticketverkoop laat zien. Speel met de opties in Power BI om de cumulatieve cijfers voor de ticketverkoop voor Contoso Concert Hall uit te zetten, zodat u zich kunt richten op verkooptrends voor alle evenementen die hier plaatsvinden. Volgen alle evenementen hetzelfde verkooppatroon? Probeer een grafiek te maken zoals degene hieronder.

![ContosoSales](./media/saas-tenancy-tenant-analytics-adf/EventSaleTrends.PNG)

De grafiek met de cumulatieve ticketverkoop voor Contoso Concert Hall in de loop van de tijd laat zien dat de koopkoorts niet voor elk evenement geldt. Speel met de filteropties om de verkooptrends voor andere evenementen te verkennen.

De inzichten in de patronen van de ticketverkoop kunnen ertoe leiden dat Wingtip Tickets zijn bedrijfsmodel moet optimaliseren. In plaats van alle tenants hetzelfde te laten betalen, kan Wingtip wellicht servicelagen introduceren van verschillende rekenkracht. Grotere locaties die meer tickets per dag moeten verkopen, kan een laag van een hoger niveau met een hoger service level agreement (SLA) worden aangeboden. Voor deze locaties kunnen de databases in een pool worden geplaatst met hogere resourcelimieten per database. Aan elke servicelaag kan een verkoopcijfer per uur worden toegewezen, waarbij extra kosten in rekening worden gebracht als de toewijzing wordt overschreden. Grotere locaties met periodieke piekverkopen zouden profiteren van de hogere lagen en Wingtip Tickets kan zijn service efficiënter benutten.

De klanten van Wingtip Tickets kunnen daarentegen een klacht indienen omdat ze moeite hebben genoeg tickets te verkopen die de servicekosten rechtvaardigen. Wellicht bieden deze inzichten een kans de ticketverkoop voor slecht presterende locaties te stimuleren. Een hogere verkoop kan de vermeende waarde van de service verbeteren. Klik met de rechtermuisknop op fact_Tickets en selecteer **Nieuwe meting**. Voer de volgende expressie in voor de nieuwe meting met de naam **AverageTicketsSold**:

```sql
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Selecteer de volgende visualisatieopties om het percentage verkochte tickets per locatie af te zetten en het relatieve succes vast te stellen.

![AvgTicketsByVenues](./media/saas-tenancy-tenant-analytics-adf/AvgTicketsByVenues-DW.PNG)

Hoewel de meeste locaties meer dan 80% van hun tickets verkopen, ziet u in de bovenstaande grafiek dat sommige moeite moeten doen om meer dan de helft van hun plaatsen gevuld te krijgen. Wijzig de selecties in het waardengebied om het maximum- of minimumpercentage tickets te selecteren dat bij elke locatie wordt verkocht.

## <a name="embedding-analytics-in-your-apps"></a>Analyses in uw apps insluiten

Deze zelfstudie is gericht op het uitvoeren van analyses voor alle tenants en wordt gebruikt om het inzicht van de softwareleverancier in hun tenants te verbeteren. De _tenants_ zelf kunnen uit de analyses ook inzichten verwerven, zodat ze hun zaken efficiënter kunnen managen.

In het voor beeld van Wingtip Tickets hebt u al eerder ontdekt dat de ticketverkoop vaak voorspelbare patronen volgt. Deze inzichten kunnen worden gebruikt om slecht presterende locaties hun ticketverkoop te helpen vergroten. Misschien kunt u machine learning-technieken gebruiken om de verkoop van tickets voor evenementen te voorspellen. De gevolgen van prijswijzigingen kunnen ook worden gemodelleerd, zodat de impact van het aanbieden van kortingen kan worden voorspeld. Power BI Embedded kan worden geïntegreerd in een toepassing voor het beheren van evenementen om voorspellende omzet te visualiseren, inclusief de impact van kortingen op het totale aantal verkochte plaatsen en de opbrengst bij slecht verkopende evenementen. Met Power BI Embedded kunt u zelfs de toegepaste korting op de ticketprijzen rechtstreeks in de visualisatie integreren.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
>
> - Het analysearchief van de tenant maken voor het laden.
> - Azure Data Factory (ADF) gebruiken voor het extraheren van gegevens uit elke tenantdatabase in het datawarehouse voor analyse.
> - De geëxtraheerde gegevens (opnieuw in een stervormig schema ondergebracht) optimaliseren.
> - Query's uitvoeren op het datawarehouse voor analyse.
> - Power BI gebruiken voor gegevensvisualisatie om trends in tenantgegevens aan te geven en aanbevelingen voor verbeteringen te doen.

Gefeliciteerd!

## <a name="additional-resources"></a>Aanvullende bronnen

- Aanvullende [zelfstudies waarbij wordt voortgebouwd op de Wingtip-SaaS-toepassing](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).