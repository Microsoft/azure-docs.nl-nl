---
author: msftradford
ms.service: spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 9554e04c82299016076f09f85f604af10a94ab4a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185295"
---
## <a name="android"></a>[Android](#tab/Android)

Het Java-voorbeeld voor Android biedt ondersteuning voor het delen van gegevens op meerdere apparaten.

Open in Android Studio het bestand *SharedActivity.java* in de map met voorbeelden. 

Voer de URL in die u in de vorige stap hebt gekopieerd (via uw implementatie van de ASP.NET-web-app in Azure) als de waarde voor `SharingAnchorsServiceUrl` in het bestand *SharedActivity.java*. 

Vervang de `index.html` in de URL door `api/anchors`. Dit ziet er als volgt uit: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Het Objective-C-voorbeeld voor iOS biedt ondersteuning voor het delen van gegevens op meerdere apparaten.

Open het bestand *SharedDemoViewController.m* in de map met voorbeelden. 

Voer de URL in die u in de vorige stap hebt verkregen (via uw implementatie van de ASP.NET-web-app in Azure) als de waarde voor `SharingAnchorsServiceUrl` in het bestand *SharedDemoViewController.m*. 

Vervang de `index.html` in de URL door `api/anchors`. Dit ziet er als volgt uit: `https://<app_name>.azurewebsites.net/api/anchors`.

Implementeer de app op uw apparaat. 

Nadat de app is gestart, selecteert u de optie **Tik om de gedeelde demo te starten** en volgt u vervolgens de instructies in de app. U kunt **Tik selecteren om het ankernummer te vinden** of **Tik om een anker te maken en op te slaan in de service**.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

De Xamarin-voorbeelden voor zowel Android als iOS bieden ondersteuning voor het delen van gegevens op meerdere apparaten.

Open het bestand *AccountDetails.cs* in de map met voorbeelden. 

Voer de URL in die u in de vorige stap hebt verkregen (via uw implementatie van de ASP.NET-web-app in Azure) als de waarde voor `AnchorSharingServiceUrl` in het bestand *AccountDetails.cs*. 

Vervang de `index.html` in de URL door `api/anchors`. Dit ziet er als volgt uit: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Een Android-apparaat instellen

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Een iOS-apparaat instellen

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Ga in het deelvenster **Project** naar `Assets\AzureSpatialAnchors.Examples\Resources`. 

Selecteer **SpatialAnchorSamplesConfig**. Voer vervolgens in het deelvenster **Inspector** de `Sharing Anchors Service` URL (van de implementatie van uw ASP.NET-web-app in Azure) in als de waarde voor `Base Sharing Url`. Vervang `index.html` door `api/anchors`. Dit ziet er als volgt uit: `https://<app_name>.azurewebsites.net/api/anchors`.

Sla de scène op door **Bestand** > **Opslaan** te selecteren.

## <a name="deploy-to-your-device"></a>Uw apparaat implementeren

### <a name="deploy-to-an-android-device"></a>Op een Android-apparaat implementeren

Meld u aan op uw Android-apparaat en maak verbinding met uw computer via een USB-kabel.

Open **Build Settings** door **File** > **Build Settings** te selecteren.

Controleer onder **Scènes in Build** of er naast elke scène een vinkje staat.

Zorg ervoor dat er naast **Project exporteren** geen vinkje staat. Selecteer **Bouwen en uitvoeren**. U wordt gevraagd uw *.apk*-bestand op te slaan. U kunt hiervoor een willekeurige naam kiezen.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Implementeren op een iOS-apparaat

Open **Build Settings** door **File** > **Build Settings** te selecteren.

Controleer onder **Scènes in Build** of er naast elke scène een vinkje staat.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

In Xcode stopt u de app door **Stop** te selecteren.
