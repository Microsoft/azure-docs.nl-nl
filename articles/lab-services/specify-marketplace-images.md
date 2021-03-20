---
title: Marketplace-installatie kopieën opgeven voor een lab in Azure Lab Services
description: In dit artikel wordt beschreven hoe u de Marketplace-installatie kopieën kunt opgeven die door Lab Creator kunnen worden gebruikt om Labs te maken in een Lab-account in Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5c81c8d7a15d67055729a29e98f5b7e30f3d0764
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434733"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Microsoft Azure Marketplace-installatiekopieën opgeven die beschikbaar zijn voor labmakers
Als eigenaar van een labaccount kunt u de Marketplace-installatiekopieën opgeven die labmakers kunnen gebruiken in het labaccount. 

## <a name="select-images-available-for-labs"></a>Beschik bare installatie kopieën voor Labs selecteren
Selecteer **Marketplace-installatiekopieën** in het menu aan de linkerkant. Standaard ziet u de volledige lijst met installatiekopieën (zowel ingeschakelde als uitgeschakelde). U kunt de lijst filteren om alleen ingeschakelde/uitgeschakelde installatie kopieën weer te geven door de optie **ingeschakeld** alleen / **uitgeschakeld** in de vervolg keuzelijst bovenaan te selecteren. 
    
![Pagina Microsoft Azure Marketplace-installatiekopieën](./media/tutorial-setup-lab-account/marketplace-images-page.png)

De Marketplace-installatiekopieën die worden weergegeven in de lijst, zijn de enige die voldoen aan de volgende voorwaarden:
    
- Hiermee wordt een enkele VM gemaakt.
- Maakt gebruik van Azure Resource Manager om VM’s in te richten
- Hiervoor hoeft u geen extra licentieabonnement aan te schaffen

## <a name="disable-images-for-a-lab"></a>Installatie kopieën voor een Lab uitschakelen 
Als u één installatie kopie voor een Lab wilt uitschakelen, selecteert u **... (weglatings tekens)** in de laatste kolom en selecteer **installatie kopie uitschakelen**. 

![Een installatiekopie uitschakelen](./media/tutorial-setup-lab-account/disable-one-image.png) 

U kunt ook het selectie vakje voor de naam van de installatie kopie selecteren en **geselecteerde installatie kopieën** op de werk balk uitschakelen selecteren. 

Als u meerdere installatie kopieën tegelijk wilt uitschakelen, selecteert u de selectie vakjes voor de naam van de afbeelding en selecteert u **geselecteerde afbeeldingen** op de werk balk uitschakelen. 

![Meerdere installatiekopieën uitschakelen](./media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Installatie kopieën inschakelen voor een Lab
Als u een uitgeschakelde afbeelding wilt inschakelen, selecteert u **... (weglatings tekens)** in de laatste kolom en selecteer **afbeelding inschakelen**. U kunt ook het selectie vakje voor de naam van de installatie kopie selecteren en **geselecteerde installatie kopieën** op de werk balk inschakelen. 

Als u meerdere installatie kopieën tegelijk wilt uitschakelen, selecteert u de selectie vakjes voor de naam van de afbeelding en selecteert u **geselecteerde afbeeldingen** op de werk balk inschakelen. 

## <a name="enable-images-at-the-time-of-lab-creation"></a>Installatie kopieën inschakelen op het moment dat het lab wordt gemaakt
U kunt meer installatie kopieën op het moment van het maken van het lab inschakelen: 

1. Aanmelden bij de [Azure Lab Services-website](https://labs.azure.com) met de referenties van de **Lab-account eigenaar**
2. Selecteer de standaard installatie kopie van de virtuele machine of de pijl-omlaag. 
3. Selecteer **meer afbeeldings opties inschakelen**. 

    ![Meer afbeeldings opties inschakelen](./media/specify-marketplace-images/enable-more-images-menu.png)
4. Volg de instructies in de vorige sectie om de geselecteerde installatie kopieën in te scha kelen. 
5. Mogelijk moet u het **nieuwe lab** -venster sluiten en opnieuw openen om de afbeeldingen te zien die u in de vorige stap hebt geselecteerd. 



## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Labs maken en beheren als labeigenaar](how-to-manage-classroom-labs.md)
- [Sjablonen instellen en publiceren als labeigenaar](how-to-create-manage-template.md)
- [Het gebruik van een lab configureren en beheren als labeigenaar](how-to-configure-student-usage.md)
- [Als test gebruiker, Access Labs](how-to-use-classroom-lab.md)
