---
title: Gegevens overdragen naar of van Azure Files met behulp van AzCopy v10 | Microsoft Docs
description: Gegevens overdragen met AzCopy en bestandsopslag. AzCopy is een opdrachtregelprogramma voor het kopiëren van blobs of bestanden van of naar een opslagaccount. Gebruik AzCopy met Azure Files.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 04bf84c5dc8a63929900f1bc95d7274074ef9d5a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498353"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Gegevens overdragen met AzCopy en bestandsopslag 

AzCopy is een opdrachtregelprogramma dat u kunt gebruiken om bestanden van of naar een opslagaccount te kopiëren. Dit artikel bevat voorbeeldopdrachten die werken met Azure Files.

Zie voordat u begint het artikel Aan de slag [met AzCopy om AzCopy](storage-use-azcopy-v10.md) te downloaden en vertrouwd te raken met het hulpprogramma.

> [!TIP]
> De voorbeelden in dit artikel plaatsen padargumenten met enkele aanhalingstekens (''). Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ('') in plaats van enkele aanhalingstekens ('').

## <a name="create-file-shares"></a>Bestandsshares maken

U kunt de opdracht [azcopy make gebruiken](storage-ref-azcopy-make.md) om een bestands share te maken. In het voorbeeld in deze sectie wordt een bestands share met de naam `myfileshare` gemaakt.

**Syntaxis**

`azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'`

**Voorbeeld**

```azcopy
azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

Zie [azcopy make](storage-ref-azcopy-make.md)voor gedetailleerde referentie docs.

## <a name="upload-files"></a>Bestanden uploaden

U kunt de [opdracht azcopy copy gebruiken](storage-ref-azcopy-copy.md) om bestanden en mappen te uploaden vanaf uw lokale computer.

Deze sectie bevat de volgende voorbeelden:

> [!div class="checklist"]
> * Een bestand uploaden
> * Een map uploaden
> * De inhoud van een map uploaden
> * Een specifiek bestand uploaden

> [!TIP]
> U kunt uw uploadbewerking aanpassen met behulp van optionele vlaggen. Hier zijn enkele voorbeelden.
>
> |Scenario|Vlag|
> |---|---|
> |Kopieer toegangsbeheerlijsten (ACL's) samen met de bestanden.|**--preserve-smb-permissions** = \[ waar \| onwaar\]|
> |Kopieer de SMB-eigenschapsgegevens samen met de bestanden.|**--preserve-smb-info** = \[ waar \| onwaar\]|
> 
> Zie opties voor een volledige [lijst.](storage-ref-azcopy-copy.md#options)

> [!NOTE]
> AzCopy berekent en opgeslagen niet automatisch de md5-hashcode van het bestand. Als u wilt dat AzCopy dit doet, moet u de vlag aan `--put-md5` elke kopieeropdracht appen. Op die manier berekent AzCopy, wanneer het bestand wordt gedownload, een MD5-hash voor gedownloade gegevens en wordt gecontroleerd of de MD5-hash die is opgeslagen in de eigenschap van het bestand overeenkomt met de berekende `Content-md5` hash.

### <a name="upload-a-file"></a>Een bestand uploaden

**Syntaxis**

`azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'`

**Voorbeeld**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

U kunt een bestand ook uploaden met behulp van een jokerteken (*) overal in het bestandspad of de bestandsnaam. Bijvoorbeeld: `'C:\myDirectory\*.txt'` of `C:\my*\*.txt` .

### <a name="upload-a-directory"></a>Een map uploaden

In dit voorbeeld wordt een map (met alle bestanden in de map) gekopieerd naar een bestandsshare. Het resultaat is een map in de bestandsshare met dezelfde naam.

**Syntaxis**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Voorbeeld**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive
```

Als u wilt kopiëren naar een map in de bestands share, geeft u de naam van die map op in de opdrachtreeks.

**Voorbeeld**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive
```

Als u de naam opgeeft van een map die niet bestaat in de bestands share, maakt AzCopy een nieuwe map met die naam.

### <a name="upload-the-contents-of-a-directory"></a>De inhoud van een map uploaden

U kunt de inhoud van een map uploaden zonder de map zelf te kopiëren met behulp van het jokerteken (*).

**Syntaxis**

`azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>`

**Voorbeeld**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"
```

> [!NOTE]
> De vlag toevoegen `--recursive` om bestanden in alle subdirecties te uploaden.

### <a name="upload-specific-files"></a>Specifieke bestanden uploaden

U kunt specifieke bestanden uploaden met volledige bestandsnamen, gedeeltelijke namen met jokertekens (*) of met behulp van datums en tijden.

#### <a name="specify-multiple-complete-file-names"></a>Meerdere volledige bestandsnamen opgeven

Gebruik de [opdracht azcopy copy](storage-ref-azcopy-copy.md) met de `--include-path` optie . Afzonderlijke bestandsnamen scheiden met behulp van een puntkomma ( `;` ).

**Syntaxis**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>`

**Voorbeeld**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'
```

In dit voorbeeld brengt AzCopy de `C:\myDirectory\photos` map en het bestand `C:\myDirectory\documents\myFile.txt` over. U moet de optie voor `--recursive` het overdragen van alle bestanden in de map `C:\myDirectory\photos` opnemen.

U kunt ook bestanden uitsluiten met behulp van de `--exclude-path` optie . Zie azcopy copy reference docs [(Referentie docs voor azcopy-kopieën)](storage-ref-azcopy-copy.md) voor meer informatie.

#### <a name="use-wildcard-characters"></a>Jokertekens gebruiken

Gebruik de [opdracht azcopy copy](storage-ref-azcopy-copy.md) met de `--include-pattern` optie . Geef gedeeltelijke namen op die de jokertekens bevatten. Scheid namen met behulp van een puntkomma ( `;` ).

**Syntaxis**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**Voorbeeld**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'
```

U kunt ook bestanden uitsluiten met behulp van de `--exclude-pattern` optie . Zie azcopy copy reference docs [(Referentie docs voor azcopy-kopieën)](storage-ref-azcopy-copy.md) voor meer informatie.

De `--include-pattern` opties en zijn alleen van toepassing op `--exclude-pattern` bestandsnamen en niet op het pad.  Als u alle tekstbestanden in een mapstructuur wilt kopiëren, gebruikt u de optie om de volledige mapstructuur op te halen. Gebruik vervolgens de en geef op om alle tekstbestanden op `--recursive` `--include-pattern` te `*.txt` halen.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>Bestanden uploaden die na een datum en tijd zijn gewijzigd 

Gebruik de [opdracht azcopy copy](storage-ref-azcopy-copy.md) met de `--include-after` optie . Geef een datum en tijd op in ISO 8601-indeling (bijvoorbeeld: `2020-08-19T15:04:00Z` ). 

**Syntaxis**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>'  --include-after <Date-Time-in-ISO-8601-format>`

**Voorbeeld**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-after '2020-08-19T15:04:00Z'
```

Zie de documentatie [azcopy copy voor](storage-ref-azcopy-copy.md) gedetailleerde naslag.

## <a name="download-files"></a>Bestanden downloaden

U kunt de [opdracht azcopy copy](storage-ref-azcopy-copy.md) gebruiken om bestanden, mappen en bestands shares naar uw lokale computer te downloaden.

Deze sectie bevat de volgende voorbeelden:

> [!div class="checklist"]
> * Een bestand downloaden
> * Een map downloaden
> * De inhoud van een map downloaden
> * Specifieke bestanden downloaden

> [!TIP]
> U kunt uw downloadbewerking aanpassen met behulp van optionele vlaggen. Hier zijn enkele voorbeelden.
>
> |Scenario|Vlag|
> |---|---|
> |Kopieer toegangsbeheerlijsten (ACL's) samen met de bestanden.|**--preserve-smb-permissions** = \[ waar \| onwaar\]|
> |Kopieer de SMB-eigenschapsgegevens samen met de bestanden.|**--preserve-smb-info** = \[ waar \| onwaar\]|
> |Bestanden automatisch decomprimeren.|**--decompress**|
> 
> Zie opties voor een volledige [lijst.](storage-ref-azcopy-copy.md#options)

> [!NOTE]
> Als de eigenschapswaarde van een bestand een hash bevat, berekent AzCopy een MD5-hash voor gedownloade gegevens en wordt gecontroleerd of de MD5-hash die is opgeslagen in de eigenschap van het bestand overeenkomt met de berekende `Content-md5` `Content-md5` hash. Als deze waarden niet overeenkomen, mislukt de download, tenzij u dit gedrag overschrijven door toe te passen of aan de `--check-md5=NoCheck` `--check-md5=LogOnly` kopieeropdracht.

### <a name="download-a-file"></a>Een bestand downloaden

**Syntaxis**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'`

**Voorbeeld**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'
```

### <a name="download-a-directory"></a>Een map downloaden

**Syntaxis**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive`

**Voorbeeld**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive
```

Dit voorbeeld resulteert in een map met `C:\myDirectory\myFileShareDirectory` de naam die alle gedownloade bestanden bevat.

### <a name="download-the-contents-of-a-directory"></a>De inhoud van een map downloaden

U kunt de inhoud van een map downloaden zonder de map waar de inhoud in zit te kopiëren, door het jokerteken (*) te gebruiken.

**Syntaxis**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'`

**Voorbeeld**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'
```

> [!NOTE]
> De vlag toevoegen `--recursive` om bestanden in alle subdirecties te downloaden.

### <a name="download-specific-files"></a>Specifieke bestanden downloaden

U kunt specifieke bestanden downloaden met volledige bestandsnamen, gedeeltelijke namen met jokertekens (*) of met behulp van datums en tijden.

#### <a name="specify-multiple-complete-file-names"></a>Meerdere volledige bestandsnamen opgeven

Gebruik de [opdracht azcopy copy](storage-ref-azcopy-copy.md) met de `--include-path` optie . Afzonderlijke bestandsnamen scheiden met behulp van een puntkomma ( `;` ).

**Syntaxis**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>`

**Voorbeeld**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive
```

In dit voorbeeld brengt AzCopy de `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` map en het bestand `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` over. Neem de `--recursive` optie op om alle bestanden over te dragen in de map `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` .

U kunt ook bestanden uitsluiten met behulp van de `--exclude-path` optie . Zie azcopy copy reference docs [(Referentie docs voor azcopy-kopieën)](storage-ref-azcopy-copy.md) voor meer informatie.

#### <a name="use-wildcard-characters"></a>Jokertekens gebruiken

Gebruik de [opdracht azcopy copy](storage-ref-azcopy-copy.md) met de `--include-pattern` optie . Geef gedeeltelijke namen op die de jokertekens bevatten. Scheid namen met behulp van een puntkomma ( `;` ).

**Syntaxis**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**Voorbeeld**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'
```

U kunt ook bestanden uitsluiten met behulp van de `--exclude-pattern` optie . Zie azcopy copy reference docs [(Referentie docs voor azcopy-kopieën)](storage-ref-azcopy-copy.md) voor meer informatie.

De `--include-pattern` opties en zijn alleen van toepassing op `--exclude-pattern` bestandsnamen en niet op het pad.  Als u alle tekstbestanden in een mapstructuur wilt kopiëren, gebruikt u de optie om de volledige mapstructuur op te halen. Gebruik vervolgens de en geef op om alle tekstbestanden op `--recursive` `--include-pattern` te `*.txt` halen.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>Bestanden downloaden die na een datum en tijd zijn gewijzigd 

Gebruik de [opdracht azcopy copy](storage-ref-azcopy-copy.md) met de `--include-after` optie . Geef een datum en tijd op in ISO-8601-indeling (bijvoorbeeld: `2020-08-19T15:04:00Z` ). 

**Syntaxis**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>/*<SAS-token>' '<local-directory-path>'  --include-after <Date-Time-in-ISO-8601-format>`

**Voorbeeld**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/*?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory' --include-after '2020-08-19T15:04:00Z'
```

Zie de documentatie [azcopy copy voor](storage-ref-azcopy-copy.md) gedetailleerde naslag.

#### <a name="download-from-a-share-snapshot"></a>Downloaden van een momentopname van een share

U kunt een specifieke versie van een bestand of  map downloaden door te verwijzen naar de datum/tijd-waarde van een momentopname van een share. Zie Overzicht van momentopnamen van share voor Azure Files voor meer informatie [over momentopnamen van Azure Files.](../files/storage-snapshots-files.md) 

**Syntaxis**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path-or-directory-name><SAS-token>&sharesnapshot=<DateTime-of-snapshot>' '<local-file-or-directory-path>'`

**Voorbeeld (een bestand downloaden)**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'
```

**Voorbeeld (een map downloaden)**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory'  --recursive
```

## <a name="copy-files-between-storage-accounts"></a>Bestanden kopiëren tussen opslagaccounts

U kunt AzCopy gebruiken om bestanden naar andere opslagaccounts te kopiëren. De kopieerbewerking is synchroon, dus wanneer de opdracht wordt geretourneerd, geeft dit aan dat alle bestanden zijn gekopieerd.

AzCopy maakt gebruik [van server-naar-server-API's,](/rest/api/storageservices/put-block-from-url) [](/rest/api/storageservices/put-page-from-url)zodat gegevens rechtstreeks tussen opslagservers worden gekopieerd. Deze kopieerbewerkingen maken geen gebruik van de netwerkbandbreedte van uw computer. U kunt de doorvoer van deze bewerkingen verhogen door de waarde van de `AZCOPY_CONCURRENCY_VALUE` omgevingsvariabele in te stellen. Zie Gelijktijdigheid verhogen [voor meer informatie.](storage-use-azcopy-optimize.md#increase-concurrency)

U kunt ook specifieke versies van een bestand kopiëren door te verwijzen naar de **datum/tijd-waarde** van een momentopname van een share. Zie Overzicht van momentopnamen van share voor Azure Files voor meer informatie [over momentopnamen van Azure Files.](../files/storage-snapshots-files.md) 

Deze sectie bevat de volgende voorbeelden:

> [!div class="checklist"]
> * Een bestand kopiëren naar een ander opslagaccount
> * Een map kopiëren naar een ander opslagaccount
> * Een bestands share kopiëren naar een ander opslagaccount
> * Alle bestandsshares, mappen en bestanden kopiëren naar een ander opslagaccount

> [!TIP]
> U kunt uw kopieerbewerking aanpassen met behulp van optionele vlaggen. Hier zijn enkele voorbeelden.
>
> |Scenario|Vlag|
> |---|---|
> |Kopieer toegangsbeheerlijsten (ACL's) samen met de bestanden.|**--preserve-smb-permissions** = \[ waar \| onwaar\]|
> |Kopieer de SMB-eigenschapsgegevens samen met de bestanden.|**--preserve-smb-info** = \[ waar \| onwaar\]|
> 
> Zie opties voor een volledige [lijst.](storage-ref-azcopy-copy.md#options)

### <a name="copy-a-file-to-another-storage-account"></a>Een bestand kopiëren naar een ander opslagaccount

**Syntaxis**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'`

**Voorbeeld**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'
```

**Voorbeeld (momentopname delen)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'
```

### <a name="copy-a-directory-to-another-storage-account"></a>Een map kopiëren naar een ander opslagaccount

**Syntaxis**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Voorbeeld**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

**Voorbeeld (momentopname delen)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="copy-a-file-share-to-another-storage-account"></a>Een bestands share kopiëren naar een ander opslagaccount

**Syntaxis**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Voorbeeld**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D
```

**Voorbeeld (momentopname delen)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Alle bestandsshares, mappen en bestanden kopiëren naar een ander opslagaccount

**Syntaxis**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'`

**Voorbeeld**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

**Voorbeeld (momentopname delen)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

## <a name="synchronize-files"></a>Bestanden synchroniseren

U kunt de inhoud van een bestands share synchroniseren met een andere bestands share. U kunt ook de inhoud van een map in een bestands share synchroniseren met de inhoud van een map die zich in een andere bestands share bevindt. Synchronisatie is één manier. Met andere woorden, u kiest welke van deze twee eindpunten de bron is en welke het doel is. Synchronisatie maakt ook gebruik van server-naar-server-API's.

> [!NOTE]
> Dit scenario wordt momenteel alleen ondersteund voor accounts die geen hiërarchische naamruimte hebben. De huidige versie van AzCopy wordt niet gesynchroniseerd tussen Azure Files en Blob Storage.

Met [de synchronisatieopdracht](storage-ref-azcopy-sync.md) worden bestandsnamen en laatst gewijzigde tijdstempels vergeleken. Stel de optionele vlag in op de waarde of om bestanden in de doelmap te verwijderen als deze bestanden niet meer `--delete-destination` `true` bestaan in de `prompt` bronmap.

Als u de vlag `--delete-destination` in stelt op , verwijdert `true` AzCopy bestanden zonder een prompt op te geven. Als u wilt dat er een prompt wordt weergegeven voordat AzCopy een bestand verwijdert, stelt u de `--delete-destination` vlag in op `prompt` .

> [!TIP]
> U kunt uw synchronisatiebewerking aanpassen met behulp van optionele vlaggen. Hier zijn enkele voorbeelden.
>
> |Scenario|Vlag|
> |---|---|
> |Kopieer toegangsbeheerlijsten (ACL's) samen met de bestanden.|**--preserve-smb-permissions** = \[ waar \| onwaar\]|
> |Kopieer de SMB-eigenschapsgegevens samen met de bestanden.|**--preserve-smb-info** = \[ waar \| onwaar\]|
> |Sluit bestanden uit op basis van een patroon.|**--exclude-path**|
> |Geef op hoe gedetailleerd u uw synchronisatielogboekgegevens wilt laten zijn.|**--log-niveau** = \[ \|WAARSCHUWINGSFOUT \| INFO \| GEEN\]|
> 
> Zie opties voor een volledige [lijst.](storage-ref-azcopy-sync.md#options)

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Een bestands share bijwerken met wijzigingen in een andere bestands share

De eerste bestands share die wordt weergegeven in deze opdracht is de bron. De tweede is het doel.

**Syntaxis**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Voorbeeld**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Een map bijwerken met wijzigingen in een map in een andere bestands share

De eerste map die wordt weergegeven in deze opdracht is de bron. De tweede is het doel.

**Syntaxis**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive`

**Voorbeeld**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>Een bestands share bijwerken om overeen te komen met de inhoud van een momentopname van een share

De eerste bestands share die wordt weergegeven in deze opdracht is de bron. Aan het einde van de URI moet u de tekenreeks `&sharesnapshot=` toevoegen, gevolgd door de **datum/tijd-waarde** van de momentopname.

**Syntaxis**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Voorbeeld**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
``` 

Zie Overzicht van momentopnamen van share voor meer informatie over momentopnamen van [shares voor Azure Files.](../files/storage-snapshots-files.md)

## <a name="next-steps"></a>Volgende stappen

Meer voorbeelden vindt u in een van deze artikelen:

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen](storage-use-azcopy-v10.md#transfer-data)

Zie deze artikelen voor het configureren van instellingen, het optimaliseren van prestaties en het oplossen van problemen:

- [AzCopy-configuratie-instellingen](storage-ref-azcopy-configuration-settings.md)
- [De prestaties van AzCopy optimaliseren](storage-use-azcopy-optimize.md)
- [Problemen met AzCopy V10 in Azure Storage oplossen met behulp van logboekbestanden](storage-use-azcopy-configure.md)

