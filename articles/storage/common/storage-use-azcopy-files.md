---
title: Gegevens overdragen van of naar Azure Files met behulp van AzCopy V10 toevoegen | Microsoft Docs
description: Gegevens overdragen met AzCopy en file storage. AzCopy is een opdracht regel programma voor het kopiëren van blobs of bestanden naar of van een opslag account. Gebruik AzCopy met Azure Files.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 2cca8a93330e5ddd965d27532895ed1d6702c123
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179643"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Gegevens overdragen met AzCopy en bestandsopslag 

AzCopy is een opdracht regel programma dat u kunt gebruiken om bestanden te kopiëren naar of van een opslag account. Dit artikel bevat voorbeeld opdrachten die samen werken met Azure Files.

Voordat u begint, raadpleegt u het artikel aan de [slag met AzCopy](storage-use-azcopy-v10.md) om AzCopy te downloaden en vertrouwd te raken met het hulp programma.

> [!TIP]
> In de voor beelden in dit artikel worden Path-argumenten met enkele aanhalings tekens (' '). Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

## <a name="create-file-shares"></a>Bestandsshares maken

U kunt de [azcopy](storage-ref-azcopy-make.md) maken om een bestands share te maken. In het voor beeld in deze sectie wordt een bestands share met de naam gemaakt `myfileshare` .

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'` |
| **Voorbeeld** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Zie [azcopy maken](storage-ref-azcopy-make.md)voor gedetailleerde naslag documentatie.

## <a name="upload-files"></a>Bestanden uploaden

U kunt de azcopy-opdracht [kopiëren](storage-ref-azcopy-copy.md) gebruiken om bestanden en mappen te uploaden van uw lokale computer.

Deze sectie bevat de volgende voor beelden:

> [!div class="checklist"]
> * Een bestand uploaden
> * Een map uploaden
> * De inhoud van een map uploaden
> * Een specifiek bestand uploaden

> [!TIP]
> U kunt de upload bewerking aanpassen met behulp van optionele vlaggen. Hier volgen enkele voor beelden.
>
> |Scenario|Vlag|
> |---|---|
> |Kopieer toegangs beheer lijsten (Acl's) samen met de bestanden.|**--behoud-SMB-machtigingen** = \[ waar \| Onwaar\]|
> |Informatie over SMB-eigenschappen samen met de bestanden kopiëren.|**--behoud-SMB-info** = \[ waar \| Onwaar\]|
> 
> Zie [Opties](storage-ref-azcopy-copy.md#options)voor een volledige lijst.

> [!NOTE]
> De MD5-hash-code van het bestand wordt niet automatisch door AzCopy berekend en opgeslagen. Als u dit wilt doen, voegt u de markering toe `--put-md5` aan elke Kopieer opdracht. Op die manier wordt, wanneer het bestand wordt gedownload, AzCopy een MD5-hash voor gedownloade gegevens berekend en wordt gecontroleerd of de MD5-hash die is opgeslagen in de eigenschap van het bestand `Content-md5` overeenkomt met de berekende hash.

### <a name="upload-a-file"></a>Een bestand uploaden

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

U kunt ook een bestand uploaden met behulp van een Joker teken (*) ergens in het bestandspad of de bestands naam. Bijvoorbeeld: `'C:\myDirectory\*.txt'` , of `C:\my*\*.txt` .

### <a name="upload-a-directory"></a>Een map uploaden

In dit voorbeeld wordt een map (met alle bestanden in de map) gekopieerd naar een bestandsshare. Het resultaat is een map in de bestandsshare met dezelfde naam.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Als u wilt kopiëren naar een map in de bestands share, geeft u alleen de naam van die map op in de opdracht teken reeks.

|    |     |
|--------|-----------|
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Als u de naam opgeeft van een map die niet voor komt in de bestands share, maakt AzCopy een nieuwe map met die naam.

### <a name="upload-the-contents-of-a-directory"></a>De inhoud van een map uploaden

U kunt de inhoud van een map uploaden zonder de bovenliggende map zelf te kopiëren met behulp van het Joker teken (*).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Voeg de `--recursive` vlag toe om bestanden te uploaden in alle submappen.

### <a name="upload-specific-files"></a>Specifieke bestanden uploaden

U kunt specifieke bestanden uploaden met behulp van volledige bestands namen, gedeeltelijke namen met Joker tekens (*) of met datums en tijden.

#### <a name="specify-multiple-complete-file-names"></a>Meerdere volledige bestands namen opgeven

Gebruik de [azcopy](storage-ref-azcopy-copy.md) -opdracht copy met de `--include-path` optie. Scheid afzonderlijke bestands namen met een punt komma ( `;` ).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

In dit voor beeld worden de `C:\myDirectory\photos` map en het bestand door AzCopy overgedragen `C:\myDirectory\documents\myFile.txt` . U moet de `--recursive` optie voor het overdragen van alle bestanden in de `C:\myDirectory\photos` map toevoegen.

U kunt ook bestanden uitsluiten met behulp van de `--exclude-path` optie. Zie voor meer informatie [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Joker tekens gebruiken

Gebruik de [azcopy](storage-ref-azcopy-copy.md) -opdracht copy met de `--include-pattern` optie. Geef gedeeltelijke namen op die de joker tekens bevatten. Scheid namen met een punt komma ( `;` ).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

U kunt ook bestanden uitsluiten met behulp van de `--exclude-pattern` optie. Zie voor meer informatie [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

De `--include-pattern` `--exclude-pattern` Opties en zijn alleen van toepassing op bestands namen en niet op het pad.  Als u alle tekst bestanden in een mapstructuur wilt kopiëren, gebruikt u de `--recursive` optie om de volledige mapstructuur op te halen en gebruikt u vervolgens de `--include-pattern` en geeft `*.txt` u op om alle tekst bestanden op te halen.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>Bestanden uploaden die na een datum en tijd zijn gewijzigd 

Gebruik de [azcopy](storage-ref-azcopy-copy.md) -opdracht copy met de `--include-after` optie. Geef een datum en tijd op in de ISO 8601-indeling (bijvoorbeeld: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-after '2020-08-19T15:04:00Z'` |

Zie [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs (Engelstalig) voor meer informatie.

## <a name="download-files"></a>Bestanden downloaden

U kunt de azcopy-opdracht [kopiëren](storage-ref-azcopy-copy.md) gebruiken om bestanden, mappen en bestands shares te downloaden naar uw lokale computer.

Deze sectie bevat de volgende voor beelden:

> [!div class="checklist"]
> * Een bestand downloaden
> * Een directory downloaden
> * De inhoud van een map downloaden
> * Specifieke bestanden downloaden

> [!TIP]
> U kunt uw download bewerking aanpassen door optionele vlaggen te gebruiken. Hier volgen enkele voor beelden.
>
> |Scenario|Vlag|
> |---|---|
> |Kopieer toegangs beheer lijsten (Acl's) samen met de bestanden.|**--behoud-SMB-machtigingen** = \[ waar \| Onwaar\]|
> |Informatie over SMB-eigenschappen samen met de bestanden kopiëren.|**--behoud-SMB-info** = \[ waar \| Onwaar\]|
> |Bestanden automatisch decomprimeren.|**--decomprimeren**|
> 
> Zie [Opties](storage-ref-azcopy-copy.md#options)voor een volledige lijst.

> [!NOTE]
> Als de `Content-md5` eigenschaps waarde van een bestand een hash bevat, wordt in AzCopy een MD5-hash voor gedownloade gegevens berekend en wordt gecontroleerd of de MD5-hash die is opgeslagen in de eigenschap van het bestand `Content-md5` overeenkomt met de berekende hash. Als deze waarden niet overeenkomen, mislukt de down load tenzij u dit gedrag overschrijft door toe te voegen `--check-md5=NoCheck` of `--check-md5=LogOnly` aan de Kopieer opdracht.

### <a name="download-a-file"></a>Een bestand downloaden

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Een directory downloaden

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Dit voor beeld resulteert in een map met `C:\myDirectory\myFileShareDirectory` de naam die alle gedownloade bestanden bevat.

### <a name="download-the-contents-of-a-directory"></a>De inhoud van een map downloaden

U kunt de inhoud van een map downloaden zonder de map waar de inhoud in zit te kopiëren, door het jokerteken (*) te gebruiken.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Voeg de `--recursive` vlag toe om bestanden in alle submappen te downloaden.

### <a name="download-specific-files"></a>Specifieke bestanden downloaden

U kunt specifieke bestanden downloaden met behulp van volledige bestands namen, gedeeltelijke namen met Joker tekens (*) of met datums en tijden.

#### <a name="specify-multiple-complete-file-names"></a>Meerdere volledige bestands namen opgeven

Gebruik de [azcopy](storage-ref-azcopy-copy.md) -opdracht copy met de `--include-path` optie. Scheid afzonderlijke bestands namen met een punt komma ( `;` ).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

In dit voor beeld worden de `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` map en het bestand door AzCopy overgedragen `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` . Neem de `--recursive` optie op voor het overdragen van alle bestanden in de `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` map.

U kunt ook bestanden uitsluiten met behulp van de `--exclude-path` optie. Zie voor meer informatie [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Joker tekens gebruiken

Gebruik de [azcopy](storage-ref-azcopy-copy.md) -opdracht copy met de `--include-pattern` optie. Geef gedeeltelijke namen op die de joker tekens bevatten. Scheid namen met een punt komma ( `;` ).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

U kunt ook bestanden uitsluiten met behulp van de `--exclude-pattern` optie. Zie voor meer informatie [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

De `--include-pattern` `--exclude-pattern` Opties en zijn alleen van toepassing op bestands namen en niet op het pad.  Als u alle tekst bestanden in een mapstructuur wilt kopiëren, gebruikt u de `--recursive` optie om de volledige mapstructuur op te halen en gebruikt u vervolgens de `--include-pattern` en geeft `*.txt` u op om alle tekst bestanden op te halen.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>Bestanden downloaden die na een datum en tijd zijn gewijzigd 

Gebruik de [azcopy](storage-ref-azcopy-copy.md) -opdracht copy met de `--include-after` optie. Geef een datum en tijd op in de ISO-8601-indeling (bijvoorbeeld: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>/*<SAS-token>' '<local-directory-path>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/*?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory' --include-after '2020-08-19T15:04:00Z'` |


Zie [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs (Engelstalig) voor meer informatie.

#### <a name="download-from-a-share-snapshot"></a>Downloaden van een moment opname van een share

U kunt een specifieke versie van een bestand of map downloaden door te verwijzen naar de waarde **DateTime** van een moment opname van een share. Zie [overzicht van moment opnamen van shares voor Azure files voor](../files/storage-snapshots-files.md)meer informatie over moment opnamen van shares. 

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path-or-directory-name><SAS-token>&sharesnapshot=<DateTime-of-snapshot>' '<local-file-or-directory-path>'` |
| **Voor beeld** (een bestand downloaden) | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |
| **Voor beeld** (een directory downloaden) | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory'  --recursive`|


## <a name="copy-files-between-storage-accounts"></a>Bestanden kopiëren tussen opslagaccounts

U kunt AzCopy gebruiken om bestanden naar andere opslag accounts te kopiëren. De kopieerbewerking is synchroon, dus wanneer de opdracht wordt geretourneerd, geeft dit aan dat alle bestanden zijn gekopieerd.

AzCopy maakt gebruik van [server-naar-server-](/rest/api/storageservices/put-block-from-url) [api's](/rest/api/storageservices/put-page-from-url), zodat gegevens rechtstreeks tussen opslag servers worden gekopieerd. Deze Kopieer bewerkingen gebruiken de netwerk bandbreedte van uw computer niet. U kunt de door Voer van deze bewerkingen verhogen door de waarde van de `AZCOPY_CONCURRENCY_VALUE` omgevings variabele in te stellen. Zie de [door Voer optimaliseren](storage-use-azcopy-configure.md#optimize-throughput)voor meer informatie.

U kunt ook specifieke versies van een bestand kopiëren door te verwijzen naar de waarde **DateTime** van een moment opname van een share. Zie [overzicht van moment opnamen van shares voor Azure files voor](../files/storage-snapshots-files.md)meer informatie over moment opnamen van shares. 

Deze sectie bevat de volgende voor beelden:

> [!div class="checklist"]
> * Een bestand kopiëren naar een ander opslag account
> * Een map kopiëren naar een ander opslag account
> * Een bestands share kopiëren naar een ander opslag account
> * Alle bestandsshares, mappen en bestanden kopiëren naar een ander opslagaccount

> [!TIP]
> U kunt uw Kopieer bewerking aanpassen met behulp van optionele vlaggen. Hier volgen enkele voor beelden.
>
> |Scenario|Vlag|
> |---|---|
> |Kopieer toegangs beheer lijsten (Acl's) samen met de bestanden.|**--behoud-SMB-machtigingen** = \[ waar \| Onwaar\]|
> |Informatie over SMB-eigenschappen samen met de bestanden kopiëren.|**--behoud-SMB-info** = \[ waar \| Onwaar\]|
> 
> Zie [Opties](storage-ref-azcopy-copy.md#options)voor een volledige lijst.

### <a name="copy-a-file-to-another-storage-account"></a>Een bestand kopiëren naar een ander opslag account

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |
| **Voor beeld** (moment opname delen) | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Een map kopiëren naar een ander opslag account

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |
| **Voor beeld** (moment opname delen) | `azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |


### <a name="copy-a-file-share-to-another-storage-account"></a>Een bestands share kopiëren naar een ander opslag account

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |
| **Voor beeld** (moment opname delen) | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |


### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Alle bestandsshares, mappen en bestanden kopiëren naar een ander opslagaccount

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |
| **Voor beeld** (moment opname delen) | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |


## <a name="synchronize-files"></a>Bestanden synchroniseren

U kunt de inhoud van een bestands share synchroniseren met een andere bestands share. U kunt ook de inhoud van een map in een bestands share synchroniseren met de inhoud van een map die zich in een andere bestands share bevindt. Synchronisatie is een manier. Met andere woorden, u kunt kiezen welke van deze twee eind punten de bron is en welke het doel is. Synchronisatie gebruikt ook server-naar-server-Api's.

> [!NOTE]
> Dit scenario wordt momenteel alleen ondersteund voor accounts die geen hiërarchische naam ruimte hebben. De huidige release van AzCopy synchroniseert niet tussen Azure Files en Blob Storage.

Met de opdracht [Sync](storage-ref-azcopy-sync.md) worden bestands namen en tijds tempels met de laatste wijziging vergeleken. Stel de `--delete-destination` optionele vlag in op een waarde van `true` of `prompt` om bestanden in de doelmap te verwijderen als deze bestanden niet meer in de bron directory aanwezig zijn.

Als u de `--delete-destination` vlag instelt op `true` , worden bestanden door AzCopy verwijderd zonder dat er een prompt wordt gegeven. Als u wilt dat er een prompt wordt weer gegeven voordat AzCopy een bestand verwijdert, stelt u de `--delete-destination` vlag in op `prompt` .

> [!TIP]
> U kunt de synchronisatie bewerking aanpassen met behulp van optionele vlaggen. Hier volgen enkele voor beelden.
>
> |Scenario|Vlag|
> |---|---|
> |Kopieer toegangs beheer lijsten (Acl's) samen met de bestanden.|**--behoud-SMB-machtigingen** = \[ waar \| Onwaar\]|
> |Informatie over SMB-eigenschappen samen met de bestanden kopiëren.|**--behoud-SMB-info** = \[ waar \| Onwaar\]|
> |Bestanden uitsluiten op basis van een patroon.|**--exclude-pad**|
> |Geef op hoe gedetailleerd u de logboek vermeldingen voor synchronisatie wilt maken.|**--logboek niveau** = \[ \|fout gegevens voor waarschuwing \| \| geen\]|
> 
> Zie [Opties](storage-ref-azcopy-sync.md#options)voor een volledige lijst.

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Een bestands share bijwerken met wijzigingen in een andere bestands share

De eerste bestands share die wordt weer gegeven in deze opdracht is de bron. De tweede is de bestemming.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Voorbeeld** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Een Directory bijwerken met wijzigingen in een map in een andere bestands share

De eerste map die wordt weer gegeven in deze opdracht is de bron. De tweede is de bestemming.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **Voorbeeld** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>Een bestands share bijwerken zodat deze overeenkomt met de inhoud van een moment opname van een share

De eerste bestands share die wordt weer gegeven in deze opdracht is de bron. Voeg aan het einde van de URI de teken reeks toe `&sharesnapshot=` gevolgd door de **datum/tijd** -waarde van de moment opname. 

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Voorbeeld** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

Zie [overzicht van moment opnamen van shares voor Azure files voor](../files/storage-snapshots-files.md)meer informatie over moment opnamen van shares.

## <a name="next-steps"></a>Volgende stappen

Meer voor beelden vindt u in een van deze artikelen:

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)

- [Gegevens overdragen](storage-use-azcopy-v10.md#transfer-data)

- [Configureren, optimaliseren en problemen oplossen in AzCopy](storage-use-azcopy-configure.md)
