---
title: Gegevens kopiëren van Google Cloud Storage naar Azure Storage met behulp van AzCopy | Microsoft Docs
description: Gebruik AzCopy om gegevens van Google Cloud Storage te kopiëren naar Azure Storage. AzCopy is een opdrachtregelprogramma dat u kunt gebruiken om blobs of bestanden vanuit of naar een opslagaccount te kopiëren.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: c6a53acd63b6aa882674f6aa29e1f7152f5b0a30
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728807"
---
# <a name="copy-data-from-google-cloud-storage-to-azure-storage-by-using-azcopy-preview"></a>Gegevens kopiëren van Google Cloud Storage naar Azure Storage met behulp van AzCopy (preview)

AzCopy is een opdrachtregelprogramma dat u kunt gebruiken om blobs of bestanden vanuit of naar een opslagaccount te kopiëren. Dit artikel helpt u bij het kopiëren van objecten, directory's en buckets van Google Cloud Storage naar Azure Blob Storage met behulp van AzCopy.

> [!IMPORTANT]
> Het kopiëren van gegevens uit Google Cloud Storage naar Azure Storage is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Choose how you'll provide authorization credentials (Kiezen hoe u autorisatiereferenties opgeeft)

* Als u wilt autoriseren met Azure Storage, gebruikt u Azure Active Directory (AD) of een Shared Access Signature-token (SAS).

* Als u wilt autoriseren met Google Cloud Storage, gebruikt u een service account Key.

### <a name="authorize-with-azure-storage"></a>Autoriseren met Azure Storage

Zie het artikel aan de [slag met AzCopy](storage-use-azcopy-v10.md) om AzCopy te downloaden en meer te weten te komen over de manieren waarop u autorisatie referenties kunt opgeven voor de opslag service.

> [!NOTE] 
> In de voor beelden in dit artikel wordt ervan uitgegaan dat u autorisatie referenties hebt ingesteld met behulp van Azure Active Directory (Azure AD).
>
> Als u liever een SAS-token gebruikt om toegang te verlenen tot BLOB-gegevens, kunt u dat token toevoegen aan de bron-URL in elke AzCopy-opdracht. Bijvoorbeeld: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

### <a name="authorize-with-google-cloud-storage"></a>Autoriseren met Google Cloud Storage

Als u wilt autoriseren met Google Cloud Storage, gebruikt u een service account Key. Zie voor meer informatie over het maken van een service account sleutel [sleutels voor service accounts maken en beheren](https://cloud.google.com/iam/docs/creating-managing-service-account-keys).

Nadat u een service sleutel hebt verkregen, stelt u de `GOOGLE_APPLICATION_CREDENTIALS` omgevings variabele in op een absoluut pad naar het sleutel bestand van het service account:

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | `set GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **Linux** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **MacOS** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |

## <a name="copy-objects-directories-and-buckets"></a>Objecten, directory's en buckets kopiëren

AzCopy maakt gebruik [van de API put van URL](/rest/api/storageservices/put-block-from-url) , zodat gegevens rechtstreeks worden gekopieerd tussen Google Cloud Storage en storage-servers. Deze Kopieer bewerkingen gebruiken de netwerk bandbreedte van uw computer niet.

> [!TIP]
> De voor beelden in deze sectie zijn pad-argumenten met enkele aanhalings tekens (' '). Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

 Deze voor beelden werken ook met accounts die een hiërarchische naam ruimte hebben. Met [toegang via meerdere protocollen op Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) kunt u dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor deze accounts gebruiken. 

### <a name="copy-an-object"></a>Een object kopiëren

Gebruik dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor accounts die een hiërarchische naam ruimte hebben.

| Syntaxis/voor beeld  |  Code |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Voorbeeld** | `azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |


### <a name="copy-a-directory"></a>Een map kopiëren

Gebruik dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor accounts die een hiërarchische naam ruimte hebben.

| Syntaxis/voor beeld  |  Code |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Voorbeeld** | `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Voor beeld** (hiërarchische naam ruimte)| `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

> [!NOTE]
> In dit voor beeld wordt de `--recursive` vlag toegevoegd om bestanden in alle submappen te kopiëren.

### <a name="copy-the-contents-of-a-directory"></a>De inhoud van een map kopiëren

U kunt de inhoud van een map kopiëren zonder de bovenliggende map zelf te kopiëren met behulp van het Joker teken (*).

| Syntaxis/voor beeld  |  Code |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Voorbeeld** | `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Voor beeld** (hiërarchische naam ruimte)| `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-cloud-storage-bucket"></a>Een Bucket voor Cloud opslag kopiëren

Gebruik dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor accounts die een hiërarchische naam ruimte hebben.

| Syntaxis/voor beeld  |  Code |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Voorbeeld** | `azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Voor beeld** (hiërarchische naam ruimte)| `azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-all-buckets-in-a-google-cloud-project"></a>Alle buckets in een Google Cloud-project kopiëren 

Stel eerst de `GOOGLE_CLOUD_PROJECT` project-id in van Google Cloud project.

Gebruik dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor accounts die een hiërarchische naam ruimte hebben.

| Syntaxis/voor beeld  |  Code |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://storage.cloud.google.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Voorbeeld** | `azcopy copy 'https://storage.cloud.google.com/' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Voor beeld** (hiërarchische naam ruimte)| `azcopy copy 'https://storage.cloud.google.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-a-subset-of-buckets-in-a-google-cloud-project"></a>Een subset van buckets kopiëren in een Google Cloud-project 

Stel eerst de `GOOGLE_CLOUD_PROJECT` project-id in van Google Cloud project.

Kopieer een subset van buckets met behulp van een Joker teken (*) in de Bucket naam. Gebruik dezelfde URL-syntaxis ( `blob.core.windows.net` ) voor accounts die een hiërarchische naam ruimte hebben.

| Syntaxis/voor beeld  |  Code |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://storage.cloud.google.com/<bucket*name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Voorbeeld** | `azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Voor beeld** (hiërarchische naam ruimte)| `azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

## <a name="handle-differences-in-bucket-naming-rules"></a>Verschillen in naamgevings regels voor Bucket afhandelen

Google Cloud Storage heeft een andere set naam conventies voor Bucket-namen in vergelijking met Azure Blob-containers. Meer informatie hierover vindt u [hier](https://cloud.google.com/storage/docs/naming-buckets). Als u een groep buckets kopieert naar een Azure Storage-account, kan de Kopieer bewerking mislukken vanwege naam verschillen.

AzCopy verwerkt drie van de meest voorkomende problemen die zich kunnen voordoen. buckets die punten bevatten, buckets die opeenvolgende afbreek streepjes bevatten en buckets die onderstrepings tekens bevatten. Google Cloud Storage-Bucket namen kunnen punten en opeenvolgende afbreek streepjes bevatten, maar een container in azure kan niet. AzCopy vervangt Peri Oden door afbreek streepjes en opeenvolgende afbreek streepjes met een getal dat het aantal opeenvolgende afbreek streepjes vertegenwoordigt (bijvoorbeeld: een Bucket met de naam `my----bucket` wordt gewijzigd `my-4-bucket` . Als de Bucket naam een onderstrepings teken ( `_` ) heeft, vervangt AzCopy het onderstrepings teken door een koppel teken (bijvoorbeeld: een Bucket met de naam `my_bucket` wordt gewijzigd `my-bucket` . 

## <a name="handle-differences-in-object-naming-rules"></a>Verschillen in regels voor object naamgeving afhandelen

Google Cloud Storage heeft een andere set naam conventies voor object namen ten opzichte van Azure-blobs. Meer informatie hierover vindt u [hier](https://cloud.google.com/storage/docs/naming-objects).

Azure Storage staat geen object namen (of een segment in het pad van de virtuele map) toe te eindigen met een punt komma (bijvoorbeeld `my-bucket...` ). Navolgende stippen worden afgekapt wanneer de Kopieer bewerking wordt uitgevoerd. 

## <a name="handle-differences-in-object-metadata"></a>Verschillen in meta gegevens van object afhandelen

Google Cloud Storage en Azure staan verschillende sets tekens toe aan de namen van object sleutels. Meer informatie over meta gegevens vindt u [hier](https://cloud.google.com/storage/docs/metadata)in Google Cloud Storage. Aan de kant van de Azure-Blob-object sleutels voldoen aan de naamgevings regels voor [C#-id's](/dotnet/csharp/language-reference/).

Als onderdeel van een AzCopy `copy` -opdracht kunt u een waarde opgeven voor de optionele `s2s-handle-invalid-metadata` vlag waarmee wordt aangegeven hoe u bestanden wilt afhandelen waarin de meta gegevens van het bestand incompatibele sleutel namen bevatten. De volgende tabel beschrijft de waarde van elke vlag.

| Vlag waarde | Description  |
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
