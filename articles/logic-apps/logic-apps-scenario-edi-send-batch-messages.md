---
title: EDI-berichten batch verwerken als een groep
description: U kunt EDI-berichten verzenden en ontvangen als batches, groepen of verzamelingen met batch verwerking in Azure Logic Apps
services: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: 6fc0833f70e3e9cd98100f193b52e5a1bfa4d651
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000399"
---
# <a name="exchange-edi-messages-as-batches-or-groups-between-trading-partners-in-azure-logic-apps"></a>EDI-berichten uitwisselen als batches of groepen tussen handels partners in Azure Logic Apps

In Business to Business (B2B)-scenario's wisselen partners vaak berichten uit in groepen of *batches*. Wanneer u een batch-oplossing met Logic Apps bouwt, kunt u berichten verzenden naar handels partners en deze berichten samen in batches verwerken. In dit artikel wordt beschreven hoe u EDI-berichten kunt verwerken met behulp van X12 als voor beeld door een logische app voor het maken van een batch-Sender en een logische receiver-toepassing. 

Batch verwerking van X12-berichten werkt zoals bij batch verwerking van andere berichten. u gebruikt een batch trigger waarmee berichten worden verzameld in een batch en een batch-actie die berichten naar de batch verzendt. Daarnaast bevat X12 batch verwerking een X12-coderings stap voordat de berichten naar de handels partner of een andere bestemming gaan. Zie [Batch Process-berichten](../logic-apps/logic-apps-batch-process-send-receive-messages.md)voor meer informatie over de batch trigger en-actie.

In dit artikel maakt u een batch oplossing door twee Logic apps te maken binnen hetzelfde Azure-abonnement, een Azure-regio en deze specifieke volg orde te volgen:

* Een [' batch receiver '](#receiver) Logic-app, waarmee berichten in een batch worden geaccepteerd en verzameld, totdat aan de opgegeven criteria wordt voldaan om deze berichten op te heffen en te verwerken. In dit scenario codeert de batch-ontvanger ook de berichten in de batch met behulp van de opgegeven X12-overeenkomst of partner identiteiten.

  Zorg ervoor dat u eerst de batch-ontvanger maakt, zodat u later de batch bestemming kunt selecteren wanneer u de batch-afzender maakt.

* Een [' batch Sender '](#sender) Logic-app, waarmee de berichten naar de eerder gemaakte batch-ontvanger worden verzonden. 

Zorg ervoor dat uw batch-ontvanger en batch-afzender hetzelfde Azure-abonnement *en* de Azure-regio delen. Als dat niet het geval is, kunt u de batch-ontvanger niet selecteren wanneer u de batch-afzender maakt, omdat deze niet zichtbaar zijn voor elkaar.

## <a name="prerequisites"></a>Vereisten

Als u dit voor beeld wilt volgen, hebt u de volgende items nodig:

* Een Azure-abonnement. Als u geen abonnement hebt, kunt u [beginnen met een gratis Azure-account](https://azure.microsoft.com/free/). U kunt [zich ook registreren voor een abonnement met betalen per gebruik](https://azure.microsoft.com/pricing/purchase-options/).

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Een bestaand [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) dat is gekoppeld aan uw Azure-abonnement en dat is gekoppeld aan uw Logic apps

* Ten minste twee bestaande [partners](../logic-apps/logic-apps-enterprise-integration-partners.md) in uw integratie account. Elke partner moet de kwalificatie X12 (Standard Carrier Alpha code) gebruiken als een zakelijke identiteit in de eigenschappen van de partner.

* Een bestaande [X12-overeenkomst](../logic-apps/logic-apps-enterprise-integration-x12.md) in uw integratie account

* Als u Visual Studio wilt gebruiken in plaats van de Azure Portal, moet u [Visual Studio instellen voor het werken met Logic apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>X12 batch-ontvanger maken

Voordat u berichten naar een batch kunt verzenden, moet deze batch eerst bestaan als de bestemming waar u deze berichten verzendt. Eerst moet u de Logic-app ' batch-ontvanger ' maken, die begint met de **batch** trigger. Op die manier kunt u tijdens het maken van de logische app Sender de logische app voor de batch-ontvanger selecteren. De batch-ontvanger gaat verder met het verzamelen van berichten totdat aan de opgegeven criteria wordt voldaan om deze berichten te vrijgeven en te verwerken. Terwijl batch-ontvangers niets hoeven te weten over batch afzenders, moeten batch afzenders de bestemming weten waar ze de berichten verzenden. 

Voor deze batch-ontvanger geeft u de batch modus, naam, release criteria, X12 overeenkomst en andere instellingen op. 

1. Maak in de [Azure Portal](https://portal.azure.com) of Visual Studio een logische app met deze naam: "BatchX12Messages"

2. [Koppel uw logische app aan uw integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. Voeg in Logic Apps Designer de **batch** trigger toe, waarmee de werk stroom van de logische app wordt gestart. Voer in het zoekvak ' batch ' in als uw filter. Selecteer deze trigger: **batch-berichten**

   ![Batch trigger toevoegen](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. De eigenschappen van de batch-ontvanger instellen: 

   | Eigenschap | Waarde | Opmerkingen | 
   |----------|-------|-------|
   | **Batch modus** | Inline |  |  
   | **Batch naam** | TestBatch | Alleen beschikbaar in de **inline** batch modus | 
   | **Release criteria** | Aantal berichten op basis van planning | Alleen beschikbaar in de **inline** batch modus | 
   | **Aantal berichten** | 10 | Alleen beschikbaar voor release criteria **op basis van het aantal berichten** | 
   | **Interval** | 10 | Alleen beschikbaar met release criteria **op basis van een planning** | 
   | **Frequentie** | minuut | Alleen beschikbaar met release criteria **op basis van een planning** | 
   ||| 

   ![Details van batch trigger opgeven](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > In dit voor beeld wordt geen partitie voor de batch ingesteld, dus elke batch maakt gebruik van dezelfde partitie sleutel. Zie [Batch Process-berichten](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender)voor meer informatie over partities.

5. Voeg nu een actie toe die elke batch codeert: 

   1. Kies **nieuwe stap** onder de batch trigger.

   2. Voer in het zoekvak ' X12 batch ' in als uw filter en selecteer deze actie (wille keurige versie): **batch code ring <*versie*>-X12** 

      ![X12 batch-coderings actie selecteren](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Als u eerder geen verbinding hebt gemaakt met uw integratie account, maakt u de verbinding nu. Geef een naam op voor de verbinding, selecteer het gewenste integratie account en kies vervolgens **maken**.

      ![Verbinding maken tussen een batch encoder en een integratie account](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Stel deze eigenschappen in voor de batch encoder-actie:

      | Eigenschap | Beschrijving |
      |----------|-------------|
      | **Naam van de X12-overeenkomst** | Open de lijst en selecteer uw bestaande overeenkomst. <p>Als de lijst leeg is, zorgt u ervoor dat u [uw logische app koppelt aan het integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) dat de gewenste overeenkomst heeft. | 
      | **Batch-naam** | Klik in dit vak en wanneer de lijst met dynamische inhoud wordt weer gegeven, selecteert u het token **batch naam** . | 
      | **PartitionName** | Klik in dit vak en selecteer nadat de lijst met dynamische inhoud wordt weer gegeven, het **partitie naam** token. | 
      | **Items** | Sluit het vak item Details en klik vervolgens in dit vak. Nadat de lijst met dynamische inhoud wordt weer gegeven, selecteert u het token **batch-items** . | 
      ||| 

      ![Details batch Codeer actie](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      Voor het vak **items** :

      ![Batch Codeer actie-items](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Sla uw logische app op. 

7. Als u Visual Studio gebruikt, zorg er dan voor dat u [uw logische app-ontvanger logica implementeert in azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Anders kunt u de batch-ontvanger niet selecteren wanneer u de batch-afzender maakt.

### <a name="test-your-logic-app"></a>Uw logische app testen

Als u er zeker van wilt zijn dat uw batch-ontvanger werkt zoals verwacht, kunt u een HTTP-actie voor test doeleinden toevoegen en een batch bericht verzenden naar de aanvraag van de [bin-service](https://requestbin.com/). 

1. Kies **nieuwe stap** onder de actie X12 coderen. 

2. Voer in het zoekvak ' http ' in als uw filter. Selecteer deze actie: **http-http**
    
   ![HTTP-actie selecteren](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Stel de eigenschappen voor de HTTP-actie in:

   | Eigenschap | Beschrijving | 
   |----------|-------------|
   | **Methode** | Selecteer in deze lijst de optie **post**. | 
   | **Uri** | Genereer een URI voor uw aanvraag-bak en voer deze URI in dit vak in. | 
   | **Hoofdtekst** | Klik in dit vak en nadat de lijst met dynamische inhoud wordt geopend, selecteert u het token **Body** , dat wordt weer gegeven in de sectie **batch-code ring op overeenkomst naam**. <p>Als u het token **Body** niet ziet, klikt u naast **batch-code ring op overeenkomst naam** op **meer weer geven**. | 
   ||| 

   ![Details over HTTP-acties opgeven](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Sla uw logische app op. 

   Uw batch-ontvanger logische app ziet eruit als in dit voor beeld: 

   ![Sla de logische app voor de ontvanger op](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>X12 batch-afzender maken

Maak nu een of meer logische apps die berichten verzenden naar de logische app-ontvanger. In elke batch-verzender kunt u de logische app voor de batch-ontvanger en de batch naam, de bericht inhoud en andere instellingen opgeven. U kunt eventueel een unieke partitie sleutel opgeven om de batch te verdelen in subsets om berichten met die sleutel te verzamelen. 

* Zorg ervoor dat u [uw batch-ontvanger](#receiver) al hebt gemaakt, dus wanneer u uw batch-afzender maakt, kunt u de bestaande batch-ontvanger als de doel batch selecteren. Terwijl batch-ontvangers niets hoeven te weten over batch afzenders, moeten batch afzenders weten waar ze berichten moeten verzenden. 

* Zorg ervoor dat uw batch-ontvanger en batch-Sender dezelfde Azure-regio *en* hetzelfde Azure-abonnement delen. Als dat niet het geval is, kunt u de batch-ontvanger niet selecteren wanneer u de batch-afzender maakt, omdat deze niet zichtbaar zijn voor elkaar.

1. Maak een andere logische app met deze naam: "SendX12MessagesToBatch" 

2. Typ in het zoekvak ' wanneer een HTTP-aanvraag ' als uw filter. Selecteer deze trigger: **Wanneer een HTTP-aanvraag wordt ontvangen** 
   
   ![De aanvraag trigger toevoegen](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Een actie toevoegen voor het verzenden van berichten naar een batch.

   1. Kies **nieuwe stap** onder de actie HTTP-aanvraag.

   2. Voer in het zoekvak ' batch ' in als uw filter. 
   Selecteer de lijst **acties** en selecteer deze actie: **een Logic apps werk stroom kiezen met batch trigger: berichten verzenden naar batch**

      ![Selecteer een Logic Apps werk stroom kiezen met batch trigger](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Selecteer nu de logische app ' BatchX12Messages ' die u eerder hebt gemaakt.

      ![De logische app voor de batch-ontvanger selecteren](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Selecteer deze actie: **Batch_messages-<*uw-batch-ontvanger* >**

      ![Selecteer de actie Batch_messages](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. De eigenschappen van de batch Sender instellen.

   | Eigenschap | Beschrijving | 
   |----------|-------------| 
   | **Batch naam** | De batch naam die is gedefinieerd door de logische app voor de ontvanger (TestBatch) in dit voor beeld <p>**Belang rijk**: de batch naam wordt gevalideerd tijdens runtime en moet overeenkomen met de naam die is opgegeven door de logische app van de ontvanger. Als u de batch naam wijzigt, mislukt de batch Sender. | 
   | **Bericht inhoud** | De inhoud van het bericht dat u wilt verzenden, dat het **hoofd** token in dit voor beeld is | 
   ||| 
   
   ![Batch eigenschappen instellen](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Sla uw logische app op. 

   De logische app voor het verzenden van batches ziet er als volgt uit:

   ![De logische app voor het verzenden van uw batch opslaan](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Uw logische Apps testen

Als u de oplossing voor batch verwerking wilt testen, plaatst u X12-berichten in uw logische app voor het verzenden van uw batch van [postman](https://www.getpostman.com/postman) of een vergelijkbaar hulp programma. Binnenkort begint u met het ophalen van X12-berichten in uw aanvraag opslaglocatie, ofwel elke 10 minuten of in batches van 10, allemaal met dezelfde partitie sleutel.

## <a name="next-steps"></a>Volgende stappen

* [Berichten verwerken als batches](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
