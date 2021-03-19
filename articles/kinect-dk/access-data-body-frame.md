---
title: Toegang tot Azure Kinect DK-gegevens in een hoofd frame
description: Meer informatie over de gegevens in een hoofd frame en de functies voor toegang tot de gegevens in azure Kinect DK.
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: how-to
keywords: hoofd tekst, kader, azure, kinect, hoofd tekst, volgen, tips
ms.openlocfilehash: be44f59cb84e99129bf526575293eee69ca64598
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277344"
---
# <a name="access-data-in-body-frame"></a>Gegevens in kader openen

In dit artikel worden de gegevens in een hoofd frame en de functies voor toegang tot deze gegevens beschreven.

De volgende functies worden behandeld:

- [k4abt_frame_get_body_id ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92)
- [k4abt_frame_get_body_index_map ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga0e4f2d0d7e330d444de7070fb1fee4f6.html#ga0e4f2d0d7e330d444de7070fb1fee4f6)
- [k4abt_frame_get_body_skeleton ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gac7032ab06268253538556750775064fb.html#gac7032ab06268253538556750775064fb)
- [k4abt_frame_get_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gad9eef11f6496bbfe997536c374217d9a.html#gad9eef11f6496bbfe997536c374217d9a)
- [k4abt_frame_get_num_bodies ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga29ab088b1a0d1a246bdb5542e21aa3c3.html#ga29ab088b1a0d1a246bdb5542e21aa3c3)
- [k4abt_frame_get_device_timestamp_usec ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga04be7b814b40296cd6b97044ed7283e4.html#ga04be7b814b40296cd6b97044ed7283e4)

## <a name="key-components-of-a-body-frame"></a>Belangrijkste onderdelen van een hoofd frame

Elk hoofd frame bevat een verzameling lichaams structs, een index toewijzing van een 2D-hoofd tekst en de invoer vastleggen waarmee dit resultaat wordt gegenereerd.

![Hoofd frame onderdelen](./media/how-to-guides/body-frame.png)

## <a name="access-the-collection-of-body-structs"></a>Toegang tot de verzameling lichaams structs

Er kunnen in één opname meerdere instanties worden gedetecteerd. U kunt het aantal instanties opvragen door de functie [k4abt_frame_get_num_bodies ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga29ab088b1a0d1a246bdb5542e21aa3c3.html#ga29ab088b1a0d1a246bdb5542e21aa3c3) aan te roepen.

```C
size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
```

U gebruikt de functies [k4abt_frame_get_body_id ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92) en [k4abt_frame_get_body_skeleton ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gac7032ab06268253538556750775064fb.html#gac7032ab06268253538556750775064fb) om de index van de hoofd tekst te herhalen om de tekst-id en de gezamenlijke positie/richtings informatie te vinden.

```C
for (size_t i = 0; i < num_bodies; i++)
{
    k4abt_skeleton_t skeleton;
    k4abt_frame_get_body_skeleton(body_frame, i, &skeleton);
    uint32_t id = k4abt_frame_get_body_id(body_frame, i);
}
```

## <a name="access-the-body-index-map"></a>De index toewijzing van de hoofd tekst openen

U gebruikt de functie [k4abt_frame_get_body_index_map ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga0e4f2d0d7e330d444de7070fb1fee4f6.html#ga0e4f2d0d7e330d444de7070fb1fee4f6) voor toegang tot de Body-index toewijzing. Raadpleeg de [Body-index toewijzing](body-index-map.md) voor een gedetailleerde uitleg van de Body-index toewijzing. Zorg ervoor dat de Body-index toewijzing wordt vrijgegeven wanneer deze niet meer nodig is.

```C
k4a_image_t body_index_map = k4abt_frame_get_body_index_map(body_frame);
...  // Do your work with the body index map
k4a_image_release(body_index_map);
```

## <a name="access-the-input-capture"></a>Toegang tot de invoer vastleggen

De hoofd tekst van de logboeken is een asynchrone API. De oorspronkelijke vastleg ging is mogelijk al vrijgegeven op het moment dat het resultaat wordt geïntroduceerd. Gebruik de functie [k4abt_frame_get_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gad9eef11f6496bbfe997536c374217d9a.html#gad9eef11f6496bbfe997536c374217d9a) om een query uit te voeren op de invoer opname die is gebruikt voor het genereren van dit resultaat voor het volgen van deze hoofdtekst De referentie telling voor de k4a_capture_t wordt verhoogd telkens wanneer deze functie wordt aangeroepen. Gebruik de functie [k4a_capture_release ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6) wanneer de opname niet meer nodig is.

```C
k4a_capture_t input_capture = k4abt_frame_get_capture(body_frame);
... // Do your work with the input capture
k4a_capture_release(input_capture);
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[De Body Tracking-SDK van Azure Kinect](https://microsoft.github.io/Azure-Kinect-Body-Tracking/)
