---
title: TLS 1,0 en 1,1 in IoT Hub afafschaffing | Microsoft Docs
description: Richt lijnen met betrekking tot de afschaffing van TLS 1,0 en 1,1 en ondersteunde code ringen in IoT Hub.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: fcf7620f53c9bfdb51eb62598f2c8b441574eca6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90006077"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Afschaffing van TLS 1,0 en 1,1 in IoT Hub

IoT Hub wordt verplaatst naar Transport Layer Security (TLS) 1,2 als versleutelings methode voor IoT-apparaten en-services om de beste versleuteling te bieden. 

## <a name="timeline"></a>Tijdlijn

IoT Hub wordt TLS 1.0/1.1 blijven ondersteunen tot verdere kennisgeving. We raden echter aan alle klanten zo snel mogelijk naar TLS 1,2 te migreren.

## <a name="deprecating-tls-11-ciphers"></a>TLS 1,1-code ringen afafschaffing

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SH`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="deprecating-tls-10-ciphers"></a>TLS 1,0-code ringen afafschaffing

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="tls-12-cipher-suites"></a>TLS 1,2-coderings suites

Zie [IOT hub TLS 1,2-coderings suites](iot-hub-tls-support.md#cipher-suites).
 
## <a name="customer-feedback"></a>Klantenfeedback

Hoewel de TLS 1,2-afdwinging een in de hele branche geschikte coderings keuze is en wordt ingeschakeld zoals gepland, zullen we graag horen van klanten over hun specifieke implementaties en problemen met het vaststellen van TLS 1,2. U kunt uw opmerkingen voor dit doel verzenden naar [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com) .
