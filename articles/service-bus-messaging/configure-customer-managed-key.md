---
title: Uw eigen sleutel configureren voor het versleutelen van Azure Service Bus gegevens in rust
description: Dit artikel bevat informatie over het configureren van uw eigen sleutel voor het versleutelen van Azure Service Bus gegevens rest.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: 5d14c8953819575d1c2688520838135efc7121e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100378312"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Door de klant beheerde sleutels configureren voor het versleutelen van Azure Service Bus gegevens op rest door gebruik te maken van de Azure Portal
Azure Service Bus Premium zorgt voor versleuteling van gegevens in rust met Azure Storage-service versleuteling (Azure SSE). Service Bus Premium gebruikt Azure Storage om de gegevens op te slaan. Alle gegevens die zijn opgeslagen met Azure Storage, worden versleuteld met door micro soft beheerde sleutels. Als u uw eigen sleutel gebruikt (ook wel Bring Your Own Key (BYOK) of door de klant beheerde sleutel), worden de gegevens nog steeds versleuteld met behulp van de door micro soft beheerde sleutel, maar wordt de door micro soft beheerde sleutel versleuteld met behulp van de door de klant beheerde sleutel. Met deze functie kunt u de toegang tot door de klant beheerde sleutels maken, draaien, uitschakelen en intrekken die worden gebruikt voor het versleutelen van door micro soft beheerde sleutels. Het inschakelen van de functie BYOK is een eenmalige installatie procedure voor uw naam ruimte.

Er zijn enkele voor behoud van de door de klant beheerde sleutel voor versleuteling aan de service zijde. 
- Deze functie wordt ondersteund door [Azure service bus Premium](service-bus-premium-messaging.md) -laag. Het kan niet worden ingeschakeld voor de standaardlaag Service Bus naam ruimten.
- De versleuteling kan alleen worden ingeschakeld voor nieuwe of lege naam ruimten. Als de naam ruimte alle wacht rijen of onderwerpen bevat, mislukt de versleutelings bewerking.

U kunt Azure Key Vault gebruiken voor het beheren van uw sleutels en het controleren van uw sleutel gebruik. U kunt uw eigen sleutels maken en deze opslaan in een sleutelkluis of u kunt de Azure Key Vault API's gebruiken om sleutels te genereren. Zie [Wat is Azure Key Vault?](../key-vault/general/overview.md) voor meer informatie over Azure Key Vault.

In dit artikel wordt beschreven hoe u een sleutel kluis kunt configureren met door de klant beheerde sleutels met behulp van de Azure Portal. Ga voor meer informatie over het maken van een sleutel kluis met behulp van de Azure Portal naar [Quick Start: een Azure Key Vault maken met behulp van de Azure Portal](../key-vault/general/quick-create-portal.md).

> [!IMPORTANT]
> Voor het gebruik van door de klant beheerde sleutels met Azure Service Bus is vereist dat er twee vereiste eigenschappen zijn geconfigureerd voor de sleutel kluis. Dit zijn:  **voorlopig verwijderen** en **niet opschonen**. Deze eigenschappen zijn standaard ingeschakeld wanneer u een nieuwe sleutel kluis maakt in de Azure Portal. Als u deze eigenschappen echter wilt inschakelen voor een bestaande sleutel kluis, moet u Power shell of Azure CLI gebruiken.

## <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen
Voer de volgende stappen uit om door de klant beheerde sleutels in te scha kelen in de Azure Portal:

1. Navigeer naar uw Service Bus Premium-naam ruimte.
2. Selecteer op de pagina **instellingen** van uw service bus-naam ruimte de optie **versleuteling**.
3. Selecteer de door de **klant beheerde sleutel versleuteling bij rest** , zoals wordt weer gegeven in de volgende afbeelding.

    ![Door de klant beheerde sleutel inschakelen](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Een sleutel kluis met sleutels instellen

Nadat u door de klant beheerde sleutels hebt ingeschakeld, moet u de door de klant beheerde sleutel koppelen aan uw Azure Service Bus naam ruimte. Service Bus ondersteunt alleen Azure Key Vault. Als u de optie **versleuteling met door de klant beheerde sleutel** in de vorige sectie inschakelt, moet u de sleutel in azure Key Vault importeren. De sleutels moeten ook **zacht verwijderen** hebben en **niet leeg** zijn geconfigureerd voor de sleutel. Deze instellingen kunnen worden geconfigureerd met [Power shell](../key-vault/general/key-vault-recovery.md) of [cli](../key-vault/general/key-vault-recovery.md).

1. Als u een nieuwe sleutel kluis wilt maken, volgt u de Azure Key Vault [Snelstartgids](../key-vault/general/overview.md). Zie [over sleutels, geheimen en certificaten](../key-vault/general/about-keys-secrets-certificates.md)voor meer informatie over het importeren van bestaande sleutels.
1. Gebruik de opdracht AZ-sleutel [kluis maken](/cli/azure/keyvault#az-keyvault-create) om zowel zacht verwijderen als beveiliging opschonen in te scha kelen bij het maken van een kluis.

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Gebruik de opdracht AZ-sleutel [kluis bijwerken](/cli/azure/keyvault#az-keyvault-update) om een schone beveiliging toe te voegen aan een bestaande kluis (waarvoor al zacht verwijderen is ingeschakeld).

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Maak sleutels door de volgende stappen uit te voeren:
    1. Als u een nieuwe sleutel wilt maken, selecteert u **genereren/importeren** in het menu **sleutels** onder **instellingen**.
        
        ![Selecteer de knop genereren/importeren](./media/configure-customer-managed-key/select-generate-import.png)

    1. Stel **Opties** in voor het **genereren** en geven van de sleutel een naam.

        ![Een sleutel maken](./media/configure-customer-managed-key/create-key.png) 

    1. U kunt nu deze sleutel selecteren om te koppelen aan de Service Bus naam ruimte voor het versleutelen van de vervolg keuzelijst. 

        ![Selecteer een sleutel in de sleutel kluis](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > U kunt Maxi maal drie sleutels toevoegen voor redundantie. In het geval dat een van de sleutels is verlopen of niet toegankelijk is, worden de andere sleutels gebruikt voor versleuteling.
        
    1. Vul de Details voor de sleutel in en klik op **selecteren**. Hiermee wordt de versleuteling van de door micro soft beheerde sleutel met uw sleutel (door de klant beheerde sleutel) ingeschakeld. 


    > [!IMPORTANT]
    > Als u een door de klant beheerde sleutel en geo-nood herstel wilt gebruiken, raadpleegt u deze sectie. 
    >
    > Voor het inschakelen van versleuteling van door micro soft beheerde sleutels met een door de klant beheerde sleutel, wordt een [toegangs beleid](../key-vault/general/secure-your-key-vault.md) ingesteld voor de service bus beheerde identiteit op de opgegeven Azure-hoofd kluis. Dit zorgt voor gecontroleerde toegang tot de Azure-sleutel kluis vanuit de Azure Service Bus naam ruimte.
    >
    > Vanwege dit:
    > 
    >   * Als [geo-nood herstel](service-bus-geo-dr.md) al is ingeschakeld voor de service bus naam ruimte en u de door de klant beheerde sleutel wilt inschakelen, 
    >     * Koppeling verbreekt
    >     * [Stel het toegangs beleid](../key-vault/general/assign-access-policy-portal.md) voor de beheerde identiteit voor de primaire en secundaire naam ruimten in op de sleutel kluis.
    >     * Stel versleuteling in voor de primaire naam ruimte.
    >     * De primaire en secundaire naam ruimten opnieuw koppelen.
    > 
    >   * Als u geo-DR wilt inschakelen voor een Service Bus naam ruimte waarin de door de klant beheerde sleutel al is ingesteld,
    >     * [Stel het toegangs beleid](../key-vault/general/assign-access-policy-portal.md) voor de beheerde identiteit voor de secundaire naam ruimte in op de sleutel kluis.
    >     * Koppel de primaire en secundaire naam ruimte.


## <a name="rotate-your-encryption-keys"></a>Uw versleutelings sleutels draaien

U kunt de sleutel in de sleutel kluis draaien met behulp van het rotatie mechanisme voor Azure-sleutel kluizen. Activerings-en verloop datums kunnen ook worden ingesteld op het automatiseren van sleutel rotatie. Met de Service Bus-service worden nieuwe sleutel versies gedetecteerd en worden ze automatisch gestart.

## <a name="revoke-access-to-keys"></a>Toegang tot sleutels intrekken

Als u de toegang tot de versleutelings sleutels intrekt, worden de gegevens niet verwijderd uit Service Bus. De gegevens kunnen echter niet worden geopend vanuit de naam ruimte Service Bus. U kunt de versleutelings sleutel intrekken via toegangs beleid of door de sleutel te verwijderen. Meer informatie over toegangs beleid en het beveiligen van uw sleutel kluis van [beveiligde toegang tot een sleutel kluis](../key-vault/general/secure-your-key-vault.md).

Zodra de versleutelings sleutel is ingetrokken, wordt de Service Bus-service op de versleutelde naam ruimte niet meer bruikbaar. Als de toegang tot de sleutel is ingeschakeld of de verwijderde sleutel is hersteld, wordt de sleutel door Service Bus service gekozen, zodat u toegang hebt tot de gegevens van de versleutelde Service Bus naam ruimte.

## <a name="caching-of-keys"></a>Cache van sleutels
Het Service Bus exemplaar controleert elke vijf minuten op de vermelde versleutelings sleutels. Ze worden in de cache opgeslagen en gebruikt tot de volgende poll, die na 5 minuten wordt uitgevoerd. Zolang er ten minste één sleutel beschikbaar is, zijn wacht rijen en onderwerpen toegankelijk. Als alle vermelde sleutels niet toegankelijk zijn tijdens het navragen, worden alle wacht rijen en onderwerpen niet meer beschikbaar. 

Hier vindt u meer informatie: 

- De Service Bus-service vraagt om de vijf minuten alle door de klant beheerde sleutels die worden vermeld in de record van de naam ruimte:
    - Als een sleutel is gedraaid, wordt de record bijgewerkt met de nieuwe sleutel.
    - Als een sleutel is ingetrokken, wordt de sleutel uit de record verwijderd.
    - Als alle sleutels zijn ingetrokken, wordt de versleutelings status van de naam ruimte ingesteld op **ingetrokken**. De gegevens kunnen niet worden geopend vanuit de naam ruimte Service Bus.. 
    

## <a name="use-resource-manager-template-to-enable-encryption"></a>Resource Manager-sjabloon gebruiken om versleuteling in te scha kelen
In deze sectie wordt beschreven hoe u de volgende taken kunt uitvoeren met behulp van **Azure Resource Manager sjablonen**. 

1. Maak een **Premium** -service bus naam ruimte met een **beheerde service-identiteit**.
2. Maak een **sleutel kluis** en verleen de service-identiteit toegang tot de sleutel kluis. 
3. Werk de Service Bus naam ruimte bij met de sleutel kluis gegevens (sleutel/waarde). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Een Premium-Service Bus naam ruimte maken met een beheerde service-identiteit
In deze sectie wordt beschreven hoe u een Azure Service Bus naam ruimte met een beheerde service-identiteit maakt met behulp van een Azure Resource Manager sjabloon en Power shell. 

1. Maak een Azure Resource Manager sjabloon om een Service Bus naam ruimte voor de Premium-laag te maken met een beheerde service-identiteit. Geef het bestand de naam: **CreateServiceBusPremiumNamespace.jsop**: 

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
2. Maak een sjabloon parameter bestand met de naam: **CreateServiceBusPremiumNamespaceParams.jsop**. 

    > [!NOTE]
    > Vervang de volgende waarden: 
    > - `<ServiceBusNamespaceName>` -Naam van de naam ruimte van uw Service Bus
    > - `<Location>` -Locatie van uw Service Bus naam ruimte

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
3. Voer de volgende Power shell-opdracht uit om de sjabloon te implementeren om een Premium-Service Bus naam ruimte te maken. Vervolgens haalt u de ID van de Service Bus naam ruimte op om deze later te gebruiken. Vervang door `{MyRG}` de naam van de resource groep voordat u de opdracht uitvoert.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Identiteit van Service Bus naam ruimte verlenen toegang tot de sleutel kluis

1. Voer de volgende opdracht uit om een sleutel kluis te maken met **opschoon beveiliging** en **zacht verwijderen** ingeschakeld. 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    OF
    
    Voer de volgende opdracht uit om een **bestaande sleutel kluis** bij te werken. Geef waarden op voor de naam van de resource groep en de sleutel kluis voordat u de opdracht uitvoert. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Stel het toegangs beleid voor de sleutel kluis zo in dat de beheerde identiteit van de Service Bus naam ruimte toegang kan krijgen tot de sleutel waarde in de sleutel kluis. Gebruik de ID van de Service Bus naam ruimte uit de vorige sectie. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Gegevens in Service Bus naam ruimte versleutelen met door de klant beheerde sleutel van de sleutel kluis
U hebt de volgende stappen tot nu toe uitgevoerd: 

1. Er is een Premium-naam ruimte met een beheerde identiteit gemaakt.
2. Maak een sleutel kluis en verleend de beheerde identiteit toegang tot de sleutel kluis. 

In deze stap werkt u de Service Bus naam ruimte bij met sleutel kluis gegevens. 

1. Maak een JSON-bestand met de naam **UpdateServiceBusNamespaceWithEncryption.jsop** met de volgende inhoud: 

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

2. Een sjabloon parameter bestand maken: **UpdateServiceBusNamespaceWithEncryptionParams.jsop**.

    > [!NOTE]
    > Vervang de volgende waarden: 
    > - `<ServiceBusNamespaceName>` -Naam van de naam ruimte van uw Service Bus
    > - `<Location>` -Locatie van uw Service Bus naam ruimte
    > - `<KeyVaultName>` -Naam van uw sleutel kluis
    > - `<KeyName>` -De naam van de sleutel in de sleutel kluis  

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
3. Voer de volgende Power shell-opdracht uit om de Resource Manager-sjabloon te implementeren. Vervang door `{MyRG}` de naam van uw resource groep voordat u de opdracht uitvoert. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:
- [Overzicht van Service Bus](service-bus-messaging-overview.md)
- [Overzicht van Key Vault](../key-vault/general/overview.md)