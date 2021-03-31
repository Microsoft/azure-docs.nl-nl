---
title: VCPU-aanvragen voor quota verhogen Azure Resource Manager
description: VCPU-aanvragen voor quota verhogen Azure Resource Manager
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 2580da2a4ac7b943dee3e5e6ff8bdbd49664505b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745244"
---
# <a name="quota-increase-requests"></a>Aanvragen voor quotumverhoging

Resource Manager-vCPU quota voor virtuele machines en virtuele-machine schaal sets worden afgedwongen op twee lagen voor elk abonnement, in elke regio.

De eerste laag is de totale regionale Vcpu's limiet voor alle VM-reeksen. De tweede laag is de limiet voor de per VM-serie Vcpu's, zoals de Vcpu's van de D-serie. Telkens wanneer een nieuwe virtuele machine moet worden geïmplementeerd, mag de som van het nieuwe en bestaande Vcpu's-gebruik voor die VM-reeks niet groter zijn dan het vCPU-quotum dat is goedgekeurd voor die bepaalde VM-reeks. Daarnaast mag het totale nieuwe en bestaande vCPU aantal dat is geïmplementeerd in alle VM-reeksen niet groter zijn dan het totale regionale Vcpu's-quotum dat is goedgekeurd voor het abonnement. Als een van deze quota wordt overschreden, is de implementatie van de VM niet toegestaan.
U kunt een toename van de Vcpu's-quotum limiet voor de VM-serie aanvragen van Azure Portal. Een toename in het quotum van de VM-reeks verhoogt automatisch de totale regionale Vcpu's limiet met hetzelfde bedrag.

Wanneer een nieuw abonnement wordt gemaakt, is de standaard regionale Vcpu's mogelijk niet gelijk aan de som van de standaard vCPU-quota voor alle afzonderlijke VM-reeksen. Dit feit kan resulteren in een abonnement met voldoende quota voor elke afzonderlijke VM-reeks die u wilt implementeren. Het kan voldoende quotum voor het totale regionale Vcpu's voor alle implementaties hebben. In dit geval moet u een aanvraag indienen om de limiet voor de totale regionale Vcpu's expliciet te verhogen. De totale limiet van de regionale Vcpu's kan niet groter zijn dan de som van goedgekeurde quota voor alle VM-reeksen voor de regio.

> [!NOTE]
> Als u de limiet of het quotum boven de standaard limiet wilt verhogen, kunt u gratis [een online klant ondersteuning-aanvraag openen](../../azure-resource-manager/templates/error-resource-quota.md#solution).

Zie voor meer informatie over quota's [vCPU quota's voor virtuele machines](../../virtual-machines/windows/quotas.md) en [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md).