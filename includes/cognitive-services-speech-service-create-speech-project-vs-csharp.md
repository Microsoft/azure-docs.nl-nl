---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001088"
---
Als u een Visual Studio-project voor Windows-ontwikkeling wilt maken, moet u het project maken, Visual Studio voor .NET Desktop Development instellen, de spraak-SDK installeren en de doel architectuur kiezen.

### <a name="create-the-project-and-add-the-workload"></a>Het project maken en de workload toevoegen

Als u wilt beginnen, maakt u het project in Visual Studio en zorgt u ervoor dat Visual Studio is ingesteld voor .NET Desktop Development:

1. Open Visual Studio 2019.

1. Selecteer in het Startvenster de optie **Een nieuw project maken**. 

1. Kies in het venster **Een nieuw project maken** **Console-app (.NET Framework)** en selecteer vervolgens **Volgende**.

1. Voer in het venster **Uw nieuwe project configureren** *helloworld* in bij **Projectnaam**, kies of maak het mappad bij **Locatie** en selecteer vervolgens **Maken**.

1. Selecteer in de menubalk van Visual Studio **Hulpprogramma's** > **Hulpprogramma's en functies ophalen**. Hiermee opent u Visual Studio Installer en wordt het dialoogvenster **Wijzigen** weergegeven.

1. Controleer of de workload **.NET-desktopontwikkeling** beschikbaar is. Als de workload niet is geïnstalleerd, schakelt u het selectievakje ernaast in en selecteert u **Wijzigen** om de installatie te starten. Het downloaden en installeren kan een paar minuten duren.

   Als het selectievakje naast **.NET-desktopontwikkeling** is ingeschakeld, selecteert u **Sluiten** om het dialoogvenster af te sluiten.

   ![.NET-desktopontwikkeling inschakelen](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Sluit Visual Studio Installer.

### <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

De volgende stap is het installeren van het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget), zodat u ernaar kunt verwijzen in de code.

1. Klik in de Solution Explorer met de rechtermuisknop op het **helloworld**-project en selecteer vervolgens **NuGet-pakketten beheren** om NuGet-pakketbeheer weer te geven.

   ![NuGet-pakketbeheer](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. Zoek in de rechter bovenhoek de vervolg keuzelijst **pakket bron** en zorg ervoor dat **nuget.org** is geselecteerd.

1. Selecteer **Bladeren** in de linkerbovenhoek.

1. Typ *Microsoft.CognitiveServices.Speech* in het zoekvak en selecteer **Invoeren**.

1. Bij de zoekresultaten selecteert u het **Microsoft.CognitiveServices.Speech**-pakket en selecteert u vervolgens **Installeren** om de meest recente stabiele versie te installeren.

   ![Het NuGet-pakket Microsoft.CognitiveServices.Speech installeren](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Accepteer alle overeenkomsten en licenties om de installatie te starten.

   Nadat het pakket is geïnstalleerd, wordt er een bevestiging weergegeven in het venster **Package Manager-console**.

### <a name="choose-the-target-architecture"></a>De doel architectuur kiezen

Maak nu een platform configuratie die overeenkomt met de architectuur van uw computer om de console toepassing te bouwen en uit te voeren.

1. Selecteer **Bouwen** > **Configuration Manager** in de menubalk. Het dialoogvenster **Configuration Manager** wordt weergegeven.

   ![Het dialoogvenster Configuration Manager](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. In de vervolgkeuzelijst **Platform actieve oplossing** selecteert u **Nieuw**. Het dialoogvenster **Platform nieuwe oplossing** wordt weergegeven.

1. In de vervolgkeuzelijst **Typ of selecteer het nieuwe platform**:
   - Als u 64-bits Windows uitvoert, selecteert u **x64**.
   - Als u 32-bits Windows uitvoert, selecteert u **x86**.

1. Selecteer **OK** en vervolgens **Sluiten**.
