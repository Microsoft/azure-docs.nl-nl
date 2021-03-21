---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d1931614356a313334d712713965346e843a403d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93424770"
---
## <a name="invoke-the-function-on-azure"></a>De functie aanroepen in Azure

Omdat uw functie gebruikmaakt van een HTTP-trigger, roept u deze aan door een HTTP-aanvraag naar de URL in de browser of met een hulpprogramma zoals curl. 

# <a name="browser"></a>[Browser](#tab/browser)

Kopieer de volledige **Aanroep-URL** die wordt weergegeven in de uitvoer van de publicatieopdracht naar de adresbalk van een browser en voeg de queryparameter `&name=Functions` toe. De browser moet vergelijkbare uitvoer weergeven als u de functie lokaal hebt uitgevoerd.

![De uitvoer van de functie die wordt uitgevoerd op Azure in een browser](./media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Voer [`curl`](https://curl.haxx.se/) uit met de **aanroep-URL** en voeg de parameter `&name=Functions` toe. De uitvoer van de opdracht moet de tekst ‘Hallo Functions’ zijn.

![De uitvoer van de functie die wordt uitgevoerd op Azure met behulp van curl](./media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---
