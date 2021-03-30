---
title: X12-berichten decoderen
description: Valideer EDI en Genereer bevestigingen met X12-bericht decoder in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 918516a5629f8570d54c641ffc29f2367937266f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "74792368"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>X12-berichten in Azure Logic Apps decoderen met Enterprise Integration Pack

Met de connector voor het decoderen van X12-berichten kunt u de envelop valideren op basis van een overeenkomst, EDI- en partner-specifieke eigenschappen valideren, interacties in transactiesets splitsen of volledige uitwisselingen behouden en bevestigingen voor verwerkte transacties genereren. Als u deze connector wilt gebruiken, moet u de connector toevoegen aan een bestaande trigger in uw logische app.

## <a name="before-you-start"></a>Voordat u begint

Dit zijn de items die u nodig hebt:

* Een Azure-account; u kunt een [gratis account](https://azure.microsoft.com/free) maken
* Een [integratie account](logic-apps-enterprise-integration-create-integration-account.md) dat al is gedefinieerd en gekoppeld aan uw Azure-abonnement. U moet een integratie account hebben voor het gebruik van de decodeer X12-bericht connector.
* Ten minste twee [partners](logic-apps-enterprise-integration-partners.md) die al zijn gedefinieerd in uw integratie account
* Een [X12-overeenkomst](logic-apps-enterprise-integration-x12.md) die al is gedefinieerd in uw integratie account

## <a name="decode-x12-messages"></a>X12-berichten decoderen

1. [Maak een logische app](quickstart-create-first-logic-app-workflow.md).

2. De decodeer X12-bericht connector heeft geen triggers, dus u moet een trigger toevoegen voor het starten van uw logische app, zoals een aanvraag trigger. Voeg in de ontwerp functie voor logische apps een trigger toe en voeg vervolgens een actie toe aan uw logische app.

3.  Voer in het zoekvak ' X12 ' in voor uw filter. Selecteer **X12-X12-bericht decoderen**.
   
    ![Zoeken naar ' X12 '](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Als u eerder geen verbindingen met uw integratie account hebt gemaakt, wordt u gevraagd om die verbinding nu te maken. Geef uw verbinding een naam en selecteer het integratie account dat u wilt verbinden. 

    ![Verbindings Details van het integratie account opgeven](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Eigenschappen met een sterretje zijn vereist.

    | Eigenschap | Details |
    | --- | --- |
    | Verbindings naam * |Voer een naam in voor de verbinding. |
    | Integratie account * |Voer een naam in voor het integratie account. Zorg ervoor dat het integratie account en de logische app zich op dezelfde Azure-locatie bevinden. |

5.  Wanneer u klaar bent, moeten de verbindings details er ongeveer als volgt uitzien. Kies **maken** om het maken van de verbinding te volt ooien.
   
    ![verbindings Details van het integratie account](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Nadat de verbinding is gemaakt, selecteert u in dit voor beeld het bericht X12 plat file om te decoderen.

    ![verbinding voor integratie account gemaakt](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Bijvoorbeeld:

    ![Selecteer X12-bericht met platte bestanden voor het decoderen](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > De daad werkelijke bericht inhoud of Payload voor de bericht matrix, goed of beschadigd, is base64-gecodeerd. U moet dus een expressie opgeven waarmee deze inhoud wordt verwerkt.
   > Hier volgt een voor beeld waarin de inhoud wordt verwerkt als XML die u kunt invoeren in de code weergave of met behulp van Expression Builder in de ontwerp functie.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Voor beeld van inhoud](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>Details van X12-decodering

De X12 decode connector voert de volgende taken uit:

* Valideert de envelop op basis van de overeenkomst voor handels partners
* Hiermee worden EDI-en partner-specifieke eigenschappen gevalideerd
  * EDI Structured Validation en Extended schema validatie
  * Validatie van de structuur van de uitwisselings enveloppe.
  * Schema validatie van de envelop op basis van het controle schema.
  * Schema validatie van de gegevens elementen van de trans actie op basis van het bericht schema.
  * EDI-validatie uitgevoerd op gegevens elementen van trans actie-set 
* Controleert of de controle nummers voor de uitwisseling, groep en trans actie geen duplicaten zijn
  * Hiermee wordt het uitwisselings controle nummer gecontroleerd op eerder ontvangen wissels.
  * Hiermee wordt het groeps controle nummer gecontroleerd op andere groeps controle nummers in de uitwisseling.
  * Hiermee wordt het controle nummer van de transactieset gecontroleerd op andere trans actie besturings nummers instellen in die groep.
* Splitst de uitwisseling in transactie sets of behoudt de volledige uitwisseling:
  * Gesplitste uitwisseling als transactie sets: Stel transactie sets uit bij fout: Splits de uitwisseling in transactie sets en parseert elke transactie groep. 
  Met de dedecodeer actie X12 worden alleen de transactie sets uitgevoerd waarvoor `badMessages` de validatie is mislukt, en worden de resterende trans acties ingesteld op `goodMessages` .
  * Gesplitste uitwisseling als transactie sets-uitwisseling onderbreken bij fout: het splitsen van uitwisseling in transactie sets en het parseren van elke transactie groep. 
  Als een of meer transactie sets in de uitwisseling mislukt, worden met de dedecodeer actie X12 alle transactie sets in die uitwisseling uitgevoerd naar `badMessages` .
  * Trans actie sets met uitwisselingen behouden bij fout: de uitwisseling behouden en de volledige batch uitwisseling verwerken. 
  Met de dedecodeer actie X12 worden alleen de transactie sets uitgevoerd waarvoor `badMessages` de validatie is mislukt, en worden de resterende trans acties ingesteld op `goodMessages` .
  * Uitwisseling van Interchange-Suspend behouden bij fout: behoud de uitwisseling en verwerk de volledige batch uitwisseling. 
  Als een of meer transactie sets in de uitwisseling mislukt, worden met de dedecodeer actie X12 alle transactie sets in die uitwisseling uitgevoerd naar `badMessages` . 
* Hiermee wordt een technische en/of functionele bevestiging gegenereerd (indien geconfigureerd).
  * Een technische bevestiging wordt gegenereerd als gevolg van de validatie van de header. De technische bevestiging rapporteert de status van de verwerking van een uitwisselings header en trailer door de ontvanger van het adres.
  * Een functie bevestiging wordt gegenereerd als gevolg van de validatie van de hoofd tekst. De functie bevestigings rapporteert elke fout die is opgetreden tijdens het verwerken van het ontvangen document

## <a name="view-the-swagger"></a>De Swagger weer geven
Zie [Swagger-gegevens](/connectors/x12/). 

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over de Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Meer informatie over Enterprise Integration Pack") 

