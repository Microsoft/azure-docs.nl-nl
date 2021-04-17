---
title: Bestanden uploaden naar Azure Blob Storage met behulp van AzCopy v10 | Microsoft Docs
description: Dit artikel bevat een verzameling AzCopy-voorbeeldopdrachten die u helpen bij het uploaden van bestanden naar Azure Blob Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 352497f0f4d23250abe9f84121f358589664002b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502909"
---
# <a name="upload-files-to-azure-blob-storage-by-using-azcopy"></a>Bestanden uploaden naar Azure Blob Storage met behulp van AzCopy

U kunt bestanden en mappen uploaden naar Blob Storage met behulp van het opdrachtregelprogramma AzCopy v10. 

Zie de koppelingen in de sectie Volgende stappen van dit artikel voor voorbeelden van andere soorten taken, zoals [](#next-steps) het downloaden van blobs, het synchroniseren met Blob Storage of het kopiëren van blobs tussen accounts.

## <a name="get-started"></a>Aan de slag

Zie het [artikel Aan de slag met AzCopy om AzCopy](storage-use-azcopy-v10.md) te downloaden en meer informatie te krijgen over de manieren waarop u autorisatiereferenties kunt verstrekken aan de opslagservice.

> [!NOTE] 
> In de voorbeelden in dit artikel wordt ervan uit gegaan dat u autorisatiereferenties hebt opgegeven met behulp van Azure Active Directory (Azure AD).
>
> Als u liever een SAS-token gebruikt om toegang tot blobgegevens te autoriëren, kunt u dat token toevoegen aan de resource-URL in elke AzCopy-opdracht. Bijvoorbeeld: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Een container maken

U kunt de opdracht [azcopy make gebruiken](storage-ref-azcopy-make.md) om een container te maken.

> [!TIP]
> In dit voorbeeld worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

Dit is een tabelvoorbeeld:

**Syntaxis**

`azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'`

**Voorbeeld**

```azcopy
https://mystorageaccount.blob.core.windows.net/mycontainer
```

**Voorbeeld (hiërarchische naamruimte)**

```azcopy
https://mystorageaccount.dfs.core.windows.net/mycontainer
```

Zie [azcopy make](storage-ref-azcopy-make.md)voor gedetailleerde referentiemateriaal.

## <a name="upload-a-file"></a>Een bestand uploaden

Upload een bestand met behulp van de [opdracht azcopy copy.](storage-ref-azcopy-copy.md)

> [!TIP]
> In dit voorbeeld worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

**Syntaxis**

`azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'`

**Voorbeeld**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

**Voorbeeld (hiërarchische naamruimte)**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'
```

U kunt een bestand ook uploaden met behulp van een jokerteken (*) overal in het bestandspad of de bestandsnaam. Bijvoorbeeld: `'C:\myDirectory\*.txt'` of `C:\my*\*.txt` .

## <a name="upload-a-directory"></a>Een map uploaden

Upload een map met behulp van [de opdracht azcopy copy.](storage-ref-azcopy-copy.md) 

In dit voorbeeld wordt een map (en alle bestanden in die map) gekopieerd naar een blobcontainer. Het resultaat is een map in de container met dezelfde naam.

> [!TIP]
> In dit voorbeeld worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ('') in plaats van enkele aanhalingstekens ('').

**Syntaxis**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive`

**Voorbeeld**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive
```

**Voorbeeld (hiërarchische naamruimte)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive
```

Als u wilt kopiëren naar een map in de container, geeft u de naam van die map op in de opdrachtreeks.

**Voorbeeld**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive
```

**Voorbeeld (hiërarchische naamruimte)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive
```

Als u de naam opgeeft van een map die niet bestaat in de container, maakt AzCopy een nieuwe map met die naam.

## <a name="upload-directory-contents"></a>Mapinhoud uploaden

Upload de inhoud van een map met behulp van de [opdracht azcopy copy.](storage-ref-azcopy-copy.md) Gebruik het jokerteken (*) om de inhoud te uploaden zonder de map zelf te kopiëren.

> [!TIP]
> In dit voorbeeld worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ('') in plaats van enkele aanhalingstekens ('').

**Syntaxis**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` 

**Voorbeeld**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'
```

**Voorbeeld (hiërarchische naamruimte)**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'
```

De vlag toevoegen `--recursive` om bestanden in alle subdirecties te uploaden.

## <a name="upload-specific-files"></a>Specifieke bestanden uploaden

U kunt specifieke bestanden uploaden met volledige bestandsnamen, gedeeltelijke namen met jokertekens (*) of met behulp van datums en tijden.

> [!TIP]
> In deze voorbeelden worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

### <a name="specify-multiple-complete-file-names"></a>Meerdere volledige bestandsnamen opgeven

Gebruik de [opdracht azcopy copy](storage-ref-azcopy-copy.md) met de `--include-path` optie . Scheid afzonderlijke bestandsnamen met behulp van een puntkomma ( `;` ).

**Syntaxis** 

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` 

**Voorbeeld**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive'
```

**Voorbeeld (hiërarchische naamruimte)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive'
```

In dit voorbeeld brengt AzCopy de `C:\myDirectory\photos` map en het bestand `C:\myDirectory\documents\myFile.txt` over. Neem de `--recursive` optie op om alle bestanden in de map over te `C:\myDirectory\photos` dragen.

U kunt ook bestanden uitsluiten met behulp van de `--exclude-path` optie . Zie azcopy copy reference docs [(Referentiemateriaal voor azcopy-kopieën)](storage-ref-azcopy-copy.md) voor meer informatie.

### <a name="use-wildcard-characters"></a>Jokertekens gebruiken

Gebruik de [opdracht azcopy copy](storage-ref-azcopy-copy.md) met de `--include-pattern` optie . Geef gedeeltelijke namen op die de jokertekens bevatten. Scheid namen met behulp van een semicolin ( `;` ). 

**Syntaxis**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` 

**Voorbeeld**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'
```

**Voorbeeld (hiërarchische naamruimte)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'
```

U kunt ook bestanden uitsluiten met behulp van de `--exclude-pattern` optie . Zie azcopy copy reference docs [(Referentiemateriaal voor azcopy-kopieën)](storage-ref-azcopy-copy.md) voor meer informatie.

De `--include-pattern` opties en zijn alleen van toepassing op `--exclude-pattern` bestandsnamen en niet op het pad.  Als u alle tekstbestanden in een mapstructuur wilt kopiëren, gebruikt u de optie om de volledige mapstructuur op te halen. Gebruik vervolgens de en geef op om alle tekstbestanden op `–recursive` `–include-pattern` te `*.txt` halen.

### <a name="upload-files-that-were-modified-before-or-after-a-date-and-time"></a>Bestanden uploaden die zijn gewijzigd vóór of na een datum en tijd 

Gebruik de [opdracht azcopy copy](storage-ref-azcopy-copy.md) met de `--include-before` optie of `--include-after` . Geef een datum en tijd op in ISO-8601-indeling (bijvoorbeeld: `2020-08-19T15:04:00Z` ). 

In de volgende voorbeelden worden bestanden geüpload die zijn gewijzigd op of na de opgegeven datum.

**Syntaxis**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` 

**Voorbeeld**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'
```

**Voorbeeld (hiërarchische naamruimte)**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'
```

Zie de documentatie [azcopy copy voor](storage-ref-azcopy-copy.md) gedetailleerde naslag.

## <a name="upload-with-index-tags"></a>Uploaden met indextags

U kunt een bestand uploaden en [blob-indextags (preview) toevoegen](../blobs/storage-manage-find-blobs.md) aan de doel-blob.  

Als u Azure AD-autorisatie gebruikt, moet [](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) aan uw beveiligingsprincipaal de rol Eigenaar van opslagblobgegevens zijn toegewezen, of moet deze machtiging krijgen voor de `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [azure-resourceproviderbewerking](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) via een aangepaste Azure-rol. Als u een SAS-token (Shared Access Signature) gebruikt, moet dat token toegang bieden tot de tags van de blob via de `t` SAS-machtiging.

Als u tags wilt toevoegen, gebruikt u de optie samen met `--blob-tags` een sleutel-waardepaar met URL-codering. Als u bijvoorbeeld de sleutel en een waarde `my tag` wilt `my tag value` toevoegen, voegt u toe `--blob-tags='my%20tag=my%20tag%20value'` aan de doelparameter. 

Scheid meerdere indextags met behulp van een ampersand ( `&` ).  Als u bijvoorbeeld een sleutel en een waarde wilt toevoegen, is `my second tag` `my second tag value` de volledige optiereeks `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

De volgende voorbeelden laten zien hoe u de optie `--blob-tags` gebruikt.

> [!TIP]
> In dit voorbeeld worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

**Bestand uploaden**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

**Een map uploaden**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

**Mapinhoud uploaden**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

> [!NOTE]
> Als u een map voor de bron opgeeft, hebben alle blobs die naar het doel worden gekopieerd dezelfde tags die u in de opdracht opgeeft.

## <a name="upload-with-optional-flags"></a>Uploaden met optionele vlaggen

U kunt uw uploadbewerking aanpassen met behulp van optionele vlaggen. Hier zijn enkele voorbeelden.

|Scenario|Vlag|
|---|---|
|Upload bestanden als toevoeg-blobs of pagina-blobs.|**--blob-type** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
|Upload naar een specifieke toegangslaag (zoals de archieflaag).|**--block-blob-tier** = \[ Geen \| Hot \| Cool \| Archive\]|

Zie opties voor een volledige [lijst.](storage-ref-azcopy-copy.md#options)

## <a name="next-steps"></a>Volgende stappen

Meer voorbeelden vindt u in deze artikelen:

- [Voorbeelden: Downloaden](storage-use-azcopy-blobs-download.md)
- [Voorbeelden: Kopiëren tussen accounts](storage-use-azcopy-blobs-copy.md)
- [Voorbeelden: Synchroniseren](storage-use-azcopy-blobs-synchronize.md)
- [Voorbeelden: Amazon S3-bucket](storage-use-azcopy-s3.md)
- [Voorbeelden: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Voorbeelden: Azure Files](storage-use-azcopy-files.md)
- [Zelfstudie: on-premises gegevens migreren naar cloudopslag met behulp van AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Zie deze artikelen voor het configureren van instellingen, het optimaliseren van prestaties en het oplossen van problemen:

- [AzCopy-configuratie-instellingen](storage-ref-azcopy-configuration-settings.md)
- [De prestaties van AzCopy optimaliseren](storage-use-azcopy-optimize.md)
- [Problemen met AzCopy V10 in Azure Storage met behulp van logboekbestanden](storage-use-azcopy-configure.md)