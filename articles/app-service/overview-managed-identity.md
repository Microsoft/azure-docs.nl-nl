---
title: Beheerde identiteiten
description: Ontdek hoe beheerde identiteiten werken in Azure App Service en Azure Functions, hoe u een beheerde identiteit configureert en een token genereert voor een back-endresource.
author: mattchenderson
ms.topic: article
ms.date: 05/27/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: badc6b6f1b45938e950ffadeefe30d81ed383440
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762439"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Beheerde identiteiten gebruiken voor App Service en Azure Functions

In dit onderwerp ziet u hoe u een beheerde identiteit maakt voor App Service en Azure Functions toepassingen en hoe u deze kunt gebruiken voor toegang tot andere resources. 

> [!Important] 
> Beheerde identiteiten voor App Service en Azure Functions gedragen zich niet zoals verwacht als uw app wordt gemigreerd tussen abonnementen/tenants. De app moet een nieuwe identiteit verkrijgen, die wordt uitgevoerd door de functie uit te stellen en opnieuw in te stellen. Zie [Een identiteit verwijderen hieronder.](#remove) Downstream-resources moeten ook toegangsbeleid hebben bijgewerkt om de nieuwe identiteit te kunnen gebruiken.

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

## <a name="add-a-system-assigned-identity"></a>Een door het systeem toegewezen identiteit toevoegen

Voor het maken van een app met een door het systeem toegewezen identiteit moet een extra eigenschap worden ingesteld voor de toepassing.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Als u een beheerde identiteit in de portal instelt, moet u eerst een toepassing als normaal aanmaken en vervolgens de functie inschakelen.

1. Maak een app in de portal zoals u dat normaal zou doen. Navigeer ernaar in de portal.

2. Als u een functie-app gebruikt, gaat u naar **Platformfuncties.** Schuif voor andere app-typen omlaag naar de **groep Instellingen** in het linkernavigatievenster.

3. Selecteer **Identiteit.**

4. Schakel op **het tabblad Systeem** toegewezen de status **in** op **Aan.** Klik op **Opslaan**.

    ![Schermopname die laat zien waar u De status wilt wijzigen in Aan en selecteer vervolgens Opslaan.](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)


> [!NOTE] 
> Als u de beheerde identiteit voor uw web-app of site-app wilt vinden in de Azure Portal, gaat u onder Bedrijfstoepassingen naar de **sectie Gebruikersinstellingen.** Meestal is de naam van de sleuf vergelijkbaar met `<app name>/slots/<slot name>` .


### <a name="using-the-azure-cli"></a>Met behulp van de Azure CLI

Als u een beheerde identiteit wilt instellen met behulp van de Azure CLI, moet u de opdracht `az webapp identity assign` gebruiken voor een bestaande toepassing. U hebt drie opties voor het uitvoeren van de voorbeelden in deze sectie:

- Gebruik [Azure Cloud Shell](../cloud-shell/overview.md) van de Azure Portal.
- Gebruik de ingesloten Azure Cloud Shell via de knop 'Probeer het' in de rechterbovenhoek van elk onderstaand codeblok.
- [Installeer de nieuwste versie van Azure CLI](/cli/azure/install-azure-cli) (2.0.31 of hoger) als u liever een lokale CLI-console gebruikt. 

De volgende stappen helpen u bij het maken van een web-app en het toewijzen van een identiteit met behulp van de CLI:

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login](/cli/azure/reference-index#az_login). Gebruik een account dat is gekoppeld aan het Azure-abonnement waaronder u de toepassing wilt implementeren:

    ```azurecli-interactive
    az login
    ```

2. Maak een webtoepassing met behulp van de CLI. Voor meer voorbeelden van het gebruik van de CLI met App Service, zie [App Service CLI-voorbeelden:](../app-service/samples-cli.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Voer de `identity assign` opdracht uit om de identiteit voor deze toepassing te maken:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De volgende stappen helpen u bij het maken van een app en het toewijzen van een identiteit met behulp van Azure PowerShell. De instructies voor het maken van een web-app en een functie-app verschillen.

#### <a name="using-azure-powershell-for-a-web-app"></a>Een Azure PowerShell voor een web-app gebruiken

1. Installeer indien nodig de Azure PowerShell de instructies in de Azure PowerShell [en](/powershell/azure/)voer vervolgens uit om een verbinding met `Login-AzAccount` Azure te maken.

2. Maak een webtoepassing met behulp Azure PowerShell. Zie PowerShell-voorbeelden voor meer voorbeelden van Azure PowerShell App Service [powershell App Service gebruiken:](../app-service/samples-powershell.md)

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $resourceGroupName -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $resourceGroupName
    ```

3. Voer de `Set-AzWebApp -AssignIdentity` opdracht uit om de identiteit voor deze toepassing te maken:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName $resourceGroupName 
    ```

#### <a name="using-azure-powershell-for-a-function-app"></a>Een Azure PowerShell gebruiken voor een functie-app

1. Installeer indien nodig de Azure PowerShell de instructies in de Azure PowerShell [en](/powershell/azure/)voer vervolgens uit om een verbinding met `Login-AzAccount` Azure te maken.

2. Maak een functie-app met behulp Azure PowerShell. Zie de [Az.Functions-naslag](/powershell/module/az.functions/#functions)voor meer voorbeelden van het Azure PowerShell met Azure Functions:

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a function app with a system-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType SystemAssigned
    ```

U kunt ook een bestaande functie-app bijwerken met behulp van `Update-AzFunctionApp` .

### <a name="using-an-azure-resource-manager-template"></a>Een sjabloon voor Azure Resource Manager gebruiken

Een Azure Resource Manager kan worden gebruikt om de implementatie van uw Azure-resources te automatiseren. Zie Resource-implementatie automatiseren [in](../app-service/deploy-complex-application-predictably.md) App Service App Service en Resource-implementatie automatiseren in Azure Functions voor meer informatie over het implementeren [in Azure Functions.](../azure-functions/functions-infrastructure-as-code.md)

Elke resource van het type kan worden gemaakt met een identiteit door de volgende eigenschap op te geven `Microsoft.Web/sites` in de resourcedefinitie:

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> Een toepassing kan tegelijkertijd zowel door het systeem toegewezen als door de gebruiker toegewezen identiteiten hebben. In dit geval is `type` de eigenschap `SystemAssigned,UserAssigned`

Door het door het systeem toegewezen type toe te voegen, moet Azure de identiteit voor uw toepassing maken en beheren.

Een web-app kan er bijvoorbeeld als volgt uitzien:

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

Wanneer de site is gemaakt, heeft deze de volgende aanvullende eigenschappen:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

De eigenschap tenantId identificeert bij welke Azure AD-tenant de identiteit hoort. De principalId is een unieke id voor de nieuwe identiteit van de toepassing. In Azure AD heeft de service-principal dezelfde naam die u aan uw App Service of Azure Functions hebt gegeven.

Als u in een latere fase in de sjabloon naar deze [ `reference()` ](../azure-resource-manager/templates/template-functions-resource.md#reference) eigenschappen moet verwijzen, kunt u dit doen via de sjabloonfunctie met de vlag `'Full'` , zoals in dit voorbeeld:

```json
{
    "tenantId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.tenantId]",
    "objectId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.principalId]",
}
```

## <a name="add-a-user-assigned-identity"></a>Een door de gebruiker toegewezen identiteit toevoegen

Voor het maken van een app met een door de gebruiker toegewezen identiteit moet u de identiteit maken en vervolgens de resource-id toevoegen aan uw app-configuratie.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Eerst moet u een door de gebruiker toegewezen identiteitsresource maken.

1. Maak een door de gebruiker toegewezen beheerde identiteitsresource volgens [deze instructies.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)

2. Maak een app in de portal zoals u dat normaal zou doen. Navigeer ernaar in de portal.

3. Als u een functie-app gebruikt, gaat u naar **Platformfuncties.** Schuif voor andere app-typen omlaag naar de **groep Instellingen** in het linkernavigatievenster.

4. Selecteer **Identiteit.**

5. Klik op **het tabblad Door** de gebruiker toegewezen op **Toevoegen.**

6. Zoek de identiteit die u eerder hebt gemaakt en selecteer deze. Klik op **Add**.

    ![Beheerde identiteit in App Service](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De volgende stappen helpen u bij het maken van een app en het toewijzen van een identiteit met behulp van Azure PowerShell.

> [!NOTE]
> De huidige versie van de Azure PowerShell-commandlets voor Azure App Service bieden geen ondersteuning voor door de gebruiker toegewezen identiteiten. De onderstaande instructies zijn voor Azure Functions.

1. Installeer indien nodig de Azure PowerShell de instructies in de Azure PowerShell [en](/powershell/azure/)voer vervolgens uit om een verbinding met `Login-AzAccount` Azure te maken.

2. Maak een functie-app met behulp Azure PowerShell. Zie de [Az.Functions-naslag](/powershell/module/az.functions/#functions)voor meer voorbeelden van Azure PowerShell met Azure Functions. Het onderstaande script maakt ook gebruik van , dat afzonderlijk moet worden geïnstalleerd volgens Create, list or delete a `New-AzUserAssignedIdentity` [user-assigned managed identity](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)using Azure PowerShell .

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create a function app with a user-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType UserAssigned -IdentityId $userAssignedIdentity.Id
    ```

U kunt ook een bestaande functie-app bijwerken met behulp van `Update-AzFunctionApp` .

### <a name="using-an-azure-resource-manager-template"></a>Een sjabloon voor Azure Resource Manager gebruiken

Een Azure Resource Manager kan worden gebruikt om de implementatie van uw Azure-resources te automatiseren. Zie Implementatie van resources automatiseren [in](../app-service/deploy-complex-application-predictably.md) App Service App Service en Implementatie van resources automatiseren in Azure Functions voor meer informatie over het implementeren [in Azure Functions.](../azure-functions/functions-infrastructure-as-code.md)

Elke resource van het type kan worden gemaakt met een identiteit door het volgende blok op te geven in de resourcedefinitie, te vervangen door de `Microsoft.Web/sites` `<RESOURCEID>` resource-id van de gewenste identiteit:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> Een toepassing kan zowel door het systeem toegewezen als door de gebruiker toegewezen identiteiten op hetzelfde moment hebben. In dit geval is `type` de eigenschap `SystemAssigned,UserAssigned`

Door het door de gebruiker toegewezen type toe te voegen, moet Azure de door de gebruiker toegewezen identiteit gebruiken die is opgegeven voor uw toepassing.

Een web-app kan er bijvoorbeeld als volgt uitzien:

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

Wanneer de site is gemaakt, heeft deze de volgende aanvullende eigenschappen:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

De principalId is een unieke id voor de identiteit die wordt gebruikt voor Azure AD-beheer. De clientId is een unieke id voor de nieuwe identiteit van de toepassing die wordt gebruikt om op te geven welke identiteit moet worden gebruikt tijdens runtime-aanroepen.

## <a name="obtain-tokens-for-azure-resources"></a>Tokens voor Azure-resources verkrijgen

Een app kan de beheerde identiteit gebruiken om tokens op te halen voor toegang tot andere resources die worden beveiligd door Azure AD, zoals Azure Key Vault. Deze tokens vertegenwoordigen de toepassing die toegang heeft tot de resource en geen specifieke gebruiker van de toepassing. 

Mogelijk moet u de doelresource configureren om toegang vanuit uw toepassing toe te staan. Als u bijvoorbeeld een token aanvraagt voor toegang tot Key Vault, moet u ervoor zorgen dat u een toegangsbeleid hebt toegevoegd dat de identiteit van uw toepassing bevat. Anders worden uw aanroepen Key Vault geweigerd, zelfs als ze het token bevatten. Zie [Azure-services](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)die ondersteuning bieden voor Azure AD-verificatie voor meer Azure Active Directory resources die ondersteuning bieden voor tokens.

> [!IMPORTANT]
> De back-endservices voor beheerde identiteiten onderhouden ongeveer 24 uur per resource-URI een cache. Als u het toegangsbeleid van een bepaalde doelresource bij te werken en onmiddellijk een token voor die resource op te halen, kunt u een token in de cache met verouderde machtigingen blijven ophalen totdat dat token verloopt. Er is momenteel geen manier om het vernieuwen van een token af te dwingen.

Er is een eenvoudig REST-protocol voor het verkrijgen van een token in App Service en Azure Functions. Dit kan worden gebruikt voor alle toepassingen en talen. Voor .NET en Java biedt de Azure SDK een abstractie via dit protocol en wordt een lokale ontwikkelervaring mogelijk gemaakt.

### <a name="using-the-rest-protocol"></a>Het REST-protocol gebruiken

> [!NOTE]
> Een oudere versie van dit protocol, met behulp van de API-versie 2017-09-01, gebruikte de header in plaats van en accepteerde alleen de eigenschap voor door de gebruiker `secret` `X-IDENTITY-HEADER` `clientid` toegewezen. Ook is de `expires_on` geretourneerd in een tijdstempelnotatie. MSI_ENDPOINT kunnen worden gebruikt als een alias voor IDENTITY_ENDPOINT en MSI_SECRET kunnen worden gebruikt als alias voor IDENTITY_HEADER. Deze versie van het protocol is momenteel vereist voor Linux-verbruik hostingplannen.

Voor een app met een beheerde identiteit zijn twee omgevingsvariabelen gedefinieerd:

- IDENTITY_ENDPOINT de URL naar de lokale tokenservice.
- IDENTITY_HEADER- een header die wordt gebruikt om aanvragenvervalsing aan de serverzijde (SSRF)-aanvallen te beperken. De waarde wordt geroteerd door het platform.

De **IDENTITY_ENDPOINT** is een lokale URL van waaruit uw app tokens kan aanvragen. Als u een token voor een resource wilt op halen, maakt u een HTTP GET-aanvraag naar dit eindpunt, met inbegrip van de volgende parameters:

> | Parameternaam    | In     | Description                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | resource          | Query  | De Azure AD-resource-URI van de resource waarvoor een token moet worden verkregen. Dit kan een van de [Azure-services zijn die ondersteuning bieden voor Azure AD-verificatie](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) of een andere resource-URI.    |
> | api-versie       | Query  | De versie van de token-API die moet worden gebruikt. Gebruik 2019-08-01 of hoger (tenzij u Linux-verbruik gebruikt, dat momenteel alleen '2017-09-01' biedt. Zie opmerking hierboven).                                                                                                                                                                                                                                                                 |
> | X-IDENTITY-HEADER | Header | De waarde van de IDENTITY_HEADER omgevingsvariabele. Deze header wordt gebruikt om aanvragenvervalsing aan de serverzijde (SSRF)-aanvallen te beperken.                                                                                                                                                                                                    |
> | client_id         | Query  | (Optioneel) De client-id van de door de gebruiker toegewezen identiteit die moet worden gebruikt. Kan niet worden gebruikt voor een aanvraag die `principal_id` , `mi_res_id` of `object_id` bevat. Als alle id-parameters ( , , en ) worden weggelaten, wordt de door het systeem toegewezen `client_id` `principal_id` identiteit `object_id` `mi_res_id` gebruikt.                                             |
> | principal_id      | Query  | (Optioneel) De principal-id van de door de gebruiker toegewezen identiteit die moet worden gebruikt. `object_id` is een alias die in plaats daarvan kan worden gebruikt. Kan niet worden gebruikt voor een aanvraag die client_id, mi_res_id of object_id. Als alle id-parameters ( , , en ) worden weggelaten, wordt de door het systeem toegewezen `client_id` `principal_id` identiteit `object_id` `mi_res_id` gebruikt. |
> | mi_res_id         | Query  | (Optioneel) De Azure-resource-id van de door de gebruiker toegewezen identiteit die moet worden gebruikt. Kan niet worden gebruikt voor een aanvraag met `principal_id` , `client_id` of `object_id` . Als alle id-parameters ( , , en ) worden weggelaten, wordt de door het systeem toegewezen `client_id` `principal_id` identiteit `object_id` `mi_res_id` gebruikt.                                      |

> [!IMPORTANT]
> Als u tokens wilt verkrijgen voor door de gebruiker toegewezen identiteiten, moet u een van de optionele eigenschappen opnemen. Anders probeert de tokenservice een token te verkrijgen voor een door het systeem toegewezen identiteit, die al dan niet bestaat.

Een geslaagd 200 OK-antwoord bevat een JSON-body met de volgende eigenschappen:

> | Naam van eigenschap | Description                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | Het aangevraagde toegangs token. De aanroepende webservice kan dit token gebruiken om te verifiëren bij de ontvangende webservice.                                                                                                                               |
> | client_id     | De client-id van de identiteit die is gebruikt.                                                                                                                                                                                                       |
> | expires_on    | De periode waarin het toegangs token verloopt. De datum wordt weergegeven als het aantal seconden van '1970-01-01T0:0:0Z UTC' (komt overeen met de claim van het `exp` token).                                                                                |
> | not_before    | De periode waarin het toegangsken van kracht wordt en kan worden geaccepteerd. De datum wordt weergegeven als het aantal seconden van '1970-01-01T0:0:0Z UTC' (komt overeen met de claim van het `nbf` token).                                                      |
> | resource      | De resource waarvoor het toegangs token is aangevraagd, die overeenkomt met de `resource` queryreeksparameter van de aanvraag.                                                                                                                               |
> | token_type    | Geeft de waarde van het tokentype aan. Het enige type dat door Azure AD wordt ondersteund, is Bearer. Zie het [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750) voor](https://www.rfc-editor.org/rfc/rfc6750.txt)meer informatie over Bearer-tokens. |

Dit antwoord is hetzelfde als het antwoord voor de toegangsaanvraag van de [Azure AD-service-naar-service-toegang.](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response)

### <a name="rest-protocol-examples"></a>Voorbeelden van REST-protocollen

Een voorbeeldaanvraag kan er als volgt uitzien:

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

En een voorbeeld van een antwoord kan er als volgt uitzien:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "1586984735",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer",
    "client_id": "5E29463D-71DA-4FE0-8E69-999B57DB23B0"
}
```

### <a name="code-examples"></a>Codevoorbeelden

# <a name="net"></a>[.NET](#tab/dotnet)

> [!TIP]
> Voor .NET-talen kunt u ook [Microsoft.Azure.Services.AppAuthentication](#asal) gebruiken in plaats van deze aanvraag zelf te maken.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2019-08-01", Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT"), resource));
    request.Headers.Add("X-IDENTITY-HEADER", Environment.GetEnvironmentVariable("IDENTITY_HEADER"));
    return await _client.SendAsync(request);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["IDENTITY_ENDPOINT"]}/?resource=${resource}&api-version=2019-08-01`,
        headers: {
            'X-IDENTITY-HEADER': process.env["IDENTITY_HEADER"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="python"></a>[Python](#tab/python)

```python
import os
import requests

identity_endpoint = os.environ["IDENTITY_ENDPOINT"]
identity_header = os.environ["IDENTITY_HEADER"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{identity_endpoint}?resource={resource_uri}&api-version=2019-08-01"
    head_msi = {'X-IDENTITY-HEADER':identity_header}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:IDENTITY_ENDPOINT + "?resource=$resourceURI&api-version=2019-08-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"X-IDENTITY-HEADER"="$env:IDENTITY_HEADER"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>De bibliotheek Microsoft.Azure.Services.AppAuthentication voor .NET gebruiken

Voor .NET-toepassingen en -functies is de eenvoudigste manier om met een beheerde identiteit te werken via het pakket Microsoft.Azure.Services.AppAuthentication. Met deze bibliotheek kunt u uw code ook lokaal testen op uw ontwikkelmachine, met behulp van uw gebruikersaccount van Visual Studio, [de Azure CLI](/cli/azure)of geïntegreerde Active Directory-verificatie. Wanneer deze wordt gehost in de cloud, wordt standaard een door het systeem toegewezen identiteit gebruikt, maar u kunt dit gedrag aanpassen met behulp van een connection string-omgevingsvariabele die verwijst naar de client-id van een door de gebruiker toegewezen identiteit. Zie de naslag voor [Microsoft.Azure.Services.AppAuthentication voor]meer informatie over ontwikkelingsopties met deze bibliotheek. In deze sectie ziet u hoe u aan de slag gaat met de bibliotheek in uw code.

1. Voeg verwijzingen naar [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) en eventuele andere benodigde NuGet-pakketten toe aan uw toepassing. In het onderstaande voorbeeld wordt [ook Microsoft.Azure.KeyVault gebruikt.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

2. Voeg de volgende code toe aan uw toepassing en wijzig deze om de juiste resource te bereiken. In dit voorbeeld ziet u twee manieren om te werken met Azure Key Vault:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Als u een door de gebruiker toegewezen beheerde identiteit wilt gebruiken, kunt u de `AzureServicesAuthConnectionString` toepassingsinstelling instellen op `RunAs=App;AppId=<clientId-guid>` . Vervang `<clientId-guid>` door de client-id van de identiteit die u wilt gebruiken. U kunt meerdere van dergelijke verbindingsreeksen definiëren met behulp van aangepaste toepassingsinstellingen en hun waarden doorgeven aan de AzureServiceTokenProvider-constructor.

```csharp
    var identityConnectionString1 = Environment.GetEnvironmentVariable("UA1_ConnectionString");
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider(identityConnectionString1);
    
    var identityConnectionString2 = Environment.GetEnvironmentVariable("UA2_ConnectionString");
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider(identityConnectionString2);
```

Zie de naslag voor [Microsoft.Azure.Services.AppAuthentication] en het voorbeeld van App Service en [KeyVault met MSI .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)voor meer informatie over het configureren van AzureServiceTokenProvider en de bewerkingen die worden uitgevoerd.

### <a name="using-the-azure-sdk-for-java"></a>De Azure SDK voor Java gebruiken

Voor Java-toepassingen en -functies is de eenvoudigste manier om met een beheerde identiteit te werken via de [Azure SDK voor Java](https://github.com/Azure/azure-sdk-for-java). In deze sectie ziet u hoe u aan de slag gaat met de bibliotheek in uw code.

1. Voeg een verwijzing toe naar de [Azure SDK-bibliotheek](https://mvnrepository.com/artifact/com.microsoft.azure/azure). Voor Maven-projecten kunt u dit fragment toevoegen aan de `dependencies` sectie van het POM-bestand van het project:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Gebruik het `AppServiceMSICredentials` -object voor verificatie. In dit voorbeeld ziet u hoe dit mechanisme kan worden gebruikt voor het werken met Azure Key Vault:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove-an-identity"></a><a name="remove"></a>Een identiteit verwijderen

Een door het systeem toegewezen identiteit kan worden verwijderd door de functie uit te uitschakelen via de portal, PowerShell of CLI op dezelfde manier als de functie is gemaakt. Door de gebruiker toegewezen identiteiten kunnen afzonderlijk worden verwijderd. Als u alle identiteiten wilt verwijderen, stelt u het identiteitstype in op Geen.

Als u een door het systeem toegewezen identiteit op deze manier verwijdert, wordt deze ook verwijderd uit Azure AD. Door het systeem toegewezen identiteiten worden ook automatisch verwijderd uit Azure AD wanneer de app-resource wordt verwijderd.

Alle identiteiten in een [ARM-sjabloon verwijderen:](#using-an-azure-resource-manager-template)

```json
"identity": {
    "type": "None"
}
```

Alle identiteiten in de Azure PowerShell verwijderen (Azure Functions alleen):

```azurepowershell-interactive
# Update an existing function app to have IdentityType "None".
Update-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -IdentityType None
```

> [!NOTE]
> Er is ook een toepassingsinstelling die kan worden ingesteld, WEBSITE_DISABLE_MSI, waardoor alleen de lokale tokenservice wordt uitgeschakeld. De identiteit blijft echter op zijn plaats en hulpprogramma's tonen de beheerde identiteit nog steeds als 'aan' of 'ingeschakeld'. Als gevolg hiervan wordt het gebruik van deze instelling niet aanbevolen.

## <a name="next-steps"></a>Volgende stappen

- [Toegang SQL Database met een beheerde identiteit](app-service-web-tutorial-connect-msi.md)
- [Toegang Azure Storage met behulp van een beheerde identiteit](scenario-secure-app-access-storage.md)
- [Een Microsoft Graph aanroepen met behulp van een beheerde identiteit](scenario-secure-app-access-microsoft-graph-as-app.md)

[Microsoft.Azure.Services.AppAuthentication-referentie]: /dotnet/api/overview/azure/service-to-service-authentication