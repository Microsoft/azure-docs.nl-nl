---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 1a0502568c1673bcd7f57d3e9bc9c95ed90bbefa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80757003"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

Ga als volgt te werk om een beleid voor gedeelde toegang te maken dat machtigingen voor het **maken van verbinding** en het **REGI ster** van services verleent en om een Connection String voor dit beleid te verkrijgen:

1. Selecteer **resource groepen** In het [Azure Portal](https://portal.azure.com). Selecteer de resource groep waar uw hub zich bevindt en selecteer vervolgens uw hub in de lijst met resources.

1. Selecteer in het linkerdeel venster van uw hub het beleid voor **gedeelde toegang**.

1. Selecteer in het bovenste menu boven de lijst met beleids regels **toevoegen**.

1. Voer onder **een gedeeld toegangs beleid toevoegen** een beschrijvende naam in voor het beleid, zoals *serviceAndRegistryReadWrite*. Selecteer onder **machtigingen** de optie **REGI ster schrijven** en **service CONNECT** en selecteer vervolgens **maken**. (De machtiging lezen in het **REGI ster** wordt automatisch opgenomen wanneer u **register schrijven** selecteert.)

    ![Weer geven hoe een nieuw beleid voor gedeelde toegang moet worden toegevoegd](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. Selecteer uw nieuwe beleid in de lijst met beleids regels.

1. Onder **gedeelde toegangs sleutels** selecteert u het Kopieer pictogram voor de **verbindings reeks--primaire sleutel** en slaat u de waarde op.

    ![Het ophalen van de verbindingsreeks](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

Zie [toegangs beheer en machtigingen](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)voor meer informatie over het IOT hub van het beleid voor gedeelde toegang en machtigingen.
