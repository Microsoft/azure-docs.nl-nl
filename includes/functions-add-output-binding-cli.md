---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 7ef3bd0f401ba54d56ed42df34cd2e761681dbc7
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904062"
---
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-an-output-binding-definition-to-the-function"></a>Een definitie van een uitvoerbinding toevoegen aan de functie

Hoewel een functie slechts één trigger kan hebben, kan deze meerdere in- en uitvoerbindingen hebben, zodat u verbinding kunt maken met andere Azure-services en -resources zonder dat u aangepaste integratiecode hoeft te schrijven. 
::: zone-end
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
U declareert deze bindingen in het bestand *function.json* in de map function. Door de vorige quickstart bevat uw bestand *function.json* in de map *HttpExample* twee bindingen in de verzameling `bindings`:  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Elke binding heeft ten minste een type, een richting en een naam. In het bovenstaande voorbeeld heeft de eerste binding het type `httpTrigger` en de richting `in`. Voor de richting `in` is `name` de naam van een invoerparameter die naar de functie wordt verzonden wanneer deze wordt aangeroepen door de trigger.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
De tweede binding in de verzameling heeft de naam `res`. Deze `http`-binding is een uitvoerbinding (`out`) die wordt gebruikt voor het schrijven van het HTTP-antwoord. 

Als u vanuit deze functie naar een Azure Storage-wachtrij wilt schrijven, voegt u een `out`-binding van het type `queue` met de naam `msg` toe, zoals wordt weergegeven in de onderstaande code:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
De tweede binding in de verzameling heeft het type `http` en de richting `out`, in welk geval de speciale `name` van `$return` aangeeft dat deze binding de retourwaarde van de functie gebruikt in plaats van een invoerparameter aan te leveren.

Als u vanuit deze functie naar een Azure Storage-wachtrij wilt schrijven, voegt u een `out`-binding van het type `queue` met de naam `msg` toe, zoals wordt weergegeven in de onderstaande code:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
De tweede binding in de verzameling heeft de naam `res`. Deze `http`-binding is een uitvoerbinding (`out`) die wordt gebruikt voor het schrijven van het HTTP-antwoord. 

Als u vanuit deze functie naar een Azure Storage-wachtrij wilt schrijven, voegt u een `out`-binding van het type `queue` met de naam `msg` toe, zoals wordt weergegeven in de onderstaande code:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
In dit geval wordt `msg` als uitvoerargument aan de functie doorgegeven. Voor een binding van het type `queue` moet u ook de naam van de wachtrij opgeven in `queueName` en de *naam* van de Azure Storage-verbinding (uit *local.settings.json*) in `connection` opgeven. 
::: zone-end  
