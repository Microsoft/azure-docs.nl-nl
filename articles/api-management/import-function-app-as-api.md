---
title: Een Azure-functie-app als API importeren in API Management
titleSuffix: Azure API Management
description: In dit artikel leert u hoe u een Azure-functie-app importeert in Azure API Management als API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: f66395b1e0f45f1e80cd0ac93bf8c9ae8674a0f2
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732942"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Een Azure-functie-app als API importeren in Azure API Management

Azure API Management biedt ondersteuning voor het importeren van Azure-functie-apps als nieuwe API's of het toevoegen hiervan aan bestaande API's. Er wordt automatisch een hostsleutel gegenereerd in de Azure-functie-app, die vervolgens wordt toegewezen aan een benoemde waarde in Azure API Management.

In dit artikel wordt stapsgewijs het importeren van een Azure-functie-app als API in Azure API Management beschreven. Ook wordt het testproces beschreven.

U leert het volgende:

> [!div class="checklist"]
> * Een Azure-functie-app als API importeren
> * Een Azure-functie-app toevoegen aan een API
> * De nieuwe hostsleutel van de Azure-functie-app en de benoemde waarde van Azure API Management weergeven
> * De API testen in Azure Portal
> * De API testen in de ontwikkelaarsportal

## <a name="prerequisites"></a>Vereisten

* Lees de snelstart [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).
* Controleer of er een Azure Functions-app in uw abonnement aanwezig is. Zie [Een Azure-functie-app maken](../azure-functions/functions-get-started.md) voor meer informatie. Deze moet functies bevatten met HTTP-trigger en waarbij het verificatieniveau is ingesteld op *Anoniem* of *Functie*.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a> Een Azure-functie-app als nieuwe API importeren

Voer de volgende stappen uit om een nieuwe API te maken vanuit een Azure-functie-app.

1. Navigeer naar uw API Management-service in de Azure Portal en selecteer **API's** in het menu.

2. Selecteer **Functie-app** in de lijst **Een nieuwe API toevoegen**.

    ![Schermopname van de tegel Functie-app.](./media/import-function-app-as-api/add-01.png)

3. Klik op **Bladeren** om functies voor het importeren te selecteren.

    ![Schermopname met de knop Bladeren gemarkeerd.](./media/import-function-app-as-api/add-02.png)

4. Klik op de sectie **Functie-app** om te kiezen uit de lijst met beschikbare functie-apps.

    ![Schermopname met het gedeelte Functie-app gemarkeerd.](./media/import-function-app-as-api/add-03.png)

5. Zoek de functie-app waarvan u functies wilt importeren, klik erop en druk op **Selecteren**.

    ![Schermopname die de Functie-app waaruit u functies wilt importeren en de knop Selecteren markeert.](./media/import-function-app-as-api/add-04.png)

6. Selecteer de functies die u wilt importeren en klik op **Selecteren**.

    ![Schermopname die de te importeren functies en de knop Selecteren markeert.](./media/import-function-app-as-api/add-05.png)

    > [!NOTE]
    > U kunt alleen functies importeren die zijn gebaseerd op HTTP-trigger en waarbij het verificatieniveau is ingesteld op *Anoniem* of *Functie*.

7. Schakel over naar de **volledige** weergave en wijs **Product** toe aan uw nieuwe API. Geef, indien nodig, andere velden op tijdens het maken of configureer deze later door naar het tabblad **Instellingen** te gaan. De instellingen worden beschreven in de zelfstudie [Uw eerste API importeren en publiceren](import-and-publish.md#import-and-publish-a-backend-api).
8. Klik op **Create**.

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a> Een Azure-functie-app toevoegen aan een bestaande API

Voer de volgende stappen uit om de Azure-functie-app toe te voegen aan een bestaande API.

1. Selecteer in uw **Azure API Management**-service-exemplaar de optie **API's** in het menu links.

2. Kies een API waarin u een Azure-functie-app wilt importeren. Klik op **...** en selecteer **Importeren** in het contextmenu.

    ![Schermopname die de optie Menu importeren markeert.](./media/import-function-app-as-api/append-01.png)

3. Klik op de tegel **Functie-app**.

    ![Schermopname met de tegel Functie-app gemarkeerd.](./media/import-function-app-as-api/append-02.png)

4. Klik in het pop-upvenster op **Bladeren**.

    ![Schermopname waarin de knop Bladeren wordt weergegeven.](./media/import-function-app-as-api/append-03.png)

5. Klik op de sectie **Functie-app** om te kiezen uit de lijst met beschikbare functie-apps.

    ![Schermopname met de lijst met Functie-apps gemarkeerd.](./media/import-function-app-as-api/add-03.png)

6. Zoek de functie-app waarvan u functies wilt importeren, klik erop en druk op **Selecteren**.

    ![Schermopname die de Functie-app waaruit u functies wilt importeren markeert.](./media/import-function-app-as-api/add-04.png)

7. Selecteer de functies die u wilt importeren en klik op **Selecteren**.

    ![Schermopname waarin de functies die u wilt importeren zijn gemarkeerd.](./media/import-function-app-as-api/add-05.png)

8. Klik op **Import**.

    ![Toevoegen vanuit functie-app](./media/import-function-app-as-api/append-04.png)

## <a name="authorization"></a><a name="authorization"></a> Autorisatie

Bij het importeren van een Azure-functie-app wordt automatisch het volgende gegenereerd:

* Hostsleutel binnen de functie-app met de naam apim-{*de naam van uw Azure API Management service-exemplaar*},
* de benoemde waarde binnen het Azure API Management-exemplaar met de naam {*de naam van uw exemplaar van Azure-functie-app*}-key, die de gemaakte hostsleutel bevat.

Voor Api's die na 4 april 2019 zijn gemaakt, wordt de hostsleutel in HTTP-aanvragen van API Management aan de functie-app in een header doorgegeven. Oudere API's geven de hostsleutel als [een query parameter](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization). Dit gedrag kan worden gewijzigd via de `PATCH Backend` [REST API-aanroep](/rest/api/apimanagement/2019-12-01/backend/update#backendcredentialscontract) op de *Back-end* entiteit die aan de functie-app is gekoppeld.

> [!WARNING]
> Als de waarde van de hostsleutel van de Azure-functie-app of de benoemde waarde van Azure API Management wordt verwijderd of gewijzigd, wordt de communicatie tussen de services verbroken. De waarden worden niet automatisch gesynchroniseerd.
>
> Als u de hostsleutel wilt verwisselen, moet u de benoemde waarde ook wijzigen in Azure API Management.

### <a name="access-azure-function-app-host-key"></a>De hostsleutel van de Azure-functie-app openen

1. Navigeer naar uw exemplaar van Azure-functie-app.

2. Selecteer **Instellingen voor functie-apps** in het overzicht.

    ![Schermopname met optie Functie-appsinstellingen gemarkeerd.](./media/import-function-app-as-api/keys-02-a.png)

3. De sleutel bevindt zich in de sectie **Hostsleutels**.

    ![Schermopname waarin de sectie Hostsleutels is gemarkeerd.](./media/import-function-app-as-api/keys-02-b.png)

### <a name="access-the-named-value-in-azure-api-management"></a>De benoemde waarde in Azure API Management openen

Navigeer naar uw Azure API Management-exemplaar en selecteer **Benoemde waarden** in het menu links. De sleutel van de Azure-functie-app is daar opgeslagen.

![Toevoegen vanuit functie-app](./media/import-function-app-as-api/keys-01.png)

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a>De nieuwe API testen in de Azure Portal

U kunt bewerkingen rechtstreeks vanuit Azure Portal aanroepen. In Azure Portal kunt u de bewerkingen van een API gemakkelijk bekijken en testen.  

1. Selecteer de API die u in de voorgaande sectie hebt gemaakt.

2. Selecteer het tabblad **Testen**.

3. Selecteer een bewerking.

    De pagina geeft velden weer voor queryparameters en velden voor de headers. Een van de headers is **Ocp-Apim-Subscription-Key** voor de abonnementssleutel van het product dat is gekoppeld aan deze API. Als u het API Management-exemplaar hebt gemaakt, bent u al een beheerder en wordt de sleutel dus automatisch ingevuld. 

4. Selecteer **Verzenden**.

    Back-end reageert met **200 OK** en enkele gegevens.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een gepubliceerde API transformeren en beveiligen](transform-api.md)
