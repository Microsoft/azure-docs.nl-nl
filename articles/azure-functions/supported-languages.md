---
title: Ondersteunde talen in Azure Functions
description: Meer informatie over welke talen worden ondersteund (GA) en in de preview-versie en hoe u de ontwikkeling van functies kunt uitbreiden naar andere talen.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: da3100521faae61e9803aaa82dbf91045a7ecbc9
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108548"
---
# <a name="supported-languages-in-azure-functions"></a>Ondersteunde talen in Azure Functions

Dit artikel bevat informatie over de ondersteunings niveaus voor talen die u kunt gebruiken met Azure Functions. Ook worden strategieën beschreven voor het maken van functies met behulp van talen die niet systeem eigen worden ondersteund.

## <a name="levels-of-support"></a>Ondersteunings niveaus

Er zijn twee ondersteunings niveaus:

* **Algemeen beschikbaar (ga)** : volledig ondersteund en goedgekeurd voor productie gebruik.
* **Preview** -wordt nog niet ondersteund, maar wordt naar verwachting in de toekomst bereikt.

## <a name="languages-by-runtime-version"></a>Talen per runtime versie 

Er zijn [drie versies van de Azure functions runtime](functions-versions.md) beschikbaar. In de volgende tabel ziet u welke talen worden ondersteund in elke runtime versie.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

## <a name="custom-handlers"></a>Aangepaste handlers

Aangepaste handlers zijn Lightweight webservers die gebeurtenissen van de Azure Functions host ontvangen. Elke taal die HTTP-primitieven ondersteunt, kan een aangepaste handler implementeren. Dit betekent dat aangepaste handlers kunnen worden gebruikt voor het maken van functies in talen die niet officieel worden ondersteund. Zie [Azure functions aangepaste handlers](functions-custom-handlers.md)voor meer informatie.

## <a name="language-extensibility"></a>Taal uitbreid baarheid

Met ingang van versie 2. x is de runtime ontworpen om [taal uitbreid baarheid](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility)aan te bieden. De Java script-en Java-talen in de 2. x-runtime zijn gebouwd met deze uitbrei ding.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende bronnen voor meer informatie over het ontwikkelen van functies in de ondersteunde talen:

+ [Naslag informatie voor ontwikkel aars van C#-klassen bibliotheek](functions-dotnet-class-library.md)
+ [Naslag informatie voor C#-script ontwikkelaars](functions-reference-csharp.md)
+ [Naslag informatie voor Java-Ontwikkel aars](functions-reference-java.md)
+ [Naslag informatie over Java script-ontwikkel aars](functions-reference-node.md)
+ [Naslag informatie over Power shell-ontwikkel aars](functions-reference-powershell.md)
+ [Naslag informatie voor python-ontwikkel aars](functions-reference-python.md)
+ [Naslag informatie voor type script-ontwikkel aars](functions-reference-node.md#typescript)
