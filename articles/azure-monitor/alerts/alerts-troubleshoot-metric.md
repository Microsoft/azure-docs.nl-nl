---
title: Problemen met metrische waarschuwingen voor Azure oplossen
description: Veelvoorkomende problemen met Azure Monitor metrische waarschuwingen en mogelijke oplossingen.
author: harelbr
ms.author: harelbr
ms.topic: troubleshooting
ms.date: 01/21/2021
ms.subservice: alerts
ms.openlocfilehash: 1908232184218316a1a887f17f2fc8104529a0e7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609584"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Problemen met metrische waarschuwingen in Azure Monitor oplossen 

In dit artikel worden veelvoorkomende problemen in Azure Monitor [metrische waarschuwingen](alerts-metric-overview.md) beschreven en wordt uitgelegd hoe u deze problemen kunt oplossen.

Azure Monitor waarschuwingen geven u proactief op de hoogte wanneer er belang rijke voor waarden worden gevonden in uw bewakings gegevens. Hiermee kunt u problemen identificeren en verhelpen voordat de gebruikers van uw systeem ze merken. Zie [overzicht van waarschuwingen in Microsoft Azure](../platform/alerts-overview.md)voor meer informatie over waarschuwingen.

## <a name="metric-alert-should-have-fired-but-didnt"></a>De metrische waarschuwing moet worden geactiveerd, maar niet 

Als u denkt dat een metrische waarschuwing moet worden geactiveerd, maar niet is geactiveerd en niet wordt gevonden in de Azure Portal, probeert u de volgende stappen uit te voeren:

1. **Configuratie** : Controleer de configuratie van de metrische waarschuwings regel om er zeker van te zijn dat deze correct is geconfigureerd:
    - Controleer of het **aggregatie type** en de granulatie van de **aggregatie (punt)** zijn geconfigureerd zoals verwacht. **Aggregatie type** bepaalt hoe metrische waarden worden geaggregeerd ( [hier](../platform/metrics-aggregation-explained.md#aggregation-types)vindt u meer informatie) en **aggregatie granulatie (period)** bepaalt hoe ver terug de evaluatie de metrische waarden opstelt wanneer de waarschuwings regel wordt uitgevoerd.
    -  Controleer of de **drempel waarde** of **gevoeligheid** is geconfigureerd zoals verwacht.
    - Voor een waarschuwings regel die gebruikmaakt van dynamische drempel waarden, controleert u of geavanceerde instellingen zijn geconfigureerd, omdat het **aantal schendingen** mogelijk waarschuwingen kan filteren en **gegevens kan negeren voordat** de drempel waarden worden berekend.

       > [!NOTE] 
       > Dynamische drempel waarden vereisen ten minste 3 dagen en 30 metrische voor beelden voordat ze actief zijn.

2. **Gestart, maar geen melding** : Controleer de [lijst met geactiveerde waarschuwingen](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) om te zien of u de geactiveerde waarschuwing kunt vinden. Als u de waarschuwing in de lijst kunt zien, maar een probleem met een aantal acties of meldingen hebt, raadpleegt u [hier](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)meer informatie.

3. **Al actief** : Controleer of er al een waarschuwing is geactiveerd voor de metrische tijds reeks waarnaar u verwacht een waarschuwing te ontvangen. Metrische waarschuwingen zijn stateful, wat betekent dat wanneer een waarschuwing wordt geactiveerd voor een specifieke metrische tijdreeks, extra waarschuwingen over die tijdreeksen pas worden geactiveerd zodra het probleem niet meer wordt waargenomen. Deze ontwerp keuze vermindert de ruis. De waarschuwing wordt automatisch opgelost wanneer aan de voor waarde van de waarschuwing niet wordt voldaan voor drie opeenvolgende evaluaties.

4. **Gebruikte dimensies** : als u bepaalde [dimensie waarden voor een metriek](./alerts-metric-overview.md#using-dimensions)hebt geselecteerd, wordt met de waarschuwings regel elke afzonderlijke meet tijd reeks gecontroleerd (zoals gedefinieerd door de combi natie van dimensie waarden) voor een schending van drempel waarden. Als u ook de cumulatieve metrische tijd reeks (zonder geselecteerde dimensies) wilt bewaken, moet u een extra waarschuwings regel voor de metriek configureren zonder dat u dimensies hoeft te selecteren.

5. **Aggregatie en tijd granulatie** : als u de metrische gegevens wilt visualiseren met [metrische grafieken](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics), zorgt u ervoor dat:
    * De geselecteerde **aggregatie** in de metrische grafiek is hetzelfde als het **aggregatie type** in uw waarschuwings regel
    * De geselecteerde **tijd granulariteit** is hetzelfde als de **granulariteit van de aggregatie (punt)** in uw waarschuwings regel (en niet ingesteld op ' automatisch ')

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>De metrische waarschuwing wordt geactiveerd wanneer deze niet

Als u denkt dat uw metrische waarschuwing niet hoeft te worden geactiveerd, maar dit wel is gelukt, kunt u de volgende stappen uitvoeren om het probleem op te lossen.

1. Raadpleeg de [lijst met geactiveerde waarschuwingen](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) om de geactiveerde waarschuwing te vinden en klik om de details ervan weer te geven. Lees de informatie in **Waarom is deze waarschuwing geactiveerd?** om de metrische grafiek, **metrische waarde** en **drempel waarde** weer te geven op het moment dat de waarschuwing werd geactiveerd.

    > [!NOTE] 
    > Als u een voor waarde voor de dynamische drempel waarden gebruikt en u er zeker van wilt zijn dat de gebruikte drempel waarden onjuist zijn, geeft u feedback met behulp van het frons pictogram. Deze feedback is van invloed op het machine learning algoritme onderzoek en helpt toekomstige detecties te verbeteren.

2. Als u meerdere dimensie waarden voor een metriek hebt geselecteerd, wordt de waarschuwing geactiveerd wanneer **een** van de metrische time series (zoals gedefinieerd door de combi natie van dimensie waarden) de drempel overschrijdt. Kijk [hier](./alerts-metric-overview.md#using-dimensions) voor meer informatie over het gebruik van dimensies in metrische waarschuwingen.

3. Controleer de configuratie van de waarschuwings regel om er zeker van te zijn dat deze correct is geconfigureerd:
    - Controleer of het **samenvoegings type**, de **aggregatie granulatie (punt)** en de **drempel waarde** of **gevoeligheid** als verwacht zijn geconfigureerd
    - Voor een waarschuwings regel die gebruikmaakt van dynamische drempel waarden, controleert u of geavanceerde instellingen zijn geconfigureerd, omdat het **aantal schendingen** kan waarschuwingen filteren en **gegevens negeert voordat** de drempel waarden worden berekend

   > [!NOTE]
   > Dynamische drempel waarden vereisen ten minste 3 dagen en 30 metrische voor beelden voordat ze actief zijn.

4. Als u de metrische gegevens wilt visualiseren [, moet](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)u ervoor zorgen dat:
    - De geselecteerde **aggregatie** in de metrische grafiek is hetzelfde als het **aggregatie type** in uw waarschuwings regel
    - De geselecteerde **tijd granulariteit** is hetzelfde als de **granulariteit van de aggregatie (punt)** in uw waarschuwings regel (en niet ingesteld op ' automatisch ')

5. Als de waarschuwing wordt geactiveerd terwijl er al waarschuwingen zijn die dezelfde criteria bewaken (die niet zijn opgelost), controleert u of de waarschuwings regel is geconfigureerd met de eigenschap *Autoreduce* ingesteld op **False** (deze eigenschap kan alleen worden geconfigureerd via rest/Power shell/CLI, dus controleer het script dat wordt gebruikt om de waarschuwings regel te implementeren). In dat geval worden geactiveerde waarschuwingen niet automatisch opgelost met de waarschuwings regel en is er geen geactiveerde waarschuwing voor het oplossen van problemen vereist.


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>Kan de metrische gegevens niet vinden voor waarschuwingen over de metrische gegevens van de gast voor virtuele machines

Als u een waarschuwing wilt ontvangen voor de metrische gegevens van het gast besturingssysteem van virtuele machines (bijvoorbeeld: geheugen, schijf ruimte), moet u ervoor zorgen dat u de vereiste agent hebt geïnstalleerd voor het verzamelen van deze informatie voor Azure Monitor metrieken:
- [Voor Windows-VM's](../platform/collect-custom-metrics-guestos-resource-manager-vm.md)
- [Voor Linux-VM's](../platform/collect-custom-metrics-linux-telegraf.md)

Zie [hier](../insights/monitor-vm-azure.md#guest-operating-system)voor meer informatie over het verzamelen van gegevens uit het gast besturingssysteem van een virtuele machine.

> [!NOTE] 
> Als u de metrische gegevens van de gast hebt geconfigureerd om te worden verzonden naar een Log Analytics-werk ruimte, worden de metrische gegevens weer gegeven onder de Log Analytics werkruimte resource en worden **alleen** data weer gegeven nadat u een waarschuwings regel hebt gemaakt waarmee deze worden gecontroleerd. Volg hiervoor de stappen voor het [configureren van een metrische waarschuwing voor logboeken](./alerts-metric-logs.md#configuring-metric-alert-for-logs).

> [!NOTE] 
> Het bewaken van een gast metriek voor meerdere virtuele machines met één waarschuwings regel wordt momenteel niet ondersteund door metrische waarschuwingen. U kunt dit doen met een [waarschuwings regel](./alerts-unified-log.md)voor het logboek. Als u dit wilt doen, controleert u of de gegevens van de gast worden verzameld naar een Log Analytics-werk ruimte en maakt u een waarschuwings regel voor het logboek in de werk ruimte.

## <a name="cant-find-the-metric-to-alert-on"></a>Kan de metrische gegevens niet vinden voor waarschuwing

Als u een specifieke metrische waarschuwing wilt instellen maar deze niet ziet bij het maken van een waarschuwingsregel, controleert u het volgende:
- Als u geen metrische gegevens ziet voor de resource, [controleert u of het resourcetype metrische waarschuwingen ondersteunt](./alerts-metric-near-real-time.md).
- Als u bepaalde metrische gegevens niet ziet terwijl andere wel worden weergegeven, [controleert u of de metrische gegevens beschikbaar zijn](../platform/metrics-supported.md). Zo ja, dan raadpleegt u de beschrijving om te controleren of de metrische gegevens alleen beschikbaar zijn in specifieke versies of edities van de resource.
- Als de metrische gegevens niet beschikbaar zijn voor de resource, zijn ze mogelijk wel beschikbaar in de resourcelogboeken en kunt u ze bewaken met behulp van logboekwaarschuwingen. Hier vindt u meer informatie over het [verzamelen en analyseren van resourcelogboeken van een Azure-resource](../learn/tutorial-resource-logs.md).

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Kan de metrische dimensie niet vinden om te waarschuwen

Als u op zoek bent naar [specifieke dimensie waarden van een metriek](./alerts-metric-overview.md#using-dimensions), maar deze waarden niet kunt vinden, moet u rekening houden met het volgende:

1. Het kan enkele minuten duren voordat de dimensiewaarden worden weergegeven onder de lijst **Dimensiewaarden**.
1. De weergegeven dimensiewaarden zijn gebaseerd op de metrische gegevens die de afgelopen dag zijn verzameld
1. Als de dimensiewaarde nog niet is verzonden of niet wordt weergegeven, kunt u de optie Aangepaste waarde toevoegen gebruiken om een aangepaste dimensiewaarde toe te voegen
1. Als u wilt waarschuwen voor alle mogelijke waarden van een dimensie (met inbegrip van toekomstige waarden), kiest u de optie alle huidige en toekomstige waarden selecteren

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>De regels voor metrische waarschuwingen zijn nog steeds gedefinieerd voor een verwijderde resource 

Wanneer u een Azure-resource verwijdert, worden de gekoppelde waarschuwingsregels voor metrische gegevens niet automatisch verwijderd. Waarschuwings regels verwijderen die zijn gekoppeld aan een resource die is verwijderd:

1. Open de resourcegroep waarin de verwijderde resource was gedefinieerd
1. Schakel in de lijst met resources het selectievakje **Verborgen typen weergeven** in
1. Filter de lijst op Type == **microsoft.insights/metricalerts**
1. Selecteer de relevante waarschuwings regels en selecteer **verwijderen** .

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>Er worden metrische waarschuwingen gegeven telkens wanneer aan mijn voor waarde wordt voldaan

Metrische waarschuwingen zijn standaard stateful en daarom worden er geen extra waarschuwingen geactiveerd als er al een waarschuwing voor een bepaalde tijd reeks wordt geactiveerd. Als u een specifieke metrische waarschuwings regel wilt maken en u waarschuwingen wilt ontvangen voor elke evaluatie waarbij aan de waarschuwings voorwaarde wordt voldaan, maakt u de waarschuwings regel programmatisch (bijvoorbeeld via [Resource Manager](./alerts-metric-create-templates.md), [Power shell](/powershell/module/az.monitor/), [rest](/rest/api/monitor/metricalerts/createorupdate), [cli](/cli/azure/monitor/metrics/alert)) en stelt u de eigenschap *autoreduce* in op ' false '.

> [!NOTE] 
> Als u een metrische waarschuwings regel stateless maakt, voor komt u dat er geactiveerde waarschuwingen worden opgelost, dus zelfs nadat de voor waarde niet meer is gehaald, blijven de geactiveerde waarschuwingen in de status geactiveerd totdat de Bewaar periode van 30 dagen is verstreken.

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>Een waarschuwings regel definiëren op basis van een aangepaste metriek die nog niet is verzonden

Wanneer u een metrische waarschuwings regel maakt, wordt de naam van de metrische gegevens gevalideerd op basis van de [API voor metrische definities](/rest/api/monitor/metricdefinitions/list) om er zeker van te zijn dat deze bestaat. In sommige gevallen wilt u een waarschuwings regel maken voor een aangepaste metriek, zelfs voordat deze wordt verzonden. Bijvoorbeeld bij het maken (met een resource manager-sjabloon) een Application Insights resource die aangepaste metrische gegevens zal genereren, samen met een waarschuwings regel die de metrische gegevens controleert.

Als u wilt voor komen dat de implementatie mislukt wanneer u probeert de definities van de aangepaste metrische gegevens te valideren, kunt u de para meter *skipMetricValidation* gebruiken in het gedeelte criteria van de waarschuwings regel, waardoor de metrische validatie wordt overgeslagen. Zie het onderstaande voor beeld voor het gebruik van deze para meter in een resource manager-sjabloon. Zie voor meer informatie de [volledige Resource Manager-sjabloon voorbeelden voor het maken van metrische waarschuwings regels](./alerts-metric-create-templates.md).

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

## <a name="export-the-azure-resource-manager-template-of-a-metric-alert-rule-via-the-azure-portal"></a>De Azure Resource Manager sjabloon van een metrische waarschuwings regel exporteren via de Azure Portal

Als u de Resource Manager-sjabloon van een metrische waarschuwings regel exporteert, kunt u de syntaxis en eigenschappen van de JSON begrijpen en gebruiken om toekomstige implementaties te automatiseren.
1. Ga naar de sectie **resource groepen** in de portal en selecteer de resource groep met de regel.
2. Schakel in de sectie Overzicht het selectie vakje **verborgen typen weer geven** in.
3. Selecteer in het filter **type** *micro soft. Insights/metricalerts*.
4. Selecteer de relevante waarschuwings regel om de details ervan weer te geven.
5. Selecteer onder **instellingen** de optie **sjabloon exporteren**.

## <a name="metric-alert-rules-quota-too-small"></a>Het quotum voor de waarschuwings regels voor metrische gegevens is te klein

Voor het toegestane aantal metrische waarschuwings regels per abonnement gelden [quotum limieten](../service-limits.md).

Als u de quotum limiet hebt bereikt, kunt u de volgende stappen uitvoeren om het probleem op te lossen:
1. Probeer regels voor metrische waarschuwingen te verwijderen of uit te scha kelen die niet meer worden gebruikt.

2. Overschakelen naar het gebruik van regels voor metrische waarschuwingen voor het bewaken van meerdere resources. Met deze mogelijkheid kan één waarschuwings regel meerdere resources bewaken met slechts één waarschuwings regel die bij het quotum wordt geteld. Zie [hier](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)voor meer informatie over deze functie en de ondersteunde resourcetypen.

3. Als de quotum limiet moet worden verhoogd, opent u een ondersteunings aanvraag en geeft u de volgende informatie op:

    - Abonnements-id ('s) waarvoor de quotum limiet moet worden verhoogd
    - Resource type voor de quota verhoging: **metrische waarschuwingen** of **metrische waarschuwingen (klassiek)**
    - Aangevraagde quotumlimiet

## <a name="check-total-number-of-metric-alert-rules"></a>Het totale aantal metrische waarschuwings regels controleren

Volg de onderstaande stappen om het huidige gebruik van metrische waarschuwings regels te controleren.

### <a name="from-the-azure-portal"></a>Vanuit Azure Portal

1. Open het scherm **Waarschuwingen** en klik op **Waarschuwingsregels beheren**
2. Filteren op het relevante abonnement met behulp van de vervolg keuzelijst voor het **abonnement**
3. Zorg ervoor dat u niet filtert op een specifieke resource groep, resource type of resource
4. Selecteer **metrische gegevens** in de vervolg keuzelijst **type signaal**
5. Controleer of het besturings element voor de vervolg keuzelijst **status** is ingesteld op **ingeschakeld**
6. Het totale aantal metrische waarschuwings regels wordt weer gegeven boven de lijst waarschuwings regels

### <a name="from-api"></a>Van API

- GPowerShell - [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2)
- REST API - [Lijst per abonnement](/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI - [lijst met metrische waarschuwingen van Az.Monitor](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Waarschuwings regels beheren met Resource Manager-sjablonen, REST API, Power shell of Azure CLI

Als u problemen ondervindt bij het maken, bijwerken, ophalen of verwijderen van metrische waarschuwingen met behulp van Resource Manager-sjablonen, REST API, Power shell of de Azure-opdracht regel interface (CLI), kunt u de volgende stappen uitvoeren om het probleem op te lossen.

### <a name="resource-manager-templates"></a>Resource Manager-sjablonen

- Bekijk de lijst met [veelvoorkomende implementatiefouten in Azure](../../azure-resource-manager/templates/common-deployment-errors.md) en los problemen in overeenstemming met de lijst op
- Raadpleeg de [metrische waarschuwingen Azure Resource Manager sjabloon voor beelden](./alerts-metric-create-templates.md) om te controleren of alle para meters correct worden door gegeven

### <a name="rest-api"></a>REST-API

Raadpleeg de [rest API-hand leiding](/rest/api/monitor/metricalerts/) om te controleren of alle para meters correct worden door gegeven

### <a name="powershell"></a>PowerShell

Zorg ervoor dat u de juiste Power shell-cmdlets gebruikt voor metrische waarschuwingen:

- PowerShell-cmdlets voor metrische waarschuwingen zijn beschikbaar in de [Az.Monitor-module](/powershell/module/az.monitor/).
- Zorg ervoor dat u de cmdlets die eindigen op v2 gebruikt voor nieuwe (niet-klassieke) metrische waarschuwingen (bijvoorbeeld [add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2))

### <a name="azure-cli"></a>Azure CLI

Zorg ervoor dat u de juiste CLI-opdrachten gebruikt voor metrische waarschuwingen:

- CLI-opdrachten voor metrische waarschuwingen beginnen met `az monitor metrics alert`. Lees het [referentiemateriaal over Azure CLI](/cli/azure/monitor/metrics/alert) voor informatie over de syntaxis.
- U kunt een [voorbeeld bekijken over het gebruik van de CLI voor metrische waarschuwingen](./alerts-metric.md#with-azure-cli).
- Als u een waarschuwing wilt ontvangen voor aangepaste metrische gegevens, moet u de naam van de metrische gegevens vooraf laten gaan door de desbetreffende metrische naamruimte: NAAMRUIMTE.METRISCHE GEGEVENS

### <a name="general"></a>Algemeen

- Als u een `Metric not found` fout bericht ontvangt:

   - Voor een platform metriek: Zorg ervoor dat u de naam van de **metrische gegevens** gebruikt op [de pagina Azure monitor ondersteunde metrische gegevens](../platform/metrics-supported.md)en niet de **weergave naam van de metrische gegevens**

   - Voor een aangepaste metriek: Zorg ervoor dat de metriek al wordt verzonden (u kunt geen waarschuwings regel maken op een aangepaste metriek die nog niet bestaat) en dat u de naam ruimte van de aangepaste metriek opgeeft (Bekijk [hier](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric)een voor beeld van een resource manager-sjabloon)

- Als u [metrische waarschuwingen op Logboeken](./alerts-metric-logs.md)wilt maken, moet u ervoor zorgen dat de juiste afhankelijkheden worden opgenomen. Zie de [voorbeeldsjabloon](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs).

- Als u een waarschuwings regel maakt die meerdere criteria bevat, moet u rekening houden met de volgende beperkingen:

   - U kunt slechts één waarde per dimensie in elk criterium selecteren
   - U kunt '\*' niet als dimensiewaarde gebruiken
   - Wanneer de metrische gegevens die in verschillende criteria zijn geconfigureerd, dezelfde dimensie ondersteunen, moet een geconfigureerde dimensie waarde expliciet op dezelfde manier worden ingesteld voor al deze metrische gegevens (Zie [hier](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria)een resource manager-sjabloon voor beeld)


## <a name="no-permissions-to-create-metric-alert-rules"></a>Er zijn geen machtigingen om regels voor metrische waarschuwingen te maken

Als u een waarschuwings regel voor metrische gegevens wilt maken, moet u over de volgende machtigingen beschikken:

- Lees machtiging voor de doel resource van de waarschuwings regel
- Schrijf machtiging voor de resource groep waarin de waarschuwings regel is gemaakt (als u de waarschuwings regel maakt op basis van de Azure Portal, wordt de waarschuwings regel standaard gemaakt in dezelfde resource groep waarin de doel resource zich bevindt)
- Lees machtiging voor elke actie groep die is gekoppeld aan de waarschuwings regel (indien van toepassing)


## <a name="naming-restrictions-for-metric-alert-rules"></a>Naamgevings beperkingen voor metrische waarschuwings regels

Houd rekening met de volgende beperkingen voor de namen van regels voor metrische waarschuwingen:

- De namen van de waarschuwings regels voor metrische gegevens kunnen niet worden gewijzigd (hernoemd) nadat deze is gemaakt
- De naam van de metrische waarschuwings regel moet uniek zijn binnen een resource groep
- De naam van de regel voor metrische waarschuwingen mag niet de volgende tekens bevatten: * # & +:  < > ? @ % { } \ / 
- De namen van regels voor metrische waarschuwingen mogen niet eindigen op een spatie of een punt

> [!NOTE] 
> Als de naam van de waarschuwings regel tekens bevat die niet alfabetisch of numeriek zijn (bijvoorbeeld spaties, lees tekens of symbolen), kunnen deze tekens URL-gecodeerd zijn wanneer ze door bepaalde clients worden opgehaald.

## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>Beperkingen bij het gebruik van dimensies in een metrische waarschuwings regel met meerdere voor waarden

Metrische waarschuwingen ondersteunen waarschuwingen over multidimensionale metrische gegevens en bieden ondersteuning voor het definiëren van meerdere voor waarden (Maxi maal 5 voor waarden per waarschuwings regel).

Houd rekening met de volgende beperkingen bij het gebruik van dimensies in een waarschuwings regel die meerdere voor waarden bevat:
- U kunt binnen elke voor waarde slechts één waarde per dimensie selecteren.
- U kunt de optie ' alle huidige en toekomstige waarden selecteren ' (selecteren) niet gebruiken \* .
- Wanneer de metrische gegevens die in verschillende voor waarden zijn geconfigureerd, dezelfde dimensie ondersteunen, moet een geconfigureerde dimensie waarde expliciet op dezelfde manier worden ingesteld voor al deze metrische gegevens (in de relevante omstandigheden).
Bijvoorbeeld:
    - Houd rekening met een metrische waarschuwings regel die is gedefinieerd in een opslag account en controleert twee voor waarden:
        * Totaal aantal **trans acties** > 5
        * Gemiddeld **SuccessE2ELatency** > 250 MS
    - Ik wil de eerste voor waarde bijwerken en alleen trans acties bewaken waarbij de dimensie **ApiName** gelijk is aan *' GetBlob '*
    - Omdat zowel de metrische gegevens van de **trans acties** als de **SuccessE2ELatency** een **ApiName** -dimensie ondersteunen, moet ik beide voor waarden bijwerken en beide de dimensie **ApiName** opgeven met de waarde *' GetBlob '* .

## <a name="setting-the-alert-rules-period-and-frequency"></a>De periode en frequentie van de waarschuwings regel instellen

U kunt het beste een *aggregatie granulatie (punt)* kiezen die groter is dan de *frequentie van de evaluatie*, om de kans te verminderen dat de eerste evaluatie van toegevoegde tijd reeksen in de volgende gevallen wordt gereduceerd:
-   Waarschuwings regel voor metrische gegevens die meerdere dimensies bewaakt: wanneer een nieuwe Dimensiewaardecombinatie wordt toegevoegd
-   Waarschuwings regel voor metrische gegevens die meerdere resources bewaakt: wanneer een nieuwe resource wordt toegevoegd aan het bereik
-   Waarschuwings regel voor metrische gegevens die een metriek bewaakt die niet continu wordt uitgezonden (sparse metrisch): wanneer de metriek wordt verzonden na een periode van meer dan 24 uur waarin deze niet is verzonden

## <a name="the-dynamic-thresholds-borders-dont-seem-to-fit-the-data"></a>De grenzen van de dynamische drempel waarden lijken niet op de gegevens

Als het gedrag van een metriek onlangs is gewijzigd, worden de wijzigingen niet nood zakelijk weer gegeven in de dynamische drempel grenzen (boven en onder), omdat deze worden berekend op basis van metrische gegevens van de afgelopen 10 dagen. Wanneer u de dynamische drempel waarden voor een bepaalde metriek bekijkt, moet u ervoor zorgen dat u de metrische trend in de afgelopen week bekijkt en niet alleen voor recente uren of dagen.

## <a name="why-is-weekly-seasonality-not-detected-by-dynamic-thresholds"></a>Waarom is wekelijkse seizoensgebondenheid niet gedetecteerd door dynamische drempels?

Voor het identificeren van de wekelijkse seizoensgebondenheid is voor het model voor dynamische drempel waarden ten minste drie weken van historische gegevens vereist. Als er voldoende historische gegevens beschikbaar zijn, wordt elke wekelijkse seizoensgebondenheid die bestaat in de metrische gegevens geïdentificeerd en wordt het model dienovereenkomstig aangepast. 

## <a name="dynamic-thresholds-shows-a-negative-lower-bound-for-a-metric-even-though-the-metric-always-has-positive-values"></a>Dynamische drempel waarden geven een negatieve ondergrens voor een metriek, hoewel de metrische gegevens altijd positieve waarde hebben

Wanneer een metriek een grote schommeling vertoont, wordt door dynamische drempel waarden een breder model rond de metrische gegevens gemaakt, wat kan leiden tot een onderrand onder nul. Dit kan in de volgende gevallen gebeuren:
1. De gevoeligheid is ingesteld op laag 
2. De mediaan waarden zijn dicht bij nul
3. De metriek vertoont een onregelmatige werking met een hoge variantie (er zijn pieken of spannings dips in de gegevens)

Wanneer de ondergrens een negatieve waarde heeft, betekent dit dat de metriek plausible is om de waarde nul te bereiken, gezien het onregelmatige gedrag van de metriek. U kunt overwegen een hogere gevoeligheid of een grotere *aggregatie granulatie (punt)* te kiezen om het model minder gevoelig te maken, of gebruik de optie *gegevens negeren vóór* om een recente irregulaity uit te sluiten van de historische gegevens die worden gebruikt om het model te bouwen.

## <a name="next-steps"></a>Volgende stappen

- Zie [problemen oplossen in azure monitor-waarschuwingen](alerts-troubleshoot.md)voor algemene informatie over het oplossen van problemen met waarschuwingen en meldingen.
