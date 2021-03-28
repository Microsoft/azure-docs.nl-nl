---
title: 'Quickstart: Een app voor Xamarin Android maken'
description: In deze quickstart leert u een Android-app maken met Xamarin en met behulp van Spatial Anchors.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 874b59b7439621c9d2777a55065cd769a5434567
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641323"
---
# <a name="quickstart-create-a-xamarin-android-app-with-azure-spatial-anchors"></a>Quickstart: Een Xamarin Android-app maken met Azure Spatial Anchors

In deze quickstart wordt besproken hoe u een Android-app met Xamarin maakt met behulp van [Azure Spatial Anchors](../overview.md). Azure Spatial Anchors is een platformoverstijgende ontwikkelaarsservice waarmee u mixed reality-ervaringen kunt maken met behulp van objecten die hun locatie in de loop van de tijd op meerdere apparaten behouden. Als u klaar bent, hebt u een Android-app gemaakt waarmee een ruimtelijk anker kan worden opgeslagen en teruggehaald.

U leert het volgende:

> [!div class="checklist"]
> * Een Spatial Anchors-account maken
> * Spatial Anchors-account-id en -accountsleutel configureren
> * Implementeren en uitvoeren op een Android-apparaat

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u over het volgende beschikt om deze snelstart te voltooien:
- Een Windows-of macOS-computer:
  - Als u Windows gebruikt:
    - Een up-to-date versie van <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 16.2 +</a>.
    - <a href="https://git-scm.com/download/win" target="_blank">Git voor Windows</a>.
    - <a href="https://git-lfs.github.com/">Git LFS</a>.
  - Als u macOS gebruikt:
    - Een up-to-date versie van <a href="/visualstudio/mac/installation?view=vsmac-2019&preserve-view=true" target="_blank">Visual Studio voor Mac 8.1+</a>.
    - <a href="https://git-scm.com/download/mac" target="_blank">Git voor macOS</a>.
    - <a href="https://git-lfs.github.com/">Git LFS</a>.
- De nieuwste versie van Xamarin. Android is geïnstalleerd en wordt uitgevoerd op het platform van uw keuze. Raadpleeg de handeling [Xamarin.Android installeren](/xamarin/android/get-started/installation/index) voor een handleiding voor het installeren van Xamarin.Android.
- Een <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">door een ontwikkelaar geactiveerd</a> en <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">voor ARCore geschikt</a> Android-apparaat.
  - Er zijn mogelijk extra apparaatstuurprogramma's vereist om uw computer te laten communiceren met uw Android-apparaat. Klik [hier](https://developer.android.com/studio/run/device.html) voor meer informatie.
- Uw app moet gericht zijn op ARCore **1.8**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Voorbeeldproject openen

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Open `Xamarin/SampleXamarin.sln` in Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Account-id en -sleutel configureren

De volgende stap is om de app te configureren om uw account-id en accountsleutel te gebruiken. U hebt ze naar een teksteditor gekopieerd bij het [instellen van de Spatial Anchors-resource](#create-a-spatial-anchors-resource).

Open `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Zoek het veld `SpatialAnchorsAccountKey` en vervang `Set me` met de accountsleutel.

Zoek het veld `SpatialAnchorsAccountId` en vervang `Set me` met de account-id.

Zoek het veld `SpatialAnchorsAccountDomain` en vervang `Set me` door het accountdomein.

## <a name="deploy-the-app-to-your-android-device"></a>De app op uw Android-apparaat implementeren

Start het Android-apparaat, meld u aan en maak verbinding met de computer via een USB-kabel.

Stel het opstartproject in op **SampleXamarin.Android**, wijzig de  **oplossingconfiguratie** naar **Release** en selecteer het apparaat waarop u wilt implementeren in de vervolgkeuzelijst Apparaat selecteren.

# <a name="windows"></a>[Windows](#tab/deploy-windows)

![Schermopname van het menu om het project en apparaat in Windows te selecteren.](./media/get-started-xamarin-android/visual-studio-windows-configuration.png)

Selecteer **Fouten opsporen** > **Fouten opsporen starten** om uw app te implementeren en te starten.

# <a name="macos"></a>[MacOS](#tab/deploy-macos)

![Configuratie van Visual Studio](./media/get-started-xamarin-android/visual-studio-macos-configuration.jpg)

Selecteer **Uitvoeren** > **Starten zonder fout opsporing** om uw app te implementeren en te starten.

---

Selecteer in de app **Basisinstellingen** om de demo uit te voeren en volg de instructies om een bladwijzer te plaatsen en opnieuw aan te roepen.

> ![Schermafbeelding 1](./media/get-started-xamarin-android/screenshot-1.jpg)
> ![Schermafbeelding 2](./media/get-started-xamarin-android/screenshot-2.jpg)
> ![Schermafbeelding 3](./media/get-started-xamarin-android/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Zelfstudie: Spatial Anchors met meerdere apparaten delen](../tutorials/tutorial-share-anchors-across-devices.md)