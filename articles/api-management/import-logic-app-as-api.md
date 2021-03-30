---
title: Een logische app als API importeren met Azure Portal | Microsoft Azure Docs
description: In dit artikel leert u hoe u API Management (APIM) moet gebruiken om handmatig een logische app als API te importeren.
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
ms.openlocfilehash: 0dd460bce01b86c72d0b8fd70351fdcc85c69bd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146957"
---
# <a name="import-a-logic-app-as-an-api"></a>Een logische app als API importeren

In dit artikel wordt uitgelegd hoe u een logische app als een API importeert en de geïmporteerde API vervolgens test.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> -   Een logische app als API importeren
> -   De API testen in Azure Portal
> -   De API testen in de ontwikkelaarsportal

## <a name="prerequisites"></a>Vereisten

-   Voltooi de volgende quickstart: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)
-   Zorg ervoor dat er een logische app in uw abonnement aanwezig is waarmee een HTTP-eindpunt zichtbaar wordt gemaakt. Raadpleeg [Werkstromen met HTTP-eindpunten activeren](../logic-apps/logic-apps-http-endpoint.md) voor meer informatie

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Een back-end-API importeren en publiceren

1. Navigeer naar uw API Management-service in de Azure Portal en selecteer **API's** in het menu.
2. Selecteer **logische app** uit de lijst **Nieuwe API toevoegen**.

    ![Logische apps](./media/import-logic-app-as-api/logic-app-api.png)

3. Druk op **Bladeren** voor een overzicht van de logische apps met een HTTP-trigger in uw abonnement. (Houd er rekening mee dat logische apps zonder HTTP-trigger niet in de lijst voorkomen.)
4. Selecteer de app. API Management zoekt de swagger die is gekoppeld aan de geselecteerde app, haalt deze op en importeert deze.
5. Voeg een achtervoegsel toe van de URL voor de API. Het achtervoegsel is een naam die deze specifieke API in dit API Management-exemplaar identificeert. Het moet uniek zijn in dit API Management-exemplaar.
6. Publiceer de API door deze aan een product te koppelen. In dit geval wordt het product _onbeperkt_ gebruikt. Als u wilt dat de API wordt gepubliceerd en beschikbaar is voor ontwikkelaars, kunt u deze toevoegen aan een product. U kunt dit doen tijdens het maken van de API of het later instellen.

    Producten zijn koppelingen van een of meer API's. U kunt een aantal API's opnemen en deze beschikbaar stellen voor ontwikkelaars via de ontwikkelaarsportal. Ontwikkelaars moeten zich eerst abonneren op een product om toegang tot de API te krijgen. Wanneer ontwikkelaars zich abonneren, ontvangen ze een abonnementssleutel die toegang biedt tot elke API in het betreffende product. Als u het API Management-exemplaar hebt gemaakt, bent u al een beheerder en bent u standaard geabonneerd op elk product.

    Standaard wordt elk API Management-exemplaar geleverd met twee voorbeeldproducten:

    - **Starter**
    - **Onbeperkt**

7. Voer andere API-instellingen in. U kunt de waarden instellen tijdens het maken of later configureren door naar het tabblad **Instellingen** te gaan. De instellingen worden beschreven in de zelfstudie [Uw eerste API importeren en publiceren](import-and-publish.md#import-and-publish-a-backend-api).
8. Selecteer **Maken**.

## <a name="test-the-api-in-the-azure-portal"></a>De API testen in Azure Portal

Bewerkingen kunnen rechtstreeks vanuit Azure Portal worden aangeroepen. Dit is een handige manier om de bewerkingen van een API te bekijken en te testen.

1. Selecteer de API die u in de vorige stap hebt gemaakt.
2. Druk op het tabblad **Testen**.
3. Selecteer een willekeurige bewerking.

    De pagina geeft velden weer voor queryparameters en velden voor de headers. Een van de headers is Ocp-Apim-Subscription-Key voor de abonnementssleutel van het product dat is gekoppeld aan deze API. Als u het API Management-exemplaar hebt gemaakt, bent u al een beheerder en wordt de sleutel dus automatisch ingevuld.

4. Druk op **Verzenden**.

    Back-end reageert met **200 OK** en enkele gegevens.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

>[!NOTE]
>Elke logische app heeft functionaliteit voor **handmatig aanroepen**. Als u wilt dat uw API uit meerdere logische apps bestaat om geen conflicten te hebben, moet u de naam van de functie veranderen.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>
> [Een gepubliceerde API transformeren en beschermen](transform-api.md)
