---
title: Automatiseringstaken maken voor het beheren en bewaken van Azure-resources
description: Stel geautomatiseerde taken in die u helpen Bij het beheren van Azure-resources en het bewaken van kosten door werkstromen te maken die worden uitgevoerd op Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/05/2021
ms.openlocfilehash: 0a98f9e4b108d2498fa19bc0b041f9d52272c7d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774913"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>Azure-resources beheren en kosten bewaken door automatiseringstaken te maken (preview)

> [!IMPORTANT]
> Deze functie is in openbare preview en wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Als u [Azure-resources](../azure-resource-manager/management/overview.md#terminology) gemakkelijker wilt beheren, kunt u geautomatiseerde beheertaken maken voor een specifieke resource of resourcegroep met behulp van automatiseringstaaksjablonen, die variëren in beschikbaarheid op basis van het resourcetype. Voor een [Azure-opslagaccount](../storage/common/storage-account-overview.md)kunt u bijvoorbeeld een automatiseringstaak instellen die u de maandelijkse kosten voor dat opslagaccount stuurt. Voor een [virtuele Azure-machine](https://azure.microsoft.com/services/virtual-machines/)kunt u een automatiseringstaak maken die die virtuele machine volgens een vooraf gedefinieerd schema in- of uitzet.

Achter de schermen is een automatiseringstaak eigenlijk een werkstroom die wordt uitgevoerd op de [Azure Logic Apps-service](../logic-apps/logic-apps-overview.md) en die wordt gefactureerd met dezelfde prijstarieven [en](https://azure.microsoft.com/pricing/details/logic-apps/) [hetzelfde prijsmodel.](../logic-apps/logic-apps-pricing.md) Nadat u de taak hebt gemaakt, kunt u de onderliggende werkstroom bekijken en bewerken door de taak te openen in logic app designer. Nadat een taak ten minste één keer is uitgevoerd, kunt u de status, geschiedenis, invoer en uitvoer voor elke run controleren.

Dit zijn de momenteel beschikbare taaksjablonen in deze preview:

| Resourcetype | Automation-taaksjablonen |
|---------------|---------------------------|
| Azure-resourcegroepen | **Wanneer de resource wordt verwijderd** |
| Alle Azure-resources | **Maandelijkse kosten voor resource verzenden** |
| Azure-VM's | Aanvullend: <p>- **Virtuele machine uitschakelen** <br>- **Virtuele machine starten** |
| Azure Storage-accounts | Aanvullend: <p>- **Oude blobs verwijderen** |
| Azure Cosmos DB | Bovendien <p>- **Queryresultaat verzenden via e-mail** |
|||

In dit artikel wordt beschreven hoe u de volgende taken uitvoert:

* [Maak een automatiseringstaak](#create-automation-task) voor een specifieke Azure-resource.

* [Bekijk de geschiedenis van een taak,](#review-task-history)die de uitvoerstatus, invoer, uitvoer en andere historische informatie bevat.

* [Bewerk de taak](#edit-task) zodat u de taak kunt bijwerken of de onderliggende werkstroom van de taak kunt aanpassen in de ontwerpfunctie voor logische apps.

<a name="differences"></a>

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>Hoe verschillen automatiseringstaken van Azure Automation?

Op dit moment kunt u een automatiseringstaak alleen op resourceniveau maken, de geschiedenis van de taak uitvoeren bekijken [](../logic-apps/logic-apps-overview.md) en de onderliggende werkstroom van de logische app van de taak bewerken, die powered by de Azure Logic Apps service. Automatiseringstaken zijn basis- en lichtgewichtr dan [Azure Automation.](../automation/automation-intro.md)

Ter vergelijking: Azure Automation is een cloudgebaseerde automatiserings- en configuratieservice die ondersteuning biedt voor consistent beheer in uw Azure- en niet-Azure-omgevingen. De service [](../automation/automation-intro.md#process-automation) omvat procesautomatisering voor het in delen van processen met behulp van [runbooks,](../automation/automation-runbook-execution.md)configuratiebeheer met bijhouden en inventaris [van](../automation/change-tracking/overview.md)wijzigen, updatebeheer, gedeelde mogelijkheden en heterogene functies. Automatisering geeft u volledige controle tijdens de implementatie, bewerkingen en het buiten gebruik stellen van workloads en bronnen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* De Azure-resource die u wilt beheren. In dit artikel wordt een Azure-opslagaccount als voorbeeld gebruikt.

* Een Office 365-account als u het voorbeeld wilt volgen, waarmee u een e-mail ontvangt via Office 365 Outlook.

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>Een automatiseringstaak maken

1. Zoek in [Azure Portal](https://portal.azure.com)de resource die u wilt beheren.

1. Schuif in het resourcemenu naar de sectie **Automation** en selecteer **Taken**

   ![Schermopname van de Azure Portal en het resourcemenu van een opslagaccount waarin de sectie 'Automation' het menu-item 'Taken' heeft geselecteerd.](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. Selecteer in **het deelvenster** Taken de optie **Toevoegen** zodat u een taaksjabloon kunt selecteren.

   ![Schermopname van het deelvenster Taken van het opslagaccount waarin 'Toevoegen' is geselecteerd op de werkbalk](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. Selecteer in **het deelvenster Een taak** toevoegen onder Een sjabloon **selecteren** de sjabloon voor de taak die u wilt maken. Als de volgende pagina niet wordt weergegeven, selecteert u **Volgende: Verificatie.**

   In dit voorbeeld wordt verder de sjabloon **Maandelijkse kosten verzenden voor resourcetaak** geselecteerd.

   ![Schermopname van de selecties 'Maandelijkse kosten voor resource verzenden' en 'Volgende: Verificatie'](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. Selecteer **onder Verificatie** in de  sectie Verbindingen de optie Maken voor elke verbinding die in de taak wordt weergegeven, zodat u **verificatiereferenties** voor alle verbindingen kunt verstrekken. De typen verbindingen in elke taak variëren op basis van de taak.

   In dit voorbeeld ziet u slechts een van de verbindingen die vereist zijn voor deze taak.

   ![Schermopname met de geselecteerde optie Maken voor de Azure Resource Manager verbinding](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. Meld u aan met de referenties van uw Azure-account wanneer u hier om wordt gevraagd.

   ![Schermopname van de selectie 'Aanmelden'](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   Elke geverifieerde verbinding lijkt op het volgende voorbeeld:

   ![Schermopname van de verbinding die is gemaakt](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. Nadat u alle verbindingen hebt geverifieerd, selecteert **u Volgende: Configuratie** als de volgende pagina niet wordt weergegeven.

1. Geef **onder Configuratie** een naam op voor de taak en eventuele andere informatie die vereist is voor de taak. Selecteer **Maken** als u klaar bent.

   > [!NOTE]
   > U kunt de naam van de taak na het maken niet wijzigen, dus overweeg een naam die nog steeds van toepassing is als u [de onderliggende werkstroom bewerkt.](#edit-task-workflow) Wijzigingen die u aan de onderliggende werkstroom aan te brengen, zijn alleen van toepassing op de taak die u hebt gemaakt, niet op de taaksjabloon.
   >
   > Als u bijvoorbeeld de taak een naam geeft, maar u later de onderliggende werkstroom bewerkt zodat deze wekelijks wordt uitgevoerd, kunt u de naam van uw taak niet wijzigen `Send monthly cost` in `Send weekly cost` .

   Voor taken die e-mailmeldingen verzenden, is een e-mailadres vereist.

   ![Schermopname met de vereiste informatie voor de geselecteerde taak](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   De taak die u hebt gemaakt, die automatisch live en actief is, wordt nu weergegeven in de **lijst Automation-taken.**

   ![Schermopname van de lijst met automatiseringstaken](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > Als de taak niet onmiddellijk wordt weergegeven, vernieuwt u de takenlijst of wacht u even voordat u de taak vernieuwt. Selecteer Vernieuwen op de **werkbalk.**

   Nadat de geselecteerde taak is uitgevoerd, ontvangt u een e-mailbericht dat lijkt op dit voorbeeld:

   ![Schermopname van de e-mailmelding die per taak is verzonden](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>Taakgeschiedenis controleren

Volg deze stappen om de geschiedenis van een taak met de status, invoer, uitvoer en andere informatie van een taak weer te geven:

1. Zoek in [Azure Portal](https://portal.azure.com)de resource met de taakgeschiedenis die u wilt controleren.

1. Selecteer in het menu van de resource onder **Instellingen** de optie **Automation-taken.**

1. Zoek in de lijst met taken de taak die u wilt controleren. Selecteer Weergave in de **kolom Runs** van **die taak.**

   ![Schermopname van een taak en de geselecteerde optie 'Weergeven'](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   In **het deelvenster** Geschiedenis van runs worden alle runs voor de taak weergegeven, samen met hun status, begintijden, id's en duur van de run.

   ![Schermopname van de runs van een taak, de status en andere informatie](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   Hier zijn de mogelijke statussen voor een run:

   | Status | Beschrijving |
   |--------|-------------|
   | **Geannuleerd** | De taak is geannuleerd tijdens het uitvoeren. |
   | **Mislukt** | De taak heeft ten minste één mislukte actie, maar er bestonden geen verdere acties om de fout af te handelen. |
   | **Wordt uitgevoerd** | De taak wordt momenteel uitgevoerd. |
   | **Geslaagd** | Alle acties zijn geslaagd. Een taak kan nog steeds worden voltooid als een actie is mislukt, maar een volgende actie bestond om de fout af te handelen. |
   | **Wachten** | De run is nog niet gestart en is onderbroken omdat een eerder exemplaar van de taak nog wordt uitgevoerd. |
   |||

   Zie Geschiedenis van runs [controleren voor meer informatie](../logic-apps/monitor-logic-apps.md#review-runs-history)

1. Als u de status en andere informatie voor elke stap in een run wilt weergeven, selecteert u die run.

   Het **deelvenster Uitvoeren van logische app** wordt geopend en toont de onderliggende werkstroom die is uitgevoerd.

   * Een werkstroom begint altijd met een [*trigger*](../connectors/apis-list.md#triggers). Voor deze taak begint de werkstroom met de [ **trigger Terugkeerpatroon.**](../connectors/connectors-native-recurrence.md)

   * Elke stap toont de status en de duur van de run. Het uitvoeren van stappen met een duur van 0 seconden duurde minder dan 1 seconde.

   ![Schermopname van elke stap in de duur van de run, status en run](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. Als u de invoer en uitvoer voor elke stap wilt controleren, selecteert u de stap , die wordt uitvuit.

   In dit voorbeeld ziet u de invoer voor de trigger Terugkeerpatroon, die geen uitvoer heeft, omdat de trigger alleen aangeeft wanneer de werkstroom wordt uitgevoerd en geen uitvoer biedt voor de volgende acties die moeten worden verwerkt.

   ![Schermopname met de uitgebreide trigger en invoer](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   De actie Een **e-mail verzenden** heeft daarentegen invoer van eerdere acties in de werkstroom en uitvoer.

   ![Schermopname van een uitgebreide actie, invoer en uitvoer](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

Zie [Quickstart: Uw](../logic-apps/quickstart-create-first-logic-app-workflow.md)eerste integratiewerkstroom maken met behulp van Azure Logic Apps - Azure Portal voor meer informatie over het bouwen van uw eigen geautomatiseerde werkstromen, zodat u naast de context van automatiseringstaken voor Azure-resources ook apps, gegevens, services en systemen kunt integreren.

<a name="edit-task"></a>

## <a name="edit-the-task"></a>De taak bewerken

Als u een taak wilt wijzigen, hebt u de volgende opties:

* [Bewerk de taak 'inline'](#edit-task-inline) zodat u de eigenschappen van de taak kunt wijzigen, zoals verbindingsgegevens of configuratiegegevens, bijvoorbeeld uw e-mailadres.

* [Bewerk de onderliggende werkstroom van de taak](#edit-task-workflow) in de ontwerpfunctie voor logische apps.

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>De taak inline bewerken

1. Zoek in [Azure Portal](https://portal.azure.com)de resource met de taak die u wilt bijwerken.

1. Selecteer in het menu van de resource onder **Automation** de optie **Taken**.

1. Zoek in de lijst met taken de taak die u wilt bijwerken. Open het menu van de taak **(...**) en selecteer **In regel bewerken.**

   ![Schermopname van het geopende menu met weglatingsleden en de geselecteerde optie 'In-line bewerken'](./media/create-automation-tasks-azure-resources/view-task-inline.png)

   Standaard wordt het **tabblad Verificatie** weergegeven met de bestaande verbindingen.

1. Als u nieuwe verificatiereferenties wilt toevoegen of andere bestaande verificatiereferenties voor een verbinding wilt selecteren,  opent u het menu met de drie puntjes **(...)** van de verbinding en selecteert u Nieuwe verbinding toevoegen of, indien beschikbaar, andere verificatiereferenties.

   ![Schermopname van het tabblad Verificatie, bestaande verbindingen en het geselecteerde menu met weglatingsleden](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. Als u andere taakeigenschappen wilt bijwerken, **selecteert u Volgende: Configuratie.**

   Voor de taak in dit voorbeeld is het e-mailadres de enige eigenschap die kan worden bewerkt.

   ![Schermopname van het tabblad Configuratie](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. Selecteer **Opslaan** als u klaar bent.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>De onderliggende werkstroom van de taak bewerken

Wanneer u de onderliggende werkstroom voor een automatiseringstaak wijzigt, zijn uw wijzigingen alleen van invloed op het taak-exemplaar dat u hebt gemaakt en niet op de sjabloon die de taak maakt. Nadat u de wijzigingen hebt aangebracht en op slaan, wordt de taak mogelijk niet meer nauwkeurig beschreven in de naam die u hebt opgegeven voor de oorspronkelijke taak, waardoor u de taak mogelijk opnieuw moet maken met een andere naam.

> [!TIP]
> Kloon best practice onderliggende werkstroom zodat u in plaats daarvan de gekopieerde versie kunt bewerken. Op die manier kunt u uw wijzigingen op de kopie aanbrengen en testen terwijl de oorspronkelijke automatiseringstaak blijft werken en worden uitgevoerd zonder dat u een onderbreking of onderbreking van de bestaande functionaliteit loopt. Nadat u de wijzigingen hebt voltooid en tevreden bent met de nieuwe versie, kunt u de oorspronkelijke automatiseringstaak uitschakelen of verwijderen en de gekloonde versie gebruiken voor uw automatiseringstaak. De volgende stappen bevatten informatie over het klonen van uw werkstroom.

1. Zoek in [Azure Portal](https://portal.azure.com)de resource met de taak die u wilt bijwerken.

1. Selecteer in het menu van de resource onder **Automation** de optie **Taken**.

1. Zoek in de lijst met taken de taak die u wilt bijwerken. Open het menu met de weglating (**...**) van de taak en selecteer **Openen in Logic Apps**.

   ![Schermopname van het geopende menu met het beletselletselmenu en de geselecteerde optie Openen in Logic Apps](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   De onderliggende werkstroom van de taak wordt geopend  in de Azure Logic Apps-service en toont het deelvenster Overzicht waarin u dezelfde geschiedenis van de runs kunt bekijken die beschikbaar is voor de taak.

   ![Schermopname van de taak in Azure Logic Apps weergave met het deelvenster Overzicht geselecteerd](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. Als u de onderliggende werkstroom in de ontwerpfunctie van de logische app wilt openen, selecteert u in het menu van de logische app de optie **Ontwerper van logische app.**

   ![Schermopname met de menuoptie 'Ontwerper van logische app' geselecteerd en ontwerpoppervlak met de onderliggende werkstroom](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   U kunt nu de eigenschappen voor de trigger en acties van de werkstroom bewerken en de trigger en acties bewerken die de werkstroom zelf definiëren. Als u echter best practice, volgt u de stappen om uw werkstroom te klonen, zodat u uw wijzigingen op een kopie kunt aanbrengen terwijl de oorspronkelijke werkstroom blijft werken en worden uitgevoerd.

1. Als u uw werkstroom wilt klonen en in plaats daarvan de gekopieerde versie wilt bewerken, volgt u deze stappen:

   1. Selecteer Overzicht in het werkstroommenu van **de** logische app.

   1. Selecteer Klonen op de werkbalk van het **overzichtsvenster.**

   1. Voer in het deelvenster voor het maken van logische apps onder **Naam** een nieuwe naam in voor de gekopieerde werkstroom van de logische app.

      Met uitzondering **van De status van** de logische app zijn de andere eigenschappen niet beschikbaar voor bewerking. 
      
   1. Selecteer **onder Status van logische app** de optie **Uitgeschakeld,** zodat de gekloonde werkstroom niet wordt uitgevoerd terwijl u uw wijzigingen aan te brengen. U kunt de werkstroom inschakelen wanneer u klaar bent om uw wijzigingen te testen.

   1. Nadat Azure klaar is met het inrichten van uw gekloonde werkstroom, kunt u die werkstroom zoeken en openen in de ontwerpfunctie voor logische apps.

1. Als u de eigenschappen voor de trigger of een actie wilt weergeven, vouwt u die trigger of actie uit.

   U kunt bijvoorbeeld de trigger Terugkeerpatroon wijzigen zodat deze wekelijks wordt uitgevoerd in plaats van maandelijks.

   ![Schermopname van de uit uitgebreide terugkeertrigger met de lijst Frequentie geopend om beschikbare frequentieopties weer te geven](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   Zie Terugkerende taken en werkstromen maken, plannen en uitvoeren met de trigger Terugkeerpatroon voor meer informatie [over de trigger Terugkeerpatroon.](../connectors/connectors-native-recurrence.md) Zie Connectors voor Azure Logic Apps voor meer informatie over andere triggers en [acties die u kunt Azure Logic Apps.](../connectors/apis-list.md)

1. Als u uw wijzigingen wilt opslaan, selecteert u Opslaan op de werkbalk van **de ontwerper.**

   ![Schermopname van de werkbalk van de ontwerper en de geselecteerde opdracht 'Opslaan'](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. Als u de bijgewerkte werkstroom wilt testen en uitvoeren, selecteert u Uitvoeren op de werkbalk van de **ontwerpfunctie.**

   Nadat de run is uitgevoerd, toont de ontwerper de details van de werkstroom.

   ![Schermopname van de details van de werkstroomuitwerkstroom in de ontwerpfunctie](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. Zie Logische apps beheren in de sectie Azure Portal om de werkstroom uit te schakelen zodat de taak [niet wordt Azure Portal.](../logic-apps/manage-logic-apps-with-azure-portal.md)

## <a name="provide-feedback"></a>Feedback geven

We horen graag van u! Neem contact op met het team van Azure Logic Apps om fouten te melden, feedback te geven of vragen te stellen over [deze preview-functie.](mailto:logicappspm@microsoft.com)

## <a name="next-steps"></a>Volgende stappen

* [Logische apps beheren in de Azure Portal](../logic-apps/manage-logic-apps-with-azure-portal.md)
