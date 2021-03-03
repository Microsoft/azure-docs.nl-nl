---
title: Automation-taken maken om Azure-resources te beheren en te controleren
description: Stel geautomatiseerde taken in die u helpen bij het beheren van Azure-resources en het bewaken van kosten door werk stromen te maken die worden uitgevoerd op Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 8180fe8554e5fff83e4caef8c245839518649ca1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719046"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>Azure-resources beheren en kosten bewaken door automatiserings taken te maken (preview)

> [!IMPORTANT]
> Deze functie is in openbare preview en wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Om [Azure-resources](../azure-resource-manager/management/overview.md#terminology) gemakkelijker te kunnen beheren, kunt u geautomatiseerde beheer taken voor een specifieke resource of resource groep maken met behulp van Automation-taak sjablonen, die in Beschik baarheid variëren op basis van het resource type. Voor een [Azure-opslag account](../storage/common/storage-account-overview.md)kunt u bijvoorbeeld een Automation-taak instellen waarmee u de maandelijkse kosten voor dat opslag account ontvangt. U kunt voor een [virtuele machine van Azure](https://azure.microsoft.com/services/virtual-machines/)een Automation-taak maken waarmee de virtuele machine op een vooraf gedefinieerd schema wordt ingeschakeld of uitgeschakeld.

Achter de schermen is een Automation-taak eigenlijk een werk stroom die wordt uitgevoerd op de [Azure Logic apps](../logic-apps/logic-apps-overview.md) -service en wordt gefactureerd volgens dezelfde [prijs tarieven](https://azure.microsoft.com/pricing/details/logic-apps/) en hetzelfde [prijs model](../logic-apps/logic-apps-pricing.md). Nadat u de taak hebt gemaakt, kunt u de onderliggende werk stroom bekijken en bewerken door de taak te openen in de ontwerp functie voor logische apps. Nadat een taak ten minste eenmaal is uitgevoerd, kunt u de status, geschiedenis, invoer en uitvoer voor elke uitvoering controleren.

Dit zijn de momenteel beschik bare taak sjablonen in deze preview-versie:

| Resourcetype | Automation-taak sjablonen |
|---------------|---------------------------|
| Azure-resourcegroepen | **Wanneer de resource wordt verwijderd** |
| Alle Azure-resources | **Verzend de maandelijkse kosten voor de resource** |
| Azure-VM's | Aanvullend: <p>- **Virtuele machine uitschakelen** <br>- **Virtuele machine starten** |
| Azure Storage-accounts | Aanvullend: <p>- **Oude blobs verwijderen** |
| Azure Cosmos DB | Let <p>- **Query resultaat verzenden via e-mail** |
|||

In dit artikel wordt beschreven hoe u de volgende taken kunt uitvoeren:

* [Een Automation-taak maken](#create-automation-task) voor een specifieke Azure-resource.

* [Bekijk de geschiedenis van een taak](#review-task-history), inclusief de uitvoerings status, invoer, uitvoer en andere historische gegevens.

* [Bewerk de taak](#edit-task) zodat u de taak kunt bijwerken of pas de onderliggende werk stroom van de taak aan in de ontwerp functie voor logische apps.

<a name="differences"></a>

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>Hoe verschillen de automatiserings taken van Azure Automation?

Op dit moment kunt u een Automation-taak alleen op resource niveau maken, de uitvoerings geschiedenis van de taak bekijken en de werk stroom van de onderliggende logische app van de taak bewerken, die wordt ingeschakeld door de [Azure Logic apps](../logic-apps/logic-apps-overview.md) -service. Automatiserings taken zijn meer basis en licht gewicht dan [Azure Automation](../automation/automation-intro.md).

Azure Automation is een op de cloud gebaseerde automatiserings-en configuratie service die consistent beheer in uw Azure-en niet-Azure-omgevingen ondersteunt. De service omvat [proces automatisering voor](../automation/automation-intro.md#process-automation) het indelen van processen door gebruik te maken van [runbooks](../automation/automation-runbook-execution.md), configuratie beheer met [Wijzigingen bijhouden en inventaris](../automation/change-tracking/overview.md), update beheer, gedeelde mogelijkheden en heterogene functies. Automatisering geeft u volledige controle tijdens de implementatie, bewerkingen en het buiten gebruik stellen van workloads en bronnen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* De Azure-resource die u wilt beheren. In dit artikel wordt een Azure Storage-account als voor beeld gebruikt.

* Een Office 365-account als u het voor beeld wilt volgen, waarmee u e-mail berichten verzendt met behulp van Office 365 Outlook.

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>Een Automation-taak maken

1. Zoek in de [Azure Portal](https://portal.azure.com)de resource die u wilt beheren.

1. Ga in het menu resource naar het gedeelte **automatisering** en selecteer **taken**

   ![Scherm opname van het menu met Azure Portal en een resource voor het opslag account waarin de sectie ' Automation ' is geselecteerd en het menu-item ' taken ' is ingeschakeld.](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. Selecteer in het deel venster **taken** de optie **toevoegen** zodat u een taak sjabloon kunt selecteren.

   ![Scherm opname van het gedeelte taken van het opslag account waarin de werk balk ' toevoegen ' is geselecteerd](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. Selecteer in het deel venster **een taak toevoegen** onder **Selecteer een sjabloon** de sjabloon voor de taak die u wilt maken. Als de volgende pagina niet wordt weer gegeven, selecteert u **volgende: authenticatie**.

   In dit voor beeld wordt de sjabloon **maandelijkse kosten voor resource taak verzenden** geselecteerd.

   ![Scherm afbeelding met de selecties ' maandelijks verzend kosten voor resource ' en ' volgende: verificatie '](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. Onder **verificatie** selecteert u in de sectie **verbindingen** de optie **maken** voor elke verbinding die wordt weer gegeven in de taak, zodat u verificatie referenties kunt opgeven voor alle verbindingen. De verbindings typen in elke taak variëren op basis van de taak.

   In dit voor beeld ziet u slechts één van de verbindingen die voor deze taak zijn vereist.

   ![Scherm opname van de geselecteerde optie ' maken ' voor de Azure Resource Manager verbinding](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. Wanneer u wordt gevraagd, meldt u zich aan met de referenties van uw Azure-account.

   ![Scherm afbeelding waarin de selectie wordt weer gegeven, "aanmelden"](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   Elke geverifieerde verbinding ziet er ongeveer uit als in dit voor beeld:

   ![Scherm opname van de verbinding die is gemaakt](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. Nadat u alle verbindingen hebt geverifieerd, selecteert u **volgende: Configuratie** als de volgende pagina niet wordt weer gegeven.

1. Geef onder **configuratie** een naam op voor de taak en eventuele andere informatie die nodig is voor de taak. Selecteer **Maken** als u klaar bent.

   > [!NOTE]
   > U kunt de naam van de taak niet wijzigen nadat deze is gemaakt. Overweeg daarom een naam die nog steeds van toepassing is als u [de onderliggende werk stroom bewerkt](#edit-task-workflow). Wijzigingen die u aanbrengt in de onderliggende werk stroom gelden alleen voor de taak die u hebt gemaakt, niet van de taak sjabloon.
   >
   > Als u bijvoorbeeld een naam voor uw taak hebt `Send monthly cost` , maar u de onderliggende werk stroom later bewerkt om wekelijks uit te voeren, kunt u de naam van de taak niet wijzigen in `Send weekly cost` .

   Taken die e-mail meldingen verzenden, hebben een e-mail adres nodig.

   ![Scherm opname van de vereiste informatie voor de geselecteerde taak](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   De taak die u hebt gemaakt, die automatisch actief is en wordt uitgevoerd, wordt nu weer gegeven in de lijst **Automation-taken** .

   ![Scherm afbeelding van de lijst met automatiserings taken](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > Als de taak niet onmiddellijk wordt weer gegeven, probeert u de lijst met taken te vernieuwen of wacht u even voordat u de gegevens vernieuwt. Selecteer **vernieuwen** op de werk balk.

   Nadat de geselecteerde taak is uitgevoerd, ontvangt u een e-mail bericht dat er als volgt uitziet:

   ![Scherm afbeelding met een e-mail melding verzonden per taak](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>Taak geschiedenis controleren

Voer de volgende stappen uit om de geschiedenis van de uitvoeringen van een taak samen met hun status, invoer, uitvoer en andere informatie weer te geven:

1. Zoek in het [Azure Portal](https://portal.azure.com)naar de resource die de taak geschiedenis bevat die u wilt controleren.

1. Selecteer in het menu van de resource onder **instellingen** de optie **automation tasks**.

1. Zoek in de lijst taken de taak die u wilt controleren. Selecteer in de kolom **runs** van de taak **weer gave**.

   ![Scherm opname van een taak en de geselecteerde optie weer geven](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   In het deel venster **historie runs** worden alle uitvoeringen van de taak weer gegeven, samen met de statussen, begin tijden, id's en uitvoerings duur.

   ![Scherm opname van de uitvoeringen van een taak, hun status en andere informatie](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   Hier volgt een overzicht van de mogelijke statussen voor een uitvoering:

   | Status | Beschrijving |
   |--------|-------------|
   | **Gevraagd** | De taak is geannuleerd tijdens het uitvoeren. |
   | **Mislukt** | De taak heeft ten minste één mislukte actie, maar er zijn geen verdere acties bevonden om de fout te verwerken. |
   | **Wordt uitgevoerd** | De taak wordt momenteel uitgevoerd. |
   | **Geslaagd** | Alle acties zijn voltooid. Een taak kan nog steeds worden voltooid als een actie is mislukt, maar er een volgende actie was om de fout te verwerken. |
   | **Wachten** | De uitvoering is nog niet gestart en is onderbroken omdat een eerder exemplaar van de taak nog steeds wordt uitgevoerd. |
   |||

   Voor meer informatie raadpleegt u [controle uitvoeringen geschiedenis](../logic-apps/monitor-logic-apps.md#review-runs-history)

1. Als u de statussen en andere informatie voor elke stap in een uitvoering wilt weer geven, selecteert u die uitvoering.

   Het deel venster voor het uitvoeren van de **logische app** wordt geopend en toont de onderliggende werk stroom die is uitgevoerd.

   * Een werk stroom begint altijd met een [*trigger*](../connectors/apis-list.md#triggers-actions). Voor deze taak begint de werk stroom met de [ **terugkeer patroon** trigger](../connectors/connectors-native-recurrence.md).

   * Elke stap toont de status en uitvoerings duur. Stappen met een duur van 0 seconde hebben minder dan 1 seconde nodig om uit te voeren.

   ![Scherm opname van elke stap in de uitvoering, status en uitvoerings duur](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. Als u de invoer en uitvoer voor elke stap wilt bekijken, selecteert u de stap, die uitvouwt.

   In dit voor beeld ziet u de invoer voor de terugkeer patroon trigger, die geen uitvoer heeft, omdat de trigger alleen opgeeft wanneer de werk stroom wordt uitgevoerd en geen uitvoer heeft voor de volgende acties die moeten worden verwerkt.

   ![Scherm opname van de uitgevouwen trigger en invoer](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   De actie **een E-mail verzenden** heeft daarentegen een invoer van eerdere acties in de werk stroom en uitvoer.

   ![Scherm afbeelding met een uitgebreide actie, invoer en uitvoer](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

Zie [Quick Start: uw eerste integratie werk stroom maken met behulp van Azure Logic apps-Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)voor meer informatie over hoe u uw eigen geautomatiseerde werk stromen kunt bouwen, zodat u apps, gegevens, services en systemen kunt integreren in de context van automatiserings taken voor Azure-resources.

<a name="edit-task"></a>

## <a name="edit-the-task"></a>De taak bewerken

Als u een taak wilt wijzigen, hebt u de volgende opties:

* [Bewerk de taak ' inline '](#edit-task-inline) zodat u de eigenschappen van de taak kunt wijzigen, zoals verbindings gegevens of configuratie gegevens, bijvoorbeeld uw e-mail adres.

* [Bewerk de onderliggende werk stroom van de taak](#edit-task-workflow) in de ontwerp functie voor logische apps.

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>De taak inline bewerken

1. Zoek in het [Azure Portal](https://portal.azure.com)de resource met de taak die u wilt bijwerken.

1. Selecteer **taken** in het resource menu onder **Automation**.

1. Zoek in de lijst taken de taak die u wilt bijwerken. Open het menu met weglatings tekens (**...**) van de taak en selecteer **in-line bewerken**.

   ![Scherm opname van het menu geopende beletsels en de geselecteerde optie ' in-line bewerken '](./media/create-automation-tasks-azure-resources/view-task-inline.png)

   Het tabblad **verificatie** wordt standaard weer gegeven en de bestaande verbindingen worden weer gegeven.

1. Als u nieuwe verificatie referenties wilt toevoegen of andere bestaande verificatie referenties voor een verbinding wilt selecteren, opent u het menu met weglatings tekens (**...**) van de verbinding en selecteert u **nieuwe verbinding toevoegen** of indien beschikbaar, andere verificatie referenties.

   ![Scherm opname van het tabblad verificatie, bestaande verbindingen en het geselecteerde menu met weglatings tekens](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. Als u andere taak eigenschappen wilt bijwerken, selecteert u **volgende: Configuratie**.

   Voor de taak in dit voor beeld is de enige eigenschap die beschikbaar is voor bewerken het e-mail adres.

   ![Scherm opname van het tabblad Configuratie](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. Selecteer **Opslaan** als u klaar bent.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>De onderliggende werk stroom van de taak bewerken

Wanneer u de onderliggende werk stroom voor een Automation-taak wijzigt, zijn de wijzigingen alleen van invloed op het taak exemplaar dat u hebt gemaakt en niet de sjabloon waarmee de taak wordt gemaakt. Nadat u uw wijzigingen hebt aangebracht en opgeslagen, wordt de taak mogelijk niet nauw keurig beschreven door de naam die u voor de oorspronkelijke taak hebt ingesteld. u moet de taak mogelijk opnieuw maken met een andere naam.

> [!TIP]
> Als best practice moet u de onderliggende werk stroom klonen zodat u in plaats daarvan de gekopieerde versie kunt bewerken. Op die manier kunt u uw wijzigingen in de kopie maken en testen terwijl de oorspronkelijke Automation-taak blijft werken en worden uitgevoerd zonder een risico storing te ondertreden of de bestaande functionaliteit te verbreken. Nadat u uw wijzigingen hebt voltooid en u tevreden bent met de nieuwe versie, kunt u de oorspronkelijke Automation-taak uitschakelen of verwijderen en de gekloonde versie van uw Automation-taak gebruiken. De volgende stappen bevatten informatie over het klonen van uw werk stroom.

1. Zoek in het [Azure Portal](https://portal.azure.com)de resource met de taak die u wilt bijwerken.

1. Selecteer **taken** in het resource menu onder **Automation**.

1. Zoek in de lijst taken de taak die u wilt bijwerken. Open het menu met weglatings tekens (**...**) van de taak en selecteer **openen in Logic apps**.

   ![Scherm opname van het menu geopende beletsels en de geselecteerde optie ' openen in Logic Apps '](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   De onderliggende werk stroom van de taak wordt geopend in de Azure Logic Apps-service en het deel venster **overzicht** wordt weer gegeven, waarin u dezelfde uitvoerings geschiedenis kunt zien die beschikbaar is voor de taak.

   ![Scherm opname van de taak in Azure Logic Apps weer gave met overzichts venster geselecteerd](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. Als u de onderliggende werk stroom in de ontwerp functie voor logische Apps wilt openen, selecteert u in het menu van de logische app de optie **Logic app Designer**.

   ![Scherm opname van de menu optie ' Logic app Designer ' die is geselecteerd en het ontwerp oppervlak met de onderliggende werk stroom wordt weer gegeven](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   U kunt nu de eigenschappen voor de trigger en acties van de werk stroom bewerken, evenals de trigger en acties waarmee de werk stroom zelf wordt gedefinieerd. Volg de stappen in het best practice om uw werk stroom te klonen zodat u wijzigingen kunt aanbrengen in een kopie terwijl de oorspronkelijke werk stroom blijft werken en uitvoeren.

1. Voer de volgende stappen uit om uw werk stroom te klonen en in plaats daarvan de gekopieerde versie te bewerken:

   1. Selecteer **overzicht** in het menu werk stroom van de logische app.

   1. Selecteer **klonen** op de werk balk van het overzichts venster.

   1. Voer in het deel venster Logic-app maken onder **naam** een nieuwe naam in voor de gekopieerde logische app-werk stroom.

      Met uitzonde ring van de status van de **logische app** zijn de andere eigenschappen niet beschikbaar om te bewerken. 
      
   1. Selecteer onder **status van logische app** de optie **uitgeschakeld** zodat de gekloonde werk stroom niet wordt uitgevoerd wanneer u de wijzigingen aanbrengt. U kunt de werk stroom inschakelen wanneer u klaar bent om uw wijzigingen te testen.

   1. Nadat Azure de inrichting van uw gekloonde werk stroom heeft voltooid, kunt u die werk stroom vinden en openen in de ontwerp functie voor logische apps.

1. Als u de eigenschappen voor de trigger of een actie wilt weer geven, vouwt u deze trigger of actie uit.

   U kunt bijvoorbeeld de terugkeer patroon trigger zo instellen dat deze wekelijks wordt uitgevoerd, in plaats van per maand.

   ![Scherm opname van de uitgebreide terugkeer patroon trigger met de frequentie lijst open om de beschik bare frequentie opties weer te geven](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   Zie voor meer informatie over de terugkeer patroon trigger [terugkerende taken en werk stromen maken, plannen en uitvoeren met de trigger voor terugkeer patroon](../connectors/connectors-native-recurrence.md). Zie [Connect oren voor Azure Logic apps](../connectors/apis-list.md)voor meer informatie over andere triggers en acties die u kunt gebruiken.

1. Als u de wijzigingen wilt opslaan, selecteert u op de werk balk ontwerpen de optie **Opslaan**.

   ![Scherm opname van de werk balk ontwerpen en de geselecteerde opdracht opslaan](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. Selecteer **uitvoeren** op de werk balk van de ontwerp functie om de bijgewerkte werk stroom te testen en uit te voeren.

   Nadat de uitvoering is voltooid, worden in de ontwerp functie Details van de werk stroom weer gegeven.

   ![Scherm afbeelding van de details van de werk stroom op de ontwerp functie](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. Zie [Logic apps beheren in de Azure Portal](../logic-apps/manage-logic-apps-with-azure-portal.md)als u de werk stroom wilt uitschakelen zodat de taak niet verder wordt uitgevoerd.

## <a name="provide-feedback"></a>Feedback geven

We horen graag van u. [Neem contact op met het Azure Logic apps team](mailto:logicappspm@microsoft.com)om fouten te rapporteren, feedback te geven of vragen over deze preview-functie te stellen.

## <a name="next-steps"></a>Volgende stappen

* [Logische apps beheren in de Azure Portal](../logic-apps/manage-logic-apps-with-azure-portal.md)
