---
title: IIS-logboeken met Log Analytics agent in Azure Monitor verzamelen
description: Internet Information Services (IIS) slaat gebruikers activiteiten op in logboek bestanden die door Azure Monitor kunnen worden verzameld.  In dit artikel wordt beschreven hoe u een verzameling IIS-logboeken en-Details configureert van de records die ze in Azure Monitor maken.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2020
ms.openlocfilehash: a089631ab199b0fe997bba001561c6b027034e2c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993683"
---
# <a name="collect-iis-logs-with-log-analytics-agent-in-azure-monitor"></a>IIS-logboeken met Log Analytics agent in Azure Monitor verzamelen
Internet Information Services (IIS) slaat gebruikers activiteiten op in logboek bestanden die kunnen worden verzameld door de Log Analytics agent en worden opgeslagen in [Azure monitor logboeken](data-platform.md).

> [!IMPORTANT]
> In dit artikel wordt beschreven hoe u IIS-logboeken verzamelt met de [log Analytics-agent](log-analytics-agent.md) , een van de agents die door Azure monitor worden gebruikt. Andere agents verzamelen verschillende gegevens en worden anders geconfigureerd. Zie [overzicht van Azure monitor agents](agents-overview.md) voor een lijst met beschik bare agents en de gegevens die ze kunnen verzamelen.

![IIS-logboeken](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>IIS-logboeken configureren
Azure Monitor verzamelt vermeldingen uit logboek bestanden die zijn gemaakt door IIS, dus moet u [IIS configureren voor logboek registratie](/previous-versions/orphan-topics/ws.11/hh831775(v=ws.11)).

Azure Monitor ondersteunt alleen IIS-logboek bestanden die zijn opgeslagen in de W3C-indeling en ondersteunt geen aangepaste velden of geavanceerde logboek registratie van IIS. Er worden geen logboeken verzameld in NCSA of de native IIS-indeling.

Configureer IIS-logboeken in Azure Monitor via het [menu Geavanceerde instellingen](agent-data-sources.md#configuring-data-sources) voor de log Analytics agent.  Er is geen configuratie vereist, anders dan het selecteren van **IIS-logboek bestanden voor W3C-indeling verzamelen**.


## <a name="data-collection"></a>Gegevensverzameling
Azure Monitor worden IIS-logboek vermeldingen van elke agent verzameld telkens wanneer de tijds tempel van het logboek wordt gewijzigd. Het logboek wordt elke **vijf minuten** gelezen. Als IIS de tijds tempel voor de rollover tijd niet bijwerkt wanneer een nieuw bestand wordt gemaakt, worden de gegevens verzameld na het maken van het nieuwe bestand. De frequentie van het maken van nieuwe bestanden wordt bepaald door de instelling voor het schema voor de **rollover van logboek bestanden** voor de IIS-site, die standaard één keer per dag wordt uitgevoerd. Als de instelling elk **uur** is, verzamelt Azure monitor elk uur het logboek. Als de instelling **dagelijks** is, verzamelt Azure monitor het logboek elke 24 uur.

> [!IMPORTANT]
> Het is raadzaam om het schema voor de rollover van het **logboek bestand** in te stellen op **elk uur**. Als deze is ingesteld op **dagelijks**, kunnen er pieken optreden in uw gegevens omdat deze slechts eenmaal per dag worden verzameld.

## <a name="iis-log-record-properties"></a>Eigenschappen van IIS-logboek record
IIS-logboek records hebben een type **W3CIISLog** en hebben de eigenschappen in de volgende tabel:

| Eigenschap | Beschrijving |
|:--- |:--- |
| Computer |De naam van de computer waarop de gebeurtenis is verzameld. |
| cIP |IP-adres van de client. |
| csMethod |De methode van de aanvraag, zoals GET of POST. |
| csReferer |Site waar de gebruiker een koppeling heeft gevolgd van naar de huidige site. |
| csUserAgent |Browser type van de client. |
| csUserName |Naam van de geverifieerde gebruiker die toegang heeft tot de server. Anonieme gebruikers worden aangeduid met een koppelteken. |
| csUriStem |Doel van de aanvraag, zoals een webpagina. |
| csUriQuery |Indien van toepassing, query die de client probeerde uit te voeren. |
| ManagementGroupName |De naam van de beheer groep voor Operations Manager agents.  Voor andere agents is dit AOI-\<workspace ID\> |
| RemoteIPCountry |Het land/de regio van het IP-adres van de client. |
| RemoteIPLatitude |De breedte graad van het client-IP-adres. |
| RemoteIPLongitude |De lengte graad van het client-IP-adres. |
| scStatus |HTTP-status code. |
| scSubStatus |Fout code van substatus. |
| scWin32Status |Windows-status code. |
| sIP |Het IP-adres van de webserver. |
| SourceSystem |OpsMgr |
| Manifestatie |Poort op de server waarmee de client is verbonden. |
| sSiteName |De naam van de IIS-site. |
| TimeGenerated |De datum en tijd waarop de vermelding is geregistreerd. |
| TimeTaken |De tijds duur voor het verwerken van de aanvraag in milliseconden. |

## <a name="log-queries-with-iis-logs"></a>Query's vastleggen in Logboeken met IIS-logboeken
De volgende tabel bevat verschillende voor beelden van logboek query's waarmee IIS-logboek records worden opgehaald.

| Query | Description |
|:--- |:--- |
| W3CIISLog |Alle IIS-logboek records. |
| W3CIISLog &#124; waarbij scStatus = = 500 |Alle IIS-logboek records met de retour status 500. |
| Aantal W3CIISLog &#124;-overzicht () door overschrijving |Aantal IIS-logboek vermeldingen op client-IP-adres. |
| W3CIISLog &#124; waarbij csHost = = "www \. contoso.com" &#124; totaal aantal () door csUriStem |Aantal IIS-logboek vermeldingen op URL voor de www-contoso.com van de host \. . |
| W3CIISLog &#124; samenvat Sum (csBytes) door computer &#124; 500000 |Het totale aantal bytes dat door elke IIS-computer is ontvangen. |

## <a name="next-steps"></a>Volgende stappen
* Configureer Azure Monitor voor het verzamelen van andere [gegevens bronnen](agent-data-sources.md) voor analyse.
* Meer informatie over [logboek query's](../log-query/log-query-overview.md) voor het analyseren van de gegevens die zijn verzameld uit gegevens bronnen en oplossingen.
