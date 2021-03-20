---
title: Problemen met Azure IoT Hub-fout 404001 DeviceNotFound
description: Meer informatie over het oplossen van fout 404001 DeviceNotFound
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76960826"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

In dit artikel worden de oorzaken en oplossingen voor **404001 DeviceNotFound** -fouten beschreven.

## <a name="symptoms"></a>Symptomen

Tijdens een Cloud-naar-apparaat-communicatie (C2D), zoals C2D-bericht, dubbele update of directe methode, mislukt de bewerking met fout **404001 DeviceNotFound**.

## <a name="cause"></a>Oorzaak

De bewerking is mislukt omdat het apparaat niet kan worden gevonden door IoT Hub. Het apparaat is niet geregistreerd of is uitgeschakeld.

## <a name="solution"></a>Oplossing

Registreer de apparaat-ID die u hebt gebruikt en probeer het opnieuw.