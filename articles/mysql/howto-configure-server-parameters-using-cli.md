---
title: Serverparameters configureren - Azure CLI - Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u de serviceparameters in Azure Database for MySQL met behulp van het Azure CLI-opdrachtregelprogramma.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 94dd7f72f6411934587c76850a05d6885e9f6862
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763213"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-cli"></a>Serverparameters configureren in Azure Database for MySQL met behulp van de Azure CLI
U kunt configuratieparameters voor een Azure Database for MySQL-server op een lijst zetten, tonen en bijwerken met behulp van Azure CLI, het Azure-opdrachtregelprogramma. Een subset van engineconfiguraties wordt op serverniveau zichtbaar en kan worden gewijzigd. 

>[!Note]
> Serverparameters kunnen globaal worden bijgewerkt op serverniveau. Gebruik [de Azure CLI,](./howto-configure-server-parameters-using-cli.md) [PowerShell](./howto-configure-server-parameters-using-powershell.md)of [Azure Portal](./howto-server-parameters.md)

## <a name="prerequisites"></a>Vereisten
Als u deze handleiding wilt door nemen, hebt u het volgende nodig:
- [Een Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure](/cli/azure/install-azure-cli) CLI-opdrachtregelprogramma of gebruik de Azure Cloud Shell in de browser.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Serverconfiguratieparameters voor Azure Database for MySQL server
Voer de opdracht [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) uit om alle wijzigbare parameters in een server en hun waarden weer te geven.

U kunt de serverconfiguratieparameters voor de **server** mydemoserver.mysql.database.azure.com onder resourcegroep **myresourcegroup.**
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Zie de sectie MySQL-referentie over serversysteemvariabelen voor de definitie van [elk van de vermelde](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)parameters.

## <a name="show-server-configuration-parameter-details"></a>Details van serverconfiguratieparameters tonen
Voer de opdracht [az mysql server configuration show](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show) uit om details weer te geven over een bepaalde configuratieparameter voor een server.

In dit voorbeeld ziet u details van de **\_ configuratieparameter \_** voor de serverserver voor langzame **query's** mydemoserver.mysql.database.azure.com resourcegroep **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>De waarde van een serverconfiguratieparameter wijzigen
U kunt ook de waarde van een bepaalde serverconfiguratieparameter wijzigen, waarmee de onderliggende configuratiewaarde voor de MySQL-serveren engine wordt bijgewerkt. Gebruik de opdracht [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) om de configuratie bij te werken. 

Als u de **\_ configuratieparameter voor de \_** server met langzame querylogboekservers wilt **bijwerken,** mydemoserver.mysql.database.azure.com onder resourcegroep **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Als u de waarde van een configuratieparameter opnieuw wilt instellen, laat u de optionele parameter weg en past de `--value` service de standaardwaarde toe. In het bovenstaande voorbeeld ziet het er als volgende uit:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Met deze code wordt de configuratie **van het logboek voor langzame \_ \_ query's** opnieuw ingesteld op de standaardwaarde **UIT.** 

## <a name="setting-parameters-not-listed"></a>Parameters instellen die niet worden vermeld
Als de serverparameter die u wilt bijwerken niet wordt vermeld in de Azure Portal, kunt u eventueel de parameter op verbindingsniveau instellen met behulp van `init_connect` . Hiermee stelt u de serverparameters in voor elke client die verbinding maakt met de server. 

Werk de **init connect-serverconfiguratieparameter \_** van **de** server mydemoserver.mysql.database.azure.com onder resourcegroep **myresourcegroup** om waarden in te stellen, zoals tekenset.
```azurecli-interactive
az mysql server configuration set --name init_connect --resource-group myresourcegroup --server mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```

## <a name="working-with-the-time-zone-parameter"></a>Werken met de tijdzoneparameter

### <a name="populating-the-time-zone-tables"></a>De tijdzonetabellen vullen

De tijdzonetabellen op uw server kunnen worden gevuld door de opgeslagen procedure aan te roepen vanuit een hulpprogramma zoals de `mysql.az_load_timezone` MySQL-opdrachtregel of MySQL Workbench.

> [!NOTE]
> Als u de opdracht vanuit `mysql.az_load_timezone` MySQL Workbench gebruikt, moet u mogelijk eerst de veilige updatemodus uitschakelen met `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Start de server opnieuw op om ervoor te zorgen dat de tijdzonetabellen correct zijn ingevuld. Gebruik de Azure Portal of CLI [om de](howto-restart-server-portal.md) server opnieuw op [te starten.](howto-restart-server-cli.md)

Voer de volgende opdracht uit om beschikbare tijdzonewaarden weer te geven:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>De tijdzone op globaal niveau instellen

De tijdzone op globaal niveau kan worden ingesteld met de [opdracht az mysql server configuration set.](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set)

Met de volgende opdracht wordt de configuratieparameter voor de **\_ tijdzoneserver** van de server **mydemoserver.mysql.database.azure.com** resourcegroep **myresourcegroup** bijgewerkt **naar US/Pacific**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>De tijdzone op sessieniveau instellen

De tijdzone op sessieniveau kan worden ingesteld door de opdracht uit te voeren vanuit een hulpprogramma zoals de `SET time_zone` MySQL-opdrachtregel of MySQL Workbench. In het onderstaande voorbeeld wordt de tijdzone naar de **tijdzone VS/Pacific.**  

```sql
SET time_zone = 'US/Pacific';
```

Raadpleeg de MySQL-documentatie voor [datum- en tijdfuncties.](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)


## <a name="next-steps"></a>Volgende stappen

- Serverparameters [configureren in Azure Portal](howto-server-parameters.md)
