---
title: Azure Automation VM's buiten bedrijfsuren starten/stoppen configureren
description: In dit artikel leest u hoe u de VM's buiten bedrijfsuren starten/stoppen-functie kunt configureren voor het ondersteunen van verschillende use cases of scenario's.
services: automation
ms.subservice: process-automation
ms.date: 03/12/2021
ms.topic: conceptual
ms.openlocfilehash: ef8b4ddcbf09fa3ec0e3b07ae797693aed095c88
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104963"
---
# <a name="configure-startstop-vms-during-off-hours"></a>VM's buiten bedrijfsuren starten/stoppen configureren

In dit artikel wordt beschreven hoe u de [VM's buiten bedrijfsuren starten/stoppen](automation-solution-vm-management.md) -functie configureert om de beschreven scenario's te ondersteunen. U kunt ook leren hoe u:

* [E-mail meldingen configureren](#configure-email-notifications)
* [Een VM toevoegen](#add-a-vm)
* [Een VM uitsluiten](#exclude-a-vm)
* [De planningen voor opstarten en afsluiten wijzigen](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Scenario 1: Vm's starten/stoppen volgens een planning

Dit scenario is de standaard configuratie wanneer u VM's buiten bedrijfsuren starten/stoppen voor het eerst implementeert. U kunt de functie bijvoorbeeld zo configureren dat alle virtuele machines in een abonnement worden gestopt wanneer u het werk in de avond verlaat en ze op de morgen start wanneer u weer terug bent in het kantoor. Wanneer u de planning **gepland-StartVM** en **gepland-StopVM** configureert tijdens de implementatie, worden de beoogde vm's gestart en gestopt. 

Het configureren van de functie om alleen Vm's te stoppen wordt ondersteund. Zie [de planningen voor opstarten en afsluiten wijzigen](#modify-the-startup-and-shutdown-schedules) voor meer informatie over het configureren van een aangepaste planning.

> [!NOTE]
> De tijd zone die wordt gebruikt door de functie is uw huidige tijd zone bij het configureren van de plannings tijd parameter. Azure Automation slaat het echter in de UTC-indeling op in Azure Automation. U hoeft geen tijd zone conversie uit te voeren, aangezien deze wordt verwerkt tijdens de implementatie van de machine.

Als u de Vm's wilt beheren die binnen het bereik vallen, configureert u de variabelen: `External_Start_ResourceGroupNames` , `External_Stop_ResourceGroupNames` en `External_ExcludeVMNames` .

U kunt de actie richten op een abonnement en resource groep, of een specifieke lijst met Vm's maken, maar niet beide.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>De start-en stop acties voor een abonnement en resource groep richten

1. Configureer de `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` variabelen en om de doel-vm's op te geven.

1. De **geplande-StartVM** en **geplande StopVM-** schema's inschakelen en bijwerken.

1. Voer het **ScheduledStartStop_Parent** runbook uit met het parameter veld voor de **actie** die is ingesteld op **Start** en het veld **WHATIF** para meter is ingesteld op True om een voor beeld van de wijzigingen weer te geven.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>De start-en stop actie richten op de VM-lijst

1. Voer het **ScheduledStartStop_Parent** runbook uit met de **actie** ingesteld op **starten**.

1. Voeg een door komma's gescheiden lijst met Vm's (zonder spaties) toe in het parameter veld **VMList** . Een voor beeld van een lijst is `vm1,vm2,vm3` .

1. Stel het parameter veld **WHATIF** in op True om een voor beeld van de wijzigingen weer te geven.

1. Configureer de `External_ExcludeVMNames` variabele met een door komma's gescheiden lijst met virtuele machines (VM1, VM2, VM3), zonder spaties tussen door komma's gescheiden waarden.

1. In dit scenario worden de `External_Start_ResourceGroupNames` variabelen en niet nageleefd `External_Stop_ResourceGroupnames` . Voor dit scenario moet u uw eigen Automation-schema maken. Zie [een Runbook plannen in azure Automation](shared-resources/schedules.md)voor meer informatie.

    > [!NOTE]
    > De waarde voor **doel-ResourceGroup namen** wordt opgeslagen als de waarden voor `External_Start_ResourceGroupNames` zowel `External_Stop_ResourceGroupNames` en. Voor nadere granulariteit kunt u elk van deze variabelen wijzigen in doel verschillende resource groepen. Voor het starten van actie, gebruiken `External_Start_ResourceGroupNames` en gebruiken `External_Stop_ResourceGroupNames` voor stop actie. Vm's worden automatisch toegevoegd aan de planningen starten en stoppen.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Scenario 2: Vm's in volg orde starten/stoppen met behulp van Tags

In een omgeving met twee of meer onderdelen op meerdere Vm's die een gedistribueerde werk belasting ondersteunen, wordt de volg orde waarin de onderdelen worden gestart en gestopt, op de juiste wijze ondersteund. 

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>De start-en stop acties voor een abonnement en resource groep richten

1. Voeg een `sequencestart` en een `sequencestop` tag met positieve gehele waarden toe aan vm's die zijn gericht op `External_Start_ResourceGroupNames` en `External_Stop_ResourceGroupNames` variabelen. De start-en stop acties worden in oplopende volg orde uitgevoerd. Zie een [virtuele Windows-machine coderen in azure](../virtual-machines/tag-portal.md) en [een virtuele Linux-machine in azure labelen](../virtual-machines/tag-cli.md)voor meer informatie over het coderen van een VM.

1. Wijzig de planningen **Sequenced-StartVM** en **Sequence-StopVM** in de datum en tijd die aan uw vereisten voldoen en schakel de planning in.

1. Voer het **SequencedStartStop_Parent** runbook uit met de **actie** ingesteld op **Start** en **WHATIF** ingesteld op True om een voor beeld van de wijzigingen weer te geven.

1. Bekijk een voor beeld van de actie en breng de benodigde wijzigingen aan voordat u implementeert op productie-Vm's. Als u klaar bent, voert u het runbook hand matig uit met de para meter ingesteld op **Onwaar**, of laat u het Automation **-schema Sequenced-StartVM** en **Sequence-StopVM** automatisch uitvoeren volgens uw voorgeschreven planning.

### <a name="target-the-start-and-stop-actions-by-vm-list"></a>De start-en stop acties op de VM-lijst richten

1. Voeg een `sequencestart` en een `sequencestop` tag met positieve gehele waarden toe aan vm's die u wilt toevoegen aan de `VMList` para meter.

1. Voer het **SequencedStartStop_Parent** runbook uit met de **actie** ingesteld op **starten**.

1. Voeg een door komma's gescheiden lijst met Vm's (zonder spaties) toe in het parameter veld **VMList** . Een voor beeld van een lijst is `vm1,vm2,vm3` .

1. Stel **WHATIF** in op True om een voor beeld van de wijzigingen weer te geven. 

1. Configureer de `External_ExcludeVMNames` variabele met een door komma's gescheiden lijst met vm's, zonder spaties tussen door komma's gescheiden waarden.

1. In dit scenario worden de `External_Start_ResourceGroupNames` variabelen en niet nageleefd `External_Stop_ResourceGroupnames` . Voor dit scenario moet u uw eigen Automation-schema maken. Zie [een Runbook plannen in azure Automation](shared-resources/schedules.md)voor meer informatie.

1. Bekijk een voor beeld van de actie en breng de benodigde wijzigingen aan voordat u implementeert op productie-Vm's. Als u klaar bent, voert u hand matig de **bewakings-en-diagnose/bewaking-Action-groupsrunbook** uit met de para meter ingesteld op **Onwaar**. U kunt ook de automatiserings planning **Sequenced-StartVM** en **Sequence-StopVM** automatisch uitvoeren volgens uw voorgeschreven planning.

## <a name="scenario-3-start-or-stop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Scenario 3: automatisch starten of stoppen op basis van CPU-gebruik

Met VM's buiten bedrijfsuren starten/stoppen kunt u de kosten van het uitvoeren van Azure Resource Manager en klassieke virtuele machines in uw abonnement controleren door machines te evalueren die niet worden gebruikt tijdens niet-piek perioden, zoals na uur, en ze automatisch uit te scha kelen als het processor gebruik lager is dan een opgegeven percentage.

De functie is standaard vooraf geconfigureerd om het percentage CPU-metriek te evalueren om te zien of het gemiddelde gebruik 5% of minder is. Dit scenario wordt bepaald door de volgende variabelen en kan worden gewijzigd als de standaard waarden niet voldoen aan uw vereisten:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

U kunt de actie op basis van een abonnement en resource groep inschakelen en deze richten, of een specifieke lijst met Vm's richten.

Wanneer u het **AutoStop_CreateAlert_Parent** runbook uitvoert, wordt gecontroleerd of het doel abonnement, de resource groep (en) en de virtuele machines bestaan. Als de virtuele machines bestaan, roept het runbook het **AutoStop_CreateAlert_Child** runbook op voor elke VM die wordt geverifieerd door het bovenliggende runbook. Dit onderliggende runbook:

* Hiermee maakt u een waarschuwings regel voor metrische gegevens voor elke geverifieerde VM.
* Hiermee wordt het **AutoStop_VM_Child** runbook voor een bepaalde virtuele machine geactiveerd als de CPU onder de geconfigureerde drempel waarde voor het opgegeven tijds interval daalt. 
* Probeert de virtuele machine te stoppen.

### <a name="target-the-autostop-action-against-all-vms-in-a-subscription"></a>De actie voor autostop voor alle virtuele machines in een abonnement richten

1. Zorg ervoor dat de `External_Stop_ResourceGroupNames` variabele leeg is of is ingesteld op * (joker tekens).

1. Beschrijving Als u sommige Vm's wilt uitsluiten van de actie autostop, kunt u een door komma's gescheiden lijst met VM-namen toevoegen aan de `External_ExcludeVMNames` variabele.

1. Schakel het **Schedule_AutoStop_CreateAlert_Parent** schema uit dat moet worden uitgevoerd om de vereiste waarschuwings regels voor het stoppen van de VM-metrische gegevens te maken voor alle virtuele machines in uw abonnement. Door dit type planning uit te voeren, kunt u nieuwe metrische waarschuwings regels maken wanneer er nieuwe virtuele machines aan het abonnement worden toegevoegd.

### <a name="target-the-autostop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>De actie autostop voor alle Vm's in een resource groep of meerdere resource groepen instellen

1. Voeg een door komma's gescheiden lijst met namen van resource groepen toe aan de `External_Stop_ResourceGroupNames` variabele.

1. Als u sommige Vm's wilt uitsluiten van de autostop, kunt u een door komma's gescheiden lijst met VM-namen toevoegen aan de `External_ExcludeVMNames` variabele.

1. Schakel het **Schedule_AutoStop_CreateAlert_Parent** schema uit dat moet worden uitgevoerd om de vereiste waarschuwings regels voor het stoppen van de VM-metrische gegevens voor alle virtuele machines in de resource groepen te maken. Als u deze bewerking uitvoert volgens een planning, kunt u nieuwe metrische waarschuwings regels maken wanneer er nieuwe virtuele machines worden toegevoegd aan de resource groep (en).

### <a name="target-the-autostop-action-to-a-list-of-vms"></a>De actie voor autostop voor een lijst met Vm's richten

1. Maak een nieuwe [planning](shared-resources/schedules.md#create-a-schedule) en koppel deze aan het **AutoStop_CreateAlert_Parent** runbook en voeg een door KOMMA'S gescheiden lijst met VM-namen toe aan de `VMList` para meter.

1. Als u sommige Vm's wilt uitsluiten van de actie voor autostop, kunt u een door komma's gescheiden lijst met VM-namen (zonder spaties) toevoegen aan de `External_ExcludeVMNames` variabele.

## <a name="configure-email-notifications"></a>E-mailmeldingen configureren

Als u e-mail meldingen wilt wijzigen nadat VM's buiten bedrijfsuren starten/stoppen is geïmplementeerd, kunt u de actie groep wijzigen die tijdens de implementatie is gemaakt.  

> [!NOTE]
> Abonnementen in de Azure Government Cloud bieden geen ondersteuning voor de e-mail functionaliteit van deze functie.

1. Klik in de Azure Portal op **waarschuwingen** onder **bewaking** en vervolgens op **acties beheren**. Controleer op de pagina **acties beheren** of u zich op het tabblad **actie groepen** bevindt. Selecteer de actie groep met de naam **StartStop_VM_Notification**.

    :::image type="content" source="media/automation-solution-vm-management/azure-monitor-sm.png" alt-text="Scherm afbeelding van de pagina monitor-actie groepen." lightbox="media/automation-solution-vm-management/azure-monitor-lg.png":::

1. Op de pagina **StartStop_VM_Notification** wordt de sectie **basis beginselen** voor u ingevuld en kan deze niet worden bewerkt, met uitzonde ring van het veld **weergave naam** . Bewerk de naam of accepteer de voorgestelde naam. Klik in de sectie **meldingen** op het potlood pictogram om de actie details te bewerken. Hiermee opent u het deel venster **e-mail/SMS-bericht/push/stem** . Werk het e-mail adres bij en klik op **OK** om uw wijzigingen op te slaan.

    :::image type="content" source="media/automation-solution-vm-management/change-email.png" alt-text="Scherm afbeelding van de pagina E-mail/SMS-bericht/push/Voice met een voor beeld van een e-mail adres.":::

    U kunt aanvullende acties toevoegen aan de actie groep. Zie [actie groepen](../azure-monitor/platform/action-groups.md) voor meer informatie over actie groepen

Hier volgt een voor beeld van een e-mail bericht dat wordt verzonden wanneer virtuele machines worden afgesloten met de functie.

:::image type="content" source="media/automation-solution-vm-management/email.png" alt-text="Scherm afbeelding van een voor beeld van een e-mail bericht dat wordt verzonden wanneer virtuele machines worden afgesloten met het onderdeel." lightbox="media/automation-solution-vm-management/email.png":::

## <a name="add-or-exclude-vms"></a><a name="add-exclude-vms"></a>Vm's toevoegen of uitsluiten

Met de functie kunt u Vm's toevoegen die moeten worden aangeduid of uitgesloten. 

### <a name="add-a-vm"></a>Een VM toevoegen

Er zijn twee manieren om ervoor te zorgen dat een virtuele machine wordt opgenomen wanneer de functie wordt uitgevoerd:

* Elk van de bovenliggende [runbooks](automation-solution-vm-management.md#runbooks) van de functie heeft een `VMList` para meter. U kunt een door komma's gescheiden lijst met VM-namen (zonder spaties) door geven aan deze para meter bij het plannen van het juiste bovenliggende runbook voor uw situatie en deze Vm's worden opgenomen wanneer de functie wordt uitgevoerd.

* Als u meerdere Vm's wilt selecteren, stelt `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` u en met de namen van de resource groepen die de vm's bevatten die u wilt starten of stoppen. U kunt ook de variabelen instellen op een waarde van `*` om de functie uit te voeren op alle resource groepen in het abonnement.

### <a name="exclude-a-vm"></a>Een VM uitsluiten

Als u een virtuele machine wilt uitsluiten van het stoppen/starten van Vm's buiten kantoor uren, kunt u de naam ervan toevoegen aan de `External_ExcludeVMNames` variabele. Deze variabele is een door komma's gescheiden lijst met specifieke Vm's (zonder spaties) die moeten worden uitgesloten van de functie. Deze lijst is beperkt tot 140 Vm's. Als u meer dan 140 Vm's aan deze lijst toevoegt, kunnen de Vm's die zijn ingesteld om te worden uitgesloten, per ongeluk worden gestart of gestopt.

## <a name="modify-the-startup-and-shutdown-schedules"></a>De planningen voor opstarten en afsluiten wijzigen

Als u de opstart-en afsluit schema's in deze functie wilt beheren, volgt u dezelfde stappen als beschreven in [een Runbook plannen in azure Automation](shared-resources/schedules.md). Er zijn afzonderlijke schema's vereist om Vm's te starten en stoppen.

Het configureren van de functie om alleen Vm's op een bepaald moment te stoppen, wordt ondersteund. In dit scenario maakt u alleen een stop schema en geen bijbehorende begin planning. 

1. Zorg ervoor dat u de resource groepen hebt toegevoegd voor de virtuele machines die u wilt afsluiten in de `External_Stop_ResourceGroupNames` variabele.

1. Maak uw eigen planning voor het moment waarop u de virtuele machines wilt afsluiten.

1. Ga naar het **ScheduledStartStop_Parent** runbook en klik op **schema**. Hierdoor kunt u het schema selecteren dat u in de vorige stap hebt gemaakt.

1. Selecteer **para meters en voer instellingen uit** en stel het **actie** veld in op **stoppen**.

1. Selecteer **OK** om uw wijzigingen op te slaan.

## <a name="next-steps"></a>Volgende stappen

* Zie [query logboeken van VM's buiten bedrijfsuren starten/stoppen](automation-solution-vm-management-logs.md)om de functie tijdens de bewerking te controleren.
* Zie problemen [met VM's buiten bedrijfsuren starten/stoppen oplossen](troubleshoot/start-stop-vm.md)voor informatie over het afhandelen van problemen tijdens VM-beheer.
