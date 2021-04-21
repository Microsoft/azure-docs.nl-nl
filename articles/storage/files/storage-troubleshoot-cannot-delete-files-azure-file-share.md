---
title: 'Azure-bestandsshare: verwijderen van bestanden uit de Azure-bestandsshare is mislukt'
description: Identificeer en los problemen op met het verwijderen van bestanden uit een Azure-bestands share.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: d8cc0cb7df4bb7bfff5a6b9d2f159cb674532927
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789749"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Azure-bestandsshare: verwijderen van bestanden uit de Azure-bestandsshare is mislukt

Het niet verwijderen van bestanden uit een Azure-bestands share kan verschillende symptomen hebben:

**Symptoom 1:**

Kan een bestand in de Azure-bestands share niet verwijderen vanwege een van de volgende twee problemen:

* Het bestand dat is gemarkeerd voor verwijderen
* De opgegeven resource kan worden gebruikt door een SMB-client

**Symptoom 2:**

Er is onvoldoende quotum beschikbaar om deze opdracht te verwerken

## <a name="cause"></a>Oorzaak

Fout 1816 treedt op wanneer u de bovengrens bereikt van gelijktijdige geopende grepen die zijn toegestaan voor een bestand, op de computer waarop de bestands share wordt bevestigd. Zie de controlelijst voor Azure Storage [prestaties en schaalbaarheid voor meer informatie.](../blobs/storage-performance-checklist.md)

## <a name="resolution"></a>Oplossing

Verminder het aantal gelijktijdige geopende grepen door enkele grepen te sluiten.

## <a name="prerequisite"></a>Vereiste

### <a name="install-the-latest-azure-powershell-module"></a>Installeer de nieuwste Azure PowerShell module

* [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Verbinding maken met Azure:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Selecteer het abonnement van het doelopslagaccount:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Context maken voor het doelopslagaccount:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>Haal de huidige geopende grepen van de bestands share op:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Voorbeeldresultaat:

|HandleId|Pad|ClientIp|ClientPort|OpenTime|LastReconnectTime|FileId|ParentId|Sessionid|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Nieuwe map/test.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test.zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Sluit een geopende greep:

Gebruik de volgende opdracht om een geopende greep te sluiten:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Volgende stappen

* [Problemen met Azure Files in Windows oplossen](storage-troubleshoot-windows-file-connection-problems.md)
* [Problemen met Azure Files in Linux oplossen](storage-troubleshoot-linux-file-connection-problems.md)
* [Problemen oplossen met Azure File Sync](../file-sync/file-sync-troubleshoot.md)