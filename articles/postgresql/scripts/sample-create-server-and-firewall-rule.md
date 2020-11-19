---
title: 'Azure CLI-script: een Azure Database for PostgreSQL maken'
description: 'Azure CLI-scriptvoorbeeld: maakt een Azure Database for PostgreSQL-server en configureert een firewallregel op serverniveau.'
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/28/2018
ms.openlocfilehash: 959873d68f8812acb08ef0c7194f53b61404c918
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660488"
---
# <a name="create-an-azure-database-for-postgresql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Maakt een Azure Database for PostgreSQL-server en configureert een firewallregel met behulp van Azure CLI
Met dit Azure CLI-voorbeeldscript wordt een Azure Database for PostgreSQL-server gemaakt en een firewallregel op serverniveau geconfigureerd. Nadat het script is uitgevoerd, is de PostgreSQL-server toegankelijk via alle Azure-services en het geconfigureerde IP-adres.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="sample-script"></a>Voorbeeldscript
Bewerk in dit voorbeeldscript de gemarkeerde regels om de gebruikersnaam en het wachtwoord van de beheerder naar uw eigen bij te werken.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/create-postgresql-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for PostgreSQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie
Gebruik de volgende opdracht om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen nadat het script is uitgevoerd. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Uitleg van het script
Dit script maakt gebruik van de opdrachten die in de volgende tabel worden weergegeven:

| **Opdracht** | **Opmerkingen** |
|---|---|
| [az group create](/cli/azure/group) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az postgres server create](/cli/azure/postgres/server) | Hiermee wordt een PostgreSQL-server gemaakt waar de SQL-database wordt gehost. |
| [az postgres server firewall create](/cli/azure/postgres/server/firewall-rule) | Hiermee wordt een firewallregel gemaakt om toegang mogelijk te maken tot de server en databases onder deze regel vanaf het ingevoerde IP-adres. |
| [az group delete](/cli/azure/group) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over Azure CLI: [Azure CLI-documentatie](/cli/azure)
- Aanvullende scripts proberen: [Azure CLI-voorbeelden voor Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
