---
title: Beheerde identiteiten gebruiken in azure API Management | Microsoft Docs
description: Meer informatie over het maken van door het systeem toegewezen en door de gebruiker toegewezen identiteiten in API Management met behulp van de Azure Portal, Power shell en een resource manager-sjabloon.
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 11/14/2020
ms.author: apimpm
ms.openlocfilehash: db1a8238cf9ddae57d73438d43daa54294ce6860
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686222"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Beheerde identiteiten gebruiken in azure API Management

In dit artikel leest u hoe u een beheerde identiteit kunt maken voor een Azure API Management-exemplaar en hoe u toegang krijgt tot andere bronnen. Met een beheerde identiteit die is gegenereerd door Azure Active Directory (Azure AD) kan uw API Management-exemplaar eenvoudig en veilig toegang krijgen tot andere met Azure AD beveiligde resources, zoals Azure Key Vault. Azure beheert deze identiteit, zodat u geen geheimen hoeft in te richten of te draaien. Zie [Wat zijn beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten.

U kunt twee typen identiteiten toekennen aan een API Management-exemplaar:

- Een door het *systeem toegewezen identiteit* is gekoppeld aan uw service en wordt verwijderd als uw service wordt verwijderd. De service kan slechts één door het systeem toegewezen identiteit hebben.
- Een door de *gebruiker toegewezen identiteit* is een zelfstandige Azure-resource die kan worden toegewezen aan uw service. De service kan meerdere door de gebruiker toegewezen identiteiten hebben.

## <a name="create-a-system-assigned-managed-identity"></a>Een door het systeem toegewezen beheerde identiteit maken

### <a name="azure-portal"></a>Azure Portal

Als u een beheerde identiteit wilt instellen in de Azure Portal, maakt u eerst een API Management-exemplaar en schakelt u vervolgens de functie in.

1. Maak een API Management-exemplaar in de portal zoals u dat gewend bent. Blader ernaar in de portal.
2. Selecteer **beheerde identiteiten**.
3. Schakel op het tabblad **systeem toegewezen** de optie **status** in **op aan**. Selecteer **Opslaan**.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="Selecties voor het inschakelen van een door het systeem toegewezen beheerde identiteit" border="true":::


### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De volgende stappen begeleiden u bij het maken van een API Management-exemplaar en het toewijzen van een identiteit met behulp van Azure PowerShell. 

1. Als dat nodig is, installeert u Azure PowerShell met behulp van de instructies in de [Azure PowerShell Guide](/powershell/azure/install-az-ps). Voer vervolgens uit `Connect-AzAccount` om een verbinding te maken met Azure.

2. Gebruik de volgende code om het exemplaar te maken. Zie voor meer voor beelden van het gebruik van Azure PowerShell met een API Management exemplaar [API Management Power shell](powershell-samples.md)-voor beelden.

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. Een bestaand exemplaar bijwerken om de identiteit te maken:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

U kunt een API Management-exemplaar maken met een identiteit door de volgende eigenschap op te nemen in de resource definitie:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Met deze eigenschap wordt aan Azure aangegeven dat de identiteit voor uw API Management-exemplaar moet worden gemaakt en beheerd.

Een complete Azure Resource Manager sjabloon kan er bijvoorbeeld als volgt uitzien:

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

De `tenantId` eigenschap geeft aan met welke Azure AD-Tenant de identiteit behoort. De `principalId` eigenschap is een unieke id voor de nieuwe identiteit van het exemplaar. De Service-Principal in azure AD heeft dezelfde naam die u hebt opgegeven voor uw API Management-exemplaar.


> [!NOTE]
> Een API Management-exemplaar kan tegelijkertijd zowel aan het systeem toegewezen als door de gebruiker toegewezen identiteiten hebben. In dit geval is de `type` eigenschap `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-system-assigned-identity"></a>Ondersteunde scenario's waarbij systeem identiteiten worden gebruikt

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Een aangepast TLS/SSL-certificaat voor het API Management-exemplaar verkrijgen van Azure Key Vault
U kunt de door het systeem toegewezen identiteit van een API Management exemplaar gebruiken om aangepaste TLS/SSL-certificaten op te halen die zijn opgeslagen in Azure Key Vault. U kunt deze certificaten vervolgens toewijzen aan aangepaste domeinen in het API Management-exemplaar. Houd rekening met het volgende:

- Het inhouds type van het geheim moet *Application/x-pkcs12/pfx-profiel* zijn.
- Gebruik het geheime eind punt van het Key Vault certificaat dat het geheim bevat.

> [!Important]
> Als u de object versie van het certificaat niet opgeeft, ontvangt API Management automatisch de nieuwere versie van het certificaat binnen vier uur nadat het is bijgewerkt in Key Vault.

In het volgende voor beeld ziet u een Azure Resource Manager sjabloon die de volgende stappen bevat:

1. Maak een API Management-exemplaar met een beheerde identiteit.
2. Het toegangs beleid van een Azure Key Vault-exemplaar bijwerken en toestaan dat het API Management exemplaar geheimen van de instantie kan verkrijgen.
3. Werk het API Management-exemplaar bij door een aangepaste domein naam in te stellen via een certificaat van de Key Vault instantie.

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

### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>Verifiëren voor de back-end met behulp van een API Management identiteit

U kunt de door het systeem toegewezen identiteit gebruiken om u te verifiëren bij de back-end via het beleid voor [beheerde identiteiten](api-management-authentication-policies.md#ManagedIdentity) van de verificatie.


## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken

> [!NOTE]
> U kunt een API Management-exemplaar koppelen aan Maxi maal 10 door de gebruiker toegewezen beheerde identiteiten.

### <a name="azure-portal"></a>Azure Portal

Als u een beheerde identiteit in de portal wilt instellen, maakt u eerst een API Management-exemplaar en schakelt u vervolgens de functie in.

1. Maak een API Management-exemplaar in de portal zoals u dat gewend bent. Blader ernaar in de portal.
2. Selecteer **beheerde identiteiten**.
3. Selecteer **toevoegen** op het tabblad door de **gebruiker toegewezen** .
4. Zoek naar de identiteit die u eerder hebt gemaakt en selecteer deze. Selecteer **Toevoegen**.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="Selecties voor het inschakelen van een door de gebruiker toegewezen beheerde identiteit" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De volgende stappen begeleiden u bij het maken van een API Management-exemplaar en het toewijzen van een identiteit met behulp van Azure PowerShell. 

1. Als dat nodig is, installeert u de Azure PowerShell met behulp van de instructies in de [Azure PowerShell Guide](/powershell/azure/install-az-ps). Voer vervolgens uit `Connect-AzAccount` om een verbinding te maken met Azure.

2. Gebruik de volgende code om het exemplaar te maken. Zie voor meer voor beelden van het gebruik van Azure PowerShell met een API Management exemplaar [API Management Power shell](powershell-samples.md)-voor beelden.

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

U kunt een API Management-exemplaar maken met een identiteit door de volgende eigenschap op te nemen in de resource definitie:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

Als u het door de gebruiker toegewezen type toevoegt, krijgt Azure de door de gebruiker toegewezen identiteit te gebruiken die voor uw exemplaar is opgegeven.

Een complete Azure Resource Manager sjabloon kan er bijvoorbeeld als volgt uitzien:

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

Wanneer de service wordt gemaakt, heeft deze de volgende aanvullende eigenschappen:

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

De `principalId` eigenschap is een unieke id voor de identiteit die wordt gebruikt voor Azure AD-beheer. De `clientId` eigenschap is een unieke id voor de nieuwe identiteit van de toepassing die wordt gebruikt om op te geven welke identiteit tijdens runtime-aanroepen moet worden gebruikt.

> [!NOTE]
> Een API Management-exemplaar kan tegelijkertijd zowel aan het systeem toegewezen als door de gebruiker toegewezen identiteiten hebben. In dit geval is de `type` eigenschap `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-user-assigned-managed-identity"></a>Ondersteunde scenario's met door de gebruiker toegewezen beheerde identiteit

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault-ua"></a>Een aangepast TLS/SSL-certificaat voor het API Management-exemplaar verkrijgen van Azure Key Vault
U kunt elke door de gebruiker toegewezen identiteit gebruiken om een vertrouwens relatie tussen een API Management exemplaar en de sleutel kluis tot stand te brengen. Deze vertrouwens relatie kan vervolgens worden gebruikt om aangepaste TLS/SSL-certificaten op te halen die zijn opgeslagen in Azure Key Vault. U kunt deze certificaten vervolgens toewijzen aan aangepaste domeinen in het API Management-exemplaar. 

Houd rekening met het volgende:

- Het inhouds type van het geheim moet *Application/x-pkcs12/pfx-profiel* zijn.
- Gebruik het geheime eind punt van het Key Vault certificaat dat het geheim bevat.

> [!Important]
> Als u de object versie van het certificaat niet opgeeft, ontvangt API Management automatisch de nieuwere versie van het certificaat binnen vier uur nadat het is bijgewerkt in Key Vault.

Zie [API Management met op sleutel kluis gebaseerd SSL met door de gebruiker toegewezen identiteit](https://github.com/Azure/azure-quickstart-templates/blob/master/101-api-management-key-vault-create/azuredeploy.json)voor de volledige sjabloon.

In deze sjabloon voert u de volgende implementatie uit:

* Azure API Management
* Door Azure beheerde gebruiker toegewezen identiteit
* Azure-sleutel kluis voor het opslaan van het SSL/TLS-certificaat

Klik op de volgende knop om de implementatie automatisch uit te voeren:

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-api-management-key-vault-create%2Fazuredeploy.json)

### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>Verifiëren voor de back-end met behulp van een door de gebruiker toegewezen identiteit

U kunt de door de gebruiker toegewezen identiteit gebruiken om u te verifiëren bij de back-end via het beleid voor [beheerde identiteiten](api-management-authentication-policies.md#ManagedIdentity) van de verificatie.


## <a name="remove-an-identity"></a><a name="remove"></a>Een identiteit verwijderen

U kunt een door het systeem toegewezen identiteit verwijderen door de functie uit te scha kelen via de portal of de Azure Resource Manager sjabloon op dezelfde manier als waarop deze is gemaakt. Door de gebruiker toegewezen identiteiten kunnen afzonderlijk worden verwijderd. Als u alle identiteiten wilt verwijderen, stelt u het identiteits type in op `"None"` .

Als u een door het systeem toegewezen identiteit op deze manier verwijdert, wordt deze ook uit Azure AD verwijderd. Door het systeem toegewezen identiteiten worden ook automatisch verwijderd uit Azure AD wanneer het API Management exemplaar wordt verwijderd.

Als u alle identiteiten wilt verwijderen met behulp van de sjabloon Azure Resource Manager, werkt u deze sectie bij:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Als een API Management-exemplaar is geconfigureerd met een aangepast SSL-certificaat van Key Vault en u probeert een beheerde identiteit uit te scha kelen, mislukt de aanvraag.
>
> U kunt de blok kering zelf opheffen door van een Azure Key Vault-certificaat naar een inline-gecodeerd certificaat te scha kelen en vervolgens de beheerde identiteit uit te scha kelen. Zie [Een aangepaste domeinnaam configureren](configure-custom-domain.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over beheerde identiteiten voor Azure-resources:

* [Wat zijn beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure Resource Manager-sjablonen](https://github.com/Azure/azure-quickstart-templates)
* [Verifiëren met een beheerde identiteit in een beleid](./api-management-authentication-policies.md#ManagedIdentity)
