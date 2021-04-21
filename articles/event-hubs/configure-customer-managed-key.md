---
title: Uw eigen sleutel configureren voor het versleutelen Azure Event Hubs data-at-rest
description: Dit artikel bevat informatie over het configureren van uw eigen sleutel voor het versleutelen van Azure Event Hubs gegevens rest.
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 33587812121051d93aa8b939c3df70530ba65c5e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812440"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Door de klant beheerde sleutels configureren voor het versleutelen Azure Event Hubs data-at-rest met behulp van de Azure Portal
Azure Event Hubs biedt versleuteling van data-at-rest met Azure Storage Service Encryption (Azure SSE). De Event Hubs service gebruikt Azure Storage om de gegevens op te slaan. Alle gegevens die zijn opgeslagen met Azure Storage worden versleuteld met door Microsoft beheerde sleutels. Als u uw eigen sleutel gebruikt (ook wel aangeduid als Bring Your Own Key (BYOK) of door de klant beheerde sleutel), worden de gegevens nog steeds versleuteld met behulp van de door Microsoft beheerde sleutel, maar wordt de door Microsoft beheerde sleutel bovendien versleuteld met behulp van de door de klant beheerde sleutel. Met deze functie kunt u de toegang tot door de klant beheerde sleutels die worden gebruikt voor het versleutelen van door Microsoft beheerde sleutels maken, draaien, uitschakelen en intrekken. Het inschakelen van de BYOK-functie is een een keer instellen van uw naamruimte.

> [!NOTE]
> - De BYOK-mogelijkheid wordt ondersteund door [Event Hubs toegewezen clusters met één tenant.](event-hubs-dedicated-overview.md) Deze functie kan niet worden ingeschakeld voor standaard Event Hubs naamruimten.
> - De versleuteling kan alleen worden ingeschakeld voor nieuwe of lege naamruimten. Als de naamruimte Event Hubs bevat, mislukt de versleutelingsbewerking.

U kunt uw Azure Key Vault gebruiken om uw sleutels te beheren en uw sleutelgebruik te controleren. U kunt uw eigen sleutels maken en deze opslaan in een sleutelkluis of u kunt de Azure Key Vault API's gebruiken om sleutels te genereren. Zie [Wat is Azure Key Vault?](../key-vault/general/overview.md) voor meer informatie over Azure Key Vault.

In dit artikel wordt beschreven hoe u een sleutelkluis configureert met door de klant beheerde sleutels met behulp van de Azure Portal. Zie Voor meer informatie over het maken van een sleutelkluis met behulp van de [Azure Portal, Quickstart: Een](../key-vault/general/quick-create-portal.md)Azure Key Vault maken met behulp van Azure Portal .

> [!IMPORTANT]
> Als u door de klant beheerde sleutels Azure Event Hubs vereist dat voor de sleutelkluis twee vereiste eigenschappen zijn geconfigureerd. Dit zijn:  **Soft Delete** **en Do Not Purge**. Deze eigenschappen zijn standaard ingeschakeld wanneer u een nieuwe sleutelkluis maakt in de Azure Portal. Als u deze eigenschappen echter wilt inschakelen voor een bestaande sleutelkluis, moet u PowerShell of Azure CLI gebruiken.

## <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen
Als u door de klant beheerde sleutels in de Azure Portal, volgt u deze stappen:

1. Navigeer naar Event Hubs Dedicated cluster.
1. Selecteer de naamruimte waarvoor u BYOK wilt inschakelen.
1. Selecteer op **de** pagina Instellingen van Event Hubs naamruimte de optie **Versleuteling.** 
1. Selecteer de **versleuteling van door de klant beheerde sleutels at rest,** zoals wordt weergegeven in de volgende afbeelding. 

    ![Door de klant beheerde sleutel inschakelen](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Een sleutelkluis met sleutels instellen
Nadat u door de klant beheerde sleutels hebt ingeschakeld, moet u de door de klant beheerde sleutel koppelen aan Azure Event Hubs naamruimte. Event Hubs ondersteunt alleen Azure Key Vault. Als u de optie **Versleuteling met door** de klant beheerde sleutel in de vorige sectie inschakelen, moet u de sleutel importeren in Azure Key Vault. Voor de sleutels moet ook **De sleutel zijn** ingesteld op Soft Delete en Do Not **Purge.** Deze instellingen kunnen worden geconfigureerd met [behulp van PowerShell](../key-vault/general/key-vault-recovery.md) of [CLI.](../key-vault/general/key-vault-recovery.md)

1. Volg de snelstart om een nieuwe sleutelkluis Azure Key Vault [maken.](../key-vault/general/overview.md) Zie Over sleutels, geheimen en certificaten voor meer informatie over het importeren [van bestaande sleutels.](../key-vault/general/about-keys-secrets-certificates.md)
1. Gebruik de opdracht [az keyvault create](/cli/azure/keyvault#az_keyvault_create) om zowel de beveiliging voor het verwijderen als het opsluizen van een kluis in te zetten.

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Gebruik de opdracht [az keyvault update](/cli/azure/keyvault#az_keyvault_update) om beveiliging tegen opsluizen toe te voegen aan een bestaande kluis (waar al een soft delete is ingeschakeld).

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Volg deze stappen om sleutels te maken:
    1. Als u een nieuwe sleutel wilt maken, **selecteert u Genereren/importeren** in het menu **Sleutels** onder **Instellingen.**
        
        ![Selecteer de knop Genereren/importeren](./media/configure-customer-managed-key/select-generate-import.png)
    1. Stel **Opties in** op **Genereren** en geef de sleutel een naam.

        ![Een sleutel maken](./media/configure-customer-managed-key/create-key.png) 
    1. U kunt deze sleutel nu selecteren om te koppelen aan Event Hubs naamruimte voor versleuteling in de vervolgkeuzelijst. 

        ![Sleutel selecteren in sleutelkluis](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Vul de details voor de sleutel in en klik op **Selecteren.** Hiermee wordt de versleuteling van de door Microsoft beheerde sleutel met uw sleutel (door de klant beheerde sleutel) ingeschakeld. 


## <a name="rotate-your-encryption-keys"></a>Uw versleutelingssleutels roteren
U kunt uw sleutel roteren in de sleutelkluis met behulp van het roulatiemechanisme van Azure Key Vaults. Activerings- en vervaldatums kunnen ook worden ingesteld om sleutelrotatie te automatiseren. De Event Hubs-service detecteert nieuwe sleutelversies en begint deze automatisch te gebruiken.

## <a name="revoke-access-to-keys"></a>Toegang tot sleutels intrekken
Als u de toegang tot de versleutelingssleutels inroept, worden de gegevens niet op Event Hubs. De gegevens zijn echter niet toegankelijk vanuit de Event Hubs naamruimte. U kunt de versleutelingssleutel intrekken via toegangsbeleid of door de sleutel te verwijderen. Meer informatie over toegangsbeleid en het beveiligen van uw sleutelkluis van [Beveiligde toegang tot een sleutelkluis.](../key-vault/general/security-features.md)

Zodra de versleutelingssleutel is ingetrokken, Event Hubs de service in de versleutelde naamruimte niet meer werkt. Als de toegang tot de sleutel is ingeschakeld of de verwijdersleutel is hersteld, kiest de Event Hubs-service de sleutel zodat u toegang hebt tot de gegevens uit de versleutelde Event Hubs-naamruimte.

## <a name="set-up-diagnostic-logs"></a>Diagnostische logboeken instellen 
Als u diagnostische logboeken instelt voor byok-naamruimten, krijgt u de vereiste informatie over de bewerkingen. Deze logboeken kunnen worden ingeschakeld en later worden gestreamd naar een Event Hub of worden geanalyseerd via Log Analytics of gestreamd naar de opslag om aangepaste analyses uit te voeren. Zie Overzicht van diagnostische logboeken van Azure voor meer informatie [over diagnostische logboeken.](../azure-monitor/essentials/platform-logs-overview.md)

## <a name="enable-user-logs"></a>Gebruikerslogboeken inschakelen
Volg deze stappen om logboeken in te stellen voor door de klant beheerde sleutels.

1. Navigeer Azure Portal de naamruimte waarin BYOK is ingeschakeld.
1. Selecteer **Diagnostische instellingen** onder **Bewaking.**

    ![Diagnostische instellingen selecteren](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Selecteer **+Diagnostische instelling toevoegen.** 

    ![Selecteer Diagnostische instelling toevoegen](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Geef een **naam** op en selecteer waar u de logboeken naar wilt streamen.
1. Selecteer **CustomerManagedKeyUserLogs** en **Save.** Met deze actie worden de logboeken voor BYOK in de naamruimte in staat gemaakt.

    ![Selecteer de optie Door de klant beheerde sleutel voor gebruikerslogboeken](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Logboekschema 
Alle logboeken worden opgeslagen in JavaScript Object Notation indeling (JSON). Elk item heeft tekenreeksvelden die gebruikmaken van de indeling die in de volgende tabel wordt beschreven. 

| Naam | Beschrijving |
| ---- | ----------- | 
| TaskName | Beschrijving van de taak die is mislukt. |
| ActivityId: | Interne id die wordt gebruikt voor tracering. |
| category | Hiermee definieert u de classificatie van de taak. Als de sleutel uit uw sleutelkluis bijvoorbeeld wordt uitgeschakeld, is dit een informatiecategorie of als een sleutel niet kan worden uitschreven, kan deze onder een fout vallen. |
| resourceId | Azure Resource Manager resource-id |
| keyVault | Volledige naam van sleutelkluis. |
| sleutel | De sleutelnaam die wordt gebruikt voor het versleutelen van Event Hubs naamruimte. |
| versie | De versie van de sleutel die wordt gebruikt. |
| bewerking | De bewerking die wordt uitgevoerd op de sleutel in uw sleutelkluis. Schakel bijvoorbeeld de sleutel, het verpakken of uitpakken in of uit |
| code | De code die aan de bewerking is gekoppeld. Voorbeeld: Foutcode 404 betekent dat de sleutel niet is gevonden. |
| message | Elk foutbericht dat is gekoppeld aan de bewerking |

Hier is een voorbeeld van het logboek voor een door de klant beheerde sleutel:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>Een Resource Manager gebruiken om versleuteling in teschakelen
In deze sectie ziet u hoe u de volgende taken uitvoert met behulp **Azure Resource Manager sjablonen.** 

1. Maak een **Event Hubs-naamruimte** met een beheerde service-identiteit.
2. Maak een **sleutelkluis** en verleen de service-identiteit toegang tot de sleutelkluis. 
3. Werk de Event Hubs bij met de sleutelkluisgegevens (sleutel/waarde). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Een cluster Event Hubs naamruimte met beheerde service-identiteit maken
In deze sectie ziet u hoe u een Azure Event Hubs maakt met managed service identity met behulp van een Azure Resource Manager en PowerShell. 

1. Maak een Azure Resource Manager om een nieuwe Event Hubs maken met een beheerde service-identiteit. Noem het bestand: **CreateEventHubClusterAndNamespace.jsop**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Maak een sjabloonparameterbestand met de **naamCreateEventHubClusterAndNamespaceParams.jsmaken op**. 

    > [!NOTE]
    > Vervang de volgende waarden: 
    > - `<EventHubsClusterName>` - Naam van uw Event Hubs cluster    
    > - `<EventHubsNamespaceName>` - Naam van uw Event Hubs-naamruimte
    > - `<Location>` - Locatie van uw Event Hubs naamruimte

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. Voer de volgende PowerShell-opdracht uit om de sjabloon te implementeren om een Event Hubs maken. Haal vervolgens de id van de Event Hubs op om deze later te gebruiken. Vervang `{MyRG}` door de naam van de resourcegroep voordat u de opdracht gaat uitvoeren.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Toegang Event Hubs naamruimte-id verlenen tot key vault

1. Voer de volgende opdracht uit om een sleutelkluis te maken met **beveiliging tegen opsluizen** en **soft-delete** ingeschakeld. 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    (OF)    
    
    Voer de volgende opdracht uit om een bestaande **sleutelkluis bij te werken.** Geef waarden op voor de namen van de resourcegroep en sleutelkluis voordat u de opdracht gaat uitvoeren. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Stel het toegangsbeleid voor de sleutelkluis in, zodat de beheerde identiteit van de Event Hubs-naamruimte toegang heeft tot de sleutelwaarde in de sleutelkluis. Gebruik de id van de Event Hubs naamruimte uit de vorige sectie. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Gegevens in Event Hubs-naamruimte versleutelen met door de klant beheerde sleutel uit key vault
U hebt tot nu toe de volgende stappen uitgevoerd: 

1. U hebt een Premium-naamruimte gemaakt met een beheerde identiteit.
2. Maak een sleutelkluis en verleende de beheerde identiteit toegang tot de sleutelkluis. 

In deze stap gaat u de naamruimte Event Hubs sleutelkluis bijwerken met informatie over de sleutelkluis. 

1. Maak een JSON-bestand **metCreateEventHubClusterAndNamespace.jsmet** de volgende inhoud: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. Maak een sjabloonparameterbestand: **UpdateEventHubClusterAndNamespaceParams.jsop**. 

    > [!NOTE]
    > Vervang de volgende waarden: 
    > - `<EventHubsClusterName>` - Naam van uw Event Hubs cluster.        
    > - `<EventHubsNamespaceName>` - Naam van uw Event Hubs naamruimte
    > - `<Location>` - Locatie van uw Event Hubs naamruimte
    > - `<KeyVaultName>` - Naam van uw sleutelkluis
    > - `<KeyName>` - Naam van de sleutel in de sleutelkluis

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. Voer de volgende PowerShell-opdracht uit om de sjabloon Resource Manager implementeren. Vervang `{MyRG}` door de naam van de resourcegroep voordat u de opdracht gaat uitvoeren. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Problemen oplossen
Schakel als best practice logboeken altijd in, zoals weergegeven in de vorige sectie. Het helpt bij het bijhouden van de activiteiten wanneer BYOK-versleuteling is ingeschakeld. Het helpt ook bij het oplossen van problemen.

Hieronder volgen de veelvoorkomende foutcodes om te zoeken wanneer BYOK-versleuteling is ingeschakeld.

| Bewerking | Foutcode | Resulterende status van gegevens |
| ------ | ---------- | ----------------------- | 
| Machtiging voor verpakken/uitpakken verwijderen uit een sleutelkluis | 403 |    Ontoegankelijk |
| AAD-rollidmaatschap verwijderen uit een AAD-principal die de machtiging voor verpakken/uitpakken heeft verleend | 403 |  Ontoegankelijk |
| Een versleutelingssleutel verwijderen uit de sleutelkluis | 404 | Ontoegankelijk |
| De sleutelkluis verwijderen | 404 | Niet toegankelijk (ervan uit dat voorlopig verwijderen is ingeschakeld, wat een vereiste instelling is.) |
| De verloopperiode voor de versleutelingssleutel wijzigen zodat deze al is verlopen | 403 |   Ontoegankelijk  |
| De NBF wijzigen (niet eerder) zodat de sleutelversleutelingssleutel niet actief is | 403 | Ontoegankelijk  |
| Selecteer de **optie MSFT Services toestaan voor** de firewall van de sleutelkluis of blokkeer op een andere manier de netwerktoegang tot de sleutelkluis die de versleutelingssleutel heeft | 403 | Ontoegankelijk |
| De sleutelkluis verplaatsen naar een andere tenant | 404 | Ontoegankelijk |  
| Onregelmatige netwerkprobleem of DNS/AAD/MSI-storing |  | Toegankelijk met de versleutelingssleutel voor gegevens in de cache |

> [!IMPORTANT]
> Als u Geo-DR wilt inschakelen voor een naamruimte die gebruik maakt van de BYOK-versleuteling, moet de secundaire naamruimte voor koppelen zich in een toegewezen cluster en moet er een door het systeem toegewezen beheerde identiteit zijn ingeschakeld. Zie Beheerde identiteiten [voor Azure-resources voor meer informatie.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:
- [Event Hubs-overzicht](event-hubs-about.md)
- [Key Vault overzicht](../key-vault/general/overview.md)