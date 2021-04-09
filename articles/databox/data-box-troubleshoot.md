---
title: Los problemen op uw Azure Data Box op Azure Data Box Heavy
description: Hierin wordt beschreven hoe u problemen oplost die worden weer gegeven in Azure Data Box en Azure Data Box Heavy bij het kopiëren van gegevens naar deze apparaten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 09/10/2020
ms.author: alkohli
ms.openlocfilehash: bb70946fda4fad7a42fd885a2515cb0d82698eca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92124671"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Problemen oplossen die verband houden met Azure Data Box en Azure Data Box Heavy

In dit artikel vindt u informatie over het oplossen van problemen die kunnen optreden bij het gebruik van de Azure Data Box of Azure Data Box Heavy voor import orders. Het artikel bevat de lijst met mogelijke fouten die worden weer gegeven wanneer gegevens worden gekopieerd naar het Data Box of wanneer gegevens worden geüpload van Data Box voor een import volgorde.

De informatie in dit artikel is niet van toepassing op het exporteren van orders die zijn gemaakt voor Data Box.

## <a name="error-classes"></a>Fout klassen

De fouten in Data Box en Data Box Heavy worden als volgt samenvatten:

| Fout categorie *        | Description        | Aanbevolen actie    |
|----------------------------------------------|---------|--------------------------------------|
| Container-of share namen | De container-of share namen volgen niet de Azure-naamgevings regels.  |Down load de fout lijsten. <br> Wijzig de naam van de containers of shares. [Meer informatie](#container-or-share-name-errors).  |
| Maximale grootte van container of share | De totale gegevens in containers of shares overschrijden de limiet van Azure.   |Down load de fout lijsten. <br> Verminder de totale hoeveelheid gegevens in de container of de share. [Meer informatie](#container-or-share-size-limit-errors).|
| Maximale object-of bestands grootte | Het object of de bestanden in containers of shares overschrijden de limiet van Azure.|Down load de fout lijsten. <br> Verklein de bestands grootte in de container of de share. [Meer informatie](#object-or-file-size-limit-errors). |    
| Gegevens-of bestands type | De gegevens indeling of het bestands type wordt niet ondersteund. |Down load de fout lijsten. <br> Voor pagina-blobs of Managed disks zorgt u ervoor dat de gegevens 512-bytes zijn uitgelijnd en naar de vooraf gemaakte mappen worden gekopieerd. [Meer informatie](#data-or-file-type-errors). |
| Niet-kritieke BLOB-of bestands fouten  | De BLOB-of bestands namen voldoen niet aan de Azure-naamgevings regels of het bestands type wordt niet ondersteund. | Deze blobs of bestanden worden mogelijk niet gekopieerd of de namen kunnen worden gewijzigd. [Meer informatie over het oplossen van deze fouten](#non-critical-blob-or-file-errors). |

\* De eerste vier fout categorieën zijn kritieke fouten en moeten worden opgelost voordat u kunt door gaan met het voorbereiden van de verzen ding.


## <a name="container-or-share-name-errors"></a>Fouten in containers of share namen

Dit zijn fouten met betrekking tot container-en share namen.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Fout beschrijving:** De container-of share naam moet tussen de 3 en 63 tekens lang zijn. 

**Voorgestelde oplossing:** De map onder de Data Box of Data Box Heavy share (SMB/NFS) waarnaar u gegevens hebt gekopieerd, wordt een Azure-container in uw opslag account. 

- Down load op de pagina **verbinding maken en kopiëren** van de lokale web-UI van het apparaat en controleer de fout bestanden om de mapnamen met problemen te identificeren.
- Wijzig de mapnaam onder de Data Box of Data Box Heavy share om ervoor te zorgen dat:

    - De naam moet tussen de 3 en 63 tekens lang zijn.
    - De namen mogen alleen letters, cijfers en afbreek streepjes bevatten.
    - De namen mogen niet beginnen of eindigen met een afbreek streepje.
    - De namen mogen geen opeenvolgende afbreek streepjes bevatten.
    - Voor beelden van geldige namen: `my-folder-1` , `my-really-extra-long-folder-111`
    - Voor beelden van namen die ongeldig zijn: `my-folder_1` , `my` , `--myfolder` , `myfolder--` , `myfolder!`

    Zie de Azure-naamgevings conventies voor [container namen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) en [share namen](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)voor meer informatie.


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Fout beschrijving:** De container-of share naam mag alleen bestaan uit letters, cijfers of afbreek streepjes.

**Voorgestelde oplossing:** De map onder de Data Box of Data Box Heavy share (SMB/NFS) waarnaar u gegevens hebt gekopieerd, wordt een Azure-container in uw opslag account. 

- Down load op de pagina **verbinding maken en kopiëren** van de lokale web-UI van het apparaat en controleer de fout bestanden om de mapnamen met problemen te identificeren.
- Wijzig de mapnaam onder de Data Box of Data Box Heavy share om ervoor te zorgen dat:

    - De naam moet tussen de 3 en 63 tekens lang zijn.
    - De namen mogen alleen letters, cijfers en afbreek streepjes bevatten.
    - De namen mogen niet beginnen of eindigen met een afbreek streepje.
    - De namen mogen geen opeenvolgende afbreek streepjes bevatten.
    - Voor beelden van geldige namen: `my-folder-1` , `my-really-extra-long-folder-111`
    - Voor beelden van namen die ongeldig zijn: `my-folder_1` , `my` , `--myfolder` , `myfolder--` , `myfolder!`

    Zie de Azure-naamgevings conventies voor [container namen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) en [share namen](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)voor meer informatie.

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Fout beschrijving:** De container namen en share namen mogen niet beginnen of eindigen met afbreek streepjes en kunnen geen opeenvolgende afbreek streepjes bevatten.

**Voorgestelde oplossing:** De map onder de Data Box of Data Box Heavy share (SMB/NFS) waarnaar u gegevens hebt gekopieerd, wordt een Azure-container in uw opslag account. 

- Down load op de pagina **verbinding maken en kopiëren** van de lokale web-UI van het apparaat en controleer de fout bestanden om de mapnamen met problemen te identificeren.
- Wijzig de mapnaam onder de Data Box of Data Box Heavy share om ervoor te zorgen dat:

    - De naam moet tussen de 3 en 63 tekens lang zijn.
    - De namen mogen alleen letters, cijfers en afbreek streepjes bevatten.
    - De namen mogen niet beginnen of eindigen met een afbreek streepje.
    - De namen mogen geen opeenvolgende afbreek streepjes bevatten.
    - Voor beelden van geldige namen: `my-folder-1` , `my-really-extra-long-folder-111`
    - Voor beelden van namen die ongeldig zijn: `my-folder_1` , `my` , `--myfolder` , `myfolder--` , `myfolder!`

    Zie de Azure-naamgevings conventies voor [container namen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) en [share namen](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)voor meer informatie.
    
### <a name="error_file_or_directory_name_illegal"></a>ERROR_FILE_OR_DIRECTORY_NAME_ILLEGAL

**Fout beschrijving**: de map of de container namen bevatten ongeldige tekens.

**Voorgestelde oplossing**: de map of de container namen die u hebt gekopieerd, bevatten niet-ondersteunde tekens.

- Down load op de pagina verbinding maken en kopiëren van de lokale webinterface en controleer de fout bestanden om de mapnamen met problemen te identificeren. 
- Wijzig de naam van de map of containers om er zeker van te zijn dat ze compatibel zijn met Azure-naamgevings conventies.

Zie de Azure-naamgevings conventies voor [directory's](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) en [containers](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)voor meer informatie.

## <a name="container-or-share-size-limit-errors"></a>Fouten in de container of share grootte beperken

Dit zijn fouten met betrekking tot gegevens die de omvang van gegevens overschrijden die zijn toegestaan in een container of een share.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Fout beschrijving:** Azure-bestands share beperkt een share tot 5 TiB aan gegevens en grote bestands shares zijn niet ingeschakeld voor het opslag account. Deze limiet is overschreden voor sommige shares.

**Voorgestelde oplossing:** Down load en controleer de fout bestanden op de pagina **verbinding maken en kopiëren** van de lokale webgebruikersinterface.

- Bepaal de mappen met dit probleem uit de fout logboeken en zorg ervoor dat de bestanden in die map onder 5 TiB staan.
- De limiet van 5 TiB is niet van toepassing op een opslag account waarmee grote bestands shares zijn toegestaan. U moet echter grote bestands shares hebben geconfigureerd wanneer u uw bestelling plaatst. 
  - Neem contact op met [Microsoft ondersteuning](data-box-disk-contact-microsoft-support.md) en vraag een nieuw verzend label aan.
  - [Schakel grote bestands shares in voor het opslag account.](../storage/files/storage-files-how-to-create-large-file-share.md#enable-large-files-shares-on-an-existing-account)
  - [Vouw de bestands shares in het opslag account uit](../storage/files/storage-files-how-to-create-large-file-share.md#expand-existing-file-shares) en stel het quotum in op 100 Tib.
  
  
## <a name="object-or-file-size-limit-errors"></a>Fouten bij de object-of bestands grootte limiet

Dit zijn fouten met betrekking tot gegevens die de maximale grootte van een object overschrijden of het bestand dat in Azure is toegestaan. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Fout beschrijving:** De bestands grootte overschrijdt de maximale bestands grootte voor het uploaden.

**Voorgestelde oplossing:** De BLOB of de bestands grootte overschrijdt de Maxi maal toegestane limiet voor het uploaden.

- Down load en controleer de fout bestanden op de pagina **verbinding maken en kopiëren** van de lokale webgebruikersinterface.
- Zorg ervoor dat de BLOB-en bestands grootten de limieten voor de grootte van Azure-objecten niet overschrijden.

## <a name="data-or-file-type-errors"></a>Gegevens of bestands type fouten

Dit zijn fouten met betrekking tot niet-ondersteund bestands type of gegevens type gevonden in de container of share. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Fout beschrijving:** De BLOB of het bestand is onjuist uitgelijnd.

**Voorgestelde oplossing:** De pagina-BLOB-share op Data Box of Data Box Heavy ondersteunt alleen bestanden van 512 bytes uitgelijnd (bijvoorbeeld VHD/VHDX). Alle gegevens die zijn gekopieerd naar de pagina-blob, worden geüpload naar Azure als pagina-blobs.

Verwijder alle niet-VHD/VHDX-gegevens van de pagina-BLOB-share. U kunt shares gebruiken voor blok-BLOB of Azure files voor algemene gegevens.

Zie [overzicht van pagina-blobs](../storage/blobs/storage-blob-pageblob-overview.md)voor meer informatie.

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Fout beschrijving:** Er is een niet-ondersteund bestands type aanwezig in een beheerde schijf share. Alleen vaste Vhd's zijn toegestaan.

**Voorgestelde oplossing:**

- Zorg ervoor dat u alleen de vaste Vhd's uploadt om beheerde schijven te maken.
- VHDX-bestanden of **dynamische** en **differentiërende** vhd's worden niet ondersteund.

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Fout beschrijving:** Een directory is niet toegestaan in een van de bestaande mappen voor de Managed disks. In deze mappen zijn alleen vaste Vhd's toegestaan.

**Voorgestelde oplossing:** Voor Managed disks, binnen elke share, worden de volgende drie mappen gemaakt die overeenkomen met containers in uw opslag account: Premium-SSD, Standard-HDD en Standard-SSD. Deze mappen komen overeen met de prestatie tier van de beheerde schijf.

- Zorg ervoor dat u de pagina-blobgegevens (Vhd's) kopieert naar een van deze bestaande mappen.
- Een map of directory is niet toegestaan in deze bestaande mappen. Verwijder alle mappen die u hebt gemaakt in de reeds bestaande mappen.

Zie [kopiëren naar Managed disks](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)voor meer informatie.

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**Fout beschrijving:** Symbolische koppelingen zijn niet toegestaan in Linux. 

**Voorgestelde oplossing:** De symbolische koppelingen zijn doorgaans koppelingen, sluizen en andere dergelijke bestanden. Verwijder de koppelingen of los de koppelingen op en kopieer de gegevens.


## <a name="non-critical-blob-or-file-errors"></a>Niet-kritieke BLOB-of bestands fouten

De volgende sectie bevat een overzicht van alle niet-kritieke fouten met betrekking tot de namen van blobs, bestanden of containers die tijdens het kopiëren van de gegevens worden weer gegeven. Als deze fouten aanwezig zijn, worden de namen gewijzigd in overeenstemming met de Azure-naamgevings conventies. De bijbehorende order status voor het uploaden van gegevens wordt **voltooid met waarschuwingen**.  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Fout beschrijving:** De BLOB-of bestands namen bevatten niet-ondersteunde Stuur codes.

**Voorgestelde oplossing:** De blobs of de bestanden die u hebt gekopieerd, bevatten namen met niet-ondersteunde tekens.

Down load en controleer de fout bestanden op de pagina **verbinding maken en kopiëren** van de lokale webgebruikersinterface.
Verwijder de bestanden om niet-ondersteunde tekens te verwijderen of de naam ervan te wijzigen.

Zie de Azure-naamgevings conventies voor [BLOB-namen](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) en [bestands namen](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)voor meer informatie.

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Fout beschrijving:** De BLOB-of bestands namen bevatten ongeldige tekens.

**Voorgestelde oplossing:** De blobs of de bestanden die u hebt gekopieerd, bevatten namen met niet-ondersteunde tekens.

Down load en controleer de fout bestanden op de pagina **verbinding maken en kopiëren** van de lokale webgebruikersinterface.
Verwijder de bestanden om niet-ondersteunde tekens te verwijderen of de naam ervan te wijzigen.

Zie de Azure-naamgevings conventies voor [BLOB-namen](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) en [bestands namen](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)voor meer informatie.


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Fout beschrijving:** De BLOB-of bestands namen eindigen met ongeldige tekens.

**Voorgestelde oplossing:** De blobs of de bestanden die u hebt gekopieerd, bevatten namen met niet-ondersteunde tekens.

Down load en controleer de fout bestanden op de pagina **verbinding maken en kopiëren** van de lokale webgebruikersinterface.
Verwijder de bestanden om niet-ondersteunde tekens te verwijderen of de naam ervan te wijzigen.

Zie de Azure-naamgevings conventies voor [BLOB-namen](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) en [bestands namen](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)voor meer informatie.


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Fout beschrijving:** De BLOB of bestands naam bevat te veel padsegmenten.

**Voorgestelde oplossing:** De blobs of de bestanden die u hebt gekopieerd, overschrijden het maximum aantal padsegmenten. Een padsegment is de teken reeks tussen opeenvolgende scheidings tekens, bijvoorbeeld de slash/.

- Down load en controleer de fout bestanden op de pagina **verbinding maken en kopiëren** van de lokale webgebruikersinterface.
- Zorg ervoor dat de [namen](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) van de BLOB en de [bestands namen](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) voldoen aan de Azure-naamgevings conventies.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Fout beschrijving:** De BLOB of bestands naam is te lang.

**Voorgestelde oplossing:** De BLOB of de bestands namen overschrijden de maximum lengte.

- Down load en controleer de fout bestanden op de pagina **verbinding maken en kopiëren** van de lokale webgebruikersinterface.
- De naam van de BLOB mag niet langer zijn dan 1.024 tekens.
- Verwijder of wijzig de naam van de BLOB of bestanden zodat de namen niet langer zijn dan 1024 tekens.

Zie de Azure-naamgevings conventies voor BLOB-namen en bestands namen voor meer informatie.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Fout beschrijving:** Een van de segmenten in de BLOB of bestands naam is te lang.

**Voorgestelde oplossing:** Een van de padsegmenten in de BLOB of bestands naam overschrijdt het maximum aantal tekens. Een padsegment is de teken reeks tussen opeenvolgende scheidings tekens, bijvoorbeeld de slash/.

- Down load en controleer de fout bestanden op de pagina **verbinding maken en kopiëren** van de lokale webgebruikersinterface.
- Zorg ervoor dat de [namen](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) van de BLOB en de [bestands namen](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) voldoen aan de Azure-naamgevings conventies.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Fout beschrijving:** Er zijn onjuiste container namen opgegeven voor beheerde schijf shares.

**Voorgestelde oplossing:** Voor beheerde schijven worden binnen elke share de volgende mappen gemaakt die overeenkomen met containers in uw opslag account: Premium-SSD, Standard-HDD en Standard-SSD. Deze mappen komen overeen met de prestatie tier van de beheerde schijf.

- Zorg ervoor dat u de pagina-blobgegevens (Vhd's) kopieert naar een van deze bestaande mappen. Alleen gegevens van deze bestaande containers worden geüpload naar Azure.
- Andere mappen die op hetzelfde niveau worden gemaakt als Premium-SSD, Standard-HDD en Standard-SSD komen niet overeen met een geldige prestatie-laag en kunnen niet worden gebruikt.
- Bestanden of mappen verwijderen die buiten de prestatie lagen zijn gemaakt.

Zie [kopiëren naar Managed disks](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [vereisten voor het data Box Blob-opslag systeem](data-box-system-requirements-rest.md).