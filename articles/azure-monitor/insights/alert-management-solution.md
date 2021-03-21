---
title: Waarschuwingenbeheer oplossing in azure Log Analytics | Microsoft Docs
description: De Waarschuwingenbeheer oplossing in Log Analytics helpt u bij het analyseren van alle waarschuwingen in uw omgeving.  Naast het samen voegen van waarschuwingen die zijn gegenereerd in Log Analytics, worden er waarschuwingen van verbonden System Center Operations Manager beheer groepen in Log Analytics geïmporteerd.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: 6f3761b1c933d113bf65c70370c29bcfa7950eea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728651"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Waarschuwingenbeheer oplossing in azure Log Analytics

![Waarschuwingenbeheer pictogram](media/alert-management-solution/icon.png)

De Waarschuwingenbeheer-oplossing helpt u bij het analyseren van alle waarschuwingen in uw Log Analytics opslag plaats.  Deze waarschuwingen zijn mogelijk afkomstig uit verschillende bronnen, waaronder de bronnen die zijn [gemaakt door log Analytics](../alerts/alerts-overview.md) of [geïmporteerd uit nagios of zabbix](../vm/quick-collect-linux-computer.md). De oplossing importeert ook waarschuwingen van alle [verbonden System Center Operations Manager-beheer groepen](../agents/om-agents.md).

## <a name="prerequisites"></a>Vereisten
De oplossing werkt met een wille keurige record in de Log Analytics-opslag plaats met een type **waarschuwing**, dus moet u de configuratie uitvoeren die nodig is voor het verzamelen van deze records.

- Maak voor Log Analytics waarschuwingen [waarschuwings regels](../alerts/alerts-overview.md) om waarschuwings records rechtstreeks in de opslag plaats te maken.
- Configureer voor nagios-en zabbix-waarschuwingen [die servers](../vm/quick-collect-linux-computer.md) om waarschuwingen naar log Analytics te verzenden.
- Voor System Center Operations Manager waarschuwingen [verbindt u uw Operations Manager-beheer groep met uw log Analytics-werk ruimte](../agents/om-agents.md).  Alle waarschuwingen die zijn gemaakt in System Center Operations Manager, worden in Log Analytics geïmporteerd.  

## <a name="configuration"></a>Configuratie
Voeg de Waarschuwingenbeheer oplossing toe aan uw Log Analytics-werk ruimte met behulp van het proces dat wordt beschreven in [oplossingen toevoegen](../insights/solutions.md). Er is geen verdere configuratie nodig.

## <a name="management-packs"></a>Management packs
Als uw System Center Operations Manager-beheer groep is verbonden met uw Log Analytics-werk ruimte, worden de volgende Management Packs geïnstalleerd in System Center Operations Manager wanneer u deze oplossing toevoegt.  Er is geen configuratie of onderhoud van de vereiste Management Packs.

* Micro soft System Center Advisor Waarschuwingenbeheer (micro soft. intelligence packs. AlertManagement)

Zie [Operations Manager koppelen aan Log Analytics](../agents/om-agents.md) voor meer informatie over de manier waarop uw management packs voor oplossingen worden bijgewerkt.

## <a name="data-collection"></a>Gegevens verzamelen
### <a name="agents"></a>Agents
De volgende tabel beschrijft de verbonden bronnen die worden ondersteund door deze oplossing.

| Verbonden bron | Ondersteuning | Beschrijving |
|:--- |:--- |:--- |
| [Windows-agents](../agents/agent-windows.md) | Nee |Directe Windows-agents genereren geen waarschuwingen.  Log Analytics waarschuwingen kunnen worden gemaakt op basis van gebeurtenissen en prestatie gegevens die zijn verzameld van Windows-agents. |
| [Linux-agents](../vm/quick-collect-linux-computer.md) | Nee |Direct Linux-agents genereren geen waarschuwingen.  Log Analytics waarschuwingen kunnen worden gemaakt op basis van gebeurtenissen en prestatie gegevens die zijn verzameld uit Linux-agents.  Nagios-en zabbix-waarschuwingen worden verzameld van de servers waarvoor de Linux-agent is vereist. |
| [Beheergroep System Center Operations Manager](../agents/om-agents.md) |Ja |Waarschuwingen die op Operations Manager agents worden gegenereerd, worden aan de beheer groep geleverd en vervolgens doorgestuurd naar Log Analytics.<br><br>Een directe verbinding van Operations Manager agents naar Log Analytics is niet vereist. Waarschuwings gegevens worden doorgestuurd van de beheer groep naar de Log Analytics opslag plaats. |


### <a name="collection-frequency"></a>Verzamelingsfrequentie
- Waarschuwings records zijn beschikbaar voor de oplossing zodra ze zijn opgeslagen in de opslag plaats.
- Waarschuwings gegevens worden vanaf de Operations Manager beheer groep verzonden naar Log Analytics om de drie minuten.  

## <a name="using-the-solution"></a>De oplossing gebruiken
Wanneer u de Waarschuwingenbeheer oplossing toevoegt aan uw Log Analytics-werk ruimte, wordt de tegel **Waarschuwingenbeheer** toegevoegd aan uw dash board.  Deze tegel toont een aantal en grafische weer gave van het aantal actieve waarschuwingen dat in de afgelopen 24 uur is gegenereerd.  U kunt dit tijds bereik niet wijzigen.

![Waarschuwingenbeheer tegel](media/alert-management-solution/tile.png)

Klik op de tegel **Waarschuwingenbeheer** om het **Waarschuwingenbeheer** dash board te openen.  Het dashboard bevat de kolommen in de volgende tabel.  Elke kolom bevat de tien belangrijkste waarschuwingen per aantal dat overeenkomt met de criteria van die kolom voor het opgegeven bereik en tijds bereik.  U kunt een zoek opdracht in het logboek uitvoeren met de volledige lijst door te klikken op **alles weer geven** onder aan de kolom of door op de kolomkop te klikken.

| Kolom | Beschrijving |
|:--- |:--- |
| Kritieke waarschuwingen |Alle waarschuwingen met de ernst kritiek gegroepeerd op waarschuwings naam.  Klik op de naam van een waarschuwing om een zoek opdracht in Logboeken uit te voeren die alle records voor die waarschuwing retourneert. |
| Waarschuwings meldingen |Alle waarschuwingen met de ernst waarschuwing, gegroepeerd op naam van de waarschuwing.  Klik op de naam van een waarschuwing om een zoek opdracht in Logboeken uit te voeren die alle records voor die waarschuwing retourneert. |
| Waarschuwingen voor actieve System Center Operations Manager |Alle waarschuwingen die zijn verzameld van Operations Manager, met een andere status dan *gesloten* , gegroepeerd op bron waardoor de waarschuwing is gegenereerd. |
| Alle actieve waarschuwingen |Alle waarschuwingen met elke Ernst, gegroepeerd op naam van de waarschuwing. Bevat alleen Operations Manager waarschuwingen met een andere status dan *gesloten*. |

Als u naar rechts schuift, worden in het dash board verschillende algemene query's vermeld waarop u kunt klikken om een [Zoek actie](../logs/log-query-overview.md) in het logboek voor waarschuwings gegevens uit te voeren.

![Waarschuwingenbeheer dash board](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Log Analytics-records
De Waarschuwingenbeheer oplossing analyseert alle records met een type **waarschuwing**.  Waarschuwingen die zijn gemaakt door Log Analytics of verzameld van nagios of zabbix, worden niet rechtstreeks verzameld door de oplossing.

De oplossing importeert waarschuwingen van System Center Operations Manager en maakt een corresponderende record voor elk met een type **waarschuwing** en een hebben van **OpsManager**.  Deze records hebben de eigenschappen in de volgende tabel:  

| Eigenschap | Beschrijving |
|:--- |:--- |
| `Type` |*Waarschuwing* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |Details van het gegevens item dat de waarschuwing heeft veroorzaakt, worden gegenereerd in XML-indeling. |
| `AlertDescription` |Gedetailleerde beschrijving van de waarschuwing. |
| `AlertId` |De GUID van de waarschuwing. |
| `AlertName` |De naam van de waarschuwing. |
| `AlertPriority` |Prioriteits niveau van de waarschuwing. |
| `AlertSeverity` |Ernst niveau van de waarschuwing. |
| `AlertState` |De laatste oplossings status van de waarschuwing. |
| `LastModifiedBy` |De naam van de gebruiker die de waarschuwing voor het laatst heeft gewijzigd. |
| `ManagementGroupName` |De naam van de beheer groep waarin de waarschuwing is gegenereerd. |
| `RepeatCount` |Aantal keer dat dezelfde waarschuwing is gegenereerd voor hetzelfde bewaakte object nadat het is opgelost. |
| `ResolvedBy` |De naam van de gebruiker die de waarschuwing heeft opgelost. Leeg als de waarschuwing nog niet is opgelost. |
| `SourceDisplayName` |Weergave naam van het bewakings object dat de waarschuwing heeft gegenereerd. |
| `SourceFullName` |Volledige naam van het bewakings object dat de waarschuwing heeft gegenereerd. |
| `TicketId` |Ticket-ID voor de waarschuwing als de System Center Operations Manager omgeving is geïntegreerd met een proces voor het toewijzen van tickets voor waarschuwingen.  Er is geen ticket-ID toegewezen. |
| `TimeGenerated` |De datum en tijd waarop de waarschuwing is gemaakt. |
| `TimeLastModified` |De datum en tijd waarop de waarschuwing voor het laatst is gewijzigd. |
| `TimeRaised` |De datum en tijd waarop de waarschuwing is gegenereerd. |
| `TimeResolved` |De datum en het tijdstip waarop de waarschuwing is opgelost. Leeg als de waarschuwing nog niet is opgelost. |

## <a name="sample-log-searches"></a>Voorbeeldzoekopdrachten in logboeken
De volgende tabel bevat voor beelden van zoek opdrachten in Logboeken voor waarschuwings records die door deze oplossing worden verzameld: 

| Query | Beschrijving |
|:---|:---|
| Waarschuwing &#124; waarbij hebben = = "OpsManager" en AlertSeverity = = "Error" en TimeRaised > geleden (24 uur) |Kritieke waarschuwingen die zijn opgetreden in de afgelopen 24 uur |
| Waarschuwing &#124; waarbij AlertSeverity = = "waarschuwing" en TimeRaised > geleden (24 uur) |Waarschuwingen die in de afgelopen 24 uur zijn geactiveerd |
| Waarschuwing &#124; waarbij hebben = = "OpsManager" en AlertState! = "gesloten" en TimeRaised > geleden (24 uur) &#124; samenvattings aantal = aantal () door SourceDisplayName |Bronnen met actieve waarschuwingen die in de afgelopen 24 uur zijn geactiveerd |
| Waarschuwing &#124; waarbij hebben = = "OpsManager" en AlertSeverity = = "Error" en TimeRaised > geleden (24 uur) en AlertState! = "gesloten" |Kritieke waarschuwingen die zijn opgetreden in de afgelopen 24 uur die nog steeds actief zijn |
| Waarschuwing &#124; waarbij hebben = = "OpsManager" en TimeRaised > geleden (24 uur) en AlertState = = "gesloten" |Waarschuwingen die in de afgelopen 24 uur zijn geactiveerd en nu zijn gesloten |
| Waarschuwing &#124; waarbij hebben = = "OpsManager" en TimeRaised > geleden (1d) &#124; samenvattings aantal = aantal () door AlertSeverity |Waarschuwingen die zijn gegenereerd tijdens de afgelopen dag, gegroepeerd op Ernst |
| Waarschuwing &#124; waarbij hebben = = "OpsManager" en TimeRaised > geleden (1d) &#124; sorteren op RepeatCount desc |Waarschuwingen die zijn gegenereerd tijdens de afgelopen dag, gesorteerd op de waarde voor aantal herhalingen |



## <a name="next-steps"></a>Volgende stappen
* Zie [Understanding alerts in Log Analytics](../alerts/alerts-overview.md) voor meer informatie over het genereren van waarschuwingen van Log Analytics.