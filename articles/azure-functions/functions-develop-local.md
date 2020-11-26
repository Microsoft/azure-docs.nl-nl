---
title: Azure Functions lokaal ontwikkelen en uitvoeren
description: Meer informatie over het coderen en testen van Azure Functions op uw lokale computer voordat u deze uitvoert op Azure Functions.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 9c37d51abcc8d612b777b845515cf07666369d4f
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168131"
---
# <a name="code-and-test-azure-functions-locally"></a>Code-en test Azure Functions lokaal

U kunt Azure Functions in het [Azure Portal]ontwikkelen en testen, maar veel ontwikkel aars geven de voor keur aan een lokale ontwikkel ervaring. Met functies kunt u eenvoudig uw favoriete code-editor en ontwikkel hulpprogramma's gebruiken om functies op uw lokale computer te maken en te testen. Uw lokale functies kunnen verbinding maken met Live Azure-Services en u kunt fouten opsporen op uw lokale computer met behulp van de volledige functions-runtime.

## <a name="local-development-environments"></a>Lokale ontwikkel omgevingen

De manier waarop u functies op uw lokale computer ontwikkelt, is afhankelijk van uw [taal](supported-languages.md) en programma voorkeuren. De omgevingen in de volgende tabel ondersteunen lokale ontwikkeling:

|Omgeving                              |Talen         |Description|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C# (Class-bibliotheek)](functions-dotnet-class-library.md), [C#-script (. CSX)](functions-reference-csharp.md), [Java script](functions-reference-node.md), [Power shell](./create-first-function-vs-code-powershell.md), [python](functions-reference-python.md) | De [uitbrei ding van de Azure functions voor VS code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voegt functies ondersteuning toe aan VS code. Vereist de kern Hulpprogramma's. Ondersteunt de ontwikkeling van Linux, MacOS en Windows bij gebruik van versie 2. x van de kern Hulpprogramma's. Zie [uw eerste functie maken met Visual Studio code](./create-first-function-vs-code-csharp.md)voor meer informatie. |
| [Opdracht prompt of Terminal](functions-run-local.md) | [C# (Class-bibliotheek)](functions-dotnet-class-library.md), [C#-script (. CSX)](functions-reference-csharp.md), [Java script](functions-reference-node.md), [Power shell](functions-reference-powershell.md), [python](functions-reference-python.md) | [Azure functions core tools] biedt de kern runtime en sjablonen voor het maken van functies, waarmee lokale ontwikkeling mogelijk wordt. Versie 2. x ondersteunt de ontwikkeling van Linux, MacOS en Windows. Alle omgevingen zijn afhankelijk van de belangrijkste Hulpprogram Ma's voor de runtime van lokale functies. |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (klassebibliotheek)](functions-dotnet-class-library.md) | De Azure Functions-hulpprogram ma's zijn opgenomen in de **Azure Development** -werk belasting van [Visual Studio 2019](https://www.visualstudio.com/vs/) en latere versies. Hiermee kunt u functies in een klassen bibliotheek compileren en het dll-bestand publiceren naar Azure. Bevat de belangrijkste Hulpprogram Ma's voor lokale tests. Zie [Azure functions ontwikkelen met Visual Studio](functions-develop-vs.md)voor meer informatie. |
| [Maven](./create-first-function-cli-java.md) (verschillende) | [Java](functions-reference-java.md) | Kan worden geïntegreerd met kern Hulpprogramma's om de ontwikkeling van Java-functies mogelijk te maken. Versie 2. x ondersteunt de ontwikkeling van Linux, MacOS en Windows. Zie [uw eerste functie maken met Java en Maven](./create-first-function-cli-java.md)voor meer informatie. Biedt ook ondersteuning voor ontwikkeling met het idee van [eclips](functions-create-maven-eclipse.md) en [IntelliJ](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Met elk van deze lokale ontwikkel omgevingen kunt u functie-app-projecten maken en vooraf gedefinieerde functie sjablonen gebruiken om nieuwe functies te maken. Elk maakt gebruik van de kern Hulpprogramma's zodat u uw functies kunt testen en fout opsporing uitvoert op de echte functions-runtime op uw eigen computer, net zoals elke andere app. U kunt uw functie-app-project ook publiceren vanuit een van deze omgevingen naar Azure.

## <a name="next-steps"></a>Volgende stappen

+ Zie [Azure functions ontwikkelen met Visual Studio](functions-develop-vs.md)voor meer informatie over de lokale ontwikkeling van gecompileerde C#-functies met behulp van visual studio 2019.
+ Zie [Azure functions van VS code implementeren](/azure/developer/javascript/tutorial-vscode-serverless-node-01)voor meer informatie over de lokale ontwikkeling van functies met behulp van VS code op een Mac-, Linux-of Windows-computer.
+ Zie [werken met Azure functions core tools](functions-run-local.md)voor meer informatie over het ontwikkelen van functies vanaf de opdracht prompt of Terminal.

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure-portal]: https://portal.azure.com
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows