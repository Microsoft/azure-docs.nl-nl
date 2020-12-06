---
title: Een toename van vCPU-quotum limieten per Azure VM-reeks aanvragen
description: Een toename van de vCPU-quotum limiet voor een VM-reeks in het Azure Portal aanvragen, waardoor de totale regionale vCPU limiet wordt verhoogd met hetzelfde bedrag.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 5db3e538a64e275313e1e0ab01f6cc6350eabb77
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745431"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Standaardquotum: limieten verhogen per VM-reeks

Azure Resource Manager ondersteunt twee typen vCPU-quota's voor virtuele machines:

* Voor *vm's met betalen per gebruik* en *gereserveerde VM-instanties* gelden een *standaard vCPU-quotum*.
* Voor de *Spot-vm's* geldt een *vCPU-quotum*.

Het standaard vCPU-quotum voor betalen per gebruik en gereserveerde exemplaren van virtuele machines wordt afgedwongen op twee lagen voor elk abonnement in elke regio:

* De eerste laag is de *totale regionale vcpu's limiet*, in alle VM-reeksen.
* De tweede laag is de *limiet voor de serie vcpu's per VM*, zoals de vcpu's van de Dv3-serie.

Wanneer u een nieuwe spot-VM implementeert, mag het totale nieuwe en bestaande vCPU-gebruik voor alle exemplaren van de virtuele machine van de VM de goedgekeurde plaatsings vCPU-quotum limiet niet overschrijden. Als het steun quotum wordt overschreden, is de implementatie van de VM met steun niet toegestaan.

U kunt een verhoging van de vCPU-quotum limiet voor de VM-serie aanvragen met behulp van de Azure Portal. Een toename in het quotum van de VM-reeks verhoogt automatisch de totale regionale vCPU limiet met hetzelfde bedrag.

Zie vCPU voor [virtuele machines](../../virtual-machines/windows/quotas.md) en Azure-abonnementen en- [service limieten](./classic-deployment-model-quota-increase-requests.md)voor meer informatie over standaard-quota quota's.

Zie voor meer informatie over het verhogen van de limiet voor vCPU per regio voor standaard quotum de [standaard quota: limieten per regio verhogen](regional-quota-requests.md).

Zie [Spot quota: limieten voor alle VM-reeksen verhogen voor](low-priority-quota.md)meer informatie over het verhogen van de vCPU limieten voor de VM.

U kunt op twee manieren een verhoging van de standaard vCPU quotum limieten per VM-reeks aanvragen, zoals beschreven in de volgende secties.

## <a name="request-a-standard-quota-increase-from-help--support"></a>Een standaard quotum verhogen van Help en ondersteuning

U kunt als volgt een standaard vCPU-quotum toename per VM-serie aanvragen in **Help en ondersteuning**:

> [!NOTE]
> U kunt ook een toename van de quotum limiet voor meerdere regio's aanvragen via één ondersteunings aanvraag. Zie voor meer informatie stap 8.

1. Selecteer **Help en ondersteuning** in het menu [Azure Portal](https://portal.azure.com) .

   ![De koppeling Help en ondersteuning](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Selecteer **nieuwe ondersteunings aanvraag** in **Help en ondersteuning**.

    ![Een nieuwe ondersteunings aanvraag maken](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Selecteer voor **probleem type** **service-en abonnements limieten (quota's)**.

   ![Selecteer een probleem type](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Selecteer bij **abonnement** het abonnement waarvan u het quotum wilt verhogen.

   ![Selecteer een abonnement voor een verhoogd quotum](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Selecteer bij **quotum type** **Compute-VM (cores-vcpu's) de limiet voor het abonnement wordt verhoogd**.

   ![Een quotum type selecteren](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selecteer **volgende: oplossingen** om **probleem Details** te openen. Selecteer **Details opgeven** om aanvullende informatie in te voeren.

   ![De koppeling Details opgeven](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Voer de volgende stappen uit in de **quota Details**:

   ![Aanvullende quotum Details TProvide](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Voor **implementatie model** selecteert u het juiste model.

   1. Selecteer een locatie voor **locaties**. Voor de geselecteerde locatie, onder **typen** in **een type selecteren**, kiest u **standaard**.

      ![Quotum Details-quotum typen](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      Onder **typen** kunt u zowel de standaard-als steun quotum typen van één ondersteunings aanvraag aanvragen via ondersteuning voor meerdere selecties.

      Zie [Azure spot-vm's voor virtuele-machine schaal sets](../../virtual-machine-scale-sets/use-spot.md)voor meer informatie over het verhogen van de limieten voor steun quota.

   1. In **standaard** selecteert u de SKU-serie voor verhoogde quota's.

      ![Quotum Details-SKU-reeks](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Geef de nieuwe quotum limieten op die u voor dit abonnement wilt. Als u een SKU uit de lijst wilt verwijderen, schakelt u het selectie vakje naast de SKU uit of selecteert u het pictogram voor het verwijderen X.

      ![Een nieuwe vCPU-limiet selecteren](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Als u een quotum verhoging voor meer dan één locatie wilt aanvragen, selecteert u een extra locatie op **locaties** en selecteert u vervolgens een geschikt VM-type. U kunt vervolgens een limiet invoeren die van toepassing is op de extra locatie.

   ![Geef aanvullende locaties op in quotum Details](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecteer **opslaan en door gaan** om door te gaan met het maken van het ondersteunings verzoek.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>Een standaard quotum voor quota aanvragen van abonnementen

Een standaard vCPU-quotum verhoging per VM-reeks aanvragen van **abonnementen**:

> [!NOTE]
> U kunt ook een toename van de quotum limiet voor meerdere regio's aanvragen via één ondersteunings aanvraag. Zie stap 7 voor meer informatie.

1. Zoek in het [Azure Portal](https://portal.azure.com)naar en selecteer **abonnementen**.

   ![Abonnementen in de Azure Portal zoeken](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selecteer het abonnement waarvan u het quotum wilt verhogen.

   ![Te selecteren abonnementen voor wijzigingen](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Selecteer in het linkerdeel venster de optie **gebruik en quota's**.

   ![De koppeling gebruik en quota's](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Selecteer in de rechter bovenhoek de optie **aanvraag verg Roten**.

   ![Selecteren om het quotum te verhogen](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Selecteer bij **quotum type** **Compute-VM (cores-vcpu's) de limiet voor het abonnement wordt verhoogd**.

   ![Een quotum type selecteren](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Voer de volgende stappen uit in de **quota Details**:

   1. Voor **implementatie model** selecteert u het juiste model en selecteert u voor **locaties** een locatie.

      ![Details van quotum opgeven](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Selecteer voor de geselecteerde locatie onder **typen** de optie **Selecteer een type** en selecteer vervolgens **standaard**.

      ![Standaard type selecteren](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      Onder **typen** kunt u zowel de standaard-als steun quotum typen van één ondersteunings aanvraag aanvragen via ondersteuning voor meerdere selecties.

      Zie [Azure spot-vm's voor virtuele-machine schaal sets](../../virtual-machine-scale-sets/use-spot.md)voor meer informatie over het verhogen van de limieten voor steun quota.

   1. Selecteer voor **standaard** de SKU-serie waarvan u de quota's wilt verg Roten.

      ![Quotum Details-SKU-reeks](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Geef de nieuwe quotum limieten op die u voor dit abonnement wilt. Als u een SKU uit de lijst wilt verwijderen, schakelt u het selectie vakje naast de SKU uit of selecteert u het pictogram voor het verwijderen X.

      ![Een nieuwe vCPU-limiet selecteren](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Als u een quotum verhoging voor meer dan één locatie wilt aanvragen, selecteert u een extra locatie op **locaties** en selecteert u vervolgens een geschikt VM-type.

   Met deze stap wordt de SKU-serie die u hebt geselecteerd voor eerdere locaties vooraf geladen. Voer de quotum limieten in die u wilt Toep assen op de extra serie.

   ![Selecteer extra locaties in quotum Details](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecteer **opslaan en door gaan** om door te gaan met het maken van het ondersteunings verzoek.