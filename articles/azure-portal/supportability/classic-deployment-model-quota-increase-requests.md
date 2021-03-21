---
title: Klassieke Azure-implementatie model
description: Het klassieke implementatie model, dat nu door het Resource Manager-model wordt vervangen, dwingt een globale vCPU-quotum limiet af voor Vm's en virtuele-machine schaal sets.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: b5b5e8c11bfe164aaa3539742dac8c4d267c69e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745601"
---
# <a name="classic-deployment-model"></a>Klassiek implementatiemodel

Het klassieke implementatie model is het Azure-implementatie model van de oudere generatie. Hiermee wordt een globale vCPU-quotum limiet afgedwongen voor virtuele machines en virtuele-machine schaal sets. Het klassieke implementatie model wordt niet meer aanbevolen en wordt nu vervangen door het Resource Manager-model.

Zie [Resource Manager en klassieke implementatie](../../azure-resource-manager/management/deployment-models.md)voor meer informatie over deze twee implementatie modellen en de voor delen van het gebruik van Resource Manager.

Wanneer een nieuw abonnement wordt gemaakt, wordt er een standaard quotum van Vcpu's aan toegewezen. Telkens wanneer een nieuwe virtuele machine moet worden geïmplementeerd met behulp van het klassieke implementatie model, mag de som van het nieuwe en bestaande vCPU-gebruik in alle regio's niet groter zijn dan het vCPU-quotum dat is goedgekeurd voor het klassieke implementatie model.

Zie [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over quota's.

U kunt een verhoging van de vCPU-quotum limiet voor het klassieke implementatie model aanvragen. Gebruik **Help + ondersteuning** of **gebruik en quota** in de Azure Portal.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>Aanvraag per VM-serie vCPU quotum verhoging op abonnements niveau met behulp van Help en ondersteuning

Volg de onderstaande instructies om een ondersteunings aanvraag te maken met behulp van **Help en ondersteuning** in de Azure Portal.

1. Selecteer in het menu [Azure Portal](https://portal.azure.com) de optie **Help en ondersteuning**.

   ![Selecteer Help en ondersteuning in het Azure Portal](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Selecteer **Nieuwe ondersteuningsaanvraag**.

   ![Een nieuwe ondersteunings aanvraag maken in de Azure Portal](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Kies in **probleem type** **service-en abonnements limieten (quota's)**.

   ![Quota's selecteren als type probleem](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Selecteer het abonnement waarvan u het quotum wilt verhogen.

   ![Abonnement selecteren waarvoor een quotum moet worden verhoogd](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Selecteer bij **quotum type** **Compute-VM (cores-vcpu's) de limiet voor het abonnement wordt verhoogd**.

   ![Selecteer het quotum type dat u wilt verhogen](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selecteer **volgende: oplossingen** om **probleem Details** te openen. Selecteer **Details opgeven** om aanvullende informatie te geven.

   ![Geef details op om uw aanvraag langs te helpen](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Selecteer in **quotum Details** de optie **klassiek** en selecteer een **locatie**.

   ![Details toevoegen, waaronder het implementatie model en de locatie](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Selecteer voor **SKU-serie** een of meer SKU-families die u wilt verhogen.

   ![De SKU-familie opgeven die u wilt verg Roten](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Voer de nieuwe limieten in die u wilt voor het abonnement. Als u een regel wilt verwijderen, maakt u de selectie van de SKU van de **SKU-familie** ongedaan of selecteert u het pictogram ' X '. Nadat u een quotum voor elke SKU-familie hebt ingevoerd, selecteert u **opslaan en gaat u verder** met **quotum Details** om door te gaan met het ondersteunings verzoek.

   ![Nieuwe limieten aanvragen](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>Aanvraag per VM-serie vCPU quotum verhoging op abonnements niveau met gebruik en quota

Volg de onderstaande instructies om een ondersteunings aanvraag te maken met behulp van **gebruik en quota's** in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com)naar en selecteer **abonnementen**.

   ![Ga naar abonnementen in de Azure Portal](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selecteer het abonnement waarvan u het quotum wilt verhogen.

   ![Selecteer het abonnement dat u wilt wijzigen](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Selecteer **gebruik en quota's**.

   ![Gebruik en quota's voor een abonnement selecteren](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Selecteer in de rechter bovenhoek **aanvraag verhoging**.

   ![Selecteren om het quotum te verhogen](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Selecteer **Compute-VM (kernen-vcpu's) de limiet** van het abonnement wordt verhoogd als het **quota type**.

   ![Quotum type selecteren](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selecteer **volgende: oplossingen** om **probleem Details** te openen. Selecteer **Details opgeven** om aanvullende informatie te geven.

   ![Geef details op voor uw aanvraag](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Selecteer in **quotum Details** de optie **klassiek** en een **locatie**.

   ![Quotum Details selecteren, inclusief het implementatie model en de locatie](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Selecteer een of meer SKU-families voor een verhoging.

   ![SKU-familie selecteren voor toename](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Voer de nieuwe limieten in die u wilt voor het abonnement. Als u een regel wilt verwijderen, maakt u de selectie van de SKU van de **SKU-familie** ongedaan of selecteert u het pictogram ' X '. Nadat u een quotum voor elke SKU-familie hebt ingevoerd, selecteert u **opslaan en gaat u verder** met **quotum Details** om door te gaan met het ondersteunings verzoek.

   ![Nieuw quotum invoeren](./media/resource-manager-core-quotas-request/new-limits-classic.png)

