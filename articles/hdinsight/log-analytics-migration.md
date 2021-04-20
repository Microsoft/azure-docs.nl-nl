---
title: Log Analytics-gegevens migreren voor Azure HDInsight
description: Meer informatie over de wijzigingen in Azure Monitor integratie en best practices voor het gebruik van de nieuwe tabellen.
ms.service: hdinsight
ms.topic: how-to
ms.author: ali
author: AliciaLiMicrosoft
ms.date: 04/19/2021
ms.openlocfilehash: 6659b515ee2d25a4b9136ccfac4cc3444e491438
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741598"
---
# <a name="log-analytics-migration-guide-for-azure-hdinsight-clusters"></a>Log Analytics-migratiehandleiding voor Azure HDInsight clusters

 Azure HDInsight is een beheerde clusterservice die klaar is voor bedrijven. Deze service voert opensource-analysekaders uit, zoals Apache Spark, Hadoop, HBase en Kafka in Azure. Azure HDInsight is geïntegreerd met andere Azure-services, zodat klanten hun big data analytics-toepassingen beter kunnen beheren.

Log Analytics biedt een hulpprogramma in de Azure Portal logboekquery's te bewerken en uit te voeren. De query's zijn afkomstig van gegevens die zijn verzameld Azure Monitor logboeken en analyseren de resultaten interactief. Klanten kunnen Log Analytics-query's gebruiken om records op te halen die voldoen aan specifieke criteria. Ze kunnen ook query's gebruiken om trends te identificeren, patronen te analyseren en inzicht te bieden in hun gegevens.

Azure HDInsight integratie met Log Analytics in 2017 ingeschakeld. HDInsight-klanten gebruiken deze functie snel om hun HDInsight-clusters te bewaken en query's uit te voeren op de logboeken in de clusters. Hoewel de acceptatie van deze functie is toegenomen, hebben klanten feedback gegeven over de integratie:

- Klanten kunnen niet bepalen welke logboeken moeten worden opgeslagen en het opslaan van alle logboeken kan duur worden.
- De huidige HDInsight-schemalogboeken volgen geen consistente naamconventen en sommige tabellen worden herhaald.
- Klanten willen een out-of-box-dashboard om eenvoudig de KPI van hun HDInsight-clusters te bewaken.
- Klanten moeten naar Log Analytics gaan om eenvoudige query's uit te voeren.

## <a name="solution-overview"></a>Oplossingenoverzicht

Gezien de feedback van klanten heeft het Azure HDInsight team geïnvesteerd in integratie met Azure Monitor. Deze integratie maakt het volgende mogelijk:

- Een nieuwe set tabellen in de Log Analytics-werkruimte van klanten. De nieuwe tabellen worden geleverd via een nieuwe Log Analytics-pijplijn.
- Hogere betrouwbaarheid
- Snellere levering van logboeken
- Tabelgroepering op basis van resources en standaardquery's

## <a name="benefits-of-the-new-azure-monitor-integration"></a>Voordelen van de nieuwe Azure Monitor integratie

Dit document bevat een overzicht van de wijzigingen in Azure Monitor integratie en best practices voor het gebruik van de nieuwe tabellen.

**Opnieuw ontworpen schema's:** de schemaopmaak voor de Azure Monitor integratie is beter geordend en gemakkelijk te begrijpen. Er zijn twee derde minder schema's om zo veel mogelijk dubbelzinnigheid in de verouderde schema's te verwijderen.

**Selectieve logboekregistratie (binnenkort beschikbaar)**: Er zijn logboeken en metrische gegevens beschikbaar via Log Analytics. Om u te helpen besparen op de bewakingskosten, brengen we een nieuwe functie voor selectieve logboekregistratie uit. Gebruik deze functie om verschillende logboeken en metrische bronnen in en uit te schakelen. Met deze functie hoeft u alleen te betalen voor wat u gebruikt.

**Integratie van clusterportal voor logboeken:** **het deelvenster Logboeken** is nieuw in de HDInsight-clusterportal. Iedereen met toegang tot het cluster kan naar dit deelvenster gaan om een query uit te voeren op elke tabel waar de clusterresource records naartoe verzendt. Gebruikers hebben geen toegang meer nodig tot de Log Analytics-werkruimte om de records voor een specifieke clusterresource te zien.

**Integratie van insights-clusterportal:** het deelvenster **Inzichten** is ook nieuw in de HDInsight-clusterportal. Nadat u de nieuwe Azure Monitor-integratie hebt  inschakelen, kunt u het deelvenster Inzichten selecteren. Er wordt automatisch een out-of-box-dashboard voor logboeken en metrische gegevens ingevuld dat specifiek is voor het clustertype. Deze dashboards zijn vernieuwd van onze vorige Azure-oplossingen. Ze geven u een diep inzicht in de prestaties en status van uw cluster.

**Inzichten op schaal:** u kunt de nieuwe werkmap **At-Scale Insights** in de **Azure Monitor-portal** gebruiken om de status en prestaties van uw clusters voor verschillende abonnementen te bewaken.

## <a name="customer-scenarios"></a>Klantscenario's

In de volgende secties wordt beschreven hoe klanten de nieuwe Azure Monitor in verschillende scenario's kunnen gebruiken. In [de sectie Een nieuwe Azure Monitor activeren wordt](#activate-a-new-azure-monitor-integration) beschreven hoe u de nieuwe Azure Monitor activeren en gebruiken. De [sectie Migreren van Azure Monitor Classic](#migrate-to-the-new-azure-monitor-integration) naar de nieuwe Azure Monitor Integration bevat aanvullende informatie voor gebruikers die afhankelijk zijn van de oude Azure Monitor integratie.

> [!NOTE]
> Alleen clusters die eind september 2020 en later zijn gemaakt, komen in aanmerking voor de nieuwe Azure Monitoring-integratie.

## <a name="activate-a-new-azure-monitor-integration"></a>Een nieuwe integratie Azure Monitor activeren 

> [!NOTE]
> U moet een Log Analytics-werkruimte hebben gemaakt in een abonnement waar u toegang toe hebt voordat u de nieuwe integratie inschakelen. Zie Create a Log Analytics workspace in the Azure Portal (Een Log Analytics-werkruimte maken in de Azure Portal) voor [meer informatie over het maken van een Log Analytics-werkruimte.](../azure-monitor/learn/quick-create-workspace.md)

Activeer de nieuwe integratie door naar de portalpagina van uw cluster te gaan en omlaag te schuiven in het menu aan de linkerkant totdat u de sectie **Bewaking** hebt bereikt. Selecteer in **de sectie** Bewaking de optie **Integratie bewaken.** Selecteer vervolgens **Inschakelen** en kies de Log Analytics-werkruimte waar uw logboeken naar moeten worden verzonden. Selecteer **Opslaan** nadat u uw werkruimte hebt gekozen. 

### <a name="access-the-new-tables"></a>Toegang tot de nieuwe tabellen

Er zijn twee manieren waarop u toegang hebt tot de nieuwe tabellen. 

### <a name="approach-1"></a>Benadering 1:
De eerste manier om toegang te krijgen tot de nieuwe tabellen is via de Log Analytics-werkruimte. 

1. Ga naar de Log Analytics-werkruimte die u hebt geselecteerd toen u de integratie hebt ingeschakeld. 
2. Schuif omlaag in het menu aan de linkerkant van het scherm en selecteer **Logboeken.** Er wordt een logboekquery-editor weergegeven met een lijst met alle tabellen in de werkruimte. 
3. Als de tabellen zijn gegroepeerd op **Oplossing**, staan de nieuwe HDI-tabellen in de **sectie Logboekbeheer.** 
4. Als u de tabellen groepeerd op **Resourcetype,** staan de tabellen onder de **sectie HDInsight-clusters,** zoals wordt weergegeven in de onderstaande afbeelding. 

> [!NOTE]
> In dit proces wordt beschreven hoe de logboeken zijn gebruikt in de oude integratie. Hiervoor moet de gebruiker toegang hebben tot de werkruimte.

### <a name="approach-2"></a>Benadering 2:

De tweede manier om toegang te krijgen tot de nieuwe tabellen is via toegang tot de clusterportal.
 
1. Navigeer naar de portalpagina van uw cluster en schuif omlaag in het menu aan de linkerkant totdat u de sectie **Bewaking** ziet. In deze sectie ziet u het deelvenster **Logboeken.** 
2. Selecteer **Logboeken** en er wordt een logboekquery-editor weergegeven. De editor bevat alle logboeken die zijn gekoppeld aan de clusterresource. U hebt de logboeken naar de Log Analytics-werkruimte verzonden toen u integratie inschakelen. Deze logboeken bieden op resources gebaseerde toegang (RBAC). Met RBAC kunnen gebruikers die wel toegang hebben tot het cluster, maar niet tot de werkruimte, de logboeken zien die aan het cluster zijn gekoppeld.

Ter vergelijking: de volgende schermafbeeldingen tonen de weergave van de verouderde integratiewerkruimte en de nieuwe integratiewerkruimteweergave:

**Weergave verouderde integratiewerkruimte**

  :::image type="content" source="./media/log-analytics-migration/legacy-integration-workspace-view.png"  lightbox="./media/log-analytics-migration/legacy-integration-workspace-view.png" alt-text="Schermopname van de weergave van de verouderde integratiewerkruimte." border="false":::

**Weergave nieuwe integratiewerkruimte**

  :::image type="content" source="./media/log-analytics-migration/new-integration-workspace-view.png" lightbox="./media/log-analytics-migration/new-integration-workspace-view.png" alt-text="Schermopname van de weergave van de nieuwe integratiewerkruimte." border="false":::

### <a name="use-the-new-tables"></a>De nieuwe tabellen gebruiken

Deze integraties kunnen u helpen bij het gebruik van de nieuwe tabellen:

#### <a name="default-queries-to-use-with-new-tables"></a>Standaardquery's voor gebruik met nieuwe tabellen

Stel in de Query-editor voor logboeken de schakelknop in op **Query's** boven de lijst met tabellen. Zorg ervoor dat u de query's groepeert op **resourcetype** en dat er geen filter is ingesteld voor een ander resourcetype dan **HDInsight-clusters.** In de volgende afbeelding ziet u hoe de resultaten eruit zien wanneer ze zijn gegroepeerd op **Resourcetype** en gefilterd op **HDInsight-clusters.** Selecteer er een en deze wordt weergegeven in de queryeditor Logboeken. Zorg ervoor dat u de opmerkingen in de query's leest, aangezien sommige vereisen dat u bepaalde informatie, zoals de naam van uw cluster, moet invoeren om de query met succes uit te voeren.

:::image type="content" source="./media/log-analytics-migration/default-query-results-grouped-resource-type.png" alt-text="Schermopname van het gegroepeerde resourcetype standaardqueryresultaten." border="true":::


#### <a name="create-your-own-queries"></a>Uw eigen query's maken

U kunt uw eigen query's invoeren in de queryeditor Logboeken. Query's die worden gebruikt voor de oude tabellen zijn niet geldig voor de nieuwe tabellen, omdat veel van de nieuwe tabellen nieuwe, verfijnde schema's hebben. De standaardquery's zijn uitstekende verwijzingen voor het vormgeven van query's op de nieuwe tabellen.

#### <a name="insights"></a>Inzichten

Inzichten zijn clusterspecifieke visualisatiedashboards die zijn gemaakt [met behulp van Azure Workbooks.](../azure-monitor/platform/workbooks-overview.md) Deze dashboards bieden u gedetailleerde grafieken en visualisaties van hoe uw cluster wordt uitgevoerd. De dashboards hebben secties voor elk clustertype, YARN, metrische gegevens van het systeem en onderdeellogboeken. U kunt het dashboard van uw cluster openen door naar de pagina  van uw cluster in de portal te gaan, omlaag te schuiven naar de sectie Bewaking en het deelvenster **Inzichten te** selecteren. Het dashboard wordt automatisch geladen als u de nieuwe integratie hebt ingeschakeld. Het duurt enkele seconden voordat de grafieken worden geladen wanneer ze een query uitvoeren op de logboeken.

:::image type="content" source="./media/log-analytics-migration/visualization-dashboard.png" lightbox="./media/log-analytics-migration/visualization-dashboard.png" alt-text="Schermopname van het visualisatiedashboard.":::

#### <a name="custom-azure-workbooks"></a>Aangepaste Azure-werkmappen

U kunt uw eigen Azure-werkmappen maken met aangepaste grafieken en visualisaties. Schuif op de portalpagina van uw  cluster omlaag naar de sectie Bewaking en selecteer het deelvenster **Werkmappen** in het menu aan de linkerkant. U kunt beginnen met het gebruik van een lege sjabloon of een van de sjablonen in de sectie **HDInsight-clusters** gebruiken. Er is een sjabloon voor elk clustertype. Sjablonen zijn handig als u specifieke aanpassingen wilt opslaan die de standaard-HDInsight Insights niet bieden. U kunt aanvragen voor nieuwe functies in HDInsight Insights verzenden als u denkt dat er iets ontbreekt.

#### <a name="at-scale-workbooks-for-new-azure-monitor-integrations"></a>Werkmappen op schaal voor nieuwe Azure Monitor integraties

Gebruik onze nieuwe werkmap op schaal om een bewakingservaring met meerdere clusters voor uw clusters te krijgen. In onze werkmap op schaal ziet u voor welke clusters de bewakingspijplijn is ingeschakeld. De werkmap biedt u ook een eenvoudige manier om de status van meerdere clusters tegelijk te controleren. Deze werkmap weergeven:

1. Ga naar **de Azure Monitor pagina** in vanaf Azure Portal startpagina
2. Selecteer op **de Azure Monitor** **Insights Hub** in de **sectie** Inzichten.
3. Selecteer **HDInsight-clusters** in de **sectie** Analyse.

   :::image type="content" source="./media/log-analytics-migration/at-scale-workbook.png" lightbox="./media/log-analytics-migration/at-scale-workbook.png" alt-text="Schermopname van de werkmap op schaal." border="false":::

#### <a name="alerts"></a>Waarschuwingen

U kunt aangepaste waarschuwingen toevoegen aan uw clusters en werkruimten in de Logboekquery-editor. Ga naar de queryeditor Logboeken door het deelvenster **Logboeken** te selecteren in uw cluster- of werkruimteportal. Voer een query uit en selecteer vervolgens **Nieuwe waarschuwingsregel,** zoals wordt weergegeven in de volgende schermopname. Lees over het configureren [van waarschuwingen voor meer informatie.](../azure-monitor/platform/alerts-log.md)

:::image type="content" source="./media/log-analytics-migration/new-rule-alert.png" alt-text="Schermopname van de nieuwe regelwaarschuwing." border="false":::

## <a name="migrate-to-the-new-azure-monitor-integration"></a>Migreren naar de nieuwe Azure Monitor Integration

Als u de integratie van de klassieke Azure Monitor gebruikt, moet u enkele aanpassingen aanbrengen in de nieuwe tabelindelingen nadat u bent overschakelt naar de Azure Monitor integratie.

Als u de integratie van Azure Monitor wilt inschakelen, volgt u de stappen die worden beschreven in de sectie [Een nieuwe Azure Monitor activeren.](#activate-a-new-azure-monitor-integration)

### <a name="run-queries-in-log-analytics"></a>Query's uitvoeren in Log Analytics

Omdat de nieuwe tabelindeling verschilt van de vorige, moeten uw query's worden bijwerkt zodat u onze nieuwe tabellen kunt gebruiken. Zodra u de nieuwe Azure Monitor inschakelen, kunt u door de tabellen en schema's bladeren om de velden te identificeren die in uw oude query's worden gebruikt.

We bieden een [toewijzingstabel](#appendix-table-mapping) tussen de oude tabel en de nieuwe tabel, zodat u snel de nieuwe velden kunt vinden die u nodig hebt om uw dashboards en query's te migreren.

**Standaardquery's:** we hebben standaardquery's gemaakt die laten zien hoe u de nieuwe tabellen kunt gebruiken voor veelvoorkomende situaties. De standaardquery's laten ook zien welke informatie beschikbaar is in elke tabel. U kunt toegang krijgen tot de standaardquery's door de instructies te volgen in de sectie [Standaardquery's](#default-queries-to-use-with-new-tables) voor gebruik met nieuwe tabellen in dit artikel.

### <a name="update-dashboards-for-hdinsight-clusters"></a>Dashboards voor HDInsight-clusters bijwerken

Als u meerdere dashboards hebt gemaakt om uw HDInsight-clusters te bewaken, moet u de query achter de tabel aanpassen zodra u de nieuwe Azure Monitor inschakelen. De tabelnaam of de veldnaam kan in de nieuwe integratie worden gewijzigd, maar alle informatie die u in de oude integratie hebt, is opgenomen.

Raadpleeg de [toewijzingstabel tussen](#appendix-table-mapping) de oude tabel/het oude schema naar de nieuwe tabel/het nieuwe schema om de query achter de dashboards bij te werken.

#### <a name="out-of-box-dashboards"></a>Out-of-box-dashboards 

We hebben ook de out-of-box-dashboards verbeterd op clusterniveau. Er is een knop rechtsboven in elke grafiek waarmee u de onderliggende query kunt zien die de informatie produceert. De grafiek is een uitstekende manier om vertrouwd te raken met de manier waarop de nieuwe tabellen effectief kunnen worden opgevraagd. U kunt toegang krijgen tot de out-of-box-dashboards door de instructies te volgen die u vindt in de [secties](#insights) Inzichten en Werkmappen op schaal voor nieuwe Azure Monitor-integraties. [](#at-scale-workbooks-for-new-azure-monitor-integrations)

### <a name="use-an-hdinsight-at-scale-monitoring-dashboard"></a>Een HDInsight-bewakingsdashboard op schaal gebruiken

Als u het out-of-box bewakingsdashboard gebruikt voor HDInsight-clusters zoals HDInsight Spark Monitoring en HDInsight Interactive Monitoring, werken we aan dezelfde mogelijkheden in de Azure Monitor-portal.

U ziet dat er een optie voor HDInsight-clusters in de Azure Monitor.

   :::image type="content" source="./media/log-analytics-migration/hdinsight-azure-monitor.png" lightbox="./media/log-analytics-migration/hdinsight-azure-monitor.png" alt-text="Schermopname met de optie HDInsight in Azure Monitor." border="false":::

De Azure Monitor insights hub van de portal biedt u de mogelijkheid om meerdere HDInsight-clusters op één plek te bewaken. We organiseren de clusters op basis van het type workload, zodat u typen zoals Spark, HBase en Hive ziet. In plaats van naar meerdere dashboards te gaan, kunt u nu al uw HDInsight-clusters in deze weergave bewaken.

> [!NOTE]
> Zie de [secties Inzichten](#insights) en Werkmappen op schaal voor nieuwe Azure Monitor [integraties](#at-scale-workbooks-for-new-azure-monitor-integrations) in dit artikel voor meer informatie.

## <a name="enable-both-integrations-to-accelerate-the-migration"></a>Schakel beide integraties in om de migratie te versnellen

U kunt zowel de klassieke als de nieuwe Azure Monitor-integraties op hetzelfde moment activeren op een cluster dat in aanmerking komt voor beide integraties om snel naar de nieuwe Azure Monitor migreren. De nieuwe integratie is beschikbaar voor alle clusters die zijn gemaakt na half september 2020.

Op deze manier kunt u eenvoudig een vergelijking naast elkaar uitvoeren voor de query's die u gebruikt.

### <a name="enabling-the-classic-integration"></a>De klassieke integratie inschakelen

Als u een cluster gebruikt dat is gemaakt na half september 2020, ziet u de nieuwe portalervaring in de portal van uw cluster. Als u de nieuwe pijplijn wilt inschakelen, kunt u de stappen volgen die worden beschreven in de sectie Een nieuwe Azure Monitor [activeren.](#activate-a-new-azure-monitor-integration) Als u de klassieke integratie op dit cluster wilt activeren, gaat u naar de portalpagina van uw cluster. Selecteer het **deelvenster Integratie** bewaken in de **sectie Bewaking** van het menu aan de linkerkant van de pagina van de clusterportal. Selecteer **Configure Azure Monitor for HDInsight clusters integration (classic)**. Er wordt een context aan de zijkant weergegeven met een schakelknop die u kunt gebruiken om de klassieke Azure Monitoring-integratie in en uit te schakelen. 

> [!NOTE]
> U ziet geen logboeken of metrische gegevens van de klassieke integratie via de logboeken en inzichtenpagina van uw clusterportal. Alleen de nieuwe integratielogboeken en metrische gegevens zijn aanwezig op deze locaties.

   :::image type="content" source="./media/log-analytics-migration/hdinsight-classic-integration.png" alt-text="Schermopname van de koppeling voor toegang tot de klassieke integratie." border="false":::

Het maken van nieuwe clusters met Azure Monitor-integratie is niet beschikbaar na 1 juli 2021.

## <a name="release-and-support-timeline"></a>Tijdlijn voor release en ondersteuning

- Klassieke Azure Monitoring-integratie is na 15 oktober 2021 niet meer beschikbaar. U kunt de klassieke Azure Monitoring-integratie na die datum niet meer inschakelen.
- Bestaande klassieke Azure-bewakingsintegraties blijven werken. Er is beperkte ondersteuning voor de klassieke Azure Monitoring-integratie. 
  - Problemen worden onderzocht zodra klanten het ondersteuningsticket indienen.
  - Als voor de oplossing een wijziging van de afbeelding is vereist, moeten klanten over naar de nieuwe integratie.
  - De klassieke Azure Monitoring-integratieclusters worden niet gepatcht, met uitzondering van kritieke beveiligingsproblemen.

## <a name="appendix-table-mapping"></a>Bijlage: Tabeltoewijzing

De volgende grafieken tonen de tabeltoewijzingen van de klassieke Azure Monitoring Integration naar onze nieuwe. In **de kolom Workload** wordt beschreven aan welke workload elke tabel is gekoppeld. In **de rij Nieuwe** tabel ziet u de naam van de nieuwe tabel. In **de rij** Beschrijving wordt het type logboeken/metrische gegevens beschreven dat beschikbaar is in deze tabel. De **rij Oude** tabel is een lijst met alle tabellen uit de klassieke Azure Monitor-integratie waarvan de gegevens nu aanwezig zijn in de tabel die wordt vermeld in de rij **Nieuwe** tabel.

> [!NOTE]
> Sommige tabellen zijn nieuw en zijn niet gebaseerd op oude tabellen.

## <a name="general-workload-tables"></a>Algemene workloadtabellen

| Nieuwe tabel | Details |
| --- | --- |
| HDInsightAmbariSystemMetrics | <ul><li>**Beschrijving:** deze tabel bevat metrische systeemgegevens die zijn verzameld uit Ambari. De metrische gegevens zijn nu afkomstig van elk knooppunt in het cluster (met uitzondering van edge-knooppunten) in plaats van alleen de twee hoofdknooppunten. Elke metrische gegevens is nu een kolom en elke metrische gegevens worden eenmaal per record gerapporteerd.</li><li>**Oude tabel:** metrische gegevens cpu mooie CL, metrische CPU-systeem cl, metrische gegevens cpu cl, metrische geheugencache CL, metrische geheugen wisselen CL, metrische geheugen totaal \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ CLmetrics \_ \_ geheugenbuffer \_ CL, metrische belasting \_ \_ 1min \_ CL, \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ metrische belasting CPU CL, metrische load knooppunten CL, metrische belasting procs CL, metrische netwerk in CL, metrische gegevens netwerk</li></ul>|
| HDInsightAmbariClusterAlerts | <ul><li>**Beschrijving:** deze tabel bevat waarschuwingen voor Ambari-clusters van elk knooppunt in het cluster (met uitzondering van edge-knooppunten). Elke waarschuwing is een record in deze tabel.</li><li>**Oude tabel:** \_ clusterwaarschuwingen \_ voor metrische \_ gegevens CL</li></ul>|
| HDInsightSecurityLogs | <ul><li>**Beschrijving:** deze tabel bevat records uit de audit- en auth-logboeken van Ambari.</li><li>**Oude tabel:** log \_ ambari \_ audit \_ CL, log \_ auth \_ CL</li></ul>|
| HDInsightRangerAuditLogs | <ul><li>**Beschrijving:** deze tabel bevat alle records uit het Auditlogboek van Ranger voor ESP-clusters.</li><li>**Oude tabel:** \_ ranger-auditlogboeken \_ \_ CL</li></ul>|
| HDInsightGatewayAuditLogs \_ CL | <ul><li>**Beschrijving:** deze tabel bevat de controlegegevens van gatewayknooppunten. Dit is dezelfde indeling als de tabel in de kolom Oude tabellen. **Deze bevindt zich nog steeds in de sectie Aangepaste logboeken.**</li><li>**Oude tabel:** \_ logboekgateway CL \_ \_ controleren</li></ul>|

## <a name="spark-workload"></a>Spark-workload

> [!NOTE]
> Aan Spark-toepassingen gerelateerde tabellen zijn vervangen door 11 nieuwe Spark-tabellen (beginnend met HDInsightSpark*) die meer uitgebreide informatie geven over uw Spark-workloads.


| Nieuwe tabel | Details |
| --- | --- |
| HDInsightSparkLogs | <ul><li>**Beschrijving:** deze tabel bevat alle logboeken die betrekking hebben op Spark en het bijbehorende onderdeel: Livy en Jupyter.</li><li>**Oude tabel:** log \_ livy, \_ CL, log \_ jupyter \_ CL, log \_ spark \_ CL, log \_ sparkappsexecutors \_ CL, log \_ sparkappsdrivers \_ CL</li></ul>|
| HDInsightSparkApplicationEvents | <ul><li>**Beschrijving:** deze tabel bevat informatie over gebeurtenissen voor Spark-toepassingen, waaronder Indienings- en voltooiingstijd, App-id en AppName. Het is handig om bij te houden wanneer toepassingen zijn gestart en voltooid. </li></ul>|
| HDInsightSparkBlockManagerEvents | <ul><li>**Beschrijving:** deze tabel bevat informatie over gebeurtenissen met betrekking tot Blokbeheer van Spark. Het bevat informatie zoals geheugengebruik van uitvoerders.</li></ul>|
| HDInsightSparkEnvironmentEvents | <ul><li>**Beschrijving:** deze tabel bevat informatie over gebeurtenissen met betrekking tot de omgeving waarin een toepassing wordt uitgevoerd, waaronder Spark Deploy Mode, Master en informatie over de uitvoerder.</li></ul>|
| HDInsightSparkExecutorEvents | <ul><li>**Beschrijving:** deze tabel bevat gebeurtenisinformatie over het gebruik van de Spark-uitvoerder voor door een toepassing.</li></ul>|
| HDInsightSparkExtraEvents | <ul><li>**Beschrijving:** deze tabel bevat gebeurtenisgegevens die niet in een andere Spark-tabel passen. </li></ul>|
| HDInsightSparkJobEvents | <ul><li>**Beschrijving:** deze tabel bevat informatie over Spark-taken, waaronder de begin- en eindtijden, het resultaat en de bijbehorende fasen.</li></ul>|
| HDInsightSparkSqlExecutionEvents | <ul><li>**Beschrijving:** deze tabel bevat gebeurtenisinformatie over Spark SQL-query's, inclusief de plangegevens en beschrijving en begin- en eindtijden.</li></ul>|
| HDInsightSparkStageEvents | <ul><li>**Beschrijving:** deze tabel bevat informatie over gebeurtenissen voor Spark-fasen, waaronder de begin- en voltooiingstijden, de foutstatus en gedetailleerde uitvoeringsinformatie.</li></ul>|
| HDInsightSparkStageTaskAccumulables | <ul><li>**Beschrijving:** deze tabel bevat metrische prestatiegegevens voor fasen en taken.</li></ul>|
| HDInsightTaskEvents | <ul><li>**Beschrijving:** deze tabel bevat informatie over gebeurtenissen voor Spark-taken, waaronder start- en voltooiingstijd, gekoppelde fasen, uitvoeringsstatus en taaktype.</li></ul>|
| HDInsightJupyterNotebookEvents | <ul><li>**Beschrijving:** deze tabel bevat informatie over gebeurtenissen voor Jupyter Notebooks.</li></ul>|

## <a name="hadoopyarn-workload"></a>Hadoop/YARN-workload

| Nieuwe tabel | Details |
| --- | --- |
| HDInsightHadoopAndYarnMetrics | <ul><li>**Beschrijving:** deze tabel bevat metrische JMX-gegevens uit de Hadoop- en YARN-frameworks. Het bevat dezelfde JMX-metrische gegevens als de oude tabellen met aangepaste logboeken, plus meer metrische gegevens die we belangrijk achten. We hebben metrische gegevens voor Tijdlijnserver, Knooppuntbeheer en Taakgeschiedenisserver toegevoegd. Het bevat één metrische gegevens per record.</li><li>**Oude tabel:** metrische \_ resourcemanager \_ clustermetrics \_ CL, metrics resourcemanager jvm CL, metrics resourcemanager queue root \_ \_ \_ \_ \_ \_ \_ CL, metrics resourcemanager queue root \_ \_ \_ \_ joblauncher \_ CL, metrics \_ resourcemanager \_ queue root \_ default \_ \_ CL, metrics \_ resourcemanager queue root \_ \_ \_ thriftsvr \_ CL</li></ul>|
| HDInsightHadoopAndYarnLogs | <ul><li>**Beschrijving:** deze tabel bevat alle logboeken die zijn gegenereerd op basis van de Hadoop- en YARN-frameworks.</li><li>**Oude tabel:** log \_ mrjobsummary \_ CL, log \_ resourcemanager \_ CL, log \_ timelineserver \_ CL, log \_ nodemanager \_ CL</li></ul>|

 
## <a name="hivellap-workload"></a>Hive-/LLAP-workload 

| Nieuwe tabel | Details |
| --- | --- |
| HDInsightHiveAndLLAPMetrics | <ul><li>**Beschrijving:** deze tabel bevat metrische JMX-gegevens uit de Hive- en LLAP-frameworks. Het bevat dezelfde metrische JMX-gegevens als de oude aangepaste logboekentabellen. Het bevat één metrische gegevens per record.</li><li>**Oude tabel:** llap metrics \_ \_ hiveserver2 \_ CL, llap \_ metrics \_ hs2 \_ metrics \_ subsystemllap \_ metrics \_ jvm \_ CL, llap \_ metrics \_ llap \_ daemon \_ info CL, llap metrics \_ \_ \_ \_ \_ allocator info \_ CL, llap \_ metrics \_ deamon \_ jvm \_ CL, llap \_ metrics \_ io \_ CL, llap \_ metrics \_ executor metrics \_ \_ CL, llap \_ metrics \_ \_ metricssystem stats \_ CL, llap \_ metrics cache \_ \_ CL</li></ul>|
| HDInsightHiveAndLLAPLogs | <ul><li>**Beschrijving:** deze tabel bevat logboeken die zijn gegenereerd op basis van Hive, LLAP en de bijbehorende onderdelen: WebHCat en Zeppelin.</li><li>**Oude tabel**: log \_ hivemetastore \_ CL log \_ hiveserver2 \_ CL, log \_ hiveserve2interactive \_ CL, log \_ webhcat \_ CL, log zeppelin zeppelin \_ \_ \_ CL</li></ul>|


## <a name="kafka-workload"></a>Kafka-workload

| Nieuwe tabel | Details |
| --- | --- |
| HDInsightKafkaMetrics | <ul><li>**Beschrijving:** deze tabel bevat metrische JMX-gegevens van Kafka. Het bevat dezelfde metrische JMX-gegevens als de oude aangepaste logboeken, plus meer metrische gegevens die we belangrijk achten. Het bevat één metrische gegevens per record.</li><li>**Oude tabel:** metrische \_ gegevens van Kafka \_ CL</li></ul>|
| HDInsightKafkaLogs | <ul><li>**Beschrijving:** deze tabel bevat alle logboeken die zijn gegenereerd op basis van de Kafka-brokers.</li><li>**Oude tabel:** log \_ kafkaserver \_ CL, log \_ kafkacontroller \_ CL</li></ul>|

## <a name="hbase-workload"></a>HBase-workload

| Nieuwe tabel | Details |
| --- | --- |
| HDInsightHBaseMetrics | <ul><li>**Beschrijving:** deze tabel bevat metrische JMX-gegevens van HBase. Het bevat dezelfde metrische JMX-gegevens uit de tabellen die worden vermeld in de kolom Oud schema. In tegenstelling tot de oude tabellen bevat elke rij één metrische gegevens.</li><li>**Oude** tabel: metrics \_ regionserver CL, metrics \_ \_ regionserver \_ wal \_ CL, metrics \_ regionserver \_ ipc \_ CL, metrics \_ regionserver os \_ \_ CL, metrics \_ regionserver \_ replication \_ CL, metrics \_ restserver \_ CL, metrics \_ restserver \_ \_ jvm CL, metrics \_ hmaster \_ assignmentmanager \_ CL, metrics \_ hmaster \_ ipc \_ CL, metrics \_ hmaser \_ os \_ CL, metrics \_ hmaster \_ balancer \_ CL, metrics \_ hmaster \_ jvm \_ CL, metrics \_ hmaster \_ CL,metrics \_ hmaster \_ fs \_ CL</li></ul>|
| HDInsightHBaseLogs | <ul><li>**Beschrijving:** deze tabel bevat logboeken van HBase en de bijbehorende onderdelen: Phoenix en HDFS.</li><li>**Oude tabel:** log \_ regionserver \_ CL, log \_ restserver \_ CL, log \_ phoenixserver \_ CL, log \_ hmaster \_ CL, log \_ hdfsnamenode \_ CL, log \_ garbage collector \_ \_ CL</li></ul>|

## <a name="storm-workload"></a>Storm-workload

| Nieuwe tabel | Details |
| --- | --- |
| HDInsightStormMetrics | <ul><li>**Beschrijving:** deze tabel bevat dezelfde metrische JMX-gegevens als de tabellen in de sectie Oude tabellen. De rijen bevatten één metrische gegevens per record.</li><li>**Oude tabel:** metrische \_ gegevens stormnimbus \_ CL, metrische \_ stormsupervisor \_ CL</li></ul>|
| HDInsightStormTopologyMetrics | <ul><li>**Beschrijving:** deze tabel bevat metrische gegevens op topologieniveau van Storm. Deze heeft dezelfde vorm als de tabel die wordt vermeld in de sectie Oude tabellen.</li><li>**Oude tabel:** metrische \_ gegevens stormrest \_ CL</li></ul>|
| HDInsightStormLogs | <ul><li>**Beschrijving:** deze tabel bevat alle logboeken die zijn gegenereerd op basis van Storm.</li><li>**Oude tabel:** log \_ supervisor \_ CL, log \_ nimbus \_ CL</li></ul>|

## <a name="oozie-workload"></a>Oozie-workload

| Nieuwe tabel | Details |
| --- | --- |
| HDInsightOozieLogs | <ul><li>**Beschrijving:** deze tabel bevat alle logboeken die zijn gegenereerd op basis van het Oozie-framework.</li><li>**Oude tabel:** Log \_ oozie \_ CL</li></ul>|

## <a name="next-steps"></a>Volgende stappen
[Query'Azure Monitor voor het bewaken van HDInsight-clusters](hdinsight-hadoop-oms-log-analytics-use-queries.md)
