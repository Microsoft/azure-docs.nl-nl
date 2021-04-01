---
title: EDIFACT-berichten coderen
description: Valideer EDI en Genereer XML met EDIFACT Message Encoder voor Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: cee97fddc619840de821ebb70d32e0cab1bbe040
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "82106522"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>EDIFACT-berichten coderen voor Azure Logic Apps met Enterprise Integration Pack

Met Coderen EDIFACT berichtenconnector kunt u EDI- en partner-specifieke eigenschappen valideren, een XML-document voor elke transactieset genereren en technische bevestiging, functionele bevestiging of beide aanvragen.
Als u deze connector wilt gebruiken, moet u de connector toevoegen aan een bestaande trigger in uw logische app.

## <a name="before-you-start"></a>Voordat u begint

Dit zijn de items die u nodig hebt:

* Een Azure-account; u kunt een [gratis account](https://azure.microsoft.com/free) maken
* Een [integratie account](logic-apps-enterprise-integration-create-integration-account.md) dat al is gedefinieerd en gekoppeld aan uw Azure-abonnement. U moet een integratie account hebben voor het gebruik van de code ring EDIFACT Message connector. 
* Ten minste twee [partners](logic-apps-enterprise-integration-partners.md) die al zijn gedefinieerd in uw integratie account
* Een [EDIFACT-overeenkomst](logic-apps-enterprise-integration-edifact.md) die al is gedefinieerd in uw integratie account

## <a name="encode-edifact-messages"></a>EDIFACT-berichten coderen

> [!IMPORTANT]
> De EDIFACT-connector ondersteunt alleen UTF-8-tekens.
> Als uw uitvoer onverwachte tekens bevat, controleert u of uw EDIFACT-berichten gebruikmaken van de UTF-8-tekenset.

1. [Maak een logische app](quickstart-create-first-logic-app-workflow.md).

2. De code ring EDIFACT Message connector heeft geen triggers. Daarom moet u een trigger toevoegen voor het starten van uw logische app, zoals een aanvraag trigger. Voeg in de ontwerp functie voor logische apps een trigger toe en voeg vervolgens een actie toe aan uw logische app.

3.  Voer in het zoekvak ' EDIFACT ' in als uw filter. Selecteer **EDIFACT-bericht coderen op overeenkomst naam** of **coderen naar EDIFACT-bericht door middel van identiteiten**.
   
    ![EDIFACT zoeken](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Als u eerder geen verbindingen met uw integratie account hebt gemaakt, wordt u gevraagd om die verbinding nu te maken. Geef uw verbinding een naam en selecteer het integratie account dat u wilt verbinden.

    ![verbinding voor integratie account maken](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Eigenschappen met een sterretje zijn vereist.

    | Eigenschap | Details |
    | --- | --- |
    | Verbindings naam * |Voer een naam in voor de verbinding. |
    | Integratie account * |Voer een naam in voor het integratie account. Zorg ervoor dat het integratie account en de logische app zich op dezelfde Azure-locatie bevinden. |

5.  Wanneer u klaar bent, moeten de verbindings details er ongeveer als volgt uitzien. Kies **maken** om het maken van de verbinding te volt ooien.

    ![verbindings Details van het integratie account](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    De verbinding wordt nu gemaakt.

    ![verbinding voor integratie account gemaakt](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>EDIFACT-bericht coderen op overeenkomst naam

Als u ervoor hebt gekozen om EDIFACT-berichten te coderen op overeenkomst naam, opent u de **naam van de EDIFACT-overeenkomst** lijst, voert u de naam van uw EDIFACT-overeenkomst in of selecteert u deze. Voer het XML-bericht in dat moet worden gecodeerd.

![Voer de naam van de EDIFACT en het XML-bericht in dat moet worden gecodeerd](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>EDIFACT-bericht coderen door middel van identiteiten

Als u ervoor kiest om EDIFACT-berichten te coderen op identiteiten, voert u de kwalificatie-id, afzender kwalificatie, ontvanger-id en ontvanger van de ontvangers in zoals deze zijn geconfigureerd in uw EDIFACT-overeenkomst. Selecteer het XML-bericht dat moet worden gecodeerd.

![Identiteiten opgeven voor de afzender en ontvanger, het XML-bericht selecteren dat moet worden gecodeerd](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Details van EDIFACT-codering

De EDIFACT-connector voor code ring voert de volgende taken uit: 

* De overeenkomst oplossen door de kwalificatie-id en-id van de afzender te vergelijken &
* Serialisatie van de EDI-uitwisseling, waarbij XML-gecodeerde berichten worden geconverteerd naar EDI-transactie sets in de uitwisseling.
* Kopregel voor transactie sets en trailer-segmenten Toep assen
* Hiermee genereert u een uitwisselings controle nummer, een groeps controle nummer en een transactie reeks controle nummer voor elke uitgaande uitwisseling
* Vervangt scheidings tekens in de gegevens van de nettolading
* Hiermee worden EDI-en partner-specifieke eigenschappen gevalideerd
  * Schema validatie van de gegevens elementen van de trans actie op basis van het bericht schema.
  * EDI-validatie uitgevoerd op trans actie-gegevens elementen.
  * Uitgebreide validatie uitgevoerd op gegevens elementen van trans actie-set
* Hiermee wordt een XML-document voor elke transactie groep gegenereerd.
* Verzoekt een technische en/of functionele bevestiging (indien geconfigureerd).
  * Als technische bevestiging wordt het CONTRL-bericht geeft de ontvangst van een uitwisseling aan.
  * Als functie bevestiging geeft het CONTRL-bericht aan dat de ontvangen uitwisseling, groep of bericht moet worden geaccepteerd of afgewezen, met een lijst met fouten of niet-ondersteunde functies

## <a name="view-swagger-file"></a>Swagger-bestand weer geven
Zie [EDIFACT](/connectors/edifact/)voor informatie over het weer geven van de Swagger-gegevens voor de EDIFACT-connector.

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over de Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Meer informatie over Enterprise Integration Pack") 

