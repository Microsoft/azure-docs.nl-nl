---
title: Azure Kinect Play-API
description: Meer informatie over hoe u de Azure Kinect sensor SDK gebruikt om een opname bestand te openen met behulp van de API voor afspelen.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, sensor, SDK, depth, RGB, record, afspelen, Matroska, MKV
ms.openlocfilehash: fe403f314c1df415537d090433f34627eb1249e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277270"
---
# <a name="the-azure-kinect-playback-api"></a>De Azure Kinect Play-API

De sensor-SDK biedt een API voor het vastleggen van apparaatgegevens in een Matroska-bestand (. MKV). De Matroska-container indeling bevat video tracks, IMU-voor beelden en kalibratie van het apparaat. U kunt opnames genereren met behulp van het geleverde [k4arecorder](record-sensor-streams-file.md) -opdracht regel programma. Opnamen kunnen ook rechtstreeks worden aangepast en opgenomen met de record-API.

Zie voor meer informatie over de API voor vastleggen [`k4a_record_create()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gae14f4181e9688e710d1c80b215413831.html#gae14f4181e9688e710d1c80b215413831) .

Zie de pagina [opname bestands indeling](record-file-format.md) voor meer informatie over de Matroska.

## <a name="use-the-playback-api"></a>De API voor afspelen gebruiken

Opname bestanden kunnen worden geopend met behulp van de API voor afspelen. De API voor afspelen biedt toegang tot sensor gegevens in dezelfde indeling als de rest van de sensor-SDK.

### <a name="open-a-record-file"></a>Een record bestand openen

In het volgende voor beeld openen we een opname met [`k4a_playback_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gacb254ac941b2ab3c202ca68f4537f368.html#gacb254ac941b2ab3c202ca68f4537f368) , drukt u de opname lengte af en sluit u het bestand met [`k4a_playback_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga76f415f2076f1c8c544e094a649306ff.html#ga76f415f2076f1c8c544e094a649306ff) .

```C
k4a_playback_t playback_handle = NULL;
if (k4a_playback_open("recording.mkv", &playback_handle) != K4A_RESULT_SUCCEEDED)
{
    printf("Failed to open recording\n");
    return 1;
}

uint64_t recording_length = k4a_playback_get_last_timestamp_usec(playback_handle);
printf("Recording is %lld seconds long\n", recording_length / 1000000);

k4a_playback_close(playback_handle);
```

### <a name="read-captures"></a>Vastleg ging lezen

Zodra het bestand is geopend, kunnen we opnamen vanuit de opname gaan lezen. In het volgende voor beeld worden alle vastleg ging in het bestand gelezen.

```C
k4a_capture_t capture = NULL;
k4a_stream_result_t result = K4A_STREAM_RESULT_SUCCEEDED;
while (result == K4A_STREAM_RESULT_SUCCEEDED)
{
    result = k4a_playback_get_next_capture(playback_handle, &capture);
    if (result == K4A_STREAM_RESULT_SUCCEEDED)
    {
        // Process capture here
        k4a_capture_release(capture);
    }
    else if (result == K4A_STREAM_RESULT_EOF)
    {
        // End of file reached
        break;
    }
}
if (result == K4A_STREAM_RESULT_FAILED)
{
    printf("Failed to read entire recording\n");
    return 1;
}
```

### <a name="seek-within-a-recording"></a>Zoeken binnen een record

Zodra het einde van het bestand is bereikt, kunnen we het opnieuw proberen om het te lezen. Dit proces kan worden uitgevoerd door achterwaarts te lezen met [`k4a_playback_get_previous_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga54732e3aa0717e1ca4eb76ee385e878c.html#ga54732e3aa0717e1ca4eb76ee385e878c) , maar het kan erg traag zijn, afhankelijk van de lengte van de opname.
In plaats daarvan kunnen we de [`k4a_playback_seek_timestamp()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaea748994a121543bd77f90417cf428f6.html#gaea748994a121543bd77f90417cf428f6) functie gebruiken om naar een bepaald punt in het bestand te gaan.

In dit voor beeld geven we tijds tempels in micro seconden op om te zoeken naar verschillende punten in het bestand.

```C
// Seek to the beginning of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to the end of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the start
if (k4a_playback_seek_timestamp(playback_handle, 10 * 1000000, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the end
if (k4a_playback_seek_timestamp(playback_handle, -10 * 1000000, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}
```

### <a name="read-tag-information"></a>Code-informatie lezen

Opnames kunnen ook verschillende meta gegevens bevatten, zoals het serie nummer en de firmware versie van het apparaat. Deze meta gegevens worden opgeslagen in Tags voor opnamen, die kunnen worden geopend met behulp van de [`k4a_playback_get_tag()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga320f966fc89b4ba0d758f787f70d5143.html#ga320f966fc89b4ba0d758f787f70d5143) functie.

```C
// Print the serial number of the device used to record
char serial_number[256];
size_t serial_number_size = 256;
k4a_buffer_result_t buffer_result = k4a_playback_get_tag(playback_handle, "K4A_DEVICE_SERIAL_NUMBER", &serial_number, &serial_number_size);
if (buffer_result == K4A_BUFFER_RESULT_SUCCEEDED)
{
    printf("Device serial number: %s\n", serial_number);
}
else if (buffer_result == K4A_BUFFER_RESULT_TOO_SMALL)
{
    printf("Device serial number too long.\n");
}
else
{
    printf("Tag does not exist. Device serial number was not recorded.\n");
}
```

### <a name="record-tag-list"></a>Lijst met record Tags

Hieronder ziet u een lijst met alle standaard tags die in een opname bestand kunnen worden opgenomen. Veel van deze waarden zijn beschikbaar als onderdeel van de [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) struct en kunnen worden gelezen met de [`k4a_playback_get_record_configuration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaab54a85c1f1e98d170d009042b449255.html#gaab54a85c1f1e98d170d009042b449255) functie.

Als een tag niet bestaat, wordt ervan uitgegaan dat deze de standaard waarde heeft.

| Tagnaam                     | Standaardwaarde      | [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) Aan | Notities     |
|------------------------------|--------------------|--------------------------------------|----------------------------------------------------------------------------------------------------------------|
| `K4A_COLOR_MODE`             | Office              | `color_format` / `color_resolution`  | Mogelijke waarden: ' OFF ', ' MJPG_1080P ', ' NV12_720P ', ' YUY2_720P ' enzovoort                                      |
| `K4A_DEPTH_MODE`             | Office              | `depth_mode` / `depth_track_enabled` | Mogelijke waarden: "OFF," NFOV_UNBINNED "," PASSIVE_IR "enzovoort                                                |
| `K4A_IR_MODE`                | Office              | `depth_mode` / `ir_track_enabled`    | Mogelijke waarden: "OFF", "actief", "PASSIEf"                                                                    |
| `K4A_IMU_MODE`               | Office              | `imu_track_enabled`                  | Mogelijke waarden: aan, uit                                                                                   |
| `K4A_CALIBRATION_FILE`       | "calibration.jsop" | N.v.t.                                  | Zie [`k4a_device_get_raw_calibration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8c4e46642cee3115aeb0b33e2b43b24f.html#ga8c4e46642cee3115aeb0b33e2b43b24f) |
| `K4A_DEPTH_DELAY_NS`         | 0,3                | `depth_delay_off_color_usec`         | De waarde die is opgeslagen in nano seconden, API biedt micro seconden.                                                        |
| `K4A_WIRED_SYNC_MODE`        | AUTONOME       | `wired_sync_mode`                    | Mogelijke waarden: ' STANDALONE ', ' MASTER ', ' slave '                                                         |
| `K4A_SUBORDINATE_DELAY_NS`   | 0,3                | `subordinate_delay_off_master_usec`  | De waarde die is opgeslagen in nano seconden, API biedt micro seconden.                                                        |
| `K4A_COLOR_FIRMWARE_VERSION` | ""                 | N.v.t.                                  | Firmware versie van de apparaat kleur, bijvoorbeeld ' 1. x. xx '                                                            |
| `K4A_DEPTH_FIRMWARE_VERSION` | ""                 | N.v.t.                                  | Versie van de apparaat diepte van de firmware, bijvoorbeeld ' 1. x. xx '                                                            |
| `K4A_DEVICE_SERIAL_NUMBER`   | ""                 | N.v.t.                                  | Serie nummer van het apparaat vastleggen                                                                                 |
| `K4A_START_OFFSET_NS`        | 0,3                | `start_timestamp_offset_usec`        | Zie de onderstaande [tijds tempel synchronisatie](record-playback-api.md#timestamp-synchronization) .                       |
| `K4A_COLOR_TRACK`            | Geen               | N.v.t.                                  | Zie [registratie bestands indeling identificeren sporen](record-file-format.md#identifying-tracks).                     |
| `K4A_DEPTH_TRACK`            | Geen               | N.v.t.                                  | Zie [registratie bestands indeling identificeren sporen](record-file-format.md#identifying-tracks).                     |
| `K4A_IR_TRACK`               | Geen               | N.v.t.                                  | Zie [registratie bestands indeling identificeren sporen](record-file-format.md#identifying-tracks).                     |
| `K4A_IMU_TRACK`              | Geen               | N.v.t.                                  | Zie [registratie bestands indeling identificeren sporen](record-file-format.md#identifying-tracks).                     |

## <a name="timestamp-synchronization"></a>Tijds tempel synchronisatie

De Matroska-indeling vereist dat opnames moeten beginnen met een tijds tempel van nul. Bij het [extern synchroniseren van camera's](record-external-synchronized-units.md), kan de eerste tijds tempel van elk apparaat niet nul zijn.

Als u de oorspronkelijke tijds tempels van de apparaten tussen opnemen en afspelen wilt behouden, slaat het bestand een offset op die moet worden toegepast op de tijds tempels.

De `K4A_START_OFFSET_NS` tag wordt gebruikt om een tijds tempel-offset op te geven, zodat bestanden na het opnemen opnieuw kunnen worden gesynchroniseerd. Deze tijds tempel-offset kan worden toegevoegd aan elke tijds tempel in het bestand om de oorspronkelijke tijds tempels van het apparaat opnieuw te maken.

De start offset is ook beschikbaar in de [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) struct.
