---
title: Azure Kinect-installatie kopie gegevens ophalen
description: Meer informatie over hoe u Azure Kinect-installatie kopie gegevens kunt ophalen met behulp van de Kinect-sensor-SDK.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, ophalen, sensor, camera, SDK, diepte, RGB, afbeeldingen, kleur, vastleg ging, oplossing, buffer
ms.openlocfilehash: fed5c7340d287d9103ba35f0fd3d80c0fff6e3ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "87538911"
---
# <a name="retrieve-azure-kinect-image-data"></a>Azure Kinect-installatie kopie gegevens ophalen

Deze pagina bevat informatie over het ophalen van installatie kopieën van de Azure-Kinect. In dit artikel wordt beschreven hoe u installatie kopieën kunt vastleggen en openen die zijn gecoördineerd tussen de kleur en diepte van het apparaat. Als u toegang wilt krijgen tot installatie kopieën, moet u eerst het apparaat openen en configureren. vervolgens kunt u installatie kopieën vastleggen.
Voordat u een installatie kopie configureert en vastlegt, moet u het [apparaat zoeken en openen](find-then-open-device.md).

U kunt ook verwijzen naar het [voor beeld van SDK-streaming](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/streaming) dat laat zien hoe u de functies in dit artikel gebruikt.

De volgende functies worden behandeld:

- [`k4a_device_start_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
- [`k4a_device_get_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4dac757a33657f4d3dbf1ae8b21c158a.html#ga4dac757a33657f4d3dbf1ae8b21c158a)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_image_get_buffer()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e)
- [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f)
- [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6)
- [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  
## <a name="configure-and-start-the-device"></a>Het apparaat configureren en starten

De twee camera's die beschikbaar zijn op uw Kinect-apparaat ondersteunen meerdere modi, resoluties en uitvoer indelingen. Raadpleeg de [hardwarespecificaties](hardware-specification.md)van de Azure Kinect Development Kit voor een volledige lijst.

De streaming-configuratie wordt ingesteld met behulp van waarden in de [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) structuur.

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;
config.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start device\n");
    goto Exit;
}
```

Zodra de camera's zijn gestart, blijven ze gegevens vastleggen totdat [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8) het apparaat wordt aangeroepen of omdat het is gesloten.

## <a name="stabilization"></a>Stabilization

Bij het opstarten van apparaten met behulp van de functie voor synchronisatie van meerdere apparaten wordt het aanbevolen dit te doen met behulp van een vaste belichtings instelling.
Als er hand matige belichtings is ingesteld, kan het Maxi maal acht opnamen van het apparaat duren voordat afbeeldingen en frame snelheid stabiel zijn. Met automatische belichting kan het Maxi maal 20 vastleggen duren voordat afbeeldingen en frame snelheid stabiel zijn.

## <a name="get-a-capture-from-the-device"></a>Een opname van het apparaat ophalen

Installatie kopieën worden op een gecorreleerde manier vastgelegd van het apparaat. Elke vastgelegde installatie kopie bevat een diepte afbeelding, een IR-afbeelding, een kleuren afbeelding of een combi natie van installatie kopieën.

De API retourneert standaard alleen een Capture zodra alle gevraagde installatie kopieën voor de streaming-modus zijn ontvangen. U kunt de API zo configureren dat gedeeltelijke opnamen alleen worden geretourneerd met diepte-of kleuren afbeeldingen zodra deze beschikbaar zijn door de [`synchronized_images_only`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t_a8208974f05d89fc1362c6a0900bdef4d.html#a8208974f05d89fc1362c6a0900bdef4d) para meter van de te wissen [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) .

```C
// Capture a depth frame
switch (k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a capture\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a capture\n");
    goto Exit;
}
```

Zodra de API een vastleg ging heeft geretourneerd, moet u aanroepen [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6) Wanneer u het vastleg object hebt voltooid.

## <a name="get-an-image-from-the-capture"></a>Een installatie kopie van de opname ophalen

Als u een vastgelegde installatie kopie wilt ophalen, roept u de juiste functie aan voor elk type installatie kopie. Een van de volgende opties:

- [`k4a_capture_get_color_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga683e440b5f22215a2de58d7fa140488c.html#ga683e440b5f22215a2de58d7fa140488c)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_capture_get_ir_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga1531c3fa76a7c174b8f2eab24de91794.html#ga1531c3fa76a7c174b8f2eab24de91794)

U moet een ingang aanroepen die [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f) [`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) door deze functies wordt geretourneerd wanneer u de installatie kopie hebt gebruikt.

## <a name="access-image-buffers"></a>Afbeeldings buffers openen

[`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) heeft veel accessor-functies om eigenschappen van de installatie kopie op te halen.

Gebruik [k4a_image_get_buffer](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e)om toegang te krijgen tot de geheugen buffer van de installatie kopie.

In het volgende voor beeld ziet u hoe u toegang krijgt tot een vastgelegde diepte afbeelding. Dit geldt ook voor andere afbeeldings typen. Zorg er echter voor dat u de variabele afbeeldings type vervangt door het juiste afbeeldings type, zoals IR of kleur.

```C
// Access the depth16 image
k4a_image_t image = k4a_capture_get_depth_image(capture);
if (image != NULL)
{
    printf(" | Depth16 res:%4dx%4d stride:%5d\n",
            k4a_image_get_height_pixels(image),
            k4a_image_get_width_pixels(image),
            k4a_image_get_stride_bytes(image));

    // Release the image
    k4a_image_release(image);
}

// Release the capture
k4a_capture_release(capture);
```

## <a name="next-steps"></a>Volgende stappen

U weet nu hoe u de afbeeldingen van de camera's kunt vastleggen en coördineren tussen de kleur en de diepte, met behulp van uw Azure Kinect-apparaat. U kunt ook het volgende doen:

>[!div class="nextstepaction"]
>[IMU-voorbeelden ophalen](retrieve-imu-samples.md)

>[!div class="nextstepaction"]
>[Toegangs microfoons](access-mics.md)
