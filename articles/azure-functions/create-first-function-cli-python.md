---
title: Een Python-functie maken via de opdrachtregel - Azure Functions
description: Ontdek hoe u een Python-functie maakt vanaf de opdrachtregel en vervolgens het lokale project publiceert op serverloze hosting in Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: 664a43dee635fa202f69927569fc1a5297bd1997
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880807"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Quickstart: Een Python-functie maken in Azure vanaf de opdrachtregel

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

In dit artikel gebruikt u opdrachtregelprogramma’s om een Python-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions.

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

Er is ook een [Versie op basis van Visual Studio Code](create-first-function-vs-code-python.md) van dit artikel.

## <a name="configure-your-local-environment"></a>Uw lokale omgeving configureren

Voordat u begint, moet u het volgende hebben:

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ De [Azure Functions Core Tools](functions-run-local.md#v2), versie 3.x.
  
+ Een van de volgende hulpprogramma's voor het maken van Azure-resources:

    + [Azure CLI](/cli/azure/install-azure-cli) versie 2.4 of hoger.

    + [Azure PowerShell](/powershell/azure/install-az-ps) versie 5.0 of hoger.

+ [Python-versies die worden ondersteund door Azure Functions](supported-languages.md#languages-by-runtime-version)

### <a name="prerequisite-check"></a>Controle van vereisten

Controleer uw vereisten. Deze zijn afhankelijk van de vraag of u Azure CLI of Azure PowerShell gebruikt voor het maken van Azure-resources:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

+ Voer in een terminal- of opdrachtvenster `func --version` uit om te controleren of de Azure Functions Core Tools versie 3.x zijn.

+ Voer `az --version` uit om te controleren of u versie 2.4 of hoger hebt van de Azure CLI.

+ Voer `az login` uit om u aan te melden bij Azure en te controleren of u een actief abonnement hebt.

+ Voer `python --version` (Linux/macOS) of `py --version` (Windows) uit om uw Python-versierapporten 3.8.x, 3.7.x of 3.6.x te controleren.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Voer in een terminal- of opdrachtvenster `func --version` uit om te controleren of de Azure Functions Core Tools versie 3.x zijn.

+ Voer `(Get-Module -ListAvailable Az).Version` uit en controleer of u versie 5.0 of hoger gebruikt. 

+ Voer `Connect-AzAccount` uit om u aan te melden bij Azure en te controleren of u een actief abonnement hebt.

+ Voer `python --version` (Linux/macOS) of `py --version` (Windows) uit om uw Python-versierapporten 3.8.x, 3.7.x of 3.6.x te controleren.

---

## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Een virtuele omgeving maken en activeren

Voer de volgende opdrachten uit in een geschikte map om een virtuele omgeving met de naam `.venv` te maken en te activeren. Zorg ervoor dat u Python 3.8, 3.7 of 3.6 gebruikt, die wordt ondersteund door Azure Functions.

# <a name="bash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Als Python het venv-pakket niet heeft geïnstalleerd in uw Linux-distributie, voert u de volgende opdracht uit:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

U voert alle volgende opdrachten uit in deze geactiveerde virtuele omgeving. 

## <a name="create-a-local-function-project"></a>Een lokaal functieproject maken

In Azure Functions is een functieproject een container voor een of meer afzonderlijke functies die elk op een bepaalde trigger reageren. Alle functies in een project delen dezelfde lokale configuratie en hostingconfiguratie. In deze sectie maakt u een functieproject dat één functie bevat.

1. Voer de opdracht `func init` als volgt uit om een functieproject te maken in een map met de naam *LocalFunctionProj* met de opgegeven runtime:  

    ```console
    func init LocalFunctionProj --python
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

#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* bevat de Python-functie`main()` die wordt geactiveerd op basis van de configuratie in *function.json*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

Voor een HTTP-trigger ontvangt de functie aanvraaggegevens in de variabele `req` zoals gedefinieerd in *function.json*. `req` is een instantie van de [azure.functions.HttpRequest-klasse](/python/api/azure-functions/azure.functions.httprequest). Het retourobject, gedefinieerd als `$return` in *function.json*, is een instantie van [azure.functions.HttpResponse-klasse](/python/api/azure-functions/azure.functions.httpresponse). Zie [Azure Functions HTTP-triggers en -bindingen](./functions-bindings-http-webhook.md?tabs=python) voor meer informatie.

#### <a name="functionjson"></a>function.json

*function.json* is een configuratiebestand dat de invoer en uitvoer `bindings` definieert voor de functie, met inbegrip van het triggertype.

U kunt `scriptFile` zo nodig wijzigen om een ander Python-bestand aan te roepen.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Elke binding vereist een richting, een type en een unieke naam. De HTTP-trigger heeft een invoerbinding van het type [`httpTrigger`](functions-bindings-http-webhook-trigger.md) en uitvoerbinding van het type [`http`](functions-bindings-http-webhook-output.md).

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>Ondersteunende Azure-resources maken voor uw functie

Voordat u uw functiecode kunt implementeren in Azure, moet u drie resources maken:

- Een resourcegroep, wat een logische container is voor gerelateerde resources.
- Een Storage-account, waarin de status en andere gegevens van uw projecten worden onderhouden.
- Een functie-app, die de omgeving biedt voor het uitvoeren van uw functiecode. Een functie-app wordt gekoppeld aan uw lokale functieproject en maakt het mogelijk om functies te groeperen als een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren en delen.

Gebruik de volgende opdrachten om deze items te maken. Zowel Azure CLI als PowerShell worden ondersteund.

1. Als u dit nog niet hebt gedaan, meldt u zich aan bij Azure:

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Met de opdracht [az login](/cli/azure/reference-index#az-login) meldt u zich aan bij uw Azure-account.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Met de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) meldt u zich aan bij uw Azure-account.

    ---

1. Maak een resourcegroep met de naam `AzureFunctionsQuickstart-rg` in de regio `westeurope`. 

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Met de opdracht [az group create](/cli/azure/group#az-group-create) maakt u een resourcegroep. Over het algemeen maakt u een resourcegroep en resources in een regio bij u in de buurt, waarbij u een beschikbare regio gebruikt die wordt geretourneerd door de opdracht `az account list-locations`.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) maakt u een resourcegroep. Over het algemeen maakt u een resourcegroep en resources in een regio bij u in de buurt, waarbij u een beschikbare regio gebruikt die wordt geretourneerd door de cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

    ---

    > [!NOTE]
    > Het is niet mogelijk om Linux- en Windows-apps in dezelfde resourcegroep te hosten. Als u een bestaande resourcegroep met de naam `AzureFunctionsQuickstart-rg` hebt die een Windows-functie-app of web-app bevat, moet u een andere resourcegroep gebruiken.

1. Maak een algemeen opslagaccount in de resourcegroep en regio:

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Met de opdracht [az storage account create](/cli/azure/storage/account#az-storage-account-create) maakt u het opslagaccount. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    Met de cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) maakt u het opslagaccount.

    ---

    Vervang `<STORAGE_NAME>` in het vorige voorbeeld door een naam die voor u passend is en die uniek is in Azure Storage. Namen mogen drie tot 24 tekens bevatten en u mag alleen kleine letters gebruiken. Met `Standard_LRS` geeft u een account voor algemeen gebruik op dat wordt [ondersteund door Functions](storage-considerations.md#storage-account-requirements).
    
    Voor het opslagaccount worden gedurende deze quickstart slechts een paar dollarcenten in rekening gebracht.

1. De functie-app maken in Azure:

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME> --os-type linux
    ```
    
    Maak de functie-app in Azure met behulp van de opdracht [az functionapp create](/cli/azure/functionapp#az_functionapp_create). Als u gebruikmaakt van Python 3.7 of 3.6, wijzigt u `--runtime-version` respectievelijk in `3.7` of `3.6`.
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```
    
    Maak de functie-app in Azure met behulp van de opdracht [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp). Als u gebruikmaakt van Python 3.7 of 3.6, wijzigt u `-RuntimeVersion` respectievelijk in `3.7` of `3.6`.

    ---
    
    Vervang in het vorige voorbeeld `<STORAGE_NAME>` door de naam van het account dat u in de vorige stap hebt gebruikt en vervang `<APP_NAME>` door een unieke naam die voor u van betekenis is.  De `<APP_NAME>` is ook het standaard DNS-domein voor de functie-app. 
    
    Met deze opdracht maakt u een functie-app die wordt uitgevoerd in de runtime van uw opgegeven taal binnen het [Azure Functions-verbruiksplan](consumption-plan.md). Dit is gratis voor het gebruik dat u hier maakt. Met deze opdracht wordt in dezelfde resourcegroep ook een gekoppelde instantie van Azure Application Insights ingericht, waarmee u uw functie-app kunt bewaken en logboeken kunt weergeven. Zie [Monitor Azure Functions](functions-monitoring.md) (Azure Functions bewaken) voor meer informatie. Er worden pas kosten in rekening gebracht voor de instantie als u deze activeert.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

Voer de volgende opdracht uit om [streaminglogboeken](functions-run-local.md#enable-streaming-logs) in bijna realtime weer te geven in Application Insights in Azure Portal:

```console
func azure functionapp logstream <APP_NAME> --browser
```

Roep in een afzonderlijk terminalvenster of in de browser opnieuw de externe functie aan. In de terminal wordt een uitgebreid logboek weergegeven van de uitvoering van de functie in Azure. 

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verbinding maken met een Azure Storage-wachtrij](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/python-functions-qs-survey)