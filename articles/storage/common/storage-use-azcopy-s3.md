---
title: Gegevens kopiëren van Amazon S3 naar Azure Storage met behulp van AzCopy | Microsoft Docs
description: Gebruik AzCopy om gegevens te kopiëren van Amazon S3 naar Azure Storage. AzCopy is een opdrachtregelprogramma dat u kunt gebruiken om blobs of bestanden vanuit of naar een opslagaccount te kopiëren.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: ac73d0e57377a8922691ea06c8de3df5ef577680
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502433"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Gegevens kopiëren van Amazon S3 naar Azure Storage met behulp van AzCopy

AzCopy is een opdrachtregelprogramma dat u kunt gebruiken om blobs of bestanden vanuit of naar een opslagaccount te kopiëren. Dit artikel helpt u bij het kopiëren van objecten, directories en buckets van Amazon Web Services (AWS) S3 naar Azure Blob Storage met behulp van AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Choose how you'll provide authorization credentials (Kiezen hoe u autorisatiereferenties opgeeft)

* Als u wilt autor Azure Storage, gebruikt u Azure Active Directory (AD) of een SHARED ACCESS SIGNATURE-token (SAS).

* Als u wilt machtigen met AWS S3, gebruikt u een AWS-toegangssleutel en een geheime toegangssleutel.

### <a name="authorize-with-azure-storage"></a>Autor toestemming geven met Azure Storage

Zie het [artikel Aan de slag met AzCopy](storage-use-azcopy-v10.md) om AzCopy te downloaden en kies hoe u autorisatiereferenties aan de opslagservice wilt verstrekken.

> [!NOTE]
> In de voorbeelden in dit artikel wordt ervan uitgenomen dat u uw identiteit hebt geverifieerd met behulp van de `AzCopy login` opdracht . AzCopy gebruikt vervolgens uw Azure AD-account om toegang te verlenen tot gegevens in Blob Storage.
>
> Als u liever een SAS-token gebruikt om toegang tot blobgegevens te autoriëren, kunt u dat token toevoegen aan de resource-URL in elke AzCopy-opdracht.
>
> Bijvoorbeeld: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Autor toestemming geven met AWS S3

Verzamel uw AWS-toegangssleutel en geheime toegangssleutel en stel vervolgens deze omgevingsvariabelen in:

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Objecten, directories en buckets kopiëren

AzCopy maakt gebruik van [de PUT Block From URL-API,](/rest/api/storageservices/put-block-from-url) zodat gegevens rechtstreeks worden gekopieerd tussen AWS S3 en opslagservers. Deze kopieerbewerkingen maken geen gebruik van de netwerkbandbreedte van uw computer.

> [!TIP]
> In de voorbeelden in deze sectie worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

 Deze voorbeelden werken ook met accounts die een hiërarchische naamruimte hebben. [Met toegang met meerdere protocollen Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) u dezelfde URL-syntaxis `blob.core.windows.net` () gebruiken voor deze accounts. 

### <a name="copy-an-object"></a>Een object kopiëren

Gebruik dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor accounts die een hiërarchische naamruimte hebben.

**Syntaxis**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'`

**Voorbeeld**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'
```

> [!NOTE]
> In voorbeelden in dit artikel worden url's in padstijl gebruikt voor AWS S3-buckets (bijvoorbeeld: `http://s3.amazonaws.com/<bucket-name>` ). 
>
> U kunt ook URL's in virtuele gehoste stijl gebruiken (bijvoorbeeld: `http://bucket.s3.amazonaws.com` ). 
>
> Zie Virtuele hosting van buckets voor meer informatie over virtuele hosting [van buckets.](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html)

### <a name="copy-a-directory"></a>Een map kopiëren

Gebruik dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor accounts die een hiërarchische naamruimte hebben.

**Syntaxis**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Voorbeeld**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

> [!NOTE]
> In dit voorbeeld wordt de vlag `--recursive` toevoegen om bestanden in alle subdirecties te kopiëren.

### <a name="copy-the-contents-of-a-directory"></a>De inhoud van een map kopiëren

U kunt de inhoud van een map kopiëren zonder de map zelf te kopiëren met behulp van het jokerteken (*).

**Syntaxis**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Voorbeeld**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

### <a name="copy-a-bucket"></a>Een bucket kopiëren

Gebruik dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor accounts die een hiërarchische naamruimte hebben.

**Syntaxis**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true`

**Voorbeeld**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true
```

### <a name="copy-all-buckets-in-all-regions"></a>Alle buckets in alle regio's kopiëren

Gebruik dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor accounts die een hiërarchische naamruimte hebben.

**Syntaxis**

`azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Voorbeeld**

```azcopy
azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Alle buckets in een specifieke S3-regio kopiëren

Gebruik dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor accounts die een hiërarchische naamruimte hebben.

**Syntaxis**

`azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Voorbeeld**

```azcopy
azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

## <a name="handle-differences-in-object-naming-rules"></a>Verschillen in naamgevingsregels voor objecten afhandelen

AWS S3 heeft een andere set naamconventnamen voor bucketnamen in vergelijking met Azure Blob-containers. U kunt er hier meer over [lezen.](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules) Als u ervoor kiest om een groep buckets naar een Azure-opslagaccount te kopiëren, kan de kopieerbewerking mislukken vanwege naamverschillen.

AzCopy verwerkt twee van de meest voorkomende problemen die zich kunnen voordoen; buckets die punten en buckets bevatten die opeenvolgende afbreekstreelopen bevatten. Namen van AWS S3-buckets kunnen punten en opeenvolgende afbreekstreelopen bevatten, maar een container in Azure niet. AzCopy vervangt punten door afbreekstreeën en opeenvolgende afbreekstreeën door een getal dat het aantal opeenvolgende afbreekstreeën vertegenwoordigt (bijvoorbeeld: een bucket met de naam `my----bucket` wordt `my-4-bucket` . 

Naarmate AzCopy bestanden kopieert, wordt er ook gecontroleerd op naamcondy's en wordt geprobeerd deze op te lossen. Als er bijvoorbeeld buckets zijn met de naam en , wordt in AzCopy eerst een bucket met de naam naar en `bucket-name` `bucket.name` vervolgens naar `bucket.name` `bucket-name` `bucket-name-2` opgelost.

## <a name="handle-differences-in-object-metadata"></a>Verschillen in objectmetagegevens verwerken

AWS S3 en Azure staan verschillende sets tekens toe in de namen van objectsleutels. U kunt hier lezen over de tekens die AWS S3 [gebruikt.](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys) Aan de azure-zijde voldoen blob-objectsleutels aan de naamgevingsregels voor [C#-id's.](/dotnet/csharp/language-reference/)

Als onderdeel van een AzCopy-opdracht kunt u een waarde opgeven voor de vlag die aangeeft hoe u bestanden wilt verwerken waarbij de metagegevens van het bestand incompatibele sleutelnamen `copy` `s2s-handle-invalid-metadata` bevatten. In de volgende tabel wordt elke vlagwaarde beschreven.

| Vlagwaarde | Beschrijving  |
|--------|-----------|
| **ExcludeIfInvalid** | (Standaardoptie) De metagegevens zijn niet opgenomen in het overgedragen object. AzCopy registreert een waarschuwing. |
| **FailIfInvalid** | Objecten worden niet gekopieerd. AzCopy registreert een fout en neemt deze fout op in het aantal mislukte pogingen dat wordt weergegeven in het overdrachtsoverzicht.  |
| **RenameIfInvalid**  | AzCopy lost de ongeldige metagegevenssleutel op en kopieert het object naar Azure met behulp van het waardepaar met de om opgeloste metagegevenssleutel. Zie de sectie Hoe azCopy de naam van objectsleutels wijzigt hieronder voor meer informatie over de stappen die [AzCopy](#rename-logic) moet nemen om de naam van objectsleutels te wijzigen. Als AzCopy de naam van de sleutel niet kan wijzigen, wordt het object niet gekopieerd. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>De naam van objectsleutels wijzigen in AzCopy

AzCopy voert de volgende stappen uit:

1. Vervangt ongeldige tekens door '_'.

2. Voegt de `rename_` tekenreeks toe aan het begin van een nieuwe geldige sleutel.

   Deze sleutel wordt gebruikt om de oorspronkelijke metagegevenswaarde op **te slaan.**

3. Voegt de `rename_key_` tekenreeks toe aan het begin van een nieuwe geldige sleutel.
   Deze sleutel wordt gebruikt om de oorspronkelijke ongeldige sleutel voor metagegevens op **te slaan.**
   U kunt deze sleutel gebruiken om de metagegevens in Azure te herstellen, omdat de metagegevenssleutel behouden blijft als een waarde in de Blob Storage-service.

## <a name="next-steps"></a>Volgende stappen

Meer voorbeelden vindt u in deze artikelen:

- [Voorbeelden: Uploaden](storage-use-azcopy-blobs-upload.md)
- [Voorbeelden: Downloaden](storage-use-azcopy-blobs-download.md)
- [Voorbeelden: Kopiëren tussen accounts](storage-use-azcopy-blobs-copy.md)
- [Voorbeelden: Synchroniseren](storage-use-azcopy-blobs-synchronize.md)
- [Voorbeelden: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Voorbeelden: Azure Files](storage-use-azcopy-files.md)
- [Zelfstudie: on-premises gegevens migreren naar cloudopslag met behulp van AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Zie deze artikelen voor het configureren van instellingen, het optimaliseren van prestaties en het oplossen van problemen:

- [AzCopy-configuratie-instellingen](storage-ref-azcopy-configuration-settings.md)
- [De prestaties van AzCopy optimaliseren](storage-use-azcopy-optimize.md)
- [Problemen met AzCopy V10 in Azure Storage met behulp van logboekbestanden](storage-use-azcopy-configure.md)
