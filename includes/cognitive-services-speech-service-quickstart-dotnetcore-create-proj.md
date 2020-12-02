---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 3f1a24db459cabd65d9ce17b89105c3b9ab8abb7
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188324"
---
1. Start Visual Studio 2019.

1. Zorg ervoor dat de workload voor **.NET platformoverschrijdende ontwikkeling** beschikbaar is. Kies **Extra** > **Hulpmiddelen en onderdelen ophalen** in de menubalk van Visual Studio om het installatieprogramma voor Visual Studio te openen. Als deze workload al is ingeschakeld, sluit u het dialoogvenster.

   ![Schermopname van het Visual Studio-installatieprogramma met het tabblad Workloads gemarkeerd](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   Selecteer anders het vak naast **platformoverschrijdende ontwikkelmogelijkheden van .NET Core** en selecteer **Wijzigen** in de rechterbenedenhoek van het dialoogvenster. De installatie van de nieuwe functie kan even duren.

1. Maak een nieuwe Visual C# .NET Core-consoletoepassing. Vouw in het dialoogvenster **Nieuw project**, vanuit het linkerdeelvenster, **Geïnstalleerd** > **Visual C#** > **.NET Core** uit. Selecteer vervolgens **Consoletoepassing (.NET Core)**. Voer als projectnaam *helloworld* in.

   ![Schermafbeelding van het dialoogvenster New Project](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Maak een Visual C#-consoletoepassing (.NET-kern)")

1. Installeer en bekijk het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget). Klik in Solution Explorer met de rechtermuisknop op de oplossing en kies **NuGet-pakketten beheren voor oplossing**.

   ![Schermopname van Solution Explorer, met de optie Manage NuGet Packages for Solution gemarkeerd](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "NuGet Packages for Solution beheren")

1. Selecteer in de rechterbovenhoek, in het veld **Package Source**, het pakket **nuget.org**. Zoek het pakket `Microsoft.CognitiveServices.Speech` en installeer het in het project **helloworld**.

   ![Schermopname van het dialoogvenster ‘Manage Packages for Solution’.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "NuGet-pakket installeren")

1. Ga akkoord met de weergegeven licentie om te beginnen met het installeren van het NuGet-pakket.

   ![Schermafbeelding van het licentievenster](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "De licentievoorwaarden accepteren")

Wanneer het pakket is geïnstalleerd, wordt er een bevestiging weergegeven in de Package Manager-console.
