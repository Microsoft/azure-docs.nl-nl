---
title: Een Azure DevTest Labs aangepaste installatie kopie maken op basis van een virtuele machine | Microsoft Docs
description: Meer informatie over het maken van een aangepaste installatie kopie in Azure DevTest Labs van een ingerichte VM met behulp van de Azure Portal
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ad45ed6eb7f97e14ec0ca0bb89efb2967c90fc16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87277024"
---
# <a name="create-a-custom-image-from-a-vm"></a>Een aangepaste installatiekopie maken vanaf een virtuele machine

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Stapsgewijze instructies

U kunt een aangepaste installatie kopie maken van een ingerichte virtuele machine en vervolgens die aangepaste installatie kopie gebruiken om identieke Vm's te maken. De volgende stappen laten zien hoe u een aangepaste installatie kopie maakt op basis van een virtuele machine:

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de lijst.

1. Selecteer in de lijst met Labs het gewenste Lab.  

1. Selecteer **mijn virtuele machines** in het hoofd venster van het lab.
 
1. Selecteer in het deel venster **mijn virtuele machines** de virtuele machine waarvan u de aangepaste installatie kopie wilt maken.

1. Selecteer in het deel venster beheer van de VM de optie **aangepaste installatie kopie maken** onder **bewerkingen**.

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image.png" alt-text="Menu-item voor aangepaste installatie kopie maken":::
1. Voer in het deel venster **aangepaste installatie kopie** een naam en een beschrijving in voor uw aangepaste installatie kopie. Deze informatie wordt weer gegeven in de lijst met basissen wanneer u een VM maakt. De aangepaste installatie kopie bevat de besturingssysteem schijf en alle gegevens schijven die aan de virtuele machine zijn gekoppeld.

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image-blade.png" alt-text="Pagina aangepaste installatie kopie maken":::
1. Selecteer of Sysprep is uitgevoerd op de virtuele machine. Als het Sysprep niet is uitgevoerd op de virtuele machine, geeft u op of Sysprep moet worden uitgevoerd op de virtuele machine wanneer de aangepaste installatie kopie wordt gemaakt.
1. Selecteer **OK** wanneer u klaar bent om de aangepaste installatie kopie te maken.

    Na een paar minuten wordt de aangepaste installatie kopie gemaakt en opgeslagen in het opslag account van de test omgeving. Wanneer een Lab-gebruiker een nieuwe virtuele machine wil maken, is de installatie kopie beschikbaar in de lijst met basis installatie kopieën.

    :::image type="content" source="./media/devtest-lab-create-template/custom-image-available-as-base.png" alt-text="aangepaste afbeelding beschikbaar in lijst met basis installatie kopieën":::

## <a name="related-blog-posts"></a>Gerelateerde blog berichten

- [Aangepaste afbeeldingen of formules?](./devtest-lab-faq.md#blog-post)
- [Aangepaste installatie kopieën kopiëren tussen Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Volgende stappen

- [Een virtuele machine toevoegen aan uw Lab](devtest-lab-add-vm.md)
