---
title: Azure Opslaganalyse metrieken (klassiek) inschakelen en beheren | Microsoft Docs
description: Meer informatie over het inschakelen, bewerken en weer geven van Azure Opslaganalyse metrische gegevens.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 784929e50d25a07ae92cf388be5ac14f6fa820a6
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221572"
---
# <a name="enable-and-manage-azure-storage-analytics-metrics-classic"></a>Azure Opslaganalyse metrische gegevens inschakelen en beheren (klassiek)

[Azure Opslaganalyse](storage-analytics.md) biedt metrische gegevens voor alle opslag Services voor blobs, wacht rijen en tabellen. U kunt de [Azure Portal](https://portal.azure.com) gebruiken om te configureren welke metrische gegevens voor uw account worden vastgelegd en grafieken te configureren die visuele voors tellingen van uw metriekgegevens bieden. In dit artikel wordt beschreven hoe u metrische gegevens inschakelt en beheert. Zie [Azure Opslaganalyse logboeken inschakelen en beheren (klassiek)](manage-storage-analytics-logs.md)voor meer informatie over het inschakelen van Logboeken.

U wordt aangeraden [Azure monitor voor opslag](../../azure-monitor/insights/storage-insights-overview.md) te bekijken (preview). Het is een functie van Azure Monitor die uitgebreide controle biedt over uw Azure Storage-accounts door een uniforme weer gave te bieden van de prestaties, capaciteit en beschik baarheid van uw Azure Storage services. U hoeft niets in te scha kelen of te configureren, en u kunt deze metrische gegevens direct weer geven vanuit de vooraf gedefinieerde interactieve diagrammen en andere visualisaties die zijn opgenomen.

> [!NOTE]
> Er zijn kosten verbonden aan het onderzoeken van bewakings gegevens in de Azure Portal. Zie [Opslaganalyse](storage-analytics.md) voor meer informatie.
>
> Opslag accounts voor een blok-BLOB voor Premium-prestaties bieden geen ondersteuning voor Opslaganalyse metrische gegevens. Als u metrische gegevens wilt weer geven met Premium-prestatie blok-Blob Storage-accounts, kunt u overwegen [Azure Storage metrische gegevens in azure monitor](../blobs/monitor-blob-storage.md)te gebruiken.
>
> Zie [Microsoft Azure Storage controleren, diagnosticeren en problemen oplossen](storage-monitoring-diagnosing-troubleshooting.md)voor meer informatie over het gebruik van Opslaganalyse en andere hulpprogram ma's voor het identificeren, vaststellen en oplossen van problemen met Azure Storage.
>

<a id="Enable-metrics"></a>

## <a name="enable-metrics"></a>Metrische gegevens inschakelen

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer in de [Azure Portal](https://portal.azure.com) **opslag accounts** en vervolgens de naam van het opslag account om het account dashboard te openen.

2. Selecteer **Diagnostische instellingen (klassiek)** in het gedeelte **bewaking (klassiek)** van de menu-Blade.
   
   ![Scherm opname van de optie Diagnostische instellingen (klassiek) in de sectie bewaking (klassiek).](./media/manage-storage-analytics-metrics/storage-enable-metrics-00.png)

3. Selecteer het **type** metrische gegevens voor elke **service** die u wilt bewaken en het **Bewaar beleid** voor de gegevens. U kunt de bewaking ook uitschakelen door de **status** in te stellen op **uit**.

   > [!div class="mx-imgBorder"]
   > ![Configureer logboek registratie in de Azure Portal.](./media/manage-storage-analytics-logs/enable-diagnostics.png) 

   Als u het Bewaar beleid voor gegevens wilt instellen, verschuift u de schuif regelaar **bewaren (dagen)** of voert u het aantal dagen aan gegevens in dat u wilt behouden, van 1 tot 365. De standaard waarde voor nieuwe opslag accounts is zeven dagen. Als u geen Bewaar beleid wilt instellen, voert u nul in. Als er geen Bewaar beleid is, kunt u de bewakings gegevens verwijderen.

   > [!WARNING]
   > Metics worden opgeslagen als gegevens in uw account. Metrische gegevens kunnen gedurende een periode in uw account worden verzameld, waardoor de opslag kosten kunnen worden verhoogd. Als u metrische gegevens slechts gedurende een korte periode nodig hebt, kunt u uw kosten verlagen door het Bewaar beleid voor gegevens te wijzigen. Verouderde metrische gegevens (gegevens die ouder zijn dan uw Bewaar beleid) worden verwijderd door het systeem. We raden u aan een Bewaar beleid in te stellen op basis van hoe lang u de metrische gegevens voor uw account wilt behouden. Zie [facturering op metrische](storage-analytics-metrics.md#billing-on-storage-metrics) gegevens van de opslag voor meer informatie.
   >

4. Wanneer u de bewakings configuratie hebt voltooid, selecteert u **Opslaan**.

Er wordt een standaardset metrische gegevens weer gegeven in grafieken op de Blade **overzicht** en op de Blade **metrische gegevens (klassiek)** . Zodra u de metrische gegevens voor een service hebt ingeschakeld, kan het tot een uur duren voordat het bestand in de grafieken wordt weer gegeven. U kunt **bewerken** selecteren in een metrische grafiek om te configureren welke metrische gegevens worden weer gegeven in de grafiek.

U kunt metrische gegevens verzamelen en logboek registratie uitschakelen door de **status** in te stellen op **uit**.

> [!NOTE]
> Azure Storage gebruikt [tabel opslag](storage-introduction.md#table-storage) om de metrische gegevens voor uw opslag account op te slaan, en slaat de metrische gegevens op in tabellen in uw account. Zie voor meer informatie [Hoe metrische gegevens worden opgeslagen](storage-analytics-metrics.md#how-metrics-are-stored).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Open een Windows Power shell-opdracht venster.

2. Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzAccount` en volg de instructies op het scherm.

   ```powershell
   Connect-AzAccount
   ```

3. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Vervang de `<subscription-id>` waarde van de tijdelijke aanduiding door de id van uw abonnement.

5. Haal de context van het opslag account op waarmee het opslag account wordt gedefinieerd dat u wilt gebruiken.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Vervang de `<resource-group-name>` waarde van de tijdelijke aanduiding door de naam van uw resource groep.

   * Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount. 

6. U kunt Power shell op uw lokale machine gebruiken om metrische opslag gegevens in uw opslag account te configureren. Gebruik de Azure PowerShell cmdlet **set-AzStorageServiceMetricsProperty** om de huidige instellingen te wijzigen. 

   Met de volgende opdracht schakelt u over de metrische gegevens voor de BLOB-service in uw opslag account waarbij de Bewaar periode is ingesteld op vijf dagen.

   ```powershell
   Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $ctx
   ```   

   Voor deze cmdlet worden de volgende para meters gebruikt:  

   - **Service** type: mogelijke waarden zijn **BLOB**, **wachtrij**, **tabel** en **bestand**.
   - **MetricsType**: mogelijke waarden zijn **uur** en **minuut**.  
   - **MetricsLevel**: mogelijke waarden zijn:
      - **Geen**: schakelt bewaking uit.
      - **Service**: verzamelt metrische gegevens, zoals ingangs-en uitkomend verkeer, Beschik baarheid, latentie en succes percentages, die worden geaggregeerd voor de blob-, wachtrij-, tabel-en bestands Services.
      - **ServiceAndApi**: als aanvulling op de metrische gegevens van de service, verzamelt dezelfde verzameling metrische gegevens voor elke opslag bewerking in de API van de Azure Storage-service.

   Met de volgende opdracht worden het huidige metrische gegevens niveau en de retentie dagen voor de BLOB-service in uw standaard-opslag account opgehaald:  

   ```powershell
   Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
   ```  

   Zie [Azure PowerShell installeren en configureren](/powershell/azure/)voor informatie over het configureren van de Azure PowerShell-cmdlets voor het werken met uw Azure-abonnement en het selecteren van het standaard opslag account dat moet worden gebruikt.  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

Zie [Azure Storage-client bibliotheken voor .net](/dotnet/api/overview/azure/storage)voor meer informatie over het gebruik van een .net-taal voor het configureren van metrische gegevens voor opslag.  

Zie [Opslaganalyse inschakelen en configureren](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)voor algemene informatie over het configureren van metrische gegevens voor opslag met behulp van de rest API. 

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Zie [Azure Storage-client bibliotheken voor .net](/dotnet/api/overview/azure/storage)voor meer informatie over het gebruik van een .net-taal voor het configureren van metrische gegevens voor opslag.  

Zie [Opslaganalyse inschakelen en configureren](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)voor algemene informatie over het configureren van metrische gegevens voor opslag met behulp van de rest API. 

---

<a id="view-metrics"></a>

## <a name="view-metrics-in-a-chart"></a>Metrische gegevens weer geven in een grafiek

Nadat u Opslaganalyse metrische gegevens hebt geconfigureerd om uw opslag account te bewaken, Opslaganalyse registreert u de metrische gegevens in een set bekende tabellen in uw opslag account. U kunt grafieken configureren voor het weer geven van metrische gegevens per uur in de [Azure Portal](https://portal.azure.com).

Gebruik de volgende procedure om te kiezen welke metrische opslag gegevens in een grafiek met metrische gegevens moeten worden weer gegeven.

1. Begin met het weer geven van een metrische opslag grafiek in het Azure Portal. U kunt grafieken vinden op de **Blade opslag account** en op de Blade **metrische gegevens (klassiek)** .

   In dit voor beeld wordt het volgende diagram gebruikt dat wordt weer gegeven op de **Blade opslag account**:

   ![Grafiek selectie in Azure Portal](./media/manage-storage-analytics-metrics/stg-customize-chart-00.png)

2. Klik ergens in het diagram om de grafiek te bewerken.

3. Selecteer vervolgens het **tijds bereik** van de metrische gegevens die u wilt weer geven in de grafiek en de **service** (BLOB, wachtrij, tabel, bestand) waarvan u de metrische gegevens wilt weer geven. Hier worden de metrische gegevens van de afgelopen week geselecteerd om weer te geven voor de BLOB-service:

   ![Tijds bereik en service selectie in de Blade grafiek bewerken](./media/manage-storage-analytics-metrics/storage-edit-metric-time-range.png)

4. Selecteer de afzonderlijke **metrische gegevens** die u wilt weer geven in de grafiek en klik vervolgens op **OK**.

   ![Afzonderlijke metrieke selectie in Blade grafiek bewerken](./media/manage-storage-analytics-metrics/storage-edit-metric-selections.png)

De instellingen van de grafiek zijn niet van invloed op de verzameling, aggregatie of opslag van bewakings gegevens in het opslag account.

#### <a name="metrics-availability-in-charts"></a>Beschik baarheid van metrische gegevens in grafieken

De lijst met beschik bare metrische gegevens wordt gewijzigd op basis van de service die u hebt gekozen in de vervolg keuzelijst en het eenheids type van de grafiek die u wilt bewerken. U kunt bijvoorbeeld percentage metrische gegevens, zoals *PercentNetworkError* en *percentthrottlingerror aan* , alleen selecteren als u een grafiek bewerkt waarin eenheden worden weer gegeven in een percentage:

![Diagram voor fout percentage voor aanvraag in de Azure Portal](./media/manage-storage-analytics-metrics/stg-customize-chart-04.png)

#### <a name="metrics-resolution"></a>Resolutie van metrische gegevens

De metrische gegevens die u in **Diagnostische gegevens** hebt geselecteerd, bepalen de resolutie van de metrische gegevens die beschikbaar zijn voor uw account:

* **Aggregatie** bewaking biedt metrische gegevens, zoals inkomend/uitgaand verkeer, Beschik baarheid, latentie en succes percentages. Deze metrische gegevens worden geaggregeerd vanuit de blob-, tabel-, bestands-en wachtrij Services.
* **Per API** biedt een nauw keurige oplossing, met metrische gegevens die beschikbaar zijn voor afzonderlijke opslag bewerkingen, naast de aggregatie op service niveau.

## <a name="download-metrics-to-archive-or-analyze-locally"></a>Metrische gegevens downloaden om deze lokaal te archiveren of te analyseren

Als u de metrische gegevens wilt downloaden voor lange termijn opslag of als u ze lokaal wilt analyseren, moet u een hulp programma gebruiken of code schrijven om de tabellen te lezen. De tabellen worden niet weer gegeven als u alle tabellen in uw opslag account vermeld, maar u kunt ze rechtstreeks op naam openen. Veel hulp middelen voor opslag bladeren zijn op de hoogte van deze tabellen en u kunt ze rechtstreeks weer geven. Zie [Azure Storage-client hulpprogramma's](./storage-explorers.md)voor een lijst met beschik bare hulpprogram ma's.

|Metrische gegevens|Tabel namen|Notities| 
|-|-|-|  
|Metrische gegevens per uur|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|In versies voorafgaand aan 15 augustus 2013 zijn deze tabellen bekend als:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Metrische gegevens voor de bestands service zijn beschikbaar vanaf versie april 2015.|  
|Metrische gegevens over minuten|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Kan alleen worden ingeschakeld met Power shell of via een programma.<br /><br /> Metrische gegevens voor de bestands service zijn beschikbaar vanaf versie april 2015.|  
|Capaciteit|$MetricsCapacityBlob|Alleen Blob service.|  

Zie [Opslaganalyse-tabel schema metrische](/rest/api/storageservices/storage-analytics-metrics-table-schema)gegevens voor gedetailleerde informatie over de schema's voor deze tabellen. De volgende voorbeeld rijen tonen alleen een subset van de beschik bare kolommen, maar ze illustreren enkele belang rijke functies van de manier waarop metrische opslag gegevens deze metrische gegevens opslaan:  

|PartitionKey|RowKey|Timestamp|TotalRequests|TotalBillableRequests|TotalIngress|TotalEgress|Beschikbaarheid|AverageE2ELatency|AverageServerLatency|PercentSuccess| 
|-|-|-|-|-|-|-|-|-|-|-|  
|20140522T1100|gebruiker Hele|2014-05-22T11:01:16.7650250 Z|7|7|4003|46801|100|104,4286|6,857143|100|  
|20140522T1100|gebruiker QueryEntities|2014-05-22T11:01:16.7640250 Z|5|5|2694|45951|100|143,8|7,8|100|  
|20140522T1100|gebruiker QueryEntity|2014-05-22T11:01:16.7650250 Z|1|1|538|633|100|3|3|100|  
|20140522T1100|gebruiker UpdateEntity|2014-05-22T11:01:16.7650250 Z|1|1|771|217|100|9|6|100|  

In dit voor beeld van gegevens met een minuut waarde, gebruikt de partitie sleutel de tijd voor de omzetting van minuten. De rij sleutel geeft aan welk type informatie in de rij wordt opgeslagen. De informatie bestaat uit het toegangs type en het aanvraag type:  

-   Het toegangs type is een **gebruiker** of **systeem**, waarbij de **gebruiker** verwijst naar alle gebruikers aanvragen naar de opslag service en het **systeem** verwijst naar aanvragen van Opslaganalyse.  
-   Het aanvraag type is **alle**, in welk geval het een samenvattings regel is, of identificeert de specifieke API, zoals **QueryEntity** of **UpdateEntity**.  

In deze voorbeeld gegevens worden alle records voor een enkele minuut weer gegeven (vanaf 11: am), dus het aantal aanvragen voor **QueryEntities** plus het aantal **QueryEntity** -aanvragen plus het aantal **UpdateEntity** -aanvragen voegt Maxi maal zeven toe. Dit totaal wordt weer gegeven in de rij **gebruiker: alle** . Op dezelfde manier kunt u de gemiddelde end-to-end-latentie 104,4286 voor de **gebruiker afleiden: alle** rijen door te berekenen ((143,8 * 5) + 3 + 9)/7.  

## <a name="view-metrics-data-programmatically"></a>Metrische gegevens weer geven via een programma

De volgende vermelding toont voor beeld C#-code die de metrische gegevens van de minuut voor een bereik van minuten opent en de resultaten weergeeft in een console venster. In het code voorbeeld wordt gebruikgemaakt van de Azure Storage-client bibliotheek versie 4. x of hoger, die de klasse **CloudAnalyticsClient** bevat waarmee de toegang tot de metrische tabellen in de opslag wordt vereenvoudigd. 

> [!NOTE]
> De **CloudAnalyticsClient** -klasse is niet opgenomen in de Azure Blob Storage-client bibliotheek V12 voor .net. Op **31 augustus 2023** worden Opslaganalyse metrische gegevens, ook wel *klassieke metrische gegevens* genoemd, buiten gebruik gesteld. Zie de [officiële aankondiging](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/) voor meer informatie. Als u gebruikmaakt van klassieke metrische gegevens, raden we u aan om vóór die datum over te stappen op metrische gegevens in Azure Monitor. 

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

<a id="add-metrics-to-dashboard"></a>

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Metrische grafieken toevoegen aan het portal-dash board

U kunt Azure Storage metrische grafieken voor uw opslag accounts toevoegen aan uw portal-dash board.

1. Selecteer in het [Azure Portal](https://portal.azure.com)op **dash board bewerken** terwijl u uw dash board weergeeft.
1. Selecteer in de **Galerie tegels** de optie **tegels zoeken op**  >  **type**.
1. Selecteer   >  **opslag accounts** opgeven.
1. Selecteer in **resources** het opslag account waarvan u de metrische gegevens aan het dash board wilt toevoegen.
1. Selecteer **categorie**  >  **bewaking**.
1. Slepen en neerzetten van de grafiek tegel naar het dash board voor de metrische gegevens die u wilt weer geven. Herhaal deze stap voor alle metrische gegevens die u op het dash board wilt weer geven. In de volgende afbeelding is de grafiek ' blobs-totaal aantal aanvragen ' gemarkeerd als voor beeld, maar alle grafieken zijn beschikbaar voor plaatsing op uw dash board.

   ![Tegel galerie in Azure Portal](./media/manage-storage-analytics-metrics/storage-customize-dashboard.png)
1. Selecteer **klaar met aanpassen** aan de bovenkant van het dash board wanneer u klaar bent met het toevoegen van grafieken.

Zodra u grafieken aan het dash board hebt toegevoegd, kunt u ze verder aanpassen zoals beschreven in metrische grafieken aanpassen.

## <a name="next-steps"></a>Volgende stappen

* Zie [Opslaganalyse](storage-analytics.md) voor Opslaganalyse voor meer informatie over Opslaganalyse.
* [Opslaganalyse logboeken configureren](manage-storage-analytics-logs.md).
* Meer informatie over het schema metrische gegevens. Zie [Opslaganalyse het tabel schema metrische gegevens](/rest/api/storageservices/storage-analytics-metrics-table-schema).