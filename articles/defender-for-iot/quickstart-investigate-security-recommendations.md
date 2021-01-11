---
title: 'Quickstart: Beveiligingsaanbevelingen onderzoeken'
description: Onderzoek beveiligingsaanbevelingen met de Defender for IoT-beveiligingsservice.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 66b5694823096f0747ea099b631a53198a819816
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835037"
---
# <a name="quickstart-investigate-security-recommendations"></a>Quickstart: Beveiligingsaanbevelingen onderzoeken


Tijdige analyse en beperking van aanbevelingen door Defender for IoT is de beste manier om de beveiligingsstatus te verbeteren en het aanvalsoppervlak in uw IoT-oplossing te verkleinen.

In deze quickstart verkennen we de informatie die beschikbaar is in elke IoT-beveiligingsaanbeveling, en wordt uitgelegd hoe u kunt inzoomen en de details van elke aanbeveling en het bijbehorende apparaat kunt gebruiken om risico’s te voorkomen.

Aan de slag.

## <a name="investigate-new-recommendations"></a>Nieuwe aanbevelingen onderzoeken

In de lijst met IoT Hub-aanbevelingen worden alle geaggregeerde beveiligingsaanbevelingen voor uw IoT Hub weergegeven.

1.  Open in de Azure-portal de **IoT Hub** die u wilt onderzoeken op nieuwe aanbevelingen.

1.  Selecteer **Aanbevelingen** in het menu **Beveiliging**. Alle beveiligingsaanbevelingen voor de IoT Hub worden weergegeven, en de aanbevelingen met een markering **Nieuw** markeren uw aanbevelingen van de afgelopen 24 uur. 

    :::image type="content" source="media/quickstart/investigate-security-recommendations-expanded.png#lightbox" alt-text="Beveiligingsaanbevelingen onderzoeken met ASC for IoT](media/quickstart/investigate-security-recommendations-inline.png)":::


1.  Selecteer en open een aanbeveling in de lijst om de details van de aanbeveling te openen en in te zoomen op de specificaties van de waarschuwing.

## <a name="security-recommendation-details"></a>Details van de beveiligingsaanbeveling

Open elke geaggregeerde aanbeveling om de gedetailleerde beschrijving van de aanbeveling, herstelstappen en apparaat-id weer te geven voor elk apparaat dat een aanbeveling heeft geactiveerd. Ook wordt de ernst van de aanbeveling en directe toegang tot onderzoek weergegeven met behulp van Log Analytics.

1.  Selecteer en open een willekeurige beveiligingsaanbeveling in de lijst **IoT Hub** \> **Beveiliging** \> **Aanbevelingen**.

1.  Bekijk de **beschrijving**, **ernst** **apparaatgegevens** voor de aanbeveling van alle apparaten die deze aanbeveling hebben uitgegeven tijdens de aggregatie. 

1.  Nadat u de details van de aanbeveling hebt bekeken, gebruikt u de instructies uit de **stap handmatig herstel** om het probleem dat de aanbeveling heeft veroorzaakt, te herstellen en op te lossen. 

    [ :::image type="content" source="media/quickstart/remediate-security-recommendations-expanded.png#lightbox" alt-text="Beveiligingsaanbevelingen herstellen met ASC for IoT](media/quickstart/remediate-security-recommendations-inline.png)":::


1.  Bekijk de aanbevelingsdetails voor een specifiek apparaat door het gewenste apparaat te selecteren op de inzoompagina.

    [ :::image type="content" source="media/quickstart/explore-security-recommendation-detail-expanded.png#lightbox" alt-text="Specifieke beveiligingsaanbevelingen voor een apparaat onderzoeken met ASC for IoT](media/quickstart/explore-security-recommendation-detail-inline.png)":::


1.  Als verder onderzoek is vereist, kunt u **De aanbevelingen onderzoeken in Log Analytics** via de koppeling. 


## <a name="next-steps"></a>Volgende stappen

Ga door naar het volgende artikel om te lezen hoe u aangepaste waarschuwingen maakt.

> [!div class="nextstepaction"]
> [Aangepaste waarschuwingen maken](quickstart-create-custom-alerts.md)
