---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: a4f03223b5067d18f5d6e747b3bb630a86031b8f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020340"
---
Als u geen uitbreidings bundels kunt gebruiken, kunt u Azure Functions Core Tools lokaal gebruiken om de specifieke extensie pakketten te installeren die vereist zijn voor uw project.

> [!IMPORTANT]
> U kunt geen expliciete uitbrei dingen installeren in een functie-app die gebruikmaakt van uitbreidings bundels. Verwijder de `extensionBundle` sectie in *host.jsop* voordat u de extensies expliciet installeert.

De volgende items beschrijven enkele redenen die u mogelijk hand matig moet installeren:

* U moet toegang hebben tot een specifieke versie van een uitbrei ding die niet beschikbaar is in een bundel.
* U moet toegang hebben tot een aangepaste extensie die niet beschikbaar is in een bundel.
* U moet toegang hebben tot een specifieke combi natie van extensies die niet beschikbaar zijn in een enkele bundel.

> [!NOTE]
> Als u de uitbrei dingen hand matig wilt installeren met behulp van kern Hulpprogramma's, moet u de [.net Core 2. x SDK](https://dotnet.microsoft.com/download) hebben geïnstalleerd. De .NET Core SDK wordt gebruikt door Azure Functions Core Tools om uitbrei dingen van NuGet te installeren. U hoeft geen kennis te hebben van .NET om Azure Functions-extensies te gebruiken.

Wanneer u de extensies expliciet installeert, wordt een .NET-project bestand met de naam Extensions. csproj toegevoegd aan de hoofdmap van uw project. Dit bestand definieert de set NuGet-pakketten die vereist zijn voor uw functies. Hoewel u met de NuGet- [pakket verwijzingen](/nuget/consume-packages/package-references-in-project-files) in dit bestand kunt werken, kunt u met de belangrijkste hulpprogram ma's uitbrei dingen installeren zonder het bestand hand matig te hoeven bewerken.

Er zijn verschillende manieren om de vereiste extensies in uw lokale project te installeren met behulp van kern Hulpprogramma's. 

#### <a name="install-all-extensions"></a>Alle uitbrei dingen installeren 

Gebruik de volgende opdracht om automatisch alle extensie pakketten toe te voegen die worden gebruikt door de bindingen in uw lokale project:

```dotnetcli
func extensions install
```
Met de opdracht leest u de *function.jsin* het bestand om te zien welke pakketten u nodig hebt, installeert u ze en bouwt u het project extensies uit (extensies. csproj). Er worden nieuwe bindingen aan de huidige versie toegevoegd, maar bestaande bindingen worden niet bijgewerkt. Gebruik de `--force` optie om bestaande bindingen bij te werken naar de meest recente versie bij het installeren van nieuwe.

Als uw functie-app bindingen gebruikt die niet worden herkend door kern Hulpprogramma's, moet u de specifieke extensie hand matig installeren.

#### <a name="install-a-specific-extension"></a>Een specifieke extensie installeren

Gebruik de volgende opdracht voor het installeren van een specifiek uitbreidings pakket op een specifieke versie, in dit geval de opslag extensie:

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```
