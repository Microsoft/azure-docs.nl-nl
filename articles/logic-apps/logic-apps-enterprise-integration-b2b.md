---
title: Exchange-berichten voor B2B-scenario's voor bedrijfs integratie
description: B2B-berichten ontvangen en verzenden tussen handels partners in Azure Logic Apps met behulp van de Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/02/2020
ms.openlocfilehash: e16cc8934407a5c54c84fd045c99e28116e656c9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93310464"
---
# <a name="receive-and-confirm--b2b-as2-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>B2B AS2-berichten ontvangen en bevestigen met behulp van Azure Logic Apps en Enterprise Integration Pack

Wanneer u een integratie account hebt dat handels partners en overeenkomsten definieert, kunt u een geautomatiseerde business to Business-werk stroom (B2B) maken waarmee berichten tussen handels partners worden uitgewisseld met behulp van [Azure Logic apps](../logic-apps/logic-apps-overview.md) met de [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). Azure Logic Apps werkt met connectors die ondersteuning bieden voor AS2-, X12-, EDIFACT-en RosettaNet-industrie standaard protocollen. U kunt deze connectors ook combi neren met andere [connectors die beschikbaar zijn in Logic apps](../connectors/apis-list.md), bijvoorbeeld Sales Force en Office 365 Outlook.

In dit artikel wordt beschreven hoe u een logische app maakt waarmee een HTTP-aanvraag wordt ontvangen met behulp van een aanvraag trigger, de bericht inhoud decodeert met behulp van de AS2-en X12-acties en vervolgens een antwoord retourneert met behulp van de reactie actie.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, kunt u [zich aanmelden voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een lege logische app, zodat u de B2B-werk stroom kunt maken met behulp van de [aanvraag](../connectors/connectors-native-reqres.md) trigger die wordt gevolgd door de volgende acties:

  * [AS2 decoderen](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [Voor waarde](../logic-apps/logic-apps-control-flow-conditional-statement.md)waarmee een [antwoord](../connectors/connectors-native-reqres.md) wordt verzonden op basis van het feit of de AS2-decoderings actie slaagt of mislukt

  * [X12-bericht decoderen](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md) en [Quick Start: uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Een [integratie account](./logic-apps-enterprise-integration-create-integration-account.md) dat is gekoppeld aan uw Azure-abonnement en is gekoppeld aan uw logische app. De logische app en het integratie account moeten zich op dezelfde locatie of Azure-regio bevinden.

* Ten minste twee [handels partners](../logic-apps/logic-apps-enterprise-integration-partners.md) die u al hebt gedefinieerd in uw integratie account samen met [AS2-en X12-overeenkomsten](logic-apps-enterprise-integration-agreements.md) voor deze partners.

## <a name="add-the-request-trigger"></a>De aanvraag trigger toevoegen

In dit voor beeld wordt de Logic app designer in de Azure Portal gebruikt, maar u kunt vergelijk bare stappen volgen voor de ontwerp functie voor logische apps in Visual Studio.

1. Open in de [Azure Portal](https://portal.azure.com)uw lege logische app in de ontwerp functie voor logische apps.

1. Voer in het zoekvak in `when a http request` en selecteer **Wanneer een HTTP-aanvraag wordt ontvangen** om te gebruiken als de trigger.

   ![Selecteer trigger aanvragen om de werk stroom van uw logische app te starten](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. Laat het vak **JSON-schema van aanvraag tekst** leeg, omdat het X12-bericht een plat bestand is.

   ![Laat het JSON-schema van de aanvraag tekst leeg](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. Selecteer **Opslaan** op de werkbalk van de ontwerper wanneer u klaar bent.

   Met deze stap wordt de **http post-URL** gegenereerd voor het verzenden van de aanvraag die de logische app activeert. Als u deze URL wilt kopiëren, selecteert u het Kopieer pictogram naast de URL.

   ![URL gegenereerd voor trigger voor aanvragen om oproepen te ontvangen](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-the-as2-decode-action"></a>De AS2-decoderings actie toevoegen

Voeg nu de B2B-acties toe die u wilt gebruiken. In dit voor beeld worden AS2-en X12-acties gebruikt.

1. Selecteer onder de trigger de optie **Nieuwe stap**. Als u de details van de trigger wilt verbergen, klikt u op de titel balk van de trigger.

   ![Nog een stap toevoegen aan de werk stroom van uw logische app](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. Onder **Kies een actie**, typt u in het zoekvak `as2 decode` en selecteert u **AS2 decode (v2)**.

   ![Zoek en selecteer ' AS2 decode (v2) '](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. Voer voor het **bericht om de eigenschap te decoderen** de invoer in die door de AS2-actie moet worden gedecodeerd. Dit is de `body` inhoud die wordt ontvangen door de HTTP-aanvraag trigger. U kunt deze inhoud op verschillende manieren opgeven als invoer, hetzij uit de lijst met dynamische inhoud hetzij als een expressie:

   * Als u wilt selecteren uit een lijst met de beschik bare trigger uitvoer, klikt u in het vak **bericht om te decoderen** . Nadat de lijst met dynamische inhoud wordt weer gegeven, selecteert u onder **Wanneer een HTTP-aanvraag wordt ontvangen** de waarde van de eigenschap **Body** , bijvoorbeeld:

     ![Selecteer de waarde body van de trigger](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * Als u een expressie wilt invoeren die naar de uitvoer van de trigger verwijst `body` , klikt u in het vak **bericht om te decoderen** . Nadat de lijst met dynamische inhoud wordt weer gegeven, selecteert u **expressie**. Voer in de expressie-editor hier de expressie in en selecteer **OK**:

     `triggerOutputs()['body']`

     Of Voer in het vak **te decoderen bericht** direct deze expressie in:

     `@triggerBody()`

     De expressie wordt omgezet in het **Body** -token.

     ![Uitvoer van opgeloste hoofd tekst van trigger](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. Voer voor de eigenschap **bericht koppen** eventuele headers in die vereist zijn voor de AS2-actie, die worden beschreven door de `headers` inhoud die wordt ontvangen door de HTTP-aanvraag trigger.

   1. Als u een expressie wilt invoeren die naar de uitvoer van de trigger verwijst `headers` , selecteert u **bericht headers naar tekst modus scha kelen**.

      ![Scherm afbeelding met de optie "bericht koppen naar tekst modus" geselecteerd.](./media/logic-apps-enterprise-integration-b2b/as2-decode-switch-text-mode.png)

   1. Klik in het vak **bericht headers** . Nadat de lijst met dynamische inhoud wordt weer gegeven, selecteert u **expressie**. Voer in de expressie-editor hier de expressie in en selecteer **OK**:

      `triggerOutputs()['Headers']`

      In de AS2-decoderings actie wordt de expressie nu weer gegeven als een token:

      ![Scherm afbeelding met het @triggerOutputs token ' () [' headers '] ' in het vak ' bericht headers '.](./media/logic-apps-enterprise-integration-b2b/as2-decode-message-header-expression.png)

   1. Om het expressie token op te halen dat moet worden omgezet in het token **headers** , schakelt u tussen de ontwerp functie en de code weergave. Na deze stap ziet de decoderings actie AS2 er als volgt uit:

      ![Uitvoer van verwijderde headers van trigger](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>Reactie actie voor melding van bericht ontvangst toevoegen

Als u de handels partner wilt melden dat het bericht is ontvangen, kunt u een antwoord retour neren dat een AS2 (MDN) bevat met behulp van de **reactie** actie. Als u deze actie onmiddellijk na de **AS2-Decoderings** actie toevoegt, moet de logische app de verwerking niet voortzetten als deze actie mislukt.

1. Selecteer **nieuwe stap** onder de actie **AS2 decoderen** .

1. Onder **Kies een actie** selecteert u in het zoekvak **ingebouwde**. Typ `condition` in het zoekvak. Selecteer in de lijst **Acties** de optie **Voorwaarde**.

   ![De actie ' voor waarde ' toevoegen](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   De vorm van de voor waarde wordt nu weer gegeven, met inbegrip van de paden voor of aan de voor waarde wordt voldaan of niet.

   ![Scherm afbeelding toont de voorwaarde vorm met lege paden.](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. Geef nu de voor waarde op die moet worden geëvalueerd. Voer in het vak **een waarde kiezen** de volgende expressie in:

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   Controleer in het middelste vak of de vergelijkings bewerking is ingesteld op `is equal to` . Voer in het vak aan de rechter kant de waarde in `Expected` . Als u de expressie wilt ophalen die als dit token moet worden omgezet, schakelt u tussen de ontwerp functie en de code weergave.

   ![Scherm afbeelding toont de voor waarde waaraan een voor waarde is toegevoegd.](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. Geef nu de antwoorden op om te retour neren of de **AS2-Decoderings** actie slaagt of niet.

   1. Selecteer **een actie toevoegen** **voor het geval** dat de **AS2-decoderings** actie slaagt. Onder **Kies een actie**, typt u in het zoekvak `response` en selecteert u **antwoord**.

      ![Zoek en selecteer de actie ' reactie '](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. Geef de volgende expressies op om toegang te krijgen tot de AS2-MDN van de uitvoer van de **AS2 Decoderings** actie:

      * Voer in de eigenschap **headers** van de **reactie** actie deze expressie in:

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * Voer in de eigenschap **Body** van de **reactie** actie deze expressie in:

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. Als u de expressies wilt ophalen die als tokens moeten worden omgezet, schakelt u tussen de ontwerp-en code weergave:

      ![Omgezette expressie voor toegang tot AS2 MDN](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. Als de **AS2-Decoderings** actie mislukt, selecteert u in de shape **indien onwaar** **een actie toevoegen**. Onder **Kies een actie**, typt u in het zoekvak `response` en selecteert u **antwoord**. Stel de **reactie** actie in om de gewenste status en fout te retour neren.

1. Sla uw logische app op.

## <a name="add-decode-x12-message-action"></a>Actie X12-bericht decoderen toevoegen

1. Voeg nu de actie **X12-bericht decoderen** toe. Selecteer **een actie toevoegen** onder de actie **reactie** .

1. Onder **Kies een actie**, typt u in het zoekvak `x12 decode` en selecteert u **X12-bericht decoderen**.

   ![Zoek en selecteer de actie X12-bericht decoderen](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. Als de actie X12 u vraagt om verbindings gegevens, geeft u de naam voor de verbinding op, selecteert u het integratie account dat u wilt gebruiken en selecteert u **maken**.

   ![Een X12-verbinding maken met een integratie account](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. Geef nu de invoer voor de X12-actie op. In dit voor beeld wordt de uitvoer van de AS2-actie gebruikt. Dit is de inhoud van het bericht, maar houd er rekening mee dat deze inhoud in JSON-object indeling is en base64-gecodeerd is. Daarom moet u deze inhoud converteren naar een teken reeks.

   Voer in het vak **X12-bericht om te decoderen** deze expressie in om de AS2-uitvoer te converteren:

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    Als u de expressie wilt ophalen die als dit token moet worden omgezet, schakelt u tussen de ontwerp functie en de code weergave.

    ![Met base64 gecodeerde inhoud converteren naar een teken reeks](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. Sla uw logische app op.

   Als u extra stappen nodig hebt voor deze logische app, bijvoorbeeld om de inhoud van het bericht te decoderen en die inhoud in JSON-object indeling uit te voeren, kunt u door gaan met het bouwen van uw logische app.

U bent nu klaar met het instellen van uw B2B Logic-app. In een echte wereld-app wilt u mogelijk de gedecodeerde X12-gegevens opslaan in een LOB-app (line-of-Business) of een gegevens archief. Zie bijvoorbeeld de volgende artikelen:

* [Verbinding maken met SAP-systemen in Azure Logic Apps](../logic-apps/logic-apps-using-sap-connector.md)
* [SFTP-bestanden bewaken, maken en beheren met behulp van SSH en Azure Logic Apps](../connectors/connectors-sftp-ssh.md)

Als u uw eigen LOB-Apps wilt verbinden en deze Api's wilt gebruiken in uw logische app, kunt u meer acties toevoegen of [aangepaste api's schrijven](../logic-apps/logic-apps-create-api-app.md).

## <a name="next-steps"></a>Volgende stappen

* [Binnenkomende HTTPS-oproepen ontvangen en erop reageren](../connectors/connectors-native-reqres.md)
* [Exchange AS2-berichten voor B2B Enter prise Integration](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Exchange X12-berichten voor B2B Enter prise Integration](../logic-apps/logic-apps-enterprise-integration-x12.md)
* Meer informatie over de [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
