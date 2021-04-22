---
title: 'Quickstart: Een server maken - Azure CLI - Azure Database for MySQL'
description: In deze Quick Start wordt beschreven hoe u Azure CLI gebruikt om een Azure-database voor MySQL-server in een Azure-resourcegroep te maken.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 07/15/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 63c7fe703a1fbf4cb46532085a33efd74e6a76ef
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875373"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>Quickstart: Een Azure-database voor MySQL-server maken met behulp van Azure CLI

> [!TIP]
> Overweeg het gebruik van de eenvoudigere Azure CLI-opdracht [az mysql up](/cli/azure/mysql#az_mysql_up) (momenteel als preview-versie). Probeer de [quickstart](./quickstart-create-server-up-azure-cli.md).

In deze quickstart wordt beschreven hoe u de [Azure CLI](/cli/azure/get-started-with-azure-cli)-opdrachten in [Azure Cloud Shell](https://shell.azure.com) gebruikt om binnen ongeveer vijf minuten een Azure Database for MySQL-server te maken. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze quickstart is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

 - Selecteer het specifieke abonnement in uw account met de opdracht [az account set](/cli/azure/account). Noteer de **id**-waarde uit de uitvoer van **az login** en gebruik deze als de waarde voor het argument **abonnement** in de opdracht. Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. U kunt al uw abonnementen ophalen met de opdracht [az account list](/cli/azure/account#az_account_list).

   ```azurecli
   az account set --subscription <subscription id>
   ```

## <a name="create-an-azure-database-for-mysql-server"></a>Een Azure-database voor MySQL-server maken
Maak een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) met behulp van de opdracht [az group create](/cli/azure/group) en maak vervolgens de MySQL-server in deze resourcegroep. U moet een unieke naam opgeven. In het volgende voorbeeld wordt een resourcegroep met de naam `myresourcegroup` gemaakt op de locatie `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Als u een Azure-database voor MySQL-server wilt maken, gebruikt u de opdracht [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create). Een server kan meerdere databases bevatten.

```azurecli
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```

Hier volgen de detailgegevens voor bovenstaande argumenten: 

**Instelling** | **Voorbeeldwaarde** | **Beschrijving**
---|---|---
naam | mydemoserver | Voer een unieke naam voor uw Azure Database for MySQL-server in. De servernaam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. en moet 3 tot 63 tekens lang zijn.
resource-group | myResourceGroup | Geef de naam op van de Azure-resourcegroep.
location | westus | De Azure-locatie voor de server.
admin-user | myadmin | De gebruikersnaam voor aanmelding als beheerder. De aanmeldingsnaam van de beheerder mag niet **azure_superuser**, **admin**, **administrator**, **root**, **guest** of **public** zijn.
admin-password | *veilig wachtwoord* | Het wachtwoord van het beheerdersaccount. Dit wachtwoord moet tussen 8 en 128 tekens bevatten. Uw wachtwoord moet tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers en niet-alfanumerieke tekens.
sku-name|GP_Gen5_2|Voer de naam van de prijscategorie en de berekeningsconfiguratie in. Volgt de verkorte notatie voor conventie {prijscategorie} _{compute-generatie}_ {vCores}. Raadpleeg de [prijscategorieën](./concepts-pricing-tiers.md) voor meer informatie.

>[!IMPORTANT] 
>- De standaardversie van MySQL op uw server is 5.7. Momenteel zijn ook versie 5.6 en 8.0 beschikbaar.
>- Als u alle argumenten voor de opdracht **az mysql server create** wilt weergeven, raadpleegt u dit [referentiedocument](/cli/azure/mysql/server#az_mysql_server_create).
>- SSL is standaard ingeschakeld op de server. Zie [SSL-connectiviteit configureren](howto-configure-ssl.md) voor meer informatie over SSL

## <a name="configure-a-server-level-firewall-rule"></a>Een serverfirewallregel configureren 
De nieuwe server die wordt gemaakt, is standaard beveiligd met firewallregels en is niet openbaar toegankelijk. U kunt de firewallregel op uw server configureren met behulp van de opdracht [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule). Hiermee kunt u lokaal verbinding maken met de server.

In het volgende voorbeeld wordt een firewallregel met de naam `AllowMyIP` gemaakt, die verbindingen van een specifiek IP-adres, 192.168.0.1, toestaat. Vervang het IP-adres vanwaar u verbinding wilt maken. U kunt zo nodig een IP-adresbereik gebruiken. Als u niet weet hoe u uw IP-adres kunt opzoeken, gaat u naar [https://whatismyipaddress.com/](https://whatismyipaddress.com/) om uw IP-adres te verkrijgen.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Verbindingen met Azure Database voor MySQL communiceren via poort 3306. Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 3306 mogelijk niet toegestaan. In dat geval kunt u alleen verbinding maken met uw server als uw IT-afdeling poort 3306 openstelt.

## <a name="get-the-connection-information"></a>De verbindingsgegevens ophalen

Als u verbinding met uw server wilt maken, moet u hostgegevens en toegangsreferenties opgeven.

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Het resultaat wordt in JSON-indeling weergegeven. Noteer de **fullyQualifiedDomainName** en de **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus",
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

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Verbinding maken met Azure Database for MySQL-server met behulp van de mysql-opdrachtregelclient
U kunt verbinding maken met uw server met behulp van een populair clienthulpprogramma, het opdrachtregelprogramma **[mysql. exe](https://dev.mysql.com/downloads/)** bij [Azure Cloud Shell](../cloud-shell/overview.md). U kunt de mysql-opdrachtregel ook gebruiken in uw lokale omgeving.
```bash
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="clean-up-resources"></a>Resources opschonen
Als u deze resources niet voor een andere Quickstart of zelfstudie nodig hebt, kunt u ze verwijderen met de volgende opdracht: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Als u alleen de zojuist gemaakte server wilt verwijderen, kunt u de opdracht [az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete) uitvoeren.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Een PHP-app bouwen in Windows met MySQL](../app-service/tutorial-php-mysql-app.md)