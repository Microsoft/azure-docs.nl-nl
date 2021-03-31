---
title: Gecorreleerde berichten in volg orde verzenden met behulp van een sequentiële verwerkings
description: Verzend gerelateerde berichten in de juiste volg orde door gebruik te maken van het sequentiële verwerkings patroon in Azure Logic Apps met Azure Service Bus
services: logic-apps
ms.suite: integration
ms.reviewer: apseth, divswa, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 8c00d2e4f622bcfad7b2468013336f0d936e318c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87048665"
---
# <a name="send-related-messages-in-order-by-using-a-sequential-convoy-in-azure-logic-apps-with-azure-service-bus"></a>Verzend gerelateerde berichten in de juiste volg orde door gebruik te maken van een opeenvolgende verwerkings in Azure Logic Apps met Azure Service Bus

Wanneer u gecorreleerde berichten in een specifieke volg orde wilt verzenden, kunt u het [ *sequentiële verwerkings* patroon](/azure/architecture/patterns/sequential-convoy) volgen wanneer u [Azure Logic apps](../logic-apps/logic-apps-overview.md) gebruikt met behulp van de [Azure service bus-connector](../connectors/connectors-create-api-servicebus.md). Gerelateerde berichten hebben een eigenschap die de relatie tussen deze berichten definieert, zoals de ID van de [sessie](../service-bus-messaging/message-sessions.md) in service bus.

Stel bijvoorbeeld dat u 10 berichten hebt voor een sessie met de naam ' sessie 1 ' en u vijf berichten hebt voor een sessie met de naam ' sessie 2 ' die allemaal naar dezelfde [Service Bus wachtrij](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)worden verzonden. U kunt een logische app maken die berichten uit de wachtrij verwerkt, zodat alle berichten van "sessie 1" worden verwerkt door één trigger uitvoering en alle berichten van sessie 2 worden verwerkt door de volgende trigger uitvoering.

![Algemeen sequentieel verwerkings-patroon](./media/send-related-messages-sequential-convoy/sequential-convoy-pattern-general.png)

In dit artikel wordt beschreven hoe u een logische app maakt die dit patroon implementeert door gebruik te maken van de **gecorreleerde in-volg orde, met behulp van service bus-sessie** sjabloon. Met deze sjabloon wordt een werk stroom voor logische apps gedefinieerd die begint met de Service Bus-Connector **Wanneer een bericht wordt ontvangen in een wachtrij (Peek-Lock)** , die berichten ontvangt van een [Service Bus wachtrij](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). Dit zijn de stappen op hoog niveau die door deze logische app worden uitgevoerd:

* Initialiseer een sessie op basis van een bericht dat de trigger van de Service Bus wachtrij leest.

* Alle berichten van dezelfde sessie in de wachtrij lezen en verwerken tijdens de huidige uitvoering van de werk stroom.

Als u het JSON-bestand van deze sjabloon wilt bekijken, raadpleegt u [github: service-bus-sessions.jsop](https://github.com/Azure/logicapps/blob/master/templates/service-bus-sessions.json).

Zie voor meer informatie [sequentieel verwerkings-patroon: Azure Architecture Cloud-ontwerp patronen](/azure/architecture/patterns/sequential-convoy).

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een Service Bus naam ruimte en een [Service Bus wachtrij](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). Dit is een Messa ging-entiteit die u in uw logische app gaat gebruiken. Deze items en uw logische app moeten gebruikmaken van hetzelfde Azure-abonnement. Zorg ervoor dat u **sessies inschakelen** selecteert bij het maken van de wachtrij. Als u deze items niet hebt, leert u [hoe u uw service bus naam ruimte en een wachtrij kunt maken](../service-bus-messaging/service-bus-create-namespace-portal.md).

  [!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

* Basis kennis over het maken van logische apps. Als u nog geen ervaring hebt met Azure Logic Apps, kunt u de Snelstartgids proberen om [uw eerste geautomatiseerde werk stroom te maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-access-to-service-bus-namespace"></a>Toegang tot Service Bus naam ruimte controleren

Als u niet zeker weet of uw logische app machtigingen heeft voor toegang tot uw Service Bus naam ruimte, moet u deze machtigingen bevestigen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Zoek en selecteer uw Service Bus *naam ruimte*.

1. Selecteer in het menu naam ruimte onder **instellingen** de optie **beleid voor gedeelde toegang**. Onder **claims** controleert u of u machtigingen voor het **beheren** van die naam ruimte hebt.

   ![Machtigingen voor Service Bus naam ruimte beheren](./media/send-related-messages-sequential-convoy/check-service-bus-permissions.png)

1. Down load nu de connection string voor uw Service Bus naam ruimte. U kunt deze teken reeks later gebruiken wanneer u een verbinding met de naam ruimte maakt vanuit uw logische app.

   1. Selecteer in het deel venster **Shared Access policies** onder **Policy** de optie **RootManageSharedAccessKey**.
   
   1. Selecteer de knop kopiëren naast uw primaire connection string. Sla de connection string op voor later gebruik.

      ![Service Bus naam ruimte connection string kopiëren](./media/send-related-messages-sequential-convoy/copy-service-bus-connection-string.png)

   > [!TIP]
   > Als u wilt controleren of uw connection string is gekoppeld aan uw Service Bus naam ruimte of een bericht entiteit, zoals een wachtrij, zoekt u de connection string voor de `EntityPath` para meter. Als u deze para meter vindt, is de connection string voor een specifieke entiteit en is de juiste teken reeks niet geschikt voor gebruik met uw logische app.

## <a name="create-logic-app"></a>Logische app maken

In deze sectie maakt u een logische app met behulp van de sjabloon voor de **gecorreleerde volg orde met Service Bus-sessies** . Dit omvat de trigger en acties voor het implementeren van dit werk stroom patroon. U maakt ook een verbinding met uw Service Bus naam ruimte en geeft de naam op voor de Service Bus wachtrij die u wilt gebruiken.

1. Maak in de [Azure Portal](https://portal.azure.com)een lege logische app. Selecteer op de start pagina van Azure **een resource**  >  **Integration**  >  **Logic-app** maken.

1. Nadat de sjabloon galerie wordt weer gegeven, schuift u voorbij de secties video en algemene triggers. Selecteer in de sectie **sjablonen** de sjabloon, **met behulp van service bus-sessies, gecorreleerde in-volg orde**.

   ![Selecteer de sjabloon "gecorreleerde in-bestelling met Service Bus-sessies"](./media/send-related-messages-sequential-convoy/select-correlated-in-order-delivery-template.png)

1. Wanneer het bevestigings venster wordt weer gegeven, selecteert **u deze sjabloon gebruiken**.

1. Selecteer in de ontwerp functie voor logische apps  in de vorm service bus **door gaan** en selecteer vervolgens het plus teken ( **+** ) dat wordt weer gegeven in de vorm.

   ![Selecteer door gaan om verbinding te maken met Azure Service Bus](./media/send-related-messages-sequential-convoy/connect-to-service-bus.png)

1. Maak nu een Service Bus verbinding door een van de volgende opties te kiezen:

   * Voer de volgende stappen uit om de connection string te gebruiken die u eerder hebt gekopieerd uit de naam ruimte van uw Service Bus:

     1. Selecteer **hand matig verbindings gegevens invoeren**.

     1. Voor de **verbindings naam** geeft u een naam op voor de verbinding. Plak uw naam ruimte in de **verbindings reeks** Connection String en selecteer **maken**, bijvoorbeeld:

        ![Voer de verbindings naam en het Service Bus in connection string](./media/send-related-messages-sequential-convoy/provide-service-bus-connection-string.png)

        > [!TIP]
        > Als u deze connection string niet hebt, leert u hoe u [de service bus naam ruimte Connection String kunt zoeken en kopiëren](#permissions-connection-string).

   * Voer de volgende stappen uit om een Service Bus naam ruimte te selecteren in het huidige Azure-abonnement:

     1. Voor de **verbindings naam** geeft u een naam op voor de verbinding. Voor **Service Bus naam ruimte** selecteert u uw service bus naam ruimte, bijvoorbeeld:

        ![Voer de verbindings naam in en selecteer Service Bus naam ruimte](./media/send-related-messages-sequential-convoy/create-service-bus-connection.png)

     1. Wanneer het volgende deel venster wordt weer gegeven, selecteert u uw Service Bus beleid en selecteert u **maken**.

        ![Selecteer Service Bus beleid en vervolgens ' maken '](./media/send-related-messages-sequential-convoy/create-service-bus-connection-2.png)

1. Wanneer u klaar bent, selecteert u **door gaan**.

   In de ontwerp functie voor logische apps wordt nu de **gecorreleerde in-volg orde weer gegeven met behulp van service bus-sessie** sjabloon, die een vooraf gevulde werk stroom met een trigger en acties bevat, met inbegrip van twee bereiken die de fout afhandeling implementeren die het `Try-Catch` patroon volgen.

U kunt nu meer te weten komen over de trigger en acties in de sjabloon, of door gaan om [de waarden voor de sjabloon van de logische app](#complete-template)op te geven.

<a name="template-summary"></a>

## <a name="template-summary"></a>Samen vatting van sjabloon

Dit is de werk stroom op het hoogste niveau in de **gecorreleerde in-volg orde waarin de service bus-sessie** sjabloon wordt gebruikt wanneer de details zijn samengevouwen:

![Werk stroom van het hoogste niveau van de sjabloon](./media/send-related-messages-sequential-convoy/template-top-level-flow.png)

| Naam | Beschrijving |
|------|-------------|
| **`When a message is received in a queue (peek-lock)`** | Op basis van het opgegeven terugkeer patroon controleert deze Service Bus trigger de opgegeven Service Bus wachtrij op berichten. Als er een bericht in de wachtrij staat, wordt de trigger geactiveerd, waarmee een werk stroom exemplaar wordt gemaakt en uitgevoerd. <p><p>De term *Peek-Lock* betekent dat de trigger een aanvraag verzendt om een bericht uit de wachtrij op te halen. Als er een bericht bestaat, wordt het bericht door de trigger opgehaald en vergrendeld zodat er geen andere verwerking op dat bericht plaatsvindt totdat de vergrendelings periode is verstreken. [Initialiseer de sessie](#initialize-session)voor meer informatie. |
| **`Init isDone`** | Deze [actie voor **het initialiseren van variabelen**](../logic-apps/logic-apps-create-variables-store-values.md#initialize-variable) maakt een Booleaanse variabele die is ingesteld op `false` en geeft aan wanneer aan de volgende voor waarden wordt voldaan: <p><p>-Er zijn niet meer berichten in de sessie beschikbaar om te lezen. <br>-De sessie vergrendeling hoeft niet meer te worden vernieuwd zodat het huidige workflowexemplaar kan worden voltooid. <p><p>Zie [de sessie initialiseren](#initialize-session)voor meer informatie. |
| **`Try`** | Deze [ **bereik** actie](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) bevat de acties die worden uitgevoerd om een bericht te verwerken. Als er een probleem in het `Try` bereik optreedt, wordt dat probleem door de volgende `Catch` **bereik** actie afgehandeld. Zie voor meer informatie het [bereik ' Try '](#try-scope). |
| **`Catch`**| Deze [ **bereik** actie](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) bevat de acties die worden uitgevoerd als er een probleem optreedt in het voor gaande `Try` bereik. Zie [' catch ' scope](#catch-scope)voor meer informatie. |
|||

<a name="try-scope"></a>

### <a name="try-scope"></a>Het bereik ' Try '

Dit is de stroom op het hoogste niveau in de `Try` [bereik actie](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) wanneer de details zijn samengevouwen:

![De werk stroom voor bereik acties ' Try '](./media/send-related-messages-sequential-convoy/try-scope-action.png)

| Naam | Beschrijving |
|------|-------------|
| **`Send initial message to topic`** | U kunt deze actie vervangen door de actie die u het eerste bericht wilt afhandelen vanuit de sessie in de wachtrij. De sessie-ID geeft de sessie aan. <p><p>Voor deze sjabloon verzendt een Service Bus actie het eerste bericht naar een Service Bus onderwerp. Zie [het eerste bericht afhandelen](#handle-initial-message)voor meer informatie. |
| (parallelle vertakking) | Met deze [actie parallelle vertakking](../logic-apps/logic-apps-control-flow-branches.md) maakt u twee paden: <p><p>-Branch #1: door gaan met het verwerken van het bericht. Zie [Branch #1: het eerste bericht in de wachtrij volt ooien](#complete-initial-message)voor meer informatie. <p><p>-Branch #2: Trek het bericht af als er iets fout gaat en laat het ophalen door een andere trigger uitvoeren. Zie voor meer informatie [vertakking #2: eerste bericht van wachtrij afbreken](#abandon-initial-message). <p><p>Beide paden worden later in de **sluitings sessie in een wachtrij geplaatst en** de actie geslaagd, zoals beschreven in de volgende rij. |
| **`Close a session in a queue and succeed`** | Deze Service Bus actie wordt toegevoegd aan de eerder beschreven branches en sluit de sessie in de wachtrij nadat een van de volgende gebeurtenissen zich voordoet: <p><p>-De werk stroom is klaar met de verwerking van beschik bare berichten in de wachtrij. <br>-De werk stroom verlaat het eerste bericht omdat er iets fout is gegaan. <p><p>Zie [een sessie in een wachtrij sluiten en slagen](#close-session-succeed)voor meer informatie. |
|||

<a name="complete-initial-message"></a>

#### <a name="branch-1-complete-initial-message-in-queue"></a>Vertakking #1: het eerste bericht in de wachtrij volt ooien

| Naam | Beschrijving |
|------|-------------|
| `Complete initial message in queue` | Met deze Service Bus actie wordt het ophalen van een bericht als voltooid gemarkeerd en wordt het bericht uit de wachtrij verwijderd om te voor komen dat de bewerking opnieuw wordt uitgevoerd. Zie [het eerste bericht afhandelen](#handle-initial-message)voor meer informatie. |
| `While there are more messages for the session in the queue` | De [lus wordt **pas**](../logic-apps/logic-apps-control-flow-loops.md#until-loop) uitgevoerd als er berichten worden ontvangen terwijl er berichten bestaan of totdat het één uur duurt. Zie voor meer informatie over de acties in deze lus, [terwijl er meer berichten zijn voor de sessie in de wachtrij](#while-more-messages-for-session). |
| **`Set isDone = true`** | Als er geen berichten meer bestaan, wordt met deze [actie **set variable**](../logic-apps/logic-apps-create-variables-store-values.md#set-variable) ingesteld `isDone` op `true` . |
| **`Renew session lock until cancelled`** | Dit [ **tot** een lus](../logic-apps/logic-apps-control-flow-loops.md#until-loop) zorgt ervoor dat de sessie vergrendeling wordt gehouden door deze logische app wanneer er berichten bestaan of totdat een uur wordt door gegeven. Voor meer informatie over de acties in deze lus raadpleegt u [sessie vergrendeling vernieuwen tot](#renew-session-while-messages-exist)u bent geannuleerd. |
|||

<a name="abandon-initial-message"></a>

#### <a name="branch-2-abandon-initial-message-from-the-queue"></a>Vertakking #2: eerste bericht in de wachtrij verlaten

Als de actie die het eerste bericht afhandelt mislukt, de Service Bus actie, het **eerste bericht uit de wachtrij verlaat**, het bericht vrijgegeven voor een ander werk stroom exemplaar dat wordt uitgevoerd om te worden opgehaald en verwerkt. Zie [het eerste bericht afhandelen](#handle-initial-message)voor meer informatie.

<a name="catch-scope"></a>

### <a name="catch-scope"></a>Bereik catch

Als acties in de `Try` Scope mislukken, moet de logische app de sessie nog steeds sluiten. De `Catch` [bereik actie](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) wordt uitgevoerd wanneer de `Try` bereik actie resulteert in de status, `Failed` , `Skipped` of `TimedOut` . Het bereik retourneert een fout bericht dat de sessie-ID bevat waarin het probleem zich voordeed en beëindigt de logische app.

Dit is de stroom op het hoogste niveau in de `Catch` bereik actie wanneer de details zijn samengevouwen:

![Werk stroom voor bereik acties catch](./media/send-related-messages-sequential-convoy/catch-scope-action.png)

| Naam | Beschrijving |
|------|-------------|
| **`Close a session in a queue and fail`** | Met deze Service Bus actie wordt de sessie in de wachtrij gesloten, zodat de sessie vergrendeling niet actief blijft. Zie [een sessie in een wachtrij sluiten](#close-session-fail)voor meer informatie. |
| **`Find failure msg from 'Try' block`** | Met deze actie voor de [ **filter matrix**](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) wordt een matrix gemaakt op basis van de invoer en uitvoer van alle acties binnen het `Try` bereik op basis van de opgegeven criteria. In dit geval retourneert deze actie de uitvoer van de acties die de status hebben veroorzaakt `Failed` . Zie [fout bericht in try-blok zoeken](#find-failure-message)voor meer informatie. |
| **`Select error details`** | Met deze [actie **Select**](../logic-apps/logic-apps-perform-data-operations.md#select-action) wordt een matrix gemaakt die JSON-objecten bevat op basis van de opgegeven criteria. Deze JSON-objecten worden samengesteld op basis van de waarden in de matrix die is gemaakt door de vorige actie, `Find failure msg from 'Try' block` . In dit geval retourneert deze actie een matrix die een JSON-object bevat dat is gemaakt op basis van de fout details die zijn geretourneerd door de vorige actie. Zie [Fout Details selecteren](#select-error-details)voor meer informatie. |
| **`Terminate`** | Deze [actie **beëindigen**](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) stopt de uitvoering van de werk stroom, annuleert acties die worden uitgevoerd, slaat alle resterende acties over en retourneert de opgegeven status, de sessie-id en het fout resultaat van de `Select error details` actie. Zie [Logic app beëindigen](#terminate-logic-app)voor meer informatie. |
|||

<a name="complete-template"></a>

## <a name="complete-the-template"></a>De sjabloon volt ooien

Ga als volgt te werk om de waarden op te geven voor de trigger en acties in de **gecorreleerde volg orde van levering via service bus-sessies** sjabloon. U moet alle vereiste waarden opgeven, die zijn gemarkeerd met een asterisk ( **\*** ) voordat u uw logische app kunt opslaan.

<a name="initialize-session"></a>

### <a name="initialize-the-session"></a>De sessie initialiseren

* Voor de trigger **Wanneer een bericht wordt ontvangen in een wachtrij (Peek-Lock)** , geeft u deze informatie zodat de sjabloon een sessie kan initialiseren met behulp van de eigenschap **sessie-id** , bijvoorbeeld:

  ![Service Bus trigger Details voor ' wanneer een bericht wordt ontvangen in een wachtrij (Peek-Lock) '](./media/send-related-messages-sequential-convoy/service-bus-check-message-peek-lock-trigger.png)

  > [!NOTE]
  > In eerste instantie is het polling-interval ingesteld op drie minuten, zodat de logische app niet vaker wordt uitgevoerd dan verwacht en leidt tot onverwachte facturerings kosten. In het ideale geval stelt u het interval en de frequentie in op 30 seconden, zodat de logische app direct wordt geactiveerd wanneer er een bericht binnenkomt.

  | Eigenschap | Vereist voor dit scenario | Waarde | Beschrijving |
  |----------|----------------------------|-------|-------------|
  | **Wachtrijnaam** | Ja | <*wachtrij naam*> | De naam voor uw eerder gemaakte Service Bus wachtrij. In dit voor beeld wordt gebruikgemaakt van Fabrikam-service-bus-wachtrij. |
  | **Wachtrij type** | Ja | **Hoofd** | Uw primaire Service Bus wachtrij |
  | **Sessie-id** | Ja | **Volgende beschikbaar** | Met deze optie wordt een sessie opgehaald voor elke trigger die wordt uitgevoerd op basis van de sessie-ID van het bericht in de Service Bus wachtrij. De sessie wordt ook vergrendeld, zodat er geen andere logische app of andere client berichten kan verwerken die aan deze sessie zijn gerelateerd. Met de volgende acties van de werk stroom worden alle berichten verwerkt die aan die sessie zijn gekoppeld, zoals verderop in dit artikel wordt beschreven. <p><p>Hier vindt u meer informatie over de andere **sessie-id-** opties: <p>- **Geen**: de standaard optie, waardoor er geen sessies zijn en kunnen niet worden gebruikt voor het implementeren van het sequentiële verwerkings-patroon. <p>- **Aangepaste waarde invoeren**: gebruik deze optie wanneer u de sessie-id kent die u wilt gebruiken, en u de trigger altijd wilt uitvoeren voor die sessie-id. <p>**Opmerking**: de service bus-connector kan een beperkt aantal unieke sessies per keer opslaan van Azure service bus naar de connector cache. Als het aantal sessies groter is dan deze limiet, worden oude sessies verwijderd uit de cache. Zie [Exchange-berichten in de Cloud met Azure Logic apps en Azure service bus](../connectors/connectors-create-api-servicebus.md#connector-reference)voor meer informatie. |
  | **Interval** | Ja | <*aantal intervallen*> | Het aantal tijds eenheden tussen herhalingen voordat op een bericht wordt gecontroleerd. |
  | **Frequentie** | Ja | **Seconde**, **minuut**, **uur**, **dag**, **week** of **maand** | De tijds eenheid voor het terugkeer patroon dat moet worden gebruikt bij het controleren op een bericht. <p>**Tip**: als u een **tijd zone** of **begin tijd** wilt toevoegen, selecteert u deze eigenschappen in de lijst **nieuwe para meters toevoegen** . |
  |||||

  Zie [service bus-wanneer een bericht wordt ontvangen in een wachtrij (Peek-Lock)](/connectors/servicebus/#when-a-message-is-received-in-a-queue-(peek-lock))voor meer informatie over triggers. De trigger voert een [ServiceBusMessage](/connectors/servicebus/#servicebusmessage)uit.

Na het initialiseren van de sessie gebruikt de werk stroom de actie **variabele initialiseren** om een Booleaanse variabele te maken die in eerste instantie is ingesteld op `false` en geeft aan wanneer aan de volgende voor waarden wordt voldaan: 

* Er zijn niet meer berichten in de sessie beschikbaar om te lezen.

* De sessie vergrendeling hoeft niet meer te worden vernieuwd zodat het huidige workflowexemplaar kan worden voltooid.

![De actie Details van de variabele ' init isDone ' initialiseren](./media/send-related-messages-sequential-convoy/init-is-done-variable.png)

Vervolgens voert de werk stroom in het blok **try** acties uit op het eerste bericht dat wordt gelezen.

<a name="handle-initial-message"></a>

### <a name="handle-the-initial-message"></a>Het eerste bericht verwerken

De eerste actie is een tijdelijke Service Bus actie, **stuurt het eerste bericht naar het onderwerp**, dat u kunt vervangen door een andere actie die u het eerste bericht uit de sessie in de wachtrij wilt afhandelen. De sessie-ID geeft de sessie van waaruit het bericht afkomstig is.

Met de tijdelijke Service Bus actie wordt het eerste bericht verzonden naar een Service Bus onderwerp dat is opgegeven door de eigenschap **Session ID** . Op die manier gaan alle berichten die zijn gekoppeld aan een specifieke sessie naar hetzelfde onderwerp. Alle **sessie-id-** eigenschappen voor volgende acties in deze sjabloon gebruiken dezelfde sessie-id-waarde.

![Service Bus actie Details voor ' eerste bericht naar onderwerp verzenden '](./media/send-related-messages-sequential-convoy/send-initial-message-to-topic-action.png)

1. In de Service Bus actie **voltooit u het eerste bericht in de wachtrij**, geeft u de naam voor uw service bus wachtrij op en behoudt u alle andere standaard eigenschaps waarden in de actie.

   ![Details van de actie Service Bus voor het eerste bericht in de wachtrij volt ooien](./media/send-related-messages-sequential-convoy/complete-initial-message-queue.png)

1. In de Service Bus actie verlaat u het **eerste bericht uit de wachtrij**, geeft u de naam voor uw service bus wachtrij op en behoudt u alle andere waarden van de standaard eigenschap in de actie.

   ![Service Bus actie Details voor ' eerste bericht van de wachtrij verlaten '](./media/send-related-messages-sequential-convoy/abandon-initial-message-from-queue.png)

Vervolgens geeft u de vereiste gegevens op voor de acties die volgen op het **eerste initiële bericht in de wachtrij** actie. U begint met de acties in de **terwijl er meer berichten zijn voor de sessie in de wachtrij** -lus.

<a name="while-more-messages-for-session"></a>

### <a name="while-there-are-more-messages-for-the-session-in-the-queue"></a>Terwijl er meer berichten zijn voor de sessie in de wachtrij

Deze actie wordt [ **pas**](../logic-apps/logic-apps-control-flow-loops.md#until-loop) uitgevoerd wanneer er berichten in de wachtrij staan of totdat een uur wordt door gegeven. Als u de tijds limiet van de lus wilt wijzigen, bewerkt u de waarde van de **time-** outeigenschap van de lus.

* Extra berichten uit de wachtrij ophalen wanneer er berichten bestaan.

* Controleer het aantal resterende berichten. Als er nog steeds berichten bestaan, kunt u door gaan met het verwerken van berichten. Als er geen berichten meer bestaan, stelt de werk stroom de `isDone` variabele in op `true` en wordt de lus afgesloten.

![Until-berichten verwerken in een wachtrij](./media/send-related-messages-sequential-convoy/while-more-messages-for-session-in-queue.png)

1. Ga in de Service Bus actie naar **extra berichten van de sessie** en geef de naam op voor uw service bus wachtrij. Als dat niet het geval is, moet u alle andere standaard eigenschaps waarden in de actie houden.

   > [!NOTE]
   > Het maximum aantal berichten is standaard ingesteld op `175` , maar deze limiet wordt beïnvloed door de eigenschap bericht grootte en maximale bericht grootte in service bus. Zie [bericht grootte voor een wachtrij](../service-bus-messaging/service-bus-quotas.md)voor meer informatie.

   ![Service Bus actie: ' extra berichten van de sessie ophalen '](./media/send-related-messages-sequential-convoy/get-additional-messages-from-session.png)

   Vervolgens wordt de werk stroom gesplitst in deze parallelle vertakkingen:

   * Als er een fout of fout optreedt tijdens het controleren op extra berichten, stelt `isDone` u de variabele in op `true` .

   * De **proces berichten als er een** voor waarde is ontvangen, controleert of het aantal resterende berichten nul is. Als false en meer berichten bestaan, gaat u verder met de verwerking. Als de waarde True is en er geen berichten meer bestaan, stelt de werk stroom de `isDone` variabele in op `true` .

   ![Voorwaarde: berichten indien aanwezig verwerken](./media/send-related-messages-sequential-convoy/process-messages-if-any.png)

   In de sectie **indien onwaar** wordt **elke lus elk** bericht in First-in, first-out order (FIFO) verwerkt. In de **instellingen** van de lus is de instelling **gelijktijdigheids beheer** ingesteld op `1` , zodat er slechts één bericht tegelijk wordt verwerkt.

   ![' Voor elke ' lus: elk bericht een voor een per keer verwerken](./media/send-related-messages-sequential-convoy/for-each-additional-message.png)

1. Voor de Service Bus acties **voltooit u het bericht in een wachtrij** en **verlaat u het bericht in een wachtrij** en geeft u de naam op voor uw service bus wachtrij.

   ![Acties Service Bus: ' het bericht in een wachtrij volt ooien ' en ' het bericht in een wachtrij afbreken '](./media/send-related-messages-sequential-convoy/abandon-or-complete-message-in-queue.png)

   Wanneer **er meer berichten zijn voor de sessie in de wachtrij** , wordt de variabele door de werk stroom ingesteld `isDone` op `true` .

Vervolgens geeft u de benodigde informatie voor de acties in de **vernieuwings sessie vergren delen totdat** een lus is geannuleerd.

<a name="renew-session-while-messages-exist"></a>

### <a name="renew-session-lock-until-cancelled"></a>Sessie vergrendeling vernieuwen tot geannuleerd

Dit [ **tot** een lus](../logic-apps/logic-apps-control-flow-loops.md#until-loop) zorgt ervoor dat de sessie vergrendeling wordt gehouden door deze logische app terwijl er berichten in de wachtrij staan of totdat het één uur duurt door deze acties uit te voeren. Als u de tijds limiet van de lus wilt wijzigen, bewerkt u de waarde van de **time-** outeigenschap van de lus.

* Stel een vertraging in van 25 seconden of een hoeveelheid tijd die kleiner is dan de time-outwaarde voor de vergren deling van de wachtrij die wordt verwerkt. De kleinste vergrendelings duur is 30 seconden, zodat de standaard waarde voldoende is. U kunt echter het aantal keren dat de lus wordt uitgevoerd, optimaliseren door de juiste aanpassing aan te passen.

* Controleer of de `isDone` variabele is ingesteld op `true` .

  * Als `isDone` niet is ingesteld op `true` , verwerkt de werk stroom nog steeds berichten, waardoor de werk stroom de vergren deling van de sessie in de wachtrij vernieuwt en de lus-voor waarde opnieuw controleert.

    U moet de naam van de Service Bus wachtrij opgeven in de Service Bus actie, de [**vergren deling van de sessie in een wachtrij vernieuwen**](#renew-lock-on-session).

  * Als `isDone` is ingesteld op `true` , wordt de vergren deling van de sessie in de wachtrij niet door de werk stroom vernieuwd en wordt de lus afgesloten.

  ![Until: ' de sessie vergrendeling vernieuwen tot deze is geannuleerd '](./media/send-related-messages-sequential-convoy/renew-lock-until-session-cancelled.png)

<a name="renew-lock-on-session"></a>

#### <a name="renew-lock-on-the-session-in-a-queue"></a>Vergren deling van de sessie in een wachtrij vernieuwen

Met deze Service Bus actie wordt de vergren deling van de sessie in de wachtrij vernieuwd terwijl de werk stroom nog bezig is met het verwerken van berichten.

* Vernieuw in de Service Bus actie **vergrendeling van de sessie in een wachtrij** en geef de naam op voor uw service bus wachtrij.

  ![Service Bus actie: de vergren deling voor de sessie in de wachtrij vernieuwen](./media/send-related-messages-sequential-convoy/renew-lock-on-session-in-queue.png)

Vervolgens geeft u de benodigde informatie op voor de Service Bus actie, **sluit u een sessie in een wachtrij en slaagt** u.

<a name="close-session-succeed"></a>

### <a name="close-a-session-in-a-queue-and-succeed"></a>Een sessie in een wachtrij sluiten en slaagt

Met deze Service Bus actie wordt de sessie in de wachtrij gesloten nadat de werk stroom klaar is met het verwerken van alle beschik bare berichten in de wachtrij of de werk stroom het eerste bericht verlaat.

* In de actie Service Bus, **sluit u een sessie in een wachtrij en** geeft u de naam voor uw service bus wachtrij.

  ![Service Bus actie: een sessie in een wachtrij sluiten en slaagt](./media/send-related-messages-sequential-convoy/close-session-in-queue-succeed.png)

In de volgende secties worden de acties in de `Catch` sectie beschreven, die fouten en uitzonde ringen afhandelen die in uw werk stroom plaatsvinden.

<a name="close-session-fail"></a>

### <a name="close-a-session-in-a-queue-and-fail"></a>Een sessie in een wachtrij sluiten en mislukken

Deze Service Bus actie wordt altijd uitgevoerd als de eerste actie in het `Catch` bereik en sluit de sessie in de wachtrij.

* In de actie Service Bus, **sluit u een sessie in een wachtrij en voert u een fout uit en** geeft u de naam op voor uw service bus wachtrij.

  ![Service Bus actie: ' een sessie in een wachtrij sluiten en de fout niet uitvoeren '](./media/send-related-messages-sequential-convoy/close-session-in-queue-fail.png)

Vervolgens maakt de werk stroom een matrix met de invoer en uitvoer van alle acties in het `Try` bereik, zodat de logische app toegang kan krijgen tot informatie over de fout of de fout die is opgetreden.

<a name="find-failure-message"></a>

### <a name="find-failure-msg-from-try-block"></a>Fout bericht van try-blok zoeken

Met deze actie voor de [ **filter matrix**](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) wordt een matrix gemaakt met de invoer en uitvoer van alle acties binnen het `Try` bereik op basis van de opgegeven criteria met behulp van de [ `result()` functie](../logic-apps/workflow-definition-language-functions-reference.md#result). In dit geval retourneert deze actie de uitvoer van de acties die `Failed` de status hebben met behulp van de [ `equals()` functie](../logic-apps/workflow-definition-language-functions-reference.md#equals) en [ `item()` functie](../logic-apps/workflow-definition-language-functions-reference.md#item).

![Actie filter matrix-' fout bericht zoeken van ' try-blok '](./media/send-related-messages-sequential-convoy/find-failure-message.png)

Hier is de JSON-definitie voor deze actie:

```json
"Find_failure_msg_from_'Try'_block": {
   "inputs": {
      "from": "@Result('Try')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "Close_the_session_in_the_queue_and_fail": [
         "Succeeded"
      ]
   },
   "type": "Query"
},
```

Vervolgens maakt de werk stroom een matrix met een JSON-object dat de fout informatie bevat in de matrix die wordt geretourneerd door de `Find failure msg from 'Try' block` actie.

<a name="select-error-details"></a>

### <a name="select-error-details"></a>Fout Details selecteren

Met deze [actie **Select**](../logic-apps/logic-apps-perform-data-operations.md#select-action) wordt een matrix gemaakt die JSON-objecten bevat op basis van de invoer matrix die de uitvoer van de vorige actie heeft, `Find failure msg from 'Try' block` . Deze actie retourneert met name een matrix met alleen de opgegeven eigenschappen voor elk object in de matrix. In dit geval bevat de matrix de naam van de actie en de eigenschappen van het fout resultaat.

![Actie selecteren-"Selecteer fout Details"](./media/send-related-messages-sequential-convoy/select-error-details.png)

Hier is de JSON-definitie voor deze actie:

```json
"Select_error_details": {
   "inputs": {
      "from": "@body('Find_failure_msg_from_''Try''_block')[0]['outputs']",
      "select": {
         "action": "@item()['name']",
         "errorResult": "@item()"
      }
   },
   "runAfter": {
      "Find_failure_msg_from_'Try'_block": [
         "Succeeded"
      ]
   },
   "type": "Select"
},
```

Vervolgens stopt de werk stroom de uitvoering van de logische app en wordt de uitvoerings status weer gegeven, samen met meer informatie over de fout of de fout die is opgetreden.

<a name="terminate-logic-app"></a>

### <a name="terminate-logic-app-run"></a>Uitvoering van logische app beëindigen

Met deze [actie **beëindigen**](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) wordt de uitvoering van de logische app gestopt en wordt geretourneerd `Failed` als de status van de uitvoering van de logische app, samen met de sessie-id en het fout resultaat van de `Select error details` actie.

![Actie beëindigen om uitvoering van logische app te stoppen](./media/send-related-messages-sequential-convoy/terminate-logic-app-run.png)

Hier is de JSON-definitie voor deze actie:

```json
"Terminate": {
   "description": "This Failure Termination only runs if the Close Session upon Failure action runs - otherwise the LA will be terminated as Success",
   "inputs": {
      "runError": {
         "code": "",
         "message": "There was an error processing messages for Session ID @{triggerBody()?['SessionId']}. The following error(s) occurred: @{body('Select_error_details')['errorResult']}"
         },
         "runStatus": "Failed"
      },
      "runAfter": {
         "Select_error_details": [
            "Succeeded"
         ]
      },
      "type": "Terminate"
   }
},
```

## <a name="save-and-run-logic-app"></a>Logische app opslaan en uitvoeren

Nadat u de sjabloon hebt voltooid, kunt u de logische app nu opslaan. Selecteer **Opslaan** op de werkbalk van de ontwerper.

Als u uw logische app wilt testen, verzendt u berichten naar uw Service Bus wachtrij. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Triggers en acties van de service bus-connector](/connectors/servicebus/)
