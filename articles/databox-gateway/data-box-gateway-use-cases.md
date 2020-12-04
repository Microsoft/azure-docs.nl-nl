---
title: Microsoft Azure Data Box Gateway use cases | Microsoft Docs
description: Beschrijft de use cases voor Azure Data Box Gateway, een opslag oplossing voor virtuele apparaten waarmee u gegevens kunt overdragen naar Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: f6daee6d4cfc3c074e004fb3835f62218e48d9ff
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582383"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Gebruiks voorbeelden voor Azure Data Box Gateway

Azure Data Box Gateway is een gateway apparaat in de cloud dat zich op uw locatie bevindt en uw installatie kopie, media en andere gegevens naar Azure verzendt. Deze Cloud Storage Gateway is een virtuele machine die is ingericht in uw Hyper Visor. U schrijft gegevens naar dit virtuele apparaat met behulp van de NFS-en SMB-protocollen, die vervolgens naar Azure worden verzonden. In dit artikel vindt u een gedetailleerde beschrijving van de scenario's waarin u dit apparaat kunt implementeren.

Gebruik Data Box Gateway voor de volgende scenario's:

- Om voortdurend enorme hoeveel heden gegevens op te nemen.
- Voor de Cloud archivering van gegevens op een veilige en efficiënte manier.
- Voor incrementele gegevens overdracht via het netwerk nadat de eerste bulk overdracht is uitgevoerd met behulp van Data Box.

Elk van deze scenario's wordt gedetailleerd beschreven in de volgende secties.


## <a name="continuous-data-ingestion"></a>Doorlopende gegevens opname

Een van de belangrijkste voor delen van Data Box Gateway is de mogelijkheid om voortdurend gegevens op te nemen in het apparaat om te kopiëren naar de Cloud, ongeacht de grootte van de gegevens.

Wanneer de gegevens naar het gatewayapparaat worden geschreven, uploadt het apparaat de gegevens naar Azure Storage. Het apparaat beheert opslag automatisch door de bestanden lokaal te verwijderen en de meta gegevens te behouden wanneer er een bepaalde drempel waarde wordt bereikt. Door een lokale kopie van de meta gegevens te bewaren, kan het gateway apparaat alleen de wijzigingen uploaden wanneer het bestand wordt bijgewerkt. De gegevens die naar uw gateway apparaat worden geüpload, moeten volgens de richt lijnen in het voor [behoud van gegevens uploads](data-box-gateway-limits.md#data-upload-caveats)zijn.

Wanneer het apparaat wordt gevuld met gegevens, begint het met het beperken van de ingangs snelheid (indien nodig) om overeen te komen met de snelheid waarmee gegevens naar de cloud worden geüpload. U kunt waarschuwingen gebruiken om de doorlopende opname op het apparaat te controleren. Deze waarschuwingen worden gegenereerd zodra de beperking is gestart en worden gewist zodra de beperking is gestopt.

## <a name="cloud-archival-of-data"></a>Cloud archivering van gegevens

Gebruik Data Box Gateway wanneer u uw gegevens lange termijn in de Cloud wilt bewaren. U kunt de archief laag van de opslag gebruiken voor lange termijn retentie.

De archief laag is geoptimaliseerd voor het opslaan van gegevens die zelden worden gebruikt gedurende ten minste 180 dagen. De archief laag biedt de laagste opslag kosten, maar heeft de hoogste toegangs kosten. Ga naar de [Access-laag](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier)van het Archief voor meer informatie.

### <a name="move-data-to-the-archive-tier"></a>Gegevens verplaatsen naar de laag archief

Voordat u begint, moet u ervoor zorgen dat u een actief Data Box Gateway apparaat hebt. Volg de stappen die worden beschreven in de [zelf studie: bereid u voor op de implementatie van Azure data Box gateway](data-box-gateway-deploy-prep.md) en blijf door gaan met de volgende zelf studie totdat u een operationeel apparaat hebt.

- Gebruik het Data Box Gateway apparaat om gegevens te uploaden naar Azure via de gebruikelijke procedure voor het overdragen van [gegevens via data Box gateway](data-box-gateway-deploy-add-shares.md).
- Nadat de gegevens zijn geüpload, moet u deze naar de laag archief verplaatsen. U kunt de BLOB-laag op twee manieren instellen: door een Azure PowerShell script of Azure Storage levenscyclus beheer beleid te gebruiken.  
    - Als u Azure PowerShell gebruikt, volgt u deze [stappen](/azure/databox/data-box-how-to-set-data-tier#use-azure-powershell-to-set-the-blob-tier) om de gegevens naar de laag archief te verplaatsen.
    - Als u Azure levenscyclus beheer gebruikt, voert u deze stappen uit om de gegevens naar de laag archief te verplaatsen.
        - [Meld](/azure/storage/common/storage-lifecycle-management-concepts) u aan voor de preview-versie van de BLOB-levenscyclus beheer service om de laag Archive te gebruiken.
        - Gebruik het volgende beleid om [gegevens op opname te archiveren](/azure/storage/blobs/storage-lifecycle-management-concepts#archive-data-after-ingest).
- Zodra de blobs zijn gemarkeerd als Archive, kunnen ze niet meer worden gewijzigd door de gateway, tenzij ze worden verplaatst naar de laag warm of koude. Als het bestand zich in de lokale opslag bevindt, worden de wijzigingen die zijn aangebracht in de lokale kopie (inclusief verwijderingen) niet geüpload naar de archief laag.
- Als u gegevens in archief opslag wilt lezen, moet u de gegevens opnieuw laten worden gehydrateerd door de BLOB-laag te wijzigen in Hot of cool. Als [de share](data-box-gateway-manage-shares.md#refresh-shares) op de gateway wordt vernieuwd, wordt de BLOB niet opnieuw gehydrateerd.

Meer informatie over het [beheren van Azure Blob Storage Lifecycle](/azure/storage/common/storage-lifecycle-management-concepts).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Eerste bulk overdracht, gevolgd door incrementele overdracht

Gebruik Data Box en Data Box Gateway samen wanneer u een grote hoeveelheid gegevens wilt uploaden, gevolgd door incrementele overdrachten. Gebruik Data Box voor de bulk overdracht in een offline modus (eerste seed) en Data Box Gateway voor incrementele overdrachten (doorlopende invoer) via het netwerk.

### <a name="seed-the-data-with-data-box"></a>De gegevens met Data Box seeden

Volg deze stappen om de gegevens te kopiëren naar Data Box en te uploaden naar Azure Storage.

1. [Bestel uw data Box](/azure/databox/data-box-deploy-ordered).
2. [Stel uw data Box](/azure/databox/data-box-deploy-set-up)in.
3. [Gegevens kopiëren naar Data box via SMB](/azure/databox/data-box-deploy-copy-data).
4. [Ga terug naar de data box, Controleer of de gegevens uploadt naar Azure](/azure/databox/data-box-deploy-picked-up).
5. Zodra het uploaden van gegevens naar Azure is voltooid, moeten alle gegevens zich in azure Storage-containers bevindt. Ga in het opslag account voor Data Box naar de container voor de BLOB (en het bestand) om ervoor te zorgen dat alle gegevens worden gekopieerd. Noteer de naam van de container, zoals u deze naam later gebruikt. In de volgende scherm afbeelding wordt bijvoorbeeld de `databox` container voor de incrementele overdracht gebruikt.

    ![Container met gegevens op Data Box](media/data-box-gateway-use-cases/data-container.png)

Deze bulk overdracht voltooit de initiële seeding-fase.

### <a name="ongoing-feed-with-data-box-gateway"></a>Voortdurende feed met Data Box Gateway

Volg deze stappen voor een doorlopende opname door Data Box Gateway. 

1. Maak een Cloud share op Data Box Gateway. Deze share uploadt automatisch alle gegevens naar het Azure Storage-account. Ga naar **shares** in uw data Box gateway-resource en klik op **+ share toevoegen**.

    ![Klik op + share toevoegen](media/data-box-gateway-use-cases/add-share.png)

2. Zorg ervoor dat deze share is gekoppeld aan de container die de seeded-gegevens bevat. Voor **Selecteer BLOB-container** kiest **u bestaande gebruiken** en bladert u naar de container waar de gegevens van data box zijn overgedragen.

    ![Share-instellingen](media/data-box-gateway-use-cases/share-settings-select-existing-container.png)

3. Nadat de share is gemaakt, vernieuwt u de share. Met deze bewerking wordt de on-premises share vernieuwd met de inhoud van Azure.

    ![Share vernieuwen](media/data-box-gateway-use-cases/refresh-share.png)

    Wanneer de share is gesynchroniseerd, worden de incrementele wijzigingen door de Data Box Gateway geüpload als de bestanden op de client zijn gewijzigd.

## <a name="next-steps"></a>Volgende stappen

- Lees de [Systeemvereisten voor Data Box Gateway](data-box-gateway-system-requirements.md).
- Begrijp de [limieten voor Data Box Gateway](data-box-gateway-limits.md).
- Implementeer [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) in de Azure Portal.