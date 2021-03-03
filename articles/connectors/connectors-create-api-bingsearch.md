---
title: Verbinding maken met Bing Search
description: Taken en werk stromen automatiseren die resultaten in Bing Search vinden met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: 306298e4338665ef52add7f46d6da8675c97c3e2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716547"
---
# <a name="find-results-in-bing-search-by-using-azure-logic-apps"></a>Resultaten zoeken in Bing Search met behulp van Azure Logic Apps

In dit artikel wordt beschreven hoe u nieuws, Video's en andere items kunt vinden via Bing Search vanuit een logische app met de Bing Search-connector. Op die manier kunt u logische apps maken die taken en werk stromen automatiseren voor het verwerken van zoek resultaten en deze items beschikbaar maken voor andere acties. 

U kunt bijvoorbeeld nieuws items vinden op basis van zoek criteria, en Twitter deze items als tweets in uw Twitter-feed plaatsen.

Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps](../logic-apps/logic-apps-overview.md) en [Quick Start: uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Zie de naslag informatie over de [Bing Search-connector](/connectors/bingsearch/)voor connector-specifieke technische gegevens.

## <a name="prerequisites"></a>Vereisten

* Een [Cognitive Services-account](../cognitive-services/cognitive-services-apis-create-account.md)

* Een [Bing Search-API-sleutel](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), waarmee u vanuit uw logische app toegang kunt krijgen tot de Bing zoeken-API's

* De logische app waar u toegang wilt krijgen tot uw event hub. Als u uw logische app wilt starten met een Bing Search trigger, hebt u een [lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)nodig.

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Een Bing Search trigger toevoegen

In Azure Logic Apps moet elke logische app beginnen met een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), die wordt geactiveerd wanneer een bepaalde gebeurtenis plaatsvindt of wanneer aan een bepaalde voor waarde wordt voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt de Logic Apps-Engine een exemplaar van een logische app en begint de werk stroom van uw app uit te voeren.

1. Maak in de Azure Portal of Visual Studio een lege logische app, waarmee u de ontwerp functie voor logische apps kunt openen. In dit voor beeld wordt de Azure Portal gebruikt.

2. Voer in het zoekvak ' Bing Search ' in als uw filter. Selecteer in de lijst triggers de gewenste trigger.

   In dit voor beeld wordt deze trigger gebruikt: **Bing Search nieuw nieuws artikel**

   ![Bing Search trigger zoeken](./media/connectors-create-api-bing-search/add-trigger.png)

3. Als u wordt gevraagd om de verbindings gegevens, [maakt u de Bing Search verbinding nu](#create-connection).
Als uw verbinding al bestaat, geeft u de benodigde informatie op voor de trigger.

   Voor dit voor beeld geeft u criteria op voor het retour neren van overeenkomende nieuws artikelen van Bing Search.

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | Zoek query | Ja | <*Zoek woorden*> | Voer de zoek woorden in die u wilt gebruiken. |
   | Markt | Ja | <*instelling*> | De land instellingen voor zoeken. De standaard instelling is "en-US", maar u kunt een andere waarde selecteren. |
   | Veilig zoeken | Ja | <*Zoek niveau*> | Het filter niveau voor het uitsluiten van inhoud voor volwassenen. De standaard waarde is ' matig ', maar u selecteert een ander niveau. |
   | Count | Nee | <*resultaten-aantal*> | Retourneert het opgegeven aantal resultaten. De standaard instelling is 20, maar u kunt een andere waarde opgeven. Het werkelijke aantal geretourneerde resultaten kan kleiner zijn dan het opgegeven aantal. |
   | Offset | Nee | <*Skip-waarde*> | Het aantal resultaten dat moet worden overgeslagen voordat resultaten worden geretourneerd |
   |||||

   Bijvoorbeeld:

   ![Trigger instellen](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Selecteer het interval en de frequentie voor het aantal keren dat u wilt dat de trigger controleert op resultaten.

5. Selecteer **Opslaan** op de werkbalk van de ontwerper wanneer u klaar bent.

6. Ga nu verder met het toevoegen van een of meer acties aan uw logische app voor de taken die u wilt uitvoeren met de trigger resultaten.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Een Bing Search actie toevoegen

In Azure Logic Apps is een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) een stap in uw werk stroom die volgt op een trigger of een andere actie. Voor dit voor beeld begint de logische app met een Bing Search trigger waarmee nieuws artikelen worden geretourneerd die overeenkomen met de opgegeven criteria.

1. Open in de Azure Portal of Visual Studio uw logische app in de ontwerp functie voor logische apps. In dit voor beeld wordt de Azure Portal gebruikt.

2. Onder de trigger of actie selecteert u **nieuwe stap**  >  **een actie toevoegen**.

   In dit voor beeld wordt deze trigger gebruikt:

   **Bing Search-op nieuw nieuws artikel**

   ![Actie toevoegen](./media/connectors-create-api-bing-search/add-action.png)

   Als u een actie wilt toevoegen tussen de bestaande stappen, plaatst u de muis aanwijzer op de verbindings pijl. 
   Selecteer het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

3. Voer in het zoekvak ' Bing Search ' in als uw filter.
Selecteer in de lijst acties de gewenste actie.

   In dit voor beeld wordt deze actie gebruikt:

   **Bing Search-Nieuws lijst per query**

   ![Bing Search actie zoeken](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Als u wordt gevraagd om de verbindings gegevens, [maakt u de Bing Search verbinding nu](#create-connection). Als uw verbinding al bestaat, geeft u de benodigde informatie op voor de actie.

   Voor dit voor beeld geeft u de criteria op voor het retour neren van een subset van de resultaten van de trigger.

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | Zoek query | Ja | <*Zoek expressie*> | Voer een expressie in voor het uitvoeren van query's op de trigger resultaten. U kunt kiezen uit de velden in de lijst met dynamische inhoud of een expressie maken met de opbouw functie voor expressies. |
   | Markt | Ja | <*instelling*> | De land instellingen voor zoeken. De standaard instelling is "en-US", maar u kunt een andere waarde selecteren. |
   | Veilig zoeken | Ja | <*Zoek niveau*> | Het filter niveau voor het uitsluiten van inhoud voor volwassenen. De standaard waarde is ' matig ', maar u selecteert een ander niveau. |
   | Count | Nee | <*resultaten-aantal*> | Retourneert het opgegeven aantal resultaten. De standaard instelling is 20, maar u kunt een andere waarde opgeven. Het werkelijke aantal geretourneerde resultaten kan kleiner zijn dan het opgegeven aantal. |
   | Offset | Nee | <*Skip-waarde*> | Het aantal resultaten dat moet worden overgeslagen voordat resultaten worden geretourneerd |
   |||||

   Stel dat u wilt dat de resultaten waarvan de categorie naam het woord ' Tech ' bevat.

   1. Klik in het vak **Zoek opdracht** , zodat de lijst met dynamische inhoud wordt weer gegeven. 
   Selecteer in de lijst **expressie** , zodat de opbouw functie voor expressies wordt weer gegeven. 

      ![Bing Search trigger](./media/connectors-create-api-bing-search/bing-search-action.png)

      U kunt nu beginnen met het maken van de expressie.

   2. Selecteer in de lijst functies de functie **contains ()** , die vervolgens wordt weer gegeven in het vak expressie. Klik op **dynamische inhoud** zodat de lijst met velden opnieuw wordt weer gegeven, maar zorg ervoor dat de cursor binnen de haakjes blijft.

      ![Selecteer een functie](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. Selecteer in de lijst met velden de **categorie** die wordt omgezet in een para meter. 
   Voeg een komma toe na de eerste para meter en Voeg na de komma het volgende woord toe: `'tech'` 

      ![Selecteer een veld](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. Wanneer u gereed bent, selecteert u **OK**.

      De expressie wordt nu weer gegeven in het vak **Zoek opdracht** in deze indeling:

      ![Voltooide expressie](./media/connectors-create-api-bing-search/resolved-expression.png)

      In de code weergave wordt deze expressie weer gegeven in de volgende indeling:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Selecteer **Opslaan** op de werkbalk van de ontwerper wanneer u klaar bent.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Verbinding maken met Bing Search

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Wanneer u wordt gevraagd om de verbindings gegevens, geeft u de volgende gegevens op:

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | Verbindingsnaam | Ja | <*verbindings naam*> | De naam die voor uw verbinding moet worden gemaakt |
   | API-versie | Ja | <*API-versie*> | De Bing Search API-versie is standaard ingesteld op de huidige versie. U kunt indien nodig een eerdere versie selecteren. |
   | API-sleutel | Ja | <*API-sleutel*> | De Bing Search-API-sleutel die u eerder hebt verkregen. Als u geen sleutel hebt, kunt u [nu uw API-sleutel](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api)ophalen. |  
   |||||  

   Bijvoorbeeld:

   ![Verbinding maken](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Selecteer **Maken** als u klaar bent.

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentie pagina van de connector](/connectors/bingsearch/)voor technische details, zoals triggers, acties en limieten, zoals beschreven in het Swagger-bestand van de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)

