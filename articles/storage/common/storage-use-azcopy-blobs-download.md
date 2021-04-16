---
title: Blobs downloaden uit Azure Blob Storage met behulp van AzCopy v10 | Microsoft Docs
description: Dit artikel bevat een verzameling AzCopy-voorbeeldopdrachten die u helpen bij het downloaden van blobs uit Azure Blob Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 56bb36cfda9d0cf1a8882950c862a73ad1e77898
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502943"
---
# <a name="download-blobs-from-azure-blob-storage-by-using-azcopy"></a>Blobs downloaden uit Azure Blob Storage met behulp van AzCopy

U kunt blobs en directories downloaden uit Blob Storage met behulp van het opdrachtregelprogramma AzCopy v10. 

Zie de koppelingen in de sectie Volgende stappen van dit artikel voor voorbeelden van andere soorten taken, [](#next-steps) zoals het uploaden van bestanden, synchroniseren met Blob Storage of het kopiëren van blobs tussen accounts.

## <a name="get-started"></a>Aan de slag

Zie het [artikel Aan de slag met AzCopy om AzCopy](storage-use-azcopy-v10.md) te downloaden en meer informatie te krijgen over de manieren waarop u autorisatiereferenties kunt verstrekken aan de opslagservice.

> [!NOTE] 
> In de voorbeelden in dit artikel wordt ervan uit gegaan dat u autorisatiereferenties hebt opgegeven met behulp van Azure Active Directory (Azure AD).
>
> Als u liever een SAS-token gebruikt om toegang tot blobgegevens te autoriëren, kunt u dat token toevoegen aan de resource-URL in elke AzCopy-opdracht. Bijvoorbeeld: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="download-a-blob"></a>Een blob downloaden

Download een blob met behulp van de [opdracht azcopy copy.](storage-ref-azcopy-copy.md)

> [!TIP]
> In dit voorbeeld worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

**Syntaxis**

``azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'``

**Voorbeeld**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'
```

**Voorbeeld (hiërarchische naamruimte)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'
```

> [!NOTE]
> Als de eigenschapswaarde van een blob een hash bevat, berekent AzCopy een MD5-hash voor gedownloade gegevens en wordt gecontroleerd of de MD5-hash die is opgeslagen in de eigenschap van de blob overeenkomt met de berekende `Content-md5` `Content-md5` hash. Als deze waarden niet overeenkomen, mislukt de download, tenzij u dit gedrag overschrijven door toe te passen of aan de `--check-md5=NoCheck` `--check-md5=LogOnly` kopieeropdracht.

## <a name="download-a-directory"></a>Een map downloaden

Download een map met behulp van de [opdracht azcopy copy.](storage-ref-azcopy-copy.md)

> [!TIP]
> In dit voorbeeld worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ('') in plaats van enkele aanhalingstekens ('').

**Syntaxis**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive`

**Voorbeeld**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive
```

**Voorbeeld (hiërarchische naamruimte)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive
```

Dit voorbeeld resulteert in een map met `C:\myDirectory\myBlobDirectory` de naam die alle gedownloade blobs bevat.

## <a name="download-directory-contents"></a>Mapinhoud downloaden

U kunt de inhoud van een map downloaden zonder de map waar de inhoud in zit te kopiëren, door het jokerteken (*) te gebruiken.

> [!TIP]
> In dit voorbeeld worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ('') in plaats van enkele aanhalingstekens ('').

> [!NOTE]
> Dit scenario wordt momenteel alleen ondersteund voor accounts die geen hiërarchische naamruimte hebben.

**Syntaxis**

`azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'`

**Voorbeeld**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'
```

De vlag toevoegen `--recursive` om bestanden in alle subdirectorieën te downloaden.

## <a name="download-specific-blobs"></a>Specifieke blobs downloaden

U kunt specifieke blobs downloaden met behulp van volledige bestandsnamen, gedeeltelijke namen met jokertekens (*) of met behulp van datums en tijden. 

> [!TIP]
> In deze voorbeelden worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ('') in plaats van enkele aanhalingstekens ('').

#### <a name="specify-multiple-complete-blob-names"></a>Meerdere volledige blobnamen opgeven

Gebruik de [opdracht azcopy copy](storage-ref-azcopy-copy.md) met de `--include-path` optie . Afzonderlijke blobnamen scheiden met behulp van een semicolin ( `;` ).

**Syntaxis**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>`

**Voorbeeld**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive
```

**Voorbeeld (hiërarchische naamruimte)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive
```

In dit voorbeeld brengt AzCopy de `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` map en het bestand `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` over. Neem de `--recursive` optie op om alle blobs over te dragen in de map `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` .

U kunt blobs ook uitsluiten met behulp van de `--exclude-path` optie . Zie azcopy copy reference docs [(Referentiemateriaal voor azcopy-kopieën)](storage-ref-azcopy-copy.md) voor meer informatie.

#### <a name="use-wildcard-characters"></a>Jokertekens gebruiken

Gebruik de [opdracht azcopy copy](storage-ref-azcopy-copy.md) met de `--include-pattern` optie . Geef gedeeltelijke namen op die de jokertekens bevatten. Scheid namen met behulp van een semicolin ( `;` ).

**Syntaxis**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**Voorbeeld**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'
```

**Voorbeeld (hiërarchische naamruimte)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'
```

U kunt blobs ook uitsluiten met behulp van de `--exclude-pattern` optie . Zie azcopy copy reference docs [(Referentiemateriaal voor azcopy-kopieën)](storage-ref-azcopy-copy.md) voor meer informatie.

De `--include-pattern` opties en zijn alleen van toepassing op `--exclude-pattern` blobnamen en niet op het pad.  Als u alle tekstbestanden (blobs) wilt kopiëren die aanwezig zijn in een mapstructuur, gebruikt u de optie om de volledige mapstructuur op te halen. Gebruik vervolgens de en om alle tekstbestanden op te `–recursive` `–include-pattern` `*.txt` halen.

#### <a name="download-blobs-that-were-modified-before-or-after-a-date-and-time"></a>Blobs downloaden die zijn gewijzigd vóór of na een datum en tijd 

Gebruik de [opdracht azcopy copy](storage-ref-azcopy-copy.md) met de `--include-before` optie of `--include-after` . Geef een datum en tijd op in ISO-8601-indeling (bijvoorbeeld: `2020-08-19T15:04:00Z` ). 

In de volgende voorbeelden worden bestanden gedownload die zijn gewijzigd op of na de opgegeven datum.

**Syntaxis**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>`

**Voorbeeld**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'
```

**Voorbeeld (hiërarchische naamruimte)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'
```

Zie de documentatie [azcopy copy reference](storage-ref-azcopy-copy.md) voor gedetailleerde naslag.

#### <a name="download-previous-versions-of-a-blob"></a>Vorige versies van een blob downloaden

Als u blobversies [hebt ingeschakeld,](../blobs/versioning-enable.md)kunt u een of meer eerdere versies van een blob downloaden. 

Maak eerst een tekstbestand dat een lijst met [versie-ID's bevat.](../blobs/versioning-overview.md) Elke versie-id moet op een afzonderlijke regel worden weergegeven. Bijvoorbeeld: 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

Gebruik vervolgens de [opdracht azcopy copy](storage-ref-azcopy-copy.md) met de `--list-of-versions` optie . Geef de locatie op van het tekstbestand dat de lijst met versies bevat (bijvoorbeeld: `D:\\list-of-versions.txt` ).  

#### <a name="download-a-blob-snapshot"></a>Een blob-momentopname downloaden

U kunt een [blob-momentopname downloaden](../blobs/snapshots-overview.md) door te verwijzen naar de **datum/tijd-waarde** van een blob-momentopname. 

**Syntaxis**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>?sharesnapshot=<DateTime-of-snapshot>' '<local-file-path>'`

**Voorbeeld**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'
```

**Voorbeeld (hiërarchische naamruimte)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'
```

> [!NOTE]
> Als u een SAS-token gebruikt om toegang tot blobgegevens te autoreren, moet u de datum/tijd van de momentopname na het SAS-token samenvoegen.  Bijvoorbeeld: `'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z'`.

## <a name="download-with-optional-flags"></a>Downloaden met optionele vlaggen

U kunt uw downloadbewerking aanpassen met behulp van optionele vlaggen. Hier zijn enkele voorbeelden.

|Scenario|Vlag|
|---|---|
|Bestanden automatisch decomprimeren.|**--decompress**|
|Geef op hoe gedetailleerd uw kopielogboekgegevens moeten zijn.|**--log-level** = \[ \|WAARSCHUWINGSFOUT \| INFO \| GEEN\]|
|Geef op of en hoe de conflicterende bestanden en blobs op de bestemming moeten worden overschreven.|**--overwrite** = \[ true \| false \| ifSourceNewer \| prompt\]|

Zie opties voor een volledige [lijst.](storage-ref-azcopy-copy.md#options)

## <a name="next-steps"></a>Volgende stappen

Meer voorbeelden vindt u in deze artikelen:

- [Voorbeelden: Uploaden](storage-use-azcopy-blobs-upload.md)
- [Voorbeelden: Kopiëren tussen account](storage-use-azcopy-blobs-copy.md)
- [Voorbeelden: Synchroniseren](storage-use-azcopy-blobs-synchronize.md)
- [Voorbeelden: Amazon S3-bucket](storage-use-azcopy-s3.md)
- [Voorbeelden: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Voorbeelden: Azure Files](storage-use-azcopy-files.md)
- [Zelfstudie: on-premises gegevens migreren naar cloudopslag met behulp van AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Zie deze artikelen voor het configureren van instellingen, het optimaliseren van prestaties en het oplossen van problemen:

- [AzCopy-configuratie-instellingen](storage-ref-azcopy-configuration-settings.md)
- [De prestaties van AzCopy optimaliseren](storage-use-azcopy-optimize.md)
- [Problemen met AzCopy V10 in Azure Storage met behulp van logboekbestanden](storage-use-azcopy-configure.md)
