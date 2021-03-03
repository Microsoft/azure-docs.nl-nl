---
title: Vereisten voor Azure import/export-service | Microsoft Docs
description: Meer informatie over de software-en hardwarevereisten voor Azure import/export-service.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 0bfc09a372584a25c23060cef33d1f698e6d5ff3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712603"
---
# <a name="azure-importexport-system-requirements"></a>Systeemvereisten voor Azure Import/Export

In dit artikel worden de belangrijkste vereisten voor de Azure import/export-service beschreven. We raden u aan de informatie zorgvuldig te bekijken voordat u de import/export-service gebruikt en vervolgens naar de gewenste gegevens te verwijzen tijdens de bewerking.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

Om de harde schijven voor te bereiden met het WAImportExport-hulp programma, worden het volgende **64-bits besturings systeem ondersteund dat ondersteuning biedt voor BitLocker-stationsversleuteling** .


|Platform |Versie |
|---------|---------|
|Windows     | Windows 7 Enter prise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enter prise, Windows 8,1 Pro, Windows 8,1 ENTER prise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Andere vereiste software voor Windows-client

|Platform |Versie |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Ondersteunde opslagaccounts

De Azure import/export-service ondersteunt de volgende typen opslag accounts:

- Standard Algemeen v2-opslag accounts (aanbevolen voor de meeste scenario's)
- Blob Storage-accounts
- Algemeen v1-opslag accounts (klassiek of Azure Resource Manager implementaties)

> [!IMPORTANT]
> Ondersteuning voor het protocol Network File System (NFS) 3,0 in Azure Blob-opslag wordt niet ondersteund met Azure import/export.

Zie [overzicht van Azure Storage-accounts](../storage/common/storage-account-overview.md)voor meer informatie over opslag accounts.

Elke taak kan worden gebruikt om gegevens over te dragen van slechts één opslag account. Met andere woorden, één import/export-taak kan niet over meerdere opslag accounts worden verdeeld. Zie [een opslag account maken](../storage/common/storage-account-create.md)voor meer informatie over het maken van een nieuw opslag account.

> [!IMPORTANT]
> Voor opslag accounts waarop de functie [service-eind punten van Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md) is ingeschakeld, gebruikt u de instelling **vertrouwde micro soft-Services toestaan...** om de [import/export](../storage/common/storage-network-security.md) -service in te scha kelen voor het uitvoeren van import/export van gegevens van/naar Azure.

## <a name="supported-storage-types"></a>Ondersteunde opslagtypen

De volgende lijst met opslag typen wordt ondersteund met Azure import/export-service.


|Taak  |Opslag service |Ondersteund  |Niet ondersteund  |
|---------|---------|---------|---------|
|Importeren     |  Azure Blob Storage <br><br> Azure File Storage       | Blok-blobs en pagina-blobs die worden ondersteund <br><br> Ondersteunde bestanden          |
|Exporteren     |   Azure Blob Storage       | Blok-blobs, pagina-blobs en toevoeg-blobs worden ondersteund         | Azure Files wordt niet ondersteund


## <a name="supported-hardware"></a>Ondersteunde hardware

Voor de Azure import/export-service hebt u ondersteunde schijven nodig om gegevens te kopiëren.

### <a name="supported-disks"></a>Ondersteunde schijven

De volgende lijst met schijven wordt ondersteund voor gebruik met de import/export-service.


|Schijftype  |Grootte  |Ondersteund |
|---------|---------|---------|
|SSD    |   2,5 '      |SATA III          |
|HDD     |  2,5 '<br>3,5 '       |SATA II, SATA III         |

De volgende schijf typen worden niet ondersteund:

- USBs.
- Externe HDD met ingebouwde USB-adapter.
- Schijven die zich in het hoofdletter gebruik van een externe HDD bevinden.

Eén import/export-taak kan het volgende hebben:

- Maxi maal 10 HDD/Ssd's.
- Een combi natie van HDD/SSD van elke grootte.

Een groot aantal stations kan worden verdeeld over meerdere taken en er zijn geen beperkingen voor het aantal taken dat kan worden gemaakt. Voor import taken wordt alleen het eerste gegevens volume op het station verwerkt. Het gegevens volume moet zijn geformatteerd met NTFS.

Bij het voorbereiden van harde schijven en het kopiëren van de gegevens met het WAImportExport-hulp programma kunt u externe USB-adapters gebruiken. De meeste kant-en-klare USB 3,0-adapters of hoger moeten werken.

## <a name="next-steps"></a>Volgende stappen

* [Gegevensoverdracht met het AzCopy-opdrachtregelprogramma](../storage/common/storage-use-azcopy-v10.md)