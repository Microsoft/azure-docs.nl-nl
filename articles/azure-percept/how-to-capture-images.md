---
title: Vastleg installatie kopieën voor een niet-code Vision-oplossing in azure percept Studio
description: Meer informatie over het vastleggen van installatie kopieën met uw Azure percept DK in azure percept Studio voor een niet-code Vision-oplossing
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: d6cece6ee3079ba9f400f40026ca26ea36668710
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024639"
---
# <a name="capture-images-for-a-vision-project-in-azure-percept-studio"></a>Afbeeldingen vastleggen voor een Vision-project in azure percept Studio

Volg deze hand leiding voor het vastleggen van installatie kopieën met behulp van het visioning-SoM van Azure percept DK voor een bestaand gezichts project in azure percept Studio. Als u nog geen Vision-project hebt gemaakt, raadpleegt u de [zelf studie over geen code](./tutorial-nocode-vision.md).

## <a name="prerequisites"></a>Vereisten

- Azure percept DK (Devkit)
- [Azure-abonnement](https://azure.microsoft.com/free/)
- [Setup van Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): u hebt uw Devkit met een Wi-Fi-netwerk verbonden, een IOT hub gemaakt en uw Devkit verbonden met de IOT hub
- [Project zonder code Vision](./tutorial-nocode-vision.md)

## <a name="capture-images"></a>Installatie kopieën vastleggen

1. Schakel uw Devkit uit.

1. Ga naar [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Klik aan de linkerkant van de pagina overzicht op **apparaten**.

    :::image type="content" source="./media/how-to-capture-images/overview-devices-inline.png" alt-text="Overzichts scherm van Azure percept Studio." lightbox="./media/how-to-capture-images/overview-devices.png":::

1. Selecteer uw Devkit in de lijst.

    :::image type="content" source="./media/how-to-capture-images/select-device.png" alt-text="Lijst met percept-apparaten.":::

1. Klik op de pagina apparaat op **installatie kopieën vastleggen voor een project**.

    :::image type="content" source="./media/how-to-capture-images/capture-images.png" alt-text="Pagina percept-apparaten met beschik bare acties weer gegeven.":::

1. Ga als volgt te werk in het venster **installatie kopie vastleggen** :

    1. Selecteer in het vervolg keuzemenu **project** het Vision-project waarvoor u installatie kopieën wilt verzamelen.

    1. Klik op **device stream weer geven** om te controleren of de camera van het visioning-aantal correct is geplaatst.

    1. Klik op **foto maken** om een installatie kopie vast te leggen.

    1. U kunt ook het selectie vakje naast **automatische installatie kopie vastleggen** inschakelen om een timer in te stellen voor het vastleggen van de installatie kopie:

        1. Selecteer uw voor keuren voor de Imaging-frequentie onder **opname frequentie**.
        1. Selecteer het totale aantal installatie kopieën dat u wilt verzamelen onder **doel**.

    :::image type="content" source="./media/how-to-capture-images/take-photo.png" alt-text="Scherm voor het vastleggen van installatie kopieën.":::

Alle installatie kopieën zijn toegankelijk in [Custom Vision](https://www.customvision.ai/).

## <a name="next-steps"></a>Volgende stappen

[Test en Train uw Vision model zonder code](../cognitive-services/custom-vision-service/test-your-model.md).