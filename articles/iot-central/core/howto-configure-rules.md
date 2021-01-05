---
title: Regels en acties configureren in Azure IoT Central | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u als een bouwer op telemetrie regels en acties in uw Azure IoT Central-toepassing kunt configureren.
author: vavilla
ms.author: vavilla
ms.date: 12/23/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 378a1dbcfbc89cdf9b24dc2490db583f1135b9a2
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796785"
---
# <a name="configure-rules"></a>Regels configureren

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

Regels in IoT Central fungeren als een hulp programma voor aanpas bare reactie dat actief bewaakte gebeurtenissen van verbonden apparaten activeren. In de volgende secties wordt beschreven hoe regels worden geëvalueerd.

## <a name="select-target-devices"></a>Doel apparaten selecteren

Gebruik de sectie doel apparaten om te selecteren op welk soort apparaten deze regel wordt toegepast. Met filters kunt u verder verfijnen welke apparaten moeten worden opgenomen. De filters gebruiken eigenschappen op het apparaatprofiel om de set apparaten te filteren. Filters zelf activeren geen actie. In de volgende scherm afbeelding zijn de apparaten waarop de doel **machine** wordt toegepast, het type apparaat sjabloon. Met het filter wordt aangegeven dat de regel alleen **koel kasten** moet bevatten waarvan de eigenschap **gefabriceerde status** gelijk is aan **Washington**.

![Voorwaarden](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Meerdere voor waarden gebruiken

Voor waarden worden de regels geactiveerd. Wanneer u op dit moment meerdere voor waarden aan een regel toevoegt, zijn ze logisch en samen. Met andere woorden: aan alle voor waarden moet worden voldaan voordat de regel als waar kan worden geëvalueerd.  

In de volgende scherm afbeelding worden de voor waarden gecontroleerd wanneer de Tempe ratuur groter is dan 70 &deg; F en de vochtigheid kleiner is dan 10. Wanneer beide instructies waar zijn, evalueert de regel naar waar en wordt een actie geactiveerd.

![Scherm afbeelding toont een koel monitor met voor waarden die zijn opgegeven voor de Tempe ratuur en vochtigheid.](media/howto-configure-rules/conditions.png)

### <a name="use-a-cloud-property-in-a-value-field"></a>Een eigenschap Cloud gebruiken in een waardeveld

U kunt vanuit het veld **waarde** voor een voor waarde verwijzen naar een eigenschap cloud van de sjabloon. De Cloud eigenschap en de telemetrie-waarde moeten soort gelijke typen hebben. Als de **Tempe ratuur** bijvoorbeeld een dubbele waarde is, worden alleen eigenschappen van de cloud van het type Double show als opties in de vervolg keuzelijst met **waarden** .

Als u een telemetrie-waarde voor gebeurtenis type kiest, bevat de vervolg keuzelijst **waarde** de optie **any**. Met **deze optie wordt de regel** geactiveerd wanneer uw toepassing een gebeurtenis van dat type ontvangt, ongeacht de payload.

## <a name="use-aggregate-windowing"></a>Aggregatie venster gebruiken

Regels evalueren cumulatieve tijd Vensters als tumblingvenstertriggers Windows. In de onderstaande scherm afbeelding is het tijd venster vijf minuten. De regel wordt elke vijf minuten geëvalueerd op de laatste vijf minuten van de gegevens. De gegevens worden slechts eenmaal geëvalueerd in het venster waarin deze overeenkomt.

![Tumblingvenstertriggers Windows](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Regels gebruiken met IoT Edge modules

Een beperking geldt voor regels die worden toegepast op IoT Edge modules. Regels op telemetrie van verschillende modules worden niet als geldige regels geëvalueerd. Doe het volgende als voor beeld. De eerste voor waarde van de regel bevindt zich op een temperatuur telemetrie van module A. De tweede voor waarde van de regel bevindt zich op een vochtigheids-telemetrie in module B. Aangezien de twee voor waarden afkomstig zijn uit verschillende modules, is dit een ongeldige set voor waarden. De regel is ongeldig en er treedt een fout op bij het opslaan van de regel.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een regel in uw Azure IoT Central-toepassing configureert, kunt u meer informatie over het [configureren van geavanceerde regels](howto-configure-rules-advanced.md) met behulp van automatische energie of Azure Logic apps.
