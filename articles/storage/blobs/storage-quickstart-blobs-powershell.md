---
title: 'Quickstart: Een blob maken met PowerShell'
titleSuffix: Azure Storage
description: In deze snelstart gebruikt u Azure PowerShell in object(blob)-opslag. Vervolgens gebruikt u PowerShell om een blob in Azure Storage te uploaden, een blob te downloaden en de blobs in een container te vermelden.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 03/31/2020
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 21fc7e6de38a98e4f0853cf513f9f3441c7e2106
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735349"
---
# <a name="quickstart-upload-download-and-list-blobs-with-powershell"></a>Quickstart: Blobs uploaden, downloaden en vermelden met PowerShell

Gebruik de Azure PowerShell-module om Azure-resources te maken en beheren. Azure-resources kunnen vanaf de PowerShell-opdrachtregel of met scripts worden gemaakt of beheerd. In deze handleiding wordt beschreven hoe PowerShell kan worden gebruikt om bestanden over te dragen tussen de lokale schijf en Azure Blob Storage.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig voor toegang tot Azure Storage. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

U hebt ook de rol gegevensinzender voor opslag BLOB nodig voor het lezen, schrijven en verwijderen van Azure Storage containers en blobs.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Voor deze snelstart is Az-versie 0.7 of hoger van de Azure PowerShell-module vereist. Voer `Get-InstalledModule -Name Az -AllVersions | select Name,Version` uit om de versie te bekijken. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Een container maken

Blobs worden altijd naar een container geüpload. U kunt groepen blobs ordenen net zoals u bestanden op uw computer in mappen ordent.

Stel de containernaam in en maak vervolgens de container met behulp van [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Stel de machtigingen in op `blob` om openbare toegang tot de bestanden te verlenen. De containernaam in dit voorbeeld is *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Blobs uploaden naar de container

Blob-opslag ondersteunt blok-blobs, toevoeg-blobs en pagina-blobs. VHD-bestanden die worden gebruikt voor IaaS-VM's zijn pagina-blobs. Gebruik toevoeg-blobs voor logboekregistratie, bijvoorbeeld wanneer u gegevens wilt wegschrijven naar een bestand en vervolgens gegevens wilt blijven toevoegen. De meeste bestanden die zijn opgeslagen in Blob-opslag, zijn blok-blobs. 

Als u een bestand wilt uploaden naar een blok-blob, haalt u een containerverwijzing op en haalt u vervolgens een verwijzing op naar de blok-blob in deze container. Zodra u de blobverwijzing hebt, kunt u er gegevens naar uploaden met behulp van [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent). Met deze bewerking wordt de blob gemaakt als deze nog niet bestaat, of overschreven als dat wel het geval is.

In de volgende voorbeelden worden *Image001.jpg* en *Image002.png* uit de map *D:\\_TestImages* op de lokale schijf geüpload naar de container die u hebt gemaakt.

```powershell
# upload a file to the default account (inferred) access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image000.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload a file to the Hot access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 
  -StandardBlobTier Hot

# upload another file to the Cool access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
  -StandardBlobTier Cool

# upload a file to a folder to the Archive access tier
Set-AzStorageBlobContent -File "D:\_TestImages\foldername\Image003.jpg" `
  -Container $containerName `
  -Blob "Foldername/Image003.jpg" `
  -Context $ctx 
  -StandardBlobTier Archive
```

Upload zoveel bestanden als u nodig hebt, voordat u doorgaat.

## <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Haal een lijst met blobs in de container op met behulp van [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob). In dit voorbeeld worden alleen de namen van de geüploade blobs weergegeven.

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>Blobs downloaden

Download de blobs naar de lokale vaste schijf. Stel voor elke blob die u wilt downloaden de naam in en roep [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent) aan om de blob te downloaden.

In dit voorbeeld worden de blobs gedownload naar *D:\\_TestImages\Downloads* op de lokale schijf. 

```powershell
# download first blob
Get-AzStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx
```

## <a name="data-transfer-with-azcopy"></a>Gegevensoverdracht met AzCopy

Het AzCopy-opdrachtregelprogramma biedt high-performance, scriptbare gegevensoverdracht voor Azure Storage. U kunt met AzCopy gegevens overzetten naar en van Blob Storage en Azure Files. Zie [Aan de slag met AzCopy](../common/storage-use-azcopy-v10.md) voor meer informatie over AzCopy v10, de nieuwste versie van AzCopy. Zie [Gegevens overzetten met AzCopy en Blob Storage](../common/storage-use-azcopy-v10.md#transfer-data) voor meer informatie over het gebruik van AzCopy v10 met Blob Storage.

In het volgende voorbeeld wordt AzCopy gebruikt om een lokaal bestand te uploaden naar een blob. Vergeet niet om de voorbeeldwaarden te vervangen door uw eigen waarden:

```powershell
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder alle activa die u hebt gemaakt. De eenvoudigste manier om assets te verwijderen is door de resourcegroep te verwijderen. Als u de resourcegroep verwijdert, worden alle resources verwijderd die deze groep bevat. In het volgende voorbeeld worden samen met de resourcegroep het opslagaccount en de resourcegroep zelf verwijderd.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u bestanden overgebracht tussen een lokaal bestandssysteem en Azure Blob Storage. Bekijk de Azure PowerShell-voorbeelden voor Blob Storage voor meer informatie over het gebruik van Blob Storage met behulp van PowerShell.

> [!div class="nextstepaction"]
> [Voorbeelden van Azure PowerShell voor Azure Blob Storage](storage-samples-blobs-powershell.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Naslaginformatie over Microsoft Azure PowerShell Storage-cmdlets

* [PowerShell Storage-cmdlets](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) is een gratis, zelfstandige app van Microsoft waarmee u visueel met Azure Storage-gegevens kunt werken in Windows, macOS en Linux.
