---
title: Een Azure DevTest Labs aangepaste installatie kopie maken op basis van een VHD-bestand | Microsoft Docs
description: Meer informatie over het maken van een aangepaste installatie kopie in Azure DevTest Labs vanuit een VHD-bestand met behulp van de Azure Portal
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: cac812a9c38fc1dedfd31659a626b122f9527e63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87289403"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Een aangepaste installatie kopie maken van een VHD-bestand

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Stapsgewijze instructies

De volgende stappen helpen u bij het maken van een aangepaste installatie kopie vanuit een VHD-bestand met behulp van de Azure Portal:

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de lijst.

1. Selecteer in de lijst met Labs het gewenste Lab.  

1. Selecteer **configuratie en beleid** in het hoofd venster van het lab. 

1. Selecteer **aangepaste installatie kopieën** in het deel venster **configuratie en beleid** .

1. Selecteer **+ toevoegen** in het deel venster **aangepaste installatie kopieën** .

    ![Aangepaste installatie kopie toevoegen](./media/devtest-lab-create-template/add-custom-image.png)

1. Voer de naam van de aangepaste installatie kopie in. Deze naam wordt weer gegeven in de lijst met basis installatie kopieën bij het maken van een virtuele machine.

1. Voer de beschrijving van de aangepaste installatie kopie in. Deze beschrijving wordt weer gegeven in de lijst met basis installatie kopieën bij het maken van een virtuele machine.

1. Voor **type besturings systeem** selecteert u **Windows** of **Linux**.

    - Als u **Windows** selecteert, geeft u via het selectie vakje op of *Sysprep* is uitgevoerd op de computer. 
    - Als u **Linux** selecteert, geeft u via het selectie vakje op of ongedaan maken van de *inrichting* op de computer is uitgevoerd. 

1. Selecteer een **VHD** in de vervolg keuzelijst. Dit is de VHD die wordt gebruikt om de nieuwe aangepaste installatie kopie te maken. Selecteer, indien nodig, om **een VHD te uploaden met behulp van Power shell**.

1. U kunt ook een plan naam invoeren, een aanbieding plannen en Publisher plannen als de installatie kopie die wordt gebruikt om de aangepaste installatie kopie te maken, geen gelicentieerde installatie kopie is (gepubliceerd door micro soft).

   - **Naam van abonnement:** Voer de naam in van de Marketplace-installatie kopie (SKU) van waaruit deze aangepaste installatie kopie is gemaakt 
   - **Aanbieding plannen:** Voer het product (aanbieding) in van de Marketplace-installatie kopie van waaruit deze aangepaste installatie kopie is gemaakt 
   - **Publisher plannen:** Voer de uitgever in van de Marketplace-installatie kopie van waaruit deze aangepaste installatie kopie is gemaakt

   > [!NOTE]
   > Als de installatie kopie die u gebruikt om een aangepaste installatie kopie te maken, **geen** afbeelding met een licentie is, zijn deze velden leeg en kunnen ze worden ingevuld als u ervoor kiest. Als de afbeelding een gelicentieerde afbeelding **is** , worden de velden automatisch ingevuld met de informatie van het plan. Als u deze in dit geval probeert te wijzigen, wordt er een waarschuwings bericht weer gegeven.
   >
   >

1. Selecteer **OK** om de aangepaste installatie kopie te maken.

Na een paar minuten wordt de aangepaste installatie kopie gemaakt en opgeslagen in het opslag account van de test omgeving. Wanneer een Lab-gebruiker een nieuwe virtuele machine wil maken, is de installatie kopie beschikbaar in de lijst met basis installatie kopieën.

![Aangepaste afbeelding beschikbaar in lijst met basis installatie kopieën](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Gerelateerde blog berichten

- [Aangepaste afbeeldingen of formules?](./devtest-lab-faq.md#blog-post)
- [Aangepaste installatie kopieën kopiëren tussen Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Volgende stappen

- [Een virtuele machine toevoegen aan uw Lab](./devtest-lab-add-vm.md)
