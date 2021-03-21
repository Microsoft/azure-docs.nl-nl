---
title: Berichten aan een Azure Storage-wachtrij toevoegen met behulp van Functions
description: Gebruik Azure Functions om een functie zonder server te maken die wordt aangeroepen met een HTTP-aanvraag, en waarmee een bericht worden gemaakt in een Azure Storage-wachtrij.
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.topic: how-to
ms.date: 04/24/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: d2821a16e0b72b32cc392b7ae626d782734458a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98674198"
---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>Berichten aan een Azure Storage-wachtrij toevoegen met behulp van Functions

In Azure Functions bieden invoer- en uitvoerbindingen een verklarende manier om gegevens van externe services beschikbaar te maken voor uw code. In deze snelstartgids gebruikt u een uitvoerbinding om een bericht in een wachtrij te maken wanneer een functie wordt geactiveerd met een HTTP-aanvraag. U gebruikt Azure storage-container om de wachtrij berichten weer te geven die door de functie worden gemaakt.

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstart:

- Een Azure-abonnement. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

- Volg de instructies in [Uw eerste functie maken vanuit Azure Portal](./functions-get-started.md) en voer de stap **Resources opschonen** niet uit. In deze snelstartgids worden de functie-app en de functie gemaakt die u hier gebruikt.

## <a name="add-an-output-binding"></a><a name="add-binding"></a>Een uitvoerbinding toevoegen

In deze sectie gebruikt u de gebruikersinterface van de portal om een Queue Storage-uitvoerbinding toe te voegen aan de functie die u eerder hebt gemaakt. Deze binding maakt het mogelijk om minimale code te schrijven om een bericht in een wachtrij te maken. U hoeft geen code te schrijven voor taken zoals het openen van een opslagverbinding, het maken van een wachtrij of het krijgen van een verwijzing naar een wachtrij. Deze taken worden voor u verwerkt via Azure Functions-runtime en Queue Storage-uitvoerbinding.

1. Open in Azure Portal de pagina Functie-app voor de functie-app die u hebt gemaakt in [Uw eerste functie maken vanuit Azure Portal](./functions-get-started.md). Als u de pagina wilt openen, zoekt en selecteert u **functie-app**. Selecteer vervolgens de functie-app.

1. Selecteer de functie-app en selecteer vervolgens de functie die u hebt gemaakt in die eerdere Snelstartgids.

1. Selecteer **integratie** en selecteer **+ uitvoer toevoegen**.

   :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding.png" alt-text="Maak een uitvoer binding voor uw functie." border="true":::

1. Selecteer het bindings type van **Azure Queue Storage** en voeg de instellingen toe zoals opgegeven in de tabel die volgt op deze scherm afbeelding: 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding-details.png" alt-text="Voeg een Queue Storage-uitvoerbinding toe aan een functie in Azure Portal." border="true":::
    
    | Instelling      |  Voorgestelde waarde   | Beschrijving                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Naam van de berichtparameter** | outputQueueItem | De naam van de uitvoerbindingparameter. | 
    | **Wachtrijnaam**   | outqueue  | De naam van de wachtrij waarmee u verbinding moet maken in uw opslagaccount. |
    | **Opslagaccountverbinding** | AzureWebJobsStorage | U kunt de opslagaccountverbinding gebruiken die al door de functie-app wordt gebruikt of u kunt een nieuwe maken.  |

1. Selecteer **OK** om de binding toe te voegen.

Nu u een uitvoerbinding hebt gedefinieerd, moet u de code bijwerken, zodat u de binding kunt gebruiken om berichten aan een wachtrij toe te voegen.  

## <a name="add-code-that-uses-the-output-binding"></a>Code toevoegen die gebruikmaakt van de uitvoerbinding

In deze sectie voegt u code toe waarmee een bericht wordt geschreven naar de uitvoerwachtrij. Het bericht bevat de waarde die wordt doorgegeven aan de HTTP-trigger in de querytekenreeks. Als de querytekenreeks bijvoorbeeld `name=Azure` bevat, is het wachtrijbericht: *Naam wordt doorgegeven aan de functie: Azure*.

1. Selecteer in de functie **code + test** om de functie code in de editor weer te geven.

1. Werk de functiecode bij, afhankelijk van de functietaal:

    # <a name="c"></a>[G\#](#tab/csharp)

    Voeg een parameter **outputQueueItem** toe aan de methodehandtekening zoals weergegeven in het volgende voorbeeld.

    ```cs
    public static async Task<IActionResult> Run(HttpRequest req,
        ICollector<string> outputQueueItem, ILogger log)
    {
        ...
    }
    ```

    Voeg in de hoofdtekst van de functie, net voor de instructie `return`, code toe die gebruikmaakt van de parameter om een wachtrijbericht te maken.

    ```cs
    outputQueueItem.Add("Name passed to the function: " + name);
    ```

    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    Voeg code toe die gebruikmaakt van de uitvoerbinding voor het object `context.bindings` om een wachtrijbericht te maken. Voeg deze code toe vóór de instructie `context.done`.

    ```javascript
    context.bindings.outputQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ---

1. Selecteer **Opslaan** om de wijzigingen op te slaan.

## <a name="test-the-function"></a>De functie testen

1. Nadat de code wijzigingen zijn opgeslagen, selecteert u **testen**.
1. Bevestig dat uw test overeenkomt met de onderstaande installatie kopie en selecteer **uitvoeren**. 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png" alt-text="Test de binding van de wachtrij opslag in de Azure Portal." border="true":::

    U ziet dat de **Aanvraagtekst** de waarde `name`*Azure* bevat. Deze waarde ziet u in het wachtrijbericht dat wordt gemaakt wanneer de functie wordt aangeroepen.
    
    In plaats van hier **Uitvoeren** te selecteren, kunt u de functie aanroepen door een URL in te voeren in een browser en de waarde `name` op te geven in de querytekenreeks. De browsermethode kunt u bekijken in de [vorige snelstartgids](./functions-get-started.md).

1. Raadpleeg de logboeken om er zeker van te zijn dat de functie is voltooid. 

Er wordt een nieuwe wachtrij met de naam **outqueue** in uw opslag account gemaakt door de functions-runtime wanneer de uitvoer binding voor het eerst wordt gebruikt. U gebruikt Storage-account om te controleren of de wachtrij en een bericht in het bestand zijn gemaakt.

### <a name="find-the-storage-account-connected-to-azurewebjobsstorage"></a>Het opslag account zoeken dat is verbonden met AzureWebJobsStorage


1. Ga naar de functie-app en selecteer **configuratie**.

1. Onder **Toepassings instellingen** selecteert u **AzureWebJobsStorage**.

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-find-storage-account.png" alt-text="Scherm afbeelding toont de configuratie pagina waarvoor AzureWebJobsStorage is geselecteerd." border="true":::

1. Zoek de naam van het account en noteer deze.

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-storage-account-name.png" alt-text="Zoek het opslag account dat is verbonden met AzureWebJobsStorage." border="true":::

### <a name="examine-the-output-queue"></a>De uitvoerwachtrij controleren

1. Selecteer in de resource groep voor de functie-app het opslag account dat u voor deze Quick Start gebruikt.

1. Selecteer onder **Queue-service** **wacht rijen** en selecteer de wachtrij met de naam **outwachtrij**. 

   De wachtrij bevat het bericht dat met de Queue Storage-uitvoerbinding is gemaakt toen u de met HTTP geactiveerde functie hebt uitgevoerd. Als u de functie hebt aangeroepen met de standaardwaarde voor `name`, namelijk *Azure*, is het wachtrijbericht *Naam is doorgegeven aan de functie: Azure*.

1. Voer de functie opnieuw uit. Er wordt nu een nieuw bericht weergegeven in de wachtrij.  

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u een uitvoerbinding toegevoegd aan een bestaande functie. Zie [Azure Functions Storage queue bindings](functions-bindings-storage-queue.md) (Opslagwachtrijbindingen van Azure Functions) voor meer informatie over de binding met Queue Storage.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps-2.md)]
