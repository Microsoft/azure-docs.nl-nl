---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 2bfa356deeede1c16bd5a464ea7081132a67faf6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994683"
---
## <a name="append-other-apis"></a>Andere API's toevoegen

Een API kan bestaan uit de API's die worden weergegeven door andere services, met inbegrip van de OpenAPI-specificatie, een SOAP-API, de API Apps-functie van Azure App Service, Azure Function App, Azure Logic Apps en Azure Service Fabric.

![Een API importeren](./media/api-management-append-apis/import.png)

Als u een andere API wilt toevoegen aan uw bestaande API, voert u de volgende stappen uit. Wanneer u een andere API importeert, worden de bewerkingen toegevoegd aan uw huidige API.

1. Ga naar uw Azure API Management-exemplaar in Azure Portal.
2. Selecteer **API's** in het menu aan de linkerkant.
3. Selecteer het beletselsteken (**...**) naast de API waaraan u een andere API wilt toevoegen.
4. Selecteer **Importeren** in de vervolgkeuzelijst.
5. Selecteer de service waaruit een API moet worden geïmporteerd.