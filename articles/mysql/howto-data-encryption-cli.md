---
title: Gegevens versleuteling-Azure CLI-Azure Database for MySQL
description: Meer informatie over het instellen en beheren van gegevens versleuteling voor uw Azure Database for MySQL met behulp van de Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6d9abc67035b4581a028d8e59ef080b4f1ffa5b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96519039"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-cli"></a>Gegevens versleuteling voor Azure Database for MySQL met behulp van de Azure CLI

Meer informatie over het gebruik van de Azure CLI voor het instellen en beheren van gegevens versleuteling voor uw Azure Database for MySQL.

## <a name="prerequisites-for-azure-cli"></a>Vereisten voor Azure CLI

* U moet een Azure-abonnement hebben en een beheerder van dat abonnement zijn.
* Maak een sleutel kluis en een sleutel die moet worden gebruikt voor een door de klant beheerde sleutel. Schakel ook leegmaken van beveiliging en zacht verwijderen in op de sleutel kluis.

  ```azurecli-interactive
  az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
  ```

* In de gemaakte Azure Key Vault maakt u de sleutel die wordt gebruikt voor de gegevens versleuteling van de Azure Database for MySQL.

  ```azurecli-interactive
  az keyvault key create --name <key_name> -p software --vault-name <vault_name>
  ```

* Als u een bestaande sleutel kluis wilt gebruiken, moet deze beschikken over de volgende eigenschappen die moeten worden gebruikt als een door de klant beheerde sleutel:

  * [Voorlopig verwijderen](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Beveiligd leegmaken](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```
  * De Bewaar dagen zijn ingesteld op 90 dagen
  ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --retention-days 90
    ```

* De sleutel moet de volgende kenmerken hebben om te kunnen worden gebruikt als een door de klant beheerde sleutel:
  * Geen vervaldatum
  * Niet uitgeschakeld
  * **Get**-, **wrap**-en **Unwrap** -bewerkingen uitvoeren
  * kenmerk recoverylevel is ingesteld op **hersteld** (hiervoor moet voorlopig verwijderen zijn ingeschakeld met de Bewaar periode ingesteld op 90 dagen)
  * Beveiliging tegen leegmaken is ingeschakeld

U kunt de bovenstaande kenmerken van de sleutel controleren met behulp van de volgende opdracht:

```azurecli-interactive
az keyvault key show --vault-name <key_vault_name> -n <key_name>
```

## <a name="set-the-right-permissions-for-key-operations"></a>De juiste machtigingen voor sleutel bewerkingen instellen

1. Er zijn twee manieren om de beheerde identiteit voor uw Azure Database for MySQL te verkrijgen.

   ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>Maak een nieuwe Azure Database for MySQL-server met een beheerde identiteit.

   ```azurecli-interactive
   az mysql server create --name -g <resource_group> --location <locations> --storage-size size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> -geo-redundant-backup <Enabled/Disabled>  --assign-identity
   ```

   ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>Een bestaande Azure Database for MySQL-server bijwerken om een beheerde identiteit op te halen.

   ```azurecli-interactive
   az mysql server update --name  <server name>  -g <resource_group> --assign-identity
   ```

2. Stel de **sleutel machtigingen** (**Get**, **wrap**, **dewrap**) in voor de **Principal**. Dit is de naam van de mysql-server.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Gegevens versleuteling instellen voor Azure Database for MySQL

1. Schakel gegevens versleuteling in voor de Azure Database for MySQL met behulp van de sleutel die in de Azure Key Vault is gemaakt.

    ```azurecli-interactive
    az mysql server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    Sleutel-URL:  `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Gegevens versleuteling gebruiken voor herstel-of replica servers

Nadat Azure Database for MySQL is versleuteld met een door de klant beheerde sleutel die is opgeslagen in Key Vault, wordt een nieuw gemaakt exemplaar van de server ook versleuteld. U kunt deze nieuwe kopie maken via een lokale of geo-herstel bewerking, of via een replica (lokale/Kruis regio). Voor een versleutelde MySQL-server kunt u dus de volgende stappen gebruiken om een versleutelde herstelde server te maken.

### <a name="creating-a-restoredreplica-server"></a>Een herstelde/replica-server maken

* [Een herstel server maken](howto-restore-server-cli.md) 
* [Een replica-server voor lezen maken](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>Nadat de server is hersteld, moet u de gegevens versleuteling van de herstelde server opnieuw valideren

*   Identiteit voor de replica server toewijzen
```azurecli-interactive
az mysql server update --name  <server name>  -g <resoure_group> --assign-identity
```

*   De bestaande sleutel ophalen die moet worden gebruikt voor de herstelde/replica-server

```azurecli-interactive
az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
```

*   Stel het beleid voor de nieuwe identiteit voor de herstelde/replica-server in
  
```azurecli-interactive
az keyvault set-policy --name <keyvault> -g <resoure_group> --key-permissions get unwrapKey wrapKey --object-id <principl id of the server returned by the step 1>
```

* De herstelde/replica server opnieuw valideren met de versleutelings sleutel

```azurecli-interactive
az mysql server key create –name  <server name> -g <resource_group> --kid <key url>
```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-mysql"></a>Aanvullende functionaliteit voor de sleutel die wordt gebruikt voor de Azure Database for MySQL

### <a name="get-the-key-used"></a>De gebruikte sleutel ophalen

```azurecli-interactive
az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
```

Sleutel-URL: `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

### <a name="list-the-key-used"></a>De sleutel weer geven die wordt gebruikt

```azurecli-interactive
az mysql server key list --name  <server name>  -g <resource_group>
```

### <a name="drop-the-key-being-used"></a>De sleutel die wordt gebruikt, verwijderen

```azurecli-interactive
az mysql server key delete -g <resource_group> --kid <key url>
```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Een Azure Resource Manager sjabloon gebruiken om gegevens versleuteling in te scha kelen

Naast de Azure Portal, kunt u ook gegevens versleuteling op uw Azure Database for MySQL-server inschakelen met behulp van Azure Resource Manager-sjablonen voor nieuwe en bestaande servers.

### <a name="for-a-new-server"></a>Voor een nieuwe server

Gebruik een van de vooraf gemaakte Azure Resource Manager sjablonen om de server in te richten met gegevens versleuteling ingeschakeld: [voor beeld met gegevens versleuteling](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

Met deze Azure Resource Manager sjabloon maakt u een Azure Database for MySQL-server en gebruikt u de sleutel **kluis** en **code** die zijn door gegeven als para meters om gegevens versleuteling op de server in te scha kelen.

### <a name="for-an-existing-server"></a>Voor een bestaande server

Daarnaast kunt u Azure Resource Manager sjablonen gebruiken om gegevens versleuteling in te scha kelen op uw bestaande Azure Database for MySQL-servers.

* Geef de resource-ID van de Azure Key Vault sleutel die u eerder hebt gekopieerd `Uri` , onder de eigenschap in het object Properties door.

* Gebruik *2020-01-01-preview* als de API-versie.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}

```

## <a name="next-steps"></a>Volgende stappen

 Zie [Azure database for MySQL Data Encryption with door de klant beheerde sleutel](concepts-data-encryption-mysql.md)voor meer informatie over gegevens versleuteling.
