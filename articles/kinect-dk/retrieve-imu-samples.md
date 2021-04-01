---
title: Azure Kinect IMU-voor beelden ophalen
description: Meer informatie over het ophalen van Azure Kinect IMU-voor beelden met behulp van de Azure Kinect-SDK.
author: qm13
ms.author: cedmonds
ms.date: 06/26/2019
ms.prod: kinect-dk
ms.topic: conceptual
keywords: kinect, azure, configure, depth, Color, RBG, camera, sensor, SDK, IMU, bewegings sensor, Motion, gyroscope, gyro, acceleratie meter, FPS
ms.openlocfilehash: 649dd5b9de62d43d59e74d53adff1ec7de8dfd32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277290"
---
# <a name="retrieve-azure-kinect-imu-samples"></a>Azure Kinect IMU-voor beelden ophalen

Het Azure Kinect-apparaat biedt toegang tot inerte bewegings eenheden (IMUs), met inbegrip van de typen versnellings meter en gyroscope. Voor toegang tot IMUs-voor beelden moet u eerst uw apparaat openen en configureren en vervolgens IMU-gegevens vastleggen. Zie [apparaat zoeken en openen](find-then-open-device.md)voor meer informatie.

IMU-voor beelden worden gegenereerd met een veel hogere frequentie dan afbeeldingen. Voor beelden worden aan de host gemeld met een lagere snelheid dan die van de steek proef. Als er wordt gewacht op een IMU-voor beeld, worden er vaak meerdere voor beelden tegelijkertijd beschikbaar.

Zie de [Hardware-specificatie](hardware-specification.md) van Azure Kinect DK voor meer informatie over de rapportage frequentie van IMU.

## <a name="configure-and-start-cameras"></a>Camera's configureren en starten

> [!NOTE]
> IMU Sens oren kunnen alleen worden gebruikt wanneer de kleur en/of de diepte camera's worden uitgevoerd. IMU Sens oren kunnen niet alleen werken.

Gebruik [k4a_device_start_cameras ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)om de camera's te starten.

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start cameras\n");
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_start_imu(device))
{
    printf("Failed to start imu\n");
    goto Exit;
}
```

## <a name="access-imu-samples"></a>Voor beelden van toegang tot IMU

 Elk [k4a_imu_sample_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__imu__sample__t.html#details) bevat een versnellings meter en gyroscope die op bijna hetzelfde moment zijn vastgelegd.

U kunt de IMU-voor beelden ophalen op dezelfde thread als u afbeeldingen vastlegt of op afzonderlijke threads.

Als u IMU-voor beelden wilt ophalen zodra deze beschikbaar zijn, kunt u kiezen [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) voor een eigen thread. De API heeft ook voldoende interne Queuing zodat u alleen kunt controleren op voor beelden nadat elke installatie kopie wordt geretourneerd.

Omdat er sprake is van een interne wachtrij met IMU-voor beelden, kunt u het volgende patroon gebruiken zonder gegevens te verwijderen:

1. Wacht op een opname, met een wille keurig frame snelheid.
2. De opname verwerken.
3. Alle IMU-voor beelden in de wachtrij ophalen.
4. Wacht op de volgende opname herhalen.

Als u alle momenteel in de wachtrij geplaatste IMU-voor beelden wilt ophalen, kunt u [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) met een `timeout_in_ms` van de 0-lussen aanroepen totdat de functie wordt geretourneerd `K4A_WAIT_RESULT_TIMEOUT` . `K4A_WAIT_RESULT_TIMEOUT` geeft aan dat er geen voor beelden in de wachtrij staan en dat er geen gearriveerd is in de opgegeven time-out.

## <a name="usage-example"></a>Voor beeld van gebruik

```C
k4a_imu_sample_t imu_sample;

// Capture a imu sample
switch (k4a_device_get_imu_sample(device, &imu_sample, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a imu sample\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a imu sample\n");
    goto Exit;
}

// Access the accelerometer readings
if (imu_sample != NULL)
{
    printf(" | Accelerometer temperature:%.2f x:%.4f y:%.4f z: %.4f\n",
            imu_sample.temperature,
            imu_sample.acc_sample.xyz.x,
            imu_sample.acc_sample.xyz.y,
            imu_sample.acc_sample.xyz.z);
}

```

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u met IMU-voor beelden werkt, kunt u ook
>[!div class="nextstepaction"]
>[Toegang tot invoer gegevens van de microfoon](access-mics.md)
