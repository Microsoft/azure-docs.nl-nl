---
title: Synchroniseren met Azure Blob Storage met behulp van AzCopy v10 | Microsoft Docs
description: Dit artikel bevat een verzameling AzCopy-voorbeeldopdrachten die u helpen bij het synchroniseren met Azure Blob Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 8b3340c00d856b13edefc7728d5baa327399a441
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502926"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy"></a>Synchroniseren met Azure Blob Storage met behulp van AzCopy

U kunt lokale opslag synchroniseren met Azure Blob-opslag met behulp van het AzCopy v10-opdrachtregelprogramma. 

U kunt de inhoud van een lokaal bestandssysteem synchroniseren met een blobcontainer. U kunt ook containers en virtuele directories met elkaar synchroniseren. Synchronisatie is één manier. Met andere woorden, u kiest welke van deze twee eindpunten de bron is en welke het doel is. Synchronisatie maakt ook gebruik van server-naar-server-API's. De voorbeelden in deze sectie werken ook met accounts die een hiërarchische naamruimte hebben. 

> [!NOTE]
> De huidige versie van AzCopy wordt niet gesynchroniseerd tussen andere bronnen en bestemmingen (bijvoorbeeld: File Storage of Amazon Web Services (AWS) S3-buckets).

Zie de koppelingen in de sectie Volgende stappen van dit artikel voor voorbeelden van andere soorten taken, zoals het uploaden van bestanden, het downloaden van blobs of het kopiëren van blobs tussen accounts. [](#next-steps)

## <a name="get-started"></a>Aan de slag

Zie het [artikel Aan de slag met AzCopy](storage-use-azcopy-v10.md) om AzCopy te downloaden en meer te weten te komen over de manieren waarop u autorisatiereferenties kunt verstrekken aan de opslagservice.

> [!NOTE] 
> In de voorbeelden in dit artikel wordt ervan uit gegaan dat u autorisatiereferenties hebt opgegeven met behulp van Azure Active Directory (Azure AD).
>
> Als u liever een SAS-token gebruikt om toegang tot blobgegevens te autoriëren, kunt u dat token toevoegen aan de resource-URL in elke AzCopy-opdracht. Bijvoorbeeld: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="guidelines"></a>Richtlijnen

- Met [de synchronisatieopdracht](storage-ref-azcopy-sync.md) worden bestandsnamen en laatst gewijzigde tijdstempels vergeleken. Stel de optionele vlag in op de waarde of om bestanden in de doelmap te verwijderen als deze bestanden niet meer `--delete-destination` `true` bestaan in de `prompt` bronmap.

- Als u de vlag `--delete-destination` in stelt op , verwijdert `true` AzCopy bestanden zonder een prompt op te geven. Als u wilt dat er een prompt wordt weergegeven voordat AzCopy een bestand verwijdert, stelt u de `--delete-destination` vlag in op `prompt` .

- Als u van plan bent om de vlag in te stellen op of , kunt u overwegen de kopieeropdracht te gebruiken in plaats van de synchronisatieopdracht en `--delete-destination` de parameter in te stellen op `prompt` `false` [](storage-ref-azcopy-copy.md) [](storage-ref-azcopy-sync.md) `--overwrite` `ifSourceNewer` . De [kopieeropdracht](storage-ref-azcopy-copy.md) verbruikt minder geheugen en er worden minder factureringskosten in rekening gebracht omdat een kopieerbewerking de bron of bestemming niet hoeft te indexeren voordat bestanden worden verplaatst. 

- Als u onbedoelde verwijderingen wilt voorkomen, moet u de functie voor zacht [verwijderen inschakelen](../blobs/soft-delete-blob-overview.md) voordat u de vlag `--delete-destination=prompt|true` gebruikt.

- De computer waarop u de synchronisatieopdracht moet een nauwkeurige systeemklok hebben omdat de laatste wijzigingstijden essentieel zijn bij het bepalen of een bestand moet worden overgedragen. Als uw systeem een aanzienlijke tijdverschil heeft, moet u voorkomen dat bestanden op de bestemming te dicht bij het tijdstip dat u van plan bent om een synchronisatieopdracht uit te voeren.

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>Een container bijwerken met wijzigingen in een lokaal bestandssysteem

In dit geval is de container het doel, en is het lokale bestandssysteem de bron. 

> [!TIP]
> In dit voorbeeld worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

**Syntaxis**

`azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Voorbeeld**

```azcopy
azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>Een lokaal bestandssysteem bijwerken met wijzigingen in een container

In dit geval is het lokale bestandssysteem het doel en is de container de bron.

> [!TIP]
> In dit voorbeeld worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

**Syntaxis**

`azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive`

**Voorbeeld**

```azcopy
azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive
```

## <a name="update-a-container-with-changes-in-another-container"></a>Een container bijwerken met wijzigingen in een andere container

De eerste container die in deze opdracht wordt weergegeven, is de bron. De tweede is het doel.

> [!TIP]
> In dit voorbeeld worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

**Syntaxis**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Voorbeeld**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>Een map bijwerken met wijzigingen in een map in een andere container

De eerste map die wordt weergegeven in deze opdracht is de bron. De tweede is de bestemming.

> [!TIP]
> In dit voorbeeld worden padargumenten met enkele aanhalingstekens ('') ingesloten. Gebruik enkele aanhalingstekens in alle opdrachtshells, met uitzondering van de Windows-opdrachtshell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten met dubbele aanhalingstekens ('') in plaats van enkele aanhalingstekens ('').

**Syntaxis**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive`

**Voorbeeld**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive
```

## <a name="synchronize-with-optional-flags"></a>Synchroniseren met optionele vlaggen

U kunt uw synchronisatiebewerking aanpassen met behulp van optionele vlaggen. Hier zijn enkele voorbeelden.

|Scenario|Vlag|
|---|---|
|Geef op hoe strikt MD5-hashes moeten worden gevalideerd bij het downloaden.|**--check-md5** = \[ NoCheck \| LogOnly \| FailIfDifferent \| FailIfDifferentOrMissing\]|
|Sluit bestanden uit op basis van een patroon.|**--exclude-path**|
|Geef op hoe gedetailleerd u uw synchronisatielogboekgegevens wilt laten zijn.|**--log-level** = \[ \|WAARSCHUWINGSFOUT \| INFO \| GEEN\]|

Zie opties voor een volledige lijst met [vlaggen.](storage-ref-azcopy-sync.md#options)

> [!NOTE]
> De `--recursive` vlag is standaard ingesteld op `true` . De `--exclude-pattern` vlaggen en zijn alleen van toepassing op `--include-pattern` bestandsnamen en niet op andere onderdelen van het bestandspad. 

## <a name="next-steps"></a>Volgende stappen

Meer voorbeelden vindt u in deze artikelen:

- [Voorbeelden: Uploaden](storage-use-azcopy-blobs-upload.md)
- [Voorbeelden: Downloaden](storage-use-azcopy-blobs-download.md)
- [Voorbeelden: Kopiëren tussen accounts](storage-use-azcopy-blobs-copy.md)
- [Voorbeelden: Amazon S3-bucket](storage-use-azcopy-s3.md)
- [Voorbeelden: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Voorbeelden: Azure Files](storage-use-azcopy-files.md)
- [Zelfstudie: on-premises gegevens migreren naar cloudopslag met behulp van AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Zie deze artikelen voor het configureren van instellingen, het optimaliseren van prestaties en het oplossen van problemen:

- [AzCopy-configuratie-instellingen](storage-ref-azcopy-configuration-settings.md)
- [De prestaties van AzCopy optimaliseren](storage-use-azcopy-optimize.md)
- [Problemen met AzCopy V10 in Azure Storage met behulp van logboekbestanden](storage-use-azcopy-configure.md)

