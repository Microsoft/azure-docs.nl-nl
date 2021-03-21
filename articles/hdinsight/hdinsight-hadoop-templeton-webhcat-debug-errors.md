---
title: WebHCat-fouten in HDInsight-Azure begrijpen en oplossen
description: Meer informatie over veelvoorkomende fouten die worden geretourneerd door WebHCat in HDInsight en hoe u deze kunt oplossen.
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: ce7a21a2b5401c68c6b58bc934fb20b0225ba8ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945456"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Fouten die vanuit WebHCat zijn ontvangen, begrijpen en oplossen op HDInsight

Meer informatie over fouten die zijn ontvangen bij het gebruik van WebHCat met HDInsight en hoe u deze kunt oplossen. WebHCat wordt intern gebruikt door Program ma's aan de client zijde zoals Azure PowerShell en de Data Lake-Hulpprogram Ma's voor Visual Studio.

## <a name="what-is-webhcat"></a>Wat is WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) is een rest API voor [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), een tabel en een opslag beheer-laag voor Apache Hadoop. WebHCat is standaard ingeschakeld op HDInsight-clusters en wordt door diverse hulpprogram ma's gebruikt om taken te verzenden, de taak status op te halen, enzovoort, zonder dat u zich hoeft aan te melden bij het cluster.

## <a name="modifying-configuration"></a>Configuratie wijzigen

Verschillende van de fouten die in dit document worden vermeld, treden op omdat een geconfigureerd maximum is overschreden. Wanneer de oplossings stap vermeldt dat u een waarde kunt wijzigen, gebruikt u Apache Ambari (Web of REST API) om de waarde te wijzigen. Zie [HDInsight beheren met Apache Ambari](hdinsight-hadoop-manage-ambari.md) voor meer informatie.

### <a name="default-configuration"></a>Standaardconfiguratie

Als de volgende standaard waarden worden overschreden, kan dit de prestaties van de WebHCat verminderen of fouten veroorzaken:

| Instelling | Wat het doet | Standaardwaarde |
| --- | --- | --- |
| [garens. scheduler. capacity. maximum-toepassingen][maximum-applications] |Het maximum aantal taken dat gelijktijdig actief kan zijn (in behandeling of uitvoering) |10.000 |
| [templeton.exec. Max-procs][max-procs] |Het maximum aantal aanvragen dat gelijktijdig kan worden verwerkt |20 |
| [MapReduce. jobhistory. max-age-MS][max-age-ms] |Het aantal dagen dat de taak geschiedenis wordt bewaard |7 dagen |

## <a name="too-many-requests"></a>Te veel aanvragen

**HTTP-status code**: 429

| Oorzaak | Oplossing |
| --- | --- |
| U hebt het maximum aantal gelijktijdige aanvragen dat wordt aangeboden door WebHCat per minuut overschreden (standaard 20) |Verminder uw werk belasting om ervoor te zorgen dat u niet meer dan het maximum aantal gelijktijdige aanvragen indient of de limiet voor gelijktijdige aanvragen wilt verhogen door deze te wijzigen `templeton.exec.max-procs` . Zie Modify [Configuration (configuratie wijzigen](#modifying-configuration) ) voor meer informatie. |

## <a name="server-unavailable"></a>De server is niet beschikbaar

**HTTP-status code**: 503

| Oorzaak | Oplossing |
| --- | --- |
| Deze status code treedt meestal op tijdens een failover tussen de primaire en secundaire hoofd knooppunt voor het cluster |Wacht twee minuten en voer de bewerking opnieuw uit |

## <a name="bad-request-content-could-not-find-job"></a>Inhoud van ongeldige aanvraag: de taak is niet gevonden

**HTTP-status code**: 400

| Oorzaak | Oplossing |
| --- | --- |
| De taak Details zijn opgeschoond door de taak geschiedenis opschoning |De standaard Bewaar periode voor de taak geschiedenis is 7 dagen. De standaard Bewaar periode kan worden gewijzigd door te wijzigen `mapreduce.jobhistory.max-age-ms` . Zie Modify [Configuration (configuratie wijzigen](#modifying-configuration) ) voor meer informatie. |
| De taak is beëindigd vanwege een failover |Taak opnieuw verzenden gedurende Maxi maal twee minuten |
| Er is een ongeldige taak-ID gebruikt |Controleren of de taak-ID juist is |

## <a name="bad-gateway"></a>Ongeldige gateway

**HTTP-status code**: 502

| Oorzaak | Oplossing |
| --- | --- |
| Interne garbagecollection wordt uitgevoerd in het WebHCat-proces |Wacht tot de garbagecollection is voltooid of start de WebHCat-service opnieuw |
| Time-out tijdens het wachten op een reactie van de Resource Manager-service. Deze fout kan optreden wanneer het aantal actieve toepassingen het geconfigureerde maximum overschrijdt (standaard 10.000) |Wacht totdat de taken die momenteel worden uitgevoerd, zijn voltooid of verg root de gelijktijdige taak limiet door te wijzigen `yarn.scheduler.capacity.maximum-applications` . Zie de sectie [configuratie wijzigen](#modifying-configuration) voor meer informatie. |
| Er wordt geprobeerd om alle taken op te halen via de aanroep [Get/Jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) terwijl `Fields` is ingesteld op `*` |Niet *alle* taak Details ophalen. Gebruik in plaats daarvan `jobid` om details op te halen voor taken die groter zijn dan een bepaalde taak-id. Of gebruik niet `Fields` |
| De WebHCat-service is niet beschikbaar tijdens de hoofd knooppunt-failover |Wacht twee minuten en voer de bewerking opnieuw uit |
| Er zijn meer dan 500 taken die moeten worden verzonden via WebHCat |Wachten tot de momenteel wachtende taken zijn voltooid voordat u meer taken hebt verzonden |

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]

[maximum-applications]: https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml