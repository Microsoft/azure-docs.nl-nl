---
author: msftradford
ms.author: parkerra
ms.date: 01/28/2021
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 386c2f8a7cc32cf65381d9d62e2e6940754e3606
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214458"
---
## <a name="configure-the-sensor-fingerprint-provider"></a>De provider voor vingerafdrukken van de sensor configureren

We beginnen met het maken en configureren van een provider voor vingerafdrukken van de sensor. De provider voor vingerafdrukken van de sensor zorgt ervoor dat de platformspecifieke sensoren op het apparaat worden afgelezen en dat de afleeswaarden worden geconverteerd naar een gebruikelijke weergave die door de sessie voor het ruimtelijk anker in de cloud wordt gebruikt.

> [!IMPORTANT]
> Controleer [hier](../articles/spatial-anchors/concepts/coarse-reloc.md#platform-availability) of de Sens oren die u inschakelt, beschikbaar zijn op uw platform.