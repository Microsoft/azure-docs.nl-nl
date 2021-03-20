---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95993300"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Voer de volgende stappen uit om de IoT Hub connection string voor het **registryReadWrite** -beleid op te halen:

1. Selecteer **resource groepen** In het [Azure Portal](https://portal.azure.com). Selecteer de resource groep waar uw hub zich bevindt en selecteer vervolgens uw hub in de lijst met resources.

2. Selecteer in het linkerdeel venster van uw hub het beleid voor **gedeelde toegang**.

3. Selecteer in de lijst met beleids regels het **registryReadWrite** -beleid.

4. Onder **gedeelde toegangs sleutels** selecteert u het Kopieer pictogram voor de **verbindings reeks--primaire sleutel** en slaat u de waarde op.

    ![Het ophalen van de verbindingsreeks](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

Zie [toegangs beheer en machtigingen](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)voor meer informatie over het IOT hub van het beleid voor gedeelde toegang en machtigingen.
