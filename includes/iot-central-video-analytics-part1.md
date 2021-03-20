---
title: bestand opnemen
description: bestand opnemen
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2f3e4bf640b8da31a7fa4d818b94b0372d3026b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96763409"
---
De voorbeeldtoepassing bevat twee gesimuleerde apparaten en één IoT Edge-gateway. De volgende zelfstudies bieden twee benaderingen om te experimenteren met de mogelijkheden van de gateway en deze te begrijpen:

* Maak de IoT Edge-gateway in een Azure-VM en verbind een gesimuleerde camera.
* Maak de IoT Edge-gateway op een echt apparaat, zoals een Intel NUC, en verbind een echte camera.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * De Azure IoT Central-toepassingssjabloon voor videoanalyse gebruiken om een detailhandelstoepassing te maken
> * De toepassingsinstellingen aanpassen
> * Een apparaatsjabloon maken voor een IoT Edge-gatewayapparaat
> * Een gatewayapparaat toevoegen aan uw IoT Central-toepassing

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudiereeks hebt u het volgende nodig:

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u er een maken via de [Azure-registratiepagina](https://aka.ms/createazuresubscription).
* Als u een echte camera gebruikt, moet er een verbinding zijn tussen het IoT Edge-apparaat en de camera en hebt u het kanaal **Real-Time Streaming Protocol** nodig.

## <a name="initial-setup"></a>Eerste configuratie

In deze zelfstudies gaat u een aantal configuratiebestanden bijwerken en gebruiken. Eerste versies van deze bestanden zijn beschikbaar in de GitHub-opslagplaats [LVA-gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway). De opslagplaats bevat een [kladblokbestand](https://github.com/Azure/live-video-analytics/blob/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt) dat u kunt downloaden en gebruiken om configuratiewaarden vast te leggen van de services die u implementeert. Met dit bestand kunt u latere stappen in de zelfstudies voltooien.

Maak een map genaamd *lva-configuration* op uw lokale computer om exemplaren van deze bestanden op te slaan. Klik vervolgens met de rechtermuisknop op elk van de volgende koppelingen en kies **Opslaan als** om het bestand op te slaan in de map *lva-configuration*:
