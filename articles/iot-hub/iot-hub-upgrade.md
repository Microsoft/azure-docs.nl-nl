---
title: Upgrade uitvoeren voor Azure IoT Hub | Microsoft Docs
description: Wijzig de prijs-en schaal categorie voor IoT Hub om meer mogelijkheden voor berichten en het beheer van apparaten te krijgen.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 96c3a7b2cfda23f173f4caeff4fb7a92b1ddc438
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "61440206"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Een upgrade uitvoeren voor uw IoT-hub

Als uw IoT-oplossing groeit, is Azure IoT Hub klaar om u te helpen opschalen. Azure IoT Hub biedt twee lagen, Basic (B) en Standard (S), voor klanten die verschillende functies willen gebruiken. Binnen elke laag zijn drie grootten (1, 2 en 3) die het aantal berichten bepalen dat elke dag kan worden verzonden.

Wanneer u meer apparaten hebt en meer mogelijkheden nodig hebt, zijn er drie manieren om uw IoT-hub aan te passen aan uw behoeften:

* Eenheden toevoegen in de IoT-hub. Elke extra eenheid in een B1 IoT-hub biedt bijvoorbeeld een extra 400.000 berichten per dag.

* Wijzig de grootte van de IoT-hub. Migreer bijvoorbeeld van de B1-laag naar de B2-laag om het aantal berichten te verhogen dat elke eenheid per dag kan ondersteunen.

* Voer een upgrade uit naar een hogere laag. Voer bijvoorbeeld een upgrade uit van de B1-laag naar de S1-laag voor toegang tot geavanceerde functies met dezelfde bericht capaciteit.

Deze wijzigingen kunnen allemaal optreden zonder dat bestaande bewerkingen worden onderbroken.

Als u uw IoT-hub wilt downgradeen, kunt u eenheden verwijderen en de grootte van de IoT-hub verminderen, maar u kunt geen downgrade uitvoeren naar een lagere laag. U kunt bijvoorbeeld van de S2-laag overstappen naar de S1-laag, maar niet van de S2-laag naar de B1-laag. Er kan slechts één type [IOT hub-editie](https://azure.microsoft.com/pricing/details/iot-hub/) binnen een laag worden gekozen per IOT hub. U kunt bijvoorbeeld een IoT Hub maken met meerdere eenheden van S1, maar niet met een combi natie van eenheden uit verschillende versies, zoals S1 en B3, of S1 en S2.

Deze voor beelden zijn bedoeld om u te helpen begrijpen hoe u uw IoT-hub kunt aanpassen als uw oplossing verandert. Voor specifieke informatie over de mogelijkheden van elke laag, moet u altijd verwijzen naar de [prijzen van Azure IOT hub](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="upgrade-your-existing-iot-hub"></a>Uw bestaande IoT-hub bijwerken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) en ga naar uw IoT Hub.

2. Selecteer **prijzen en schalen**.

   ![Prijzen en schaal](./media/iot-hub-upgrade/pricing-scale.png)

3. Als u de laag voor uw hub wilt wijzigen, selecteert u **prijs en schaal laag**. Kies de nieuwe laag en klik vervolgens op **selecteren**.

   ![Prijs- en schaalniveau](./media/iot-hub-upgrade/select-tier.png)

4. Als u het aantal eenheden in uw hub wilt wijzigen, voert u een nieuwe waarde in bij **IOT hub eenheden**.

5. Selecteer **Opslaan** om uw wijzigingen op te slaan.

Uw IoT-hub is nu aangepast en uw configuraties worden niet gewijzigd.

De maximale partitie limiet voor de Basic-laag IoT Hub en de standaardlaag IoT Hub is 32. De meeste IoT-hubs hebben alleen vier partities nodig. De partitie limiet wordt gekozen wanneer IoT Hub wordt gemaakt en koppelt de apparaat-naar-Cloud-berichten aan het aantal gelijktijdige lezers van deze berichten. Deze waarde blijft ongewijzigd wanneer u migreert van de Basic-laag naar de Standard-laag.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het kiezen van de juiste IOT hub-laag](iot-hub-scaling.md).