---
title: Logboekregistratie van Azure Opslaganalyse
description: Gebruik Opslaganalyse om details over Azure Storage aanvragen te registreren. Bekijk welke aanvragen worden geregistreerd, hoe logboeken worden opgeslagen, hoe opslag logboeken worden ingeschakeld, en meer.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 07/23/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: eb71de223e2d840e0caa0444b837e16e1f091414
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484784"
---
# <a name="azure-storage-analytics-logging"></a>Logboekregistratie van Azure Opslaganalyse

Opslaganalyse registreert gedetailleerde informatie over geslaagde en mislukte aanvragen bij een opslagservice. Deze informatie kan worden gebruikt voor het bewaken van afzonderlijke aanvragen en voor het vaststellen van problemen met een opslagservice. Aanvragen worden op de beste basis geregistreerd.

 Logboekregistratie van Opslaganalyse is niet standaard ingeschakeld voor uw opslagaccount. U kunt deze inschakelen in de [Azure Portal](https://portal.azure.com/); Zie [een opslag account bewaken in de Azure Portal](./storage-monitor-storage-account.md)voor meer informatie. U kunt Opslaganalyse ook via een programma inschakelen via de REST API of de client bibliotheek. Gebruik de bewerkingen eigenschappen van [BLOB-service ophalen](/rest/api/storageservices/Blob-Service-REST-API), [Eigenschappen van wachtrij service ophalen](/rest/api/storageservices/Get-Queue-Service-Properties)en [Eigenschappen van Table service ophalen](/rest/api/storageservices/Get-Table-Service-Properties) om Opslaganalyse in te scha kelen voor elke service.

 Logboek vermeldingen worden alleen gemaakt als er aanvragen worden gedaan voor het service-eind punt. Als een opslag account bijvoorbeeld activiteit heeft in het BLOB-eind punt, maar niet in de tabel-of wachtrij-eind punten, worden alleen logboeken gemaakt die betrekking hebben op het Blob service.

> [!NOTE]
>  Logboekregistratie voor opslaganalyse is momenteel alleen beschikbaar voor de blob-, wachtrij- en tabelservices. Opslaganalyse-logboek registratie is ook beschikbaar voor Premium-performance [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) -accounts. Het is echter niet beschikbaar voor algemeen gebruik v2-accounts met Premium-prestaties.

## <a name="requests-logged-in-logging"></a>Aanvragen vastgelegd in logboek registratie
### <a name="logging-authenticated-requests"></a>Geverifieerde aanvragen registreren

 De volgende typen geverifieerde aanvragen worden geregistreerd:

- Geslaagde aanvragen
- Mislukte aanvragen, inclusief time-out-, beperkings-, netwerk-, autorisatiefouten en overige fouten
- Aanvragen waarvoor een SAS (Shared Access Signature) of OAuth is gebruikt, inclusief geslaagde en mislukte aanvragen
- Aanvragen voor analysegegevens

  Aanvragen die worden gedaan door Opslaganalyse zichzelf, zoals het maken of verwijderen van een logboek, worden niet geregistreerd. Een volledige lijst met de geregistreerde gegevens wordt gedocumenteerd in de onderwerpen [Opslaganalyse vastgelegde bewerkingen en status berichten](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) en [Opslaganalyse logboek indeling](/rest/api/storageservices/storage-analytics-log-format) .

### <a name="logging-anonymous-requests"></a>Anonieme aanvragen registreren

 De volgende typen anonieme aanvragen worden geregistreerd:

- Geslaagde aanvragen
- Serverfouten
- Time-outfouten voor client en server
- Mislukte GET-aanvragen met foutcode 304 (Niet gewijzigd)

  Alle andere mislukte anonieme aanvragen worden niet geregistreerd. Een volledige lijst met de geregistreerde gegevens wordt gedocumenteerd in de onderwerpen [Opslaganalyse vastgelegde bewerkingen en status berichten](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) en [Opslaganalyse logboek indeling](/rest/api/storageservices/storage-analytics-log-format) .

## <a name="how-logs-are-stored"></a>Hoe logboeken worden opgeslagen

Alle logboeken worden opgeslagen in blok-blobs in een container met de naam `$logs` , die automatisch wordt gemaakt wanneer Opslaganalyse is ingeschakeld voor een opslag account. De `$logs` container bevindt zich in de BLOB-naam ruimte van het opslag account, bijvoorbeeld: `http://<accountname>.blob.core.windows.net/$logs` . Deze container kan niet worden verwijderd als Opslaganalyse is ingeschakeld, maar de inhoud ervan kan worden verwijderd. Als u het hulp programma voor het bladeren door opslag gebruikt om rechtstreeks naar de container te navigeren, worden alle blobs weer gegeven die uw logboek gegevens bevatten.

> [!NOTE]
>  De `$logs` container wordt niet weer gegeven wanneer een container vermelding wordt uitgevoerd, zoals de bewerking lijst containers. Deze moet rechtstreeks worden geopend. U kunt bijvoorbeeld de bewerking lijst-blobs gebruiken om toegang te krijgen tot de blobs in de `$logs` container.

Wanneer aanvragen worden geregistreerd, worden de tussenliggende resultaten door Opslaganalyse geüpload als blokken. Deze blokken worden regel matig door Opslaganalyse doorgevoerd en beschikbaar gemaakt als een blob. Het kan tot een uur duren voordat de logboek gegevens worden weer gegeven in de blobs in de container **$logs** , omdat de frequentie waarmee de logboek schrijvers worden leeg gemaakt door de opslag service. Er kunnen dubbele records bestaan voor logboeken die in hetzelfde uur zijn gemaakt. U kunt bepalen of een record een duplicaat is door de **aanvraag** -en **bewerkings** nummer te controleren.

Als u een groot aantal logboek gegevens met meerdere bestanden per uur hebt, kunt u de BLOB-meta gegevens gebruiken om te bepalen welke gegevens het logboek bevat door de BLOB-meta gegevens velden te controleren. Dit is ook handig omdat er soms vertraging kan optreden terwijl er gegevens naar de logboek bestanden worden geschreven: de BLOB-meta gegevens geven een nauw keurigere indicatie van de blob-inhoud dan de naam van de blob.

Met de meeste hulpprogram ma's voor opslag navigatie kunt u de meta gegevens van blobs weer geven. u kunt deze informatie ook lezen met behulp van Power shell of via een programma. Het volgende Power shell-fragment is een voor beeld van het filteren van de lijst met logboek-blobs op naam om een tijd op te geven en door meta gegevens om alleen die logboeken te identificeren die **Schrijf** bewerkingen bevatten.  

 ```powershell
 Get-AzStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Zie het [inventariseren van BLOB-resources](/rest/api/storageservices/Enumerating-Blob-Resources) en [het instellen en ophalen van eigenschappen en meta gegevens voor BLOB-resources](/rest/api/storageservices/Setting-and-Retrieving-Properties-and-Metadata-for-Blob-Resources)voor meer informatie over het programmatisch weer geven van blobs.  

### <a name="log-naming-conventions"></a>Naamgevings regels vastleggen

 Elk logboek wordt in de volgende indeling geschreven:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 In de volgende tabel wordt elk kenmerk in de logboek naam beschreven:

|Kenmerk|Beschrijving|
|---------------|-----------------|
|`<service-name>`|De naam van de opslag service. Bijvoorbeeld: `blob` , `table` , of `queue`|
|`YYYY`|Het jaar van vier cijfers voor het logboek. Bijvoorbeeld: `2011`|
|`MM`|De maand met twee cijfers voor het logboek. Bijvoorbeeld: `07`|
|`DD`|De twee cijfer dagen voor het logboek. Bijvoorbeeld: `31`|
|`hh`|Het twee cijferige uur waarmee het begin uur voor de logboeken wordt aangegeven, in UTC-notatie van 24 uur. Bijvoorbeeld: `18`|
|`mm`|Het nummer van twee cijfers dat de begin minuut voor de logboeken aangeeft. **Opmerking:**  Deze waarde wordt niet ondersteund in de huidige versie van Opslaganalyse en de waarde ervan is altijd `00` .|
|`<counter>`|Een teller op basis van nul met zes cijfers waarmee het aantal logboek-blobs wordt aangegeven dat is gegenereerd voor de opslag service in een uur tijds periode. Deze teller begint bij `000000` . Bijvoorbeeld: `000001`|

 Hier volgt een volledige naam voor een voor beeld van een logboek waarin de bovenstaande voor beelden worden gecombineerd:

 `blob/2011/07/31/1800/000001.log`

 Hier volgt een voor beeld-URI die kan worden gebruikt om toegang te krijgen tot het bovenstaande logboek:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Wanneer een opslag aanvraag wordt geregistreerd, komt de resulterende logboek naam overeen met het uur wanneer de aangevraagde bewerking is voltooid. Als een GetBlob-aanvraag bijvoorbeeld is voltooid op 6:17.30 op 7/31/2011, wordt het logboek met het volgende voor voegsel geschreven: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Meta gegevens van logboek

 Alle logboek-blobs worden opgeslagen met meta gegevens die kunnen worden gebruikt om te identificeren welke logboek registratie gegevens de BLOB bevat. In de volgende tabel wordt elk meta gegevens kenmerk beschreven:

|Kenmerk|Beschrijving|
|---------------|-----------------|
|`LogType`|Hierin wordt beschreven of het logboek informatie bevat die betrekking heeft op lees-, schrijf-of verwijder bewerkingen. Deze waarde kan één type of een combi natie van alle drie zijn, gescheiden door komma's.<br /><br /> Voor beeld 1: `write`<br /><br /> Voor beeld 2: `read,write`<br /><br /> Voor beeld 3: `read,write,delete`|
|`StartTime`|De vroegste tijd van een vermelding in het logboek, in de vorm van `YYYY-MM-DDThh:mm:ssZ` . Bijvoorbeeld: `2011-07-31T18:21:46Z`|
|`EndTime`|De meest recente tijd van een vermelding in het logboek, in de vorm van `YYYY-MM-DDThh:mm:ssZ` . Bijvoorbeeld: `2011-07-31T18:22:09Z`|
|`LogVersion`|De versie van de logboek indeling.|

 De volgende lijst geeft een overzicht van de volledige voor beelden van meta gegevens aan de hand

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Opslag logboek registratie inschakelen

U kunt logboek registratie van opslag inschakelen met Azure Portal-, Power shell-en opslag-Sdk's.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Opslag logboek registratie inschakelen met behulp van de Azure Portal  

Gebruik in de Azure Portal de Blade **Diagnostische instellingen (klassiek)** om de logboek registratie van opslag te beheren, toegankelijk via het gedeelte **bewaking (klassiek)** van de **menu Blade** van een opslag account.

U kunt de opslag services opgeven die u wilt registreren en de retentie periode (in dagen) voor de geregistreerde gegevens.  

### <a name="enable-storage-logging-using-powershell"></a>Logboek registratie van opslag inschakelen met Power shell  

 U kunt Power shell op uw lokale machine gebruiken om opslag logboek registratie te configureren in uw opslag account met behulp van de cmdlet **Get-AzStorageServiceLoggingProperty** van Azure PowerShell om de huidige instellingen op te halen en de cmdlet **set-AzStorageServiceLoggingProperty** om de huidige instellingen te wijzigen.  

 De cmdlets die de logboek registratie van opslag regelen, gebruiken een **LoggingOperations** -para meter die een teken reeks is met een door komma's gescheiden lijst met aanvraag typen die moeten worden geregistreerd. De drie mogelijke aanvraag typen zijn **lezen**, **schrijven** en **verwijderen**. Als u logboek registratie wilt uitschakelen, gebruikt u de waarde **geen** voor de para meter **LoggingOperations** .  

 Met de volgende opdracht wordt de logboek registratie voor lees-, schrijf-en verwijder aanvragen in het Queue-service in uw standaard-opslag account met Bewaar termijn ingesteld op vijf dagen:  

```powershell
Set-AzStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 Met de volgende opdracht schakelt u de logboek registratie voor de tabel service in uw standaard-opslag account uit:  

```powershell
Set-AzStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Zie voor informatie over het configureren van de Azure PowerShell-cmdlets voor het werken met uw Azure-abonnement en het selecteren van het standaard opslag account dat moet worden gebruikt: [Azure PowerShell installeren en configureren](/powershell/azure/).  

### <a name="enable-storage-logging-programmatically"></a>Opslag logboek registratie via een programma inschakelen  

 Naast het gebruik van de Azure Portal of de Azure PowerShell-cmdlets voor het beheren van de opslag logboek registratie, kunt u ook een van de Azure Storage-Api's gebruiken. Als u bijvoorbeeld een .NET-taal gebruikt, kunt u de Storage-client bibliotheek gebruiken.  

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

# <a name="net-v11-sdk"></a>[\.NET V11 SDK](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

---


 Zie voor meer informatie over het gebruik van een .NET-taal voor het configureren van opslag logboek registratie de [referentie opslag-client bibliotheek](/previous-versions/azure/dn261237(v=azure.100)).  

 Zie [Opslaganalyse inschakelen en configureren](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)voor algemene informatie over het configureren van opslag logboek registratie met behulp van de rest API.  

## <a name="download-storage-logging-log-data"></a>Logboek gegevens van opslag logboeken downloaden

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

Zie [specifieke bestanden downloaden](./storage-use-azcopy-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#download-specific-files)voor meer informatie over het downloaden van specifieke bestanden.

Wanneer u uw logboek gegevens hebt gedownload, kunt u de logboek vermeldingen in de bestanden weer geven. Deze logboek bestanden maken gebruik van een tekst indeling met scheidings tekens die door veel hulpprogram ma's voor logboeken kunnen worden geparseerd (Zie de hand leiding voor [bewaking, diagnose en probleem oplossing Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)) voor meer informatie. Andere hulpprogram ma's hebben verschillende voorzieningen voor het opmaken, filteren, sorteren en zoeken in de inhoud van uw logboek bestanden. Zie [Opslaganalyse-logboek indeling](/rest/api/storageservices/storage-analytics-log-format) en [Opslaganalyse geregistreerde bewerkingen en status berichten](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)voor meer informatie over de indeling en inhoud van het logboek bestand voor opslag logboek registratie.

## <a name="next-steps"></a>Volgende stappen

* [Opslaganalyse-logboek indeling](/rest/api/storageservices/storage-analytics-log-format)
* [Geregistreerde bewerkingen en status berichten Opslaganalyse](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Opslaganalyse metrische gegevens (klassiek)](storage-analytics-metrics.md)