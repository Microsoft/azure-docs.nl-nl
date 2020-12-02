---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 6d20df031633df4642ce9fb5cbbc469fd7f0a5da
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188325"
---
Als u in Xamarin een Visual Studio-project wilt maken voor platformoverschrijdende mobiele appontwikkeling met .NET, moet u het volgende doen:
- Ontwikkelopties instellen in Visual Studio.
- Het project maken en de doelarchitectuur selecteren. 
- De Speech-SDK installeren.

### <a name="set-up-visual-studio-development-options"></a>Ontwikkelopties instellen in Visual Studio

Zorg er eerst voor dat Visual Studio correct is ingesteld voor platformoverschrijdende mobiele ontwikkeling met .NET:

1. Open Visual Studio 2019.

1. Selecteer in de menubalk van Visual Studio **Hulpprogramma's** > **Hulpprogramma's en functies ophalen** om Visual Studio Installer te openen en het dialoogvenster **Wijzigen** te bekijken.

   ![Schermopname van het tabblad Werkbelastingen, het dialoogvenster Wijzigen en de Visual Studio Installer](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. Zoek op het tabblad **Werkbelastingen** onder **Windows** de werkbelasting **Mobiele ontwikkeling met .NET**. Als het selectievakje naast deze werkbelasting al is geselecteerd, sluit u het dialoogvenster **Wijzigen** en gaat u naar stap 5.

1. Schakel het selectievakje **Mobiele ontwikkeling met .NET** in en selecteer **Wijzigen**. In het dialoogvenster **Voordat we beginnen** selecteert u **Doorgaan** om de werkbelasting Mobiele ontwikkeling met .NET te installeren. Het installeren van de nieuwe functie kan even duren.

1. Sluit Visual Studio Installer.

### <a name="create-the-project"></a>Het project maken

1. In de menubalk van Visual Studio selecteert u **Bestand** > **Nieuw** > **Project** om het venster **Een nieuw project maken** weer te geven.

   ![Schermopname die laat zien hoe u een nieuw project maakt in Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Zoek en selecteer **Mobiele app (Xamarin.Forms)** .

1. Selecteer **Volgende** om het scherm **Uw nieuwe project configureren** weer te geven.

   ![Schermopname die laat zien hoe u uw nieuwe project in Visual Studio kunt configureren.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. Bij **Projectnaam** voert u *helloworld* in.

1. Ga in **Locatie** naar de map waarin u uw project wilt opslaan en selecteer deze, of maak deze map.

1. Selecteer **Maken** om naar het venster **Nieuw project voor mobiele app Xamarin.Forms** te gaan.

   ![Schermopname van het dialoogvenster Nieuw project voor mobiele app Xamarin.Forms in Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Selecteer de sjabloon **Leeg**.

1. Vink in **Platform** de selectievakjes voor **Android**, **iOS** en **Windows (UWP)** aan.

1. Selecteer **OK**. U keert terug naar de Visual Studio0IDE, met het nieuwe project gemaakt en zichtbaar in het deelvenster **Solution Explorer** (Oplossingsverkenner).

   ![Het helloworld-project: Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Selecteer nu de doelplatformarchitectuur en het opstartproject. Ga op de werkbalk van Visual Studio naar de vervolgkeuzelijst **Oplossingsplatformen**. (Als u deze niet ziet, selecteert u **Beeld** > **Werkbalken** > **Standaard** om de werkbalk met **Oplossingsplatformen** weer te geven.) Als u een 64-bits versie van Windows gebruikt, selecteert u **x64** in de vervolgkeuzelijst. Als u wilt, kunt u **x86-** selecteren, omdat 64-bits Windows ook 32-bits toepassingen kan uitvoeren. In de vervolgkeuzelijst **Opstartprojecten** stelt u **helloworld.UWP (Universal Windows)** in.

### <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Installeer het [NuGet-pakket van de Speech-SDK](https://aka.ms/csspeech/nuget) en verwijs in uw project naar de Speech-SDK.

1. Klik in **Solution Explorer** met de rechtermuisknop op uw oplossing. Selecteer **NuGet-pakketten beheren voor Solution** om naar het venster **NuGet - Solution** te gaan.

1. Selecteer **Bladeren**.

   ![Schermopname van het dialoogvenster Pakketten beheren voor Solution tijdens het installeren van de Speech-SDK.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. In **Pakketbron** selecteert u nuget.org.

1. In het vak **Zoeken** voert u *Microsoft.CognitiveServices.Speech* in. Selecteer dat pakket nadat het in de zoekresultaten wordt weergegeven.

   ![Schermopname van het pakket Microsoft.CognitiveServices.Speech.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)

   > [!NOTE] 
   > De iOS-bibliotheek in NuGet `Microsoft.CognitiveServices.Speech` heeft geen ingeschakelde bitcode. Als de bitcodebibliotheek van uw toepassing ingeschakeld moet zijn, gebruikt u specifiek voor het iOS-project NuGet `Microsoft.CognitiveServices.Speech.Xamarin.iOS`.

1. In het deelvenster Pakketstatus selecteert u naast de zoekresultaten alle projecten: **helloworld**, **helloworld.Android**, **helloworld.iOS** en **helloworld.UWP**.

1. Selecteer **Installeren**.

1. Selecteer **OK** in het dialoogvenster **Voorbeeld van wijzigingen bekijken**.

1. Lees de licentie in het dialoogvenster **Acceptatie van licentie** en selecteer **Ik ga akkoord**. Installeer de pakketverwijzing Speech-SDK voor alle projecten. Nadat de installatie is voltooid, ziet u mogelijk de volgende waarschuwing voor helloworld.iOS. Dit is een bekend probleem en zou geen invloed moeten hebben op de functionaliteit van uw app.

   > Kon de referentie 'C:\Users\Default\.nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a' niet oplossen. Als deze verwijzing vereist is in de code, kunnen er compilatiefouten optreden.
