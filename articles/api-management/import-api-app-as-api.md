---
title: Een logische app als API importeren met Azure Portal | Microsoft Docs
description: In dit artikel leert u hoe u API Management (APIM) moet gebruiken om handmatig logische app als API te importeren.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: 41209233ec59f578db4ff7fd344bb96aefeb975e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994737"
---
# <a name="import-an-api-app-as-an-api"></a>Een API-app importeren als API

In dit artikel wordt uitgelegd hoe u een logische app als API importeert. In het artikel wordt ook uitgelegd hoe u de APIM-API kunt testen.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een API-app importeren als API
> * De API testen in Azure Portal
> * De API testen in de ontwikkelaarsportal

## <a name="prerequisites"></a>Vereisten

+ Voltooi de volgende quickstart: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)
+ Zorg ervoor dat er een API-app in uw abonnement aanwezig is. Zie [App Service-documentatie](../app-service/index.yml) voor meer informatie

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Een back-end-API importeren en publiceren

1. Navigeer naar uw API Management-service in de Azure Portal en selecteer **API's** in het menu.
2. Selecteer **API-app** uit de lijst **Nieuwe API toevoegen**.

    ![API-app](./media/import-api-app-as-api/api-app.png)
3. Druk op **Bladeren** voor een overzicht van de API-apps in uw abonnement.
4. Selecteer de app. APIM zoekt de swagger die is gekoppeld aan de geselecteerde app, haalt deze op en importeert hem. 

    Als APIM geen swagger vindt, maakt deze de API beschikbaar als een 'pass-through'-API. 
5. Voeg een achtervoegsel toe van de URL voor de API. Het achtervoegsel is een naam die deze specifieke API in dit APIM-exemplaar identificeert. Hij moet uniek zijn in dit APIM-exemplaar.
6. Publiceer de API door deze aan een product te koppelen. In dit geval wordt het product *onbeperkt* gebruikt.  Als u wilt dat de API wordt gepubliceerd en beschikbaar is voor ontwikkelaars, kunt u deze toevoegen aan een product. U kunt dit doen tijdens het maken van de API of het later instellen.

    Producten zijn koppelingen van een of meer API's. U kunt een aantal API's opnemen en deze beschikbaar stellen voor ontwikkelaars via de ontwikkelaarsportal. Ontwikkelaars moeten zich eerst abonneren op een product om toegang tot de API te krijgen. Wanneer ontwikkelaars zich abonneren, ontvangen ze een abonnementssleutel die toegang biedt tot elke API in het betreffende product. Als u de APIM-abonnementssleutel hebt gemaakt, bent u al een beheerder en bent u standaard geabonneerd op elk product.

    Standaard wordt elk API Management-exemplaar geleverd met twee voorbeeldproducten:

    * **Starter**
    * **Onbeperkt**   
7. Voer andere API-instellingen in. U kunt de waarden instellen tijdens het maken of later configureren door naar het tabblad **Instellingen** te gaan. De instellingen worden beschreven in de zelfstudie [Uw eerste API importeren en publiceren](import-and-publish.md#import-and-publish-a-backend-api).
8. Selecteer **Maken**.

## <a name="test-the-new-api-in-the-azure-portal"></a>De nieuwe API testen in de Azure-portal

Bewerkingen kunnen rechtstreeks vanuit Azure Portal worden aangeroepen. Dit is een handige manier om de bewerkingen van een API te bekijken en te testen.  

1. Selecteer de API die u in de vorige stap hebt gemaakt.
2. Druk op het tabblad **Testen**.
3. Selecteer een willekeurige bewerking.

    De pagina geeft velden weer voor queryparameters en velden voor de headers. Een van de headers is Ocp-Apim-Subscription-Key voor de abonnementssleutel van het product dat is gekoppeld aan deze API. Als u de APIM-instantie hebt gemaakt, bent u al een beheerder en wordt de sleutel automatisch ingevoerd. 
1. Druk op **Verzenden**.

    Back-end reageert met **200 OK** en enkele gegevens.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een gepubliceerde API transformeren en beveiligen](transform-api.md)
