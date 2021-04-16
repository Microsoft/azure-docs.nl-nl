---
title: Kopieer vanuit Google Cloud Storage naar Azure Storage met AzCopy | Microsoft Docs
description: Gebruik AzCopy om gegevens te kopiëren van Google Cloud Storage naar Azure Storage. AzCopy is een opdrachtregelprogramma dat u kunt gebruiken om blobs of bestanden vanuit of naar een opslagaccount te kopiëren.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 3b2ad11abb7d1a3e64deef1ca49d9f84f03e5879
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498336"
---
# <a name="copy-data-from-google-cloud-storage-to-azure-storage-by-using-azcopy-preview"></a>Gegevens kopiëren van Google Cloud Storage naar Azure Storage met azcopy (preview)

AzCopy is een opdrachtregelprogramma dat u kunt gebruiken om blobs of bestanden vanuit of naar een opslagaccount te kopiëren. Dit artikel helpt u bij het kopiëren van objecten, directories en buckets van Google Cloud Storage naar Azure Blob Storage behulp van AzCopy.

> [!IMPORTANT]
> Het kopiëren van gegevens van Google Cloud Storage naar Azure Storage is momenteel in openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Choose how you'll provide authorization credentials (Kiezen hoe u autorisatiereferenties opgeeft)

* Als u wilt autor Azure Storage, gebruikt u Azure Active Directory (AD) of een Shared Access Signature (SAS)-token.

* Gebruik een serviceaccountsleutel om te autor maken met Google Cloud Storage.

### <a name="authorize-with-azure-storage"></a>Autor werken met Azure Storage

Zie het [artikel Aan de slag met AzCopy](storage-use-azcopy-v10.md) om AzCopy te downloaden en meer te weten te komen over de manieren waarop u autorisatiereferenties kunt verstrekken aan de opslagservice.

> [!NOTE] 
> In de voorbeelden in dit artikel wordt ervan uit gegaan dat u autorisatiereferenties hebt opgegeven met behulp van Azure Active Directory (Azure AD).
>
> Als u liever een SAS-token gebruikt om toegang tot blobgegevens te autoriëren, kunt u dat token toevoegen aan de resource-URL in elke AzCopy-opdracht. Bijvoorbeeld: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

### <a name="authorize-with-google-cloud-storage"></a>Autor werken met Google Cloud Storage

Als u wilt machtigen met Google Cloud Storage, gebruikt u een serviceaccountsleutel. Zie Serviceaccountsleutels maken en beheren voor meer informatie over het maken van een [serviceaccountsleutel.](https://cloud.google.com/iam/docs/creating-managing-service-account-keys)

Nadat u een servicesleutel hebt verkregen, stelt u de omgevingsvariabele in `GOOGLE_APPLICATION_CREDENTIALS` op absoluut pad naar het sleutelbestand van het serviceaccount:

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | `set GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **Linux** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **MacOS** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |

## <a name="copy-objects-directories-and-buckets"></a>Objecten, directories en buckets kopiëren

AzCopy maakt gebruik van [de PUT Block From URL-API,](/rest/api/storageservices/put-block-from-url) zodat gegevens rechtstreeks worden gekopieerd tussen Google Cloud Storage en opslagservers. Deze kopieerbewerkingen maken geen gebruik van de netwerkbandbreedte van uw computer.

> [!TIP]
> De voorbeelden in deze sectie plaatsen padargumenten met enkele aanhalingstekens (''). Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

 Deze voorbeelden werken ook met accounts die een hiërarchische naamruimte hebben. Toegang met meerdere protocollen op Data Lake Storage stelt u in staat om dezelfde [URL-syntaxis](../blobs/data-lake-storage-multi-protocol-access.md) () te gebruiken `blob.core.windows.net` voor deze accounts. 

### <a name="copy-an-object"></a>Een object kopiëren

Gebruik dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor accounts die een hiërarchische naamruimte hebben.

**Syntaxis**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'`

**Voorbeeld**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'
```


### <a name="copy-a-directory"></a>Een map kopiëren

Gebruik dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor accounts die een hiërarchische naamruimte hebben.

**Syntaxis**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Voorbeeld**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

> [!NOTE]
> In dit voorbeeld wordt de vlag `--recursive` toevoegen om bestanden in alle subdirecties te kopiëren.

### <a name="copy-the-contents-of-a-directory"></a>De inhoud van een map kopiëren

U kunt de inhoud van een map kopiëren zonder de map zelf te kopiëren met behulp van het jokerteken (*).

**Syntaxis**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Voorbeeld**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

### <a name="copy-a-cloud-storage-bucket"></a>Een Cloud Storage-bucket kopiëren

Gebruik dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor accounts die een hiërarchische naamruimte hebben.

**Syntaxis**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Voorbeeld**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-all-buckets-in-a-google-cloud-project"></a>Alle buckets in een Google Cloud-project kopiëren 

Stel eerst de in `GOOGLE_CLOUD_PROJECT` op project-id van Google Cloud-project.

Gebruik dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor accounts die een hiërarchische naamruimte hebben.

**Syntaxis**

`azcopy copy 'https://storage.cloud.google.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Voorbeeld**

```azcopy
azcopy copy 'https://storage.cloud.google.com/' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-a-subset-of-buckets-in-a-google-cloud-project"></a>Een subset van buckets kopiëren in een Google Cloud-project 

Stel eerst de in `GOOGLE_CLOUD_PROJECT` op project-id van Google Cloud-project.

Kopieer een subset van buckets met behulp van een jokerteken (*) in de bucketnaam. Gebruik dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor accounts die een hiërarchische naamruimte hebben.

**Syntaxis**

`azcopy copy 'https://storage.cloud.google.com/<bucket*name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Voorbeeld**

```azcopy
azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

## <a name="handle-differences-in-bucket-naming-rules"></a>Omgaan met verschillen in bucketnaamgevingsregels

Google Cloud Storage heeft een andere set naamconventnamen voor bucketnamen in vergelijking met Azure Blob-containers. U kunt er hier meer over [lezen.](https://cloud.google.com/storage/docs/naming-buckets) Als u ervoor kiest om een groep buckets naar een Azure-opslagaccount te kopiëren, kan de kopieerbewerking mislukken vanwege naamverschillen.

AzCopy verwerkt drie van de meest voorkomende problemen die zich kunnen voordoen; buckets die punten, buckets met opeenvolgende afbreekstreepingen en buckets met onderstrepingstekens bevatten. Namen van Google Cloud Storage-buckets kunnen punten en opeenvolgende afbreekstreelopen bevatten, maar een container in Azure kan dat niet. AzCopy vervangt punten met afbreekstreeën en opeenvolgende afbreekstreeën door een getal dat het aantal opeenvolgende afbreekstreeën vertegenwoordigt (bijvoorbeeld: een bucket met de naam `my----bucket` wordt `my-4-bucket` . Als de bucketnaam een onderstrepingsteken ( ) heeft, vervangt AzCopy het onderstrepingsteken door een koppelteken (bijvoorbeeld: een bucket met de naam `_` `my_bucket` wordt `my-bucket` . 

## <a name="handle-differences-in-object-naming-rules"></a>Verschillen in naamgevingsregels voor objecten afhandelen

Google Cloud Storage heeft een andere set naamconventnamen voor objectnamen in vergelijking met Azure-blobs. U kunt er hier meer over [lezen.](https://cloud.google.com/storage/docs/naming-objects)

Azure Storage objectnamen (of een segment in het pad van de virtuele map) mogen niet eindigen met punt volgen (bijvoorbeeld `my-bucket...` ). Punt aan het punt wordt afgekort wanneer de kopieerbewerking wordt uitgevoerd. 

## <a name="handle-differences-in-object-metadata"></a>Verschillen in objectmetagegevens verwerken

Google Cloud Storage en Azure staan verschillende sets tekens toe in de namen van objectsleutels. Meer informatie over metagegevens in Google Cloud Storage [vindt u hier.](https://cloud.google.com/storage/docs/metadata) Aan de azure-zijde voldoen blob-objectsleutels aan de naamgevingsregels voor [C#-id's.](/dotnet/csharp/language-reference/)

Als onderdeel van een AzCopy-opdracht kunt u een waarde opgeven voor de vlag die aangeeft hoe u bestanden wilt verwerken waarbij de metagegevens van het bestand incompatibele sleutelnamen `copy` `s2s-handle-invalid-metadata` bevatten. In de volgende tabel wordt elke vlagwaarde beschreven.

| Vlagwaarde | Beschrijving  |
|--------|-----------|
| **ExcludeIfInvalid** | (Standaardoptie) De metagegevens zijn niet opgenomen in het overgedragen object. AzCopy registreert een waarschuwing. |
| **FailIfInvalid** | Objecten worden niet gekopieerd. AzCopy registreert een fout en bevat deze fout in het aantal mislukte pogingen dat wordt weergegeven in het overdrachtsoverzicht.  |
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
- [Voorbeelden: Amazon S3-bucket](storage-use-azcopy-s3.md)
- [Voorbeelden: Azure Files](storage-use-azcopy-files.md)
- [Zelfstudie: on-premises gegevens migreren naar cloudopslag met behulp van AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Zie deze artikelen voor het configureren van instellingen, het optimaliseren van prestaties en het oplossen van problemen:

- [AzCopy-configuratie-instellingen](storage-ref-azcopy-configuration-settings.md)
- [De prestaties van AzCopy optimaliseren](storage-use-azcopy-optimize.md)
- [Problemen met AzCopy V10 in Azure Storage oplossen met behulp van logboekbestanden](storage-use-azcopy-configure.md)