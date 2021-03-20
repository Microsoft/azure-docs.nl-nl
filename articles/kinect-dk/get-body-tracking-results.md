---
title: Resultaten van het bijhouden van hoofd tekst van Azure Kinect
description: Meer informatie over het ophalen van de resultaten van het bijhouden van hoofd tekst met behulp van de Azure Kinect Body tracking SDK.
author: qm13
ms.prod: kinect-dk
ms.author: quentinm
ms.reviewer: yijwan
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, sensor, SDK, hoofd tekst, bijhouden, gewricht
ms.openlocfilehash: 1b62022242144d5db51455a32ac04b67c3e5dd7a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277342"
---
# <a name="get-body-tracking-results"></a>Resultaten van het volgen van een lichaam ophalen

Voor het bijhouden van de hoofd tekst van de SDK wordt een body tracker-object gebruikt voor het verwerken van Azure Kinect DK-vastleg ging en wordt het bijhouden van hoofd Daarnaast blijft de algemene status van de beheer-, verwerkings wachtrijen en de uitvoer wachtrij. Er zijn drie stappen in het gebruik van de hoofd tekst vastleggen:

- Een tracker maken
- Diepte-en IR-installatie kopieën vastleggen met behulp van Azure Kinect DK
- Plaats de opname in de wachtrij en pop de resultaten.

## <a name="create-a-tracker"></a>Een tracker maken


De eerste stap bij het bijhouden van hoofd tekst is het maken van een tracker en vereist het door geven van de sensor kalibratie [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) -structuur. De kalibratie van de sensor kan worden opgevraagd met behulp van de Azure Kinect sensor SDK [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) -functie.

```C
k4a_calibration_t sensor_calibration;
if (K4A_RESULT_SUCCEEDED != k4a_device_get_calibration(device, device_config.depth_mode, K4A_COLOR_RESOLUTION_OFF, &sensor_calibration))
{
    printf("Get depth camera calibration failed!\n");
    return 0;
}

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
if (K4A_RESULT_SUCCEEDED != k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker))
{
    printf("Body tracker initialization failed!\n");
    return 0;
}
```

## <a name="capture-depth-and-ir-images"></a>Diepte-en IR-installatie kopieën vastleggen

Het vastleggen van de installatie kopie met behulp van Azure Kinect DK vindt u op de pagina [afbeeldingen ophalen](retrieve-images.md) .

>[!NOTE]
> `K4A_DEPTH_MODE_NFOV_UNBINNED` of `K4A_DEPTH_MODE_WFOV_2X2BINNED` modi worden aanbevolen voor de beste prestaties en nauw keurigheid. Gebruik niet de `K4A_DEPTH_MODE_OFF` modus of `K4A_DEPTH_MODE_PASSIVE_IR` .

De ondersteunde Azure Kinect DK-modi worden beschreven in de hardware- [specificatie](hardware-specification.md) van Azure Kinect en [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d) .

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

## <a name="enqueue-the-capture-and-pop-the-results"></a>De opname in de wachtrij plaatsen en de resultaten oproepen

De tracker beheert intern een invoerwachtrij en een uitvoerwachtrij om de Azure Kinect DK-opnamen efficiënter asynchroon te verwerken. Gebruik de functie [k4abt_tracker_enqueue_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e) om een nieuwe opname toe te voegen aan de invoer wachtrij. Gebruik de functie [k4abt_tracker_pop_result ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b) uit de uitvoer wachtrij als resultaat. Het gebruik van de time-outwaarde is afhankelijk van de toepassing en regelt de wacht tijd voor de wachtrij.

### <a name="real-time-processing"></a>Real-time verwerking
Gebruik dit patroon voor toepassingen met één thread die real-time resultaten nodig hebben en verloren frames kunnen bevatten. Het `simple_3d_viewer` voor beeld bevindt zich in [github Azure-Kinect-samples](https://github.com/microsoft/Azure-Kinect-Samples) is een voor beeld van real-time verwerking.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, 0);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, 0);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

### <a name="synchronous-processing"></a>Synchrone verwerking
Gebruik dit patroon voor toepassingen die geen real-time resultaten hebben of die geen gedropte frames kunnen bevatten.

De verwerkings doorvoer kan beperkt zijn.

Het `simple_sample.exe` voor beeld bevindt zich in [github Azure-Kinect-samples](https://github.com/microsoft/Azure-Kinect-Samples) is een voor beelden van synchrone verwerking.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Popping body tracking result failed!\n");
    break;
}
// Successfully popped the body tracking result. Start your processing
...

k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Gegevens in kader openen](access-data-body-frame.md)
