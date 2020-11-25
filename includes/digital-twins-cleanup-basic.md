---
author: baanders
description: bestand opnemen voor het opschonen van een eenvoudig Azure Digital Twins-exemplaar
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 4c03ef942896dda63f678018cdd257024cfbb6d4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011250"
---
Als u de resources die u in deze zelfstudie hebt gemaakt niet meer nodig hebt, kunt u ze verwijderen met de volgende stappen.

Met behulp van [Azure Cloud Shell](https://shell.azure.com) kunt u alle Azure-resources in een resourcegroep verwijderen met de opdracht [az group delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete). Met deze opdracht verwijdert u de resourcegroep en het Azure Digital Twins-exemplaar.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert.

Open Azure Cloud Shell en voer de volgende opdracht uit om de resourcegroep en alles daarin te verwijderen.

```azurecli-interactive
az group delete --name <your-resource-group>
```