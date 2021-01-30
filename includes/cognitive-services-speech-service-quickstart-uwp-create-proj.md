---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 7386cfaaf410614c6eeee19669f8f769ad691c1f
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99215000"
---
Als u een Visual Studio-project voor UWP-ontwikkeling (Universeel Windows-platform) wilt maken, moet u ontwikkelingsopties voor Visual Studio instellen, het project maken, de doelarchitectuur selecteren, audio-opname instellen en de Speech-SDK installeren.

### <a name="set-up-visual-studio-development-options"></a>Ontwikkelopties instellen in Visual Studio

Zorg er eerst voor dat Visual Studio correct is ingesteld voor UWP-ontwikkeling:

1. Open Visual Studio 2019 om het **Start**-venster weer te geven.

   ![Schermopname van het venster Start, met de actie Doorgaan zonder code gemarkeerd.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. Selecteer **Doorgaan zonder code** om naar de Visual Studio-IDE te gaan.

1. Selecteer in de menubalk van Visual Studio **Tools** (Extra) > **Get Tools and Features** (Hulpprogramma's en functies ophalen) om Visual Studio Installer te openen en het dialoogvenster **Modifying** (Wijzigen) te bekijken.

   ![Schermopname van het tabblad Workloads, in het dialoogvenster Wijzigen, met Universal Windows Platform-ontwikkeling gemarkeerd.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. Ga op het tabblad **Workloads** (Werkbelastingen) onder **Windows** naar de werkbelasting **Universal Windows Platform** (Universeel Windows-platform). Als het selectievakje naast deze werkbelasting al is geselecteerd, sluit u het dialoogvenster **Modifying** (Wijzigen) en gaat u naar stap 6.

1. Schakel het selectievakje **Universal Windows Platform development** (Ontwikkelen voor Universeel Windows-platform) in, selecteer **Modify** (Wijzigen), en selecteer vervolgens in het dialoogvenster **Before we get started** (Voordat we beginnen) **Continue** (Doorgaan) om de UWP-ontwikkelingswerkbelasting te installeren. Het installeren van de nieuwe functie kan even duren.

1. Sluit Visual Studio Installer.

### <a name="create-the-project-and-select-the-target-architecture"></a>Het project maken en de doelarchitectuur selecteren

Maak vervolgens uw project:

1. Kies in de menubalk van Visual Studio **File** (Bestand) > **New** (Nieuw) > **Project** om het venster **Create a new project** (Een nieuw project maken) te openen.

   ![Schermopname van het venster Een nieuw project maken, met Lege app (Universal Windows) geselecteerd, en de knop Volgende gemarkeerd.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. Zoek en selecteer **Blank App (Universal Windows)** (Lege app (Universal Windows)). Zorg ervoor dat u de C#-versie van dit projecttype selecteert (in plaats van Visual Basic).

1. Selecteer **Next** (Volgende) om het scherm **Configure your new project** (Uw nieuwe project configureren) weer te geven.

   ![Schermopname van het venster Uw nieuwe project configureren, met de velden Projectnaam en Locatie, en de knop Maken gemarkeerd.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. Voer bij **Project name** (Projectnaam) `helloworld` in.

1. Navigeer in **Location** (Locatie) naar de map waarin u uw project wilt opslaan en selecteer deze, of maak deze map.

1. Selecteer **Create** (Maken) om naar het venster **New Universal Windows Platform Project** (Nieuw Universeel Windows-platformproject) te gaan.

   ![Schermopname van het dialoogvenster Nieuw Universal Windows Platform-project.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Kies in de tweede vervolgkeuzelijst, **Minimum version** (Minimale versie), **Windows 10 Fall Creators Update (10.0; Build 16299)** . Dit is de minimale vereiste voor de Speech-SDK.

1. Kies in de eerste vervolgkeuzelijst, **Target version** (Doelversie), een waarde die gelijk is aan of hoger is dan de waarde in **Minimum version**.

1. Selecteer **OK**. U keert terug naar de Visual Studio0IDE, met het nieuwe project gemaakt en zichtbaar in het deelvenster **Solution Explorer** (Oplossingsverkenner).

   ![helloworld-project: Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

Selecteer nu de doelplatformarchitectuur. Ga op de werkbalk van Visual Studio naar de vervolgkeuzelijst **Solution Platforms** (Oplossingsplatforms). (Als u deze niet ziet, kiest u **View** (Beeld) > **Toolbars** (Werkbalken) > **Standard** (Standaard) om de werkbalk met **Solution Platforms** weer te geven.) Als u een 64-bits versie van Windows gebruikt, kiest u **x64** in de vervolgkeuzelijst. Met 64-bits Windows kunt u ook 32-bits toepassingen uitvoeren, dus u kunt **x86** kiezen als u dat liever hebt.

> [!NOTE]
> De Speech-SDK ondersteunt alle Intel-compatibele processors, maar **alleen x64**-versies van ARM-processors.

### <a name="set-up-audio-capture"></a>Audio-opname instellen

Mogelijk maken dat het project audio-invoer vastlegt:

1. Dubbelklik in **Solution Explorer** op **Package.appxmanifest** om het toepassingsmanifest van het pakket te bewerken.

1. Selecteer het tabblad **Capabilities** (Mogelijkheden).

   ![Tabblad Capabilities, pakkettoepassingsmanifest: Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Schakel het selectievakje in voor de mogelijkheid **Microphone** (Microfoon).

1. Kies in de menubalk **File** (Bestand) > **Save Package.appxmanifest** (Package.appxmanifest opslaan) om uw wijzigingen op te slaan.

### <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Installeer tot slot het [Speech SDK NuGet package](https://aka.ms/csspeech/nuget) en verwijs in uw project naar de Speech-SDK:

1. Klik in **Solution Explorer** met de rechtermuisknop op de oplossing en kies **Manage NuGet Packages for Solution** (NuGet-pakketten voor oplossing beheren) om naar het venster **NuGet - Solution** te gaan.

1. Selecteer **Bladeren**.

   ![Schermopname van het dialoogvenster Pakketten voor oplossing beheren, met het tabblad Bladeren, het vak Zoeken, en de optie Pakketbron gemarkeerd.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Kies **nuget.org** in **Package source** (Pakketbron).

1. Voer in het vak **Search** (Zoeken) `Microsoft.CognitiveServices.Speech` in, en kies dat pakket wanneer het in de zoekresultaten wordt weergegeven.

   ![Schermopname met Microsoft.CognitiveServices.Speech geselecteerd, met het project en de knop Installeren gemarkeerd.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. Selecteer in het deelvenster met de pakketstatus naast de zoekresultaten uw **helloworld**-project.

1. Selecteer **Installeren**.

1. Selecteer **OK** in het dialoog venster **Preview Changes** (Voorbeeld van wijzigingen bekijken).

1. Lees de licentie in het dialoogvenster **License Acceptance** (Acceptatie van licentie) en selecteer **I Accept** (Ik ga akkoord). De installatie van het pakket wordt gestart, en wanneer de installatie is voltooid, wordt in het deelvenster **Output** (Uitvoer) een bericht weergegeven met een bericht als `Successfully installed 'Microsoft.CognitiveServices.Speech 1.15.0' to helloworld`.
