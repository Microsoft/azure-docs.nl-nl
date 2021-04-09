---
title: Beheerde identiteiten
description: Meer informatie over hoe beheerde identiteiten werken in Azure App Service en Azure Functions, hoe u een beheerde identiteit kunt configureren en een token voor een back-end-bron kunt genereren.
author: mattchenderson
ms.topic: article
ms.date: 05/27/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 16cd4685f513eb628372802cc158195b81bce72a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736168"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Beheerde identiteiten gebruiken voor App Service en Azure Functions

In dit onderwerp wordt beschreven hoe u een beheerde identiteit kunt maken voor App Service en Azure Functions toepassingen en hoe u deze kunt gebruiken om toegang te krijgen tot andere resources. 

> [!Important] 
> Beheerde identiteiten voor App Service en Azure Functions werken niet zoals verwacht als uw app wordt gemigreerd tussen abonnementen/tenants. De app moet een nieuwe identiteit verkrijgen. dit wordt gedaan door de functie uit te scha kelen en opnieuw in te scha kelen. Zie hieronder [een identiteit verwijderen](#remove) . Downstream-resources moeten ook toegangs beleid hebben bijgewerkt voor het gebruik van de nieuwe identiteit.

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

## <a name="add-a-system-assigned-identity"></a>Een door het systeem toegewezen identiteit toevoegen

Voor het maken van een app met een door het systeem toegewezen identiteit moet er een extra eigenschap worden ingesteld voor de toepassing.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Als u een beheerde identiteit in de portal instelt, moet u eerst een toepassing als normaal aanmaken en vervolgens de functie inschakelen.

1. Maak een app in de portal zoals u dat gewend bent. Navigeer ernaar in de portal.

2. Als u een functie-app gebruikt, navigeert u naar **platform functies**. Voor andere typen apps schuift u omlaag naar de **instellingen** groep in het linkernavigatievenster.

3. Selecteer **identiteit**.

4. Schakel op het tabblad **systeem toegewezen** de optie **status** in **op aan**. Klik op **Opslaan**.

    ![Scherm opname van de plaats waar u de status wilt wijzigen in en selecteer vervolgens opslaan.](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)


> [!NOTE] 
> Als u de beheerde identiteit voor de web-app of de app wilt vinden in de Azure Portal, gaat u naar de sectie **gebruikers instellingen** onder **bedrijfs toepassingen**. Normaal gesp roken is de naam van de sleuf gelijk aan `<app name>/slots/<slot name>` .


### <a name="using-the-azure-cli"></a>Met behulp van de Azure CLI

Als u een beheerde identiteit wilt instellen met behulp van de Azure CLI, moet u de opdracht gebruiken voor `az webapp identity assign` een bestaande toepassing. Er zijn drie opties voor het uitvoeren van de voor beelden in deze sectie:

- Gebruik [Azure Cloud shell](../cloud-shell/overview.md) van de Azure Portal.
- Gebruik de Inge sloten Azure Cloud Shell via de knop ' Probeer het ', in de rechter bovenhoek van elk hieronder opgenomen code blok.
- [Installeer de nieuwste versie van Azure cli](/cli/azure/install-azure-cli) (2.0.31 of hoger) als u liever een lokale cli-console gebruikt. 

De volgende stappen helpen u bij het maken van een web-app en het toewijzen van een-id met behulp van de CLI:

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login](/cli/azure/reference-index#az-login). Gebruik een account dat is gekoppeld aan het Azure-abonnement waaronder u de toepassing wilt implementeren:

    ```azurecli-interactive
    az login
    ```

2. Maak een webtoepassing met behulp van de CLI. Voor meer voor beelden van het gebruik van de CLI met App Service raadpleegt u [app service cli](../app-service/samples-cli.md)-voor beelden:

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

De volgende stappen helpen u bij het maken van een app en het toewijzen van een id met behulp van Azure PowerShell. De instructies voor het maken van een web-app en een functie-app verschillen.

#### <a name="using-azure-powershell-for-a-web-app"></a>Azure PowerShell gebruiken voor een web-app

1. Als dat nodig is, installeert u de Azure PowerShell met behulp van de instructies in de [Azure PowerShell Guide](/powershell/azure/)en voert `Login-AzAccount` u uit om een verbinding te maken met Azure.

2. Maak een webtoepassing met behulp van Azure PowerShell. Zie voor meer voor beelden van het gebruik van Azure PowerShell met App Service [app Service Power shell](../app-service/samples-powershell.md)-voor beelden:

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

#### <a name="using-azure-powershell-for-a-function-app"></a>Azure PowerShell gebruiken voor een functie-app

1. Als dat nodig is, installeert u de Azure PowerShell met behulp van de instructies in de [Azure PowerShell Guide](/powershell/azure/)en voert `Login-AzAccount` u uit om een verbinding te maken met Azure.

2. Maak een functie-app met behulp van Azure PowerShell. Zie voor meer voor beelden van het gebruik van Azure PowerShell met Azure Functions de [verwijzing AZ. functions](/powershell/module/az.functions/#functions):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a function app with a system-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType SystemAssigned
    ```

U kunt ook een bestaande functie-app bijwerken met behulp van `Update-AzFunctionApp` in plaats daarvan.

### <a name="using-an-azure-resource-manager-template"></a>Een Azure Resource Manager-sjabloon gebruiken

Een Azure Resource Manager sjabloon kan worden gebruikt voor het automatiseren van de implementatie van uw Azure-resources. Zie voor meer informatie over de implementatie van App Service en functions de [implementatie van resources automatiseren in app service](../app-service/deploy-complex-application-predictably.md) en de [implementatie van resources in azure functions automatiseren](../azure-functions/functions-infrastructure-as-code.md).

U kunt een bron van `Microsoft.Web/sites` het type maken met een identiteit door de volgende eigenschap op te nemen in de resource definitie:

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> Een toepassing kan gelijktijdig een door het systeem toegewezen en door de gebruiker toegewezen identiteiten hebben. In dit geval is de `type` eigenschap `SystemAssigned,UserAssigned`

Door het door het systeem toegewezen type toe te voegen, vertelt Azure om de identiteit voor uw toepassing te maken en te beheren.

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

Wanneer de site wordt gemaakt, heeft deze de volgende aanvullende eigenschappen:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

De eigenschap tenantId identificeert de Azure AD-Tenant waartoe de identiteit behoort. De principalId is een unieke id voor de nieuwe identiteit van de toepassing. De Service-Principal in azure AD heeft dezelfde naam die u hebt gegeven aan uw App Service-of Azure Functions-exemplaar.

Als u in een latere fase van de sjabloon naar deze eigenschappen moet verwijzen, kunt u dit doen via de [ `reference()` sjabloon functie](../azure-resource-manager/templates/template-functions-resource.md#reference) met de `'Full'` vlag, zoals in dit voor beeld:

```json
{
    "tenantId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.tenantId]",
    "objectId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.principalId]",
}
```

## <a name="add-a-user-assigned-identity"></a>Een door de gebruiker toegewezen identiteit toevoegen

Voor het maken van een app met een door de gebruiker toegewezen identiteit moet u de identiteit maken en vervolgens de resource-id toevoegen aan uw app-configuratie.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Eerst moet u een door de gebruiker toegewezen id-resource maken.

1. Maak een door de gebruiker toegewezen beheerde identiteits bron op basis van [deze instructies](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Maak een app in de portal zoals u dat gewend bent. Navigeer ernaar in de portal.

3. Als u een functie-app gebruikt, navigeert u naar **platform functies**. Voor andere typen apps schuift u omlaag naar de **instellingen** groep in het linkernavigatievenster.

4. Selecteer **identiteit**.

5. Klik op het tabblad **toegewezen door gebruiker** op **toevoegen**.

6. Zoek de identiteit die u eerder hebt gemaakt en selecteer deze. Klik op **Add**.

    ![Beheerde identiteit in App Service](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De volgende stappen helpen u bij het maken van een app en het toewijzen van een id met behulp van Azure PowerShell.

> [!NOTE]
> De huidige versie van de Azure PowerShell Commandlets voor Azure App Service biedt geen ondersteuning voor door de gebruiker toegewezen identiteiten. De onderstaande instructies zijn voor Azure Functions.

1. Als dat nodig is, installeert u de Azure PowerShell met behulp van de instructies in de [Azure PowerShell Guide](/powershell/azure/)en voert `Login-AzAccount` u uit om een verbinding te maken met Azure.

2. Maak een functie-app met behulp van Azure PowerShell. Zie de [Naslag informatie over AZ. functions](/powershell/module/az.functions/#functions)(Engelstalig) voor meer voor beelden van het gebruik van Azure PowerShell met Azure functions. Het onderstaande script maakt ook gebruik van `New-AzUserAssignedIdentity` dat afzonderlijk moet worden geïnstalleerd als per [Create, lijst of een door de gebruiker toegewezen beheerde identiteit te verwijderen met behulp van Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

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

U kunt ook een bestaande functie-app bijwerken met behulp van `Update-AzFunctionApp` in plaats daarvan.

### <a name="using-an-azure-resource-manager-template"></a>Een Azure Resource Manager-sjabloon gebruiken

Een Azure Resource Manager sjabloon kan worden gebruikt voor het automatiseren van de implementatie van uw Azure-resources. Zie voor meer informatie over de implementatie van App Service en functions de [implementatie van resources automatiseren in app service](../app-service/deploy-complex-application-predictably.md) en de [implementatie van resources in azure functions automatiseren](../azure-functions/functions-infrastructure-as-code.md).

U kunt een bron van `Microsoft.Web/sites` het type maken met een identiteit door het volgende blok in de resource definitie op te nemen, waarbij u vervangt door `<RESOURCEID>` de resource-id van de gewenste identiteit:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> Een toepassing kan gelijktijdig een door het systeem toegewezen en door de gebruiker toegewezen identiteiten hebben. In dit geval is de `type` eigenschap `SystemAssigned,UserAssigned`

Als u het door de gebruiker toegewezen type toevoegt, krijgt Azure de door de gebruiker toegewezen identiteit te gebruiken die voor uw toepassing is opgegeven.

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

Wanneer de site wordt gemaakt, heeft deze de volgende aanvullende eigenschappen:

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

De principalId is een unieke id voor de identiteit die wordt gebruikt voor Azure AD-beheer. De clientId is een unieke id voor de nieuwe identiteit van de toepassing die wordt gebruikt om op te geven welke identiteit tijdens runtime-aanroepen moet worden gebruikt.

## <a name="obtain-tokens-for-azure-resources"></a>Tokens verkrijgen voor Azure-resources

Een app kan de beheerde identiteit gebruiken om tokens te verkrijgen voor toegang tot andere bronnen die worden beveiligd door Azure AD, zoals Azure Key Vault. Deze tokens vertegenwoordigen de toepassing die toegang heeft tot de resource en geen specifieke gebruiker van de toepassing. 

Mogelijk moet u de doel bron configureren om toegang toe te staan vanuit uw toepassing. Als u bijvoorbeeld een token aanvraagt om toegang te krijgen tot Key Vault, moet u ervoor zorgen dat u een toegangs beleid hebt toegevoegd dat de identiteit van uw toepassing bevat. Anders worden uw aanroepen naar Key Vault geweigerd, zelfs als ze het token bevatten. Zie [Azure-Services die ondersteuning bieden voor Azure AD-verificatie](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)voor meer informatie over welke bronnen Azure Active Directory-tokens ondersteunen.

> [!IMPORTANT]
> De back-end-services voor beheerde identiteiten zorgen voor een cache per resource-URI gedurende circa 24 uur. Als u het toegangs beleid van een bepaalde doel resource bijwerkt en direct een token voor die bron ophaalt, kunt u een token in de cache met verouderde machtigingen ontvangen totdat het token verloopt. Er is momenteel geen manier om het vernieuwen van tokens af te dwingen.

Er is een eenvoudig REST-protocol voor het verkrijgen van een token in App Service en Azure Functions. Dit kan worden gebruikt voor alle toepassingen en talen. Voor .NET en Java biedt de Azure SDK een abstractie over dit protocol en wordt een lokale ontwikkel ervaring vergemakkelijkt.

### <a name="using-the-rest-protocol"></a>Het REST-protocol gebruiken

> [!NOTE]
> Een oudere versie van dit protocol, met behulp van de API-versie 2017-09-01, heeft de `secret` header gebruikt in plaats van `X-IDENTITY-HEADER` en heeft alleen de eigenschap geaccepteerd voor door de `clientid` gebruiker toegewezen. Er wordt ook `expires_on` een time stamp-notatie geretourneerd. MSI_ENDPOINT kan worden gebruikt als een alias voor IDENTITY_ENDPOINT en MSI_SECRET kan worden gebruikt als een alias voor IDENTITY_HEADER. Deze versie van het protocol is momenteel vereist voor hosting plannen voor Linux-verbruik.

Er zijn twee omgevings variabelen gedefinieerd voor een app met een beheerde identiteit:

- IDENTITY_ENDPOINT: de URL naar de lokale token service.
- IDENTITY_HEADER: een kop die wordt gebruikt om SSRF-aanvallen (server-side Request vervalsing) te voor komen. De waarde wordt geroteerd door het platform.

De **IDENTITY_ENDPOINT** is een lokale URL van waaruit uw app tokens kan aanvragen. Als u een token voor een resource wilt ophalen, maakt u een HTTP GET-aanvraag naar dit eind punt, met inbegrip van de volgende para meters:

> | Parameternaam    | In     | Description                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | resource          | Query  | De Azure AD-resource-URI van de resource waarvoor een token moet worden verkregen. Dit kan een van de [Azure-Services zijn die ondersteuning bieden voor Azure AD-verificatie](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) of een andere resource-URI.    |
> | api-versie       | Query  | De versie van de token-API die moet worden gebruikt. Gebruik ' 2019-08-01 ' of hoger (tenzij u Linux-verbruik gebruikt, dat momenteel alleen ' 2017-09-01 ' biedt).                                                                                                                                                                                                                                                                 |
> | X-IDENTITEIT-HEADER | Header | De waarde van de omgevings variabele IDENTITY_HEADER. Deze header wordt gebruikt om SSRF-aanvallen (server-side Request vervalsing) te voor komen.                                                                                                                                                                                                    |
> | client_id         | Query  | Beschrijving De client-ID van de door de gebruiker toegewezen identiteit die moet worden gebruikt. Kan niet worden gebruikt voor een aanvraag met `principal_id` , `mi_res_id` of `object_id` . Als alle id-para meters ( `client_id` ,, `principal_id` `object_id` en `mi_res_id` ) worden wegge laten, wordt de door het systeem toegewezen identiteit gebruikt.                                             |
> | principal_id      | Query  | Beschrijving De principal-ID van de door de gebruiker toegewezen identiteit die moet worden gebruikt. `object_id` is een alias die in plaats daarvan kan worden gebruikt. Kan niet worden gebruikt voor een aanvraag die client_id, mi_res_id of object_id bevat. Als alle id-para meters ( `client_id` ,, `principal_id` `object_id` en `mi_res_id` ) worden wegge laten, wordt de door het systeem toegewezen identiteit gebruikt. |
> | mi_res_id         | Query  | Beschrijving De Azure-Resource-ID van de door de gebruiker toegewezen identiteit die moet worden gebruikt. Kan niet worden gebruikt voor een aanvraag met `principal_id` , `client_id` of `object_id` . Als alle id-para meters ( `client_id` ,, `principal_id` `object_id` en `mi_res_id` ) worden wegge laten, wordt de door het systeem toegewezen identiteit gebruikt.                                      |

> [!IMPORTANT]
> Als u probeert tokens te verkrijgen voor door de gebruiker toegewezen identiteiten, moet u een van de optionele eigenschappen toevoegen. Anders probeert de token service een token te verkrijgen voor een door het systeem toegewezen identiteit, die al dan niet bestaat.

Een geslaagd 200 OK-antwoord bevat een JSON-hoofd tekst met de volgende eigenschappen:

> | Naam van eigenschap | Description                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | Het aangevraagde toegangs token. De aanroepende webservice kan dit token gebruiken om te verifiëren bij de ontvangende webservice.                                                                                                                               |
> | client_id     | De client-ID van de identiteit die is gebruikt.                                                                                                                                                                                                       |
> | expires_on    | De time span op het moment dat het toegangs token verloopt. De datum wordt weer gegeven als het aantal seconden van ' 1970-01-01T0:0: 0Z UTC ' (komt overeen met de claim van het token `exp` ).                                                                                |
> | not_before    | De time span wanneer het toegangs token van kracht is en kan worden geaccepteerd. De datum wordt weer gegeven als het aantal seconden van ' 1970-01-01T0:0: 0Z UTC ' (komt overeen met de claim van het token `nbf` ).                                                      |
> | resource      | De bron waarvoor het toegangs token is aangevraagd, dat overeenkomt met de `resource` query teken reeks parameter van de aanvraag.                                                                                                                               |
> | token_type    | Geeft de waarde van het token type aan. Het enige type dat door Azure AD wordt ondersteund, is Bearer. Zie voor meer informatie over Bearer-tokens [het OAuth 2,0 Authorization Framework: Bearer-token gebruik (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |

Dit antwoord is hetzelfde als het [antwoord op de aanvraag van de Azure AD-service-naar-service-toegangs token](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

### <a name="rest-protocol-examples"></a>Voor beelden van REST-protocollen

Een voorbeeld aanvraag kan er als volgt uitzien:

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

En een voor beeld van een antwoord kan er als volgt uitzien:

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
> Voor .NET-talen kunt u ook gebruikmaken van [micro soft. Azure. Services. AppAuthentication](#asal) in plaats van deze aanvraag zelf te vervaardigen.

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

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>De bibliotheek micro soft. Azure. Services. AppAuthentication voor .NET gebruiken

Voor .NET-toepassingen en-functies is de eenvoudigste manier om te werken met een beheerde identiteit via het pakket micro soft. Azure. Services. AppAuthentication. Met deze bibliotheek kunt u uw code ook lokaal op uw ontwikkel computer testen met behulp van uw gebruikers account uit Visual Studio, de [Azure cli](/cli/azure)of Active Directory geïntegreerde verificatie. Als deze in de cloud worden gehost, wordt standaard een door het systeem toegewezen identiteit gebruikt, maar u kunt dit gedrag aanpassen met behulp van een connection string omgevings variabele die verwijst naar de client-ID van een door de gebruiker toegewezen identiteit. Voor meer informatie over de ontwikkelings opties voor deze bibliotheek raadpleegt u de [Naslag informatie voor micro soft. Azure. Services. AppAuthentication]. In deze sectie wordt beschreven hoe u aan de slag kunt met de bibliotheek in uw code.

1. Voeg verwijzingen naar [micro soft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) en alle andere benodigde NuGet-pakketten toe aan uw toepassing. In het onderstaande voor beeld wordt ook gebruikgemaakt van [micro soft. Azure.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)de sleutel kluis.

2. Voeg de volgende code toe aan uw toepassing, waarbij u de juiste resource wijzigt. In dit voor beeld ziet u twee manieren om te werken met Azure Key Vault:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Als u een door de gebruiker toegewezen beheerde identiteit wilt gebruiken, kunt u de `AzureServicesAuthConnectionString` toepassings instelling instellen op `RunAs=App;AppId=<clientId-guid>` . Vervang door `<clientId-guid>` de client-id van de identiteit die u wilt gebruiken. U kunt meerdere dergelijke verbindings reeksen definiëren door aangepaste toepassings instellingen te gebruiken en hun waarden door te geven aan de AzureServiceTokenProvider-constructor.

```csharp
    var identityConnectionString1 = Environment.GetEnvironmentVariable("UA1_ConnectionString");
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider(identityConnectionString1);
    
    var identityConnectionString2 = Environment.GetEnvironmentVariable("UA2_ConnectionString");
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider(identityConnectionString2);
```

Zie voor meer informatie over het configureren van AzureServiceTokenProvider en de bewerkingen die worden weer gegeven de naslag informatie over [micro soft. Azure. Services. AppAuthentication] en het [app service en de sleutel kluis met MSI .net-voor beeld](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>De Azure SDK voor Java gebruiken

Voor Java-toepassingen en-functies is de eenvoudigste manier om te werken met een beheerde identiteit via de [Azure SDK voor Java](https://github.com/Azure/azure-sdk-for-java). In deze sectie wordt beschreven hoe u aan de slag kunt met de bibliotheek in uw code.

1. Voeg een verwijzing naar de [Azure SDK-bibliotheek](https://mvnrepository.com/artifact/com.microsoft.azure/azure)toe. Voor maven-projecten kunt u dit fragment toevoegen aan de `dependencies` sectie van het pom-bestand van het project:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Gebruik het `AppServiceMSICredentials` object voor authenticatie. Dit voor beeld laat zien hoe dit mechanisme kan worden gebruikt voor het werken met Azure Key Vault:

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

Een door het systeem toegewezen identiteit kan worden verwijderd door de functie uit te scha kelen met behulp van de portal, Power shell of CLI op dezelfde manier als waarop deze is gemaakt. Door de gebruiker toegewezen identiteiten kunnen afzonderlijk worden verwijderd. Als u alle identiteiten wilt verwijderen, stelt u het identiteits type in op geen.

Als u een door het systeem toegewezen identiteit op deze manier verwijdert, wordt deze ook uit Azure AD verwijderd. Door het systeem toegewezen identiteiten worden ook automatisch verwijderd uit Azure AD wanneer de app-resource wordt verwijderd.

Alle identiteiten in een [arm-sjabloon](#using-an-azure-resource-manager-template)verwijderen:

```json
"identity": {
    "type": "None"
}
```

Alle identiteiten in Azure PowerShell verwijderen (alleen Azure Functions):

```azurepowershell-interactive
# Update an existing function app to have IdentityType "None".
Update-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -IdentityType None
```

> [!NOTE]
> Er is ook een toepassings instelling die kan worden ingesteld, WEBSITE_DISABLE_MSI, waarmee de lokale token service wordt uitgeschakeld. De identiteit blijft echter aanwezig en er wordt nog steeds de beheerde identiteit weer gegeven als ' aan ' of ' ingeschakeld '. Als gevolg hiervan wordt het gebruik van deze instelling niet aanbevolen.

## <a name="next-steps"></a>Volgende stappen

- [Veilig toegang SQL Database met behulp van een beheerde identiteit](app-service-web-tutorial-connect-msi.md)
- [Veilig toegang Azure Storage met behulp van een beheerde identiteit](scenario-secure-app-access-storage.md)
- [Microsoft Graph veilig aanroepen met behulp van een beheerde identiteit](scenario-secure-app-access-microsoft-graph-as-app.md)

[Naslag informatie over micro soft. Azure. Services. AppAuthentication]: /dotnet/api/overview/azure/service-to-service-authentication