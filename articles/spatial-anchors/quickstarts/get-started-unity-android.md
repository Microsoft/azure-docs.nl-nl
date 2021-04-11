---
title: 'Quickstart: Een Unity Android-app maken'
description: In deze quickstart leert u een Android-app maken met Unity en met behulp van Spatial Anchors.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/18/2021
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4f4bfdb5d09da6a9967b53ca56bb2491976592a1
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105881"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Quickstart: Een Unity Android-app maken met Azure Spatial Anchors

In deze quickstart wordt besproken hoe u een Unity Android-app maakt met behulp van [Azure Spatial Anchors](../overview.md). Azure Spatial Anchors is een platformoverstijgende ontwikkelaarsservice waarmee u mixed reality-ervaringen kunt maken met behulp van objecten die hun locatie in de loop van de tijd op meerdere apparaten behouden. Als u klaar bent, hebt u een ARCore Android-app met Unity gemaakt waarmee een ruimtelijk anker kan worden opgeslagen en teruggehaald.

U leert het volgende:

> [!div class="checklist"]
> * Een Spatial Anchors-account maken
> * Build-instellingen voor Unity voorbereiden
> * De Spatial Anchors-account-id en -accountsleutel configureren
> * Het Android Studio-project exporteren
> * Implementeren en uitvoeren op een Android-apparaat

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u over het volgende beschikt om deze snelstart te voltooien:

- Een Windows-of macOS-computer met <a href="https://unity3d.com/get-unity/download" target="_blank">Unity (LTS)</a>, met inbegrip van de **Android-build-ondersteuning** met **Android SDK & ndk-hulpprogram ma's** en **openjdk** -modules. Gebruik **unit 2020 LTS** met ASA SDK-versie 2,9 of hoger (die gebruikmaakt [van het unit XR-invoeg toepassings raamwerk](https://docs.unity3d.com/Manual/XRPluginArchitecture.html)) of **Unit 2019 LTS** met ASA SDK-versie 2,8 of eerder.
  - Als u werkt met Windows, hebt u ook <a href="https://git-scm.com/download/win" target="_blank">Git voor Windows</a> en <a href="https://git-lfs.github.com/">Git LFS</a> nodig.
  - Als u werkt met macOS, kunt u Git downloaden via HomeBrew. Voer de volgende opdracht in op één regel van de terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Voer vervolgens `brew install git` en `brew install git-lfs` uit.
- Een <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">door een ontwikkelaar geactiveerd</a> en <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">voor ARCore geschikt</a> Android-apparaat.
  - Er zijn mogelijk extra apparaatstuurprogramma's vereist om uw computer te laten communiceren met uw Android-apparaat. Kijk [hier](https://developer.android.com/studio/run/device.html) voor meer informatie en instructies.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Het Unity-voorbeeldproject downloaden en openen

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Volg de instructies [hier](../how-tos/setup-unity-project.md#download-asa-packages) om de ASA SDK-pakketten te downloaden en te importeren die vereist zijn voor het Android-platform.

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-android-studio-project"></a>Exporteer het Android Studio-project

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Selecteer uw apparaat in **Apparaat uitvoeren** en selecteer vervolgens **Compileren en uitvoeren**. U wordt gevraagd een `.apk`-bestand op te slaan, dat u een willekeurige naam kunt geven.

Selecteer in de app **BasicDemo** met behulp van de pijlen en selecteer vervolgens de knop **Go!** om de demo uit te voeren. Volg de instructies om een anker te plaatsen en terug te halen.

![Schermafbeelding 1](./media/get-started-unity-android/screenshot-1.jpg)
![Schermafbeelding 2](./media/get-started-unity-android/screenshot-2.jpg)
![Schermafbeelding 3](./media/get-started-unity-android/screenshot-3.jpg)

Volg de instructies in de app om een anker te plaatsen en terug te halen.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="rendering-issues"></a>Problemen met de weergave

Als u bij het uitvoeren van de app de camera niet als achtergrond ziet (u ziet bijvoorbeeld lege, blauwe of andere structuur) dan moet u waarschijnlijk assets opnieuw in Unity importeren. De app stoppen. Kies in het bovenste menu in Unity **Assets -> Alles opnieuw importeren**. Voer de vervolgens opnieuw app uit.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Zelfstudie: Spatial Anchors met meerdere apparaten delen](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Procedure: Azure Spatial Anchors configureren in een Unity-project](../how-tos/setup-unity-project.md)
