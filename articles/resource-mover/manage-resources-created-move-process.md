---
title: Resources beheren die zijn gemaakt tijdens het verplaatsen van de VM in azure resource-overwerkers
description: Meer informatie over het beheren van resources die zijn gemaakt tijdens het verplaatsen van de VM in azure resource-overwerkers
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 7ad0e73a90e733af0dd752100ebc71908f68181a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388478"
---
# <a name="manage-resources-created-for-the-vm-move"></a>Resources beheren die zijn gemaakt voor het verplaatsen van de VM

In dit artikel wordt beschreven hoe u resources beheert die expliciet door [Azure resource](overview.md) Facility worden gemaakt om het VM-verplaatsings proces te vergemakkelijken. 

Na het verplaatsen van Vm's in verschillende regio's, is er een aantal resources gemaakt door middel van resource-overschakeling die hand matig moet worden opgeruimd.

## <a name="delete-resources-created-for-vm-move"></a>Resources verwijderen die zijn gemaakt voor het verplaatsen van de VM

Verwijder hand matig de verzameling verplaatsen en Site Recovery resources die zijn gemaakt voor het verplaatsen van de virtuele machine.

1. Controleer de resources in de resource groep ```ResourceMoverRG-<sourceregion>-<target-region>``` .
2. Controleer of de virtuele machine en alle andere bron bronnen in de verplaatsings verzameling zijn verplaatst/verwijderd. Zo weet u zeker dat er geen resources in behandeling zijn die hiervan gebruikmaken.
2. Verwijder deze resources.

    - De naam van de verzameling voor verplaatsen is ```movecollection-<sourceregion>-<target-region>-<metadata-region>```.
    - De naam van het cacheopslagaccount is ```resmovecache<guid>```
    - De naam van de kluis is ```ResourceMove-<sourceregion>-<target-region>-GUID```.

## <a name="next-steps"></a>Volgende stappen

Verplaats [een virtuele machine](tutorial-move-region-virtual-machines.md) naar een andere regio met resource-overschakeling.
