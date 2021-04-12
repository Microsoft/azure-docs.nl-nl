---
title: Problemen met Azure IoT Hub-fout 429001 ThrottlingException
description: Meer informatie over het oplossen van fout 429001 ThrottlingException
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 9619a3d2ba24e806f6c684a5576bce03d7e6b793
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060972"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

In dit artikel worden de oorzaken en oplossingen voor **429001 ThrottlingException** -fouten beschreven.

## <a name="symptoms"></a>Symptomen

Uw aanvragen voor IoT Hub mislukken met de fout **429001 ThrottlingException**.

## <a name="cause"></a>Oorzaak

De [limieten](./iot-hub-devguide-quotas-throttling.md) voor IOT hub beperking zijn voor de aangevraagde bewerking overschreden.

## <a name="solution"></a>Oplossing

Controleer of u de beperkings limiet hebt bereikt door uw *telemetrie-bericht verzendings* waarden te vergelijken met de hierboven opgegeven limieten. U kunt ook het aantal metrieke *beperkings fouten* controleren. Zie metrische gegevens over de [telemetrie](monitor-iot-hub-reference.md#device-telemetry-metrics)van een apparaat voor meer informatie over deze gegevens. Zie [IOT hub bewaken](monitor-iot-hub.md)voor meer informatie over het gebruik van metrische gegevens voor het bewaken van uw IOT-hub.

IoT Hub retourneert 429 ThrottlingException nadat de limiet te lang is geschonden. Dit wordt gedaan zodat uw berichten niet worden verwijderd als uw IoT-hub burst-verkeer ontvangt. In de tussentijd worden de berichten in IoT Hub verwerkt volgens de vertragingsfactor voor bewerkingen. Als er te veel achterstallig verkeer is, kan dit traag zijn. Zie [IoT traffic shaping](./iot-hub-devguide-quotas-throttling.md#traffic-shaping)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

U kunt [uw IOT hub opschalen](./iot-hub-scaling.md) als u quotum-of beperkings limieten wilt uitvoeren.