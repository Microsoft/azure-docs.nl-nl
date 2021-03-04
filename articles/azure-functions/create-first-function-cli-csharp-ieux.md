---
title: Een C#-functie maken via de opdrachtregel - Azure Functions
description: Ontdek hoe u een C#-functie maakt vanaf de opdrachtregel en vervolgens het lokale project publiceert op serverloze hosting in Azure Functions.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a78abea5bcc5925cb2e137d918c7217ae92b118e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044320"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Quickstart: Een C#-functie maken in Azure vanaf de opdrachtregel

> [!div class="op_single_selector" title1="Uw functietaal selecteren: "]
> - [C#](create-first-function-cli-csharp-ieux.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [Python](create-first-function-cli-python.md)
> - [TypeScript](create-first-function-cli-typescript.md)

In dit artikel gebruikt u opdrachtregelprogramma's om een op een C#-klassenbibliotheek gebaseerde functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de <abbr title="Een runtime Computing Environment waarin alle details van de server transparant zijn voor toepassings ontwikkelaars, waardoor het proces van het implementeren en beheren van code wordt vereenvoudigd.">serverloos</abbr> omgeving van <abbr title="De Azure-service die een goedkope computer omgeving biedt voor toepassingen.">Azure Functions</abbr>.

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

Er is ook een [Versie op basis van Visual Studio Code](create-first-function-vs-code-csharp.md) van dit artikel.

## <a name="1-prepare-your-environment"></a>1. uw omgeving voorbereiden

+ Een Azure ophalen <abbr title="Het profiel waarmee facturerings gegevens voor Azure-gebruik worden bijgehouden.">account</abbr> met een actieve <abbr title="De basis organisatie structuur waarin u resources op Azure beheert, meestal gekoppeld aan een individu of afdeling binnen een organisatie.">abonnement</abbr>. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ Installeer [.NET Core SDK 3,1](https://www.microsoft.com/net/download)

+ Installeer [Azure functions core tools](functions-run-local.md#v2) versie 3. x.

+ Ofwel de <abbr title="Een reeks verschillende platform opdracht regel Programma's voor het werken met Azure-resources vanaf uw lokale ontwikkel computer, als alternatief voor het gebruik van de Azure Portal.">Azure CLI</abbr> of <abbr title="Een Power shell-module die opdrachten biedt voor het werken met Azure-resources vanaf uw lokale ontwikkel computer, als alternatief voor het gebruik van de Azure Portal.">Azure PowerShell</abbr> voor het maken van Azure-resources:

    + [Azure CLI](/cli/azure/install-azure-cli) versie 2.4 of hoger.

    + [Azure PowerShell](/powershell/azure/install-az-ps) versie 5.0 of hoger.

---

### <a name="2-verify-prerequisites"></a>2. Controleer de vereisten

Controleer uw vereisten, afhankelijk van of u de Azure CLI of Azure PowerShell gebruikt voor het maken van Azure-resources:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

+ Voer in een Terminal-of opdracht venster uit `func --version` om te controleren of de <abbr title="De set opdracht regel Programma's voor het werken met Azure Functions op uw lokale computer.">Azure Functions Core Tools</abbr> zijn versie 3. x.

+ **Uitvoeren** `az --version` Controleer of de Azure CLI-versie 2,4 of hoger is.

+ **Uitvoeren** `az login` Meld u aan bij Azure en controleer of er een actief abonnement is.

+ **Uitvoeren** `dotnet --list-sdks` controleren of .NET Core SDK versie 3.1. x is geïnstalleerd

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+**Uitvoeren** `func --version` controleren of de Azure Functions Core Tools versie 3. x zijn.

+ **Uitvoeren** `(Get-Module -ListAvailable Az).Version` en controleer versie 5,0 of hoger. 

+ **Uitvoeren** `Connect-AzAccount` Meld u aan bij Azure en controleer of er een actief abonnement is.

+ **Uitvoeren** `dotnet --list-sdks` controleren of .NET Core SDK versie 3.1. x is geïnstalleerd

---

## <a name="3-create-a-local-function-project"></a>3. een lokale functie project maken

In deze sectie maakt u een lokale <abbr title="Een logische container voor een of meer afzonderlijke functies die samen kunnen worden geïmplementeerd en beheerd.">Azure Functions project</abbr> in C#. Elke functie in het project reageert op een specifiek <abbr title="Een gebeurtenis die de code van de functie aanroept, zoals een HTTP-aanvraag, een wachtrij bericht of een specifieke tijd.">activeren</abbr>.

1. Voer de `func init` opdracht uit om een functions-project te maken in een map met de naam *LocalFunctionProj* met de opgegeven runtime:  

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. **Uitvoeren** ' cd LocalFunctionProj ' om naar het <abbr title="Deze map bevat verschillende bestanden voor het project,waaronder configuratiebestanden met de naam local.settings.json en host.json. Omdat local.settings.json geheimen kan bevatten die zijn gedownload vanuit Azure, wordt het bestand standaard uitgesloten van broncodebeheer in het bestand .gitignore.">projectmap</abbr>.

    ```console
    cd LocalFunctionProj
    ```
    <br/>

1. Voeg een functie toe aan uw project met behulp van de volgende opdracht:
    
    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    Het `--name` argument is de unieke naam van uw functie (HttpExample).

    Het `--template` argument geeft de trigger van de functie aan (http).

    
    <br/>   
    <details>  
    <summary><strong>Optioneel: code voor HttpExample.cs</strong></summary>  
    
    *HttpExample.cs* bevat een `Run`-methode waarmee aanvraaggegevens worden ontvangen in de `req`-variabele. Dit is een [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) die is gedecoreerd met **HttpTriggerAttribute**, waarmee het gedrag van de trigger wordt gedefinieerd.

    :::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::
        
    Het retourobject is een [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) die een antwoordbericht retourneert als een [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) of een [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Zie [Azure Functions HTTP-triggers en -bindingen](./functions-bindings-http-webhook.md?tabs=csharp) voor meer informatie.  
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


1. Selecteer  <kbd>CTRL + C</kbd> en kies <kbd>y</kbd> om de functions-host te stoppen.

<br/>

---
    
## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. ondersteunende Azure-resources maken voor uw functie

Voordat u uw functie code kunt implementeren in azure, moet u een maken <abbr title="Een logische container voor gerelateerde Azure-resources die u kunt beheren als een eenheid.">resourcegroep</abbr>, een <abbr title="Een account dat al uw Azure Storage-gegevens objecten bevat. Het opslag account biedt een unieke naam ruimte voor uw opslag gegevens.">opslagaccount</abbr>, en een <abbr title="De Cloud resource die fungeert als host voor serverloze functies in azure, die de onderliggende reken omgeving biedt waarin functies worden uitgevoerd.">functie-app</abbr> met behulp van de volgende opdrachten:

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

    Met de opdracht [az group create](/cli/azure/group#az-group-create) maakt u een resourcegroep. In het algemeen maakt u de resource groep en-resources in een <abbr title="Een geografische verwijzing naar een specifiek Azure-Data Center waarin resources worden toegewezen.">regio</abbr> in de buurt van een beschik bare regio die wordt geretourneerd door de `az account list-locations` opdracht.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```


    ---

    Het is niet mogelijk om Linux- en Windows-apps in dezelfde resourcegroep te hosten. Als u een bestaande resourcegroep met de naam `AzureFunctionsQuickstart-rg` hebt die een Windows-functie-app of web-app bevat, moet u een andere resourcegroep gebruiken.
    
1. Maak een Azure Storage account voor algemeen gebruik in uw resource groep en regio:

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```


    ---

    Vervang door `<STORAGE_NAME>` een naam die geschikt is voor u en <abbr title="De naam moet uniek zijn voor alle opslag accounts die worden gebruikt door alle Azure-klanten wereld wijd. U kunt bijvoorbeeld een combi natie van uw persoonlijke of bedrijfs naam, toepassings naam en een numerieke id gebruiken, zoals in contosobizappstorage20">uniek in Azure Storage</abbr>. Namen mogen drie tot 24 tekens bevatten en u mag alleen kleine letters gebruiken. Met `Standard_LRS` geeft u een account voor algemeen gebruik op dat wordt [ondersteund door Functions](storage-considerations.md#storage-account-requirements).


1. Maak de functie-app in Azure.
**Vervangen** ' <STORAGE_NAME> * * met de naam in de vorige stap.
**Vervangen** <APP_NAME> met een wereld wijd unieke naam.

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    
    ---
    
    Vervang door `<STORAGE_NAME>` de naam van het account dat u in de vorige stap hebt gebruikt.

    Vervangen `<APP_NAME>` door een <abbr title="Een naam die uniek moet zijn voor alle Azure-klanten wereld wijd. U kunt bijvoorbeeld een combi natie van uw persoonlijke of organisatie naam, toepassings naam en een numerieke id gebruiken, zoals in contoso-bizapp-func-20.">unieke naam</abbr>. De `<APP_NAME>` is ook het standaard DNS-domein voor de functie-app. 

    <br/>
    <details>
    <summary><strong>Wat zijn de kosten van de resources die zijn ingericht in azure?</strong></summary>

    Met deze opdracht maakt u een functie-app die wordt uitgevoerd in uw opgegeven taal runtime onder het [verbruiks abonnement van Azure functions](consumption-plan.md). Dit is gratis voor de hoeveelheid gebruik die u hier maakt. Met deze opdracht wordt in dezelfde resourcegroep ook een gekoppelde instantie van Azure Application Insights ingericht, waarmee u uw functie-app kunt bewaken en logboeken kunt weergeven. Zie [Monitor Azure Functions](functions-monitoring.md) (Azure Functions bewaken) voor meer informatie. Er worden pas kosten in rekening gebracht voor de instantie als u deze activeert.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. Implementeer het functie project in azure


**Kopiëren** de functie func Azure funtionapp Publish <APP_NAME> in uw terminal wordt **vervangen** door `<APP_NAME>` de naam van uw app.
**Uitvoeren**

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

Kopieer de volledige **invoke-URL** die wordt weer gegeven in de uitvoer van de `publish` opdracht naar de adres balk van een browser. **Voeg** de query parameter **&name = functions** toe. 

![De uitvoer van de functie die wordt uitgevoerd op Azure in een browser](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

<br/>

---

## <a name="8-clean-up-resources"></a>8. Resources opschonen

Als u doorgaat met de [volgende stap](#next-steps) en een Azure Storage wachtrij-uitvoer toevoegt <abbr title="Een declaratieve verbinding tussen een functie en andere resources. Een invoer binding biedt gegevens voor de functie. een uitvoer binding biedt gegevens van de functie aan andere resources.">dwingen</abbr>, zorgt u ervoor dat al uw resources op de juiste plaats staan, zoals u gaat bouwen op wat u al hebt gedaan.

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
