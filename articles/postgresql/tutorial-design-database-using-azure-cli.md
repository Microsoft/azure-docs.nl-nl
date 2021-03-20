---
title: 'Zelfstudie: Een Azure Database for PostgreSQL ontwerpen - Single Server - Azure CLI'
description: Deze zelfstudie laat zien hoe u uw eerste Azure Database for PostgreSQL Single Server kunt maken en configureren en er query's op kunt toepassen met behulp van Azure CLI.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc, devx-track-azurecli
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: 019e6e738ea312b7e6a16c44354c7dcd54e24f2f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93331893"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-azure-cli"></a>Zelfstudie: Een Azure Database for PostgreSQL ontwerpen - Single Server ontwerpen met behulp van Azure CLI 
In deze zelfstudie gebruikt u Azure CLI (Command Line Interface of opdrachtregelinterface in goed Nederlands) en andere hulpprogramma's om deze bewerkingen uit te voeren:
> [!div class="checklist"]
> * Een Azure-database voor PostgreSQL-server maken
> * De serverfirewall configureren
> * Het hulpprogramma [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) gebruiken om een database te maken
> * Voorbeeldgegevens laden
> * Querygegevens
> * Gegevens bijwerken
> * Gegevens terugzetten

U kunt de Azure Cloud Shell gebruiken in de browser of [Azure CLI installeren]( /cli/azure/install-azure-cli) op uw eigen computer om de opdrachten in deze zelfstudie uit te voeren.

## <a name="prerequisites"></a>Vereisten
Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. 

Als u meerdere abonnementen hebt, kiest u het abonnement waarin de resource is opgenomen of wordt gefactureerd. Selecteer een specifiek abonnements-ID in uw account met de opdracht [az account set](/cli/azure/account).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken
Maak een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) met de opdracht [az group create](/cli/azure/group). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam `myresourcegroup` gemaakt op de locatie `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Een Azure-database voor PostgreSQL-server maken
Maak een [Azure-database voor PostgreSQL-server](overview.md) met behulp van de opdracht [az postgres server create](/cli/azure/postgres/server). Een server bevat een groep met databases die worden beheerd als groep. 

In het volgende voorbeeld wordt een server gemaakt met de naam `mydemoserver` in uw resourcegroep `myresourcegroup` met aanmeldgegevens van de serverbeheerder `myadmin`. De naam van een server komt overeen met een DNS-naam en moet dus globaal uniek zijn in Azure. Vervang het `<server_admin_password>` door uw eigen waarde. Dit is een Gen 5-server voor Algemeen gebruik met twee vCores.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```
De parameterwaarde voor de sku-naam volgt de conventie {prijscategorie} \_{compute-generatie}\_{vCores}, zoals in de onderstaande voorbeelden:
+ `--sku-name B_Gen5_2` komt overeen met Basic, Gen 5 en 2 vCores.
+ `--sku-name GP_Gen5_32` komt overeen met Algemeen gebruik, Gen 5 en 32 vCores.
+ `--sku-name MO_Gen5_2` komt overeen met Geoptimaliseerd voor geheugen, Gen 5 en 2 vCores.

Raadpleeg de documentatie over [prijscategorieën](./concepts-pricing-tiers.md) om de geldige waarden per regio en categorie te begrijpen.

> [!IMPORTANT]
> De beheerdersaanmelding bij de server en het wachtwoord die u hier opgeeft, zijn vereist voor aanmelding bij de server en de bijbehorende databases verderop in deze quickstart. Onthoud of noteer deze informatie voor later gebruik.

De database **postgres** wordt standaard gemaakt op uw server. De database [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) is een standaarddatabase die kan worden gebruikt door gebruikers, hulpprogramma's en toepassingen van derden. 


## <a name="configure-a-server-level-firewall-rule"></a>Een serverfirewallregel configureren

Maak een Azure PostgreSQL-firewallregel op serverniveau met de opdracht [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule). Met een firewallregel op serverniveau kan een externe toepassing, zoals [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) of [PgAdmin](https://www.pgadmin.org/) verbinding maken met uw server via de firewall van de Azure PostgreSQL-service. 

U kunt een firewallregel voor een IP-bereik zodat u vanaf uw netwerk verbinding kunt maken. In het volgende voorbeeld wordt [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) gebruikt om een firewallregel `AllowMyIP` te maken die verbinding vanaf één IP-adres toestaat.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

Als u de toegang tot uw Azure PostgreSQL-server wilt beperken tot alleen uw netwerk, kunt u de firewallregel zo instellen dat deze alleen het IP-adresbereik van uw bedrijfsnetwerk dekt.

> [!NOTE]
> De Azure PostgreSQL-server communiceert via poort 5432. Als u verbinding maakt vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 5432 mogelijk niet toegestaan door de firewall van uw netwerk. Vraag aan de IT-afdeling of ze poort 5432 willen openen, zodat u verbinding kunt maken met uw Azure SQL Database-server.
>

## <a name="get-the-connection-information"></a>De verbindingsgegevens ophalen

Als u verbinding met uw server wilt maken, moet u hostgegevens en toegangsreferenties opgeven.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Het resultaat wordt in JSON-indeling weergegeven. Noteer de **aanmeldgegevens van de beheerder** en **fullyQualifiedDomainName**.
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

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>Verbinding maken met een Azure Database for PostgreSQL-database via psql
Als op uw clientcomputer PostgreSQL is geïnstalleerd, kunt u een lokale instantie van [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) of de Azure Cloud Console gebruiken om verbinding te maken met een Azure PostgreSQL-server. U gaat nu het opdrachtregelprogramma psql gebruiken om verbinding te maken met de Azure Database voor PostgreSQL-server.

1. Voer de volgende psql-opdracht uit om verbinding te maken met een Azure Database for PostgreSQL-database:
   ```
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Met de volgende opdracht maakt u bijvoorbeeld verbinding met de standaarddatabase **postgres** op uw PostgreSQL-server **mydemoserver.postgres.database.azure.com** met behulp van toegangsreferenties. Voer het `<server_admin_password>` in dat u koos toen u werd gevraagd om een wachtwoord.
  
   ```
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Als u liever een URL-pad gebruikt om verbinding te maken met Postgres, URL-codeert u het teken @ in de gebruikersnaam met `%40`. De verbindingsreeks voor psql is bijvoorbeeld
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```

2. Wanneer u met de server bent verbonden, maakt u bij de prompt een lege database:
   ```sql
   CREATE DATABASE mypgsqldb;
   ```

3. Voer na de prompt de volgende opdracht uit om verbinding te maken met de zojuist gemaakte database **mypgsqldb**:
   ```sql
   \c mypgsqldb
   ```

## <a name="create-tables-in-the-database"></a>Tabellen maken in de database
U weet nu hoe u verbinding kunt maken met de Azure Database for PostgreSQL en dus is het tijd om enkele eenvoudige taken uit te voeren:

We gaan eerst een tabel maken en hierin enkele gegevens laden. Maak bijvoorbeeld een tabel waarin voorraadgegevens worden bijgehouden:
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

U kunt de zojuist gemaakte tabel nu in de lijst met tabellen zien door het volgende te typen:
```sql
\dt
```

## <a name="load-data-into-the-table"></a>Gegevens laden in de tabel
Voer enkele gegevens in de gemaakte tabel in. Voer bij de opdrachtprompt de volgende query uit om enkele rijen met gegevens in te voegen:
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

U hebt nu twee rijen met voorbeeldgegevens toegevoegd aan de tabel die u eerder hebt gemaakt.

## <a name="query-and-update-the-data-in-the-tables"></a>De gegevens in de tabellen opvragen en bijwerken
Voer de volgende query uit om gegevens op te halen uit de voorraadtabel: 
```sql
SELECT * FROM inventory;
```

U kunt ook de gegevens in de voorraadtabel bijwerken:
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

U kunt de bijgewerkte waarden zien wanneer u de gegevens ophaalt:
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Een database herstellen naar een eerder tijdstip
Stel dat u een tabel per ongeluk hebt verwijderd. Dit is iets wat u niet eenvoudig kunt herstellen. Met Azure Database for PostgreSQL kunt u gegevens herstellen van elk tijdstip waarop een back-up van uw server is gemaakt (welke tijdstippen dat zijn, is afhankelijk van de geconfigureerde bewaarperiode voor back-ups) en dit tijdstip herstellen naar een nieuwe server. U kunt deze nieuwe server dan gebruiken om de verwijderde gegevens te herstellen. 

Met de volgende opdracht wordt de voorbeeldserver hersteld naar een tijdstip voordat de tabel is toegevoegd:
```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mydemoserver
```

De opdracht `az postgres server restore` vereist de volgende parameters:

| Instelling | Voorgestelde waarde | Beschrijving  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  De resourcegroep waarin de bronserver bestaat.  |
| naam | mydemoserver-restored | De naam van de nieuwe server die door de opdracht restore is gemaakt. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Selecteer een bepaald tijdstip om naar te herstellen. Deze datum en tijd moet binnen de back-upretentieperiode van de bronserver vallen. Gebruik ISO8601-notatie voor datum en tijd. U kunt bijvoorbeeld uw eigen lokale tijdzone, zoals `2017-04-13T05:59:00-08:00`, gebruiken of de UTC Zulu-notatie `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | De naam of ID van de bronserver voor het herstellen. |

Wanneer u een server naar een bepaald tijdstip herstelt, wordt er een nieuwe server gemaakt, die een kopie is van de oorspronkelijke server vanaf het opgegeven tijdstip. De locatie en prijscategorie van de herstelde server zijn hetzelfde als die van de bronserver.

De opdracht is synchroon en keert terug nadat de server is hersteld. Nadat het herstel is voltooid, zoekt u de nieuwe server. Controleer of de gegevens zijn hersteld zoals verwacht.

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. Als u deze resources in de toekomst niet nodig denkt te hebben, kunt u de servergroep verwijderen. Druk op de knop *Verwijderen* op de pagina *Overzicht* voor uw servergroep. Wanneer u daarom op een pop-uppagina wordt gevraagd, bevestigt u de naam van de servergroep en klikt u op de laatste knop *Verwijderen*.


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u Azure CLI (Command Line Interface of opdrachtregelinterface) en andere hulpprogramma's gebruikt om deze bewerkingen uit te voeren:
> [!div class="checklist"]
> * Een Azure-database voor PostgreSQL-server maken
> * De serverfirewall configureren
> * Het hulpprogramma **psql** gebruiken om een database te maken
> * Voorbeeldgegevens laden
> * Querygegevens
> * Gegevens bijwerken
> * Gegevens terugzetten

> [!div class="nextstepaction"]
> [Uw eerste Azure Database for PostgreSQL ontwerpen met Azure Portal](tutorial-design-database-using-azure-portal.md)
