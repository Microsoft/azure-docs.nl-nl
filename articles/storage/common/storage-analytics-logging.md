---
title: Logboekregistratie van Azure Opslaganalyse
description: Gebruik Opslaganalyse om details over Azure Storage aanvragen te registreren. Bekijk welke aanvragen worden geregistreerd, hoe logboeken worden opgeslagen, hoe opslag logboeken worden ingeschakeld, en meer.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 217a804b0155d7886a068283f8669ace0bc81856
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218516"
---
# <a name="azure-storage-analytics-logging"></a>Azure Storage-analyselogboeken

Opslaganalyse registreert gedetailleerde informatie over geslaagde en mislukte aanvragen bij een opslagservice. Deze informatie kan worden gebruikt voor het bewaken van afzonderlijke aanvragen en voor het vaststellen van problemen met een opslagservice. Aanvragen worden op de beste basis geregistreerd.

> [!NOTE]
> U wordt aangeraden Azure Storage-Logboeken in Azure Monitor te gebruiken in plaats van Opslaganalyse Logboeken. Azure Storage-Logboeken in Azure Monitor bevinden zich in de open bare preview en is beschikbaar voor preview-tests in alle open bare Cloud regio's. Met deze preview-versie kunt u logboeken maken voor blobs (waaronder Azure Data Lake Storage Gen2), bestanden, wacht rijen en tabellen. Zie een van de volgende artikelen voor meer informatie:
>
> - [Azure Blob Storage controleren](../blobs/monitor-blob-storage.md)
> - [Bewakings Azure Files](../files/storage-files-monitoring.md)
> - [Azure Queue Storage controleren](../queues/monitor-queue-storage.md)
> - [Azure-tabel opslag bewaken](../tables/monitor-table-storage.md)

 Logboekregistratie van Opslaganalyse is niet standaard ingeschakeld voor uw opslagaccount. U kunt deze functie inschakelen in de [Azure Portal](https://portal.azure.com/) of met behulp van Power shell of Azure cli. Zie [Azure Opslaganalyse-logboeken inschakelen en beheren (klassiek)](manage-storage-analytics-logs.md)voor stapsgewijze instructies. 

U kunt Opslaganalyse logboeken ook programmatisch inschakelen via de REST API of de client bibliotheek. Gebruik de bewerkingen eigenschappen van [BLOB-service ophalen](/rest/api/storageservices/Blob-Service-REST-API), [Eigenschappen van wachtrij service ophalen](/rest/api/storageservices/Get-Queue-Service-Properties)en [Eigenschappen van Table service ophalen](/rest/api/storageservices/Get-Table-Service-Properties) om Opslaganalyse in te scha kelen voor elke service. Zie [Logboeken inschakelen](manage-storage-analytics-logs.md) als u een voor beeld wilt weer geven waarmee Opslaganalyse-logboeken worden ingeschakeld met behulp van .net.

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


## <a name="next-steps"></a>Volgende stappen

* [Azure Opslaganalyse logboeken inschakelen en beheren (klassiek)](manage-storage-analytics-logs.md)
* [Opslaganalyse-logboek indeling](/rest/api/storageservices/storage-analytics-log-format)
* [Geregistreerde bewerkingen en status berichten Opslaganalyse](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Opslaganalyse metrische gegevens (klassiek)](storage-analytics-metrics.md)
