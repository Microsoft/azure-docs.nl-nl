---
title: Taken automatiseren met meerdere Azure-services
description: 'Zelfstudie: maak geautomatiseerde werkstromen om e-mails te verwerken met Azure Logic Apps, Azure Storage en Azure Functions'
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 02/27/2020
ms.openlocfilehash: bd1715dc0a3767bc5826154616bbdc97c7b61dd3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576360"
---
# <a name="tutorial-automate-tasks-to-process-emails-by-using-azure-logic-apps-azure-functions-and-azure-storage"></a>Zelfstudie: Automatiseer taken om e-mails te verwerken met behulp van Azure Logic Apps, Azure Functions en Azure Storage

Azure Logic Apps helpt u om uw werkstromen te automatiseren en om gegevens te integreren in Azure-services, Microsoft-services, andere SaaS-apps (software als een service) en on-premises systemen. In deze zelfstudie leert u hoe u een [logische app](../logic-apps/logic-apps-overview.md) bouwt die binnenkomende e-mails en eventuele bijlagen verwerkt. Deze logische app analyseert de inhoud van de e-mails, bewaart de inhoud in een Azure-opslag en verzendt een melding om die inhoud te bekijken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een [Azure-opslag](../storage/common/storage-introduction.md) en Storage Explorer instellen om opgeslagen e-mails en bijlagen te doorzoeken.
> * Maak een [Azure-functie](../azure-functions/functions-overview.md) waarmee HTML uit e-mailberichten wordt verwijderd. Deze zelfstudie bevat de code die u voor deze functie kunt gebruiken.
> * Een lege, logische app maken.
> * Voeg een trigger toe waarmee e-mailberichten op bijlagen worden gecontroleerd.
> * Voeg een voorwaarde toe waarmee wordt gecontroleerd of e-mails bijlagen bevatten.
> * Voeg een actie toe waarmee de Azure-functie wordt aangeroepen als een e-mail bijlagen bevat.
> * Voeg een actie toe waarmee opslagblobs voor e-mails en bijlagen worden gemaakt.
> * Voeg een actie toe waarmee e-mailmeldingen worden verzonden.

Wanneer u bent klaar, ziet uw logische app eruit als deze werkstroom op hoog niveau:

![Een voltooide logische app op hoog niveau](./media/tutorial-process-email-attachments-workflow/overview.png)

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een e-mailaccount van een e-mailprovider die door Logic Apps wordt ondersteund, bijvoorbeeld Office 365 Outlook, Outlook.com of Gmail. Voor andere providers [kunt u hier de lijst met connectors bekijken](/connectors/).

  In deze logische app wordt gebruikgemaakt van een werk- of schoolaccount. Als u een ander e-mailaccount gebruikt, blijven de algemene stappen gelijk, maar uw gebruikersinterface kan er iets anders uitzien.

  > [!IMPORTANT]
  > Als u de Gmail-connector wilt gebruiken, kunnen alleen bedrijfsaccounts van G Suite deze connector zonder beperking in logische apps gebruiken. Als u een Gmail-consumentenaccount hebt, kunt u deze connector alleen gebruiken met specifieke door Google goedgekeurde services, of u kunt [een Google-client-app maken voor verificatie bij uw Gmail-connector](/connectors/gmail/#authentication-and-bring-your-own-application). Zie [Beleid voor gegevensbeveiliging en privacybeleid voor Google-connectors in Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md) voor meer informatie.

* Download en installeer de [gratis Microsoft Azure Storage Explorer](https://storageexplorer.com/). Dit hulpprogramma help u om te controleren of uw opslagcontainer correct is ingesteld.

* Als uw logische app moet communiceren via een firewall die het verkeer beperkt tot specifieke IP-adressen, moet die firewall toegang toestaan voor *zowel* de [inkomende](logic-apps-limits-and-config.md#inbound) als [uitgaande](logic-apps-limits-and-config.md#outbound) IP-adressen die worden gebruikt door de Logic apps service of runtime in de Azure-regio waar uw logische app bestaat. Als uw logische app ook [beheerde connectors](../connectors/apis-list.md#managed-api-connectors)gebruikt, zoals de Office 365 Outlook-Connector of de SQL-connector, of [aangepaste connectors](/connectors/custom-connectors/)gebruikt, moet de firewall ook toegang toestaan voor *alle* [uitgaande IP-adressen van beheerde connectors](logic-apps-limits-and-config.md#outbound) in de Azure-regio van de logische app.

## <a name="set-up-storage-to-save-attachments"></a>De opslag instellen om bijlagen te bewaren

U kunt binnenkomende e-mails en bijlagen als blobs opslaan in een [Azure-opslagcontainer](../storage/common/storage-introduction.md).

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Voordat u een opslagcontainer kunt maken, [maakt u een opslagaccount](../storage/common/storage-account-create.md) met de volgende instellingen op het tabblad **Basis** in Azure Portal:

   | Instelling | Waarde | Beschrijving |
   |---------|-------|-------------|
   | **Abonnement** | <*Azure-subscription-name*> | De naam van uw Azure-abonnement |  
   | **Resourcegroep** | <*Azure-resource-group*> | De naam van de [Azure-resourcegroep](../azure-resource-manager/management/overview.md) die wordt gebruikt om verwante resources te organiseren en te beheren. In dit voorbeeld wordt LA-Tutorial-RG gebruikt. <p>**Opmerking:** er is een resourcegroep aanwezig binnen een bepaalde regio. Hoewel de items in deze zelfstudie mogelijk niet in alle regio's beschikbaar zijn, dient u, wanneer mogelijk, dezelfde regio te gebruiken. |
   | **Naam van opslagaccount** | <*Azure-storage-account-name*> | De naam van het opslagaccount moet 3-24 tekens bevatten en mag alleen uit kleine letters en cijfers bestaan. In dit voorbeeld wordt attachmentstorageacct gebruikt. |
   | **Locatie** | <*Azure-regio*> | De regio waar u informatie over uw opslagaccount opslaat. In dit voorbeeld wordt 'US - west' gebruikt. |
   | **Prestaties** | Standard | Deze instelling bepaalt de gegevenstypen die worden ondersteund en de media die moeten worden opgeslagen. Zie [Typen opslagaccounts](../storage/common/storage-introduction.md#types-of-storage-accounts). |
   | **Type account** | Algemeen doel | Het [type opslagaccount](../storage/common/storage-introduction.md#types-of-storage-accounts) |
   | **Replicatie** | Lokaal redundante opslag (LRS) | Deze instelling bepaalt hoe uw gegevens worden gekopieerd, opgeslagen, beheerd en gesynchroniseerd. Zie [Lokaal redundante opslag (LRS): Gegevensredundantie met lage kosten voor Azure Storage](../storage/common/storage-redundancy.md). |
   | **Opslaglaag (standaard)** | Behoud de huidige instelling. |
   ||||

   Selecteer op het tabblad **Geavanceerd** deze instelling:

   | Instelling | Waarde | Beschrijving |
   |---------|-------|-------------|
   | **Veilige overdracht vereist** | Uitgeschakeld | Deze instelling bepaalt de beveiliging die nodig is voor het aanvragen van verbindingen. Zie [Require secure transfer](../storage/common/storage-require-secure-transfer.md) (veilige overdracht vereist). |
   ||||

   U kunt ook [Azure PowerShell](../storage/common/storage-account-create.md?tabs=powershell) of [Azure CLI](../storage/common/storage-account-create.md?tabs=azure-cli) gebruiken om uw opslagaccount te maken.

1. Selecteer als u klaar bent de optie **Beoordelen en maken**.

1. Nadat Azure uw opslagaccount heeft geïmplementeerd, gaat u naar uw opslagaccount en haalt u de toegangssleutel van het opslagaccount op:

   1. In het menu van uw opslagaccount selecteert u onder het kopje **Instellingen** **Toegangssleutels**.

   1. Kopieer de naam van uw opslagaccount en **key1** en bewaar deze waarden op een veilige plek.

      ![Kopieer de naam en de sleutel van de opslagaccount en sla deze op](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   U kunt ook [Azure PowerShell](/powershell/module/az.storage/get-azstorageaccountkey) of [Azure CLI](/cli/azure/storage/account/keys) gebruiken om de toegangssleutel van uw opslagaccount op te halen.

1. Maak een Blob Storage-container voor uw e-mailbijlagen.

   1. Selecteer **Overzicht** in uw opslagaccountmenu. Selecteer in het deelvenster Overzicht de optie **Containers**.

      ![Blob Storage-container toevoegen](./media/tutorial-process-email-attachments-workflow/create-storage-container.png)

   1. Nadat de pagina **Containers** opent in de werkbalk, selecteert u **Container**.

   1. Voer onder **Nieuwe container** `attachments` in als containernaam. Selecteer onder **Niveau openbare toegang** **Container (anonieme leestoegang voor containers en blobs)**  > **OK**.

      Wanneer u klaar bent, vindt u uw opslagcontainer in uw opslagaccount hier in Azure Portal:

      ![Voltooide opslagcontainer](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

   U kunt ook [Azure PowerShell](/powershell/module/az.storage/new-azstoragecontainer) of [Azure CLI](/cli/azure/storage/container#az-storage-container-create) gebruiken om een opslagcontainer te maken.

Koppel vervolgens Storage Explorer aan uw opslagaccount.

## <a name="set-up-storage-explorer"></a>Storage Explorer instellen

Koppel nu Storage Explorer aan uw opslagaccount, zodat u kunt bevestigen dat uw logische app bijlagen correct als blobs kan opslaan in uw opslagcontainer.

1. Start Microsoft Azure Storage Explorer.

   Storage Explorer vraagt u om een verbinding met uw opslagaccount.

1. Selecteer in het deelvenster **Verbinding maken met Azure Storage** de optie **Een opslagaccountnaam en -sleutel gebruiken** > **Volgende**.

   ![Storage Explorer - verbinding maken met opslagaccount](./media/tutorial-process-email-attachments-workflow/storage-explorer-choose-storage-account.png)

   > [!TIP]
   > Als u niets wordt gevraagd, selecteert u **Account toevoegen** op de werkbalk van Storage Explorer.

1. Geef onder **Weergavenaam** een beschrijvende naam op voor uw verbinding. Onder **Accountnaam** geeft u de naam op van uw opslagaccount. Onder **Accountsleutel** geeft u de toegangssleutel op die u eerder hebt opgeslagen en selecteer **Volgende**.

1. Bevestig uw verbindingsinformatie en selecteer **Verbinding maken**.

   Storage Explorer maakt de verbinding en toont uw opslagaccount in het Explorer-venster onder **(Lokaal en gekoppeld)**  > **Opslagaccounts**.

1. U vindt uw Blob Storage-container onder **Opslagaccounts** waar u uw opslagaccount uitvouwt (in dit geval **attachmentstorageacct**). Vervolgens vouwt u **Blob-containers** uit waar u de container **bijlagen** vindt, bijvoorbeeld:

   ![Storage Explorer - opslagcontainer zoeken](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

Maak vervolgens een [Azure-functie](../azure-functions/functions-overview.md) waarmee HTML uit binnenkomende e-mailberichten wordt verwijderd.

## <a name="create-function-to-clean-html"></a>Een functie maken om HTML te verwijderen

Gebruik nu het codefragment in deze stappen om een Azure-functie te maken waarmee HTML uit binnenkomende e-mailberichten wordt verwijderd. Op die manier is de inhoud van de e-mail schoner en eenvoudiger te verwerken. U kunt deze functie dan aanroepen vanaf uw logische app.

1. Voordat u deze functie kunt maken, [maakt u een functie-app](../azure-functions/functions-create-function-app-portal.md) met de volgende instellingen:

   | Instelling | Waarde | Beschrijving |
   | ------- | ----- | ----------- |
   | **Naam van app** | <*function-app-name*> | De naam van uw functie-app, die uniek moet zijn in Azure. Dit voorbeeld gebruikt al CleanTextFunctionApp, dus geef een andere naam op, bijvoorbeeld MyCleanTextFunctionApp-<*uw-naam*>. |
   | **Abonnement** | <*your-Azure-subscription-name*> | Hetzelfde Azure-abonnement dat u eerder hebt gebruikt |
   | **Resourcegroep** | LA-Tutorial-RG | Dezelfde Azure-resourcegroep die u eerder hebt gebruikt |
   | **Besturingssysteem** | <*uw-besturingssysteem*> | Selecteer het besturingssysteem dat uw favoriete functieprogrammeertaal ondersteunt. Voor dit voorbeeld selecteert u **Windows**. |
   | **Hostingabonnement** | Verbruiksabonnement | Deze instelling bepaalt hoe de resources worden toegewezen en geschaald, bijvoorbeeld de rekenkracht, om uw functie-app uit te voeren. Bekijk [Vergelijking van hostingabonnementen](../azure-functions/functions-scale.md). |
   | **Locatie** | VS - west | Dezelfde regio die u eerder hebt gebruikt |
   | **Runtimestack** | Voorkeurstaal | Selecteer een runtime die uw favoriete functieprogrammeertaal ondersteunt. Selecteer **.NET** voor C#- en F#-functies. |
   | **Storage** | cleantextfunctionstorageacct | Maak een opslagaccount voor uw functie-app. Gebruik alleen kleine letters en cijfers. <p>**Opmerking:** Dit opslagaccount bevat uw functie-apps en verschilt van uw eerder gemaakte opslagaccount voor e-mailbijlagen. |
   | **Application Insights** | Uitschakelen | Hiermee schakelt u toepassingsbewaking met [Application Insights](../azure-monitor/app/app-insights-overview.md) in, maar selecteer voor deze zelfstudie de optie **Uitschakelen** > **Toepassen**. |
   ||||

   Als uw functie-app na de implementatie niet automatisch opent, zoekt u via het zoekvak van [Azure Portal](https://portal.azure.com) **Functie-app** en selecteert u deze. Selecteer onder **Functie-app** uw functie-app.

   ![Functie-app selecteren](./media/tutorial-process-email-attachments-workflow/select-function-app.png)

   Anders opent Azure automatisch uw functie-app, zoals hier wordt weergegeven:

   ![Gemaakte functie-app](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   U kunt ook [Azure CLI](../azure-functions/create-first-function-cli-csharp.md) of [PowerShell- en Resource Manager-sjablonen](../azure-resource-manager/templates/deploy-powershell.md) gebruiken om een functie-app te maken.

1. Vouw in de lijst **Functie-apps** uw functie-app uit, als deze nog niet is uitgevouwen. Selecteer onder uw functie-app de optie **Functies**. Selecteer op de functiewerkbalk **Nieuwe functie**.

   ![Nieuwe functie maken](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

1. Selecteer in het menu **Kies hieronder een sjabloon of ga naar de quickstart** het sjabloon **HTTP-trigger**.

   ![Selecteer de sjabloon voor de HTTP-trigger](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

   Azure maakt een functie met een taalspecifieke sjabloon voor een door HTTP getriggerde functie.

1. In het deelvenster **Nieuwe functie** voert u `RemoveHTMLFunction` in onder **Naam**. Houd het **Autorisatieniveau** ingesteld op **Functie** en selecteer **Maken**.

   ![Een naam voor de functie opgeven](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

1. Nadat de editor wordt geopend, vervangt u de sjablooncode door deze voorbeeldcode, waarmee de HTML wordt gewist en de resultaten worden geretourneerd aan de aanroeper:

   ```csharp
   #r "Newtonsoft.Json"

   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   using Newtonsoft.Json;
   using System.Text.RegularExpressions;

   public static async Task<IActionResult> Run(HttpRequest req, ILogger log) {

      log.LogInformation("HttpWebhook triggered");

      // Parse query parameter
      string emailBodyContent = await new StreamReader(req.Body).ReadToEndAsync();

      // Replace HTML with other characters
      string updatedBody = Regex.Replace(emailBodyContent, "<.*?>", string.Empty);
      updatedBody = updatedBody.Replace("\\r\\n", " ");
      updatedBody = updatedBody.Replace(@"&nbsp;", " ");

      // Return cleaned text
      return (ActionResult)new OkObjectResult(new { updatedBody });
   }
   ```

1. Selecteer **Opslaan** als u klaar bent. Als u uw functie wilt testen, selecteert u **Testen** onder het pijlpictogram ( **<** ) aan de rechterkant van de editor.

   ![Open het testpaneel](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

1. Voer in het deelvenster **Testen** onder de kop **Aanvraagtekst** deze regel in en selecteer **Uitvoeren**.

   `{"name": "<p><p>Testing my function</br></p></p>"}`

   ![Uw functie testen](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   In het venster **Uitvoer** wordt het resultaat van de functie weergegeven:

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

Nadat u hebt gecontroleerd of uw functie werkt, maakt u uw logische app. In deze zelfstudie wordt laten zien hoe u een functie maakt die HTML uit e-mails wist, maar Logic Apps biedt ook een connector waarmee **HTML in tekst** wordt omgezet.

## <a name="create-your-logic-app"></a>Uw logische app maken

1. Voer in het bovenste zoekvak van Azure `logic apps` in en selecteer **Logische apps**.

   ![Zoek en selecteer 'Logic Apps'](./media/tutorial-process-email-attachments-workflow/find-select-logic-apps.png)

1. Selecteer in het deelvenster **Logische apps** de optie **Toevoegen**.

   ![Nieuwe logische app toevoegen](./media/tutorial-process-email-attachments-workflow/add-new-logic-app.png)

1. Voer in het deelvenster **Logische app** informatie in over uw logische app zoals hier afgebeeld. Selecteer als u klaar bent de optie **Beoordelen en maken**.

   ![Informatie over logische app opgeven](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | Instelling | Waarde | Beschrijving |
   | ------- | ----- | ----------- |
   | **Abonnement** | <*your-Azure-subscription-name*> | Hetzelfde Azure-abonnement dat u eerder hebt gebruikt |
   | **Resourcegroep** | LA-Tutorial-RG | Dezelfde Azure-resourcegroep die u eerder hebt gebruikt |
   | **Naam van logische app** | LA-ProcessAttachment | De naam voor uw logische app |
   | **Selecteer de locatie** | VS - west | Dezelfde regio die u eerder hebt gebruikt |
   | **Log Analytics** | Uit | Selecteer voor deze zelfstudie de instelling **Uit**. |
   ||||

1. Nadat Azure uw app heeft geïmplementeerd, selecteert u op de werkbalk van Azure het meldingspictogram en selecteert u **Ga naar resource**.

   ![Selecteer in de lijst met meldingen van Azure de optie Ga naar resource](./media/tutorial-process-email-attachments-workflow/go-to-new-logic-app-resource.png)

1. Nadat de ontwerpfunctie van logische apps is geopend, ziet u een pagina met een inleidende video en sjablonen voor veelgebruikte patronen voor logische apps. Kies onder **Sjablonen** de optie **Lege logische app**.

   ![Selecteer een leeg sjabloon voor uw logische app](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

Voeg vervolgens een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die binnenkomende e-mails met bijlagen afwacht. Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer nieuwe gegevens voldoen aan een bepaalde voorwaarde. Bekijk [Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md) voor meer informatie.

## <a name="monitor-incoming-email"></a>Binnenkomende e-mail controleren

1. Voer in de ontwerpfunctie in het zoekvenster `when new email arrives` in als uw filter. Selecteer deze trigger voor uw e-mailprovider: **Wanneer een nieuwe e-mail binnenkomt - <*uw-e-mailprovider*>**

   Bijvoorbeeld:

   ![Selecteer deze trigger voor de e-mailprovider: Wanneer een nieuwe e-mail binnenkomt](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * Voor werk- of schoolaccounts van Azure selecteert u Outlook van Office 365.

   * Selecteer Outlook.com voor persoonlijke Microsoft-accounts.

1. Als u om uw referenties wordt gevraagd, meldt u zich aan bij uw e-mailaccount, zodat Logic Apps verbinding met uw e-mailaccount kan maken.

1. Geef nu de criteria op die de trigger gebruikt om nieuwe e-mails te filteren.

   1. Geef de hieronder beschreven instellingen op voor het controleren van e-mails.

      ![Geef map, interval en frequentie voor het controleren van e-mails op](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | Instelling | Waarde | Beschrijving |
      | ------- | ----- | ----------- |
      | **Map** | Postvak IN | De te controleren e-mailmap |
      | **Heeft bijlage** | Ja | Ontvang alleen e-mails met bijlagen. <p>**Opmerking:** de trigger verwijdert geen e-mails van uw account, maar controleert alleen op nieuwe berichten en verwerkt alleen e-mails die overeenkomen met het onderwerpfilter. |
      | **Bijlagen opnemen** | Ja | Haalt de bijlagen op als invoer voor uw werkstroom, in plaats van dat er alleen wordt gecontroleerd op bijlagen. |
      | **Interval** | 1 | Het aantal intervallen dat tussen controles moet worden gewacht |
      | **Frequentie** | Minuut | De tijdseenheid voor elk interval tussen controles |
      ||||

   1. Selecteer in de lijst **Nieuwe parameter toevoegen** de optie **Onderwerpfilter**.

   1. Wanneer het vak **Onderwerpfilter** wordt weergegeven in de actie, geeft u het onderwerp op zoals hier wordt weergegeven:

      | Instelling | Waarde | Beschrijving |
      | ------- | ----- | ----------- |
      | **Onderwerpfilter** | `Business Analyst 2 #423501` | De tekst die moet worden gezocht in het onderwerp van de e-mail |
      ||||

1. Als u de details van de trigger voorlopig wilt verbergen, klikt u in de titelbalk van de trigger.

   ![Shape samenvouwen om details te verbergen](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

   Uw logische app is nu live, maar kan niets anders doen dan uw e-mails controleren. Voeg vervolgens een voorwaarde toe waarmee criteria worden opgegeven, zodat de werkstroom door blijft gaan.

## <a name="check-for-attachments"></a>Controleren op bijlagen

Voeg nu een voorwaarde toe waarmee er alleen e-mails met bijlagen worden geselecteerd.

1. Selecteer onder de trigger de optie **Nieuwe stap**.

   ![Nieuwe stap](./media/tutorial-process-email-attachments-workflow/add-condition-under-trigger.png)

1. Voer in het zoekvak onder **Kies een actie** `condition` in. Selecteer deze actie: **Voorwaarde**

   ![Selecteer Voorwaarde](./media/tutorial-process-email-attachments-workflow/select-condition.png)

   1. Geef de voorwaarde een naam met een betere beschrijving. Selecteer op de titelbalk van de voorwaarde het beletselteken ( **...** ) > **Naam wijzigen**.

      ![Naam voorwaarde wijzigen](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

   1. Verander de naam van uw voorwaarde in deze beschrijving: `If email has attachments and key subject phrase`

1. Voer een voorwaarde in waarmee er op e-mails met bijlagen wordt gecontroleerd.

   1. Klink in het linkervakje in de eerste rij onder **En**. Selecteer vanuit de lijst met dynamische inhoud de eigenschap **Heeft bijlage**.

      ![Schermopname van de eigenschap 'And' als voorwaarde en de eigenschap 'Heeft bijlage' geselecteerd.](./media/tutorial-process-email-attachments-workflow/build-condition.png)

   1. In het middelste vakje behoud u **is gelijk aan** voor de operator.

   1. Voer in het rechtervakje **waar** in als waarde, zodat de eigenschapswaarde **Heeft bijlage** wordt vergeleken met de trigger.

      ![Voorwaarde bouwen](./media/tutorial-process-email-attachments-workflow/finished-condition.png)

      Als beide waarden gelijk zijn, bevat de e-mail ten minste één bijlage, wordt er aan de voorwaarde voldaan en gaat de werkstroom verder.

   In de onderliggende definitie van uw logische app, die u in het venster Code bewerken kunt bekijken, ziet de waarde eruit als in het volgende voorbeeld:

   ```json
   "Condition": {
      "actions": { <actions-to-run-when-condition-passes> },
      "expression": {
         "and": [ {
            "equals": [
               "@triggerBody()?['HasAttachment']",
                 "true"
            ]
         } ]
      },
      "runAfter": {},
      "type": "If"
   }
   ```

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

### <a name="test-your-condition"></a>Uw verbinding testen

Test nu of de voorwaarde correct werkt:

1. Als uw logische app nog niet wordt uitgevoerd, selecteert u **Uitvoeren** in de werkbalk van de ontwerpfunctie.

   Met deze stap wordt u logische app handmatig gestart zonder dat u hoeft te wachten tot de door u opgegeven interval is verstreken. Er gebeurt echter niets totdat het test-e-mailbericht in uw postvak binnenkomt.

1. Verstuur een e-mail naar uzelf die aan de volgende criteria voldoet:

   * Het onderwerp van de e-mail bevat de tekst die u hebt opgegeven in het **Onderwerpfilter** van de trigger: `Business Analyst 2 #423501`

   * Uw e-mailbericht heeft één bijlage. Maak voor nu een leeg tekstbestand en voeg dat als bijlage toe aan uw e-mail.

   Wanneer de e-mail binnenkomt, controleert uw logische app op bijlagen en op de opgegeven onderwerptekst. Als er aan de voorwaarde wordt voldaan, wordt de trigger geactiveerd en maakt de Logic Apps-engine een exemplaar van een logische app en wordt de werkstroom in gang gezet.

1. Als u wilt controleren of de trigger is geactiveerd en de logische app succesvol is uitgevoerd, selecteert u **Overzicht** in het menu van de logische app.

   ![Controleer trigger en uitvoergeschiedenis](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   Bekijk [Troubleshoot your logic app](../logic-apps/logic-apps-diagnosing-failures.md) (Problemen met uw logische app oplossen) als uw logische app niet is geactiveerd of uitgevoerd, hoewel de trigger wel succesvol is geactiveerd.

Bepaal vervolgens de te nemen acties voor de vertakking **Indien waar**. Als u uw e-mail met eventuele bijlagen wilt opslaan, wist u de HTML uit de hoofdtekst van de e-mail en maakt u blobs in de opslagcontainer voor de e-mail en de bijlagen.

> [!NOTE]
> Uw logische app hoeft niets te doen voor de vertakking **Indien onwaar** als een e-mail geen bijlagen heeft.
> Als extra oefening na deze zelfstudie, kunt u een geschikte actie toevoegen voor de vertakking **Indien onwaar**.

## <a name="call-removehtmlfunction"></a>RemoveHTMLFunction aanroepen

Met deze stap wordt uw eerder gemaakte Azure-functie toegevoegd aan uw logische app en wordt de tekstinhoud van de e-mail overgezet van de trigger naar uw functie.

1. Selecteer in het menu van de logische app **Logic App Designer**. Selecteer in de vertakking **Indien waar** de optie **Een actie toevoegen**.

   ![In vertakking 'indien waar', actie toevoegen](./media/tutorial-process-email-attachments-workflow/if-true-add-action.png)

1. Zoek 'Azure-functies' in het zoekvak en selecteer deze actie: **Een Azure-functie kiezen - Azure Functions**

   ![Selecteer actie voor 'Een Azure-functie kiezen'](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

1. Selecteer uw eerder gemaakte functie-app, die `CleanTextFunctionApp` in dit voorbeeld is:

   ![Selecteer uw Azure-functie-app](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

1. Selecteer nu uw functie: **RemoveHTMLFunction**

   ![Selecteer uw Azure-functie](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

1. Verander de naam van de functievorm in deze beschrijving: `Call RemoveHTMLFunction to clean email body`

1. Geef nu de invoer op die uw functie moet verwerken.

   1. Voer bij **Aanvraagtekst** deze tekst in met een spatie aan het einde:

      `{ "emailBody":`

      Terwijl u in de volgende stappen aan deze invoer werkt, verschijnt er een fout over ongeldige JSON, totdat uw invoer correct is geformatteerd als JSON. Wanneer u deze functie eerder hebt getest, gebruikte de voor deze functie opgegeven invoer JavaScript Object Notation (JSON). Diezelfde taal moet daarom ook door de aanvraagtekst worden gebruikt.

      Wanneer de cursor zich binnen het venster **Aanvraagtekst** bevindt, verschijnt de lijst met dynamische inhoud, zodat u beschikbare eigenschapswaarden van vorige acties kunt selecteren.

   1. Uit de lijst met dynamische inhoud, onder **Wanneer er een nieuwe e-mail binnenkomt**, selecteert u de eigenschap **Hoofdtekst**. Vergeet niet om na deze eigenschap de sluitende accolade toe te voegen: `}`

      ![Geef de aanvraagtekst op die aan de functie wordt doorgegeven](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   Wanneer u klaar bent, ziet de invoer voor uw functie eruit als in dit voorbeeld:

   ![Afgemaakte aanvraagtekst om door te geven aan uw functie](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing-2.png)

1. Sla uw logische app op.

Voeg vervolgens een actie toe waarmee er een blob wordt gemaakt in uw opslagcontainer, zodat u de hoofdtekst van de e-mail kunt opslaan.

## <a name="create-blob-for-email-body"></a>Een blob maken voor de hoofdtekst van de e-mail

1. Selecteer in het vak **Indien waar** en onder uw Azure-functie de optie **Een actie toevoegen**.

1. Voer in het zoekvenster `create blob` in als uw filter en selecteer deze actie: **Blob maken**

   ![Voeg de actie toe om een blob voor de hoofdtekst van een e-mail te maken](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

1. Maak verbinding met uw opslagaccount met de instellingen die hier worden weergegeven en beschreven. Als u gereed bent, selecteert u **Maken**.

   ![Maak verbinding met opslagaccount](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | Instelling | Waarde | Beschrijving |
   | ------- | ----- | ----------- |
   | **Verbindingsnaam** | AttachmentStorageConnection | Een beschrijvende naam voor de verbinding |
   | **Opslagaccount** | attachmentstorageacct | De naam voor de opslagaccount die u eerder hebt gemaakt om bijlagen op te slaan |
   ||||

1. Verander de naam van de actie **Blob maken** in deze beschrijving: `Create blob for email body`

1. Geef in de actie **Blob maken** de volgende informatie op en selecteer de volgende velden om de blob te maken zoals wordt weergegeven en beschreven:

   ![Blobinformatie opgeven voor hoofdtekst van de e-mail](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | Instelling | Waarde | Beschrijving |
   | ------- | ----- | ----------- |
   | **Mappad** | /attachments | Het pad en de naam van de container die u eerder hebt gemaakt. Voor dit voorbeeld klikt u op het mappictogram en selecteert u de container '/bijlagen'. |
   | **Blobnaam** | Veld **Van** | Voor dit voorbeeld gebruikt u de naam van de verzender als de naam van de blob. Klik binnen dit venster, zodat de lijst met dynamische inhoud verschijnt, en selecteer vervolgens het veld **Van** onder de actie **Wanneer er een nieuwe e-mail binnenkomt**. |
   | **Blobinhoud** | Veld **Inhoud** | Voor dit voorbeeld gebruikt u de HTML-vrije hoofdtekst van de e-mail als de blobinhoud. Klik binnen dit venster, zodat de lijst met dynamische inhoud verschijnt, en selecteer vervolgens **Hoofdtekst** onder de actie **RemoveHTMLFunction aanroepen om de hoofdtekst van de e-mail op te schonen**. |
   ||||

   Wanneer u klaar bent, ziet de actie eruit als in dit voorbeeld:

   ![Schermopname van een voorbeeld van een voltooide bewerking 'Blob maken'.](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body-done.png)

1. Sla uw logische app op.

### <a name="check-attachment-handling"></a>Controleer de verwerking van bijlagen

Test nu of uw logische app e-mails verwerkt zoals u hebt opgegeven:

1. Als uw logische app nog niet wordt uitgevoerd, selecteert u **Uitvoeren** in de werkbalk van de ontwerpfunctie.

1. Verstuur een e-mail naar uzelf die aan de volgende criteria voldoet:

   * Het onderwerp van de e-mail bevat de tekst die u hebt opgegeven in het **Onderwerpfilter** van de trigger: `Business Analyst 2 #423501`

   * Uw e-mailbericht heeft ten minste één bijlage. Maak voor nu een leeg tekstbestand en voeg dat als bijlage toe aan uw e-mail.

   * Uw e-mailbericht bevat testinhoud in de hoofdtekst, bijvoorbeeld: `Testing my logic app`

   Bekijk [Troubleshoot your logic app](../logic-apps/logic-apps-diagnosing-failures.md) (Problemen met uw logische app oplossen) als uw logische app niet is geactiveerd of uitgevoerd, hoewel de trigger wel succesvol is geactiveerd.

1. Controleer of uw logische app het e-mailbericht in de juiste opslagcontainer heeft opgeslagen.

   1. Vouw in Storage Explorer **(Lokaal en verbonden)**  > **Opslagaccounts** > **attachmentstorageaccount (Sleutel)**  > **Blobcontainers** > **bijlagen** uit.

   1. Controleer de container **bijlagen** op het e-mailbericht.

      Op dit moment verschijnt alleen de e-mail in de container, omdat de logische app de bijlagen nog niet verwerkt.

      ![Controleer Storage Explorer op opgeslagen e-mail](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   1. Wanneer u klaar bent, verwijdert u de e-mail in Storage Explorer.

1. U kunt ook een e-mail verzenden die niet voldoet aan de criteria om de vertakking **Indien onwaar** te testen, maar deze doet momenteel nog niets.

Voeg vervolgens een lus toe om alle e-mailbijlagen te verwerken.

## <a name="process-attachments"></a>Bijlagen verwerken

Voeg de lus **Voor elke** toe aan de werkstroom van uw logische app om elke bijlage in de e-mail te verwerken.

1. Selecteer onder de vorm **Blob maken voor de hoofdtekst van de e-mail** de optie **Een actie toevoegen**.

   ![Lus 'voor elke' toevoegen](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

1. Voer onder **Kies een actie** in het zoekvak `for each` in als uw filter en selecteer deze actie: **Voor elke**

   ![Selecteer Voor elke](./media/tutorial-process-email-attachments-workflow/select-for-each.png)

1. Verander de naam van uw lus in deze beschrijving: `For each email attachment`

1. Geef nu de gegevens op die de lus gaat verwerken. Klik binnen het venster **Een uitvoer van vorige stappen selecteren**, zodat de lijst met dynamische inhoud opent, en selecteer **Bijlagen**.

   ![Selecteer 'Bijlagen'](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   Het veld **Bijlagen** geeft een matrix door die alle bijlagen bevat die in een e-mail zijn bijgevoegd. De lus **Voor elke** herhaalt acties voor elk item dat met de matrix wordt ingevoerd.

1. Sla uw logische app op.

Vervolgens voegt u de actie toe waarmee elke bijlage als blob in uw opslagcontainer **bijlagen** wordt opgeslagen.

## <a name="create-blob-for-each-attachment"></a>Een blob maken voor elke bijlage

1. In de lus **Voor elke e-mailbijlage** selecteert u **Een actie toevoegen**, zodat u de uit te voeren taak kunt opgeven voor elke gevonden bijlage.

   ![Actie aan lus toevoegen](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

1. Voer in het zoekvenster `create blob` in als uw filter en selecteer deze actie: **Blob maken**

   ![Actie toevoegen om blob te maken](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

1. Verander de naam van de actie **Blob maken 2** in deze beschrijving: `Create blob for each email attachment`

1. Geef in de actie **Blob maken voor elke e-mailbijlage** de volgende informatie op en selecteer de eigenschappen voor elke blob die u wilt maken zoals wordt weergegeven en beschreven:

   ![Blobinformatie opgeven](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | Instelling | Waarde | Beschrijving |
   | ------- | ----- | ----------- |
   | **Mappad** | /attachments | Het pad en de naam van de container die u eerder hebt gemaakt. Voor dit voorbeeld klikt u op het mappictogram en selecteert u de container '/bijlagen'. |
   | **Blobnaam** | Veld **Naam** | Voor dit voorbeeld gebruikt u de naam van de bijlage als de naam van de blob. Klik binnen dit venster, zodat de lijst met dynamische inhoud verschijnt, en selecteer vervolgens het veld **Naam** onder de actie **Wanneer er een nieuwe e-mail binnenkomt**. |
   | **Blobinhoud** | Veld **Inhoud** | Gebruik voor dit voorbeeld het veld **Inhoud** als de blobinhoud. Klik binnen dit venster, zodat de lijst met dynamische inhoud verschijnt, en selecteer vervolgens **Inhoud** onder de actie **Wanneer er een nieuwe e-mail binnenkomt**. |
   ||||

   Wanneer u klaar bent, ziet de actie eruit als in dit voorbeeld:

   ![Actie 'Blob maken' voltooid](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment-done.png)

1. Sla uw logische app op.

### <a name="check-attachment-handling"></a>Controleer de verwerking van bijlagen

Test vervolgens of uw logische app de bijlagen verwerkt zoals u hebt opgegeven:

1. Als uw logische app nog niet wordt uitgevoerd, selecteert u **Uitvoeren** in de werkbalk van de ontwerpfunctie.

1. Verstuur een e-mail naar uzelf die aan de volgende criteria voldoet:

   * Het onderwerp van de e-mail bevat de tekst die u hebt opgegeven in de eigenschap **Onderwerpfilter** van de trigger: `Business Analyst 2 #423501`

   * Uw e-mailbericht heeft ten minste twee bijlagen. Maak voor nu twee lege tekstbestanden en voeg die als bijlagen toe aan uw e-mail.

   Bekijk [Troubleshoot your logic app](../logic-apps/logic-apps-diagnosing-failures.md) (Problemen met uw logische app oplossen) als uw logische app niet is geactiveerd of uitgevoerd, hoewel de trigger wel succesvol is geactiveerd.

1. Controleer of uw logische app het e-mailbericht en de bijlagen in de juiste opslagcontainer heeft opgeslagen.

   1. Vouw in Storage Explorer **(Lokaal en verbonden)**  > **Opslagaccounts** > **attachmentstorageaccount (Sleutel)**  > **Blobcontainers** > **bijlagen** uit.

   1. Controleer de container **bijlagen** op zowel de e-mail als op de bijlagen.

      ![Controleer op opgeslagen e-mails en bijlagen](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   1. Wanneer u klaar bent, verwijdert u de e-mail en de bijlage in Storage Explorer.

Voeg vervolgens een actie toe, zodat uw logische app een e-mail verzendt om melding te maken van de bijlagen.

## <a name="send-email-notifications"></a>E-mailmeldingen verzenden

1. Selecteer in de vertakking **Indien waar**, bij de lus **Voor elke e-mailbijlage**, de optie **Een actie toevoegen**.

   ![Actie toevoegen onder lus 'voor elke'](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

1. Voer in het zoekveld `send email` in als uw filter en selecteer vervolgens de actie 'e-mail versturen' voor uw e-mailprovider.

   Als u de lijst met acties wilt filteren op een bepaalde service, kunt u eerst de connector selecteren.

   ![Selecteer de actie 'e-mail verzenden' voor uw e-mailprovider](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * Voor werk- of schoolaccounts van Azure selecteert u Outlook van Office 365.

   * Selecteer Outlook.com voor persoonlijke Microsoft-accounts.

1. Als u om uw referenties wordt gevraagd, meldt u zich aan bij uw e-mailaccount, zodat Logic Apps een verbinding met uw e-mailaccount kan maken.

1. Verander de naam van de actie **Een e-mail verzenden** in deze beschrijving: `Send email for review`

1. Geef de informatie voor deze actie op en selecteer de velden die u wilt opnemen in de e-mail zoals die wordt weergegeven en beschreven. Als u lege regels wilt toevoegen in een invoervak, drukt u op Shift + Enter.

   ![E-mailmelding verzenden](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   Als u geen verwacht veld in de lijst met dynamische inhoud kunt vinden, selecteert u **Meer weergeven** naast **Wanneer er een nieuwe e-mail binnenkomt**.

   | Instelling | Waarde | Opmerkingen |
   | ------- | ----- | ----- |
   | **Aan** | <*recipient-email-address*> | Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken. |
   | **Onderwerp**  | ```ASAP - Review applicant for position:``` **Onderwerp** | Het e-mailonderwerp dat u wilt opnemen. Klik in dit venster, voer de voorbeeldtekst in en selecteer vanuit de lijst met dynamische inhoud het veld **Onderwerp** onder **Wanneer er een nieuwe e-mail binnenkomt**. |
   | **Hoofdtekst** | ```Please review new applicant:``` <p>```Applicant name:``` **Van** <p>```Application file location:``` **Pad** <p>```Application email content:```**Hoofdtekst** | De hoofdtekst van het e-mailbericht. Klik in dit venster, voer de voorbeeldtekst in en selecteer de volgende velden uit de lijst met dynamische inhoud: <p>- Het veld **Van** onder het kopje **Wanneer er een nieuwe e-mail binnenkomt** </br>- Het veld **Pad** onder het kopje **Blob maken voor de hoofdtekst van de e-mail** </br>- Het veld **Hoofdtekst** onder het kopje **RemoveHTMLFunction aanroepen om de hoofdtekst van de e-mail op te schonen** |
   ||||

   > [!NOTE]
   > Als u een veld selecteert waarin een matrix is opgeslagen, zoals het veld **Inhoud**, wat een matrix is die bijlagen bevat, wordt in de ontwerpfunctie automatisch een lus 'Voor elke' toegevoegd rond de actie die naar het veld verwijst.
   > Op die manier kan de actie voor elk matrixitem worden uitgevoerd.
   > Als u de lus wilt verwijderen, verwijdert u het veld voor de matrix, verplaatst u de verwijzende actie naar buiten de lus, selecteert u de weglaattekens ( **...** ) op de titelbalk van de lus en selecteert u **Verwijderen**.

1. Sla uw logische app op.

Test nu uw logische app, die er nu uitziet als in dit voorbeeld:

![Voltooide logische app](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>Uw logische app uitvoeren

1. Verstuur een e-mail naar uzelf die aan de volgende criteria voldoet:

   * Het onderwerp van de e-mail bevat de tekst die u hebt opgegeven in de eigenschap **Onderwerpfilter** van de trigger: `Business Analyst 2 #423501`

   * Uw e-mail heeft één of meerdere bijlagen. U kunt een leeg tekstbestand uit uw vorige tests opnieuw gebruiken. Voeg een cv toe voor een realistischer scenario.

   * De hoofdtekst van de e-mail bevat deze tekst, die u kunt kopiëren en plakken:

     ```text

     Name: Jamal Hartnett

     Street address: 12345 Anywhere Road

     City: Any Town

     State or Country: Any State

     Postal code: 00000

     Email address: jamhartnett@outlook.com

     Phone number: 000-000-0000

     Position: Business Analyst 2 #423501

     Technical skills: Dynamics CRM, MySQL, Microsoft SQL Server, JavaScript, Perl, Power BI, Tableau, Microsoft Office: Excel, Visio, Word, PowerPoint, SharePoint, and Outlook

     Professional skills: Data, process, workflow, statistics, risk analysis, modeling; technical writing, expert communicator and presenter, logical and analytical thinker, team builder, mediator, negotiator, self-starter, self-managing  

     Certifications: Six Sigma Green Belt, Lean Project Management

     Language skills: English, Mandarin, Spanish

     Education: Master of Business Administration
     ```

1. Voer uw logische app uit. Als dit succesvol is, verzendt uw logische app u een e-mailbericht dat op dit voorbeeld lijkt:

   ![E-mailmelding van logische app](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   Als u geen een e-mailberichten ontvangt, controleert u de map met ongewenste e-mail. Het is mogelijk dat uw filter voor ongewenste e-mail dit soort e-mails in deze map zet. Als u niet zeker weet of uw logische app correct wordt uitgevoerd, kunt u [Problemen met uw logische app oplossen](../logic-apps/logic-apps-diagnosing-failures.md) raadplegen.

Gefeliciteerd, u hebt nu een logische app gemaakt en uitgevoerd die taken in verschillende Azure-services automatiseert en aangepaste code aanroept.

## <a name="clean-up-resources"></a>Resources opschonen

Als u dit voorbeeld niet meer nodig hebt, verwijdert u de resourcegroep die uw logische app en alle gerelateerde resources bevat.

1. Typ `resources groups` in het bovenste zoekvak van Azure en selecteer **Resourcegroepen**.

   ![Zoek en selecteer Resourcegroepen](./media/tutorial-process-email-attachments-workflow/find-azure-resource-groups.png)

1. Selecteer in de lijst **Resourcegroepen** de resource groep voor deze zelfstudie. 

   ![Zoek de resourcegroep voor zelfstudie](./media/tutorial-process-email-attachments-workflow/find-select-tutorial-resource-group.png)

1. Selecteer op de pagina **Overzicht** de optie **Resourcegroep verwijderen**.

   ![Resourcegroep van logische app verwijderen](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

1. Voer de naam van de resourcegroep in en selecteer **Verwijderen** als het bevestigingsdeelvenster wordt weergegeven.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u door Azure-services, zoals Azure Storage en Azure Functions, te integreren een logische app gemaakt die e-mailbijlagen verwerkt en opslaat. U kunt nu meer leren over andere connectoren die u kunt gebruiken om logische apps te bouwen.

> [!div class="nextstepaction"]
> [Meer te weten komen over connectoren voor Logic Apps](../connectors/apis-list.md)