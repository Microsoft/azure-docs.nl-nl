---
title: Status controleren, geschiedenis weer geven en waarschuwingen instellen
description: Problemen met logische apps oplossen door de status van de uitvoering te controleren, de trigger geschiedenis te bekijken en waarschuwingen in te scha kelen in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 3c3d1930234c178a56227830ef0702450ddf4a8c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580677"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>Uitvoeringsstatus bewaken, triggergeschiedenis controleren, en waarschuwingen instellen voor Azure Logic Apps

Nadat u [een logische app hebt gemaakt en uitgevoerd](../logic-apps/quickstart-create-first-logic-app-workflow.md), kunt u de uitvoerings status van de logische app, de [geschiedenis](#review-runs-history), de [trigger geschiedenis](#review-trigger-history)en de prestaties controleren. Als u meldingen over mislukte of andere mogelijke problemen wilt ontvangen, stelt u [waarschuwingen](#add-azure-alerts)in. U kunt bijvoorbeeld een waarschuwing maken die detecteert dat er gedurende een uur meer dan vijf uitvoeringen mislukken.

Voor real-time gebeurtenis bewaking en uitgebreidere fout opsporing kunt u diagnostische logboek registratie voor uw logische app instellen met behulp van [Azure monitor-logboeken](../azure-monitor/overview.md). Deze Azure-service helpt u bij het bewaken van uw Cloud-en on-premises omgevingen, zodat u hun Beschik baarheid en prestaties gemakkelijker kunt onderhouden. U kunt vervolgens gebeurtenissen zoeken en weer geven, zoals trigger gebeurtenissen, uitvoerings gebeurtenissen en actie gebeurtenissen. Door deze informatie op te slaan in [Azure monitor logboeken](../azure-monitor/logs/data-platform-logs.md), kunt u [logboek query's](../azure-monitor/logs/log-query-overview.md) maken waarmee u deze informatie vindt en analyseert. U kunt deze diagnostische gegevens ook gebruiken met andere Azure-Services, zoals Azure Storage en Azure Event Hubs. Zie [Logic apps bewaken met behulp van Azure monitor](../logic-apps/monitor-logic-apps-log-analytics.md)voor meer informatie.

> [!NOTE]
> Als uw Logic apps worden uitgevoerd in een [ISE (Integration service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) die is gemaakt om een [intern toegangs punt](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)te gebruiken, kunt u in *het virtuele netwerk* invoer en uitvoer bekijken en openen vanuit de geschiedenis van de uitvoering van de logische app. Zorg ervoor dat u verbinding hebt met het netwerk tussen de privé-eind punten en de computer van waaruit u de geschiedenis van uitvoeringen wilt openen. Uw client computer kan bijvoorbeeld bestaan in het virtuele netwerk van de ISE of binnen een virtueel netwerk dat is verbonden met het virtuele netwerk van de ISE, bijvoorbeeld via peering of een virtueel particulier netwerk. Zie [ISE endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)(Engelstalig) voor meer informatie. 

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>Uitvoeringsgeschiedenis controleren

Telkens wanneer de trigger wordt geactiveerd voor een item of gebeurtenis, maakt en voert de Logic Apps Engine een afzonderlijk werk stroom exemplaar voor elk item of elke gebeurtenis. Standaard wordt elk workflowexemplaar parallel uitgevoerd zodat er geen werk stroom moet worden gewacht voordat een uitvoering wordt gestart. U kunt zien wat er tijdens de uitvoering is gebeurd, inclusief de status van elke stap in de werk stroom plus de invoer en uitvoer voor elke stap.

1. Zoek en open uw logische app in het [Azure Portal](https://portal.azure.com)in de ontwerp functie voor logische apps.

   Als u uw logische app wilt vinden, voert u in het hoofd venster van Azure Search in `logic apps` en selecteert u **Logic apps**.

   ![Zoek en selecteer de service ' Logic Apps '](./media/monitor-logic-apps/find-your-logic-app.png)

   In de Azure Portal worden alle Logic apps weer gegeven die zijn gekoppeld aan uw Azure-abonnementen. U kunt deze lijst filteren op basis van naam, abonnement, resource groep, locatie, enzovoort.

   ![Logische apps weer geven die zijn gekoppeld aan abonnementen](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Selecteer uw logische app en selecteer vervolgens **overzicht**.

   In het deel venster Overzicht, onder **uitvoerings geschiedenis**, worden alle eerdere, huidige en eventuele wachtende uitvoeringen voor uw logische app weer gegeven. Als in de lijst veel uitvoeringen worden weer gegeven en u de gewenste vermelding niet kunt vinden, kunt u de lijst filteren.

   > [!TIP]
   > Als de uitvoerings status niet wordt weer gegeven, kunt u de pagina overzicht vernieuwen door **vernieuwen** te selecteren. Er vindt geen uitvoering plaats voor een trigger die wordt overgeslagen vanwege unmet criteria of het vinden van geen gegevens.

   ![Overzicht, geschiedenis van uitvoeringen en andere informatie over logische apps](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   Hier volgen de mogelijke uitvoerings statussen:

   | Uitvoerings status | Description |
   |------------|-------------|
   | **Aborted** | De uitvoering is gestopt of niet voltooid vanwege externe problemen, bijvoorbeeld een systeem storing of een vervallen Azure-abonnement. |
   | **Gevraagd** | De uitvoering is geactiveerd en gestart, maar er is een annulerings aanvraag ontvangen. |
   | **Mislukt** | Ten minste één bewerking in de uitvoering is mislukt. Er zijn geen verdere acties in de werk stroom ingesteld voor het afhandelen van de fout. |
   | **Wordt uitgevoerd** | De uitvoering is geactiveerd en wordt uitgevoerd, maar deze status kan ook worden weer gegeven voor een uitvoering die wordt beperkt als gevolg van [actie limieten](logic-apps-limits-and-config.md) of het [huidige prijs plan](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Tip**: als u [logboek registratie van diagnostische gegevens](monitor-logic-apps-log-analytics.md)instelt, kunt u informatie ophalen over eventuele vertragings gebeurtenissen die plaatsvinden. |
   | **Geslaagd** | De uitvoering is voltooid. Als een actie is mislukt, wordt die fout door een volgende actie in de werk stroom verwerkt. |
   | **Time-out opgetreden** | Er is een time-out opgetreden tijdens het uitvoeren omdat de huidige duur de limiet voor de uitvoerings duur heeft overschreden, die wordt bepaald door de [ **Bewaar periode voor het bewaren van uitvoerings geschiedenis in dagen**](logic-apps-limits-and-config.md#run-duration-retention-limits). De duur van een uitvoering wordt berekend met behulp van de begin tijd van de uitvoering en de duur limiet voor uitvoering op die begin tijd. <p><p>**Opmerking**: als de duur van de uitvoering ook de huidige *Bewaar limiet voor de uitvoerings geschiedenis* overschrijdt, die ook wordt bepaald door de Bewaar periode voor het bewaren van de [ **uitvoerings geschiedenis in dagen**](logic-apps-limits-and-config.md#run-duration-retention-limits), wordt de uitvoering uit de geschiedenis van uitvoeringen door een dagelijkse opschoon taak gewist. Of de uitvoerings tijd wordt uitgecheckt of voltooid, de Bewaar periode wordt altijd berekend met behulp van de begin tijd en de *huidige* Bewaar limiet van de uitvoering. Als u de limiet voor de duur van een in-Flight-uitvoering verkort, wordt er dus een time-out uitgevoerd. De uitvoering blijft echter behouden of wordt gewist uit de geschiedenis van de uitvoering, op basis van het feit of de duur van de uitvoering de Bewaar limiet heeft overschreden. |
   | **Wachten** | De uitvoering is niet gestart of wordt onderbroken, bijvoorbeeld door een eerder werk stroom exemplaar dat nog steeds actief is. |
   |||

1. Als u de stappen en andere informatie voor een specifieke uitvoering wilt controleren, selecteert u in de **geschiedenis** van uitvoeringen de optie uitvoeren.

   ![Selecteer een specifieke uitvoering om te controleren](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   In het deel venster voor het uitvoeren van de **logische app** ziet u elke stap in de geselecteerde uitvoering, de uitvoerings status van elke stap en de tijd die nodig is voor het uitvoeren van elke stap, bijvoorbeeld:

   ![Elke actie in de specifieke uitvoering](./media/monitor-logic-apps/logic-app-run-pane.png)

   Als u deze informatie in het formulier lijst wilt weer geven, selecteert u op de werk balk van de **logische app uitvoeren** de optie **Details uitvoeren**.

   ![Selecteer op de werk balk de optie Details uitvoeren](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   In de weer gave uitvoerings Details worden elke stap, hun status en andere informatie weer gegeven.

   ![Bekijk de details van elke stap in de uitvoering](./media/monitor-logic-apps/review-logic-app-run-details.png)

   U kunt bijvoorbeeld de eigenschap **correlatie-id** van het run ophalen, die u mogelijk nodig hebt wanneer u de [REST API gebruikt voor Logic apps](/rest/api/logic).

1. Selecteer een van de volgende opties voor meer informatie over een specifieke stap:

   * Selecteer in het deel venster voor het uitvoeren van de **logische app** de stap zodat de shape wordt uitgevouwen. U kunt nu gegevens weer geven, zoals invoer, uitvoer en eventuele fouten die zijn opgetreden in die stap, bijvoorbeeld:

     ![Bekijk de mislukte stap in het deel venster Logic app uitvoeren](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * Selecteer de gewenste stap in het detail venster van de **logische app uitvoeren** .

     ![Bekijk in het detail venster uitvoeren de mislukte stap](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     U kunt nu gegevens weer geven, zoals invoer en uitvoer voor die stap, bijvoorbeeld:

   > [!NOTE]
   > Alle runtime Details en-gebeurtenissen zijn versleuteld in de Logic Apps-service. Ze worden alleen ontsleuteld wanneer een gebruiker vraagt om die gegevens weer te geven. U kunt [invoer en uitvoer verbergen in de uitvoerings geschiedenis](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) of de gebruikers toegang tot deze gegevens beheren door gebruik te maken van [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/overview.md).

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>Triggergeschiedenis controleren

De uitvoering van elke logische app begint met een trigger. De trigger geschiedenis bevat een lijst met alle trigger pogingen die uw logische app heeft gemaakt en informatie over de invoer en uitvoer voor elke trigger poging.

1. Zoek en open uw logische app in het [Azure Portal](https://portal.azure.com)in de ontwerp functie voor logische apps.

   Als u uw logische app wilt vinden, voert u in het hoofd venster van Azure Search in `logic apps` en selecteert u **Logic apps**.

   ![Zoek en selecteer de service ' Logic Apps '](./media/monitor-logic-apps/find-your-logic-app.png)

   In de Azure Portal worden alle Logic apps weer gegeven die zijn gekoppeld aan uw Azure-abonnementen. U kunt deze lijst filteren op basis van naam, abonnement, resource groep, locatie, enzovoort.

   ![Logische apps weer geven die zijn gekoppeld aan abonnementen](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Selecteer uw logische app en selecteer vervolgens **overzicht**.

1. Selecteer **overzicht** in het menu van de logische app. Selecteer in de sectie **samen vatting** onder **evaluatie** de optie **trigger geschiedenis weer geven**.

   ![De trigger geschiedenis voor uw logische app weer geven](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   In het deel venster trigger geschiedenis ziet u alle trigger pogingen die uw logische app heeft gedaan. Telkens wanneer de trigger wordt geactiveerd voor een item of gebeurtenis, maakt de Logic Apps-Engine een afzonderlijk exemplaar van een logische app waarmee de werk stroom wordt uitgevoerd. Elk exemplaar wordt standaard parallel uitgevoerd, zodat er geen werk stroom moet worden gewacht voordat een uitvoering wordt gestart. Dus als uw logische app op meerdere items tegelijk wordt geactiveerd, wordt er een trigger vermelding met dezelfde datum en tijd weer gegeven voor elk item.

   ![Meerdere trigger pogingen voor verschillende items](./media/monitor-logic-apps/logic-app-trigger-history.png)

   Dit zijn de mogelijke statussen van trigger pogingen:

   | Triggerstatus | Description |
   |----------------|-------------|
   | **Mislukt** | Er is een fout opgetreden. Als u gegenereerde fout berichten voor een mislukte trigger wilt controleren, selecteert u de trigger poging en kiest u **uitvoer**. U kunt bijvoorbeeld invoer zoeken die niet geldig is. |
   | **Overgeslagen** | De trigger heeft het eind punt gecontroleerd, maar er zijn geen gegevens gevonden die voldoen aan de opgegeven criteria. |
   | **Geslaagd** | De trigger heeft het eind punt gecontroleerd en beschik bare gegevens gevonden. Normaal gesp roken naast deze status wordt ook een **geactiveerd** status weer gegeven. Als dat niet het geval is, kan de definitie van de trigger een voor waarde of een opdracht hebben die niet is `SplitOn` voldaan. <p><p>Deze status kan van toepassing zijn op een hand matige trigger, een herhalings trigger of een polling-trigger. Een trigger kan worden uitgevoerd, maar de uitvoeringsrun zelf kan echter nog steeds mislukken wanneer de acties onverwerkte fouten genereren. |
   |||

   > [!TIP]
   > U kunt de trigger opnieuw controleren zonder te wachten op het volgende terugkeer patroon. Selecteer op de werk balk overzicht de optie **trigger uitvoeren** en selecteer de trigger die een controle afdwingt. U kunt ook **uitvoeren** op de werk balk van Logic apps Designer selecteren.

1. Als u informatie over een specifieke trigger poging wilt weer geven, selecteert u in het deel venster trigger de trigger gebeurtenis. Als in de lijst veel trigger pogingen worden weer gegeven en u de gewenste vermelding niet kunt vinden, kunt u de lijst filteren. Als u de verwachte gegevens niet kunt vinden, kunt u op de werk balk op **vernieuwen** klikken.

   ![Specifieke trigger poging weer geven](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   U kunt nu informatie over de geselecteerde trigger gebeurtenis bekijken, bijvoorbeeld:

   ![Specifieke trigger gegevens weer geven](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>Bewakingswaarschuwingen instellen

Als u waarschuwingen wilt ontvangen op basis van specifieke metrische gegevens of drempel waarden voor de logische app hebt overschreden, stelt u [waarschuwingen in azure monitor in](../azure-monitor/alerts/alerts-overview.md). Meer informatie over [metrische gegevens in azure](../azure-monitor/data-platform.md). Voer de volgende stappen uit als u waarschuwingen wilt instellen zonder [Azure monitor](../azure-monitor/logs/log-query-overview.md)te gebruiken.

1. Selecteer in het menu van de logische app, onder **bewaking**, de **optie waarschuwing**  >  **nieuwe waarschuwings regel**.

   ![Een waarschuwing voor uw logische app toevoegen](./media/monitor-logic-apps/add-new-alert-rule.png)

1. Selecteer in het deel venster **regel maken** onder **resource** de logische app, als deze nog niet is geselecteerd. Onder **voor waarde**, selecteert u **toevoegen** zodat u de voor waarde kunt definiëren waarmee de waarschuwing wordt geactiveerd.

   ![Een voor waarde voor de regel toevoegen](./media/monitor-logic-apps/add-condition-for-rule.png)

1. Zoek en selecteer in het deel venster **signaal logica configureren** het signaal waarvoor u een waarschuwing wilt ontvangen. U kunt het zoekvak gebruiken, of de signalen alfabetisch sorteren, de kolomkop **signaal naam** selecteren.

   Als u bijvoorbeeld een waarschuwing wilt verzenden wanneer een trigger mislukt, voert u de volgende stappen uit:

   1. Zoek en selecteer in de kolom **signaal naam** het signaal **Triggers mislukt** .

      ![Signaal selecteren voor het maken van een waarschuwing](./media/monitor-logic-apps/find-and-select-signal.png)

   1. Stel in het deel venster informatie dat wordt geopend voor het geselecteerde signaal, onder **waarschuwings logica**, uw voor waarde in, bijvoorbeeld:

   1. Voor **operator** selecteert u **groter dan of gelijk aan**.

   1. Selecteer **aantal** bij **aggregatie type**.

   1. Voer in bij **drempel waarde** `1` .

   1. Controleer onder **voor waarde preview** of uw voor waarde juist wordt weer gegeven.

   1. Stel onder **geëvalueerd op basis van** het interval en de frequentie in voor het uitvoeren van de waarschuwings regel. Selecteer voor de **granulariteit van aggregatie (punt)** de periode voor het groeperen van de gegevens. Voor de **frequentie van de evaluatie** selecteert u hoe vaak u de voor waarde wilt controleren.

   1. Wanneer u klaar bent, selecteert u **gereed**.

   Dit is de voor waarde voltooid:

   ![Voor waarde voor waarschuwing instellen](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   De pagina **regel maken** bevat nu de voor waarde die u hebt gemaakt en de kosten voor het uitvoeren van die waarschuwing.

   ![Nieuwe waarschuwing op de pagina regel maken](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. Geef een naam, een optionele beschrijving en een Ernst niveau voor uw waarschuwing op. Laat de instelling **regel inschakelen bij maken** ingeschakeld of schakel deze optie uit totdat u klaar bent om de regel in te scha kelen.

1. Wanneer u klaar bent, selecteert u **waarschuwings regel maken**.

> [!TIP]
> Als u een logische app wilt uitvoeren vanuit een waarschuwing, kunt u de [trigger voor aanvragen](../connectors/connectors-native-reqres.md) toevoegen aan uw werk stroom, waarmee u taken zoals deze voor beelden kunt uitvoeren:
> 
> * [Post naar toegestane vertraging](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Een sms verzenden](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Een bericht aan een wachtrij toevoegen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>Volgende stappen

* [Logische apps bewaken met behulp van Azure Monitor](../logic-apps/monitor-logic-apps-log-analytics.md)
