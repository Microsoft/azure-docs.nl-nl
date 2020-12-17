---
title: Blobs downloaden van Azure Blob-opslag met behulp van AzCopy V10 toevoegen | Microsoft Docs
description: Dit artikel bevat een verzameling AzCopy-voorbeeld opdrachten waarmee u blobs kunt downloaden vanuit Azure Blob-opslag.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 382adb36712fbf4bee83044c8b2d096223eb6269
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630034"
---
# <a name="download-blobs-from-azure-blob-storage-by-using-azcopy-v10"></a>Blobs downloaden van Azure Blob-opslag met behulp van AzCopy V10 toevoegen

U kunt blobs en directory's vanuit Blob Storage downloaden met behulp van het AzCopy V10 toevoegen-opdracht regel programma. 

Zie de koppelingen die worden weer gegeven in de sectie [volgende stappen](#next-steps) van dit artikel om voor beelden te bekijken van andere typen taken, zoals het uploaden van bestanden, synchroniseren met Blob Storage of het kopiëren van blobs tussen accounts.

## <a name="get-started"></a>Aan de slag

Zie het artikel aan de [slag met AzCopy](storage-use-azcopy-v10.md) om AzCopy te downloaden en meer te weten te komen over de manieren waarop u autorisatie referenties kunt opgeven voor de opslag service.

> [!NOTE] 
> In de voor beelden in dit artikel wordt ervan uitgegaan dat u autorisatie referenties hebt ingesteld met behulp van Azure Active Directory (Azure AD).
>
> Als u liever een SAS-token gebruikt om toegang te verlenen tot BLOB-gegevens, kunt u dat token toevoegen aan de bron-URL in elke AzCopy-opdracht. Bijvoorbeeld: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="download-a-blob"></a>Een blob downloaden

Down load een blob met behulp van de [Kopieer opdracht azcopy](storage-ref-azcopy-copy.md) .

> [!TIP]
> In dit voor beeld worden padvariabelen met enkele aanhalings tekens (' ') Inge sloten. Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> Als de `Content-md5` eigenschaps waarde van een BLOB een hash bevat, wordt in AzCopy een MD5-hash voor gedownloade gegevens berekend en wordt gecontroleerd of de MD5-hash die is opgeslagen in de eigenschap van de BLOB `Content-md5` overeenkomt met de berekende hash. Als deze waarden niet overeenkomen, mislukt de down load tenzij u dit gedrag overschrijft door toe te voegen `--check-md5=NoCheck` of `--check-md5=LogOnly` aan de Kopieer opdracht.

## <a name="download-a-directory"></a>Een directory downloaden

Down load een map met behulp van de [Kopieer opdracht azcopy](storage-ref-azcopy-copy.md) .

> [!TIP]
> In dit voor beeld worden padvariabelen met enkele aanhalings tekens (' ') Inge sloten. Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Dit voor beeld resulteert in een map met de naam `C:\myDirectory\myBlobDirectory` die alle gedownloade blobs bevat.

## <a name="download-directory-contents"></a>Inhoud van map downloaden

U kunt de inhoud van een map downloaden zonder de map waar de inhoud in zit te kopiëren, door het jokerteken (*) te gebruiken.

> [!TIP]
> In dit voor beeld worden padvariabelen met enkele aanhalings tekens (' ') Inge sloten. Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

> [!NOTE]
> Dit scenario wordt momenteel alleen ondersteund voor accounts die geen hiërarchische naam ruimte hebben.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

Voeg de `--recursive` vlag toe om bestanden in alle submappen te downloaden.

## <a name="download-specific-blobs"></a>Specifieke blobs downloaden

U kunt specifieke blobs downloaden met behulp van volledige bestands namen, gedeeltelijke namen met Joker tekens (*) of met datums en tijden. 

> [!TIP]
> In deze voor beelden worden Path-argumenten met enkele aanhalings tekens (' '). Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

#### <a name="specify-multiple-complete-blob-names"></a>Meerdere volledige BLOB-namen opgeven

Gebruik de [azcopy](storage-ref-azcopy-copy.md) -opdracht copy met de `--include-path` optie. Scheid afzonderlijke BLOB-namen met behulp van een semicolin ( `;` ).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

In dit voor beeld worden de `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` map en het bestand door AzCopy overgedragen `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` . Neem de `--recursive` optie voor het overdragen van alle blobs in de `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` map op.

U kunt ook blobs uitsluiten met behulp van de `--exclude-path` optie. Zie voor meer informatie [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Joker tekens gebruiken

Gebruik de [azcopy](storage-ref-azcopy-copy.md) -opdracht copy met de `--include-pattern` optie. Geef gedeeltelijke namen op die de joker tekens bevatten. Scheid namen met behulp van een semicolin ( `;` ).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

U kunt ook blobs uitsluiten met behulp van de `--exclude-pattern` optie. Zie voor meer informatie [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

De `--include-pattern` `--exclude-pattern` Opties en zijn alleen van toepassing op BLOB-namen en niet op het pad.  Als u alle tekst bestanden (blobs) wilt kopiëren die zich in een mapstructuur bevinden, gebruikt u de `–recursive` optie om de volledige mapstructuur op te halen en gebruikt u vervolgens de `–include-pattern` en geeft `*.txt` u vervolgens alle tekst bestanden op.

#### <a name="download-blobs-that-were-modified-before-or-after-a-date-and-time"></a>Blobs downloaden die vóór of na een datum en tijd zijn gewijzigd 

Gebruik de [azcopy](storage-ref-azcopy-copy.md) -opdracht copy met `--include-before` de `--include-after` optie of. Geef een datum en tijd op in de ISO-8601-indeling (bijvoorbeeld: `2020-08-19T15:04:00Z` ). 

De volgende voor beelden downloaden bestanden die zijn gewijzigd op of na de opgegeven datum.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |

Zie [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs (Engelstalig) voor meer informatie.

#### <a name="download-previous-versions-of-a-blob"></a>Eerdere versies van een BLOB downloaden

Als u BLOB- [versie beheer](../blobs/versioning-enable.md)hebt ingeschakeld, kunt u een of meer eerdere versies van een BLOB downloaden. 

Maak eerst een tekst bestand dat een lijst met versie- [id's](../blobs/versioning-overview.md)bevat. Elke versie-ID moet op een afzonderlijke regel worden weer gegeven. Bijvoorbeeld: 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

Gebruik vervolgens de azcopy-opdracht [copy](storage-ref-azcopy-copy.md) met de `--list-of-versions` optie. Geef de locatie van het tekst bestand op dat de lijst met versies bevat (bijvoorbeeld: `D:\\list-of-versions.txt` ).  

#### <a name="download-a-blob-snapshot"></a>Een blob-momentopname downloaden

U kunt een [BLOB-moment opname](/azure/storage/blobs/snapshots-overview) downloaden door te verwijzen naar de waarde **DateTime** van een BLOB-moment opname. 

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>?sharesnapshot=<DateTime-of-snapshot>' '<local-file-path>'` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> Als u een SAS-token gebruikt om toegang te verlenen tot blobgegevens, voegt u de **datum/tijd** van de moment opname na het SAS-token toe. Bijvoorbeeld: `'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z'`.

## <a name="download-with-optional-flags"></a>Downloaden met optionele vlaggen

U kunt uw download bewerking aanpassen door optionele vlaggen te gebruiken. Hier volgen enkele voor beelden.

|Scenario|Vlag|
|---|---|
|Bestanden automatisch decomprimeren.|**--decomprimeren**|
|Geef op hoe gedetailleerd de logboek vermeldingen voor het kopiëren moeten zijn.|**--logboek niveau** = \[ \|fout gegevens voor waarschuwing \| \| geen\]|
|Geef op of en hoe de conflicterende bestanden en blobs op de bestemming moeten worden overschreven.|**--overschrijven** = \[ True \| False \| ifSourceNewer- \| prompt\]|

Zie [Opties](storage-ref-azcopy-copy.md#options)voor een volledige lijst.

## <a name="next-steps"></a>Volgende stappen

Meer voor beelden vindt u in deze artikelen:

- [Voorbeelden: Uploaden](storage-use-azcopy-blobs-upload.md)
- [Voor beelden: kopiëren tussen account](storage-use-azcopy-blobs-copy.md)
- [Voorbeelden: Synchroniseren](storage-use-azcopy-blobs-synchronize.md)
- [Voorbeelden: Amazon S3-bucket](storage-use-azcopy-s3.md)
- [Voor beelden: Azure Files](storage-use-azcopy-files.md)
- [Zelfstudie: on-premises gegevens migreren naar cloudopslag met behulp van AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Configureren, optimaliseren en problemen oplossen in AzCopy](storage-use-azcopy-configure.md)