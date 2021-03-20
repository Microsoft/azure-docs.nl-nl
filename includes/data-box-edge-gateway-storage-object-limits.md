---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 563849d3875ed0156d81770f58340633d90d515b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67176488"
---
Dit zijn de groottes van de Azure-objecten die kunnen worden geschreven. Zorg ervoor dat alle bestanden die worden geüpload voldoen aan deze limieten.

| Azure-object type | Upload limiet                                             |
|-------------------|-----------------------------------------------------------|
| Blok-BLOB        | ~ 4,75 TB                                                 |
| Pagina-BLOB         | 1 TB <br> Elk bestand dat is geüpload in de pagina-BLOB-indeling moet 512 bytes uitgelijnd (een integraal veelvoud), anders mislukt het uploaden. <br> De VHD en VHDX zijn 512 bytes uitgelijnd. |
| Azure Files         | 1 TB <br> Elk bestand dat is geüpload in de pagina-BLOB-indeling moet 512 bytes uitgelijnd (een integraal veelvoud), anders mislukt het uploaden. <br> De VHD en VHDX zijn 512 bytes uitgelijnd. |

> [!IMPORTANT]
> Het maken van bestanden (ongeacht het opslag type) is Maxi maal 5 TB toegestaan. Als u echter een bestand maakt waarvan de grootte groter is dan de upload limiet die in de voor gaande tabel is gedefinieerd, wordt het bestand niet geüpload. U moet het bestand hand matig verwijderen om de ruimte vrij te maken.