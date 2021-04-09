---
title: bestand opnemen
description: Include-bestand
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: d9c2aea284a2ab84b5d45fe35a35785adfc88123
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832063"
---
### <a name="publish-the-device-template"></a>De apparaatsjabloon publiceren

Voordat u een apparaat aan de toepassing kunt toevoegen, moet u de apparaatsjabloon publiceren:

1. Selecteer in de apparaatsjabloon **LVA Edge Gateway v2** de optie **Publiceren**.

1. Selecteer op de pagina **Deze apparaatsjabloon publiceren naar de toepassing** de optie **Publiceren**.

**LVA Edge Gateway v2** is nu beschikbaar als apparaattype voor gebruik op de pagina **Apparaten** in de toepassing.

## <a name="migrate-the-gateway-device"></a>Het gatewayapparaat migreren

De bestaande apparaat **gateway-001** maakt gebruik van het apparaatsjabloon **LVA Edge Gateway**. Als u uw nieuwe implementatiemanifest wilt gebruiken, migreert u het apparaat naar het nieuwe apparaatsjabloon:

Om het apparaat **gateway-001** te migreren:

1. Navigeer naar de pagina **Apparaten** en selecteer het apparaat **gateway-001** om het te markeren in de lijst.

1. Selecteer **Migreren**. Als het pictogram **Migreren** niet wordt weergegeven, selecteert u **...** om meer opties weer te geven.

    :::image type="content" source="media/iot-central-video-analytics-part4/migrate-device.png" alt-text="Het gatewayapparaat migreren naar een nieuwe versie":::

1. Selecteer in de lijst in het dialoogvenster **Migreren** **LVA Edge Gateway v2** en vervolgens **Migreren**.

Na enkele seconden is de migratie voltooid. Uw apparaat gebruikt nu het apparaatsjabloon **LVA Edge Gateway v2** met uw aangepaste implementatiemanifest.

Er zijn geen apparaten die gebruikmaken van de oorspronkelijke apparaatsjabloon **LVA Edge Gateway**. Deze apparaatsjabloon verwijderen:

1. Navigeer naar de pagina **Apparaatsjablonen** en selecteer de apparaatsjabloon **LVA Edge Gateway**.

1. Selecteer **Verwijderen** om de apparaatsjabloon te verwijderen.

### <a name="get-the-device-credentials"></a>De apparaatreferenties ophalen

U hebt de referenties nodig waarmee het apparaat verbinding kan maken met uw IoT Central-toepassing. De apparaatreferenties ophalen:

1. Selecteer op de pagina **Apparaten** het apparaat **gateway-001**.

1. Selecteer **Verbinding maken**.

1. Op de pagina **Apparaatverbinding** noteert u in het bestand *scratchpad.txt* het **Id-bereik**, de **Apparaat-id** en de **Primaire sleutel** van het apparaat. U gebruikt deze waarden later.

1. Zorg dat de verbindingsmethode is ingesteld op **Shared Access Signature**.

1. Selecteer **Sluiten**.

