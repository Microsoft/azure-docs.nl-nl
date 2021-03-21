---
title: Een python-functie maken vanaf de opdracht regel voor Azure Functions
description: Meer informatie over het maken van een python-functie vanaf de opdracht regel en het publiceren van het lokale project op serverloze hosting in Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: da7f6fdaedd8105363cc62bf55bae2cb5f72f234
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031647"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Quickstart: Een Python-functie maken in Azure vanaf de opdrachtregel

> [!div class="op_single_selector" title1="Uw functietaal selecteren: "]
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

In dit artikel gebruikt u opdrachtregelprogramma’s om een Python-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de <abbr title="Een runtime Computing Environment waarin alle details van de server transparant zijn voor toepassings ontwikkelaars, waardoor het proces van het implementeren en beheren van code wordt vereenvoudigd.">serverloos</abbr> omgeving van <abbr title="Een Azure-service die een voordelige serverloze computer omgeving biedt voor toepassingen.">Azure Functions</abbr>.

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

Er is ook een [Versie op basis van Visual Studio Code](create-first-function-vs-code-python.md) van dit artikel.

## <a name="1-configure-your-environment"></a>1. Configureer uw omgeving

Voordat u begint, moet u het volgende hebben:

+ Een Azure <abbr title="Het profiel waarmee facturerings gegevens voor Azure-gebruik worden bijgehouden.">account</abbr> met een actieve <abbr title="De basis organisatie structuur waarin u resources in azure beheert, meestal gekoppeld aan een individu of afdeling binnen een organisatie.">abonnement</abbr>. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ De [Azure Functions Core Tools](functions-run-local.md#v2), versie 3.x. 
  
+ Ofwel de <abbr title="Een reeks verschillende platform opdracht regel Programma's voor het werken met Azure-resources vanaf uw lokale ontwikkel computer, als alternatief voor het gebruik van de Azure Portal.">Azure CLI</abbr> of <abbr title="Een Power shell-module die opdrachten biedt voor het werken met Azure-resources vanaf uw lokale ontwikkel computer, als alternatief voor het gebruik van de Azure Portal.">Azure PowerShell</abbr> voor het maken van Azure-resources:

    + [Azure CLI](/cli/azure/install-azure-cli) versie 2.4 of hoger.

    + [Azure PowerShell](/powershell/azure/install-az-ps) versie 5.0 of hoger.

+ [Python 3.8 (64-bits)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64-bits)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64-bits)](https://www.python.org/downloads/release/python-368/), die allemaal door versie 3.x van Azure Functions worden ondersteund.

### <a name="11-prerequisite-check"></a>1,1 controle op vereisten

Controleer uw vereisten, afhankelijk van of u de Azure CLI of Azure PowerShell gebruikt voor het maken van Azure-resources:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

+ Voer in een Terminal-of opdracht venster uit `func --version` om te controleren of de <abbr title="De set opdracht regel Programma's voor het werken met Azure Functions op uw lokale computer.">Azure Functions Core Tools</abbr> zijn versie 3. x.

+ Voer `az --version` uit om te controleren of u versie 2.4 of hoger hebt van de Azure CLI.

+ Voer `az login` uit om u aan te melden bij Azure en te controleren of u een actief abonnement hebt.

+ Voer `python --version` (Linux/macOS) of `py --version` (Windows) uit om uw Python-versierapporten 3.8.x, 3.7.x of 3.6.x te controleren.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Voer in een Terminal-of opdracht venster uit `func --version` om te controleren of de <abbr title="De set opdracht regel Programma's voor het werken met Azure Functions op uw lokale computer.">Azure Functions Core Tools</abbr> zijn versie 3. x.

+ Voer `(Get-Module -ListAvailable Az).Version` uit en controleer of u versie 5.0 of hoger gebruikt. 

+ Voer `Connect-AzAccount` uit om u aan te melden bij Azure en te controleren of u een actief abonnement hebt.

+ Voer `python --version` (Linux/macOS) of `py --version` (Windows) uit om uw Python-versierapporten 3.8.x, 3.7.x of 3.6.x te controleren.

---

<br/>

---

## <a name="2-create-and-activate-a-virtual-environment"></a>2. <a name="create-venv"></a> een virtuele omgeving maken en activeren

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

<br/>

---

## <a name="3-create-a-local-function-project"></a>3. een lokale functie project maken

In deze sectie maakt u een lokale <abbr title="Een logische container voor een of meer afzonderlijke functies die samen kunnen worden geïmplementeerd en beheerd.">Azure Functions project</abbr> in python. Elke functie in het project reageert op een specifiek <abbr title="Het type gebeurtenis waarmee de code van de functie wordt aangeroepen, zoals een HTTP-aanvraag, een wachtrij bericht of een specifieke tijd.">activeren</abbr>.

1. Voer de `func init` opdracht uit om een functions-project te maken in een map met de naam *LocalFunctionProj* met de opgegeven runtime:  

    ```console
    func init LocalFunctionProj --python
    ```

1. Navigeer naar de projectmap:

    ```console
    cd LocalFunctionProj
    ```
    
    <br/>
    <details>
    <summary><strong>Wat wordt er gemaakt in de map LocalFunctionProj?</strong></summary>
    
    Deze map bevat verschillende bestanden voor het project,waaronder configuratiebestanden met de naam [local.settings.json](functions-run-local.md#local-settings-file) en [host.json](functions-host-json.md). Omdat *local.settings.json* geheimen kan bevatten die zijn gedownload vanuit Azure, wordt het bestand standaard uitgesloten van broncodebeheer in het bestand *.gitignore*.
    </details>

1. Voeg een functie toe aan uw project met behulp van de volgende opdracht:

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    Het `--name` argument is de unieke naam van uw functie (HttpExample).

    Het `--template` argument geeft de trigger van de functie aan (http).
    
    `func new`Hiermee maakt u een submap die overeenkomt met de naam van de functie die een *\_ \_ init \_ \_ . py* -bestand bevat met de code van de functie en een configuratie bestand met de naam *function.jsop*.

    <br/>    
    <details>
    <summary><strong>Code voor __init__. py</strong></summary>
    
    *\_\_init\_\_.py* bevat de Python-functie`main()` die wordt geactiveerd op basis van de configuratie in *function.json*.
    
    :::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::
    
    Voor een HTTP-trigger ontvangt de functie aanvraaggegevens in de variabele `req` zoals gedefinieerd in *function.json*. `req` is een instantie van de [azure.functions.HttpRequest-klasse](/python/api/azure-functions/azure.functions.httprequest). Het retourobject, gedefinieerd als `$return` in *function.json*, is een instantie van [azure.functions.HttpResponse-klasse](/python/api/azure-functions/azure.functions.httpresponse). Zie [Azure Functions HTTP-triggers en -bindingen](./functions-bindings-http-webhook.md?tabs=python) voor meer informatie.
    </details>

    <br/>
    <details>
    <summary><strong>Code voor function.jsop</strong></summary>

    *function.js* is een configuratie bestand dat de <abbr title="Declaratieve verbindingen tussen een functie en andere resources. Een invoer binding biedt gegevens voor de functie. een uitvoer binding biedt gegevens van de functie aan andere resources.">invoer-en uitvoer bindingen</abbr> voor de functie, met inbegrip van het trigger type.
    
    U kunt `scriptFile` zo nodig wijzigen om een ander Python-bestand aan te roepen.
    
    :::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
    
    Elke binding vereist een richting, een type en een unieke naam. De HTTP-trigger heeft een invoerbinding van het type [`httpTrigger`](functions-bindings-http-webhook-trigger.md) en uitvoerbinding van het type [`http`](functions-bindings-http-webhook-output.md).    
    </details>

<br/>

---

## <a name="4-run-the-function-locally"></a>4. Voer de functie lokaal uit

1. Voer uw functie uit door de lokale Azure Functions runtime host te starten vanuit de map *LocalFunctionProj*:

    ```
    func start
    ```

    Naar het einde van de uitvoer moeten de volgende regels worden weergegeven: 
    
    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    <br/>
    <details>
    <summary><strong>HttpExample wordt niet weer geven in de uitvoer</strong></summary>

    Als HttpExample niet wordt weer gegeven, hebt u waarschijnlijk de host gestart van buiten de hoofdmap van het project. In dat geval gebruikt u <kbd>CTRL + C</kbd> om de host te stoppen, navigeert u naar de hoofdmap van het project en voert u de vorige opdracht opnieuw uit.
    </details>

1. Kopieer de URL van de **HttpExample** -functie van deze uitvoer naar een browser en voeg de query teken reeks toe **? name =<YOUR_NAME>**, waardoor de volledige URL, zoals **http://localhost:7071/api/HttpExample?name=Functions** . In de browser moet een bericht als **Hello-functies** worden weer gegeven:

    ![Resultaat van de functie lokaal uitgevoerd in de browser](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. De terminal waarin u uw project hebt gestart, toont ook de logboek uitvoer wanneer u aanvragen doet.

1. Wanneer u klaar bent, gebruikt u <kbd>CTRL + C</kbd> en kiest u <kbd>y</kbd> om de functions-host te stoppen.

<br/>

---

## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. ondersteunende Azure-resources maken voor uw functie

Voordat u uw functie code kunt implementeren in azure, moet u een maken <abbr title="Een logische container voor gerelateerde Azure-resources die u kunt beheren als een eenheid.">resourcegroep</abbr>, een <abbr title="Een account dat al uw Azure Storage-gegevens objecten bevat. Het opslag account biedt een unieke naam ruimte voor uw opslag gegevens.">opslagaccount</abbr>, en een <abbr title="De Cloud resource die fungeert als host voor serverloze functies in azure, die de onderliggende reken omgeving biedt waarin functies worden uitgevoerd.">functie-app</abbr> met behulp van de volgende opdrachten:

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
 
    Met de opdracht [az group create](/cli/azure/group#az-group-create) maakt u een resourcegroep. In het algemeen maakt u de resource groep en-resources in een <abbr title="Een geografische verwijzing naar een specifiek Azure-Data Center waarin resources worden toegewezen.">regio</abbr> in de buurt van een beschik bare regio die wordt geretourneerd door de `az account list-locations` opdracht.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) maakt u een resourcegroep. Over het algemeen maakt u een resourcegroep en resources in een regio bij u in de buurt, waarbij u een beschikbare regio gebruikt die wordt geretourneerd door de cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

    ---

    Het is niet mogelijk om Linux- en Windows-apps in dezelfde resourcegroep te hosten. Als u een bestaande resourcegroep met de naam `AzureFunctionsQuickstart-rg` hebt die een Windows-functie-app of web-app bevat, moet u een andere resourcegroep gebruiken.

1. Maak een Azure Storage account voor algemeen gebruik in uw resource groep en regio:

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

    Vervang door `<STORAGE_NAME>` een naam die geschikt is voor u en <abbr title="De naam moet uniek zijn voor alle opslag accounts die worden gebruikt door alle Azure-klanten wereld wijd. U kunt bijvoorbeeld een combi natie van uw persoonlijke of bedrijfs naam, toepassings naam en een numerieke id gebruiken, zoals in contosobizappstorage20.">uniek in Azure Storage</abbr>. Namen mogen drie tot 24 tekens bevatten en u mag alleen kleine letters gebruiken. Met `Standard_LRS` geeft u een account voor algemeen gebruik op dat wordt [ondersteund door Functions](storage-considerations.md#storage-account-requirements).
    
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
    
    Vervang door `<STORAGE_NAME>` de naam van het account dat u in de vorige stap hebt gebruikt.

    Vervangen `<APP_NAME>` door een <abbr title="Een naam die uniek moet zijn voor alle Azure-klanten wereld wijd. U kunt bijvoorbeeld een combi natie van uw persoonlijke of organisatie naam, toepassings naam en een numerieke id gebruiken, zoals in contoso-bizapp-func-20.">wereld wijd unieke naam die geschikt is voor u</abbr>. De `<APP_NAME>` is ook het standaard DNS-domein voor de functie-app. 
    
    <br/>
    <details>
    <summary><strong>Wat zijn de kosten van de resources die zijn ingericht in azure?</strong></summary>

    Met deze opdracht maakt u een functie-app die wordt uitgevoerd in de runtime van uw opgegeven taal binnen het [Azure Functions-verbruiksplan](functions-scale.md#overview-of-plans). Dit is gratis voor het gebruik dat u hier maakt. Met deze opdracht wordt in dezelfde resourcegroep ook een gekoppelde instantie van Azure Application Insights ingericht, waarmee u uw functie-app kunt bewaken en logboeken kunt weergeven. Zie [Monitor Azure Functions](functions-monitoring.md) (Azure Functions bewaken) voor meer informatie. Er worden pas kosten in rekening gebracht voor de instantie als u deze activeert.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. Implementeer het functie project in azure

Nadat u de functie-app in azure hebt gemaakt, bent u klaar om **uw lokale** functions-project te implementeren met behulp van de opdracht [func Azure functionapp Publish](functions-run-local.md#project-file-deployment) .  

Vervang `<APP_NAME>` in het volgende voorbeeld door de naam van uw app.

```console
func azure functionapp publish <APP_NAME>
```

De `publish` opdracht toont resultaten die vergelijkbaar zijn met de volgende uitvoer (afgekapt voor eenvoud):

<pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>

<br/>

---

## <a name="7-invoke-the-function-on-azure"></a>7. de functie aanroepen op Azure

Omdat uw functie gebruikmaakt van een HTTP-trigger, roept u deze aan door een HTTP-aanvraag in te stellen op de URL in de browser of met een hulp programma zoals <abbr title="Een opdracht regel programma voor het genereren van HTTP-aanvragen naar een URL; kijken https://curl.se/">curl</abbr>. 

# <a name="browser"></a>[Browser](#tab/browser)

Kopieer de volledige **invoke-URL** die wordt weer gegeven in de uitvoer van de `publish` opdracht naar een adres balk van de browser en voeg de query parameter **&name = functions** toe. De browser moet vergelijkbare uitvoer weergeven als u de functie lokaal hebt uitgevoerd.

![De uitvoer van de functie die wordt uitgevoerd op Azure in een browser](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Voer uit [`curl`](https://curl.haxx.se/) met de **aanroepen-URL** en voeg de para meter **&name = functions** toe. De uitvoer van de opdracht moet de tekst ‘Hallo Functions’ zijn.

![De uitvoer van de functie die wordt uitgevoerd op Azure met behulp van curl](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

### <a name="71-view-real-time-streaming-logs"></a>7,1 realtime streaming-logboeken weer geven

Voer de volgende opdracht uit om [streaminglogboeken](functions-run-local.md#enable-streaming-logs) in bijna realtime weer te geven in Application Insights in Azure Portal:

```console
func azure functionapp logstream <APP_NAME> --browser
```

Vervang door `<APP_NAME>` de naam van uw functie-app.

Roep in een afzonderlijk terminalvenster of in de browser opnieuw de externe functie aan. In de terminal wordt een uitgebreid logboek weergegeven van de uitvoering van de functie in Azure. 

<br/>

---

## <a name="8-clean-up-resources"></a>8. Resources opschonen

Als u doorgaat met de [volgende stap](#next-steps) en een <abbr title="Een manier om een functie te koppelen aan een opslag wachtrij, zodat deze berichten kan maken in de wachtrij. ">Uitvoer binding van Azure Storage wachtrij</abbr>, zorgt u ervoor dat al uw resources op de juiste plaats staan, zoals u gaat bouwen op wat u al hebt gedaan.

Gebruik anders de volgende opdracht om de resourcegroep en alle bijbehorende resources te verwijderen om te voorkomen dat er verdere kosten in rekening worden gebracht.

 # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

<br/>

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verbinding maken met een Azure Storage-wachtrij](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/python-functions-qs-survey)
