---
title: 'Zelfstudie: Een Azure Database for MySQL maken - Azure Resource Manager-sjabloon'
description: In deze zelfstudie wordt uitgelegd hoe u Azure Database for MySQL-serverimplementaties inricht en automatiseert met behulp van een Azure Resource Manager-sjabloon.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/02/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: fd5fa4e09dd3f73aa7a8f044ded04d504bd2f3dd
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705469"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>Zelfstudie: Een Azure Database for MySQL-server inrichten met behulp van een Azure Resource Manager-sjabloon

Met de [Azure Database for MySQL REST API](/rest/api/mysql/) kunnen DevOps-technici de inrichting, configuratie en bewerkingen van beheerde MySQL-servers en -databases in Azure automatiseren en integreren.  Met de API kunnen MySQL-servers en -databases in de Azure Database for MySQL-service worden gemaakt, opgesomd, beheerd en verwijderd.

Azure Resource Manager maakt gebruik van de onderliggende REST API om de Azure-resources te declareren en te programmeren die nodig zijn voor grootschalige implementaties, in lijn met de infrastructuur als codeconcept. De sjabloon maakt een parameter van de Azure-resourcenaam, de SKU, het netwerk, de firewallconfiguratie en de instellingen, zodat deze eenmalig kan worden gemaakt en meerdere malen kan worden gebruikt.  Azure Resource Manager-sjablonen kunnen eenvoudig worden gemaakt met behulp van [Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) of [Visual Studio Code](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=CLI). Ze zijn bedoeld voor het inpakken van toepassingen, standaardisatie en automatisering van de implementatie, geschikt voor integratie in de CI/CD-pijplijn van DevOps.  Als u bijvoorbeeld snel een web-app met Azure Database for MySQL-back-end wilt implementeren, kunt u de end-to-end-implementatie uitvoeren met behulp van deze [snelstartsjabloon](https://azure.microsoft.com/resources/templates/101-webapp-managed-mysql/) uit de GitHub-galerie.

In deze zelfstudie gebruikt u een sjabloon van Azure Resource Manager en andere hulpprogramma's om het volgende te leren:

> [!div class="checklist"]
> * Een Azure Database for MySQL-server met VNet-service-eindpunt maken met behulp van een Azure Resource Manager-sjabloon
> * Het [opdrachtregelprogramma mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) gebruiken om een database te maken
> * Voorbeeldgegevens laden
> * Querygegevens
> * Gegevens bijwerken

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Een Azure Database for MySQL-server met VNet-service-eindpunt maken met behulp van een Azure Resource Manager-sjabloon

Als u een verwijzing van de JSON-sjabloon voor een Azure Database for MySQL-server wilt ophalen, gaat u naar de sjabloonverwijzing [Microsoft.DBforMySQL servers](/azure/templates/microsoft.dbformysql/servers) (Microsoft.DBforMySQL-servers). Hieronder ziet u de JSON-voorbeeldsjabloon die kan worden gebruikt om een nieuwe server met Azure Database for MySQL met een VNet-service-eindpunt te maken.
```json
{
  "apiVersion": "2017-12-01",
  "type": "Microsoft.DBforMySQL/servers",
  "name": "string",
  "location": "string",
  "tags": "string",
  "properties": {
    "version": "string",
    "sslEnforcement": "string",
    "administratorLogin": "string",
    "administratorLoginPassword": "string",
    "storageProfile": {
      "storageMB": "string",
      "backupRetentionDays": "string",
      "geoRedundantBackup": "string"
    }
  },
  "sku": {
    "name": "string",
    "tier": "string",
    "capacity": "string",
    "family": "string"
  },
  "resources": [
    {
      "name": "AllowSubnet",
      "type": "virtualNetworkRules",
      "apiVersion": "2017-12-01",
      "properties": {
        "virtualNetworkSubnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
        "ignoreMissingVnetServiceEndpoint": true
      },
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/servers/', parameters('serverName'))]"
      ]
    }
  ]
}
```
In deze aanvraag moeten de volgende waarden worden aangepast:
+   `name` -Geef de naam van uw MySQL-server op (zonder domeinnaam).
+   `location` -Geef een geldige Azure-datacentrumregio voor uw MySQL-server op. Bijvoorbeeld westus2.
+   `properties/version` -Geef de versie van de te implementeren MySQL-server op. Bijvoorbeeld 5.6 of 5.7.
+   `properties/administratorLogin` -Geef de MySQL-aanmeldgegevens van de beheerder voor de server op. De aanmeldingsnaam van de beheerder mag niet azure_superuser, admin, administrator, root, guest of public zijn.
+   `properties/administratorLoginPassword` -Geef het wachtwoord voor de hierboven opgegeven MySQL-beheerder op.
+   `properties/sslEnforcement` -Geef Ingeschakeld/uitgeschakeld op om sslEnforcement in- of uit te schakelen.
+   `storageProfile/storageMB` -Geef de maximaal ingerichte opslaggrootte die nodig is voor de server op in megabytes. Bijvoorbeeld 5120.
+   `storageProfile/backupRetentionDays` -Geef de gewenste bewaarperiode voor back-ups in dagen op. Bijvoorbeeld 7. 
+   `storageProfile/geoRedundantBackup` -Geef Ingeschakeld/uitgeschakeld op, afhankelijk van Geo-DR-vereisten.
+   `sku/tier` - Geef de laag Basic, GeneralPurpose of MemoryOptimized voor de implementatie op.
+   `sku/capacity` - Geef de vCore-capaciteit op. Mogelijke waarden zijn 2, 4, 8, 16, 32 of 64.
+   `sku/family` - Geef Gen5 op om de hardwaregeneratie voor de serverimplementatie te kiezen.
+   `sku/name` -Geef TierPrefix_family_capacity op. Bijvoorbeeld B_Gen5_1, GP_Gen5_16, MO_Gen5_32. Raadpleeg de documentatie over [prijscategorieën](./concepts-pricing-tiers.md) om de geldige waarden per regio en per laag te begrijpen.
+   `resources/properties/virtualNetworkSubnetId` -Geef de Azure-id van het subnet in VNet op waar de Azure MySQL-server moet worden geplaatst. 
+   `tags(optional)` -Geef optioneel tags op. Dit zijn sleutel-waardeparen zijn die u zou gebruiken om de resources voor facturering en dergelijke te categoriseren.

Als u een Azure Resource Manager-sjabloon wilt maken om Azure Database for MySQL-implementaties voor uw organisatie te automatiseren, wordt aanbevolen te beginnen met het voorbeeld [Azure Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) in Azure GitHub-snelstartgalerie en daarop voort te bouwen. 

Als u niet bekend bent met Azure Resource Manager-sjablonen en u deze wilt proberen, kunt u starten door de volgende stappen te volgen:
+   Kloon of download het voorbeeld [Azure Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) vanuit de Azure-snelstartgalerie.  
+   Wijzig de azuredeploy.parameters.json om de parameterwaarden bij te werken naar uw voorkeur en sla het bestand op. 
+   Gebruik Azure CLI om de Azure MySQL-server te maken met behulp van de volgende opdrachten

U kunt de Azure Cloud Shell gebruiken in de browser of Azure CLI installeren op uw eigen computer om de codeblokken in deze zelfstudie uit te voeren.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

```azurecli-interactive
az login
az group create -n ExampleResourceGroup  -l "West US2"
az deployment group create -g $ ExampleResourceGroup   --template-file $ {templateloc} --parameters $ {parametersloc}
```

## <a name="get-the-connection-information"></a>De verbindingsgegevens ophalen
Als u verbinding met uw server wilt maken, moet u hostgegevens en toegangsreferenties opgeven.
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Het resultaat wordt in JSON-indeling weergegeven. Noteer de **fullyQualifiedDomainName** en de **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus2",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-mysql"></a>Verbinding maken met de server met behulp van mysql
Gebruik het [opdrachtregelprogramma mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) om een verbinding tot stand te brengen met uw Azure Database voor MySQL-server. In dit voorbeeld is dit de opdracht:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Een lege database maken
Zodra u met de server bent verbonden, maakt u een lege database.
```sql
mysql> CREATE DATABASE mysampledb;
```

Voer bij de prompt de volgende opdracht uit om verbinding te maken met de zojuist gemaakte database:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Tabellen maken in de database
U weet nu hoe u verbinding kunt maken met de Azure Database for MySQL-database en dus is het tijd om enkele eenvoudige taken uit te voeren.

We gaan eerst een tabel maken en hierin enkele gegevens laden. Laten we een tabel maken waarin voorraadgegevens worden opgeslagen.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Gegevens laden in de tabellen
De volgende stap bestaat uit het toevoegen van gegevens aan de tabel. Voer bij de opdrachtprompt de volgende query uit om enkele rijen met gegevens in te voegen.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

We hebben nu twee rijen met voorbeeldgegevens in de tabel die u eerder hebt gemaakt.

## <a name="query-and-update-the-data-in-the-tables"></a>De gegevens in de tabellen opvragen en bijwerken
Voer de volgende query uit om gegevens op te halen uit de databasetabel.
```sql
SELECT * FROM inventory;
```

U kunt ook de gegevens in de tabellen bijwerken.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

De rij wordt dan meteen bijgewerkt wanneer u gegevens ophaalt.
```sql
SELECT * FROM inventory;
```

## <a name="clean-up-resources"></a>Resources opschonen

Als de resourcegroep niet meer nodig is, verwijdert u deze. Hierdoor worden ook de resources in de resourcegroep verwijderd.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zoek en selecteer [Resourcegroepen](https://portal.azure.com) in **Azure Portal**.

2. Kies in de lijst met resourcegroepen de naam van uw resourcegroep.

3. Selecteer op de pagina **Overzicht** van uw resourcegroep de optie **Resourcegroep verwijderen**.

4. Typ de naam van de resourcegroep in het bevestigingsvenster. Selecteer vervolgens **Verwijderen**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u het volgende geleerd:
> [!div class="checklist"]
> * Een Azure Database for MySQL-server met VNet-service-eindpunt maken met behulp van een Azure Resource Manager-sjabloon
> * Het opdrachtregelprogramma mysql gebruiken om een database te maken
> * Voorbeeldgegevens laden
> * Querygegevens
> * Gegevens bijwerken

> [!div class="nextstepaction"]
> [Toepassingen koppelen met Azure Database voor MySQL](./howto-connection-string.md)