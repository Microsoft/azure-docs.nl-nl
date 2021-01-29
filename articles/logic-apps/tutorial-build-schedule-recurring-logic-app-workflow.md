---
title: Geautomatiseerde op planning gebaseerde werkstromen bouwen met Azure
description: 'Zelfstudie: een terugkerende Automation-werkstroom op basis van een planning maken die in Cloud Services kan worden geïntegreerd met behulp van Azure Logic Apps.'
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/30/2020
ms.openlocfilehash: 95275e68d0c7674caf4dd2b20f5586db5193fd03
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054057"
---
# <a name="tutorial-create-schedule-based-and-recurring-automation-workflows-with-azure-logic-apps"></a>Zelfstudie: Op schema gebaseerde en terugkerende automatiseringswerkstromen maken met Azure Logic Apps

Deze zelfstudie laat zien hoe u een [logic app](../logic-apps/logic-apps-overview.md) kunt bouwen en een terugkerende workflow kunt automatiseren die volgens een planning draait. Dit voorbeeld van een logische app wordt elke doordeweekse ochtend uitgevoerd en controleert de reistijd, inclusief het verkeer, tussen twee plaatsen. Als de tijd een bepaalde limiet overschrijdt, verzendt de logische app een e-mail met de reistijd en de extra tijd die u nodig hebt om uw bestemming te bereiken. De workflow bevat verschillende stappen, die beginnen met een trigger op basis van een schema, gevolgd door een Bing Maps-actie, een actie voor gegevensbewerkingen, een controlestroom-actie en een e-mailmeldingsactie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een lege, logische app maken.
> * Voeg de trigger Terugkeerpatroon toe die de planning voor uw logische app specificeert.
> * Een Bing Maps-actie toevoegen die de reistijd voor een route ophaalt.
> * Een actie toevoegen die een variabele maakt, de reistijd converteert van seconden naar minuten en dat resultaat opslaat in de variabele.
> * Een voorwaarde toevoegen die de reistijd vergelijkt met een opgegeven limiet.
> * Een actie toevoegen die u een e-mailbericht stuurt als de reistijd de limiet overschrijdt.

Wanneer u bent klaar, ziet uw logische app eruit als deze werkstroom op hoog niveau:

![Schermopname met het overzicht op hoog niveau van een voorbeeld van een werkstroom voor logische apps.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een e-mailaccount van een e-mailprovider die door Logic Apps wordt ondersteund, bijvoorbeeld Office 365 Outlook, Outlook.com of Gmail. Voor andere providers [kunt u hier de lijst met connectors bekijken](/connectors/). In deze quickstart wordt gebruikgemaakt van Microsoft 365 Outlook met een werk- of schoolaccount. Als u een ander e-mailaccount gebruikt, blijven de algemene stappen gelijk, maar uw gebruikersinterface kan er iets anders uitzien.

  > [!IMPORTANT]
  > Als u de Gmail-connector wilt gebruiken, kunnen alleen bedrijfsaccounts van G Suite deze connector zonder beperking in logische apps gebruiken. Als u een Gmail-consumentenaccount hebt, kunt u deze connector alleen gebruiken met specifieke door Google goedgekeurde services, of u kunt [een Google-client-app maken voor verificatie bij uw Gmail-connector](/connectors/gmail/#authentication-and-bring-your-own-application). Zie [Beleid voor gegevensbeveiliging en privacybeleid voor Google-connectors in Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md) voor meer informatie.

* Als u de reistijd voor een route wilt ophalen, hebt u een toegangssleutel nodig voor de API van Bing Kaarten. Volg de stappen in [Een Bing Kaarten-sleutel krijgen](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key) om deze sleutel te krijgen.

* Als uw logische app moet communiceren via een firewall die het verkeer beperkt tot specifieke IP-adressen, moet die firewall toegang toestaan voor *zowel* de [inkomende](logic-apps-limits-and-config.md#inbound) als [uitgaande](logic-apps-limits-and-config.md#outbound) IP-adressen die worden gebruikt door de Logic apps service of runtime in de Azure-regio waar uw logische app bestaat. Als uw logische app ook [beheerde connectors](../connectors/apis-list.md#managed-api-connectors)gebruikt, zoals de Office 365 Outlook-Connector of de SQL-connector, of [aangepaste connectors](/connectors/custom-connectors/)gebruikt, moet de firewall ook toegang toestaan voor *alle* [uitgaande IP-adressen van beheerde connectors](logic-apps-limits-and-config.md#outbound) in de Azure-regio van de logische app.

## <a name="create-your-logic-app"></a>Uw logische app maken

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com). Selecteer op de startpagina van het Azure-portal **Een resource maken**.

1. Selecteer op het Azure Marketplace-menu **Integratie** > **Logische app**.

   ![Schermopname van het Azure Marketplace-menu met "Integratie" en "Logische app" geselecteerd.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. Geef in het deelvenster **Logische app** de informatie op die hier wordt beschreven over de Logische app die u wilt maken.

   ![Schermopname van het deelvenster voor het maken van een logische app en de informatie die u voor de nieuwe logische app kunt opgeven.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Abonnement** | <*Azure-subscription-name*> | Uw Azure-abonnementnaam. In dit voorbeeld wordt `Pay-As-You-Go` gebruikt. |
   | **Resourcegroep** | LA-TravelTime-RG | De naam van de [Azure-resourcegroep](../azure-resource-manager/management/overview.md) die wordt gebruikt om verwante resources te organiseren. In het volgende voorbeeld wordt een resourcegroep met de naam `LA-TravelTime-RG` gemaakt. |
   | **Naam** | LA-TravelTime | De naam van uw logische app mag alleen letters, cijfers, afbreekstreepjes (`-`), onderstrepingstekens (`_`), haakjes (`(`, `)`) en punten (`.`) bevatten. In dit voorbeeld wordt `LA-TravelTime` gebruikt. |
   | **Locatie** | VS - west | De regio waarin informatie over uw logische app moet worden opgeslagen. In dit voorbeeld wordt `West US` gebruikt. |
   | **Log Analytics** | Uit | Behoud de instelling **Uit** voor het vastleggen van diagnostische gegevens. |
   ||||

1. Selecteer als u klaar bent de optie **Beoordelen en maken**. Nadat Azure de informatie over uw logische app heeft gevalideerd, selecteert u **Maken**.

1. Nadat uw app in Azure is geïmplementeerd, selecteert u **Naar de resource gaan**.

   Azure opent het deelvenster voor het selecteren van logische apps-sjablonen, waarin een introductievideo, veelgebruikte triggers en sjabloonpatronen voor logische apps wordt weergegeven.

1. Scrol omlaag vanaf de video en de algemene triggers naar **Sjablonen** en selecteer **Lege logische app**.

   ![Schermopname van het deelvenster sjabloonselectie voor logische apps waarin 'lege logische app' is geselecteerd.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Voeg vervolgens de [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) Terugkeerpatroon toe, die de workflow op basis van een opgegeven planning activeert. Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer nieuwe gegevens voldoen aan een bepaalde voorwaarde. Bekijk [Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md) voor meer informatie.

## <a name="add-the-recurrence-trigger"></a>De trigger Terugkeerpatroon toevoegen

1. In het zoekvak van Logic Apps Designer voert u `recurrence` in en selecteert u de trigger met de naam **Terugkeerpatroon**.

   ![Schermopname van het zoekvak van de Logic Apps Designer die de zoekterm 'terugkeer' bevat en in de lijst 'triggers' wordt de trigger 'terugkeerpatroon' weergegeven.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Selecteer op de vorm **Terugkeerpatroon** de knop met het **beletselteken** ( **...** ) en selecteer **Naam wijzigen**. Wijzig de naam van de trigger met deze beschrijving: `Check travel time every weekday morning`

   ![Schermopname waarin de knop met weglatingstekens is geselecteerd, de lijst 'instellingen' is geopend en de opdracht 'naam wijzigen' is geselecteerd.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Wijzig in de trigger deze eigenschappen zoals hier beschreven en weergegeven.

   ![Screenshot die de wijzigingen in het interval en de frequentie van de trigger laat zien.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Interval** | Ja | 1 | Het aantal intervallen dat tussen controles moet worden gewacht |
   | **Frequentie** | Ja | Wekelijks | Tijdseenheid die voor het terugkeerpatroon wordt gebruikt |
   |||||

1. Open onder **Interval** en **Frequentie** de lijst **Nieuwe parameter toevoegen** en selecteer deze eigenschappen om aan de trigger toe te voegen.

   * **Deze dagen**
   * **Deze uren**
   * **Deze minuten**

   ![Schermafbeelding die de geopende lijst 'Nieuwe parameter toevoegen' en deze geselecteerde eigenschappen toont: 'Op deze dagen', 'op deze uren' en 'in deze minuten'.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Stel nu de waarden voor de extra eigenschappen in zoals hier wordt weergegeven en beschreven.

   ![Schermopname met de extra eigenschappen die zijn ingesteld op de waarden zoals beschreven in de volgende tabel.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Deze dagen** | Maandag, dinsdag, woensdag, donderdag, vrijdag | Deze instelling is alleen beschikbaar wanneer u de **Frequentie** instelt op **Week**. |
   | **Deze uren** | 7, 8, 9 | Deze instelling is alleen beschikbaar wanneer u de **Frequentie** instelt op **Week** of **Dag**. Selecteer voor dit terugkeerpatroon de uren van de dag. Dit voorbeeld wordt uitgevoerd om de `7`, `8` en `9`-uurmarkeringen. |
   | **Deze minuten** | 0, 15, 30, 45 | Deze instelling is alleen beschikbaar wanneer u de **Frequentie** instelt op **Week** of **Dag**. Selecteer voor dit terugkeerpatroon de minuten van de dag. Dit voorbeeld wordt iedere 15 minuten na het hele uur uitgevoerd. |
   ||||

   Deze trigger wordt iedere doordeweekse dag om de 15 minuten uitgevoerd tussen 07:00 en 09:45 uur. Het vak **Preview** toont de terugkeerplanning. Zie [Taken en werkstromen plannen](../connectors/connectors-native-recurrence.md) en [Werkstroomacties en -triggers](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) voor meer informatie.

1. Om de details van de trigger voorlopig te verbergen, vouwt u de vorm samen door in de titelbalk van de vorm te klikken.

   ![Schermopname die de samengevouwen triggervorm laat zien.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

Uw logische app is nu live in de Azure Portal, maar doet niets anders dan triggeren op basis van het opgegeven schema. Daarom gaat u nu een actie toevoegen die reageert wanneer de trigger wordt geactiveerd.

## <a name="get-the-travel-time-for-a-route"></a>De reistijd voor een route ophalen

Nu u een trigger hebt, voegt u een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die de reistijd tussen twee locaties ophaalt. Logic Apps biedt een connector voor de API van Bing Kaarten. Hiermee kunt u deze informatie eenvoudig ophalen. Voordat u deze taak start, moet u ervoor zorgen dat u een API-sleutel van Bing Kaarten hebt, zoals beschreven in de vereisten in deze zelfstudie.

1. Selecteer in de ontwerpfunctie van de logische app onder uw terugkerende trigger de optie **Nieuwe stap**.

1. Selecteer onder **Kies een actie** de optie **Standard**. Voer in het zoekvenster `bing maps` in en selecteer de actie met de naam **Route ophalen**.

   ![Schermafbeelding met de actie 'Kies een bewerking' die is gefilterd op 'Bing Maps' acties en 'route ophalen' geselecteerd.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Als u geen verbinding met Bing Kaarten hebt, wordt u gevraagd om een verbinding te maken. Geef de verbindingsdetails op zoals weergegeven en beschreven, en selecteer vervolgens **Maken**.

   ![Schermopname met het verbindingsvenster Bing Maps met de opgegeven verbindingsnaam en Bing Maps-API-sleutel.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Verbindingsnaam** | Ja | BingMapsConnection | Geef een naam op voor uw verbinding. In dit voorbeeld wordt `BingMapsConnection` gebruikt. |
   | **API-sleutel** | Ja | <*Bing-Maps-API-sleutel*> | Voer de sleutel voor Bing Maps API in die u eerder hebt ontvangen. Leer [hoe u een sleutel kunt verkrijgen](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key) als u geen sleutel voor Bing Kaarten hebt. |
   |||||

1. Wijzig de naam van de actie met deze beschrijving: `Get route and travel time with traffic`.

1. Open in de actie de optie **Nieuwe parameterlijst toevoegen** en selecteer deze eigenschappen.

   * **Optimaliseren**
   * **Afstandseenheid**
   * **Vervoermiddel**

   ![Schermafbeelding met de actie 'route ophalen...' met de eigenschappen 'optimaliseren', 'afstandseenheid' en 'reismodus' geselecteerd.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Voer nu de waarden voor de eigenschappen in zoals hier wordt weergegeven en beschreven.

   ![Schermopname met aanvullende eigenschapswaarden voor de actie 'route ophalen'.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Routepunt 1** | Ja | <*start-location*> | Het beginpunt van uw route. In dit voorbeeld wordt een beginadres als voorbeeld opgegeven. |
   | **Routepunt 2** | Ja | <*end-location*> | De bestemming van uw route. In dit voorbeeld wordt een eindadres als voorbeeld opgegeven. |
   | **Optimaliseren** | Nee | timeWithTraffic | Een parameter voor het optimaliseren van uw route, zoals afstand, reistijd in actuele verkeerssituatie, enzovoort. Kies de parameterwaarde, **timeWithTraffic**. |
   | **Afstandseenheid** | Nee | <*your-preference*> | De afstandseenheid die voor de route wordt gebruikt. Dit voorbeeld maakt gebruik van **Mijl** als eenheid. |
   | **Vervoermiddel** | Nee | Auto | Het vervoermiddel voor uw route. Selecteer modus **Rijden**. |
   |||||

   Zie [Een route berekenen](/bingmaps/rest-services/routes/calculate-a-route) voor meer informatie over deze parameters en waarden.

1. Selecteer **Opslaan** op de werkbalk van de ontwerper.

Maak vervolgens een variabele, zodat u de actuele reistijd kunt converteren en opslaan in minuten in plaats van seconden. Zo kunt u voorkomen dat de conversie wordt herhaald en kunt u de waarden eenvoudiger gebruiken in latere stappen. 

## <a name="create-a-variable-to-store-travel-time"></a>Een variabele maken voor het opslaan van reistijden

Soms wilt u bewerkingen uitvoeren op gegevens in uw werkstroom en de resultaten gebruiken bij latere acties. U kunt variabelen maken die deze resultaten opslaan nadat ze die hebben verwerkt. Zo kunt u de opgeslagen resultaten later gemakkelijk hergebruiken of raadplegen. U kunt alleen variabelen maken in het hoogste niveau van uw logische app.

De hiervoor genoemde actie **Route ophalen** retourneert standaard de actuele reistijd met verkeer binnen enkele seconden via de eigenschap **Reistijd in huidige verkeerssituatie**. Door deze waarde in plaats daarvan te converteren en op te slaan, kunt u de waarde later eenvoudiger hergebruiken zonder dat u opnieuw moet omrekenen.

1. Selecteer onder de actie **Route ophalen** de optie **Nieuwe stap**.

1. Selecteer onder **Kies een actie** de optie **Ingebouwd**. Voer in het zoekvenster `variables` in en selecteer de actie met de naam **Variabele starten**.

   ![Schermopname die de geselecteerde actie 'Variabele starten' laat zien.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Wijzig de naam van deze actie met deze beschrijving: `Create variable to store travel time`

1. Geef deze informatie voor de variabele op zoals weergegeven in deze tabel en in de stappen onder de tabel:

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Naam** | Ja | travelTime | De naam van uw variabele. In dit voorbeeld wordt `travelTime` gebruikt. |
   | **Type** | Ja | Geheel getal | Het gegevenstype van uw variabele |
   | **Waarde** | Nee | Een expressie die de actuele reistijd van seconden naar minuten converteert (zie de stappen onder deze tabel). | De beginwaarde van uw variabele |
   |||||

   1. Als u de expressie voor de eigenschap **Waarde** wilt maken, klikt u in het vak, zodat de lijst met dynamische inhoud wordt weergegeven. Als u de lijst niet ziet, verbreedt u uw browser totdat de lijst met dynamische inhoud wel wordt weergegeven. Selecteer in de lijst dynamische inhoud **Expressie**, waarin de expressie-editor wordt weergegeven.

      ![Schermopname met de actie 'Variabele starten' met de cursor in de eigenschap 'waarde', waarmee de lijst met dynamische inhoud wordt geopend.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      In de lijst met dynamische inhoud ziet u de uitvoer van de vorige acties die u kunt selecteren als invoer voor de volgende acties in uw werkstroom. De lijst met dynamische inhoud bevat een expressie-editor die u kunt gebruiken om functies te selecteren voor het uitvoeren van bewerkingen in uw expressie. Deze expressie-editor wordt alleen weergegeven in de lijst met dynamische inhoud.

   1. Voer deze expressie in in de expressie-editor: `div(,60)`

      ![Scherm opname van de expressie-editor waarin de expressie 'div(,60)' is opgegeven.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Plaats de cursor in de expressie tussen het haakje openen ( **(** ) en de komma ( **,** ) en selecteer **Dynamische inhoud**.

      ![Scherm afbeelding die laat zien waar de cursor in de expressie 'div(,60)' met 'dynamische inhoud' is geselecteerd.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. Selecteer, onder selecteer de eigenschapswaarde, **Reistijd in huidige verkeerssituatie** in de lijst met dynamische inhoud.

      ![Schermafbeelding die de geselecteerde waarde 'reistijd in huidige verkeerssituatie' van de eigenschap toont.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Nadat de eigenschapswaarde in de expressie is omgezet, selecteert u **OK**.

      ![Schermopname die de knop 'OK' geselecteerd toont.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      De eigenschap **Waarde** wordt nu zoals hier weergegeven:

      ![Schermopname van de eigenschap 'Waarde' met de omgezette expressie.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Sla uw logische app op.

Vervolgens voegt u een voorwaarde toe waarmee wordt gecontroleerd of de actuele reistijd een bepaalde limiet overschrijdt.

## <a name="compare-the-travel-time-with-limit"></a>Vergelijk de reistijd vergelijken met de limiet

1. Selecteer **Een variabele maken om reistijd op te slaan** onder de actie **Nieuwe stap**.

1. Selecteer onder **Kies een actie** de optie **Ingebouwd**. Typ `condition` in het zoekvak. en selecteer in de lijst met acties de actie **Voorwaarde**.

   ![Schermopname met de geselecteerde actie 'voorwaarde'](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Wijzig de naam van de voorwaarde met deze beschrijving: `If travel time exceeds limit`

1. Maak een voorwaarde waarmee wordt gecontroleerd of de eigenschapswaarde **travelTime** groter is dan de door u opgegeven limiet zoals hier wordt beschreven en weergegeven:

   1. Klik in de voorwaarde in het vak **Kies een waarde** aan de linkerkant van de voorwaarde.

   1. Selecteer in de lijst met dynamische inhoud onder **Variabelen** de eigenschap **travelTime**.

      ![Schermopname van het vak 'Kies een waarde' op de linkerkant van de voorwaarde met de lijst met dynamische inhoud open en de eigenschap 'travelTime' geselecteerd.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. Selecteer in het middelste vergelijkingsvak de operator **is groter dan**.

   1. Voer in het vak **Kies een waarde** aan de rechterkant van de voorwaarde deze limiet in: `15`

      Wanneer u klaar bent, ziet de voorwaarde eruit zoals in dit voorbeeld:

      ![Schermopname van de voltooide voorwaarde voor het vergelijken van de reistijd en de opgegeven limiet.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Sla uw logische app op.

Voeg vervolgens de actie toe die moet worden uitgevoerd als de reistijd uw limiet overschrijdt.

## <a name="send-email-when-limit-exceeded"></a>E-mail verzenden als de limiet wordt overschreden

Voeg nu de actie toe die ervoor zorgt dat u een e-mailbericht ontvangt als de reistijd uw limiet overschrijdt. Dit e-mailbericht bevat de actuele reistijd en de extra tijd die nodig is om de opgegeven route af te leggen.

1. Selecteer in de vertakking **Indien waar** van de voorwaarde de optie **Een actie toevoegen**.

1. Selecteer onder **Kies een actie** de optie **Standard**. Typ `send email` in het zoekvak. De lijst levert veel resultaten op, dus selecteer eerst de gewenste e-mailconnector.

   Als u bijvoorbeeld een Outlook-e-mailaccount heeft, selecteert u de connector voor uw accounttype:

   * Selecteer **Office 365 Outlook** voor werk- of schoolaccounts van Azure.
   * Selecteer **Outlook.com** voor persoonlijke Microsoft-accounts.

   In dit voorbeeld wordt verder Office 365 Outlook geselecteerd.

   ![Schermopname met de categorie 'Kies een bewerkingslijst' met 'standard' en 'Office 365 Outlook-Connector' geselecteerd.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

1. Wanneer de acties van de connector worden weergegeven, selecteert u de actie die e-mails verzendt, bijvoorbeeld:

   ![Schermopname met de geselecteerde actie 'Een e-mail sturen'.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Als u nog geen verbinding hebt, meldt u zich aan en verifieert u de toegang tot uw e-mailaccount wanneer u hierom wordt gevraagd.

   Azure Logic Apps maakt een verbinding met uw e-mailaccount.

1. Wijzig de naam van de actie met deze beschrijving: `Send email with travel time`

1. Voer het e-mailadres van de ontvanger in het vak **Aan** in. Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken.

1. Geef het onderwerp van het e-mailbericht op in het vak **Onderwerp** en sluit de variabele **travelTime** door de volgende stappen te volgen:

   1. Voer de tekst in `Current travel time (minutes):` met een spatie aan het eind. Bewaar de cursor in het vak **onderwerp** zodat de lijst met dynamische inhoud geopend blijft.

   1. Selecteer in de lijst met dynamische inhoud in de **variabelen** de optie **meer weergeven** zodat de variabele met de naam **travelTime** wordt weergegeven.

      ![Schermopname waarin de lijst met dynamische inhoud wordt weergegeven met de sectie 'variabelen' en 'meer weergeven' geselecteerd.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

      > [!NOTE]
      > In de lijst met dynamische inhoud wordt niet automatisch de **travelTime** -variabele weergegeven omdat de eigenschap **subject** een tekenreekswaarde verwacht, terwijl **travelTime** een gehele waarde is.

      ![Schermopname waarin de lijst met dynamische inhoud wordt weergegeven en de variabele 'travelTime' is geselecteerd.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. Geef voor de eigenschap **Hoofdtekst** de inhoud voor de hoofdtekst van het e-mailbericht op door de volgende stappen uit te voeren:

   1. Voer de tekst in `Add extra travel time (minutes):` met een spatie aan het eind. Bewaar de cursor in het vak **Hoofdtekst** zodat de lijst met dynamische inhoud geopend blijft.

   1. Selecteer in de lijst dynamische inhoud **Expressie**, waarin de expressie-editor wordt weergegeven.

      ![Schermopname waarin de lijst met dynamische inhoud wordt weergegeven en de variabele 'Expressie' is geselecteerd.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. Voer `sub(,15)` in de expressie-editor in, zodat u het aantal minuten waarmee de limiet wordt overschreden, kunt berekenen: 

      ![Schermopname van de expressie-editor waarin de expressie 'sub(,15)' is opgegeven.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Plaats de cursor in de expressie tussen het haakje openen ( **(** ) en de komma ( **,** ) en selecteer **Dynamische inhoud**.

      ![Schermopname die laat zien waar de cursor in de expressie 'div(,15)' met 'dynamische inhoud' is geselecteerd.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. Selecteer onder **Variabelen** **travelTime**.

      ![Schermopname waarin de lijst met dynamische inhoud wordt weergegeven en de variabele 'travelTime' is geselecteerd.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Nadat de eigenschap in de expressie is omgezet, selecteert u **OK**.

      ![Schermopname waarin de lijst met dynamische inhoud wordt weergegeven en 'OK' is geselecteerd.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      De eigenschap **Hoofdtekst** wordt nu zoals hier weergegeven:

      ![Schermopname waarin de lijst met dynamische inhoud wordt weergegeven met de expressie die is opgelost in de eigenschap Body van de e-mailactie.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Sla uw logische app op.

Test vervolgens uw logische app en voer deze uit, die er nu bijna net zo uitziet als dit voorbeeld:

![Schermopname van de voltooide voorbeeldwerkstroom voor logische apps](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Uw logische app uitvoeren

Selecteer **Uitvoeren** op de werkbalk in de ontwerper als u de logische app handmatig wilt uitvoeren.

* Als de actuele reistijd onder uw limiet blijft, zal uw logische app verder niets doen en wacht die met het uitvoeren van een nieuwe controle tot het volgende interval. 

* Als de actuele reistijd de limiet overschrijdt, krijgt u een e-mailbericht met de actuele reistijd en het aantal minuten waarmee de limiet wordt overschreden. Hier ziet u een voorbeeld van een e-mail die uw logische app verstuurt:

  ![Schermopname met een voorbeeld van een e-mailbericht met de huidige reistijd en de extra reistijd die de opgegeven limiet overschrijdt.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

  > [!TIP]
  > Als u geen een e-mailberichten ontvangt, controleert u de map met ongewenste e-mail. Het is mogelijk dat uw filter voor ongewenste e-mail dit soort e-mails in deze map zet. Als u niet zeker weet of uw logische app correct wordt uitgevoerd, kunt u [Problemen met uw logische app oplossen](../logic-apps/logic-apps-diagnosing-failures.md) raadplegen.

Gefeliciteerd, u hebt nu een herhalende logische app op basis van een planning gemaakt en uitgevoerd. 

Als u andere logische apps wilt maken die de trigger **Terugkeerpatroon** gebruiken, kunt u deze sjablonen gebruiken, die beschikbaar worden nadat u een logische app hebt gemaakt:

* Dagelijkse herinneringen per e-mail ontvangen.
* Oudere Azure-blobs verwijderen.
* Een bericht toevoegen aan een wachtrij van Azure Storage.

## <a name="clean-up-resources"></a>Resources opschonen

De logische app wordt uitgevoerd totdat u de app uitschakelt of verwijdert. Als u het voorbeeld van de logische app niet meer nodig hebt, verwijdert u de resourcegroep die uw logische app en alle gerelateerde resources bevat.

1. Voer in het zoekvak van de Azure Portal de naam in voor de resourcegroep die u hebt gemaakt. Selecteer uit de resultaten onder **Resourcegroepen** de resourcegroep.

   In het voorbeeld wordt een resourcegroep gemaakt met de naam `LA-TravelTime-RG`.

   ![Schermopname van het Azure-zoekvak met 'La-Travel-time-RG' ingevoerd en **LA-TravelTime-RG** geselecteerd.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-resource-group.png)

   > [!TIP]
   > Als op de startpagina van Azure de resourcegroep onder **recente resources** wordt weergegeven, kunt u de groep selecteren op de startpagina.

1. Controleer in het menu resourcegroep of **Overzicht** is geselecteerd. Selecteer op de pagina **Overzicht** de optie **Resourcegroep verwijderen**.

   ![Schermopname van het deelvenster Overzicht van de resourcegroep en waarbij op de werkbalk van het deelvenster 'resourcegroep verwijderen' is geselecteerd.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Voer de naam van de resourcegroep in en selecteer **Verwijderen** als het bevestigingsdeelvenster wordt weergegeven.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een logische app gemaakt die verkeer controleert op basis van een opgegeven planning ('s ochtends op doordeweekse dagen) en die actie onderneemt (e-mails verzenden) wanneer de reistijd een bepaalde limiet overschrijdt. Leer nu hoe u een logische app maakt waarmee u aanmeldingen voor een mailinglijst indient ter goedkeuring. Hiervoor gebruikt u een integratie van Azure-services, Microsoft-services en andere Software-as-a-Service (SaaS)-apps.

> [!div class="nextstepaction"]
> [Mailinglijstaanvragen beheren](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
