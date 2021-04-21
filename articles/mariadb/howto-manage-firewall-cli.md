---
title: Firewallregels beheren - Azure CLI - Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u firewallregels Azure Database for MariaDB en beheren met behulp van de Azure CLI-opdrachtregel.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 87ff75a07bd1b91121d614e0f41c0ecf216e1b41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791711"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Firewallregels voor Azure Database for MariaDB maken en beheren met behulp van de Azure CLI
Firewallregels op serverniveau kunnen worden gebruikt voor het beheren van toegang tot een Azure Database for MariaDB Server vanaf een specifiek IP-adres of een bereik van IP-adressen. Met behulp van handige Azure CLI-opdrachten kunt u firewallregels maken, bijwerken, verwijderen, weergeven en weergeven om uw server te beheren. Zie firewallregels voor Azure Database for MariaDB server voor Azure Database for MariaDB overzicht [van firewalls.](./concepts-firewall-rules.md)

Virtual Network regels (VNet) kunnen ook worden gebruikt om de toegang tot uw server te beveiligen. Meer informatie over [het maken en beheren Virtual Network service-eindpunten en -regels met behulp van de Azure CLI.](howto-manage-vnet-cli.md)

## <a name="prerequisites"></a>Vereisten
* [Installeer Azure CLI.](/cli/azure/install-azure-cli)
* Een [Azure Database for MariaDB server en database](quickstart-create-mariadb-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Opdrachten voor firewallregel:
De **opdracht az mariadb server firewall-rule** wordt vanuit de Azure CLI gebruikt om firewallregels te maken, te verwijderen, weer te geven, weer te geven en bij te werken.

Opdrachten:
- **maken:** maak een firewallregel voor de Azure MariaDB-server.
- **delete:** verwijder een firewallregel voor de Azure MariaDB-server.
- **list:** de firewallregels voor de Azure MariaDB-server opneren.
- **show:** de details van een Azure MariaDB-serverfirewallregel tonen.
- **update:** werk een firewallregel voor de Azure MariaDB-server bij.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Meld u aan bij Azure en vermeld uw Azure Database for MariaDB Servers
Verbind Azure CLI veilig met uw Azure-account met behulp van de **opdracht az login.**

1. Voer vanaf de opdrachtregel de volgende opdracht uit:
   ```azurecli
   az login
   ```
   Met deze opdracht wordt een code uitgevoerd die in de volgende stap moet worden gebruikt.

2. Gebruik een webbrowser om de pagina te openen [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en voer de code in.

3. Meld u bij de prompt aan met uw Azure-referenties.

4. Nadat uw aanmelding is geautoriseerd, wordt er een lijst met abonnementen afgedrukt in de -console. Kopieer de id van het gewenste abonnement om het huidige abonnement in te stellen dat moet worden gebruikt. Gebruik de [opdracht az account set.](/cli/azure/account#az_account_set)
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Vermeld de Azure Databases for MariaDB-servers voor uw abonnement en resourcegroep als u niet zeker weet welke namen u hebt. Gebruik de [opdracht az mariadb server list.](/cli/azure/mariadb/server#az_mariadb_server_list)

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   Noteer het naamkenmerk in de vermelding, waaraan u de MariaDB-server moet opgeven om mee te werken. Bevestig zo nodig de details voor die server en gebruik het naamkenmerk om er zeker van te zijn dat deze juist is. Gebruik de [opdracht az mariadb server show.](/cli/azure/mariadb/server#az_mariadb_server_show)

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Firewallregels op Azure Database for MariaDB server 
Gebruik de servernaam en de naam van de resourcegroep om de bestaande serverfirewallregels op de server weer te geven. Gebruik de [opdracht az mariadb server firewall list.](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_list)  U ziet dat het kenmerk servernaam is opgegeven in de **switch --server** en niet in de **switch --name.** 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
De uitvoer bevat de regels, indien van u, in JSON-indeling (standaard). U kunt de **schakelknop --output table** gebruiken om de resultaten uit te geven in een beter leesbare tabelindeling.
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Een firewallregel maken op Azure Database for MariaDB Server
Maak met behulp van de Azure MariaDB-servernaam en de naam van de resourcegroep een nieuwe firewallregel op de server. Gebruik de [opdracht az mariadb server firewall create.](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_create) Geef een naam op voor de regel, evenals het begin-IP-adres en eind-IP-adres (om toegang te bieden tot een bereik van IP-adressen) voor de regel.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Als u toegang wilt toestaan voor één IP-adres, geeft u hetzelfde IP-adres op als het begin-IP-adres en het eind-IP-adres, zoals in dit voorbeeld.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Als u wilt dat toepassingen van Azure IP-adressen verbinding kunnen maken met uw Azure Database for MariaDB-server, geeft u het IP-adres 0.0.0.0 op als het begin- en eind-IP-adres, zoals in dit voorbeeld.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Met deze optie configureert u de firewall zo dat alle verbindingen vanuit Azure zijn toegestaan, inclusief verbindingen vanuit de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorgt u er dan voor dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.
> 

Als de opdracht is gemaakt, geeft elke opdrachtuitvoer de details weer van de firewallregel die u hebt gemaakt, in JSON-indeling (standaard). Als er een fout is opgetreden, wordt in de uitvoer in plaats daarvan foutberichttekst weergegeven.

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Een firewallregel op de Azure Database for MariaDB bijwerken 
Werk met behulp van de Azure MariaDB-servernaam en de naam van de resourcegroep een bestaande firewallregel bij op de server. Gebruik de [opdracht az mariadb server firewall update.](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_update) Geef de naam van de bestaande firewallregel op als invoer, evenals de ip-begin- en eind-IP-kenmerken die moeten worden bijgewerkt.
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Als de opdracht is uitgevoerd, worden de details weergegeven van de firewallregel die u hebt bijgewerkt, in JSON-indeling (standaard). Als er een fout is opgetreden, wordt in de uitvoer in plaats daarvan foutberichttekst weergegeven.

> [!NOTE]
> Als de firewallregel niet bestaat, wordt de regel gemaakt met de opdracht update.

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Details van firewallregel op de Azure Database for MariaDB server
Gebruik de Azure MariaDB-servernaam en de naam van de resourcegroep om de bestaande firewallregeldetails van de server weer te geven. Gebruik de [opdracht az mariadb server firewall show.](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_show) Geef de naam van de bestaande firewallregel op als invoer.
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Als de opdracht is uitgevoerd, worden de details weergegeven van de firewallregel die u hebt opgegeven, in JSON-indeling (standaard). Als er een fout is opgetreden, wordt in de uitvoer in plaats daarvan foutberichttekst weergegeven.

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Een firewallregel op de Azure Database for MariaDB verwijderen
Verwijder met behulp van de Azure MariaDB-servernaam en de naam van de resourcegroep een bestaande firewallregel van de server. Gebruik de [opdracht az mariadb server firewall delete.](/cli/azure/mariadb/server/firewall-rule#az_mariadb_server_firewall_rule_delete) Geef de naam van de bestaande firewallregel op.
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Als dit is gelukt, is er geen uitvoer. Bij een fout wordt de tekst van het foutbericht weergegeven.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over Azure Database for MariaDB [Server-firewallregels.](./concepts-firewall-rules.md)
- [Maak en beheer Azure Database for MariaDB firewallregels met behulp van Azure Portal](./howto-manage-firewall-portal.md).
- Beveilig de toegang tot uw server verder door het maken en [beheren Virtual Network service-eindpunten](howto-manage-vnet-cli.md)en -regels met behulp van de Azure CLI.