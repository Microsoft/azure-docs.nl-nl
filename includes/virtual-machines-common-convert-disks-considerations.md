---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 72050ff4887642ba16d52948c1c1253ca01443c0
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473735"
---
* De virtuele machine wordt opnieuw gestart door de conversie, zodat de migratie van uw Vm's wordt gepland tijdens een reeds bestaand onderhouds venster. 

* De conversie kan niet ongedaan worden gemaakt. 

* Houd er rekening mee dat gebruikers met de rol [Inzender voor virtuele machines](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) de VM-grootte niet kunnen wijzigen (zoals vóór de conversie). De reden hiervoor is dat de gebruiker van de virtuele machines van het besturings systeem de machtiging micro soft. Compute/disks/write moet hebben voor Vm's met Managed disks.

* Test de conversie. Migreer de test-VM voordat u de migratie in de productieomgeving uitvoert.

* Tijdens de conversie moet u de toewijzing van de VM ongedaan maken. De VM ontvangt een nieuw IP-adres wanneer deze na de conversie wordt opgestart. Indien vereist kunt u [een statisch IP-adres toewijzen](../articles/virtual-network/public-ip-addresses.md) aan de VM.

* Controleer de minimale versie van de Azure VM-agent die is vereist voor de ondersteuning van het conversie proces. Zie [minimale versie ondersteuning voor VM-agents in azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) voor informatie over het controleren en bijwerken van de versie van de agent.
