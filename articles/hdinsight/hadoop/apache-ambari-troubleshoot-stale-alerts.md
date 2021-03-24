---
title: Verlopen waarschuwingen voor Apache Ambari in azure HDInsight
description: Discussie en analyse van mogelijke oorzaken en oplossingen voor Ambari verouderde waarschuwingen in HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 22985f295e504a2d73015f1dc96064f89be47bd7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866927"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Scenario: Apache Ambari verouderde waarschuwingen in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

In de Apache Ambari-gebruikers interface ziet u mogelijk een waarschuwing als volgt:

:::image type="content" source="./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png" alt-text="Voor beeld van een verlopen Ambari-waarschuwing voor Apache" border="true":::

## <a name="cause"></a>Oorzaak

Ambari agents bewaken voortdurend de status van veel resources. *Waarschuwingen* kunnen worden geconfigureerd om u te informeren of specifieke cluster eigenschappen binnen de vooraf ingestelde drempel waarden vallen. Nadat elke resource controle is uitgevoerd, rapporteren Ambari-agents de status terug naar de Ambari-server en activeren ze een waarschuwing als er wordt voldaan aan de waarschuwings voorwaarde. Als een waarschuwing niet wordt gecontroleerd volgens het interval in het waarschuwings profiel, activeert de server een waarschuwing over *verlopen waarschuwingen van Ambari server* .

Er zijn verschillende redenen waarom een status controle niet kan worden uitgevoerd met het gedefinieerde interval:

* De hosts zijn zwaar gebruik (hoog CPU-gebruik), zodat de Ambari-agent niet genoeg systeem bronnen kan verkrijgen om de waarschuwingen op tijd uit te voeren.

* Het cluster is bezig met het uitvoeren van veel taken of services tijdens een lange belasting periode.

* Een klein aantal hosts in het cluster fungeert als host voor veel onderdelen en is daarom vereist om veel waarschuwingen uit te voeren. Als het aantal onderdelen groot is, kunnen waarschuwings taken de geplande intervallen missen.

## <a name="resolution"></a>Oplossing

Probeer de volgende methoden om problemen met Ambari verlopen waarschuwingen op te lossen.

### <a name="increase-the-alert-interval-time"></a>De tijd van waarschuwings interval verhogen

U kunt de waarde van een afzonderlijk waarschuwings interval verhogen op basis van de reactie tijd van uw cluster en laden:

1. Selecteer in de Apache Ambari-gebruikers interface het tabblad **waarschuwingen** .
1. Selecteer de naam van de waarschuwings definitie die u wilt.
1. Selecteer in de definitie **bewerken**.
1. Verhoog de waarde voor het **controle-interval** en selecteer vervolgens **Opslaan**.

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>De waarschuwings interval tijd voor Ambari-server waarschuwingen verhogen

1. Selecteer in de Apache Ambari-gebruikers interface het tabblad **waarschuwingen** .
1. Selecteer in de vervolg keuzelijst **groepen** de optie **AMBARI standaard**.
1. Selecteer de waarschuwing **Ambari server waarschuwingen** .
1. Selecteer in de definitie **bewerken**.
1. Verhoog de waarde voor het **controle-interval** .
1. Verhoog de waarde voor **interval vermenigvuldiger** en selecteer vervolgens **Opslaan**.

### <a name="disable-and-reenable-the-alert"></a>De waarschuwing uitschakelen en opnieuw inschakelen

Als u een verouderde waarschuwing wilt verwijderen, schakelt u deze opnieuw in:

1. Selecteer in de Apache Ambari-gebruikers interface het tabblad **waarschuwingen** .
1. Selecteer de naam van de waarschuwings definitie die u wilt.
1. Selecteer de optie **ingeschakeld** in het uiterst rechtse deel van de gebruikers interface van de definitie.
1. Selecteer in het pop-upvenster **bevestigen** de optie **uitschakelen**.
1. Wacht een paar seconden totdat alle waarschuwingen die op de pagina worden weer gegeven, moeten worden gewist.
1. Selecteer in de definitie **uitgeschakeld** in het uiterst rechtse deel van de gebruikers interface.
1. Selecteer in het pop-upvenster **bevestigen** de optie **inschakelen**.

### <a name="increase-the-alert-grace-period"></a>De respijt periode van de waarschuwing verhogen

Er is een respijt periode voordat een Ambari-agent rapporteert dat een geconfigureerde waarschuwing het schema mist. Als de waarschuwing de geplande tijd niet heeft gemist, maar wel binnen de respijt periode is uitgevoerd, wordt de verouderde waarschuwing niet gegenereerd.

De standaard `alert_grace_period` waarde is 5 seconden. U kunt deze instelling configureren in/etc/ambari-agent/conf/ambari-agent.ini. Voor hosts waarop verouderde waarschuwingen met regel matige tussen pozen optreden, kunt u proberen de waarde te verhogen naar 10. Start vervolgens de Ambari-agent opnieuw.

## <a name="next-steps"></a>Volgende stappen

Als uw probleem hier niet is vermeld of als u het niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts bij [Azure-Community-ondersteuning](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) op Twitter. Dit is het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. De Azure-community wordt verbonden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Als u deze wilt weer geven, selecteert u Help (**?**) in het menu van de portal of opent u het deel venster **Help + ondersteuning** . Zie [een Azure-ondersteunings aanvraag maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. 

  Ondersteuning voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement. Technische ondersteuning is beschikbaar via de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).