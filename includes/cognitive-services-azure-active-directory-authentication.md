---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 05/11/2020
ms.openlocfilehash: 2d186463f340be14113228baa583fdcf6ff55401
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510940"
---
## <a name="authenticate-with-azure-active-directory"></a>Verifiëren met Azure Active Directory

> [!IMPORTANT]
> 1. Momenteel zijn **alleen** de Computer Vision-API, Face-API, Text Analytics-API, insluitende lezer, formulier herkenning, afwijkings Detector, QnA maker en alle Bing-services, met uitzonde ring van Bing aangepaste zoekopdrachten ondersteuning voor verificatie met behulp van Azure Active Directory (Aad).
> 2. AAD-verificatie moet altijd worden gebruikt in combi natie met aangepaste subdomeinnaam van uw Azure-resource. [Regionale eind punten](../articles/cognitive-services/cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) bieden geen ondersteuning voor Aad-verificatie.

In de vorige secties laten we u zien hoe u met Azure Cognitive Services kunt verifiëren met behulp van een single-service of een sleutel van een abonnement op meerdere services. Hoewel deze sleutels een snel en eenvoudig pad bieden om te beginnen met de ontwikkeling, vallen ze korter in complexere scenario's waarvoor Azure op rollen gebaseerd toegangs beheer (Azure RBAC) vereist. Laten we eens kijken wat u nodig hebt om te verifiëren met behulp van Azure Active Directory (AAD).

In de volgende secties gebruikt u de Azure Cloud Shell-omgeving of de Azure CLI voor het maken van een subdomein, het toewijzen van rollen en het verkrijgen van een Bearer-token om de Azure Cognitive Services aan te roepen. Als u vastloopt, worden er links in elke sectie weer gegeven met alle beschik bare opties voor elke opdracht in Azure Cloud Shell/Azure CLI.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Een resource maken met een aangepast subdomein

De eerste stap is het maken van een aangepast subdomein. Als u een bestaande Cognitive Services resource zonder aangepaste subdomeinnaam wilt gebruiken, volgt u de instructies in [Cognitive Services aangepaste subdomeinen](../articles/cognitive-services/cognitive-services-custom-subdomains.md#how-does-this-impact-existing-resources) om het aangepaste subdomein voor uw resource in te scha kelen.

1. Begin met het openen van de Azure Cloud Shell. [Selecteer vervolgens een abonnement](/powershell/module/az.accounts/set-azcontext):

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. Maak vervolgens [een Cognitive Services resource](/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount) met een aangepast subdomein. De naam van het subdomein moet globaal uniek zijn en mag geen speciale tekens bevatten, zoals: ".", "!", ",".

   ```powershell-interactive
   $account = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. Als dit lukt, moet het **eind punt** de subdomeinnaam weer geven die uniek is voor uw resource.


### <a name="assign-a-role-to-a-service-principal"></a>Een rol toewijzen aan een Service-Principal

Nu u een aangepast subdomein hebt dat aan uw resource is gekoppeld, moet u een rol aan een Service-Principal toewijzen.

> [!NOTE]
> Houd er rekening mee dat Azure-roltoewijzingen het Maxi maal vijf minuten kan duren voordat deze wordt door gegeven.

1. Eerst gaan we een Aad- [toepassing](/powershell/module/Az.Resources/New-AzADApplication)registreren.

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   $app = New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   U hebt de **ApplicationId** nodig in de volgende stap.

2. Vervolgens moet u [een service-principal maken](/powershell/module/az.resources/new-azadserviceprincipal) voor de Aad-toepassing.

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Als u een toepassing registreert in de Azure Portal, wordt deze stap voor u voltooid.

3. De laatste stap is het [toewijzen van de rol ' Cognitive Services gebruiker '](/powershell/module/az.Resources/New-azRoleAssignment) aan de Service-Principal (bereik van de resource). Door een rol toe te wijzen, verleent u Service-Principal-toegang tot deze resource. U kunt dezelfde service-principal toegang verlenen tot meerdere resources in uw abonnement.
   >[!NOTE]
   > De ObjectId van de service-principal wordt gebruikt, niet de ObjectId voor de toepassing.
   > De ACCOUNT_ID is de Azure-resource-id van het Cognitive Services account dat u hebt gemaakt. U vindt de Azure-resource-id van ' Eigenschappen ' van de resource in Azure Portal.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Voorbeeld aanvraag

In dit voor beeld wordt een wacht woord gebruikt voor het verifiëren van de Service-Principal. Het gegeven token wordt vervolgens gebruikt om de Computer Vision-API aan te roepen.

1. Down load uw **TenantId**:
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Een Token ophalen:
   > [!NOTE]
   > Als u Azure Cloud Shell gebruikt, is de `SecureClientSecret` klasse niet beschikbaar. 

   #### <a name="powershell"></a>[PowerShell](#tab/powershell)
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
   
   #### <a name="azure-cloud-shell"></a>[Azure Cloud Shell](#tab/azure-cloud-shell)
   ```Azure Cloud Shell
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, <YOUR_PASSWORD>
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ``` 

   ---

3. Roep de Computer Vision-API aan:
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

U kunt de Service-Principal ook verifiëren met een certificaat. Naast de service-principal wordt ook User Principal ondersteund door de machtigingen te delegeren via een andere AAD-toepassing. In dit geval, in plaats van wacht woorden of certificaten, wordt gebruikers gevraagd om twee ledige verificatie bij het ophalen van token.

## <a name="authorize-access-to-managed-identities"></a>Toegang tot beheerde identiteiten toestaan
 
Cognitive Services ondersteuning voor Azure Active Directory (Azure AD)-verificatie met [beheerde identiteiten voor Azure-resources](../articles/active-directory/managed-identities-azure-resources/overview.md). Beheerde identiteiten voor Azure-resources kunnen toegang tot Cognitive Services resources toestaan via Azure AD-referenties van toepassingen die worden uitgevoerd in azure virtual machines (Vm's), functie-apps, schaal sets voor virtuele machines en andere services. Door beheerde identiteiten voor Azure-resources te gebruiken in combi natie met Azure AD-verificatie kunt u voor komen dat referenties worden opgeslagen in uw toepassingen die in de cloud worden uitgevoerd.  

### <a name="enable-managed-identities-on-a-vm"></a>Beheerde identiteiten op een virtuele machine inschakelen

Voordat u beheerde identiteiten voor Azure-resources kunt gebruiken om toegang te verlenen tot Cognitive Services resources van uw VM, moet u beheerde identiteiten voor Azure-resources inschakelen op de VM. Zie voor meer informatie over het inschakelen van beheerde identiteiten voor Azure-resources:

- [Azure-portal](../articles/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../articles/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-CLI](../articles/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjabloon](../articles/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Client bibliotheken Azure Resource Manager](../articles/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Zie [beheerde identiteiten voor Azure-resources](../articles/active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten.