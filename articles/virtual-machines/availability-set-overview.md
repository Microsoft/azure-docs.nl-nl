---
title: Overzicht van beschikbaarheids sets
description: Meer informatie over de beschikbaarheids sets in azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: c0bbc218723ea75064d9eb6f9c5f8f8c0d18a10b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102510488"
---
# <a name="availability-sets-overview"></a>Overzicht van beschikbaarheids sets

In dit artikel vindt u een overzicht van de beschik baarheid van Azure virtual machines (Vm's).

## <a name="what-is-an-availability-set"></a>Wat is een beschikbaarheidsset? 

Een beschikbaarheidsset is een logische groepering van virtuele machines waaruit Azure kan begrijpen hoe uw toepassing is ontworpen, om zo redundantie en beschikbaarheid te kunnen bieden. We raden u aan twee of meer virtuele machines te maken binnen een beschikbaarheidsset om te voorzien in een Maxi maal beschik bare toepassing en om te voldoen aan de [99,95% Azure Sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Er zijn geen kosten voor de Beschikbaarheidsset zelf, u betaalt alleen voor elk VM-exemplaar dat u maakt.

## <a name="how-do-availability-sets-work"></a>Hoe werken beschikbaarheids sets?
Elke virtuele machine in uw beschikbaarheidsset krijgt een **updatedomein** en een **foutdomein** toegewezen door het onderliggende Azure-platform. Voor iedere beschikbaarheidsset worden standaard vijf updatedomeinen toegewezen die niet door gebruiker te bewerken zijn (voor Resource Manager-implementaties kan dit aantal worden opgehoogd tot 20 updatedomeinen), om groepen virtuele machines en onderliggende fysieke hardware aan te duiden die op hetzelfde moment opnieuw kunnen worden opgestart. Wanneer in één beschikbaarheidsset meer dan vijf virtuele machines worden geconfigureerd, wordt de zesde virtuele machine in hetzelfde updatedomein geplaatst als de eerste virtuele machine, de zevende in hetzelfde updatedomein als de tweede virtuele machine, enzovoort. De volgorde waarin updatedomeinen opnieuw worden opgestart, verloopt tijdens gepland onderhoud niet altijd sequentieel, maar er wordt slechts één updatedomein tegelijk opnieuw opgestart. Een updatedomein dat opnieuw is opgestart, heeft 30 minuten om te herstellen voordat onderhoud wordt geïnitieerd op een ander updatedomein.

Foutdomeinen duiden de groep virtuele machines aan die een gemeenschappelijke voeding en switch delen. Standaard worden de virtuele machines die in uw beschikbaarheidsset zijn geconfigureerd, gescheiden in Maxi maal drie fout domeinen voor implementaties van Resource Manager. Hoewel het in een beschikbaarheidsset plaatsen van uw virtuele machines uw toepassing niet beschermt tegen problemen met het besturingssysteem of de toepassing zelf, worden zo wel de gevolgen van mogelijke problemen met de fysieke hardware, netwerkstoringen of stroomonderbrekingen beperkt.

![Beschikbaarheidssets](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)


Voor virtuele machines die gebruikmaken van [Azure Managed Disks](./faq-for-disks.md) en deel uitmaken van een beheerde beschikbaarheidsset, worden de virtuele machines afgestemd op Managed Disk-foutdomeinen. Deze afstemming zorgt ervoor dat alle beheerde schijven die zijn gekoppeld aan een virtuele machine, zich binnen hetzelfde Managed Disk-foutdomein bevinden. 

In een beheerde beschikbaarheidsset kunnen alleen virtuele machines met beheerde schijven worden gemaakt. Het aantal Managed Disk-foutdomeinen verschilt per regio: er zijn twee of drie Managed Disk-foutdomeinen per regio. 

![Beheerde beschikbaarheidsset](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

## <a name="next-steps"></a>Volgende stappen
U kunt nu deze functies voor beschikbaarheid en redundantie gaan gebruiken om uw eigen Azure-omgeving te bouwen. Zie voor informatie over aanbevolen procedures de [aanbevolen procedures voor Azure-beschikbaarheid](/azure/architecture/checklist/resiliency-per-service).

