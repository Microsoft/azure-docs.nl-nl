---
title: Azure Data Box Disk limieten | Microsoft Docs
description: Hierin worden systeem limieten en aanbevolen grootten voor de Microsoft Azure Data Box Disk beschreven.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: e003d0121721838bd5ae038a3a8b4d1b8cd9d1eb
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573187"
---
# <a name="azure-data-box-disk-limits"></a>Azure Data Box Disk limieten


Houd rekening met deze beperkingen wanneer u uw Microsoft Azure Data Box Disk oplossing implementeert en gebruikt.

## <a name="data-box-service-limits"></a>Data Box-Service limieten

 - Data Box-Service is beschikbaar in de Azure-regio's die worden vermeld in de [Beschik baarheid van regio's](data-box-disk-overview.md#region-availability).
 - Er wordt één opslag account ondersteund met Data Box Disk.

## <a name="data-box-disk-performance"></a>Prestaties Data Box Disk

Bij een test met schijven die verbonden waren via USB 3.0 was de prestatie van de schijven maximaal 430 MB/s. De werkelijke waarden variëren, afhankelijk van de gebruikte bestandsgrootte. Voor kleinere bestanden zijn de prestaties lager.

## <a name="azure-storage-limits"></a>Limieten voor Azure Storage

In deze sectie worden de limieten voor de Azure Storage-service en de vereiste naam conventies voor Azure Files, Azure Block blobs en Azure-pagina-blobs beschreven, zoals van toepassing op de Data Box Service. Controleer de opslag limieten zorgvuldig en volg alle aanbevelingen.

Voor de meest recente informatie over Azure Storage-service limieten en aanbevolen procedures voor het benoemen van shares, containers en bestanden, gaat u naar:

- [Naamgeving en verwijzen naar containers](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Naamgeving van en verwijzing naar shares](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Conventies voor blok-blobs en pagina-blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Als er bestanden of mappen zijn die de Azure Storage service limieten overschrijden of niet voldoen aan de naamgevings conventies van Azure Files/blob, worden deze bestanden of mappen niet opgenomen in de Azure Storage via de Data Box-Service.

## <a name="data-upload-caveats"></a>Voorbehouden voor het uploaden van gegevens

- Kopieer gegevens niet rechtstreeks naar de schijven. Gegevens kopiëren naar vooraf gemaakte *BlockBlob*-,*PageBlob*-en *AzureFile* -mappen.
- Een map onder de *BlockBlob* en *PageBlob* is een container. Zo worden bijvoorbeeld containers gemaakt als *BlockBlob/container* en *PageBlob/container*.
- Als u een bestaand Azure-object (zoals een blob) in de Cloud hebt met dezelfde naam als het object dat wordt gekopieerd, wordt de naam van het bestand in de cloud in Data Box Disk gewijzigd in het bestand (1).
- Elk bestand dat in *BlockBlob* -en *PageBlob* -shares wordt geschreven, wordt respectievelijk geüpload als een blok-Blob en een pagina-blob.
- Een lege Directory-hiërarchie (zonder bestanden) die is gemaakt onder de mappen *BlockBlob* en *PageBlob* is niet geüpload.
- Als er fouten zijn bij het uploaden van gegevens naar Azure, wordt er een fouten logboek gemaakt in het doel-opslag account. Het pad naar dit fouten logboek is beschikbaar in de Portal wanneer het uploaden is voltooid en u kunt het logboek controleren om de corrigerende actie te ondernemen. Verwijder geen gegevens uit de bron zonder de geüploade gegevens te verifiëren.
- Meta gegevens en NTFS-machtigingen voor bestanden blijven niet behouden wanneer de gegevens naar Azure Files worden geüpload. Het kenmerk *Laatst gewijzigd* van de bestanden wordt bijvoorbeeld niet bewaard wanneer de gegevens worden gekopieerd.
- Als u beheerde schijven in de volg orde hebt opgegeven, raadpleegt u de volgende aanvullende overwegingen:

    - Er kan slechts één beheerde schijf met een bepaalde naam in een resourcegroep aanwezig zijn voor alle vooraf gemaakte mappen en voor de hele Data Box Disk. Dit betekent dat de VHD's die naar de vooraf gemaakte mappen zijn geüpload, unieke namen moeten hebben. Zorg ervoor dat de opgegeven naam niet overeenkomt met een bestaande beheerde schijf in een resourcegroep. Als VHD's dezelfde naam hebben, wordt er slechts één VHD geconverteerd naar een beheerde schijf met die naam. De overige VHD's worden als pagina-blobs geüpload in het faseringsopslagaccount.
    - Kopieer de VHD's altijd naar een van de vooraf gemaakte mappen. Als u de VHD's buiten deze mappen kopieert of kopieert naar een map die u zelf hebt gemaakt, worden de VHD's, en niet de beheerde schijven, naar een Azure Storage-account als pagina-blobs geüpload.
    - Alleen de vaste VHD's kunnen worden geüpload om beheerde schijven te maken. Dynamische VHD's, differentiërende VHD's of VHDX-bestanden worden niet ondersteund.
    - Niet-VHD-bestanden die zijn gekopieerd naar de voorgemaakte mappen voor beheerde schijven, worden niet geconverteerd naar een beheerde schijf.

## <a name="azure-storage-account-size-limits"></a>Maximale grootte van Azure Storage-account

Dit zijn de limieten voor de grootte van gegevens die kunnen worden gekopieerd naar een opslag account. Zorg ervoor dat de gegevens die u uploadt, voldoen aan deze limieten. 

| Type gegevens             | Standaardlimiet          |
|--------------------------|------------------------|
| blok-blob, pagina-BLOB    | Zie [Azure Blob-opslag schaal doelen](../storage/blobs/scalability-targets.md#scale-targets-for-blob-storage), [Azure Standard-opslag schaal doelen](../storage/common/scalability-targets-standard-account.md#scale-targets-for-standard-storage-accounts)en [Azure files-schaal doel](../storage/files/storage-files-scale-targets.md#file-share-and-file-scale-targets)voor actuele informatie over deze limieten. <br /><br /> De limieten omvatten gegevens uit alle bronnen, inclusief Data Box Disk.|


## <a name="azure-object-size-limits"></a>Limieten voor Azure-object grootte

Dit zijn de groottes van de Azure-objecten die kunnen worden geschreven. Zorg ervoor dat alle bestanden die worden geüpload voldoen aan deze limieten.

| Azure-object type | Standaardlimiet                                             |
|-------------------|-----------------------------------------------------------|
| Blok-BLOB        | ~ 4,75 TiB                                                 |
| Pagina-BLOB         | 8 TiB <br> (Elk bestand dat is geüpload in de pagina-BLOB-indeling moet 512 bytes zijn uitgelijnd, anders mislukt het uploaden. <br> Zowel de VHD als de VHDX zijn 512 bytes uitgelijnd.) |
|Azure Files        | 1 TiB <br> Met maximaal grootte van share is 5 TiB     |
| Managed Disks     |4 TiB <br> Zie voor meer informatie over grootte en limieten: <li>[Schaalbaarheids doelen voor beheerde schijven](../virtual-machines/disks-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure Block blob-, page Blob-en file naamgevings conventies

| Entiteit                                       | Conventies                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Container namen voor blok-Blob en pagina-BLOB <br> Bestands share namen voor Azure Files | Moet een geldige DNS-naam zijn van 3 tot 63 tekens lang zijn. <br>  Moet beginnen met een letter of cijfer. <br> Mag alleen kleine letters, cijfers en het koppel teken (-) bevatten. <br> Elk koppelteken (-) moet direct worden voorafgegaan en gevolgd door een letter of cijfer. <br> In namen worden geen opeenvolgende koppeltekens toegestaan. |
| Map-en bestands namen voor Azure files     |<li> Case: behouden, hoofdletter gevoelig en mag niet langer zijn dan 255 tekens. </li><li> Kan niet eindigen met een slash (/). </li><li>Als dit wordt gegeven, wordt deze automatisch verwijderd. </li><li> De volgende tekens zijn niet toegestaan: <code>" \\ / : \| < > * ?</code></li><li> Gereserveerde tekens voor URL's moeten op de juiste wijze van een escape-teken zijn voorzien. </li><li> Ongeldige tekens voor URL-paden zijn niet toegestaan. Code punten zoals \\ uE000 zijn geen geldige Unicode-tekens. Sommige ASCII-of Unicode-tekens, zoals Stuur codes (0x00 tot 0x1F, \\ u0081, enzovoort), zijn ook niet toegestaan. Zie RFC 2616, Section 2,2: Basic rules and RFC 3987 (Engelstalig) voor regels voor het Unicode-teken reeksen in HTTP/1.1. </li><li> De volgende bestands namen zijn niet toegestaan: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK $, punt teken (.) en twee punten (..).</li>|
| Blobnamen voor blok-blob en pagina-blob      | Blobnamen zijn hoofdlettergevoelig en kunnen elke combinatie van tekens bevatten. <br> Een blobnaam moet 1 tot 1024 tekens bevatten. <br> Gereserveerde tekens voor URL's moeten op de juiste wijze van een escape-teken zijn voorzien. <br>Het aantal padsegmenten dat de blobnaam omvat, mag niet meer dan 254 zijn. Een padsegment is de tekenreeks tussen opeenvolgende scheidingstekens (bijvoorbeeld de slash '/') die overeenkomt met de naam van een virtuele map. |

## <a name="managed-disk-naming-conventions"></a>Naamgevings regels voor beheerde schijven

| Entiteit | Conventies                                             |
|-------------------|-----------------------------------------------------------|
| Namen van beheerde schijven       | <li> De naam moet 1 tot 80 tekens lang zijn. </li><li> De naam moet beginnen met een letter of cijfer, eindigen met een letter, cijfer of onderstrepings teken. </li><li> De naam mag alleen letters, cijfers, onderstrepings tekens, punten of afbreek streepjes bevatten. </li><li>   De naam mag geen spaties of bevatten `/` .                                              |

## <a name="next-steps"></a>Volgende stappen

- [Data Box disk systeem vereisten](data-box-disk-system-requirements.md) controleren