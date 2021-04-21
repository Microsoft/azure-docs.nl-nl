---
title: Instellingen voor functie-apps configureren in Azure Functions
description: Meer informatie over het configureren van instellingen voor functie-apps in Azure Functions.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 04/13/2020
ms.custom: cc996988-fb4f-47, devx-track-azurecli
ms.openlocfilehash: ed87a5a744defb15d4a898aeabdce5267b7431fe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775651"
---
# <a name="manage-your-function-app"></a>Uw functie-app beheren 

In Azure Functions biedt een functie-app de uitvoeringscontext voor uw afzonderlijke functies. Het gedrag van functie-apps is van toepassing op alle functies die worden gehost door een bepaalde functie-app. Alle functies in een functie-app moeten van dezelfde taal [zijn.](supported-languages.md) 

Afzonderlijke functies in een functie-app worden samen geïmplementeerd en samen geschaald. Alle functies in dezelfde functie-app delen resources per exemplaar terwijl de functie-app wordt geschaald. 

Verbindingsreeksen, omgevingsvariabelen en andere toepassingsinstellingen worden afzonderlijk gedefinieerd voor elke functie-app. Alle gegevens die moeten worden gedeeld tussen functie-apps, moeten extern worden opgeslagen in een persistente opslag.

## <a name="get-started-in-the-azure-portal"></a>Aan de slag in de Azure-portal

1. Ga eerst naar de Azure Portal [en] meld u aan bij uw Azure-account. Voer in de zoekbalk boven aan de portal de naam van uw functie-app in en selecteer deze in de lijst. 

2. Selecteer **configuratie** onder Instellingen in het **linkerdeelvenster.**

    :::image type="content" source="./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png" alt-text="Overzicht van functie-apps in de Azure Portal":::

U kunt navigeren naar alles wat u nodig hebt om uw functie-app te beheren vanaf de overzichtspagina, met name de **[Toepassingsinstellingen](#settings)** en **[Platformfuncties.](#platform-features)**

## <a name="work-with-application-settings"></a><a name="settings"></a>Werken met toepassingsinstellingen

Toepassingsinstellingen kunnen worden beheerd vanuit [de Azure Portal](functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) en met behulp van de [Azure CLI](functions-how-to-use-azure-function-app-settings.md?tabs=azurecli#settings) en [Azure PowerShell](functions-how-to-use-azure-function-app-settings.md?tabs=powershell#settings). U kunt ook toepassingsinstellingen beheren [vanuit Visual Studio Code](functions-develop-vs-code.md#application-settings-in-azure) en vanuit [Visual Studio](functions-develop-vs.md#function-app-settings). 

Deze instellingen worden versleuteld opgeslagen. Zie Beveiliging van toepassingsinstellingen [voor meer informatie.](security-concepts.md#application-settings)

# <a name="portal"></a>[Portal](#tab/portal)

Zie Aan de slag in de Azure Portal om de [toepassingsinstellingen te Azure Portal.](#get-started-in-the-azure-portal) 

Het **tabblad Toepassingsinstellingen** onderhoudt instellingen die worden gebruikt door uw functie-app. U moet Waarden **tonen selecteren om** de waarden in de portal weer te geven. Als u een instelling wilt toevoegen in de portal, selecteert u **Nieuwe toepassingsinstelling** en voegt u het nieuwe sleutel-waardepaar toe.

![Instellingen voor functie-apps in Azure Portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli)

De [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_list) opdracht retourneert de bestaande toepassingsinstellingen, zoals in het volgende voorbeeld:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

Met [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) de opdracht wordt een toepassingsinstelling toegevoegd of bijgewerkt. In het volgende voorbeeld wordt een instelling gemaakt met een sleutel met `CUSTOM_FUNCTION_APP_SETTING` de naam en een waarde van `12345` :


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

De [`Get-AzFunctionAppSetting`](/powershell/module/az.functions/get-azfunctionappsetting) cmdlet retourneert de bestaande toepassingsinstellingen, zoals in het volgende voorbeeld: 

```azurepowershell-interactive
Get-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME>
```

Met [`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting) de opdracht wordt een toepassingsinstelling toegevoegd of bijgewerkt. In het volgende voorbeeld wordt een instelling gemaakt met een sleutel met `CUSTOM_FUNCTION_APP_SETTING` de naam en een waarde van `12345` :

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"CUSTOM_FUNCTION_APP_SETTING" = "12345"}
```

---

### <a name="use-application-settings"></a>Toepassingsinstellingen gebruiken

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Wanneer u een functie-app lokaal ontwikkelt, moet u lokale kopieën van deze waarden onderhouden in de local.settings.jsop het projectbestand. Zie Lokaal [instellingenbestand voor meer informatie.](functions-run-local.md#local-settings-file)

## <a name="hosting-plan-type"></a>Type hostingplan

Wanneer u een functie-app maakt, maakt u ook een hostingplan waarin de app wordt uitgevoerd. Een plan kan een of meer functie-apps hebben. De functionaliteit, schaalbaarheid en prijzen van uw functies zijn afhankelijk van het type abonnement. Zie hostingopties voor [Azure Functions meer informatie.](functions-scale.md)

U kunt het type plan dat door uw functie-app wordt gebruikt, bepalen vanuit de Azure Portal of met behulp van de Azure CLI of Azure PowerShell API's. 

De volgende waarden geven het plantype aan:

| Plantype | Portal | Azure CLI/PowerShell |
| --- | --- | --- |
| [Verbruik](consumption-plan.md) | **Verbruik** | `Dynamic` |
| [Premium](functions-premium-plan.md) | **ElasticPremium** | `ElasticPremium` |
| [Toegewezen (App Service)](dedicated-plan.md) | Verschillende | Verschillende |

# <a name="portal"></a>[Portal](#tab/portal)

Als u het type plan wilt bepalen dat door uw  functie-app wordt gebruikt, ziet **u App Service plan** op het tabblad Overzicht voor de functie-app in [Azure Portal](https://portal.azure.com). Als u de prijscategorie wilt zien, selecteert u de **naam van App Service abonnement** en selecteert u vervolgens **Eigenschappen** in het linkerdeelvenster.

![Schaalplan weergeven in de portal](./media/functions-scale/function-app-overview-portal.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli)

Voer de volgende Azure CLI-opdracht uit om het type hostingplan op te halen:

```azurecli-interactive
functionApp=<FUNCTION_APP_NAME>
resourceGroup=FunctionMonitoringExamples
appServicePlanId=$(az functionapp show --name $functionApp --resource-group $resourceGroup --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv

```  

Vervang in het vorige voorbeeld en door respectievelijk de namen van de resourcegroep en `<RESOURCE_GROUP>` `<FUNCTION_APP_NAME>` functie-app. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Voer de volgende opdracht Azure PowerShell om het type hostingplan op te halen:

```azurepowershell-interactive
$FunctionApp = '<FUNCTION_APP_NAME>'
$ResourceGroup = '<RESOURCE_GROUP>'

$PlanID = (Get-AzFunctionApp -ResourceGroupName $ResourceGroup -Name $FunctionApp).AppServicePlan
(Get-AzFunctionAppPlan -Name $PlanID -ResourceGroupName $ResourceGroup).SkuTier
```
Vervang in het vorige voorbeeld en door respectievelijk de namen van de resourcegroep en `<RESOURCE_GROUP>` `<FUNCTION_APP_NAME>` functie-app. 

---

## <a name="plan-migration"></a>Migratie plannen

U kunt Azure CLI-opdrachten gebruiken om een functie-app te migreren tussen een Verbruiksplan en een Premium-abonnement in Windows. De specifieke opdrachten zijn afhankelijk van de richting van de migratie. Directe migratie naar een Dedicated-abonnement (App Service) wordt momenteel niet ondersteund.

Deze migratie wordt niet ondersteund in Linux.

### <a name="consumption-to-premium"></a>Verbruik naar Premium

Gebruik de volgende procedure om te migreren van een Verbruiksplan naar een Premium-abonnement in Windows:

1. Voer de volgende opdracht uit om een nieuw App Service-abonnement (Elastic Premium) te maken in dezelfde regio en resourcegroep als uw bestaande functie-app.  

    ```azurecli-interactive
    az functionapp plan create --name <NEW_PREMIUM_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP> --location <REGION> --sku EP1
    ```

1. Voer de volgende opdracht uit om de bestaande functie-app te migreren naar het nieuwe Premium-abonnement

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_PREMIUM_PLAN>
    ```

1. Als u uw vorige verbruiksfunctie-app-abonnement niet meer nodig hebt, verwijdert u uw oorspronkelijke functie-app-plan nadat u hebt bevestigd dat u naar het nieuwe abonnement bent gemigreerd. Voer de volgende opdracht uit om een lijst met alle verbruiksplannen in uw resourcegroep op te halen.

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='Y'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

    U kunt het plan veilig verwijderen met nul sites. Dit is de site van waaruit u hebt gemigreerd.

1. Voer de volgende opdracht uit om het verbruiksplan te verwijderen dat u hebt gemigreerd.

    ```azurecli-interactive
    az functionapp plan delete --name <CONSUMPTION_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

### <a name="premium-to-consumption"></a>Premium naar verbruik

Gebruik de volgende procedure om te migreren van een Premium-abonnement naar een verbruiksplan in Windows:

1. Voer de volgende opdracht uit om een nieuwe functie-app (Verbruik) te maken in dezelfde regio en resourcegroep als uw bestaande functie-app. Met deze opdracht maakt u ook een nieuw verbruiksplan waarin de functie-app wordt uitgevoerd.

    ```azurecli-interactive
    az functionapp create --resource-group <MY_RESOURCE_GROUP> --name <NEW_CONSUMPTION_APP_NAME> --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --storage-account <STORAGE_NAME>
    ```

1. Voer de volgende opdracht uit om de bestaande functie-app te migreren naar het nieuwe verbruiksplan.

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_CONSUMPTION_PLAN>
    ```

1. Verwijder de functie-app die u in stap 1 hebt gemaakt, omdat u alleen het plan nodig hebt dat is gemaakt om de bestaande functie-app uit te voeren.

    ```azurecli-interactive
    az functionapp delete --name <NEW_CONSUMPTION_APP_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

1. Als u uw vorige Premium-functie-app-abonnement niet meer nodig hebt, verwijdert u uw oorspronkelijke functie-app-plan nadat u hebt bevestigd dat u naar het nieuwe abonnement bent gemigreerd. Als het abonnement niet wordt verwijderd, worden er nog steeds kosten in rekening gebracht voor het Premium-abonnement. Voer de volgende opdracht uit om een lijst met alle Premium-abonnementen in uw resourcegroep op te halen.

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='EP'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

1. Voer de volgende opdracht uit om het Premium-abonnement te verwijderen dat u hebt gemigreerd.

    ```azurecli-interactive
    az functionapp plan delete --name <PREMIUM_PLAN> --resource-group <MY_RESOURCE_GROUP>
    ```

## <a name="platform-features"></a>Platformfuncties

Functie-apps worden uitgevoerd in en worden onderhouden door het Azure App Service platform. Als zodanig hebben uw functie-apps toegang tot de meeste functies van het belangrijkste webhostingplatform van Azure. In het linkerdeelvenster krijgt u toegang tot de vele functies van App Service platform die u in uw functie-apps kunt gebruiken. 

> [!NOTE]
> Niet alle App Service zijn beschikbaar wanneer een functie-app wordt uitgevoerd op het hostingplan Verbruik.

De rest van dit artikel richt zich op de volgende App Service functies in de Azure Portal die nuttig zijn voor Functions:

+ [App Service-editor](#editor)
+ [Console](#console)
+ [Geavanceerde hulpprogramma's (Kudu)](#kudu)
+ [Implementatieopties](#deployment)
+ [CORS](#cors)
+ [Verificatie](#auth)

Zie Configure Azure App Service Settings (Instellingen configureren) App Service meer informatie over het werken [App Service instellingen.](../app-service/configure-common.md)

### <a name="app-service-editor"></a><a name="editor"></a>App Service-editor

![De App Service-editor](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

De App Service-editor is een geavanceerde in-portal-editor die u kunt gebruiken om JSON-configuratiebestanden en codebestanden gelijk te wijzigen. Als u deze optie kiest, wordt een afzonderlijk browsertabblad met een eenvoudige editor geopend. Hiermee kunt u integreren met de Git-opslagplaats, code uitvoeren en fouten opsporen, en instellingen voor functie-apps wijzigen. Deze editor biedt een verbeterde ontwikkelomgeving voor uw functies vergeleken met de ingebouwde functie-editor.  

U wordt aangeraden uw functies te ontwikkelen op uw lokale computer. Wanneer u lokaal ontwikkelt en publiceert naar Azure, zijn uw projectbestanden alleen-lezen in de portal. Zie Code and test Azure Functions locally (Code Azure Functions [testen) voor meer informatie.](functions-develop-local.md)

### <a name="console"></a><a name="console"></a>Console

![Console van functie-app](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

De console in de portal is een ideaal hulpprogramma voor ontwikkelaars wanneer u liever vanaf de opdrachtregel met uw functie-app werkt. Veelvoorkomende opdrachten omvatten het maken en navigeren van mappen en bestanden, evenals het uitvoeren van batchbestanden en scripts. 

Wanneer u lokaal ontwikkelt, raden we u aan de [Azure Functions Core Tools](functions-run-local.md) en de Azure CLI [te gebruiken.]

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>Geavanceerde hulpprogramma's (Kudu)

![Kudu configureren](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

De geavanceerde hulpprogramma's App Service (ook wel kudu genoemd) bieden toegang tot geavanceerde beheerfuncties van uw functie-app. Vanuit Kudu beheert u systeemgegevens, app-instellingen, omgevingsvariabelen, site-extensies, HTTP-headers en servervariabelen. U kunt **Kudu** ook starten door naar het SCM-eindpunt voor uw functie-app te bladeren, zoals `https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment-center"></a><a name="deployment"></a>Implementatiecenter

Wanneer u een broncodebeheeroplossing gebruikt om uw functiecode te ontwikkelen en te onderhouden, kunt u met Deployment Center vanuit broncodebeheer bouwen en implementeren. Uw project wordt gebouwd en geïmplementeerd in Azure wanneer u updates aan het maken bent. Zie Implementatietechnologieën [in](functions-deployment-technologies.md)Azure Functions.

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>Cross-origin-resources delen

Om te voorkomen dat schadelijke code op de client wordt uitgevoerd, blokkeren moderne browsers aanvragen van webtoepassingen naar resources die worden uitgevoerd in een afzonderlijk domein. [Met Cross-Origin Resource Sharing (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) kan een header declareeren welke oorsprongen eindpunten in uw `Access-Control-Allow-Origin` functie-app mogen aanroepen.

#### <a name="portal"></a>Portal

Wanneer u de lijst **Toegestane oorsprongen** voor uw functie-app configureert, wordt de header automatisch toegevoegd aan alle antwoorden van `Access-Control-Allow-Origin` HTTP-eindpunten in uw functie-app. 

![De CORS-lijst van de functie-app configureren](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Wanneer het jokerteken ( `*` ) wordt gebruikt, worden alle andere domeinen genegeerd. 

Gebruik de [`az functionapp cors add`](/cli/azure/functionapp/cors#az_functionapp_cors_add) opdracht om een domein toe te voegen aan de lijst met toegestane oorsprongen. In het volgende voorbeeld wordt het contoso.com toegevoegd:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Gebruik de [`az functionapp cors show`](/cli/azure/functionapp/cors#az_functionapp_cors_show) opdracht om de huidige toegestane oorsprongen weer te geven.

### <a name="authentication"></a><a name="auth"></a>Verificatie

![Verificatie configureren voor een functie-app](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Wanneer functies een HTTP-trigger gebruiken, kunt u vereisen dat aanroepen eerst worden geverifieerd. App Service ondersteunt Azure Active Directory verificatie en aanmelding met sociale providers, zoals Facebook, Microsoft en Twitter. Zie overzicht van Azure App Service verificatie voor [meer informatie over het configureren van specifieke verificatieproviders.](../app-service/overview-authentication-authorization.md) 


## <a name="next-steps"></a>Volgende stappen

+ [Instellingen Azure App Service configureren](../app-service/configure-common.md)
+ [Continue implementatie voor Azure Functions](functions-continuous-deployment.md)

[Azure-CLI]: /cli/azure/
[Azure-portal]: https://portal.azure.com
