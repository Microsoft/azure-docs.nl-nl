---
title: Firewall regels beheren-Azure CLI-Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe u firewall regels maakt en beheert in Azure Database for PostgreSQL-één server met behulp van de Azure CLI-opdracht regel.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3a559b8c65ab57b0144b807a3b4cc1faa912d430
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422736"
---
# <a name="create-and-manage-firewall-rules-in-azure-database-for-postgresql---single-server-using-azure-cli"></a>Firewall regels maken en beheren in Azure Database for PostgreSQL-één server met behulp van Azure CLI
Firewall regels op server niveau kunnen worden gebruikt voor het beheren van de toegang tot een Azure Database for PostgreSQL-server vanuit een specifiek IP-adres of IP-bereik. Met behulp van handige Azure CLI-opdrachten kunt u firewall regels maken, bijwerken, verwijderen en weer geven om uw server te beheren. Zie [Azure database for PostgreSQL Server firewall rules](concepts-firewall-rules.md)(Engelstalig) voor een overzicht van Azure database for PostgreSQL firewall regels.

Regels voor Virtual Network (VNet) kunnen ook worden gebruikt voor het beveiligen van de toegang tot uw server. Meer informatie over [het maken en beheren van Virtual Network Service-eind punten en-regels met behulp van de Azure cli](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Vereisten
Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:
- Installeer het [Azure cli](/cli/azure/install-azure-cli) -opdracht regel programma of gebruik de Azure Cloud shell in de browser.
- Een [Azure database for postgresql-server en-data base](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Firewall regels voor Azure Database for PostgreSQL configureren
De opdracht [AZ post gres Server firewall-Rule](/cli/azure/postgres/server/firewall-rule) wordt gebruikt voor het configureren van firewall regels.

## <a name="list-firewall-rules"></a>Firewall regels weer geven 
Als u de bestaande firewall regels van de server wilt weer geven, voert u de opdracht [AZ post gres Server firewall-Rule List](/cli/azure/postgres/server/firewall-rule) uit.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
In de uitvoer worden de firewall regels standaard weer gegeven in JSON-indeling. U kunt de schakel optie `--output table` voor een lees bare tabel indeling gebruiken als uitvoer.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Firewallregel maken
Voer de opdracht [AZ post gres Server firewall-Rule Create](/cli/azure/postgres/server/firewall-rule) uit om een nieuwe firewall regel te maken op de server. 


Als u toegang tot een enkelvoudig IP-adres wilt toestaan, geeft u hetzelfde adres op in de `--start-ip-address` en `--end-ip-address` , zoals in dit voor beeld, waarbij de IP-adressen die hier worden weer gegeven, worden vervangen door uw specifieke IP.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Als u wilt toestaan dat toepassingen van IP-adressen van Azure verbinding maken met uw Azure Database for PostgreSQL server, geeft u het IP-adres 0.0.0.0 op als IP-begin-en eind-IP, zoals in dit voor beeld.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Met deze optie configureert u de firewall zo dat alle verbindingen vanuit Azure zijn toegestaan, inclusief verbindingen vanuit de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorgt u er dan voor dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.
> 

Wanneer de opdracht is voltooid, worden de details van de firewall regel die u hebt gemaakt, standaard weer gegeven in JSON-indeling. Als er een fout is, wordt in plaats daarvan een fout bericht weer gegeven in de uitvoer.

## <a name="update-firewall-rule"></a>Firewall regel bijwerken 
Werk een bestaande firewall regel op de server bij met de opdracht [AZ post gres Server firewall-Rule update](/cli/azure/postgres/server/firewall-rule) . Geef de naam van de bestaande firewall regel op als invoer en het IP-adres en het laatste IP-adres dat moet worden bijgewerkt.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
Wanneer de opdracht is voltooid, worden de details van de firewall regel die u hebt bijgewerkt, standaard weer gegeven in JSON-indeling. Als er een fout is, wordt in plaats daarvan een fout bericht weer gegeven in de uitvoer.
> [!NOTE]
> Als de firewall regel niet bestaat, wordt deze gemaakt met de opdracht bijwerken.

## <a name="show-firewall-rule-details"></a>Details van firewall regel weer geven
U kunt ook de details van een bestaande firewall regel op server niveau weer geven door de opdracht [AZ post gres Server firewall-Rule show](/cli/azure/postgres/server/firewall-rule) uit te voeren.
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Wanneer de opdracht is voltooid, worden de details van de firewall regel die u hebt opgegeven, standaard weer gegeven in JSON-indeling. Als er een fout is, wordt in plaats daarvan een fout bericht weer gegeven in de uitvoer.

## <a name="delete-firewall-rule"></a>Firewall regel verwijderen
Als u de toegang tot een IP-bereik wilt intrekken voor de-server, verwijdert u een bestaande firewall regel door de opdracht [AZ post gres Server firewall-Rule delete](/cli/azure/postgres/server/firewall-rule) uit te voeren. Geef de naam op van de bestaande firewall regel.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Als de bewerking is voltooid, is er geen uitvoer. Bij een fout wordt de tekst van het fout bericht geretourneerd.

## <a name="next-steps"></a>Volgende stappen
- Op dezelfde manier kunt u een webbrowser gebruiken voor [het maken en beheren van Azure database for PostgreSQL firewall regels met behulp van de Azure Portal](howto-manage-firewall-using-portal.md).
- Meer informatie over de [firewall regels van Azure database for postgresql server](concepts-firewall-rules.md).
- Verdere beveiligde toegang tot uw server door [het maken en beheren van Virtual Network Service-eind punten en-regels met behulp van de Azure cli](howto-manage-vnet-using-cli.md).
- Zie voor hulp bij het maken van verbinding met een Azure Database for PostgreSQL-server [verbindings bibliotheken voor Azure database for PostgreSQL](concepts-connection-libraries.md).
