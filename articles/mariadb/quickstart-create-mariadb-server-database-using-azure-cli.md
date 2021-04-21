---
title: 'Quickstart: Een server maken - Azure CLI - Azure Database for MariaDB'
description: In deze snelstartgids wordt beschreven hoe u met Azure CLI een Azure Database for MariaDB-server maakt in een Azure-resourcegroep.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 042c70fdd08a6de2b97c4560eb2b6a24eec0bb34
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789965"
---
# <a name="quickstart-create-an-azure-database-for-mariadb-server-by-using-the-azure-cli"></a>Quickstart: Een Azure Database for MariaDB-server maken met behulp van de Azure CLI

U kunt de Azure CLI gebruiken voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. In deze snelstartgids wordt beschreven hoe u Azure CLI gebruikt om binnen ongeveer vijf minuten een Azure Database for MariaDB-server te maken in een Azure-resourcegroep.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

Als u meerdere abonnementen hebt, kiest u het abonnement dat de resource bevat of het abonnement waarvoor u wordt gefactureerd. Selecteer als volgt een specifieke abonnements-id in uw account met de opdracht [az account set](/cli/azure/account#az_account_set):

```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) met de opdracht [az group create](/cli/azure/group#az_group_create). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam `myresourcegroup` gemaakt op de locatie `westus`:

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Een Azure Database for MariaDB-server maken

U maakt een Azure Database for MariaDB-server met de opdracht [az mariadb server create](/cli/azure/mariadb/server#az_mariadb_server_create). Een server kan meerdere databases beheren. Een aparte database wordt doorgaans gebruikt voor elk project of voor elke gebruiker.

Instelling | Voorbeeldwaarde | Beschrijving
---|---|---
naam | **mydemoserver** | Voer een unieke naam in ter identificatie van de Azure Database for MariaDB-server. De servernaam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. Dit wachtwoord moet tussen 3 en 63 tekens bevatten.
resource-group | **myresourcegroup** | Voer de naam van de Azure-resourcegroep in.
sku-name | **GP_Gen5_2** | De naam van de SKU. Volgt de verkorte notatie voor conventie *{prijscategorie}* \_ *{compute-bewerking}* \_ *{vCores}* . Zie de sectie onder deze tabel voor meer informatie over de parameter **sku-name**.
backup-retention | **7** | Hoe lang een back-up moet worden bewaard. De eenheid is dagen. Bereik: 7 tot 35. 
geo-redundant-backup | **Uitgeschakeld** | Of geografisch redundante back-ups moeten worden ingeschakeld voor deze server. Toegestane waarden: **Ingeschakeld**, **Uitgeschakeld**.
location | **westus** | De Azure-locatie voor de server.
ssl-enforcement | **Ingeschakeld** | Of SSL moet worden ingeschakeld voor deze server. Toegestane waarden: **Ingeschakeld**, **Uitgeschakeld**.
storage-size | **51200** | De opslagcapaciteit van de server (de eenheid is MB). Geldige opslagruimten zijn 5.120 MB (minimaal) met verhogingen in stappen van 1024 MB. Zie [Prijscategorieën](./concepts-pricing-tiers.md) voor meer informatie over de opslaglimieten. 
versie | **10.2** | De versie van de hoofd-engine van MariaDB.
admin-user | **myadmin** | De gebruikersnaam voor aanmelding als beheerder. De parameter voor **admin-user** kan niet **azure_superuser**, **admin**, **administrator**, **root**, **guest** of **public** zijn.
admin-password | *Uw wachtwoord* | Het wachtwoord van het beheerdersaccount. Uw wachtwoord moet tussen 8 en 128 tekens bevatten. Het moet tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers en niet-alfanumerieke tekens.

De parameterwaarde voor de sku-naam volgt de conventie {prijscategorie} \_{compute-generatie}\_{vCores}, zoals in de onderstaande voorbeelden:
+ `--sku-name B_Gen5_1` komt overeen met Basic, Gen 5 en 1 vCore. Deze optie is de kleinst beschikbare SKU.
+ `--sku-name GP_Gen5_32` komt overeen met Algemeen gebruik, Gen 5 en 32 vCores.
+ `--sku-name MO_Gen5_2` komt overeen met Geoptimaliseerd voor geheugen, Gen 5 en 2 vCores.

Zie [Prijscategorieën](./concepts-pricing-tiers.md) voor meer informatie over geldige waarden per regio en voor de categorieën.

In het volgende voorbeeld wordt een server met de naam **mydemoserver** gemaakt in de regio VS - west. De server bevindt zich in de resourcegroep **myresourcegroup** en heeft **myadmin** als aanmeldgegevens van de serverbeheerder. De server is een Gen 5-server in de prijscategorie Algemeen gebruik en heeft 2 vCores. Een servernaam wordt toegewezen aan een DNS-naam en moet wereldwijd uniek zijn in Azure. Vervang `<server_admin_password>` door uw eigen serverbeheerderswachtwoord.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

> [!NOTE]
> Overweeg het gebruik van de prijscategorie Basic als lichte reken- en I/O-capaciteit voldoende is voor uw workload. Servers die zijn gemaakt in de prijscategorie Basic kunnen later niet meer worden geschaald voor Algemeen gebruik of Geoptimaliseerd voor geheugen. Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/mariadb/) voor meer informatie.

## <a name="configure-a-firewall-rule"></a>Een firewallregel configureren

U kunt met de opdracht [az mariadb server firewall rule create](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_create) een firewallregel op serverniveau maken voor een Azure Database for MariaDB-server. Een firewallregel op serverniveau stelt een externe toepassing, zoals het opdrachtregelprogramma mysql of MySQL Workbench, in staat om verbinding te maken met uw server via de Azure Database for MariaDB-servicefirewall.

In het volgende voorbeeld wordt een firewallregel met de naam `AllowMyIP` gemaakt, die verbindingen van een specifiek IP-adres, 192.168.0.1, toestaat. Vervang dit door een IP-adres of een bereik van IP-adressen dat overeenkomt met de locatie waaruit u verbinding maakt.

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Verbindingen met Azure Database for MariaDB communiceren via poort 3306. Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 3306 mogelijk niet toegestaan. In dat geval kunt u alleen verbinding maken met uw server als uw IT-afdeling poort 3306 openstelt.

## <a name="configure-ssl-settings"></a>SSL-instellingen configureren

Standaard worden SSL-verbindingen tussen uw server en clienttoepassingen afgedwongen. Deze standaardinstelling zorgt voor beveiliging van gegevens 'in beweging' door de gegevensstroom via internet te versleutelen. Voor deze snelstart schakelen we SSL-verbindingen uit voor uw server. Dit wordt afgeraden voor productieservers. Zie [Configure SSL connectivity in your application to securely connect to Azure Database for MariaDB](./howto-configure-ssl.md) (SSL-connectiviteit in uw toepassing configureren om veilig verbinding te maken met Azure Database for MariaDB) voor meer informatie.

In het volgende voorbeeld wordt het afdwingen van SSL op uw Azure Database for MariaDB-server uitgeschakeld:

```azurecli-interactive
az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
```

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen

Als u verbinding met uw server wilt maken, moet u hostgegevens en toegangsreferenties opgeven. Voer de volgende opdracht uit om de verbindingsgegevens op te halen:

```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

Het resultaat wordt in JSON-indeling weergegeven. Noteer de waarden voor **fullyQualifiedDomainName** en de **administratorLogin**.

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
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
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-the-server-by-using-the-mysql-command-line-tool"></a>Verbinding maken met de server met het opdrachtregelprogramma mysql

Maak verbinding met de server met behulp van het opdrachtregelprogramma mysql. U kunt het opdrachtregelprogramma [downloaden](https://dev.mysql.com/downloads/) en vervolgens op uw computer installeren. U kunt het opdrachtregelhulpprogramma ook openen door de knop **Probeer het nu** in een codevoorbeeld in dit artikel te selecteren. U kunt ook toegang krijgen tot het opdrachtregelhulpprogramma door de knop **> _** in de werkbalk rechtsboven in de Azure-portal te selecteren om **Azure Cloud Shell** te openen.

Maak als volgt verbinding met de server met het opdrachtregelprogramma mysql:

1. Verbinding maken met de server:

   ```azurecli-interactive
   mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   ```

2. De serverstatus bekijken bij de prompt van `mysql>`:

   ```sql
   status
   ```

   De uitvoer moet er ongeveer uitzien als de volgende tekst:

   ```cmd
   C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   Enter password: ***********
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 65512
   Server version: 5.6.39.0 MariaDB Server

   Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.

   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

   mysql> status
   --------------
   mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64)

   Connection id:          64681
   Current database:
   Current user:           myadmin@40.118.201.21
   SSL:                    Cipher in use is AES256-SHA
   Current pager:          stdout
   Using outfile:          ''
   Using delimiter:        ;
   Server version:         5.6.39.0 MariaDB Server
   Protocol version:       10
   Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
   Server characterset:    latin1
   Db     characterset:    latin1
   Client characterset:    utf8
   Conn.  characterset:    utf8
   TCP port:               3306
   Uptime:                 1 day 3 hours 28 min 50 sec

   Threads: 10  Questions: 29002  Slow queries: 0  Opens: 33  Flush tables: 3  Open tables: 1  Queries per second avg: 0.293
   --------------

   mysql>
   ```

> [!TIP]
> Zie [hoofdstuk 4.5.1 in de Engelstalige naslaghandleiding van MySQL 5.7](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) voor aanvullende opdrachten.

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>Verbinding maken met de server met MySQL Workbench

1. Open MySQL Workbench op uw clientcomputer. Als dit nog niet is geïnstalleerd, [downloadt](https://dev.mysql.com/downloads/workbench/) en installeert u de toepassing.

2. Voer in het dialoogvenster **Nieuwe verbinding instellen** de volgende gegevens in op het tabblad **Parameters**:

   ![Een nieuwe verbinding instellen](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

   | Instelling | Voorgestelde waarde | Beschrijving |
   |---|---|---|
   | Verbindingsnaam | **Demoverbinding** | Geef een label op voor deze verbinding (de verbindingsnaam kan van alles zijn) |
   | Verbindingsmethode | **Standard (TCP/IP)** | Gebruik het TCP/IP-protocol om verbinding te maken met Azure Database for MariaDB |
   | Hostnaam | **mydemoserver.mariadb.database.azure.com** | De servernaam die u eerder hebt genoteerd. |
   | Poort | **3306** | De standaardpoort voor Azure Database for MariaDB. |
   | Gebruikersnaam | **myadmin\@mydemoserver** | De serverbeheerdersnaam die u eerder hebt genoteerd. |
   | Wachtwoord | *Uw wachtwoord* | Gebruik het beheerdersaccountwachtwoord voor het beheerdersaccount dat u eerder hebt ingesteld. |

3. Selecteer **Verbinding testen** om te controleren of alle parameters juist zijn geconfigureerd.

4. Selecteer de verbinding om verbinding te maken met de server.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze resources niet voor een andere snelstart of zelfstudie nodig hebt, kunt u ze verwijderen door de volgende opdracht uit te voeren: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Als u alleen de server wilt verwijderen die u hebt gemaakt in deze snelstart, voert u de opdracht [az mariadb server delete](/cli/azure/mariadb/server#az_mariadb_server_delete) uit:

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een MariaDB-database ontwerpen met Azure CLI](tutorial-design-database-cli.md)
