---
title: Aangepaste web-Api's aanroepen & REST-Api's
description: Uw eigen web-Api's aanroepen & REST Api's vanuit Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 05/13/2020
ms.openlocfilehash: 7b4d00e8c0366d10fddafa66db699c1a59fd9ad7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83659783"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Aangepaste API's implementeren en aanroepen vanuit werkstromen in Azure Logic Apps

Nadat u [uw eigen api's hebt gemaakt](./logic-apps-create-api-app.md) voor gebruik in uw logische app-werk stromen, moet u die api's implementeren voordat u ze kunt aanroepen. U kunt uw Api's als [Web-apps](../app-service/overview.md)implementeren, maar u kunt ook uw Api's als [API-apps](../app-service/app-service-web-tutorial-rest-api.md)implementeren, waardoor uw taak eenvoudiger wordt wanneer u api's in de Cloud en on-premises bouwt, host en verbruikt. U hoeft geen code in uw Api's te wijzigen: implementeer gewoon uw code naar een API-app. U kunt uw Api's hosten op [Azure app service](../app-service/overview.md), een Paas-Aanbieding (platform-as-a-Service) die een uiterst schaal bare en eenvoudige API-hosting biedt.

Hoewel u een API kunt aanroepen vanuit een logische app, moet u voor de beste ervaring [Swagger-meta gegevens](https://swagger.io/specification/) toevoegen met een beschrijving van de bewerkingen en para meters van uw API. Dit Swagger-document helpt uw API gemakkelijker te integreren en beter te werken met Logic apps.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Uw API als een web-app of API-app implementeren

Voordat u uw aangepaste API kunt aanroepen vanuit een logische app, implementeert u uw API als een web-app of API-app voor Azure App Service. Als u wilt dat uw Swagger-document kan worden gelezen door de Logic Apps Designer, stelt u de API-definitie-eigenschappen in en schakelt u de functie [Cross-Origin Resource Sharing (CORS)](../app-service/overview.md) in voor uw web-app of API-app.

1. Selecteer uw web-app of API-app in het [Azure Portal](https://portal.azure.com).

2. Kies in het menu app dat wordt geopend, onder **API** de **API-definitie**. Stel de **locatie** van de API-definitie in op de URL voor uw swagger.jsin het bestand.

   Normaal gesp roken wordt de URL in de volgende indeling weer gegeven: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Koppeling naar Swagger-document voor uw aangepaste API](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. Onder **API** kiest u **CORS**. Stel het CORS-beleid voor **toegestane oorsprongen** in op **' * '** (alles toestaan).

   Met deze instelling kunt u aanvragen van de ontwerp functie voor logische apps toestaan.

   ![Aanvragen van de ontwerp functie voor logische apps toestaan voor uw aangepaste API](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Zie [een resterende API hosten met CORS in azure app service](../app-service/app-service-web-tutorial-rest-api.md)voor meer informatie.

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Uw aangepaste API aanroepen vanuit werk stromen voor logische apps

Nadat u de API-definitie-eigenschappen en CORS hebt ingesteld, moeten de triggers en acties van uw aangepaste API'S beschikbaar zijn voor u in de werk stroom van uw logische app kunt toevoegen. 

*  Als u websites met OpenAPI-Url's wilt weer geven, kunt u door de websites van uw abonnement bladeren in de Logic Apps Designer.

*  Als u beschik bare acties en invoer wilt weer geven door op een Swagger-document te wijzen, gebruikt u de [actie http + Swagger](../connectors/connectors-native-http-swagger.md).

*  Als u een API wilt aanroepen, met inbegrip van Api's die geen Swagger-document hebben of weer geven, kunt u altijd een aanvraag met de [http-actie](../connectors/connectors-native-http.md)maken.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van aangepaste connectors](../logic-apps/custom-connector-overview.md)
