---
title: 'Power shell gebruiken voor het beheren van gegevens: Azure Data Lake Storage Gen2'
description: Gebruik Power shell-cmdlets voor het beheren van mappen en bestanden in opslag accounts waarvoor een hiërarchische naam ruimte is ingeschakeld.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 552d53ff0257105ff61397e281504c5270512319
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573860"
---
# <a name="use-powershell-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Power shell gebruiken voor het beheren van mappen en bestanden in Azure Data Lake Storage Gen2

In dit artikel leest u hoe u Power shell gebruikt voor het maken en beheren van mappen en bestanden in opslag accounts met een hiërarchische naam ruimte.

Zie [Power shell gebruiken voor het beheren van acl's in azure data Lake Storage Gen2](data-lake-storage-acl-powershell.md)voor meer informatie over het ophalen, instellen en bijwerken van de acl's (toegangs beheer lijsten) van mappen en bestanden.

[Naslag informatie](/powershell/module/Az.Storage/)  |  Toewijzing van gen1 [naar Gen2](#gen1-gen2-map)  |  [Feedback geven](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

- Een opslag account waarvoor een hiërarchische naam ruimte is ingeschakeld. Volg [deze](create-data-lake-storage-account.md) instructies om er een te maken.

- .NET Framework is 4.7.2 of hoger geïnstalleerd. Zie [.NET Framework downloaden](https://dotnet.microsoft.com/download/dotnet-framework).

- Power shell-versie `5.1` of hoger.

## <a name="install-the-powershell-module"></a>De PowerShell-module installeren

1. Controleer of de versie van Power shell die is geïnstalleerd, `5.1` of hoger is met behulp van de volgende opdracht.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```

   Zie [bestaande Windows Power shell upgraden](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell) voor informatie over het bijwerken van uw versie van Power shell

2. Installeer **AZ. Storage** -module.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Zie [de module Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie over het installeren van Power shell-modules.

## <a name="connect-to-the-account"></a>Verbinding maken met het account

Kies hoe u wilt dat uw opdrachten autorisatie aanvragen voor het opslag account. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-azure-ad"></a>Optie 1: autorisatie verkrijgen met behulp van Azure Active Directory (Azure AD)

Met deze methode zorgt het systeem ervoor dat uw gebruikers account beschikt over de juiste toewijzings-en ACL-machtigingen van Azure op rollen gebaseerde toegangs beheer (Azure RBAC).

1. Open een Windows Power shell-opdracht venster en meld u vervolgens aan bij uw Azure-abonnement met de `Connect-AzAccount` opdracht en volg de instructies op het scherm.

   ```powershell
   Connect-AzAccount
   ```

2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslag account waarin u directory's wilt maken en beheren. In dit voor beeld vervangt `<subscription-id>` u de waarde van de tijdelijke aanduiding door de id van uw abonnement.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ``` 

3. De context van het opslag account ophalen.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
   ```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Optie 2: autorisatie verkrijgen met behulp van de sleutel van het opslag account

Met deze methode controleert het systeem geen Azure RBAC-of ACL-machtigingen. Haal de context van het opslag account op met behulp van een account sleutel.

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
```

## <a name="create-a-container"></a>Een container maken

Een container fungeert als bestands systeem voor uw bestanden. U kunt er een maken met behulp van de- `New-AzStorageContainer` cmdlet. 

In dit voor beeld wordt een container gemaakt met de naam `my-file-system` .

```powershell
$filesystemName = "my-file-system"
New-AzStorageContainer -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Een map maken

Een directory verwijzing maken met behulp van de- `New-AzDataLakeGen2Item` cmdlet. 

In dit voor beeld wordt een map met de naam toegevoegd `my-directory` aan een container.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

In dit voor beeld wordt dezelfde map toegevoegd, maar worden ook de machtigingen, umask, eigenschaps waarden en meta gegevens waarden ingesteld. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Mapeigenschappen weer geven

In dit voor beeld wordt een Directory opgehaald met behulp van de `Get-AzDataLakeGen2Item` -cmdlet en worden de eigenschaps waarden vervolgens naar de console afgedrukt.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```

> [!NOTE]
> Als u de hoofdmap van de container wilt ophalen, laat u de `-Path` para meter weg.

## <a name="rename-or-move-a-directory"></a>Een map een andere naam geven of verplaatsen

Wijzig de naam van een map of verplaats deze met behulp van de `Move-AzDataLakeGen2Item` cmdlet.

In dit voor beeld wordt de naam van een map gewijzigd van de naam `my-directory` in de naam `my-new-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Gebruik de `-Force` para meter als u wilt overschrijven zonder prompts.

In dit voor beeld wordt een map verplaatst met de naam `my-directory` naar een submap met de `my-directory-2` naam `my-subdirectory` . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Een map verwijderen

Een directory verwijderen met de `Remove-AzDataLakeGen2Item` cmdlet.

In dit voor beeld wordt een map met de naam verwijderd `my-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

U kunt de `-Force` para meter gebruiken om het bestand zonder prompt te verwijderen.

## <a name="download-from-a-directory"></a>Downloaden uit een directory

Down load een bestand vanuit een directory met de `Get-AzDataLakeGen2ItemContent` cmdlet.

In dit voor beeld wordt een bestand gedownload met de naam `upload.txt` van een map met de naam `my-directory` .

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Mapinhoud weergeven

De inhoud van een directory weer geven met behulp van de- `Get-AzDataLakeGen2ChildItem` cmdlet. U kunt de optionele para meter gebruiken `-OutputUserPrincipalName` om de naam (in plaats van de object-id) van de gebruikers op te halen.

In dit voor beeld wordt de inhoud weer gegeven van een map met de naam `my-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

In het volgende voor beeld worden de `ACL` `Permissions` Eigenschappen,, `Group` en `Owner` van elk item in de map weer gegeven. De `-FetchProperty` para meter is vereist om waarden op te halen voor de `ACL` eigenschap. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

> [!NOTE]
> Als u de inhoud van de hoofdmap van de container wilt weer geven, laat u de `-Path` para meter weg.

## <a name="upload-a-file-to-a-directory"></a>Een bestand uploaden naar een map

Een bestand uploaden naar een map met behulp van de- `New-AzDataLakeGen2Item` cmdlet.

In dit voor beeld wordt een bestand geüpload `upload.txt` met de naam naar een map met de naam `my-directory` . 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

In dit voor beeld wordt hetzelfde bestand geüpload, maar worden vervolgens de machtigingen, umask, eigenschaps waarden en meta gegevens waarden van het doel bestand ingesteld. In dit voor beeld worden deze waarden ook afgedrukt naar de-console.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

> [!NOTE]
> Als u een bestand wilt uploaden naar de hoofdmap van de container, laat u de `-Path` para meter weg.

## <a name="show-file-properties"></a>Bestands eigenschappen weer geven

In dit voor beeld wordt een bestand opgehaald met behulp van de `Get-AzDataLakeGen2Item` -cmdlet en worden de eigenschaps waarden vervolgens naar de console afgedrukt.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
```

## <a name="delete-a-file"></a>Een bestand verwijderen

Een bestand verwijderen met behulp van de- `Remove-AzDataLakeGen2Item` cmdlet.

In dit voor beeld wordt een bestand met de naam verwijderd `upload.txt` . 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

U kunt de `-Force` para meter gebruiken om het bestand zonder prompt te verwijderen.

<a id="gen1-gen2-map"></a>

## <a name="gen1-to-gen2-mapping"></a>Toewijzing van gen1 naar Gen2

In de volgende tabel ziet u hoe de cmdlets die worden gebruikt voor Data Lake Storage Gen1 worden toegewezen aan de cmdlets voor Data Lake Storage Gen2.

|Data Lake Storage Gen1-cmdlet| Data Lake Storage Gen2-cmdlet| Notities |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Standaard worden in de cmdlet Get-AzDataLakeGen2ChildItem alleen de onderliggende items van het eerste niveau weer gegeven. Met de para meter-recursief worden onderliggende items recursief weer gegeven. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|De uitvoer items van de cmdlet Get-AzDataLakeGen2Item hebben de volgende eigenschappen: ACL, eigenaar, groep, machtiging.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|De Get-AzDataLakeGen2FileContent-cmdlet bestand inhoud downloaden naar een lokaal bestand.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|Met deze cmdlet wordt de nieuwe bestands inhoud van een lokaal bestand geüpload.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|Met de cmdlet Update-AzDataLakeGen2Item wordt één enkel item bijgewerkt en niet recursief. Als u recursief wilt bijwerken, kunt u items weer geven met behulp van de cmdlet Get-AzDataLakeStoreChildItem en vervolgens pijp lijn naar de Update-AzDataLakeGen2Item-cmdlet.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|Met de cmdlet Get-AzDataLakeGen2Item wordt een fout gerapporteerd als het item niet bestaat.|

## <a name="see-also"></a>Zie ook

- [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [PowerShell Storage-cmdlets](/powershell/module/az.storage)
