---
author: baanders
description: bestand insluiten voor het opschonen van een Azure Digital Apparaatdubbels-exemplaar
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 9a02c4f5c5699b4a6308bfaa519fa9eb776414d6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102245034"
---
* **Als u geen van de resources nodig hebt die u in deze zelf studie hebt gemaakt**, kunt u het Azure Digital apparaatdubbels-exemplaar en alle andere resources uit dit artikel verwijderen met de opdracht [AZ Group delete](/cli/azure/group#az-group-delete) . Hiermee worden alle Azure-resources in een resource groep en de resource groep zelf verwijderd.
    
    > [!IMPORTANT]
    > Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert.
    
    Open [Azure Cloud shell](https://shell.azure.com)en voer de volgende opdracht uit om de resource groep en alles daarin te verwijderen.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```