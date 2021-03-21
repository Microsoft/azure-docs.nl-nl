---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: bff2f05a95faf9c475189cb5a8003cb7fd9f69be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701387"
---
1. Als u de functie wilt uitvoeren, drukt u op <kbd>F5</kbd> in Visual Studio. Mogelijk moet u ook een firewall-uitzondering maken, zodat de hulpprogramma's HTTP-aanvragen kunnen afhandelen. Er worden geen autorisatieniveaus afgedwongen wanneer u een functie lokaal uitvoert.

2. Kopieer de URL van uw functie vanuit de uitvoer van de Azure Functions-runtime.

    ![Lokale Azure-runtime](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Plak de URL van de HTTP-aanvraag in de adresbalk van uw browser. Voeg de queryreeks `?name=<YOUR_NAME>` toe aan de URL en voer de aanvraag uit. In de afbeelding hieronder ziet u de reactie in de browser op de lokale GET-aanvraag die door de functie wordt geretourneerd: 

    ![De reactie van de lokale host van de functie in de browser](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Als u het fout opsporingsprogramma wilt stoppen, drukt u op <kbd>SHIFT</kbd> + <kbd>F5</kbd> in Visual Studio.
