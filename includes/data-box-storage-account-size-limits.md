---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 01/12/2021
ms.author: alkohli
ms.openlocfilehash: 6cabac4c59b09d146c1e42762402043622edb60e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98225184"
---
Dit zijn de limieten voor de grootte van de gegevens die worden gekopieerd naar een opslag account. Zorg ervoor dat de gegevens die u uploadt, voldoen aan deze limieten. Zie [schaalbaarheids-en prestatie doelen voor Blob Storage](../articles/storage/blobs/scalability-targets.md) en [Azure files schaalbaarheids-en prestatie doelen](../articles/storage/files/storage-files-scale-targets.md)voor de meest actuele informatie over deze limieten.

| Grootte van gegevens die zijn gekopieerd naar een Azure-opslag account                      | Standaardlimiet          |
|---------------------------------------------------------------------|------------------------|
| BLOB en pagina-BLOB blok keren                                            | De maximum limiet is hetzelfde als de [opslag limiet die is gedefinieerd voor het Azure-abonnement](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) en bevat gegevens uit alle bronnen, inclusief data box.   |
| Azure Files                                                          | Data Box ondersteunt grote bestands shares (100 TiB) als deze functie is ingeschakeld voordat de Data Box order wordt gemaakt. <br> Als deze niet is ingeschakeld voor het maken van de order, wordt de maximale bestands share grootte die wordt ondersteund, 5 TiB. <br> Data Box biedt ondersteuning voor Azure Premium-bestands shares voor een totaal van 100 TiB voor alle shares in het opslag account. <br> De maximale bruikbare capaciteit is iets minder vanwege de ruimte die logboeken en controle Logboeken kopiëren gebruikt. Voor het kopie logboek en controle logboek is mini maal 100 GiB elk gereserveerd. Zie [audit logboeken voor Azure Data Box Azure data Box Heavy](../articles/databox/data-box-audit-logs.md)voor meer informatie. <br> Alle mappen onder *StorageAccount_AzureFiles* moeten deze limiet volgen. <br> Zie voor meer informatie [grote bestands shares inschakelen en maken](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |
