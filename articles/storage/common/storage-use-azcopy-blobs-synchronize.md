---
title: Synchroniseren met Azure Blob Storage met behulp van AzCopy V10 toevoegen | Microsoft Docs
description: Dit artikel bevat een verzameling AzCopy-voorbeeld opdrachten die u helpen bij het synchroniseren met Azure Blob-opslag.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: ab1da88899ba2b90e303da107631e3878b3a8b58
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102635872"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy-v10"></a>Synchroniseren met Azure Blob Storage met behulp van AzCopy V10 toevoegen

U kunt lokale opslag met Azure Blob-opslag synchroniseren met behulp van het AzCopy V10 toevoegen-opdracht regel programma. 

U kunt de inhoud van een lokaal bestands systeem synchroniseren met een BLOB-container. U kunt ook containers en virtuele mappen synchroniseren met elkaar. Synchronisatie is een manier. Met andere woorden, u kunt kiezen welke van deze twee eind punten de bron is en welke het doel is. Synchronisatie gebruikt ook server-naar-server-Api's. De voor beelden die in deze sectie worden weer gegeven, werken ook met accounts die een hiërarchische naam ruimte hebben. 

> [!NOTE]
> De huidige release van AzCopy synchroniseert niet tussen andere bronnen en doelen (bijvoorbeeld: File Storage of Amazon Web Services (AWS) S3-buckets).

Zie de koppelingen die worden weer gegeven in de sectie [volgende stappen](#next-steps) van dit artikel om voor beelden te bekijken van andere typen taken, zoals het uploaden van bestanden, het downloaden van blobs of het kopiëren van blobs tussen accounts.

## <a name="get-started"></a>Aan de slag

Zie het artikel aan de [slag met AzCopy](storage-use-azcopy-v10.md) om AzCopy te downloaden en meer te weten te komen over de manieren waarop u autorisatie referenties kunt opgeven voor de opslag service.

> [!NOTE] 
> In de voor beelden in dit artikel wordt ervan uitgegaan dat u autorisatie referenties hebt ingesteld met behulp van Azure Active Directory (Azure AD).
>
> Als u liever een SAS-token gebruikt om toegang te verlenen tot BLOB-gegevens, kunt u dat token toevoegen aan de bron-URL in elke AzCopy-opdracht. Bijvoorbeeld: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="guidelines"></a>Richtlijnen

- Met de opdracht [Sync](storage-ref-azcopy-sync.md) worden bestands namen en tijds tempels met de laatste wijziging vergeleken. Stel de `--delete-destination` optionele vlag in op een waarde van `true` of `prompt` om bestanden in de doelmap te verwijderen als deze bestanden niet meer in de bron directory aanwezig zijn.

- Als u de `--delete-destination` vlag instelt op `true` , worden bestanden door AzCopy verwijderd zonder dat er een prompt wordt gegeven. Als u wilt dat er een prompt wordt weer gegeven voordat AzCopy een bestand verwijdert, stelt u de `--delete-destination` vlag in op `prompt` .

- Als u onbedoeld verwijderen wilt voor komen, moet u ervoor zorgen dat u de functie voor [voorlopig verwijderen](../blobs/soft-delete-blob-overview.md) inschakelt voordat u de `--delete-destination=prompt|true` vlag gebruikt.

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>Een container met wijzigingen in een lokaal bestands systeem bijwerken

In dit geval is de container het doel, en is het lokale bestandssysteem de bron. 

> [!TIP]
> In dit voor beeld worden padvariabelen met enkele aanhalings tekens (' ') Inge sloten. Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Voorbeeld** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>Een lokaal bestandssysteem bijwerken met wijzigingen in een container

In dit geval is het lokale bestands systeem het doel en de container de bron.

> [!TIP]
> In dit voor beeld worden padvariabelen met enkele aanhalings tekens (' ') Inge sloten. Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Voorbeeld** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

## <a name="update-a-container-with-changes-in-another-container"></a>Een container bijwerken met wijzigingen in een andere container

De eerste container die wordt weer gegeven in deze opdracht is de bron. De tweede is de bestemming.

> [!TIP]
> In dit voor beeld worden padvariabelen met enkele aanhalings tekens (' ') Inge sloten. Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Voorbeeld** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>Een Directory bijwerken met wijzigingen in een map in een andere container

De eerste map die wordt weer gegeven in deze opdracht is de bron. De tweede is de bestemming.

> [!TIP]
> In dit voor beeld worden padvariabelen met enkele aanhalings tekens (' ') Inge sloten. Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd.exe). Als u een Windows-opdracht shell (cmd.exe) gebruikt, plaatst u path-argumenten met dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Voorbeeld** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="synchronize-with-optional-flags"></a>Synchroniseren met optionele vlaggen

U kunt de synchronisatie bewerking aanpassen met behulp van optionele vlaggen. Hier volgen enkele voor beelden.

|Scenario|Vlag|
|---|---|
|Opgeven hoe strikt MD5-hashes moeten worden gevalideerd bij het downloaden.|**--Check-MD5** = \[ Niet-gecontroleerd \| aanmelden \| FailIfDifferent \| FailIfDifferentOrMissing\]|
|Bestanden uitsluiten op basis van een patroon.|**--exclude-pad**|
|Geef op hoe gedetailleerd u de logboek vermeldingen voor synchronisatie wilt maken.|**--logboek niveau** = \[ \|fout gegevens voor waarschuwing \| \| geen\]|

Zie [Opties](storage-ref-azcopy-sync.md#options)voor een volledige lijst.

## <a name="next-steps"></a>Volgende stappen

Meer voor beelden vindt u in deze artikelen:

- [Voorbeelden: Uploaden](storage-use-azcopy-blobs-upload.md)
- [Voorbeelden: Downloaden](storage-use-azcopy-blobs-download.md)
- [Voorbeelden: Kopiëren tussen accounts](storage-use-azcopy-blobs-copy.md)
- [Voorbeelden: Amazon S3-bucket](storage-use-azcopy-s3.md)
- [Voor beelden: Azure Files](storage-use-azcopy-files.md)
- [Zelfstudie: on-premises gegevens migreren naar cloudopslag met behulp van AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Configureren, optimaliseren en problemen oplossen in AzCopy](storage-use-azcopy-configure.md)