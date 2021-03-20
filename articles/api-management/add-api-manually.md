---
title: Een API handmatig toevoegen met Azure Portal | Microsoft Docs
description: Deze zelfstudie laat u zien hoe u een API Management (APIM) moet gebruiken om handmatig een API toe te voegen.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 39a3b9d7dd9efbda93de0b5d7c5f9938922d0012
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96183816"
---
# <a name="add-an-api-manually"></a>Handmatig een API toevoegen

In dit artikel wordt uitgelegd hoe u handmatig een API moet toevoegen aan het exemplaar van API Management (APIM) met Azure Portal. Een veelvoorkomend scenario wanneer u een lege API wilt maken en het handmatig wilt definiëren om een gesimuleerde API te maken. Zie voor meer informatie over het simuleren van een API [Gesimuleerde API-antwoorden](mock-api-responses.md).

Als u een bestaande API wilt importeren, zie de sectie [Verwante onderwerpen](#related-topics).

In dit artikel maken we een lege API en geven [httpbin.org](https://httpbin.org) (een openbare testservice) op als back-end-API.

## <a name="prerequisites"></a>Vereisten

Voltooi de volgende quickstart: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>Een API maken

1. Navigeer naar uw API Management-service in de Azure Portal en selecteer **API's** in het menu.
2. Selecteer **+ API toevoegen** in het linkermenu.
3. Selecteer **Lege API** uit de lijst.  
    ![Lege API](media/add-api-manually/blank-api.png)  
4. Voer de instellingen voor de API in. De instellingen worden beschreven in de zelfstudie [Uw eerste API importeren en publiceren](import-and-publish.md#import-and-publish-a-backend-api).
5. Selecteer **Maken**.

U hebt op dit moment geen bewerkingen in API Management die zijn toegewezen aan de bewerkingen in uw back-end-API. Als u een bewerking aanroept die beschikbaar is gesteld via de back-end maar niet via de API Management, krijgt u een **404**.

>[!NOTE] 
> Wanneer u een API toevoegt, zal APIM, zelfs als deze is verbonden met een back-endservice, standaard geen bewerkingen tonen totdat u deze toestaat. Om een werking van uw back-end-service mogelijk te maken, maakt u een APIM-bewerking die is toegewezen aan de back-end-bewerking.

## <a name="add-and-test-an-operation"></a>Toevoegen en testen van een bewerking

In deze sectie wordt beschreven hoe u een bewerking "/get" toevoegt om ze te kunnen toewijzen aan de back-end-bewerking "http://httpbin.org/get".

### <a name="add-an-operation"></a>Een bewerking toevoegen

1. Selecteer de API die u in de vorige stap hebt gemaakt.
2. Klik op **+ Bewerking toevoegen**.
3. Selecteer **GET** in de **URL** en voer "*/get*" in de resource in.
4. Voer "*FetchData*" in als **Weergavenaam**.
5. Selecteer **Opslaan**.

### <a name="test-an-operation"></a>Een bewerking testen

Test de functie in Azure Portal. U kunt het ook testen in de **Ontwikkelaarsportal**.

1. Selecteer het tabblad **Testen**.
2. Selecteer **FetchData**.
3. Druk op **Verzenden**.

Het antwoord dat de "http://httpbin.org/get"-bewerking genereert, wordt weergegeven. Als u uw bewerkingen wilt transformeren, gaat u naar [Uw API transformeren en beschermen](transform-api.md).

## <a name="add-and-test-a-parameterized-operation"></a>Een bewerking met parameters toevoegen en testen

In deze sectie wordt beschreven hoe u een bewerking toevoegt die een parameter heeft. In dit geval, wijst u de bewerking toe aan "http://httpbin.org/status/200".

### <a name="add-the-operation"></a>Voeg de bewerking toe

1. Selecteer de API die u in de vorige stap hebt gemaakt.
2. Klik op **+ Bewerking toevoegen**.
3. Selecteer **GET** in de **URL** en voer "*/status/{code}*" in de resource in. Desgewenst kunt u sommige gegevens die zijn gekoppeld aan deze parameter opgeven. Voer bijvoorbeeld "*getal*" in bij **TYPE**, "*200*" (standaard) bij **VALUES**.
4. Voer "GetStatus" in bij **Weergavenaam**.
5. Selecteer **Opslaan**.

### <a name="test-the-operation"></a>Test de bewerking 

Test de functie in Azure Portal.  U kunt het ook testen in de **Ontwikkelaarsportal**.

1. Selecteer het tabblad **Testen**.
2. Selecteer **GetStatus**. De waarde is standaard ingesteld op "*200*". U kunt deze wijzigen als u andere waarden wilt testen. Typ bijvoorbeeld "*418*".
3. Druk op **Verzenden**.

    Het antwoord dat de "http://httpbin.org/status/200"-bewerking genereert, wordt weergegeven. Als u uw bewerkingen wilt transformeren, gaat u naar [Uw API transformeren en beschermen](transform-api.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een gepubliceerde API transformeren en beveiligen](transform-api.md)
