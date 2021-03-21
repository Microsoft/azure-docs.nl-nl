---
title: Azure Automation runbooks met metrische waarschuwingen bewaken
description: In dit artikel wordt beschreven hoe u een metrische waarschuwing kunt instellen op basis van de voltooiings status van het runbook.
services: automation
ms.date: 08/10/2020
ms.topic: article
ms.openlocfilehash: 3a52824a7030d78647d8a664819f439d92d7296d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "100593953"
---
# <a name="monitor-runbooks-with-metric-alerts"></a>Runbooks bewaken met metrische waarschuwingen

In dit artikel leert u hoe u een [metrische waarschuwing](../azure-monitor/alerts/alerts-metric-overview.md) kunt maken op basis van de voltooiings status van het runbook.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [Azure Portal](https://portal.azure.com)

## <a name="create-alert"></a>Waarschuwing maken

Met waarschuwingen kunt u een voor waarde definiëren die moet worden bewaakt en een actie die moet worden uitgevoerd wanneer aan deze voor waarde wordt voldaan.

1. Start de Azure Automation-Service in de Azure Portal door op **alle services** te klikken en vervolgens **Automation-accounts** te zoeken en te selecteren.

2. Selecteer in de lijst met Automation-accounts het account waarvoor u een waarschuwing wilt maken. 

3. Onder **bewaking** selecteert u **waarschuwingen** en selecteert u vervolgens **+ nieuwe waarschuwings regel**. Het bereik voor het doel is al gedefinieerd en gekoppeld aan uw Automation-account.

### <a name="configure-alert-criteria"></a>Waarschuwings criteria configureren

1. Klik op **voor waarde selecteren**. Selecteer **metrische gegevens** voor het **signaal type** en kies **Totaal aantal taken** in de lijst.

2. Op de pagina **signaal logica configureren** kunt u de logica definiëren waarmee de waarschuwing wordt geactiveerd. Onder de historische grafiek ziet u twee dimensies, de naam en de **status** van het **Runbook** . Dimensies zijn verschillende eigenschappen voor een metriek die kan worden gebruikt voor het filteren van resultaten. Selecteer bij **runbooknaam** het runbook waarvoor u een melding wilt ontvangen of laat het veld leeg om op alle runbooks te waarschuwen. Selecteer voor **status** een status in de vervolg keuzelijst die u wilt controleren. De runbook-naam en status waarden die in de vervolg keuzelijst worden weer gegeven, zijn alleen voor taken die in de afgelopen week zijn uitgevoerd.

   Als u een waarschuwing wilt ontvangen over een status of runbook dat niet wordt weer gegeven in de vervolg keuzelijst, klikt u op de optie **aangepaste waarde toevoegen** naast de dimensie. Met deze actie wordt een dialoog venster geopend waarin u een aangepaste waarde kunt opgeven die onlangs niet voor die dimensie is verzonden. Als u een waarde opgeeft die niet bestaat voor een eigenschap, wordt uw waarschuwing niet geactiveerd.

   > [!NOTE]
   > Als u geen naam opgeeft voor de naam van de **runbooknaam** , wordt er een waarschuwing weer gegeven als er runbooks zijn die voldoen aan de status criteria, die verborgen systeem runbooks bevatten.

    Als u bijvoorbeeld een waarschuwing wilt ontvangen wanneer een runbook een _mislukte_ status retourneert, moet u, naast het opgeven van de naam van het runbook, de aangepaste dimensie waarde voor de **status** dimensie toevoegen **mislukt**.

    :::image type="content" source="./media/automation-alert-metric/specify-dimension-custom-value.png" alt-text="Aangepaste dimensie waarde opgeven" border="false":::

3. Definieer onder **waarschuwings logica** de voor waarde en drempel waarde voor uw waarschuwing. Hieronder wordt een voor beeld van uw voor waarde gedefinieerd.

4. Selecteer onder **geëvalueerd op basis van** de tijds duur voor de query en hoe vaak deze query moet worden uitgevoerd. Als u bijvoorbeeld **de laatste vijf minuten** voor de **periode** en **elke 1 minuut** voor de **frequentie** hebt gekozen, zoekt de waarschuwing naar het aantal runbooks dat in de afgelopen vijf minuten aan uw criteria is voldaan. Deze query wordt elke minuut uitgevoerd en zodra de opgegeven waarschuwings criteria niet meer in een venster van vijf minuten worden gevonden, wordt de waarschuwing automatisch opgelost. Wanneer u klaar bent, klikt u op **gereed**.

   ![Een resource voor de waarschuwing selecteren](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-the-action-to-take"></a>Definieer de actie die moet worden uitgevoerd

1. Onder **actie groep** selecteert u **actie groep opgeven**. Een actie groep is een groep acties die u in meer dan één waarschuwing kunt gebruiken. Deze kunnen bestaan uit, maar zijn niet beperkt tot, e-mail meldingen, runbooks, webhooks en nog veel meer. Zie [actie groepen maken en beheren](../azure-monitor/alerts/action-groups.md)voor meer informatie over actie groepen en stappen voor het maken van een e-mail melding.

### <a name="define-alert-details"></a>Waarschuwingsdetails definiëren

1. Geef bij Details van de **waarschuwings regel** de waarschuwing een beschrijvende naam en beschrijving. Stel de **Ernst** in die overeenkomt met uw waarschuwings voorwaarde. Er zijn vijf Ernst, variërend van 0 tot 5. De waarschuwingen worden gezien op dezelfde onafhankelijk van de ernst, u kunt de ernst overeenkomen met uw bedrijfs logica.

1. Standaard worden regels ingeschakeld bij het maken, tenzij u **Nee** selecteert voor de optie **waarschuwings regel inschakelen bij het maken**. Voor waarschuwingen die zijn gemaakt met een uitgeschakelde status kunt u ze in de toekomst inschakelen wanneer u klaar bent. Selecteer **waarschuwings regel maken** om uw wijzigingen op te slaan.

## <a name="receive-notification"></a>Melding ontvangen

Wanneer aan de waarschuwings criteria wordt voldaan, voert de actie groep de gedefinieerde actie uit. In het voor beeld van dit artikel wordt een e-mail verzonden. De volgende afbeelding is een voor beeld van een e-mail bericht dat wordt weer gegeven nadat de waarschuwing is geactiveerd:

![E-mail waarschuwing](./media/automation-alert-activity-log/alert-email.png)

Zodra de metriek niet langer buiten de opgegeven drempel waarde ligt, wordt de waarschuwing gedeactiveerd en voert de actie groep de gedefinieerde actie uit. Als er een actie type voor e-mail is geselecteerd, wordt er een oplossings-e-mail verzonden met de melding dat deze is opgelost.

## <a name="next-steps"></a>Volgende stappen

* Zie [een waarschuwing gebruiken om een Azure Automation runbook te activeren](automation-create-alert-triggered-runbook.md)voor meer informatie.
