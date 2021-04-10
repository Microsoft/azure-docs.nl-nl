---
title: Azure Opslaganalyse logboeken inschakelen en beheren (klassiek) | Microsoft Docs
description: Meer informatie over het bewaken van een opslag account in azure met behulp van Azure Opslaganalyse.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 0c182e1093c29206d27a0e55a46dd9a5607fa6ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701702"
---
# <a name="enable-and-manage-azure-storage-analytics-logs-classic"></a>Azure Opslaganalyse logboeken inschakelen en beheren (klassiek)

[Azure Opslaganalyse](storage-analytics.md) biedt logboeken voor blobs, wacht rijen en tabellen. U kunt de [Azure Portal](https://portal.azure.com) gebruiken om logboeken te configureren worden vastgelegd voor uw account. In dit artikel leest u hoe u Logboeken kunt inschakelen en beheren. Zie [Azure Opslaganalyse metrische gegevens inschakelen en beheren (klassiek)]()voor meer informatie over het inschakelen van metrische gegevens.  Er zijn kosten verbonden aan het onderzoeken en opslaan van bewakings gegevens in de Azure Portal. Zie [Opslaganalyse](storage-analytics.md) voor meer informatie.

> [!NOTE]
> U wordt aangeraden Azure Storage-Logboeken in Azure Monitor te gebruiken in plaats van Opslaganalyse Logboeken. Azure Storage-Logboeken in Azure Monitor bevinden zich in de open bare preview en is beschikbaar voor preview-tests in alle open bare Cloud regio's. Met deze preview-versie kunt u logboeken maken voor blobs (waaronder Azure Data Lake Storage Gen2), bestanden, wacht rijen en tabellen. Zie een van de volgende artikelen voor meer informatie:
>
> - [Azure Blob Storage controleren](../blobs/monitor-blob-storage.md)
> - [Bewakings Azure Files](../files/storage-files-monitoring.md)
> - [Azure Queue Storage controleren](../queues/monitor-queue-storage.md)
> - [Azure-tabel opslag bewaken](../tables/monitor-table-storage.md)

Zie [Microsoft Azure Storage controleren, diagnosticeren en problemen oplossen](storage-monitoring-diagnosing-troubleshooting.md)voor meer informatie over het gebruik van Opslaganalyse en andere hulpprogram ma's voor het identificeren, vaststellen en oplossen van problemen met Azure Storage.

<a id="configure-logging"></a>

## <a name="enable-logs"></a>Logboeken inschakelen

U kunt Azure Storage voor het opslaan van Diagnostische logboeken voor lees-, schrijf-en verwijderings aanvragen voor de blob-, Table-en Queue-Services. Het beleid voor gegevens retentie dat u instelt, is ook van toepassing op deze logboeken.

> [!NOTE]
> Azure Files ondersteunt momenteel Opslaganalyse metrische gegevens, maar biedt geen ondersteuning voor Opslaganalyse logboek registratie.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer in de [Azure Portal](https://portal.azure.com) **opslag accounts** en vervolgens de naam van het opslag account om de Blade opslag account te openen.

2. Selecteer **Diagnostische instellingen (klassiek)** in het gedeelte **bewaking (klassiek)** van de menu-Blade.

    ![Menu-item diagnostische gegevens onder bewaking in het Azure Portal.](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. Controleer of de **status** is ingesteld **op aan** en selecteer de **Services** waarvoor u logboek registratie wilt inschakelen.

   > [!div class="mx-imgBorder"]
   > ![Configureer logboek registratie in de Azure Portal.](./media/manage-storage-analytics-logs/enable-diagnostics.png)    


4. Zorg ervoor dat het selectie vakje **gegevens verwijderen** is ingeschakeld.  Stel vervolgens het aantal dagen in dat logboek gegevens bewaard moeten blijven door het besturings element schuif regelaar onder het selectie vakje te verplaatsen of door de waarde die wordt weer gegeven in het tekstvak naast het besturings element schuif regelaar rechtstreeks te wijzigen. De standaard waarde voor nieuwe opslag accounts is zeven dagen. Als u geen Bewaar beleid wilt instellen, voert u nul in. Als er geen Bewaar beleid is, is het aan te raden om de logboek gegevens te verwijderen.

   > [!WARNING]
   > Logboeken worden opgeslagen als gegevens in uw account. logboek gegevens kunnen gedurende een periode in uw account worden verzameld, waardoor de opslag kosten kunnen worden verhoogd. Als u slechts gedurende een lange periode logboek gegevens nodig hebt, kunt u uw kosten verlagen door het Bewaar beleid voor gegevens te wijzigen. Verlopen logboek gegevens (gegevens die ouder zijn dan het Bewaar beleid) worden verwijderd door het systeem. We raden u aan een Bewaar beleid in te stellen op basis van hoe lang u de logboek gegevens voor uw account wilt bewaren. Zie [facturering op metrische](storage-analytics-metrics.md#billing-on-storage-metrics) gegevens van de opslag voor meer informatie.

4. Klik op **Opslaan**.

   De diagnostische logboeken worden opgeslagen in een BLOB-container met de naam *$logs* in uw opslag account. U kunt de logboek gegevens weer geven met behulp van een opslag Verkenner, zoals de [Microsoft Azure Storage Explorer](https://storageexplorer.com), of programmatisch met behulp van de Storage-client bibliotheek of Power shell.

   Zie [logboek registratie van Storage Analytics](storage-analytics-logging.md)voor informatie over het openen van de $logs-container.

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

6. Gebruik de **set-AzStorageServiceLoggingProperty** om de huidige logboek instellingen te wijzigen. De cmdlets die de logboek registratie van opslag regelen, gebruiken een **LoggingOperations** -para meter die een teken reeks is met een door komma's gescheiden lijst met aanvraag typen die moeten worden geregistreerd. De drie mogelijke aanvraag typen zijn **lezen**, **schrijven** en **verwijderen**. Als u logboek registratie wilt uitschakelen, gebruikt u de waarde **geen** voor de para meter **LoggingOperations** .  

   Met de volgende opdracht wordt de logboek registratie voor lees-, schrijf-en verwijder aanvragen in het Queue-service in uw standaard-opslag account met Bewaar termijn ingesteld op vijf dagen:  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5 -Context $ctx
   ```  

   > [!WARNING]
   > Logboeken worden opgeslagen als gegevens in uw account. logboek gegevens kunnen gedurende een periode in uw account worden verzameld, waardoor de opslag kosten kunnen worden verhoogd. Als u slechts gedurende een lange periode logboek gegevens nodig hebt, kunt u uw kosten verlagen door het Bewaar beleid voor gegevens te wijzigen. Verlopen logboek gegevens (gegevens die ouder zijn dan het Bewaar beleid) worden verwijderd door het systeem. We raden u aan een Bewaar beleid in te stellen op basis van hoe lang u de logboek gegevens voor uw account wilt bewaren. Zie [facturering op metrische](storage-analytics-metrics.md#billing-on-storage-metrics) gegevens van de opslag voor meer informatie.
   
   Met de volgende opdracht schakelt u de logboek registratie voor de tabel service in uw standaard-opslag account uit:  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none -Context $ctx 
   ```  

   Zie voor informatie over het configureren van de Azure PowerShell-cmdlets voor het werken met uw Azure-abonnement en het selecteren van het standaard opslag account dat moet worden gebruikt: [Azure PowerShell installeren en configureren](/powershell/azure/).  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
``` 

---

<a id="modify-retention-policy"></a>

## <a name="modify-log-data-retention-period"></a>Bewaar periode voor logboek gegevens wijzigen

Logboek gegevens kunnen gedurende een periode in uw account worden verzameld, waardoor de opslag kosten kunnen worden verhoogd. Als u slechts gedurende een periode logboek gegevens nodig hebt, kunt u uw kosten verlagen door de Bewaar periode voor logboek gegevens te wijzigen. Als u bijvoorbeeld slechts drie dagen logboeken nodig hebt, stelt u de Bewaar periode voor uw logboek gegevens in op een waarde van `3` . Op die manier worden logboeken na drie dagen automatisch uit uw account verwijderd. In deze sectie wordt beschreven hoe u de huidige Bewaar periode voor de logboek gegevens weergeeft en hoe u die periode bijwerkt als u dat wilt.

> [!NOTE]
> Deze stappen zijn alleen van toepassing op accounts waarvoor de instelling **hiërarchische naam ruimte** niet is ingeschakeld. Als u deze instelling hebt ingeschakeld voor uw account, wordt de instelling voor Bewaar dagen nog niet ondersteund. In plaats daarvan moet u Logboeken hand matig verwijderen door gebruik te maken van elk ondersteund hulp programma, zoals Azure Storage Explorer, REST of een SDK. Als u deze logboeken wilt vinden in uw opslag account, raadpleegt u [hoe logboeken worden opgeslagen](storage-analytics-logging.md#how-logs-are-stored).

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer in de [Azure Portal](https://portal.azure.com) **opslag accounts** en vervolgens de naam van het opslag account om de Blade opslag account te openen.
2. Selecteer **Diagnostische instellingen (klassiek)** in het gedeelte **bewaking (klassiek)** van de menu-Blade.

    ![Menu-item diagnostische gegevens onder bewaking in het Azure Portal](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. Zorg ervoor dat het selectie vakje **gegevens verwijderen** is ingeschakeld.  Stel vervolgens het aantal dagen in dat logboek gegevens bewaard moeten blijven door het besturings element schuif regelaar onder het selectie vakje te verplaatsen of door de waarde die wordt weer gegeven in het tekstvak naast het besturings element schuif regelaar rechtstreeks te wijzigen.

   > [!div class="mx-imgBorder"]
   > ![De Bewaar periode in de Azure Portal wijzigen](./media/manage-storage-analytics-logs/modify-retention-period.png)

   Het standaard aantal dagen voor nieuwe opslag accounts is zeven dagen. Als u geen Bewaar beleid wilt instellen, voert u nul in. Als er geen Bewaar beleid is, kunt u de bewakings gegevens verwijderen.
   
4. Klik op **Opslaan**.

   De diagnostische logboeken worden opgeslagen in een BLOB-container met de naam *$logs* in uw opslag account. U kunt de logboek gegevens weer geven met behulp van een opslag Verkenner, zoals de [Microsoft Azure Storage Explorer](https://storageexplorer.com), of programmatisch met behulp van de Storage-client bibliotheek of Power shell.

   Zie [logboek registratie van Storage Analytics](storage-analytics-logging.md)voor informatie over het openen van de $logs-container.

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

5. Haal de context van het opslag account op waarmee het opslag account wordt gedefinieerd.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Vervang de `<resource-group-name>` waarde van de tijdelijke aanduiding door de naam van uw resource groep.

   * Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount. 

6. Gebruik de [Get-AzStorageServiceLoggingProperty](/powershell/module/az.storage/get-azstorageserviceloggingproperty) om het huidige beleid voor het bewaren van logboeken weer te geven. In het volgende voor beeld wordt de Bewaar periode voor Blob-en Queue Storage-services afgedrukt op de console.

   ```powershell
   Get-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -Context $ctx
   ```  

   In de-console-uitvoer wordt de Bewaar periode weer gegeven onder de kolomkop `RetentionDays` .

   > [!div class="mx-imgBorder"]
   > ![Bewaar beleid in Power shell-uitvoer](./media/manage-storage-analytics-logs/retention-period-powershell.png)

7. Gebruik de [set-AzStorageServiceLoggingProperty](/powershell/module/az.storage/set-azstorageserviceloggingproperty) om de Bewaar periode te wijzigen. In het volgende voor beeld wordt de Bewaar periode gewijzigd in 4 dagen.  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -RetentionDays 4 -Context $ctx
   ```  

   Zie voor informatie over het configureren van de Azure PowerShell-cmdlets voor het werken met uw Azure-abonnement en het selecteren van het standaard opslag account dat moet worden gebruikt: [Azure PowerShell installeren en configureren](/powershell/azure/).  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

In het volgende voor beeld wordt de Bewaar periode voor Blob-en Queue Storage-services afgedrukt op de console.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ViewRetentionPeriod":::

In het volgende voor beeld wordt de Bewaar periode gewijzigd in 4 dagen. 

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ModifyRetentionPeriod":::

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

In het volgende voor beeld wordt de Bewaar periode voor Blob-en Queue Storage-services afgedrukt op de console.

```csharp
var storageAccount = CloudStorageAccount.Parse(connectionString);

var blobClient = storageAccount.CreateCloudBlobClient();
var queueClient = storageAccount.CreateCloudQueueClient();

var blobserviceProperties = blobClient.GetServiceProperties();
var queueserviceProperties = queueClient.GetServiceProperties();

Console.WriteLine("Retention period for logs from the blob service is: " +
   blobserviceProperties.Logging.RetentionDays.ToString());

Console.WriteLine("Retention period for logs from the queue service is: " +
   queueserviceProperties.Logging.RetentionDays.ToString());
```

In het volgende voor beeld wordt de Bewaar periode voor logboeken voor de BLOB-en Queue Storage-services tot 4 dagen gewijzigd. 

```csharp

blobserviceProperties.Logging.RetentionDays = 4;
queueserviceProperties.Logging.RetentionDays = 4;

blobClient.SetServiceProperties(blobserviceProperties);
queueClient.SetServiceProperties(queueserviceProperties);  
``` 

---

### <a name="verify-that-log-data-is-being-deleted"></a>Controleren of de logboek gegevens worden verwijderd

U kunt controleren of Logboeken worden verwijderd door de inhoud van de `$logs` container van uw opslag account weer te geven. In de volgende afbeelding ziet u de inhoud van een map in de `$logs` container. De map komt overeen met 2021 januari en elke map bevat logboeken één dag. Als de dag vandaag januari 29 2021 was en het Bewaar beleid is ingesteld op slechts één dag, moet deze map alleen logboeken voor één dag bevatten.

> [!div class="mx-imgBorder"]
> ![Lijst met logboek mappen in azure Portal](./media/manage-storage-analytics-logs/verify-and-delete-logs.png)

<a id="download-storage-logging-log-data"></a>

## <a name="view-log-data"></a>Logboek gegevens weer geven

 Als u uw logboek gegevens wilt bekijken en analyseren, moet u de blobs downloaden die de logboek gegevens bevatten die u wilt raadplegen op een lokale computer. Veel hulpprogram ma's voor opslag bladeren maken het mogelijk om blobs te downloaden vanuit uw opslag account. u kunt ook het Azure Storage team dat is voorzien van het opdracht regel programma Azure copy tool [AzCopy](storage-use-azcopy-v10.md) gebruiken om uw logboek gegevens te downloaden.  
 
>[!NOTE]
> De `$logs` container is niet geïntegreerd met Event grid, dus u ontvangt geen meldingen wanneer er logboek bestanden worden geschreven. 

 Om ervoor te zorgen dat u de logboek gegevens downloadt die u interesseert en om te voor komen dat dezelfde logboek gegevens meermaals worden gedownload:  

-   Gebruik de naam Conventie voor datum en tijd voor blobs die logboek gegevens bevatten om bij te houden welke blobs u al hebt gedownload voor analyse om te voor komen dat dezelfde gegevens meerdere keren opnieuw worden gedownload.  

-   Gebruik de meta gegevens op de blobs met logboek gegevens om de specifieke periode te identificeren waarvoor de BLOB logboek gegevens bevat om de exacte BLOB te identificeren die u moet downloaden.  

Om aan de slag te gaan met AzCopy raadpleegt u aan de [slag met AzCopy](storage-use-azcopy-v10.md) 

In het volgende voor beeld ziet u hoe u de logboek gegevens voor de wachtrij service kunt downloaden voor de uren, te beginnen bij 09 uur, 10 uur en 11 uur op twintig mei 2014.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Voor meer informatie over het downloaden van specifieke bestanden raadpleegt u [blobs in Azure Blob Storage downloaden met behulp van AzCopy V10 toevoegen](./storage-use-azcopy-blobs-download.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Wanneer u uw logboek gegevens hebt gedownload, kunt u de logboek vermeldingen in de bestanden weer geven. Deze logboek bestanden maken gebruik van een tekst indeling met scheidings tekens die door veel hulpprogram ma's voor logboeken kunnen worden geparseerd (Zie de hand leiding voor [bewaking, diagnose en probleem oplossing Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)) voor meer informatie. Andere hulpprogram ma's hebben verschillende voorzieningen voor het opmaken, filteren, sorteren en zoeken in de inhoud van uw logboek bestanden. Zie [Opslaganalyse-logboek indeling](/rest/api/storageservices/storage-analytics-log-format) en [Opslaganalyse geregistreerde bewerkingen en status berichten](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)voor meer informatie over de indeling en inhoud van het logboek bestand voor opslag logboek registratie.

## <a name="next-steps"></a>Volgende stappen

* Zie [Opslaganalyse](storage-analytics.md) voor Opslaganalyse voor meer informatie over Opslaganalyse.
* Zie voor meer informatie over het gebruik van een .NET-taal voor het configureren van opslag logboek registratie de [referentie opslag-client bibliotheek](/previous-versions/azure/dn261237(v=azure.100)). 
* Zie [Opslaganalyse inschakelen en configureren](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)voor algemene informatie over het configureren van opslag logboek registratie met behulp van de rest API.
* Meer informatie over de indeling van Opslaganalyse-Logboeken. Zie [Opslaganalyse-logboek indeling](/rest/api/storageservices/storage-analytics-log-format).
