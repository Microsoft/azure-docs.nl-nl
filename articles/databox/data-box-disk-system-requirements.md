---
title: Systeemvereisten voor Microsoft Azure Data Box Disk | Microsoft Docs
description: Lees meer over de software- en netwerkvereisten voor uw Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/22/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 750ed8f65db04199ea284e69693bced65a1dc8d9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703130"
---
::: zone target="docs"

# <a name="azure-data-box-disk-system-requirements"></a>Systeemvereisten voor Azure Data Box Disk

In dit artikel worden de belangrijkste systeemvereisten beschreven voor uw oplossing met Microsoft Azure Data Box Disk en voor de clients die verbinding maken met de Data Box Disk. We adviseren om de informatie zorgvuldig door te nemen voordat u uw Data Box Disk implementeert. Als u tijdens de implementatie en daaropvolgende bewerkingen nog vragen hebt, kunt u de informatie er altijd nog even bij pakken.

De systeemvereisten omvatten de ondersteunde platforms voor clients die verbinding maken met schijven, ondersteunde opslagaccounts en opslagtypen.

::: zone-end

::: zone target="chromeless"

## <a name="review-prerequisites"></a>Vereisten controleren

1. U moet uw Data Box Disk hebben besteld met behulp van [Zelfstudie: Azure Data Box Disk bestellen](data-box-disk-deploy-ordered.md). U hebt de schijven en één aansluitkabel per schijf ontvangen.
2. U hebt een clientcomputer beschikbaar van waaruit u de gegevens kunt kopiëren. De clientcomputer moet voldoen aan deze vereisten:

    - Er is een ondersteund besturingssysteem geïnstalleerd.
    - Andere vereiste software is geïnstalleerd.

::: zone-end

## <a name="supported-operating-systems-for-clients"></a>Ondersteunde besturingssystemen voor clients

Hier volgt een lijst met de ondersteunde besturingssystemen voor het ontgrendelen van de schijf en het kopiëren van gegevens via de clients die zijn verbonden met de Data Box Disk.

| **Besturingssysteem** | **Geteste versies** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows (64-bits) |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6.9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Andere vereiste software voor Windows-clients

Voor Windows-clients moet de volgende software ook zijn geïnstalleerd.

| **Software**| **Versie** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.1|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Andere vereiste software voor Linux-clients

Voor Linux-clients wordt de volgende vereiste software geïnstalleerd door de Data Box Disk-werkset:

- dislocker
- OpenSSL

## <a name="supported-connection"></a>Ondersteunde verbinding

De clientcomputer met de gegevens moet een USB 3.0-poort of hoger hebben. De schijven maken verbinding met deze client met behulp van de meegeleverde kabel.

## <a name="supported-storage-accounts"></a>Ondersteunde opslagaccounts

Hier volgt een lijst met de ondersteunde opslagtypen voor de Data Box Disk.

| **Opslagaccount** | **Opmerkingen** |
| --- | --- |
| Klassiek | Standard |
| Algemeen gebruik  |Standard; zowel V1 als V2 wordt ondersteund. Zowel dynamische als statische servicelagen worden ondersteund. |
| Blob-opslagaccount | |

> [!IMPORTANT]
> Ondersteuning voor het protocol Network File System (NFS) 3,0 in Azure Blob-opslag wordt niet ondersteund met Data Box Disk.

## <a name="supported-storage-types-for-upload"></a>Ondersteunde opslagtypen voor uploaden

Hier volgt een lijst met de opslagtypen die worden ondersteund voor uploaden naar Azure met behulp van Data Box Disk.

| **Bestandsindeling** | **Opmerkingen** |
| --- | --- |
| Azure-blok-blob | |
| Azure-pagina-blob  | |
| Azure Files  | |
| Beheerde schijven | |

::: zone target="docs"

## <a name="next-step"></a>Volgende stap

* [Azure Data Box Disk implementeren](data-box-disk-deploy-ordered.md)

::: zone-end

