---
title: Veelgestelde vragen over Azure Site Recovery bewaking
description: Krijg antwoorden op veelgestelde vragen over Azure Site Recovery bewaking, met behulp van ingebouwde bewaking en Azure Monitor (Log Analytics)
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.openlocfilehash: 715fd3a30dcc37a26b07f2ca1c6a0f2ce168a23a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581724"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Veelgestelde vragen over Site Recovery bewaking

In dit artikel vindt u antwoorden op veelgestelde vragen over het controleren van Azure [site Recovery](site-recovery-overview.md), het gebruik van inbouw site Recovery bewaking en Azure Monitor (log Analytics).

## <a name="general"></a>Algemeen

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>Hoe verschilt de RPO-waarde die is vastgelegd in het meest recente beschik bare herstel punt?

Site Recovery gebruikt een met meerdere stappen asynchroon proces voor het repliceren van machines naar Azure.

- In de Penultimate stap van replicatie worden recente wijzigingen op de computer, samen met meta gegevens, gekopieerd naar een logboek/cache-opslag account.
- Deze wijzigingen, samen met de tag waarmee een herstel bare punt wordt geïdentificeerd, worden geschreven naar het opslag account/de beheerde schijf in de doel regio.
- Site Recovery kan nu een herstel bare punt genereren voor de machine.
- Op dit moment is de RPO voldaan aan de wijzigingen die tot nu toe zijn geüpload naar het opslag account. Met andere woorden, de computer-RPO op dit moment is gelijk aan de tijd die is verstreken van de tijds tempel die overeenkomt met het herstel bare punt.
- Site Recovery kiest nu de geüploade gegevens uit het opslag account en past deze toe op de replica schijven die zijn gemaakt voor de machine.
- Site Recovery vervolgens een herstel punt gegenereerd en maakt dit punt beschikbaar voor herstel bij een failover.
- Het meest recente beschik bare herstel punt geeft dus de tijds tempel aan die overeenkomt met het laatste herstel punt dat al is verwerkt en toegepast op de replica schijven.


Een onjuiste systeem tijd op de replicerende bron machine, of op on-premises infrastructuur servers, scheef de berekende RPO-waarde. Voor nauw keurige RPO-rapportage moet u ervoor zorgen dat de systeem klok nauw keurig is op alle servers en machines.



## <a name="inbuilt-site-recovery-logging"></a>Inbouw Site Recovery logboek registratie


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>Waarom is het aantal VM'S in de weer gave kluis infrastructuur verschillend van het totale aantal dat wordt weer gegeven in gerepliceerde items?

De weer gave kluis infrastructuur bevindt zich in het bereik van replicatie scenario's. Alleen machines in het momenteel geselecteerde replicatie scenario zijn opgenomen in de telling voor de weer gave. Daarnaast tellen we alleen Vm's die zijn geconfigureerd voor replicatie naar Azure. Machines waarvoor failover is uitgevoerd, of computers die worden gerepliceerd naar een on-premises site, worden niet meegeteld in de weer gave.

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>Waarom wijkt het aantal gerepliceerde items in Essentials af van het totale aantal gerepliceerde items op het dash board?

Alleen machines waarvoor de initiële replicatie is voltooid, worden opgenomen in het aantal dat wordt weer gegeven in Essentials. Het totaal aantal gerepliceerde items omvat alle computers in de kluis, met inbegrip van de machines waarvoor de initiële replicatie momenteel wordt uitgevoerd.

## <a name="azure-monitor-logging"></a>Logboekregistratie van Azure Monitor


### <a name="how-often-does-site-recovery-send-resource-logs-to-azure-monitor-log"></a>Hoe vaak Site Recovery u resource logboeken naar Azure Monitor logboek verzenden? 

- AzureSiteRecoveryReplicationStats en AzureSiteRecoveryRecoveryPoints worden elke 15 minuten verzonden.  
- AzureSiteRecoveryReplicationDataUploadRate en AzureSiteRecoveryProtectedDiskDataChurn worden elke vijf minuten verzonden. 
- AzureSiteRecoveryJobs wordt verzonden naar de trigger en de voltooiing van een taak.
- AzureSiteRecoveryEvents wordt verzonden wanneer een gebeurtenis wordt gegenereerd. 
- AzureSiteRecoveryReplicatedItems wordt verzonden wanneer er omgevings wijzigingen zijn. Normaal gesp roken is de tijd voor het vernieuwen van gegevens 15 minuten na een wijziging. 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>Hoe lang worden gegevens opgeslagen in Azure Monitor logboeken? 

Bewaar periode is standaard 31 dagen. U kunt de periode verhogen in het gedeelte **gebruik en geschatte kosten** in de werk ruimte log Analytics. Klik op **gegevens retentie** en kies het bereik.

### <a name="whats-the-size-of-the-resource-logs"></a>Wat is de grootte van de bron logboeken? 

Normaal gesp roken is de grootte van een logboek 15-20 KB. 


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het bewaken met [site Recovery ingebouwde bewaking](site-recovery-monitor-and-troubleshoot.md)of [Azure monitor](monitor-log-analytics.md).


