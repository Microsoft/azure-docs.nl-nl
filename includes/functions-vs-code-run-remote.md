---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 55a75651b724a4fe975f655958e36fbd40e35db7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96748263"
---
## <a name="run-the-function-in-azure"></a>De functie in Azure uitvoeren

1. Ga terug naar **Azure: Functions** en vouw in de zijbalk de nieuwe functie-app uit onder uw abonnement. Vouw **Functies** uit, klik met de rechtermuisknop (Windows) of <kbd>Ctrl -</kbd> klik (macOS) op **HttpExample** en kies vervolgens **Functie-URL kopiëren**.

    ![De functie-URL kopiëren voor de nieuwe HTTP-trigger](./media/functions-vs-code-run-remote/function-copy-endpoint-url.png)

1. Plak deze URL voor de HTTP-aanvraag in de adresbalk van uw browser, voeg de `name`queryreeks toe als `?name=Functions` aan het einde van deze URL en voer de aanvraag uit. De URL die uw HTTP-geactiveerde functie aanroept, moet de volgende indeling hebben:

    ```http
    http://<FUNCTION_APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions
    ```

    U ziet in het volgende voorbeeld het antwoord op de externe GET-aanvraag dat door de functie wordt geretourneerd, weergegeven in de browser:

    ![Het antwoord van de functie in de browser](./media/functions-vs-code-run-remote/functions-test-remote-browser.png)
