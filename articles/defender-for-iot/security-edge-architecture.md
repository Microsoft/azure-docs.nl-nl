---
title: Defender voor IoT-beveiligings module voor IoT Edge
description: Meer informatie over de architectuur en mogelijkheden van Azure Defender voor IoT Security module voor IoT Edge.
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
ms.openlocfilehash: 3e653983b39e8eb4ac13ad840ca53e4ab6d1cfc7
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820616"
---
# <a name="azure-defender-for-iot-edge-security-module"></a>Beveiligings module voor Azure Defender voor IoT Edge

[Azure IOT Edge](../iot-edge/index.yml) biedt krachtige mogelijkheden voor het beheren en uitvoeren van zakelijke werk stromen aan de rand.
Het belangrijkste deel dat IoT Edge speelt in IoT-omgevingen, maakt het bijzonder aantrekkelijk voor kwaad aardige actors.

Defender voor IoT Security module biedt een uitgebreide beveiligings oplossing voor uw IoT Edge-apparaten.
In de module Defender voor IoT worden onbewerkte beveiligings gegevens van uw besturings systeem en container systeem verzameld, geaggregeerd en geanalyseerd in aanbevelingen voor beveiliging en waarschuwingen.

Net als bij Defender voor IoT-beveiligings agenten voor IoT-apparaten is de module Defender voor IoT Edge zeer aanpasbaar via de module twee.
Zie [uw agent configureren](how-to-agent-configuration.md) voor meer informatie.

Defender voor IoT Security module voor IoT Edge biedt de volgende functies:

- Hiermee worden onbewerkte beveiligings gebeurtenissen van het onderliggende besturings systeem (Linux) en de IoT Edge container systemen verzameld.

  Zie [Defender voor IOT-agent configuratie](how-to-agent-configuration.md) voor meer informatie over beschik bare beveiligings gegevens verzamelaars.

- Analyse van IoT Edge implementatie manifesten.

- Voegt onbewerkte beveiligings gebeurtenissen samen in berichten die via [IOT Edge hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub)worden verzonden.

- Verwijder de configuratie door middel van het gebruik van de beveiligings module dubbele.

  Zie [een Defender voor IOT-agent configureren](how-to-agent-configuration.md) voor meer informatie.

Defender voor IoT-beveiligings module voor IoT Edge wordt uitgevoerd in een beschermde modus onder IoT Edge.
De modus Privileged is vereist om de module toe te staan het besturings systeem en andere IoT Edge modules te bewaken.

## <a name="module-supported-platforms"></a>Door module ondersteunde platforms

Defender voor IoT Security module voor IoT Edge is momenteel alleen beschikbaar voor Linux.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de architectuur en mogelijkheden van Defender voor IoT Security module voor IoT Edge.

Als u aan de slag wilt gaan met Defender voor IoT-implementatie, gebruikt u de volgende artikelen:

- [Beveiligings module voor IOT Edge](how-to-deploy-edge.md) implementeren
- Meer informatie over [het configureren van uw beveiligings module](how-to-agent-configuration.md)
- Raadpleeg de Defender voor IoT [Defender voor IOT-horizon](resources-manage-proprietary-protocols.md)
- Meer informatie over het [inschakelen van Defender voor IOT-service in uw IOT hub](quickstart-onboard-iot-hub.md)
- Meer informatie over de service van de [Veelgestelde vragen over Defender voor IOT](resources-frequently-asked-questions.md)