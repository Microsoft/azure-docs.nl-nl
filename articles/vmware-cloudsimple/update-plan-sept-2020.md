---
title: Azure VMware-oplossing van CloudSimple september 2020 update
description: In dit artikel vindt u informatie over wat u kunt verwachten tijdens deze onderhouds bewerking en wijzigingen in uw privécloud.
author: dikamath
ms.author: dikamath
ms.date: 09/3/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
manager: dikamath
ms.openlocfilehash: ae9c1ba5259e95ed030d7099e5dafe2d4f7935b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89447853"
---
# <a name="azure-vmware-solution-by-cloudsimple-september-2020-update"></a>Azure VMware-oplossing van CloudSimple september 2020 update

In september wordt een belang rijke update voor Azure VMware Solution service uitgevoerd. Een e-mail melding die als onderdeel van het onderhoud wordt verzonden, omvat de tijd lijn van het onderhoud. In dit artikel vindt u informatie over wat u kunt verwachten tijdens deze onderhouds bewerking en wijzigingen in uw privécloud.

> [!NOTE]
> Dit is een niet-verstorende upgrade. Tijdens de upgrade ziet u mogelijk een van de redundante onderdelen.

## <a name="vmware-infrastructure-upgrade"></a>VMware-infrastructuur upgrade

De VMware-infra structuur van uw privécloud wordt bijgewerkt naar een nieuwere versie. Dit omvat updates voor de onderdelen vCenter, ESXi, NSX-T en Hybrid Cloud extension (HCX) (indien geïmplementeerd) van uw privécloud.

Tijdens de upgrade wordt een nieuw knoop punt toegevoegd aan uw privécloud voordat een knoop punt in de onderhouds modus wordt geplaatst voor de upgrade. Dit zorgt ervoor dat de capaciteit van uw privécloud en de beschik baarheid van uw privécloud behouden blijft tijdens het upgrade proces. Tijdens de upgrade van VMware-onderdelen ziet u mogelijk waarschuwingen weer gegeven in uw vCenter-webgebruikersinterface. De waarschuwingen zijn onderdeel van de onderhouds bewerkingen die worden uitgevoerd door het service operations-team.

**Onderdeel versie**

- ESXi 6,7 U3
- vCenter 6,7 U3
- vSAN 6,7
- NSX Data Center 2.5.1
- HCX 3.5.2

## <a name="datacenter-updates"></a>Data Center-updates

Deze update bevat updates voor de infra structuur van data centers. Tijdens de onderhouds periode worden niet-verstorende updates uitgevoerd. Tijdens het update proces ziet u minder redundantie. Waarschuwingen kunnen worden gegenereerd in uw particuliere cloud-VMware-infra structuur, uw ExpressRoute-circuits, GlobalReach-verbindingen en alle site-naar-site-VPN-apparaten die betrekking hebben op een van de koppelings-Beschik baarheid. Dit is normaal tijdens de update omdat de onderdelen opnieuw worden opgestart als onderdeel van de update.

-   Als een site-naar-site-VPN wordt geïmplementeerd als één exemplaar (niet-HA), moet u mogelijk de VPN-verbinding opnieuw tot stand brengen.

-   Als u een punt-naar-site-VPN-verbinding gebruikt, moet u de VPN-verbinding opnieuw tot stand brengen.

## <a name="post-update"></a>Update plaatsen

Zodra de updates zijn voltooid, ziet u nieuwere versies van VMware-onderdelen. Als u problemen ondervindt of vragen hebt, neemt u contact op met ons ondersteunings team door een [ondersteunings ticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)te openen.
