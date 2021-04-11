---
title: Problemen oplossen met Vm's starten/stoppen (preview)
description: In dit artikel leest u hoe u problemen kunt oplossen die zijn opgetreden met de functie Vm's starten/stoppen (preview) voor uw virtuele Azure-machines.
services: azure-functions
ms.subservice: ''
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: 3c379c1eb36fc19368630188f1b584e1d8a7b8ad
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111637"
---
# <a name="troubleshoot-common-issues-with-startstop-vms-preview"></a>Veelvoorkomende problemen met Vm's voor starten/stoppen (preview) oplossen

Dit artikel bevat informatie over het oplossen van problemen die zich kunnen voordoen tijdens het installeren en configureren van Vm's voor starten/stoppen (preview). Zie [overzicht Vm's starten/stoppen](overview.md)voor algemene informatie.

## <a name="general-validation-and-troubleshooting"></a>Algemene validatie en probleem oplossing

In deze sectie wordt beschreven hoe u algemene problemen met de plannings scenario's oplost en de hoofd oorzaak kunt identificeren.

### <a name="azure-dashboard"></a>Azure-dashboard

U kunt beginnen met het bekijken van het gedeelde dash board van Azure. Het gedeelde Azure-dash board dat is geïmplementeerd als onderdeel van Vm's voor starten/stoppen v2 (preview) is een snelle en eenvoudige manier om de status te controleren van elke bewerking die wordt uitgevoerd op uw Vm's. Raadpleeg de tegel **recent geprobeerd acties op vm's** voor een overzicht van alle recente bewerkingen die zijn uitgevoerd op uw vm's. Er is enige latentie, ongeveer vijf minuten, voor het weer geven van gegevens in het rapport wanneer het gegevens ophaalt uit de Application Insights resource.

### <a name="logic-apps"></a>Logic Apps

Afhankelijk van welke Logic Apps u hebt ingeschakeld voor de ondersteuning van uw start/stop-scenario, kunt u de uitvoerings geschiedenis bekijken om te helpen vaststellen waarom het geplande opstart-en afsluit scenario niet is voltooid voor een of meer doel-Vm's. Zie [Logic apps uitvoerings geschiedenis](../../logic-apps/monitor-logic-apps.md#review-runs-history)voor meer informatie over hoe u dit gedetailleerd kunt bekijken.

### <a name="azure-storage"></a>Azure Storage

U kunt de Details bekijken van de bewerkingen die worden uitgevoerd op de virtuele machines die zijn geschreven naar de tabel **requestsstoretable** in het Azure-opslag account dat wordt gebruikt voor het starten/stoppen van vm's v2 (preview). Voer de volgende stappen uit om deze records weer te geven.

1. Navigeer naar het opslag account in de Azure Portal en in het account Select * * Storage Explorer (preview) in het linkerdeel venster.
1. Selecteer **tabellen** en selecteer vervolgens **requeststoretable**.
1. Elke record in de tabel vertegenwoordigt de actie starten/stoppen die wordt uitgevoerd op een Azure VM op basis van het doel bereik dat is gedefinieerd in het scenario van de logische app. U kunt de resultaten filteren op een van de record eigenschappen (bijvoorbeeld Time Stamp, ACTION of TARGETTOPLEVELRESOURCENAME).

### <a name="azure-functions"></a>Azure Functions

U kunt de meest recente details van de aanroep bekijken voor een van de Azure Functions die verantwoordelijk zijn voor het starten van de VM en het stoppen van de uitvoering. Eerst gaan we de uitvoerings stroom bekijken.

De uitvoerings stroom voor zowel het **geplande** als het **Sequence** -scenario wordt bepaald door dezelfde functie. Het payload-schema bepaalt welk scenario wordt uitgevoerd. Voor het **geplande** scenario is de uitvoerings stroom- **geplande** http-> **VirtualMachineRequestOrchestrator** wachtrij > **VirtualMachineRequestExecutor** -wachtrij.

Vanuit de logische app wordt de **geplande** http-functie aangeroepen met het payload-schema. Zodra de **geplande** http-functie de aanvraag ontvangt, verzendt deze de informatie naar de **Orchestrator** -wachtrij functie, die op zijn beurt meerdere wacht rijen maakt voor elke VM om de actie uit te voeren.

Voer de volgende stappen uit om de details van de aanroep te bekijken.

1. Ga in het Azure Portal naar **Azure functions**.
1. Selecteer de functie-app voor het starten/stoppen van Vm's v2 (preview) in de lijst.
1. Selecteer **functies** in het linkerdeel venster.
1. In de lijst ziet u verschillende functies die zijn gekoppeld aan elk scenario. Selecteer de **geplande** http-functie.
1. Selecteer **monitor** in het linkerdeel venster.
1. Selecteer de meest recente uitvoerings tracering om de details van de aanroep en het bericht gedeelte voor gedetailleerde logboek registratie te bekijken.
1. Herhaal dezelfde stappen voor elke functie die wordt beschreven als onderdeel van het controleren van de uitvoerings stroom.

Zie [Azure functions telemetrie in Application Insights analyseren](../../azure-functions/analyze-telemetry-data.md)voor meer informatie over het bewaken van Azure functions.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het bewaken van Azure Functions en Logic apps:

* [Azure functions bewaken](../../azure-functions/functions-monitoring.md).

* [Bewaking configureren voor Azure functions](../../azure-functions/configure-monitoring.md).

* [Logische apps bewaken](../../logic-apps/monitor-logic-apps.md).