---
title: Een gelicentieerde installatie kopie inschakelen in uw Lab in Azure DevTest Labs | Microsoft Docs
description: Meer informatie over het inschakelen van een gelicentieerde installatie kopie in Azure DevTest Labs met behulp van de Azure Portal
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e2b6e495be2257548782143517f5a970b424860c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91276353"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Een gelicentieerde installatie kopie inschakelen in uw Lab in Azure DevTest Labs

In Azure DevTest Labs is een gelicentieerde installatie kopie die voor waarden bevat, doorgaans van een derde partij, die moet worden geaccepteerd voordat de installatie kopie toegankelijk is voor gebruikers in het lab. In de volgende secties wordt beschreven hoe u met gelicentieerde installatie kopieën werkt zodat ze beschikbaar zijn voor het maken van virtuele machines.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Bepalen of een gelicentieerde installatie kopie beschikbaar is voor gebruikers
De eerste stap om gebruikers toe te staan Vm's te maken op basis van een gelicentieerde installatie kopie, is ervoor te zorgen dat de voor waarden zijn geaccepteerd voor de gelicentieerde installatie kopie. De volgende stappen laten zien hoe u de status van een gelicentieerde installatie kopie kunt bekijken en, indien nodig, de voor waarden van de aanbieding accepteert.

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de lijst.

1. Selecteer in de lijst met Labs het gewenste Lab.  

1. Selecteer in het linkerdeel venster onder **instellingen** de optie **configuratie en beleid**.

1. Selecteer in het linkerdeel venster onder basis van **virtuele machine** de optie **Marketplace-installatie kopieën**. 

    ![Menu-item Marketplace-installatie kopieën](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    Er wordt een lijst weer gegeven met alle beschik bare Marketplace-installatie kopieën, inclusief de STATUS van de **aanbieding** voor elke installatie kopie.

    ![Lijst met Marketplace-installatie kopieën met de status van de aanbieding voor elke installatie kopie](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    Een afbeelding met een licentie toont een aanbiedings status van 
    
    - **Voor waarden geaccepteerd:** de gelicentieerde installatie kopie is beschikbaar voor gebruikers om vm's te maken. 
    - **Voor waarden voor beoordeling vereist:** de gelicentieerde installatie kopie is momenteel niet beschikbaar voor gebruikers. De voor waarden van de licentie moeten worden geaccepteerd voordat Lab-gebruikers ze kunnen gebruiken om virtuele machines te maken. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Een gelicentieerde installatie kopie beschikbaar maken voor gebruikers met een Lab
Om ervoor te zorgen dat een gelicentieerde installatie kopie beschikbaar is voor gebruikers met een lab, moet een Lab-eigenaar met beheerders machtigingen eerst de voor waarden voor die gelicentieerde afbeelding accepteren. Als u programmatische implementatie inschakelt voor het abonnement dat is gekoppeld aan een gelicentieerde installatie kopie, worden automatisch de juridische voor waarden en privacyverklaringen voor die installatie kopie geaccepteerd. [Werken met Marketplace-installatie kopieën op Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) biedt aanvullende informatie over het programmatisch implementeren van installatie kopieën van Marketplace.

U kunt de programmatische implementatie voor een gelicentieerde installatie kopie inschakelen door de volgende stappen uit te voeren:

1. Ga in het [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)naar de lijst met **installatie kopieën voor Marketplace**.

1. Identificeer een gelicentieerde installatie kopie waarvoor gebruikers toegang mogen hebben, maar waarvan de voor waarden niet zijn geaccepteerd. U ziet bijvoorbeeld een Data Science Virtual Machine waarin de status van de **geaccepteerde voor waarden** of de **vereiste voor waarden** worden weer gegeven.

    ![Scherm opname van Data Science Virtual Machine afbeeldingen met de status van de aanbieding geaccepteerd en voor waarden controleren vereist](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Data Science Vm's zijn installatie kopieën van virtuele machines van Azure, vooraf geïnstalleerd, geconfigureerd en getest met verschillende populaire hulpprogram ma's die veel worden gebruikt voor gegevens analyse, machine learning-en AI-training. [Inleiding tot Azure data Science virtual machine voor Linux en Windows](../machine-learning/data-science-virtual-machine/overview.md) biedt veel informatie over dsvm.
   >
   >

1. Selecteer in de kolom **aanbiedings status** voor de installatie kopie de optie voor het controleren van de **voor waarden**.

1. Selecteer **inschakelen** in het venster programmatische implementatie configureren.

    ![Het venster programmatische implementatie configureren](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > U ziet mogelijk meerdere abonnementen die worden weer gegeven in het venster programmatische implementatie configureren. Zorg ervoor dat u alleen programmatische implementatie inschakelt voor het beoogde abonnement.
   >
   >


1. Selecteer **Opslaan**. 

    In de lijst met installatie kopieën van Marketplace worden nu de **voor waarden geaccepteerd** en beschikbaar gesteld voor gebruikers om virtuele machines te maken.

> [!NOTE]
> Gebruikers kunnen een aangepaste installatie kopie maken op basis van een gelicentieerde installatie kopie. Zie [een aangepaste installatie kopie maken op basis van een VHD-bestand](devtest-lab-create-template.md) voor meer informatie.
>
>


## <a name="related-blog-posts"></a>Gerelateerde blog berichten

- [Aangepaste afbeeldingen of formules?](./devtest-lab-faq.md#blog-post)
- [Aangepaste installatie kopieën kopiëren tussen Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste installatiekopie maken vanaf een virtuele machine](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Een aangepaste installatie kopie maken van een VHD-bestand](devtest-lab-create-template.md)
- [Een virtuele machine toevoegen aan uw Lab](devtest-lab-add-vm.md)
