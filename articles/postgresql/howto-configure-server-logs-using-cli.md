---
title: Logboeken beheren-Azure CLI-Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe u de server Logboeken (logboek bestanden) kunt configureren en openen in Azure Database for PostgreSQL-één server met behulp van de Azure CLI.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 71dac0b6ea2202e712280607a73e860ae68bdb73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005585"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Server logboeken configureren en openen met behulp van Azure CLI
U kunt de fouten logboeken van de PostgreSQL-server downloaden met behulp van de opdracht regel interface (Azure CLI). Toegang tot transactie Logboeken wordt echter niet ondersteund. 

## <a name="prerequisites"></a>Vereisten
Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:
- [Azure Database for PostgreSQL server](quickstart-create-server-database-azure-cli.md)
- Het opdracht regel hulpprogramma van [Azure cli](/cli/azure/install-azure-cli) of de Azure Cloud shell in de browser

## <a name="configure-logging"></a>Logboek registratie configureren
U kunt de server configureren voor toegang tot de query logboeken en fout Logboeken. Fout logboeken kunnen automatische vacuüm-, verbindings-en controlepunt gegevens hebben.
1. Logboekregistratie inschakelen.
2. Als u query logboek registratie wilt inschakelen, geeft u de instructie update **Log \_** en **Log \_ min \_ duration \_** op.
3. Bewaar periode bijwerken.

Zie [para meters voor Server configuratie aanpassen](howto-configure-server-parameters-using-cli.md)voor meer informatie.

## <a name="list-logs"></a>Logboeken weer geven
Als u de beschik bare logboek bestanden voor uw server wilt weer geven, voert u de opdracht [AZ post gres server-logs List](/cli/azure/postgres/server-logs) uit.

U kunt de logboek bestanden voor server **mydemoserver.postgres.database.Azure.com** weer geven onder de resource groep **myresourcegroup**. Ga vervolgens naar de lijst met logboek bestanden naar een tekst bestand met de naam **logboek \_ bestanden \_list.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Logboeken lokaal downloaden van de server
Met de opdracht [AZ post gres server-logs down](/cli/azure/postgres/server-logs) load kunt u afzonderlijke logboek bestanden downloaden voor uw server. 

Gebruik het volgende voor beeld om het specifieke logboek bestand voor de server **mydemoserver.postgres.database.Azure.com** onder de resource groep **myresourcegroup** te downloaden naar uw lokale omgeving.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Volgende stappen
- Zie [Server Logboeken in azure database for PostgreSQL](concepts-server-logs.md)voor meer informatie over server Logboeken.
- Zie voor meer informatie over server parameters de [para meters voor Server configuratie aanpassen met behulp van Azure cli](howto-configure-server-parameters-using-cli.md).
