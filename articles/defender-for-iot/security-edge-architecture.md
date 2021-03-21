---
title: Defender voor IoT Defender-IoT-micro agent voor IoT Edge
description: Meer informatie over de architectuur en mogelijkheden van Azure Defender voor IoT Defender-IoT-micro agent voor IoT Edge.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 4b7377f74e24660945555c112fed4d77d2c5ddcc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495943"
---
# <a name="azure-defender-for-iot-edge-defender-iot-micro-agent"></a>Azure Defender voor IoT Edge Defender-IoT-micro-agent

[Azure IOT Edge](../iot-edge/index.yml) biedt krachtige mogelijkheden voor het beheren en uitvoeren van zakelijke werk stromen aan de rand.
Het belangrijkste deel dat IoT Edge speelt in IoT-omgevingen, maakt het bijzonder aantrekkelijk voor kwaad aardige actors.

Defender voor IoT Defender-IoT-micro agent biedt een uitgebreide beveiligings oplossing voor uw IoT Edge-apparaten.
In de module Defender voor IoT worden onbewerkte beveiligings gegevens van uw besturings systeem en container systeem verzameld, geaggregeerd en geanalyseerd in aanbevelingen voor beveiliging en waarschuwingen.

Net als bij Defender voor IoT-beveiligings agenten voor IoT-apparaten is de module Defender voor IoT Edge zeer aanpasbaar via de module twee.
Zie [uw agent configureren](how-to-agent-configuration.md) voor meer informatie.

Defender voor IoT Defender-IoT-micro agent for IoT Edge biedt de volgende functies:

- Hiermee worden onbewerkte beveiligings gebeurtenissen van het onderliggende besturings systeem (Linux) en de IoT Edge container systemen verzameld.

  Zie [Defender voor IOT-agent configuratie](how-to-agent-configuration.md) voor meer informatie over beschik bare beveiligings gegevens verzamelaars.

- Analyse van IoT Edge implementatie manifesten.

- Voegt onbewerkte beveiligings gebeurtenissen samen in berichten die via [IOT Edge hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub)worden verzonden.

- Configuratie verwijderen via het gebruik van de Defender-IoT-micro-agent dubbele.

  Zie [een Defender voor IOT-agent configureren](how-to-agent-configuration.md) voor meer informatie.

Defender voor IoT Defender-IoT-micro agent for IoT Edge wordt uitgevoerd in een geprivilegieerde modus onder IoT Edge.
De modus Privileged is vereist om de module toe te staan het besturings systeem en andere IoT Edge modules te bewaken.

## <a name="module-supported-platforms"></a>Door module ondersteunde platforms

Defender voor IoT Defender-IoT-micro agent voor IoT Edge is momenteel alleen beschikbaar voor Linux.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de architectuur en mogelijkheden van Defender voor IoT Defender-IoT-micro-agent voor IoT Edge.

Als u aan de slag wilt gaan met Defender voor IoT-implementatie, gebruikt u de volgende artikelen:

- [Defender-IOT-micro agent voor IOT Edge](how-to-deploy-edge.md) implementeren
- Meer informatie over [het configureren van uw Defender-IOT-micro-agent](how-to-agent-configuration.md)
- Raadpleeg de Defender voor IoT [Defender voor IOT-horizon](resources-manage-proprietary-protocols.md)
- Meer informatie over het [inschakelen van Defender voor IOT-service in uw IOT hub](quickstart-onboard-iot-hub.md)
- Meer informatie over de service van de [Veelgestelde vragen over Defender voor IOT](resources-frequently-asked-questions.md)