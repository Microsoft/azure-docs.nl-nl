---
title: Aan de slag met Azure Functions
description: Volg de eerste stappen voor het werken met Azure Functions.
author: craigshoemaker
ms.topic: overview
ms.date: 11/19/2020
ms.author: cshoe
zone_pivot_groups: programming-languages-set-functions-lang-workers
ms.openlocfilehash: 77d370b895c777278d3136c7d2c511e7f9e23b36
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179252"
---
# <a name="getting-started-with-azure-functions"></a>Aan de slag met Azure Functions

## <a name="introduction"></a>Inleiding

U kunt met [Azure Functions](./functions-overview.md) de logica van uw systeem implementeren in direct beschikbare codeblokken. Deze codeblokken worden 'functies' genoemd.

Gebruik de volgende bronnen om aan de slag te gaan.

::: zone pivot="programming-language-csharp"

| Actie | Resources |
| --- | --- |
| **Uw eerste functie maken** | Gebruik een van de volgende tools:<br><br><li>[Visual Studio](./functions-create-your-first-function-visual-studio.md)<li>[Visual Studio Code](./create-first-function-vs-code-csharp.md)<li>[Opdrachtregel](./create-first-function-cli-csharp.md) |
| **Zie een functie die wordt uitgevoerd** | <li>[Azure-voorbeeldenbrowser](/samples/browse/?expanded=azure&languages=csharp&products=azure-functions)<li>[Bibliotheek van de Azure-community](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=C%23) |
| **Een zelfstudie verkennen**| <li>[De beste serverloze technologie van Azure kiezen voor uw bedrijfsscenario](/learn/modules/serverless-fundamentals/)<li>[Goed ontworpen Framework - Prestatie-efficiëntie](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Een Azure-functie uitvoeren met triggers](/learn/modules/execute-azure-function-with-triggers/) <br><br>Zie Microsoft Learn voor een [volledige lijst met interactieve zelfstudies](/learn/browse/?expanded=azure&products=azure-functions).|
| **Best practices beoordelen** |<li>[Prestaties en betrouw baarheid](./functions-best-practices.md)<li>[Verbindingen beheren](./manage-connections.md)<li>[Fout afhandeling en functionele nieuwe pogingen](./functions-bindings-error-pages.md?tabs=csharp)<li>[Beveiliging](./security-concepts.md)|
| **Meer gedetailleerde informatie** | <li>Meer informatie over hoe functies exemplaren [vergroten of verkleinen](./functions-scale.md) om te voldoen aan de vraag<li>Bekijk de verschillende beschikbare [implementatiemethoden](./functions-deployment-technologies.md)<li>Gebruik ingebouwde [bewakingshulpprogramma's](./functions-monitoring.md) om uw functies te analyseren<li>Lees de [naslaginformatie voor de taal C#](./functions-dotnet-class-library.md)|

::: zone-end

::: zone pivot="programming-language-java"
| Actie | Resources |
| --- | --- |
| **Uw eerste functie maken** | Gebruik een van de volgende tools:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-java.md)<li>[Java/Maven-functie met Terminal/opdrachtprompt](./create-first-function-cli-java.md)<li>[Gradle](./functions-create-first-java-gradle.md)<li>[Eclipse](./functions-create-maven-eclipse.md)<li>[IntelliJ IDEA](./functions-create-maven-intellij.md) |
| **Zie een functie die wordt uitgevoerd** | <li>[Azure-voorbeeldenbrowser](/samples/browse/?expanded=azure&languages=java&products=azure-functions)<li>[Bibliotheek van de Azure-community](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Java) |
| **Een zelfstudie verkennen**| <li>[De beste serverloze technologie van Azure kiezen voor uw bedrijfsscenario](/learn/modules/serverless-fundamentals/)<li>[Goed ontworpen Framework - Prestatie-efficiëntie](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Een app ontwikkelen met de Maven-invoegtoepassing voor Azure Functions](/learn/modules/develop-azure-functions-app-with-maven-plugin/) <br><br>Zie Microsoft Learn voor een [volledige lijst met interactieve zelfstudies](/learn/browse/?expanded=azure&products=azure-functions).|
| **Best practices beoordelen** |<li>[Prestaties en betrouw baarheid](./functions-best-practices.md)<li>[Verbindingen beheren](./manage-connections.md)<li>[Fout afhandeling en functionele nieuwe pogingen](./functions-bindings-error-pages.md?tabs=java)<li>[Beveiliging](./security-concepts.md)|
| **Meer gedetailleerde informatie** | <li>Meer informatie over hoe functies exemplaren [vergroten of verkleinen](./functions-scale.md) om te voldoen aan de vraag<li>Bekijk de verschillende beschikbare [implementatiemethoden](./functions-deployment-technologies.md)<li>Gebruik ingebouwde [bewakingshulpprogramma's](./functions-monitoring.md) om uw functies te analyseren<li>Lees de [naslaginformatie voor de taal Java](./functions-reference-java.md)|
::: zone-end

::: zone pivot="programming-language-javascript"
| Actie | Resources |
| --- | --- |
| **Uw eerste functie maken** | Gebruik een van de volgende tools:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-node.md)<li>[Node.js-terminal/-opdrachtprompt](./create-first-function-cli-node.md) |
| **Zie een functie die wordt uitgevoerd** | <li>[Azure-voorbeeldenbrowser](/samples/browse/?expanded=azure&languages=javascript%2ctypescript&products=azure-functions)<li>[Bibliotheek van de Azure-community](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=JavaScript%2CTypeScript) |
| **Een zelfstudie verkennen** | <li>[De beste serverloze technologie van Azure kiezen voor uw bedrijfsscenario](/learn/modules/serverless-fundamentals/)<li>[Goed ontworpen Framework - Prestatie-efficiëntie](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Serverloze API's maken met Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Serverloze logica maken met Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Node.js- en Express-API's herstructureren naar serverloze API's met Azure Functions](/learn/modules/shift-nodejs-express-apis-serverless/) <br><br>Zie Microsoft Learn voor een [volledige lijst met interactieve zelfstudies](/learn/browse/?expanded=azure&products=azure-functions).|
| **Best practices beoordelen** |<li>[Prestaties en betrouw baarheid](./functions-best-practices.md)<li>[Verbindingen beheren](./manage-connections.md)<li>[Fout afhandeling en functionele nieuwe pogingen](./functions-bindings-error-pages.md?tabs=javascript)<li>[Beveiliging](./security-concepts.md)|
| **Meer gedetailleerde informatie** | <li>Meer informatie over hoe functies exemplaren [vergroten of verkleinen](./functions-scale.md) om te voldoen aan de vraag<li>Bekijk de verschillende beschikbare [implementatiemethoden](./functions-deployment-technologies.md)<li>Gebruik ingebouwde [bewakingshulpprogramma's](./functions-monitoring.md) om uw functies te analyseren<li>Lees de naslaginformatie voor de taal [JavaScript](./functions-reference-node.md) of [TypeScript](./functions-reference-node.md#typescript)|
::: zone-end

::: zone pivot="programming-language-powershell"
| Actie | Resources |
| --- | --- |
| **Uw eerste functie maken** | <li>[Visual Studio Code](./create-first-function-vs-code-powershell.md) gebruiken |
| **Zie een functie die wordt uitgevoerd** | <li>[Azure-voorbeeldenbrowser](/samples/browse/?expanded=azure&languages=powershell&products=azure-functions)<li>[Bibliotheek van de Azure-community](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=PowerShell) |
| **Een zelfstudie verkennen** | <li>[De beste serverloze technologie van Azure kiezen voor uw bedrijfsscenario](/learn/modules/serverless-fundamentals/)<li>[Goed ontworpen Framework - Prestatie-efficiëntie](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Serverloze API's maken met Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Serverloze logica maken met Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Een Azure-functie uitvoeren met triggers](/learn/modules/execute-azure-function-with-triggers/) <br><br>Zie Microsoft Learn voor een [volledige lijst met interactieve zelfstudies](/learn/browse/?expanded=azure&products=azure-functions).|
| **Best practices beoordelen** |<li>[Prestaties en betrouw baarheid](./functions-best-practices.md)<li>[Verbindingen beheren](./manage-connections.md)<li>[Fout afhandeling en functionele nieuwe pogingen](./functions-bindings-error-pages.md?tabs=powershell)<li>[Beveiliging](./security-concepts.md)|
| **Meer gedetailleerde informatie** | <li>Meer informatie over hoe functies exemplaren [vergroten of verkleinen](./functions-scale.md) om te voldoen aan de vraag<li>Bekijk de verschillende beschikbare [implementatiemethoden](./functions-deployment-technologies.md)<li>Gebruik ingebouwde [bewakingshulpprogramma's](./functions-monitoring.md) om uw functies te analyseren<li>Lees de [naslaginformatie voor de taal PowerShell](./functions-reference-powershell.md))|
::: zone-end

::: zone pivot="programming-language-python"
| Actie | Resources |
| --- | --- |
| **Uw eerste functie maken** | Gebruik een van de volgende tools:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-csharp.md?pivots=programming-language-python)<li>[Terminal/opdrachtprompt](./create-first-function-cli-csharp.md?pivots=programming-language-python) |
| **Zie een functie die wordt uitgevoerd** | <li>[Azure-voorbeeldenbrowser](/samples/browse/?expanded=azure&languages=python&products=azure-functions)<li>[Bibliotheek van de Azure-community](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Python) |
| **Een zelfstudie verkennen** | <li>[De beste serverloze technologie van Azure kiezen voor uw bedrijfsscenario](/learn/modules/serverless-fundamentals/)<li>[Goed ontworpen Framework - Prestatie-efficiëntie](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Serverloze API's maken met Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Serverloze logica maken met Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/) <br><br>Zie Microsoft Learn voor een [volledige lijst met interactieve zelfstudies](/learn/browse/?expanded=azure&products=azure-functions).|
| **Best practices beoordelen** |<li>[Prestaties en betrouw baarheid](./functions-best-practices.md)<li>[Verbindingen beheren](./manage-connections.md)<li>[Fout afhandeling en functionele nieuwe pogingen](./functions-bindings-error-pages.md?tabs=python)<li>[Beveiliging](./security-concepts.md)<li>[De doorvoer prestaties verbeteren](./python-scale-performance-reference.md)|
| **Meer gedetailleerde informatie** | <li>Meer informatie over hoe functies exemplaren [vergroten of verkleinen](./functions-scale.md) om te voldoen aan de vraag<li>Bekijk de verschillende beschikbare [implementatiemethoden](./functions-deployment-technologies.md)<li>Gebruik ingebouwde [bewakingshulpprogramma's](./functions-monitoring.md) om uw functies te analyseren<li>Lees de [naslaginformatie voor de taal Python](./functions-reference-python.md)|
::: zone-end

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over de anatomie van een Azure Functions-toepassing](./functions-reference.md)
