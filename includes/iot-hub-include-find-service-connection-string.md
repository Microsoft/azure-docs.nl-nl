---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 55cdd864d73ce084d994c64233e79d5a58b17def
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "69558495"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

Voer de volgende stappen uit om de IoT Hub connection string voor het **service** beleid op te halen:

1. Selecteer **resource groepen** In het [Azure Portal](https://portal.azure.com). Selecteer de resource groep waar uw hub zich bevindt en selecteer vervolgens uw hub in de lijst met resources.

1. Selecteer in het linkerdeel venster van uw IoT-hub het **beleid voor gedeelde toegang**.

1. Selecteer het **service** beleid in de lijst met beleids regels.

1. Onder **gedeelde toegangs sleutels** selecteert u het Kopieer pictogram voor de **verbindings reeks--primaire sleutel** en slaat u de waarde op.

    ![Het ophalen van de verbindingsreeks](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

Zie [toegangs beheer en machtigingen](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)voor meer informatie over het IOT hub van het beleid voor gedeelde toegang en machtigingen.
