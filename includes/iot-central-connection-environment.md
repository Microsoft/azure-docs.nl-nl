---
title: bestand opnemen
description: bestand opnemen
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 11/03/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: a3c85a7c76115ff72e5042788ad83fd31f601c9c
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122680"
---
Stel de omgevingsvariabelen in om het voorbeeld te configureren. Het volgende codefragment laat zien hoe u de omgevingsvariabelen instelt vanaf de Windows-opdrachtprompt. Als u een **bash**-shell gebruikt, vervangt u de `set`-opdrachten door `export`-opdrachten:

```cmd
set IOTHUB_DEVICE_SECURITY_TYPE=DPS
set IOTHUB_DEVICE_DPS_ID_SCOPE=<The ID scope you made a note of previously>
set IOTHUB_DEVICE_DPS_DEVICE_ID=sample-device-01
set IOTHUB_DEVICE_DPS_DEVICE_KEY=<The generated device key you made a note of previously>
set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
```
