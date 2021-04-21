---
title: Een Python-functie maken vanaf de opdrachtregel voor Azure Functions
description: Meer informatie over het maken van een Python-functie vanaf de opdrachtregel en het publiceren van het lokale project naar serverloze hosting in Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-powershell
- devx-track-azurecli
- devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e144304ae1b36ca02d4b8796e7994e87b09505d9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831092"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Quickstart: Een Python-functie maken in Azure vanaf de opdrachtregel

> [!div class="op_single_selector" title1="Uw functietaal selecteren: "]
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

In dit artikel gebruikt u opdrachtregelprogramma’s om een Python-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de <abbr title="Een runtime-computingomgeving waarin alle details van de server transparant zijn voor toepassingsontwikkelaars, waardoor het implementatie- en beheerproces van code wordt vereenvoudigd.">serverloos</abbr> omgeving van <abbr title="Een Azure-service die een goedkope serverloze computingomgeving biedt voor toepassingen.">Azure Functions</abbr>.

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

Er is ook een [Versie op basis van Visual Studio Code](create-first-function-vs-code-python.md) van dit artikel.

## <a name="1-configure-your-environment"></a>1. Uw omgeving configureren

Voordat u begint, moet u het volgende hebben:

+ Een Azure-account <abbr title="Het profiel dat factureringsgegevens voor Azure-gebruik bijhoudt.">account</abbr> met een actief <abbr title="De basisstructuur van de organisatie waarin u resources in Azure beheert, meestal gekoppeld aan een individu of afdeling binnen een organisatie.">abonnement</abbr>. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ De [Azure Functions Core Tools](functions-run-local.md#v2), versie 3.x. 
  
+ De <abbr title="Een set platformoverschrijdende opdrachtregelprogramma's voor het werken met Azure-resources vanaf uw lokale ontwikkelcomputer, als alternatief voor het gebruik van de Azure Portal.">Azure CLI</abbr> of <abbr title="Een PowerShell-module die opdrachten biedt voor het werken met Azure-resources vanaf uw lokale ontwikkelcomputer, als alternatief voor het gebruik van de Azure Portal.">Azure PowerShell</abbr> voor het maken van Azure-resources:

    + [Azure CLI](/cli/azure/install-azure-cli) versie 2.4 of hoger.

    + [Azure PowerShell](/powershell/azure/install-az-ps) versie 5.0 of hoger.

+ [Python 3.8 (64-bits)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64-bits)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64-bits)](https://www.python.org/downloads/release/python-368/), die allemaal door versie 3.x van Azure Functions worden ondersteund.

### <a name="11-prerequisite-check"></a>1.1 Controle van vereisten

Controleer uw vereisten, die afhankelijk zijn van of u de Azure CLI gebruikt of Azure PowerShell voor het maken van Azure-resources:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

+ Voer in een terminal- of opdrachtvenster uit `func --version` om te controleren of de <abbr title="De set opdrachtregelprogramma's voor het werken met Azure Functions op uw lokale computer.">Azure Functions Core Tools</abbr> zijn versie 3.x.

+ Voer `az --version` uit om te controleren of u versie 2.4 of hoger hebt van de Azure CLI.

+ Voer `az login` uit om u aan te melden bij Azure en te controleren of u een actief abonnement hebt.

+ Voer `python --version` (Linux/macOS) of `py --version` (Windows) uit om uw Python-versierapporten 3.8.x, 3.7.x of 3.6.x te controleren.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Voer in een terminal- of opdrachtvenster uit `func --version` om te controleren of de <abbr title="De set opdrachtregelprogramma's voor het werken met Azure Functions op uw lokale computer.">Azure Functions Core Tools</abbr> zijn versie 3.x.

+ Voer `(Get-Module -ListAvailable Az).Version` uit en controleer of u versie 5.0 of hoger gebruikt. 

+ Voer `Connect-AzAccount` uit om u aan te melden bij Azure en te controleren of u een actief abonnement hebt.

+ Voer `python --version` (Linux/macOS) of `py --version` (Windows) uit om uw Python-versierapporten 3.8.x, 3.7.x of 3.6.x te controleren.

---

<br/>

---

## <a name="2-create-and-activate-a-virtual-environment"></a>2. <a name="create-venv"></a> Een virtuele omgeving maken en activeren

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

## <a name="3-create-a-local-function-project"></a>3. Een lokaal functieproject maken

In deze sectie maakt u een lokale <abbr title="Een logische container voor een of meer afzonderlijke functies die samen kunnen worden geïmplementeerd en beheerd.">Azure Functions project</abbr> in Python. Elke functie in het project reageert op een specifiek <abbr title="Het type gebeurtenis dat de code van de functie aanroept, zoals een HTTP-aanvraag, een wachtrijbericht of een specifiek tijdstip.">activeren</abbr>.

1. Voer de `func init` opdracht uit om een Functions-project te maken in een map met de naam *LocalFunctionProj* met de opgegeven runtime:  

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

    Met `--template` het argument wordt de trigger (HTTP) van de functie opgegeven.
    
    `func new`maakt een submap die overeenkomt met de functienaam die een *\_ \_ init \_ \_ .py-bestand* bevat met de code van de functie en een configuratiebestand met de *naamfunction.jsop*.

    <br/>    
    <details>
    <summary><strong>Code voor __init__.py</strong></summary>
    
    *\_\_init\_\_.py* bevat de Python-functie`main()` die wordt geactiveerd op basis van de configuratie in *function.json*.
    
    :::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::
    
    Voor een HTTP-trigger ontvangt de functie aanvraaggegevens in de variabele `req` zoals gedefinieerd in *function.json*. `req` is een instantie van de [azure.functions.HttpRequest-klasse](/python/api/azure-functions/azure.functions.httprequest). Het retourobject, gedefinieerd als `$return` in *function.json*, is een instantie van [azure.functions.HttpResponse-klasse](/python/api/azure-functions/azure.functions.httpresponse). Zie [Azure Functions HTTP-triggers en -bindingen](./functions-bindings-http-webhook.md?tabs=python) voor meer informatie.
    </details>

    <br/>
    <details>
    <summary><strong>Code voor function.jsaan</strong></summary>

    *function.jsis een* configuratiebestand dat definieert <abbr title="Declaratieve verbindingen tussen een functie en andere resources. Een invoerbinding levert gegevens aan de functie; een uitvoerbinding levert gegevens van de functie aan andere resources.">invoer- en uitvoerbindingen</abbr> voor de functie, met inbegrip van het triggertype.
    
    U kunt `scriptFile` zo nodig wijzigen om een ander Python-bestand aan te roepen.
    
    :::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
    
    Elke binding vereist een richting, een type en een unieke naam. De HTTP-trigger heeft een invoerbinding van het type [`httpTrigger`](functions-bindings-http-webhook-trigger.md) en uitvoerbinding van het type [`http`](functions-bindings-http-webhook-output.md).    
    </details>

<br/>

---

## <a name="4-run-the-function-locally"></a>4. De functie lokaal uitvoeren

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
    <summary><strong>Ik zie HttpExample niet in de uitvoer</strong></summary>

    Als HttpExample niet wordt weergegeven, hebt u de host waarschijnlijk gestart buiten de hoofdmap van het project. Gebruik in dat geval <kbd>Ctrl+C</kbd> om de host te stoppen, navigeer naar de hoofdmap van het project en voer de vorige opdracht opnieuw uit.
    </details>

1. Kopieer de URL van uw **HttpExample-functie** van deze uitvoer naar een browser en plaats de querytekenreeks **?name=<YOUR_NAME>**, waardoor de volledige URL als wordt **http://localhost:7071/api/HttpExample?name=Functions** weergegeven. In de browser moet een bericht worden weergegeven als **Hallo functies:**

    ![Resultaat van de functie lokaal uitgevoerd in de browser](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. De terminal waarin u uw project hebt gestart, toont ook de logboek uitvoer wanneer u aanvragen doet.

1. Wanneer u klaar bent, gebruikt u <kbd>Ctrl +C</kbd> en kiest <kbd>u y om</kbd> de functions-host te stoppen.

<br/>

---

## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. Ondersteunende Azure-resources maken voor uw functie

Voordat u uw functiecode kunt implementeren in Azure, moet u een <abbr title="Een logische container voor gerelateerde Azure-resources die u als eenheid kunt beheren.">resourcegroep</abbr>A <abbr title="Een account dat al uw Azure Storage-gegevensobjecten bevat. Het opslagaccount biedt een unieke naamruimte voor uw opslaggegevens.">opslagaccount</abbr>, en een <abbr title="De cloudresource die als host fungeert voor serverloze functies in Azure, die de onderliggende rekenomgeving biedt waarin functies worden uitgevoerd.">functie-app</abbr> met behulp van de volgende opdrachten:

1. Als u dit nog niet hebt gedaan, meldt u zich aan bij Azure:

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Met de opdracht [az login](/cli/azure/reference-index#az_login) meldt u zich aan bij uw Azure-account.

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
 
    Met de opdracht [az group create](/cli/azure/group#az_group_create) maakt u een resourcegroep. Over het algemeen maakt u uw resourcegroep en resources in een <abbr title="Een geografische verwijzing naar een specifiek Azure-datacenter waarin resources worden toegewezen.">regio</abbr> bij u in de buurt, met behulp van een beschikbare regio die wordt geretourneerd door de `az account list-locations` opdracht .

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) maakt u een resourcegroep. Over het algemeen maakt u een resourcegroep en resources in een regio bij u in de buurt, waarbij u een beschikbare regio gebruikt die wordt geretourneerd door de cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

    ---

    Het is niet mogelijk om Linux- en Windows-apps in dezelfde resourcegroep te hosten. Als u een bestaande resourcegroep met de naam `AzureFunctionsQuickstart-rg` hebt die een Windows-functie-app of web-app bevat, moet u een andere resourcegroep gebruiken.

1. Maak een account voor algemeen Azure Storage in uw resourcegroep en regio:

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Met de opdracht [az storage account create](/cli/azure/storage/account#az_storage_account_create) maakt u het opslagaccount. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    Met de cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) maakt u het opslagaccount.

    ---

    Vervang `<STORAGE_NAME>` door een naam die geschikt is voor u en <abbr title="De naam moet uniek zijn voor alle opslagaccounts die worden gebruikt door alle Azure-klanten wereldwijd. U kunt bijvoorbeeld een combinatie van uw persoonlijke naam of bedrijfsnaam, toepassingsnaam en numerieke id gebruiken, zoals in contosobizappstorage20.">uniek in Azure Storage</abbr>. Namen mogen drie tot 24 tekens bevatten en u mag alleen kleine letters gebruiken. Met `Standard_LRS` geeft u een account voor algemeen gebruik op dat wordt [ondersteund door Functions](storage-considerations.md#storage-account-requirements).
    
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
    
    Vervang `<STORAGE_NAME>` door de naam van het account dat u in de vorige stap hebt gebruikt.

    Vervangen `<APP_NAME>` door een <abbr title="Een naam die uniek moet zijn voor alle Azure-klanten wereldwijd. U kunt bijvoorbeeld een combinatie van uw persoonlijke naam of organisatienaam, toepassingsnaam en numerieke id gebruiken, zoals in contoso-bizapp-func-20.">wereldwijd unieke naam die geschikt is voor u</abbr>. De `<APP_NAME>` is ook het standaard DNS-domein voor de functie-app. 
    
    <br/>
    <details>
    <summary><strong>Wat zijn de kosten van de resources die zijn ingericht in Azure?</strong></summary>

    Met deze opdracht maakt u een functie-app die wordt uitgevoerd in de runtime van uw opgegeven taal binnen het [Azure Functions-verbruiksplan](functions-scale.md#overview-of-plans). Dit is gratis voor het gebruik dat u hier maakt. Met deze opdracht wordt in dezelfde resourcegroep ook een gekoppelde instantie van Azure Application Insights ingericht, waarmee u uw functie-app kunt bewaken en logboeken kunt weergeven. Zie [Monitor Azure Functions](functions-monitoring.md) (Azure Functions bewaken) voor meer informatie. Er worden pas kosten in rekening gebracht voor de instantie als u deze activeert.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. Het functieproject implementeren in Azure

Nadat u uw functie-app in Azure hebt gemaakt, kunt u uw lokale **Functions-project** implementeren met behulp van de [opdracht func azure functionapp publish.](functions-run-local.md#project-file-deployment)  

Vervang `<APP_NAME>` in het volgende voorbeeld door de naam van uw app.

```console
func azure functionapp publish <APP_NAME>
```

De `publish` opdracht toont resultaten die vergelijkbaar zijn met de volgende uitvoer (afgekapt voor het gemak):

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

## <a name="7-invoke-the-function-on-azure"></a>7. De functie aanroepen in Azure

Omdat uw functie gebruikmaakt van een HTTP-trigger, roept u deze aan door een HTTP-aanvraag naar de URL ervan te maken in de browser of met een hulpprogramma zoals <abbr title="Een opdrachtregelprogramma voor het genereren van HTTP-aanvragen naar een URL; Zie https://curl.se/">curl</abbr>. 

# <a name="browser"></a>[Browser](#tab/browser)

Kopieer de volledige **Aanroep-URL** die wordt weergegeven in de uitvoer van de opdracht naar een adresbalk van de browser, en&`publish` queryparameter toe aan **name=Functions.** De browser moet vergelijkbare uitvoer weergeven als u de functie lokaal hebt uitgevoerd.

![De uitvoer van de functie die wordt uitgevoerd op Azure in een browser](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Voer [`curl`](https://curl.haxx.se/) uit met de **Aanroep-URL**, en wijs de parameter **&name=Functions toe.** De uitvoer van de opdracht moet de tekst ‘Hallo Functions’ zijn.

![De uitvoer van de functie die wordt uitgevoerd op Azure met behulp van curl](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

### <a name="71-view-real-time-streaming-logs"></a>7.1 Realtime streaminglogboeken weergeven

Voer de volgende opdracht uit om [streaminglogboeken](functions-run-local.md#enable-streaming-logs) in bijna realtime weer te geven in Application Insights in Azure Portal:

```console
func azure functionapp logstream <APP_NAME> --browser
```

Vervang `<APP_NAME>` door de naam van uw functie-app.

Roep in een afzonderlijk terminalvenster of in de browser opnieuw de externe functie aan. In de terminal wordt een uitgebreid logboek weergegeven van de uitvoering van de functie in Azure. 

<br/>

---

## <a name="8-clean-up-resources"></a>8. Resources opschonen

Als u verder gaat met de [volgende stap en](#next-steps) een toevoegt <abbr title="Een manier om een functie te koppelen aan een opslagwachtrij, zodat deze berichten in de wachtrij kan maken. ">Azure Storage wachtrijuitvoerbinding</abbr>, bewaar al uw resources terwijl u verder gaat bouwen op wat u al hebt gedaan.

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
