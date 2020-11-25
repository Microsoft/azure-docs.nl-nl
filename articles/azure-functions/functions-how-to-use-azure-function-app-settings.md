---
title: Instellingen voor functie-apps in azure configureren
description: Meer informatie over het configureren van instellingen voor Azure function-apps.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 04/13/2020
ms.custom: cc996988-fb4f-47, devx-track-azurecli
ms.openlocfilehash: f597e58c70d6ac9daff753f5c0a54199c2383c42
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019499"
---
# <a name="manage-your-function-app"></a>Uw functie-app beheren 

In Azure Functions biedt een functie-app de uitvoerings context voor uw afzonderlijke functies. Het gedrag van functie-apps is van toepassing op alle functies die worden gehost door een bepaalde functie-app. Alle functies in een functie-app moeten van dezelfde [taal](supported-languages.md)zijn. 

Afzonderlijke functies in een functie-app worden samen geïmplementeerd en worden tegelijk geschaald. Alle functies in dezelfde functie-app delen resources per exemplaar, zoals de functie-app wordt geschaald. 

Verbindings reeksen, omgevings variabelen en andere toepassings instellingen worden afzonderlijk voor elke functie-app gedefinieerd. Gegevens die moeten worden gedeeld tussen functie-apps, moeten extern worden opgeslagen in een persistente opslag.

In dit artikel wordt beschreven hoe u uw functie-apps configureert en beheert. 

> [!TIP]  
> Veel configuratie opties kunnen ook worden beheerd met behulp van de [Azure cli]. 

## <a name="get-started-in-the-azure-portal"></a>Aan de slag in de Azure-portal

1. Als u wilt beginnen, gaat u naar de [Azure Portal] en meldt u zich aan bij uw Azure-account. Voer in de zoek balk boven in de Portal de naam van uw functie-app in en selecteer deze in de lijst. 

2. Selecteer **configuratie** onder **instellingen** in het linkerdeel venster.

    :::image type="content" source="./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png" alt-text="Overzicht van functie-app in de Azure Portal":::

U kunt navigeren naar alles wat u nodig hebt om uw functie-app te beheren op de pagina overzicht, met name de **[Toepassings instellingen](#settings)** en **[platform functies](#platform-features)**.

## <a name="application-settings"></a><a name="settings"></a>Toepassingsinstellingen

Op het tabblad **Toepassings instellingen** worden instellingen onderhouden die worden gebruikt door de functie-app. Deze instellingen worden versleuteld opgeslagen en u moet **waarden weer geven** selecteren om de waarden in de portal weer te geven. U kunt ook toegang krijgen tot toepassings instellingen met behulp van de Azure CLI.

### <a name="portal"></a>Portal

Als u een instelling wilt toevoegen in de portal, selecteert u **nieuwe toepassings instelling** en voegt u het nieuwe sleutel-waardepaar toe.

![Functie-app-instellingen in de Azure Portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Azure CLI

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

### <a name="use-application-settings"></a>Toepassings instellingen gebruiken

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Wanneer u een functie-app lokaal ontwikkelt, moet u lokale kopieën van deze waarden in de local.settings.jsvan het project bestand behouden. Zie [Local Settings file](functions-run-local.md#local-settings-file)(Engelstalig) voor meer informatie.

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

[Azure CLI]: /cli/azure/
[Azure-portal]: https://portal.azure.com
