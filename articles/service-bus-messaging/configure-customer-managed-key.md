---
title: Uw eigen sleutel configureren voor het versleutelen Azure Service Bus data-at-rest
description: Dit artikel bevat informatie over het configureren van uw eigen sleutel voor het versleutelen van Azure Service Bus data rest.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: 6b982f01b02e7aa99f1b83e2f590e3660cb69c54
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751342"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Door de klant beheerde sleutels configureren voor het versleutelen Azure Service Bus data-at-rest met behulp van de Azure Portal
Azure Service Bus Premium biedt versleuteling van data-at-rest met Azure Storage Service Encryption (Azure SSE). Service Bus Premium gebruikt Azure Storage om de gegevens op te slaan. Alle gegevens die zijn opgeslagen met Azure Storage worden versleuteld met door Microsoft beheerde sleutels. Als u uw eigen sleutel gebruikt (ook wel aangeduid als Bring Your Own Key (BYOK) of door de klant beheerde sleutel), worden de gegevens nog steeds versleuteld met behulp van de door Microsoft beheerde sleutel, maar wordt de door Microsoft beheerde sleutel bovendien versleuteld met behulp van de door de klant beheerde sleutel. Met deze functie kunt u de toegang maken, draaien, uitschakelen en intrekken tot door de klant beheerde sleutels die worden gebruikt voor het versleutelen van door Microsoft beheerde sleutels. Het inschakelen van de BYOK-functie is een een keer instellen in uw naamruimte.

Er zijn enkele waarschuwingen voor de door de klant beheerde sleutel voor versleuteling aan de servicezijde. 
- Deze functie wordt ondersteund door [Azure Service Bus Premium-laag.](service-bus-premium-messaging.md) Deze kan niet worden ingeschakeld voor standard-Service Bus-naamruimten.
- De versleuteling kan alleen worden ingeschakeld voor nieuwe of lege naamruimten. Als de naamruimte wachtrijen of onderwerpen bevat, mislukt de versleutelingsbewerking.

U kunt deze Azure Key Vault sleutels te beheren en uw sleutelgebruik te controleren. U kunt uw eigen sleutels maken en deze opslaan in een sleutelkluis of u kunt de Azure Key Vault API's gebruiken om sleutels te genereren. Zie [Wat is Azure Key Vault?](../key-vault/general/overview.md) voor meer informatie over Azure Key Vault.

In dit artikel wordt beschreven hoe u een sleutelkluis configureert met door de klant beheerde sleutels met behulp van de Azure Portal. Zie Voor meer informatie over het maken van een sleutelkluis met behulp van de Azure Portal [Quickstart: Een](../key-vault/general/quick-create-portal.md)Azure Key Vault maken met behulp van Azure Portal .

> [!IMPORTANT]
> Als u door de klant beheerde sleutels Azure Service Bus, moet voor de sleutelkluis twee vereiste eigenschappen zijn geconfigureerd. Dit zijn:  **Soft Delete** **en Do Not Purge**. Deze eigenschappen zijn standaard ingeschakeld wanneer u een nieuwe sleutelkluis in de Azure Portal. Als u deze eigenschappen echter wilt inschakelen voor een bestaande sleutelkluis, moet u PowerShell of Azure CLI gebruiken.

## <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen
Als u door de klant beheerde sleutels in de Azure Portal, volgt u deze stappen:

1. Navigeer naar Service Bus Premium-naamruimte.
2. Selecteer op **de** pagina Instellingen van Service Bus naamruimte de optie **Versleuteling.**
3. Selecteer **versleuteling at rest door de klant beheerde sleutel,** zoals wordt weergegeven in de volgende afbeelding.

    ![Door de klant beheerde sleutel inschakelen](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Een sleutelkluis met sleutels instellen

Nadat u door de klant beheerde sleutels hebt ingeschakeld, moet u de door de klant beheerde sleutel koppelen aan Azure Service Bus naamruimte. Service Bus ondersteunt alleen Azure Key Vault. Als u de optie **Versleuteling met door** de klant beheerde sleutel in de vorige sectie inschakelen, moet u de sleutel importeren in Azure Key Vault. Daarnaast moeten voor de sleutels **De sleutel zijn** geconfigureerd voor Soft Delete en Do Not **Purge.** Deze instellingen kunnen worden geconfigureerd met Behulp van [PowerShell](../key-vault/general/key-vault-recovery.md) of [CLI.](../key-vault/general/key-vault-recovery.md)

1. Als u een nieuwe sleutelkluis wilt maken, volgt u de Azure Key Vault [Quickstart.](../key-vault/general/overview.md) Zie Over sleutels, geheimen en certificaten voor meer informatie over het importeren [van bestaande sleutels.](../key-vault/general/about-keys-secrets-certificates.md)
1. Gebruik de opdracht [az keyvault create](/cli/azure/keyvault#az-keyvault-create) om bij het maken van een kluis zowel de beveiliging voor het verwijderen als het opsluizen van de kluis in te voeren.

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Gebruik de opdracht [az keyvault update](/cli/azure/keyvault#az-keyvault-update) om beveiliging tegen opsluizen toe te voegen aan een bestaande kluis (die al is ingeschakeld voor soft delete).

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Volg deze stappen om sleutels te maken:
    1. Als u een nieuwe sleutel wilt maken, **selecteert u Genereren/importeren** in het menu **Sleutels** onder **Instellingen.**
        
        ![Selecteer de knop Genereren/importeren](./media/configure-customer-managed-key/select-generate-import.png)

    1. Stel **Opties in** op **Genereren** en geef de sleutel een naam.

        ![Een sleutel maken](./media/configure-customer-managed-key/create-key.png) 

    1. U kunt deze sleutel nu selecteren om te koppelen aan Service Bus naamruimte voor versleuteling in de vervolgkeuzelijst. 

        ![Sleutel selecteren in sleutelkluis](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Voor redundantie kunt u maximaal 3 sleutels toevoegen. Als een van de sleutels is verlopen of niet toegankelijk is, worden de andere sleutels gebruikt voor versleuteling.
        
    1. Vul de details voor de sleutel in en klik op **Selecteren.** Hiermee wordt de versleuteling van de door Microsoft beheerde sleutel met uw sleutel (door de klant beheerde sleutel) ingeschakeld. 


    > [!IMPORTANT]
    > Als u de door de klant beheerde sleutel wilt gebruiken samen met geo-noodherstel, raadpleegt u deze sectie. 
    >
    > Als u versleuteling van door Microsoft beheerde sleutels wilt inschakelen met een door de klant beheerde sleutel, wordt een toegangsbeleid ingesteld voor de beheerde identiteit van de Service Bus op de opgegeven Azure KeyVault. [](../key-vault/general/security-overview.md) Dit zorgt voor beheerde toegang tot De Azure KeyVault vanuit Azure Service Bus naamruimte.
    >
    > Als gevolg van deze:
    > 
    >   * Als [geo-noodherstel](service-bus-geo-dr.md) al is ingeschakeld voor de Service Bus-naamruimte en u op zoek bent naar door de klant beheerde sleutel, dan 
    >     * De koppeling breken
    >     * [Stel het toegangsbeleid voor](../key-vault/general/assign-access-policy-portal.md) de beheerde identiteit in voor de primaire en secundaire naamruimten voor de sleutelkluis.
    >     * Stel versleuteling in voor de primaire naamruimte.
    >     * Koppel de primaire en secundaire naamruimten opnieuw.
    > 
    >   * Als u Geo-DR wilt inschakelen voor een Service Bus waar de door de klant beheerde sleutel al is ingesteld, dan -
    >     * [Stel het toegangsbeleid voor de](../key-vault/general/assign-access-policy-portal.md) beheerde identiteit in voor de secundaire naamruimte voor de sleutelkluis.
    >     * Koppel de primaire en secundaire naamruimten.


## <a name="rotate-your-encryption-keys"></a>Uw versleutelingssleutels roteren

U kunt uw sleutel roteren in de sleutelkluis met behulp van het roulatiemechanisme van Azure Key Vaults. Activerings- en vervaldatums kunnen ook worden ingesteld om sleutelrotatie te automatiseren. De Service Bus-service detecteert nieuwe sleutelversies en begint deze automatisch te gebruiken.

## <a name="revoke-access-to-keys"></a>Toegang tot sleutels intrekken

Als u de toegang tot de versleutelingssleutels inroept, worden de gegevens niet op Service Bus. De gegevens zijn echter niet toegankelijk vanuit de Service Bus naamruimte. U kunt de versleutelingssleutel intrekken via toegangsbeleid of door de sleutel te verwijderen. Meer informatie over toegangsbeleid en het beveiligen van uw sleutelkluis van [Beveiligde toegang tot een sleutelkluis.](../key-vault/general/security-overview.md)

Zodra de versleutelingssleutel is ingetrokken, Service Bus de service in de versleutelde naamruimte niet meer werkt. Als de toegang tot de sleutel is ingeschakeld of de verwijderde sleutel is hersteld, kiest de Service Bus-service de sleutel zodat u toegang hebt tot de gegevens uit de versleutelde Service Bus-naamruimte.

## <a name="caching-of-keys"></a>Sleutels in deaching
Het Service Bus de vermelde versleutelingssleutels om de vijf minuten te peilen. Deze worden in de cache opgeslagen en gebruikt tot de volgende poll, die na 5 minuten is. Zolang er ten minste één sleutel beschikbaar is, zijn wachtrijen en onderwerpen toegankelijk. Als alle vermelde sleutels niet toegankelijk zijn wanneer deze worden gepeild, zijn alle wachtrijen en onderwerpen niet meer beschikbaar. 

Hier vindt u meer informatie: 

- Om de 5 minuten peilt Service Bus service alle door de klant beheerde sleutels die worden vermeld in de record van de naamruimte:
    - Als een sleutel is geroteerd, wordt de record bijgewerkt met de nieuwe sleutel.
    - Als een sleutel is ingetrokken, wordt de sleutel verwijderd uit de record.
    - Als alle sleutels zijn ingetrokken, wordt de versleutelingsstatus van de naamruimte ingesteld **op Ingetrokken.** De gegevens zijn niet toegankelijk vanuit de Service Bus.. 
    

## <a name="use-resource-manager-template-to-enable-encryption"></a>Een Resource Manager gebruiken om versleuteling in teschakelen
In deze sectie ziet u hoe u de volgende taken uitvoert met behulp **Azure Resource Manager sjablonen.** 

1. Maak een **premium** Service Bus-naamruimte met een **beheerde service-identiteit.**
2. Maak een **sleutelkluis** en verleen de service-id toegang tot de sleutelkluis. 
3. Werk de Service Bus bij met de sleutelkluisgegevens (sleutel/waarde). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Een Premium Service Bus-naamruimte maken met beheerde service-identiteit
In deze sectie ziet u hoe u een Azure Service Bus met beheerde service-identiteit maakt met behulp van een Azure Resource Manager en PowerShell. 

1. Maak een Azure Resource Manager om een naamruimte Service Bus Premium-laag te maken met een beheerde service-identiteit. Noem het bestand: **CreateServiceBusPremiumNamespace.jsop**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
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
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Maak een sjabloonparameterbestand met de **naamCreateServiceBusPremiumNamespaceParams.jsop**. 

    > [!NOTE]
    > Vervang de volgende waarden: 
    > - `<ServiceBusNamespaceName>` - Naam van uw Service Bus naamruimte
    > - `<Location>` - Locatie van uw Service Bus-naamruimte

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. Voer de volgende PowerShell-opdracht uit om de sjabloon te implementeren om een premium Service Bus maken. Haal vervolgens de id van de Service Bus op om deze later te gebruiken. Vervang `{MyRG}` door de naam van de resourcegroep voordat u de opdracht gaat uitvoeren.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Toegang Service Bus naamruimte-identiteit verlenen tot de sleutelkluis

1. Voer de volgende opdracht uit om een sleutelkluis te maken met **beveiliging tegen opsluizen** en **soft-delete** ingeschakeld. 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    (OF)
    
    Voer de volgende opdracht uit om een bestaande **sleutelkluis bij te werken.** Geef waarden op voor resourcegroep- en sleutelkluisnamen voordat u de opdracht gaat uitvoeren. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Stel het toegangsbeleid voor de sleutelkluis in, zodat de beheerde identiteit van de Service Bus-naamruimte toegang heeft tot de sleutelwaarde in de sleutelkluis. Gebruik de id van de Service Bus naamruimte uit de vorige sectie. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Gegevens versleutelen in Service Bus-naamruimte met door de klant beheerde sleutel uit key vault
U hebt tot nu toe de volgende stappen uitgevoerd: 

1. U hebt een Premium-naamruimte gemaakt met een beheerde identiteit.
2. Maak een sleutelkluis en verleende de beheerde identiteit toegang tot de sleutelkluis. 

In deze stap gaat u de naamruimte Service Bus sleutelkluis bijwerken met informatie over de sleutelkluis. 

1. Maak een JSON-bestand **UpdateServiceBusNamespaceWithEncryption.jsmet** de volgende inhoud: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
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
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
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

2. Maak een sjabloonparameterbestand: **UpdateServiceBusNamespaceWithEncryptionParams.jsop**.

    > [!NOTE]
    > Vervang de volgende waarden: 
    > - `<ServiceBusNamespaceName>` - Naam van uw Service Bus-naamruimte
    > - `<Location>` - Locatie van uw Service Bus-naamruimte
    > - `<KeyVaultName>` - Naam van uw sleutelkluis
    > - `<KeyName>` - Naam van de sleutel in de sleutelkluis  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:
- [Service Bus overzicht](service-bus-messaging-overview.md)
- [Key Vault overzicht](../key-vault/general/overview.md)