---
title: Ondersteunings matrix voor back-ups van Azure-blobs
description: Biedt een overzicht van de ondersteunings instellingen en-beperkingen bij het maken van back-ups van Azure-blobs (in preview-versie)
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: 12d289fdc3f84e7cbb3489a3ece283179e51772c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561896"
---
# <a name="support-matrix-for-azure-blobs-backup-in-preview"></a>Ondersteunings matrix voor back-ups van Azure-blobs (in preview-versie)

Dit artikel bevat een overzicht van de regionale Beschik baarheid, ondersteunde scenario's en beperkingen van operationele back-ups van blobs.

## <a name="supported-regions"></a>Ondersteunde regio’s

Operationele back-ups voor blobs is momenteel beschikbaar in de volgende regio's: Australië-centraal, Australië-oost, Brazilië-zuid, Canada-centraal, Centraal-India, VS, Azië-oost, VS-Oost, VS-Oost 2, Duitsland-west-centraal, Japan-Oost, Japan-West, Korea-centraal, Korea-zuid, Europa-noord, Zuid-Centraal VS, Zuid-Azië-oost, Zwitserland-noord, UAE-noord, UK-zuid, UK-west, West-Centraal VS, Europa-west , VS-West, VS-West 2

## <a name="limitations"></a>Beperkingen

Operationele back-up van blobs maakt gebruik van BLOB Point-in-time Restore, Blob-versie beheer, zacht verwijderen voor blobs, feed wijzigen voor blobs en verwijder Lock om een lokale back-upoplossing te bieden. Beperkingen die van toepassing zijn op deze mogelijkheden, gelden ook voor operationele back-ups.

**Ondersteunde scenario's:** Operational Backup ondersteunt alleen blok-blobs in standaard v2-opslag accounts voor algemeen gebruik. Dit betekent dat ADLS Gen2 accounts niet worden ondersteund. Ook worden pagina-blobs, toevoeg-blobs en Premium-blobs in uw opslag account niet hersteld en alleen blok-blobs worden hersteld.

**Andere beperkingen:**

- Als u een container tijdens de Bewaar periode hebt verwijderd, wordt die container niet hersteld met de herstel bewerking naar een bepaald tijdstip. Als u probeert een aantal blobs te herstellen dat blobs bevat in een verwijderde container, mislukt de herstel bewerking naar een bepaald tijdstip. Zie [voorlopig verwijderen voor containers (preview)](../storage/blobs/soft-delete-container-overview.md)voor meer informatie over het beveiligen van containers tegen verwijderen.
- Als een BLOB tussen de warme en koele lagen is verplaatst in de periode tussen het huidige moment en het herstel punt, wordt de BLOB teruggezet naar de vorige laag. Het herstellen van blok-blobs in de opslaglaag wordt niet ondersteund. Als een BLOB in de warme laag bijvoorbeeld twee dagen geleden is verplaatst naar de archief laag en een herstel bewerking herstelt naar een bepaald punt drie dagen geleden, wordt de BLOB niet teruggezet naar de warme laag. Als u een gearchiveerde BLOB wilt herstellen, moet u deze eerst uit de laag archief verplaatsen. Zie BLOB-gegevens opnieuw [inbreken vanuit de laag archief](../storage/blobs/storage-blob-rehydration.md)voor meer informatie.
- Een blok dat is geüpload via de [put-blok kering](/rest/api/storageservices/put-block) of het [put-blok van de URL](/rest/api/storageservices/put-block-from-url), maar niet is doorgevoerd via een [put-lijst](/rest/api/storageservices/put-block-list), maakt geen deel uit van een BLOB en wordt dus niet hersteld als onderdeel van een herstel bewerking.
- Een blob met een actieve lease kan niet worden hersteld. Als een blob met een actieve lease is opgenomen in het bereik van blobs dat moet worden hersteld, mislukt de herstel bewerking automatisch. Verbreekt actieve leases voordat u de herstel bewerking start.
- Moment opnamen worden niet gemaakt of verwijderd als onderdeel van een herstel bewerking. Alleen de basis-BLOB wordt teruggezet naar de vorige status.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van operationele back-ups voor Azure-blobs (in preview-versie)](blob-backup-overview.md)