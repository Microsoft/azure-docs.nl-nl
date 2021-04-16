---
title: Overzicht van beschikbaarheidssets
description: Meer informatie over de beschikbaarheidssets in Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: b4e9f106354915fe40a4bcf9b60bc35443345202
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530572"
---
# <a name="availability-sets-overview"></a>Overzicht van beschikbaarheidssets

In dit artikel vindt u een overzicht van de beschikbaarheidsfuncties van virtuele Azure-machines (VM's).

## <a name="what-is-an-availability-set"></a>Wat is een beschikbaarheidsset? 

Een beschikbaarheidsset is een logische groepering van virtuele machines waaruit Azure kan begrijpen hoe uw toepassing is ontworpen, om zo redundantie en beschikbaarheid te kunnen bieden. We raden u aan twee of meer VM's te maken binnen een beschikbaarheidsset om te voorzien in een toepassing met hoge beschikbaarheid en om te voldoen aan de Azure SLA van [99,95%.](https://azure.microsoft.com/support/legal/sla/virtual-machines/) Er zijn geen kosten voor de beschikbaarheidsset zelf. U betaalt alleen voor elk VM-exemplaar dat u maakt.

## <a name="how-do-availability-sets-work"></a>Hoe werken beschikbaarheidssets?
Elke virtuele machine in uw beschikbaarheidsset krijgt een **updatedomein** en een **foutdomein** toegewezen door het onderliggende Azure-platform. Elke beschikbaarheidsset kan worden geconfigureerd met maximaal drie foutdomeinen en twintig updatedomeinen. Updatedomeinen geven groepen virtuele machines en onderliggende fysieke hardware aan die tegelijkertijd opnieuw kunnen worden opgestart. Wanneer in één beschikbaarheidsset meer dan vijf virtuele machines worden geconfigureerd, wordt de zesde virtuele machine in hetzelfde updatedomein geplaatst als de eerste virtuele machine, de zevende in hetzelfde updatedomein als de tweede virtuele machine, enzovoort. De volgorde waarin updatedomeinen opnieuw worden opgestart, verloopt tijdens gepland onderhoud niet altijd sequentieel, maar er wordt slechts één updatedomein tegelijk opnieuw opgestart. Een updatedomein dat opnieuw is opgestart, heeft 30 minuten om te herstellen voordat onderhoud wordt geïnitieerd op een ander updatedomein.

Foutdomeinen duiden de groep virtuele machines aan die een gemeenschappelijke voeding en switch delen. Standaard worden de virtuele machines die in uw beschikbaarheidsset zijn geconfigureerd, verdeeld over maximaal drie foutdomeinen. Hoewel het in een beschikbaarheidsset plaatsen van uw virtuele machines uw toepassing niet beschermt tegen problemen met het besturingssysteem of de toepassing zelf, worden zo wel de gevolgen van mogelijke problemen met de fysieke hardware, netwerkstoringen of stroomonderbrekingen beperkt.

:::image type="content" source="./media/virtual-machines-common-manage-availability/ud-fd-configuration.png" alt-text="Diagram met verschillende rekenclusters gesplitst in foutdomeinen en binnen deze foutdomeinen hebben we meerdere updatedomeinen":::

VM's worden ook uitgelijnd met schijffoutdomeinen. Deze uitlijning zorgt ervoor dat alle beheerde schijven die zijn gekoppeld aan een VM, zich binnen dezelfde foutdomeinen kunnen vinden. 

In een beheerde beschikbaarheidsset kunnen alleen virtuele machines met beheerde schijven worden gemaakt. Het aantal Managed Disk-foutdomeinen verschilt per regio: er zijn twee of drie Managed Disk-foutdomeinen per regio. 

:::image type="content" source="./media/virtual-machines-common-manage-availability/md-fd-updated.png" alt-text="Diagram waarin wordt weergegeven hoe de foutdomeinen voor schijven en VM's worden uitgelijnd.":::

## <a name="next-steps"></a>Volgende stappen
U kunt nu deze functies voor beschikbaarheid en redundantie gaan gebruiken om uw eigen Azure-omgeving te bouwen. Zie voor informatie over aanbevolen procedures de [aanbevolen procedures voor Azure-beschikbaarheid](/azure/architecture/checklist/resiliency-per-service).

