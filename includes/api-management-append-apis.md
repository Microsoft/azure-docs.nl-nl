---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 04/16/2021
ms.author: vlvinogr
ms.openlocfilehash: 329ea156b296810395eb7b8e8310bed5ee0ee4c9
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601938"
---
## <a name="append-other-apis"></a>Andere API's toevoegen

U kunt een API opstellen met API's die beschikbaar worden gemaakt door verschillende services, waaronder:
* De OpenAPI-specificatie
* EEN SOAP-API
* De API Apps-functie van Azure App Service
* Azure Function-app
* Azure Logic Apps
* Azure Service Fabric

Gebruik de volgende stappen om een andere API toe te passen aan uw bestaande API. 

>[!NOTE] 
> Wanneer u een andere API importeert, worden de bewerkingen toegevoegd aan uw huidige API.

1. Ga naar uw Azure API Management-exemplaar in Azure Portal.

    :::image type="content" source="./media/api-management-append-apis/service-page.png" alt-text="Ga naar het Azure API Mgmt-exemplaar":::

1. Selecteer **API's** in het menu aan de linkerkant.

    :::image type="content" source="./media/api-management-append-apis/api-select.png" alt-text="API's selecteren":::

1. Selecteer het beletselsteken (**...**) naast de API waaraan u een andere API wilt toevoegen.
1. Selecteer **Importeren** in de vervolgkeuzelijst.

    :::image type="content" source="./media/api-management-append-apis/append-01.png" alt-text="Importeren selecteren":::

1. Selecteer de service waaruit een API moet worden geïmporteerd.

    :::image type="content" source="./media/api-management-append-apis/select-to-import.png" alt-text="Service selecteren":::