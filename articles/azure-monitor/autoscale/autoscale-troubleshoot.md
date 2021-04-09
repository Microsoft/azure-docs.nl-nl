---
title: Problemen oplossen met Azure automatisch schalen
description: Het bijhouden van problemen met Azure-automatisch schalen die wordt gebruikt in Service Fabric, Virtual Machines, Web Apps en Cloud Services.
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: a0fed6c2d06edcb2c9eb8d715feb0ef6c6ade46f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711396"
---
# <a name="troubleshooting-azure-autoscale"></a>Problemen oplossen met Azure automatisch schalen
 
Azure Monitor automatisch schalen helpt u het juiste aantal resources te laten werken om de belasting van uw toepassing af te handelen. U kunt hiermee resources toevoegen voor het afhandelen van toename van de belasting en bespaart u geld door resources te verwijderen die niet actief zijn. U kunt schalen op basis van een planning, een vaste datum/tijd of de metrische gegevens van de resource die u kiest. Zie [overzicht van automatisch schalen](autoscale-overview.md)voor meer informatie.

De service voor automatisch schalen biedt u metrische gegevens en Logboeken om te begrijpen welke schaal acties hebben plaatsgevonden en de evaluatie van de voor waarden die naar die acties hebben geleid. U vindt antwoorden op vragen zoals:

- Waarom is mijn service uitgeschaald of in?
- Waarom is mijn service niet geschaald?
- Waarom is een actie voor automatisch schalen mislukt?
- Waarom neemt een actie voor automatisch schalen de tijd om te schalen?
  
## <a name="autoscale-metrics"></a>Metrische gegevens automatisch schalen

Automatisch schalen biedt u [vier metrische gegevens](../essentials/metrics-supported.md#microsoftinsightsautoscalesettings) om inzicht te krijgen in de werking ervan. 

- **Waargenomen metrische waarde** : de waarde van de metrische gegevens die u hebt gekozen om de schaal actie op te halen, zoals wordt weer gegeven of berekend door de engine voor automatisch schalen. Omdat één instelling voor automatisch schalen meerdere regels en daarom meerdere metrische bronnen kan hebben, kunt u filteren met metrische bronnen als een dimensie.
- **Drempel waarde voor metrische gegevens** : de drempel waarde die u hebt ingesteld om de schaal actie uit te voeren. Omdat één instelling voor automatisch schalen meerdere regels en daarom meerdere metrische bronnen kan hebben, kunt u filteren met metrische regel als een dimensie.
- **Waargenomen capaciteit** : het actieve aantal exemplaren van de doel resource, zoals wordt gezien door de engine voor automatisch schalen.
- **Gestarte schaalacties**: het aantal acties voor uit- en inschalen dat door de engine voor automatisch schalen wordt gestart. U kunt filteren op uitschalen versus schalen in acties.

U kunt de [Metrics Explorer](../essentials/metrics-getting-started.md) gebruiken om de bovenstaande metrische gegevens op één plek te diagram. In de grafiek moet het volgende worden weer gegeven:

  - de werkelijke metrische waarde
  - de metrische gegevens zoals weer gegeven/berekend door de engine voor automatisch schalen
  - de drempel waarde voor een schaal actie
  - de wijziging in de capaciteit 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>Voor beeld 1: een eenvoudige regel voor automatisch schalen analyseren 

We hebben een eenvoudige instelling voor automatisch schalen voor een schaalset voor virtuele machines die:

- Hiermee wordt uitgeschaald wanneer het gemiddelde CPU-percentage van een set langer is dan 70% gedurende tien minuten 
- Hiermee wordt geschaald wanneer het CPU-percentage van de set meer dan 10 minuten minder dan 5% is. 

Laten we de metrische gegevens van de service voor automatisch schalen eens bekijken.
 
![Scherm afbeelding toont een CPU-voor beeld van een schaalset van virtuele machines.](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![CPU-voor beeld van schaalset voor virtuele machines](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***Afbeelding 1a: percentage CPU-metriek voor schaal sets voor virtuele machines en de waargenomen metrische waarde voor metrische gegevens voor automatisch schalen***

![Metrische drempel waarde en waargenomen capaciteit](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***Afbeelding 1B-metrische drempel waarde en waargenomen capaciteit***

In afbeelding 1B is de **metrische drempel waarde** (licht blauwe lijn) voor de scale-out-regel 70.  De **waargenomen capaciteit** (donker blauwe lijn) toont het aantal actieve instanties, dat nu 3 is. 

> [!NOTE]
> U moet de **drempel waarde voor metrische gegevens** filteren met de regel dimensie scale-out trigger regel (toename) om de drempel waarde voor uitschalen en de schaal in regel (afname) te bekijken. 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>Voor beeld 2: Geavanceerd automatisch schalen voor een schaalset voor virtuele machines

We hebben een instelling voor automatisch schalen waarmee een bron van een schaalset voor virtuele machines kan worden uitgeschaald op basis van de eigen metrische **uitgaande stromen**. U ziet dat de optie **metrische gegevens delen per exemplaar aantal** voor de metrische drempel waarde is ingeschakeld. 

De regel voor de schaal actie is: 

Als de waarde van de **uitgaande stroom per instantie** groter is dan 10, moet de service voor automatisch schalen worden uitgeschaald op 1 exemplaar. 

In dit geval wordt de geobserveerde metrische waarde van de engine voor automatisch schalen berekend als de werkelijke metrische waarde gedeeld door het aantal exemplaren. Als de waargenomen metrische waarde lager is dan de drempelwaarde, wordt er geen uitschaal actie gestart. 
 
![Scherm afbeelding toont de pagina gemiddelde uitgaande stromen, met een voor beeld van een schaalset met metrische gegevens grafieken van de virtuele machine.](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![Voor beeld van grafieken met metrische gegevens voor de schaalset voor virtuele machines](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***Afbeelding 2: voor beeld van de grafieken schaal sets voor virtuele machines automatisch schalen***

In afbeelding 2 ziet u twee metrische grafieken. 

In de grafiek bovenaan ziet u de werkelijke waarde van de metrische gegevens van de **uitgaande stromen** . De werkelijke waarde is 6. 

In de grafiek onderaan ziet u een aantal waarden. 
 - De **waargenomen metrische waarde** (licht blauw) is 3, omdat er 2 actieve instanties zijn en 6 gedeeld door 2 3. 
 - De **waargenomen capaciteit** (paars) toont het aantal instanties dat wordt weer gegeven door de engine voor automatisch schalen. 
 - De **metrische drempel** waarde (licht groen) is ingesteld op 10. 

Als er meerdere schaal actie regels zijn, kunt u splitsen of de optie **filter toevoegen** gebruiken in de grafiek metrische gegevens Verkenner om de metrische gegevens te bekijken op basis van een specifieke bron of regel. Zie [geavanceerde functies van metrische grafieken-splitsen](../essentials/metrics-charts.md#apply-splitting) voor meer informatie over het splitsen van een metrische grafiek

## <a name="example-3---understanding-autoscale-events"></a>Voor beeld 3: informatie over automatisch schalen

In het scherm instelling voor automatisch schalen gaat u naar het tabblad **uitvoerings geschiedenis** om de meest recente schaal acties weer te geven. Op het tabblad ziet u ook de wijziging van de **waargenomen capaciteit** gedurende een bepaalde periode. Voor meer informatie over alle acties voor automatisch schalen, waaronder bewerkingen zoals het bijwerken/verwijderen van instellingen voor automatisch schalen, bekijkt u het activiteiten logboek en filtert u op automatisch schalen.

![Geschiedenis van instellingen voor automatisch schalen uitvoeren](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>Resource logboeken automatisch schalen

Net als elke andere Azure-resource biedt de service voor automatisch schalen [resource logboeken](../essentials/platform-logs-overview.md). Er zijn twee soorten logboeken.

- **Evaluaties automatisch schalen** : de engine voor automatisch schalen legt logboek vermeldingen voor elke evaluatie van één voor waarde vast telkens wanneer er een controle wordt gedaan.  De vermelding bevat details over de waargenomen waarden van de metrische gegevens, de regels die zijn geëvalueerd en als de evaluatie een schaal actie heeft veroorzaakt.

- **Schaal acties** voor automatisch schalen: de engine registreert actie gebeurtenissen die zijn geïnitieerd door de service voor automatisch schalen en de resultaten van deze schalen (geslaagd, mislukt, en hoeveel schalen er is opgetreden, zoals gezien door de service voor automatisch schalen).

Net als bij elke Azure Monitor ondersteunde service kunt u [Diagnostische instellingen](../essentials/diagnostic-settings.md) gebruiken om deze logboeken te routeren:

- naar uw Log Analytics-werk ruimte voor gedetailleerde analyse
- Event Hubs en vervolgens naar niet-Azure-hulpprogram ma's
- naar uw Azure Storage-account voor archivering  

![Diagnostische instellingen automatisch schalen](media/autoscale-troubleshoot/diagnostic-settings.png)

In de vorige afbeelding ziet u de Azure Portal Diagnostische instellingen voor automatisch schalen. Hier kunt u het tabblad Diagnostische/resource logs selecteren en logboek verzameling en route ring inschakelen. U kunt ook dezelfde actie uitvoeren met behulp van REST API, CLI, Power shell, Resource Manager-sjablonen voor Diagnostische instellingen door het resource type te kiezen als *micro soft. Insights/AutoscaleSettings*. 

## <a name="troubleshooting-using-autoscale-logs"></a>Problemen oplossen met Logboeken voor automatisch schalen 

Voor de beste probleemoplossings ervaring raden wij u aan om uw logboeken te routeren naar Azure Monitor logs (Log Analytics) via een werk ruimte wanneer u de instelling voor automatisch schalen maakt. Dit proces wordt weer gegeven in de afbeelding in de vorige sectie. U kunt de evaluaties en schaal acties beter valideren met behulp van Log Analytics.

Zodra u de logboeken voor automatisch schalen hebt geconfigureerd om te worden verzonden naar de Log Analytics-werk ruimte, kunt u de volgende query's uitvoeren om de logboeken te controleren. 

Om aan de slag te gaan, kunt u deze query proberen om de meest recente evaluatie logboeken voor automatisch schalen weer te geven:

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

Of voer de volgende query uit om de meest recente schaal actie logboeken weer te geven:

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

Gebruik de volgende secties voor deze vragen. 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>Er is een schaal actie opgetreden die niet verwacht

Voer eerst de query voor schaal actie uit om de schaal actie te vinden waarin u bent geïnteresseerd. Als het de meest recente schaal actie is, gebruikt u de volgende query:

```Kusto
AutoscaleScaleActionsLog
| take 1
```

Selecteer het veld CorrelationId in het logboek met schaal acties. Gebruik de CorrelationId om het juiste evaluatie logboek te vinden. Als de onderstaande query wordt uitgevoerd, worden alle regels en voor waarden weer gegeven die het resultaat zijn van die schaal actie.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>Welk profiel heeft een schaal actie veroorzaakt?

Er is een geschaalde actie opgetreden, maar u hebt overlappende regels en profielen en u moet bijhouden wat de actie heeft veroorzaakt. 

Zoek de correlatie van de schaal actie (zoals beschreven in voor beeld 1) en voer vervolgens de query uit op evaluatie logboeken voor meer informatie over het profiel.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

De volledige profiel evaluatie kan ook beter worden begrepen met behulp van de volgende query

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>Er is geen schaal actie uitgevoerd

Er werd een schaal actie verwacht, maar deze is niet uitgevoerd. Er zijn mogelijk geen schaal actie gebeurtenissen of Logboeken.

Controleer de metrische gegevens voor automatisch schalen als u een schaal regel op basis van metrische gegevens gebruikt. Het is mogelijk dat de **waargenomen metrische waarde** of de **waargenomen capaciteit** niet naar verwachting is en dat de schaal regel niet is geactiveerd. U ziet nog wel evaluaties, maar geen scale-out-regel. Het is ook mogelijk dat de uitgekoelde tijd een schaal actie heeft behouden. 

 Bekijk de logboeken voor automatisch schalen tijdens de periode waarin u werd verwacht dat de schaal actie wordt uitgevoerd. Bekijk alle evaluaties en waarom het heeft besloten geen schaal actie te activeren.


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>Schaal actie mislukt

Er is mogelijk een situatie waarbij de schaal actie door de service voor automatisch schalen is uitgevoerd, maar het systeem heeft besloten de schaal actie niet te schalen of niet is gelukt. Gebruik deze query om de uitgevallen schaal acties te vinden.

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

Maak waarschuwings regels om op de hoogte te worden gesteld van acties voor automatisch schalen of fouten. U kunt ook waarschuwings regels maken om een melding te ontvangen over gebeurtenissen voor automatisch schalen.

## <a name="schema-of-autoscale-resource-logs"></a>Schema voor het automatisch schalen van resource logboeken

Zie [resource logboeken automatisch schalen](autoscale-resource-log-schema.md) voor meer informatie

## <a name="next-steps"></a>Volgende stappen
Lees informatie over [Aanbevolen procedures voor automatisch schalen](autoscale-best-practices.md).