---
title: Snelstart voor het beheren van Azure-bestandsshares met Azure PowerShell
description: Gebruik deze snelstart om te leren hoe u Azure-bestandsshares beheert met Azure PowerShell.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8b4bd9ece5f010f1294356ad4673543834e5076a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94626908"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Snelstart: Een Azure-bestandsshare maken en beheren met Azure PowerShell 
In deze handleiding worden de basisbeginselen besproken van het werken met [Azure-bestandsshares](storage-files-introduction.md) met PowerShell. Azure-bestandsshares zijn net als andere bestandsshares, maar worden in de cloud opgeslagen en ondersteund op het Azure-platform. Azure-bestandsshares ondersteunen het SMB-protocol (Server Message Block) volgens de industriestandaard, (de preview van) het NFS-protocol (Network File System) en bieden de mogelijkheid bestanden te delen tussen meerdere computers, toepassingen en exemplaren. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor deze gids versie 0.7 of hoger van de Azure PowerShell-module vereist. Voer `Get-Module -ListAvailable Az` uit als u wilt weten welke versie van de Azure PowerShell-module u gebruikt. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Login-AzAccount` uitvoeren om u aan te melden bij uw Azure-account.

## <a name="create-a-resource-group"></a>Een resourcegroep maken
Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Als u nog geen Azure-resourcegroep hebt, kunt u een nieuwe groep maken met de cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt in de regio VS - west 2:

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

## <a name="create-a-storage-account"></a>Create a storage account
Een opslagaccount is een gedeelde opslaggroep die u kunt gebruiken voor het implementeren van Azure-bestandsshares. Een opslagaccount kan een onbeperkt aantal shares bevatten en een share kan een onbeperkt aantal bestanden bevatten, totdat de capaciteit van het opslagaccount is bereikt. In dit voorbeeld wordt een GPv2-opslagaccount (een algemene gebruiksversie 2) gemaakt, waarmee standaard Azure-bestandsshares of andere opslagresources zoals blobs of wachtrijen, worden opgeslagen op de harde schijf (HDD) rotatiemedia. Azure Files biedt ook ondersteuning voor Premium Solid-State Disk-schijven (SSD's); Premium Azure-bestandsshares kunnen worden gemaakt in FileStorage-opslagaccounts.

In dit voorbeeld wordt een opslagaccount gemaakt met behulp van de cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Het opslagaccount krijgt de naam *mystorageaccount\<random number>* en een verwijzing naar dit opslagaccount wordt opgeslagen in de variabele **$storageAcct**. Namen van opslagaccounts moeten uniek zijn. Gebruik `Get-Random` om een nummer toe te voegen aan de naam en deze zo uniek te maken. 

```azurepowershell-interactive 
$storageAccountName = "mystorageacct$(Get-Random)"

$storageAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $region `
    -Kind StorageV2 `
    -SkuName Standard_ZRS `
    -EnableLargeFileShare
```

> [!Note]  
> Shares groter dan 5 TiB (maximaal 100 TiB per share) zijn alleen beschikbaar in lokaal redundante (LRS) en zoneredundante (ZRS) opslagaccounts. Als u een geo-redundante (GRS) of geo-zone-redundante (GZRS) opslagaccount wilt maken, verwijdert u de parameter `-EnableLargeFileShare`.

## <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken
U kunt nu uw eerste Azure-bestandsshare maken. U kunt een bestandsshare maken met de cmdlet [New-AzRmStorageShare](/powershell/module/az.storage/New-AzRmStorageShare). In dit voorbeeld wordt een share gemaakt met de naam `myshare`.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
```

De namen van shares moeten bestaan uit kleine letters, cijfers en afbreekstreepjes, maar mogen niet beginnen met een afbreekstreepje. Zie [Shares, mappen, bestanden en metagegevens een naam geven en hiernaar verwijzen](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) voor meer informatie over de naamgeving van bestandsshares en bestanden.

## <a name="use-your-azure-file-share"></a>Azure-bestandsshare gebruiken
Azure Files biedt twee methoden voor het werken met bestanden en mappen in uw Azure-bestandsshare: het [SMB-protocol (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) volgens de industriestandaard en het [File REST-protocol](/rest/api/storageservices/file-service-rest-api). 

Zie het volgende document op basis van het besturingssysteem om een bestandsshare met SMB te koppelen:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [MacOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Een Azure-bestandsshare gebruiken met het File REST-protocol 
Het is mogelijk om met het File REST-protocol te werken (dat wil zeggen HTTP REST-aanroepen zelf te maken). De meest voorkomende manier om het File REST-protocol te gebruiken, is echter met de Azure PowerShell-module, de [Azure CLI](storage-how-to-use-files-cli.md) of een Azure Storage-SDK, die allemaal een mooie wrapper rond het File REST-protocol bieden in de script-/programmeertaal van uw keuze.  

In de meeste gevallen gebruikt u uw Azure-bestandsshare via het SMB-protocol, omdat dit u de mogelijkheid biedt om de bestaande toepassingen en hulpprogramma's te gebruiken die u verwacht te kunnen gebruiken, maar er zijn diverse redenen waarom het gebruik van de File REST API in plaats van SMB voordelen oplevert, zoals:

- U bladert door uw bestandsshare via de PowerShell Cloud Shell (die geen ondersteuning biedt voor het koppelen van bestandsshares via SMB).
- U profiteert van serverloze resources, zoals [Azure Functions](../../azure-functions/functions-overview.md). 
- U maakt een waarde-toevoegende-service die werkt met veel Azure-bestandsshares, zoals het uitvoeren van back-ups of antivirusscans.

In de volgende voorbeelden ziet u hoe u de Azure PowerShell-module gebruikt voor het bewerken van uw Azure-bestandsshare met het File REST-protocol. De parameter `-Context` wordt gebruikt voor het ophalen van de sleutel van het opslagaccount om de aangegeven acties op de bestandsshare uit te voeren. Als u de sleutel voor het opslagaccount wilt ophalen, moet u over de Azure-rol `Owner` voor het opslagaccount beschikken.

#### <a name="create-directory"></a>Map maken
Als u in de hoofdmap van uw Azure-bestandsshare een nieuwe map wilt maken met de naam *myDirectory*, gebruikt u de cmdlet [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory).

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Bestand uploaden
Om te laten zien hoe u met de cmdlet [Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) een bestand kunt uploaden, moeten we op de scratch-schijf van uw PowerShell Cloud Shell eerst een bestand maken om te uploaden. 

In dit voorbeeld worden de huidige datum en tijd in een nieuw bestand op uw scratch-schijf geplaatst, waarna het bestand naar de bestandsshare wordt geüpload.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
cd "~/CloudDrive/"
Get-Date | Out-File -FilePath "SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Source "SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Als u PowerShell lokaal uitvoert, vervangt u `~/CloudDrive/` door een bestaand pad op uw computer.

Nadat het bestand is geüpload, kunt u de cmdlet [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) gebruiken om te controleren of het bestand daadwerkelijk is geüpload naar uw Azure-bestandsshare. 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```

#### <a name="download-a-file"></a>Bestand downloaden
U kunt de cmdlet [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) gebruiken om een kopie te downloaden van het bestand dat u net hebt geüpload naar de scratch-schijf van uw Cloud Shell.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
    -Path "SampleDownload.txt" `
    -Force `
    -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt" `
    -Destination "SampleDownload.txt"
```

Na het downloaden van het bestand, kunt u `Get-ChildItem` gebruiken om te controleren of het bestand is gedownload naar de scratch-schijf van uw PowerShell Cloud Shell.

```azurepowershell-interactive
Get-ChildItem | Where-Object { $_.Name -eq "SampleDownload.txt" }
``` 

#### <a name="copy-files"></a>Bestanden kopiëren
Een veelvoorkomende taak is het kopiëren van bestanden tussen bestandsshares. U kunt deze functionaliteit proberen door een nieuwe share te maken en het bestand dat u net hebt geüpload met de cmdlet [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy) naar deze nieuwe share te kopiëren. 

```azurepowershell-interactive
$otherShareName = "myshare2"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $otherShareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $otherShareName `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName $shareName `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName $otherShareName `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Als u nu de bestanden in de nieuwe share opvraagt, ziet u het gekopieerde bestand.

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $otherShareName `
    -Path "myDirectory2" 
```

Hoewel de cmdlet `Start-AzStorageFileCopy` handig is voor het ad hoc-bestand tussen Azure-bestandsshares, raden wij voor migraties en grotere gegevensverplaatsingen u aan `robocopy` te gebruiken voor Windows en `rsync` voor macOS en Linux. `robocopy` en `rsync` gebruiken SMB om de gegevensverplaatsingen uit te voeren in plaats van de FileREST-API.

## <a name="create-and-manage-share-snapshots"></a>Momentopnamen van shares maken en beheren
Nog een andere handige taak die u kunt doen met een Azure-bestandsshare is het maken van een momentopname van de share. Een momentopname bevat voor een specifiek moment de actuele inhoud van een Azure-bestandsshare. Momentopnamen van een share zijn vergelijkbaar met technologieën van besturingssystemen die u mogelijk al kent, zoals:

- [Volume Shadow Copy Service (VSS)](/windows/desktop/VSS/volume-shadow-copy-service-portal) voor Windows-bestandssystemen zoals NTFS en ReFS.
- Momentopnamen van [Logical Volume Manager (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) voor Linux-systemen.
- Momentopnamen van [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) voor macOS. 

U kunt een momentopname van een share maken door de methode `Snapshot` uit te voeren op een PowerShell-object voor een bestandsshare, dat u kunt opvragen met de cmdlet [Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare). 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name $shareName
$snapshot = $share.CloudFileShare.Snapshot()
```

### <a name="browse-share-snapshots"></a>Bladeren door momentopnamen van shares
U kunt door de inhoud van de momentopname van een share bladeren door de verwijzing van de momentopname (`$snapshot`) door te geven aan de parameter `-Share` van de cmdlet `Get-AzStorageFile`.

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Momentopnamen van shares opvragen
Gebruik de volgende opdracht om een lijst op te vragen met de momentopnamen die u hebt gemaakt voor de share.

```azurepowershell-interactive
Get-AzStorageShare `
        -Context $storageAcct.Context | `
    Where-Object { $_.Name -eq $shareName -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Bestand herstellen vanuit een share-momentopname
U kunt een bestand herstellen met behulp van de opdracht `Start-AzStorageFileCopy` die we eerder hebben gebruikt. Voor deze snelstartgids verwijderen we eerst het bestand `SampleUpload.txt` dat we eerder hebben geüpload, zodat we het vanuit de momentopname kunnen herstellen.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName $shareName `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Een share-momentopname verwijderen
U kunt een momentopname van een share verwijderen met behulp van de cmdlet [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare), met de variabele met de verwijzing `$snapshot` naar de parameter `-Share`.

```azurepowershell-interactive
Remove-AzStorageShare `
    -Share $snapshot `
    -Confirm:$false `
    -Force
```

## <a name="clean-up-resources"></a>Resources opschonen
Als u klaar bent, kunt u de cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep en alle gerelateerde resources te verwijderen. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

U kunt er ook voor kiezen om resources één voor één te verwijderen:

- De Azure-bestandsshares verwijderen die we voor deze snelstartgids hebben gemaakt:

    ```azurepowershell-interactive
    Get-AzRmStorageShare -StorageAccount $storageAcct | Remove-AzRmStorageShare -Force
    ```

    > [!Note]  
    > U moet alle momentopnamen van shares voor de Azure-bestandsshares verwijderen die u hebt gemaakt voordat u de Azure-bestandsshare verwijdert.

- Het opslagaccount zelf verwijderen (hierdoor worden impliciet de Azure-bestandsshares verwijderd die we hebben gemaakt, evenals andere opslagresources die u hebt gemaakt zoals een Azure Blob-opslagcontainer):

    ```azurepowershell-interactive
    Remove-AzStorageAccount `
        -ResourceGroupName $storageAcct.ResourceGroupName `
        -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Wat is Azure Files?](storage-files-introduction.md)