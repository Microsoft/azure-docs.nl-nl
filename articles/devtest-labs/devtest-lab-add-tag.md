---
title: Tags toevoegen aan een lab in Azure DevTest Labs | Microsoft Docs
description: Leer hoe u aangepaste tags maakt in Azure DevTest Labs en tags gebruikt om resources te categoriseren. U kunt alle resources in uw abonnement zien die een tag hebben.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a4d2b328626f0d0f096082fa88bb170894ccf3e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88270730"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Tags toevoegen aan een lab in Azure DevTest Labs

U kunt aangepaste tags maken en deze Toep assen op uw DevTest Labs-resources om uw resources logisch te categoriseren. U kunt later snel en eenvoudig alle resources in uw abonnement met die tag weer geven. Tags zijn handig wanneer u resources voor facturering of beheer wilt indelen.

De volgende bronnen worden ondersteund door Tags:

* Virtuele machines berekenen
* NIC’s
* IP-adressen
* Load balancers
* Opslagaccounts
* Managed Disks

U kunt Tags Toep assen wanneer u [een Lab maakt](devtest-lab-create-lab.md) en deze later beheren via de Blade labels onder configuratie en instellingen.

Elke tag bestaat uit een combi natie van een **naam** / **waarde** . U kunt bijvoorbeeld een tag maken met de naam *CostCenter* met de waarde *34543*. Een tag zoals dit kan nuttig zijn bij het identificeren van Lab-resources die Factureerbaar zijn voor dit specifieke gebied van uw organisatie. U krijgt de keuze uit namen en waarden die zinvol zijn voor de manier waarop u uw abonnement wilt ordenen.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Stappen voor het beheren van tags in een bestaand Lab

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer indien nodig **alle services** en selecteer vervolgens **DevTest Labs** in de lijst. Uw Lab wordt mogelijk al weer gegeven op het dash board onder **alle resources**.
1. Selecteer in de lijst met Labs het lab waarin u labels wilt toevoegen of beheren.
1. Selecteer in het gedeelte **overzicht** van het lab de optie **configuratie en beleid**.

    ![Knop configuratie en beleid](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. Selecteer aan de linkerkant onder **beheren** de optie **Tags**.
1. Als u een nieuwe tag voor dit Lab wilt maken, voert u een **naam** / **waarde** paar in en selecteert u **Opslaan**. U kunt ook een bestaande tag in de lijst selecteren om de resources die zijn gekoppeld aan deze tag te bekijken of te beheren.

    ![Tags beheren](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

> [!NOTE]
> Tags die zijn gemaakt op het niveau van het lab, worden door alle factureer bare resources door lopen die het lab in uw abonnement draait. Bijvoorbeeld, Lab-niveau Tags stroomt naar de onderliggende reken-Vm's van Lab Vm's. u kunt tags gebruiken in de context van cost management. Labels van het lab-niveau worden weer gegeven in het label filter voor het kosten beheer.

## <a name="understanding-limitations-to-tags"></a>Meer informatie over de beperkingen van Tags

Voor tags gelden de volgende beperkingen:

* Elke resource of resourcegroep mag maximaal 15 combinaties van tagnaam en -waarde hebben. Deze beperking geldt alleen voor tags die direct worden toegepast op de resourcegroep of resource. Een resourcegroep kan veel bronnen bevatten die elk 15 combinaties van tagnaam en -waarde bevatten.
* De tagnaam is beperkt tot 512 tekens en de tagwaarde is beperkt tot 256 tekens. Voor opslagaccounts is de tagnaam beperkt tot 128 tekens en de tagwaarde beperkt tot 256 tekens.
* Tags die zijn toegepast op de resourcegroep, worden niet overgenomen door de resources in deze resourcegroep.

[Tags gebruiken om uw Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md) , biedt meer informatie over het gebruik van tags in azure, met inbegrip van het beheren van tags met Power shell of Azure cli.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Volgende stappen
* U kunt beperkingen en conventies voor uw abonnement Toep assen met behulp van aangepast beleid. Een beleid dat u definieert, kan vereisen dat alle resources een waarde hebben voor een bepaalde tag. Zie [beleid en Schema's instellen](devtest-lab-set-lab-policy.md)voor meer informatie.
* Verken de [DevTest Labs Azure Resource Manager-sjabloon galerie voor Quick](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)start.
