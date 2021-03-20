---
title: 'Wijzigingen in virtuele machines bewaken: Azure Event Grid en Logic Apps'
description: Hier leert u hoe u met behulp van Azure Event Grid en Logic Apps op wijzigingen in virtuele machines (VM's) kunt controleren
services: logic-apps, event-grid
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: estfan, LADocs
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 1a5d8c36382433024efd1f1cc6ba9fd878d28ddc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92329522"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>Zelfstudie: Wijzigingen in virtuele machines bewaken met Azure Event Grid en Logic Apps

Voor het bewaken en reageren op specifieke gebeurtenissen die plaatsvinden in Azure-resources of externe resources, kunt u taken als een werkstroom automatiseren en uitvoeren door een [logische app](../logic-apps/logic-apps-overview.md) te maken die gebruikmaakt van minimale code. Deze resources kunnen gebeurtenissen publiceren naar een [Azure-gebeurtenisraster](../event-grid/overview.md). Het gebeurtenisraster verstuurt die gebeurtenissen vervolgens naar abonnees die webhooks, wachtrijen of [gebeurtenishubs](../event-hubs/event-hubs-about.md) als eindpunt hebben. Als abonnee kan uw logische app wachten op die gebeurtenissen uit het gebeurtenisraster voordat geautomatiseerde werkstromen worden gestart voor het uitvoeren van taken.

Dit zijn enkele voorbeelden van gebeurtenissen die uitgevers naar abonnees kunnen versturen via de service Azure Event Grid:

* Maken, lezen, bijwerken of verwijderen van een resource. U kunt bijvoorbeeld controleren op wijzigingen die extra kosten op uw Azure-abonnement tot gevolg kunnen hebben en dus van invloed zijn op de hoogte van uw factuur.

* Toevoegen of verwijderen van een persoon uit een Azure-abonnement.

* Uitvoeren van een bepaalde actie door uw app.

* Nieuw bericht in een wachtrij.

In deze zelfstudie maakt u een logische app waarmee u controleert op wijzigingen van een virtuele machine en e-mailberichten verstuurt over deze wijzigingen. Wanneer u een logische app maakt met een gebeurtenisabonnement voor een Azure-resource, worden gebeurtenissen vanuit die resource via een gebeurtenisraster naar de logische app geleid. In de zelfstudie gaat u deze logische app bouwen:

![Schermopname van Logic Apps Designer, met de werkstroom voor het controleren van de VM met Event Grid.](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een logische app maken die gebeurtenissen uit een gebeurtenisraster bewaakt.
> * Een voorwaarde toevoegen die specifiek controleert op wijzigingen van de virtuele machine.
> * Een e-mail verzenden wanneer de virtuele machine wordt gewijzigd.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een e-mailaccount van een e-mailprovider die door Logic Apps wordt ondersteund voor het verzenden van meldingen, bijvoorbeeld Office 365 Outlook, Outlook.com of Gmail. Voor andere providers [kunt u hier de lijst met connectors bekijken](/connectors/).

  In deze zelfstudie wordt gebruikgemaakt van een Office 365 Outlook-account. Als u een ander e-mailaccount gebruikt, blijven de algemene stappen gelijk, maar uw gebruikersinterface kan er iets anders uitzien.

  > [!IMPORTANT]
  > Als u de Gmail-connector wilt gebruiken, kunnen alleen bedrijfsaccounts van G Suite deze connector zonder beperking in logische apps gebruiken. Als u een Gmail-consumentenaccount hebt, kunt u deze connector alleen gebruiken met specifieke door Google goedgekeurde services, of u kunt [een Google-client-app maken voor verificatie bij uw Gmail-connector](/connectors/gmail/#authentication-and-bring-your-own-application). Zie [Beleid voor gegevensbeveiliging en privacybeleid voor Google-connectors in Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md) voor meer informatie.

* Een [virtuele machine](https://azure.microsoft.com/services/virtual-machines) die zich in een eigen Azure-resourcegroep bevindt. Als u dit nog niet hebt gedaan, maakt u een virtuele machine aan de hand van de [zelfstudie Een VM maken](../virtual-machines/windows/quick-create-portal.md). U [hoeft verder niets te doen](../event-grid/overview.md) om gebeurtenissen te laten publiceren door de virtuele machine.

## <a name="create-blank-logic-app"></a>Een lege, logische app maken

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Selecteer in het hoofdmenu van Azure **Een resource maken** > **Integratie** > **Logische app**.

   ![Schermopname van de Azure-portal, met de knop voor het maken van een logische app-resource.](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. Geef onder **Logische app** informatie op over de resource van de logische app. Als u gereed bent, selecteert u **Maken**.

   ![Schermopname van het menu voor het maken van logische apps, met details zoals de naam, het abonnement, de resourcegroep en de locatie.](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Naam** | Ja | <*naam-van-logische-app*> | Geef een unieke naam voor de logische app op. |
   | **Abonnement** | Ja | <*Azure-subscription-name*> | Selecteer voor alle services in deze zelfstudie hetzelfde Azure-abonnement. |
   | **Resourcegroep** | Ja | <*Azure-resource-group*> | De naam van de Azure-resourcegroep voor uw logische app. U kunt deze selecteren voor alle services in deze zelfstudie. |
   | **Locatie** | Ja | <*Azure-regio*> | Selecteer dezelfde regio voor alle services in deze tutorial. |
   |||

1. Nadat uw logische app is geïmplementeerd, wordt door de ontwerpfunctie voor logische apps een pagina getoond met een inleidende video en veelgebruikte triggers. Scrol voorbij de video en triggers.

1. Kies onder **Sjablonen** de optie **Lege logische app**.

   ![Schermopname van Logic Apps-sjablonen, met de selectie voor het maken van een lege logische app.](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   De ontwerpfunctie voor logische apps toont nu de [*triggers*](../logic-apps/logic-apps-overview.md#logic-app-concepts) die u kunt gebruiken om de logische app te starten. Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer er aan een bepaalde voorwaarde is voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt Azure Logic Apps een werkstroominstantie waarin uw logische app wordt uitgevoerd.

## <a name="add-an-event-grid-trigger"></a>Event Grid-trigger toevoegen

Voeg nu de Event Grid-trigger toe, die u gebruikt om de resourcegroep voor de virtuele machine te bewaken.

1. Voer in de ontwerpfunctie, in het zoekvenster, `event grid` in als uw filter. Selecteer in de lijst met triggers de trigger **Wanneer een resourcegebeurtenis optreedt**.

   ![Schermopname van Logic Apps Designer, met de selectie van Event Grid-trigger voor een resourcegebeurtenis.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Wanneer dat wordt gevraagd, meldt u zich aan bij Azure Event Grid met de referenties voor uw Azure-account. In de lijst **Tenant**, waarin de Azure Active Directory-tenant wordt weergegeven die aan uw Azure-abonnement is gekoppeld, controleert u of de juiste tenant wordt weergegeven, bijvoorbeeld:

   ![Schermopname van Logic Apps Designer, met de aanmeldingsprompt van Azure om verbinding te maken met Event Grid.](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Als u zich aanmeldt met een persoonlijk Microsoft-account, zoals @outlook.com of @hotmail.com, wordt de trigger voor het gebeurtenisraster mogelijk niet juist weergegeven. Als tijdelijke oplossing kunt u [Verbinding maken met service-principal](../active-directory/develop/howto-create-service-principal-portal.md) selecteren of u laten verifiëren als een lid van de Azure Active Directory die is gekoppeld aan uw Azure-abonnement, bijvoorbeeld *gebruikersnaam*@emailoutlook.onmicrosoft.com.

1. Abonneer de logische app nu op gebeurtenissen van de uitgever. Geef de details van uw gebeurtenisabonnement op, zoals beschreven in de volgende tabel, bijvoorbeeld:

   ![Schermopname van Logic Apps Designer, met de detailseditor voor de trigger voor wanneer er een resourcegebeurtenis plaatsvindt.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   | -------- | -------- | ----- | ----------- |
   | **Abonnement** | Ja | <*event-publisher-Azure-subscription-name*> | Selecteer de naam voor het Azure-abonnement dat is gekoppeld aan de *gebeurtenisuitgever*. Selecteer voor deze zelfstudie de naam van het Azure-abonnement voor uw virtuele machine. |
   | **Resourcetype** | Ja | <*event-publisher-Azure-resource-type*> | Selecteer het Azure-resourcetype voor de gebeurtenisuitgever. Zie [Azure-resourceproviders en -typen](../azure-resource-manager/management/resource-providers-and-types.md) voor meer informatie over Azure-resourcetypen. Voor deze zelfstudie selecteert u de waarde `Microsoft.Resources.ResourceGroups` voor het bewaken van Azure-resourcegroepen. |
   | **Resourcenaam** |  Ja | <*event-publisher-Azure-resource-name*> | Selecteer de naam van de Azure-resource voor de gebeurtenisuitgever. Deze lijst varieert met het resourcetype dat u hebt geselecteerd. Selecteer voor deze zelfstudie de naam van de Azure-resourcegroep dat uw virtuele machine bevat. |
   | **Item van gebeurtenistype** |  Nee | <*event-types*> | Selecteer een of meer specifieke gebeurtenistypen om te filteren en naar uw gebeurtenisraster te verzenden. Eventueel kunt u deze gebeurtenistypen toevoegen om te kunnen detecteren wanneer resources worden gewijzigd of verwijderd: <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>Raadpleeg de volgende onderwerpen voor meer informatie: <p><p>- [Azure Event Grid-gebeurtenisschema voor resourcegroepen](../event-grid/event-schema-resource-groups.md) <br>- [Inzicht in het filteren van gebeurtenissen](../event-grid/event-filtering.md) <br>- [Gebeurtenissen filteren voor Event Grid](../event-grid/how-to-filter-events.md) |
   | Als u nieuwe, optionele eigenschappen wilt toevoegen, selecteert u **Nieuwe parameter toevoegen** en selecteert u vervolgens de gewenste eigenschappen. | Nee | {zie de beschrijvingen} | * **Voorvoegselfilter**: Laat in deze zelfstudie deze eigenschap leeg. Het standaardgedrag komt overeen met alle waarden. U kunt echter een voorvoegseltekenreeks opgeven als een filter, bijvoorbeeld een pad en een parameter voor een specifieke resource. <p>* **Achtervoegselfilter**: Laat in deze zelfstudie deze eigenschap leeg. Het standaardgedrag komt overeen met alle waarden. U kunt echter een achtervoegseltekenreeks opgeven als een filter, bijvoorbeeld een bestandsnaamextensie, als u alleen bepaalde bestandstypen wilt gebruiken. <p>* **Abonnementsnaam**: Voor deze zelfstudie kunt u een unieke naam opgeven voor het gebeurtenisabonnement. |
   |||

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper. Selecteer de titelbalk van de actie om de details van een actie in uw logische app samen te vouwen en te verbergen.

   ![Schermopname van Logic Apps Designer, met de knop Opslaan om werkstroombewerkingen op te slaan.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Wanneer u uw logische app met een trigger voor het gebeurtenisraster opslaat, maakt Azure automatisch een gebeurtenisabonnement voor uw logische app voor de geselecteerde resource. Wanneer de resource nu een gebeurtenis naar het gebeurtenisraster publiceert, wordt die gebeurtenis automatisch door het raster naar de logische app verstuurd. Deze gebeurtenis triggert de logische app, en maakt vervolgens een exemplaar van de werkstroom die u in de volgende stappen definieert. Ten slotte wordt de werkstroom uitgevoerd.

Uw logische app is nu live en luistert naar gebeurtenissen uit het gebeurtenisraster, maar kan verder nog niks totdat u acties aan de werkstroom toevoegt.

## <a name="add-a-condition"></a>Een voorwaarde toevoegen

Als u de logische app alleen wilt laten uitvoeren als er een bepaalde gebeurtenis of bewerking optreedt, voegt u een voorwaarde toe waarmee wordt gecontroleerd op de bewerking `Microsoft.Compute/virtualMachines/write`. Als aan deze voorwaarde wordt voldaan (is Waar), verstuurt de logische app een e-mail met meer gegevens van de bijgewerkte virtuele machine.

1. Selecteer in de ontwerpfunctie van logische apps, onder de trigger voor het gebeurtenisraster, de optie **Nieuwe stap**.

   ![Schermopname van Logic Apps Designer, met de knop voor het toevoegen van een nieuwe stap aan de werkstroom.](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. Voer in het zoekvak onder **Kies een actie** `condition` in als uw filter. Selecteer in de lijst met acties de actie **Voorwaarde**.

   ![Schermopname van Logic Apps Designer, met de knop voor het toevoegen van een voorwaarde voor een actie aan de werkstroom.](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   Er wordt een lege voorwaarde voor uw werkstroom toegevoegd, inclusief de te volgen actiepaden wanneer de voorwaarde Waar of Onwaar is.

   ![Schermopname van Logic Apps Designer, met een lege voorwaarde die is toegevoegd aan de werkstroom.](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Wijzig de titel van de voorwaarde in `If a virtual machine in your resource group has changed`. Selecteer op de titelbalk van de voorwaarde het beletselteken ( **...** ) en selecteer **Naam wijzigen**.

   ![Schermopname van Logic Apps Designer, met het contextmenu van de voorwaarde-editor waarvoor de optie Naam wijzigen is geselecteerd.](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Maak een voorwaarde waarmee de `body` van een gebeurtenis wordt gecontroleerd op een `data`-object waar de eigenschap `operationName` gelijk is aan de bewerking `Microsoft.Compute/virtualMachines/write`. Lees hier meer over het [gebeurtenisschema van Event Grid](../event-grid/event-schema.md).

   1. Klink in het linkervakje in de eerste rij onder **En**. Selecteer in de lijst met dynamische inhoud die wordt weergegeven de optie **Expressie**.

      ![Schermopname van Logic Apps Designer, met een expressie die in de voorwaarde-editor is geselecteerd.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. Voer deze expressie in de expressie-editor in, waarna de naam van de bewerking vanuit de trigger wordt geretourneerd. Selecteer vervolgens **OK**:

      `triggerBody()?['data']['operationName']`

      Bijvoorbeeld:

      ![Schermopname van Logic Apps Designer, met de voorwaarde-editor met de expressie voor het extraheren van de naam van de bewerking.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. In het middelste vakje behoud u **is gelijk aan** voor de operator.

   1. Voer deze waarde in het rechtervak in. Dit is de specifieke bewerking die u bewaakt:

      `Microsoft.Compute/virtualMachines/write`

   De voltooide voorwaarde ziet er nu uit als in dit voorbeeld:

   ![Schermopname van Logic Apps Designer, met een voorwaarde waarmee de bewerking wordt vergeleken.](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Als u van de ontwerpweergave naar de codeweergave schakelt en weer terug naar de ontwerpweergave, wordt de expressie die u in de voorwaarde hebt opgegeven, omgezet naar het token **data.operationName**:

   ![Schermopname van Logic Apps Designer, met een voorwaarde met opgeloste tokens.](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Sla uw logische app op.

## <a name="send-email-notifications"></a>E-mailmeldingen verzenden

U voegt nu een [*actie*](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe, zodat u een e-mail kunt ontvangen wanneer aan de opgegeven voorwaarde wordt voldaan.

1. Selecteer **Een actie toevoegen** in het vak **Indien waar** van de voorwaarde.

   ![Schermopname van de voorwaarde-editor van Logic Apps Designer, met de knop om een actie toe te voegen wanneer de voorwaarde waar is.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. Voer in het zoekvak onder **Kies een actie** `send an email` in als uw filter. Zoek en selecteer de bijbehorende connector op basis van uw e-mailprovider. Selecteer vervolgens de actie 'e-mail verzenden' voor de connector. Bijvoorbeeld:

   * Selecteer voor een werk- of schoolaccount van Azure de Office 365 Outlook-connector.

   * Selecteer voor een persoonlijk Microsoft-account de Outlook.com-connector.

   * Selecteer voor een Gmail-account de Gmail-connector.

   Deze zelfstudie wordt vervolgd met de Office 365 Outlook-connector. Als u een andere provider gebruikt, blijven de stappen gelijk, maar uw gebruikersinterface kan er iets anders uitzien.

   ![Schermopname van Logic Apps Designer, met een zoekopdracht voor de actie Een e-mail verzenden in de Office 365 Outlook-connector.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Als u nog geen verbinding voor uw e-mailprovider hebt, meldt u zich aan bij uw e-mailaccount wanneer er om verificatie wordt gevraagd.

1. Geef de actie E-mail verzenden de volgende titel: `Send email when virtual machine updated`

1. Geef informatie op voor het e-mailbericht, zoals aangegeven in de volgende tabel:

   ![Schermopname van Logic Apps Designer, met dynamische inhoud die wordt toegevoegd aan de onderwerpregel van een e-mail voor een voorwaarde die waar is.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Als u uitvoer van de vorige stappen in uw werkstroom wilt selecteren, klikt u in een bewerkingsvak, zodat de lijst met dynamische inhoud wordt weergegeven. U kunt ook **Dynamische inhoud toevoegen** selecteren. Als u meer resultaten wilt zien, selecteert u **Meer weergeven** voor elke sectie in de lijst. U kunt de lijst met dynamische inhoud sluiten door opnieuw **Dynamische inhoud toevoegen** te selecteren.

   | Eigenschap | Vereist | Waarde | Beschrijving |
   | -------- | -------- | ----- | ----------- |
   | **Aan** | Ja | <*recipient\@domain*> | Voer het e-mailadres van de ontvanger in. Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken. |
   | **Onderwerp** | Ja | `Resource updated:` **Onderwerp** | Voer de inhoud van het onderwerp van de e-mail in. Voor deze zelfstudie voert u de opgegeven tekst in en selecteert u het veld **Onderwerp** van de gebeurtenis. Hier bevat het onderwerp van de e-mail de naam voor de bijgewerkte resource (virtuele machine). |
   | **Hoofdtekst** | Ja | `Resource:` **Onderwerp** <p>`Event type:` **Gebeurtenistype**<p>`Event ID:` **Id**<p>`Time:` **Tijdstip van gebeurtenis** | Voer de inhoud voor de hoofdtekst van de e-mail in. Voor deze zelfstudie voert u de opgegeven tekst in en selecteert u de velden **Onderwerp**, **Gebeurtenistype**, **Id** en **Tijdstip van gebeurtenis** van de gebeurtenis, zodat uw e-mailbericht de resource bevat waarmee de gebeurtenis is geactiveerd, evenals het gebeurtenistype, het tijdstempel van de gebeurtenis en de gebeurtenis-id voor de update. Voor deze zelfstudie is de resource de Azure-resourcegroep die in de trigger is geselecteerd. <p>U kunt lege regels toevoegen aan de inhoud door op Shift+Enter te drukken. |
   ||||

   > [!NOTE]
   > Als u een veld selecteert dat een matrix vertegenwoordigt, wordt in de ontwerpfunctie automatisch een lus **Voor elke** toegevoegd rond de actie die naar de matrix verwijst. Op die manier wordt de actie uitgevoerd voor elk matrixitem.

   De e-mailactie ziet er nu ongeveer uit zoals in dit voorbeeld:

   ![Schermopname van Logic Apps Designer, met de geselecteerde uitvoer die in een e-mail moet worden verzonden wanneer de VM wordt bijgewerkt.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   De voltooide logische app ziet er dan ongeveer zo uit:

   ![Schermopname van Logic Apps Designer, met de gemaakte logische app met detailgegevens van triggers en acties.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Sla uw logische app op. Selecteer de titelbalk van de actie om de details van een actie in uw logische app samen te vouwen en te verbergen.

   Uw logische app is nu live, maar er wordt gewacht op wijzigingen van de virtuele machine voordat er iets gebeurt. Ga door naar de volgende sectie als u de logische app nu wilt testen.

## <a name="test-your-logic-app-workflow"></a>De werkstroom van uw logische app testen

1. Om te controleren of uw logische app de opgegeven gebeurtenissen ontvangt, moet u de virtuele machine bijwerken.

   U kunt bijvoorbeeld de grootte van de virtuele machine aanpassen in Azure Portal of [met Azure PowerShell](../virtual-machines/windows/resize-vm.md).

   Binnen enkele ogenblikken moet u dan een e-mailbericht krijgen. Bijvoorbeeld:

   ![Schermopname van voorbeeld-e-mail in Outlook, met detailgegevens van de update van de VM.](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Als u de uitvoerings- en triggergeschiedenis van de logische app wilt bekijken, selecteert u **Overzicht** in het menu van de logische app. Als u meer details over een run wilt bekijken, selecteert u de rij voor die run.

   ![Schermopname van de overzichtspagina van de logische app, met een geslaagde uitvoering geselecteerd.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

1. Als u de in- en uitvoer van elke stap wilt bekijken, vouwt u de stap uit die u wilt bekijken. Aan de hand van deze gegevens kunt u problemen met de logische app vaststellen en oplossen.

   ![Schermopname van de uitvoeringsgeschiedenis van de logische app, met detailgegevens van elke uitvoering.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

U hebt nu een logische app gemaakt en uitgevoerd die resourcegebeurtenissen controleert met behulp van een gebeurtenisraster en u een e-mailbericht stuurt wanneer deze gebeurtenissen optreden. Daarnaast hebt u geleerd hoe eenvoudig het is om werkstromen te maken voor het automatiseren van processen en hoe u systemen en cloudservices kunt integreren.

U kunt andere configuratiewijzigingen bewaken met gebeurtenisrasters en logische apps, zoals:

* Een virtuele machine krijgt rechten via Azure-toegangsbeheer op basis van rollen (Azure RBAC).
* Een netwerkbeveiligingsgroep (NSG) op een netwerkinterface (NIC) wordt gewijzigd.
* Schijven voor een virtuele machine worden toegevoegd of verwijderd.
* Er wordt een openbaar IP-adres toegewezen aan de NIC van een virtuele machine.

## <a name="clean-up-resources"></a>Resources opschonen

In deze zelfstudie worden resources gebruikt en acties uitgevoerd die kosten voor uw Azure-abonnement met zich mee kunnen brengen. Als u klaar bent met de zelfstudie en het testen, denkt u er dan aan dat u resources waarvoor u geen kosten wilt maken, uitschakelt of verwijdert.

* Als u uw logische app niet meer wilt uitvoeren, maar uw werk niet wilt verwijderen, schakelt u uw app uit. Selecteer **Overzicht** in het menu van de logische app. Selecteer **Uitschakelen** op de werkbalk.

  ![Schermopname van het overzicht van een logische app, waarin de knop Uitschakelen is geselecteerd om de logische app uit te schakelen.](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Als u het menu van de logische app niet ziet, probeer dan terug te gaan naar het Azure-dashboard. Open uw logische app dan opnieuw.

* Als u de logische app permanent wilt verwijderen, selecteert u **Overzicht** in het menu van de logische app. Selecteer **Verwijderen** op de werkbalk. Bevestig dat u uw logische app wilt verwijderen en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

* [Aangepaste gebeurtenissen maken en routeren met behulp van Event Grid](../event-grid/custom-event-quickstart.md)
