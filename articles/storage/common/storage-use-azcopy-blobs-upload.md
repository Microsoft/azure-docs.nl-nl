---
title: Bestanden uploaden naar Azure Blob-opslag met behulp van AzCopy V10 toevoegen | Microsoft Docs
description: Dit artikel bevat een verzameling AzCopy-voorbeeld opdrachten die u helpen bij het uploaden van bestanden naar Azure Blob-opslag.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 11d40805cda2ea2e3693c6c93034ae19f1f0fcc0
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96907516"
---
# <a name="upload-files-to-azure-blob-storage-by-using-azcopy-v10"></a>Bestanden uploaden naar Azure Blob Storage met behulp van AzCopy V10 toevoegen

U kunt bestanden en mappen uploaden naar Blob-opslag met behulp van het AzCopy V10 toevoegen-opdracht regel programma. 

Zie de koppelingen die worden weer gegeven in de sectie [volgende stappen](#next-steps) van dit artikel om voor beelden te bekijken van andere typen taken, zoals het downloaden van blobs, synchroniseren met Blob Storage of het kopiëren van blobs tussen accounts.

## <a name="get-started"></a>Aan de slag

Zie het artikel aan de [slag met AzCopy](storage-use-azcopy-v10.md) om AzCopy te downloaden en meer te weten te komen over de manieren waarop u autorisatie referenties kunt opgeven voor de opslag service.

> [!NOTE] 
> In de voor beelden in dit artikel wordt ervan uitgegaan dat u autorisatie referenties hebt ingesteld met behulp van Azure Active Directory (Azure AD).
>
> Als u liever een SAS-token gebruikt om toegang te verlenen tot BLOB-gegevens, kunt u dat token toevoegen aan de bron-URL in elke AzCopy-opdracht. Bijvoorbeeld: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Een container maken

U kunt de opdracht [azcopy](storage-ref-azcopy-make.md) maken gebruiken om een container te creëren.

> [!TIP]
> In dit voor beeld worden padvariabelen met enkele aanhalings tekens (' ') Inge sloten. Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Voorbeeld** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Zie [azcopy maken](storage-ref-azcopy-make.md)voor gedetailleerde naslag documentatie.

## <a name="upload-a-file"></a>Bestand uploaden

Upload een bestand met behulp van de [Kopieer opdracht azcopy](storage-ref-azcopy-copy.md) .

> [!TIP]
> In dit voor beeld worden padvariabelen met enkele aanhalings tekens (' ') Inge sloten. Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

U kunt ook een bestand uploaden met behulp van een Joker teken (*) ergens in het bestandspad of de bestands naam. Bijvoorbeeld: `'C:\myDirectory\*.txt'` , of `C:\my*\*.txt` .

## <a name="upload-a-directory"></a>Een map uploaden

Upload een map met behulp van de [Kopieer opdracht azcopy](storage-ref-azcopy-copy.md) . 

In dit voor beeld wordt een map (en alle bestanden in die map) naar een BLOB-container gekopieerd. Het resultaat is een map in de container met dezelfde naam.

> [!TIP]
> In dit voor beeld worden padvariabelen met enkele aanhalings tekens (' ') Inge sloten. Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Als u wilt kopiëren naar een map in de container, geeft u alleen de naam van die map op in de opdracht reeks.

|    |     |
|--------|-----------|
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Als u de naam opgeeft van een map die niet bestaat in de container, maakt AzCopy een nieuwe map met die naam.

## <a name="upload-directory-contents"></a>Mapinhoud uploaden

Upload de inhoud van een map met behulp van de [Kopieer opdracht azcopy](storage-ref-azcopy-copy.md) . Gebruik het Joker teken (*) om de inhoud te uploaden zonder de bovenliggende map zelf te kopiëren.

> [!TIP]
> In dit voor beeld worden padvariabelen met enkele aanhalings tekens (' ') Inge sloten. Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |


Voeg de `--recursive` vlag toe om bestanden in alle submappen te uploaden.

## <a name="upload-specific-files"></a>Specifieke bestanden uploaden

U kunt specifieke bestanden uploaden met behulp van volledige bestands namen, gedeeltelijke namen met Joker tekens (*) of met datums en tijden.

> [!TIP]
> In deze voor beelden worden Path-argumenten met enkele aanhalings tekens (' '). Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

### <a name="specify-multiple-complete-file-names"></a>Meerdere volledige bestands namen opgeven

Gebruik de [azcopy](storage-ref-azcopy-copy.md) -opdracht copy met de `--include-path` optie. Scheid afzonderlijke bestands namen met een punt komma ( `;` ).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

In dit voor beeld worden de `C:\myDirectory\photos` map en het bestand door AzCopy overgedragen `C:\myDirectory\documents\myFile.txt` . Neem de `--recursive` optie op voor het overdragen van alle bestanden in de `C:\myDirectory\photos` map.

U kunt ook bestanden uitsluiten met behulp van de `--exclude-path` optie. Zie voor meer informatie [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

### <a name="use-wildcard-characters"></a>Joker tekens gebruiken

Gebruik de [azcopy](storage-ref-azcopy-copy.md) -opdracht copy met de `--include-pattern` optie. Geef gedeeltelijke namen op die de joker tekens bevatten. Scheid namen met behulp van een semicolin ( `;` ). 

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

U kunt ook bestanden uitsluiten met behulp van de `--exclude-pattern` optie. Zie voor meer informatie [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

De `--include-pattern` `--exclude-pattern` Opties en zijn alleen van toepassing op bestands namen en niet op het pad.  Als u alle tekst bestanden in een mapstructuur wilt kopiëren, gebruikt u de `–recursive` optie om de volledige mapstructuur op te halen en gebruikt u vervolgens de `–include-pattern` en geeft `*.txt` u op om alle tekst bestanden op te halen.

### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>Bestanden uploaden die na een datum en tijd zijn gewijzigd 

Gebruik de [azcopy](storage-ref-azcopy-copy.md) -opdracht copy met de `--include-after` optie. Geef een datum en tijd op in de ISO-8601-indeling (bijvoorbeeld: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

Zie [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs (Engelstalig) voor meer informatie.

## <a name="upload-with-index-tags"></a>Uploaden met index Tags

U kunt een bestand uploaden en [BLOB-index Tags (preview)](../blobs/storage-manage-find-blobs.md) toevoegen aan de doel-blob.  

Als u Azure AD-autorisatie gebruikt, moet aan uw beveiligingsprincipal de rol van de eigenaar van de [BLOB-gegevens opslag](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) worden toegewezen of moet aan de `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure resource provider-bewerking](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) toestemming worden verleend via een aangepaste Azure-rol. Als u een Shared Access Signature SAS-token gebruikt, moet dat token toegang bieden tot de labels van de BLOB via de `t` SAS-machtiging.

Om tags toe te voegen, gebruikt u de `--blob-tags` optie samen met een URL-gecodeerd sleutel-waardepaar. Als u bijvoorbeeld de sleutel `my tag` en een waarde wilt toevoegen `my tag value` , voegt u toe `--blob-tags='my%20tag=my%20tag%20value'` aan de para meter Destination. 

Scheid meerdere index Tags met behulp van een en-teken ( `&` ).  Als u bijvoorbeeld een sleutel `my second tag` en een waarde wilt toevoegen `my second tag value` , is de teken reeks voor de volledige optie `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

In de volgende voor beelden ziet u hoe u de `--blob-tags` optie gebruikt.

> [!TIP]
> In dit voor beeld worden padvariabelen met enkele aanhalings tekens (' ') Inge sloten. Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

|    |     |
|--------|-----------|
| **Bestand uploaden** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Een map uploaden** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`|
| **Mapinhoud uploaden** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

> [!NOTE]
> Als u een map voor de bron opgeeft, hebben alle blobs die worden gekopieerd naar de bestemming dezelfde tags die u in de opdracht opgeeft.

## <a name="upload-with-optional-flags"></a>Uploaden met optionele vlaggen

U kunt de upload bewerking aanpassen met behulp van optionele vlaggen. Hier volgen enkele voor beelden.

|Scenario|Vlag|
|---|---|
|Upload bestanden als toevoeg-blobs of pagina-blobs.|**--BLOB-type** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
|Upload naar een specifieke toegangslaag (zoals de archieflaag).|**--Block-BLOB-tier** = \[ Geen \| Hot- \| koud \| Archief\]|

Zie [Opties](storage-ref-azcopy-copy.md#options)voor een volledige lijst.

## <a name="next-steps"></a>Volgende stappen

Meer voor beelden vindt u in deze artikelen:

- [Voor beelden: downloaden](storage-use-azcopy-blobs-download.md)
- [Voor beelden: kopiëren tussen accounts](storage-use-azcopy-blobs-copy.md)
- [Voor beelden: synchroniseren](storage-use-azcopy-blobs-synchronize.md)
- [Voor beelden: Amazon S3-buckets](storage-use-azcopy-s3.md)
- [Voor beelden: Azure Files](storage-use-azcopy-files.md)
- [Zelfstudie: on-premises gegevens migreren naar cloudopslag met behulp van AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Configureren, optimaliseren en problemen oplossen in AzCopy](storage-use-azcopy-configure.md)