---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "70050416"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Ga als volgt te werk om een beleid voor gedeelde toegang te maken dat machtigingen voor het verbinden en het **REGI ster** van **Services** verleent en een Connection String voor dit beleid krijgt:

1. Selecteer **resource groepen** In het [Azure Portal](https://portal.azure.com). Selecteer de resource groep waar uw hub zich bevindt en selecteer vervolgens uw hub in de lijst met resources.

1. Selecteer in het linkerdeel venster van uw hub het beleid voor **gedeelde toegang**.

1. Selecteer in het bovenste menu boven de lijst met beleids regels **toevoegen**.

1. Voer onder **een gedeeld toegangs beleid toevoegen** een beschrijvende naam in voor het beleid, zoals *serviceAndRegistryRead*. Onder **machtigingen** selecteert u **REGI ster lezen** en **service verbinding** maken en selecteert u **maken**.

    ![Weer geven hoe een nieuw beleid voor gedeelde toegang moet worden toegevoegd](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. Selecteer uw nieuwe beleid in de lijst met beleids regels.

1. Onder **gedeelde toegangs sleutels** selecteert u het Kopieer pictogram voor de **verbindings reeks--primaire sleutel** en slaat u de waarde op.

    ![Het ophalen van de verbindingsreeks](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Zie [toegangs beheer en machtigingen](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)voor meer informatie over het IOT hub van het beleid voor gedeelde toegang en machtigingen.
