---
title: Een C#-functie maken via de opdrachtregel - Azure Functions
description: Ontdek hoe u een C#-functie maakt vanaf de opdrachtregel en vervolgens het lokale project publiceert op serverloze hosting in Azure Functions.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2d03f8c820e0a8b6a19394649db66f8028b62781
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768791"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Quickstart: Een C#-functie maken in Azure vanaf de opdrachtregel

> [!div class="op_single_selector" title1="Uw functietaal selecteren: "]
> - [C#](create-first-function-cli-csharp-ieux.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [Python](create-first-function-cli-python.md)
> - [TypeScript](create-first-function-cli-typescript.md)

In dit artikel gebruikt u opdrachtregelprogramma's om een op een C#-klassenbibliotheek gebaseerde functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de <abbr title="Een runtime-computingomgeving waarin alle details van de server transparant zijn voor toepassingsontwikkelaars, wat het proces van het implementeren en beheren van code vereenvoudigt.">serverloos</abbr> omgeving van <abbr title="De service van Azure die een goedkope serverloze computingomgeving biedt voor toepassingen.">Azure Functions</abbr>.

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

Er is ook een [Versie op basis van Visual Studio Code](create-first-function-vs-code-csharp.md) van dit artikel.

## <a name="1-prepare-your-environment"></a>1. Uw omgeving voorbereiden

+ Een Azure-account krijgen <abbr title="Het profiel dat factureringsgegevens voor Azure-gebruik bijhoudt.">account</abbr> met een actief <abbr title="De basisorganisatiestructuur waarin u resources in Azure beheert, meestal gekoppeld aan een individu of afdeling binnen een organisatie.">abonnement</abbr>. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ Installeer [.NET Core SDK 3.1](https://www.microsoft.com/net/download)

+ Installeer [Azure Functions Core Tools](functions-run-local.md#v2) versie 3.x.

+ De <abbr title="Een set platformoverschrijdende opdrachtregelprogramma's voor het werken met Azure-resources vanaf uw lokale ontwikkelcomputer, als alternatief voor het gebruik van de Azure Portal.">Azure CLI</abbr> of <abbr title="Een PowerShell-module die opdrachten biedt voor het werken met Azure-resources vanaf uw lokale ontwikkelcomputer, als alternatief voor het gebruik van de Azure Portal.">Azure PowerShell</abbr> voor het maken van Azure-resources:

    + [Azure CLI](/cli/azure/install-azure-cli) versie 2.4 of hoger.

    + [Azure PowerShell](/powershell/azure/install-az-ps) versie 5.0 of hoger.

---

### <a name="2-verify-prerequisites"></a>2. Vereisten controleren

Controleer uw vereisten, die afhankelijk zijn van het gebruik van de Azure CLI of Azure PowerShell voor het maken van Azure-resources:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

+ Voer uit in een terminal- of opdrachtvenster `func --version` om te controleren of de <abbr title="De set opdrachtregelprogramma's voor het werken met Azure Functions op uw lokale computer.">Azure Functions Core Tools</abbr> zijn versie 3.x.

+ **Uitvoeren** `az --version` om te controleren of de Versie van Azure CLI 2.4 of hoger is.

+ **Uitvoeren** `az login` om u aan te melden bij Azure en een actief abonnement te verifiëren.

+ **Uitvoeren** `dotnet --list-sdks` controleren of .NET Core SDK versie 3.1.x is geïnstalleerd

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+**Uitvoeren** `func --version` om te controleren of Azure Functions Core Tools versie 3.x is.

+ **Uitvoeren** `(Get-Module -ListAvailable Az).Version` en controleer versie 5.0 of hoger. 

+ **Uitvoeren** `Connect-AzAccount` om u aan te melden bij Azure en een actief abonnement te verifiëren.

+ **Uitvoeren** `dotnet --list-sdks` controleren of .NET Core SDK versie 3.1.x is geïnstalleerd

---

## <a name="3-create-a-local-function-project"></a>3. Een lokaal functieproject maken

In deze sectie maakt u een lokale <abbr title="Een logische container voor een of meer afzonderlijke functies die samen kunnen worden geïmplementeerd en beheerd.">Azure Functions project</abbr> in C#. Elke functie in het project reageert op een specifieke <abbr title="Een gebeurtenis die de code van de functie aanroept, zoals een HTTP-aanvraag, een wachtrijbericht of een specifiek tijdstip.">activeren</abbr>.

1. Voer de `func init` opdracht uit om een Functions-project te maken in een map met de naam *LocalFunctionProj* met de opgegeven runtime:  

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. **Uitvoeren** 'cd LocalFunctionProj' om naar de te navigeren <abbr title="Deze map bevat verschillende bestanden voor het project,waaronder configuratiebestanden met de naam local.settings.json en host.json. Omdat local.settings.json geheimen kan bevatten die zijn gedownload vanuit Azure, wordt het bestand standaard uitgesloten van broncodebeheer in het bestand .gitignore.">projectmap</abbr>.

    ```console
    cd LocalFunctionProj
    ```
    <br/>

1. Voeg een functie toe aan uw project met behulp van de volgende opdracht:
    
    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    Het `--name` argument is de unieke naam van uw functie (HttpExample).

    Met `--template` het argument wordt de trigger van de functie (HTTP) opgegeven.

    
    <br/>   
    <details>  
    <summary><strong>Optioneel: Code voor HttpExample.cs</strong></summary>  
    
    *HttpExample.cs* bevat een `Run`-methode waarmee aanvraaggegevens worden ontvangen in de `req`-variabele. Dit is een [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) die is gedecoreerd met **HttpTriggerAttribute**, waarmee het gedrag van de trigger wordt gedefinieerd.

    :::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::
        
    Het retourobject is een [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) die een antwoordbericht retourneert als een [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) of een [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Zie [Azure Functions HTTP-triggers en -bindingen](./functions-bindings-http-webhook.md?tabs=csharp) voor meer informatie.  
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

    Als HttpExample niet wordt weergegeven, hebt u de host waarschijnlijk van buiten de hoofdmap van het project gestart. In dat geval gebruikt u <kbd>Ctrl+C om</kbd> de host te stoppen, naar de hoofdmap van het project te navigeren en de vorige opdracht opnieuw uit te voeren.
    </details>

1. Kopieer de URL van uw **HttpExample-functie** uit deze uitvoer naar een browser en plaats de querytekenreeks **?name=<YOUR_NAME>**, waardoor de volledige URL als wordt **http://localhost:7071/api/HttpExample?name=Functions** weergegeven. In de browser moet een bericht als **Hallo functies worden weergegeven:**

    ![Resultaat van de functie lokaal uitgevoerd in de browser](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)


1. Selecteer  <kbd>Ctrl+C en</kbd> kies <kbd>y om</kbd> de functions-host te stoppen.

<br/>

---
    
## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. Ondersteunende Azure-resources maken voor uw functie

Voordat u uw functiecode in Azure kunt implementeren, moet u een <abbr title="Een logische container voor gerelateerde Azure-resources die u als eenheid kunt beheren.">resourcegroep</abbr>A <abbr title="Een account dat al uw Azure Storage-gegevensobjecten bevat. Het opslagaccount biedt een unieke naamruimte voor uw opslaggegevens.">opslagaccount</abbr>, en een <abbr title="De cloudresource die als host fungeert voor serverloze functies in Azure, die de onderliggende rekenomgeving biedt waarin functies worden uitgevoerd.">functie-app</abbr> met behulp van de volgende opdrachten:

1. Als u dit nog niet hebt gedaan, meldt u zich aan bij Azure:

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```


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


    ---

    Het is niet mogelijk om Linux- en Windows-apps in dezelfde resourcegroep te hosten. Als u een bestaande resourcegroep met de naam `AzureFunctionsQuickstart-rg` hebt die een Windows-functie-app of web-app bevat, moet u een andere resourcegroep gebruiken.
    
1. Maak een account voor algemeen Azure Storage in uw resourcegroep en regio:

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```


    ---

    Vervang `<STORAGE_NAME>` door een naam die geschikt is voor u en <abbr title="De naam moet uniek zijn voor alle opslagaccounts die door alle Azure-klanten wereldwijd worden gebruikt. U kunt bijvoorbeeld een combinatie van uw persoonlijke naam of bedrijfsnaam, toepassingsnaam en numerieke id gebruiken, zoals in contosobizappstorage20">uniek in Azure Storage</abbr>. Namen mogen drie tot 24 tekens bevatten en u mag alleen kleine letters gebruiken. Met `Standard_LRS` geeft u een account voor algemeen gebruik op dat wordt [ondersteund door Functions](storage-considerations.md#storage-account-requirements).


1. Maak de functie-app in Azure.
**Vervangen** '<STORAGE_NAME>** met de naam in de vorige stap.
**Vervangen** '<APP_NAME>' met een wereldwijd unieke naam.

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    
    ---
    
    Vervang `<STORAGE_NAME>` door de naam van het account dat u in de vorige stap hebt gebruikt.

    Vervangen `<APP_NAME>` door een <abbr title="Een naam die uniek moet zijn voor alle Azure-klanten wereldwijd. U kunt bijvoorbeeld een combinatie van uw persoonlijke naam of organisatienaam, toepassingsnaam en numerieke id gebruiken, zoals in contoso-bizapp-func-20.">unieke naam</abbr>. De `<APP_NAME>` is ook het standaard DNS-domein voor de functie-app. 

    <br/>
    <details>
    <summary><strong>Wat zijn de kosten van de resources die zijn ingericht in Azure?</strong></summary>

    Met deze opdracht maakt u een functie-app die wordt uitgevoerd in de opgegeven taalruntime onder het [Azure Functions Consumption-abonnement](consumption-plan.md). Dit is gratis voor de hoeveelheid gebruik die u hier maakt. Met deze opdracht wordt in dezelfde resourcegroep ook een gekoppelde instantie van Azure Application Insights ingericht, waarmee u uw functie-app kunt bewaken en logboeken kunt weergeven. Zie [Monitor Azure Functions](functions-monitoring.md) (Azure Functions bewaken) voor meer informatie. Er worden pas kosten in rekening gebracht voor de instantie als u deze activeert.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. Het functieproject implementeren in Azure


**Kopiëren** ' func azure funtionapp publish <APP_NAME> in uw terminal **Vervang door** de naam van `<APP_NAME>` uw app.
**Uitvoeren**

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

## <a name="7-invoke-the-function-on-azure"></a>7. De functie in Azure aanroepen

Kopieer de volledige **Aanroep-URL** die wordt weergegeven in de uitvoer van de `publish` opdracht naar een adresbalk van de browser. **De queryparameter** toevoegen **&name=Functions**. 

![De uitvoer van de functie die wordt uitgevoerd op Azure in een browser](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

<br/>

---

## <a name="8-clean-up-resources"></a>8. Resources opschonen

Als u verder gaat met de [volgende stap en](#next-steps) een uitvoer van Azure Storage wachtrij toevoegt <abbr title="Een declaratieve verbinding tussen een functie en andere resources. Een invoerbinding levert gegevens aan de functie; een uitvoerbinding levert gegevens van de functie aan andere resources.">Bindend</abbr>, bewaar al uw resources terwijl u verder gaat bouwen op wat u al hebt gedaan.

Gebruik anders de volgende opdracht om de resourcegroep en alle bijbehorende resources te verwijderen om te voorkomen dat er verdere kosten in rekening worden gebracht.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

---

<br/>

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verbinding maken met een Azure Storage-wachtrij](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp)
