---
title: Beheerde identiteiten gebruiken in Azure API Management | Microsoft Docs
description: Informatie over het maken van door het systeem toegewezen en door de gebruiker toegewezen identiteiten in API Management met behulp van de sjabloon Azure Portal, PowerShell en Resource Manager.
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 03/09/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 40ee196f53af040e4099fb344de5488109ce001b
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812242"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Beheerde identiteiten gebruiken in Azure API Management

In dit artikel wordt beschreven hoe u een beheerde identiteit maakt voor een Azure API Management-exemplaar en hoe u toegang krijgt tot andere resources. Met een beheerde identiteit die wordt gegenereerd door Azure Active Directory (Azure AD) kan uw API Management-exemplaar eenvoudig en veilig toegang krijgen tot andere met Azure AD beveiligde resources, zoals Azure Key Vault. Azure beheert deze identiteit, zodat u geen geheimen hoeft in terichten of draaien. Zie Wat zijn beheerde identiteiten voor Azure-resources? voor meer informatie [over beheerde identiteiten.](../active-directory/managed-identities-azure-resources/overview.md)

U kunt twee typen identiteiten aan een API Management verlenen:

- Een *door het systeem toegewezen identiteit* is gekoppeld aan uw service en wordt verwijderd als uw service wordt verwijderd. De service kan slechts één door het systeem toegewezen identiteit hebben.
- Een *door de gebruiker toegewezen identiteit* is een zelfstandige Azure-resource die kan worden toegewezen aan uw service. De service kan meerdere door de gebruiker toegewezen identiteiten hebben.

## <a name="create-a-system-assigned-managed-identity"></a>Een door het systeem toegewezen beheerde identiteit maken

### <a name="azure-portal"></a>Azure Portal

Als u een beheerde identiteit wilt instellen in de Azure Portal, maakt u eerst een API Management-instantie en vervolgens wordt de functie ingeschakeld.

1. Maak een API Management in de portal zoals u normaal zou doen. Blader er in de portal naar.
2. Selecteer **Beheerde identiteiten.**
3. Schakel op **het tabblad Systeem** toegewezen de status **in** op **Aan.** Selecteer **Opslaan**.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="Selecties voor het inschakelen van een door het systeem toegewezen beheerde identiteit" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De volgende stappen helpen u bij het maken van een API Management en het toewijzen van een identiteit met behulp van Azure PowerShell. 

1. Installeer indien nodig de Azure PowerShell met behulp van de instructies in de [Azure PowerShell handleiding.](/powershell/azure/install-az-ps) Voer vervolgens `Connect-AzAccount` uit om een verbinding met Azure te maken.

2. Gebruik de volgende code om het exemplaar te maken. Zie PowerShell Azure PowerShell voorbeelden voor API Management API Management meer voorbeelden van het gebruik van [een API Management-instantie.](powershell-samples.md)

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. Werk een bestaand exemplaar bij om de identiteit te maken:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

U kunt een API Management maken met een identiteit door de volgende eigenschap op te geven in de resourcedefinitie:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Deze eigenschap vertelt Azure om de identiteit voor uw API Management maken en beheren.

Zo kan een volledige Azure Resource Manager er als volgt uitzien:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "systemAssigned"
        }
    }]
}
```

Wanneer het exemplaar wordt gemaakt, heeft het de volgende aanvullende eigenschappen:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

De `tenantId` eigenschap identificeert tot welke Azure AD-tenant de identiteit behoort. De eigenschap is een unieke id voor de nieuwe identiteit `principalId` van het exemplaar. In Azure AD heeft de service-principal dezelfde naam die u aan uw API Management-exemplaar hebt gegeven.

> [!NOTE]
> Een API Management kan tegelijkertijd zowel door het systeem toegewezen als door de gebruiker toegewezen identiteiten hebben. In dit geval is `type` de eigenschap `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-system-assigned-identity"></a>Ondersteunde scenario's met door het systeem toegewezen identiteit

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Verkrijg een aangepast TLS/SSL-certificaat voor het API Management-exemplaar van Azure Key Vault
U kunt de door het systeem toegewezen identiteit van een API Management gebruiken om aangepaste TLS/SSL-certificaten op te halen die zijn opgeslagen in Azure Key Vault. U kunt deze certificaten vervolgens toewijzen aan aangepaste domeinen in API Management-exemplaar. Houd rekening met het volgende:

- Het inhoudstype van het geheim moet *application/x-pkcs12 zijn.*
- Gebruik het Key Vault certificaatgeheim, dat het geheim bevat.

> [!Important]
> Als u de objectversie van het certificaat niet op geeft, krijgt API Management automatisch de nieuwere versie van het certificaat binnen vier uur nadat het is bijgewerkt in Key Vault.

In het volgende voorbeeld ziet u Azure Resource Manager sjabloon met de volgende stappen:

1. Maak een API Management met een beheerde identiteit.
2. Werk het toegangsbeleid van een Azure Key Vault bij en sta het API Management toe om er geheimen uit te halen.
3. Werk het API Management bij door een aangepaste domeinnaam in te stellen via een certificaat van het Key Vault exemplaar.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management instance"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management instance."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the Key Vault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>Verifiëren bij de back-end met behulp van API Management identiteit

U kunt de door het systeem toegewezen identiteit gebruiken om te verifiëren bij de [back-end via het beleid authentication-managed-identity.](api-management-authentication-policies.md#ManagedIdentity)

### <a name="connect-to-azure-resources-behind-ip-firewall-using-system-assigned-managed-identity"></a><a name="apim-as-trusted-service"></a>Verbinding maken met Azure-resources achter IP Firewall met behulp van door het systeem toegewezen beheerde identiteit


API Management is een vertrouwde Microsoft-service voor de volgende resources. Hierdoor kan de service verbinding maken met de volgende resources achter een firewall. Nadat de juiste Azure-rol expliciet is toegewezen aan de door het systeem toegewezen beheerde identiteit voor dat resource-exemplaar, komt het toegangsbereik voor het exemplaar overeen met de [Azure-rol](../active-directory/managed-identities-azure-resources/overview.md) die is toegewezen aan de beheerde identiteit.


|Azure-service | Koppeling|
|---|---|
|Azure Storage | [Trusted-access-to-azure-storage](../storage/common/storage-network-security.md?tabs=azure-portal#trusted-access-based-on-system-assigned-managed-identity)|
|Azure Service Bus | [Trusted-access-to-azure-service-bus](../service-bus-messaging/service-bus-ip-filtering.md#trusted-microsoft-services)|
|Azure Event Hub | [Trused-access-to-azure-event-hub](../event-hubs/event-hubs-ip-filtering.md#trusted-microsoft-services)|


## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken

> [!NOTE]
> U kunt een API Management aan maximaal 10 door de gebruiker toegewezen beheerde identiteiten koppelen.

### <a name="azure-portal"></a>Azure Portal

Als u een beheerde identiteit in de portal wilt instellen, maakt u eerst een API Management en vervolgens wordt de functie ingeschakeld.

1. Maak een API Management in de portal zoals u dat normaal zou doen. Blader er in de portal naar.
2. Selecteer **Beheerde identiteiten.**
3. Selecteer op **het tabblad Door** de gebruiker toegewezen de optie **Toevoegen.**
4. Zoek de identiteit die u eerder hebt gemaakt en selecteer deze. Selecteer **Toevoegen**.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="Selecties voor het inschakelen van een door de gebruiker toegewezen beheerde identiteit" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De volgende stappen helpen u bij het maken van een API Management en het toewijzen van een identiteit met behulp van Azure PowerShell. 

1. Installeer indien nodig de Azure PowerShell aan de hand van de instructies in de [Azure PowerShell handleiding.](/powershell/azure/install-az-ps) Voer vervolgens `Connect-AzAccount` uit om een verbinding met Azure te maken.

2. Gebruik de volgende code om het exemplaar te maken. Zie PowerShell Azure PowerShell voorbeelden voor API Management API Management meer voorbeelden van het gebruik van [een API Management-instantie.](powershell-samples.md)

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. Een bestaande service bijwerken om een identiteit aan de service toe te wijzen:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

U kunt een API Management maken met een identiteit door de volgende eigenschap op te geven in de resourcedefinitie:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

Door het door de gebruiker toegewezen type toe te voegen, weet Azure dat de door de gebruiker toegewezen identiteit moet worden gebruikt die is opgegeven voor uw exemplaar.

Zo kan een volledige Azure Resource Manager er als volgt uitzien:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-12-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "UserAssigned",
             "userAssignedIdentities": {
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
             }
        },
         "dependsOn": [       
          "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
        ]
    }]
}
```

Wanneer de service is gemaakt, heeft deze de volgende aanvullende eigenschappen:

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

De `principalId` eigenschap is een unieke id voor de identiteit die wordt gebruikt voor Azure AD-beheer. De eigenschap is een unieke id voor de nieuwe identiteit van de toepassing die wordt gebruikt om op te geven welke identiteit moet worden gebruikt `clientId` tijdens runtime-aanroepen.

> [!NOTE]
> Een API Management kan tegelijkertijd zowel door het systeem toegewezen als door de gebruiker toegewezen identiteiten hebben. In dit geval is `type` de eigenschap `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-user-assigned-managed-identity"></a>Ondersteunde scenario's met door de gebruiker toegewezen beheerde identiteit

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault-ua"></a>Verkrijg een aangepast TLS/SSL-certificaat voor het API Management-exemplaar van Azure Key Vault
U kunt elke door de gebruiker toegewezen identiteit gebruiken om een vertrouwensrelatie tot stand te API Management een exemplaar en KeyVault. Deze vertrouwensrelatie kan vervolgens worden gebruikt om aangepaste TLS/SSL-certificaten op te halen die zijn opgeslagen in Azure Key Vault. U kunt deze certificaten vervolgens toewijzen aan aangepaste domeinen in de API Management-instantie. 

Houd rekening met het volgende:

- Het inhoudstype van het geheim moet *application/x-pkcs12 zijn.*
- Gebruik het Key Vault certificaatgeheim dat het geheim bevat.

> [!Important]
> Als u de objectversie van het certificaat niet op geeft, krijgt API Management automatisch de nieuwere versie van het certificaat binnen vier uur nadat het is bijgewerkt in Key Vault.

Zie voor de volledige sjabloon API Management SSL op basis [van KeyVault](https://github.com/Azure/azure-quickstart-templates/blob/master/101-api-management-key-vault-create/azuredeploy.json)met behulp van door de gebruiker toegewezen identiteit.

In deze sjabloon implementeert u:

* Azure API Management
* Door Azure beheerde door de gebruiker toegewezen identiteit
* Azure KeyVault voor het opslaan van het SSL/TLS-certificaat

Klik op de volgende knop om de implementatie automatisch uit te voeren:

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-api-management-key-vault-create%2Fazuredeploy.json)

### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>Verifiëren bij de back-end met behulp van een door de gebruiker toegewezen identiteit

U kunt de door de gebruiker toegewezen identiteit gebruiken om te verifiëren bij de back-end via het [beleid authentication-managed-identity.](api-management-authentication-policies.md#ManagedIdentity)

## <a name="remove-an-identity"></a><a name="remove"></a>Een identiteit verwijderen

U kunt een door het systeem toegewezen identiteit verwijderen door de functie via de portal of de Azure Resource Manager-sjabloon op dezelfde manier uit te Azure Resource Manager die is gemaakt. Door de gebruiker toegewezen identiteiten kunnen afzonderlijk worden verwijderd. Als u alle identiteiten wilt verwijderen, stelt u het identiteitstype in op `"None"` .

Als u een door het systeem toegewezen identiteit op deze manier verwijdert, wordt deze ook verwijderd uit Azure AD. Door het systeem toegewezen identiteiten worden ook automatisch verwijderd uit Azure AD wanneer API Management-exemplaar wordt verwijderd.

Als u alle identiteiten wilt verwijderen met behulp van Azure Resource Manager sjabloon, moet u deze sectie bijwerken:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Als een API Management is geconfigureerd met een aangepast SSL-certificaat van Key Vault en u een beheerde identiteit probeert uit te schakelen, mislukt de aanvraag.
>
> U kunt uzelf deblokkeren door over te schakelen van een Azure Key Vault certificaat naar een inline gecodeerd certificaat en vervolgens de beheerde identiteit uit te schakelen. Zie [Een aangepaste domeinnaam configureren](configure-custom-domain.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over beheerde identiteiten voor Azure-resources:

* [Wat zijn beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure Resource Manager-sjablonen](https://github.com/Azure/azure-quickstart-templates)
* [Verifiëren met een beheerde identiteit in een beleid](./api-management-authentication-policies.md#ManagedIdentity)
