---
title: Een C#-functie maken via de opdrachtregel - Azure Functions
description: Ontdek hoe u een C#-functie maakt vanaf de opdrachtregel en vervolgens het lokale project publiceert op serverloze hosting in Azure Functions.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-csharp-ieux
ms.openlocfilehash: 08b1f2b112542a4b4772744d122ce1260b0edffd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704766"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Quickstart: Een C#-functie maken in Azure vanaf de opdrachtregel

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

In dit artikel gebruikt u opdrachtregelprogramma's om een op een C#-klassenbibliotheek gebaseerde functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions.

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

Er is ook een [Versie op basis van Visual Studio Code](create-first-function-vs-code-csharp.md) van dit artikel.

## <a name="configure-your-local-environment"></a>Uw lokale omgeving configureren

Voordat u begint, moet u het volgende hebben:

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ De [.NET Core SDK 3.1](https://www.microsoft.com/net/download)

+ De [Azure Functions Core Tools](functions-run-local.md#v2), versie 3.x.

+ Een van de volgende hulpprogramma's voor het maken van Azure-resources:

    + [Azure CLI](/cli/azure/install-azure-cli) versie 2.4 of hoger.

    + [Azure PowerShell](/powershell/azure/install-az-ps) versie 5.0 of hoger.

### <a name="prerequisite-check"></a>Controle van vereisten

Controleer uw vereisten. Deze zijn afhankelijk van de vraag of u Azure CLI of Azure PowerShell gebruikt voor het maken van Azure-resources:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

+ Voer in een terminal- of opdrachtvenster `func --version` uit om te controleren of de Azure Functions Core Tools versie 3.x zijn.

+ Voer `az --version` uit om te controleren of u versie 2.4 of hoger hebt van de Azure CLI.

+ Voer `az login` uit om u aan te melden bij Azure en te controleren of u een actief abonnement hebt.

+ Voer `dotnet --list-sdks` uit om te controleren of .NET Core SDK-versie 3.1.x is geïnstalleerd

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Voer in een terminal- of opdrachtvenster `func --version` uit om te controleren of de Azure Functions Core Tools versie 3.x zijn.

+ Voer `(Get-Module -ListAvailable Az).Version` uit en controleer of u versie 5.0 of hoger gebruikt. 

+ Voer `Connect-AzAccount` uit om u aan te melden bij Azure en te controleren of u een actief abonnement hebt.

+ Voer `dotnet --list-sdks` uit om te controleren of .NET Core SDK-versie 3.1.x is geïnstalleerd

---

## <a name="create-a-local-function-project"></a>Een lokaal functieproject maken

In Azure Functions is een functieproject een container voor een of meer afzonderlijke functies die elk op een bepaalde trigger reageren. Alle functies in een project delen dezelfde lokale configuratie en hostingconfiguratie. In deze sectie maakt u een functieproject dat één functie bevat.

1. Voer de opdracht `func init` als volgt uit om een functieproject te maken in een map met de naam *LocalFunctionProj* met de opgegeven runtime:  

    ```csharp
    func init LocalFunctionProj --dotnet
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

    `func new` maakt een HttpExample.cs-code bestand.

### <a name="optional-examine-the-file-contents"></a>(Optioneel) De inhoud van het bestand bekijken

Desgewenst kunt u doorgaan naar [De functie lokaal uitvoeren](#run-the-function-locally) en de inhoud van het bestand later bekijken.

#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* bevat een `Run`-methode waarmee aanvraaggegevens worden ontvangen in de `req`-variabele. Dit is een [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) die is gedecoreerd met **HttpTriggerAttribute**, waarmee het gedrag van de trigger wordt gedefinieerd.

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Het retourobject is een [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) die een antwoordbericht retourneert als een [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) of een [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Zie [Azure Functions HTTP-triggers en -bindingen](./functions-bindings-http-webhook.md?tabs=csharp) voor meer informatie.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. De functie-app maken in Azure:

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Maak de functie-app in Azure met behulp van de opdracht [az functionapp create](/cli/azure/functionapp#az_functionapp_create). 
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    Maak de functie-app in Azure met behulp van de opdracht [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp). 
    
    ---
    
    Vervang in het vorige voorbeeld `<STORAGE_NAME>` door de naam van het account dat u in de vorige stap hebt gebruikt en vervang `<APP_NAME>` door een unieke naam die voor u van betekenis is. De `<APP_NAME>` is ook het standaard DNS-domein voor de functie-app. 
    
    Met deze opdracht maakt u een functie-app die wordt uitgevoerd in de runtime van uw opgegeven taal binnen het [Azure Functions-verbruiksplan](consumption-plan.md). Dit is gratis voor het gebruik dat u hier maakt. Met deze opdracht wordt in dezelfde resourcegroep ook een gekoppelde instantie van Azure Application Insights ingericht, waarmee u uw functie-app kunt bewaken en logboeken kunt weergeven. Zie [Monitor Azure Functions](functions-monitoring.md) (Azure Functions bewaken) voor meer informatie. Er worden pas kosten in rekening gebracht voor de instantie als u deze activeert.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verbinding maken met een Azure Storage-wachtrij]

[Verbinding maken met een Azure Storage-wachtrij]: functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp
