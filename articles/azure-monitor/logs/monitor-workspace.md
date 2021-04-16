---
title: De status van de Log Analytics-werkruimte in Azure Monitor
description: Beschrijft hoe u de status van uw Log Analytics-werkruimte bewaakt met behulp van gegevens in de tabel Bewerking.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: 6f1a23170d84e39e5d531ae4e3a64b59d29bd677
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538845"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>De status van de Log Analytics-werkruimte in Azure Monitor
Als u de prestaties en beschikbaarheid van uw Log Analytics-werkruimte in Azure Monitor wilt behouden, moet u eventuele problemen die zich voordoen proactief kunnen detecteren. In dit artikel wordt beschreven hoe u de status van uw Log Analytics-werkruimte bewaakt met behulp van gegevens in de [tabel](/azure/azure-monitor/reference/tables/operation) Bewerking. Deze tabel is opgenomen in elke Log Analytics-werkruimte en bevat fouten en waarschuwingen die in uw werkruimte optreden. U moet deze gegevens regelmatig controleren en waarschuwingen maken om proactief te worden gewaarschuwd wanneer er belangrijke incidenten in uw werkruimte zijn.

## <a name="_logoperation-function"></a>_LogOperation functie

Azure Monitor logboeken verzendt details over eventuele problemen naar de tabel [Bewerking](/azure/azure-monitor/reference/tables/operation) in de werkruimte waar het probleem is opgetreden. De **_LogOperation** is gebaseerd op de tabel **Operation** en biedt een vereenvoudigde set informatie voor analyse en waarschuwingen.

## <a name="columns"></a>Kolommen

De **_LogOperation** retourneert de kolommen in de volgende tabel.

| Kolom | Beschrijving |
|:---|:---|
| TimeGenerated | Het tijdstip dat het incident plaatsvond in UTC. |
| Categorie  | Categoriegroep bewerking. Kan worden gebruikt om te filteren op typen bewerkingen en om nauwkeurigere systeemcontrole en -waarschuwingen te maken. Zie de onderstaande sectie voor een lijst met categorieën. |
| Bewerking  | Beschrijving van het bewerkingstype. Dit kan een van de Log Analytics-limieten, het type bewerking of een deel van een proces aangeven. |
| Niveau | Ernstniveau van het probleem:<br>- Info: er is geen specifieke aandacht nodig.<br>- Waarschuwing: Het proces is niet voltooid zoals verwacht en er is aandacht nodig.<br>-Fout: Proces is mislukt en er is urgente aandacht nodig. 
| Detail | Gedetailleerde beschrijving van de bewerking bevat een specifiek foutbericht als deze bestaat. |
| _ResourceId | Resource-id van de Azure-resource die is gerelateerd aan de bewerking.  |
| Computer | Computernaam als de bewerking is gerelateerd aan een Azure Monitor agent. |
| CorrelationId | Wordt gebruikt om opeenvolgende gerelateerde bewerkingen te groepen. |


## <a name="categories"></a>Categorieën

In de volgende tabel worden de categorieën van de _LogOperation beschreven. 

| Categorie | Beschrijving |
|:---|:---|
| Gegevensopname           | Bewerkingen die deel uitmaken van het gegevensingestieproces. Zie hieronder voor meer informatie. |
| Agent               | Geeft een probleem aan met de installatie van de agent. |
| Gegevens verzamelen     | Bewerkingen met betrekking tot processen voor gegevensverzamelingen. |
| Oplossingstargeting  | Bewerking van het type *ConfigurationScope* is verwerkt. |
| Evaluatieoplossing | Er is een evaluatieproces uitgevoerd. |


### <a name="ingestion"></a>Gegevensopname
Opnamebewerkingen zijn problemen die zijn opgetreden tijdens het opnemen van gegevens, waaronder meldingen over het bereiken van de limieten van de Azure Log Analytics-werkruimte. Foutvoorwaarden in deze categorie kunnen leiden tot gegevensverlies, dus ze zijn met name belangrijk om te controleren. In de onderstaande tabel vindt u meer informatie over deze bewerkingen. Zie [Azure Monitor servicelimieten voor](../service-limits.md#log-analytics-workspaces) servicelimieten voor Log Analytics-werkruimten.


| Bewerking | Niveau | Detail | Gerelateerd artikel |
|:---|:---|:---|:---|
| Aangepast logboek | Fout   | De kolomlimiet voor aangepaste velden is bereikt. | [Servicebeperkingen van Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Aangepast logboek | Fout   | De opname van aangepaste logboeken is mislukt. | |
| Metagegevens. | Fout | Er is een configuratiefout gedetecteerd. | |
| Gegevens verzamelen | Fout   | Gegevens zijn weggevallen omdat de aanvraag eerder is gemaakt dan het aantal dagen dat is ingesteld. | [Gebruik en kosten beheren met Azure Monitor-logboeken](./manage-cost-storage.md#alert-when-daily-cap-reached)
| Gegevens verzamelen | Info    | De configuratie van de verzamelingsmachine wordt gedetecteerd.| |
| Gegevens verzamelen | Info    | Het verzamelen van gegevens is gestart vanwege een nieuwe dag. | [Gebruik en kosten beheren met Azure Monitor-logboeken](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Gegevens verzamelen | Waarschuwing | Het verzamelen van gegevens is gestopt omdat de dagelijkse limiet is bereikt.| [Gebruik en kosten beheren met Azure Monitor-logboeken](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Gegevensverwerking | Fout   | Ongeldige JSON-indeling. | [Logboekgegevens verzenden naar Azure Monitor http-gegevensverzamelaar-API (openbare preview)](../logs/data-collector-api.md#request-body) | 
| Gegevensverwerking | Waarschuwing | De waarde is ingekort tot de maximaal toegestane grootte. | [Servicebeperkingen van Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Gegevensverwerking | Waarschuwing | Veldwaarde bijgesneden wanneer de limiet voor de grootte is bereikt. | [Servicebeperkingen van Azure Monitor](../service-limits.md#log-analytics-workspaces) | 
| Opnamesnelheid | Info | De opnamefrequentielimiet nadert 70%. | [Servicebeperkingen van Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Opnamesnelheid | Waarschuwing | De opnamefrequentielimiet nadert de limiet. | [Servicebeperkingen van Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Opnamesnelheid | Fout   | De limiet voor de snelheid is bereikt. | [Servicebeperkingen van Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Storage | Fout   | Kan geen toegang krijgen tot het opslagaccount omdat de gebruikte referenties ongeldig zijn.  |



   

## <a name="alert-rules"></a>Waarschuwingsregels
Gebruik [logboekquerywaarschuwingen](../alerts/alerts-log-query.md) in Azure Monitor proactief te worden gewaarschuwd wanneer er een probleem wordt gedetecteerd in uw Log Analytics-werkruimte. U moet een strategie gebruiken waarmee u tijdig kunt reageren op problemen en tegelijkertijd uw kosten kunt minimaliseren. Voor elke waarschuwingsregel worden kosten in rekening gebracht voor uw abonnement, afhankelijk van de frequentie die wordt geëvalueerd.

Een aanbevolen strategie is om te beginnen met twee waarschuwingsregels op basis van het niveau van het probleem. Gebruik een korte frequentie, zoals elke 5 minuten voor fouten en een langere frequentie, zoals 24 uur voor waarschuwingen. Omdat fouten duiden op mogelijk gegevensverlies, wilt u er snel op reageren om het verlies te minimaliseren. Waarschuwingen duiden doorgaans op een probleem waarvoor geen onmiddellijke aandacht nodig is, zodat u ze dagelijks kunt bekijken.

Gebruik het proces in [Logboekwaarschuwingen maken, weergeven](../alerts/alerts-log.md) en beheren met Azure Monitor om de logboekwaarschuwingsregels te maken. In de volgende secties worden de details voor elke regel beschreven.


| Query | Drempelwaarde | Periode | Frequentie |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

Deze waarschuwingsregels reageren op dezelfde manier op alle bewerkingen met Fout of Waarschuwing. Naarmate u meer vertrouwd bent met de bewerkingen die waarschuwingen genereren, wilt u mogelijk op een andere manier reageren op bepaalde bewerkingen. U kunt bijvoorbeeld meldingen naar verschillende personen verzenden voor bepaalde bewerkingen. 

Als u een waarschuwingsregel voor een specifieke bewerking wilt maken, gebruikt u een query die de kolommen **Categorie** en **Bewerking** bevat. 

In het volgende voorbeeld wordt een waarschuwing gemaakt wanneer het opnamevolume 80% van de limiet heeft bereikt.

- Doel: Selecteer uw Log Analytics-werkruimte
- Criteria:
  - Signaalnaam: Aangepast zoeken in logboeken
  - Zoekquery: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - Op basis van: Aantal resultaten
  - Voorwaarde: Groter dan
  - Drempelwaarde: 0
  - Periode: 5 (minuten)
  - Frequentie: 5 (minuten)
- Naam van waarschuwingsregel: Dagelijkse gegevenslimiet bereikt
- Ernst: Waarschuwing (ernst 1)


In het volgende voorbeeld wordt een waarschuwing gemaakt wanneer de dagelijkse limiet voor het verzamelen van gegevens is bereikt. 

- Doel: Selecteer uw Log Analytics-werkruimte
- Criteria:
  - Signaalnaam: Aangepast zoeken in logboeken
  - Zoekquery: `_LogOperation | where Category == "Ingestion" | where Operation == "Data collection Status" | where Level == "Warning"`
  - Op basis van: Aantal resultaten
  - Voorwaarde: Groter dan
  - Drempelwaarde: 0
  - Periode: 5 (minuten)
  - Frequentie: 5 (minuten)
- Naam van waarschuwingsregel: Dagelijkse gegevenslimiet bereikt
- Ernst: Waarschuwing (ernst 1)



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [logboekwaarschuwingen.](../alerts/alerts-log.md)
- [Querycontrolegegevens voor uw](./query-audit.md) werkruimte verzamelen.
