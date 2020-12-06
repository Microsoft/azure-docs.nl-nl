---
title: Het quotum van de virtuele machine van de locatie
description: Verhoog de quotum limieten voor harde Vm's, die een model van Azure-gebruik bieden waarmee u lagere kosten in Exchange kunt gebruiken om Azure virtuele machines te laten verwijderen als dat nodig is.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c8b9a2251d7923fe1919b7b934f6c97877cd5b37
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745482"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Spot quotum: limieten voor alle VM-reeksen verhogen

Spot virtuele machines (Vm's) bieden een ander model voor Azure-gebruik. Ze laten u uitgaan van lagere kosten in Exchange, zodat Azure virtuele machines kan verwijderen als dat nodig is voor de implementaties van betalen per gebruik of gereserveerde VM-instanties. Zie [Azure spot vm's voor virtuele-machine schaal sets](../../virtual-machine-scale-sets/use-spot.md)voor meer informatie over spot vm's.

Azure Resource Manager ondersteunt twee typen vCPU-quota's voor virtuele machines:

* Voor *vm's met betalen per gebruik* en *gereserveerde VM-instanties* gelden een *standaard vCPU-quotum*.
* Voor de *Spot-vm's* geldt een *vCPU-quotum*.

Voor het quota type spot vCPU worden vCPU-quota van Resource Manager afgedwongen voor alle beschik bare reeksen van virtuele machines als één regionale limiet.

Wanneer u een nieuwe spot-VM implementeert, mag het totale nieuwe en bestaande vCPU-gebruik voor alle exemplaren van de virtuele machine van de VM de goedgekeurde plaatsings vCPU-quotum limiet niet overschrijden. Als het steun quotum wordt overschreden, is de implementatie van de VM met steun niet toegestaan.

In dit artikel wordt beschreven hoe u een toename van de vCPU-quotum limiet aanvraagt met behulp van de Azure Portal.

Zie voor meer informatie over de standaard vCPU-quota's de [virtuele machine vCPU quota's](../../virtual-machines/windows/quotas.md) en [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Zie [standaard quotum: limieten per regio verhogen](regional-quota-requests.md)voor meer informatie over het verhogen van de limiet voor vCPU per regio.

## <a name="request-a-quota-limit-increase-from-help--support"></a>Een verhoging van de quotum limiet van Help en ondersteuning aanvragen

Voor het aanvragen van een verhoging van de limiet voor het aantal virtuele machines met behulp van **Help en ondersteuning**:

> [!NOTE]
> U kunt ook een toename van de quotum limiet voor meerdere regio's aanvragen via één ondersteunings aanvraag. Zie voor meer informatie stap 8.

1. Selecteer in het menu [Azure Portal](https://portal.azure.com) de optie **Help en ondersteuning**.

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

1. Voer de volgende stappen uit in **quotum Details**:

   1. Voor **implementatie model** selecteert u het juiste model en selecteert u voor **locaties** een locatie.

      ![Aanvullende quotum Details opgeven](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Voor de geselecteerde locatie, onder **typen** in **een type selecteren**, kiest u **Spot**.

      ![Steun type selecteren](./media/resource-manager-core-quotas-request/select-spot-type.png)

       Onder **typen** kunt u zowel de standaard-als steun quotum typen van één ondersteunings aanvraag aanvragen via ondersteuning voor meerdere selecties.

       Zie voor meer informatie [standaard quotum: limieten verhogen per VM-serie](per-vm-quota-requests.md).

   1. Voer de nieuwe quotum limiet in die u voor dit abonnement wilt.

      ![Een nieuw quotum voor de spot VM selecteren](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Als u een quotum verhoging voor meer dan één locatie wilt aanvragen, selecteert u een extra locatie op **locaties** en selecteert u vervolgens een geschikt VM-type. U kunt vervolgens een limiet invoeren die van toepassing is op de extra locatie.

   ![Geef aanvullende locaties op in quotum Details](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecteer **opslaan en door gaan** om door te gaan met het maken van het ondersteunings verzoek.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Een toename van de quotum limiet van het deel venster abonnementen aanvragen

Als u een verhoging van de limiet voor het aantal vm's voor alle VM-reeksen wilt aanvragen in het deel venster **abonnementen** :

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

1. Selecteer **volgende: oplossingen** om **probleem Details** te openen. Selecteer **Details opgeven** om aanvullende informatie in te voeren. Voer de volgende gegevens in bij **quotum Details**:

   1. Voor **implementatie model** selecteert u het juiste model en selecteert u voor **locaties** een locatie.

      ![Details van quotum opgeven](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Voor de geselecteerde locatie, onder **typen** in **een type selecteren**, kiest u **Spot**.

      ![Steun type selecteren](./media/resource-manager-core-quotas-request/select-spot-type.png)

      Zie voor meer informatie [standaard quotum: limieten verhogen per VM-serie](per-vm-quota-requests.md).

   1. Voer de nieuwe quotum limiet in die u voor dit abonnement wilt.

      ![Voer een nieuwe waarde in voor de vCPU-limiet](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Als u een quotum verhoging voor meer dan één locatie wilt aanvragen, selecteert u een extra locatie op **locaties** en selecteert u vervolgens een geschikt VM-type. U kunt vervolgens een limiet invoeren die van toepassing is op de extra locatie.

   ![Selecteer extra locaties in quotum Details](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecteer **opslaan en door gaan** om door te gaan met het maken van het ondersteunings verzoek.
