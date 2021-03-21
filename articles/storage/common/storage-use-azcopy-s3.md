---
title: Gegevens van Amazon S3 naar Azure Storage kopiëren met behulp van AzCopy | Microsoft Docs
description: Gebruik AzCopy om gegevens van Amazon S3 naar Azure Storage te kopiëren. AzCopy is een opdrachtregelprogramma dat u kunt gebruiken om blobs of bestanden vanuit of naar een opslagaccount te kopiëren.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 34f54bb30e959ecc2fa27fba5ab7392b9eddc68e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103494509"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Gegevens van Amazon S3 naar Azure Storage kopiëren met behulp van AzCopy

AzCopy is een opdrachtregelprogramma dat u kunt gebruiken om blobs of bestanden vanuit of naar een opslagaccount te kopiëren. Dit artikel helpt u bij het kopiëren van objecten, directory's en buckets van Amazon Web Services (AWS) S3 naar Azure Blob Storage met behulp van AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Choose how you'll provide authorization credentials (Kiezen hoe u autorisatiereferenties opgeeft)

* Als u de Azure Storage wilt autoriseren, gebruikt u Azure Active Directory (AD) of een Shared Access Signature-token (SAS).

* Als u wilt autoriseren met AWS S3, gebruikt u een AWS-toegangs sleutel en een geheime toegangs sleutel.

### <a name="authorize-with-azure-storage"></a>Autoriseren met Azure Storage

Zie het artikel aan de [slag met AzCopy](storage-use-azcopy-v10.md) om AzCopy te downloaden en kies hoe u autorisatie referenties opgeeft voor de opslag service.

> [!NOTE]
> In de voor beelden in dit artikel wordt ervan uitgegaan dat u uw identiteit hebt geverifieerd met behulp van de `AzCopy login` opdracht. AzCopy gebruikt vervolgens uw Azure AD-account om toegang te verlenen tot gegevens in Blob Storage.
>
> Als u liever een SAS-token gebruikt om toegang te verlenen tot BLOB-gegevens, kunt u dat token toevoegen aan de bron-URL in elke AzCopy-opdracht.
>
> Bijvoorbeeld: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Autoriseren met AWS S3

Verzamel uw AWS-toegangs sleutel en geheime toegangs sleutel en stel deze omgevings variabelen in:

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Objecten, directory's en buckets kopiëren

AzCopy maakt gebruik [van de API put van URL](/rest/api/storageservices/put-block-from-url) , zodat gegevens rechtstreeks tussen AWS S3 en opslag servers worden gekopieerd. Deze Kopieer bewerkingen gebruiken de netwerk bandbreedte van uw computer niet.

> [!TIP]
> De voor beelden in deze sectie zijn pad-argumenten met enkele aanhalings tekens (' '). Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

 Deze voor beelden werken ook met accounts die een hiërarchische naam ruimte hebben. Met [toegang via meerdere protocollen op Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) kunt u dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor deze accounts gebruiken. 

### <a name="copy-an-object"></a>Een object kopiëren

Gebruik dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor accounts die een hiërarchische naam ruimte hebben.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Voorbeeld** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> Voor beelden in dit artikel gebruiken Path-Url's voor AWS S3-buckets (bijvoorbeeld: `http://s3.amazonaws.com/<bucket-name>` ). 
>
> U kunt ook virtuele Url's voor gehoste stijlen gebruiken (bijvoorbeeld: `http://bucket.s3.amazonaws.com` ). 
>
> Zie [virtuele hosting van buckets](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html)voor meer informatie over virtuele hosting van buckets.

### <a name="copy-a-directory"></a>Een map kopiëren

Gebruik dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor accounts die een hiërarchische naam ruimte hebben.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Voorbeeld** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Voor beeld** (hiërarchische naam ruimte)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

> [!NOTE]
> In dit voor beeld wordt de `--recursive` vlag toegevoegd om bestanden in alle submappen te kopiëren.

### <a name="copy-the-contents-of-a-directory"></a>De inhoud van een map kopiëren

U kunt de inhoud van een map kopiëren zonder de bovenliggende map zelf te kopiëren met behulp van het Joker teken (*).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Voorbeeld** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Voor beeld** (hiërarchische naam ruimte)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>Een Bucket kopiëren

Gebruik dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor accounts die een hiërarchische naam ruimte hebben.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **Voorbeeld** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **Voor beeld** (hiërarchische naam ruimte)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Alle buckets in alle regio's kopiëren

Gebruik dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor accounts die een hiërarchische naam ruimte hebben.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Voorbeeld** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Voor beeld** (hiërarchische naam ruimte)| `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Alle buckets in een specifieke S3-regio kopiëren

Gebruik dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor accounts die een hiërarchische naam ruimte hebben.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Voorbeeld** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Voor beeld** (hiërarchische naam ruimte)| `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Verschillen in regels voor object naamgeving afhandelen

AWS S3 heeft een andere set naam conventies voor Bucket-namen in vergelijking met Azure Blob-containers. Meer informatie hierover vindt u [hier](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Als u een groep buckets kopieert naar een Azure Storage-account, kan de Kopieer bewerking mislukken vanwege naam verschillen.

AzCopy verwerkt twee van de meest voorkomende problemen die zich kunnen voordoen. buckets die punten en buckets bevatten die opeenvolgende afbreek streepjes bevatten. AWS S3-Bucket namen kunnen punten en opeenvolgende afbreek streepjes bevatten, maar een container in azure kan niet. AzCopy vervangt Peri Oden door afbreek streepjes en opeenvolgende afbreek streepjes met een getal dat het aantal opeenvolgende afbreek streepjes vertegenwoordigt (bijvoorbeeld: een Bucket met de naam `my----bucket` wordt gewijzigd `my-4-bucket` . 

Als AzCopy kopieën over bestanden overneemt, wordt ook gecontroleerd op naam conflicten en wordt geprobeerd deze op te lossen. Als er bijvoorbeeld buckets zijn met de naam `bucket-name` en `bucket.name` , wordt met AzCopy een Bucket omgezet met de naam `bucket.name` First naar `bucket-name` en vervolgens naar `bucket-name-2` .

## <a name="handle-differences-in-object-metadata"></a>Verschillen in meta gegevens van object afhandelen

AWS S3 en Azure staan verschillende sets tekens toe aan de namen van object sleutels. U vindt [hier](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys)meer informatie over de tekens die AWS S3 gebruikt. Aan de kant van de Azure-Blob-object sleutels voldoen aan de naamgevings regels voor [C#-id's](/dotnet/csharp/language-reference/).

Als onderdeel van een AzCopy `copy` -opdracht kunt u een waarde opgeven voor de optionele `s2s-handle-invalid-metadata` vlag waarmee wordt aangegeven hoe u bestanden wilt afhandelen waarin de meta gegevens van het bestand incompatibele sleutel namen bevatten. De volgende tabel beschrijft de waarde van elke vlag.

| Vlag waarde | Beschrijving  |
|--------|-----------|
| **ExcludeIfInvalid** | (Standaard optie) De meta gegevens zijn niet opgenomen in het overgezette object. AzCopy registreert een waarschuwing. |
| **FailIfInvalid** | Objecten worden niet gekopieerd. AzCopy registreert een fout en bevat een fout in het aantal mislukte overzichten dat wordt weer gegeven in het overzicht van de overdracht.  |
| **RenameIfInvalid**  | AzCopy lost de ongeldige meta gegevens sleutel op en kopieert het object naar Azure met behulp van het omgezette meta gegevens sleutel waarde-paar. Zie de sectie [How a object Keys van AzCopy](#rename-logic) reAzCopys voor meer informatie over de stappen die nodig zijn om de naam van object sleutels te wijzigen. Als AzCopy de naam van de sleutel niet kan wijzigen, wordt het object niet gekopieerd. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>Het wijzigen van de naam van object sleutels in AzCopy

AzCopy voert de volgende stappen uit:

1. Ongeldige tekens vervangen door _.

2. De teken reeks wordt `rename_` aan het begin van een nieuwe geldige sleutel toegevoegd.

   Deze sleutel wordt gebruikt om de oorspronkelijke meta gegevens **waarde** op te slaan.

3. De teken reeks wordt `rename_key_` aan het begin van een nieuwe geldige sleutel toegevoegd.
   Deze sleutel wordt gebruikt om de oorspronkelijke ongeldige **sleutel** van de meta gegevens op te slaan.
   U kunt deze sleutel gebruiken om de meta gegevens in azure Side te herstellen omdat de meta gegevens sleutel wordt behouden als een waarde in de Blob Storage-service.

## <a name="next-steps"></a>Volgende stappen

Meer voor beelden vindt u in een van deze artikelen:

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)

- [Gegevens overdragen](storage-use-azcopy-v10.md#transfer-data)

- [Configureren, optimaliseren en problemen oplossen in AzCopy](storage-use-azcopy-configure.md)
