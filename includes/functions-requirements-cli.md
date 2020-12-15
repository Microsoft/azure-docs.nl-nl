---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: f102a5dd5b7dccba6643176d06d17a2a65171c90
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904063"
---
## <a name="configure-your-local-environment"></a>Uw lokale omgeving configureren

Voordat u begint, moet u het volgende hebben:

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java,programming-language-other"  
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2), versie 2.7.1846 of hoger.
::: zone-end  
::: zone pivot="programming-language-python"
+ De Azure Functions Core Tools-versie die overeenkomt met de geïnstalleerde Python-versie:

   | Python-versie | Core Tools-versie |
   | -------------- | ------------------ |
   | Python 3.8     | [versie 3.x](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3.6<br/>Python 3.7 | [Versie 2.7.1846 of een latere versie](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) versie 2.4 of hoger. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), Active LTS en Maintenance LTS-versies (8.11.1 en 10.14.1 aanbevolen).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64-bits)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64-bits)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64-bit)](https://www.python.org/downloads/release/python-368/), die door Azure Functions worden ondersteund. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ De [.NET Core SDK 3.1](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versie 8 of 11. 

+ [Apache Maven](https://maven.apache.org), versie 3.0 of hoger.

::: zone-end
::: zone pivot="programming-language-other"
+ Ontwikkelhulpprogramma's voor de taal die u gebruikt. In deze zelfstudie wordt de [R-programmeertaal](https://www.r-project.org/) gebruikt als voorbeeld.
::: zone-end