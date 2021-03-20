---
title: X12-berichten verzenden en ontvangen voor B2B
description: X12-berichten uitwisselen voor B2B-scenario's voor bedrijfs integratie met behulp van Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/29/2020
ms.openlocfilehash: 61f3f2af61bc24f76d061de672a3eaacd54f7f0e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015193"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Exchange X12-berichten voor B2B Enter prise integration in Azure Logic Apps met Enterprise Integration Pack

Als u wilt werken met X12-berichten in Azure Logic Apps, kunt u de X12-connector gebruiken. Deze bevat triggers en acties voor het beheren van X12-communicatie. Zie [Exchange EDIFACT-berichten](logic-apps-enterprise-integration-edifact.md)voor meer informatie over EDIFACT-berichten.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, [meldt u zich aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De logische app van waaruit u de X12-connector wilt gebruiken en een trigger waarmee de werk stroom van de logische app wordt gestart. De X12-connector biedt alleen acties, geen triggers. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps](../logic-apps/logic-apps-overview.md) en [Quick Start: uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Een [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) dat is gekoppeld aan uw Azure-abonnement en gekoppeld aan de logische app waar u de X12-connector wilt gaan gebruiken. De logische app en het integratie account moeten zich op dezelfde locatie of Azure-regio bevinden.

* Ten minste twee [handels partners](../logic-apps/logic-apps-enterprise-integration-partners.md) die u al hebt gedefinieerd in uw integratie account met behulp van de X12-identiteits kwalificatie.

* De [schema's](../logic-apps/logic-apps-enterprise-integration-schemas.md) die u voor XML-validatie wilt gebruiken die u al aan uw integratie account hebt toegevoegd. Zie [HIPAA-schema's](#hipaa-schemas)als u werkt met de Health-Schema's voor portabiliteit en aansprakelijkheids Act (HIPAA).

* Voordat u de X12-connector kunt gebruiken, moet u een X12- [overeenkomst](../logic-apps/logic-apps-enterprise-integration-agreements.md) maken tussen uw handels partners en de overeenkomst opslaan in uw integratie account. Als u met Health Insurance-schema's (porteer baarheid en aansprakelijkheids Act) werkt, moet u een sectie toevoegen `schemaReferences` aan uw overeenkomst. Zie voor meer informatie [HIPAA-schema's](#hipaa-schemas).

<a name="receive-settings"></a>

## <a name="receive-settings"></a>Instellingen voor ontvangen

Nadat u de eigenschappen van de overeenkomst hebt ingesteld, kunt u configureren hoe deze overeenkomst inkomende berichten identificeert en verwerkt die u van uw partner ontvangt via deze overeenkomst.

1. Onder **toevoegen** selecteert u **instellingen voor ontvangen**.

1. Configureer deze eigenschappen op basis van uw overeenkomst met de partner die berichten met u uitwisselt. De **Receive-instellingen** zijn ingedeeld in de volgende secties:

   * [Id's](#inbound-identifiers)
   * [Bevestigings](#inbound-acknowledgement)
   * [Schema's](#inbound-schemas)
   * [Enveloppen](#inbound-envelopes)
   * [Controle nummers](#inbound-control-numbers)
   * [Validaties](#inbound-validations)
   * [Interne instellingen](#inbound-internal-settings)

   Zie voor beschrijvingen van eigenschappen de tabellen in deze sectie.

1. Wanneer u klaar bent, moet u de instellingen opslaan door **OK** te selecteren.

<a name="inbound-identifiers"></a>

### <a name="receive-settings---identifiers"></a>Instellingen ontvangen-Id's

![Id-eigenschappen voor inkomende berichten](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-identifiers.png)

| Eigenschap | Beschrijving |
|----------|-------------|
| **ISA1 (autorisatie kwalificatie)** | De waarde van de autorisatie kwalificatie die u wilt gebruiken. De standaard waarde is **00-er is geen autorisatie-informatie aanwezig**. <p>**Opmerking**: als u andere waarden selecteert, geeft u een waarde op voor de eigenschap **ISA2** . |
| **ISA2** | De waarde van de autorisatie gegevens die moet worden gebruikt wanneer de eigenschap **ISA1** niet **00 is: er zijn geen autorisatie gegevens aanwezig**. Deze eigenschaps waarde moet mini maal één alfanumeriek teken en Maxi maal 10 hebben. |
| **ISA3 (beveiligings kwalificatie)** | De waarde van de beveiligings kwalificatie die u wilt gebruiken. De standaard waarde is **00: er zijn geen beveiligings gegevens aanwezig**. <p>**Opmerking**: als u andere waarden selecteert, geeft u een waarde op voor de eigenschap **ISA4** . |
| **ISA4** | De waarde voor de beveiligings gegevens die moet worden gebruikt wanneer de eigenschap **ISA3** niet **00 is: er is geen beveiligings informatie aanwezig**. Deze eigenschaps waarde moet mini maal één alfanumeriek teken en Maxi maal 10 hebben. |
|||

<a name="inbound-acknowledgement"></a>

### <a name="receive-settings---acknowledgement"></a>Instellingen ontvangen-bevestiging

![Bevestiging voor inkomende berichten](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-acknowledgement.png)

| Eigenschap | Beschrijving |
|----------|-------------|
| **TA1 verwacht** | Een technische bevestiging (TA1) retour neren aan de verzender van het uitwisselings certificaat. |
| **FA verwacht** | Een functie-bevestigingen (FA) retour neren naar de verzender van het uitwisselings certificaat. <p>Selecteer de 997-of 999-bevestigingen op basis van de schema versie voor de eigenschap **VA-versie** . <p>Als u het genereren van AK2-lussen in functionele bevestigingen voor geaccepteerde transactie sets wilt inschakelen, selecteert u **AK2/IK2-lus toevoegen**. |
||||

<a name="inbound-schemas"></a>

### <a name="receive-settings---schemas"></a>Instellingen ontvangen-Schema's

![Schema's voor inkomende berichten](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-schemas.png)

Selecteer voor deze sectie een [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) uit uw [integratie account](./logic-apps-enterprise-integration-create-integration-account.md) voor elk transactie type (ST01) en Sender Application (GS02). De EDI receive-pijp lijn ontleedt het inkomende bericht door te voldoen aan de waarden en het schema dat u in deze sectie hebt ingesteld met de waarden voor ST01 en GS02 in het inkomende bericht en met het schema van het binnenkomende bericht. Nadat u elke rij hebt voltooid, wordt er automatisch een nieuwe lege rij weer gegeven.

| Eigenschap | Beschrijving |
|----------|-------------|
| **Versie** | De X12-versie voor het schema |
| **Transactie type (ST01)** | Het transactie type |
| **Sender Application (GS02)** | De afzender toepassing |
| **Schema** | Het schema bestand dat u wilt gebruiken |
|||

<a name="inbound-envelopes"></a>

### <a name="receive-settings---envelopes"></a>Instellingen ontvangen-enveloppen

![Scheidings tekens die moeten worden gebruikt in transactie sets voor inkomende berichten](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-envelopes.png)

| Eigenschap | Beschrijving |
|----------|-------------|
| **ISA11 gebruik** | Het scheidings teken dat in een transactieset moet worden gebruikt: <p>- **Standaard-id**: gebruik een punt (.) voor de decimale notatie in plaats van de decimale notatie van het inkomende document in de EDI-ontvangst pijplijn. <p>- **Herhalings scheidings teken**: Geef het scheidings teken voor herhaalde exemplaren van een eenvoudig gegevens element of een herhaalde gegevens structuur op. Meestal wordt de dakje (^) gebruikt als scheidings teken voor herhalingen. Voor HIPAA-schema's kunt u alleen de dakje gebruiken. |
|||

<a name="inbound-control-numbers"></a>

### <a name="receive-settings---control-numbers"></a>Instellingen ontvangen-controle nummers

![Controle nummer duplicaten voor inkomende berichten verwerken](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-control-numbers.png) 

| Eigenschap | Beschrijving |
|----------|-------------|
| **Dubbele uitwisselings controle nummers niet toestaan** | Dubbele interwijzigingen blok keren. Controleer het uitwisselings controle nummer (ISA13) voor het ontvangen Interchange Control-nummer. Als er een overeenkomst wordt gedetecteerd, verwerkt de EDI receive-pijp lijn de uitwisseling niet. <p><p>Als u het aantal dagen wilt opgeven dat de controle moet worden uitgevoerd, voert u een waarde in voor de eigenschap **controleren op dubbele ISA13 elke (dagen)** . |
| **Dubbele items van groeps controle nummers niet toestaan** | Interwijzigingen met dubbele groeps controle nummers blok keren. |
| **Geen duplicaten voor het instellen van transactie sets toestaan** | Interacties blok keren die dubbele trans acties hebben voor het instellen van controle nummers. |
|||

<a name="inbound-validations"></a>

### <a name="receive-settings---validations"></a>Instellingen ontvangen-validaties

![Validaties voor inkomende berichten](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-validations.png)

De **standaard** rij bevat de validatie regels die worden gebruikt voor een EDI-bericht type. Als u verschillende regels wilt definiëren, selecteert u elk vak waar u de regel wilt instellen op **waar**. Nadat u elke rij hebt voltooid, wordt er automatisch een nieuwe lege rij weer gegeven.

| Eigenschap | Beschrijving |
|----------|-------------|
| **Bericht type** | Het EDI-bericht type |
| **EDI-validatie** | Voer EDI-validatie uit op gegevens typen zoals gedefinieerd door de EDI-eigenschappen, lengte beperkingen, lege gegevens elementen en navolgende scheidings tekens van het schema. |
| **Uitgebreide validatie** | Als het gegevens type niet EDI is, is validatie van de vereiste voor het gegevens element en de toegestane herhaling, opsommingen en gegevens element lengte validatie (min of Max). |
| **Voor loop-en volg nullen toestaan** | Bewaar eventuele extra voor loop-of volg spaties. Verwijder deze tekens niet. |
| **Voor loop-en volg nullen bijsnijden** | Verwijder alle voor loop-of volg spaties. |
| **Volg scheidings beleid** | Navolgende scheidings tekens genereren. <p>- **Niet toegestaan**: de Volg begrenzingen en scheidings tekens in de binnenkomende uitwisseling verbieden. Als de uitwisseling navolgende scheidings tekens en schei heeft, wordt de uitwisseling gedeclareerd als ongeldig. <p>- **Optioneel**: interwijzigingen accepteren met of zonder navolgende scheidings tekens en schei. <p>- **Verplicht**: de inkomende uitwisseling moet volg scheidings tekens en scheidings tekens bevatten. |
|||

<a name="inbound-internal-settings"></a>

### <a name="receive-settings---internal-settings"></a>Instellingen ontvangen-interne instellingen

![Interne instellingen voor inkomende berichten](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-internal-settings.png)

| Eigenschap | Beschrijving |
|----------|-------------|
| **De geïmpliceerde decimale notatie nn converteren naar een numerieke waarde met grondtal 10** | Een EDI-nummer dat is opgegeven met de indeling "nn" converteren naar een numerieke waarde met grondtal 10. |
| **Lege XML-tags maken als navolgende scheidings tekens zijn toegestaan** | Laat de verzender van de uitwisseling lege XML-tags bevatten voor navolgende scheidings tekens. |
| **Gesplitste uitwisseling als transactie sets-transactie sets uitstellen bij fout** | Parseer elke transactie groep in een uitwisseling in een afzonderlijk XML-document door de juiste envelop toe te passen op de transactieset. Alleen de trans acties onderbreken waarvoor de validatie is mislukt. |
| **Gesplitste uitwisseling als transactie sets: uitwisseling onderbreken bij fout** | Parseer elke transactieset in een uitwisseling in een afzonderlijk XML-document door de juiste envelop toe te passen. De volledige uitwisseling onderbreken wanneer een of meer transactie sets in de uitwisseling mislukt. |
| **Trans actie sets met uitwisselingen behouden bij fout** | Laat de uitwisseling intact en maak een XML-document voor de gehele gebatcheerde uitwisseling. Onderbreek alleen de transactie sets die niet zijn gevalideerd, maar blijf alle andere transactie sets verwerken. |
| **Uitwisseling van uitwisseling met fouten behouden** |Laat de uitwisseling intact, maakt een XML-document voor de gehele gebatcheerde uitwisseling. Hiermee wordt het hele uitwisselings proces onderbroken wanneer een of meer transactie sets in de uitwisseling mislukt. |
|||

<a name="send-settings"></a>

## <a name="send-settings"></a>Instellingen verzenden

Nadat u de eigenschappen van de overeenkomst hebt ingesteld, kunt u configureren hoe deze overeenkomst uitgaande berichten identificeert en verwerkt die u via deze overeenkomst naar uw partner verzendt.

1. Onder **toevoegen** selecteert u **instellingen verzenden**.

1. Configureer deze eigenschappen op basis van uw overeenkomst met de partner die berichten met u uitwisselt. Zie voor beschrijvingen van eigenschappen de tabellen in deze sectie.

   De **instellingen voor verzenden** zijn ingedeeld in de volgende secties:

   * [Id's](#outbound-identifiers)
   * [Bevestigings](#outbound-acknowledgement)
   * [Schema's](#outbound-schemas)
   * [Enveloppen](#outbound-envelopes)
   * [Versie nummer van besturings element](#outbound-control-version-number)
   * [Controle nummers](#outbound-control-numbers)
   * [Teken sets en scheidings tekens](#outbound-character-sets-separators)
   * [Validatie](#outbound-validation)

1. Wanneer u klaar bent, moet u de instellingen opslaan door **OK** te selecteren.

<a name="outbound-identifiers"></a>

### <a name="send-settings---identifiers"></a>Instellingen verzenden-Id's

![Id-eigenschappen voor uitgaande berichten](./media/logic-apps-enterprise-integration-x12/x12-send-settings-identifiers.png)

| Eigenschap | Beschrijving |
|----------|-------------|
| **ISA1 (autorisatie kwalificatie)** | De waarde van de autorisatie kwalificatie die u wilt gebruiken. De standaard waarde is **00-er is geen autorisatie-informatie aanwezig**. <p>**Opmerking**: als u andere waarden selecteert, geeft u een waarde op voor de eigenschap **ISA2** . |
| **ISA2** | De waarde van de autorisatie gegevens die moet worden gebruikt wanneer de eigenschap **ISA1** niet **00 is: er zijn geen autorisatie gegevens aanwezig**. Deze eigenschaps waarde moet mini maal één alfanumeriek teken en Maxi maal 10 hebben. |
| **ISA3 (beveiligings kwalificatie)** | De waarde van de beveiligings kwalificatie die u wilt gebruiken. De standaard waarde is **00: er zijn geen beveiligings gegevens aanwezig**. <p>**Opmerking**: als u andere waarden selecteert, geeft u een waarde op voor de eigenschap **ISA4** . |
| **ISA4** | De waarde voor de beveiligings gegevens die moet worden gebruikt wanneer de eigenschap **ISA3** niet **00 is: er is geen beveiligings informatie aanwezig**. Deze eigenschaps waarde moet mini maal één alfanumeriek teken en Maxi maal 10 hebben. |
|||

<a name="outbound-acknowledgement"></a>

### <a name="send-settings---acknowledgement"></a>Instellingen verzenden-bevestiging

![Bevestigings eigenschappen voor uitgaande berichten](./media/logic-apps-enterprise-integration-x12/x12-send-settings-acknowledgement.png)

| Eigenschap | Beschrijving |
|----------|-------------|
| **TA1 verwacht** | Een technische bevestiging (TA1) retour neren aan de verzender van het uitwisselings certificaat. <p>Met deze instelling geeft u op dat de host-partner die het bericht verzendt, een bevestiging vraagt van de gast partner in de overeenkomst. Deze bevestigingen worden verwacht door de host-partner op basis van de ontvangst instellingen van de overeenkomst. |
| **FA verwacht** | Een functie-bevestigingen (FA) retour neren naar de verzender van het uitwisselings certificaat. Selecteer de 997-of 999-bevestigingen op basis van de schema versie voor de eigenschap **VA-versie** . <p>Met deze instellingen geeft u op dat de host-partner die het bericht verzendt, een bevestiging vraagt van de gast partner in de overeenkomst. Deze bevestigingen worden verwacht door de host-partner op basis van de ontvangst instellingen van de overeenkomst. |
|||

<a name="outbound-schemas"></a>

### <a name="send-settings---schemas"></a>Instellingen verzenden-Schema's

![Schema's voor uitgaande berichten](./media/logic-apps-enterprise-integration-x12/x12-send-settings-schemas.png)

Selecteer voor deze sectie een [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) uit uw [integratie account](./logic-apps-enterprise-integration-create-integration-account.md) voor elk transactie type (ST01). Nadat u elke rij hebt voltooid, wordt er automatisch een nieuwe lege rij weer gegeven.

| Eigenschap | Beschrijving |
|----------|-------------|
| **Versie** | De X12-versie voor het schema |
| **Transactie type (ST01)** | Het transactie type voor het schema |
| **Schema** | Het schema bestand dat u wilt gebruiken. Als u eerst het schema selecteert, worden de versie en het transactie type automatisch ingesteld. |
|||

<a name="outbound-envelopes"></a>

### <a name="send-settings---envelopes"></a>Instellingen verzenden-enveloppen

![Scheidings tekens in een trans actie die moeten worden gebruikt voor uitgaande berichten](./media/logic-apps-enterprise-integration-x12/x12-send-settings-envelopes.png)

| Eigenschap | Beschrijving |
|----------|-------------|
| **ISA11 gebruik** | Het scheidings teken dat in een transactieset moet worden gebruikt: <p>- **Standaard-id**: gebruik een punt (.) voor de decimale notatie in plaats van de decimale notatie van het uitgaande document in de EDI Send-pijp lijn. <p>- **Herhalings scheidings teken**: Geef het scheidings teken voor herhaalde exemplaren van een eenvoudig gegevens element of een herhaalde gegevens structuur op. Meestal wordt de dakje (^) gebruikt als scheidings teken voor herhalingen. Voor HIPAA-schema's kunt u alleen de dakje gebruiken. |
|||

<a name="outbound-control-version-number"></a>

### <a name="send-settings---control-version-number"></a>Instellingen verzenden-versie nummer van besturings element

![Het versie nummer van het besturings element voor uitgaande berichten](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-version-number.png)

Selecteer voor deze sectie een [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) uit uw [integratie account](./logic-apps-enterprise-integration-create-integration-account.md) voor elke uitwisseling. Nadat u elke rij hebt voltooid, wordt er automatisch een nieuwe lege rij weer gegeven.

| Eigenschap | Beschrijving |
|----------|-------------|
| **Controle versie nummer (ISA12)** | De versie van de X12-standaard |
| **Gebruiks indicator (ISA15)** | De context van een uitwisseling, ofwel **test** gegevens, **informatie** gegevens of **productie** gegevens |
| **Schema** | Het schema dat moet worden gebruikt voor het genereren van de GS-en ST-segmenten voor een met X12 gecodeerde uitwisseling die wordt verzonden naar de EDI Send-pijp lijn. |
| **GS1** | Optioneel, selecteer de functie code. |
| **GS2** | Optioneel, geef de afzender van de toepassing op. |
| **GS3** | Optioneel, geef de ontvanger van de toepassing op. |
| **GS4** | Optioneel, selecteer **CCYYMMDD** of **JJMMDD**. |
| **GS5** | Optioneel, selecteer **HHMM**, **hhmmss** of **HHMMSSdd**. |
| **GS7** | Optioneel, selecteer een waarde voor het verantwoordelijke instituut. |
| **GS8** | Optioneel, de versie van het schema document opgeven. |
|||

<a name="outbound-control-numbers"></a>

### <a name="send-settings---control-numbers"></a>Instellingen verzenden-controle nummers

![Controle nummers voor uitgaande berichten](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-numbers.png)

| Eigenschap | Beschrijving |
|----------|-------------|
| **Uitwisselings controle nummer (ISA13)** | Het waarden bereik voor het uitwisselings controle nummer, dat een minimum van waarde 1 en een maximum waarde van 999999999 kan hebben |
| **Groeps controle nummer (GS06)** | Het waarden bereik voor het groeps besturings element nummer, dat een minimum waarde van 1 en een maximum waarde van 999999999 kan hebben |
| **ST02 (trans actie set Control Number)** | Het waarden bereik voor het besturings nummer van de transactieset, die een minimum waarde van 1 en een maximum waarde van 999999999 kunnen hebben <p>- **Voor voegsel**: optioneel, een alfanumerieke waarde <br>- **Achtervoegsel**: optioneel, een alfanumerieke waarde |
|||

<a name="outbound-character-sets-separators"></a>

### <a name="send-settings---character-sets-and-separators"></a>Instellingen verzenden: teken sets en scheidings tekens

![Scheidings tekens voor bericht typen in uitgaande berichten](./media/logic-apps-enterprise-integration-x12/x12-send-settings-character-sets-separators.png)

De **standaard** rij toont de tekenset die wordt gebruikt als scheidings teken voor een bericht schema. Als u de **standaard** tekenset niet wilt gebruiken, kunt u een andere set scheidings tekens voor elk bericht type opgeven. Nadat u elke rij hebt voltooid, wordt er automatisch een nieuwe lege rij weer gegeven.

> [!TIP]
> Als u speciale teken waarden wilt opgeven, bewerkt u de overeenkomst als JSON en geeft u de ASCII-waarde voor het speciale teken op.

| Eigenschap | Beschrijving |
|----------|-------------|
| **Tekenset die moet worden gebruikt** | De X12-tekenset, een **Basic**, **Extended** of **utf8**. |
| **Schema** | Het schema dat u wilt gebruiken. Nadat u het schema hebt geselecteerd, selecteert u de tekenset die u wilt gebruiken, op basis van de onderstaande scheidings tekens. |
| **Invoer type** | Het invoer type voor de tekenset |
| **Onderdeel scheidings teken** | Eén teken waarmee samengestelde gegevens elementen worden gescheiden |
| **Scheidings teken voor gegevens elementen** | Eén teken waarmee eenvoudige gegevens elementen worden gescheiden in samengestelde gegevens |
| **vervangende teken scheiding** | Een vervangings teken dat alle scheidings tekens in de payload-gegevens vervangt bij het genereren van het uitgaande X12-bericht |
| **Segment Terminator** | Een enkel teken dat het einde van een EDI-segment aangeeft |
| **Achtervoegsel** | Het teken dat moet worden gebruikt met de segment-id. Als u een achtervoegsel opgeeft, kan het gegevens element van het segment Terminator leeg zijn. Als de eind afsluit van het segment leeg blijft, moet u een achtervoegsel aanwijzen. |
|||

<a name="outbound-validation"></a>

### <a name="send-settings---validation"></a>Instellingen verzenden-validatie

![Validatie-eigenschappen voor uitgaande berichten](./media/logic-apps-enterprise-integration-x12/x12-send-settings-validation.png) 

De **standaard** rij bevat de validatie regels die worden gebruikt voor een EDI-bericht type. Als u verschillende regels wilt definiëren, selecteert u elk vak waar u de regel wilt instellen op **waar**. Nadat u elke rij hebt voltooid, wordt er automatisch een nieuwe lege rij weer gegeven.

| Eigenschap | Beschrijving |
|----------|-------------|
| **Bericht type** | Het EDI-bericht type |
| **EDI-validatie** | Voer EDI-validatie uit op gegevens typen zoals gedefinieerd door de EDI-eigenschappen, lengte beperkingen, lege gegevens elementen en navolgende scheidings tekens van het schema. |
| **Uitgebreide validatie** | Als het gegevens type niet EDI is, is validatie van de vereiste voor het gegevens element en de toegestane herhaling, opsommingen en gegevens element lengte validatie (min of Max). |
| **Voor loop-en volg nullen toestaan** | Bewaar eventuele extra voor loop-of volg spaties. Verwijder deze tekens niet. |
| **Voor loop-en volg nullen bijsnijden** | Verwijder alle voor loop-of volg spaties. |
| **Volg scheidings beleid** | Navolgende scheidings tekens genereren. <p>- **Niet toegestaan**: eind scheidingen en scheidings tekens in de uitgaande uitwisseling verbieden. Als de uitwisseling navolgende scheidings tekens en schei heeft, wordt de uitwisseling gedeclareerd als ongeldig. <p>- **Optioneel**: verwisselt met of zonder navolgende scheidings tekens en schei dingen. <p>- **Verplicht**: de uitgaande uitwisseling moet volg scheidings tekens en scheidings tekens bevatten. |
|||

<a name="hipaa-schemas"></a>

## <a name="hipaa-schemas-and-message-types"></a>HIPAA-schema's en-bericht typen

Wanneer u werkt met HIPAA-schema's en de 277-of 837-bericht typen, moet u een paar extra stappen uitvoeren. De [document versie nummers (gs8)](#outbound-control-version-number) voor deze bericht typen bevatten meer dan 9 tekens, bijvoorbeeld ' 005010X222A1 '. Sommige document versie nummers worden ook toegewezen aan de typen van de variant-berichten. Als u niet naar het juiste bericht type verwijst in uw schema en in uw overeenkomst, wordt dit fout bericht weer gegeven:

`"The message has an unknown document type and did not resolve to any of the existing schemas configured in the agreement."`

Deze tabel bevat de betrokken bericht typen, varianten en de document versie nummers die zijn toegewezen aan deze bericht typen:

| Bericht type of-variant |  Beschrijving | Document versie nummer (GS8) |
|-------------------------|--------------|-------------------------------|
| 277 | Melding van status van informatie over gezondheids zorg | 005010X212 |
| 837_I | Gezondheids zorg claim institutionele | 004010X096A1 <br>005010X223A1 <br>005010X223A2 |
| 837_D | Claim tand gezondheids zorg | 004010X097A1 <br>005010X224A1 <br>005010X224A2 |
| 837_P | Claim medewerker gezondheids zorg | 004010X098A1 <br>005010X222 <br>005010X222A1 |
|||

U moet ook EDI-validatie uitschakelen wanneer u deze document versie nummers gebruikt, omdat ze een fout veroorzaken waardoor de teken lengte ongeldig is.

Voer de volgende stappen uit om deze document versie nummers en bericht typen op te geven:

1. Vervang in uw HIPAA-schema het huidige bericht type door het bericht type variant voor het document versie nummer dat u wilt gebruiken.

   Stel dat u het document versie nummer `005010X222A1` met het bericht type wilt gebruiken `837` . Vervang in uw schema elke `"X12_00501_837"` waarde door de `"X12_00501_837_P"` waarde in plaats daarvan.

   Voer de volgende stappen uit om het schema bij te werken:

   1. Ga in het Azure Portal naar uw integratie account. Zoek en down load het schema. Vervang het bericht type en wijzig de naam van het schema bestand en upload uw gereviseerde schema naar uw integratie account. Zie [Schema's bewerken](../logic-apps/logic-apps-enterprise-integration-schemas.md#edit-schemas)voor meer informatie.

   1. In de bericht instellingen van uw overeenkomst selecteert u het gewijzigde schema.

1. Voeg in het object van de overeenkomst `schemaReferences` een andere vermelding toe die het type variant bericht opgeeft dat overeenkomt met het versie nummer van uw document.

   Stel dat u het document versie nummer `005010X222A1` voor het bericht type wilt gebruiken `837` . Uw overeenkomst heeft een `schemaReferences` sectie met de volgende eigenschappen en waarden:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      }
   ]
   ```

   In deze `schemaReferences` sectie voegt u een andere vermelding toe met de volgende waarden:

   * `"messageId": "837_P"`
   * `"schemaVersion": "00501"`
   * `"schemaName": "X12_00501_837_P"`

   Wanneer u klaar bent, `schemaReferences` ziet uw sectie er als volgt uit:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      },
      {
         "messageId": "837_P",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837_P"
      }
   ]
   ```

1. Schakel in de bericht instellingen van uw overeenkomst EDI-validatie uit door het selectie vakje **EDI-validatie** uit te scha kelen voor elk bericht type of voor alle bericht typen als u de **standaard** waarden gebruikt.

   ![Validatie uitschakelen voor alle bericht typen of elk bericht type](./media/logic-apps-enterprise-integration-x12/x12-disable-validation.png) 

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentie pagina van de connector](/connectors/x12/)voor aanvullende technische informatie over deze connector, zoals acties en limieten zoals beschreven in het Swagger-bestand van de connector.

> [!NOTE]
> Voor Logic apps in een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), gebruikt de ISE-label versie van deze connector de [limieten voor B2B-berichten voor ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [connectors voor Logic apps](../connectors/apis-list.md)
