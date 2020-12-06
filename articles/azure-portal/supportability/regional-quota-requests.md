---
title: Een toename van Azure Regional vCPU-quotum limieten aanvragen
description: Een toename van de vCPU-quotum limiet voor een regio in de Azure Portal aanvragen.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: eadf740c6b5caccbf678a1238f993d4ec0b34095
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745414"
---
# <a name="standard-quota-increase-limits-by-region"></a>Standaard quotum: limieten per regio verhogen

Azure Resource Manager ondersteunt twee typen vCPU-quota's voor virtuele machines:

* Voor *vm's met betalen per gebruik* en *gereserveerde VM-instanties* gelden een *standaard vCPU-quotum*.
* Voor de *Spot-vm's* geldt een *vCPU-quotum*.

Het standaard vCPU-quotum voor betalen per gebruik en gereserveerde exemplaren van virtuele machines wordt afgedwongen op twee lagen voor elk abonnement in elke regio:

* De eerste laag is de *totale regionale vcpu's limiet*, in alle VM-reeksen.
* De tweede laag is de *limiet voor de serie vcpu's per VM*, zoals de vcpu's van de D-serie.

Wanneer u een nieuwe spot-VM implementeert, mag het totale nieuwe en bestaande vCPU-gebruik voor die VM-reeks niet groter zijn dan het goedgekeurde vCPU-quotum voor die bepaalde VM-reeks. Daarnaast mag het totale aantal nieuwe en bestaande Vcpu's die zijn geïmplementeerd in alle VM-reeksen niet groter zijn dan het totale goedgekeurde regionale vCPU-quotum voor het abonnement. Als een van deze quota wordt overschreden, is de implementatie van de VM niet toegestaan.

U kunt een verhoging van de vCPU-quotum limiet voor de VM-serie aanvragen met behulp van de Azure Portal. Een toename in het quotum van de VM-reeks verhoogt automatisch de totale regionale vCPU limiet met hetzelfde bedrag.

Wanneer u een nieuw abonnement maakt, is het standaard totaal aantal regionale Vcpu'ss mogelijk niet gelijk aan het totale standaard quotum voor vCPU voor alle afzonderlijke VM-reeksen. Dit verschil kan resulteren in een abonnement met voldoende quota voor elke afzonderlijke VM-reeks die u wilt implementeren. Het is echter mogelijk dat er onvoldoende quota zijn voor de totale regionale Vcpu's van alle implementaties. In dit geval moet u een aanvraag indienen om de limiet van het totale aantal regionale Vcpu's expliciet te verhogen. De limiet voor het totale regionale vCPU kan niet groter zijn dan het totale goedgekeurde quotum voor alle VM-reeksen voor de regio.

Zie voor meer informatie over de standaard vCPU-quota's de [virtuele machine vCPU quota's](../../virtual-machines/windows/quotas.md) en [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Zie [Spot quota: limieten voor alle VM-reeksen verhogen voor](low-priority-quota.md)meer informatie over het verhogen van de vCPU limieten voor de VM.

U kunt op twee manieren een verhoging van uw vCPU-standaard quotum limiet per regio aanvragen.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>Een quotum verhoging per regio aanvragen bij Help en ondersteuning

Voor het aanvragen van een vCPU quotum toename per regio van **Help en ondersteuning**:

1. Selecteer in het menu [Azure Portal](https://portal.azure.com) de optie **Help en ondersteuning**.

   ![De koppeling Help + ondersteuning](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Selecteer **nieuwe ondersteunings aanvraag** in **Help en ondersteuning**.

    ![Nieuwe ondersteuningsaanvraag](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Selecteer voor **probleem type** **service-en abonnements limieten (quota's)**.

   ![Selecteer een probleem type](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Selecteer bij **abonnement** het abonnement waarvan u het quotum wilt verhogen.

   ![Een abonnement selecteren](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Voor het **quotum type** selecteert u **andere aanvragen**.

   ![Een quotum type selecteren](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Selecteer **volgende: oplossingen** om **probleem Details** te openen. Geef in **Beschrijving** de volgende informatie op:

    1. Geef voor **implementatie model** **Resource Manager** op.  
    1. Geef voor **regio** uw vereiste regio op, bijvoorbeeld VS- **Oost 2**.  
    1. Geef voor **nieuwe limiet** een nieuwe vCPU-limiet op voor de regio. Deze waarde mag niet groter zijn dan de som van de goedgekeurde quota's voor de afzonderlijke SKU-serie voor dit abonnement.

    ![Details voor de quotum aanvraag invoeren](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selecteer **controleren + maken** om door te gaan met het maken van het ondersteunings verzoek.

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>Een quotum toename per regio aanvragen bij abonnementen

Om een vCPU-quotum toename per regio aan te vragen bij **abonnementen**:

1. Zoek in het [Azure Portal](https://portal.azure.com)naar en selecteer **abonnementen**.

   ![Ga naar abonnementen in de Azure Portal](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selecteer het abonnement waarvan u het quotum wilt verhogen.

   ![Selecteer een abonnement om te wijzigen](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Selecteer in het linkerdeel venster de optie **gebruik en quota's**.

   ![De koppeling gebruik en quota's volgen](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Selecteer in de rechter bovenhoek de optie **aanvraag verg Roten**.

   ![Selecteren om het quotum te verhogen](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Selecteer van **quotum type** **andere aanvragen**.

   ![Het quotum type selecteren](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Selecteer **volgende: oplossingen** om **probleem Details** te openen. Geef in het vak **Beschrijving** de volgende aanvullende informatie op:

    1. Geef voor **implementatie model** **Resource Manager** op.  
    1. Geef voor **regio** uw vereiste regio op, bijvoorbeeld VS- **Oost 2**.  
    1. Geef voor **nieuwe limiet** een nieuwe vCPU-limiet op voor de regio. Deze waarde mag niet groter zijn dan de som van de goedgekeurde quota's voor de afzonderlijke SKU-serie voor dit abonnement.

    ![Voer informatie in details in](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selecteer **controleren + maken** om door te gaan met het maken van het ondersteunings verzoek.
