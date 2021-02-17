---
title: 'Quickstart: Instelling van het platform voor Speech-SDK voor .NET Framework (Windows) - Speech-service'
titleSuffix: Azure Cognitive Services
description: Gebruik deze handleiding om uw platform in te stellen voor het gebruik van C# onder .NET Framework voor Windows met de Speech-service-SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-dotnet
ms.openlocfilehash: 1a9faf24c5a82b815b40afe15769480b69074dc9
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551480"
---
In deze gids ontdekt u hoe u de [Speech-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) kunt installeren voor .NET Framework (Windows). Als u enkel de pakketnaam nodig hebt om zelf aan de slag te gaan, voer dan `Install-Package Microsoft.CognitiveServices.Speech` uit in de NuGet-console.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* In Windows hebt u het [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) voor uw platform nodig. Bij een eerste installatie is mogelijk een herstart vereist.
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Een Visual Studio-project maken en de Speech-SDK installeren

U moet het [NuGet-pakket met de Speech-SDK](https://aka.ms/csspeech/nuget) installeren zodat u hiernaar kunt verwijzen in uw code. Hiervoor moet u mogelijk eerst een **helloworld**-project maken. Als u al een project hebt waarvoor de workload **.NET-desktopontwikkeling** beschikbaar is, kunt u dat project gebruiken en meteen verder gaan naar [NuGet-pakketbeheer gebruiken om de Speech-SDK te installeren](#use-nuget-package-manager-to-install-the-speech-sdk).

### <a name="create-helloworld-project"></a>Een helloworld-project maken

1. Open Visual Studio 2019.

1. Selecteer in het Startvenster de optie **Een nieuw project maken**. 

1. Kies in het venster **Een nieuw project maken** **Console-app (.NET Framework)** en selecteer vervolgens **Volgende**.

1. Voer in het venster **Uw nieuwe project configureren** *helloworld* in bij **Projectnaam**, kies of maak het mappad bij **Locatie** en selecteer vervolgens **Maken**.

1. Selecteer in de menubalk van Visual Studio **Hulpprogramma's** > **Hulpprogramma's en functies ophalen**. Hiermee opent u Visual Studio Installer en wordt het dialoogvenster **Wijzigen** weergegeven.

1. Controleer of de workload **.NET-desktopontwikkeling** beschikbaar is. Als de workload niet is geïnstalleerd, schakelt u het selectievakje ernaast in en selecteert u **Wijzigen** om de installatie te starten. Het downloaden en installeren kan een paar minuten duren.

   Als het selectievakje naast **.NET-desktopontwikkeling** is ingeschakeld, selecteert u **Sluiten** om het dialoogvenster af te sluiten.

   ![.NET-desktopontwikkeling inschakelen](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Sluit Visual Studio Installer.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>NuGet-pakketbeheer gebruiken om de Speech-SDK te installeren

1. Klik in de Solution Explorer met de rechtermuisknop op het **helloworld**-project en selecteer vervolgens **NuGet-pakketten beheren** om NuGet-pakketbeheer weer te geven.

   ![NuGet-pakketbeheer](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. Zoek de vervolgkeuzelijst **Pakketbron** in de rechterbovenhoek en controleer of **`nuget.org`** is geselecteerd.

1. Selecteer **Bladeren** in de linkerbovenhoek.

1. Typ *Microsoft.CognitiveServices.Speech* in het zoekvak en selecteer **Invoeren**.

1. Bij de zoekresultaten selecteert u het **Microsoft.CognitiveServices.Speech**-pakket en selecteert u vervolgens **Installeren** om de meest recente stabiele versie te installeren.

   ![Het NuGet-pakket Microsoft.CognitiveServices.Speech installeren](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Accepteer alle overeenkomsten en licenties om de installatie te starten.

   Nadat het pakket is geïnstalleerd, wordt er een bevestiging weergegeven in het venster **Package Manager-console**.

### <a name="choose-target-architecture"></a>De doelarchitectuur selecteren

Voor het bouwen en uitvoeren van de consoletoepassing maakt u een platformconfiguratie die overeenkomt met de architectuur van uw computer.

1. Selecteer **Bouwen** > **Configuration Manager** in de menubalk. Het dialoogvenster **Configuration Manager** wordt weergegeven.

   ![Het dialoogvenster Configuration Manager](~/articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. In de vervolgkeuzelijst **Platform actieve oplossing** selecteert u **Nieuw**. Het dialoogvenster **Platform nieuwe oplossing** wordt weergegeven.

1. In de vervolgkeuzelijst **Typ of selecteer het nieuwe platform**:
   - Als u 64-bits Windows uitvoert, selecteert u **x64**.
   - Als u 32-bits Windows uitvoert, selecteert u **x86**.

1. Selecteer **OK** en vervolgens **Sluiten**.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [windows](../quickstart-list.md)]
