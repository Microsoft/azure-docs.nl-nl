---
title: Quota en limieten in uw Lab in Azure DevTest Labs schalen | Microsoft Docs
description: In dit artikel wordt beschreven hoe u uw Lab in Azure DevTest Labs kunt schalen. Bekijk uw gebruiks quota's en limieten en vraag om een verhoging.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2166eaab073e2abc24ca24494ae13eb876db1fcf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87533951"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Quota en limieten schalen in DevTest Labs
Wanneer u in DevTest Labs werkt, merkt u misschien dat er bepaalde standaard limieten gelden voor sommige Azure-resources, die van invloed kunnen zijn op de service DevTest Labs. Deze limieten worden **quota's** genoemd.

> [!NOTE]
> De DevTest Labs-service heeft geen quota opgelegd. Alle quota's die u kunt tegen komen, zijn standaard beperkingen van het hele Azure-abonnement.

U kunt elke Azure-Resource gebruiken totdat u het quotum bereikt. Elk abonnement heeft afzonderlijke quota en het gebruik wordt bijgehouden per abonnement.

Elk abonnement heeft bijvoorbeeld een standaard quotum van 20 kernen. Als u bijvoorbeeld virtuele machines in uw Lab maakt met vier kernen, kunt u slechts vijf Vm's maken.

Met de [limieten voor Azure-abonnementen en-services](../azure-resource-manager/management/azure-subscription-service-limits.md) worden enkele van de meest voorkomende Quota's voor Azure-resources vermeld. De resources die het meest worden gebruikt in een lab en waarvoor u quota kunt tegen komen, zijn onder andere VM-kernen, open bare IP-adressen, netwerk interface, beheerde schijven, Azure-roltoewijzing en ExpressRoute-circuits.

## <a name="view-your-usage-and-quotas"></a>Uw gebruik en quota's weer geven
In deze stappen ziet u hoe u de huidige quota's in uw abonnement kunt weer geven voor specifieke Azure-resources en kunt u zien welk percentage van elk quotum u hebt gebruikt.

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer **meer services** en selecteer vervolgens **facturering** in de lijst.
1. Selecteer een abonnement op de Blade facturering.
4. Selecteer **gebruik en quota's**.

   ![De knop gebruik en quota's](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas-new.png)

   De Blade gebruik + quota's wordt weer gegeven, waarin verschillende beschik bare resources in het abonnement en het percentage van het quotum dat per resource wordt gebruikt, worden vermeld.

   ![Quota en gebruik](./media/devtest-lab-scale-lab/devtestlab-view-quotas-new.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Meer resources aanvragen in uw abonnement
Als u een quotum limiet bereikt, kan de standaard limiet van een resource in een abonnement worden verhoogd tot een maximum limiet, zoals beschreven in [Azure-abonnement en service limieten](../azure-resource-manager/management/azure-subscription-service-limits.md).

Deze stappen laten zien hoe u een quotum verhoging kunt aanvragen via de [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecteer **meer services**, selecteer **facturering** en selecteer vervolgens **gebruik en quota's**.
1. Selecteer op de Blade gebruik en quota's de knop voor het **verg Roten van aanvragen** .

   ![Knop verhogen aanvragen](./media/devtest-lab-scale-lab/devtestlab-request-increase-new.png)

1. Vul de vereiste gegevens in op alle drie tabbladen van het nieuwe formulier voor een **ondersteunings aanvraag** om de aanvraag uit te voeren en in te dienen.

   ![Formulier verg Roten aanvragen](./media/devtest-lab-scale-lab/devtestlab-support-form-new.png)

Meer informatie over [Azure-limieten en verg Roten](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) biedt meer gegevens over het contact opnemen met de ondersteuning van Azure om een quotum toename aan te vragen.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Volgende stappen
* Verken de [DevTest Labs Azure Resource Manager-sjabloon galerie voor Quick](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)start.
