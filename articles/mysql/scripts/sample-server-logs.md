---
title: "CLI-script: logboeken voor langzame query's downloaden in Azure Database for MySQL"
description: Dit Azure CLI-voorbeeldscript laat zien hoe u de serverlogboeken van een Azure Database for MySQL-server kunt activeren en downloaden.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 28dbf51fa58dfdfc375bf8f44964a6b66db008e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94539155"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Langzame-querylogboeken van een Azure Database for MySQL-server activeren en downloaden met behulp van Azure CLI
Met dit CLI-voorbeeldscript worden de langzame-querylogboeken van één Azure Database for MySQL-server geactiveerd en gedownload.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd. 

## <a name="sample-script"></a>Voorbeeldscript
Bewerk in dit voorbeeldscript de gemarkeerde regels om de gebruikersnaam en het wachtwoord van de beheerder naar uw eigen bij te werken. Vervang &lt;log_file_name&gt; in de `az monitor`-opdrachten door de naam van uw eigen serverlogboekbestand.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie
Gebruik de volgende opdracht om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen nadat het script is uitgevoerd. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Uitleg van het script
Dit script maakt gebruik van de opdrachten die in de volgende tabel worden weergegeven:

| **Opdracht** | **Opmerkingen** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Hiermee wordt een MySQL-server gemaakt waar de databases worden gehost. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) | Hiermee maakt u een lijst van de configuratiewaarden voor een server. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) | Hiermee werkt u de configuratie van een server bij. |
| [az mysql server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) | Hiermee maakt u een lijst met de logboekbestanden van een server. |
| [az mysql server-logs download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) | Hiermee kunt u logboekgegevens downloaden. |
| [az group delete](/cli/azure/group#az-group-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over Azure CLI: [Azure CLI-documentatie](/cli/azure).
- Aanvullende scripts proberen: [Azure CLI-voorbeelden voor Azure Database for MySQL](../sample-scripts-azure-cli.md)
