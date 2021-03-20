---
title: Gegevens versleuteling-Azure CLI-voor Azure Database for PostgreSQL-één server
description: Meer informatie over het instellen en beheren van gegevens versleuteling voor uw Azure Database for PostgreSQL één server met behulp van de Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 757782e8842fbcaca9c8d95ec8086dd5791a817b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93240610"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-cli"></a>Gegevens versleuteling voor Azure Database for PostgreSQL Eén server met behulp van de Azure CLI

Meer informatie over het gebruik van de Azure CLI voor het instellen en beheren van gegevens versleuteling voor uw Azure Database for PostgreSQL één server.

## <a name="prerequisites-for-azure-cli"></a>Vereisten voor Azure CLI

* U moet een Azure-abonnement hebben en een beheerder van dat abonnement zijn.
* Maak een sleutel kluis en een sleutel die moet worden gebruikt voor een door de klant beheerde sleutel. Schakel ook leegmaken van beveiliging en zacht verwijderen in op de sleutel kluis.

   ```azurecli-interactive
   az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
   ```

* In de gemaakte Azure Key Vault maakt u de sleutel die wordt gebruikt voor het versleutelen van gegevens van de Azure Database for PostgreSQL enkele server.

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

* De sleutel moet de volgende kenmerken hebben om te kunnen worden gebruikt als een door de klant beheerde sleutel:
  * Geen vervaldatum
  * Niet uitgeschakeld
  * **Get**-, **Terugloop** -en **Unwrap** -bewerkingen uitvoeren

## <a name="set-the-right-permissions-for-key-operations"></a>De juiste machtigingen voor sleutel bewerkingen instellen

1. Er zijn twee manieren om de beheerde identiteit voor uw Azure Database for PostgreSQL één server op te halen.

    ### <a name="create-an-new-azure-database-for-postgresql-server-with-a-managed-identity"></a>Maak een nieuwe Azure Database for PostgreSQL-server met een beheerde identiteit.

    ```azurecli-interactive
    az postgres server create --name <server_name> -g <resource_group> --location <location> --storage-size <size>  -u <user> -p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled> --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-postgresql-server-to-get-a-managed-identity"></a>Een bestaande Azure Database for PostgreSQL-server bijwerken om een beheerde identiteit op te halen.

    ```azurecli-interactive
    az postgres server update --resource-group <resource_group> --name <server_name> --assign-identity
    ```

2. Stel de **sleutel machtigingen** (**Get**, **wrap**, **dewrap**) in voor de **Principal**. Dit is de naam van de postgresql één server server.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Gegevens versleuteling instellen voor Azure Database for PostgreSQL één server

1. Schakel gegevens versleuteling in voor de Azure Database for PostgreSQL één server met behulp van de sleutel die in de Azure Key Vault is gemaakt.

    ```azurecli-interactive
    az postgres server key create --name <server_name> -g <resource_group> --kid <key_url>
    ```

    Sleutel-URL:  `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Gegevens versleuteling gebruiken voor herstel-of replica servers

Nadat Azure Database for PostgreSQL één server is versleuteld met een door de klant beheerde sleutel die is opgeslagen in Key Vault, wordt een nieuw gemaakt exemplaar van de server ook versleuteld. U kunt deze nieuwe kopie maken via een lokale of geo-herstel bewerking, of via een replica (lokale/Kruis regio). U kunt voor een versleutelde PostgreSQL één server server de volgende stappen gebruiken om een versleutelde herstelde server te maken.

### <a name="creating-a-restoredreplica-server"></a>Een herstelde/replica-server maken

* [Een herstel server maken](howto-restore-server-cli.md)
* [Een replica-server voor lezen maken](howto-read-replicas-cli.md)

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>Nadat de server is hersteld, moet u de gegevens versleuteling van de herstelde server opnieuw valideren

*   Identiteit voor de replica server toewijzen
```azurecli-interactive
az postgres server update --name  <server name>  -g <resoure_group> --assign-identity
```

*   De bestaande sleutel ophalen die moet worden gebruikt voor de herstelde/replica-server

```azurecli-interactive
az postgres server key list --name  '<server_name>'  -g '<resource_group_name>'
```

*   Stel het beleid voor de nieuwe identiteit voor de herstelde/replica-server in

```azurecli-interactive
az keyvault set-policy --name <keyvault> -g <resoure_group> --key-permissions get unwrapKey wrapKey --object-id <principl id of the server returned by the step 1>
```

* De herstelde/replica server opnieuw valideren met de versleutelings sleutel

```azurecli-interactive
az postgres server key create –name  <server name> -g <resource_group> --kid <key url>
```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-postgresql-single-server"></a>Extra mogelijkheid voor de sleutel die wordt gebruikt voor de Azure Database for PostgreSQL één server

### <a name="get-the-key-used"></a>De gebruikte sleutel ophalen

```azurecli-interactive
az postgres server key show --name <server name>  -g <resource_group> --kid <key url>
```

Sleutel-URL: `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

### <a name="list-the-key-used"></a>De sleutel weer geven die wordt gebruikt

```azurecli-interactive
az postgres server key list --name  <server name>  -g <resource_group>
```

### <a name="drop-the-key-being-used"></a>De sleutel die wordt gebruikt, verwijderen

```azurecli-interactive
az postgres server key delete -g <resource_group> --kid <key url> 
```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Een Azure Resource Manager sjabloon gebruiken om gegevens versleuteling in te scha kelen

Naast Azure Portal, kunt u ook gegevens versleuteling inschakelen op uw Azure Database for PostgreSQL enkele server met behulp van Azure Resource Manager-sjablonen voor nieuwe en bestaande server.

### <a name="for-a-new-server"></a>Voor een nieuwe server

Gebruik een van de vooraf gemaakte Azure Resource Manager sjablonen om de server in te richten met gegevens versleuteling ingeschakeld: [voor beeld met gegevens versleuteling](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Met deze Azure Resource Manager sjabloon maakt u een Azure Database for PostgreSQL enkele server en gebruikt u de **sleutel** **kluis** en code die zijn door gegeven als para meters om gegevens versleuteling op de server in te scha kelen.

### <a name="for-an-existing-server"></a>Voor een bestaande server
Daarnaast kunt u Azure Resource Manager sjablonen gebruiken om gegevens versleuteling in te scha kelen op uw bestaande Azure Database for PostgreSQL enkele servers.

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
      "type": "Microsoft.DBforPostgreSQL/servers",
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
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
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
                    "objectId": "[reference(resourceId('Microsoft.DBforPostgreSQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
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
      "type": "Microsoft.DBforPostgreSQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
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

 Zie [Azure database for PostgreSQL gegevens versleuteling met één server met door de klant beheerde sleutel](concepts-data-encryption-postgresql.md)voor meer informatie over gegevens versleuteling.
