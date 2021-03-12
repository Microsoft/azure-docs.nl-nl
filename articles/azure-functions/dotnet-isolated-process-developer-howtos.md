---
title: .NET 5-functies ontwikkelen en publiceren met behulp van Azure Functions
description: Meer informatie over het maken en opsporen van C#-functies met behulp van .NET 5,0 en vervolgens het lokale project implementeren op serverloze hosting in Azure Functions.
ms.date: 03/03/2021
ms.topic: how-to
zone_pivot_groups: development-environment-functions
ms.openlocfilehash: 9751d5d354d738a64d6b02e8153464491d78ec14
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201377"
---
# <a name="develop-and-publish-net-5-functions-using-azure-functions"></a>.NET 5-functies ontwikkelen en publiceren met behulp van Azure Functions 

Dit artikel laat u zien hoe u met C#-functies werkt met behulp van .NET 5,0, die out-of-process wordt uitgevoerd vanuit de Azure Functions runtime. U leert hoe u lokale .NET-functies voor geïsoleerde processen kunt maken, opsporen en publiceren naar Azure. In Azure worden deze functies uitgevoerd in een geïsoleerd proces dat .NET 5,0 ondersteunt. Zie voor meer informatie de [hand leiding voor het uitvoeren van functies op .net 5,0 in azure](dotnet-isolated-process-guide.md).

Als u .NET 5,0 niet meer nodig hebt of als u uw functions out-of-process wilt uitvoeren, kunt u in plaats daarvan [een C#-klassen bibliotheek functie maken](functions-create-your-first-function-visual-studio.md).

>[!NOTE]
>Het ontwikkelen van .NET-functies voor geïsoleerde processen in het Azure Portal wordt momenteel niet ondersteund. U moet de Azure CLI-of Visual Studio code-publicatie gebruiken om een functie-app in azure te maken die het uitvoeren van .NET 5,0-apps out-of-process ondersteunt.   

## <a name="prerequisites"></a>Vereisten

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ [.NET SDK 5.0](https://www.microsoft.com/net/download)

+ [Azure functions core tools](functions-run-local.md#v2) versie 3.0.3381 of een latere versie.

+ Versie 2,20 van [Azure cli](/cli/azure/install-azure-cli) of hoger.  
::: zone pivot="development-environment-vscode"
+ [Visual Studio Code](https://code.visualstudio.com/) op een van de [ondersteunde platforms](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  

+ De [C#-extensie](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) voor Visual Studio Code.  

+ De [Azure functions-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voor Visual Studio code, versie 1.3.0 of nieuwer.
::: zone-end
::: zone pivot="development-environment-vs"
+ [Visual Studio 2019](https://azure.microsoft.com/downloads/), met inbegrip van de **Azure Development** -workload.  
.NET geïsoleerd function project sjablonen en-publicatie zijn momenteel niet beschikbaar in Visual Studio.
::: zone-end

## <a name="create-a-local-function-project"></a>Een lokaal functieproject maken

In Azure Functions is een functieproject een container voor een of meer afzonderlijke functies die elk op een bepaalde trigger reageren. Alle functies in een project delen dezelfde lokale configuratie en hostingconfiguratie. In deze sectie maakt u een functieproject dat één functie bevat.

::: zone pivot="development-environment-vs"

>[!NOTE]  
> Op dit moment zijn er geen Visual Studio-project sjablonen die ondersteuning bieden voor het maken van .NET geïsoleerde functie projecten. Dit artikel laat u zien hoe u met behulp van kern Hulpprogramma's uw C#-project kunt maken, dat u vervolgens lokaal en fouten opspoort in Visual Studio.  

::: zone-end

::: zone pivot="development-environment-vscode"  
1. Kies het Azure-pictogram in de activiteitenbalk en selecteer vervolgens in het gedeelte **Azure: Functions** het pictogram **Nieuw project maken...** .

    ![Een nieuw project maken kiezen](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Kies een maplocatie voor de werkruimte van uw project en kies **Selecteren**.

    > [!NOTE]
    > Deze stappen waren bedoeld om buiten een werkruimte te worden voltooid. Selecteer in dit geval geen projectmap die deel uitmaakt van een werkruimte.

1. Geef de volgende informatie op bij de prompts:

    + **Selecteer een taal voor uw functieproject**: Kies `C#`.

    + **Selecteer een .NET runtime**: kiezen `.NET 5 isolated` .

    + **Selecteer een sjabloon voor de eerste functie van uw project**: Kies `HTTP trigger`.

    + **Geef een functienaam op**: Typ `HttpExample`.

    + **Geef een naamruimte op**: Typ `My.Functions`.

    + **Autorisatieniveau**: Kies `Anonymous`, waarmee iedereen uw functie-eindpunt kan aanroepen. Zie [Autorisatiesleutels](functions-bindings-http-webhook-trigger.md#authorization-keys) voor meer informatie over autorisatieniveau.

    + **Selecteer hoe u uw project wilt openen**: Kies `Add to workspace`.

1. Met behulp van deze informatie wordt met Visual Studio Code een Azure Functions-project gegenereerd met een HTTP-trigger. U kunt de lokale projectbestanden weergeven in de Explorer. Zie [Gegenereerde projectbestanden](functions-develop-vs-code.md#generated-project-files) voor meer informatie over bestanden die worden gemaakt.
::: zone-end  
::: zone pivot="development-environment-cli,development-environment-vs"  

1. Voer de `func init` opdracht als volgt uit om een functions-project te maken in een map met de naam *LocalFunctionProj*:  

    ```console
    func init LocalFunctionProj --worker-runtime dotnetisolated
    ```
    
    Als u deze opgeeft, wordt `dotnetisolated` er een project gemaakt dat wordt uitgevoerd op .net 5,0.


1. Navigeer naar de projectmap:

    ```console
    cd LocalFunctionProj
    ```

    Deze map bevat verschillende bestanden voor het project, met inbegrip van de [local.settings.jsop](functions-run-local.md#local-settings-file) en [host.jsop](functions-host-json.md) configuratie bestanden. Omdat *local.settings.json* geheimen kan bevatten die zijn gedownload vanuit Azure, wordt het bestand standaard uitgesloten van broncodebeheer in het bestand *.gitignore*.

1. Voeg een functie toe aan uw project met behulp van de volgende opdracht, waarbij het argument `--name` de unieke naam van de functie is (HttpExample) en het argument `--template` de trigger van de functie (HTTP).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` Hiermee maakt u een HttpExample.cs-code bestand.
::: zone-end  

::: zone pivot="development-environment-vscode"  

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end  
::: zone pivot="development-environment-cli" 

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone-end

::: zone pivot="development-environment-vs"

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Op dit moment kunt u de opdracht uitvoeren `func start` vanuit de hoofdmap van de projectmap om het project C# geïsoleerde functies te compileren en uit te voeren. Als u op dit moment fouten wilt opsporen in de code van de out-of-process-functie in Visual Studio, moet u hand matig een fout opsporingsprogramma koppelen aan het runtime proces van de uitgevoerde functies door de volgende stappen uit te voeren:  

1. Open het project bestand (. csproj) in Visual Studio. U kunt de project code controleren en wijzigen en de gewenste afbreek punten in de code instellen. 

1. Gebruik in de hoofdmap van het hoofd project de volgende opdracht vanaf de terminal of een opdracht prompt om de runtime host te starten:

    ```console
    func start --dotnet-isolated-debug
    ```

    De `--dotnet-isolated-debug` optie geeft aan dat het proces moet wachten op het toevoegen van een fout opsporingsprogramma voordat u doorgaat. Aan het einde van de uitvoer moet er ongeveer als de volgende regels worden weer geven: 
    
    <pre>
    ...
    
    Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample

    For detailed output, run func with --verbose flag.
    [2021-03-09T08:41:41.904Z] Azure Functions .NET Worker (PID: 81720) initialized in debug mode. Waiting for debugger to attach...
    ...
    
    </pre> 

    `PID: XXXXXX`Hiermee wordt de proces-id (PID) aangegeven van het dotnet.exe proces dat de host van de actieve functies is.
 
1. In de uitvoer van Azure Functions runtime noteert u de proces-ID van het hostproces, waaraan u een debugger kunt koppelen. Noteer ook de URL van uw lokale functie.

1. Selecteer in het menu **fout opsporing** in Visual Studio **koppelen aan proces...**, zoek het proces dat overeenkomt met de proces-id en selecteer **bijvoegen**. 
    
    :::image type="content" source="media/dotnet-isolated-process-developer-howtos/attach-to-process.png" alt-text="De debugger koppelen aan het hostproces van functions":::    

    Wanneer het fout opsporingsprogramma is gekoppeld, kunt u de functie code als normaal opsporen.

1. Typ in de adres balk van uw browser uw lokale functie-URL, die er als volgt uitziet en voer de aanvraag uit. 

    <http://localhost:7071/api/HttpExample>

    U ziet een tracerings uitvoer van de aanvraag die is geschreven naar de actieve Terminal. Uitvoering van code wordt gestopt bij onderbrekingen die u in uw functie code hebt ingesteld.

1. Wanneer u klaar bent, gaat u naar de Terminal en drukt u op CTRL + C om het hostproces te stoppen.
 
Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren in Azure.

> [!NOTE]  
> Visual Studio-publicaties zijn momenteel niet beschikbaar voor .NET-toepassingen voor geïsoleerde processen. Nadat u klaar bent met het ontwikkelen van uw project in Visual Studio, moet u de Azure CLI gebruiken om de externe Azure-resources te maken. Vervolgens kunt u Azure Functions Core Tools opnieuw gebruiken vanaf de opdracht regel om uw project naar Azure te publiceren.
::: zone-end

::: zone pivot="development-environment-cli,development-environment-vs" 
## <a name="create-supporting-azure-resources-for-your-function"></a>Ondersteunende Azure-resources maken voor uw functie

Voordat u uw functiecode kunt implementeren in Azure, moet u drie resources maken:

- Een [resource groep](../azure-resource-manager/management/overview.md), een logische container voor gerelateerde resources.
- Een [opslag account](../storage/common/storage-account-create.md), dat wordt gebruikt om de status en andere informatie over uw functies te onderhouden.
- Een functie-app, die de omgeving biedt voor het uitvoeren van uw functiecode. Een functie-app wordt gekoppeld aan uw lokale functieproject en maakt het mogelijk om functies te groeperen als een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren en delen.

Gebruik de volgende opdrachten om deze items te maken.

1. Als u dit nog niet hebt gedaan, meldt u zich aan bij Azure:

    ```azurecli
    az login
    ```

    Met de opdracht [az login](/cli/azure/reference-index#az_login) meldt u zich aan bij uw Azure-account.

1. Maak een resourcegroep met de naam `AzureFunctionsQuickstart-rg` in de regio `westeurope`:

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Met de opdracht [az group create](/cli/azure/group#az_group_create) maakt u een resourcegroep. Over het algemeen maakt u een resourcegroep en resources in een regio bij u in de buurt, waarbij u een beschikbare regio gebruikt die wordt geretourneerd door de opdracht `az account list-locations`.

1. Maak een algemeen opslagaccount in de resourcegroep en regio:

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Met de opdracht [az storage account create](/cli/azure/storage/account#az_storage_account_create) maakt u het opslagaccount. 

    Vervang `<STORAGE_NAME>` in het vorige voorbeeld door een naam die voor u passend is en die uniek is in Azure Storage. Namen mogen drie tot 24 tekens bevatten en u mag alleen kleine letters gebruiken. Met `Standard_LRS` geeft u een account voor algemeen gebruik op dat wordt [ondersteund door Functions](storage-considerations.md#storage-account-requirements).

1. De functie-app maken in Azure:
   
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet-isolated --runtime-version 5.0 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    Maak de functie-app in Azure met behulp van de opdracht [az functionapp create](/cli/azure/functionapp#az_functionapp_create). 
    
    Vervang in het vorige voorbeeld `<STORAGE_NAME>` door de naam van het account dat u in de vorige stap hebt gebruikt en vervang `<APP_NAME>` door een unieke naam die voor u van betekenis is. De `<APP_NAME>` is ook het standaard DNS-domein voor de functie-app. 
    
    Met deze opdracht maakt u een functie-app met .NET 5,0 onder het [verbruiks abonnement van Azure functions](consumption-plan.md). Dit plan moet gratis zijn voor de hoeveelheid gebruik die u in dit artikel maakt. Met deze opdracht wordt ook een gekoppeld Azure-toepassing Insights-exemplaar in dezelfde resource groep ingericht. Gebruik dit exemplaar om uw functie-app te controleren en logboeken te bekijken. Zie [Monitor Azure Functions](functions-monitoring.md) (Azure Functions bewaken) voor meer informatie. Er worden pas kosten in rekening gebracht voor de instantie als u deze activeert.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

::: zone-end

::: zone pivot="development-environment-vscode"

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Het project naar Azure publiceren

In deze sectie maakt u een functie-app en de bijbehorende resources in uw Azure-abonnement en implementeert u vervolgens uw code.

> [!IMPORTANT]
> Als u in een bestaande functie-app publiceert, wordt de inhoud van die app in Azure overschreven.


1. Kies het Azure-pictogram in de activiteitenbalk en selecteer vervolgens in het gedeelte **Azure: In het gebied** Functies kiest u de knop **Implementeren in functie-app ...** .

    ![Uw project naar Azure publiceren](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Geef de volgende informatie op bij de prompts:

    - **Selecteer map**: Kies een map in uw werkruimte of blader naar een map die de functie-app bevat. Deze prompt wordt niet weer gegeven wanneer u al een geldige functie-app hebt geopend.

    - **Selecteer abonnement**: Kies het abonnement dat u wilt gebruiken. Deze vraag wordt niet weer gegeven wanneer u slechts één abonnement hebt.

    - **Selecteer functie-app in Azure**: Kies `- Create new Function App`. (Kies niet de optie `Advanced` die niet in dit artikel wordt behandeld.)
      
    - **Voer een globaal unieke naam in voor de functie-app**: Typ een naam die geldig is in een URL-pad. De naam die u typt, wordt gevalideerd om er zeker van te zijn dat deze uniek is in Azure Functions.
    
    - **Een runtimestack selecteren**: Kies `.NET 5 (non-LTS)`. 
    
    - **Selecteer een locatie voor nieuwe resources**:  Kies voor betere prestaties een [regio](https://azure.microsoft.com/regions/) bij u in de buurt. 
    
    In het systeemvak ziet u de status van afzonderlijke resources die zijn gemaakt in Azure.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Melding van het maken van Azure-resources":::
    
1.  Wanneer dit is voltooid, worden de volgende Azure-resources in uw abonnement gemaakt met behulp van namen op basis van de naam van uw functie-app:
    
    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    Nadat de functie-app is gemaakt en het implementatiepakket is toegepast, wordt er een melding weergegeven. 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. Selecteer in deze melding de optie **Uitvoer weergeven** om de resultaten van het maken en implementeren te bekijken, inclusief de Azure-resources die u hebt gemaakt. Als u de melding mist, selecteert u het belpictogram in de rechterbenedenhoek om deze opnieuw weer te geven.

    ![Volledige melding maken](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

::: zone-end

::: zone pivot="development-environment-cli"  
[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]  
::: zone-end  

::: zone pivot="development-environment-vscode"  
[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]  
::: zone-end  

## <a name="clean-up-resources"></a>Resources opschonen

U hebt resources gemaakt om dit artikel te volt ooien. Deze resources kunnen bij u in rekening worden gebracht, afhankelijk van de [accountstatus](https://azure.microsoft.com/account/) en [serviceprijzen](https://azure.microsoft.com/pricing/). 

::: zone pivot="development-environment-cli"  
Gebruik de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen om te voor komen dat er meer kosten in rekening worden gebracht.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end  

::: zone pivot="development-environment-vscode"  
Gebruik de volgende stappen om de functie-app en de bijbehorende resources te verwijderen om te voor komen dat er verdere kosten in rekening worden gebracht.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]  
::: zone-end  
::: zone pivot="development-environment-vs"   
Gebruik de volgende stappen om de functie-app en de bijbehorende resources te verwijderen om te voor komen dat er verdere kosten in rekening worden gebracht.

1. Vouw in Cloud Explorer uw abonnement uit > **App Services**, klik met de rechtermuisknop op de functie-app en kies **Openen in portal**. 

1. Selecteer op de pagina Functie-app het tabblad **Overzicht**, en selecteer vervolgens de koppeling onder **Resourcegroep**.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Selecteer op de pagina Functie-app de resourcegroep die u wilt verwijderen":::

2. Bekijk op de pagina **Resourcegroep** de lijst met opgenomen resources, en controleer of dit de resources zijn die u wilt verwijderen.
 
3. Selecteer **Resourcegroep verwijderen** en volg de instructies.

   Verwijderen kan enkele minuten duren. Wanneer dit is voltooid, verschijnt een aantal seconden een melding in beeld. U kunt ook het belpictogram boven aan de pagina selecteren om de melding te bekijken.
::: zone-end

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over geïsoleerde .NET-functies](dotnet-isolated-process-guide.md)

