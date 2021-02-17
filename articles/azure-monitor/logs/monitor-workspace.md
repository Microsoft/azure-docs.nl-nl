---
title: De status van de Log Analytics werk ruimte in Azure Monitor bewaken
description: Hierin wordt beschreven hoe u de status van uw Log Analytics-werk ruimte bewaakt met behulp van gegevens in de bewerkings tabel.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: bef7db19142f0387077af1487230e7891c255aa1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610821"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>De status van de Log Analytics werk ruimte in Azure Monitor bewaken
Als u de prestaties en beschik baarheid van uw Log Analytics-werk ruimte in Azure Monitor wilt behouden, moet u alle problemen die zich voordoen, proactief kunnen detecteren. In dit artikel wordt beschreven hoe u de status van uw Log Analytics-werk ruimte kunt controleren met behulp van gegevens in de [bewerkings](/azure/azure-monitor/reference/tables/operation) tabel. Deze tabel is opgenomen in elke Log Analytics-werk ruimte en bevat fout-en waarschuwingen die zich in uw werk ruimte voordoen. U moet deze gegevens regel matig bekijken en waarschuwingen maken om proactief te worden gewaarschuwd wanneer er belang rijke incidenten in uw werk ruimte zijn.

## <a name="_logoperation-function"></a>Functie _LogOperation

Met Azure Monitor logboeken worden gegevens over problemen verzonden naar de [bewerkings](/azure/azure-monitor/reference/tables/operation) tabel in de werk ruimte waar het probleem is opgetreden. De **_LogOperation** -systeem functie is gebaseerd op de **bewerkings** tabel en biedt een vereenvoudigde set informatie voor analyse en waarschuwingen.

## <a name="columns"></a>Kolommen

De functie **_LogOperation** retourneert de kolommen in de volgende tabel.

| Kolom | Beschrijving |
|:---|:---|
| TimeGenerated | De tijd waarop het incident is opgetreden in UTC. |
| Categorie  | Bewerkings categorie groep. Kan worden gebruikt om te filteren op typen bewerkingen en om nauw keurigere systeem controle en-waarschuwingen te maken. Zie de sectie hieronder voor een lijst met categorieën. |
| Bewerking  | Beschrijving van het bewerkings type. Dit kan duiden op een van de Log Analytics limieten, het type bewerking of een deel van een proces. |
| Niveau | Ernst van het probleem:<br>-Info: er is geen specifieke aandacht nodig.<br>-Waarschuwing: het proces is niet volgens verwachting voltooid en er is aandacht vereist.<br>-Fout: het proces is mislukt en de dringend aandacht is vereist. 
| Detail | Gedetailleerde beschrijving van de bewerking bevat specifiek fout bericht als het bestaat. |
| _ResourceId | De resource-ID van de Azure-resource die is gerelateerd aan de bewerking.  |
| Computer | Computer naam als de bewerking betrekking heeft op een Azure Monitor-agent. |
| CorrelationId | Wordt gebruikt voor het groeperen van opeenvolgende gerelateerde bewerkingen. |


## <a name="categories"></a>Categorieën

De volgende tabel beschrijft de categorieën van de functie _LogOperation. 

| Categorie | Beschrijving |
|:---|:---|
| Gegevensopname           | Bewerkingen die deel uitmaken van het proces voor gegevens opname. Zie hieronder voor meer informatie. |
| Agent               | Duidt op een probleem met de installatie van de agent. |
| Gegevens verzamelen     | Bewerkingen met betrekking tot gegevens verzamelings processen. |
| Doel items van oplossingen  | De bewerking van het type *ConfigurationScope* is verwerkt. |
| Beoordelings oplossing | Er is een evaluatie proces uitgevoerd. |


### <a name="ingestion"></a>Gegevensopname
Opname bewerkingen zijn problemen die zich hebben voorgedaan tijdens het opnemen van gegevens, inclusief melding over het bereiken van de limieten voor Azure Log Analytics-werk ruimten. Fout voorwaarden in deze categorie kunnen gegevens verlies voordoen, zodat ze vooral belang rijk zijn om te controleren. De onderstaande tabel bevat details over deze bewerkingen. Zie [Azure Monitor service limieten](../service-limits.md#log-analytics-workspaces) voor service limieten voor log Analytics-werk ruimten.


| Bewerking | Niveau | Detail | Verwant artikel |
|:---|:---|:---|:---|
| Aangepast logboek | Fout   | De kolom limiet voor aangepaste velden is bereikt. | [Servicebeperkingen van Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Aangepast logboek | Fout   | Opname van aangepaste Logboeken is mislukt. | |
| Metagegevensarchiefmethode. | Fout | Er is een configuratie fout gedetecteerd. | |
| Gegevens verzamelen | Fout   | De gegevens zijn verwijderd omdat de aanvraag eerder is gemaakt dan het aantal ingestelde dagen. | [Gebruik en kosten beheren met Azure Monitor-logboeken](../platform/manage-cost-storage.md#alert-when-daily-cap-reached)
| Gegevens verzamelen | Info    | De configuratie van de verzamelings machine is gedetecteerd.| |
| Gegevens verzamelen | Info    | Het verzamelen van gegevens is gestart vanwege een nieuwe dag. | [Gebruik en kosten beheren met Azure Monitor-logboeken](../platform/manage-cost-storage.md#alert-when-daily-cap-reached) |
| Gegevens verzamelen | Waarschuwing | Het verzamelen van gegevens is gestopt omdat de dagelijkse limiet is bereikt.| [Gebruik en kosten beheren met Azure Monitor-logboeken](../platform/manage-cost-storage.md#alert-when-daily-cap-reached) |
| Gegevensverwerking | Fout   | Ongeldige JSON-indeling. | [Logboek gegevens naar Azure Monitor verzenden met de HTTP-gegevens verzamelaar-API (open bare preview)](../logs/data-collector-api.md#request-body) | 
| Gegevensverwerking | Waarschuwing | Waarde is afgekapt tot de Maxi maal toegestane grootte. | [Servicebeperkingen van Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Gegevensverwerking | Waarschuwing | Veld waarde is afgekapt omdat de maximale grootte is bereikt. | [Servicebeperkingen van Azure Monitor](../service-limits.md#log-analytics-workspaces) | 
| Opname frequentie | Info | De limiet voor opname frequentie is 70%. | [Servicebeperkingen van Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Opname frequentie | Waarschuwing | De limiet voor opname snelheden die de limiet nadert. | [Servicebeperkingen van Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Opname frequentie | Fout   | Frequentie limiet bereikt. | [Servicebeperkingen van Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Storage | Fout   | Kan geen toegang krijgen tot het opslag account omdat de gebruikte referenties ongeldig zijn.  |



   

## <a name="alert-rules"></a>Waarschuwingsregels
Gebruik [waarschuwingen voor logboek query's](../alerts/alerts-log-query.md) in azure monitor om proactief te worden gewaarschuwd wanneer er een probleem wordt gedetecteerd in uw log Analytics-werk ruimte. U moet een strategie gebruiken waarmee u tijdig kunt reageren op problemen terwijl u de kosten minimaliseert. Voor elke waarschuwings regel worden kosten in rekening gebracht, afhankelijk van de frequentie waarmee deze wordt geëvalueerd.

Een aanbevolen strategie is om te beginnen met twee waarschuwings regels op basis van het niveau van het probleem. Gebruik een korte frequentie, bijvoorbeeld elke 5 minuten voor fouten en een langere frequentie, zoals 24 uur voor waarschuwingen. Omdat fouten wijzen op mogelijke gegevens verlies, wilt u deze snel reageren om verlies te minimaliseren. Waarschuwingen duiden doorgaans op een probleem waarvoor geen onmiddellijke aandacht is vereist, zodat u ze dagelijks kunt controleren.

Gebruik het proces in [logboek waarschuwingen maken, weer geven en beheren met Azure monitor](../alerts/alerts-log.md) om de waarschuwings regels voor het logboek te maken. De volgende secties beschrijven de details van elke regel.


| Query’s uitvoeren | Drempelwaarde | Periode | Frequentie |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

Deze waarschuwings regels reageren hetzelfde op alle bewerkingen met een fout of waarschuwing. Naarmate u vertrouwd bent met de bewerkingen die waarschuwingen genereren, wilt u mogelijk anders reageren op bepaalde bewerkingen. U kunt bijvoorbeeld meldingen naar verschillende personen verzenden voor bepaalde bewerkingen. 

Als u een waarschuwings regel voor een specifieke bewerking wilt maken, gebruikt u een query die de kolommen **categorie** en **bewerking** bevat. 

In het volgende voor beeld wordt een waarschuwing gegeven wanneer de frequentie van het opname volume 80% van de limiet heeft bereikt.

- Doel: Selecteer uw Log Analytics-werk ruimte
- Gezocht
  - Signaal naam: aangepaste zoek opdracht in Logboeken
  - Zoek query: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - Gebaseerd op: aantal resultaten
  - Voor waarde: groter dan
  - Drempel waarde: 0
  - Periode: 5 (minuten)
  - Frequentie: 5 (minuten)
- Naam van waarschuwings regel: dagelijkse gegevens limiet bereikt
- Ernst: waarschuwing (Ernst 1)


In het volgende voor beeld wordt een waarschuwing gegeven wanneer de gegevens verzameling de dagelijkse limiet heeft bereikt. 

- Doel: Selecteer uw Log Analytics-werk ruimte
- Gezocht
  - Signaal naam: aangepaste zoek opdracht in Logboeken
  - Zoek query: `_LogOperation | where Category == "Ingestion" | where Operation == "Data Collection" | where Level == "Warning"`
  - Gebaseerd op: aantal resultaten
  - Voor waarde: groter dan
  - Drempel waarde: 0
  - Periode: 5 (minuten)
  - Frequentie: 5 (minuten)
- Naam van waarschuwings regel: dagelijkse gegevens limiet bereikt
- Ernst: waarschuwing (Ernst 1)



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [waarschuwingen voor logboeken](../alerts/alerts-log.md).
- [Query controle gegevens verzamelen](../log-query/query-audit.md) voor uw werk ruimte.