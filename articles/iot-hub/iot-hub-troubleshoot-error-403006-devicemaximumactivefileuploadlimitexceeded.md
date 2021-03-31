---
title: Problemen met Azure IoT Hub-fout 403006 DeviceMaximumActiveFileUploadLimitExceeded
description: Meer informatie over het oplossen van fout 403006 DeviceMaximumActiveFileUploadLimitExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1e3c05e4cc3ccf34573b55d3729aded16e26d66e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "76960839"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

In dit artikel worden de oorzaken en oplossingen voor **403006 DeviceMaximumActiveFileUploadLimitExceeded** -fouten beschreven.

## <a name="symptoms"></a>Symptomen

De aanvraag voor het uploaden van het bestand is mislukt met de fout code **403006** en een bericht ' aantal actieve aanvragen voor uploaden van bestanden mag niet meer dan 10 ' zijn.

## <a name="cause"></a>Oorzaak

Elke apparaatclient is beperkt tot [10 gelijktijdige uploads van bestanden](./iot-hub-devguide-quotas-throttling.md#other-limits). 

U kunt de limiet eenvoudig overschrijden als uw apparaat geen melding krijgt IoT Hub wanneer het uploaden van bestanden is voltooid. Dit probleem wordt meestal veroorzaakt door een onbetrouwbaar netwerk op het apparaat.

## <a name="solution"></a>Oplossing

Controleer of het apparaat de voltooiing van het [uploaden van IOT hub bestanden](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload)onmiddellijk kan melden. [Verminder vervolgens de TTL van het SAS-token voor de configuratie van het uploaden van bestanden](iot-hub-configure-file-upload.md).

## <a name="next-steps"></a>Volgende stappen

Zie [bestanden uploaden met IOT hub](./iot-hub-devguide-file-upload.md) en [IOT hub bestand uploads configureren met behulp van de Azure Portal](./iot-hub-configure-file-upload.md)voor meer informatie over bestand geüpload.