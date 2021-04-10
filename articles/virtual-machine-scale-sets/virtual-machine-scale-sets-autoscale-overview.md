---
title: Overzicht van automatisch schalen met virtuele-machineschaalsets van Azure
description: Lees hier alles over de verschillende manieren waarop u een virtuele-machineschaalset van Azure automatisch kunt schalen op basis van prestaties of volgens een vast schema.
author: avirishuv
ms.author: avverma
ms.topic: overview
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: dbd2d6dc3034ff64ef6b5d4d36d836ce8462b154
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100591399"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Overzicht van automatisch schalen met virtuele-machineschaalsets van Azure
Met een virtuele-machineschaalset van Azure kunt u automatisch het aantal VM-exemplaren vergroten of verkleinen waarop uw toepassing wordt uitgevoerd. Dit geautomatiseerde en elastische gedrag vermindert de overhead die nodig is voor het controleren en optimaliseren van de prestaties van uw toepassing. U stelt regels op die de aanvaardbare prestaties bepalen voor een positieve klantervaring. Wanneer deze gedefinieerde drempelwaarden worden bereikt, worden op basis van de regels voor automatisch schalen acties ondernomen om de capaciteit van de schaalset aan te passen. U kunt ook gebeurtenissen plannen om de capaciteit van een schaalset op vaste tijden automatisch te vergroten of verkleinen. In dit artikel vindt u een overzicht van de metrische gegevens over prestaties die beschikbaar zijn en welke acties u met automatisch schalen kunt uitvoeren.


## <a name="benefits-of-autoscale"></a>Voordelen van automatisch schalen
Als de vraag van uw toepassing toeneemt, neemt de belasting van de VM-exemplaren in de schaalset ook toe. Als deze toegenomen belasting consistent is, en geen piekbelasting is, kunt u regels voor automatisch schalen configureren om het aantal VM-exemplaren in de schaalset te verhogen.

> [!NOTE]
> Wanneer u automatische exemplaarreparaties voor uw schaalset gebruikt, is het maximum aantal exemplaren in de schaalset 200. Meer informatie over [Automatische exemplaarreparaties](./virtual-machine-scale-sets-automatic-instance-repairs.md).

Wanneer deze VM-exemplaren worden gemaakt en uw toepassingen worden geïmplementeerd, zorgt de schaalset ervoor dat er via de load balancer verkeer wordt gedistribueerd naar de exemplaren. U bepaalt welke metrische gegevens moeten worden bewaakt, zoals CPU of schijf, hoe lang de belasting van de toepassing overeen moet komen met een bepaalde drempelwaarde en hoeveel VM-exemplaren er moeten worden toegevoegd aan de schaalset.

In het weekend of 's avonds kan de vraag voor uw toepassing afnemen. Als deze afgenomen belasting consistent is gedurende een bepaalde periode, kunt u regels voor automatisch schalen configureren om het aantal VM-exemplaren in de schaalset te verlagen. Deze inschaalactie reduceert de kosten voor het uitvoeren van uw schaalset, aangezien u alleen het aantal exemplaren uitvoert dat vereist is om te voldoen aan de actuele vraag.


## <a name="use-host-based-metrics"></a>Hostgebaseerde metrische gegevens gebruiken
U kunt regels voor automatisch schalen maken die werken met ingebouwde hostgebaseerde metrische gegevens die beschikbaar zijn via uw VM-exemplaren. Met hostgebaseerde metrische gegevens krijgt u inzicht in de prestaties van de VM-exemplaren in een schaalset zonder dat u extra agents en gegevensverzamelingen hoeft te installeren of configureren. Regels voor automatisch schalen die gebruikmaken van deze metrische gegevens kunnen het aantal VM-exemplaren uit- of inschakelen in reactie op CPU-gebruik, geheugenvraag of schijftoegang.

Regels voor automatisch schalen die gebruikmaken van hostgebaseerde metrische gegevens, kunnen worden gemaakt met een van de volgende hulpprogramma's:

- [Azure-portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure-CLI](tutorial-autoscale-cli.md)
- [Azure-sjabloon](tutorial-autoscale-template.md)

Als u regels voor automatisch schalen wilt maken die meer gedetailleerde metrische gegevens van prestaties gebruiken, kunt u [de Azure Diagnostics-extensie installeren en configureren](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) op VM-exemplaren of [uw toepassing configureren voor het gebruik van App Insights](#application-level-metrics-with-app-insights).

Regels voor automatisch schalen die gebruikmaken van hostgebaseerde metrische gegevens, metrische gegevens van de gast-VM met de Azure Diagnostics-extensie en App Insights kunnen de volgende configuratie-instellingen gebruiken.

### <a name="metric-sources"></a>Bronnen van metrische gegevens
Regels voor automatisch schalen kunnen metrische gegevens uit een van de volgende bronnen gebruiken:

| Bron van metrische gegevens        | Toepassing                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Huidige schaalset    | Voor hostgebaseerde metrische gegevens waarvoor geen extra agents hoeven te worden geïnstalleerd of geconfigureerd.                                  |
| Storage-account      | De Azure Diagnostics-extensie schrijft metrische gegevens van prestaties naar Azure Storage, waar ze die vervolgens worden gebruikt om regels voor automatisch schalen te activeren. |
| Service Bus-wachtrij    | Uw toepassing of andere onderdelen kunnen berichten in een Azure Service Bus-wachtrij verzenden om regels te activeren.                   |
| Application Insights | Een instrumentatiepakket dat in uw toepassing wordt geïnstalleerd om metrische gegevens rechtstreeks vanuit de app te streamen.                         |


### <a name="autoscale-rule-criteria"></a>Criteria voor regels voor automatisch schalen
De volgende hostgebaseerde metrische gegevens zijn beschikbaar voor gebruik wanneer u regels voor automatisch schalen gaat maken. Als u de Azure Diagnostics-extensie of App Insights gebruikt, definieert u welke metrische gegevens u wilt bewaken en gebruiken met regels voor automatisch schalen.

| Naam van metrische gegevens               |
|---------------------------|
| Percentage CPU            |
| Netwerk in                |
| Netwerk uit               |
| Gelezen bytes op de schijf           |
| Geschreven bytes op de schijf          |
| Leesbewerkingen op de schijf/seconde  |
| Schrijfbewerkingen op de schijf/seconde |
| Resterend CPU-tegoed     |
| Verbruikt CPU-tegoed      |

Wanneer u regels voor automatisch schalen maakt om bepaalde metrische gegevens te bewaken, analyseren de regels een van de volgende aggregatie-acties voor metrische gegevens:

| Type aggregatie |
|------------------|
| Average          |
| Minimum          |
| Maximum          |
| Totaal            |
| Laatste             |
| Count            |

De regels voor automatisch schalen worden vervolgens geactiveerd wanneer de metrische gegevens met behulp van een van de volgende operatoren worden vergeleken met uw gedefinieerde drempelwaarde:

| Operator                 |
|--------------------------|
| Groter dan             |
| Groter dan of gelijk aan |
| Kleiner dan                |
| Kleiner dan of gelijk aan    |
| Gelijk aan                 |
| Niet gelijk aan             |


### <a name="actions-when-rules-trigger"></a>Acties wanneer regels worden geactiveerd
Wanneer een regel voor automatisch schalen wordt geactiveerd, kan uw schaalset automatisch op een van de volgende manieren worden geschaald:

| Schaalbewerking     | Toepassing                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Aantal verhogen met   | Een vast aantal VM-exemplaren dat moet worden gemaakt. Handig in schaalsets met een kleiner aantal VM's.                                           |
| Percentage verhogen met | Een procentuele toename van het aantal VM-exemplaren. Geschikt voor grotere schaalsets waarbij een vaste toename de prestaties mogelijk niet merkbaar zal verbeteren. |
| Aantal verhogen tot   | Zo veel VM-exemplaren maken als nodig is om een gewenste maximumhoeveelheid te bereiken.                                                            |
| Aantal verlagen met   | Een vast aantal VM-exemplaren dat moet worden verwijderd. Handig in schaalsets met een kleiner aantal VM's.                                           |
| Percentage verlagen met | Een procentuele afname van het aantal VM-exemplaren. Geschikt voor grotere schaalsets waarbij een vaste afname het verbruik en de kosten van resources mogelijk niet merkbaar zal verbeteren. |
| Aantal verlagen tot   | Zo veel VM-exemplaren verwijderen als nodig is om een gewenste minimumhoeveelheid te bereiken.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Metrische gegevens van gast-VM met Azure Diagnostics-extensie
De Azure Diagnostics-extensie is een agent die wordt uitgevoerd binnen een VM-exemplaar. De agent bewaakt metrische gegevens van prestaties en slaat deze op in Azure Storage. Deze metrische gegevens van prestaties bevatten meer gedetailleerde informatie over de status van de VM, zoals *AverageReadTime* voor schijven of *PercentIdleTime-* voor CPU. U kunt regels voor automatisch schalen maken op basis van een meer gedetailleerd inzicht in de prestaties van de VM, niet alleen het percentage CPU-gebruik of geheugengebruik.

Als u de Azure Diagnostics-extensie wilt gebruiken, moet u Azure-opslagaccounts maken voor uw VM-exemplaren, de Azure Diagnostics-agent installeren en vervolgens de VM's configureren voor het streamen van specifieke prestatiemeteritems naar het opslagaccount.

Zie voor meer informatie de artikelen voor het inschakelen van de diagnostische Azure-extensie op een [Linux-VM](../virtual-machines/extensions/diagnostics-linux.md) of [Windows-VM](../virtual-machines/extensions/diagnostics-windows.md).


## <a name="application-level-metrics-with-app-insights"></a>Metrische gegevens op toepassingsniveau met App Insights
Als u een beter beeld wilt krijgen van de prestaties van uw toepassingen, kunt u Application Insights gebruiken. U installeert dan een klein instrumentatiepakket in uw toepassing waarmee de app wordt bewaakt en telemetrie naar Azure wordt verzonden. U kunt metrische gegevens bewaken zoals de reactietijden van uw toepassing, de prestaties van het laden van pagina's en de sessie-aantallen. Deze metrische gegevens op toepassingsniveau kunnen worden gebruikt voor het maken van regels voor automatisch schalen op een gedetailleerd en ingesloten niveau wanneer u regels activeert op basis van toepasbare inzichten die van invloed kunnen zijn op de ervaring van de klant.

Zie [Wat is Application Insights?](../azure-monitor/app/app-insights-overview.md) voor meer informatie over App Insights.


## <a name="scheduled-autoscale"></a>Gepland automatisch schalen
U kunt ook regels voor automatisch schalen maken op basis van planningen. Met deze regels op basis van een planning kunt u het aantal VM-exemplaren op vaste tijden automatisch schalen. Met regels op basis van prestaties kunnen de prestaties van de toepassing nadelig worden beïnvloed voordat de regels voor automatisch schalen worden geactiveerd en de nieuwe VM-exemplaren worden ingericht. Als u kunt inspelen op een dergelijke vraag, worden de extra VM-exemplaren ingericht en zijn ze gereed voor het extra gebruik door klanten en de vraag van de toepassing.

De volgende voorbeelden zijn scenario's die voordeel kunnen hebben van het gebruik van regels voor automatisch schalen op basis van een planning:

- Automatisch uitbreiden van het aantal VM-exemplaren aan het begin van de werkdag wanneer de vraag van de klant toeneemt. Aan het einde van de werkdag wordt het aantal VM-exemplaren automatisch ingeschaald om de resourcekosten 's nachts zo veel mogelijk te beperken wanneer het gebruik van de toepassing laag is.
- Als een afdeling een toepassing intensief gebruikt gedurende bepaalde perioden van de maand of de fiscale cyclus, wordt het aantal VM-exemplaren automatisch geschaald om te voldoen aan de extra vraag.
- Wanneer er sprake is van een marketingactiviteit, aanbieding of bijvoorbeeld kerstuitverkoop, kunt u het aantal VM-exemplaren automatisch schalen vóór dat de verwachte vraag van de klant realiteit wordt. 


## <a name="next-steps"></a>Volgende stappen
U kunt met een van de volgende hulpprogramma's regels voor automatisch schalen maken die gebruikmaken van hostgebaseerde metrische gegevens:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure-CLI](tutorial-autoscale-cli.md)
- [Azure-sjabloon](tutorial-autoscale-template.md)

In dit overzicht wordt uitgelegd hoe u regels voor automatisch schalen kunt gebruiken om horizontaal te schalen en het *aantal* VM-exemplaren in uw schaalset te vergroten of verkleinen. U kunt ook verticaal schalen om de *grootte* van VM-exemplaren te vergroten of verkleinen. Zie voor meer informatie [Vertical autoscale with virtual machine scale sets](virtual-machine-scale-sets-vertical-scale-reprovision.md) (Verticaal automatisch schalen met virtuele-machineschaalsets).

Zie [Manage a virtual machine scale set with Azure PowerShell](./virtual-machine-scale-sets-manage-powershell.md) (Virtuele-machineschaalset beheren met Azure PowerShell) voor meer informatie over het beheren van uw VM-exemplaren.

Als u wilt weten hoe u waarschuwingen kunt genereren wanneer uw regels voor automatisch schalen worden geactiveerd, raadpleegt u [Use autoscale actions to send email and webhook alert notifications in Azure Monitor](../azure-monitor/autoscale/autoscale-webhook-email.md) (Acties voor automatisch schalen gebruiken om waarschuwingen per e-mail of webhooks te verzenden in Azure Monitor). U kunt ook [controlelogboeken gebruiken om waarschuwingen per e-mail of webhooks te verzenden in Azure Monitor](../azure-monitor/alerts/alerts-log-webhook.md).
