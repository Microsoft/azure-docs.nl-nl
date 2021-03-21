---
title: Firewall regels beheren-Azure CLI-Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u Azure Database for MariaDB firewall regels maakt en beheert met behulp van de Azure CLI-opdracht regel.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: ef04e2b4c820c14ea7df6c35ecb0189ef31ef7dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665035"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Azure Database for MariaDB firewall regels maken en beheren met behulp van de Azure CLI
Firewall regels op server niveau kunnen worden gebruikt om de toegang tot een Azure Database for MariaDB server te beheren vanuit een specifiek IP-adres of een bereik met IP-adressen. Met behulp van handige Azure CLI-opdrachten kunt u firewall regels maken, bijwerken, verwijderen en weer geven om uw server te beheren. Zie [Azure database for MariaDB Server firewall-regels](./concepts-firewall-rules.md)voor een overzicht van Azure database for MariaDB firewalls.

Regels voor Virtual Network (VNet) kunnen ook worden gebruikt voor het beveiligen van de toegang tot uw server. Meer informatie over [het maken en beheren van Virtual Network Service-eind punten en-regels met behulp van de Azure cli](howto-manage-vnet-cli.md).

## <a name="prerequisites"></a>Vereisten
* [Installeer Azure cli](/cli/azure/install-azure-cli).
* Een [Azure database for MariaDB-server en-data base](quickstart-create-mariadb-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Opdracht firewall regel:
De opdracht **AZ mariadb Server firewall-Rule** wordt gebruikt vanuit de Azure CLI om firewall regels te maken, verwijderen, weer geven en bijwerken.

Opdrachten:
- **maken**: Maak een firewall regel voor Azure MariaDB server.
- **verwijderen**: Verwijder een Azure MariaDB Server-firewall regel.
- **lijst**: Geef de firewall regels van de Azure MariaDB-server weer.
- **weer geven**: de details van een Azure MariaDB Server-firewall regel weer geven.
- **Update**: een Azure MariaDB Server-firewall regel bijwerken.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Meld u aan bij Azure en vermeld uw Azure Database for MariaDB-servers
Maak veilig verbinding met Azure CLI met uw Azure-account met behulp van de opdracht **AZ login** .

1. Voer vanaf de opdracht regel de volgende opdracht uit:
   ```azurecli
   az login
   ```
   Met deze opdracht voert u een code uit die in de volgende stap wordt gebruikt.

2. Gebruik een webbrowser om de pagina te openen [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en voer vervolgens de code in.

3. Meld u aan met uw Azure-referenties bij de prompt.

4. Nadat uw aanmelding is geautoriseerd, wordt een lijst met abonnementen afgedrukt in de-console. Kopieer de ID van het gewenste abonnement om het huidige abonnement in te stellen op gebruik. Gebruik de opdracht [AZ account set](/cli/azure/account#az-account-set) .
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Vermeld de Azure-data bases voor MariaDB-servers voor uw abonnement en resource groep als u niet zeker weet wat de namen zijn. Gebruik de opdracht [AZ mariadb server list](/cli/azure/mariadb/server#az-mariadb-server-list) .

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   Noteer het naam kenmerk in de vermelding, waarvoor u de MariaDB-server moet opgeven om te werken. Controleer, indien nodig, de details van die server en gebruik het naam kenmerk om te controleren of het juist is. Gebruik de opdracht [AZ mariadb server show](/cli/azure/mariadb/server#az-mariadb-server-show) .

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Firewall regels op Azure Database for MariaDB server weer geven 
Gebruik de server naam en de naam van de resource groep om de bestaande server firewall regels op de-server weer te geven. Gebruik de opdracht [AZ mariadb Server firewall List](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-list) .  U ziet dat het kenmerk server naam is opgegeven in de schakel optie **-Server** en niet in de schakel optie **-naam** . 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
In de uitvoer worden de regels, indien aanwezig, in JSON-indeling weer gegeven (standaard). U kunt de schakel optie **--output** gebruiken om de resultaten in een meer Lees bare tabel indeling uit te voeren.
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Een firewall regel maken op Azure Database for MariaDB server
Maak een nieuwe firewall regel op de server met behulp van de naam van de Azure MariaDB-server en de naam van de resource groep. Gebruik de opdracht [AZ mariadb Server firewall Create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) . Geef een naam op voor de regel, evenals het eerste IP-adres en het laatste IP-adres (om toegang te bieden tot een bereik van IP-adressen) voor de regel.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Als u toegang wilt verlenen voor een enkel IP-adres, geeft u hetzelfde IP-adres op als het IP-begin-en eind-IP, zoals in dit voor beeld.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Als u wilt toestaan dat toepassingen van IP-adressen van Azure verbinding maken met uw Azure Database for MariaDB server, geeft u het IP-adres 0.0.0.0 op als IP-begin-en eind-IP, zoals in dit voor beeld.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Met deze optie configureert u de firewall zo dat alle verbindingen vanuit Azure zijn toegestaan, inclusief verbindingen vanuit de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorgt u er dan voor dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.
> 

Bij voltooiing worden met elke opdracht uitvoer een lijst gemaakt met de details van de firewall regel die u hebt gemaakt, in JSON-indeling (standaard). Als er een fout is, wordt in plaats daarvan de tekst van het fout bericht weer gegeven in de uitvoer.

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Een firewall regel op Azure Database for MariaDB server bijwerken 
Werk een bestaande firewall regel op de server bij met behulp van de naam van de Azure MariaDB-server en de naam van de resource groep. Gebruik de opdracht [AZ mariadb Server firewall update](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-update) . Geef de naam van de bestaande firewall regel op als invoer, evenals het eerste IP-adres en het laatste IP-kenmerk dat moet worden bijgewerkt.
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Wanneer de opdracht is voltooid, worden de details weer gegeven van de firewall regel die u hebt bijgewerkt, in JSON-indeling (standaard). Als er een fout is, wordt in plaats daarvan de tekst van het fout bericht weer gegeven in de uitvoer.

> [!NOTE]
> Als de firewall regel niet bestaat, wordt de regel gemaakt met de opdracht bijwerken.

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Details van firewall regel op Azure Database for MariaDB server weer geven
De naam van de bestaande firewall regel van de server weer geven met behulp van de naam van de Azure MariaDB-server en de resource groep. Gebruik de opdracht [AZ mariadb Server firewall show](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-show) . Geef de naam van de bestaande firewall regel op als invoer.
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Wanneer de opdracht is voltooid, worden de details van de firewall regel die u hebt opgegeven, weer gegeven in JSON-indeling (standaard). Als er een fout is, wordt in plaats daarvan de tekst van het fout bericht weer gegeven in de uitvoer.

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Een firewall regel op Azure Database for MariaDB server verwijderen
Verwijder een bestaande firewall regel van de server met behulp van de naam van de Azure MariaDB-server en de naam van de resource groep. Gebruik de opdracht [AZ mariadb Server firewall delete](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-delete) . Geef de naam op van de bestaande firewall regel.
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Als de bewerking is voltooid, is er geen uitvoer. Wanneer de fout is opgetreden, wordt tekst weer gegeven.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [firewall regels van Azure database for MariaDB server](./concepts-firewall-rules.md).
- [Azure database for MariaDB firewall regels maken en beheren met behulp van de Azure Portal](./howto-manage-firewall-portal.md).
- Verdere beveiligde toegang tot uw server door [het maken en beheren van Virtual Network Service-eind punten en-regels met behulp van de Azure cli](howto-manage-vnet-cli.md).