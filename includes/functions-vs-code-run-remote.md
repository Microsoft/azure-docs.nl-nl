---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 121b10cc568cce089c90e66b9c6f292c74f4acbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99809559"
---
## <a name="run-the-function-in-azure"></a>De functie in Azure uitvoeren

1. Ga terug naar het gebied **Azure: functions** in de zijbalk, vouw uw abonnement uit, uw nieuwe functie-app en **functions**. Klik met de rechter muisknop (Windows) of <kbd>CTRL</kbd> (macOS) op de `HttpExample` functie en kies **functie nu uitvoeren...**.

    :::image type="content" source="media/functions-vs-code-run-remote/execute-function-now.png" alt-text="De functie nu uitvoeren in azure vanuit Visual Studio code":::

1. In de **hoofd tekst** van de aanvraag ziet u de waarde van de aanvraag bericht hoofdtekst van `{ "name": "Azure" }` . Druk op ENTER om dit aanvraag bericht naar uw functie te verzenden.  

1. Wanneer de functie wordt uitgevoerd in Azure en een antwoord retourneert, wordt er een melding gegenereerd in Visual Studio code.
