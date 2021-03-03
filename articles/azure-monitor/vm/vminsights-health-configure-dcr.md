---
title: Bewaking configureren in VM Insights-gast status met behulp van regels voor gegevens verzameling (preview)
description: Hierin wordt beschreven hoe u de standaard bewaking in VM Insights-gast status op schaal wijzigt met behulp van Resource Manager-sjablonen.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2020
ms.openlocfilehash: 907aea16b018fb5dd3846db546787d132f8f5a9f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731218"
---
# <a name="configure-monitoring-in-vm-insights-guest-health-using-data-collection-rules-preview"></a>Bewaking configureren in VM Insights-gast status met behulp van regels voor gegevens verzameling (preview)
Met de [VM Insights-gast status](vminsights-health-overview.md) kunt u de status van een virtuele machine bekijken, zoals gedefinieerd door een set prestatie metingen die regel matig worden steek proeven. In dit artikel wordt beschreven hoe u de standaard bewaking op meerdere virtuele machines kunt wijzigen met behulp van regels voor het verzamelen van gegevens.


## <a name="monitors"></a>Monitors
De status van een virtuele machine wordt bepaald door de status van het [totaliseren](vminsights-health-overview.md#health-rollup-policy) van de monitor. Er zijn twee soorten monitors in de VM Insights-gast status, zoals wordt weer gegeven in de volgende tabel.

| Monitor | Beschrijving |
|:---|:---|
| Unit-monitor | Meet een bepaald aspect van een resource of toepassing. Dit kan het controleren van een prestatiemeteritem zijn om de prestaties van de resource of de beschikbaarheid ervan te bepalen. |
| Aggregaatmonitor | Groepeert meerdere monitors om één samengevoegde status te bieden. Een aggregaatmonitor kan één of meer unit-monitors en andere aggregaatmonitors bevatten. |

De set monitors die worden gebruikt door de VM Insights-gast status en de configuratie ervan kunnen niet rechtstreeks worden gewijzigd. U kunt [onderdrukkingen](#overrides) maken, waarbij u het gedrag van de standaard configuratie wijzigt. Onderdrukkingen worden gedefinieerd in regels voor gegevens verzameling. U kunt meerdere regels voor het verzamelen van gegevens maken met meerdere onderdrukkingen om uw vereiste bewakings configuratie te creëren.

## <a name="monitor-properties"></a>Monitor eigenschappen
In de volgende tabel worden de eigenschappen beschreven die op elke monitor kunnen worden geconfigureerd.

| Eigenschap | Monitors | Beschrijving |
|:---|:---|:---|
| Ingeschakeld | Samenvoegen<br>Eenheid | Als deze eigenschap waar is, wordt de status monitor berekend en wordt de status van de virtuele machine bijgedragen. Er kan een waarschuwing worden geactiveerd wanneer waarschuwingen zijn ingeschakeld. |
| Waarschuwingen | Samenvoegen<br>Eenheid | Als deze eigenschap waar is, wordt er een waarschuwing voor de monitor geactiveerd wanneer deze naar een slechte status wordt verplaatst. Als deze eigenschap onwaar is, wordt de status van de monitor nog steeds bijdraagt aan de status van de virtuele machine, waardoor een waarschuwing kan worden geactiveerd. |
| Waarschuwing | Eenheid | Criteria voor de waarschuwings status. Als geen, dan wordt er nooit een waarschuwings status door de monitor ingevoerd. |
| Kritiek | Eenheid | Criteria voor de kritieke status. Als geen, dan zal de monitor nooit een kritieke status invoeren. |
| Evaluatie frequentie | Eenheid | Frequentie waarmee de status wordt geëvalueerd. |
| Lookback | Eenheid | Grootte van lookback-venster in seconden. Zie het [monitorConfiguration-element](#monitorconfiguration-element) voor een gedetailleerde beschrijving. |
| Evaluatie type | Eenheid | Hiermee definieert u welke waarde moet worden gebruikt in de voor beeld-set. Zie het [monitorConfiguration-element](#monitorconfiguration-element) voor een gedetailleerde beschrijving. |
| Min-voor beeld | Eenheid | Minimum aantal waarden dat moet worden gebruikt om de waarde te berekenen. |
| Maximum voor beeld | Eenheid | Het maximum aantal waarden dat moet worden gebruikt om de waarde te berekenen. |


## <a name="default-configuration"></a>Standaardconfiguratie
De volgende tabel bevat de standaard configuratie voor elke monitor. Deze standaard configuratie kan niet rechtstreeks worden gewijzigd, maar u kunt [onderdrukkingen](#overrides) definiëren waarmee de monitor configuratie voor bepaalde virtuele machines wordt gewijzigd.


| Monitor | Ingeschakeld | Waarschuwingen | Waarschuwing | Kritiek | Evaluatie frequentie | Lookback | Evaluatie type | Min-voor beeld | Maximum aantal steek proeven |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| CPU-gebruik  | Waar | Niet waar | Geen | \> 90%    | 60 sec. | 240 SEC | Min | 2 | 3 |
| Beschikbaar geheugen | Waar | Niet waar | Geen | \< 100 MB | 60 sec. | 240 SEC | Max | 2 | 3 |
| Bestandssysteem      | Waar | Niet waar | Geen | \< 100 MB | 60 sec. | 120 sec | Max | 1 | 1 |


## <a name="overrides"></a>Overschrijvingen
Een *onderdrukking* wijzigt een of meer eigenschappen van een monitor. Een onderdrukking kan bijvoorbeeld een monitor uitschakelen die standaard is ingeschakeld, waarschuwings criteria definiëren voor de monitor of de kritieke drempel waarde van de monitor wijzigen. 

Onderdrukkingen worden gedefinieerd in een [gegevens verzamelings regel (DCR)](../agents/data-collection-rule-overview.md). U kunt meerdere Dcr's met verschillende sets onderdrukkingen maken en deze Toep assen op meerdere virtuele machines. U past een DCR toe op een virtuele machine door een koppeling te maken zoals beschreven in [gegevens verzameling configureren voor de Azure monitor-agent (preview)](../agents/data-collection-rule-azure-monitor-agent.md#data-collection-rule-associations).


## <a name="multiple-overrides"></a>Meerdere onderdrukkingen

Eén monitor kan meerdere onderdrukkingen hebben. Als de onderdrukkingen verschillende eigenschappen definiëren, is de resulterende configuratie een combi natie van alle onderdrukkingen.

De `memory|available` monitor geeft bijvoorbeeld geen waarschuwings drempel op of Schakel standaard waarschuwingen in. Houd rekening met de volgende onderdrukkingen die worden toegepast op deze monitor:

- Override 1 definieert `alertConfiguration.isEnabled` eigenschaps waarde als `true`
- Onderdrukking 2 definieert `monitorConfiguration.warningCondition` met een drempel waarde van `< 250` .

De resulterende configuratie is een monitor die in de status van een waarschuwing wordt weer gegeven wanneer er minder dan 250Mb geheugen beschikbaar is en een waarschuwing voor de ernst 2 wordt gemaakt en een kritieke status krijgt als er minder dan 100 MB beschik bare geheugen beschikbaar is en de ernst van de waarschuwing wordt gemaakt (of bestaande waarschuwing wijzigt van Ernst 2 in 1 als deze al bestaat).

Als twee onderdrukkingen dezelfde eigenschap op dezelfde monitor definiëren, heeft één waarde prioriteit. Onderdrukkingen worden toegepast op basis van hun [bereik](#scopes-element), van het algemeen tot het meest specifieke. Dit betekent dat de meest specifieke onderdrukkingen de grootste kans hebben om te worden toegepast. De specifieke volg orde is als volgt:

1. Globaal 
2. Abonnement
3. Resourcegroep
4. Een virtuele machine. 

Als meerdere onderdrukkingen op hetzelfde bereik niveau dezelfde eigenschap op dezelfde monitor definiëren, worden ze toegepast in de volg orde waarin ze in de DCR worden weer gegeven. Als de onderdrukkingen zich in verschillende Dcr's bevinden, worden ze in alfabetische volg orde van de bron-Id's van DCR toegepast.


## <a name="data-collection-rule-configuration"></a>Configuratie van de gegevens verzamelings regel
De JSON-elementen in de gegevens verzamelings regel waarmee onderdrukkingen worden gedefinieerd, worden beschreven in de volgende secties. Een volledig voor beeld wordt gegeven in de [voorbeeld regel](#sample-data-collection-rule)voor het verzamelen van gegevens.

## <a name="extensions-structure"></a>extensie structuur
De gast status wordt geïmplementeerd als een uitbrei ding van de Azure Monitor-agent, waardoor onderdrukkingen worden gedefinieerd in het `extensions` element van de gegevens verzamelings regel. 

```json
"extensions": [
    {
        "name": "Microsoft-VMInsights-Health",
        "streams": [
            "Microsoft-HealthStateChange"
        ],
        "extensionName": "HealthExtension",
        "extensionSettings": {   }
    }
]
```

| Element | Vereist | Beschrijving |
|:---|:---|:---|
| `name` | Ja | Door de gebruiker gedefinieerde teken reeks voor de extensie. |
| `streams` | Ja | Lijst met stromen waarnaar de gast status gegevens worden verzonden. Dit moet **micro soft-HealthStateChange** omvatten.  |
| `extensionName` | Ja | De naam van de extensie. Dit moet **HealthExtension** zijn. |
| `extensionSettings` | Ja | Matrix van `healthRuleOverride` elementen die moeten worden toegepast op de standaard configuratie. |


## <a name="extensionsettings-element"></a>Geldige-element
Bevat de instellingen voor de extensie.

```json
"extensionSettings": {
    "schemaVersion": "1.0",
    "contentVersion": "",
    "healthRuleOverrides": [ ]
}
```

| Element | Vereist | Beschrijving |
|:---|:---|:---|
| `schemaVersion` | Ja | Teken reeks die door micro soft is gedefinieerd om het verwachte schema van het element weer te geven. Momenteel moet worden ingesteld op 1,0 |
| `contentVersion` | Nee | De teken reeks die door de gebruiker is gedefinieerd om verschillende versies van de status configuratie, indien nodig, bij te houden. |
| `healthRuleOverrides` | Ja | Matrix van `healthRuleOverride` elementen die moeten worden toegepast op de standaard configuratie. |

## <a name="healthrulesoverrides-element"></a>healthRulesOverrides-element
Bevat een of meer `healthRuleOverride` elementen die elk een onderdrukking definiëren.

```json
"healthRuleOverrides": [
    {
        "scopes": [ ],
        "monitors": [ ],
        "monitorConfiguration": { },
        "alertConfiguration": {  },
        "isEnabled": true|false
    }
]
```

| Element | Vereist | Beschrijving |
|:---|:---|:---|
| `scopes` | Ja | Lijst met een of meer bereiken waarmee de virtuele machines worden opgegeven waarop deze onderdrukking van toepassing is. Hoewel de DCR is gekoppeld aan een virtuele machine, moet de virtuele machine binnen een bereik vallen zodat de onderdrukking kan worden toegepast. |
| `monitors` | Ja | Lijst met een of meer teken reeksen die bepalen welke monitors deze onderdrukking zullen ontvangen.  |
| `monitorConfiguration` | Nee | Configuratie voor de monitor, inclusief statussen en hoe deze worden berekend. |
| `alertConfiguration` | Nee | Configuratie van waarschuwingen voor de monitor. |
| `isEnabled` | Nee | Hiermee wordt bepaald of monitor is ingeschakeld of niet. Uitgeschakelde monitor schakelt over naar speciale *Uitgeschakelde* status en statussen uitgeschakeld, tenzij u deze opnieuw inschakelt. Als u dit weglaat, wordt de status van de monitor overgenomen van de bovenliggende monitor in de hiërarchie. |


## <a name="scopes-element"></a>element bereiken
Elke onderdrukking heeft een of meer bereiken met de definitie van de virtuele machines waarop de onderdrukking moet worden toegepast. Het bereik kan een abonnement, een resource groep of een enkele virtuele machine zijn. Zelfs als de onderdrukking zich in een DCR bevindt die is gekoppeld aan een bepaalde virtuele machine, wordt deze alleen toegepast op die virtuele machine als de virtuele machine zich binnen een van de bereiken van de onderdrukking bevindt. Zo kunt u een kleiner aantal Dcr's koppelen aan een set virtuele machines, maar beschikt u over gedetailleerde controle over de toewijzing van elke onderdrukking binnen de DCR zelf. U kunt een kleine set Dcr's maken en deze koppelen aan een set virtuele machines met behulp van beleid terwijl u onderdrukkingen voor de status monitor opgeeft voor verschillende subsets van die virtuele machines met het element scopes.

In de volgende tabel ziet u voor beelden van verschillende bereiken.

| Bereik | Voorbeeld |
|:---|:---|
| Eén virtuele machine | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name/providers/Microsoft.Compute/virutalMachines/my-vm` |
| Alle virtuele machines in een resource groep | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name` |
| Alle virtuele machines in een abonnement | `/subscriptions/00000000-0000-0000-0000-000000000000/` |
| Alle virtuele machines waaraan de regel voor gegevens verzameling is gekoppeld | `*` |


## <a name="monitors-element"></a>bewaakt element
Lijst met een of meer teken reeksen die definiëren welke monitors in de status hiërarchie deze onderdrukking zullen ontvangen. Elk element kan een monitor naam of type naam zijn die overeenkomt met een of meer monitors die deze onderdrukking zullen ontvangen. 

```json
"monitors": [
    "<monitor name>"
 ],
```



De volgende tabel geeft een lijst van de huidige beschik bare monitor namen.

| Type naam | Naam | Beschrijving |
|:---|:---|:---|
| basis | basis | Monitor op het hoogste niveau die de status van de virtuele machine weergeeft. | |
| CPU-gebruik | CPU-gebruik | Monitor voor CPU-gebruik. | |
| logische schijven | logische schijven | Aggregaatmonitor voor het controleren van de status van alle bewaakte schijven op virtuele Windows-machines. | |
| logische schijven\|* | logische schijven \| C:<br>logische schijven \| D: | Aggregaatmonitor voor het bijhouden van de status van een bepaalde schijf op een virtuele Windows-machine. | 
| logische schijven \| * \| vrije ruimte | logische schijven \| C: \| vrije ruimte<br>logische schijven \| D: \| vrije ruimte | Monitor voor beschik bare schijf ruimte op virtuele Windows-machines. |
| bestands systemen | bestands systemen | Aggregaatmonitor voor het controleren van de status van alle bestands systemen op virtuele Linux-machines. |
| bestands systemen\|* | bestands systemen\|/<br>bestandssysteem \| /mnt | Aggregaatmonitor voor het bijhouden van de status van een bestands systeem van een virtuele Linux-machine. | bestands systemen|/var/log |
| bestandssysteem \| * \| vrije ruimte | bestandssysteem \| / \| vrije ruimte<br>bestands systemen \| /mnt \| vrije ruimte | Monitor voor beschik bare schijf ruimte op het bestands systeem van de virtuele Linux-machine. | 
| geheugen | geheugen | Aggregaatmonitor voor het controleren van de status van het geheugen van de virtuele machine. | |
| \|beschikbaar geheugen| \|beschikbaar geheugen | Beschik bare geheugen tracering bewaken op de virtuele machine. | |


## <a name="alertconfiguration-element"></a>alertConfiguration-element
Hiermee wordt aangegeven of een waarschuwing moet worden gemaakt op basis van de monitor.

```json
"alertConfiguration": {
    "isEnabled": true|false
}
```

| Element | Verplicht | Beschrijving | 
|:---|:---|:---|
| `isEnabled` | Nee | Als deze eigenschap is ingesteld op True, wordt er een waarschuwing gegenereerd wanneer wordt overgeschakeld naar een kritieke of waarschuwings status en wordt er een waarschuwing weer gegeven wanneer u terugkeert naar een goede status. Als deze eigenschap onwaar is of wordt wegge laten, wordt er geen waarschuwing gegenereerd.  |


## <a name="monitorconfiguration-element"></a>monitorConfiguration-element
Is alleen van toepassing op unit monitors. Hiermee definieert u de configuratie voor de monitor, inclusief statussen en hoe deze worden berekend.

Met para meters definieert u het algoritme voor het berekenen van de metrische waarde die moet worden vergeleken met de drempel waarden. In plaats van een voor beeld van gegevens uit de onderliggende metriek te gebruiken, evalueert de monitor verschillende metrische voor beelden die binnen het venster worden ontvangen van de evaluatie tijd en `lookbackSec` geleden. Alle voor beelden die in deze periode worden ontvangen, worden beschouwd en als het aantal steek proeven groter is dan `maxSamples` , worden oudere voor beelden `maxSamples` genegeerd. 

Als er minder steek proeven zijn in het lookback-interval dan `minSamples` , wordt de monitor overgeschakeld naar een *onbekende* status die aangeeft dat er onvoldoende gegevens zijn om een weloverwogen beslissing te nemen over de status van de onderliggende meet waarden. Als er meer steek proeven `minSamples` beschikbaar zijn, wordt een statistische functie die is opgegeven door de evaluationType para meter, die door ons is ingesteld, uitgevoerd voor het berekenen van een enkele waarde.


```json
"monitorConfiguration": {
        "evaluationType" : "<type-of-evaluation>",
        "lookbackSecs": <lookback-number-of-seconds>,
        "evaluationFrequencySecs": <evaluation-frequency-number-of-seconds>,
        "minSamples": <minimum-samples>,
        "maxSamples": <maximum-samples>,
        "warningCondition": {  },
        "criticalCondition": {  }
    }
}
```

| Element | Verplicht | Beschrijving | 
|:---|:---|:---|
| `evaluationFrequencySecs` | Nee | Hiermee wordt de frequentie voor de status evaluatie gedefinieerd. Elke monitor wordt geëvalueerd op het moment dat de agent wordt gestart en volgens een regel matig interval dat door deze para meter wordt gedefinieerd. |
| `lookbackSecs`   | Nee | Grootte van lookback-venster in seconden. |
| `evaluationType` | Nee | `min` – minimale waarde uit de volledige Voorbeeldset halen<br>`max` -maximum waarde van de volledige Voorbeeldset halen<br>`avg` : gemiddelde waarden instellen voor steek proeven<br>`all` : elke enkele waarde in de set vergelijken met drempel waarden. De status van monitor switches als en alleen als alle voor beelden in de set voldoen aan de drempel waarde. |
| `minSamples`     | Nee | Minimum aantal waarden dat moet worden gebruikt om de waarde te berekenen. |
| `maxSamples`     | Nee | Het maximum aantal waarden dat moet worden gebruikt om de waarde te berekenen. |
| `warningCondition`  | Nee | Drempel waarde en vergelijkings logica voor de waarschuwings voorwaarde. |
| `criticalCondition` | Nee | Drempel waarde en vergelijkings logica voor de kritieke situatie. |


## <a name="warningcondition-element"></a>warningCondition-element
Hiermee worden de drempel-en vergelijkings logica voor de waarschuwings voorwaarde gedefinieerd. Als dit element niet is opgenomen, wordt de status van de monitor nooit gewijzigd in de waarschuwing status.

```json
"warningCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| Eigenschap | Verplicht | Beschrijving | 
|:---|:---|:---|
| `isEnabled` | Nee | Hiermee wordt aangegeven of voor waarde is ingeschakeld. Als deze eigenschap is ingesteld op **False**, wordt de voor waarde uitgeschakeld, hoewel de drempel waarde en de operator eigenschappen kunnen worden ingesteld. |
| `threshold` | Nee | Definieert drempel om de geëvalueerde waarde te vergelijken. |
| `operator`  | Nee | Definieert de vergelijkings operator die in de drempel expressie moet worden gebruikt. Mogelijke waarden: >, <, >=, <=, = =. |


## <a name="criticalcondition-element"></a>criticalCondition-element
Hiermee worden de drempel-en vergelijkings logica voor de kritieke voor waarde gedefinieerd. Als dit element niet is opgenomen, wordt de status van de monitor nooit gewijzigd in een kritieke status.

```json
"criticalCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| Eigenschap | Verplicht | Beschrijving | 
|:---|:---|:---|
| `isEnabled` | Nee | Hiermee wordt aangegeven of voor waarde is ingeschakeld. Als deze eigenschap is ingesteld op **False**, wordt de voor waarde uitgeschakeld, hoewel de drempel waarde en de operator eigenschappen kunnen worden ingesteld. |
| `threshold` | Nee | Definieert drempel om de geëvalueerde waarde te vergelijken. |
| `operator`  | Nee | Definieert de vergelijkings operator die in de drempel expressie moet worden gebruikt. Mogelijke waarden: >, <, >=, <=, = =. |

## <a name="sample-data-collection-rule"></a>Regel voor het verzamelen van voorbeeld gegevens
Zie [een virtuele machine inschakelen met de Resource Manager-sjabloon](vminsights-health-enable.md#enable-a-virtual-machine-using-resource-manager-template)voor een voor beeld van een regel voor het verzamelen van gegevens.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [regels](../agents/data-collection-rule-overview.md)voor het verzamelen van gegevens.