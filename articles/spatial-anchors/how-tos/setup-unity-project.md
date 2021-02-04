---
title: Ruimtelijke Azure-ankers installeren voor Unity
description: Een eenheids project configureren voor het gebruik van Azure-ruimtelijke ankers
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 2/3/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: e058186d8848256bf97d99ee1b8b1ddae7d78383
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550620"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Ruimtelijke Azure-ankers configureren in een Unity-project

In deze hand leiding wordt uitgelegd hoe u aan de slag gaat met de Azure spatiale-ankers SDK in uw Unity-project.

## <a name="requirements"></a>Vereisten

De Azure spatiale ankers ondersteunen eenheid 2019,4 (LTS) met de volgende configuraties.

* Unity 2019,4 met AR Foundation 3,1 wordt ondersteund in de Azure spatiale ankers 2.4.0 +.

## <a name="configuring-a-project"></a>Een project configureren

### <a name="download-packages"></a>Pakketten downloaden
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-packages"></a>Pakketten importeren
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="android-only-configure-the-maintemplategradle-file"></a>Alleen Android: Configureer het bestand mainTemplate. gradle

1. Ga naar **Edit** > **Project Settings** > **Player**.
2. Selecteer in het **deel venster Inspector** voor instellingen van de **speler** het pictogram **Android** .
3. Schakel onder de sectie **bouwen** het selectie vakje **aangepaste hoofd Gradle-sjabloon** in om een aangepaste Gradle-sjabloon te genereren op `Assets\Plugins\Android\mainTemplate.gradle` .
4. Open het `mainTemplate.gradle` bestand in een tekst editor.
5. `dependencies`Plak de volgende afhankelijkheden in de sectie:

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

Als alles klaar is, moet uw `dependencies` sectie er ongeveer als volgt uitzien:

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Procedure: ankers in eenheid maken en vinden](./create-locate-anchors-unity.md)
