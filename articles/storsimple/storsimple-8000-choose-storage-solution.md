---
title: Opties voor gegevensoverdracht naar Azure met behulp van een | Microsoft Docs
description: Meer informatie over het kiezen van het juiste apparaat voor on-premises gegevensoverdracht naar Azure tussen Data Box Edge, Azure File Sync en de StorSimple 8000-serie.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 1345486e6bda7501a862612652b722b0075e190f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791158"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>StorSimple vergelijken met overdrachtsopties van Azure File Sync en Data Box Edge 

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]
 
In dit document vindt u een overzicht van de opties voor on-premises gegevensoverdracht naar Azure, met een vergelijking van: Data Box Edge versus Azure File Sync versus de StorSimple 8000-serie.

- **[Data Box Edge:](../databox-online/azure-stack-edge-overview.md)** Data Box Edge is een on-premises netwerkapparaat dat gegevens naar en uit Azure verplaatst en edge-rekenkracht met AI heeft om gegevens vooraf te verwerken tijdens het uploaden. Data Box Gateway is een virtuele versie van het apparaat met dezelfde mogelijkheden voor gegevensoverdracht.
- **[Azure File Sync:](../storage/file-sync/file-sync-deployment-guide.md)** Azure File Sync kunnen worden gebruikt om de bestands shares van uw organisatie te centraliseren in Azure Files, terwijl de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver behouden blijven. Door Azure File Sync wordt Windows Server getransformeerd in een snelle cache van uw Azure-bestandsshare. Algemene beschikbaarheid van Azure File Sync is eerder in 2018 aangekondigd.
- **[StorSimple:](./storsimple-overview.md)** StorSimple is een hybride apparaat waarmee ondernemingen hun opslaginfrastructuur voor primaire opslag, gegevensbeveiliging, archivering en herstel na noodherstel kunnen consolideren op één oplossing door nauw te integreren met Azure Storage. De levenscyclus van het product voor StorSimple vindt u [hier.](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)

## <a name="comparison-summary"></a>Vergelijkingsoverzicht

|                           |StorSimple 8000   |Azure File Sync   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|**Overzicht**     |Gelaagde hybride opslag en archivering|Algemene bestandsserveropslag met opslag in cloudlagen en synchronisatie van meerdere servers.  |Opslagoplossing voor het vooraf verwerken van gegevens en het verzenden ervan via het netwerk naar Azure.        |
|**Scenario's**    |Bestandsserver, archivering, back-updoel |Bestandsserver, archivering (multi-site)   |Gegevensoverdracht, voorverwerking van gegevens, inclusief ML-deferencing, IoT, archivering    |
|**Edge-rekenkracht** |Niet beschikbaar |Niet beschikbaar |Ondersteunt het uitvoeren van containers met Azure IoT Edge    |
|**Formulierfactor**  |Fysiek apparaat   |Agent geïnstalleerd op Windows Server |Fysiek apparaat   |
|**Hardware**     |Fysiek apparaat van Microsoft geleverd als onderdeel van de service | Door de klant opgegeven |Fysiek apparaat van Microsoft geleverd als onderdeel van de service  |
|**Gegevensindeling**  |Aangepaste indeling   |Bestanden         |Blobs of bestanden    |
|**Protocolondersteuning** |iSCSI          |SMB, NFS    | SMB of NFS      |
|**Prijzen**      |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie [Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md) [en Azure Data Box Gateway](../databox-gateway/data-box-gateway-overview.md)
- Meer informatie [over Azure File Sync](../storage/file-sync/file-sync-deployment-guide.md)