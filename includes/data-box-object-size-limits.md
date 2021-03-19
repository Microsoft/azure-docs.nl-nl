---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 9805f53d5901226fc9e32b24a323256cd1da6844
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88655303"
---
Dit zijn de groottes van de Azure-objecten die kunnen worden geschreven. Zorg ervoor dat alle bestanden die worden geüpload voldoen aan deze limieten.

| Azure-object type | Standaardlimiet                                             |
|-------------------|-----------------------------------------------------------|
| Blok-blob        | ~ 4,75 TiB                                                 |
| Pagina-blob         | 8 TiB <br> Elk bestand dat is geüpload in de pagina-BLOB-indeling moet 512 bytes uitgelijnd (een integraal veelvoud), anders mislukt het uploaden. <br> VHD en VHDX zijn 512 bytes uitgelijnd. |
| Azure Files        | 1 TiB                                                      |
| Managed Disks     | 4 TiB <br> Zie voor meer informatie over grootte en limieten: <li>[Schaalbaarheids doelen van de Standard-Ssd's](../articles/virtual-machines/disks-types.md#standard-ssd)</li><li>[Schaalbaarheids doelen van Premium-Ssd's](../articles/virtual-machines/disks-types.md#standard-hdd)</li><li>[Schaalbaarheids doelen van de Standard-Hdd's](../articles/virtual-machines/disks-types.md#premium-ssd)</li><li>[Prijzen en facturering van beheerde schijven](../articles/virtual-machines/disks-types.md#billing)</li>  
