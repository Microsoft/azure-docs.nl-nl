---
title: Een galerie met gedeelde afbeeldingen koppelen of ontkoppelen in Azure Lab Services | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een galerie met gedeelde afbeeldingen koppelt aan een leslokaal Lab in Azure Lab Services.
ms.topic: article
ms.date: 09/11/2020
ms.openlocfilehash: 99b944ad85e311cd66b0a1cec4e585de37f83500
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786398"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Een galerie met gedeelde afbeeldingen koppelen of ontkoppelen in Azure Lab Services
In dit artikel wordt beschreven hoe u een galerie met gedeelde afbeeldingen koppelt aan of koppelt aan een Lab-account. 

> [!NOTE]
> Wanneer u [een sjabloon afbeelding van een Lab](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery) in Azure Lab Services opslaat in een galerie met gedeelde afbeeldingen, wordt de afbeelding geüpload naar de galerie als een gespecialiseerde afbeelding. [Gespecialiseerde installatie kopieën](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) blijven computerspecifieke informatie en gebruikers profielen. U kunt nog steeds rechtstreeks een gegeneraliseerde installatie kopie uploaden naar de galerie buiten Azure Lab Services. 
>
> Een Lab-Maker kan een sjabloon-VM maken op basis van gegeneraliseerde en gespecialiseerde installatie kopieën in Azure Lab Services. 

## <a name="scenarios"></a>Scenario's
Hier volgen enkele scenario's die door deze functie worden ondersteund: 

- Een Lab-account beheerder koppelt een galerie met gedeelde afbeeldingen aan het lab-account en uploadt een afbeelding naar de galerie met gedeelde afbeeldingen buiten de context van een lab. Vervolgens kunnen Lab-ontwikkel aars die installatie kopie uit de galerie met gedeelde afbeeldingen gebruiken om Labs te maken. 
- Een Lab-account beheerder koppelt een galerie met gedeelde afbeeldingen aan het lab-account. Met een Lab Creator (docent) wordt de aangepaste afbeelding van zijn/haar Lab opgeslagen in de galerie met gedeelde afbeeldingen. Vervolgens kunnen andere Lab-makers deze afbeelding selecteren in de galerie met gedeelde afbeeldingen om een sjabloon voor hun Labs te maken. 

    Wanneer een afbeelding wordt opgeslagen in een galerie met gedeelde afbeeldingen, Azure Lab Services de opgeslagen installatie kopie repliceren naar andere regio's die beschikbaar zijn in dezelfde [geografie](https://azure.microsoft.com/global-infrastructure/geographies/). Het zorgt ervoor dat de installatie kopie beschikbaar is voor Labs die is gemaakt in andere regio's in dezelfde geografie. Het opslaan van afbeeldingen naar een galerie met gedeelde afbeeldingen brengt extra kosten met zich mee voor alle gerepliceerde installatie kopieën. Deze kosten zijn gescheiden van de kosten voor het Azure Lab Services gebruik. Voor meer informatie over de prijzen voor de galerie met gedeelde afbeeldingen raadpleegt u [Galerie met gedeelde afbeeldingen-facturering](../virtual-machines/shared-image-galleries.md#billing).

> [!IMPORTANT]
> Bij het gebruik van een galerie met gedeelde afbeeldingen ondersteunt Azure Lab Services alleen installatie kopieën met minder dan 128 GB schijf ruimte op het besturings systeem. Installatie kopieën met meer dan 128 GB schijf ruimte of meerdere schijven worden niet weer gegeven in de lijst met installatie kopieën van virtuele machines tijdens het maken van het lab.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configureren op het moment dat het lab-account wordt gemaakt
Wanneer u een Lab-account maakt, kunt u een galerie met gedeelde afbeeldingen koppelen aan het lab-account. U kunt een bestaande galerie met gedeelde afbeeldingen selecteren in de vervolg keuzelijst of een nieuwe maken. Als u een galerie met gedeelde afbeeldingen wilt maken en koppelen aan het lab-account, selecteert u **Nieuw maken**, voert u een naam in voor de galerie en voert u **OK** in. 

![Galerie met gedeelde afbeeldingen configureren op het moment dat het account wordt gemaakt](./media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Configureren nadat het lab-account is gemaakt
Nadat het lab-account is gemaakt, kunt u de volgende taken uitvoeren:

- Een galerie met gedeelde afbeeldingen maken en koppelen
- Een galerie met gedeelde afbeeldingen koppelen aan het lab-account
- Een galerie met gedeelde afbeeldingen loskoppelen van het lab-account

## <a name="create-and-attach-a-shared-image-gallery"></a>Een galerie met gedeelde afbeeldingen maken en koppelen
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer in het menu links **Alle services**. Selecteer **Lab Services** in de sectie **DEVOPS** . Selecteer de ster (`*`) naast de **Lab Services** om er een toe te voegen aan de sectie **FAVORIETEN** in het menu links. Vanaf de volgende keer selecteert u **Lab Services** onder **FAVORIETEN**.

    ![Alle Services -> Lab Services](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selecteer uw Lab-account om de pagina **Lab-account** weer te geven. 
4. Selecteer in het menu links **gedeelde installatie kopie galerie** en selecteer **+ maken** op de werk balk.  

    ![Knop gedeelde installatie kopie galerie maken](./media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. Voer in het venster **gedeelde installatie kopie galerie maken** een **naam** in voor de galerie en voer **OK** in. 

    ![Venster gedeelde installatie kopie galerie maken](./media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services maakt de galerie met gedeelde afbeeldingen en koppelt deze aan het lab-account. Alle Labs die in dit lab-account is gemaakt, hebben toegang tot de gekoppelde gedeelde afbeeldingen galerie. 

    ![Galerie met bijgevoegde afbeeldingen](./media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    In het onderste deel venster ziet u afbeeldingen in de galerie met gedeelde afbeeldingen. In deze nieuwe galerie zijn er geen installatie kopieën. Wanneer u afbeeldingen naar de galerie uploadt, worden deze op deze pagina weer geven.     

    Alle installatie kopieën in de gekoppelde gedeelde installatie kopie galerie zijn standaard ingeschakeld. U kunt geselecteerde installatie kopieën in-of uitschakelen door ze te selecteren in de lijst en de knop geselecteerde installatie kopieën **inschakelen** of **geselecteerde afbeeldingen uitschakelen** te gebruiken.

## <a name="attach-an-existing-shared-image-gallery"></a>Een bestaande galerie met gedeelde afbeeldingen koppelen
De volgende procedure laat zien hoe u een bestaande galerie met gedeelde afbeeldingen koppelt aan een Lab-account. 

1. Selecteer op de pagina **Lab-account** de optie **Galerie met gedeelde afbeeldingen** in het linkermenu en selecteer vervolgens **bijvoegen** op de werk balk. 

    ![Galerie gedeelde afbeeldingen-knop toevoegen](./media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Selecteer op de pagina **een bestaande gedeelde installatie kopie galerie koppelen** de galerie met gedeelde afbeeldingen en selecteer **OK**.

    ![Een bestaande galerie selecteren](./media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Het volgende scherm wordt weer gegeven: 

    ![Mijn galerie in de lijst](./media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    In dit voor beeld zijn er nog geen installatie kopieën in de galerie met gedeelde afbeeldingen.

    Azure Lab Services identiteit wordt toegevoegd als een bijdrager aan de galerie met gedeelde afbeeldingen die is gekoppeld aan het lab. Hiermee kunnen docenten/IT-beheerders installatie kopieën van virtuele machines opslaan in de galerie met gedeelde installatie kopieën. Alle Labs die in dit lab-account is gemaakt, hebben toegang tot de gekoppelde gedeelde afbeeldingen galerie. 

    Alle installatie kopieën in de gekoppelde gedeelde installatie kopie galerie zijn standaard ingeschakeld. U kunt geselecteerde installatie kopieën in-of uitschakelen door ze te selecteren in de lijst en de knop geselecteerde installatie kopieën **inschakelen** of **geselecteerde afbeeldingen uitschakelen** te gebruiken. 

## <a name="detach-a-shared-image-gallery"></a>Een galerie met gedeelde afbeeldingen loskoppelen
Er kan slechts één galerie met gedeelde afbeeldingen worden gekoppeld aan een lab. Als u een andere galerie met gedeelde afbeeldingen wilt koppelen, moet u het huidige loskoppelen voordat u het nieuwe toevoegt. Als u een galerie met gedeelde installatie kopieën wilt loskoppelen van uw Lab, selecteert u **loskoppelen** op de werk balk en bevestigt u de koppelings bewerking. 

![De galerie met gedeelde afbeeldingen loskoppelen van het lab-account](./media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Volgende stappen
Zie de [Galerie met gedeelde afbeeldingen gebruiken](how-to-use-shared-image-gallery.md)voor meer informatie over het opslaan van een Lab-afbeelding in de galerie met gedeelde afbeeldingen of het gebruik van een afbeelding uit de galerie met gedeelde afbeeldingen om een virtuele machine te maken.

Zie [Galerie met gedeelde afbeeldingen](../virtual-machines/shared-image-galleries.md)voor meer informatie over gedeelde afbeeldings galerieën in het algemeen.