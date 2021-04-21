---
title: Key Vault-referenties gebruiken
description: Meer informatie over het instellen van Azure App Service en Azure Functions het gebruik van Azure Key Vault referenties. Maak Key Vault geheimen beschikbaar voor uw toepassingscode.
author: mattchenderson
ms.topic: article
ms.date: 02/05/2021
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 1e6f46b205790d81a3e76d2aafbcf7e13dbb5afd
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815212"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Gebruik Key Vault voor App Service en Azure Functions

In dit onderwerp ziet u hoe u werkt met geheimen van Azure Key Vault in uw App Service of Azure Functions-toepassing zonder dat er codewijzigingen nodig zijn. [Azure Key Vault](../key-vault/general/overview.md) is een service die gecentraliseerd geheimenbeheer biedt, met volledige controle over toegangsbeleid en controlegeschiedenis.

## <a name="granting-your-app-access-to-key-vault"></a>Uw app toegang verlenen tot Key Vault

Als u geheimen wilt lezen uit Key Vault, moet u een kluis hebben gemaakt en uw app toestemming geven om er toegang toe te krijgen.

1. Maak een sleutelkluis door de Key Vault [te volgen.](../key-vault/secrets/quick-create-cli.md)

1. Maak een [door het systeem toegewezen beheerde identiteit](overview-managed-identity.md) voor uw toepassing.

   > [!NOTE] 
   > Key Vault bieden momenteel alleen ondersteuning voor door het systeem toegewezen beheerde identiteiten. Door de gebruiker toegewezen identiteiten kunnen niet worden gebruikt.

1. Maak een [toegangsbeleid in Key Vault](../key-vault/general/security-features.md#privileged-access) voor de toepassings-id die u eerder hebt gemaakt. Schakel de machtiging Geheim 'Get' in voor dit beleid. Configureer de 'geautoriseerde toepassing' of `applicationId` instellingen niet, omdat dit niet compatibel is met een beheerde identiteit.

### <a name="access-network-restricted-vaults"></a>Toegang tot kluizen met beperkte netwerktoegang

> [!NOTE]
> Linux-toepassingen kunnen momenteel geen geheimen uit een sleutelkluis met beperkte netwerksleutel oplossen, tenzij de app wordt gehost binnen [een App Service Environment](./environment/intro.md).

Als uw kluis is geconfigureerd met [netwerkbeperkingen,](../key-vault/general/overview-vnet-service-endpoints.md)moet u er ook voor zorgen dat de toepassing netwerktoegang heeft.

1. Zorg ervoor dat voor de toepassing uitgaande netwerkmogelijkheden zijn geconfigureerd, zoals beschreven in App Service [netwerkfuncties](./networking-features.md) en [Azure Functions netwerkopties.](../azure-functions/functions-networking-options.md)

2. Zorg ervoor dat de configuratieaccounts van de kluis voor het netwerk of subnet zijn waarmee uw app toegang krijgt.

> [!IMPORTANT]
> Toegang tot een kluis via integratie van virtuele netwerken is momenteel niet compatibel met automatische updates voor [geheimen zonder een opgegeven versie](#rotation).

## <a name="reference-syntax"></a>Verwijzingssyntaxis

Een Key Vault heeft de vorm `@Microsoft.KeyVault({referenceString})` , waarbij wordt vervangen door een van de volgende `{referenceString}` opties:

> [!div class="mx-tdBreakAll"]
> | Referentiereeks                                                            | Description                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri =_secretUri_                                                       | De **SecretUri** moet de volledige gegevensvlak-URI zijn van een geheim in Key Vault, eventueel inclusief een versie, bijvoorbeeld `https://myvault.vault.azure.net/secrets/mysecret/` of `https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931`  |
> | VaultName=_vaultName_; SecretName =_secretName_; SecretVersion=_secretVersion_ | De **VaultName** is vereist en moet de naam van uw Key Vault resource. De **SecretName** is vereist en moet de naam van het doelgeheim zijn. De **SecretVersion** is optioneel, maar als aanwezig de versie aangeeft van het geheim dat moet worden gebruikt. |

Een volledige verwijzing ziet er bijvoorbeeld als volgt uit:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/)
```

U kunt ook het volgende doen:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret)
```

## <a name="rotation"></a>Rotatie

> [!IMPORTANT]
> [Toegang tot een kluis via integratie van virtuele netwerken](#access-network-restricted-vaults) is momenteel niet compatibel met automatische updates voor geheimen zonder een opgegeven versie.

Als er geen versie is opgegeven in de verwijzing, gebruikt de app de nieuwste versie die bestaat in Key Vault. Wanneer er nieuwere versies beschikbaar komen, zoals bij een rotatiegebeurtenis, wordt de app automatisch bijgewerkt en wordt binnen één dag de nieuwste versie gebruikt. Eventuele configuratiewijzigingen die in de app worden aangebracht, zorgen voor een onmiddellijke update van de nieuwste versies van alle geheimen waarnaar wordt verwezen.

## <a name="source-application-settings-from-key-vault"></a>Brontoepassingsinstellingen van Key Vault

Key Vault kunnen worden gebruikt als waarden [](configure-common.md#configure-app-settings)voor toepassingsinstellingen, zodat u geheimen in Key Vault plaats van de site-configuratie kunt bewaren. Toepassingsinstellingen worden in rust veilig versleuteld, maar als u mogelijkheden voor geheimbeheer nodig hebt, moeten ze worden Key Vault.

Als u een Key Vault voor een toepassingsinstelling wilt gebruiken, stelt u de verwijzing in als de waarde van de instelling. Uw app kan op de gebruikelijke manier verwijzen naar het geheim via de sleutel. Er zijn geen codewijzigingen vereist.

> [!TIP]
> De meeste toepassingsinstellingen Key Vault referenties moeten worden gemarkeerd als site-instellingen, omdat u afzonderlijke kluizen voor elke omgeving moet hebben.

### <a name="azure-resource-manager-deployment"></a>Implementatie van Azure Resource Manager

Bij het automatiseren van resource-implementaties via Azure Resource Manager sjablonen, moet u mogelijk uw afhankelijkheden in een bepaalde volgorde sequentieën om deze functie te laten werken. Let op: u moet uw toepassingsinstellingen definiëren als hun eigen resource, in plaats van een eigenschap in de `siteConfig` sitedefinitie te gebruiken. Dit komt doordat de site eerst moet worden gedefinieerd, zodat de door het systeem toegewezen identiteit hiermee wordt gemaakt en kan worden gebruikt in het toegangsbeleid.

Een voorbeeld van een pseudosjabloon voor een functie-app kan er als volgt uitzien:

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> In dit voorbeeld is de implementatie van broncodebeheer afhankelijk van de toepassingsinstellingen. Dit is normaal gesproken onveilig gedrag, omdat de update van de app-instelling asynchroon werkt. Omdat we de toepassingsinstelling hebben `WEBSITE_ENABLE_SYNC_UPDATE_SITE` opgenomen, is de update echter synchroon. Dit betekent dat de implementatie van broncodebeheer pas begint wanneer de toepassingsinstellingen volledig zijn bijgewerkt.

## <a name="troubleshooting-key-vault-references"></a>Problemen met Key Vault oplossen

Als een verwijzing niet correct wordt opgelost, wordt in plaats daarvan de referentiewaarde gebruikt. Dit betekent dat voor toepassingsinstellingen een omgevingsvariabele wordt gemaakt waarvan de waarde de `@Microsoft.KeyVault(...)` syntaxis heeft. Dit kan ertoe leiden dat de toepassing fouten veroorzaakt, omdat er een geheim van een bepaalde structuur werd verwacht.

Dit komt meestal door een onjuiste configuratie van het Key Vault [toegangsbeleid](#granting-your-app-access-to-key-vault). Dit kan echter ook het gevolg zijn van een geheim dat niet meer bestaat of een syntaxisfout in de verwijzing zelf.

Als de syntaxis juist is, kunt u andere oorzaken voor fouten bekijken door de huidige oplossingsstatus in de portal te controleren. Navigeer naar Toepassingsinstellingen en selecteer Bewerken voor de referentie in kwestie. Onder de instellingsconfiguratie ziet u statusinformatie, inclusief eventuele fouten. Het ontbreken van deze impliceert dat de verwijzingssyntaxis ongeldig is.

U kunt ook een van de ingebouwde detectoren gebruiken om aanvullende informatie op te halen.

### <a name="using-the-detector-for-app-service"></a>De detector gebruiken voor App Service

1. Navigeer in de portal naar uw app.
2. Selecteer **Problemen vaststellen en oplossen**.
3. Kies **Beschikbaarheid en prestaties en** selecteer **Web-app niet beschikbaar.**
4. Zoek **Key Vault Application Settings Diagnostics** en klik op Meer **informatie.**


### <a name="using-the-detector-for-azure-functions"></a>De detector gebruiken voor Azure Functions

1. Navigeer in de portal naar uw app.
2. Navigeer naar **Platformfuncties.**
3. Selecteer **Problemen vaststellen en oplossen**.
4. Kies **Beschikbaarheid en prestaties en** selecteer **Functie-app niet beschikbaar of rapporteert fouten.**
5. Klik op **Key Vault Application Settings Diagnostics.**
