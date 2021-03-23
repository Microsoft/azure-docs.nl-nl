---
title: 'Quickstart: Beveiligingswaarschuwingen onderzoeken'
description: Beveiligingswaarschuwingen van Defender for IoT op uw IoT-apparaten leren kennen, erop inzoomen en onderzoeken.
ms.topic: quickstart
ms.date: 07/30/2020
ms.openlocfilehash: 2eb4a10372680348536231aa0333c43199b8d883
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780987"
---
# <a name="quickstart-investigate-security-alerts"></a>Quickstart: Beveiligingswaarschuwingen onderzoeken

Gepland onderzoek en herstel van de waarschuwingen die zijn uitgegeven door Azure Defender for IoT, is de beste manier om compliance en beveiliging te garanderen in uw IoT-oplossing.

In deze quickstart verkennen we de informatie die beschikbaar is in elke IoT-beveiligingswaarschuwing, en wordt uitgelegd hoe u kunt inzoomen en de details van elke waarschuwing en het bijbehorende apparaat kunt gebruiken om te reageren en te herstellen. 

Aan de slag. 


## <a name="investigate-new-security-alerts"></a>Nieuwe beveiligingswaarschuwingen onderzoeken

In de lijst met beveiligingswaarschuwingen van IoT Hub worden alle geaggregeerde beveiligingswaarschuwingen voor uw IoT Hub weergegeven. 

1. Open in de Azure Portal de **IoT Hub** die u wilt onderzoeken op nieuwe waarschuwingen.
1. Selecteer **Waarschuwingen** in het menu **Beveiliging**. Alle beveiligingswaarschuwingen voor de IoT Hub worden weergegeven en de waarschuwingen met een **nieuwe** markering, markeren uw waarschuwingen van de afgelopen 24 uur.
:::image type="content" source="media/quickstart/investigate-new-security-alerts.png" alt-text="Nieuwe IoT-beveiligingswaarschuwingen onderzoeken met behulp van de markering van nieuwe waarschuwingen":::
1. Selecteer en open een waarschuwing in de lijst om de details van de waarschuwing te openen en in te zoomen op de specificaties van de waarschuwing. 

## <a name="security-alert-details"></a>Details van beveiligingswaarschuwing

Bij het openen van elke geaggregeerde waarschuwing worden de gedetailleerde beschrijving van de waarschuwing, de herstelstappen en de apparaat-ID weergegeven voor elk apparaat dat een waarschuwing heeft geactiveerd, evenals de ernst van de waarschuwing en de toegang tot direct onderzoek met behulp van Log Analytics. 

1. Selecteer en open een beveiligingswaarschuwing uit de lijst met **IoT Hub** > **beveiligings** >  **waarschuwingen**. 
1. Bekijk de **beschrijving** van de waarschuwing, de **ernst**, de **bron van de detectie**, en de **apparaatgegevens** van alle apparaten die deze waarschuwing hebben uitgegeven tijdens de aggregatieperiode.
:::image type="content" source="media/quickstart/drill-down-iot-alert-details.png" alt-text="Inzoomen en de details van elk apparaat in een geaggregeerde waarschuwing controleren "::: 
1. Nadat u de details van de waarschuwing hebt bekeken, gebruikt u de instructies uit de **stap handmatig herstel** om het probleem dat de waarschuwing heeft veroorzaakt, te herstellen en/of op te lossen. 
:::image type="content" source="media/quickstart/iot-alert-manual-remediation-steps.png" alt-text="Voer de handmatige herstelstappen uit om de beveiligingswaarschuwingen van uw apparaat op te lossen of te verhelpen":::

1. Als verder onderzoek is vereist, **onderzoek dan de waarschuwingen in Log Analytics** met behulp van de link. 
:::image type="content" source="media/quickstart/investigate-iot-alert-log-analytics.png" alt-text="Als u een waarschuwing verder wilt onderzoeken, gebruikt u de log analytics-link op het scherm":::

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over waarschuwingstypen van Azure Defender en mogelijke aanpassingen...

> [!div class="nextstepaction"]
> [Inzicht in IoT- beveiligingswaarschuwingen](concept-security-alerts.md)
