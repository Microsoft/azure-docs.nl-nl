---
title: 'Quickstart: Een iOS-app maken'
description: U gaat leren om programmatisch een iOS-app te maken met behulp van Azure Spatial Anchors in Swift of Objective-C.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.custom: has-adal-ref
ms.openlocfilehash: 4434118313989eb8bc70f1d44e977b2df3633050
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "96009072"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Quickstart: een iOS-app maken met Azure Spatial Anchors, in Swift of Objective-C

In deze quickstart wordt besproken hoe u een iOS-app maakt met behulp van [Azure Spatial Anchors](../overview.md) in Swift of Objective-C. Azure Spatial Anchors is een platformoverstijgende ontwikkelaarsservice waarmee u mixed reality-ervaringen kunt maken met behulp van objecten die hun locatie in de loop van de tijd op meerdere apparaten behouden. Als u klaar bent, hebt u een ARKit iOS-app gemaakt waarmee een ruimtelijk anker kan worden opgeslagen en teruggehaald.

U leert het volgende:

> [!div class="checklist"]
> * Een Spatial Anchors-account maken
> * De Spatial Anchors-account-id en -accountsleutel configureren
> * Implementeren en uitvoeren op een iOS-apparaat

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u over het volgende beschikt om deze snelstart te voltooien:

- Een door de ontwikkelaar ingeschakelde macOS-computer met de nieuwste versie van <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> en <a href="https://cocoapods.org" target="_blank">CocoaPods</a> geïnstalleerd.
- Git geïnstalleerd via HomeBrew:
  1. Voer de volgende opdracht in als één regel in de terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. 
  1. Voer `brew install git` en `brew install git-lfs` uit.
  1. Werk uw Git-configuratie bij met `git lfs install` (voor de huidige gebruiker) of `git lfs install --system` (voor het hele systeem).
- Een door een ontwikkelaar geactiveerd en <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">met ARKit compatibel</a> iOS-apparaat.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Voorbeeldproject openen

Gebruik de Terminal om de volgende acties uit te voeren.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

De benodigde pods installeren met CocoaPods:

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Navigeer naar `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Navigeer naar `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

---

Voer `pod install --repo-update` uit om de CocoaPods voor het project te installeren.

Open nu `.xcworkspace` in Xcode.

> [!NOTE]
> Raadpleeg [deze](#cocoapods-issues-on-macos-catalina-1015) stappen voor het oplossen van problemen als u CocoaPod-problemen ondervindt na de upgrade naar macOS Catalina (10.15).

# <a name="swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Account-id en -sleutel configureren

De volgende stap is om de app te configureren om uw account-id en accountsleutel te gebruiken. U hebt ze naar een teksteditor gekopieerd bij het [instellen van de Spatial Anchors-resource](#create-a-spatial-anchors-resource).

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Open `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Zoek het veld `spatialAnchorsAccountKey` en vervang `Set me` met de accountsleutel.

Zoek het veld `spatialAnchorsAccountId` en vervang `Set me` met de account-id.

Zoek het veld `spatialAnchorsAccountDomain` en vervang `Set me` door het accountdomein.

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Open `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Zoek het veld `SpatialAnchorsAccountKey` en vervang `Set me` met de accountsleutel.

Zoek het veld `SpatialAnchorsAccountId` en vervang `Set me` met de account-id.

Zoek het veld `SpatialAnchorsAccountDomain` en vervang `Set me` door het accountdomein.

---

## <a name="deploy-the-app-to-your-ios-device"></a>De app implementeren op uw iOS-apparaat

Verbind het iOS-apparaat met de Mac en stel het **actieve schema** in voor uw iOS-apparaat.

![Apparaat selecteren](./media/get-started-ios/select-device.png)

Selecteer **Build and then run the current scheme** (Het huidige schema compileren en uitvoeren).

![Implementeren en uitvoeren](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Als u de fout `library not found for -lPods-SampleObjC` ziet, hebt u waarschijnlijk het bestand `.xcodeproj` geopend in plaats van `.xcworkspace`. Open `.xcworkspace` en probeer het opnieuw.

In Xcode stopt u de app door op **Stop** te klikken.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>CocoaPods-problemen op macOS Catalina (10.15)

Als u onlangs een update hebt uitgevoerd naar macOS Catalina (10.15) en CocoaPods al eerder hebt geïnstalleerd, kan het zijn dat CocoaPods een defecte status heeft en uw pods en `.xcworkspace`-projectbestanden niet goed kan configureren. U kunt dit probleem oplossen door CocoaPods opnieuw te installeren met behulp van de volgende opdrachten:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="app-crashes-when-deploying-to-ios-1031-from-a-personal-provisioning-profiledeveloper-account"></a>App loopt vast bij het implementeren van iOS 10.3.1 vanuit een persoonlijk inrichtingsprofiel/ontwikkelaarsaccount 

Als u uw iOS-app op iOS 10.3.1 implementeert vanuit een persoonlijk inrichtingsprofiel/ontwikkelaarsaccount, ziet u deze fout mogelijk: `Library not loaded: @rpath/ADAL...`. 

Het probleem oplossen:

- Gebruik een inrichtingsprofiel dat geen persoonlijk teamprofiel is (betaald ontwikkelaarsaccount).
- Implementeer uw app op een iOS-apparaat met iOS 13.3 of eerder of op een van de iOS 13.4 bèta- of releaseversie.
- Meer informatie over dit probleem vindt u op [Stack Overflow](https://stackoverflow.com/questions/60015309/running-ios-apps-causes-runtime-error-for-frameworks-code-signature-invalid).


[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Zelfstudie: Spatial Anchors met meerdere apparaten delen](../tutorials/tutorial-share-anchors-across-devices.md)
