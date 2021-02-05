---
author: baanders
description: bestand insluiten voor het opschonen van een Azure Digital Apparaatdubbels-exemplaar
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: c3c1b814b357a2e4b724590261657e485852f99c
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575676"
---
* **Als u geen van de resources nodig hebt die u in deze zelf studie hebt gemaakt**, kunt u het Azure Digital apparaatdubbels-exemplaar en alle andere resources uit dit artikel verwijderen met de opdracht [AZ Group delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) . Hiermee worden alle Azure-resources in een resource groep en de resource groep zelf verwijderd.
    
    > [!IMPORTANT]
    > Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert.
    
    Open [Azure Cloud shell](https://shell.azure.com)en voer de volgende opdracht uit om de resource groep en alles daarin te verwijderen.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```