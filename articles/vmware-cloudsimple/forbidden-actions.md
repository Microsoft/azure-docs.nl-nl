---
title: Verboden acties tijdens verhoogde toegang
description: VMware-engine herstelt de wijzigingen om ervoor te zorgen dat de service niet wordt onderbroken wanneer VMware engine een van de volgende verboden acties detecteert.
titleSuffix: Azure VMware Solution by CloudSimple
ms.date: 10/28/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
author: divka78
ms.author: dikamath
ms.openlocfilehash: 81a09a9cfe749de56f7306a8d5e1ae5db01295fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92915407"
---
# <a name="forbidden-actions-during-elevated-access"></a>Verboden acties tijdens verhoogde toegang

Tijdens het tijds interval voor de verhoging zijn bepaalde acties niet toegestaan. Wanneer VMware engine een van de volgende verboden acties detecteert, worden de wijzigingen door VMware engine teruggezet om ervoor te zorgen dat de service niet wordt onderbroken.

## <a name="cluster-actions"></a>Cluster acties

- Een cluster verwijderen uit vCenter.
- VSphere hoge Beschik baarheid (HA) in een cluster wijzigen.
- Er wordt een host van vCenter aan het cluster toegevoegd.
- Een host uit het cluster verwijderen uit vCenter.

## <a name="host-actions"></a>Acties hosten

- De gegevens opslag op een ESXi-host wordt verwijderd.
- De vCenter-agent wordt verwijderd van de host.
- De configuratie van de host wijzigen.
- Wijzigingen aanbrengen in de hostgroepen.
- Een host in de onderhouds modus plaatsen.

## <a name="network-actions"></a>Netwerk acties

- De standaard gedistribueerde virtuele switch (DVS) verwijderen in een privécloud.
- Het verwijderen van een host uit de standaard DVS.
- Een DVS-instelling importeren.
- Een DVS-instelling opnieuw te configureren.
- Een wille keurige DVS bijwerken.
- De beheer poort groep verwijderen.
- De beheer poort groep bewerken.

## <a name="roles-and-permissions-actions"></a>Acties voor rollen en machtigingen

- Een globale rol maken.
- De machtiging voor beheer objecten wijzigen of verwijderen.
- Het wijzigen of verwijderen van standaard rollen.
- Verhoog de bevoegdheden van een rol op hoger dan de rol van Cloud-eigenaar.

## <a name="other-actions"></a>Overige acties

- De standaard licenties worden verwijderd:
  - vCenter Server
  - ESXi-knoop punten
  - NSX-T
  - HCX
- De beheer resource groep wijzigen of verwijderen.
- Beheer-Vm's klonen.


## <a name="next-steps"></a>Volgende stappen
[Onderhoud en updates voor CloudSimple](cloudsimple-maintenance-updates.md) 
