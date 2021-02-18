---
title: Aangepaste logboeken verzamelen met de Log Analytics-agent in Azure Monitor
description: Azure Monitor kunt gebeurtenissen verzamelen uit tekst bestanden op zowel Windows-als Linux-computers.  In dit artikel wordt beschreven hoe u een nieuw aangepast logboek en Details definieert van de records die ze in Azure Monitor maken.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: 73496e350a5e40a3945343271b76c6d883991b62
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610569"
---
# <a name="collect-custom-logs-with-log-analytics-agent-in-azure-monitor"></a>Aangepaste logboeken verzamelen met de Log Analytics-agent in Azure Monitor

Met de gegevens bron aangepaste logboeken voor de Log Analytics-agent in Azure Monitor kunt u gebeurtenissen verzamelen uit tekst bestanden op zowel Windows-als Linux-computers. Veel toepassingen registreren gegevens in tekst bestanden in plaats van standaard logboek registratie Services, zoals Windows-gebeurtenis logboek of syslog. Zodra de gegevens zijn verzameld, kunt u deze in afzonderlijke velden in uw query's parseren of de gegevens tijdens het verzamelen naar afzonderlijke velden extra heren.

> [!IMPORTANT]
> In dit artikel wordt beschreven hoe u aangepaste logboeken verzamelt met de [log Analytics-agent](../platform/log-analytics-agent.md) die een van de agents die door Azure monitor worden gebruikt. Andere agents verzamelen verschillende gegevens en worden anders geconfigureerd. Zie [overzicht van Azure monitor agents](../agents/agents-overview.md) voor een lijst met beschik bare agents en de gegevens die ze kunnen verzamelen.

![Aangepaste logboek verzameling](media/data-sources-custom-logs/overview.png)

De logboek bestanden die moeten worden verzameld, moeten voldoen aan de volgende criteria.

- Het logboek moet één vermelding per regel bevatten of een tijds tempel hebben die overeenkomt met een van de volgende notaties aan het begin van elk item.

    JJJJ-MM-DD UU: MM: SS<br>M/D/JJJJ UU: MM: SS AM/PM<br>Ma DD, jjjj uu: MM: SS<br />JJMMDD uu: mm: SS<br />ddMMyy uu: mm: SS<br />MMM d uu: mm: SS<br />DD/MMM/jjjj: uu: mm: SS zzz<br />JJJJ-MM-DDTuu: mm: ssK

- Het logboek bestand mag geen circulaire logboek registratie of logboek rotatie toestaan, waarbij het bestand wordt overschreven door nieuwe vermeldingen.
- Het logboek bestand moet ASCII-of UTF-8-code ring gebruiken.  Andere indelingen, zoals UTF-16, worden niet ondersteund.
- Voor Linux wordt tijdzone conversie niet ondersteund voor tijds tempels in de logboeken.

>[!NOTE]
> Als er dubbele vermeldingen in het logboek bestand staan, worden deze door Azure Monitor verzameld. De query resultaten zijn echter inconsistent wanneer de filter resultaten meer gebeurtenissen weer geven dan het aantal resultaten. Het is belang rijk dat u het logboek valideert om te bepalen of de toepassing die deze maakt, dit gedrag veroorzaakt en zo goed mogelijk verhelpt voordat u de aangepaste definitie van de logboek verzameling maakt.  
>

>[!NOTE]
> Een Log Analytics-werk ruimte ondersteunt de volgende limieten:
> 
> * Er kunnen slechts 500 aangepaste logboeken worden gemaakt.
> * Een tabel ondersteunt Maxi maal 500 kolommen. 
> * Het maximum aantal tekens voor een kolomnaam is 500. 
>

>[!IMPORTANT]
>Aangepaste logboek verzameling vereist dat de toepassing die het logboek bestand schrijft, regel matig de logboek inhoud op de schijf opschoont. Dit komt omdat de aangepaste logboek verzameling afhankelijk is van wijzigings meldingen van het bestands systeem voor het logboek bestand dat wordt bijgehouden.

## <a name="defining-a-custom-log"></a>Een aangepast logboek definiëren
Gebruik de volgende procedure om een aangepast logboek bestand te definiëren.  Ga naar het einde van dit artikel voor een overzicht van een voor beeld van het toevoegen van een aangepast logboek.

### <a name="step-1-open-the-custom-log-wizard"></a>Stap 1. De wizard aangepast logboek openen
De wizard aangepast logboek wordt uitgevoerd in de Azure Portal en stelt u in staat om een nieuw aangepast logboek te definiëren dat u wilt verzamelen.

1. Selecteer in de Azure Portal **log Analytics werk ruimten** > uw werk ruimte > **Geavanceerde instellingen**.
2. Klik op   >  **aangepaste logboeken** voor gegevens.
3. Standaard worden alle configuratie wijzigingen automatisch naar alle agents gepusht. Voor Linux-agents wordt een configuratie bestand verzonden naar de gefluente gegevens verzamelaar.
4. Klik op **toevoegen +** om de wizard Aangepaste logboeken te openen.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Stap 2. Een voorbeeld logboek uploaden en parseren
U begint met het uploaden van een voor beeld van het aangepaste logboek.  Met de wizard worden de vermeldingen in dit bestand die u wilt valideren, geparseerd en weer gegeven.  Azure Monitor gebruikt het door u opgegeven scheidings teken om elke record te identificeren.

**Nieuwe regel** is het standaard scheidings teken dat wordt gebruikt voor logboek bestanden met één vermelding per regel.  Als de regel begint met een datum en tijd in een van de beschik bare notaties, kunt u een **tijds tempel** scheidings teken opgeven dat ondersteuning biedt voor vermeldingen die meer dan één regel omvatten.

Als er een tijds tempel scheidings teken wordt gebruikt, wordt de eigenschap TimeGenerated van elke record die is opgeslagen in Azure Monitor ingevuld met de datum/tijd die is opgegeven voor dat item in het logboek bestand.  Als er een nieuw regel scheidings teken wordt gebruikt, wordt TimeGenerated ingevuld met de datum en tijd waarop de invoer Azure Monitor verzameld.

1. Klik op **Bladeren** en blader naar een voorbeeld bestand.  Houd er rekening mee dat deze knop mogelijk een label **kiest** in sommige browsers.
2. Klik op **Volgende**.
3. Met de wizard voor aangepaste logboek bestanden wordt het bestand geüpload en worden de records weer geven die worden geïdentificeerd.
4. Wijzig het scheidings teken dat wordt gebruikt om een nieuwe record te identificeren en selecteer het scheidings teken dat het beste de records in het logboek bestand identificeert.
5. Klik op **Volgende**.

### <a name="step-3-add-log-collection-paths"></a>Stap 3. Paden voor logboek verzameling toevoegen
U moet een of meer paden definiëren op de agent waar het aangepaste logboek kan worden gevonden.  U kunt een specifiek pad en naam opgeven voor het logboek bestand, of u kunt een pad met een Joker teken voor de naam. Dit biedt ondersteuning voor toepassingen die elke dag een nieuw bestand maken of wanneer een bestand een bepaalde grootte heeft bereikt. U kunt ook meerdere paden opgeven voor één logboek bestand.

Een toepassing kan bijvoorbeeld elke dag een logboek bestand maken met de datum die is opgenomen in de naam in log20100316.txt. Een patroon voor een dergelijk logboek kan *Log \* . txt* zijn die van toepassing zou zijn op elk logboek bestand volgens het naamgevings schema van de toepassing.

De volgende tabel bevat voor beelden van geldige patronen om andere logboek bestanden op te geven.

| Description | Pad |
|:--- |:--- |
| Alle bestanden in *C:\Logs* met de extensie. txt op de Windows-agent |C:\Logs \\ \* . txt |
| Alle bestanden in *C:\Logs* met een naam die begint met log en een. txt-extensie in Windows-agent |C:\Logs\log \* . txt |
| Alle bestanden in */var/log/audit* met de extensie. txt in de Linux-agent |/var/log/audit/*. txt |
| Alle bestanden in */var/log/audit* met een naam die begint met log en een. txt-extensie in de Linux-agent |/var/log/audit/log \* . txt |

1. Selecteer Windows of Linux om op te geven welke indeling u wilt toevoegen.
2. Typ het pad en klik op de **+** knop.
3. Herhaal dit proces voor alle extra paden.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Stap 4. Geef een naam en beschrijving voor het logboek op
De naam die u opgeeft, wordt gebruikt voor het logboek type zoals hierboven is beschreven.  Deze wordt altijd beëindigd met _CL om het te onderscheiden als een aangepast logboek.

1. Typ een naam voor het logboek.  Het **\_ cl** -achtervoegsel wordt automatisch ingevuld.
2. Voeg een optionele **Beschrijving** toe.
3. Klik op **volgende** om de aangepaste definitie van het logboek op te slaan.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Stap 5. Controleren of de aangepaste logboeken worden verzameld
Het kan tot een uur duren voordat de initiële gegevens van een nieuw aangepast logboek worden weer gegeven in Azure Monitor.  De gegevens worden verzameld van de logboeken die zijn gevonden in het pad dat u hebt opgegeven vanaf het punt dat u het aangepaste logboek hebt gedefinieerd.  De items die u tijdens het maken van de aangepaste Logboeken hebt geüpload, worden niet bewaard, maar er worden al bestaande vermeldingen verzameld in de logboek bestanden die worden gevonden.

Zodra Azure Monitor begint met het verzamelen van het aangepaste logboek, zijn records beschikbaar met een logboek query.  Gebruik de naam die u hebt opgegeven voor het aangepaste logboek als het **type** in uw query.

> [!NOTE]
> Als de eigenschap RawData ontbreekt in de query, moet u mogelijk uw browser sluiten en opnieuw openen.

### <a name="step-6-parse-the-custom-log-entries"></a>Stap 6. De aangepaste logboek vermeldingen parseren
De volledige logboek vermelding wordt opgeslagen in één eigenschap met de naam **RawData**.  Waarschijnlijk wilt u de verschillende delen van de gegevens in elk item scheiden in afzonderlijke eigenschappen voor elke record. Raadpleeg het [parseren van tekst gegevens in azure monitor](../log-query/parse-text.md) voor opties voor het parseren van **RawData** in meerdere eigenschappen.

## <a name="removing-a-custom-log"></a>Een aangepast logboek verwijderen
Gebruik het volgende proces in de Azure Portal om een aangepast logboek te verwijderen dat u eerder hebt gedefinieerd.

1. Selecteer in het menu **Data** van de **Geavanceerde instellingen** voor uw werk ruimte de optie **aangepaste logboeken** om alle aangepaste logboeken weer te geven.
2. Klik op **verwijderen** naast het aangepaste logboek dat u wilt verwijderen.

## <a name="data-collection"></a>Gegevens verzamelen
Azure Monitor worden ongeveer elke vijf minuten nieuwe vermeldingen van elk aangepast logboek verzameld.  De agent registreert de locatie in elk logboek bestand dat wordt verzameld.  Als de agent gedurende een bepaalde tijd offline gaat, worden er door Azure Monitor gegevens verzameld van waar deze zich voor het laatst heeft verlaten, zelfs als deze vermeldingen zijn gemaakt terwijl de agent offline was.

De volledige inhoud van de logboek vermelding wordt geschreven naar één eigenschap met de naam **RawData**.  Zie [tekst gegevens parseren in azure monitor](../log-query/parse-text.md) voor methoden om elke geïmporteerde logboek vermelding te parseren in meerdere eigenschappen.

## <a name="custom-log-record-properties"></a>Eigenschappen van aangepaste logboek record
Aangepaste logboek records hebben een type met de naam van het logboek dat u opgeeft en de eigenschappen in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| TimeGenerated |De datum en tijd waarop de record is verzameld door Azure Monitor.  Als in het logboek een scheidings teken op basis van tijd wordt gebruikt, is dit de tijd die van de vermelding is verzameld. |
| SourceSystem |Type agent waaruit het record is verzameld. <br> OpsManager: Windows-agent, Direct Connect of System Center Operations Manager <br> Linux: alle Linux-agents |
| RawData |Volledige tekst van het verzamelde item. Waarschijnlijk wilt u [deze gegevens in afzonderlijke eigenschappen parseren](../log-query/parse-text.md). |
| ManagementGroupName |Naam van de beheer groep voor System Center Operations-agents.  Voor andere agents is dit AOI-\<workspace ID\> |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Voorbeeld scenario voor het toevoegen van een aangepast logboek
In het volgende gedeelte wordt een voor beeld van het maken van een aangepast logboek beschreven.  Het voorbeeld logboek dat wordt verzameld heeft één vermelding op elke regel, beginnend met een datum en tijd en vervolgens door komma's gescheiden velden voor code, status en bericht.  Hieronder ziet u enkele voor beelden van vermeldingen.

```output
2019-08-27 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2019-08-27 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2019-08-27 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2019-08-27 01:38:22 302,Error,Application could not connect to database
2019-08-27 01:31:34 303,Error,Application lost connection to database
```

### <a name="upload-and-parse-a-sample-log"></a>Een voorbeeld logboek uploaden en parseren
We bieden een van de logboek bestanden en kunnen de gebeurtenissen zien die worden verzameld.  In dit geval is de nieuwe regel een voldoende scheidings teken.  Als één vermelding in het logboek echter meerdere regels kan omvatten, moet er een tijds tempel scheidings teken worden gebruikt.

![Een voorbeeld logboek uploaden en parseren](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Paden voor logboek verzameling toevoegen
De logboek bestanden worden opgeslagen in *C:\MyApp\Logs*.  Er wordt elke dag een nieuw bestand gemaakt met een naam die de datum bevat in het patroon *appYYYYMMDD. log*.  Een voldoende patroon voor dit logboek is *C:\MyApp\Logs \\ \* . log*.

![Pad naar logboek verzameling](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Geef een naam en beschrijving voor het logboek op
We gebruiken de naam van *MyApp_CL* en typen een **Beschrijving**.

![Logboeknaam](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Controleren of de aangepaste logboeken worden verzameld
We gebruiken een eenvoudige query van *MyApp_CL* om alle records uit het verzamelde logboek te retour neren.

![Logboek query zonder aangepaste velden](media/data-sources-custom-logs/query-01.png)


## <a name="alternatives-to-custom-logs"></a>Alternatieven voor aangepaste logboeken
Hoewel aangepaste logboeken handig zijn als uw gegevens voldoen aan de bovenstaande criteria, zijn er situaties zoals de volgende waar u een andere strategie nodig hebt:

- De gegevens passen niet op de vereiste structuur, zoals het hebben van de tijds tempel in een andere indeling.
- Het logboek bestand voldoet niet aan de vereisten, zoals bestands codering of een niet-ondersteunde mapstructuur.
- De gegevens moeten voorafgaand aan verwerking of filteren vóór de verzameling worden uitgevoerd. 

In de gevallen waarin uw gegevens niet kunnen worden verzameld met aangepaste logboeken, moet u rekening houden met de volgende alternatieve strategieën:

- Gebruik een aangepast script of een andere methode om gegevens te schrijven naar [Windows-gebeurtenissen](data-sources-windows-events.md) of [syslog](data-sources-syslog.md) die worden verzameld door Azure monitor. 
- De gegevens rechtstreeks naar Azure Monitor verzenden met behulp van [http data collector API](../platform/data-collector-api.md). 

## <a name="next-steps"></a>Volgende stappen
* Zie [tekst gegevens parseren in azure monitor](../log-query/parse-text.md) voor methoden om elke geïmporteerde logboek vermelding te parseren in meerdere eigenschappen.
* Meer informatie over [logboek query's](../log-query/log-query-overview.md) voor het analyseren van de gegevens die zijn verzameld uit gegevens bronnen en oplossingen.
