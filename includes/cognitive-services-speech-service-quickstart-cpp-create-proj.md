---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 967861363d5056a5a466aa88840cda2aafbeb86b
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188315"
---
Als u een Visual Studio-project voor C++-desktopontwikkeling wilt maken, moet u ontwikkelingsopties voor Visual Studio instellen, het project maken, de doelarchitectuur selecteren en de Speech SDK installeren.

### <a name="set-up-visual-studio-development-options"></a>Ontwikkelopties instellen in Visual Studio

Zorg er eerst voor dat Visual Studio correct is ingesteld voor C++-desktopontwikkeling:

1. Open Visual Studio 2019 om het **Start**-venster weer te geven.

   ![Schermopname van het Start-venster in Visual Studio 2019.](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png)

1. Selecteer **Doorgaan zonder code** om naar de Visual Studio-IDE te gaan.

1. Selecteer in de menubalk van Visual Studio **Hulpprogramma's** > **Hulpprogramma's en functies ophalen** om Visual Studio Installer te openen en het dialoogvenster **Wijzigen** te bekijken.

   ![Schermopname van het tabblad Werkbelastingen van het dialoogvenster Wijzigen voor de Visual Studio Installer.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. Zoek op het tabblad **Werkbelastingen** onder **Windows** de werkbelasting **Desktopontwikkeling met C++** . Selecteer het selectievakje naast die werkbelasting als dat nog niet is geselecteerd.

1. Zoek op het tabblad **Afzonderlijke onderdelen** het selectievakje **NuGet-pakketbeheer**. Selecteer het selectievakje als dat nog niet is geselecteerd.

1. Selecteer de knop in de hoek met de melding **Sluiten** of **Wijzigen**. (De knopnaam is afhankelijk van de vraag of u functies voor installatie hebt geselecteerd.) Als u **Wijzigen** selecteert, wordt de installatie gestart. Dit kan enige tijd duren.

1. Sluit Visual Studio Installer.

### <a name="create-the-project-and-select-the-target-architecture"></a>Het project maken en de doelarchitectuur selecteren

Maak vervolgens uw project:

1. Kies in de menubalk van Visual Studio **Bestand** > **Nieuw** > **Project** om het venster **Een nieuw project maken** weer te geven.

   ![Een nieuw project maken, C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Zoek en selecteer **Console-app**. Zorg ervoor dat u de C++-versie van dit projecttype selecteert (in plaats van C# of Visual Basic).

1. Selecteer **Volgende** om het scherm **Uw nieuwe project configureren** weer te geven.

   ![Uw nieuwe project configureren, C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. Voer bij **Projectnaam** `helloworld` in.

1. Navigeer in **Locatie** naar de map waarin u uw project wilt opslaan en selecteer deze, of maak deze map.

Selecteer nu de doelplatformarchitectuur. Ga op de werkbalk van Visual Studio naar de vervolgkeuzelijst **Oplossingsplatformen**. (Als u deze niet ziet, kiest u **Beeld** > **Werkbalken** > **Standaard** om de werkbalk met **Oplossingsplatformen** weer te geven.) Als u een 64-bits versie van Windows gebruikt, kiest u **x64** in de vervolgkeuzelijst. Met 64-bits Windows kunt u ook 32-bits toepassingen uitvoeren, dus u kunt **x86** kiezen als u dat liever hebt.

### <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Installeer tot slot het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget) en verwijs in uw project naar de Speech SDK:

1. Klik in **Solution Explorer** met de rechtermuisknop op de oplossing en kies **NuGet-pakketten voor oplossing beheren** om naar het venster **NuGet - Oplossing** te gaan.

1. Selecteer **Bladeren**.

   ![NuGet - Het tabblad Oplossing, Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. Kies **nuget.org** in **Pakketbron**.

1. Voer in het vak **Zoeken** `Microsoft.CognitiveServices.Speech` in, en kies dat pakket wanneer het in de zoekresultaten wordt weergegeven.

   ![Het Microsoft.CognitiveServices.Speech C++-pakket installeren - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. Selecteer in het deelvenster met de pakketstatus naast de zoekresultaten uw **helloworld**-project.

1. Selecteer **Installeren**.

1. Selecteer **OK** in het dialoogvenster **Voorbeeld van wijzigingen bekijken**.

1. Lees de licentie in het dialoogvenster **Acceptatie van licentie** en selecteer **Ik ga akkoord**. De installatie van het pakket wordt gestart, en wanneer de installatie is voltooid, wordt in het deelvenster **Uitvoer** een bericht weergegeven met een bericht als `Successfully installed 'Microsoft.CognitiveServices.Speech 1.13.0' to helloworld`.
