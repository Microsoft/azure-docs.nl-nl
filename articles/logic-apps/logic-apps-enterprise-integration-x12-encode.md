---
title: X12-berichten coderen
description: Valideer EDI en converteer XML-gecodeerde berichten met de X12 Message encoder in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: c87a61dc77e656a1cfe667ce87f852303a0cc486
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74792346"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>X12-berichten in Azure Logic Apps coderen met Enterprise Integration Pack

Met de Encode X12-berichtenconnector kunt u EDI- en partner-specifieke eigenschappen valideren, XML-gecodeerde berichten in EDI-transactiesets converteren en technische bevestiging, functionele bevestiging of beide aanvragen.
Als u deze connector wilt gebruiken, moet u de connector toevoegen aan een bestaande trigger in uw logische app.

## <a name="before-you-start"></a>Voordat u begint

Dit zijn de items die u nodig hebt:

* Een Azure-account; u kunt een [gratis account](https://azure.microsoft.com/free) maken
* Een [integratie account](logic-apps-enterprise-integration-create-integration-account.md) dat al is gedefinieerd en gekoppeld aan uw Azure-abonnement. U moet een integratie account hebben voor het gebruik van de code ring X12 Message connector.
* Ten minste twee [partners](logic-apps-enterprise-integration-partners.md) die al zijn gedefinieerd in uw integratie account
* Een [X12-overeenkomst](logic-apps-enterprise-integration-x12.md) die al is gedefinieerd in uw integratie account

## <a name="encode-x12-messages"></a>X12-berichten coderen

1. [Maak een logische app](quickstart-create-first-logic-app-workflow.md).

2. De code ring X12 Message connector heeft geen triggers. Daarom moet u een trigger toevoegen voor het starten van uw logische app, zoals een aanvraag trigger. Voeg in de ontwerp functie voor logische apps een trigger toe en voeg vervolgens een actie toe aan uw logische app.

3.  Voer in het zoekvak ' X12 ' in voor uw filter. Selecteer **X12-encode to X12-bericht op overeenkomst naam** of **X12-encode to X12-bericht door identiteiten**.
   
    ![Zoeken naar ' X12 '](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Als u eerder geen verbindingen met uw integratie account hebt gemaakt, wordt u gevraagd om die verbinding nu te maken. Geef uw verbinding een naam en selecteer het integratie account dat u wilt verbinden. 
   
    ![verbinding van integratie account](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Eigenschappen met een sterretje zijn vereist.

    | Eigenschap | Details |
    | --- | --- |
    | Verbindings naam * |Voer een naam in voor de verbinding. |
    | Integratie account * |Voer een naam in voor het integratie account. Zorg ervoor dat het integratie account en de logische app zich op dezelfde Azure-locatie bevinden. |

5.  Wanneer u klaar bent, moeten de verbindings details er ongeveer als volgt uitzien. Kies **maken** om het maken van de verbinding te volt ooien.

    ![verbinding voor integratie account gemaakt](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    De verbinding wordt nu gemaakt.

    ![verbindings Details van het integratie account](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>X12-berichten coderen op overeenkomst naam

Als u ervoor hebt gekozen om X12-berichten te coderen op overeenkomst naam, opent u de **naam van de X12-overeenkomst** lijst, voert u uw bestaande X12-overeenkomst in of selecteert u deze. Voer het XML-bericht in dat moet worden gecodeerd.

![Voer de naam van de X12 en het XML-bericht in dat moet worden gecodeerd](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>X12-berichten coderen op identiteiten

Als u ervoor kiest om X12-berichten te coderen op identiteiten, voert u de kwalificatie-id, afzender kwalificatie, ontvanger-id en ontvanger van de ontvangers in zoals deze zijn geconfigureerd in uw X12-overeenkomst. Selecteer het XML-bericht dat moet worden gecodeerd.
   
![Identiteiten opgeven voor de afzender en ontvanger, het XML-bericht selecteren dat moet worden gecodeerd](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>Details van X12-codering

De X12 encoded connector voert de volgende taken uit:

* Overeenkomst omzetting door overeenkomende eigenschappen van Sender and receive context.
* Serialisatie van de EDI-uitwisseling, waarbij XML-gecodeerde berichten worden geconverteerd naar EDI-transactie sets in de uitwisseling.
* Kopregel voor transactie sets en trailer-segmenten Toep assen
* Hiermee genereert u een uitwisselings controle nummer, een groeps controle nummer en een transactie reeks controle nummer voor elke uitgaande uitwisseling
* Vervangt scheidings tekens in de gegevens van de nettolading
* Hiermee worden EDI-en partner-specifieke eigenschappen gevalideerd
  * Schema validatie van de gegevens elementen van de trans actie op basis van het bericht schema
  * EDI-validatie uitgevoerd op trans actie-gegevens elementen.
  * Uitgebreide validatie uitgevoerd op gegevens elementen van trans actie-set
* Verzoekt een technische en/of functionele bevestiging (indien geconfigureerd).
  * Een technische bevestiging wordt gegenereerd als gevolg van de validatie van de header. De technische bevestiging rapporteert de status van de verwerking van een uitwisselings header en trailer door de ontvanger van het adres
  * Een functie bevestiging wordt gegenereerd als gevolg van de validatie van de hoofd tekst. De functie bevestigings rapporteert elke fout die is opgetreden tijdens het verwerken van het ontvangen document

## <a name="view-the-swagger"></a>De Swagger weer geven
Zie [Swagger-gegevens](/connectors/x12/). 

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over de Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Meer informatie over Enterprise Integration Pack") 

