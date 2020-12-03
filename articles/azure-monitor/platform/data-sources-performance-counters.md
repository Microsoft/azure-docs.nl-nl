---
title: Windows-en Linux-prestatie gegevens bronnen met Log Analytics agent in Azure Monitor verzamelen
description: Prestatie meter items worden verzameld door Azure Monitor om de prestaties van Windows-en Linux-agents te analyseren.  In dit artikel wordt beschreven hoe u een verzameling prestatie meter items configureert voor Windows-en Linux-agents, hoe deze worden opgeslagen in de werk ruimte en hoe u deze kunt analyseren in de Azure Portal.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: 533d4a83ea73b98e26a57febc077a607bcb25465
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532296"
---
# <a name="collect-windows-and-linux-performance-data-sources-with-log-analytics-agent"></a>Windows-en Linux-prestatie gegevens bronnen met Log Analytics-agent verzamelen
Prestatie meter items in Windows en Linux bieden inzicht in de prestaties van hardwareonderdelen, besturings systemen en toepassingen.  Azure Monitor kunt prestatie meter items van Log Analytics agents met veelvuldige intervallen verzamelen voor analyse van bijna realtime (NRT), naast het samen voegen van prestatie gegevens voor langere termijn analyse en rapportage.

> [!IMPORTANT]
> Dit artikel heeft betrekking op het verzamelen van prestatie gegevens met de [log Analytics-agent](log-analytics-agent.md) die een van de agents die door Azure monitor worden gebruikt. Andere agents verzamelen verschillende gegevens en worden anders geconfigureerd. Zie [overzicht van Azure monitor agents](agents-overview.md) voor een lijst met beschik bare agents en de gegevens die ze kunnen verzamelen.

![Prestatiemeteritems](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Prestatie meter items configureren
Configureer prestatie meter items [in het menu Data van geavanceerde instellingen](agent-data-sources.md#configuring-data-sources) voor de werk ruimte log Analytics.

Wanneer u voor het eerst Windows-of Linux-prestatie meter items voor een nieuwe werk ruimte configureert, krijgt u de optie om snel verschillende algemene prestatie meter items te maken.  Ze worden weergegeven met een selectievakje ernaast.  Zorg ervoor dat alle items die u in eerste instantie wilt maken, worden gecontroleerd en klik vervolgens op **de geselecteerde prestatie meter items toevoegen**.

Voor Windows-prestatie meter items kunt u een specifiek exemplaar voor elk prestatie meter item kiezen. Voor Linux-prestatie meter items geldt het exemplaar van elk item dat u kiest, van toepassing op alle onderliggende items van het bovenliggende item. In de volgende tabel ziet u de algemene instanties die beschikbaar zijn voor de prestatie meter items Linux en Windows.

| Exemplaarnaam | Beschrijving |
| --- | --- |
| \_Totaal |Totaal van alle exemplaren |
| \* |Alle instanties |
| (/&#124;/var) |Komt overeen met instanties met de naam:/of/var |

### <a name="windows-performance-counters"></a>Windows-prestatiemeteritems

![Windows-prestatie meter items configureren](media/data-sources-performance-counters/configure-windows.png)

Volg deze procedure om een nieuw Windows-prestatie meter item toe te voegen om te verzamelen. V2 Windows-prestatie meter items worden niet ondersteund.

1. Typ de naam van de teller in het tekstvak in de indeling *object (instantie) \counter*.  Wanneer u begint te typen, wordt er een overeenkomende lijst met algemene prestatie meter items weer gegeven.  U kunt een item in de lijst selecteren of een van uw eigen items typen.  U kunt ook alle instanties voor een bepaald prestatie meter item retour neren door *object\counter* op te geven.  

    Bij het verzamelen van SQL Server prestatie meter items van benoemde instanties beginnen alle benoemde exemplaar items met *MSSQL $* en gevolgd door de naam van het exemplaar.  Als u bijvoorbeeld het item cache treffers van de logboeken voor alle data bases wilt verzamelen uit het database prestatie object voor benoemde SQL-instantie INST2, geeft u op `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio` .

2. Klik **+** of druk op **Enter** om de teller toe te voegen aan de lijst.
3. Wanneer u een teller toevoegt, wordt de standaard waarde van 10 seconden gebruikt voor het **steekproef interval**.  U kunt dit wijzigen in een hogere waarde van Maxi maal 1800 seconden (30 minuten) als u de opslag vereisten van de verzamelde prestatie gegevens wilt reduceren.
4. Wanneer u klaar bent met het toevoegen van items, klikt u op de knop **Opslaan** boven aan het scherm om de configuratie op te slaan.

### <a name="linux-performance-counters"></a>Linux-prestatie meter items

![Linux-prestatie meter items configureren](media/data-sources-performance-counters/configure-linux-1.png)

Volg deze procedure om een nieuw Linux-prestatie meter item toe te voegen om te verzamelen.

1. Typ de naam van de teller in het tekstvak in de indeling *object (instantie) \counter*.  Wanneer u begint te typen, wordt er een overeenkomende lijst met algemene prestatie meter items weer gegeven.  U kunt een item in de lijst selecteren of een van uw eigen items typen.  
1. Klik **+** of druk op **Enter** om de teller toe te voegen aan de lijst met andere tellers voor het object.
1. Alle tellers voor een object gebruiken hetzelfde **steekproef interval**.  De standaardinstelling is 10 seconden.  U kunt dit wijzigen in een hogere waarde van Maxi maal 1800 seconden (30 minuten) als u de opslag vereisten van de verzamelde prestatie gegevens wilt reduceren.
1. Wanneer u klaar bent met het toevoegen van items, klikt u op de knop **Opslaan** boven aan het scherm om de configuratie op te slaan.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Linux-prestatie meter items configureren in het configuratie bestand
In plaats van Linux-prestatie meter items te configureren met behulp van de Azure Portal, hebt u de mogelijkheid om configuratie bestanden te bewerken in de Linux-agent.  De prestatie gegevens die moeten worden verzameld, worden bepaald door de configuratie in **/etc/opt/Microsoft/omsagent/ \<workspace id\> /conf/omsagent.conf**.

Elk object, of elke categorie, van prestatie gegevens die moeten worden verzameld, moet in het configuratie bestand als één `<source>` element worden gedefinieerd. De syntaxis is gebaseerd op het onderstaande patroon.

```xml
<source>
    type oms_omi  
    object_name "Processor"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>
```


De para meters in dit element worden in de volgende tabel beschreven.

| Parameters | Beschrijving |
|:--|:--|
| object \_ naam | Object naam voor de verzameling. |
| regex-exemplaar \_ |  Een *reguliere expressie* die definieert welke exemplaren moeten worden verzameld. De waarde: `.*` Hiermee geeft u alle exemplaren op. Als u metrische gegevens van de processor alleen voor het \_ totale exemplaar wilt verzamelen, kunt u opgeven `_Total` . Als u proces metrische gegevens alleen voor de crond-of sshd-instanties wilt verzamelen, kunt u het volgende opgeven: `(crond\|sshd)` . |
| \_regex-naam van teller \_ | Een *reguliere expressie* waarmee wordt gedefinieerd welke items (voor het object) moeten worden verzameld. Als u alle tellers voor het object wilt verzamelen, geeft u het volgende op: `.*` . Als u alleen tellers voor wissel ruimte voor het object memory wilt verzamelen, kunt u bijvoorbeeld het volgende opgeven: `.+Swap.+` |
| interval | De frequentie waarmee de tellers van het object worden verzameld. |


De volgende tabel bevat de objecten en prestatie meter items die u in het configuratie bestand kunt opgeven.  Er zijn extra tellers beschikbaar voor bepaalde toepassingen, zoals wordt beschreven in [prestatie meter items verzamelen voor Linux-toepassingen in azure monitor](data-sources-linux-applications.md).

| Object naam | Tellernaam |
|:--|:--|
| Logische schijf | % Vrije inodes |
| Logische schijf | Percentage beschik bare ruimte |
| Logische schijf | % Gebruikte inodes |
| Logische schijf | Percentage gebruikte ruimte |
| Logische schijf | Gelezen bytes per seconde |
| Logische schijf | Lees bewerkingen per seconde |
| Logische schijf | Schijf overdrachten per seconde |
| Logische schijf | Geschreven bytes per seconde |
| Logische schijf | Schrijf bewerkingen per seconde |
| Logische schijf | Beschik bare mega bytes |
| Logische schijf | Bytes van logische schijf per seconde |
| Geheugen | Percentage beschikbaar geheugen |
| Geheugen | Percentage beschik bare wissel ruimte |
| Geheugen | Percentage gebruikt geheugen |
| Geheugen | Percentage gebruikte wissel ruimte |
| Geheugen | Beschikbaar geheugen in mega bytes |
| Geheugen | Beschik bare mega bytes wisselen |
| Geheugen | Gelezen pagina's per seconde |
| Geheugen | Geschreven pagina's per seconde |
| Geheugen | Pagina's per seconde |
| Geheugen | Gebruikte MB wissel ruimte |
| Geheugen | Gebruikt geheugen Mbytes |
| Netwerk | Totaal aantal verzonden bytes |
| Netwerk | Totaal aantal ontvangen bytes |
| Netwerk | Totaal aantal bytes |
| Netwerk | Totaal aantal verzonden pakketten |
| Netwerk | Totaal aantal ontvangen pakketten |
| Netwerk | Totaal aantal RX-fouten |
| Netwerk | Totaal aantal TX-fouten |
| Netwerk | Totaal aantal conflicten |
| Fysieke schijf | Gemiddelde Lees tijd schijf |
| Fysieke schijf | Gemiddelde tijd schijf overdracht |
| Fysieke schijf | Gemiddelde schrijf tijd schijf |
| Fysieke schijf | Bytes van fysieke schijf per seconde |
| Proces | Pct-geprivilegieerde tijd |
| Proces | Pct-gebruikers tijd |
| Proces | Gebruikte geheugen-kBytes |
| Proces | Virtueel gedeeld geheugen |
| Processor | Percentage DPC-tijd |
| Processor | Percentage niet-actieve tijd |
| Processor | Percentage interrupt-tijd |
| Processor | % I/o-wacht tijd |
| Processor | Percentage tijd in Nice |
| Processor | Percentage tijd in beschermde modus |
| Processor | Percentage processortijd |
| Processor | Percentage gebruikers tijd |
| Systeem | Vrij fysiek geheugen |
| Systeem | Vrije ruimte in wissel geheugen bestanden |
| Systeem | Vrij virtueel geheugen |
| Systeem | Processen |
| Systeem | Grootte opgeslagen in Wissel bestanden |
| Systeem | Uptime |
| Systeem | Gebruikers |


Hieronder volgt de standaard configuratie voor metrische gegevens over prestaties.

```xml
<source>
    type oms_omi
    object_name "Physical Disk"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 5m
</source>

<source>
    type oms_omi
    object_name "Logical Disk"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 5m
</source>

<source>
    type oms_omi
    object_name "Processor"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>

<source>
    type oms_omi
    object_name "Memory"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>
```

## <a name="data-collection"></a>Gegevens verzamelen
Azure Monitor verzamelt alle opgegeven prestatie meter items op het opgegeven steekproef interval voor alle agents waarop dat item is geïnstalleerd.  De gegevens worden niet geaggregeerd en de onbewerkte gegevens zijn beschikbaar in alle logboek query weergaven voor de duur die is opgegeven door de log Analytics-werk ruimte.

## <a name="performance-record-properties"></a>Eigenschappen van prestatie record
Prestatie records hebben het type **perf** en hebben de eigenschappen in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| Computer |Computer waarop de gebeurtenis is verzameld. |
| CounterName |Naam van het prestatie meter item |
| CounterPath |Het volledige pad van de teller in de teller van het formulier \\ \\ \<Computer> \\ object (exemplaar) \\ . |
| CounterValue |Numerieke waarde van het prestatie meter item. |
| InstanceName |De naam van het gebeurtenis exemplaar.  Leeg als er geen exemplaar is. |
| ObjectName |Naam van het prestatie object |
| SourceSystem |Type agent waaruit de gegevens zijn verzameld. <br><br>OpsManager: Windows-agent, hetzij direct Connect of SCOM <br> Linux: alle Linux-agents  <br> Opslag – Azure Diagnostics |
| TimeGenerated |De datum en tijd waarop de gegevens worden voor bereid. |

## <a name="sizing-estimates"></a>Grootte schattingen
 Een ruwe schatting voor het verzamelen van een bepaalde teller met een interval van 10 seconden is ongeveer 1 MB per dag per exemplaar.  U kunt de opslag vereisten van een bepaald item schatten met de volgende formule.

> 1 MB x (aantal tellers) x (aantal agents) x (aantal exemplaren)

## <a name="log-queries-with-performance-records"></a>Query's vastleggen met prestatie records
De volgende tabel bevat verschillende voor beelden van logboek query's waarmee prestatie records worden opgehaald.

| Query’s uitvoeren | Beschrijving |
|:--- |:--- |
| Prestaties |Alle prestatie gegevens |
| Perf &#124; waarbij computer = "mijn systeem" |Alle prestatie gegevens van een bepaalde computer |
| Perf &#124; waarbij CounterName = = "huidige wachtrij lengte voor schijf" |Alle prestatie gegevens voor een bepaald prestatie meter item |
| Perf &#124; waarbij ObjectName = = "processor" en CounterName = = "% processor tijd" en INSTANCENAME = = "_Total" &#124; samenvatten AVGCPU = AVG (CounterValue) door computer |Gemiddeld CPU-gebruik op alle computers |
| Perf &#124; waarbij CounterName = = "% processor tijd" &#124; samenvatten AggregatedValue = Max (CounterValue) door computer |Maxi maal CPU-gebruik op alle computers |
| Perf &#124; waarbij object naam = = "logische schijf" en CounterName = = "huidige wachtrij lengte voor schijven" en computer = = "MyComputerName" &#124; samen vatting AggregatedValue = AVG (CounterValue) door INSTANCENAME |De gemiddelde wachtrij lengte van de huidige schijf voor alle exemplaren van een bepaalde computer |
| Perf &#124; waarbij CounterName = = "schijf overdrachten per seconde" &#124; samenvatten AggregatedValue = percentiel (CounterValue, 95) per computer |95e percentiel van schijf overdrachten per seconde op alle computers |
| Perf &#124; waarbij CounterName = = "% processor tijd" en INSTANCENAME = = "_Total" &#124; samenvatten AggregatedValue = AVG (CounterValue) by bin (TimeGenerated, 1U), computer |Gemiddeld uur CPU-gebruik op alle computers |
| Perf &#124; waarbij computer = = "mijn systeem" en CounterName startswith_cs "%" en INSTANCENAME = = "_Total" &#124; samenvatten AggregatedValue = percentiel (CounterValue, 70) op bin (TimeGenerated, 1U), CounterName | 70 percentiel van elk% procent item voor een bepaalde computer |
| Perf &#124; waarbij CounterName = = "% processor tijd" en INSTANCENAME = = "_Total" and computer = "mijn werk" &#124; samenvatting ["min (CounterValue)"] = min (CounterValue), ["AVG (CounterValue)"] = AVG (CounterValue), ["percentile75 (CounterValue)"] = percentiel (CounterValue, 75), ["Max (CounterValue)"] = Max (CounterValue) per bak (TimeGenerated, 1U), computer |Gemiddeld uur, minimum, maximum en 75-percentiel CPU-gebruik voor een specifieke computer |
| Perf &#124; waarbij ObjectName = = "MSSQL $ INST2: data bases" en INSTANCENAME = = "Master" | Alle prestatie gegevens van het prestatie object Data Base voor de hoofd database van de benoemde SQL Server instantie INST2.  




## <a name="next-steps"></a>Volgende stappen
* [Verzamelen van prestatie meter items van Linux-toepassingen](data-sources-linux-applications.md) , waaronder de MySQL-en Apache HTTP-server.
* Meer informatie over [logboek query's](../log-query/log-query-overview.md) voor het analyseren van de gegevens die zijn verzameld uit gegevens bronnen en oplossingen.  
* Verzamelde gegevens exporteren naar [Power bi](powerbi.md) voor extra visualisaties en analyse.
