---
title: 'Quickstart: uw eerste werkstroom voor Logic Apps bouwen - Azure Portal'
description: Bouw uw eerste geautomatiseerde Logic Apps-werkstroom in de Azure Portal met behulp van deze quickstart. Informatie over de basis van systeemintegratie en oplossingen van Enterprise Application Integration (EAI) in Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/30/2020
ms.openlocfilehash: d90b9e38158d951990fffc21a43317c688da12c9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99052039"
---
# <a name="quickstart-create-your-first-logic-apps-workflow---azure-portal"></a>Quickstart: Uw eerste werkstroom voor Logic Apps bouwen - Azure Portal

In deze quickstart wordt uitgelegd hoe u uw eerste werkstroom kunt bouwen in [Azure Logic Apps](logic-apps-overview.md) via de [Azure Portal](https://portal.azure.com). In deze inleidende handleiding worden ook de basisconcepten van de Logic Apps-service uitgelegd, met inbegrip van het maken van een nieuwe logische app, het toevoegen van een trigger en actie aan uw logische app en het testen van uw logische app. Volg deze quickstart om een voorbeeld van een logische app te maken die regelmatig een RSS-feed controleert en een e-mailmelding verzendt voor nieuwe items. De volgende schermopname toont de werkstroom op hoog niveau van deze logische voorbeeld-app:

![Schermopname van Logic Apps Designer toont een logische voorbeeld-app waarbij de trigger een RSS-feed is en de actie een e-mail verzendt.](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

<a name="prerequisites"></a>

Als u wilt weten hoe u uw eerste logische app kunt maken en beheren via andere interfaces en apps, raadpleegt u de volgende Logic Apps quickstarts: 

* [Logische apps maken en beheren met behulp van de Azure Command Line Interface (Azure CLI)](quickstart-logic-apps-azure-cli.md)
* [Logische apps maken en beheren in Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)
* [Logische apps maken en beheren in Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. Als u er geen hebt, kunt u zich [Registreren voor een gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Een e-mailaccount van een service die door Logic Apps wordt ondersteund (bijvoorbeeld Office 365 Outlook of Outlook.com). Voor andere ondersteunde e-mailproviders [bekijkt u de lijst met connectors](/connectors/).

    > [!IMPORTANT]
    > Als u de [Gmail-connector](/connectors/gmail/) gebruikt, kunnen alleen accounts van G Suite deze connector zonder beperking in Logic Apps gebruiken. Als u een Gmail-consumentenaccount hebt, kunt u deze connector alleen gebruiken met specifieke, door Google goedgekeurde services, tenzij u [een Google-client-app maakt voor verificatie bij uw Gmail-connector](/connectors/gmail/#authentication-and-bring-your-own-application). Zie [Beleid voor gegevensbeveiliging en privacybeleid voor Google-connectors in Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md) voor meer informatie.

* Als uw logische app moet communiceren via een firewall die het verkeer beperkt tot specifieke IP-adressen, moet die firewall toegang toestaan voor *zowel* de [inkomende](logic-apps-limits-and-config.md#inbound) als [uitgaande](logic-apps-limits-and-config.md#outbound) IP-adressen die worden gebruikt door de Logic apps service of runtime in de Azure-regio waar uw logische app bestaat. Als uw logische app ook [beheerde connectors](../connectors/apis-list.md#managed-api-connectors)gebruikt, zoals de Office 365 Outlook-Connector of de SQL-connector, of [aangepaste connectors](/connectors/custom-connectors/)gebruikt, moet de firewall ook toegang toestaan voor *alle* [uitgaande IP-adressen van beheerde connectors](logic-apps-limits-and-config.md#outbound) in de Azure-regio van de logische app.

<a name="create-logic-app"></a>

## <a name="create-your-logic-app"></a>Uw logische app maken

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Voer in het zoek van de Azure-portal `logic apps` in en selecteer **Logic Apps**.

   ![Schermopname van het zoekvak in de Azure Portal met 'logic apps' als zoekterm en 'Logic Apps' als het geselecteerde zoekresultaat.](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. Selecteer op de pagina **Logic Apps** de optie **Toevoegen**.

   ![Schermopname van de Logic Apps-servicepagina in Azure Portal, met daarin de lijst met logische apps en de geselecteerde knop 'Toevoegen'.](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. Geef in het deelvenster **Logic App** basisinformatie en instellingen op voor uw logische app. Maak een nieuwe [resourcegroep](../azure-resource-manager/management/overview.md#terminology) voor het doel van deze logische voorbeeld-app.

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | <*naam-van-logische-app*> | Namen voor logische apps moeten uniek zijn in verschillende regio's. De naam mag alleen letters, cijfers, afbreekstreepjes (`-`), onderstrepingstekens (`_`), haakjes (`(` en `)`) en punten (`.`) bevatten. In dit voorbeeld wordt 'My-First-Logic-App' gebruikt. |
   | **Abonnement** | <*Azure-subscription-name*> | De naam van uw Azure-abonnement. |
   | **Resourcegroep** | <*Naam-van-Azure-resourcegroep*> | De naam van de [Azure-resourcegroep](../azure-resource-manager/management/overview.md#terminology) waarin u de logische app maakt. Namen voor resourcegroepen moeten uniek zijn in verschillende regio's. In dit voor beeld wordt 'My-First-LA-RG' gebruikt. |
   | **Locatie** | <*Azure-regio*> | De Azure regio waarin informatie over uw logische app moet worden opgeslagen. In dit voorbeeld wordt 'US - west' gebruikt. |
   | **Log Analytics** | Uitgeschakeld | De instelling voor diagnostische logboekregistratie, die standaard is **Uitgeschakeld**. Behoud voor deze zelfstudie de instelling **Uitgeschakeld**. |
   ||||

   ![Schermopname van de pagina 'Logic Apps maken', met daarop het deelvenster met details voor een nieuwe logische app.](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

1. Als u klaar bent, selecteert u **Beoordelen en maken**. Controleer de gegevens die u hebt ingevoerd en selecteer **Maken**.

1. Nadat uw app in Azure is geïmplementeerd, selecteert u **Naar de resource gaan**. U kunt ook uw logische app zoeken en selecteren door de naam in het zoekvak te typen.

   ![Schermopname van de pagina voor het implementeren van een resource waarop de knop 'Ga naar resource' is geselecteerd.](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   De Logic Apps-ontwerpfunctie wordt geopend en u ziet een pagina met een inleidende video en veelgebruikte triggers. Kies onder **Sjablonen** de optie **Lege logische app**.

   ![Schermopname van de sjabloongalerie van Logic Apps Designer en de geselecteerde sjabloon 'Lege Logic App'.](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Voeg vervolgens [Een trigger toe aan uw logische app](#add-rss-trigger).

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>De RSS-trigger toevoegen

Elke logische app moet beginnen met een [trigger](../logic-apps/logic-apps-overview.md#how-do-logic-apps-work), die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer er aan een bepaalde voorwaarde is voldaan. Telkens wanneer de trigger nieuwe items vindt, wordt de trigger geactiveerd en maakt de Logic Apps-engine een exemplaar van een logische app. Dit exemplaar wordt gestart en voert de werkstroom uit. Als de trigger geen nieuwe items vindt, wordt deze niet geactiveerd en wordt het instantiëren van de werkstroom overgeslagen.

In dit snelstartvoorbeeld, nadat u [een logische app hebt gemaakt](#create-your-logic-app), voegt u een trigger toe. Deze trigger controleert op nieuwe items in een RSS-feed en wordt geactiveerd wanneer er nieuwe items zijn. U kunt ook logische apps maken met verschillende typen triggers, zoals in de zelfstudie voor het [maken van automatische werkstromen voor goedkeuring](tutorial-process-mailing-list-subscriptions-workflow.md).

1. Selecteer in de **Ontwerpfunctie van logische app** onder het zoekvak de optie **Alle**.

1. Typ `rss` in het zoekvak om de RSS-connector te vinden. Selecteer in de lijst met **Triggers** de RSS-trigger **Wanneer een feeditem wordt gepubliceerd**.

   ![Schermopname van Logic Apps Designer met 'rss' in het zoekvak en de geselecteerde trigger 'Wanneer een feeditem wordt gepubliceerd'.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Geef de URL van de RSS-feed voor de trigger op. Definieer vervolgens het schema van de trigger door het interval en de frequentie in te stellen.

   | Eigenschap | Waarde | Beschrijving |
   | -------- | ----- | ----------- |
   | **De URL voor de RSS-feed** | <*RSS-feed-URL*> | De URL van de RSS-feed die u wilt bewaken. Dit voorbeeld gebruikt de RSS-feed van Wall Street Journal op `https://feeds.a.dj.com/rss/RSSMarketsMain.xml`. In dit voorbeeld kunt u echter een RSS-feed gebruiken waarvoor geen HTTP-autorisatie is vereist. Kies een RSS-feed die regelmatig wordt gepubliceerd, zodat u uw logische app later eenvoudig kunt testen. |
   | **Interval** | 1 | Het aantal intervallen dat tussen controles moet worden gewacht. In dit voorbeeld worden intervallen van 1 minuut gebruikt. |
   | **Frequentie** | Minuut | De tijdseenheid voor elk interval tussen controles van de RSS-feed. In dit voorbeeld worden intervallen van 1 minuut gebruikt. |
   ||||

   ![Schermopname van Logic Apps Designer met instellingen voor een RSS-trigger, inclusief de URL, de frequentie en het interval.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

1. Verberg de details van de triggers, voor nu, door in de titelbalk van de vorm te klikken.

   ![Schermopname van Logic Apps Designer met een samengevouwen shape van een logische app.](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Sla uw logische app op door op **Opslaan** te selecteren op de werkbalk van de ontwerpfunctie.

Uw logische app is nu live, maar kan alleen de RSS-feed controleren. [Voeg vervolgens een actie toe](#add-email-action) om te bepalen wat er gebeurt wanneer de trigger wordt geactiveerd.

<a name="add-email-action"></a>

## <a name="add-the-send-email-action"></a>De actie 'E-mail verzenden' toevoegen

Nadat u [een trigger voor uw logische app hebt toegevoegd](#add-rss-trigger), moet u vervolgens een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) toevoegen. Dit doet u om het antwoord te bepalen wanneer uw logische app de RSS-feed controleert en er een nieuw item wordt weergegeven. U kunt ook logische apps maken met veel complexere acties, zoals in de zelfstudie voor het [verwerken van e-mails met Logic Apps, Azure Functions en Azure Storage](./tutorial-process-email-attachments-workflow.md).

> [!NOTE]
> In dit voorbeeld wordt gebruikgemaakt van Office 365 Outlook als e-mailservice. Als u een andere, ondersteunde e-mail service in uw logische app gebruikt, kan de gebruikersinterface er anders uitzien. De basisconcepten voor het maken van verbinding met een andere e-mailservice blijven echter ongewijzigd.

1. Selecteer onder de trigger **Wanneer een feeditem wordt gepubliceerd** **Nieuwe stap**.

   ![Schermopname van de Logic Apps Designer, waarin een werkstroom wordt weergegeven met de geselecteerde knop 'Nieuwe stap'.](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. Selecteer onder **Kies een actie** en het zoekvak de optie **Alle**.

1. Typ `send an email` in het zoekvak om connectors te vinden die deze actie aanbieden. Als u de lijst met acties wilt filteren op een bepaalde app of service, kunt u eerst die app of service selecteren.

   Als u bijvoorbeeld een werk- of schoolaccount van Microsoft gebruikt en Office 365 Outlook wilt gebruiken, selecteert u **Office 365 Outlook**. Als u gebruikmaakt van een persoonlijk Microsoft-account, kunt u ook Outlook.com selecteren. In dit voorbeeld wordt verder Office 365 Outlook gebruikt:

   ![Schermopname van de Logic Apps Designer, met daarin de actiestap met de geselecteerde e-mailconnector 'Office 365 Outlook'.](./media/quickstart-create-first-logic-app-workflow/select-connector.png)

   U kunt de actie die u wilt gebruiken nu gemakkelijker vinden en selecteren, bijvoorbeeld `send an email`:

   ![Schermopname van de Logic Apps Designer, met daarin een lijst van gefilterde acties voor de e-mailconnector 'Office 365 Outlook'.](./media/quickstart-create-first-logic-app-workflow/filtered-actions-list.png)

1. Als de geselecteerde e-mailconnector u vraagt om uw identiteit te verifiëren, voert u deze stap nu uit. U moet een verbinding maken tussen uw logische app en uw e-mailservice om dit voorbeeld te laten werken. 

    > [!NOTE]
    > Dit voorbeeld toont handmatige verificatie voor de Office 365 Outlook-connector. Andere connectors kunnen echter verschillende verificatietypen ondersteunen.
    > U kunt ook verificatie voor uw logische apps op verschillende manieren afhandelen, afhankelijk van uw gebruiksvoorbeeld. Als u bijvoorbeeld Azure Resource Manager-sjablonen gebruikt voor implementatie, kunt u parameteriseren om de beveiliging te verbeteren voor invoer die u vaak of eenvoudig wilt wijzigen (zoals verbindingsgegevens). Raadpleeg de volgende onderwerpen voor meer informatie:
   > * [Sjabloonparameters voor implementatie](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [OAuth-verbindingen autoriseren](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Toegang verifiëren met beheerde identiteiten](../logic-apps/create-managed-service-identity.md)
   > * [Verbindingen voor implementatie van logische apps verifiëren](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. In de actie **Een e-mail verzenden** geeft u de informatie op die u in de e-mailmelding wilt opnemen.

   1. Voer het e-mailadres van de ontvanger in het vak **Aan** in. Gebruik voor dit voorbeeld uw e-mailadres.

        > [!NOTE]
        > De **Lijst met dynamische inhoud toevoegen** wordt weergegeven wanneer u in het vak **Naar** en bepaalde andere invoervakken in de Logic Apps Designer klikt. In dit voorbeeld wordt dynamische inhoud in een latere stap gebruikt. De **Lijst met dynamische inhoud toevoegen** bevat alle beschikbare uitvoer, van de vorige stap, die u kunt gebruiken als invoer voor de huidige actie.

   1. Voer in het vak **Onderwerp** het onderwerp in voor uw e-mailmelding. Voor dit voorbeeld voert u de volgende tekst in met een afsluitende spatie: `New RSS item: `

      ![Schermopname van Logic Apps Designer met de actie 'Een e-mail verzenden' en de cursor in het vak voor de eigenschap 'Onderwerp'.](./media/quickstart-create-first-logic-app-workflow/send-email-subject.png)

   1. Selecteer in de lijst **Dynamische inhoud toevoegen** **Feedtitel**, die deel is van de triggeruitvoer **Wanneer een feeditem wordt gepubliceerd**. Uw e-mailmelding gebruikt deze uitvoer om de titel van het RSS-item op te halen.

      ![Schermopname van Logic Apps Designer met de actie 'Een e-mail verzenden' en de cursor in het vak voor de eigenschap 'Onderwerp' met een lijst met geopende dynamische inhoud en de geselecteerde uitvoer 'Feedtitel'.](./media/quickstart-create-first-logic-app-workflow/send-email-subject-dynamic-content.png)

      > [!TIP]
      > Als er in de lijst met dynamische inhoud geen uitvoer wordt weergegeven van de trigger **Wanneer een feeditem wordt gepubliceerd** naast de header van de actie, selecteert u **Meer weergeven**.
      > 
      > ![Schermopname van Logic Apps Designer. Hierop is de lijst te zien met geopende dynamische inhoud en 'Meer weergeven' geselecteerd voor de trigger.](./media/quickstart-create-first-logic-app-workflow/dynamic-content-list-see-more-actions.png)

      Wanneer u klaar bent, ziet het onderwerp van de e-mail eruit zoals in dit voorbeeld:

      ![Schermopname van Logic Apps Designer met de actie 'Een e-mail verzenden' en een voorbeeld van een e-mailonderwerp met opgenomen eigenschap 'Feedtitel'.](./media/quickstart-create-first-logic-app-workflow/send-email-feed-title.png)

      Als een 'For each'-lus in de ontwerpfunctie wordt weergegeven, hebt u een token voor een matrix geselecteerd, bijvoorbeeld het token **Categories-Item**. Voor dit type tokens voegt de ontwerpfunctie automatisch deze lus toe rond de actie die verwijst naar dat token. Op die manier wordt dezelfde actie uitgevoerd voor elk matrixitem. Selecteer het **beletselteken** (**...**) op de titelbalk van de lus en kies vervolgens **Verwijderen** om de lus te verwijderen.

   1. Voer in het vak **Body** de inhoud voor de body van de e-mail in. In dit voorbeeld bevat de inhoud drie eigenschappen met beschrijvende tekst voor elke: `Title:`, de eigenschap **feedtitel**, `Date published:`, de eigenschap **Feed gepubliceerd op**, en `Link:` (de eigenschap **Primaire feedlink**). Als u lege regels wilt toevoegen in een invoervak, drukt u op Shift + Enter.

      | Eigenschap | Beschrijving |
      |----------|-------------|
      | **Feedtitel** | De titel van het item |
      | **Feed gepubliceerd op** | De publicatiedatum en -tijd van het item |
      | **Primaire feedkoppeling** | De URL voor het item |
      |||

      ![Schermopname van Logic Apps Designer met de actie 'Een e-mail verzenden' en geselecteerde eigenschappen in het vak 'Body'.](./media/quickstart-create-first-logic-app-workflow/send-email-body.png)

1. Sla uw logische app op. Selecteer **Opslaan** in het ontwerpmenu.

[Test vervolgens of uw logische app werkt](#test-logic-app).

<a name="test-logic-app"></a>

## <a name="run-your-logic-app"></a>Uw logische app uitvoeren

Nadat u uw logische voorbeeld-app hebt gemaakt, controleert u of de werkstroom juist is geconfigureerd. U kunt wachten op de logische app om uw RSS-feed op basis van het opgegeven schema te controleren. Of u kunt uw logische app handmatig uitvoeren door op **Uitvoeren** te klikken in de werkbalk 'Logic Apps Designer', zoals weergeven in de volgende schermopname. 

Als de RSS-feed nieuwe items heeft, verzendt uw logische app een e-mailbericht voor elk nieuw item. Anders wacht uw logische app tot het volgende interval om de RSS-feed opnieuw te controleren. 

![Schermopname van Logic Apps Designer met de knop 'Uitvoeren' geselecteerd in het ontwerpmenu.](./media/quickstart-create-first-logic-app-workflow/run-logic-app-test.png)

In de volgende schermoponame ziet u een voorbeeld van een e-mailmelding van deze logische voorbeeld-app. De e-mailmelding bevat de details voor elk item in de RSS-feed dat is geselecteerd in het ontwerpmenu, evenals de beschrijvende tekst die voor elke items is toegevoegd.

![Schermopname van Outlook, waarop een voorbeeld wordt getoond van een e-mailmelding. Deze melding is ontvangen toen er een nieuw item in de RSS-feed verscheen. De melding geeft de titel van het item, de publicatiedatum en de link weer.](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Als u geen e-mailmeldingen van de logische app op de verwachte manier ontvangt:

* Controleer de map 'Ongewenste e-mail' van uw e-mail account als het bericht onjuist is gefilterd.
* Zorg ervoor dat de RSS-feed die u gebruikt gepubliceerde items heeft sinds de laatste geplande of handmatige controle.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het testen van deze logische voorbeeld-app, moet u de app en alle gerelateerde resources opschonen. Dit doet u door de resourcegroep die u hebt gemaakt voor dit voorbeeld te verwijderen.

> [!NOTE]
> Wanneer u [een logische app verwijdert](manage-logic-apps-with-azure-portal.md#delete-logic-apps), worden geen nieuwe uitvoeringen geïnstantieerd. Alle uitvoeringen die bezig zijn en wachten op uitvoering worden geannuleerd. Als u duizenden uitvoeringen hebt, kan de annulering een aanzienlijke tijd in beslag nemen.

1. Typ `resource groups` in het zoekvak van Azure en selecteer **Resourcegroepen**.

   ![Schermopname van het zoekvak van de Azure-portal zoekvak met de zoekterm 'resourcegroepen'.](./media/quickstart-create-first-logic-app-workflow/find-resource-groups.png)

1. Zoek en selecteer de resourcegroep van uw logische app. Selecteer op de pagina **Overzicht** de optie **Resourcegroep verwijderen**.

   ![Schermopname van de Azure-portal met de geselecteerde resourcegroep en de knop 'Resourcegroep verwijderen'.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Voer de naam van de resourcegroep in en selecteer **Verwijderen** als het bevestigingsdeelvenster wordt weergegeven.

   ![Schermopname van de Azure-portal met het bevestigingsvenster en de ingevoerde naam van de resourcegroep die u wilt verwijderen.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u uw eerste logische app gemaakt in de Azure Portal. Deze app controleert een RSS-feed op updates volgens een planning en verzendt een e-mailmelding over elk nieuw item in de feed. 

Zie de volgende zelfstudie als u meer wilt weten over het maken van geavanceerde werkstromen op basis van planning in Logic Apps:

> [!div class="nextstepaction"]
> [Verkeer controleren met een logische app op basis van een planning](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)