---
title: Blobs kopiëren tussen Azure-opslagaccounts met AzCopy v10 | Microsoft Docs
description: Dit artikel bevat een verzameling AzCopy-voorbeeldopdrachten die u helpen bij het kopiëren van blobs tussen opslagaccounts.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: bfdc91ac8f4ce618052cc78e76b27e8bdeabeb77
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502977"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy"></a>Blobs kopiëren tussen Azure-opslagaccounts met behulp van AzCopy

U kunt blobs, directories en containers kopiëren tussen opslagaccounts met behulp van het opdrachtregelprogramma AzCopy v10. 

Zie de koppelingen in de sectie Volgende stappen van dit artikel voor voorbeelden van andere soorten taken, zoals het uploaden van bestanden, het downloaden van blobs en het synchroniseren met Blob Storage. [](#next-steps)

AzCopy maakt gebruik [van server-naar-server-API's,](/rest/api/storageservices/put-block-from-url) [](/rest/api/storageservices/put-page-from-url)zodat gegevens rechtstreeks tussen opslagservers worden gekopieerd. Deze kopieerbewerkingen maken geen gebruik van de netwerkbandbreedte van uw computer.

Zie Aan de slag met AzCopy om AzCopy te downloaden en meer te weten te komen over de manieren waarop u autorisatiereferenties kunt verstrekken aan [de opslagservice.](storage-use-azcopy-v10.md) 

## <a name="guidelines"></a>Richtlijnen

Pas de volgende richtlijnen toe op uw AzCopy-opdrachten. 

- Uw client moet netwerktoegang hebben tot zowel de bron- als de doelopslagaccounts. Zie Configure Azure Storage firewalls and virtual networks (Firewalls en virtuele netwerken configureren) voor meer informatie over het configureren van de netwerkinstellingen [voor elk opslagaccount.](storage-network-security.md?toc=/azure/storage/blobs/toc.json)

- Een SAS-token toevoegen aan elke bron-URL. 

  Als u autorisatiereferenties op Azure Active Directory (Azure AD), kunt u het SAS-token alleen weglaten uit de doel-URL. Zorg ervoor dat u de juiste rollen hebt ingesteld in uw doelaccount. Zie [Optie 1: gebruik Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory). 

  In de voorbeelden in dit artikel wordt ervan uitgenomen dat u uw identiteit hebt geverifieerd met behulp van Azure AD, zodat in de voorbeelden de SAS-tokens uit de doel-URL worden weglaten.

-  Als u naar een Premium-blok-blobopslagaccount kopieert, laat u de toegangslaag van een blob weg uit de kopieerbewerking door de in te stellen `s2s-preserve-access-tier` op `false` (bijvoorbeeld: `--s2s-preserve-access-tier=false` ). Premium blok-blob-opslagaccounts bieden geen ondersteuning voor toegangslagen. 

- Als u naar of van een account met een hiërarchische naamruimte kopieert, gebruikt u in plaats `blob.core.windows.net` van `dfs.core.windows.net` in de URL-syntaxis. [Met toegang met meerdere](../blobs/data-lake-storage-multi-protocol-access.md) protocollen op Data Lake Storage kunt u gebruiken. Dit is de enige `blob.core.windows.net` ondersteunde syntaxis voor account-naar-account-kopieerscenario's. 

- U kunt de doorvoer van kopieerbewerkingen verhogen door de waarde van de `AZCOPY_CONCURRENCY_VALUE` omgevingsvariabele in te stellen. Zie Gelijktijdigheid verhogen [voor meer informatie.](storage-use-azcopy-optimize.md#increase-concurrency) 

- Als de bron-blobs indextags hebben en u deze tags wilt behouden, moet u ze opnieuw op de doel-blobs toe te voegen. Zie de sectie Blobs kopiëren naar een ander opslagaccount met [indextags](#copy-between-accounts-and-add-index-tags) in dit artikel voor meer informatie over het instellen van indextags.

## <a name="copy-a-blob"></a>Een blob kopiëren

Kopieer een blob naar een ander opslagaccount met behulp van de [opdracht azcopy copy.](storage-ref-azcopy-copy.md) 

> [!TIP]
> In dit voorbeeld worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

**Syntaxis**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'`

**Voorbeeld**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

De kopieerbewerking is synchroon, dus wanneer de opdracht wordt geretourneerd, geeft dit aan dat alle bestanden zijn gekopieerd. 

## <a name="copy-a-directory"></a>Een map kopiëren

Kopieer een map naar een ander opslagaccount met behulp van de [opdracht azcopy copy.](storage-ref-azcopy-copy.md) 

> [!TIP]
> In dit voorbeeld worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

**Syntaxis**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Voorbeeld**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

De kopieerbewerking is synchroon, dus wanneer de opdracht wordt geretourneerd, geeft dit aan dat alle bestanden zijn gekopieerd.

## <a name="copy-a-container"></a>Een container kopiëren

Kopieer een container naar een ander opslagaccount met behulp van [de opdracht azcopy copy.](storage-ref-azcopy-copy.md)

> [!TIP]
> In dit voorbeeld worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

**Syntaxis**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Voorbeeld**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

De kopieerbewerking is synchroon, dus wanneer de opdracht wordt geretourneerd, geeft dit aan dat alle bestanden zijn gekopieerd.

## <a name="copy-containers-directories-and-blobs"></a>Containers, directories en blobs kopiëren

Kopieer alle containers, directories en blobs naar een ander opslagaccount met behulp van de [opdracht azcopy copy.](storage-ref-azcopy-copy.md)

> [!TIP]
> In dit voorbeeld worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows Command Shell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ('') in plaats van enkele aanhalingstekens ('').

**Syntaxis**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive`

**Voorbeeld**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive
```

De kopieerbewerking is synchroon, dus wanneer de opdracht wordt geretourneerd, geeft dit aan dat alle bestanden zijn gekopieerd.

<a id="copy-between-accounts-and-add-index-tags"></a>

## <a name="copy-blobs-and-add-index-tags"></a>Blobs kopiëren en indextags toevoegen

Kopieer blobs naar een ander opslagaccount en voeg [blob-indextags (preview) toe](../blobs/storage-manage-find-blobs.md) aan de doel-blob.

Als u Azure AD-autorisatie gebruikt, moet [](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) aan uw beveiligingsprincipaal de rol Eigenaar van opslagblobgegevens zijn toegewezen, of moet deze machtiging krijgen voor de `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [azure-resourceproviderbewerking](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) via een aangepaste Azure-rol. Als u een SAS-token (Shared Access Signature) gebruikt, moet dat token toegang bieden tot de tags van de blob via de `t` SAS-machtiging.

Als u tags wilt toevoegen, gebruikt u de optie samen met `--blob-tags` een sleutel-waardepaar met URL-codering. 

Als u bijvoorbeeld de sleutel en een waarde `my tag` wilt `my tag value` toevoegen, voegt u toe `--blob-tags='my%20tag=my%20tag%20value'` aan de doelparameter. 

Scheid meerdere indextags met behulp van een ampersand ( `&` ).  Als u bijvoorbeeld een sleutel en een waarde wilt toevoegen, is `my second tag` `my second tag value` de volledige optiereeks `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

De volgende voorbeelden laten zien hoe u de optie `--blob-tags` gebruikt.

> [!TIP]
> In deze voorbeelden worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows Command Shell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ('') in plaats van enkele aanhalingstekens ('').

**Blobvoorbeeld**

```azcopy

`azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

**Directoryvoorbeeld**

```azcopy
`azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

 **Containervoorbeeld**

```azcopy
`azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

**Accountvoorbeeld**

```azcopy
`azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

De kopieerbewerking is synchroon, dus wanneer de opdracht wordt geretourneerd, geeft dit aan dat alle bestanden zijn gekopieerd.

> [!NOTE]
> Als u een map, container of account voor de bron opgeeft, hebben alle blobs die naar de bestemming worden gekopieerd dezelfde tags die u in de opdracht opgeeft.

## <a name="copy-with-optional-flags"></a>Kopiëren met optionele vlaggen

U kunt uw kopieerbewerking aanpassen met behulp van optionele vlaggen. Hier zijn enkele voorbeelden.

|Scenario|Vlag|
|---|---|
|Kopieer blobs als Blok-, Pagina- of Toevoegen-blobs.|**--blob-type** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
|Kopieer naar een specifieke toegangslaag (zoals de archieflaag).|**--block-blob-tier** = \[ Geen \| Hot \| Cool \| Archive\]|
|Bestanden automatisch decomprimeren.|**--decompress** = \[ gzip \| deflate\]|

Zie opties voor een volledige [lijst.](storage-ref-azcopy-copy.md#options) 

## <a name="next-steps"></a>Volgende stappen

Meer voorbeelden vindt u in deze artikelen:

- [Voorbeelden: Uploaden](storage-use-azcopy-blobs-upload.md)
- [Voorbeelden: Downloaden](storage-use-azcopy-blobs-download.md)
- [Voorbeelden: Synchroniseren](storage-use-azcopy-blobs-synchronize.md)
- [Voorbeelden: Amazon S3-bucket](storage-use-azcopy-s3.md)
- [Voorbeelden: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Voorbeelden: Azure Files](storage-use-azcopy-files.md)
- [Zelfstudie: on-premises gegevens migreren naar cloudopslag met behulp van AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Zie deze artikelen voor het configureren van instellingen, het optimaliseren van prestaties en het oplossen van problemen:

- [AzCopy-configuratie-instellingen](storage-ref-azcopy-configuration-settings.md)
- [De prestaties van AzCopy optimaliseren](storage-use-azcopy-optimize.md)
- [Problemen met AzCopy V10 in Azure Storage met behulp van logboekbestanden](storage-use-azcopy-configure.md)