---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 112c30fdd242c20f11c43f42ba54e3717e074bbb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706029"
---
Hier volgt een lijst met de ondersteunde opslag accounts en opslag typen voor een Data Box apparaat. Zie [typen opslag accounts](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts)voor een volledige lijst met alle mogelijkheden voor alle typen opslag accounts.

De volgende tabel bevat de ondersteunde opslag accounts voor import orders.

| **Opslag account/ondersteunde opslag typen** | **Blok-blob** |**Pagina-BLOB** _ |_ *Azure files** |**Opmerkingen**|
| --- | --- | -- | -- | -- |
| Klassieke standaard | J | J | J |
| Algemene v1-standaard  | J | J | J | Zowel warm als koud worden ondersteund.|
| V1 Premium voor algemeen gebruik  |  | J| | |
| V2-standaard voor algemeen gebruik  | J | J | J | Zowel warm als koud worden ondersteund.|
| V2 Premium voor algemeen gebruik  |  |J | | |
| Azure Premium FileStorage |  |  | J |  |  
| Blob Storage-standaard |J | | |Zowel warm als koud worden ondersteund. |

\**-Gegevens die zijn geüpload naar pagina-blobs, moeten 512 bytes, zoals vhd's, zijn uitgelijnd.*

De volgende tabel bevat de ondersteunde opslag accounts voor export orders.

| **Opslag account/ondersteunde opslag typen** | **Blok-blob** |**Pagina-BLOB** _ |_ *Azure files** |**Ondersteunde toegangslagen**|
| --- | --- | -- | -- | -- |
| Klassieke standaard | J | J | J | |
| Algemene v1-standaard  | J | J | J | Warm, cool|
| V1 Premium voor algemeen gebruik  |  | J| | |
| V2-standaard voor algemeen gebruik  | J | J | J | Warm, cool|
| V2 Premium voor algemeen gebruik  |  |J | | |
| Azure Premium FileStorage |  |  | J |  |
| Blob Storage-standaard |J | | |Warm, cool |
| De Premium-opslag voor BLOB blok keren |J | | |Warm, cool |
| Pagina Blob Storage Premium | |J | | |

> [!IMPORTANT]
> - Voor accounts voor algemeen gebruik biedt Data Box geen ondersteuning voor wachtrij-, tabel-en schijf opslag typen voor import orders. Data Box biedt voor export orders geen ondersteuning voor wachtrij-, tabel-, schijf-en Azure Data Lake gen 2-opslag typen voor accounts voor algemeen gebruik.
> - Data Box biedt geen ondersteuning voor toevoeg-blobs voor Blob Storage en Blob Storage-accounts blok keren.
> - Ondersteuning voor het protocol Network File System (NFS) 3,0 in Azure Blob-opslag wordt niet ondersteund met Data Box.
> - Gegevens die zijn geüpload naar pagina-blobs, moeten 512 bytes, zoals Vhd's, zijn uitgelijnd.
> - Maxi maal 80 TB kan worden geëxporteerd.
> - Bestands geschiedenis en BLOB-moment opnamen worden niet geëxporteerd.