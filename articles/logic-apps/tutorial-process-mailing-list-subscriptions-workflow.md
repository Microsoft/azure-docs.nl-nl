---
title: Geautomatiseerde op goedkeuring gebaseerde werkstromen bouwen
description: 'Zelfstudie: geautomatiseerde op goedkeuring gebaseerde werkstroom maken die mailinglijstabonnementen verwerkt met behulp van Azure Logic Apps'
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/07/2020
ms.openlocfilehash: 1690b8d143b86e5caa691f5f8f479f715f57f0c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99054650"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Zelfstudie: Geautomatiseerde op goedkeuring gebaseerde workflows maken met behulp van Azure Logic Apps

In deze zelfstudie leert u hoe u een voorbeeld van een [logische app](../logic-apps/logic-apps-overview.md) bouwt waarmee een op goedkeuring gebaseerde workflow wordt geautomatiseerd. Dit voorbeeld van een logische app verwerkt specifiek abonnementsaanvragen voor een mailinglijst die wordt beheerd door de [MailChimp](https://mailchimp.com/)-service. Deze logische app omvat verschillende stappen. De eerste stap bestaat uit het bewaken van een e-mailaccount voor aanvragen, vervolgens worden deze aanvragen ter goedkeuring verzonden, wordt gecontroleerd of de aanvraag wordt goedgekeurd, worden goedgekeurde leden aan de mailinglijst toegevoegd en ten slotte wordt gecontroleerd of er nieuwe leden aan de lijst worden toegevoegd.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een lege, logische app maken.
> * Een trigger toevoegen waarmee e-mailberichten op abonnementaanvragen worden gecontroleerd.
> * Een actie toevoegen waarmee e-mailberichten worden verzonden om deze aanvragen goed te keuren of af te wijzen.
> * Een voorwaarde toevoegen voor het controleren van het goedkeuringsantwoord.
> * Een actie toevoegen waarmee goedgekeurde leden worden toegevoegd aan de adressenlijst.
> * Een voorwaarde toevoegen waarmee wordt gecontroleerd of deze leden met succes aan de lijst zijn toegevoegd.
> * Een voorwaarde toevoegen waarmee e-mailberichten worden verzonden om te bevestigen of deze leden met succes aan de lijst zijn toegevoegd.

Wanneer u bent klaar, ziet uw logische app eruit als deze werkstroom op hoog niveau:

![Overzicht van een voltooide logische app op hoog niveau](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Een MailChimp-account waar u eerder een lijst met de naam test-leden-ML hebt gemaakt en waaraan uw logische app e-mailadressen voor goedgekeurde leden kan toevoegen. Als u geen account hebt, kunt u zich [aanmelden voor een gratis account](https://login.mailchimp.com/signup/) en leren [hoe u een MailChimp-lijst kunt maken](https://us17.admin.mailchimp.com/lists/#).

* Een e-mailaccount van een e-mailprovider die door Logic Apps wordt ondersteund, bijvoorbeeld Office 365 Outlook, Outlook.com of Gmail. Voor andere providers [kunt u hier de lijst met connectors bekijken](/connectors/). In deze quickstart wordt gebruikgemaakt van Microsoft 365 Outlook met een werk- of schoolaccount. Als u een ander e-mailaccount gebruikt, blijven de algemene stappen gelijk, maar uw gebruikersinterface kan er iets anders uitzien.

* Een e-mailaccount in Office 365 Outlook of Outlook.com, die goedkeuringswerkstromen. In deze zelfstudie wordt Office 365 Outlook gebruikt. Als u een ander e-mailaccount gebruikt, blijven de algemene stappen gelijk, maar uw gebruikersinterface kan er iets anders uitzien.

* Als uw logische app moet communiceren via een firewall die het verkeer beperkt tot specifieke IP-adressen, moet die firewall toegang toestaan voor *zowel* de [inkomende](logic-apps-limits-and-config.md#inbound) als [uitgaande](logic-apps-limits-and-config.md#outbound) IP-adressen die worden gebruikt door de Logic apps service of runtime in de Azure-regio waar uw logische app bestaat. Als uw logische app ook [beheerde connectors](../connectors/apis-list.md#managed-api-connectors)gebruikt, zoals de Office 365 Outlook-Connector of de SQL-connector, of [aangepaste connectors](/connectors/custom-connectors/)gebruikt, moet de firewall ook toegang toestaan voor *alle* [uitgaande IP-adressen van beheerde connectors](logic-apps-limits-and-config.md#outbound) in de Azure-regio van de logische app.

## <a name="create-your-logic-app"></a>Uw logische app maken

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com). Selecteer op de startpagina van het Azure-portal **Een resource maken**.

1. Selecteer op het Azure Marketplace-menu **Integratie** > **Logische app**.

   ![Schermopname van het Azure Marketplace-menu met "Integratie" en "Logische app" geselecteerd.](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. Geef in het deelvenster **Logische app** de informatie op die hier wordt beschreven over de Logische app die u wilt maken.

   ![Schermopname van het deelvenster voor het maken van een logische app en de informatie die u voor de nieuwe logische app kunt opgeven.](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Abonnement** | <*Azure-subscription-name*> | Uw Azure-abonnementnaam. In dit voorbeeld wordt `Pay-As-You-Go` gebruikt. |
   | **Resourcegroep** | LA-MailingList-RG | De naam van de [Azure-resourcegroep](../azure-resource-manager/management/overview.md) die wordt gebruikt om verwante resources te organiseren. In het volgende voorbeeld wordt een resourcegroep met de naam `LA-MailingList-RG` gemaakt. |
   | **Naam** | LA-MailingList | De naam van uw logische app mag alleen letters, cijfers, afbreekstreepjes (`-`), onderstrepingstekens (`_`), haakjes (`(`, `)`) en punten (`.`) bevatten. In dit voorbeeld wordt `LA-MailingList` gebruikt. |
   | **Locatie** | VS - west | De regio waarin informatie over uw logische app moet worden opgeslagen. In dit voorbeeld wordt `West US` gebruikt. |
   | **Log Analytics** | Uit | Behoud de instelling **Uit** voor het vastleggen van diagnostische gegevens. |
   ||||

1. Selecteer als u klaar bent de optie **Beoordelen en maken**. Nadat Azure de informatie over uw logische app heeft gevalideerd, selecteert u **Maken**.

1. Nadat uw app in Azure is geïmplementeerd, selecteert u **Naar de resource gaan**.

   Azure opent het deelvenster voor het selecteren van logische apps-sjablonen, waarin een introductievideo, veelgebruikte triggers en sjabloonpatronen voor logische apps wordt weergegeven.

1. Scrol omlaag vanaf de video en de algemene triggers naar **Sjablonen** en selecteer **Lege logische app**.

   ![Schermopname van het deelvenster sjabloonselectie voor logische apps waarin 'lege logische app' is geselecteerd.](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Voeg vervolgens een Outlook-[trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die luistert naar binnenkomende e-mailberichten met abonnementsaanvragen. Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer nieuwe gegevens aan een bepaalde voorwaarde voldoen. Bekijk [Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md) voor meer informatie.

## <a name="add-trigger-to-monitor-emails"></a>Trigger voor het bewaken van e-mailberichten toevoegen

1. In het zoekvak van Logic Apps Designer voert u `when email arrives` in en selecteert u de trigger met de naam **Wanneer een nieuwe e-mail binnenkomt**.

   * Selecteer **Office 365 Outlook** voor werk- of schoolaccounts van Azure.
   * Selecteer **Outlook.com** voor persoonlijke Microsoft-accounts.

   In dit voorbeeld wordt verder Office 365 Outlook geselecteerd.

   ![Schermopname van het zoekvak van de Logic Apps-ontwerpfunctie dat de zoekterm 'wanneer e-mail binnenkomt' bevat en met de trigger 'Wanneer een nieuwe e-mail binnenkomt' wordt weergegeven.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

1. Als u nog geen verbinding hebt, meldt u zich aan en verifieert u de toegang tot uw e-mailaccount wanneer u hierom wordt gevraagd.

   Azure Logic Apps maakt een verbinding met uw e-mailaccount.

1. Geef in de trigger de criteria op voor het controleren van nieuwe e-mail.

   1. Geef de map op voor het controleren van e-mails en laat de andere eigenschappen op de standaardwaarden ingesteld staan.

      ![Schermopname van de ontwerpfunctie met de actie 'Wanneer een nieuwe e-mail binnenkomt' en de map ingesteld op Postvak in.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

   1. Voeg de eigenschap **Onderwerpfilter** van de trigger toe, zodat u e-mailberichten kunt filteren op basis van de onderwerpregel. Open de lijst **Nieuwe parameter toevoegen** en selecteer **Onderwerpfilter**.

      ![Schermopname met de geopende lijst 'Nieuwe parameter toevoegen' en 'Onderwerpfilter' geselecteerd.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Zie voor meer informatie over de eigenschappen van deze trigger de [Office 365 Outlook-connectorreferentie](/connectors/office365/) of de [Outlook.com-connectorreferentie](/connectors/outlook/).

   1. Nadat de eigenschap in de trigger verschijnt, voert u deze tekst in: `subscribe-test-members-ML`

      ![Schermopname met de eigenschap Onderwerpfilter met de ingevoerde tekst 'subscribe-test-members-ML'.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Om de details van de trigger voorlopig te verbergen, vouwt u de vorm samen door in de titelbalk van de vorm te klikken.

   ![Schermopname die de samengevouwen triggervorm laat zien.](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

Uw logische app is nu live, maar doet niets behalve uw binnenkomende e-mail controleren. Daarom gaat u nu een actie toevoegen die reageert wanneer de trigger wordt geactiveerd.

## <a name="send-approval-email"></a>Goedkeurings-e-mail verzenden

Nu u een trigger hebt, kunt u een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) toevoegen waarmee een e-mailbericht wordt verzonden om de aanvraag goed te keuren of af te wijzen.

1. Selecteer in de Logic Apps-ontwerpfunctie, onder de trigger **Wanneer een nieuwe e-mail binnenkomt**, de optie **Nieuwe stap**.

1. Onder **Kies een actie** voert u `send approval` in het zoekvak in en selecteert u de actie **Een e-mail ter goedkeuring verzenden**.

   ![Schermopname met de lijst 'Kies een bewerking', gefilterd op acties voor 'goedkeuring' en met de actie 'een e-mail ter goedkeuring verzenden' geselecteerd.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Voer nu de waarden voor de opgegeven eigenschappen in, zoals hier wordt weergegeven en beschreven. waarbij u alle overige op de standaardwaarden laat staan. Zie [Office 365 Outlook-connectorreferentie](/connectors/office365/) of [Outlook.com-connectorreferentie](/connectors/outlook/) voor meer informatie over deze eigenschappen.

   ![Schermopname met de eigenschappen voor E-mail ter goedkeuring verzenden](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Aan** | <*approval-email-address*> | Het e-mailadres van de fiatteur. Voor testdoeleinden kunt u uw eigen adres gebruiken. In dit voorbeeld wordt het fictieve e-mailadres `sophiaowen@fabrikam.com` gebruikt. |
   | **Onderwerp** | `Approve member request for test-members-ML` | Een beschrijvend e-mailonderwerp |
   | **Gebruikersopties** | `Approve, Reject` | Zorg ervoor dat deze eigenschap de antwoordopties aangeeft die de fiatteur kan selecteren, te weten **Goedkeuren** of **Weigeren** (de standaardopties). |
   ||||

   > [!NOTE]
   > Wanneer u in een aantal bewerkingsvelden klikt, verschijnt de dynamische inhoudslijst. U kunt deze voorlopig negeren. In de lijst ziet u de uitvoer van de vorige acties die u kunt selecteren als invoer voor de volgende acties in uw werkstroom.
 
1. Sla uw logische app op.

Voeg vervolgens een voorwaarde toe waarmee het door de fiatteur geselecteerde antwoord wordt gecontroleerd.

## <a name="check-approval-response"></a>Goedkeuringsantwoord controleren

1. Selecteer onder de actie **Goedkeurings-e-mail verzenden** de optie **Nieuwe stap**.

1. Selecteer onder **Kies een actie** de optie **Ingebouwd**. Voer in het zoekvenster `condition` in en selecteer de actie **Voorwaarde**.

   ![Schermopname met het zoekvak Kies een bewerking, met Ingebouwd geselecteerd en 'voorwaarde' als zoekterm, terwijl de actie Voorwaarde is geselecteerd.](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Selecteer op de titelbalk **Voorwaarde** het **beletselteken** ( **...** ) en selecteer vervolgens **Naam wijzigen**. Wijzig de naam van de voorwaarde met deze beschrijving: `If request approved`

   ![Schermopname waarin de knop met het beletselteken is geselecteerd, de lijst Instellingen is geopend en de opdracht Naam wijzigen is geselecteerd.](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

1. Bouw een voorwaarde op waarmee wordt gecontroleerd of de fiatteur **Goedkeuren** heeft geselecteerd.

   1. Klik links van de voorwaarde in het vak **Kies een waarde**.

   1. Selecteer in de lijst met dynamische inhoud die verschijnt onder **Goedkeurings-e-mail verzenden** de eigenschap **SelectedOption**.

      ![Schermopname van de lijst met dynamische inhoud waar in de sectie Goedkeuring-e-mail verzenden de uitvoer SelectedOption is geselecteerd.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. Selecteer in het middelste vergelijkingsvak de operator **is gelijk aan**.

   1. Voer rechts van de voorwaarde, in het vak **Kies een waarde**, de tekst `Approve` in.

      Wanneer u klaar bent, ziet de voorwaarde eruit zoals in dit voorbeeld:

      ![Schermopname met de voltooide voorwaarde voor het voorbeeld van de goedgekeurde aanvraag](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Sla uw logische app op.

Geef vervolgens de actie op die uw logische app uitvoert wanneer de fiatteur de aanvraag goedkeurt. 

## <a name="add-member-to-mailchimp-list"></a>Lid toevoegen aan MailChimp-lijst

Voeg nu een actie toe waarmee het goedgekeurde lid wordt toegevoegd aan uw mailinglijst.

1. Selecteer in de vertakking **Indien waar** van de voorwaarde de optie **Een actie toevoegen**.

1. Selecteer onder het zoekvak **Kies een bewerking** de optie **Alle**. Voer in het zoekvak `mailchimp` in en selecteer de actie **Lid aan lijst toevoegen**.

   ![Schermopname van het vak Kies een bewerking, met de zoekterm mailchimp, en met de actie Lid aan lijst toevoegen geselecteerd.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Als er nog geen verbinding met uw MailChimp-account is, wordt u gevraagd u aan te melden.

1. Geef in de actie **Lid aan lijst toevoegen** de informatie op zoals hier wordt weergegeven en beschreven:

   ![Schermopname met de gegevens voor de actie Lid aan lijst toevoegen.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **List Id (Lijst-id)** | Ja | <*mailing-list-name*> | Selecteer de naam voor uw MailChimp-mailinglijst. In dit voorbeeld wordt `test-members-ML` gebruikt. |
   | **Email Address (E-mailadres)** | Ja | <*e-mailadres-nieuw-lid*> | In de lijst met dynamische inhoud die wordt geopend, selecteert u in het gedeelte **Wanneer een nieuwe e-mail binnenkomt** de optie **Van**. Dit is uitvoer van de trigger en geeft het e-mailadres voor het nieuwe lid op. |
   | **Status** | Ja | <*member-subscription-status*> | Selecteer de abonnementsstatus die voor het nieuwe lid is ingesteld. In dit voorbeeld wordt `subscribed` geselecteerd. <p>Zie voor meer informatie [Manage subscribers with the MailChimp API (Abonnees beheren met de MailChimp-API)](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   |||||

   Zie de **MailChimp-connectorreferentie** voor meer informatie over de eigenschappen van de actie [Lid aan lijst toevoegen](/connectors/mailchimp/).

1. Sla uw logische app op.

Voeg vervolgens een voorwaarde toe waarmee u kunt controleren of het nieuwe lid met succes aan uw adressenlijst is toegevoegd. Op die manier meldt uw logische app u of de bewerking is geslaagd.

## <a name="check-for-success-or-failure"></a>Controleren op slagen of mislukken

1. In de vertakking **Waar**, onder de actie **Lid aan lijst toevoegen**, selecteert u **Een actie toevoegen**.

1. Selecteer onder **Kies een actie** de optie **Ingebouwd**. Voer in het zoekvenster `condition` in en selecteer de actie **Voorwaarde**.

1. Wijzig de naam van de voorwaarde met deze beschrijving: `If add member succeeded`

1. Maak een voorwaarde die controleert of het goedgekeurde lid met succes aan uw adressenlijst is toegevoegd of niet:

   1. Klik links van de voorwaarde in het vak **Kies een waarde**. Selecteer in de lijst met dynamische inhoud die verschijnt, in het gedeelte **Lid aan lijst toevoegen**, de eigenschap **Status**.

      Uw voorwaarde ziet eruit zoals in dit voorbeeld:

      ![Schermopname met aan de linkerkant van de voorwaarde het vak Kies een waarde, met hierin Status ingevoerd.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. Selecteer in het middelste vergelijkingsvak de operator **is gelijk aan**.

   1. Voer in het vak **Kies een waarde**, aan de rechterkant van de voorwaarde, deze tekst in: `subscribed`

      Wanneer u klaar bent, ziet de voorwaarde eruit zoals in dit voorbeeld:

      ![Schermopname van de voltooide voorwaarde voor het controleren van een geslaagd of mislukt abonnement.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Stel vervolgens de e-mailberichten in die moeten worden verzonden als het goedgekeurde lid al dan niet aan uw mailinglijst wordt toegevoegd.

## <a name="send-email-if-member-added"></a>E-mailbericht verzenden als lid is toegevoegd

1. Onder de voorwaarde **Indien toevoegen van lid is geslaagd**, in de vertakking **Waar**, selecteert u **Een actie toevoegen**.

   ![Schermopname van de vertakking Waar van de voorwaarde Indien toevoegen van lid is geslaagd, met Een actie toevoegen geselecteerd.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. In het zoekvak **Kies een bewerking** voert u `outlook send email` in en selecteert u de actie **Een e-mail verzenden**.

   ![Schermopname van het zoekvak Kies een bewerking waarin 'e-mail is verzonden', met de actie Een e-mail verzenden geselecteerd als melding.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Wijzig de naam van de actie met deze beschrijving: `Send email on success`

1. Geef in de actie **E-mail verzenden indien geslaagd** de informatie op zoals hier wordt weergegeven en beschreven:

   ![Schermopname met de actie E-mail verzenden indien geslaagd, en de informatie die is opgegeven voor dit e-mailbericht.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Hoofdtekst** | Ja | <*success-email-body*> | De hoofdtekst voor het e-mailbericht bij succes. Voor deze zelfstudie voert u de volgende stappen uit: <p>1. Voer deze tekst in met een spatie aan het eind: `New member has joined "test-members-ML":` <p>2. Selecteer in de lijst met dynamische inhoud die verschijnt, de eigenschap **E-mailadres**. <p>**Opmerking**: Als deze eigenschap niet wordt weergegeven, klikt u naast de sectiekop **Lid aan lijst toevoegen** de optie **Meer weergeven**. <p>3. Voer in de volgende rij deze tekst in met een spatie aan het eind: `Member opt-in status: ` <p>4. Selecteer in de lijst met dynamische inhoud onder **Lid toevoegen aan lijst** de eigenschap **Status**. |
   | **Onderwerp** | Ja | <*success-email-subject*> | Het onderwerp voor het e-mailbericht bij succes. Voor deze zelfstudie voert u de volgende stappen uit: <p>1. Voer deze tekst in met een spatie aan het eind: `Success! Member added to "test-members-ML": ` <p>2. Selecteer in de lijst met dynamische inhoud onder **Lid toevoegen aan lijst** de eigenschap **E-mailadres**. |
   | **Aan** | Ja | <*uw-e-mailadres*> | Het e-mailadres waarnaar het bericht bij succes moet worden verzonden. Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken. |
   |||||

1. Sla uw logische app op.

## <a name="send-email-if-member-not-added"></a>E-mailbericht verzenden als lid niet is toegevoegd

1. Onder de voorwaarde **Indien toevoegen van lid is geslaagd**, in de vertakking **Niet waar**, selecteert u **Een actie toevoegen**.

   ![Schermopname van de vertakking Niet waar van de voorwaarde Indien toevoegen van lid is geslaagd, met Een actie toevoegen geselecteerd.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. In het zoekvak **Kies een bewerking** voert u `outlook send email` in en selecteert u de actie **Een e-mail verzenden**.

   ![Schermopname van het zoekvak Kies een bewerking waarin 'e-mail is verzonden', met de actie Een e-mail verzenden geselecteerd.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Wijzig de naam van de actie met deze beschrijving: `Send email on failure`

1. Geef informatie over deze actie op zoals hier wordt weergegeven en beschreven:

   ![Schermopname met de actie E-mail verzenden indien mislukt, en de informatie die is opgegeven voor dit e-mailbericht.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Hoofdtekst** | Ja | <*hoofdtekst-voor-e-mail-bij-mislukken*> | De hoofdtekst voor het e-mailbericht bij mislukken. Voer voor deze zelfstudie deze tekst in: <p>`Member might already exist. Check your MailChimp account.` |
   | **Onderwerp** | Ja | <*onderwerp-voor-e-mail-bij-mislukken*> | Het onderwerp voor het e-mailbericht bij mislukken. Voor deze zelfstudie voert u de volgende stappen uit: <p>1. Voer deze tekst in met een spatie aan het eind: `Failed, member not added to "test-members-ML": ` <p>2. Selecteer in de lijst met dynamische inhoud onder **Lid toevoegen aan lijst** de eigenschap **E-mailadres**. |
   | **Aan** | Ja | <*uw-e-mailadres*> | Het e-mailadres waarnaar het bericht bij mislukken moet worden verzonden. Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken. |
   |||||

1. Sla uw logische app op. 

Test vervolgens uw logische app, die er nu bijna net zo uitziet als dit voorbeeld:

![Schermopname van de voltooide voorbeeldwerkstroom voor logische apps.](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Uw logische app uitvoeren

1. Stuur uzelf een e-mailbericht met een aanvraag om lid te worden van uw adressenlijst. Wacht totdat de aanvraag in uw Postvak IN aankomt.

1. Selecteer **Uitvoeren** op de werkbalk in de ontwerpfunctie als u de logische app handmatig wilt uitvoeren. 

   Als uw e-mail een onderwerp heeft dat overeenkomt met het onderwerpfilter van de trigger, stuurt uw logische app u een e-mail om de abonnementaanvraag goed te keuren.

1. Selecteer **Goedkeuren** in de goedkeurings-e-mail die u ontvangt.

1. Als het e-mailadres van de abonnee niet voorkomt in uw adressenlijst, voegt uw logische app het e-mailadres van die persoon toe en stuurt u een e-mail zoals dit voorbeeld:

   ![Schermopname met het voorbeeld van een e-mailbericht voor een geslaagd abonnement.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Als uw logische app de abonnee niet kan toevoegen, krijgt u een e-mail als in dit voorbeeld:

   ![Schermopname met het voorbeeld van een e-mailbericht voor een mislukt abonnement.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

  > [!TIP]
  > Als u geen een e-mailberichten ontvangt, controleert u de map met ongewenste e-mail. Het is mogelijk dat uw filter voor ongewenste e-mail dit soort e-mails in deze map zet. Als u niet zeker weet of uw logische app correct wordt uitgevoerd, kunt u [Problemen met uw logische app oplossen](../logic-apps/logic-apps-diagnosing-failures.md) raadplegen.

Gefeliciteerd, u hebt nu een logische app gemaakt en uitgevoerd die informatie in Azure, Microsoft-services en andere SaaS-apps integreert.

## <a name="clean-up-resources"></a>Resources opschonen

De logische app wordt uitgevoerd totdat u de app uitschakelt of verwijdert. Als u het voorbeeld van de logische app niet meer nodig hebt, verwijdert u de resourcegroep die uw logische app en alle gerelateerde resources bevat.

1. Voer in het zoekvak van de Azure Portal de naam in voor de resourcegroep die u hebt gemaakt. Selecteer uit de resultaten onder **Resourcegroepen** de resourcegroep.

   In het voorbeeld wordt een resourcegroep gemaakt met de naam `LA-MailingList-RG`.

   ![Schermopname van het Azure-zoekvak met 'la-mailinglist-rg' ingevoerd en **LA-MailingList-RG** geselecteerd.](./media/tutorial-process-mailing-list-subscriptions-workflow/find-resource-group.png)

   > [!TIP]
   > Als op de startpagina van Azure de resourcegroep onder **recente resources** wordt weergegeven, kunt u de groep selecteren op de startpagina.

1. Controleer in het menu resourcegroep of **Overzicht** is geselecteerd. Selecteer op de pagina **Overzicht** de optie **Resourcegroep verwijderen**.

   ![Schermopname van het deelvenster Overzicht van de resourcegroep en waarbij op de werkbalk van het deelvenster 'resourcegroep verwijderen' is geselecteerd.](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. Voer de naam van de resourcegroep in en selecteer **Verwijderen** als het bevestigingsdeelvenster wordt weergegeven.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een logische app gemaakt die goedkeuringen voor mailinglijstaanvragen verwerkt. Leer nu hoe u een logische app kunt bouwen die e-mailbijlagen verwerkt en opslaat door integratie van Azure-diensten, zoals Azure Storage en Azure Functions.

> [!div class="nextstepaction"]
> [E-mailbijlagen verwerken](../logic-apps/tutorial-process-email-attachments-workflow.md)
