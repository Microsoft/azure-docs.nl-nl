---
title: bestand opnemen
description: bestand opnemen
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 55390a3eb2a074729b4a0868416a95e208325b76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91401075"
---
```json
{
  "VendorName": "Microsoft",
  "AlertType": "SUSPECT_SVCHOST",
  "StartTimeUtc": "2016-12-20T13:38:00.000Z",
  "EndTimeUtc": "2019-12-20T13:40:01.733Z",
  "ProcessingEndTime": "2019-09-16T12:10:19.5673533Z",
  "TimeGenerated": "2016-12-20T13:38:03.000Z",
  "IsIncident": false,
  "Severity": "High",
  "Status": "New",
  "ProductName": "Azure Security Center",
  "SystemAlertId": "2342409243234234_F2BFED55-5997-4FEA-95BD-BB7C6DDCD061",
  "CompromisedEntity": "WebSrv1",
  "Intent": "Execution",
  "AlertDisplayName": "Suspicious process detected",
  "Description": "Suspicious process named 'SVCHOST.EXE' was running from path: %{Process Path}",
  "RemediationSteps": ["contact your security information team"],
  "ExtendedProperties": {
    "Process Path": "c:\\temp\\svchost.exe",
    "Account": "Contoso\\administrator",
    "PID": 944,
    "ActionTaken": "Detected"
  },
  "Entities": [],
  "ResourceIdentifiers": [
        {
            Type: "AzureResource",
            AzureResourceId: "/subscriptions/86057C9F-3CDD-484E-83B1-7BF1C17A9FF8/resourceGroups/backend-srv/providers/Microsoft.Compute/WebSrv1"
        },
        {
            Type: "LogAnalytics",
            WorkspaceId: "077BA6B7-8759-4F41-9F97-017EB7D3E0A8",
            WorkspaceSubscriptionId: "86057C9F-3CDD-484E-83B1-7BF1C17A9FF8",
            WorkspaceResourceGroup: "omsrg",
            AgentId: "5A651129-98E6-4E6C-B2CE-AB89BD815616",
        }
  ]
}
```

### <a name="the-data-model-of-the-schema"></a>Het gegevens model van het schema

|Veld|Gegevenstype|Beschrijving|
|----|----|----|
|**AlertDisplayName**|Tekenreeks|De weergave naam van de waarschuwing.|
|**AlertType**|Tekenreeks|Het type waarschuwing. Waarschuwingen van hetzelfde type moeten dezelfde waarde hebben. Dit veld is een sleutel reeks die het type waarschuwing vertegenwoordigt en niet van een waarschuwings exemplaar. Alle waarschuwings exemplaren van dezelfde detectie logica/analyse moeten dezelfde waarde voor het waarschuwings type hebben.|
|**CompromisedEntity**|Tekenreeks|De weergave naam van de resource die het meest aan deze waarschuwing is gerelateerd.|
|**Beschrijving**|Tekenreeks|De beschrijving van de waarschuwing.|
|**EndTimeUtc**|DateTime|De tijd van de laatste gebeurtenis of activiteit die in de waarschuwing is opgenomen.  Het veld moet een teken reeks zijn die voldoet aan de ISO8601-indeling, inclusief informatie over de UTC-tijd zone.|
|**Entiteiten**|IEnumerable (IEntity)|Een lijst met entiteiten die aan de waarschuwing zijn gerelateerd. Deze lijst kan een combi natie van entiteiten van verschillende typen bevatten. Het entiteits type kan een van de typen zijn die zijn gedefinieerd in de sectie entities. Entiteiten die zich niet in de onderstaande lijst bevinden, kunnen ook worden verzonden, maar het is niet zeker dat ze worden verwerkt (de waarschuwing wordt niet uitgevoerd met nieuwe typen entiteiten).|
|**ExtendedProperties**|Dictionary (teken reeks, teken reeks)|Providers kunnen eventueel aangepaste velden toevoegen.|
|**Intentie**|Enum|De Kill-keten die zich achter de waarschuwing bevindt. Zie [bedoelingen](../articles/security-center/alerts-reference.md#intentions)voor een lijst met ondersteunde waarden en uitleg van de ondersteunde Kill-ketens van Azure Security Center.<br/>Dit veld kan meerdere waarden bevatten (gescheiden door komma's).|
|**IsIncident**|Booleaanse waarde|Dit veld bepaalt of de waarschuwing een incident (een samengestelde groepering van verschillende waarschuwingen) of een enkele waarschuwing is. De standaard waarde voor het veld is ' false ' (wat betekent dat het een enkele waarschuwing is).|
|**ProcessingEndTime**|DateTime|Het tijdstip waarop de waarschuwing voor de eind gebruiker toegankelijk is in het oorspronkelijke product dat de waarschuwing heeft.|
|**ProductName**|Tekenreeks|De naam van het product dat deze waarschuwing heeft gepubliceerd (Azure Security Center, Azure ATP, micro soft Defender ATP, MCAS, enzovoort).|
|**RemediationSteps**|Orderverzamellijst<String>|Hand matige actie-items die moeten worden uitgevoerd om de waarschuwing te herstellen.|
|**ResourceIdentifiers**|Lijst (resource-Id's)|De resource-id's voor deze waarschuwing die kunnen worden gebruikt om de waarschuwing te sturen naar de juiste product blootstellings groep (Tenant, werk ruimte, abonnement etc.). Er kunnen meerdere id's van hetzelfde type per waarschuwing zijn.|
|**Ernst**|Enum|De ernst van de waarschuwing, zoals gerapporteerd door de provider. Mogelijke waarden: informatief, laag, gemiddeld en hoog.|
|**StartTimeUtc**|DateTime|De tijd van de eerste gebeurtenis of activiteit die in de waarschuwing is opgenomen. Het veld moet een teken reeks zijn die voldoet aan de ISO8601-indeling, inclusief informatie over de UTC-tijd zone.|
|**Status**|Enum|De status van de levens cyclus van de waarschuwing.<br/>Ondersteunde statussen zijn: nieuw, opgelost, genegeerd, onbekend.<br/>Voor een waarschuwing waarbij een andere waarde dan de ondersteunde opties wordt opgegeven, wordt de status ' onbekend ' toegewezen.<br/>Bij een waarschuwing waarbij geen waarde wordt opgegeven, wordt de status nieuw toegewezen.|
|**SystemAlertId**|Tekenreeks|De id van de waarschuwing.|
|**TimeGenerated**|DateTime|Het tijdstip waarop de waarschuwing is gegenereerd door de provider van de waarschuwing. Als niet wordt gerapporteerd door interne waarschuwings providers, kan een product ervoor kiezen de tijd toe te wijzen die door het product is ontvangen voor verwerking.  Het veld moet een teken reeks zijn die voldoet aan de ISO8601-indeling, inclusief informatie over de UTC-tijd zone.|
|**Leveranciers naam**|Tekenreeks|De naam van de leverancier die de waarschuwing heeft gegenereerd.|
|||
