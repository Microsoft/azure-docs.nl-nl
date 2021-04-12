---
title: 'Zelfstudie: RESTful API hosten met CORS'
description: Informatie over hoe u met Azure App Service uw RESTful API's met CORS-ondersteuning kunt hosten. App Service kan zowel front-end-web-apps als back-end-API’s hosten.
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/28/2020
ms.custom: devx-track-csharp, mvc, devcenter, seo-javascript-september2019, seo-javascript-october2019, seodec18
ms.openlocfilehash: 9481b6d2740d27b8c3d1309e205edda6017868fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005743"
---
# <a name="tutorial-host-a-restful-api-with-cors-in-azure-app-service"></a>Zelfstudie: een RESTful API hosten met CORS in Azure App Service

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie. Daarnaast bevat App Service ingebouwde ondersteuning voor [CORS (Cross-Origin Resource Sharing)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing) voor RESTful API's. In deze zelfstudie leert u hoe u een ASP.NET Core API-app in App Service met CORS-ondersteuning implementeert. U configureert de app met opdrachtregelprogramma's en implementeert de app met behulp van Git. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * App Service-resources maken met Azure CLI
> * Een RESTful API implementeren in Azure met behulp van Git
> * CORS-ondersteuning van App Service implementeren

U kunt de stappen in deze zelfstudie volgen voor macOS, Linux en Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

* <a href="https://git-scm.com/" target="_blank">Git installeren</a>
 * <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">De nieuwste versie van .NET Core 3.1 SDK installeren</a>

## <a name="create-local-aspnet-core-app"></a>Lokale ASP.NET Core-app maken

In deze stap stelt u het lokale ASP.NET Core-project in. App Service ondersteunt dezelfde werkstroom voor API's die in andere talen zijn geschreven.

### <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Voer in het terminalvenster de opdracht `cd` naar een werkmap uit.  

Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. 

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
```

Deze opslagplaats bevat een app die wordt gemaakt op basis van de volgende zelfstudie: [ASP.NET Core Web API help pages using Swagger](/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio) (Help-pagina's van ASP.NET Core Web API met behulp van Swagger). Er wordt een Swagger-generator gebruikt voor de [Swagger-gebruikersinterface](https://swagger.io/swagger-ui/) en het Swagger JSON-eindpunt.

### <a name="run-the-application"></a>De toepassing uitvoeren

Voer de volgende opdrachten uit om de vereiste pakketten te installeren, databasemigraties uit te voeren en de toepassing te starten.

```bash
cd dotnet-core-api
dotnet restore
dotnet run
```

Ga naar `http://localhost:5000/swagger` in een browser om kennis te maken met de Swagger-gebruikersinterface.

![ASP.NET Core-API lokaal uitvoeren](./media/app-service-web-tutorial-rest-api/azure-app-service-local-swagger-ui.png)

Ga naar `http://localhost:5000/api/todo` om een lijst met ToDo JSON-items te bekijken.

Ga naar `http://localhost:5000` om met de browser-app kennis te maken. Later wijst u de browser-app naar een externe API in App Service om CORS functioneel te testen. Code voor de browser-app vindt u in de map _wwwroot_ van de opslagplaats.

Druk op `Ctrl+C` in de terminal als u ASP.NET Core wilt stoppen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>App in Azure implementeren

In deze stap implementeert u de met SQL Database verbonden .NET Core-app met App Service.

### <a name="configure-local-git-deployment"></a>Lokale Git-implementatie configureren

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-an-app-service-plan"></a>Een App Service-plan maken

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Een webtoepassing maken

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="push-to-azure-from-git"></a>Pushen naar Azure vanaf Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 83, done.
Counting objects: 100% (83/83), done.
Delta compression using up to 8 threads
Compressing objects: 100% (78/78), done.
Writing objects: 100% (83/83), 22.15 KiB | 3.69 MiB/s, done.
Total 83 (delta 26), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '509236e13d'.
remote: Generating deployment script.
remote: Project file path: .\TodoApi.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
To https://&lt;app_name&gt;.scm.azurewebsites.net/&lt;app_name&gt;.git
 * [new branch]      master -> master
</pre>

### <a name="browse-to-the-azure-app"></a>Naar de Azure-app bladeren

Ga naar `http://<app_name>.azurewebsites.net/swagger` in een browser om kennis te maken met de Swagger-gebruikersinterface.

![ASP.NET Core-API uitvoeren in Azure App Service](./media/app-service-web-tutorial-rest-api/azure-app-service-browse-app.png)

Ga naar `http://<app_name>.azurewebsites.net/swagger/v1/swagger.json` om de _swagger.json_ om uw geïmplementeerde API te zien.

Ga naar `http://<app_name>.azurewebsites.net/api/todo` om de geïmplementeerde API in werking te zien.

## <a name="add-cors-functionality"></a>CORS-functionaliteit toevoegen

Schakel vervolgens de ingebouwde CORS-ondersteuning in App Service voor uw API in.

### <a name="test-cors-in-sample-app"></a>CORS in voorbeeld-app testen

Open _wwwroot/index.html_ in uw lokale opslagplaats.

Stel in regel 51 de variabele `apiEndpoint` in op de URL van uw geïmplementeerde API (`http://<app_name>.azurewebsites.net`). Vervang _\<appname>_ door de naam van de app in App Service.

Voor de voorbeeld-app opnieuw uit in het lokale terminalvenster.

```bash
dotnet run
```

Ga naar de browser-app op `http://localhost:5000`. Open het venster van de hulpprogramma's voor ontwikkelaars in uw browser (`Ctrl`+`Shift`+`i` in Chrome voor Windows) en controleer het tabblad **Console**. U ziet nu het foutbericht, `No 'Access-Control-Allow-Origin' header is present on the requested resource`.

![CORS-fout in de browserclient](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-error.png)

Vanwege een verschil tussen de domeinen van de browser-app (`http://localhost:5000`) en de externe resource (`http://<app_name>.azurewebsites.net`), en het feit dat de API in App Service de kop `Access-Control-Allow-Origin` niet verzendt, is voorkomen dat inhoud uit beide domeinen in de browser-app is geladen.

In productie zou de browser-app een openbare URL hebben in plaats van de localhost-URL. De manier om CORS voor een localhost-URL in te schakelen, is echter dezelfde als voor een openbare URL.

### <a name="enable-cors"></a>CORS inschakelen 

Schakel in Cloud Shell CORS in voor de URL van de client met de opdracht [`az webapp cors add`](/cli/azure/webapp/cors#az-webapp-cors-add). Vervang de tijdelijke aanduiding _&lt;app-naam>_ .

```azurecli-interactive
az webapp cors add --resource-group myResourceGroup --name <app-name> --allowed-origins 'http://localhost:5000'
```

U kunt in `properties.cors.allowedOrigins` (`"['URL1','URL2',...]"`) meer dan één client-URL instellen. Met `"['*']"` kunt u ook alle client-URL's instellen.

> [!NOTE]
> Als er voor uw app referenties, zoals cookies of verificatietokens, moeten worden verzonden, is het mogelijk dat de browser de header `ACCESS-CONTROL-ALLOW-CREDENTIALS` van het antwoord vereist. Als u dit wilt inschakelen in App Service, stelt u `properties.cors.supportCredentials` in uw CORS-configuratie in op `true`. Dit kan niet worden ingeschakeld als `allowedOrigins``'*'` bevat.

### <a name="test-cors-again"></a>CORS opnieuw testen

Vernieuw de browser-app op `http://localhost:5000`. Het foutbericht in het **Console**-venster is nu verdwenen. U kunt de gegevens van de geïmplementeerde API zien en ermee werken. De externe API biedt nu ondersteuning voor CORS voor uw browser-app, die lokaal wordt uitgevoerd. 

![CORS in de browserclient](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-success.png)

Gefeliciteerd! U voert een API uit in Azure App Service met CORS-ondersteuning.

## <a name="app-service-cors-vs-your-cors"></a>CORS voor App Service versus uw eigen CORS

U kunt uw eigen CORS-hulpprogramma's gebruiken in plaats van CORS voor App Service voor meer flexibiliteit. U kunt bijvoorbeeld verschillende toegestane oorsprongen opgeven voor verschillende routes of methoden. Omdat u met CORS voor App Service één set geaccepteerde oorsprongen voor alle API-routes en -methoden kunt opgeven, kunt u ook uw eigen CORS-code gebruiken. Zie hoe ASP.NET Core dit doet op [Enabling Cross-Origin Requests (CORS)](/aspnet/core/security/cors) (CORS (Cross-Origin Requests) inschakelen).

> [!NOTE]
> Gebruik CORS voor App Service en uw eigen CORS niet samen. Als u dat doet, heeft CORS van App Service voorrang en heeft uw eigen CORS-code geen effect.
>
>

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Volgende stappen

Wat u hebt geleerd:

> [!div class="checklist"]
> * App Service-resources maken met Azure CLI
> * Een RESTful API implementeren in Azure met behulp van Git
> * CORS-ondersteuning van App Service implementeren

Ga naar de volgende zelfstudie voor informatie over het verifiëren en autoriseren van gebruikers.

> [!div class="nextstepaction"]
> [Zelfstudie: gebruikers eind-tot-eind verifiëren en autoriseren](tutorial-auth-aad.md)
