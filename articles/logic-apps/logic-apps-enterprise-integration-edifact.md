---
title: EDIFACT berichten voor B2B-integratie
description: Exchange EDIFACT-berichten in EDI-indeling voor B2B Enter prise integration in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: b0df55e59bd519a816c4022f2434edfcd4460780
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006520"
---
# <a name="exchange-edifact-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>EDIFACT-berichten voor B2B Enterprise-integratie in Azure Logic Apps uitwisselen met Enterprise Integration Pack

Voordat u EDIFACT-berichten kunt uitwisselen voor Azure Logic Apps, moet u een EDIFACT-overeenkomst maken en die overeenkomst opslaan in uw integratie account. Hier volgen de stappen voor het maken van een EDIFACT-overeenkomst.

> [!NOTE]
> Op deze pagina worden de EDIFACT-functies voor Azure Logic Apps beschreven. Zie [X12](logic-apps-enterprise-integration-x12.md)voor meer informatie.

## <a name="before-you-start"></a>Voordat u begint

Dit zijn de items die u nodig hebt:

* Een [integratie account](logic-apps-enterprise-integration-create-integration-account.md) dat al is gedefinieerd en gekoppeld aan uw Azure-abonnement  
* Ten minste twee [partners](logic-apps-enterprise-integration-partners.md) die al zijn gedefinieerd in uw integratie account

> [!NOTE]
> Wanneer u een overeenkomst maakt, moet de inhoud in de berichten die u ontvangt of van en naar de partner verzendt, overeenkomen met het overeenkomst type.

Nadat u [een integratie account hebt gemaakt](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) en [partners hebt toegevoegd](logic-apps-enterprise-integration-partners.md), kunt u een Edifact-overeenkomst maken door de volgende stappen uit te voeren.

## <a name="create-an-edifact-agreement"></a>Een EDIFACT-overeenkomst maken 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com "Azure Portal"). 

2. Selecteer in het hoofd menu van Azure **alle services**. Voer in het zoekvak ' Integration ' in en selecteer vervolgens **integratie accounts**.

   ![Uw integratie account zoeken](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Als **alle services** niet worden weer gegeven, moet u het menu mogelijk eerst uitbreiden. Selecteer boven in het samengevouwen menu de optie **Tekstlabels weer geven**.

3. Onder **integratie accounts** selecteert u het integratie account waar u de overeenkomst wilt maken.

   ![Integratie account selecteren waar de overeenkomst moet worden gemaakt](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Kies **overeenkomsten**. Als u geen tegel overeenkomsten hebt, voegt u eerst de tegel toe.   

   ![Kies de tegel ' overeenkomsten '](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. Kies op de pagina overeenkomsten de optie **toevoegen**.

   ![Kies toevoegen](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. Voer onder **toevoegen** een **naam** in voor uw overeenkomst. Selecteer voor **type overeenkomst** de optie **EDIFACT**. Selecteer de **host-partner**, de **identiteit** van de host, de **gast partner** en de **gast identiteit** voor uw overeenkomst.

   ![Details van overeenkomst opgeven](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Eigenschap | Beschrijving |
   | --- | --- |
   | Name |Naam van de overeenkomst |
   | Type overeenkomst | Moet EDIFACT zijn |
   | Host-partner |Een overeenkomst heeft zowel een host-als een gast partner nodig. De host-partner vertegenwoordigt de organisatie die de overeenkomst configureert. |
   | Host-id |Een id voor de host-partner |
   | Gast partner |Een overeenkomst heeft zowel een host-als een gast partner nodig. De gast partner vertegenwoordigt de organisatie die zakendoet met de host-partner. |
   | Gast identiteit |Een id voor de gast partner |
   | Instellingen voor ontvangen |Deze eigenschappen zijn van toepassing op alle berichten die door een overeenkomst worden ontvangen. |
   | Instellingen verzenden |Deze eigenschappen zijn van toepassing op alle berichten die door een overeenkomst worden verzonden. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configureren hoe uw overeenkomst ontvangen berichten verwerkt

Nu u de eigenschappen van de overeenkomst hebt ingesteld, kunt u configureren hoe deze overeenkomst inkomende berichten die van uw partner worden ontvangen via deze overeenkomst identificeren en verwerken.

> [!IMPORTANT]
> De EDIFACT-connector ondersteunt alleen UTF-8-tekens.
> Als uw uitvoer onverwachte tekens bevat, controleert u of uw EDIFACT-berichten gebruikmaken van de UTF-8-tekenset.

1. Onder **toevoegen** selecteert u **instellingen voor ontvangen**.
Configureer deze eigenschappen op basis van uw overeenkomst met de partner die berichten met u uitwisselt. Zie voor beschrijvingen van eigenschappen de tabellen in deze sectie.

   **Ontvangst-instellingen** zijn ingedeeld in de volgende secties: Id's, bevestiging, Schema's, controle nummers, validatie en interne instellingen.

   ![Instellingen voor het ontvangen van berichten configureren](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Wanneer u klaar bent, slaat u de instellingen op door **OK** te kiezen.

U bent nu klaar om inkomende berichten af te handelen die overeenkomen met de geselecteerde instellingen.

### <a name="identifiers"></a>Id's

| Eigenschap | Beschrijving |
| --- | --- |
| UNB 6.1 (referentie wacht woord van de ontvanger) |Voer een alfanumerieke waarde tussen 1 en 14 tekens in. |
| UNB 6.2 (referentie kwalificatie van de ontvanger) |Voer een alfanumerieke waarde in met mini maal één teken en Maxi maal twee tekens. |

### <a name="acknowledgments"></a>Dankwoord

| Eigenschap | Beschrijving |
| --- | --- |
| Ontvangst van bericht (CONTRL) |Schakel dit selectie vakje in om een technische (CONTRL) bevestiging te retour neren naar de verzender van het uitwisselings certificaat. De bevestiging wordt verzonden naar de verzender van het uitwisselings bericht op basis van de instellingen voor verzenden voor de overeenkomst. |
| Bevestiging (CONTRL) |Schakel dit selectie vakje in om een functionele (CONTRL) bevestiging te retour neren naar de verzender van het uitwisselings bericht dat de bevestiging wordt verzonden naar de verzender van het uitwisselings certificaat op basis van de instellingen voor het verzenden van de overeenkomst. |

### <a name="schemas"></a>Schema 's

| Eigenschap | Beschrijving |
| --- | --- |
| UNH 2.1 (TYPE) |Selecteer een type transactieset. |
| UNH 2.2 (VERSIE) |Voer het versie nummer van het bericht in. (Mini maal, één teken; maximum, drie tekens). |
| UNH 2.3 (RELEASE) |Voer het nummer van het bericht in. (Mini maal, één teken; maximum, drie tekens). |
| UNH 2.5 (GEKOPPELDE TOEGEWEZEN CODE) |Voer de toegewezen code in. (Maxi maal zes tekens. Moet alfanumeriek zijn). |
| UNG 2.1 (ID VAN DE APP-AFZENDER) |Voer een alfanumerieke waarde in met mini maal één teken en Maxi maal 35 tekens. |
| UNG 2.2 (APP SENDER CODE QUALIFIER) |Voer een alfanumerieke waarde in met een maximum van vier tekens. |
| SCHEMA |Selecteer het eerder geüploade schema dat u wilt gebruiken uit het gekoppelde integratie account. |

### <a name="control-numbers"></a>Controle nummers

| Eigenschap | Beschrijving |
| --- | --- |
| Dubbele uitwisselings controle nummers niet toestaan |Selecteer deze eigenschap om dubbele interwijzigingen te blok keren. Als u deze waarde selecteert, controleert de decoderings actie EDIFACT of het uitwisselings controle nummer (UNB5) voor de ontvangen uitwisseling niet overeenkomt met een eerder verwerkte Interchange Control-nummer. Als er een overeenkomst wordt gedetecteerd, wordt de uitwisseling niet verwerkt. |
| Controleren op dubbele UNB5 elke (dagen) |Als u dubbele uitwisselings controle nummers niet toestaat, kunt u het aantal dagen opgeven wanneer de controle moet worden uitgevoerd door de juiste waarde voor deze instelling op te geven. |
| Dubbele items van groeps controle nummers niet toestaan |Selecteer deze eigenschap om interwijzigen met dubbele groeps controle nummers (UNG5) te blok keren. |
| Geen duplicaten voor het instellen van transactie sets toestaan |Selecteer deze eigenschap om interacties te blok keren met dubbele trans actie-UNH1 (set Control Numbers). |
| EDIFACT bevestigings controle nummer |Als u de referentie nummers van de transactie set wilt opgeven voor gebruik in een bevestiging, voert u een waarde in voor het voor voegsel, een bereik van de referentie nummers en een achtervoegsel. |

### <a name="validation"></a>Validatie

Wanneer u elke validatie rij voltooit, wordt er automatisch een andere toegevoegd. Als u geen regels opgeeft, wordt met de validatie de rij ' default ' gebruikt.

| Eigenschap | Beschrijving |
| --- | --- |
| Bericht type |Selecteer het type EDI-bericht. |
| EDI-validatie |Voer EDI-validatie uit op gegevens typen zoals gedefinieerd door de EDI-eigenschappen, lengte beperkingen, lege gegevens elementen en navolgende scheidings tekens van het schema. |
| Uitgebreide validatie |Als het gegevens type niet EDI is, is validatie van de vereiste voor het gegevens element en de toegestane herhaling, opsommingen en gegevens element lengte validatie (min/max). |
| Voor loop-en volg nullen toestaan |Bewaar eventuele extra voor loop-of volg spaties. Verwijder deze tekens niet. |
| Voor loop-en volg nullen bijsnijden |Voor loop-of volg spaties verwijderen. |
| Volg scheidings beleid |Navolgende scheidings tekens genereren. <p>Selecteer **niet toegestaan** om navolgende scheidings tekens en schei in de ontvangen uitwisseling te verbieden. Als de uitwisseling navolgende scheidings tekens en schei heeft, wordt de uitwisseling gedeclareerd als ongeldig. <p>Selecteer **optioneel** om wissels met of zonder navolgende scheidings tekens en schei te accepteren. <p>Selecteer **verplicht** wanneer de ontvangen uitwisseling een Volg scheidings teken en scheidings tekens moet hebben. |

### <a name="internal-settings"></a>Interne instellingen

| Eigenschap | Beschrijving |
| --- | --- |
| Lege XML-tags maken als navolgende scheidings tekens zijn toegestaan |Schakel dit selectie vakje in als u wilt dat de verzender van de uitwisseling lege XML-codes bevat voor afsluitende scheidings tekens. |
| Gesplitste uitwisseling als transactie sets-transactie sets uitstellen bij fout|Parseert elke transactie set in een uitwisseling in een afzonderlijk XML-document door de juiste envelop toe te passen op de transactieset. Alleen de transactie sets onderbreken waarvoor validatie is mislukt. |
| Gesplitste uitwisseling als transactie sets: uitwisseling onderbreken bij fout|Parseert elke transactie set in een uitwisseling in een afzonderlijk XML-document door de juiste envelop toe te passen. De volledige uitwisseling onderbreken wanneer een of meer transactie sets in de uitwisseling mislukt. | 
| Trans actie sets met uitwisselingen behouden bij fout |Laat de uitwisseling intact, maakt een XML-document voor de gehele gebatcheerde uitwisseling. Alleen de transactie sets onderbreken die niet zijn gevalideerd, terwijl alle andere transactie sets worden verwerkt. |
| Uitwisseling van uitwisseling met fouten behouden |Laat de uitwisseling intact, maakt een XML-document voor de gehele gebatcheerde uitwisseling. De volledige uitwisseling onderbreken wanneer een of meer transactie sets in de uitwisseling mislukt. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configureren hoe uw overeenkomst berichten verzendt

U kunt configureren hoe deze overeenkomst uitgaande berichten identificeert en verwerkt die u via deze overeenkomst naar uw partners verzendt.

1.  Onder **toevoegen** selecteert u **instellingen verzenden**.
Configureer deze eigenschappen op basis van uw overeenkomst met uw partner die berichten met u uitwisselt. Zie voor beschrijvingen van eigenschappen de tabellen in deze sectie.

    De **instellingen voor verzenden** zijn ingedeeld in de volgende secties: Id's, bevestiging, Schema's, enveloppen, teken sets en scheidings tekens, controle nummers en validaties.

    ![Instellingen voor verzenden configureren](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Wanneer u klaar bent, slaat u de instellingen op door **OK** te kiezen.

Uw overeenkomst is nu klaar voor het afhandelen van uitgaande berichten die voldoen aan de geselecteerde instellingen.

### <a name="identifiers"></a>Id's

| Eigenschap | Beschrijving |
| --- | --- |
| UNB 1.2 (syntaxis versie) |Selecteer een waarde tussen **1** en **4**. |
| UNB 2.3 (afzender adres voor omkerend verkeer) |Voer een alfanumerieke waarde in met mini maal één teken en Maxi maal 14 tekens. |
| UNB 3.3 (omgekeerd routerings adres van ontvanger) |Voer een alfanumerieke waarde in met mini maal één teken en Maxi maal 14 tekens. |
| UNB 6.1 (referentie wacht woord van de ontvanger) |Voer een alfanumerieke waarde in met mini maal één en Maxi maal 14 tekens. |
| UNB 6.2 (referentie kwalificatie van de ontvanger) |Voer een alfanumerieke waarde in met mini maal één teken en Maxi maal twee tekens. |
| UNB7 (toepassings verwijzing-ID) |Voer een alfanumerieke waarde in met mini maal één teken en Maxi maal 14 tekens |

### <a name="acknowledgment"></a>Bevestiging

| Eigenschap | Beschrijving |
| --- | --- |
| Ontvangst van bericht (CONTRL) |Schakel dit selectie vakje in als de gehoste partner verwacht een technische bevestiging (CONTRL) te ontvangen. Met deze instelling geeft u op dat de gehoste partner, die het bericht verzendt, een bevestiging vraagt van de gast partner. |
| Bevestiging (CONTRL) |Schakel dit selectie vakje in als de gehoste partner verwacht een CONTRL-bevestiging te ontvangen. Met deze instelling geeft u op dat de gehoste partner, die het bericht verzendt, een bevestiging vraagt van de gast partner. |
| SG1/SG4-lus genereren voor geaccepteerde transactie sets |Als u ervoor hebt gekozen om een functionele bevestiging aan te vragen, schakelt u dit selectie vakje in om het genereren van SG1/SG4-lussen in functionele CONTRL-ontvangst bevestigingen voor geaccepteerde transactie sets af te dwingen. |

### <a name="schemas"></a>Schema 's

| Eigenschap | Beschrijving |
| --- | --- |
| UNH 2.1 (TYPE) |Selecteer een type transactieset. |
| UNH 2.2 (VERSIE) |Voer het versie nummer van het bericht in. |
| UNH 2.3 (RELEASE) |Voer het nummer van het bericht in. |
| SCHEMA |Selecteer het schema dat u wilt gebruiken. Schema's bevinden zich in uw integratie account. Als u toegang wilt krijgen tot uw schema's, koppelt u eerst uw integratie account aan uw logische app. |

### <a name="envelopes"></a>Enveloppen

| Eigenschap | Beschrijving |
| --- | --- |
| UNB8 (verwerkings prioriteits code) |Voer een alfabetische waarde in die niet meer dan één teken lang is. |
| UNB10 (communicatie overeenkomst) |Voer een alfanumerieke waarde in met mini maal één teken en Maxi maal 40 tekens. |
| UNB11 (test indicator) |Schakel dit selectie vakje in om aan te geven dat het gegenereerde uitwisseling test gegevens is |
| UNA segment Toep assen (advies voor service teken reeks) |Schakel dit selectie vakje in om een UNA-segment te genereren voor het verzenden van de uitwisseling. |
| UNG segmenten Toep assen (koptekst functie groep) |Schakel dit selectie vakje in om Groepeer segmenten in de kop van de functionele groep te maken in de berichten die naar de gast partner worden verzonden. De volgende waarden worden gebruikt om de UNG-segmenten te maken: <p>Voer voor **UNG1** een alfanumerieke waarde in met mini maal één teken en Maxi maal zes tekens. <p>Voer voor **Ung 2.1** een alfanumerieke waarde in met mini maal één teken en maxi maal 35 tekens. <p>Voer voor **Ung 2.2** een alfanumerieke waarde in met een maximum van vier tekens. <p>Voer voor **ung 3.1** een alfanumerieke waarde in met mini maal één teken en maxi maal 35 tekens. <p>Voer voor **ung 3.2** een alfanumerieke waarde in met een maximum van vier tekens. <p>Voer voor **UNG6** een alfanumerieke waarde in met mini maal één en Maxi maal drie tekens. <p>Voer voor **ung 7.1** een alfanumerieke waarde in met mini maal één teken en Maxi maal drie tekens. <p>Voer voor **ung 7.2** een alfanumerieke waarde in met mini maal één teken en Maxi maal drie tekens. <p>Voer voor **ung 7.3** een alfanumerieke waarde in met mini maal 1 teken en Maxi maal 6 tekens. <p>Voer voor **UNG8** een alfanumerieke waarde in met mini maal één teken en Maxi maal 14 tekens. |

### <a name="character-sets-and-separators"></a>Teken sets en scheidings tekens

Met uitzonde ring van de tekenset kunt u een andere set scheidings tekens invoeren die voor elk bericht type moeten worden gebruikt. Als er geen tekenset is opgegeven voor een bepaald bericht schema, wordt de standaard tekenset gebruikt.

| Eigenschap | Beschrijving |
| --- | --- |
| UNB 1.1 (systeem-id) |Selecteer de teken reeks EDIFACT die u wilt Toep assen op de uitgaande uitwisseling. |
| Schema |Selecteer een schema in de vervolg keuzelijst. Nadat u elke rij hebt voltooid, wordt er automatisch een nieuwe rij toegevoegd. Voor het geselecteerde schema selecteert u de scheidings tekens die u wilt gebruiken, op basis van de onderstaande scheidings tekens. |
| Invoertype |Selecteer een invoer type in de vervolg keuzelijst. |
| Onderdeel scheidings teken |Voer één teken in om samengestelde gegevens elementen van elkaar te scheiden. |
| Scheidings teken voor gegevens elementen |Als u eenvoudige gegevens elementen in samengestelde gegevens elementen wilt scheiden, voert u één teken in. |
| Segment Terminator |Voer één teken in om het einde van een EDI-segment aan te geven. |
| Achtervoegsel |Selecteer het teken dat wordt gebruikt met de segment-id. Als u een achtervoegsel aanwijst, kan het gegevens element van het segment Terminator leeg zijn. Als de eind afsluit van het segment leeg blijft, moet u een achtervoegsel aanwijzen. |

### <a name="control-numbers"></a>Controle nummers

| Eigenschap | Beschrijving |
| --- | --- |
| UNB5 (Interchange Control Number) |Voer een voor voegsel, een reeks waarden voor het uitwisselings controle nummer en een achtervoegsel in. Deze waarden worden gebruikt voor het genereren van een uitgaande uitwisseling. Het voor voegsel en achtervoegsel zijn optioneel, terwijl het controle nummer is vereist. Het controle nummer wordt voor elk nieuw bericht verhoogd. het voor voegsel en achtervoegsel blijven hetzelfde. |
| UNG5 (groeps controle nummer) |Voer een voor voegsel, een reeks waarden voor het uitwisselings controle nummer en een achtervoegsel in. Deze waarden worden gebruikt om het groeps controle nummer te genereren. Het voor voegsel en achtervoegsel zijn optioneel, terwijl het controle nummer is vereist. Het controle nummer wordt voor elk nieuw bericht verhoogd totdat de maximum waarde is bereikt. het voor voegsel en achtervoegsel blijven hetzelfde. |
| UNH1 (referentie nummer van de bericht header) |Voer een voor voegsel, een reeks waarden voor het uitwisselings controle nummer en een achtervoegsel in. Deze waarden worden gebruikt om het referentie nummer van de bericht header te genereren. Het voor voegsel en achtervoegsel zijn optioneel, terwijl het referentie nummer is vereist. Het referentie nummer wordt voor elk nieuw bericht verhoogd. het voor voegsel en achtervoegsel blijven hetzelfde. |

### <a name="validation"></a>Validatie

Wanneer u elke validatie rij voltooit, wordt er automatisch een andere toegevoegd. Als u geen regels opgeeft, wordt met de validatie de rij ' default ' gebruikt.

| Eigenschap | Beschrijving |
| --- | --- |
| Bericht type |Selecteer het type EDI-bericht. |
| EDI-validatie |Voer EDI-validatie uit op gegevens typen zoals gedefinieerd door de EDI-eigenschappen van het schema, de lengte beperkingen, lege gegevens elementen en navolgende scheidings tekens. |
| Uitgebreide validatie |Als het gegevens type niet EDI is, is validatie van de vereiste voor het gegevens element en de toegestane herhaling, opsommingen en gegevens element lengte validatie (min/max). |
| Voor loop-en volg nullen toestaan |Bewaar eventuele extra voor loop-of volg spaties. Verwijder deze tekens niet. |
| Voor loop-en volg nullen bijsnijden |Voor loop-of volg spaties verwijderen. |
| Volg scheidings beleid |Navolgende scheidings tekens genereren. <p>Selecteer **niet toegestaan** om het afsluiten van de scheidings tekens en schei ding van de gezonden uitwisseling te verhinderen. Als de uitwisseling navolgende scheidings tekens en schei heeft, wordt de uitwisseling gedeclareerd als ongeldig. <p>Selecteer **optioneel** om wissels met of zonder navolgende scheidings tekens en schei dingen te verzenden. <p>Selecteer **verplicht** als het gestuurde uitwisseling moet navolgende scheidings tekens en schei ding. |

## <a name="find-your-created-agreement"></a>Uw gemaakte overeenkomst zoeken

1.  Nadat u alle eigenschappen van de overeenkomst hebt ingesteld, kiest u **OK** op de pagina **toevoegen** om uw overeenkomst te maken en terug te keren naar uw integratie account.

    De zojuist toegevoegde overeenkomst wordt nu weer gegeven in de lijst met **overeenkomsten** .

2.  U kunt uw overeenkomsten ook bekijken in het overzicht van het integratie account. Kies **overzicht** in het menu integratie account en selecteer vervolgens de tegel **overeenkomsten** . 

    ![Scherm opname van de tegel overeenkomsten.](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="connector-reference"></a>Connector-verwijzing

Voor meer technische informatie over deze connector, zoals acties en limieten zoals beschreven in het Swagger-bestand van de connector, raadpleegt u de [referentie pagina van de connector](/connectors/edifact/).

> [!NOTE]
> Voor Logic apps in een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), gebruikt de ISE-label versie van deze connector de [limieten voor B2B-berichten voor ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)
