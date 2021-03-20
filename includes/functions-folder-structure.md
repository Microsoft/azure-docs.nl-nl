---
title: bestand opnemen
description: bestand opnemen
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5f5f682c8c31c17f1a645bcdacdc78f32bd5c001
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018418"
---
De code voor alle functies in een specifieke functie-app bevinden zich in een hoofdmap die een hostconfiguratiebestand en een of meer submappen bevat. Elke submap bevat de code voor een afzonderlijke functie. De mapstructuur wordt weergegeven in de volgende weergave:

```
FunctionApp
 | - host.json
 | - MyFirstFunction
 | | - function.json
 | | - ...  
 | - MySecondFunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

In versie 2.x en hoger van de Functions-runtime moeten alle functies in de functie-app dezelfde taalstack delen.  

Het bestand [host.json](../articles/azure-functions/functions-host-json.md) bevat runtimespecifieke configuraties en bevindt zich in de hoofdmap van de functie-app. Een *bin*-map bevat pakketten en andere bibliotheekbestanden die de functie-app nodig heeft. Raadpleeg de taalspecifieke vereisten voor een functie-app-project:

* [C#-klassebibliotheek (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C#-script (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F#-script](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)
