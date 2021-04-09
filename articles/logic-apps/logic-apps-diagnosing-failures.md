---
title: Problemen met werkstromen vaststellen en oplossen
description: Meer informatie over het oplossen en diagnosticeren van problemen, fouten en fouten in uw werk stromen in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995043"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Problemen met werkstromen in Azure Logic Apps vaststellen en oplossen

Uw logische app genereert informatie die u kan helpen bij het vaststellen en oplossen van problemen in uw app. U kunt een logische app diagnosticeren door elke stap in de werk stroom te bekijken via de Azure Portal. U kunt ook enkele stappen toevoegen aan een werk stroom voor runtime-fout opsporing.

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>Trigger geschiedenis controleren

De uitvoering van elke logische app begint met een trigger poging, dus als de trigger niet wordt gestart, voert u de volgende stappen uit:

1. Controleer de status van de trigger door [de trigger geschiedenis te controleren](../logic-apps/monitor-logic-apps.md#review-trigger-history). Als u meer informatie over de trigger poging wilt weer geven, selecteert u die trigger gebeurtenis, bijvoorbeeld:

   ![Trigger status en geschiedenis weer geven](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. Controleer de invoer van de trigger om te bevestigen dat ze worden weer gegeven zoals u verwacht. Selecteer onder **invoer koppeling** de koppeling, waarin het deel venster **invoer** wordt weer gegeven.

   Trigger invoer bevat de gegevens die de trigger verwacht en vereist om de werk stroom te starten. Door deze invoer te controleren, kunt u bepalen of de trigger invoer juist is en of aan de voor waarde is voldaan, zodat de werk stroom kan door gaan.

   De `feedUrl` eigenschap hier heeft bijvoorbeeld een onjuiste waarde voor de RSS-feed:

   ![Invoer van triggers controleren op fouten](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. Controleer de triggers-uitvoer, indien van toepassing, om te bevestigen dat ze worden weer gegeven zoals verwacht. Selecteer onder **uitvoer koppeling** de koppeling, waarin het deel venster **uitvoer** wordt weer gegeven.

   Trigger uitvoer bevat de gegevens die door de trigger worden door gegeven aan de volgende stap in uw werk stroom. Door deze uitvoer te bekijken, kunt u bepalen of de juiste of verwachte waarden zijn door gegeven aan de volgende stap in uw werk stroom, bijvoorbeeld:

   ![Activerings uitvoer controleren op fouten](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > Als u inhoud vindt die u niet herkent, kunt u meer te weten komen over [verschillende inhouds typen](../logic-apps/logic-apps-content-type.md) in azure Logic apps.

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>Geschiedenis van uitvoeringen controleren

Telkens wanneer de trigger wordt geactiveerd voor een item of gebeurtenis, maakt en voert de Logic Apps Engine een afzonderlijk werk stroom exemplaar voor elk item of elke gebeurtenis. Als een uitvoering mislukt, volgt u deze stappen om te controleren wat er is gebeurd tijdens de uitvoering, inclusief de status van elke stap in de werk stroom plus de invoer en uitvoer voor elke stap.

1. Controleer de uitvoerings status van de werk stroom door [de runs History te controleren](../logic-apps/monitor-logic-apps.md#review-runs-history). Als u meer informatie wilt weer geven over een mislukte uitvoering, inclusief alle stappen in die worden uitgevoerd in hun status, selecteert u de mislukte uitvoering.

   ![Uitvoerings geschiedenis weer geven en mislukte uitvoering selecteren](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. Als alle stappen in de uitvoering worden weer gegeven, vouwt u de eerste mislukte stap uit.

   ![Eerste mislukte stap uitvouwen](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. Controleer de invoer van de mislukte stap om te controleren of ze worden weer gegeven zoals verwacht.

1. Bekijk de details van elke stap in een specifieke uitvoering. Selecteer onder uitvoerings **geschiedenis** de uitvoering die u wilt onderzoeken.

   ![Uitvoeringsgeschiedenis controleren](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![Details bekijken van een run van een logische app](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. Als u de invoer, uitvoer en eventuele fout berichten voor een specifieke stap wilt onderzoeken, kiest u die stap zodat de shape wordt uitgebreid en de details worden weer gegeven. Bijvoorbeeld:

   ![Details van de stappen bekijken](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Fouten in runtime opsporen

Als u hulp nodig hebt bij het opsporen van fouten, kunt u diagnostische stappen toevoegen aan een werk stroom voor logische apps, samen met het controleren van de trigger-en uitvoerings geschiedenis. U kunt bijvoorbeeld stappen toevoegen die gebruikmaken van de [webhook tester](https://webhook.site/) -service, zodat u HTTP-aanvragen kunt inspecteren en de exacte grootte, vorm en indeling bepaalt.

1. Ga naar de website van de [webhook-tester](https://webhook.site/) en kopieer de gegenereerde unieke URL.

1. Voeg in uw logische app een HTTP POST-actie plus de hoofd inhoud toe die u wilt testen, bijvoorbeeld een expressie of een andere stap uitvoer.

1. Plak de URL van de webhook-tester in de actie HTTP POST.

1. Als u wilt zien hoe een aanvraag wordt gevormd wanneer deze wordt gegenereerd vanuit de Logic Apps-Engine, voert u de logische app uit en bezoekt u de site van de webhook-tester voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Uw logische app bewaken](../logic-apps/monitor-logic-apps.md)
