---
title: 'Azure-bestandsshare: verwijderen van bestanden uit de Azure-bestandsshare is mislukt'
description: De fout bij het verwijderen van bestanden van een Azure-bestands share identificeren en oplossen.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 3d4f10745d90ccd83e7251af40d3e92a230f2fcd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629679"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Azure-bestandsshare: verwijderen van bestanden uit de Azure-bestandsshare is mislukt

Het verwijderen van bestanden uit de Azure-bestands share kan zich op verschillende symptomen voordoen:

**Symptoom 1:**

Het verwijderen van een bestand in de Azure-bestands share is mislukt vanwege een van de volgende twee problemen:

* Het bestand dat is gemarkeerd voor verwijderen
* De opgegeven resource wordt mogelijk gebruikt door een SMB-client

**Symptoom 2:**

Onvoldoende quotum beschikbaar om deze opdracht te verwerken

## <a name="cause"></a>Oorzaak

Fout 1816 treedt op wanneer de bovengrens van gelijktijdige geopende ingangen die zijn toegestaan voor een bestand, worden bereikt op de computer waarop de bestands share wordt gekoppeld. Zie de [Azure Storage controle lijst voor prestaties en schaal baarheid](../blobs/storage-performance-checklist.md)voor meer informatie.

## <a name="resolution"></a>Oplossing

Verminder het aantal gelijktijdige open ingangen door enkele ingangen te sluiten.

## <a name="prerequisite"></a>Vereiste

### <a name="install-the-latest-azure-powershell-module"></a>De nieuwste Azure PowerShell-module installeren

* [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Verbinding maken met Azure:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Selecteer het abonnement van het doel opslag account:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Context maken voor het doel opslag account:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>De huidige geopende ingangen van de bestands share ophalen:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Voor beeld van resultaat:

|HandleId|Pad|ClientIp|ClientPort|Opentijd|LastReconnectTime|Id|ParentId|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Nieuwe map/test.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test.zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Een open ingang sluiten:

Als u een open ingang wilt sluiten, gebruikt u de volgende opdracht:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Volgende stappen

* [Problemen met Azure Files in Windows oplossen](storage-troubleshoot-windows-file-connection-problems.md)
* [Problemen met Azure Files in Linux oplossen](storage-troubleshoot-linux-file-connection-problems.md)
* [Problemen oplossen met Azure File Sync](storage-sync-files-troubleshoot.md)