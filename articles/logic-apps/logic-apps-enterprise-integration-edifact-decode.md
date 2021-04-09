---
title: EDIFACT-berichten decoderen
description: Valideer EDI en Genereer bevestigingen met de EDIFACT-bericht decoder voor Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: b36641677dbf36402c7f578b9b1887c52f441afd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000008"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>EDIFACT-berichten decoderen voor Azure Logic Apps met de Enterprise Integration Pack

Met de connector voor het decoderen van EDIFACT-berichten kunt u EDI- en partner-specifieke eigenschappen valideren, interacties in transactiesets splitsen of volledige uitwisselingen behouden en bevestigingen voor verwerkte transacties genereren. Als u deze connector wilt gebruiken, moet u de connector toevoegen aan een bestaande trigger in uw logische app.

## <a name="before-you-start"></a>Voordat u begint

Dit zijn de items die u nodig hebt:

* Een Azure-account; u kunt een [gratis account](https://azure.microsoft.com/free) maken
* Een [integratie account](logic-apps-enterprise-integration-create-integration-account.md) dat al is gedefinieerd en gekoppeld aan uw Azure-abonnement. U moet een integratie account hebben voor het gebruik van de decodeer EDIFACT-bericht connector. 
* Ten minste twee [partners](logic-apps-enterprise-integration-partners.md) die al zijn gedefinieerd in uw integratie account
* Een [EDIFACT-overeenkomst](logic-apps-enterprise-integration-edifact.md) die al is gedefinieerd in uw integratie account

## <a name="decode-edifact-messages"></a>EDIFACT-berichten decoderen

> [!IMPORTANT]
> De EDIFACT-connector ondersteunt alleen UTF-8-tekens.
> Als uw uitvoer onverwachte tekens bevat, controleert u of uw EDIFACT-berichten gebruikmaken van de UTF-8-tekenset. 

1. [Maak een logische app](quickstart-create-first-logic-app-workflow.md).

2. De decodeer EDIFACT-bericht connector heeft geen triggers, dus u moet een trigger toevoegen voor het starten van uw logische app, zoals een aanvraag trigger. Voeg in de ontwerp functie voor logische apps een trigger toe en voeg vervolgens een actie toe aan uw logische app.

3. Voer in het zoekvak ' EDIFACT ' in als uw filter. Selecteer **EDIFACT-bericht decoderen**.
   
    ![EDIFACT zoeken](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Als u eerder geen verbindingen met uw integratie account hebt gemaakt, wordt u gevraagd om die verbinding nu te maken. Geef uw verbinding een naam en selecteer het integratie account dat u wilt verbinden.
   
    ![integratie account maken](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Eigenschappen met een sterretje zijn vereist.

    | Eigenschap | Details |
    | --- | --- |
    | Verbindings naam * |Voer een naam in voor de verbinding. |
    | Integratie account * |Voer een naam in voor het integratie account. Zorg ervoor dat het integratie account en de logische app zich op dezelfde Azure-locatie bevinden. |

4. Wanneer u klaar bent om het maken van de verbinding te volt ooien, kiest u **maken**. De verbindings details moeten er ongeveer als volgt uitzien:

    ![Details van het integratie account](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Nadat de verbinding is gemaakt, selecteert u in dit voor beeld het bericht EDIFACT plat file om te decoderen.

    ![verbinding voor integratie account gemaakt](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Bijvoorbeeld:

    ![Selecteer EDIFACT-bericht met platte bestanden voor het decoderen](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Details van EDIFACT-decoder

De decode EDIFACT-connector voert de volgende taken uit: 

* Valideert de envelop tegen de overeenkomst van de handels partner.
* Hiermee wordt de overeenkomst opgelost door de kwalificatie van de afzender & id en de kwalificatie & id te vergelijken.
* Splitst een uitwisseling in meerdere trans acties wanneer de uitwisseling meer dan één trans actie heeft op basis van de configuratie van de ontvangst instellingen van de overeenkomst.
* Deassembleert de uitwisseling.
* Valideert EDI en partner-specifieke eigenschappen, waaronder:
  * Validatie van de structuur voor de uitwisseling van enveloppen
  * Schema validatie van de envelop op basis van het controle schema
  * Schema validatie van de gegevens elementen van de trans actie op basis van het bericht schema
  * EDI-validatie uitgevoerd op gegevens elementen van trans actie-set
* Controleert of de controle nummers voor de uitwisseling, groep en trans actie geen duplicaten zijn (indien geconfigureerd) 
  * Hiermee wordt het uitwisselings controle nummer gecontroleerd op eerder ontvangen wissels. 
  * Hiermee wordt het groeps controle nummer gecontroleerd op andere groeps controle nummers in de uitwisseling. 
  * Hiermee wordt het controle nummer van de transactieset gecontroleerd op andere trans actie besturings nummers instellen in die groep.
* Splitst de uitwisseling in transactie sets of behoudt de volledige uitwisseling:
  * Gesplitste uitwisseling als transactie sets: Stel transactie sets uit bij fout: Splits de uitwisseling in transactie sets en parseert elke transactie groep. 
  Met de dedecodeer actie EDIFACT worden alleen de transactie sets uitgevoerd waarvoor `badMessages` de validatie is mislukt, en worden de resterende trans acties ingesteld op `goodMessages` .
  * Gesplitste uitwisseling als transactie sets-uitwisseling onderbreken bij fout: het splitsen van uitwisseling in transactie sets en het parseren van elke transactie groep. 
  Als een of meer transactie sets in de uitwisseling mislukt, worden met de dedecodeer actie EDIFACT alle transactie sets in die uitwisseling uitgevoerd naar `badMessages` .
  * Trans actie sets met uitwisselingen behouden bij fout: de uitwisseling behouden en de volledige batch uitwisseling verwerken. 
  Met de dedecodeer actie EDIFACT worden alleen de transactie sets uitgevoerd waarvoor `badMessages` de validatie is mislukt, en worden de resterende trans acties ingesteld op `goodMessages` .
  * Uitwisseling van Interchange-Suspend behouden bij fout: behoud de uitwisseling en verwerk de volledige batch uitwisseling. 
  Als een of meer transactie sets in de uitwisseling mislukt, worden met de dedecodeer actie EDIFACT alle transactie sets in die uitwisseling uitgevoerd naar `badMessages` .
* Hiermee wordt een technische (controle) en/of functionele bevestiging gegenereerd (indien geconfigureerd).
  * Een technische bevestiging of de CONTRL ACK rapporteert de resultaten van een syntactische controle van de volledige ontvangen uitwisseling.
  * Een functie bevestiging erkent of weigert een ontvangen uitwisseling of groep

## <a name="view-swagger-file"></a>Swagger-bestand weer geven
Zie [EDIFACT](/connectors/edifact/)voor informatie over het weer geven van de Swagger-gegevens voor de EDIFACT-connector.

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over de Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Meer informatie over Enterprise Integration Pack") 

