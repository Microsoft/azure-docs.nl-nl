---
title: CLI-script - Serverparameters wijzigen - Azure Database for MySQL
description: Dit CLI-voorbeeldscript maakt een lijst met alle beschikbare serverconfiguratieopties en werkt de waarde van innodb_lock_wait_timeout bij.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: eea11eb79cda47a11456178100941db05f0f2fa1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791659"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Configuraties van een Azure Database for MySQL-server opsommen en bijwerken met behulp van Azure CLI
Met dit CLI-voorbeeldscript wordt een lijst gemaakt van alle beschikbare configuratieparameters en de toegestane waarden ervan voor een Azure Database for MySQL-server, en wordt *innodb_lock_wait_timeout* ingesteld op een andere waarde dan de standaardwaarde.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd. 

## <a name="sample-script"></a>Voorbeeldscript
Bewerk in dit voorbeeldscript de gemarkeerde regels om de gebruikersnaam en het wachtwoord van de beheerder naar uw eigen bij te werken.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie
Gebruik de volgende opdracht om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen nadat het script is uitgevoerd. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Uitleg van het script
Dit script maakt gebruik van de opdrachten die in de volgende tabel worden weergegeven:

| **Opdracht** | **Opmerkingen** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) | Hiermee wordt een MySQL-server gemaakt waar de databases worden gehost. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) | Hiermee wordt een lijst gemaakt van de configuraties van een Azure Database for MySQL-server. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) | Hiermee wordt de configuratie van een Azure Database for MySQL-server bijgewerkt. |
| [az mysql server configuration show](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show) | Hiermee wordt de configuratie van een Azure Database for MySQL-server weergegeven. |
| [az group delete](/cli/azure/group#az_group_delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over Azure CLI: [Azure CLI-documentatie](/cli/azure).
- Aanvullende scripts proberen: [Azure CLI-voorbeelden voor Azure Database for MySQL](../sample-scripts-azure-cli.md)
- Zie [Serverparameters in Azure Database for MySQL configureren](../howto-server-parameters.md) voor meer informatie over serverparameters.
