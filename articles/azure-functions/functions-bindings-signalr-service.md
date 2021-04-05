---
title: Service bindingen van Azure Functions signalering
description: Meer informatie over het gebruik van seingevings service bindingen met Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 9e60fd9a20720d75f96a0b78ee783bd5509a8f90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97763486"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>SignalR-servicebindingen voor Azure Functions

In deze reeks artikelen wordt uitgelegd hoe u real-time berichten verifieert en verzendt naar clients die zijn verbonden met de [Azure signalerings service](https://azure.microsoft.com/services/signalr-service/) door gebruik te maken van de service bindingen van de signalering in azure functions. Azure Functions ondersteunt invoer- en uitvoerbindingen voor SignalR Service.

| Bewerking | Type |
|---------|---------|
| Berichten van de seingevings service verwerken | [Binding activeren](./functions-bindings-signalr-service-trigger.md) |
| De URL en het toegangs token van het service-eind punt retour neren | [Invoer binding](./functions-bindings-signalr-service-input.md) |
| Signaal service berichten verzenden |[Uitvoer binding](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Toevoegen aan uw functions-app

### <a name="functions-2x-and-higher"></a>Functions 2.x en hoger

Voor het werken met de trigger en bindingen moet u verwijzen naar het juiste pakket. Het NuGet-pakket wordt gebruikt voor .NET-klassen bibliotheken terwijl de uitbreidings bundel wordt gebruikt voor alle andere toepassings typen.

| Taal                                        | Toevoegen door...                                   | Opmerkingen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Het [NuGet-pakket]installeren, versie 3. x | |
| C#-script, Java, java script, Python, Power shell | De [uitbreidings bundel] registreren          | De [extensie voor Azure-Hulpprogram ma's] wordt aanbevolen voor gebruik met Visual Studio code. |
| C#-script (alleen online in Azure Portal)         | Een binding toevoegen                            | Zie [uw extensies bijwerken]om bestaande bindings extensies bij te werken zonder uw functie-app opnieuw te publiceren. |

[NuGet-pakket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[uitbreidings bundel]: ./functions-bindings-register.md#extension-bundles
[Uw extensies bijwerken]: ./functions-bindings-register.md
[Extensie van Azure-Hulpprogram Ma's]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

Raadpleeg [Azure functions ontwikkeling en configuratie met de Azure signalerings service](../azure-signalr/signalr-concept-serverless-development-config.md)voor meer informatie over het configureren en gebruiken van de seingevings service en Azure functions samen.

### <a name="annotations-library-java-only"></a>Aantekeningen bibliotheek (alleen Java)

Als u de aantekeningen van de seingevings service in Java-functies wilt gebruiken, moet u een afhankelijkheid toevoegen aan het *Azure-functions-Java-Library-seingevings-* artefact (versie 1,0 of hoger) aan uw *pom.xml* -bestand.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>Volgende stappen

- [Berichten van de seingevings service (binding activeren) afhandelen](./functions-bindings-signalr-service-trigger.md)
- [De URL en het toegangs token van het service-eind punt retour neren (invoer binding)](./functions-bindings-signalr-service-input.md)
- [Signaal service berichten verzenden (uitvoer binding)](./functions-bindings-signalr-service-output.md)