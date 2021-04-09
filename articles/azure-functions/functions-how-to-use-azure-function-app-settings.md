---
title: Instellingen voor de functie-app configureren in Azure Functions
description: Meer informatie over het configureren van instellingen voor de functie-app in Azure Functions.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 04/13/2020
ms.custom: cc996988-fb4f-47, devx-track-azurecli
ms.openlocfilehash: 5080d16a7b14506b24e07e2ee4ba862c645f83a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98875446"
---
# <a name="manage-your-function-app"></a>Uw functie-app beheren 

In Azure Functions biedt een functie-app de uitvoerings context voor uw afzonderlijke functies. Het gedrag van functie-apps is van toepassing op alle functies die worden gehost door een bepaalde functie-app. Alle functies in een functie-app moeten van dezelfde [taal](supported-languages.md)zijn. 

Afzonderlijke functies in een functie-app worden samen geïmplementeerd en worden tegelijk geschaald. Alle functies in dezelfde functie-app delen resources per exemplaar, zoals de functie-app wordt geschaald. 

Verbindings reeksen, omgevings variabelen en andere toepassings instellingen worden afzonderlijk voor elke functie-app gedefinieerd. Gegevens die moeten worden gedeeld tussen functie-apps, moeten extern worden opgeslagen in een persistente opslag.

## <a name="get-started-in-the-azure-portal"></a>Aan de slag in de Azure-portal

1. Als u wilt beginnen, gaat u naar de [Azure Portal] en meldt u zich aan bij uw Azure-account. Voer in de zoek balk boven in de Portal de naam van uw functie-app in en selecteer deze in de lijst. 

2. Selecteer **configuratie** onder **instellingen** in het linkerdeel venster.

    :::image type="content" source="./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png" alt-text="Overzicht van functie-app in de Azure Portal":::

U kunt navigeren naar alles wat u nodig hebt om uw functie-app te beheren op de pagina overzicht, met name de **[Toepassings instellingen](#settings)** en **[platform functies](#platform-features)**.

## <a name="work-with-application-settings"></a><a name="settings"></a>Werken met toepassings instellingen

Toepassings instellingen kunnen worden beheerd vanuit de [Azure Portal](functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) en met behulp van de [Azure cli](functions-how-to-use-azure-function-app-settings.md?tabs=azurecli#settings) en [Azure PowerShell](functions-how-to-use-azure-function-app-settings.md?tabs=powershell#settings). U kunt ook toepassings instellingen beheren vanuit [Visual Studio code](functions-develop-vs-code.md#application-settings-in-azure) en [Visual Studio](functions-develop-vs.md#function-app-settings). 

Deze instellingen worden versleuteld opgeslagen. Zie [beveiliging van toepassings instellingen](security-concepts.md#application-settings)voor meer informatie.

# <a name="portal"></a>[Portal](#tab/portal)

Zie aan [de slag in de Azure Portal](#get-started-in-the-azure-portal)om de toepassings instellingen te vinden. 

Op het tabblad **Toepassings instellingen** worden instellingen onderhouden die worden gebruikt door de functie-app. U moet **waarden weer geven** selecteren om de waarden in de portal weer te geven. Als u een instelling wilt toevoegen in de portal, selecteert u **nieuwe toepassings instelling** en voegt u het nieuwe sleutel-waardepaar toe.

![Functie-app-instellingen in de Azure Portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli)

De [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) opdracht retourneert de bestaande toepassings instellingen, zoals in het volgende voor beeld:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

Met de [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) opdracht wordt een toepassings instelling toegevoegd of bijgewerkt. In het volgende voor beeld wordt een instelling gemaakt met een sleutel met de naam `CUSTOM_FUNCTION_APP_SETTING` en een waarde van `12345` :


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

De [`Get-AzFunctionAppSetting`](/powershell/module/az.functions/get-azfunctionappsetting) cmdlet retourneert de bestaande toepassings instellingen, zoals in het volgende voor beeld: 

```azurepowershell-interactive
Get-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME>
```

Met de [`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting) opdracht wordt een toepassings instelling toegevoegd of bijgewerkt. In het volgende voor beeld wordt een instelling gemaakt met een sleutel met de naam `CUSTOM_FUNCTION_APP_SETTING` en een waarde van `12345` :

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"CUSTOM_FUNCTION_APP_SETTING" = "12345"}
```

---

### <a name="use-application-settings"></a>Toepassings instellingen gebruiken

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Wanneer u een functie-app lokaal ontwikkelt, moet u lokale kopieën van deze waarden in de local.settings.jsvan het project bestand behouden. Zie [Local Settings file](functions-run-local.md#local-settings-file)(Engelstalig) voor meer informatie.

## <a name="hosting-plan-type"></a>Type hosting plan

Wanneer u een functie-app maakt, maakt u ook een hosting plan waarin de app wordt uitgevoerd. Een plan kan een of meer functie-apps hebben. De functionaliteit, schaal baarheid en prijs van uw functies zijn afhankelijk van het type abonnement. Zie [Azure functions hosting opties](functions-scale.md)voor meer informatie.

U kunt bepalen welk type plan wordt gebruikt door uw functie-app vanuit de Azure Portal, of door gebruik te maken van de Azure CLI-of Azure PowerShell-Api's. 

Met de volgende waarden wordt het type abonnement aangegeven:

| Plantype | Portal | Azure CLI/Power shell |
| --- | --- | --- |
| [Verbruik](consumption-plan.md) | **Verbruik** | `Dynamic` |
| [Premium](functions-premium-plan.md) | **ElasticPremium** | `ElasticPremium` |
| [Toegewezen (App Service)](dedicated-plan.md) | Sommige | Sommige |

# <a name="portal"></a>[Portal](#tab/portal)

Zie **app service plan** op het tabblad **overzicht** voor de functie-app in de [Azure Portal](https://portal.azure.com)om het type abonnement te bepalen dat door uw functie-app wordt gebruikt. Als u de prijs categorie wilt zien, selecteert u de naam van het **app service plan** en selecteert u vervolgens **Eigenschappen** in het linkerdeel venster.

![Schaal plan weer geven in de portal](./media/functions-scale/function-app-overview-portal.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli)

Voer de volgende Azure CLI-opdracht uit om het type hosting plan op te halen:

```azurecli-interactive
functionApp=<FUNCTION_APP_NAME>
resourceGroup=FunctionMonitoringExamples
appServicePlanId=$(az functionapp show --name $functionApp --resource-group $resourceGroup --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv

```  

Vervang in het vorige voor beeld `<RESOURCE_GROUP>` en `<FUNCTION_APP_NAME>` met de namen van de resource groep en de functie-app, respectievelijk. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Voer de volgende Azure PowerShell opdracht uit om het type hosting abonnement te verkrijgen:

```azurepowershell-interactive
$FunctionApp = '<FUNCTION_APP_NAME>'
$ResourceGroup = '<RESOURCE_GROUP>'

$PlanID = (Get-AzFunctionApp -ResourceGroupName $ResourceGroup -Name $FunctionApp).AppServicePlan
(Get-AzFunctionAppPlan -Name $PlanID -ResourceGroupName $ResourceGroup).SkuTier
```
Vervang in het vorige voor beeld `<RESOURCE_GROUP>` en `<FUNCTION_APP_NAME>` met de namen van de resource groep en de functie-app, respectievelijk. 

---

## <a name="plan-migration"></a>Migratie plannen

U kunt Azure CLI-opdrachten gebruiken voor het migreren van een functie-app tussen een verbruiks abonnement en een Premium-abonnement op Windows. De specifieke opdrachten zijn afhankelijk van de richting van de migratie. Direct migreren naar een speciaal (App Service)-abonnement wordt momenteel niet ondersteund.

Deze migratie wordt niet ondersteund in Linux.

### <a name="consumption-to-premium"></a>Verbruik voor Premium

Gebruik de volgende procedure om vanuit een verbruiks plan te migreren naar een Premium-abonnement op Windows:

1. Voer de volgende opdracht uit om een nieuw App Service plan (elastisch Premium) te maken in dezelfde regio en resource groep als uw bestaande functie-app.  

    ```azurecli-interactive
    az functionapp plan create --name <NEW_PREMIUM_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP> --location <REGION> --sku EP1
    ```

1. Voer de volgende opdracht uit om de bestaande functie-app te migreren naar het nieuwe Premium-plan

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_PREMIUM_PLAN>
    ```

1. Als u uw vorige app-abonnement voor verbruiks functies niet meer nodig hebt, verwijdert u het abonnement voor de oorspronkelijke functie-app nadat u hebt gecontroleerd of u de migratie naar de nieuwe hebt gemigreerd. Voer de volgende opdracht uit om een lijst met alle verbruiks plannen in uw resource groep op te halen.

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='Y'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

    U kunt het plan met nul sites veilig verwijderen. Dit is het abonnement dat u hebt gemigreerd.

1. Voer de volgende opdracht uit om het verbruiks plan dat u hebt gemigreerd, te verwijderen.

    ```azurecli-interactive
    az functionapp plan delete --name <CONSUMPTION_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

### <a name="premium-to-consumption"></a>Premium-naar-verbruik

Gebruik de volgende procedure om te migreren van een Premium-abonnement naar een verbruiks abonnement op Windows:

1. Voer de volgende opdracht uit om een nieuwe functie-app (verbruik) in dezelfde regio en resource groep te maken als uw bestaande functie-app. Met deze opdracht wordt ook een nieuw verbruiks plan gemaakt waarin de functie-app wordt uitgevoerd.

    ```azurecli-interactive
    az functionapp create --resource-group <MY_RESOURCE_GROUP> --name <NEW_CONSUMPTION_APP_NAME> --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --storage-account <STORAGE_NAME>
    ```

1. Voer de volgende opdracht uit om de bestaande functie-app te migreren naar het nieuwe verbruiks plan.

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_CONSUMPTION_PLAN>
    ```

1. Verwijder de functie-app die u in stap 1 hebt gemaakt, omdat u alleen het plan nodig hebt dat is gemaakt om de bestaande functie-app uit te voeren.

    ```azurecli-interactive
    az functionapp delete --name <NEW_CONSUMPTION_APP_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

1. Als u het abonnement van uw vorige Premium-functie-app niet meer nodig hebt, verwijdert u het abonnement voor de oorspronkelijke functie-app nadat u hebt gecontroleerd of u de migratie naar de nieuwe hebt gemigreerd. Als het plan niet wordt verwijderd, worden er nog steeds kosten in rekening gebracht voor het Premium-abonnement. Voer de volgende opdracht uit om een lijst met alle Premium-abonnementen in de resource groep op te halen.

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='EP'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

1. Voer de volgende opdracht uit om het Premium-abonnement dat u hebt gemigreerd, te verwijderen.

    ```azurecli-interactive
    az functionapp plan delete --name <PREMIUM_PLAN> --resource-group <MY_RESOURCE_GROUP>
    ```

## <a name="platform-features"></a>Platform functies

Functie-apps worden uitgevoerd in en worden beheerd door, het Azure App Service-platform. Als zodanig hebben uw functie-apps toegang tot de meeste functies van het hoofd platform voor webhosting van Azure. In het linkerdeel venster krijgt u toegang tot de vele functies van het App Service-platform dat u kunt gebruiken in uw functie-apps. 

> [!NOTE]
> Niet alle App Service-functies zijn beschikbaar wanneer een functie-app wordt uitgevoerd op het verbruiks hosting plan.

De rest van dit artikel richt zich op de volgende App Service functies in de Azure Portal die nuttig zijn voor functies:

+ [App Service editor](#editor)
+ [Console](#console)
+ [Geavanceerde hulp middelen (kudu)](#kudu)
+ [Implementatieopties](#deployment)
+ [CORS](#cors)
+ [Verificatie](#auth)

Zie [Azure app service-instellingen configureren](../app-service/configure-common.md)voor meer informatie over het werken met app service-instellingen.

### <a name="app-service-editor"></a><a name="editor"></a>App Service editor

![De App Service editor](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

De App Service editor is een geavanceerde editor in de portal die u kunt gebruiken om de JSON-configuratie bestanden en code bestanden te wijzigen. Als u deze optie kiest, wordt er een afzonderlijk browser tabblad met een basis editor geopend. Op deze manier kunt u integreren met de Git-opslag plaats, de code voor uitvoering en fout opsporing en de instellingen van de functie-app wijzigen. Deze editor biedt een verbeterde ontwikkel omgeving voor uw functies vergeleken met de ingebouwde functie-editor.  

U wordt aangeraden uw functies op de lokale computer te ontwikkelen. Wanneer u lokaal ontwikkelt en publiceert naar Azure, zijn uw project bestanden alleen-lezen in de portal. Zie [code en test Azure functions lokaal](functions-develop-local.md)voor meer informatie.

### <a name="console"></a><a name="console"></a>Console

![Console van functie-app](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

De console in de portal is een ideaal ontwikkelaars hulpprogramma wanneer u liever met uw functie-app vanaf de opdracht regel. Algemene opdrachten zijn onder andere het maken en navigeren van mappen en bestanden, en het uitvoeren van batch bestanden en scripts. 

Wanneer u lokaal ontwikkelt, kunt u het beste de [Azure functions core tools](functions-run-local.md) en de [Azure cli]gebruiken.

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>Geavanceerde hulp middelen (kudu)

![Kudu configureren](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

De geavanceerde hulp middelen voor App Service (ook wel bekend als kudu) bieden toegang tot geavanceerde beheer functies van uw functie-app. Vanuit kudu beheert u systeem informatie, app-instellingen, omgevings variabelen, site-uitbrei dingen, HTTP-headers en Server variabelen. U kunt **kudu** ook starten door te bladeren naar het SCM-eind punt voor uw functie-app, zoals `https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment-center"></a><a name="deployment"></a>Implementatiecenter

Wanneer u een oplossing voor broncode beheer gebruikt voor het ontwikkelen en onderhouden van uw functions-code, kunt u met het implementatie centrum maken en implementeren vanuit broncode beheer. Uw project wordt gemaakt en geïmplementeerd in azure wanneer u updates maakt. Zie [implementatie technologieën in azure functions](functions-deployment-technologies.md)voor meer informatie.

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>Cross-origin-resources delen

Om te voor komen dat schadelijke code wordt uitgevoerd op de client, blok keren moderne browsers aanvragen van webtoepassingen naar bronnen die worden uitgevoerd in een afzonderlijk domein. [Cross-Origin Resource Sharing (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) kan een `Access-Control-Allow-Origin` header declareren welke oorsprongen eind punten mogen aanroepen in uw functie-app.

#### <a name="portal"></a>Portal

Wanneer u de lijst met **toegestane oorsprongen** voor uw functie-app configureert, `Access-Control-Allow-Origin` wordt de header automatisch toegevoegd aan alle antwoorden van http-eind punten in uw functie-app. 

![De CORS-lijst van de functie-app configureren](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Wanneer het Joker teken ( `*` ) wordt gebruikt, worden alle andere domeinen genegeerd. 

Gebruik de [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) opdracht om een domein toe te voegen aan de lijst toegestane oorsprongen. In het volgende voor beeld wordt het contoso.com-domein toegevoegd:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Gebruik de [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) opdracht om de huidige toegestane oorsprongen weer te geven.

### <a name="authentication"></a><a name="auth"></a>Verificatie

![Verificatie configureren voor een functie-app](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Wanneer functies een HTTP-trigger gebruiken, kunt u vereisen dat aanroepen eerst worden geverifieerd. App Service ondersteunt Azure Active Directory-verificatie en aanmelden met sociale providers, zoals Facebook, micro soft en Twitter. Zie [Azure app service Authentication-overzicht](../app-service/overview-authentication-authorization.md)voor meer informatie over het configureren van specifieke verificatie providers. 


## <a name="next-steps"></a>Volgende stappen

+ [Azure App Service-instellingen configureren](../app-service/configure-common.md)
+ [Doorlopende implementatie voor Azure Functions](functions-continuous-deployment.md)

[Azure-CLI]: /cli/azure/
[Azure-portal]: https://portal.azure.com
