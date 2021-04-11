---
title: Problemen met Azure IoT Hub-fout 504101 GatewayTimeout
description: Meer informatie over het oplossen van fout 504101 GatewayTimeout
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 5d5962c43a8a3cd97b31ad3fa50bce774e2626cb
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060938"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

In dit artikel worden de oorzaken en oplossingen voor **504101 GatewayTimeout** -fouten beschreven.

## <a name="symptoms"></a>Symptomen

Wanneer u probeert een directe methode aan te roepen van IoT Hub naar een apparaat, mislukt de aanvraag met de fout **504101 GatewayTimeout**.

## <a name="cause"></a>Oorzaak

### <a name="cause-1"></a>Oorzaak 1

Er is een fout opgetreden in IoT Hub en kan niet bevestigen of de directe methode is voltooid voordat er een time-out optreedt.

### <a name="cause-2"></a>Oorzaak 2

Wanneer u een eerdere versie van de Azure IoT C# SDK (<1.19.0) gebruikt, kan de AMQP-koppeling tussen het apparaat en IoT Hub op de achtergrond worden verwijderd vanwege een fout.

## <a name="solution"></a>Oplossing

### <a name="solution-1"></a>Oplossing 1

Geef een nieuwe poging.

### <a name="solution-2"></a>Oplossing 2

Voer een upgrade uit naar de nieuwste versie van de Azure IOT C# SDK.