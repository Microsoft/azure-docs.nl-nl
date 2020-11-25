---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 2868e5a53686cfa94dc206c1aab65fe866d19b6d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026361"
---
Dit zijn de limieten voor de grootte van de gegevens die worden gekopieerd naar het opslag account. Zorg ervoor dat de gegevens die u uploadt, voldoen aan deze limieten. Zie [schaalbaarheids-en prestatie doelen voor Blob Storage](../articles/storage/blobs/scalability-targets.md) en [Azure files schaalbaarheids-en prestatie doelen](../articles/storage/files/storage-files-scale-targets.md)voor de meest actuele informatie over deze limieten.

| Grootte van gegevens die zijn gekopieerd naar een Azure-opslag account                      | Standaardlimiet          |
|---------------------------------------------------------------------|------------------------|
| BLOB en pagina-BLOB blok keren                                            | De maximum limiet is hetzelfde als de [opslag limiet die is gedefinieerd voor het Azure-abonnement](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) en bevat gegevens uit alle bronnen, inclusief data box. |
| Azure Files                                                          | Data Box ondersteunt grote bestands shares (100TiB) als deze functie is ingeschakeld voordat de Data Box order wordt gemaakt. <br> Als deze niet is ingeschakeld voor het maken van de order, wordt de maximale bestands share grootte die wordt ondersteund, 5 TiB. <br> Premium-bestands shares worden nog niet ondersteund.<br> Alle mappen onder *StorageAccount_AzureFiles* moeten deze limiet volgen. <br> Zie voor meer informatie [grote bestands shares inschakelen en maken](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |