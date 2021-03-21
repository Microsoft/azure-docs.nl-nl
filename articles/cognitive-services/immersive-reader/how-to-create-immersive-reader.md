---
title: Een insluitende lezerservaring maken
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u een nieuwe insluitende lezer-resource maakt met een aangepast subdomein en vervolgens Azure AD configureert in uw Azure-Tenant.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: d7a8a598ab2d0c873cf07510cf40a08c22c0f1d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608594"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>Een resource voor insluitende lezer maken en Azure Active Directory authenticatie configureren

In dit artikel bieden we een script waarmee een insluitende lezer-resource wordt gemaakt en Azure Active Directory-verificatie (Azure AD) wordt geconfigureerd. Telkens wanneer een insluitende lezer-resource wordt gemaakt, met dit script of in de portal, moet deze ook worden geconfigureerd met Azure AD-machtigingen. Dit script helpt u bij dat.

Het script is ontworpen voor het maken en configureren van alle benodigde insluitende lezer en Azure AD-resources voor u in één stap. U kunt echter ook de Azure AD-verificatie voor een bestaande insluitende lezer-resource configureren, als dat bijvoorbeeld het geval is, dan moet u er al een hebben gemaakt in de Azure Portal.

Voor sommige klanten kan het nodig zijn om meerdere insluitende lezer-resources te maken voor ontwikkelings-en productie doeleinden, of mogelijk voor meerdere verschillende regio's waarop uw service is geïmplementeerd. In dergelijke gevallen kunt u het script meerdere keren gebruiken om verschillende insluitende lezers bronnen te maken en deze te configureren met de Azure AD-machtigingen.

Het script is zo ontworpen dat het flexibel is. Er wordt eerst gezocht naar bestaande insluitende lezer en Azure AD-resources in uw abonnement en deze worden alleen indien nodig gemaakt als ze nog niet bestaan. Als dit de eerste keer is dat u een insluitende lezer-resource maakt, heeft het script alles wat u nodig hebt. Als u de functie alleen wilt gebruiken voor het configureren van Azure AD voor een bestaande insluitende lezer-resource die in de portal is gemaakt, doet u dat ook. Het kan ook worden gebruikt voor het maken en configureren van meerdere insluitende Reader-resources.

## <a name="set-up-powershell-environment"></a>Power shell-omgeving instellen

1. Begin met het openen van de [Azure Cloud shell](../../cloud-shell/overview.md). Zorg ervoor dat Cloud Shell is ingesteld op Power shell in de vervolg keuzelijst linksboven of door te typen `pwsh` .

1. Kopieer het volgende code fragment en plak het in de shell.

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true, Position=0)] [String] $SubscriptionName,
        [Parameter(Mandatory=$true)] [String] $ResourceName,
        [Parameter(Mandatory=$true)] [String] $ResourceSubdomain,
        [Parameter(Mandatory=$true)] [String] $ResourceSKU,
        [Parameter(Mandatory=$true)] [String] $ResourceLocation,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupName,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $AADAppDisplayName="ImmersiveReaderAAD",
        [Parameter(Mandatory=$true)] [String] $AADAppIdentifierUri,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecretExpiration
    )
    {
        $unused = ''
        if (-not [System.Uri]::TryCreate($AADAppIdentifierUri, [System.UriKind]::Absolute, [ref] $unused)) {
            throw "Error: AADAppIdentifierUri must be a valid URI"
        }

        Write-Host "Setting the active subscription to '$SubscriptionName'"
        $subscriptionExists = Get-AzSubscription -SubscriptionName $SubscriptionName
        if (-not $subscriptionExists) {
            throw "Error: Subscription does not exist"
        }
        az account set --subscription $SubscriptionName

        $resourceGroupExists = az group exists --name $ResourceGroupName
        if ($resourceGroupExists -eq "false") {
            Write-Host "Resource group does not exist. Creating resource group"
            $groupResult = az group create --name $ResourceGroupName --location $ResourceGroupLocation
            if (-not $groupResult) {
                throw "Error: Failed to create resource group"
            }
            Write-Host "Resource group created successfully"
        }

        # Create an Immersive Reader resource if it doesn't already exist
        $resourceId = az cognitiveservices account show --resource-group $ResourceGroupName --name $ResourceName --query "id" -o tsv
        if (-not $resourceId) {
            Write-Host "Creating the new Immersive Reader resource '$ResourceName' (SKU '$ResourceSKU') in '$ResourceLocation' with subdomain '$ResourceSubdomain'"
            $resourceId = az cognitiveservices account create `
                            --name $ResourceName `
                            --resource-group $ResourceGroupName `
                            --kind ImmersiveReader `
                            --sku $ResourceSKU `
                            --location $ResourceLocation `
                            --custom-domain $ResourceSubdomain `
                            --query "id" `
                            -o tsv

            if (-not $resourceId) {
                throw "Error: Failed to create Immersive Reader resource"
            }
            Write-Host "Immersive Reader resource created successfully"
        }

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id $AADAppIdentifierUri --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $clientId = az ad app create --password $AADAppClientSecret --end-date "$AADAppClientSecretExpiration" --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully."
            Write-Host "NOTE: To manage your Active Directory app client secrets after this Immersive Reader Resource has been created please visit https://portal.azure.com and go to Home -> Azure Active Directory -> App Registrations -> $AADAppDisplayName -> Certificates and Secrets blade -> Client Secrets section" -ForegroundColor Yellow
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            az ad sp create --id $clientId | Out-Null
            $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv

            if (-not $principalId) {
                throw "Error: Failed to create new service principal"
            }
            Write-Host "New service principal created successfully"
        }

        # Sleep for 5 seconds to allow the new service principal to propagate
        Write-Host "Sleeping for 5 seconds"
        Start-Sleep -Seconds 5

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        $accessResult = az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User"
        if (-not $accessResult) {
            throw "Error: Failed to grant service principal access"
        }
        Write-Host "Service principal access granted successfully"

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $AADAppClientSecret
        $result.Subdomain = $ResourceSubdomain

        Write-Host "Success! " -ForegroundColor Green -NoNewline
        Write-Host "Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. Voer de functie uit `Create-ImmersiveReaderResource` en geef zo nodig de tijdelijke aanduidingen <PARAMETER_VALUES> op met uw eigen waarden.

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource -SubscriptionName '<SUBSCRIPTION_NAME>' -ResourceName '<RESOURCE_NAME>' -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' -ResourceSKU '<RESOURCE_SKU>' -ResourceLocation '<RESOURCE_LOCATION>' -ResourceGroupName '<RESOURCE_GROUP_NAME>' -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>' -AADAppClientSecretExpiration '<AAD_APP_CLIENT_SECRET_EXPIRATION>'
    ```

    De volledige opdracht ziet er ongeveer als volgt uit. Hier hebben we elke para meter op een eigen regel voor duidelijkheid geplaatst, zodat u de hele opdracht kunt zien. Deze opdracht mag niet worden gekopieerd of gebruikt. Kopieer en gebruik de bovenstaande opdracht met uw eigen waarden. Dit voor beeld bevat dummy waarden voor de bovenstaande <PARAMETER_VALUES>. Uw eigen namen voor deze waarden komen niet overeen met uw eigen naam.

    ```
    Create-ImmersiveReaderResource
        -SubscriptionName 'MyOrganizationSubscriptionName'
        -ResourceName 'MyOrganizationImmersiveReader'
        -ResourceSubdomain 'MyOrganizationImmersiveReader'
        -ResourceSKU 'S0'
        -ResourceLocation 'westus2'
        -ResourceGroupName 'MyResourceGroupName'
        -ResourceGroupLocation 'westus2'
        -AADAppDisplayName 'MyOrganizationImmersiveReaderAADApp'
        -AADAppIdentifierUri 'https://MyOrganizationImmersiveReaderAADApp'
        -AADAppClientSecret 'SomeStrongPassword'
        -AADAppClientSecretExpiration '2021-12-31'
    ```

    | Parameter | Opmerkingen |
    | --- | --- |
    | SubscriptionName |De naam van het Azure-abonnement dat moet worden gebruikt voor uw insluitende lezer-resource. U moet een abonnement hebben om een resource te kunnen maken. |
    | ResourceName |  Moet alfanumeriek zijn en kan '-' bevatten, zolang het niet het eerste of laatste teken is. De lengte mag niet langer zijn dan 63 tekens.|
    | ResourceSubdomain |Er is een aangepast subdomein nodig voor uw insluitende lezer-resource. Het subdomein wordt gebruikt door de SDK bij het aanroepen van de insluitende lezer-service om de lezer te starten. Het subdomein moet globaal uniek zijn. Het subdomein moet alfanumeriek zijn en kan '-' bevatten, zolang het niet het eerste of laatste teken is. De lengte mag niet langer zijn dan 63 tekens. Deze para meter is optioneel als de resource al bestaat. |
    | ResourceSKU |Opties: `S0` (Standard-laag) of `S1` (onderwijs/non-profit organisaties). Ga naar onze [pagina met Cognitive Services prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) voor meer informatie over elke beschik bare SKU. Deze para meter is optioneel als de resource al bestaat. |
    | ResourceLocation |Opties: `eastus` , `eastus2` , `southcentralus` , `westus` , `westus2` , `australiaeast` , `southeastasia` , `centralindia` , `japaneast` , `northeurope` , `uksouth` , `westeurope` . Deze para meter is optioneel als de resource al bestaat. |
    | ResourceGroupName |Resources worden gemaakt in resource groepen binnen abonnementen. Geef de naam van een bestaande resource groep op. Als de resource groep nog niet bestaat, wordt er een nieuwe met deze naam gemaakt. |
    | ResourceGroupLocation |Als uw resource groep niet bestaat, moet u een locatie opgeven voor het maken van de groep. Voer uit om een lijst met locaties te vinden `az account list-locations` . Gebruik de eigenschap *name* (zonder spaties) van het geretourneerde resultaat. Deze para meter is optioneel als uw resource groep al bestaat. |
    | AADAppDisplayName |De weergave naam van de Azure Active Directory-toepassing. Als een bestaande Azure AD-toepassing niet wordt gevonden, wordt er een nieuw item met deze naam gemaakt. Deze para meter is optioneel als de Azure AD-toepassing al bestaat. |
    | AADAppIdentifierUri |De URI voor de Azure AD-app. Als een bestaande Azure AD-app niet wordt gevonden, wordt er een nieuw item met deze URI gemaakt. Bijvoorbeeld `https://immersivereaderaad-mycompany`. |
    | AADAppClientSecret |Een wacht woord dat u maakt, wordt later gebruikt voor verificatie bij het ophalen van een token om de insluitende lezer te starten. Het wacht woord moet ten minste 16 tekens lang zijn, ten minste één speciaal teken bevatten en ten minste één numeriek teken bevatten. Als u Azure AD-client geheimen wilt beheren nadat u deze resource hebt gemaakt, gaat u naar en gaat u naar de https://portal.azure.com Start-> Azure Active Directory-> app-registraties-> `[AADAppDisplayName]` -> certificaten en geheimen Blade-> client geheimen sectie (zoals weer gegeven in de scherm afbeelding uw Azure AD-toepassings geheimen beheren). |
    | AADAppClientSecretExpiration |De datum of tijd waarna uw `[AADAppClientSecret]` verloopt (bijvoorbeeld ' 2020-12-31T11:59:59 + 00:00 ' of ' 2020-12-31 '). |

    Uw Azure AD-toepassings geheimen beheren

    ![Blade certificaten en geheimen van Azure Portal](./media/client-secrets-blade.png)

1. Kopieer de JSON-uitvoer naar een tekst bestand voor later gebruik. De uitvoer moet er als volgt uitzien.

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>Volgende stappen

* Bekijk de [Node.js Snelstartgids](./quickstarts/client-libraries.md?pivots=programming-language-nodejs) om te zien wat u nog meer kunt doen met de insluitende lezer-SDK met behulp van Node.js
* Bekijk de zelfstudie over [Android](./tutorial-android.md) om te zien wat u nog meer kunt doen met de Insluitende lezer-SDK met Java of Kotlin voor Android
* Bekijk de [iOS-zelfstudie](./tutorial-ios.md) om te zien wat u nog meer kunt doen met de Insluitende lezer-SDK met behulp van Swift voor iOS
* Bekijk de [python-zelf studie](./tutorial-python.md) om te zien wat u nog meer kunt doen met de insluitende Reader SDK met behulp van python
* De [SDK voor Insluitende lezer](https://github.com/microsoft/immersive-reader-sdk) en de [naslaginformatie voor de SDK voor Insluitende lezer](./reference.md) verkennen