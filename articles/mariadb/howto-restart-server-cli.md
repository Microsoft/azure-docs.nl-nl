---
title: Server opnieuw opstarten-Azure CLI-Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u een Azure Database for MariaDB server opnieuw kunt opstarten met behulp van de Azure CLI.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8ae69adfe83b871eb29c85fc4d03e817026ec006
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664916"
---
# <a name="restart-azure-database-for-mariadb-server-using-the-azure-cli"></a>Azure Database for MariaDB server opnieuw opstarten met behulp van de Azure CLI
In dit onderwerp wordt beschreven hoe u een Azure Database for MariaDB server opnieuw kunt starten. Mogelijk moet u de server opnieuw opstarten om onderhouds redenen te zorgen, waardoor er een korte storing optreedt terwijl de server de bewerking uitvoert.

Het opnieuw opstarten van de server wordt geblokkeerd als de service bezet is. De service kan bijvoorbeeld een eerder aangevraagde bewerking verwerken, zoals het schalen van vCores.

De tijd die nodig is om opnieuw op te starten, is afhankelijk van het MariaDB-herstel proces. Om de herstarttijd te verlagen, raden we u aan om de hoeveelheid activiteit die op de server plaatsvindt, te minimaliseren voordat de computer opnieuw wordt opgestart.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze hand leiding:

- U hebt een [Azure database for MariaDB-server](quickstart-create-mariadb-server-database-using-azure-cli.md)nodig.
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Voor dit artikel is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.


## <a name="restart-the-server"></a>Start de server opnieuw

Start de server opnieuw op met de volgende opdracht:

```azurecli-interactive
az mariadb server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het instellen van para meters in azure database for MariaDB](howto-configure-server-parameters-cli.md)
