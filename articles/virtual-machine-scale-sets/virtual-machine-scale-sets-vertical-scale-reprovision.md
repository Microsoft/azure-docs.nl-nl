---
title: Schaal sets voor virtuele Azure-machines verticaal schalen
description: Een virtuele machine verticaal schalen als reactie op het controleren van waarschuwingen met Azure Automation
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 04/18/2019
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: b172f1f7137b53e98384d92c9c709694eaf0b7e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594487"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Verticaal automatisch schalen met schaal sets voor virtuele machines

In dit artikel wordt beschreven hoe u Azure [Virtual Machine Scale sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/) verticaal kunt schalen met of zonder opnieuw in te richten. 

Verticaal schalen, ook wel bekend als *Omhoog schalen* en omlaag schalen, betekent dat de grootte van virtuele machines (VM) wordt verg root of *verkleind* als reactie op een werk belasting. Vergelijk dit gedrag met [horizon taal schalen](virtual-machine-scale-sets-autoscale-overview.md), ook wel *uitschalen* en schalen genoemd *in*, waarbij het aantal vm's wordt gewijzigd, afhankelijk van de werk belasting.

Opnieuw inrichten betekent dat een bestaande virtuele machine wordt verwijderd en vervangen door een nieuwe. Wanneer u de grootte van Vm's in een schaalset voor virtuele machines verhoogt of verlaagt, kunt u in sommige gevallen het formaat van bestaande Vm's wijzigen en uw gegevens behouden, terwijl u in andere gevallen nieuwe Vm's van de nieuwe grootte moet implementeren. Dit document behandelt beide gevallen.

Verticaal schalen kan handig zijn wanneer:

* Een service die op virtuele machines is gebouwd, is in gebruik (bijvoorbeeld in het weekend). Het verminderen van de grootte van de virtuele machine kan de maandelijkse kosten verlagen.
* Verhoog de grootte van de virtuele machine om te voldoen aan de grotere vraag zonder extra Vm's te maken.

U kunt verticaal schalen instellen om te worden geactiveerd op basis van waarschuwingen op basis van metrische gegevens uit de schaalset voor virtuele machines. Wanneer de waarschuwing wordt geactiveerd, wordt er een webhook geactiveerd waarmee een runbook wordt geactiveerd waarmee de schaal instelling kan worden aangepast. Verticaal schalen kan worden geconfigureerd door de volgende stappen uit te voeren:

1. Maak een Azure Automation-account met uitvoeren als-mogelijkheid.
2. Importeer Azure Automation grootschalige runbooks voor schaal sets voor virtuele machines in uw abonnement.
3. Voeg een webhook toe aan uw runbook.
4. Een waarschuwing toevoegen aan de schaalset voor virtuele machines met behulp van een webhook-melding.

> [!NOTE]
> Omdat de grootte van de eerste virtuele machine kan worden geschaald, kan de grootte worden beperkt door de beschik baarheid van de andere grootten in de cluster huidige virtuele machine wordt geïmplementeerd in. In de gepubliceerde Automation-runbooks die in dit artikel worden gebruikt, wordt dit geval in rekening gebracht en schaalt deze alleen binnen de onderstaande VM-grootte paren. Dit betekent dat een Standard_D1v2 virtuele machine niet plotseling kan worden geschaald naar Standard_G5 of omlaag geschaald naar Basic_A0. Ook omhoog/omlaag schalen van beperkte virtuele machines wordt niet ondersteund. U kunt kiezen tussen de volgende combi Naties van grootten:
> 
> | VM-grootten schalen koppel lid | Lid |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2_v3 |Standard_E64_v3 |
> | Standard_E2s_v3 |Standard_E64s_v3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> | Standard_NV12s_v3 |Standard_NV48s_v3 |
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Een Azure Automation-account maken met de functie uitvoeren als
Het eerste wat u moet doen, is het maken van een Azure Automation account dat als host fungeert voor de runbooks die worden gebruikt om de instanties van de virtuele-machine schaalset te schalen. Onlangs [Azure Automation](https://azure.microsoft.com/services/automation/) de functie ' uitvoeren als-account ' geïntroduceerd waardoor het instellen van de service-principal voor het automatisch uitvoeren van runbooks namens de gebruiker wordt uitgevoerd. Zie voor meer informatie:

* [Runbooks verifiëren met Azure Uitvoeren als-account](../automation/manage-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Azure Automation verticaal schalen van runbooks importeren in uw abonnement

De runbooks die nodig zijn om uw virtuele-machine schaal sets verticaal te schalen, worden al gepubliceerd in de galerie met Azure Automation Runbook. Volg de stappen in dit artikel om ze te importeren in uw abonnement:

* [Runbook- en modulegalerieën voor Azure Automation](../automation/automation-runbook-gallery.md)

Kies in het menu Runbooks de optie Blader galerie:

![Te importeren Runbooks][runbooks]

De runbooks die moeten worden geïmporteerd, worden weer gegeven. Selecteer het runbook op basis van of u verticaal wilt schalen met of zonder opnieuw inrichten:

![Runbooks galerie][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Een webhook toevoegen aan uw runbook

Wanneer u de runbooks hebt geïmporteerd, voegt u een webhook toe aan het runbook zodat deze kan worden geactiveerd door een waarschuwing van een schaalset voor virtuele machines. De details van het maken van een webhook voor uw Runbook worden beschreven in dit artikel:

* [Azure Automation webhooks](../automation/automation-webhooks.md)

> [!NOTE]
> Zorg ervoor dat u de webhook-URI kopieert voordat u het dialoog venster webhook sluit wanneer u dit adres nodig hebt in de volgende sectie.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Een waarschuwing toevoegen aan de schaalset van de virtuele machine

Hieronder ziet u een Power shell-script waarin wordt getoond hoe u een waarschuwing toevoegt aan een schaalset voor virtuele machines. Raadpleeg het volgende artikel om de naam op te halen van de metrische gegevens voor het activeren van de waarschuwing: [Azure monitor automatisch schalen van algemene metrische gegevens](../azure-monitor/autoscale/autoscale-common-metrics.md).

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> U wordt aangeraden een redelijk tijd venster voor de waarschuwing te configureren om te voor komen dat verticaal schalen en eventuele bijbehorende onderbrekingen van de service worden onderbroken. Overweeg een venster van Mini maal 20-30 minuten of meer. Overweeg horizon taal schalen als u onderbrekingen moet vermijden.
> 
> 

Raadpleeg de volgende artikelen voor meer informatie over het maken van waarschuwingen:

* [Azure Monitor PowerShell-voorbeelden](../azure-monitor/powershell-samples.md)
* [Andere CLI-voor beelden van platformen Azure Monitor](../azure-monitor/cli-samples.md)

## <a name="summary"></a>Samenvatting

In dit artikel worden eenvoudige verticale schaal voorbeelden weer gegeven. Met deze bouw stenen-Automation-account, runbooks, webhooks en waarschuwingen: u kunt een groot aantal verschillende gebeurtenissen verbinden met een aangepaste reeks acties.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
