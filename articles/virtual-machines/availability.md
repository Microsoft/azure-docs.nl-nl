---
title: Beschikbaarheidsopties
description: Meer informatie over de beschik baarheid van functies voor het uitvoeren van virtuele machines in azure
author: cynthn
ms.author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: c336c1632cf206cdf2bf7151dc191c4de5ef820d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036925"
---
# <a name="availability-options-for-virtual-machines-in-azure"></a>Beschikbaarheidsopties voor virtuele machines in Azure

In dit artikel vindt u een overzicht van de beschik baarheid van Azure virtual machines (Vm's).

## <a name="high-availability"></a>Hoge beschikbaarheid

Werk belastingen worden meestal verspreid over verschillende virtuele machines voor hoge door Voer, prestaties en het maken van redundantie voor het geval dat een virtuele machine wordt beïnvloed door een update of een andere gebeurtenis. 

Azure biedt een aantal opties die voor hoge Beschik baarheid kunnen worden gerealiseerd. Eerst bespreken we de basis constructies. 

### <a name="availability-zones"></a>Beschikbaarheidszones

[Beschikbaarheidszones](../availability-zones/az-overview.md) vergroten de controle die u nodig hebt om de beschikbaarheid van de toepassingen en gegevens op uw VM's te behouden. Een beschikbaarheids zone is een fysiek gescheiden zone binnen een Azure-regio. Er zijn drie Beschikbaarheidszones per ondersteunde Azure-regio. 

Elke beschikbaarheidszone heeft een afzonderlijke voedingsbron en koeling, en een afzonderlijk netwerk. Door uw oplossingen te ontwikkelen voor het gebruik van gerepliceerde Vm's in zones, kunt u uw apps en gegevens beveiligen tegen verlies van een Data Center. Als er een zone wordt aangetast, zijn de gerepliceerde apps en gegevens direct beschikbaar in een andere zone. 

![Beschikbaarheidszones](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Meer informatie over het implementeren van een VM voor [Windows](./windows/create-powershell-availability-zone.md) of [Linux](./linux/create-cli-availability-zone.md) in een beschikbaarheidszone.


### <a name="fault-domains"></a>Foutdomeinen

Een foutdomein is een logische groep onderliggende hardware met een gemeenschappelijke voeding en netwerkswitch, vergelijkbaar met een rack in een on-premises datacenter. 

### <a name="update-domains"></a>Updatedomeinen

Een updatedomein is een logische groep onderliggende hardware die op hetzelfde moment onderhoud kan ondergaan of opnieuw kan worden opgestart. 

Deze aanpak zorgt ervoor dat altijd ten minste één exemplaar van uw toepassing beschikbaar blijft wanneer er periodiek onderhoud wordt uitgevoerd aan het Azure-platform. De volg orde waarin update domeinen opnieuw worden opgestart, wordt mogelijk niet sequentieel voortgezet tijdens het onderhoud, maar er wordt slechts één update domein tegelijk opnieuw opgestart.


## <a name="virtual-machines-scale-sets"></a>Virtual Machines schaal sets 

Met behulp van schaalsets voor virtuele Azure-machines kunt u een groep VM's met gelijke taakverdeling maken en beheren. Het aantal VM-exemplaren kan automatisch toenemen of afnemen in reactie op vraag of een ingesteld schema. Schaal sets bieden een hoge Beschik baarheid voor uw toepassingen en u kunt een groot aantal virtuele machines centraal beheren, configureren en bijwerken. We raden u aan twee of meer virtuele machines te maken binnen een schaalset om te voorzien in een Maxi maal beschik bare toepassing en om te voldoen aan de [99,95% Azure Sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Er zijn geen kosten verbonden aan de schaalset zelf, u betaalt alleen voor elk VM-exemplaar dat u maakt. Wanneer één virtuele machine gebruikmaakt van [Azure Premium ssd's](./disks-types.md#premium-ssd), is de Azure-sla van toepassing op niet-geplande onderhouds gebeurtenissen. Virtuele machines in een schaalset kunnen worden geïmplementeerd in meerdere update domeinen en fout domeinen om de beschik baarheid en tolerantie te maximaliseren voor uitval vanwege Data Center-storingen en geplande of niet-geplande onderhouds gebeurtenissen. Virtuele machines in een schaalset kunnen ook in één beschikbaarheids zone of regio worden geïmplementeerd. De implementatie opties voor de beschikbaarheids zone kunnen verschillen op basis van de Orchestration-modus.

**Fout domeinen en update domeinen**

Virtuele-machine schaal sets vereenvoudigen het ontwerpen voor hoge Beschik baarheid door het uitlijnen van fout domeinen en update domeinen. U hoeft alleen het aantal fout domeinen voor de schaalset te definiëren. Het aantal fout domeinen dat beschikbaar is voor de schaal sets kan per regio verschillen. Zie [de beschik baarheid van virtuele machines in azure beheren](./manage-availability.md).

**Orchestration-modi voor schaal sets**

Met de indelings modi voor virtuele-machine schaal sets kunt u meer controle over de manier waarop exemplaren van virtuele machines worden beheerd door de schaalset. U kunt een uniforme of flexibele Orchestration-modus inschakelen in uw schaalset. Uniforme indeling is geoptimaliseerd voor grootschalige workloads met een hoge schaal met identieke exemplaren. Flexibele indeling (preview) is bedoeld voor hoge Beschik baarheid op schaal met identieke of meerdere typen virtuele machines. Meer informatie over deze [Orchestration-modi](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md) en hoe u deze kunt inschakelen.


## <a name="availability-sets"></a>Beschikbaarheidssets
Een beschikbaarheidsset is een logische groepering van virtuele machines waaruit Azure kan begrijpen hoe uw toepassing is ontworpen, om zo redundantie en beschikbaarheid te kunnen bieden. We raden u aan twee of meer virtuele machines te maken binnen een beschikbaarheidsset om te voorzien in een Maxi maal beschik bare toepassing en om te voldoen aan de [99,95% Azure Sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Er zijn geen kosten voor de Beschikbaarheidsset zelf, u betaalt alleen voor elk VM-exemplaar dat u maakt. Wanneer één virtuele machine gebruikmaakt van [Azure Premium ssd's](./disks-types.md#premium-ssd), is de Azure-sla van toepassing op niet-geplande onderhouds gebeurtenissen.

In een beschikbaarheidsset worden Vm's automatisch gedistribueerd in deze fout domeinen. Deze aanpak beperkt de gevolgen van mogelijke problemen met de fysieke hardware, netwerkstoringen of stroomonderbrekingen.

Voor virtuele machines die gebruikmaken van [Azure Managed Disks](./faq-for-disks.md) en deel uitmaken van een beheerde beschikbaarheidsset, worden de virtuele machines afgestemd op Managed Disk-foutdomeinen. Deze afstemming zorgt ervoor dat alle beheerde schijven die zijn gekoppeld aan een virtuele machine, zich binnen hetzelfde Managed Disk-foutdomein bevinden. 

In een beheerde beschikbaarheidsset kunnen alleen virtuele machines met beheerde schijven worden gemaakt. Het aantal Managed Disk-foutdomeinen verschilt per regio: er zijn twee of drie Managed Disk-foutdomeinen per regio. Meer informatie over deze Managed Disk-fout domeinen voor [Linux-vm's](./manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) of [Windows-vm's](./manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)vindt u hier.

![Beheerde beschikbaarheidsset](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


Vm's binnen een beschikbaarheidsset worden ook automatisch gedistribueerd in update domeinen. 

![Beschikbaarheidssets](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Volgende stappen
U kunt nu deze functies voor beschikbaarheid en redundantie gaan gebruiken om uw eigen Azure-omgeving te bouwen. Zie voor informatie over aanbevolen procedures de [aanbevolen procedures voor Azure-beschikbaarheid](/azure/architecture/checklist/resiliency-per-service).
