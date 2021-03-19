---
title: Zoek het Azure Kinect-apparaat en open het
description: Meer informatie over het zoeken en openen van een Azure Kinect-apparaat met behulp van de Azure Kinect Senor-SDK.
author: cedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, sensor, SDK, diepte, RGB, apparaat, zoeken, openen
ms.openlocfilehash: 67fc93b924d5d663bb43098969c54d1975bd5895
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277274"
---
# <a name="find-then-open-the-azure-kinect-device"></a>Zoek het Azure Kinect-apparaat en open het

In dit artikel wordt beschreven hoe u de Azure Kinect DK kunt vinden en openen. In dit artikel wordt uitgelegd hoe u kunt afhandelen wanneer er meerdere apparaten zijn aangesloten op uw machine.

U kunt ook verwijzen naar het [voor beeld van de SDK, waarin wordt](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/enumerate) gedemonstreerd hoe u de functies in dit artikel gebruikt.

De volgende functies worden behandeld:
 * [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc)
 * [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
 * [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22)
 * [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)

## <a name="discover-the-number-of-connected-devices"></a>Het aantal verbonden apparaten detecteren

Haal eerst de telling op van de momenteel verbonden Azure Kinect-apparaten met [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc) .

```C
uint32_t device_count = k4a_device_get_installed_count();

printf("Found %d connected devices:\n", device_count);
```

## <a name="open-a-device"></a>Een apparaat openen

Als u informatie wilt ophalen over een apparaat of als u gegevens wilt lezen, moet u eerst een ingang naar het apparaat openen met behulp van [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) .

```C
k4a_device_t device = NULL;

for (uint8_t deviceIndex = 0; deviceIndex < device_count; deviceIndex++)
{
    if (K4A_RESULT_SUCCEEDED != k4a_device_open(deviceIndex, &device))
    {
        printf("%d: Failed to open device\n", deviceIndex);
        continue;
    }

    ...

    k4a_device_close(device);
}
```

De `index` para meter van [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) geeft aan welk apparaat moet worden geopend als er meer dan één verbinding is. Als u slechts één apparaat zou kunnen verbinden, kunt u een argument van `K4A_DEVICE_DEFAULT` of 0 door geven om het eerste apparaat aan te geven.

Telkens wanneer u een apparaat opent, moet u aanroepen [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423) Wanneer u klaar bent met de ingang. Er kunnen geen andere ingangen op hetzelfde apparaat worden geopend totdat u de ingang hebt gesloten.

## <a name="identify-a-specific-device"></a>Een specifiek apparaat identificeren

De opsommen van de order apparaten die op index worden opgesomd, worden pas gewijzigd nadat apparaten zijn gekoppeld of losgekoppeld. Als u een fysiek apparaat wilt identificeren, moet u het serie nummer van het apparaat gebruiken.

Als u het serie nummer van het apparaat wilt lezen, gebruikt [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22) u de functie nadat u een ingang hebt geopend.

In dit voor beeld ziet u hoe u de juiste hoeveelheid geheugen toewijst om het serie nummer op te slaan.

```C
char *serial_number = NULL;
size_t serial_number_length = 0;

if (K4A_BUFFER_RESULT_TOO_SMALL != k4a_device_get_serialnum(device, NULL, &serial_number_length))
{
    printf("%d: Failed to get serial number length\n", deviceIndex);
    k4a_device_close(device);
    device = NULL;
    continue;
}

serial_number = malloc(serial_number_length);
if (serial_number == NULL)
{
    printf("%d: Failed to allocate memory for serial number (%zu bytes)\n", deviceIndex, serial_number_length);
    k4a_device_close(device);
    device = NULL;
    continue;
}

if (K4A_BUFFER_RESULT_SUCCEEDED != k4a_device_get_serialnum(device, serial_number, &serial_number_length))
{
    printf("%d: Failed to get serial number\n", deviceIndex);
    free(serial_number);
    serial_number = NULL;
    k4a_device_close(device);
    device = NULL;
    continue;
}

printf("%d: Device \"%s\"\n", deviceIndex, serial_number);
```

## <a name="open-the-default-device"></a>Het standaard apparaat openen

In de meeste toepassingen is er slechts één Azure Kinect DK aan dezelfde computer gekoppeld. Als u alleen verbinding wilt maken met een enkel verwacht apparaat, kunt u [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) `index` `K4A_DEVICE_DEFAULT` het eerste apparaat aanroepen om het te openen.

```C
k4a_device_t device = NULL;
uint32_t device_count = k4a_device_get_installed_count();

if (device_count != 1)
{
    printf("Unexpected number of devices found (%d)\n", device_count);
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_open(K4A_DEVICE_DEFAULT, &device))
{
    printf("Failed to open device\n");
    goto Exit;
}
```

## <a name="next-steps"></a>Volgende stappen

>[!div class="nextstepaction"]
>[Afbeeldingen ophalen](retrieve-images.md)

>[!div class="nextstepaction"]
>[IMU-voor beelden ophalen](retrieve-imu-samples.md)

