---
title: 'Gids voor predictief onderhoud voor lucht vaart: team data Science process'
description: Een technische hand leiding voor het oplossings sjabloon voor predictief onderhoud in de lucht vaart, hulpprogram ma's en het Trans Port.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 3edeee8f41c806c90f32208c0c4f174c76ba38d0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "93321990"
---
# <a name="technical-guide-to-the-solution-template-for-predictive-maintenance-in-aerospace"></a>Technische hand leiding voor het oplossings sjabloon voor predictief onderhoud in lucht vaart

> [!Important]
> Dit artikel is afgeschaft. De discussie over voorspellend onderhoud in B.v. is nog steeds relevant, maar voor actuele informatie raadpleegt u het [overzicht van oplossingen voor zakelijke doel groepen](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Oplossings sjablonen zijn ontworpen om het proces van het bouwen van een E2E-demo te versnellen. Een geïmplementeerde sjabloon voorziet in uw abonnement met de benodigde onderdelen en bouwt vervolgens de relaties tussen de sjablonen. Daarnaast wordt de gegevens pijplijn met voorbeeld gegevens van een data generator toepassing die u downloadt en installeert op uw lokale computer nadat u de oplossings sjabloon hebt geïmplementeerd. De gegevens van de generator hebben de gegevens pijplijn gehydrateerd en beginnen machine learning voor spellingen te genereren, die vervolgens kunnen worden gevisualiseerd op het Power BI dash board.

Het implementatie proces begeleidt u door verschillende stappen voor het instellen van de referenties van uw oplossing. Zorg ervoor dat u de referenties vastlegt, zoals de naam van de oplossing, de gebruikers naam en het wacht woord die u tijdens de implementatie hebt opgegeven. 


De doel stellingen van dit artikel zijn:
- Beschrijf de referentie architectuur en onderdelen die in uw abonnement zijn ingericht.
- Laat zien hoe u de voorbeeld gegevens vervangt door uw eigen gegevens. 
- Laat zien hoe u de oplossings sjabloon kunt wijzigen.  

## <a name="overview"></a>Overzicht
![Architectuur voor voor speld onderhoud](./media/predictive-maintenance-technical-guide/predictive-maintenance-architecture.png)

Wanneer u de oplossing implementeert, worden Azure-Services, waaronder Event hub, Stream Analytics, HDInsight, Data Factory en Machine Learning, geactiveerd. In het architectuur diagram ziet u hoe het voorspellende onderhoud voor de oplossings sjabloon voor de sjablonen voor voor speld wordt gemaakt. U kunt deze services in de Azure Portal onderzoeken door erop te klikken in het oplossings sjabloon diagram dat is gemaakt met de implementatie van de oplossing (met uitzonde ring van HDInsight, dat op aanvraag is ingericht wanneer de gerelateerde pijplijn activiteiten moeten worden uitgevoerd en daarna worden verwijderd).
Down load een [volledige versie van het diagram](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

In de volgende secties worden de oplossings onderdelen beschreven.

## <a name="data-source-and-ingestion"></a>Gegevens bron en opname
### <a name="synthetic-data-source"></a>Synthetische gegevens bron
Voor deze sjabloon wordt de gebruikte gegevens bron gegenereerd op basis van een gedownloade bureaublad toepassing die u lokaal uitvoert na een geslaagde implementatie.

Als u de instructies voor het downloaden en installeren van deze toepassing wilt vinden, selecteert u het eerste knoop punt, Predictive Maintenance data generator, in het oplossings sjabloon diagram. De instructies vindt u in de eigenschappen balk. Met deze toepassing wordt de [Azure Event hub](#azure-event-hub) -service gevoed door gegevens punten of gebeurtenissen die worden gebruikt in de rest van de oplossings stroom. Deze gegevens bron is afgeleid van openbaar beschik bare gegevens uit de [NASA-gegevensopslag plaats](https://c3.nasa.gov/dashlink/resources/139/) met behulp van de [simulatie gegevensset voor het degraderen](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan)van de turbofan-engine.

De toepassing voor het genereren van gebeurtenissen vult de Azure Event hub alleen in wanneer deze op uw computer wordt uitgevoerd.  

### <a name="azure-event-hub"></a>Azure Event Hub  
De [Azure Event hub](https://azure.microsoft.com/services/event-hubs/) -service is de ontvanger van de invoer die is opgegeven door de synthetische gegevens bron.

## <a name="data-preparation-and-analysis"></a>Gegevens voorbereiding en-analyse  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Gebruik [Azure stream Analytics](https://azure.microsoft.com/services/stream-analytics/) om bijna realtime analyses te bieden op de invoer stroom vanuit de [Azure Event hub](#azure-event-hub) -service. U publiceert vervolgens resultaten naar een [Power bi](https://powerbi.microsoft.com) dash board en archiveert alle onbewerkte binnenkomende gebeurtenissen naar de [Azure Storage](https://azure.microsoft.com/services/storage/) -service voor latere verwerking door de [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) service.

### <a name="hdinsight-custom-aggregation"></a>Aangepaste HDInsight-aggregatie
Voer [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) -scripts (in azure Data Factory) uit met HDInsight om aggregaties te bieden voor de onbewerkte gebeurtenissen die zijn gearchiveerd met de Azure stream Analytics-resource.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Maak voor spellingen voor de resterende levens duur (resterende levens duur) van een bepaalde vliegtuig motor met behulp van de invoer ontvangen met [Azure machine learning service](https://azure.microsoft.com/services/machine-learning/) (georganiseerd door Azure Data Factory). 

## <a name="data-publishing"></a>Gegevens publiceren
### <a name="azure-sql-database"></a>Azure SQL Database
Gebruik [Azure SQL database](https://azure.microsoft.com/services/sql-database/) om de voor spellingen op te slaan die worden ontvangen door de Azure machine learning, die vervolgens worden gebruikt in het [Power bi](https://powerbi.microsoft.com) dash board.

## <a name="data-consumption"></a>Gegevensverbruik
### <a name="power-bi"></a>Power BI
Gebruik [Power bi](https://powerbi.microsoft.com) om een dash board weer te geven dat aggregaties en waarschuwingen bevat die door [Azure stream Analytics](https://azure.microsoft.com/services/stream-analytics/)worden geleverd, evenals resterende levens duur-voor spellingen die zijn opgeslagen in [Azure SQL database](https://azure.microsoft.com/services/sql-database/) die zijn gemaakt met behulp van [Azure machine learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Uw eigen gegevens meenemen
In deze sectie wordt beschreven hoe u uw eigen gegevens naar Azure brengt en welke gebieden wijzigingen vereisen voor de gegevens die u in deze architectuur meebrengt.

Het is niet waarschijnlijk dat uw gegevensset overeenkomt met de gegevensset die wordt gebruikt door de [simulatie gegevensset](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) van de turbofan-engine die wordt gebruikt voor deze oplossings sjabloon. Informatie over uw gegevens en de vereisten zijn van cruciaal belang voor het wijzigen van deze sjabloon om met uw eigen gegevens te werken. 

In de volgende secties worden de onderdelen van de sjabloon besproken die moeten worden gewijzigd wanneer er een nieuwe gegevensset wordt geïntroduceerd.

### <a name="azure-event-hub"></a>Azure Event Hub
Azure Event hub is gene riek; gegevens kunnen in een CSV-of JSON-indeling naar de hub worden gepost. Er vindt geen speciale verwerking plaats in de Event hub van Azure, maar het is belang rijk dat u begrijpt welke gegevens erin worden ingevoerd.

In dit document wordt niet beschreven hoe u uw gegevens opneemt, maar u kunt eenvoudig gebeurtenissen of gegevens verzenden naar een Azure Event hub met behulp van de Event hub-Api's.

### <a name="azure-stream-analytics"></a><a name="azure-stream-analytics-1"></a>Azure Stream Analytics
Gebruik de Azure Stream Analytics resource om bijna realtime analyses te bieden door gegevens stromen te lezen en gegevens uit te voeren naar een wille keurig aantal bronnen.

Voor de oplossings sjabloon voor voor speld onderhoud bestaat de Azure Stream Analytics query uit vier subquery's, waarbij elke query gebeurtenissen uit de Azure Event hub-service gebruikt, met uitvoer naar vier verschillende locaties. Deze uitvoer bestaat uit drie Power BI gegevens sets en één Azure Storage locatie.

De Azure Stream Analytics query kan worden gevonden door:

* Verbinding maken met de Azure Portal
* Zoeken naar het Stream Analytics- ![ taak stream Analytics pictogram ](./media/predictive-maintenance-technical-guide/icon-stream-analytics.png) dat is gegenereerd toen de oplossing werd geïmplementeerd (*bijvoorbeeld* **maintenancesa02asapbi** en **maintenancesa02asablob** voor de oplossing voor voor speld onderhoud)
* Selecteren
  
  * ***Invoer voor het*** weer geven van de query-invoer
  * ***Query*** voor het weer geven van de query zelf
  * ***Uitvoer*** voor het weer geven van de verschillende uitvoer

Informatie over het bouwen van Azure Stream Analytics query's vindt u in de naslag informatie over het [Stream Analytics query](/stream-analytics-query/stream-analytics-query-language-reference) op MSDN.

In deze oplossing worden met de query's drie gegevens sets uitgevoerd met bijna realtime analyse gegevens over de binnenkomende gegevens stroom naar een Power BI dash board dat is opgenomen als onderdeel van deze oplossings sjabloon. Omdat er impliciete kennis is over de binnenkomende gegevens indeling, moeten deze query's worden gewijzigd op basis van uw gegevens indeling.

Met de query in de tweede Stream Analytics-taak **maintenancesa02asablob** worden alleen alle [Event hub](https://azure.microsoft.com/services/event-hubs/) -gebeurtenissen naar [Azure Storage](https://azure.microsoft.com/services/storage/) uitgevoerd en is er dus geen wijziging nodig, ongeacht uw gegevens indeling, omdat de volledige gebeurtenis gegevens naar de opslag worden gestreamd.

### <a name="azure-data-factory"></a>Azure Data Factory
De [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) -service organiseert de verplaatsing en verwerking van gegevens. In de oplossings sjabloon voor voor speld onderhoud voor sjablonen is de data factory bestaat uit drie [pijp lijnen](../../data-factory/concepts-pipelines-activities.md) die de gegevens met behulp van verschillende technologieën verplaatsen en verwerken.  Open uw data factory door het knoop punt Data Factory aan de onderkant van het oplossings sjabloon diagram te openen dat is gemaakt met de implementatie van de oplossing. Fouten onder uw gegevens sets worden veroorzaakt door data factory worden geïmplementeerd voordat de gegevens generator werd gestart. Deze fouten kunnen worden genegeerd en verhinderen dat uw data factory werkt.

![Fouten in de Data Factory-gegevensset](./media/predictive-maintenance-technical-guide/data-factory-dataset-error.png)

In deze sectie worden de benodigde [pijp lijnen en activiteiten](../../data-factory/concepts-pipelines-activities.md) in de [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)besproken. Hier volgt een diagram weergave van de oplossing.

![Azure Data Factory](./media/predictive-maintenance-technical-guide/azure-data-factory.png)

Twee van de pijp lijnen van deze Factory bevatten [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) -scripts die worden gebruikt om de gegevens te partitioneren en samen te voegen. De scripts bevinden zich in het [Azure Storage](https://azure.microsoft.com/services/storage/) -account dat tijdens de installatie is gemaakt. De locatie is: maintenancesascript \\ \\ \\ \\ -script Hive \\ \\ (of https://[uw oplossings naam]. blob. core. Windows. net/maintenancesascript).

Net als bij [Azure stream Analytics](#azure-stream-analytics-1) query's hebben de [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) -scripts impliciete kennis over de binnenkomende gegevens indeling en moeten ze worden gewijzigd op basis van uw gegevens indeling.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Deze [pijp lijn](../../data-factory/concepts-pipelines-activities.md) bevat één activiteit: een [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) -activiteit met een [HDInsightLinkedService](/previous-versions/azure/dn893526(v=azure.100)) die een [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) -script uitvoert voor het partitioneren van de gegevens die in [Azure Storage](https://azure.microsoft.com/services/storage/) worden geplaatst tijdens de [Azure stream Analytics](https://azure.microsoft.com/services/stream-analytics/) taak.

Het [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) -script voor deze partitie taak is ***AggregateFlightInfo. HQL***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Deze [pijp lijn](../../data-factory/concepts-pipelines-activities.md) bevat verschillende activiteiten waarvan het eind resultaat de gescoorde voor spelling is van het [Azure machine learning](https://azure.microsoft.com/services/machine-learning/) experiment dat is gekoppeld aan deze oplossings sjabloon.

Inbegrepen activiteiten zijn:

* [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) -activiteit met een [HDInsightLinkedService](/previous-versions/azure/dn893526(v=azure.100)) die een [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) -script uitvoert om aggregaties en functie-engineering uit te voeren die nodig zijn voor het [Azure machine learning](https://azure.microsoft.com/services/machine-learning/) -experiment.
  Het [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) -script voor deze partitie taak is ***PrepareMLInput. HQL***.
* [Kopieer](/previous-versions/azure/dn835035(v=azure.100)) activiteit waarmee de resultaten van de [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) -activiteit worden verplaatst naar een enkele [Azure Storage](https://azure.microsoft.com/services/storage/) blob die wordt geopend door de [AzureMLBatchScoring](/previous-versions/azure/dn894009(v=azure.100)) -activiteit.
* Met [AzureMLBatchScoring](/previous-versions/azure/dn894009(v=azure.100)) -activiteit wordt het [Azure machine learning](https://azure.microsoft.com/services/machine-learning/) experiment aangeroepen, waarbij de resultaten in één [Azure Storage](https://azure.microsoft.com/services/storage/) BLOB worden geplaatst.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Deze [pijp lijn](../../data-factory/concepts-pipelines-activities.md) bevat één activiteit: een [Kopieer](/previous-versions/azure/dn835035(v=azure.100)) activiteit waarmee de resultaten van het [Azure machine learning](#azure-machine-learning) experiment worden verplaatst van de ***MLScoringPipeline*** naar de [Azure SQL database](https://azure.microsoft.com/services/sql-database/) ingericht als onderdeel van de installatie van de oplossings sjabloon.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Het [Azure machine learning](https://azure.microsoft.com/services/machine-learning/) experiment dat wordt gebruikt voor deze oplossings sjabloon biedt de resterende levens duur (resterende levens duur) van een vliegtuig motor. Het experiment is specifiek voor de gebruikte gegevensset en vereist aanpassing of vervanging specifiek voor de gegevens die in worden gebracht.

## <a name="monitor-progress"></a>Voortgang van monitor
Zodra de gegevens Generator is gestart, begint de pijp lijn te gedehydrateerd en de verschillende onderdelen van uw oplossing beginnen met het uitvoeren van de opdrachten die worden verstrekt door de data factory. Er zijn twee manieren om de pijp lijn te bewaken.

* Met een van de Stream Analytics Jobs worden de onbewerkte binnenkomende gegevens naar Blob-opslag geschreven. Als u in het scherm op Blob Storage onderdeel van uw oplossing klikt en vervolgens in het rechterdeel venster op openen klikt, gaat u naar de [Azure Portal](https://portal.azure.com/). Klik daarna op blobs. In het volgende deel venster ziet u een lijst met containers. Klik op **maintenancesadata**. In het volgende deel venster vindt u de map **rawdata** . In de map rawdata zijn mappen met namen zoals Hour = 17 en uur = 18. De aanwezigheid van deze mappen geeft aan dat onbewerkte gegevens op uw computer worden gegenereerd en opgeslagen in Blob Storage. U ziet CSV-bestanden met een eindige grootte in MB in deze mappen.
* De laatste stap van de pijp lijn is het schrijven van gegevens (bijvoorbeeld voor spelling van machine learning) naar SQL Database. Mogelijk moet u Maxi maal drie uur wachten tot de gegevens worden weer gegeven in SQL Database. Een manier om te controleren hoeveel gegevens er in uw SQL Database beschikbaar zijn, is via de [Azure Portal](https://portal.azure.com/). Zoek in het linkerdeel venster naar SQL-data BASEs :::image type="icon" source="./media/predictive-maintenance-technical-guide/icon-SQL-databases.png" border="false"::: en klik erop. Zoek vervolgens de **pmaintenancedb** van de data base en klik erop. Klik op de volgende pagina onderaan op beheren.
   
    ![Pictogram beheren](./media/predictive-maintenance-technical-guide/icon-manage.png)
   
    Hier kunt u klikken op nieuwe query en query voor het aantal rijen (bijvoorbeeld aantal selecteren (*) van PMResult). Naarmate uw data base groeit, moet het aantal rijen in de tabel toenemen.

## <a name="power-bi-dashboard"></a>Power BI Dashboard

Stel een Power BI-dash board in voor het visualiseren van uw Azure Stream Analytics gegevens (warme pad) en de resultaten van batch voorspelling van Azure machine learning (koude pad).

### <a name="set-up-the-cold-path-dashboard"></a>Het koude pad-dash board instellen
In de gegevens pijplijn met koude paden is het doel om de voorspellende resterende levens duur (resterende levens duur) van elke vliegtuig motor te verkrijgen zodra een vlucht (cyclus) is voltooid. Het Voorspellings resultaat wordt elke drie uur bijgewerkt voor het voors pellen van de vliegtuig motoren die een vlucht in de afgelopen drie uur hebben voltooid.

Power BI maakt verbinding met een Azure SQL Database als de bijbehorende gegevens bron, waarbij de Voorspellings resultaten worden opgeslagen. 

Opmerking: 
1.    Bij het implementeren van uw oplossing wordt binnen drie uur een voor spelling in de Data Base weer gegeven. Het pbix-bestand dat is meegeleverd met de generator van het pakket bevat enkele seed-gegevens, zodat u het dash board van Power BI direct kunt maken. 
2.    In deze stap is het vereist dat u de gratis software [Power bi bureau blad](/power-bi/fundamentals/desktop-get-the-desktop)downloadt en installeert.

De volgende stappen begeleiden u bij het koppelen van het pbix-bestand aan de SQL Database die is opgetreden op het moment van de oplossings implementatie met gegevens (bijvoorbeeld Voorspellings resultaten) voor visualisatie.

1. De database referenties ophalen.
   
   U hebt de **naam van de database server, de database naam, de gebruikers naam en het wacht woord** nodig voordat u verdergaat met de volgende stappen. Hier volgen de stappen om u te helpen bij het vinden van deze.
   
   * Zodra de **Azure SQL database** in het diagram van de oplossings sjabloon groen wordt, klikt u erop en klikt u vervolgens op **openen**.
   * Er wordt een nieuw browser tabblad/-venster weer gegeven met de Azure Portal-pagina. Klik op **resource groepen** in het linkerdeel venster.
   * Selecteer het abonnement dat u gebruikt voor het implementeren van de oplossing en selecteer vervolgens **' YourSolutionName \_ ResourceGroup '**.
   * Klik in het deel venster nieuwe pop-out op het  :::image type="icon" source="./media/predictive-maintenance-technical-guide/icon-sql.png" border="false"::: pictogram voor toegang tot uw data base. De naam van uw Data Base bevindt zich naast dit pictogram (bijvoorbeeld **' pmaintenancedb '**) en de naam van de **database server** wordt vermeld onder de eigenschap Server naam en moet er ongeveer uitzien als **YourSolutionName.database.Windows.net**.
   * De **gebruikers** naam en het **wacht woord** voor de Data Base zijn hetzelfde als de gebruikers naam en het wacht woord die eerder zijn vastgelegd tijdens de implementatie van de oplossing.
2. Werk de gegevens bron van het rapport bestand met koude paden bij met Power BI Desktop.
   
   * Dubbel klik in de map waar u het generator bestand hebt gedownload en uitgepakt op het bestand **PowerBI \\ PredictiveMaintenanceAerospace. pbix** . Als er waarschuwings berichten worden weer gegeven wanneer u het bestand opent, negeert u deze. Klik boven aan het bestand op **Query's bewerken**.
     
     ![Query's bewerken](./media/predictive-maintenance-technical-guide/edit-queries.png)
   * U ziet twee tabellen, **RemainingUsefulLife** en **PMResult**. Selecteer de eerste tabel en klik op ![ het pictogram query instellingen ](./media/predictive-maintenance-technical-guide/icon-query-settings.png) naast **bron** onder **toegepaste stappen** in het rechterdeel venster **' query instellingen '** . Negeer eventuele waarschuwingsberichten die worden weergegeven.
   * Vervang in het venster pop-out **' server '** en **' data base '** door uw eigen server-en database namen en klik vervolgens op **OK**. Zorg ervoor dat u voor de server naam de poort 1433 (**YourSolutionName.database.Windows.net, 1433**) opgeeft. Verlaat het veld Data Base als **pmaintenancedb**. De waarschuwings berichten negeren die op het scherm worden weer gegeven.
   * In het volgende pop-out venster ziet u twee opties in het linkerdeel venster (**Windows** en **Data Base**). Klik op **Data Base**, vul uw **gebruikers naam** en **wacht** woord in (de gebruikers naam en het wacht woord die u hebt ingevoerd toen u de oplossing voor het eerst implementeerde en een Azure SQL database hebt gemaakt). Controleer de optie database niveau in ***Selecteer welk niveau u deze instellingen wilt Toep assen op***. Klik vervolgens op **verbinding maken**.
   * Klik op de tweede tabel **PMResult** en klik vervolgens op ![ Navigatie pictogram ](./media/predictive-maintenance-technical-guide/icon-navigation.png) naast **' Bron '** onder **' toegepaste stappen '** in het rechterdeel venster ' **query instellingen** ' en werk de namen van de server en de data base bij, zoals in de bovenstaande stappen. Klik vervolgens op OK.
   * Nadat u de vorige pagina hebt weer gegeven, sluit u het venster. Er wordt een bericht weer gegeven: Klik op **Toep assen**. Klik ten slotte op de knop **Opslaan** om de wijzigingen op te slaan. Uw Power BI-bestand heeft nu verbinding gemaakt met de server. Als uw visualisaties leeg zijn, moet u de selecties op de visualisaties wissen om alle gegevens te visualiseren door te klikken op het pictogram gum in de rechter bovenhoek van de legenda's. Gebruik de knop Vernieuwen om nieuwe gegevens in de visualisaties weer te geven. In eerste instantie worden alleen de seedgegevens in uw visualisaties weer gegeven, omdat de data factory is gepland om elke 3 uur te vernieuwen. Na 3 uur ziet u nieuwe voor spellingen die worden weer gegeven in uw visualisaties Wanneer u de gegevens vernieuwt.
3. Beschrijving Publiceer het dash board koud pad om [online te Power bi](https://www.powerbi.com/). Voor deze stap hebt u een Power BI-account (of een werk-of school account) nodig.
   
   * Klik op **publiceren** en een paar seconden later verschijnt een venster met de weer gave ' publiceren naar Power bi slagen! '. met een groen vinkje. Klik op de koppeling onder "open PredictiveMaintenanceAerospace. pbix in Power BI". Zie [publiceren vanuit Power bi Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop)voor gedetailleerde instructies.
   * Als u een nieuw dash board wilt maken, klikt u op het **+** teken naast het gedeelte **Dash boards** in het linkerdeel venster. Voer de naam ' demo voor voor speld onderhoud ' in voor dit nieuwe dash board.
   * Zodra u het rapport hebt geopend, klikt u op het ![ pictogram vastmaken ](./media/predictive-maintenance-technical-guide/icon-pin.png) om alle visualisaties aan uw dash board vast te maken. Zie [een tegel vastmaken aan een Power bi-dash board vanuit een rapport](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report)om gedetailleerde instructies te vinden.
     Ga naar de dashboard pagina en pas de grootte en locatie van uw visualisaties aan en bewerk de titels. Zie [een tegel bewerken--formaat wijzigen, verplaatsen, naam wijzigen, vastmaken, verwijderen, Hyper Link toevoegen](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename)voor gedetailleerde instructies voor het bewerken van uw tegels. Hier volgt een voor beeld van een dash board met enkele visualisaties met koude paden die hieraan zijn vastgemaakt.  Afhankelijk van hoe lang u uw gegevens Generator uitvoert, kunnen uw getallen in de visualisaties anders zijn.
     <br/>
     ![Laatste weer gave](./media/predictive-maintenance-technical-guide/final-view.png)
     <br/>
   * Als u de gegevens wilt vernieuwen, houdt u de muis aanwijzer over de **PredictiveMaintenanceAerospace** -gegevensset, klikt u op ![ pictogram met het weglatings teken ](./media/predictive-maintenance-technical-guide/icon-elipsis.png) en kiest u vervolgens **vernieuwen plannen**.
     <br/>
     > [!NOTE]
     > Als er een waarschuwings bericht wordt weer gegeven, klikt u op **referenties bewerken** en controleert u of de database referenties hetzelfde zijn als de gegevens die u in stap 1 hebt beschreven.
     <br/>
     ![Planning vernieuwen](./media/predictive-maintenance-technical-guide/schedule-refresh.png)
     <br/>
   * Vouw de sectie **planning vernieuwen** uit. Schakel de optie uw gegevens up-to-date houden in.
     <br/>
   * Plan het vernieuwen op basis van uw behoeften. Zie [gegevens vernieuwen in Power bi](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi)voor meer informatie.

### <a name="setup-hot-path-dashboard"></a>Dash board voor dynamische paden instellen
De volgende stappen begeleiden u bij het visualiseren van gegevens uitvoer van Stream Analytics-taken die zijn gegenereerd op het moment van de implementatie van de oplossing. U hebt een [Power bi online](https://www.powerbi.com/) account nodig om de volgende stappen uit te voeren. Als u geen account hebt, kunt u er [een maken](https://powerbi.microsoft.com/pricing).

1. Power BI uitvoer toevoegen in Azure Stream Analytics (ASA).
   
   * U moet de instructies in [Azure Stream Analytics & Power BI: een dash board van Analytics voor realtime zicht baarheid van streaminggegevens](../../stream-analytics/stream-analytics-power-bi-dashboard.md) om de uitvoer van uw Azure stream Analytics-taak in te stellen als uw Power bi dash board.
   * De ASA-query heeft drie uitvoer, **aircraftmonitor**, **aircraftalert** en **flightsbyhour**. U kunt de query weer geven door te klikken op het tabblad query. Die overeenkomt met elk van deze tabellen, moet u een uitvoer toevoegen aan ASA. Wanneer u de eerste uitvoer (**aircraftmonitor**) toevoegt, moet u ervoor zorgen dat de **uitvoer alias**, de naam van de **gegevensset** en de **tabel naam** hetzelfde zijn (**aircraftmonitor**). Herhaal de stappen voor het toevoegen van uitvoer voor **aircraftalert** en **flightsbyhour**. Zodra u alle drie de uitvoer tabellen hebt toegevoegd en de ASA-taak hebt gestart, wordt een bevestigings bericht weer gegeven ("starten Stream Analytics-taak maintenancesa02asapbi geslaagd").
2. Meld u online aan bij [Power bi](https://www.powerbi.com)
   
   * In de sectie gegevens sets van het linkerdeel venster in mijn werk ruimte moeten de ***gegevensset** _ namen _ * aircraftmonitor * *, **aircraftalert** en **flightsbyhour** worden weer gegeven. Dit zijn de streaminggegevens die u hebt gepusht van Azure Stream Analytics in de vorige stap. De **flightsbyhour** van de gegevensset wordt mogelijk niet tegelijk met de andere twee gegevens sets weer gegeven vanwege de aard van de SQL-query achter deze. Het moet echter na een uur worden weer gegeven.
   * Zorg ervoor dat het deel venster ***Visualisaties*** is geopend en wordt weer gegeven aan de rechter kant van het scherm.
3. Zodra u de gegevens in Power BI hebt geplaatst, kunt u beginnen met het visualiseren van de gegevens stromen. Hieronder ziet u een voor beeld van een dash board met een aantal hot path-visualisaties. U kunt andere dashboard tegels maken op basis van de juiste gegevens sets. Afhankelijk van hoe lang u uw gegevens Generator uitvoert, kunnen uw getallen in de visualisaties anders zijn.

    ![Dashboardweergave](media/predictive-maintenance-technical-guide/dashboard-view.png)

1. Hier volgen enkele stappen voor het maken van een van de bovenstaande tegels: de tegel ' vloot weergave van sensor 11 versus drempel waarde 48,26 '.
   
   * Klik op gegevensset **aircraftmonitor** in het gedeelte gegevens sets van het linkerdeel venster.
   * Klik op het pictogram **lijn diagram** .
   * Klik op **verwerkt** in het deel venster **velden** , zodat deze onder as wordt weer gegeven in het deel venster **Visualisaties** .
   * Klik op "S11" en "S11- \_ waarschuwing" zodat deze beide onder "waarden" worden weer gegeven. Klik op de kleine pijl naast **S11** -en **S11- \_ waarschuwing**, Wijzig "Sum" in "Average".
   * Klik bovenaan op **Opslaan** en noem het rapport ' aircraftmonitor '. Het rapport met de naam ' aircraftmonitor ' wordt weer gegeven in de sectie **rapporten** in het **Navigatie** deel venster aan de linkerkant.
   * Klik op het pictogram voor het **visuele element** in de rechter bovenhoek van dit lijn diagram. Het venster ' vastmaken aan dash board ' kan worden weer gegeven om een dash board te kiezen. Selecteer demo voor voor speld onderhoud en klik vervolgens op vastmaken.
   * Houd de muis aanwijzer boven deze tegel op het dash board en klik op het pictogram bewerken in de rechter bovenhoek om de titel te wijzigen in ' vloot weergave van sensor 11 versus drempel waarde 48,26 ' en ondertitel op ' gemiddeld over de hele periode '.

## <a name="delete-your-solution"></a>Uw oplossing verwijderen
Zorg ervoor dat u de gegevens Generator stopt wanneer u de oplossing niet actief gebruikt voor het uitvoeren van de gegevens generator, waardoor er hogere kosten in rekening worden gebracht. Verwijder de oplossing als u deze niet gebruikt. Als u de oplossing verwijdert, worden alle onderdelen die in uw abonnement zijn ingericht, verwijderd wanneer u de oplossing implementeert. Als u de oplossing wilt verwijderen, klikt u op de naam van de oplossing in het linkerdeel venster van de oplossings sjabloon en klikt u vervolgens op **verwijderen**.

## <a name="cost-estimation-tools"></a>Hulpprogram ma's voor kosten raming
De volgende twee hulpprogram ma's zijn beschikbaar om u te helpen beter inzicht te krijgen in de totale kosten die zijn verbonden aan het uitvoeren van de oplossings sjabloon voor predictief onderhoud voor sjablonen in uw abonnement:

* [Hulp programma voor Microsoft Azure kosten Estimator (online)](https://azure.microsoft.com/pricing/calculator/)
* [Hulp programma voor Microsoft Azure kosten Estimator (Desktop)](https://www.microsoft.com/download/details.aspx?id=43376)