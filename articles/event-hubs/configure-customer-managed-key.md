---
title: Configureer uw eigen sleutel voor het versleutelen van Azure Event Hubs gegevens in rust
description: Dit artikel bevat informatie over het configureren van uw eigen sleutel voor het versleutelen van Azure Event Hubs gegevens rest.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 1b0469a2f25b7f2bec2668b6ab33ff99eb1df809
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348208"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Door de klant beheerde sleutels voor het versleutelen van Azure Event Hubs-gegevens op rest configureren met behulp van de Azure Portal
Azure Event Hubs zorgt voor versleuteling van gegevens in rust met Azure Storage-service versleuteling (Azure SSE). Event Hubs is afhankelijk van Azure Storage om de gegevens op te slaan en standaard worden alle gegevens die zijn opgeslagen met Azure Storage versleuteld met door micro soft beheerde sleutels. 

## <a name="overview"></a>Overzicht
Azure Event Hubs ondersteunt nu de mogelijkheid om gegevens in rust te versleutelen met door micro soft beheerde sleutels of door de klant beheerde sleutels (Bring Your Own Key – BYOK). Met deze functie kunt u toegang tot de door de klant beheerde sleutels maken, draaien, uitschakelen en intrekken die worden gebruikt voor het versleutelen van Azure Event Hubs-gegevens in rust.

Het inschakelen van de functie BYOK is een eenmalige installatie procedure voor uw naam ruimte.

> [!NOTE]
> De BYOK-functionaliteit wordt ondersteund door [Event hubs toegewezen clusters met één Tenant](event-hubs-dedicated-overview.md) . Het kan niet worden ingeschakeld voor standaard Event Hubs naam ruimten.

U kunt Azure Key Vault gebruiken voor het beheren van uw sleutels en het controleren van uw sleutel gebruik. U kunt uw eigen sleutels maken en deze opslaan in een sleutelkluis of u kunt de Azure Key Vault API's gebruiken om sleutels te genereren. Zie [Wat is Azure Key Vault?](../key-vault/general/overview.md) voor meer informatie over Azure Key Vault.

In dit artikel wordt beschreven hoe u een sleutel kluis kunt configureren met door de klant beheerde sleutels met behulp van de Azure Portal. Ga voor meer informatie over het maken van een sleutel kluis met behulp van de Azure Portal naar [Quick Start: een Azure Key Vault maken met behulp van de Azure Portal](../key-vault/general/quick-create-portal.md).

> [!IMPORTANT]
> Voor het gebruik van door de klant beheerde sleutels met Azure Event Hubs moet de sleutel kluis twee vereiste eigenschappen hebben geconfigureerd. Dit zijn:  **voorlopig verwijderen** en **niet opschonen**. Deze eigenschappen zijn standaard ingeschakeld wanneer u een nieuwe sleutel kluis maakt in de Azure Portal. Als u deze eigenschappen echter wilt inschakelen voor een bestaande sleutel kluis, moet u Power shell of Azure CLI gebruiken.

## <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen
Voer de volgende stappen uit om door de klant beheerde sleutels in te scha kelen in de Azure Portal:

1. Navigeer naar uw Event Hubs Dedicated-cluster.
1. Selecteer de naam ruimte waarvoor u BYOK wilt inschakelen.
1. Selecteer op de pagina **instellingen** van uw event hubs-naam ruimte de optie **versleuteling**. 
1. Selecteer de door de **klant beheerde sleutel versleuteling bij rest** , zoals wordt weer gegeven in de volgende afbeelding. 

    ![Door de klant beheerde sleutel inschakelen](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Een sleutel kluis met sleutels instellen
Nadat u door de klant beheerde sleutels hebt ingeschakeld, moet u de door de klant beheerde sleutel koppelen aan uw Azure Event Hubs-naam ruimte. Event Hubs ondersteunt alleen Azure Key Vault. Als u de optie **versleuteling met door de klant beheerde sleutel** in de vorige sectie inschakelt, moet u de sleutel in azure Key Vault importeren. De sleutels moeten ook **zacht verwijderen** hebben en **niet leeg** zijn geconfigureerd voor de sleutel. Deze instellingen kunnen worden geconfigureerd met [Power shell](../key-vault/general/key-vault-recovery.md) of [cli](../key-vault/general/key-vault-recovery.md).

1. Als u een nieuwe sleutel kluis wilt maken, volgt u de Azure Key Vault [Snelstartgids](../key-vault/general/overview.md). Zie [over sleutels, geheimen en certificaten](../key-vault/general/about-keys-secrets-certificates.md)voor meer informatie over het importeren van bestaande sleutels.
1. Gebruik de opdracht AZ-sleutel [kluis maken](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) om zowel zacht verwijderen als beveiliging opschonen in te scha kelen bij het maken van een kluis.

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Gebruik de opdracht AZ-sleutel [kluis bijwerken](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) om een schone beveiliging toe te voegen aan een bestaande kluis (waarvoor al zacht verwijderen is ingeschakeld).

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Maak sleutels door de volgende stappen uit te voeren:
    1. Als u een nieuwe sleutel wilt maken, selecteert u **genereren/importeren** in het menu **sleutels** onder **instellingen**.
        
        ![Selecteer de knop genereren/importeren](./media/configure-customer-managed-key/select-generate-import.png)
    1. Stel **Opties** in voor het **genereren** en geven van de sleutel een naam.

        ![Een sleutel maken](./media/configure-customer-managed-key/create-key.png) 
    1. U kunt nu deze sleutel selecteren om te koppelen aan de Event Hubs naam ruimte voor het versleutelen van de vervolg keuzelijst. 

        ![Selecteer een sleutel in de sleutel kluis](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Vul de Details voor de sleutel in en klik op **selecteren**. Dit zorgt ervoor dat de versleuteling van gegevens in rust in de naam ruimte met een door de klant beheerde sleutel. 


## <a name="rotate-your-encryption-keys"></a>Uw versleutelings sleutels draaien
U kunt de sleutel in de sleutel kluis draaien met behulp van het rotatie mechanisme voor Azure-sleutel kluizen. Activerings-en verloop datums kunnen ook worden ingesteld op het automatiseren van sleutel rotatie. Met de Event Hubs-service worden nieuwe sleutel versies gedetecteerd en worden ze automatisch gestart.

## <a name="revoke-access-to-keys"></a>Toegang tot sleutels intrekken
Als u de toegang tot de versleutelings sleutels intrekt, worden de gegevens niet verwijderd uit Event Hubs. De gegevens kunnen echter niet worden geopend vanuit de naam ruimte Event Hubs. U kunt de versleutelings sleutel intrekken via toegangs beleid of door de sleutel te verwijderen. Meer informatie over toegangs beleid en het beveiligen van uw sleutel kluis van [beveiligde toegang tot een sleutel kluis](../key-vault/general/secure-your-key-vault.md).

Zodra de versleutelings sleutel is ingetrokken, wordt de Event Hubs-service op de versleutelde naam ruimte niet meer bruikbaar. Als de toegang tot de sleutel is ingeschakeld of de sleutel verwijderen is hersteld, wordt de sleutel door Event Hubs service gekozen, zodat u toegang hebt tot de gegevens van de versleutelde Event Hubs naam ruimte.

## <a name="set-up-diagnostic-logs"></a>Diagnostische logboeken instellen 
Door Diagnostische logboeken in te stellen voor met BYOK ingeschakelde naam ruimten, beschikt u over de vereiste informatie over de bewerkingen wanneer een naam ruimte wordt versleuteld met door de klant beheerde sleutels. Deze logboeken kunnen worden ingeschakeld en later naar een Event Hub worden gestreamd of geanalyseerd via log Analytics of worden gestreamd naar opslag om aangepaste analyses uit te voeren. Zie [overzicht van Diagnostische logboeken van Azure](../azure-monitor/platform/platform-logs-overview.md)voor meer informatie over Diagnostische logboeken.

## <a name="enable-user-logs"></a>Gebruikers Logboeken inschakelen
Volg deze stappen om Logboeken in te scha kelen voor door de klant beheerde sleutels.

1. Navigeer in het Azure Portal naar de naam ruimte waarop BYOK is ingeschakeld.
1. Selecteer **Diagnostische instellingen** onder **bewaking**.

    ![Diagnostische instellingen selecteren](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Selecteer **+ Diagnostische instelling toevoegen**. 

    ![Diagnostische instelling toevoegen selecteren](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Geef een **naam** op en selecteer waarnaar u de logboeken wilt streamen.
1. Selecteer **CustomerManagedKeyUserLogs** en **Opslaan**. Met deze actie worden de logboeken voor BYOK in de naam ruimte ingeschakeld.

    ![Selecteer door de klant beheerde sleutel gebruikers Logboeken](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Logboek schema 
Alle logboeken worden opgeslagen in de indeling van de JavaScript Object Notation (JSON). Elke vermelding bevat teken reeks velden die gebruikmaken van de indeling die wordt beschreven in de volgende tabel. 

| Naam | Beschrijving |
| ---- | ----------- | 
| TaskName | Beschrijving van de taak die is mislukt. |
| ActivityId | Interne ID die wordt gebruikt voor tracering. |
| category | Hiermee wordt de classificatie van de taak gedefinieerd. Als de sleutel van uw sleutel kluis bijvoorbeeld wordt uitgeschakeld, zou dit een informatie categorie zijn, of als een sleutel niet kan worden teruggedraaid, kan dit onder een fout vallen. |
| resourceId | Resource-ID Azure Resource Manager |
| keyVault | Volledige naam van de sleutel kluis. |
| sleutel | De naam van de sleutel die wordt gebruikt voor het versleutelen van de Event Hubs naam ruimte. |
| versie | De versie van de sleutel die wordt gebruikt. |
| bewerking | De bewerking die wordt uitgevoerd op de sleutel in uw sleutel kluis. Schakel bijvoorbeeld de sleutel, de tekst terugloop of de terugloop uit |
| code | De code die aan de bewerking is gekoppeld. Voor beeld: fout code, 404 betekent dat de sleutel niet is gevonden. |
| message | Elk fout bericht dat is gekoppeld aan de bewerking |

Hier volgt een voor beeld van het logboek voor een door de klant beheerde sleutel:

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

## <a name="use-resource-manager-template-to-enable-encryption"></a>Resource Manager-sjabloon gebruiken om versleuteling in te scha kelen
In deze sectie wordt beschreven hoe u de volgende taken kunt uitvoeren met behulp van **Azure Resource Manager sjablonen**. 

1. Maak een **Event hubs naam ruimte** met een beheerde service-identiteit.
2. Maak een **sleutel kluis** en verleen de service-identiteit toegang tot de sleutel kluis. 
3. Werk de Event Hubs naam ruimte bij met de sleutel kluis gegevens (sleutel/waarde). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Een Event Hubs cluster en een naam ruimte maken met een beheerde service-identiteit
In deze sectie wordt beschreven hoe u een Azure Event Hubs-naam ruimte met een beheerde service-identiteit maakt met behulp van een Azure Resource Manager sjabloon en Power shell. 

1. Maak een Azure Resource Manager sjabloon voor het maken van een Event Hubs naam ruimte met een beheerde service-identiteit. Geef het bestand de naam: **CreateEventHubClusterAndNamespace.jsop**: 

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
2. Maak een sjabloon parameter bestand met de naam: **CreateEventHubClusterAndNamespaceParams.jsop**. 

    > [!NOTE]
    > Vervang de volgende waarden: 
    > - `<EventHubsClusterName>` -De naam van uw Event Hubs cluster    
    > - `<EventHubsNamespaceName>` -Naam van de naam ruimte van uw Event Hubs
    > - `<Location>` -Locatie van uw Event Hubs naam ruimte

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
3. Voer de volgende Power shell-opdracht uit om de sjabloon te implementeren om een Event Hubs naam ruimte te maken. Vervolgens haalt u de ID van de Event Hubs naam ruimte op om deze later te gebruiken. Vervang door `{MyRG}` de naam van de resource groep voordat u de opdracht uitvoert.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Identiteit van Event Hubs naam ruimte verlenen toegang tot de sleutel kluis

1. Voer de volgende opdracht uit om een sleutel kluis te maken met **opschoon beveiliging** en **zacht verwijderen** ingeschakeld. 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    OF    
    
    Voer de volgende opdracht uit om een **bestaande sleutel kluis** bij te werken. Geef waarden op voor de naam van de resource groep en de sleutel kluis voordat u de opdracht uitvoert. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Stel het toegangs beleid voor de sleutel kluis zo in dat de beheerde identiteit van de Event Hubs naam ruimte toegang kan krijgen tot de sleutel waarde in de sleutel kluis. Gebruik de ID van de Event Hubs naam ruimte uit de vorige sectie. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Gegevens in Event Hubs naam ruimte versleutelen met door de klant beheerde sleutel van de sleutel kluis
U hebt de volgende stappen tot nu toe uitgevoerd: 

1. Er is een Premium-naam ruimte met een beheerde identiteit gemaakt.
2. Maak een sleutel kluis en verleend de beheerde identiteit toegang tot de sleutel kluis. 

In deze stap werkt u de Event Hubs naam ruimte bij met sleutel kluis gegevens. 

1. Maak een JSON-bestand met de naam **CreateEventHubClusterAndNamespace.jsop** met de volgende inhoud: 

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

2. Een sjabloon parameter bestand maken: **UpdateEventHubClusterAndNamespaceParams.jsop**. 

    > [!NOTE]
    > Vervang de volgende waarden: 
    > - `<EventHubsClusterName>` -De naam van uw Event Hubs cluster.        
    > - `<EventHubsNamespaceName>` -Naam van de naam ruimte van uw Event Hubs
    > - `<Location>` -Locatie van uw Event Hubs naam ruimte
    > - `<KeyVaultName>` -Naam van uw sleutel kluis
    > - `<KeyName>` -De naam van de sleutel in de sleutel kluis

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
3. Voer de volgende Power shell-opdracht uit om de Resource Manager-sjabloon te implementeren. Vervang door `{MyRG}` de naam van uw resource groep voordat u de opdracht uitvoert. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Problemen oplossen
Schakel als best practice altijd Logboeken in, zoals wordt weer gegeven in de vorige sectie. Het helpt bij het volgen van de activiteiten wanneer BYOK-versleuteling is ingeschakeld. Het helpt ook om het probleem op te lossen.

Hieronder vindt u de algemene fout codes die u moet zoeken wanneer BYOK-versleuteling is ingeschakeld.

| Bewerking | Foutcode | Resulterende status van gegevens |
| ------ | ---------- | ----------------------- | 
| De machtiging voor het verpakken/uitpakken van een sleutel kluis verwijderen | 403 |    Niet toegankelijk |
| Het lidmaatschap van AAD-rollen verwijderen uit een AAD-principal die de machtiging voor terugloop/uitpakken heeft gekregen | 403 |  Niet toegankelijk |
| Een versleutelings sleutel uit de sleutel kluis verwijderen | 404 | Niet toegankelijk |
| De sleutel kluis verwijderen | 404 | Niet toegankelijk (Hiermee wordt verondersteld dat zacht verwijderen is ingeschakeld. Dit is een vereiste instelling.) |
| De verval periode van de versleutelings sleutel wijzigen zodat deze al is verlopen | 403 |   Niet toegankelijk  |
| De NBF (niet voor) wijzigen, waardoor de sleutel versleutelings sleutel niet actief is | 403 | Niet toegankelijk  |
| Als u de optie **MSFT-Services toestaan** voor de sleutel kluis firewall selecteert of de netwerk toegang blokkeert voor de sleutel kluis met de versleutelings sleutel | 403 | Niet toegankelijk |
| De sleutel kluis verplaatsen naar een andere Tenant | 404 | Niet toegankelijk |  
| Onregelmatige netwerk problemen of DNS/AAD/MSI-onderbreking |  | Toegankelijk via de gegevens versleutelings sleutel in de cache |

> [!IMPORTANT]
> Als u geo-DR wilt inschakelen voor een naam ruimte die gebruikmaakt van de BYOK-versleuteling, moet de secundaire naam ruimte voor koppelen zich in een toegewijd cluster bevinden en moet er een door het systeem toegewezen beheerde identiteit zijn ingeschakeld. Zie [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:
- [Event Hubs-overzicht](event-hubs-about.md)
- [Overzicht van Key Vault](../key-vault/general/overview.md)