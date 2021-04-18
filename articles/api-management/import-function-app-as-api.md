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
ms.date: 04/16/2021
ms.author: apimpm
ms.openlocfilehash: 8e8047fc6865bab8f4aac2315b269bf7526b1e42
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599285"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Een Azure-functie-app als API importeren in Azure API Management

Azure API Management biedt ondersteuning voor het importeren van Azure-functie-apps als nieuwe API's of het toevoegen hiervan aan bestaande API's. Er wordt automatisch een hostsleutel gegenereerd in de Azure-functie-app, die vervolgens wordt toegewezen aan een benoemde waarde in Azure API Management.

In dit artikel wordt beschreven hoe u een Azure-functie-app als API importeert en test in Azure API Management. 

U leert het volgende:

> [!div class="checklist"]
> * Een Azure-functie-app als API importeren
> * Een Azure-functie-app toevoegen aan een API
> * De nieuwe hostsleutel van de Azure-functie-app en de benoemde waarde van Azure API Management weergeven
> * De API testen in Azure Portal

## <a name="prerequisites"></a>Vereisten

* Voltooi de [quickstart Een Azure API Management-exemplaar](get-started-create-service-instance.md) maken.
* Controleer of er een Azure Functions-app in uw abonnement aanwezig is. Zie [Een Azure-functie-app maken](../azure-functions/functions-get-started.md) voor meer informatie. Voor functies moeten http-triggers en autorisatieniveau zijn ingesteld *op Anoniem* of *Functie.*

> [!NOTE]
> U kunt de extensie API Management voor Visual Studio Code gebruiken om uw API's te importeren en te beheren. Volg de [zelfstudie API Management-extensie om](visual-studio-code-tutorial.md) te installeren en aan de slag te gaan.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a> Een Azure-functie-app als nieuwe API importeren

Voer de volgende stappen uit om een nieuwe API te maken vanuit een Azure-functie-app.

1. Navigeer naar uw API Management-service in de Azure Portal en selecteer **API's** in het menu.

2. Selecteer **Functie-app** in de lijst **Een nieuwe API toevoegen**.

    :::image type="content" source="./media/import-function-app-as-api/add-01.png" alt-text="Schermopname van de tegel Functie-app.":::

3. Klik op **Bladeren** om functies voor het importeren te selecteren.

    :::image type="content" source="./media/import-function-app-as-api/add-02.png" alt-text="Schermopname met de knop Bladeren gemarkeerd.":::

4. Klik op de sectie **Functie-app** om te kiezen uit de lijst met beschikbare functie-apps.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="Schermopname met het gedeelte Functie-app gemarkeerd.":::

5. Zoek de functie-app waarvan u functies wilt importeren, klik erop en druk op **Selecteren**.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="Schermopname die de Functie-app waaruit u functies wilt importeren en de knop Selecteren markeert.":::

6. Selecteer de functies die u wilt importeren en klik op **Selecteren**.
    * U kunt functies alleen importeren op basis van EEN HTTP-trigger met *anonieme* of *functieautorisatieniveaus.*

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="Schermopname die de te importeren functies en de knop Selecteren markeert.":::

7. Schakel over naar de **volledige** weergave en wijs **Product** toe aan uw nieuwe API. 
8. Geef indien nodig andere velden op tijdens het maken of configureer ze later via het **tabblad** Instellingen. 
    * De instellingen worden beschreven in de zelfstudie [Uw eerste API importeren en publiceren](import-and-publish.md#import-and-publish-a-backend-api).

    >[!NOTE]
    > Producten zijn associaties van een of meer API's die aan ontwikkelaars worden aangeboden via de ontwikkelaarsportal. Eerst moeten ontwikkelaars zich abonneren op een product om toegang te krijgen tot de API. Zodra ze zich hebben geabonneerd, krijgen ze een abonnementssleutel voor elke API in dat product. Als maker van het API Management bent u een beheerder en bent u standaard geabonneerd op elk product.
    >
    > Elk API Management wordt geleverd met twee standaardvoorbeeldproducten:
    > - **Starter**
    > - **Onbeperkt**

9. Klik op **Create**.

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a> Een Azure-functie-app toevoegen aan een bestaande API

Voer de volgende stappen uit om de Azure-functie-app toe te voegen aan een bestaande API.

1. Selecteer in uw **Azure API Management**-service-exemplaar de optie **API's** in het menu links.

2. Kies een API waarin u een Azure-functie-app wilt importeren. Klik op **...** en selecteer **Importeren** in het contextmenu.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-1.png" alt-text="Schermopname die de optie Menu importeren markeert.":::

3. Klik op de tegel **Functie-app**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-2.png" alt-text="Schermopname met de tegel Functie-app gemarkeerd.":::

4. Klik in het pop-upvenster op **Bladeren**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-3.png" alt-text="Schermopname waarin de knop Bladeren wordt weergegeven.":::

5. Klik op de sectie **Functie-app** om te kiezen uit de lijst met beschikbare functie-apps.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="Schermopname met de lijst met Functie-apps gemarkeerd.":::

6. Zoek de functie-app waarvan u functies wilt importeren, klik erop en druk op **Selecteren**.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="Schermopname die de Functie-app waaruit u functies wilt importeren markeert.":::

7. Selecteer de functies die u wilt importeren en klik op **Selecteren**.

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="Schermopname met de functies die u wilt importeren.":::

8. Klik op **Import**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-4.png" alt-text="Toevoegen vanuit functie-app":::

## <a name="authorization"></a><a name="authorization"></a> Autorisatie

Bij het importeren van een Azure-functie-app wordt automatisch het volgende gegenereerd:

* Hostsleutel binnen de functie-app met de naam apim-{*de naam van uw Azure API Management service-exemplaar*},
* de benoemde waarde binnen het Azure API Management-exemplaar met de naam {*de naam van uw exemplaar van Azure-functie-app*}-key, die de gemaakte hostsleutel bevat.

Voor Api's die na 4 april 2019 zijn gemaakt, wordt de hostsleutel in HTTP-aanvragen van API Management aan de functie-app in een header doorgegeven. Oudere API's geven de hostsleutel als [een query parameter](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization). U kunt dit gedrag wijzigen via de REST API `PATCH Backend` [aanroep](/rest/api/apimanagement/2019-12-01/backend/update#backendcredentialscontract) van de *back-end-entiteit* die is gekoppeld aan de functie-app.

> [!WARNING]
> Als u de waarde van de hostsleutel van de Azure-functie-app of de benoemde waarde van de Azure-API Management app verwijdert of verandert, wordt de communicatie tussen de services breekt. De waarden worden niet automatisch gesynchroniseerd.
>
> Als u de hostsleutel wilt verwisselen, moet u de benoemde waarde ook wijzigen in Azure API Management.

### <a name="access-azure-function-app-host-key"></a>De hostsleutel van de Azure-functie-app openen

1. Navigeer naar uw exemplaar van Azure-functie-app.

    :::image type="content" source="./media/import-function-app-as-api/keys-01.png" alt-text="Schermopname van het selecteren van uw function-app-exemplaar.":::

2. Selecteer **app-sleutels** in de sectie Functies van het navigatiemenu **aan de zijkant.**

    :::image type="content" source="./media/import-function-app-as-api/keys-02b.png" alt-text="Schermopname met optie Functie-appsinstellingen gemarkeerd.":::

3. Zoek de sleutels in de **sectie Hostsleutels.**

    :::image type="content" source="./media/import-function-app-as-api/keys-03.png" alt-text="Schermopname waarin de sectie Hostsleutels is gemarkeerd.":::

### <a name="access-the-named-value-in-azure-api-management"></a>De benoemde waarde in Azure API Management openen

Navigeer naar uw Azure API Management-exemplaar en selecteer **Benoemde waarden** in het menu links. De sleutel van de Azure-functie-app is daar opgeslagen.

:::image type="content" source="./media/import-function-app-as-api/api-named-value.png" alt-text="Toevoegen vanuit functie-app":::

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a>De nieuwe API testen in de Azure Portal

U kunt bewerkingen rechtstreeks vanuit Azure Portal aanroepen. In Azure Portal kunt u de bewerkingen van een API gemakkelijk bekijken en testen.  

:::image type="content" source="./media/import-function-app-as-api/test-api.png" alt-text="Schermopname met de testprocedure.":::

1. Selecteer de API die u in de voorgaande sectie hebt gemaakt.

2. Selecteer het tabblad **Testen**.

3. Selecteer de bewerking die u wilt testen.

    * Op de pagina worden velden weergegeven voor queryparameters en headers. 
    * Een van de headers is Ocp-Apim-Subscription-Key voor de productabonnementssleutel die aan deze API is gekoppeld. 
    * Als maker van het API Management bent u al een beheerder, dus de sleutel wordt automatisch ingevuld. 

4. Selecteer **Verzenden**.

    * Wanneer de test is geslaagd, reageert de back-end met **200 OK** en enkele gegevens.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een gepubliceerde API transformeren en beveiligen](transform-api.md)
