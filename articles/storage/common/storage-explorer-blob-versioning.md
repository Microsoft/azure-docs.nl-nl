---
title: Versie beheer van Azure Storage Explorer BLOB | Microsoft Docs
description: Richt lijnen voor het versie beheer van blobs voor Azure Storage Explorer
services: storage
author: chuye
ms.service: storage
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: chuye
ms.openlocfilehash: e20733cb4b93fcfac7606895746645727f12d6c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92783824"
---
# <a name="azure-storage-explorer-blob-versioning-guide"></a>Hand leiding voor versie beheer van Azure Storage Explorer

Microsoft Azure Storage Explorer biedt eenvoudige toegang tot en beheer van BLOB-versies. Deze hand leiding helpt u te begrijpen hoe BLOB-versie beheer werkt in Storage Explorer. Voordat u doorgaat, wordt u aangeraden meer te lezen over [BLOB-versie beheer](../blobs/versioning-overview.md).

## <a name="terminology"></a>Terminologie

Deze sectie bevat enkele definities waarmee u het gebruik in dit artikel kunt begrijpen.

- Zacht verwijderen: een alternatieve functie voor het automatisch beveiligen van gegevens. Meer informatie over voorlopig verwijderen vindt u [hier](../blobs/soft-delete-blob-overview.md).
- Actieve blob: er is een BLOB-of BLOB-versie gemaakt in de actieve status. U kunt alleen op blobs of BLOB-versies in de actieve status worden uitgevoerd.
- Zacht verwijderde blob: een BLOB-of BLOB-versie die is gemarkeerd als zacht-verwijderd. Voorlopig verwijderde blobs worden alleen bewaard voor de Bewaar periode.
- BLOB-versie: een blob die is gemaakt met Blob-versie beheer is ingeschakeld. Elke BLOB-versie is gekoppeld aan een versie-ID.
- Huidige versie: een BLOB-versie die is gemarkeerd als de huidige versie.
- Vorige versie: een BLOB-versie die niet de huidige versie is.
- Niet-versie-blob: een blob die is gemaakt met Blob-versie beheer is uitgeschakeld. Een niet-versie-BLOB heeft geen versie-ID.

## <a name="view-blob-versions"></a>BLOB-versies weer geven

Storage Explorer ondersteunt vier verschillende weer gaven voor het weer geven van blobs.

| Weergave | Actieve niet-versie-blobs | Voorlopig verwijderde niet-versie-blobs | BLOB-versies |
| ---- | :----------: | :-----------: | :------------------: |
| Actieve blobs | Ja | Nee | Alleen huidige versie |
| Actieve blobs en zachte verwijderde blobs | Ja | Ja | Alleen huidige versie |
| Actieve blobs en blobs zonder huidige versie | Ja | Nee | Huidige versie of nieuwste actieve versie |
| Alle blobs en blobs zonder huidige versie | Ja | Ja | Huidige versie of nieuwste versie |

### <a name="active-blobs"></a>Actieve blobs

In deze weer gave worden Storage Explorer weer gegeven:

- Actieve niet-versie-blobs
- Huidige versies

### <a name="active-blobs-and-soft-deleted-blobs"></a>Actieve blobs en zachte verwijderde blobs

In deze weer gave worden Storage Explorer weer gegeven:

- Actieve niet-versie-blobs
- Voorlopig verwijderde niet-versie-blobs
- Huidige versies.

### <a name="active-blobs-and-blobs-without-current-version"></a>Actieve blobs en blobs zonder huidige versie

In deze weer gave worden Storage Explorer weer gegeven:

- Actieve niet-versie-blobs
- Huidige versies
- Nieuwste actieve vorige versies. 

Voor blobs die geen huidige versie hebben, maar wel een actieve vorige versie hebben, Storage Explorer de meest recente versie van Active Previous weer geven als een representatie van die blob.

### <a name="all-blobs-and-blobs-without-current-version"></a>Alle blobs en blobs zonder huidige versie

In deze weer gave worden Storage Explorer weer gegeven:

- Actieve niet-versie-blobs
- Voorlopig verwijderde niet-versie-blobs
- Huidige versies
- Meest recente vorige versies. 

Voor blobs die geen huidige versie hebben, Storage Explorer de meest recente vorige versie weer geven als een representatie van die blob.

> [!Note]
> Vanwege de service beperking heeft Storage Explorer een extra verwerking nodig om een hiërarchische weer gave van uw virtuele mappen te krijgen bij het weer geven van BLOB-versies. Het duurt langer om blobs in de volgende weer gaven te vermelden:
> 
> - Actieve blobs en blobs zonder huidige versie
> - Alle blobs en blobs zonder huidige versie

## <a name="manage-blob-versions"></a>BLOB-versies beheren

### <a name="view-versions-of-a-blob"></a>Versies van een BLOB weer geven

Storage Explorer biedt een opdracht voor het **beheren van versies** voor het weer geven van alle versies van een blob. Als u de versies van een BLOB wilt weer geven, selecteert u de BLOB waarvoor u de versies wilt weer geven en selecteert u **beheren historie &rarr; versies beheren** in de werk balk of in het context menu.

### <a name="download-blob-versions"></a>BLOB-versies downloaden

Als u een of meer BLOB-versies wilt downloaden, selecteert u de BLOB-versies die u wilt downloaden en selecteert u **downloaden** in de werk balk of in het context menu.

Als u meerdere versies van een BLOB downloadt, krijgen de gedownloade bestanden hun versie-Id's aan het begin van hun bestands namen.

### <a name="delete-blob-versions"></a>BLOB-versies verwijderen

Als u een of meer BLOB-versies wilt verwijderen, selecteert u de BLOB-versies die u wilt verwijderen en selecteert u **verwijderen** in de werk balk of in het context menu.

BLOB-versies zijn onderhevig aan uw voorlopig beleid voor verwijderen. Als voorlopig verwijderen is ingeschakeld, worden de BLOB-versies zacht verwijderd. Een speciaal geval is het verwijderen van een huidige versie. Als u een huidige versie verwijdert, wordt deze in plaats daarvan automatisch een actieve vorige versie geworden.

### <a name="promote-blob-version"></a>BLOB-versie verhogen

U kunt de inhoud van een BLOB herstellen door een eerdere versie te promo veren tot de huidige versie. Selecteer de BLOB-versie die u wilt promoten en selecteer **versie verhogen** in de werk balk of in het context menu.

Niet-versie-blobs worden overschreven door de gepromoveerde BLOB-versie. Zorg ervoor dat u deze gegevens niet meer nodig hebt of dat u geen back-up van de gegevens hoeft te maken voordat u de bewerking bevestigt. Huidige versies worden automatisch vorige versies, dus Storage Explorer wordt niet gevraagd om bevestiging.

### <a name="undelete-blob-version"></a>Verwijdering van BLOB-versie ongedaan maken

De verwijdering van BLOB-versies kan niet afzonderlijk worden verwijderd. Ze moeten allemaal tegelijk worden verwijderd. Als u het verwijderen van alle BLOB-versies van een BLOB ongedaan wilt maken, selecteert u een van de versies van de BLOB en selecteert u **verwijderen ongedaan** maken in de werk balk of in het context menu.

### <a name="change-access-tier-of-blob-versions"></a>De toegangs laag van BLOB-versies wijzigen

Elke BLOB-versie heeft een eigen toegangs laag. Als u de toegangs laag van BLOB-versies wilt wijzigen, selecteert u de BLOB-versies waarvan u de toegangs laag wilt wijzigen en selecteert u **toegangs laag wijzigen...** in het context menu.

## <a name="see-also"></a>Zie ook

* [BLOB-versie beheer](../blobs/versioning-overview.md)
* [Blobs voorlopig verwijderen](../blobs/soft-delete-blob-overview.md)