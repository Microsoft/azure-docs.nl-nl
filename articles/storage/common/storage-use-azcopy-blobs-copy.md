---
title: Blobs kopiëren tussen Azure Storage-accounts met AzCopy V10 toevoegen | Microsoft Docs
description: Dit artikel bevat een verzameling AzCopy-voorbeeld opdrachten waarmee u blobs tussen opslag accounts kunt kopiëren.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: f536e163e3d19d91c150506ab44fdd9cbc02c693
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96907508"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy-v10"></a>Blobs kopiëren tussen Azure-opslag accounts met behulp van AzCopy V10 toevoegen

U kunt blobs, directory's en containers kopiëren tussen opslag accounts met behulp van het AzCopy V10 toevoegen-opdracht regel programma. 

Zie de koppelingen die worden weer gegeven in de sectie [volgende stappen](#next-steps) van dit artikel om voor beelden te bekijken van andere typen taken, zoals het uploaden van bestanden, het downloaden van blobs en synchroniseren met Blob Storage.

AzCopy maakt gebruik van [server-naar-server-](/rest/api/storageservices/put-block-from-url) [api's](/rest/api/storageservices/put-page-from-url), zodat gegevens rechtstreeks tussen opslag servers worden gekopieerd. Deze Kopieer bewerkingen gebruiken de netwerk bandbreedte van uw computer niet.

Zie aan de [slag met AzCopy](storage-use-azcopy-v10.md)om AzCopy te downloaden en meer te weten te komen over de manieren waarop u autorisatie referenties kunt opgeven voor de opslag service. 

## <a name="guidelines"></a>Richtlijnen

Pas de volgende richt lijnen toe op uw AzCopy-opdrachten. 

- Voeg een SAS-token toe aan elke bron-URL. 

  Als u autorisatie referenties opgeeft met behulp van Azure Active Directory (Azure AD), kunt u de SAS-token alleen weglaten van de doel-URL. Zorg ervoor dat u de juiste rollen in uw doel account hebt ingesteld. Zie [optie 1: Azure Active Directory gebruiken](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory). 

  In de voor beelden in dit artikel wordt ervan uitgegaan dat u uw identiteit hebt geverifieerd met behulp van Azure AD, zodat de voor beelden de SAS-tokens van de doel-URL weglaten.

-  Als u naar een Premium Block Blob-opslag account kopieert, laat u de toegangs laag van een BLOB van de Kopieer bewerking weg door de `s2s-preserve-access-tier` in te stellen op `false` (bijvoorbeeld: `--s2s-preserve-access-tier=false` ). Premium Block Blob Storage-accounts bieden geen ondersteuning voor toegangs lagen. 

- Als u naar of van een account met een hiërarchische naam ruimte kopieert, gebruikt u `blob.core.windows.net` in plaats van `dfs.core.windows.net` de URL-syntaxis. [Toegang via meerdere protocollen op Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) biedt u de mogelijkheid om te gebruiken `blob.core.windows.net` . Dit is de enige ondersteunde syntaxis voor accounts voor het kopiëren van accounts. 

- U kunt de door Voer van Kopieer bewerkingen verhogen door de waarde van de `AZCOPY_CONCURRENCY_VALUE` omgevings variabele in te stellen. Zie de [door Voer optimaliseren](storage-use-azcopy-configure.md#optimize-throughput)voor meer informatie. 

- Als de bron-blobs index Tags hebben en u deze tags wilt behouden, moet u ze opnieuw Toep assen op de doel-blobs. Zie de sectie [blobs kopiëren naar een ander opslag account met index Tags](#copy-between-accounts-and-add-index-tags) in dit artikel voor meer informatie over het instellen van index Tags.

## <a name="copy-a-blob"></a>Een BLOB kopiëren

Kopieer een BLOB naar een ander opslag account met behulp van de [Kopieer opdracht azcopy](storage-ref-azcopy-copy.md) . 

> [!TIP]
> In dit voor beeld worden padvariabelen met enkele aanhalings tekens (' ') Inge sloten. Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

De kopieerbewerking is synchroon, dus wanneer de opdracht wordt geretourneerd, geeft dit aan dat alle bestanden zijn gekopieerd. 

## <a name="copy-a-directory"></a>Een map kopiëren

Kopieer een map naar een ander opslag account met behulp van de [Kopieer opdracht azcopy](storage-ref-azcopy-copy.md) . 

> [!TIP]
> In dit voor beeld worden padvariabelen met enkele aanhalings tekens (' ') Inge sloten. Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

De kopieerbewerking is synchroon, dus wanneer de opdracht wordt geretourneerd, geeft dit aan dat alle bestanden zijn gekopieerd.

## <a name="copy-a-container"></a>Een container kopiëren

Kopieer een container naar een ander opslag account met behulp van de [Kopieer opdracht azcopy](storage-ref-azcopy-copy.md) .

> [!TIP]
> In dit voor beeld worden padvariabelen met enkele aanhalings tekens (' ') Inge sloten. Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

De kopieerbewerking is synchroon, dus wanneer de opdracht wordt geretourneerd, geeft dit aan dat alle bestanden zijn gekopieerd.

## <a name="copy-containers-directories-and-blobs"></a>Containers, directory's en blobs kopiëren

Kopieer alle containers, directory's en blobs naar een ander opslag account met behulp van de [Kopieer opdracht azcopy](storage-ref-azcopy-copy.md) .

> [!TIP]
> In dit voor beeld worden padvariabelen met enkele aanhalings tekens (' ') Inge sloten. Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

De kopieerbewerking is synchroon, dus wanneer de opdracht wordt geretourneerd, geeft dit aan dat alle bestanden zijn gekopieerd.

<a id="copy-between-accounts-and-add-index-tags"></a>

## <a name="copy-blobs-and-add-index-tags"></a>Blobs kopiëren en index Tags toevoegen

Kopieer blobs naar een ander opslag account en voeg [BLOB-index Tags (preview)](../blobs/storage-manage-find-blobs.md) toe aan de doel-blob.

Als u Azure AD-autorisatie gebruikt, moet aan uw beveiligingsprincipal de rol van de eigenaar van de [BLOB-gegevens opslag](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) worden toegewezen of moet aan de `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure resource provider-bewerking](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) toestemming worden verleend via een aangepaste Azure-rol. Als u een Shared Access Signature SAS-token gebruikt, moet dat token toegang bieden tot de labels van de BLOB via de `t` SAS-machtiging.

Om tags toe te voegen, gebruikt u de `--blob-tags` optie samen met een URL-gecodeerd sleutel-waardepaar. 

Als u bijvoorbeeld de sleutel `my tag` en een waarde wilt toevoegen `my tag value` , voegt u toe `--blob-tags='my%20tag=my%20tag%20value'` aan de para meter Destination. 

Scheid meerdere index Tags met behulp van een en-teken ( `&` ).  Als u bijvoorbeeld een sleutel `my second tag` en een waarde wilt toevoegen `my second tag value` , is de teken reeks voor de volledige optie `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

In de volgende voor beelden ziet u hoe u de `--blob-tags` optie gebruikt.

> [!TIP]
> In deze voor beelden worden Path-argumenten met enkele aanhalings tekens (' '). Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

|    |     |
|--------|-----------|
| **Blob** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Directory** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Container** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Account** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

De kopieerbewerking is synchroon, dus wanneer de opdracht wordt geretourneerd, geeft dit aan dat alle bestanden zijn gekopieerd.

> [!NOTE]
> Als u een map, container of account voor de bron opgeeft, hebben alle blobs die zijn gekopieerd naar de bestemming dezelfde tags die u in de opdracht opgeeft.

## <a name="copy-with-optional-flags"></a>Kopiëren met optionele vlaggen

U kunt uw Kopieer bewerking aanpassen met behulp van optionele vlaggen. Hier volgen enkele voor beelden.

|Scenario|Vlag|
|---|---|
|Blobs kopiëren als blok-, pagina-of toevoeg-blobs.|**--BLOB-type** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
|Kopiëren naar een specifieke toegangs laag (zoals de laag archief).|**--Block-BLOB-tier** = \[ Geen \| Hot- \| koud \| Archief\]|
|Bestanden automatisch decomprimeren.|**--decomprimeren** = \[ gzip- \| Deflate\]|

Zie [Opties](storage-ref-azcopy-copy.md#options)voor een volledige lijst. 

## <a name="next-steps"></a>Volgende stappen

Meer voor beelden vindt u in deze artikelen:

- [Voor beelden: uploaden](storage-use-azcopy-blobs-upload.md)
- [Voor beelden: downloaden](storage-use-azcopy-blobs-download.md)
- [Voor beelden: synchroniseren](storage-use-azcopy-blobs-synchronize.md)
- [Voor beelden: Amazon S3-buckets](storage-use-azcopy-s3.md)
- [Voor beelden: Azure Files](storage-use-azcopy-files.md)
- [Zelfstudie: on-premises gegevens migreren naar cloudopslag met behulp van AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Configureren, optimaliseren en problemen oplossen in AzCopy](storage-use-azcopy-configure.md)