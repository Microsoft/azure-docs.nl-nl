---
title: Key Vault-referenties gebruiken
description: Meer informatie over het instellen van Azure App Service en Azure Functions om Azure Key Vault verwijzingen te gebruiken. Key Vault geheimen beschikbaar maken voor uw toepassings code.
author: mattchenderson
ms.topic: article
ms.date: 02/05/2021
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 69fc0d6f3c4e18b34555a099f4e28e278ca3bdad
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635384"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Gebruik Key Vault verwijzingen voor App Service en Azure Functions

In dit onderwerp wordt beschreven hoe u kunt werken met geheimen van Azure Key Vault in uw App Service of Azure Functions toepassing zonder dat u code wijzigingen hoeft aan te brengen. [Azure Key Vault](../key-vault/general/overview.md) is een service die gecentraliseerd geheimen beheer biedt, met volledige controle over het toegangs beleid en de controle geschiedenis.

## <a name="granting-your-app-access-to-key-vault"></a>Toegang tot Key Vault verlenen aan uw app

Als u geheimen van Key Vault wilt lezen, moet er een kluis zijn gemaakt en moet u uw app toegang geven tot het bestand.

1. Maak een sleutel kluis door de [Key Vault Snelstartgids](../key-vault/secrets/quick-create-cli.md)te volgen.

1. Maak een door het [systeem toegewezen beheerde identiteit](overview-managed-identity.md) voor uw toepassing.

   > [!NOTE] 
   > Key Vault verwijzingen worden momenteel alleen door het systeem toegewezen beheerde identiteiten ondersteund. Door de gebruiker toegewezen identiteiten kunnen niet worden gebruikt.

1. Maak een [toegangs beleid in Key Vault](../key-vault/general/secure-your-key-vault.md#key-vault-access-policies) voor de toepassings-id die u eerder hebt gemaakt. Schakel de geheime machtiging ' Get ' in voor dit beleid. Configureer de "geautoriseerde toepassing" of `applicationId` instellingen niet, omdat deze niet compatibel is met een beheerde identiteit.

   > [!IMPORTANT]
   > Key Vault verwijzingen zijn niet in staat om geheimen op te lossen die zijn opgeslagen in een sleutel kluis met [netwerk beperkingen](../key-vault/general/overview-vnet-service-endpoints.md) , tenzij de app wordt gehost in een [app service Environment](./environment/intro.md).

## <a name="reference-syntax"></a>Verwijzings syntaxis

Een Key Vault verwijzing is van het formulier `@Microsoft.KeyVault({referenceString})` , waarbij `{referenceString}` wordt vervangen door een van de volgende opties:

> [!div class="mx-tdBreakAll"]
> | Verwijzings reeks                                                            | Description                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri =_SecretUri_                                                       | De **SecretUri** moet de volledige gegevenslaag URI zijn van een geheim in Key Vault, eventueel met inbegrip van een versie, bijvoorbeeld, `https://myvault.vault.azure.net/secrets/mysecret/` of `https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931`  |
> | Kluisnaam =_kluis_; Geheim =_geheim_; SecretVersion =_SecretVersion_ | De **kluisnaam** is vereist en moet de naam van uw Key Vault bron zijn. De naam van het **geheim** is vereist en moet het doel geheim zijn. De **SecretVersion** is optioneel, maar als deze aanwezig is, is dit de versie van het geheim dat moet worden gebruikt. |

Een volledige verwijzing ziet er bijvoorbeeld als volgt uit:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/)
```

U kunt ook het volgende doen:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret)
```

## <a name="rotation"></a>Rotatie

Als er in de verwijzing geen versie is opgegeven, gebruikt de app de nieuwste versie die in Key Vault aanwezig is. Als nieuwere versies beschikbaar komen, zoals bij een rotatie gebeurtenis, wordt de app automatisch bijgewerkt en wordt de nieuwste versie binnen één dag gebruikt. Wijzigingen in de configuratie van de app zorgen ervoor dat de meest recente versies van alle geheimen waarnaar wordt verwezen direct worden bijgewerkt.

## <a name="source-application-settings-from-key-vault"></a>Instellingen van de bron toepassing van Key Vault

Key Vault verwijzingen kunnen worden gebruikt als waarden voor [Toepassings instellingen](configure-common.md#configure-app-settings), zodat u geheimen in Key Vault in plaats van de site configuratie kunt blijven gebruiken. Toepassings instellingen worden op rest versleuteld, maar als u de mogelijkheden van een geheim beheer nodig hebt, moeten ze in Key Vault gaan.

Als u een Key Vault referentie voor een toepassings instelling wilt gebruiken, stelt u de verwijzing in als de waarde van de instelling. Uw app kan naar het geheim verwijzen via de sleutel normaal. Er zijn geen code wijzigingen vereist.

> [!TIP]
> De meeste toepassings instellingen die gebruikmaken van Key Vault verwijzingen moeten worden gemarkeerd als sleuf instellingen, omdat u voor elke omgeving afzonderlijke kluizen moet hebben.

### <a name="azure-resource-manager-deployment"></a>Implementatie van Azure Resource Manager

Wanneer u de implementatie van resources via Azure Resource Manager sjablonen automatiseert, moet u mogelijk uw afhankelijkheden in een bepaalde volg orde rangschikken om deze functie te kunnen gebruiken. Houd er rekening mee dat u de toepassings instellingen moet definiëren als hun eigen resource, in plaats van een `siteConfig` eigenschap in de site definitie te gebruiken. Dit komt doordat de site eerst moet worden gedefinieerd, zodat de door het systeem toegewezen identiteit wordt gemaakt en kan worden gebruikt in het toegangs beleid.

Een voor beeld van een pseudo-sjabloon voor een functie-app kan er als volgt uitzien:

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
> In dit voor beeld is de bron beheer implementatie afhankelijk van de toepassings instellingen. Dit is normaal gesp roken onveilig gedrag, omdat het bijwerken van de app-instelling asynchroon werkt. Omdat we echter de `WEBSITE_ENABLE_SYNC_UPDATE_SITE` toepassings instelling hebben opgenomen, is de update synchroon. Dit betekent dat de implementatie van broncode beheer alleen begint zodra de instellingen van de toepassing volledig zijn bijgewerkt.

## <a name="troubleshooting-key-vault-references"></a>Problemen met Key Vault verwijzingen oplossen

Als een verwijzing niet correct wordt opgelost, wordt in plaats daarvan de referentie waarde gebruikt. Dit betekent dat voor toepassings instellingen een omgevings variabele wordt gemaakt waarvan de waarde de `@Microsoft.KeyVault(...)` syntaxis heeft. Dit kan ertoe leiden dat de toepassing fouten genereert, omdat er een geheim van een bepaalde structuur werd verwacht.

Dit wordt meestal veroorzaakt door een onjuiste configuratie van het Key Vault- [toegangs beleid](#granting-your-app-access-to-key-vault). Het kan echter ook worden veroorzaakt door een geheim dat niet meer aanwezig is of een syntaxis fout in de verwijzing zelf.

Als de syntaxis juist is, kunt u andere oorzaken voor fouten weer geven door de huidige oplossings status in de portal te controleren. Navigeer naar toepassings instellingen en selecteer bewerken voor de betreffende verwijzing. Onder de instellings configuratie ziet u status informatie, inclusief eventuele fouten. Als deze ontbreken, betekent dit dat de verwijzings syntaxis ongeldig is.

U kunt ook een van de ingebouwde detectoren gebruiken om aanvullende informatie te krijgen.

### <a name="using-the-detector-for-app-service"></a>De detector voor App Service gebruiken

1. Navigeer in de portal naar uw app.
2. Selecteer **Problemen vaststellen en oplossen**.
3. Kies **Beschik baarheid en prestaties** en selecteer **Web-app lager.**
4. Zoek **Key Vault diagnostische gegevens over toepassings instellingen** en klik op **meer informatie**.


### <a name="using-the-detector-for-azure-functions"></a>De detector voor Azure Functions gebruiken

1. Navigeer in de portal naar uw app.
2. Navigeer naar **platform functies.**
3. Selecteer **Problemen vaststellen en oplossen**.
4. Kies **Beschik baarheid en prestaties** en selecteer **functie-app omlaag of rapportage fouten.**
5. Klik op **Key Vault diagnostische gegevens over toepassings instellingen.**
