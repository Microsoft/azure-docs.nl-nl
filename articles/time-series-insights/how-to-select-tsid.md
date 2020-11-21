---
title: Aanbevolen procedures voor het kiezen van een tijd reeks-ID-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over aanbevolen procedures bij het kiezen van een time series-ID in Azure Time Series Insights Gen2.
author: shipramishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: c3f6f6a5ac1068f2eabca351e85376b8e16d1058
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016748"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Best practices voor het kiezen van een tijdreeks-id

Dit artikel bevat een overzicht van het belang van de tijd reeks-ID voor uw Azure Time Series Insights Gen2-omgeving en aanbevolen procedures voor het kiezen van een.

## <a name="choose-a-time-series-id"></a>Een Time Series-id kiezen

Het selecteren van de juiste tijd reeks-ID is van cruciaal belang. Het kiezen van een Time Series-id is net als het kiezen van een partitiesleutel voor een database. Dit is vereist wanneer u een Azure Time Series Insights Gen2-omgeving maakt.

> [!IMPORTANT]
> De time series-Id's zijn:
>
> * Een *hoofdletter gevoelige* eigenschap: letter-en teken-darmen worden gebruikt in Zoek opdrachten, vergelijkingen, updates en het partitioneren van partities.
> * Een *onveranderbare* eigenschap: zodra deze is gemaakt, kan deze niet meer worden gewijzigd.

> [!TIP]
> Als uw gebeurtenis bron een IoT-hub is, is uw tijd reeks-ID waarschijnlijk ***iothub-Connection-apparaat-id** _.

De belangrijkste aanbevolen procedures zijn:

_ Kies een partitie sleutel met veel afzonderlijke waarden (bijvoorbeeld honderden of duizenden). In veel gevallen kan dit de apparaat-ID, sensor-ID of label-ID in uw JSON zijn.
* De time series-ID moet uniek zijn op het leaf-knooppunt niveau van uw [Time Series-model](./concepts-model-overview.md).
* De teken limiet voor de eigenschaps naam van de tijd reeks-ID is 128. De teken limiet voor de eigenschaps waarde van de tijd reeks-ID is 1.024.
* Als een unieke eigenschaps waarde voor de time series-ID ontbreekt, wordt deze behandeld als een null-waarde en volgt dezelfde regel van de uniekheids beperking.
* Als uw tijd reeks-ID is genest in een complex JSON-object, moet u de inkomende [regels](./concepts-json-flattening-escaping-rules.md) volgen wanneer u de naam van uw eigenschap opgeeft. Bekijk voor beeld [B](concepts-json-flattening-escaping-rules.md#example-b).
* U kunt ook Maxi maal *drie* sleutel eigenschappen selecteren als uw tijd reeks-id. De combi natie hiervan is een samengestelde sleutel die de tijd reeks-ID vertegenwoordigt.  
  > [!NOTE]
  > Uw drie sleutel eigenschappen moeten teken reeksen zijn.
  > U moet een query uitvoeren op deze samengestelde sleutel in plaats van één eigenschap tegelijk.

## <a name="select-more-than-one-key-property"></a>Meer dan één sleutel eigenschap selecteren

In de volgende scenario's wordt het selecteren van meer dan één sleutel eigenschap als uw tijd reeks-ID beschreven.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Voor beeld 1: een time reeks-ID met een unieke sleutel

* U hebt verouderde vloots van activa. Elk heeft een unieke sleutel.
* Eén vloot wordt uniek geïdentificeerd door de property **deviceId**. Voor een andere vloot is de unieke eigenschap **objectId**. Geen van beide vloot bevat de unieke eigenschap van de andere vloot. In dit voor beeld selecteert u twee sleutels, **deviceId** en **objectId** als unieke sleutels.
* We accepteren Null-waarden en het ontbreken van de aanwezigheid van een eigenschap in de nettolading van de gebeurtenis telt als een null-waarde. Dit is ook de juiste manier om het verzenden van gegevens naar twee gebeurtenis bronnen te verwerken, waarbij de gegevens in elke gebeurtenis bron een unieke time series-ID hebben.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Voor beeld 2: een tijd reeks-ID met een samengestelde sleutel

* U wilt dat meerdere eigenschappen uniek zijn binnen dezelfde vloot van activa.
* U bent een fabrikant van Smart gebouwen en implementeert Sens oren in elke kamer. In elke ruimte hebt u doorgaans dezelfde waarden voor **sensorId**. Voor beelden zijn **sensor1**, **sensor2** en **sensor3**.
* Uw gebouw heeft overlappende vloer-en kamer nummers tussen sites in de eigenschap **flrRm**. Deze getallen hebben waarden als **1a**, **2b** en **3a**.
* U hebt een eigenschap, **locatie**, die waarden bevat zoals **Redmond**, **Barcelona** en **Tokyo**. Als u een uniek karakter wilt maken, wijst u de volgende drie eigenschappen toe als uw tijd reeks-ID-sleutels: **sensorId**, **flrRm** en **locatie**.

Voor beeld van onbewerkte gebeurtenis:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

In de Azure Portal, kunt u de samengestelde sleutel als volgt invoeren:

[![Configureer de Time Series-id voor de omgeving.](media/v2-how-to-tsid/configure-environment-key.png)](media/v2-how-to-tsid/configure-environment-key.png#lightbox)

  > [!NOTE]
  > Voer in Azure Portal geen door komma's gescheiden namen van eigenschappen in één texbox in, anders wordt deze behandeld als één eigenschaps naam met komma's.
  > Geef elke eigenschaps naam op in een eigen texbox.

## <a name="next-steps"></a>Volgende stappen

* Lees de [regels voor json-afvlakking en-Escapes](./concepts-json-flattening-escaping-rules.md) om te begrijpen hoe gebeurtenissen worden opgeslagen.

* Plan uw [Azure time series Insights Gen2-omgeving](./how-to-plan-your-environment.md).