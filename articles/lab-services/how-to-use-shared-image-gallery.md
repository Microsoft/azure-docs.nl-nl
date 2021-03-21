---
title: Een galerie met gedeelde afbeeldingen gebruiken in Azure Lab Services | Microsoft Docs
description: Informatie over het configureren van een Lab-account voor het gebruik van een galerie met gedeelde afbeeldingen zodat een gebruiker een installatie kopie kan delen met andere en een andere gebruiker kan de installatie kopie gebruiken om een sjabloon-VM in het lab te maken.
ms.topic: article
ms.date: 09/11/2020
ms.openlocfilehash: 984cb4c47099928ffab327895a728cbe8a8f9604
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791387"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Een galerie met gedeelde afbeeldingen gebruiken in Azure Lab Services
In dit artikel wordt uitgelegd hoe docenten/Lab-beheerders een sjabloon installatie kopie van een virtuele machine kunnen opslaan in een [Galerie met gedeelde installatie kopieën](../virtual-machines/shared-image-galleries.md) , zodat deze door anderen kunnen worden gebruikt om Labs te maken. 

> [!IMPORTANT]
> Bij het gebruik van een galerie met gedeelde afbeeldingen ondersteunt Azure Lab Services alleen installatie kopieën met minder dan 128 GB schijf ruimte op het besturings systeem. Installatie kopieën met meer dan 128 GB schijf ruimte of meerdere schijven worden niet weer gegeven in de lijst met installatie kopieën van virtuele machines tijdens het maken van het lab.

## <a name="scenarios"></a>Scenario's
Hier volgen enkele scenario's die door deze functie worden ondersteund: 

- Een Lab-account beheerder koppelt een galerie met gedeelde afbeeldingen aan het lab-account en uploadt een afbeelding naar de galerie met gedeelde afbeeldingen buiten de context van een lab. Vervolgens kunnen Lab-ontwikkel aars die installatie kopie uit de galerie met gedeelde afbeeldingen gebruiken om Labs te maken. 
- Een Lab-account beheerder koppelt een galerie met gedeelde afbeeldingen aan het lab-account. Met een Lab Creator (docent) wordt de aangepaste afbeelding van zijn/haar Lab opgeslagen in de galerie met gedeelde afbeeldingen. Vervolgens kunnen andere Lab-makers deze afbeelding selecteren in de galerie met gedeelde afbeeldingen om een sjabloon voor hun Labs te maken. 

    Wanneer een afbeelding wordt opgeslagen in een galerie met gedeelde afbeeldingen, Azure Lab Services de opgeslagen installatie kopie repliceren naar andere regio's die beschikbaar zijn in dezelfde [geografie](https://azure.microsoft.com/global-infrastructure/geographies/). Het zorgt ervoor dat de installatie kopie beschikbaar is voor Labs die is gemaakt in andere regio's in dezelfde geografie. Het opslaan van afbeeldingen naar een galerie met gedeelde afbeeldingen brengt extra kosten met zich mee voor alle gerepliceerde installatie kopieën. Deze kosten zijn gescheiden van de kosten voor het Azure Lab Services gebruik. Voor meer informatie over de prijzen voor de galerie met gedeelde afbeeldingen raadpleegt u [Galerie met gedeelde afbeeldingen-facturering]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).
    
## <a name="prerequisites"></a>Vereisten
- Maak een galerie met gedeelde installatie kopieën met behulp van [Azure PowerShell](../virtual-machines/shared-images-powershell.md) of [Azure cli](../virtual-machines/shared-images-cli.md).
- U hebt de galerie met gedeelde afbeeldingen aan het lab-account gekoppeld. Zie de [Galerie gedeelde installatie kopieën koppelen of ontkoppelen](how-to-attach-detach-shared-image-gallery.md)voor stapsgewijze instructies.


## <a name="save-an-image-to-the-shared-image-gallery"></a>Een afbeelding opslaan in de galerie met gedeelde afbeeldingen
Nadat een galerie met gedeelde installatie kopieën is gekoppeld, kan een Lab-account beheerder of een docent een installatie kopie opslaan in de galerie met gedeelde afbeeldingen, zodat deze opnieuw kan worden gebruikt door andere docenten. 

1. Op de **sjabloon** pagina voor het lab selecteert **u exporteren naar gedeelde installatie kopie galerie** op de werk balk.

    ![Knop Afbeelding opslaan](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. Voer in het dialoog venster **exporteren naar gedeelde afbeeldingen galerie** een **naam in voor de installatie kopie** en selecteer vervolgens **exporteren**. 

    ![Dialoog venster exporteren naar de galerie gedeelde afbeeldingen](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. U kunt de voortgang van deze bewerking zien op de **sjabloon** pagina. Deze bewerking kan enige tijd duren. 

    ![Exporteren wordt uitgevoerd](./media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Wanneer de export bewerking is voltooid, wordt het volgende bericht weer gegeven:

    ![Exporteren is voltooid](./media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    Nadat u de installatie kopie in de galerie met gedeelde afbeeldingen hebt opgeslagen, kunt u deze installatie kopie uit de galerie gebruiken bij het maken van een ander lab. U kunt ook een installatie kopie uploaden naar de galerie met gedeelde afbeeldingen buiten de context van een lab. Zie overzicht van de [Galerie met gedeelde afbeeldingen](../virtual-machines/shared-images-powershell.md)voor meer informatie. 

    > [!IMPORTANT]
    > Wanneer u [een sjabloon afbeelding van een Lab](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery) in Azure Lab Services opslaat in een galerie met gedeelde afbeeldingen, wordt de afbeelding geüpload naar de galerie als een **gespecialiseerde afbeelding**. [Gespecialiseerde installatie kopieën](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) blijven computerspecifieke informatie en gebruikers profielen. U kunt nog steeds rechtstreeks een gegeneraliseerde installatie kopie uploaden naar de galerie buiten Azure Lab Services.    

## <a name="use-an-image-from-the-shared-image-gallery"></a>Een installatie kopie uit de galerie met gedeelde afbeeldingen gebruiken
Een docent kan een aangepaste installatie kopie kiezen die beschikbaar is in de galerie met gedeelde afbeeldingen voor de sjabloon tijdens het maken van een nieuwe lab.

![Installatie kopie van virtuele machine uit de galerie gebruiken](./media/how-to-use-shared-image-gallery/use-shared-image.png)

> [!NOTE]
> U kunt een sjabloon-VM maken op basis van **gegeneraliseerde** en **gespecialiseerde** installatie kopieën in Azure Lab Services. 


## <a name="next-steps"></a>Volgende stappen
Zie [Galerie met gedeelde afbeeldingen](../virtual-machines/shared-image-galleries.md)voor meer informatie over gedeelde afbeeldings galerieën.