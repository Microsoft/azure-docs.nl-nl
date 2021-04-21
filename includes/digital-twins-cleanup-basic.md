---
author: baanders
description: bestand opnemen voor het ops Azure Digital Twins exemplaar
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 0d8cc30c0511098caf7b6c47d7f7bd400dc32f1b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799821"
---
* **Als u de resources** die u in deze zelfstudie hebt gemaakt niet nodig hebt, kunt u de Azure Digital Twins-instantie en alle andere resources uit dit artikel verwijderen met de [opdracht az group](/cli/azure/group#az_group_delete) delete. Hiermee verwijdert u alle Azure-resources in een resourcegroep, evenals de resourcegroep zelf.
    
    > [!IMPORTANT]
    > Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert.
    
    Open [Azure Cloud Shell](https://shell.azure.com)en voer de volgende opdracht uit om de resourcegroep en alles wat deze bevat te verwijderen.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```