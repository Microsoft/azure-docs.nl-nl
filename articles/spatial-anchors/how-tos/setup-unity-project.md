---
title: Ruimtelijke Azure-ankers installeren voor Unity
description: Een eenheids project configureren voor het gebruik van Azure-ruimtelijke ankers
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 2330310b0bf4e165af71208477db128650a787cf
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95496937"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Ruimtelijke Azure-ankers configureren in een Unity-project

In deze hand leiding wordt uitgelegd hoe u aan de slag gaat met de Azure spatiale-ankers SDK in uw Unity-project.

## <a name="requirements"></a>Vereisten

De Azure spatiale ankers ondersteunen eenheid 2019,4 (LTS) met de volgende configuraties.

* Unity 2019,4 met AR Foundation 3,1 wordt ondersteund in de Azure spatiale ankers 2.4.0 +.

## <a name="configuring-a-project"></a>Een project configureren

### <a name="add-the-unity-package-manager-packages-to-your-project"></a>[De pakketten unit package manager toevoegen aan uw project](#tab/UPMPackage)

Ruimtelijke-ankers voor Azure voor eenheid worden momenteel gedistribueerd met unit package manager (UPM)-pakketten. Deze pakketten vindt u in het [NPM-REGI ster](https://bintray.com/microsoft/AzureMixedReality-NPM). Zie [de documentatie van](https://docs.unity3d.com/Manual/upm-scoped.html)de officiële eenheid voor meer informatie over het werken met het bereik van pakket registers in een Unity-project.

#### <a name="add-the-registry-to-your-unity-project"></a>Het register toevoegen aan uw Unity-project

1. Ga in een verkenner naar de map `Packages` van uw Unity-project. Open in een teksteditor manifestbestand `manifest.json` van het project.
2. Voeg boven aan het bestand, op hetzelfde niveau als de sectie `dependencies`, de volgende vermelding toe om het register van Azure Spatial Anchors in uw project op te nemen. De vermelding `scopedRegistries` geeft voor Unity aan waar de Azure Spatial Anchors-SDK-pakketten zijn te vinden.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-19&highlight=2-10)]

#### <a name="add-the-sdk-packages-to-your-unity-project"></a>De SDK-pakket (en) toevoegen aan uw Unity-project

| Platform | Naam van het pakket                                    |
|----------|-------------------------------------------------|
| Android  | com. micro soft. Azure. ruimtelijke-ankers-SDK. Android |
| iOS      | com. micro soft. Azure. Spatial-anchors-SDK. IOS     |
| HoloLens | com. micro soft. Azure. ruimtelijke-ankers-SDK. Windows |

1. Voor elk platform (Android/iOS/HoloLens) dat u wilt ondersteunen in uw project, voegt u een vermelding met de pakket naam en pakket versie toe aan de `dependencies` sectie in uw project manifest. Hieronder vindt u een voorbeeld.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-22&highlight=12-14)]

2. Sla het bestand `manifest.json` op en sluit het vervolgens. Wanneer u terugkeert naar Unity, zou Unity de wijziging in het projectmanifest automatisch moeten detecteren en de opgegeven pakketten ophalen. U kunt de map `Packages` in uw projectweergave uitvouwen om te controleren of de juiste pakketten zijn geïmporteerd.

#### <a name="android-only-configure-the-maintemplategradle-file"></a>Alleen Android: Configureer het bestand mainTemplate. gradle

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

### <a name="import-the-asset-package"></a>[Het Asset-pakket importeren](#tab/UnityAssetPackage)

> [!WARNING]
> De verdeling van unit Asset-pakketten van de Azure spatiale ankers SDK is afgeschaft in versie 2.5.0 en is niet meer beschikbaar vanaf 2.6.0.

1. Down load het `AzureSpatialAnchors.unitypackage` bestand voor de versie die u wilt richten op basis van de [github-releases](https://github.com/Azure/azure-spatial-anchors-samples/releases).
2. Volg de instructies [hier](https://docs.unity3d.com/Manual/AssetPackagesImport.html) om het Asset-pakket Unit te importeren in uw project.

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Procedure: ankers in eenheid maken en vinden](./create-locate-anchors-unity.md)
