---
title: Durable Functions maken met behulp van de Azure Portal
description: Meer informatie over het installeren van de Durable Functions extensie voor Azure Functions voor portal ontwikkeling.
ms.topic: conceptual
ms.date: 04/10/2020
ms.reviewer: azfuncdf
ms.openlocfilehash: b029fa246977dfe4210f6e8df242415f7e4103f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87081913"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Durable Functions maken met behulp van de Azure Portal

De uitbrei ding voor de [Durable functions](durable-functions-overview.md) voor Azure functions is opgenomen in het NuGet-pakket [micro soft. Azure. webjobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Deze uitbrei ding moet worden geïnstalleerd in uw functie-app. In dit artikel wordt beschreven hoe u dit pakket installeert zodat u duurzame functies kunt ontwikkelen in de Azure Portal.

> [!NOTE]
> 
> * Als u duurzame functies in C# ontwikkelt, moet u in plaats daarvan [Visual Studio 2019-ontwikkeling](durable-functions-create-first-csharp.md)overwegen.
> * Als u duurzame functies ontwikkelt in Java script, moet u in plaats daarvan [Visual Studio code Development](./quickstart-js-vscode.md)overwegen.

## <a name="create-a-function-app"></a>Een functie-app maken

U moet een functie-app hebben om de uitvoering van een functie te hosten. Met een functie-app kunt u uw functies groeperen als logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren, schalen en delen. U kunt een .NET-of Java script-app maken.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

De functie-app die is gemaakt, maakt standaard gebruik van versie 2. x van de Azure Functions runtime. De uitbrei ding Durable Functions werkt op versie 1. x en 2. x van de Azure Functions runtime in C# en versie 2. x in Java script. Sjablonen zijn echter alleen beschikbaar bij het doel versie 2. x van de runtime, ongeacht de gekozen taal.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Het NPM-pakket met duurzame functies installeren (alleen Java script)

Als u Java script-Durable Functions maakt, moet u het NPM- [ `durable-functions` pakket](https://www.npmjs.com/package/durable-functions)installeren:

1. Selecteer op de pagina van de functie-app **geavanceerde hulp** middelen onder **ontwikkelingsprogram ma's** in het linkerdeel venster.

   :::image type="content" source="./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png" alt-text="Functies platform functies kiezen kudu":::

2. Selecteer op de pagina **geavanceerde hulp middelen** de optie **Go**.

3. Selecteer in de kudu-console **fout opsporing console** en klik vervolgens op **cmd**.

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Console voor fout opsporing kudu":::

3. De structuur van de bestands directory van de functie-app moet worden weer gegeven. Navigeer naar de map `site/wwwroot`. Van daaruit kunt u een bestand uploaden `package.json` door het te slepen en neer te zetten in het venster van de bestands directory. Hieronder ziet u een voor beeld `package.json` :

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Kudu-upload package.jsop":::

4. Nadat uw `package.json` is geüpload, voert u de `npm install` opdracht uit vanuit de kudu-console voor externe uitvoering.

   ![Kudu uitvoeren NPM installeren](./media/durable-functions-create-portal/kudu-npm-install.png)
   
## <a name="create-an-orchestrator-function"></a>Een Orchestrator-functie maken

1. Selecteer in de functie-app **functies** in het linkerdeel venster en selecteer vervolgens **toevoegen** in het bovenste menu. 

1. Voer in het zoek veld van de pagina **nieuwe functie** in `durable` en kies vervolgens de sjabloon **Durable functions http-starter** .

   :::image type="content" source="./media/durable-functions-create-portal/durable-functions-http-starter-template.png" alt-text="Durable Functions HTTP-starter selecteren":::

1. Voer voor de **nieuwe functie** naam in `HttpStart` en selecteer vervolgens **functie maken**.

   De functie die wordt gemaakt, wordt gebruikt om de indeling te starten.

1. Maak een andere functie in de functie-app, dit keer door gebruik te maken van de **Durable functions Orchestrator** -sjabloon. Geef een naam op voor de nieuwe Orchestration-functie `HelloSequence` .

1. Maak een derde functie `Hello` met de naam met behulp van de sjabloon **Durable functions activiteit** .

## <a name="test-the-durable-function-orchestration"></a>De functie voor de integratie van duurzame functies testen

1. Ga terug naar de functie **HttpStart** , kies **functie-URL ophalen** en selecteer het pictogram **kopiëren naar klem bord** om de URL te kopiëren. U gebruikt deze URL om de functie **HelloSequence** te starten.

1. Gebruik een HTTP-hulp programma zoals postman of krul om een POST-aanvraag te verzenden naar de URL die u hebt gekopieerd. Het volgende voor beeld is een krul opdracht waarmee een POST-aanvraag wordt verzonden naar de functie duurzame:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence --header "Content-Length: 0"
    ```

    In dit voor beeld `{your-function-app-name}` is het domein de naam van de functie-app. Het antwoordbericht bevat een serie URI-eindpunten die u kunt gebruiken om de uitvoering te controleren en beheren. De uitvoering ziet eruit zoals in het volgende voorbeeld:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Roep de `statusQueryGetUri` URI van het eind punt aan en u ziet de huidige status van de duurzame functie. deze kan er als volgt uitzien:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Ga door met het aanroepen van het `statusQueryGetUri` eind punt totdat de status is gewijzigd in **voltooid** en er een antwoord wordt weer geven, zoals in het volgende voor beeld:

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Uw eerste duurzame functie is nu actief en wordt uitgevoerd in Azure.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over algemene patronen van duurzame functies](durable-functions-overview.md#application-patterns)
