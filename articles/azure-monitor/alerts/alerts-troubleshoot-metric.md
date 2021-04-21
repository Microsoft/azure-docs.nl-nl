---
title: Problemen met metrische waarschuwingen van Azure oplossen
description: Veelvoorkomende problemen Azure Monitor metrische waarschuwingen en mogelijke oplossingen.
author: harelbr
ms.author: harelbr
ms.topic: troubleshooting
ms.date: 04/12/2021
ms.openlocfilehash: fc9af94b07add5728201baaa8fa6992728a60a8c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786005"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Problemen met metrische waarschuwingen in Azure Monitor oplossen 

In dit artikel worden veelvoorkomende problemen in Azure Monitor [waarschuwingen voor metrische gegevens en](alerts-metric-overview.md) hoe u deze kunt oplossen.

Azure Monitor waarschuwingen waarschuwen u proactief wanneer er belangrijke voorwaarden in uw bewakingsgegevens worden gevonden. Ze bieden u de mogelijkheid om problemen te identificeren en op te lossen voordat de gebruikers van uw systeem deze merken. Zie Overzicht van waarschuwingen in Microsoft Azure voor [meer Microsoft Azure.](./alerts-overview.md)

## <a name="metric-alert-should-have-fired-but-didnt"></a>Waarschuwing voor metrische gegevens had moeten worden afgelost, maar niet 

Als u denkt dat een waarschuwing voor metrische gegevens had moeten worden gefingeerd, maar deze niet is ge fireed en niet is gevonden in de Azure Portal, probeert u de volgende stappen:

1. **Configuratie:** controleer de configuratie van de metrische waarschuwingsregel om te controleren of deze correct is geconfigureerd:
    - Controleer of **het Aggregatietype** en **aggregatiegranulatie (periode)** zijn geconfigureerd zoals verwacht. **Het aggregatietype** bepaalt hoe metrische waarden worden geaggregeerd (hier meer [informatie)](../essentials/metrics-aggregation-explained.md#aggregation-types)en **Aggregatiegranulatie (periode)** bepaalt hoe ver terug de evaluatie de metrische waarden samenvoegt telkens wanneer de waarschuwingsregel wordt uitgevoerd.
    -  Controleer of de **Drempelwaarde** of **Gevoeligheid** is geconfigureerd zoals verwacht.
    - Voor een **waarschuwingsregel** die gebruikmaakt van dynamische drempelwaarden, controleert u of  er geavanceerde instellingen zijn geconfigureerd. Met Aantal schendingen kunnen waarschuwingen worden gefilterd en Gegevens negeren vóór van invloed kunnen zijn op de manier waarop de drempelwaarden worden berekend.

       > [!NOTE] 
       > Dynamische drempelwaarden vereisen ten minste 3 dagen en 30 metrische steekproeven voordat ze actief worden.

2. **Niet gefingeerd, maar** geen melding: bekijk de lijst [met geseede](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) waarschuwingen om te zien of u de waarschuwing kunt vinden. Als u de waarschuwing in de lijst kunt zien, maar een probleem hebt met een aantal acties of meldingen, kunt u hier meer [informatie bekijken.](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)

3. **Al actief:** controleer of er al een waarschuwing is gemaakt voor de metrische tijdreeks die u verwacht een waarschuwing te ontvangen. Metrische waarschuwingen zijn stateful, wat betekent dat wanneer een waarschuwing wordt geactiveerd voor een specifieke metrische tijdreeks, extra waarschuwingen over die tijdreeksen pas worden geactiveerd zodra het probleem niet meer wordt waargenomen. Deze ontwerpkeuze vermindert ruis. De waarschuwing wordt automatisch opgelost wanneer drie opeenvolgende evaluaties niet aan de waarschuwingsvoorwaarde worden voldaan.

4. **Gebruikte dimensies:** als u een aantal dimensiewaarden voor een metrische waarde hebt [geselecteerd,](./alerts-metric-overview.md#using-dimensions)controleert de waarschuwingsregel elke afzonderlijke metrische tijdreeks (zoals gedefinieerd door de combinatie van dimensiewaarden) op een drempelwaardeschending. Als u ook de geaggregeerde metrische tijdreeks wilt bewaken (zonder dat er dimensies zijn geselecteerd), configureert u een extra waarschuwingsregel voor de metrische waarde zonder dimensies te selecteren.

5. **Aggregatie en tijdgranulatie:** als u de metrische gegevens visualiseert met grafieken [met](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)metrische gegevens, controleert u het volgende:
    * De geselecteerde **aggregatie** in de grafiek met metrische gegevens is hetzelfde als **het aggregatietype** in de waarschuwingsregel
    * De geselecteerde **tijdgranulatie** is hetzelfde als de **Aggregatiegranulatie (periode)** in de waarschuwingsregel (en is niet ingesteld op Automatisch)

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Waarschuwing voor metrische gegevens is op een niet-metrische gegevens afgekomen

Als u denkt dat de waarschuwing voor metrische gegevens niet moest worden gelost, maar dit wel is gedaan, helpen de volgende stappen mogelijk om het probleem op te lossen.

1. Bekijk de lijst [met waarschuwingen om](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) de waarschuwing te vinden en klik erop om de details ervan weer te geven. Bekijk de informatie onder Waarom is deze waarschuwing **geactiveerd?** om de  grafiek met metrische gegevens, **de** metrische waarde en de drempelwaarde te bekijken op het moment dat de waarschuwing werd geactiveerd.

    > [!NOTE] 
    > Als u een voorwaardetype voor dynamische drempelwaarden gebruikt en denkt dat de gebruikte drempelwaarden niet juist zijn, geeft u feedback met behulp van het fronspictogram. Deze feedback is van invloed op machine learning onderzoek naar algoritmen en helpt bij het verbeteren van toekomstige detecties.

2. Als u meerdere dimensiewaarden voor een metrische waarde hebt  geselecteerd, wordt de waarschuwing geactiveerd wanneer een van de metrische tijdreeksen (zoals gedefinieerd door de combinatie van metrische waarden) de drempelwaarde overschrijdt. Kijk [hier](./alerts-metric-overview.md#using-dimensions) voor meer informatie over het gebruik van dimensies in metrische waarschuwingen.

3. Controleer de configuratie van de waarschuwingsregel om te controleren of deze correct is geconfigureerd:
    - Controleer of het **aggregatietype**, **aggregatiegranulatie (periode)** en drempelwaarde of Gevoeligheid zijn geconfigureerd zoals verwacht  
    - Voor een **waarschuwingsregel** die gebruikmaakt van dynamische drempelwaarden, controleert u of  er geavanceerde instellingen zijn geconfigureerd, omdat met Aantal schendingen waarschuwingen kunnen worden gefilterd en Gegevens negeren vóór invloed kan hebben op de manier waarop de drempelwaarden worden berekend

   > [!NOTE]
   > Dynamische drempelwaarden vereisen ten minste 3 dagen en 30 metrische steekproeven voordat ze actief worden.

4. Als u de metrische gegevens visualiseert met behulp van de [grafiek Metrische](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)gegevens, controleert u het volgende:
    - De geselecteerde **aggregatie** in de grafiek met metrische gegevens is hetzelfde als **het aggregatietype** in de waarschuwingsregel
    - De geselecteerde **tijdgranulatie** is hetzelfde als de **Aggregatiegranulatie (periode)** in de waarschuwingsregel (en is niet ingesteld op Automatisch)

5. Als de waarschuwing is gemaakt terwijl er al waarschuwingen zijn die dezelfde criteria bewaken (die niet zijn opgelost), controleert u of de waarschuwingsregel is geconfigureerd met de eigenschap *autoMitigate* ingesteld op **false** (deze eigenschap kan alleen worden geconfigureerd via REST/PowerShell/CLI, dus controleer het script dat is gebruikt om de waarschuwingsregel te implementeren). In dat geval worden door de waarschuwingsregel niet automatisch waarschuwingen opgelost en hoeft er geen waarschuwing te worden opgelost voordat deze opnieuw wordt gebruikt.


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>Kan de metrische gegevens voor waarschuwingen niet vinden op - Metrische gastgegevens voor virtuele machines

Als u waarschuwingen wilt ontvangen over metrische gegevens van gastbesturingssystemen van virtuele machines (bijvoorbeeld geheugen, schijfruimte), moet u ervoor zorgen dat u de vereiste agent hebt geïnstalleerd om deze gegevens te verzamelen Azure Monitor Metrische gegevens:
- [Voor Windows-VM's](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
- [Voor Linux-VM's](../essentials/collect-custom-metrics-linux-telegraf.md)

Zie hier voor meer informatie over het verzamelen van gegevens van het gastbesturingssysteem van een [virtuele machine.](../vm/monitor-vm-azure.md#guest-operating-system)

> [!NOTE] 
> Als u hebt geconfigureerd dat metrische gastgegevens worden verzonden naar een Log Analytics-werkruimte, worden de metrische gegevens weergegeven onder de Resource van de Log Analytics-werkruimte en worden de gegevens pas weergegeven nadat er een waarschuwingsregel is ingesteld die deze bewaakt.  Volg hiervoor de stappen voor het [configureren van een metrische waarschuwing voor logboeken](./alerts-metric-logs.md#configuring-metric-alert-for-logs).

> [!NOTE] 
> Het bewaken van gastmetrische gegevens voor meerdere virtuele machines met één waarschuwingsregel wordt momenteel niet ondersteund door metrische waarschuwingen. U kunt dit doen met een [waarschuwingsregel voor logboeken.](./alerts-unified-log.md) Zorg ervoor dat de metrische gastgegevens worden verzameld in een Log Analytics-werkruimte en maak een waarschuwingsregel voor logboeken in de werkruimte.

## <a name="cant-find-the-metric-to-alert-on"></a>Kan de te waarschuwen metrische gegevens niet vinden

Als u een specifieke metrische waarschuwing wilt instellen maar deze niet ziet bij het maken van een waarschuwingsregel, controleert u het volgende:
- Als u geen metrische gegevens ziet voor de resource, [controleert u of het resourcetype metrische waarschuwingen ondersteunt](./alerts-metric-near-real-time.md).
- Als u bepaalde metrische gegevens niet ziet terwijl andere wel worden weergegeven, [controleert u of de metrische gegevens beschikbaar zijn](../essentials/metrics-supported.md). Zo ja, dan raadpleegt u de beschrijving om te controleren of de metrische gegevens alleen beschikbaar zijn in specifieke versies of edities van de resource.
- Als de metrische gegevens niet beschikbaar zijn voor de resource, zijn ze mogelijk wel beschikbaar in de resourcelogboeken en kunt u ze bewaken met behulp van logboekwaarschuwingen. Hier vindt u meer informatie over het [verzamelen en analyseren van resourcelogboeken van een Azure-resource](../essentials/tutorial-resource-logs.md).

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Kan de dimensie voor metrische gegevens niet vinden om een waarschuwing op uit te geven

Als u een waarschuwing wilt ontvangen over specifieke dimensiewaarden van een [metrische](./alerts-metric-overview.md#using-dimensions)waarde, maar deze waarden niet kunt vinden, let dan op het volgende:

1. Het kan enkele minuten duren voordat de dimensiewaarden worden weergegeven onder de lijst **Dimensiewaarden**.
2. De weergegeven dimensiewaarden zijn gebaseerd op de metrische gegevens die de afgelopen dag zijn verzameld
3. Als de dimensiewaarde nog niet is verzonden of niet wordt weergegeven, kunt u de optie Aangepaste waarde toevoegen gebruiken om een aangepaste dimensiewaarde toe te voegen
4. Als u een waarschuwing wilt ontvangen over alle mogelijke waarden van een dimensie (inclusief toekomstige waarden), kiest u de optie Alle huidige en toekomstige waarden selecteren
5. Aangepaste dimensies voor metrische Application Insights resources zijn standaard uitgeschakeld. Zie hier om de verzameling dimensies voor deze aangepaste metrische gegevens in te [zetten.](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>Metrische waarschuwingsregels die nog steeds zijn gedefinieerd voor een verwijderde resource 

Wanneer u een Azure-resource verwijdert, worden de gekoppelde waarschuwingsregels voor metrische gegevens niet automatisch verwijderd. Waarschuwingsregels verwijderen die zijn gekoppeld aan een resource die is verwijderd:

1. Open de resourcegroep waarin de verwijderde resource was gedefinieerd
1. Schakel in de lijst met resources het selectievakje **Verborgen typen weergeven** in
1. Filter de lijst op Type == **microsoft.insights/metricalerts**
1. Selecteer de relevante waarschuwingsregels en selecteer **Verwijderen**

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>Waarschuwingen voor metrische gegevens instellen telkens wanneer aan mijn voorwaarde wordt voldaan

Metrische waarschuwingen zijn standaard stateful en daarom worden er geen extra waarschuwingen geactiveerd als er al een geactiveerde waarschuwing voor een bepaalde tijdreeks is. Als u een specifieke metrische waarschuwingsregel staatloos wilt maken en wilt worden gewaarschuwd bij elke evaluatie waarin aan de waarschuwingsvoorwaarde wordt voldaan, maakt u de waarschuwingsregel programmatisch (bijvoorbeeld via [Resource Manager](./alerts-metric-create-templates.md), [PowerShell](/powershell/module/az.monitor/), [REST](/rest/api/monitor/metricalerts/createorupdate), [CLI)](/cli/azure/monitor/metrics/alert)en stelt u de eigenschap *autoMitigate* in op 'False'.

> [!NOTE] 
> Als u een metrische waarschuwingsregel staatloos maakt, wordt voorkomen dat er waarschuwingen worden opgelost, dus zelfs nadat niet meer aan de voorwaarde is voldaan, blijven de waarschuwingen actief tot de bewaarperiode van 30 dagen.

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>Een waarschuwingsregel definiëren voor aangepaste metrische gegevens die nog niet zijn verstuurd

Bij het maken van een waarschuwingsregel voor metrische gegevens wordt de naam van de metrische gegevens gevalideerd op de [API voor](/rest/api/monitor/metricdefinitions/list) metrische definities om te controleren of deze bestaat. In sommige gevallen wilt u een waarschuwingsregel maken voor aangepaste metrische gegevens, zelfs voordat deze wordt verstuurd. Bij het maken (met behulp van een Resource Manager-sjabloon) bijvoorbeeld een Application Insights-resource die een aangepaste metrische waarde zal zenden, samen met een waarschuwingsregel die die metrische gegevens bewaakt.

Om te voorkomen dat de implementatie mislukt wanneer u probeert de definities van de aangepaste metrische gegevens te valideren, kunt u de parameter *skipMetricValidation* gebruiken in de sectie criteria van de waarschuwingsregel, waardoor de metrische validatie wordt overgeslagen. Zie het onderstaande voorbeeld voor het gebruik van deze parameter in een Resource Manager sjabloon. Zie de volledige sjabloonvoorbeelden voor het maken Resource Manager waarschuwingsregels voor metrische gegevens voor [meer informatie.](./alerts-metric-create-templates.md)

```json
"criteria": {
    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
        "allOf": [
            {
                "name" : "condition1",
                "metricName": "myCustomMetric",
                "metricNamespace": "myCustomMetricNamespace",
                "dimensions":[],
                "operator": "GreaterThan",
                "threshold" : 10,
                "timeAggregation": "Average",
                "skipMetricValidation": true
            }
        ]
    }
```

## <a name="export-the-azure-resource-manager-template-of-a-metric-alert-rule-via-the-azure-portal"></a>Exporteert de Azure Resource Manager van een waarschuwingsregel voor metrische gegevens via de Azure Portal

Als u de Resource Manager van een waarschuwingsregel voor metrische gegevens exporteert, begrijpt u de JSON-syntaxis en eigenschappen ervan en kunt u toekomstige implementaties automatiseren.
1. Open in Azure Portal waarschuwingsregel om de details ervan weer te geven.
2. Klik op **Eigenschappen**.
3. Selecteer **onder Automation** de optie Sjabloon **exporteren.**

## <a name="metric-alert-rules-quota-too-small"></a>Quotum voor metrische waarschuwingsregels is te klein

Het toegestane aantal regels voor metrische waarschuwingen per abonnement is onderhevig aan [quotumlimieten.](../service-limits.md)

Als u de quotumlimiet hebt bereikt, kunnen de volgende stappen u helpen het probleem op te lossen:
1. Probeer waarschuwingsregels voor metrische gegevens te verwijderen of uit te stellen die niet meer worden gebruikt.

2. Overschakelen naar het gebruik van regels voor metrische waarschuwingen voor het bewaken van meerdere resources. Met deze mogelijkheid kan één waarschuwingsregel meerdere resources bewaken met slechts één waarschuwingsregel die wordt geteld voor het quotum. Zie [hier](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)voor meer informatie over deze functie en de ondersteunde resourcetypen.

3. Als u de quotumlimiet wilt verhogen, opent u een ondersteuningsaanvraag en geeft u de volgende informatie op:

    - Abonnements-id('s) waarvoor de quotumlimiet moet worden verhoogd
    - Resourcetype voor de quotumverhoging: **Metrische waarschuwingen** of **Metrische waarschuwingen (klassiek)**
    - Aangevraagde quotumlimiet

## <a name="check-total-number-of-metric-alert-rules"></a>Het totale aantal metrische waarschuwingsregels controleren

Volg de onderstaande stappen om het huidige gebruik van regels voor metrische waarschuwingen te controleren.

### <a name="from-the-azure-portal"></a>Vanuit Azure Portal

1. Open het scherm **Waarschuwingen** en klik op **Waarschuwingsregels beheren**
2. Filter op het relevante abonnement met behulp van het **vervolgkeuze besturingselement** Abonnement
3. Zorg ervoor dat U NIET filtert op een specifieke resourcegroep, resourcetype of resource
4. Selecteer **Metrische gegevens in** de vervolgkeuze besturingselement **Signaaltype**
5. Controleer of het **besturingselement vervolgkeuzeset Status** is ingesteld **op Ingeschakeld**
6. Het totale aantal metrische waarschuwingsregels wordt weergegeven boven de lijst met waarschuwingsregels

### <a name="from-api"></a>Van API

- GPowerShell - [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2)
- REST API - [Lijst per abonnement](/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI - [lijst met metrische waarschuwingen van Az.Monitor](/cli/azure/monitor/metrics/alert#az_monitor_metrics_alert_list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Waarschuwingsregels beheren met behulp Resource Manager sjablonen, REST API, PowerShell of Azure CLI

Als u problemen hebt met het maken, bijwerken, ophalen of verwijderen van metrische waarschuwingen met behulp van Resource Manager-sjablonen, REST API, PowerShell of de Azure-opdrachtregelinterface (CLI), kunnen de volgende stappen helpen het probleem op te lossen.

### <a name="resource-manager-templates"></a>Resource Manager-sjablonen

- Bekijk de lijst met [veelvoorkomende implementatiefouten in Azure](../../azure-resource-manager/templates/common-deployment-errors.md) en los problemen in overeenstemming met de lijst op
- Raadpleeg de [metrische waarschuwingen Azure Resource Manager sjabloonvoorbeelden](./alerts-metric-create-templates.md) om ervoor te zorgen dat u alle parameters correct door geeft

### <a name="rest-api"></a>REST-API

Bekijk de [REST API om](/rest/api/monitor/metricalerts/) te controleren of u alle parameters correct door geeft

### <a name="powershell"></a>PowerShell

Zorg ervoor dat u de juiste PowerShell-cmdlets gebruikt voor metrische waarschuwingen:

- PowerShell-cmdlets voor metrische waarschuwingen zijn beschikbaar in de [Az.Monitor-module](/powershell/module/az.monitor/).
- Zorg ervoor dat u de cmdlets gebruikt die eindigen op 'V2' voor nieuwe (niet-klassieke) metrische waarschuwingen (bijvoorbeeld [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2))

### <a name="azure-cli"></a>Azure CLI

Zorg ervoor dat u de juiste CLI-opdrachten gebruikt voor metrische waarschuwingen:

- CLI-opdrachten voor metrische waarschuwingen beginnen met `az monitor metrics alert`. Lees het [referentiemateriaal over Azure CLI](/cli/azure/monitor/metrics/alert) voor informatie over de syntaxis.
- U kunt een [voorbeeld bekijken over het gebruik van de CLI voor metrische waarschuwingen](./alerts-metric.md#with-azure-cli).
- Als u een waarschuwing wilt ontvangen voor aangepaste metrische gegevens, moet u de naam van de metrische gegevens vooraf laten gaan door de desbetreffende metrische naamruimte: NAAMRUIMTE.METRISCHE GEGEVENS

### <a name="general"></a>Algemeen

- Als er een foutmelding `Metric not found` wordt weergegeven:

   - Voor metrische gegevens voor een platform:  zorg ervoor dat u de metrische naam van de pagina [Azure Monitor](../essentials/metrics-supported.md)ondersteunde metrische gegevens gebruikt en niet de weergavenaam **voor metrische gegevens**

   - Voor aangepaste metrische gegevens: zorg ervoor dat de metrische waarde al wordt verstuurd (u kunt geen waarschuwingsregel maken voor aangepaste metrische gegevens die nog niet bestaan) en dat u de naamruimte van de aangepaste metrische waarde op geeft (zie hier een voorbeeld van een [Resource Manager-sjabloon)](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric)

- Als u waarschuwingen voor metrische [gegevens maakt voor logboeken,](./alerts-metric-logs.md)moet u ervoor zorgen dat de juiste afhankelijkheden zijn opgenomen. Zie de [voorbeeldsjabloon](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs).

- Als u een waarschuwingsregel maakt die meerdere criteria bevat, moet u rekening houden met de volgende beperkingen:

   - U kunt slechts één waarde per dimensie in elk criterium selecteren
   - U kunt '\*' niet als dimensiewaarde gebruiken
   - Wanneer metrische gegevens die zijn geconfigureerd in verschillende criteria dezelfde dimensie ondersteunen, moet een geconfigureerde dimensiewaarde expliciet op dezelfde manier [](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria)worden ingesteld voor al deze metrische gegevens (zie hier een voorbeeld van een Resource Manager-sjabloon)


## <a name="no-permissions-to-create-metric-alert-rules"></a>Geen machtigingen voor het maken van metrische waarschuwingsregels

Als u een waarschuwingsregel voor metrische gegevens wilt maken, hebt u de volgende machtigingen nodig:

- Leesmachtiging voor de doelresource van de waarschuwingsregel
- Schrijfmachtiging voor de resourcegroep waarin de waarschuwingsregel is gemaakt (als u de waarschuwingsregel maakt op basis van de Azure Portal, wordt de waarschuwingsregel standaard gemaakt in dezelfde resourcegroep waarin de doelresource zich bevindt)
- Leesmachtiging voor een actiegroep die is gekoppeld aan de waarschuwingsregel (indien van toepassing)


## <a name="naming-restrictions-for-metric-alert-rules"></a>Naamgevingsbeperkingen voor regels voor metrische waarschuwingen

Houd rekening met de volgende beperkingen voor namen van metrische waarschuwingsregel:

- Namen van waarschuwingsregel voor metrische gegevens kunnen niet worden gewijzigd (hernoemd) nadat deze zijn gemaakt
- Namen van waarschuwingsregel voor metrische gegevens moeten uniek zijn binnen een resourcegroep
- Namen van waarschuwingsregel voor metrische gegevens mogen niet de volgende tekens bevatten: * # & + : < > ? @ % { } \ / 
- Namen van waarschuwingsregel voor metrische gegevens mogen niet eindigen op een spatie of punt

> [!NOTE] 
> Als de naam van de waarschuwingsregel tekens bevat die niet alfabetisch of numeriek zijn (bijvoorbeeld spaties, leestekens of symbolen), kunnen deze tekens URL-gecodeerd zijn wanneer ze worden opgehaald door bepaalde clients.

## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>Beperkingen bij het gebruik van dimensies in een metrische waarschuwingsregel met meerdere voorwaarden

Metrische waarschuwingen ondersteunen waarschuwingen voor multidimensionale metrische gegevens en bieden ondersteuning voor het definiëren van meerdere voorwaarden (maximaal 5 voorwaarden per waarschuwingsregel).

Houd rekening met de volgende beperkingen bij het gebruik van dimensies in een waarschuwingsregel die meerdere voorwaarden bevat:
- U kunt binnen elke voorwaarde slechts één waarde per dimensie selecteren.
- U kunt de optie 'Alle huidige en toekomstige waarden selecteren' niet gebruiken (Selecteer \* ).
- Wanneer metrische gegevens die in verschillende omstandigheden zijn geconfigureerd dezelfde dimensie ondersteunen, moet een geconfigureerde dimensiewaarde expliciet op dezelfde manier worden ingesteld voor al deze metrische gegevens (in de relevante voorwaarden).
Bijvoorbeeld:
    - Overweeg een waarschuwingsregel voor metrische gegevens die is gedefinieerd voor een opslagaccount en die twee voorwaarden bewaakt:
        * Totaal **aantal transacties** > 5
        * Gemiddelde **SuccessE2ELatency** > 250 ms
    - Ik wil de eerste voorwaarde bijwerken en alleen transacties controleren waarbij de **dimensie ApiName** gelijk is aan *'GetBlob'*
    - Omdat zowel de metrische gegevens **Transactions** als **SuccessE2ELatency** een **ApiName-dimensie** ondersteunen, moet ik beide voorwaarden bijwerken en beide de **dimensie ApiName** opgeven met de waarde *'GetBlob'.*

## <a name="setting-the-alert-rules-period-and-frequency"></a>De periode en frequentie van de waarschuwingsregel instellen

We raden u aan een *Aggregatiegranulatie (Periode)* te kiezen die groter is dan de evaluatiefrequentie *om* de kans te verkleinen dat de eerste evaluatie van toegevoegde tijdreeksen ontbreekt in de volgende gevallen:
-   Waarschuwingsregel voor metrische gegevens die meerdere dimensies bewaakt: wanneer een nieuwe dimensiewaardecombinatie wordt toegevoegd
-   Waarschuwingsregel voor metrische gegevens die meerdere resources bewaakt: wanneer een nieuwe resource wordt toegevoegd aan het bereik
-   Waarschuwingsregel voor metrische gegevens die een metrische gegevens bewaakt die niet continu worden weergegeven (sparse metrische gegevens) - Wanneer de metrische gegevens worden weergegeven na een periode langer dan 24 uur waarin deze niet is uitgegeven

## <a name="the-dynamic-thresholds-borders-dont-seem-to-fit-the-data"></a>De randen van dynamische drempelwaarden lijken niet aan de gegevens te passen

Als het gedrag van een metrische gegevens onlangs is gewijzigd, worden de wijzigingen niet noodzakelijkerwijs onmiddellijk doorgevoerd in de dynamische drempelwaarden (boven- en ondergrenzen), omdat deze worden berekend op basis van metrische gegevens van de afgelopen tien dagen. Wanneer u de grenzen van de dynamische drempelwaarde voor een bepaald metrisch gegeven bekijkt, moet u kijken naar de metrische trend in de afgelopen week en niet alleen voor recente uren of dagen.

## <a name="why-is-weekly-seasonality-not-detected-by-dynamic-thresholds"></a>Waarom wordt wekelijkse seizoensgebondenheid niet gedetecteerd door dynamische drempelwaarden?

Voor het identificeren van wekelijkse seizoensgebondenheid vereist het model voor dynamische drempelwaarden ten minste drie weken aan historische gegevens. Zodra er voldoende historische gegevens beschikbaar zijn, wordt elke wekelijkse seizoensgebondenheid in de metrische gegevens geïdentificeerd en wordt het model dienovereenkomstig aangepast. 

## <a name="dynamic-thresholds-shows-a-negative-lower-bound-for-a-metric-even-though-the-metric-always-has-positive-values"></a>Dynamische drempelwaarden toont een negatieve ondergrens voor een metrische waarde, zelfs als de metrische waarde altijd positieve waarden bevat

Wanneer een metrische waarde grote schommelingen vertoont, bouwen dynamische drempelwaarden een breder model rond de metrische waarden, wat ertoe kan leiden dat de ondergrens onder nul komt. Dit kan met name gebeuren in de volgende gevallen:
1. De gevoeligheid is ingesteld op laag 
2. De mediaanwaarden liggen dicht bij nul
3. De metrische gegevens vertonen een onregelmatig gedrag met een hoge afwijking (er zijn pieken of dalen in de gegevens)

Wanneer de ondergrens een negatieve waarde heeft, betekent dit dat het waarschijnlijk is dat de metrische waarde een nulwaarde bereikt op basis van het onregelmatige gedrag van de metrische waarde. U kunt overwegen een hogere gevoeligheid of een grotere *Aggregatiegranulatie (Periode)* te  kiezen om het model minder gevoelig te maken, of de optie Gegevens negeren vóór te gebruiken om een recente irregulaiteit uit te sluiten van de historische gegevens die worden gebruikt om het model te bouwen.

## <a name="next-steps"></a>Volgende stappen

- Zie [Troubleshooting problems in](alerts-troubleshoot.md)Azure Monitor alerts voor algemene informatie over het oplossen Azure Monitor waarschuwingen.
