---
title: 'Azure CLI-script: schaal aanpassen van Azure Database voor PostgreSQL en database controleren'
description: "Azure CLI-voorbeeldscript: de schaal van een Azure Database for PostgreSQL-server aanpassen naar een ander prestatieniveau na het uitvoeren van query's op de metrische gegevens."
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc, devx-track-azurecli
ms.topic: sample
ms.date: 08/07/2019
ms.openlocfilehash: 56fd88ab658e59cccb14a35559d1793bc3ad1aa0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778416"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Eén PostgreSQL-server bewaken en de schaal ervan aanpassen met Azure CLI
Met dit CLI-voorbeeldscript worden de compute- en opslagservices van een Azure Database for PostgreSQL-server aangepast nadat er query's zijn uitgevoerd op de metrische gegevens. Compute kan omhoog of omlaag worden geschaald. Opslag kan alleen omhoog worden geschaald. 

> [!IMPORTANT] 
> Opslag kan alleen omhoog en niet omlaag worden geschaald.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="sample-script"></a>Voorbeeldscript
Werk het script bij met uw abonnements-id.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie
Gebruik de volgende opdracht om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen nadat het script is uitgevoerd. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Uitleg van het script
Dit script maakt gebruik van de opdrachten die in de volgende tabel worden weergegeven:

| **Opdracht** | **Opmerkingen** |
|---|---|
| [az group create](/cli/azure/group) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) | Hiermee wordt een PostgreSQL-server gemaakt waar de SQL-database wordt gehost. |
| [az postgres server update](/cli/azure/postgres/server#az_postgres_server_update) | Hiermee worden eigenschappen van de PostgreSQL-server bijgewerkt. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Geeft de metrische waarde weer voor de resources. |
| [az group delete](/cli/azure/group) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen
- Lees meer over [Compute en opslag van Azure Database voor PostgreSQL](../concepts-pricing-tiers.md)
- Aanvullende scripts proberen: [Azure CLI-voorbeelden voor Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
- Lees meer over de [Azure CLI](/cli/azure).
