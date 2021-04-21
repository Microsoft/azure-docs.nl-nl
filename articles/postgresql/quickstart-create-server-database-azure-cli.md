---
title: 'Quickstart: Server maken - Azure CLI - Azure Database for PostgreSQL - Eén server'
description: In deze quickstart gaat u een Azure Database for PostgreSQL-server maken met behulp van Azure CLI.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a595d677cf0964083526cb7e2c73471148be0fd4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778414"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-cli"></a>Quickstart: Een Azure Database for PostgreSQL-server maken met behulp van Azure CLI

In deze quickstart wordt beschreven hoe u [Azure CLI](/cli/azure/get-started-with-azure-cli)-opdrachten in [Azure Cloud Shell](https://shell.azure.com) gebruikt om binnen ongeveer vijf minuten één Azure Database for PostgreSQL-server te maken. Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

    > [!TIP]
    >  Overweeg het gebruik van de eenvoudigere Azure CLI-opdracht [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up), die momenteel als preview-versie beschikbaar is. Probeer de [quickstart](./quickstart-create-server-up-azure-cli.md).

- Selecteer de specifieke abonnements-id in uw account met de opdracht [az account set](/cli/azure/account).

    - Noteer de **id**-waarde uit de uitvoer van **az login** en gebruik deze als de waarde voor het argument **abonnement** in de opdracht. 

        ```azurecli
        az account set --subscription <subscription id>
        ```

    - Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. U kunt al uw abonnementen ophalen met de opdracht [az account list](/cli/azure/account#az_account_list).

## <a name="create-an-azure-database-for-postgresql-server"></a>Een Azure-database voor PostgreSQL-server maken

Maak een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) met behulp van de opdracht [az group create](/cli/azure/group#az_group_create) en maak vervolgens de PostgreSQL-server in deze resourcegroep. U moet een unieke naam opgeven. In het volgende voorbeeld wordt een resourcegroep met de naam `myresourcegroup` gemaakt op de locatie `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Maak een [Azure Database for PostgreSQL-server](overview.md) met behulp van de opdracht [az postgres server create](/cli/azure/postgres/server). Een server kan meerdere databases bevatten.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
Dit zijn de gegevens van de voorgaande argumenten: 

**Instelling** | **Voorbeeldwaarde** | **Beschrijving**
---|---|---
naam | mydemoserver | Een unieke naam ter identificatie van uw Azure Database for PostgreSQL-server. De servernaam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. De naam moet 3 tot 63 tekens bevatten. Zie [Naamgevingsregels voor Azure Database for PostgreSQL](../azure-resource-manager/management/resource-name-rules.md#microsoftdbforpostgresql) voor meer informatie.
resource-group | myResourceGroup | De naam van de Azure-resourcegroep.
location | westus | De Azure-locatie voor de server.
admin-user | myadmin | De gebruikersnaam voor aanmelding als beheerder. Deze gebruikersnaam mag niet **azure_superuser**, **admin**, **administrator**, **root**, **guest** of **public** zijn.
admin-password | *veilig wachtwoord* | Het wachtwoord van de beheerder. Het moet 8 tot 128 tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers en niet-alfanumerieke tekens.
sku-name|GP_Gen5_2| De naam van de prijscategorie en de berekeningsconfiguratie. Volg de verkorte notatie voor de conventie {prijscategorie} _{compute-generatie}_ {vCores}. Zie [Prijzen voor Azure Database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/) voor meer informatie.

>[!IMPORTANT] 
>- De standaardversie van PostgreSQL op uw server is 9.6. Zie [Supported PostgreSQL major versions](./concepts-supported-versions.md) (Primaire ondersteunde versies van PostgreSQL) voor een overzicht van alle ondersteunde versies.
>- Als u alle argumenten voor de opdracht **az postgres server create** wilt weergeven, raadpleegt u [dit referentiedocument](/cli/azure/postgres/server#az_postgres_server_create).
>- SSL is standaard ingeschakeld op de server. Zie [SSL-connectiviteit configureren](./concepts-ssl-connection-security.md) voor meer informatie over SSL.

## <a name="configure-a-server-level-firewall-rule"></a>Een serverfirewallregel configureren 
De door u gemaakte server is standaard beveiligd met firewallregels en is niet openbaar toegankelijk. U kunt de firewallregels op uw server configureren met behulp van de opdracht [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule), zodat uw lokale omgeving toegang tot de server krijgt en verbinding kan maken. 

In het volgende voorbeeld wordt een firewallregel met de naam `AllowMyIP` gemaakt, die verbindingen van een specifiek IP-adres, 192.168.0.1, toestaat. Vervang dit adres door het IP-adres of de reeks IP-adressen die overeenkomen met het IP-adres waarmee u verbinding gaat maken. Als u uw IP-adres niet weet, gaat u naar [WhatIsMyIPAddress.com](https://whatismyipaddress.com/) om het te zoeken.


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Zorg ervoor dat de firewall van uw netwerk poort 5432 toestaat om verbindingsproblemen te voorkomen. Azure Database for PostgreSQL-servers gebruiken die poort. 

## <a name="get-the-connection-information"></a>De verbindingsgegevens ophalen

Als u verbinding met uw server wilt maken, geeft u hostgegevens en toegangsreferenties op.

```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Het resultaat wordt in JSON-indeling weergegeven. Noteer de waarden voor **administratorLogin** en **fullyQualifiedDomainName**.

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-the-azure-database-for-postgresql-server-by-using-psql"></a>Verbinding maken met de Azure Database for PostgreSQL-server met behulp van psql
De [psql](https://www.postgresql.org/docs/current/static/app-psql.html)-client is een populaire keuze voor het verbinding maken met PostgreSQL-servers. U kunt verbinding met uw server maken door [psql](../cloud-shell/overview.md) met Azure Cloud Shell te gebruiken. U kunt psql ook in uw lokale omgeving gebruiken, indien beschikbaar. Er wordt automatisch een lege database, **postgres**, gemaakt met een nieuwe PostgreSQL-server. U kunt deze database gebruiken om verbinding te maken met psql, zoals wordt weergegeven in de volgende code. 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

> [!TIP]
> Als u liever een URL-pad gebruikt om verbinding te maken met Postgres, URL-codeert u het teken @ in de gebruikersnaam met `%40`. De verbindingsreeks voor psql zou bijvoorbeeld zijn:
>
> ```
> psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
> ```


## <a name="clean-up-resources"></a>Resources opschonen
Als u deze resources niet voor een andere quickstart of zelfstudie nodig hebt, kunt u ze verwijderen met de volgende opdracht. 

```azurecli-interactive
az group delete --name myresourcegroup
```

Als u alleen de zojuist gemaakte server wilt verwijderen, kunt u de opdracht [az postgres server delete](/cli/azure/postgres/server) uitvoeren.

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Een database migreren met behulp van exporteren en importeren](./howto-migrate-using-export-and-import.md)
