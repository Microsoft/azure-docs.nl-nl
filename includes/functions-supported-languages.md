---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d09d69db33cadee1b5a214e9cb2f09a508f37242
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431840"
---
|Taal                                 |1.x         |2.x| 3.x |
|-----------------------------------------|------------|---| --- |
|[C#](../articles/azure-functions/functions-reference-csharp.md)|GA (.NET Framework 4.7)|GA (.NET Core 2,2<sup>1</sup>)| GA (.NET Core 3.1)<br/>[Preview (.NET 5,0)](../articles/azure-functions/dotnet-isolated-process-guide.md) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|GA (Node 6)|GA (Node 10 & 8)| GA (Node 12 & 10)<br />Preview (Node 14) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|GA (.NET Framework 4.7)|GA (.NET Core 2,2<sup>1</sup>)| GA (.NET Core 3.1) |
|[Java](../articles/azure-functions/functions-reference-java.md)|N.v.t.|GA (Java 8)| GA (Java 11 & 8)|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |N.v.t.|GA (PowerShell Core 6)| GA (PowerShell 7 & Core 6)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|N.v.t.|GA (Python 3.7 & 3.6)| GA (Python 3.8, 3.7, & 3.6) <br/> Preview (python 3,9)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript) |N.v.t.|GA<sup>2</sup>| GA<sup>2</sup> |


<sup>1</sup> .net Class Library-apps met als doel runtime versie 2. x kunnen nu worden uitgevoerd op .net Core 3,1 in .net Core 2. x-compatibiliteits modus. Zie voor meer informatie [functions versie 2. x overwegingen](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).  
<sup>2</sup> Ondersteund via transpilatie naar JavaScript.

Raadpleeg het taalspecifieke artikel in de ontwikkelaarshandleiding voor meer informatie over ondersteunde taalversies.   
Zie [Azure-roadmap](https://azure.microsoft.com/roadmap/?tag=functions) voor informatie over geplande wijzigingen aan taalondersteuning.
