---
title: Een JavaScript-functie maken via de opdrachtregel - Azure Functions
description: Ontdek hoe u een JavaScript-functie maakt vanaf de opdrachtregel en vervolgens het lokale Node.js-project publiceert op serverloze hosting in Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: b8db78e56087e7cb777d1aa85391d4b6ac2aae27
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787535"
---
# <a name="quickstart-create-a-javascript-function-in-azure-from-the-command-line"></a>Quickstart: Een JavaScript-functie maken in Azure vanaf de opdrachtregel


[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

In dit artikel gebruikt u opdrachtregelprogramma’s om een JavaScript-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions. 

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

Er is ook een [Versie op basis van Visual Studio Code](create-first-function-vs-code-node.md) van dit artikel.

## <a name="configure-your-local-environment"></a>Uw lokale omgeving configureren

Voordat u begint, moet u het volgende hebben:

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ De [Azure Functions Core Tools](./functions-run-local.md#v2), versie 3.x.

+ Een van de volgende hulpprogramma's voor het maken van Azure-resources:

    + [Azure CLI](/cli/azure/install-azure-cli) versie 2.4 of hoger.

    + [Azure PowerShell](/powershell/azure/install-az-ps) versie 5.0 of hoger.

+ [Node.js](https://nodejs.org/) versie 12. Node.js versie 10 wordt ook ondersteund.

### <a name="prerequisite-check"></a>Controle van vereisten

Controleer uw vereisten. Deze zijn afhankelijk van de vraag of u Azure CLI of Azure PowerShell gebruikt voor het maken van Azure-resources:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

+ Voer in een terminal- of opdrachtvenster `func --version` uit om te controleren of de Azure Functions Core Tools versie 3.x zijn.

+ Voer `az --version` uit om te controleren of u versie 2.4 of hoger hebt van de Azure CLI.

+ Voer `az login` uit om u aan te melden bij Azure en te controleren of u een actief abonnement hebt.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Voer in een terminal- of opdrachtvenster `func --version` uit om te controleren of de Azure Functions Core Tools versie 3.x zijn.

+ Voer `(Get-Module -ListAvailable Az).Version` uit en controleer of u versie 5.0 of hoger gebruikt. 

+ Voer `Connect-AzAccount` uit om u aan te melden bij Azure en te controleren of u een actief abonnement hebt.

---

## <a name="create-a-local-function-project"></a>Een lokaal functieproject maken

In Azure Functions is een functieproject een container voor een of meer afzonderlijke functies die elk op een bepaalde trigger reageren. Alle functies in een project delen dezelfde lokale configuratie en hostingconfiguratie. In deze sectie maakt u een functieproject dat één functie bevat.

1. Voer de opdracht `func init` als volgt uit om een functieproject te maken in een map met de naam *LocalFunctionProj* met de opgegeven runtime:  

    ```console
    func init LocalFunctionProj --javascript
    ```

1. Navigeer naar de projectmap:

    ```console
    cd LocalFunctionProj
    ```

    Deze map bevat verschillende bestanden voor het project,waaronder configuratiebestanden met de naam [local.settings.json](functions-run-local.md#local-settings-file) en [host.json](functions-host-json.md). Omdat *local.settings.json* geheimen kan bevatten die zijn gedownload vanuit Azure, wordt het bestand standaard uitgesloten van broncodebeheer in het bestand *.gitignore*.

1. Voeg een functie toe aan uw project met behulp van de volgende opdracht, waarbij het argument `--name` de unieke naam van de functie is (HttpExample) en het argument `--template` de trigger van de functie (HTTP).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```  
    
    `func new` maakt een submap met de naam van de functie die een codebestand bevat dat geschikt is voor de taal van het project en een configuratiebestand met de naam *function.json*.
    
### <a name="optional-examine-the-file-contents"></a>(Optioneel) De inhoud van het bestand bekijken

Desgewenst kunt u doorgaan naar [De functie lokaal uitvoeren](#run-the-function-locally) en de inhoud van het bestand later bekijken.

#### <a name="indexjs"></a>index.js

*index.js* exporteert een functie die wordt geactiveerd op basis van de configuratie in *function.json*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

Voor een HTTP-trigger ontvangt de functie aanvraaggegevens in de variabele `req` zoals gedefinieerd in *function.json*. Het antwoord wordt gedefinieerd als `res` in *function.jsen* kan worden gebruikt met `context.res` . Zie [Azure Functions HTTP-triggers en -bindingen](./functions-bindings-http-webhook.md?tabs=javascript) voor meer informatie.

#### <a name="functionjson"></a>function.json

*function.json* is een configuratiebestand dat de invoer en uitvoer `bindings` definieert voor de functie, met inbegrip van het triggertype. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::

Elke binding vereist een richting, een type en een unieke naam. De HTTP-trigger heeft een invoerbinding van het type [`httpTrigger`](functions-bindings-http-webhook-trigger.md) en uitvoerbinding van het type [`http`](functions-bindings-http-webhook-output.md).

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. De functie-app maken in Azure:

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 12 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Maak de functie-app in Azure met behulp van de opdracht [az functionapp create](/cli/azure/functionapp#az_functionapp_create). Als u Node.js 10 gebruikt, wijzigt u ook `--runtime-version` in `10`.
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime node -RuntimeVersion 12 -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    Maak de functie-app in Azure met behulp van de opdracht [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp). Als u Node.js 10 gebruikt, wijzigt u `-RuntimeVersion` in `10`.
    
    ---
    
    Vervang in het vorige voorbeeld `<STORAGE_NAME>` door de naam van het account dat u in de vorige stap hebt gebruikt en vervang `<APP_NAME>` door een unieke naam die voor u van betekenis is. De `<APP_NAME>` is ook het standaard DNS-domein voor de functie-app. 
    
    Met deze opdracht maakt u een functie-app die wordt uitgevoerd in de runtime van uw opgegeven taal binnen het [Azure Functions-verbruiksplan](consumption-plan.md). Dit is gratis voor het gebruik dat u hier maakt. Met deze opdracht wordt in dezelfde resourcegroep ook een gekoppelde instantie van Azure Application Insights ingericht, waarmee u uw functie-app kunt bewaken en logboeken kunt weergeven. Zie [Monitor Azure Functions](functions-monitoring.md) (Azure Functions bewaken) voor meer informatie. Er worden pas kosten in rekening gebracht voor de instantie als u deze activeert.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verbinding maken met een Azure Storage-wachtrij](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-javascript)