---
title: Resource logboeken verzamelen & analyseren
description: Registreer en analyseer bron logboek gebeurtenissen voor Azure Container Registry zoals verificatie, installatie kopie push en installatie kopie ophalen.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 952f239e94df6b3b21317985f56d3d7a999813fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572003"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Azure Container Registry logboeken voor diagnostische evaluatie en controle

In dit artikel wordt uitgelegd hoe u logboek gegevens kunt verzamelen voor een Azure container Registry met behulp van de functies van [Azure monitor](../azure-monitor/overview.md). Azure Monitor verzamelt [bron logboeken](../azure-monitor/essentials/platform-logs-overview.md) (voorheen *Diagnostische logboeken* genoemd) voor door gebruikers gestuurde gebeurtenissen in het REGI ster. Verzamelen en gebruiken van deze gegevens om te voldoen aan de behoeften, zoals:

* Register verificatie gebeurtenissen controleren om te zorgen voor beveiliging en naleving 

* Geef een compleet activiteiten spoor op register artefacten, zoals pull-en pull-gebeurtenissen, zodat u operationele problemen met uw REGI ster kunt vaststellen 

Het verzamelen van bron logboek gegevens met behulp van Azure Monitor kan extra kosten in rekening worden gebracht. Zie [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/). 

## <a name="repository-events"></a>Opslagplaats gebeurtenissen

De volgende gebeurtenissen op opslagplaats niveau voor installatie kopieën en andere artefacten worden momenteel geregistreerd:

* **Push**
* **Citat**
* **Tag**
* **Verwijderen** (inclusief opslag plaats gebeurtenissen verwijderen)
* **Label leegmaken** en **manifest leegmaken**

> [!NOTE]
> Het opschonen van gebeurtenissen wordt alleen vastgelegd als er een [Bewaar beleid](container-registry-retention-policy.md) voor het REGI ster is geconfigureerd.

## <a name="registry-resource-logs"></a>Register bron logboeken

Bron logboeken bevatten gegevens die worden verzonden door Azure-resources die hun interne bewerking beschrijven. Voor een Azure container Registry bevatten de logboeken verificatie en gebeurtenissen op opslagplaats niveau die in de volgende tabellen zijn opgeslagen. 

* **ContainerRegistryLoginEvents**  -register verificatie gebeurtenissen en status, met inbegrip van de binnenkomende identiteit en het IP-adres
* **ContainerRegistryRepositoryEvents** : bewerkingen zoals pushen en pullen voor installatie kopieën en andere artefacten in register opslagplaatsen
* **AzureMetrics**  -  [Metrische gegevens van container register](../azure-monitor/essentials/metrics-supported.md#microsoftcontainerregistryregistries) , zoals geaggregeerde push-en pull-aantallen.

Voor bewerkingen zijn logboek gegevens inclusief:
  * Status geslaagd of mislukt
  * Begin-en eind tijd tempels

Naast resource Logboeken biedt Azure een [activiteiten logboek](../azure-monitor/essentials/platform-logs-overview.md), een record met Azure-beheer gebeurtenissen op abonnements niveau, zoals het maken of verwijderen van een container register.

## <a name="enable-collection-of-resource-logs"></a>Verzamelen van resource logboeken inschakelen

Het verzamelen van bron logboeken voor een container register is standaard niet ingeschakeld. Schakel de diagnostische instellingen expliciet in voor elk REGI ster dat u wilt bewaken. Zie [Diagnostische instelling maken voor het verzamelen van platform logboeken en metrische gegevens in azure](../azure-monitor/essentials/diagnostic-settings.md)voor opties voor het inschakelen van diagnostische instellingen.

Als u bijvoorbeeld Logboeken en metrische gegevens voor een container register in bijna realtime in Azure Monitor wilt weer geven, verzamelt u de bron Logboeken in een Log Analytics-werk ruimte. Als u deze diagnostische instelling wilt inschakelen met behulp van de Azure Portal:

1. Als u nog geen werk ruimte hebt, maakt u een werk ruimte met behulp van de [Azure Portal](../azure-monitor/logs/quick-create-workspace.md). Om latentie bij het verzamelen van gegevens te minimaliseren, moet u ervoor zorgen dat de werk ruimte zich in **dezelfde regio** bevindt als uw container register.
1. Selecteer in de portal het REGI ster en selecteer **bewaking > Diagnostische instellingen > diagnostische instelling toevoegen**.
1. Voer een naam in voor de instelling en selecteer **verzenden naar log Analytics**.
1. Selecteer de werk ruimte voor de diagnostische logboeken van het REGI ster.
1. Selecteer de logboek gegevens die u wilt verzamelen en klik op **Opslaan**.

De volgende afbeelding toont het maken van een diagnostische instelling voor een REGI ster met behulp van de portal.

![Diagnostische instellingen inschakelen](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Verzamel alleen de gegevens die u nodig hebt, berekenings kosten en uw bewakings behoeften. Als u bijvoorbeeld alleen verificatie gebeurtenissen wilt controleren, selecteert u alleen het **ContainerRegistryLoginEvents** -logboek. 

## <a name="view-data-in-azure-monitor"></a>Gegevens in Azure Monitor weer geven

Nadat u het verzamelen van Diagnostische logboeken in Log Analytics hebt ingeschakeld, kan het enkele minuten duren voordat gegevens worden weer gegeven in Azure Monitor. Als u de gegevens in de portal wilt weer geven, selecteert u het REGI ster en selecteert u **> logboeken controleren**. Selecteer een van de tabellen die gegevens voor het REGI ster bevatten. 

Voer query's uit om de gegevens weer te geven. Er worden verschillende voorbeeldquery's gegeven of u kunt uw eigen query's uitvoeren. Met de volgende query worden bijvoorbeeld de meest recente 24 uur aan gegevens opgehaald uit de **ContainerRegistryRepositoryEvents** -tabel:

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

De volgende afbeelding toont voorbeeld uitvoer:

![Query uitvoeren op logboekgegevens](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

Zie [aan de slag met Azure Monitor Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)of probeer de log Analytics- [demo omgeving](https://portal.loganalytics.io/demo)voor een zelf studie over het gebruik van log Analytics in het Azure Portal. 

Zie [overzicht van logboek query's in azure monitor](../azure-monitor/logs/log-query-overview.md)voor meer informatie over logboek query's.

## <a name="query-examples"></a>Voorbeelden van query's

### <a name="error-events-from-the-last-hour"></a>Fout gebeurtenissen van het afgelopen uur

```Kusto
union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
| where TimeGenerated > ago(1h)
| where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
```

### <a name="100-most-recent-registry-events"></a>100 meest recente register gebeurtenissen

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer, OperationName, Identity, Repository, DurationMs, Region , ResultType
```

### <a name="identity-of-user-or-object-that-deleted-repository"></a>Identiteit van de gebruiker of het object dat de opslag plaats heeft verwijderd

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Delete"
| project LoginServer, OperationName, Repository, Identity, CallerIpAddress
```

### <a name="identity-of-user-or-object-that-deleted-tag"></a>Identiteit van de gebruiker of het object dat de tag heeft verwijderd

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Untag"
| project LoginServer, OperationName, Repository, Tag, Identity, CallerIpAddress
```

### <a name="repository-level-operation-failures"></a>Bewerkings fouten op opslagplaats niveau

```kusto
ContainerRegistryRepositoryEvents 
| where ResultDescription contains "40"
| project TimeGenerated, OperationName, Repository, Tag, ResultDescription
```

### <a name="registry-authentication-failures"></a>Register authenticatie fouten

```kusto
ContainerRegistryLoginEvents 
| where ResultDescription != "200"
| project TimeGenerated, Identity, CallerIpAddress, ResultDescription
```


## <a name="additional-log-destinations"></a>Aanvullende logboek doelen

Naast het verzenden van de logboeken naar Log Analytics, of als alternatief, is het een gemeen schappelijke scenario om een Azure Storage account te selecteren als een logboek bestemming. Als u Logboeken in Azure Storage wilt archiveren, maakt u een opslag account voordat u archiveren via de diagnostische instellingen inschakelt.

U kunt ook diagnostische logboek gebeurtenissen streamen naar een [Azure Event hub](../event-hubs/event-hubs-about.md). Event Hubs kan miljoenen gebeurtenissen per seconde opnemen, die u vervolgens kunt transformeren en opslaan met behulp van een real-time analyse provider. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het gebruik van [log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) en het maken van [logboek query's](../azure-monitor/logs/get-started-queries.md).
* Zie [overzicht van Azure platform-logboeken](../azure-monitor/essentials/platform-logs-overview.md) voor meer informatie over platform logboeken die beschikbaar zijn op verschillende lagen van Azure.