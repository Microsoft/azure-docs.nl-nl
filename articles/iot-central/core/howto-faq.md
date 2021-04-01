---
title: Veelgestelde vragen over Azure IoT Central | Microsoft Docs
description: Veelgestelde vragen over Azure IoT Central (FAQ) en antwoorden
author: dominicbetts
ms.author: dobett
ms.date: 12/20/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: acabaf843f8acfe7bc0b5e9456dee09bde74bef7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97796037"
---
# <a name="frequently-asked-questions-for-iot-central"></a>Veelgestelde vragen over IoT Central

**Hoe kan ik controleren op referentie problemen als een apparaat geen verbinding met mijn IoT Central-toepassing heeft?**

Het [oplossen van problemen met gegevens van uw apparaten wordt niet weer gegeven in Azure IOT Central](troubleshoot-connection.md) bevat stappen voor het vaststellen van verbindings problemen voor apparaten.

**Hoe kan ik het bestand een ticket met klant ondersteuning?**

Als u hulp nodig hebt, kunt u een [Azure-ondersteunings ticket](https://portal.azure.com/#create/Microsoft.Support)indienen.

Zie [Azure IOT-ondersteuning en Help-opties](../../iot-fundamentals/iot-support-help.md)voor meer informatie, waaronder andere ondersteunings opties.

**Hoe kan ik de blok kering van een apparaat opheffen?**

Wanneer een apparaat is geblokkeerd, kunnen er geen gegevens worden verzonden naar uw IoT Central-toepassing. Geblokkeerde apparaten hebben de status **geblokkeerd** op de pagina **apparaten** in uw toepassing. Een operator moet het apparaat blok keren voordat het het verzenden van gegevens kan hervatten:

:::image type="content" source="media/howto-faq/blocked.png" alt-text="Scherm afbeelding van geblokkeerd apparaat":::

Wanneer een operator de blok kering van een apparaat opheffen, wordt de status teruggezet naar de vorige waarde, **geregistreerd** of **ingericht**.

**Een apparaat Hoe kan ik goed keuren?**

Als de apparaatstatus wacht op **goed keuring** op de pagina **apparaten** , betekent dit dat de optie **automatisch goed keuren** is uitgeschakeld:

:::image type="content" source="media/howto-faq/auto-approve.png" alt-text="Scherm opname met de optie toepassing met automatisch goed keuren uitgeschakeld.":::

Een operator moet een apparaat expliciet goed keuren voordat er gegevens worden verzonden. Apparaten die niet hand matig zijn geregistreerd op de pagina **apparaten** , maar die zijn verbonden met geldige referenties, hebben de status van het apparaat **wacht op goed keuring**. Opera tors kunnen deze apparaten goed keuren op de pagina **apparaten** met behulp van de knop **goed keuren** :

:::image type="content" source="media/howto-faq/approve-device.png" alt-text="Scherm afbeelding die laat zien hoe een apparaat moet worden goedgekeurd":::

**Hoe kan ik een apparaat aan een apparaatprofiel koppelen?**

Als de apparaatstatus niet is **gekoppeld**, betekent dit dat er voor het apparaat waarmee verbinding wordt gemaakt met IOT Central geen sjabloon voor het apparaat is gekoppeld. Deze situatie treedt doorgaans op in de volgende scenario's:

- Er wordt een set apparaten toegevoegd met behulp van **importeren** op de pagina **apparaten** zonder de sjabloon voor het apparaat op te geven.
- Een apparaat is hand matig geregistreerd op de pagina **apparaten** zonder de sjabloon voor het apparaat op te geven. Het apparaat is vervolgens verbonden met geldige referenties.  

De operator kan een apparaat koppelen aan een sjabloon op de pagina **apparaten** met behulp van de knop **migreren** . Zie [apparaten in uw Azure IOT Central-toepassing beheren > de migratie van apparaten naar een sjabloon](howto-manage-devices.md)voor meer informatie.

**Waar kan ik meer informatie vinden over IoT Hub?**

Azure IoT Central maakt gebruik van Azure IoT Hub als een Cloud gateway die connectiviteit van apparaten mogelijk maakt. IoT Hub maakt het volgende mogelijk:

- Gegevens opname op schaal in de Cloud.
- Apparaatbeheer.
- Connectiviteit van beveiligde apparaten.

Zie voor meer informatie over IoT Hub [Azure IOT hub](../../iot-hub/index.yml).

**Waar kan ik meer informatie vinden over de Device Provisioning Service (DPS)?**

Azure IoT Central gebruikt DPS om apparaten in staat te stellen verbinding te maken met uw toepassing. Zie voor meer informatie over de rol DPS speelt bij het verbinden van apparaten met IoT Central verbinding maken met [Azure IOT Central](concepts-get-connected.md). Zie [apparaten inrichten met Azure IOT hub Device Provisioning Service](../../iot-dps/about-iot-dps.md)voor meer informatie over DPS.