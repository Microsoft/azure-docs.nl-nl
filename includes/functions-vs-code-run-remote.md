---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 4b15fec0f22db740bbd7c24fcc0acf2ad1a2d1cd
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493458"
---
## <a name="run-the-function-in-azure"></a>De functie in Azure uitvoeren

1. Ga terug naar het gedeelte **functies van Azure: functions** op de balk aan de zijkant en vouw **lokale project**  >  **functies** uit. Klik met de rechter muisknop (Windows) of <kbd>CTRL</kbd> (macOS) op de `HttpExample` functie en kies **functie nu uitvoeren...**.

    :::image type="content" source="media/functions-vs-code-run-remote/execute-function-now.png" alt-text="De functie nu uitvoeren in azure vanuit Visual Studio code":::

1. In de **hoofd tekst** van de aanvraag ziet u de waarde van de aanvraag bericht hoofdtekst van `{ "name": "Azure" }` . Druk op ENTER om dit aanvraag bericht naar uw functie te verzenden.  

1. Wanneer de functie wordt uitgevoerd in Azure en een antwoord retourneert, wordt er een melding gegenereerd in Visual Studio code.
