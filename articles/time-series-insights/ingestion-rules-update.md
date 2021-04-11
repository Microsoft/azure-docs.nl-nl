---
title: Aanstaande wijzigingen in de opname-en afvlakking regels in Azure Time Series Insights Gen2 | Microsoft Docs
description: Wijzigingen in opname regel
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: lyhughes
ms.openlocfilehash: 44553e2a57f796dcf48561ff3b273e25514247ae
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504529"
---
# <a name="upcoming-changes-to-json-flattening-and-escaping-rules-for-new-environments"></a>Aanstaande wijzigingen in JSON-afvlakking en-Escape regels voor nieuwe omgevingen

> [!IMPORTANT]
> Deze wijzigingen worden alleen toegepast op *onlangs gemaakte* Microsoft Azure time series Insights Gen2-omgevingen. De wijzigingen zijn niet van toepassing op gen1-omgevingen.

Uw Azure Time Series Insights Gen2-omgeving maakt uw opslag kolommen dynamisch volgens een bepaalde set naam conventies. Wanneer een gebeurtenis wordt opgenomen, past Time Series Insights een reeks regels toe op de JSON-nettolading en eigenschaps namen. Wijzigingen in de manier waarop JSON-gegevens worden afgevlakt en opgeslagen, zijn in juli 2020 van kracht geworden voor nieuwe Azure Time Series Insights Gen2-omgevingen. Deze wijziging is van invloed op de volgende gevallen:

* De JSON-Payload bevat geneste objecten.
* De JSON-nettolading bevat matrices.
* U gebruikt een van de volgende vier speciale tekens in de naam van een JSON-eigenschap: `[` `\` `.``'`
* Een of meer van uw time series-ID-eigenschappen bevinden zich in een genest object.

Als u een nieuwe omgeving maakt en een of meer van deze cases van toepassing zijn op uw gebeurtenis lading, worden uw gegevens afgevlakt en op een andere manier opgeslagen. De volgende tabel geeft een overzicht van de wijzigingen:

| Huidige regel | Nieuwe regel | Voor beeld JSON | Vorige kolom naam | Nieuwe kolomnaam
|---|---| ---| ---|  ---|
| Geneste JSON wordt afgevlakt door gebruik te maken van een onderstrepings teken als de afbakening. |Geneste JSON wordt afgevlakt door een punt als de afbakening te gebruiken.  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| Speciale tekens worden niet met een escape-teken. | De namen van JSON-eigenschappen die de speciale tekens bevatten `.` `[`   `\` en die `'` worden voorafgegaan door `['` en `']` . In `['` en is `']` er nog meer Escapes van enkele aanhalings tekens en backslashes. Een enkele aanhalings tekens worden geschreven als `\'` een back slash als `\\` .  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| Matrices van primitieven worden opgeslagen als een teken reeks. | Matrices van primitieve typen worden opgeslagen als een dynamisch type.  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Matrices met objecten worden altijd afgevlakt, waardoor er meerdere gebeurtenissen worden geproduceerd. | Als de objecten in een matrix geen TS-ID of Time Stamp-eigenschappen hebben, wordt de matrix van objecten geheel als dynamisch type opgeslagen. | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Aanbevolen wijzigingen voor nieuwe omgevingen

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Als uw TS-ID en/of tijds tempel eigenschap binnen een object is genest

Nieuwe implementaties moeten overeenkomen met de nieuwe opname regels. Als uw TS-ID bijvoorbeeld is `telemetry_tagId` , moet u alle Azure Resource Manager sjablonen of geautomatiseerde implementatie scripts bijwerken om te configureren `telemetry.tagId` als de OMGEVINGS-id. U hebt deze wijziging ook nodig voor tijds tempels van gebeurtenis bronnen in geneste JSON.

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Als uw payload geneste JSON of speciale tekens bevat en u de variabelen expressies voor het maken van de [tijd reeks](./concepts-model-overview.md) kunt automatiseren

Werk uw client code bij waarmee [TypesBatchPut](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) wordt uitgevoerd, zodat deze overeenkomen met de nieuwe opname regels. U moet bijvoorbeeld een eerdere [Time Series-expressie](/rest/api/time-series-insights/reference-time-series-expression-syntax) van `"value": {"tsx": "$event.series_value.Double"}` op een van de volgende opties bijwerken:

* `"value": {"tsx": "$event.series.value.Double"}`
* `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure time series Insights Gen2-opslag en](./concepts-ingestion-overview.md)inkomend verkeer.

* Meer informatie over het uitvoeren van een query op uw gegevens met behulp van [Time Series query-api's](./concepts-query-overview.md).

* Meer informatie over de [syntaxis van de nieuwe time series-expressie](/rest/api/time-series-insights/reference-time-series-expression-syntax).