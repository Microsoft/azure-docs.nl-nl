---
title: SOAP API importeren met behulp van Azure Portal | Microsoft Docs
description: Informatie over het importeren van een standaard XML-representatie van een SOAP API en het testen van de API in de Azure en ontwikkelaarsportals.
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
ms.openlocfilehash: 81ded79ee72fb7c2d89898595602cb3e6d7ae5e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011061"
---
# <a name="import-soap-api"></a>SOAP API importeren

Dit artikel laat zien hoe u een standaard XML-representatie van een SOAP API moet importeren. In het artikel wordt ook uitgelegd hoe u de API Management-API kunt testen.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * SOAP API importeren
> * De API testen in Azure Portal
> * De API testen in de ontwikkelaarsportal

## <a name="prerequisites"></a>Vereisten

Voltooi de volgende quickstart: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Een back-end-API importeren en publiceren

1. Navigeer naar uw API Management-service in de Azure Portal en selecteer **API's** in het menu.
2. Selecteer **WSDL** uit de lijst **Nieuwe API toevoegen**.

    ![SOAP API](./media/import-soap-api/wsdl-api.png)
3. Geef in de **WSDL-specificatie** de URL op waar de SOAP API is opgeslagen.
4. Het **SOAP pass-through**-keuzerondje is standaard geselecteerd. Met deze selectie zal de API worden weergegeven als SOAP. De consument moet SOAP-regels gebruiken. Als u van de API een REST API wilt maken, volg dan de stappen in [Import a SOAP API and convert it to REST](restify-soap-api.md) (Een SOAP API importeren en converteren naar REST).

    ![Schermopname toont het dialoogvenster Maken op basis van een W S D L waarin u een W S D L-specificatie kunt invoeren.](./media/import-soap-api/pass-through.png)
5. Druk op tab.

    De volgende velden worden gevuld met de gegevens van de SOAP API: Weergavenaam, Naam, Beschrijving.
6. Voeg een achtervoegsel toe van de URL voor de API. Het achtervoegsel is een naam die deze specifieke API in dit API Management-exemplaar identificeert. Het moet uniek zijn in dit API Management-exemplaar.
7. Publiceer de API door deze aan een product te koppelen. In dit geval wordt het product *onbeperkt* gebruikt.  Als u wilt dat de API wordt gepubliceerd en beschikbaar is voor ontwikkelaars, kunt u deze toevoegen aan een product. U kunt dit doen tijdens het maken van de API of het later instellen.

    Producten zijn koppelingen van een of meer API's. U kunt een aantal API's opnemen en deze beschikbaar stellen voor ontwikkelaars via de ontwikkelaarsportal. Ontwikkelaars moeten zich eerst abonneren op een product om toegang tot de API te krijgen. Wanneer ontwikkelaars zich abonneren, ontvangen ze een abonnementssleutel die toegang biedt tot elke API in het betreffende product. Als u het API Management-exemplaar hebt gemaakt, bent u al een beheerder en bent u standaard geabonneerd op elk product.

    Standaard wordt elk API Management-exemplaar geleverd met twee voorbeeldproducten:

    * **Starter**
    * **Onbeperkt**   
8. Voer andere API-instellingen in. U kunt de waarden instellen tijdens het maken of later configureren door naar het tabblad **Instellingen** te gaan. De instellingen worden beschreven in de zelfstudie [Uw eerste API importeren en publiceren](import-and-publish.md#import-and-publish-a-backend-api).
9. Selecteer **Maken**.

### <a name="test-the-new-api-in-the-administrative-portal"></a>De nieuwe API testen in de administratieve portal

Bewerkingen kunnen rechtstreeks vanuit de administratieve portal worden aangeroepen. Dit is een handige manier om de bewerkingen van een API te bekijken en te testen.  

1. Selecteer de API die u in de vorige stap hebt gemaakt.
2. Druk op het tabblad **Testen**.
3. Selecteer een willekeurige bewerking.

    De pagina geeft velden weer voor queryparameters en velden voor de headers. Een van de headers is Ocp-Apim-Subscription-Key voor de abonnementssleutel van het product dat is gekoppeld aan deze API. Als u het API Management-exemplaar hebt gemaakt, bent u al een beheerder en wordt de sleutel dus automatisch ingevuld. 
1. Druk op **Verzenden**.

    Back-end reageert met **200 OK** en enkele gegevens.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een gepubliceerde API transformeren en beveiligen](transform-api.md)