---
title: 'Zelfstudie: Gebruikers verifiëren E2E'
description: Leer hoe u verificatie en autorisatie van App Service kunt gebruiken om uw App Service-apps end-to-end te beveiligen, waaronder toegang tot externe API's.
keywords: app service, azure app service, authN, authZ, beveiligen, beveiliging, meerdere lagen, azure active directory, azure ad
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/29/2020
ms.custom: devx-track-csharp, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 8c3fca6cf7782c3aaac91388a8f8395e288f5ea5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96558739"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service"></a>Zelfstudie: Zelfstudie: Gebruikers eind-tot-eind verifiëren en autoriseren in Azure App Service

::: zone pivot="platform-windows"  

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie. Daarnaast bevat App Service ingebouwde ondersteuning voor [verificatie en autorisatie van gebruikers](overview-authentication-authorization.md). In deze zelfstudie leest u hoe u apps kunt beveiligen met verificatie en autorisatie van App Service. Als voorbeeld wordt gebruikgemaakt van een ASP.NET Core-app met een Angular.js-front-end. Verificatie en autorisatie van App Service ondersteunt runtime voor alle talen en u leert hoe u deze kunt toepassen op uw taal van voorkeur door de zelfstudie te volgen.

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie via het Linux-besturingssysteem. Daarnaast bevat App Service ingebouwde ondersteuning voor [verificatie en autorisatie van gebruikers](overview-authentication-authorization.md). In deze zelfstudie leest u hoe u apps kunt beveiligen met verificatie en autorisatie van App Service. Als voorbeeld wordt gebruikgemaakt van een ASP.NET Core-app met een Angular.js-front-end. Verificatie en autorisatie van App Service ondersteunt runtime voor alle talen en u leert hoe u deze kunt toepassen op uw taal van voorkeur door de zelfstudie te volgen.

::: zone-end

![Eenvoudige verificatie en autorisatie](./media/tutorial-auth-aad/simple-auth.png)

U leest ook hoe u een app met meerdere lagen beveiligt door namens de geverifieerde gebruiker een beveiligde back-end-API te openen, zowel door middel van [servercode](#call-api-securely-from-server-code) als [browsercode](#call-api-securely-from-browser-code).

![Geavanceerde verificatie en autorisatie](./media/tutorial-auth-aad/advanced-auth.png)

Dit zijn slechts enkele van de mogelijke scenario's voor verificatie en autorisatie in App Service. 

Hieronder vindt u een meer uitgebreide lijst met zaken die u in de zelfstudie leert:

> [!div class="checklist"]
> * Ingebouwde verificatie en autorisatie inschakelen
> * Apps beveiligen tegen niet-geverifieerde aanvragen
> * Azure Active Directory gebruiken als id-provider
> * Externe apps openen namens de aangemelde gebruiker
> * Aanroepen tussen services beveiligen met tokenverificatie
> * Toegangstokens gebruiken vanuit servercode
> * Toegangstokens gebruiken vanuit clientcode (browser)

U kunt de stappen in deze zelfstudie volgen voor macOS, Linux en Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

- <a href="https://git-scm.com/" target="_blank">Git installeren</a>
- <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">De nieuwste .NET Core 3.1-SDK installeren</a>
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-local-net-core-app"></a>Lokale .NET Core-app maken

In deze stap stelt u het lokale .NET Core-project in. U gebruikt hetzelfde project voor het implementeren van een back-end-API-app en een front-end-web-app.

### <a name="clone-and-run-the-sample-application"></a>De voorbeeldtoepassing klonen en uitvoeren

Voer de volgende opdrachten uit om de voorbeeldopslagplaats te klonen en voer deze uit.

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
cd dotnet-core-api
dotnet run
```

Ga naar `http://localhost:5000` en voeg taken toe, bewerk deze en verwijder ze. 

![ASP.NET Core-API lokaal uitvoeren](./media/tutorial-auth-aad/local-run.png)

Druk op `Ctrl+C` in de terminal als u ASP.NET Core wilt stoppen.

## <a name="deploy-apps-to-azure"></a>Apps in Azure implementeren

In deze stap implementeert u het project in twee App Service-apps. De ene is de front-end-app en de andere is de back-end-app.

### <a name="configure-a-deployment-user"></a>Een implementatiegebruiker configureren

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-azure-resources"></a>Azure-resources maken

::: zone pivot="platform-windows"  

Voer in Azure Cloud Shell de volgende opdrachten uit om twee Windows-web-apps te maken. Vervang _\<front-end-app-name>_ en _\<back-end-app-name>_ door een wereldwijd unieke naam (geldige tekens zijn `a-z`, `0-9` en `-`). Zie [RESTful API hosten met CORS in Azure App Service](app-service-web-tutorial-rest-api.md) voor meer informatie over de opdrachten.

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
```

::: zone-end

::: zone pivot="platform-linux"

Voer in Azure Cloud Shell de volgende opdrachten uit om twee web-apps te maken. Vervang _\<front-end-app-name>_ en _\<back-end-app-name>_ door een wereldwijd unieke naam (geldige tekens zijn `a-z`, `0-9` en `-`). Zie [Een .NET Core-app maken in Azure App Service](quickstart-dotnetcore.md) voor meer informatie over elke opdracht.

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE --is-linux
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git --query deploymentLocalGitUrl
```

::: zone-end

> [!NOTE]
> Sla de URL's van de externe Git-instanties voor de front-en-app en de back-end-app op. Deze worden in de uitvoer van `az webapp create` weergegeven.
>

### <a name="push-to-azure-from-git"></a>Pushen naar Azure vanaf Git

Voer in het _lokale terminalvenster_ de volgende Git-opdrachten uit om de implementatie in de back-end-app uit te voeren. Vervang _\<deploymentLocalGitUrl-of-back-end-app>_ door de URL van de externe Git-instantie die u hebt opgeslagen bij [Azure-resources maken](#create-azure-resources). Wanneer u door Git Credential Manager om referenties wordt gevraagd, geeft u [de referenties voor implementatie](deploy-configure-credentials.md) op en niet de referenties die u gebruikt om u aan te melden bij de Azure-portal.

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

Voer in het lokale terminalvenster de volgende Git-opdrachten uit om dezelfde code in de front-end-app te implementeren. Vervang _\<deploymentLocalGitUrl-of-front-end-app>_ door de URL van de externe Git-instantie die u hebt opgeslagen bij [Azure-resources maken](#create-azure-resources).

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-apps"></a>Naar de apps bladeren

Ga in een browser naar de volgende URL's om de twee apps in werking te zien.

```
http://<back-end-app-name>.azurewebsites.net
http://<front-end-app-name>.azurewebsites.net
```

:::image type="content" source="./media/tutorial-auth-aad/azure-run.png" alt-text="Schermopname van een REST API-voorbeeld van Azure App Service in een browservenster met daarin een takenlijst-app.":::

> [!NOTE]
> Als de app opnieuw wordt gestart, hebt u wellicht gezien dat nieuwe gegevens zijn gewist. Dit gedrag is inherent aan het ontwerp omdat de ASP.NET Core-voorbeeld-app gebruikmaakt van een database in het geheugen.
>
>

## <a name="call-back-end-api-from-front-end"></a>Back-end-API aanroepen vanuit front-end

In deze stap legt u voor de servercode van de front-end-app vast dat toegang tot de back-end-API mogelijk is. Later schakelt u geverifieerde toegang in vanaf de front-end tot de back-end.

### <a name="modify-front-end-code"></a>Front-end-code wijzigen

Open _Controllers/TodoController.cs_ in de lokale opslagplaats. Voeg aan het begin van de `TodoController`-klasse de volgende regels toe, en vervang _\<back-end-app-name>_ door de naam van de back-end-app:

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back-end-app-name>.azurewebsites.net";
```

Zoek de methode die is voorzien van `[HttpGet]`, en vervang de code binnen de accolades door:

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo");
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

In de eerste regel wordt een `GET /api/Todo`-aanroep naar de back-end-API-app uitgevoerd.

Zoek vervolgens de methode die is voorzien van `[HttpGet("{id}")]`, en vervang de code binnen de accolades door:

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}");
return Content(data, "application/json");
```

In de eerste regel wordt een `GET /api/Todo/{id}`-aanroep naar de back-end-API-app uitgevoerd.

Zoek vervolgens de methode die is voorzien van `[HttpPost]`, en vervang de code binnen de accolades door:

```cs
var response = await _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", todoItem);
var data = await response.Content.ReadAsStringAsync();
return Content(data, "application/json");
```

In de eerste regel wordt een `POST /api/Todo`-aanroep naar de back-end-API-app uitgevoerd.

Zoek vervolgens de methode die is voorzien van `[HttpPut("{id}")]`, en vervang de code binnen de accolades door:

```cs
var res = await _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", todoItem);
return new NoContentResult();
```

In de eerste regel wordt een `PUT /api/Todo/{id}`-aanroep naar de back-end-API-app uitgevoerd.

Zoek vervolgens de methode die is voorzien van `[HttpDelete("{id}")]`, en vervang de code binnen de accolades door:

```cs
var res = await _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}");
return new NoContentResult();
```

In de eerste regel wordt een `DELETE /api/Todo/{id}`-aanroep naar de back-end-API-app uitgevoerd.

Sla al uw wijzigingen op. Implementeer via het lokale terminalvenster de wijzigingen aan de front-end-app met de volgende Git-opdrachten:

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>Wijzigingen controleren

Ga naar `http://<front-end-app-name>.azurewebsites.net` en voeg enkele items toe, bijvoorbeeld `from front end 1` en `from front end 2`.

Ga naar `http://<back-end-app-name>.azurewebsites.net` om de items te zien die aan de front-end-app zijn toegevoegd. Voeg nu ook een paar items toe, zoals `from back end 1` en `from back end 2`, en vernieuw de front-end-app om de doorgevoerde wijzigingen te zien.

:::image type="content" source="./media/tutorial-auth-aad/remote-api-call-run.png" alt-text="Schermopname van een REST API-voorbeeld van Azure App Service in een browservenster met daarin een takenlijst-app met items die zijn toegevoegd uit de front-end-app.":::

## <a name="configure-auth"></a>Verificatie configureren

In deze stap schakelt u verificatie en autorisatie voor de twee apps in. U configureert ook de front-end-app om een toegangstoken te genereren dat u kunt gebruiken om geverifieerde aanroepen uit te voeren naar de back-end-app.

U gebruikt Azure Active Directory als id-provider. Zie [Verificatie van Azure Active Directory configureren voor de App Services-toepassing](configure-authentication-provider-aad.md) voor meer informatie.

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>Verificatie en autorisatie voor de back-end-app inschakelen

Selecteer in het menu van de [Azure-portal](https://portal.azure.com) de optie **Resourcegroepen** of zoek ernaar en selecteer *Resourcegroepen* vanaf een willekeurige pagina.

Zoek in **Resourcegroepen** uw resourcegroep en selecteer deze. Selecteer in **Overzicht** de beheerpagina van de back-end-app.

:::image type="content" source="./media/tutorial-auth-aad/portal-navigate-back-end.png" alt-text="Schermopname van het venster Resourcegroepen met daarin het overzicht van een voorbeeldresourcegroep en een geselecteerde beheerpagina van een back-end-app.":::

Selecteer in het linkermenu van de back-end-app de optie **Verificatie/autorisatie** en schakel vervolgens App Service-verificatie in door **Aan** te selecteren.

Selecteer **Te ondernemen actie wanneer de aanvraag niet is geverifieerd** de optie **Aanmelden met Azure Active Directory**.

Selecteer onder **Azure Active Directory** de optie **Verificatieproviders**.

:::image type="content" source="./media/tutorial-auth-aad/configure-auth-back-end.png" alt-text="Schermopname van het linkermenu van de back-end-app met de geselecteerde optie Verificatie/autorisatie en het rechtermenu met de geselecteerde instellingen.":::

Selecteer **Express** en accepteer vervolgens de standaardinstellingen om een nieuwe AD-app te maken. Selecteer vervolgens **OK**.

Selecteer op de pagina **Verificatie/autorisatie** de optie **Opslaan**.

Vernieuw de portalpagina als de melding met het bericht `Successfully saved the Auth Settings for <back-end-app-name> App` wordt weergegeven.

Selecteer **Azure Active Directory** opnieuw, en selecteer vervolgens de **Azure AD-app**.

Kopieer de **Client-id** van de Azure AD-toepassing in een kladblok. U hebt deze waarde later nog nodig.

:::image type="content" source="./media/tutorial-auth-aad/get-application-id-back-end.png" alt-text="Schermopname van het venster Azure Active Directory-instellingen met de Azure AD-app en het venster Azure AD-toepassingen met de te kopiëren client-id.":::

Als u hier stopt, hebt u een zelfstandige app die al wordt beveiligd met verificatie en autorisatie van App Service. In de overige secties ziet u hoe u een oplossing met meerdere apps kunt beveiligen door de geverifieerde gebruiker van de front-end naar de back-end te ‘stromen’. 

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>Verificatie en autorisatie voor de front-end-app inschakelen

Volg dezelfde stappen voor de front-end-app, maar sla de laatste stap over. U hebt de client-id niet nodig voor de front-end-app.

Ga, als u wilt, naar `http://<front-end-app-name>.azurewebsites.net`. U wordt nu doorgestuurd naar een beveiligde aanmeldingspagina. Nadat u zich hebt aangemeld, *hebt u nog steeds geen toegang tot de gegevens van de back-end-app*, omdat voor de back-end-app nu Azure Active Directory-aanmelding vanuit de front-end-app is vereist. U moet drie dingen doen:

- De front-end toegang geven tot de back-end
- App Service configureren zodat een bruikbaar token wordt geretourneerd
- De token in de code gebruiken

> [!TIP]
> Als u tegen fouten aanloopt en de instellingen voor verificatie en autorisatie opnieuw configureert, kunnen de tokens in het tokenarchief mogelijk niet met de nieuwe instellingen opnieuw worden gegenereerd. Als u er zeker van wilt zijn dat de tokens opnieuw worden gegenereerd, dient u zich af te melden en vervolgens opnieuw aan te melden. U kunt dit doen door de browser in de privémodus te zetten en de browser vervolgens te sluiten en opnieuw in de privémodus te openen nadat u de instellingen in de apps hebt gewijzigd.

### <a name="grant-front-end-app-access-to-back-end"></a>Front-end-app toegang verlenen tot de back-end-app

Nu u voor beide apps verificatie en autorisatie hebt ingeschakeld, worden beide door een AD-toepassing ondersteund. In deze stap geeft u de front-end-app namens de gebruiker machtigingen voor toegang tot de back-end-app. (Technisch gesproken geeft u de _AD-toepassing_ van de front-end-app namens de gebruiker machtigingen voor toegang tot de _AD-toepassing_ van de back-end-app.)

Selecteer in het menu van de [Azure-portal](https://portal.azure.com) de optie **Azure Active Directory**, of zoek en selecteer *Azure Active Directory* op een willekeurige pagina.

Selecteer **App-registraties** > **Toepassingen in eigendom** > **Alle toepassingen in deze map weergeven**. Selecteer de naam van de front-end-app, en selecteer vervolgens **API-machtigingen**.

:::image type="content" source="./media/tutorial-auth-aad/add-api-access-front-end.png" alt-text="Schermopname van het venster Microsoft - App-registraties waarin Toepassingen in eigendom, de naam van een front-end-app en API-machtigingen zijn geselecteerd.":::

Selecteer **Een machtiging toevoegen**, en selecteer vervolgens **API's die in mijn organisatie worden gebruikt** >  **\<back-end-app-name>** .

Selecteer op de pagina **API-machtigingen aanvragen** voor de back-end-app de optie **Gedelegeerde machtigingen** en **user_impersonation**. Selecteer vervolgens **Machtigingen toevoegen**.

:::image type="content" source="./media/tutorial-auth-aad/select-permission-front-end.png" alt-text="Schermopname van de pagina API-machtigingen aanvragen waarop Gedelegeerde machtigingen, user_impersonation en de knop Machtigingen toevoegen zijn geselecteerd.":::

### <a name="configure-app-service-to-return-a-usable-access-token"></a>App Service configureren zodat een bruikbaar toegangstoken wordt geretourneerd

De front-end-app beschikt nu over de vereiste machtigingen voor toegang tot de back-end-app als de aangemelde gebruiker. In deze stap configureert u App Service-verificatie en -autorisatie zodat u een bruikbaar toegangstoken hebt voor toegang tot de back-end. Voor deze stap hebt u de client-id van de back-end nodig die u hebt gekopieerd bij [Verificatie en autorisatie voor de back-end-app inschakelen](#enable-authentication-and-authorization-for-back-end-app).

Ga naar [Azure Resource Explorer](https://resources.azure.com) en gebruik de resourcestructuur om de front-end-web-app te zoeken.

De [Azure Resource Explorer](https://resources.azure.com) wordt nu geopend met de front-end-app die u hebt geselecteerd in de resourcestructuur. Klik boven aan de pagina op **Lezen/schrijven** om de Azure-resources te kunnen bewerken.

:::image type="content" source="./media/tutorial-auth-aad/resources-enable-write.png" alt-text="Schermopname van de knoppen Alleen-lezen en Lezen/schrijven aan de bovenkant van de pagina Azure Resource Explorer, waarvan de knop Lezen/schrijven is geselecteerd.":::

Bekijk in de linkernavigatiebalk de gegevens voor **config** > **authsettings**.

Klik in de weergave **authsettings** op **Bewerken**. Stel `additionalLoginParams` in op de volgende JSON-tekenreeks met behulp van de client-id die u hebt gekopieerd. 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back-end-client-id>"],
```

:::image type="content" source="./media/tutorial-auth-aad/additional-login-params-front-end.png" alt-text="Schermopname van een codevoorbeeld in de weergave authsettings met daarin de tekenreeks additionalLoginParams met een voorbeeld van een client-id.":::

Sla de instellingen op door op **PUT** te klikken.

Uw apps zijn nu geconfigureerd. De front-end is nu gereed voor toegang tot de back-end met het juiste toegangstoken.

Zie [Toegangstokens van id-providers vernieuwen](app-service-authentication-how-to.md#refresh-identity-provider-tokens) voor informatie over het configureren van het toegangstoken voor andere providers.

## <a name="call-api-securely-from-server-code"></a>API veilig aanroepen vanuit servercode

In deze stap activeert u de eerder gewijzigde servercode, zodat u geverifieerde aanroepen naar de back-end-API kunt uitvoeren.

De front-end-app heeft nu de vereiste machtiging en voegt tevens de client-id van de back-end-app toe aan de aanmeldingsparameters. Op die manier kan een toegangstoken voor verificatie van de back-end-app verkregen worden. App Service stelt dit token aan de servercode beschikbaar door in elke geverifieerde aanvraag een `X-MS-TOKEN-AAD-ACCESS-TOKEN`-header te injecteren (zie [Tokens ophalen in app-code](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)).

> [!NOTE]
> Deze headers worden voor alle ondersteunde talen geïnjecteerd. U kunt ze openen met het standaardpatroon voor elke betreffende taal.

Open opnieuw _Controllers/TodoController.cs_ in de lokale opslagplaats. Voeg onder de `TodoController(TodoContext context)`-constructor de volgende code toe:

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);
}
```

Met deze code wordt de standaard-HTTP-header `Authorization: Bearer <access-token>` aan alle externe API-aanroepen toegevoegd. In de ASP.NET Core-pijplijn voor de uitvoering van de aanvraag wordt `OnActionExecuting` vlak voor de desbetreffende actie uitgevoerd, zodat nu elke uitgaande API-aanroep over het toegangstoken beschikt.

Sla al uw wijzigingen op. Implementeer via het lokale terminalvenster de wijzigingen aan de front-end-app met de volgende Git-opdrachten:

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

Meld u opnieuw aan bij `https://<front-end-app-name>.azurewebsites.net`. Klik op de pagina over de gebruiksovereenkomst voor gebruikersgegevens op **Accepteren**.

U moet nu net als eerder gegevens uit de back-end-app kunnen maken, lezen, bijwerken en verwijderen. Het enige verschil is dat beide apps nu worden beveiligd door App Service-verificatie en -autorisatie, waaronder de aanroepen tussen services.

Gefeliciteerd! De servercode heeft nu toegang tot de gegevens van de back-end namens de geverifieerde gebruiker.

## <a name="call-api-securely-from-browser-code"></a>API veilig vanuit browsercode aanroepen

In deze stap legt u vast dat de Angular.js-app van de front-end naar de back-end-API verwijst. Op die manier leert u hoe u het toegangstoken kunt ophalen en er API-aanroepen naar de back-end-app mee kunt uitvoeren.

Terwijl de servercode toegang heeft tot de aanvraag-headers, heeft de clientcode toegang tot `GET /.auth/me` om dezelfde toegangstokens te kunnen ophalen (zie [Tokens ophalen in app-code](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)).

> [!TIP]
> In deze sectie worden de standaard-HTTP-methoden gebruikt om de veilige HTTP-aanroepen te demonstreren. U kunt echter [Microsoft Authentication Library voor JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js) gebruiken om het Angular.js-toepassingspatroon te vereenvoudigen.
>

### <a name="configure-cors"></a>CORS configureren

Schakel in Cloud Shell CORS in voor de URL van de client met de opdracht [`az webapp cors add`](/cli/azure/webapp/cors#az-webapp-cors-add). Vervang de tijdelijke aanduidingen _\<back-end-app-name>_ en _\<front-end-app-name>_ .

```azurecli-interactive
az webapp cors add --resource-group myAuthResourceGroup --name <back-end-app-name> --allowed-origins 'https://<front-end-app-name>.azurewebsites.net'
```

Deze stap heeft geen betrekking op verificatie en autorisatie. U hebt deze echter nodig zodat de browser API-aanroepen tussen domeinen vanaf de Angular.js-app toestaat. Zie [CORS-functionaliteit toevoegen](app-service-web-tutorial-rest-api.md#add-cors-functionality) voor meer informatie.

### <a name="point-angularjs-app-to-back-end-api"></a>Angular.js-app naar back-end-API laten verwijzen

Open _wwwroot/index.html_ in de lokale opslagplaats.

Stel in regel 51 de variabele `apiEndpoint` in op de HTTPS URL van de back-end-app (`https://<back-end-app-name>.azurewebsites.net`). Vervang _\<back-end-app-name>_ door de naam van de app in App Service.

Open _wwwroot/app/scripts/todoListSvc.js_ in de lokale opslagplaats en controleer of alle API-aanroepen door `apiEndpoint` vooraf worden gegaan. De Angular.js-app roept nu de back-end-API's aan. 

### <a name="add-access-token-to-api-calls"></a>Toegangstoken toevoegen aan API-aanroepen

Voeg in _wwwroot/app/scripts/todoListSvc.js_, boven de lijst met API-aanroepen (boven de regel `getItems : function(){`), de volgende functie aan de lijst toe:

```javascript
setAuth: function (token) {
    $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
},
```

Deze functie wordt aangeroepen om de standaard-`Authorization`-header in te stellen met het toegangstoken. De functie wordt in de volgende stap aangeroepen.

Open _wwwroot/app/scripts/app.js_ in de lokale opslagplaats en zoek de volgende code:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
}).otherwise({ redirectTo: "/Home" });
```

Vervang het hele codeblok door de volgende code:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
    resolve: {
        token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
            return $http.get('/.auth/me').then(function (response) {
                todoListSvc.setAuth(response.data[0].access_token);
                return response.data[0].access_token;
            });
        }]
    },
}).otherwise({ redirectTo: "/Home" });
```

Door deze wijziging wordt de toewijzing `resolve` toegevoegd, waarmee `/.auth/me` wordt aangeroepen en het toegangstoken ingesteld. Hiermee wordt gegarandeerd dat u over het toegangstoken beschikt voordat de controller `todoListCtrl` wordt geïnstantieerd. Op die manier bevatten alle API-aanroepen van de controller het token.

### <a name="deploy-updates-and-test"></a>Updates implementeren en tests uitvoeren

Sla al uw wijzigingen op. Implementeer via het lokale terminalvenster de wijzigingen aan de front-end-app met de volgende Git-opdrachten:

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

Ga opnieuw naar `https://<front-end-app-name>.azurewebsites.net`. U moet nu rechtstreeks in de Angular.js-app gegevens in de back-end kunnen maken, lezen, bijwerken en verwijderen.

Gefeliciteerd! De clientcode heeft nu toegang tot de gegevens van de back-end namens de geverifieerde gebruiker.

## <a name="when-access-tokens-expire"></a>Wanneer de toegangstokens verlopen

Uw toegangstoken verloopt na bepaalde tijd. Voor meer informatie over het vernieuwen van uw toegangstokens zonder dat gebruikers zich opnieuw moeten verifiëren bij uw app raadpleegt u [Toegangstokens van id-providers vernieuwen](app-service-authentication-how-to.md#refresh-identity-provider-tokens).

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. Als u deze resources niet meer nodig denkt te hebben, verwijdert u de resourcegroep door de volgende opdracht in Cloud Shell uit te voeren:

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

Het kan een minuut duren voordat deze opdracht is uitgevoerd.

<a name="next"></a>
## <a name="next-steps"></a>Volgende stappen

Wat u hebt geleerd:

> [!div class="checklist"]
> * Ingebouwde verificatie en autorisatie inschakelen
> * Apps beveiligen tegen niet-geverifieerde aanvragen
> * Azure Active Directory gebruiken als id-provider
> * Externe apps openen namens de aangemelde gebruiker
> * Aanroepen tussen services beveiligen met tokenverificatie
> * Toegangstokens gebruiken vanuit servercode
> * Toegangstokens gebruiken vanuit clientcode (browser)

Ga door naar de volgende zelfstudie om te leren hoe u een aangepaste DNS-naam aan uw app kunt toewijzen.

> [!div class="nextstepaction"]
> [Een bestaande aangepaste DNS-naam toewijzen aan Azure App Service](app-service-web-tutorial-custom-domain.md)
