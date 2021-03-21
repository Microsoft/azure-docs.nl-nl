---
title: Bekende problemen met Azure Kinect en probleem oplossing
description: Meer informatie over een aantal bekende problemen en tips voor het oplossen van problemen bij het gebruik van de sensor SDK met Azure Kinect DK.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/05/2021
ms.topic: conceptual
keywords: problemen oplossen, bijwerken, bug, kinect, feedback, herstel, logboek registratie, tips
ms.openlocfilehash: 32a86deb0b6ab70e42ae3d659504256baae76202
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654761"
---
# <a name="azure-kinect-known-issues-and-troubleshooting"></a>Bekende problemen met Azure Kinect en probleem oplossing

Deze pagina bevat bekende problemen en tips voor het oplossen van problemen met het gebruik van sensor SDK met Azure Kinect DK. Zie ook [pagina's met product ondersteuning](./index.yml) voor specifieke hardwareproblemen.

## <a name="known-issues"></a>Bekende problemen

- Compatibiliteits problemen met ASMedia USB-hostcontrollers (bijvoorbeeld ASM1142-chipset)
  - Sommige gevallen met het micro soft USB-stuur programma kunnen de blok kering opheffen
  - Veel Pc's hebben ook alternatieve hostcontrollers en het wijzigen van de USB3-poort kan helpen

Raadpleeg [github-problemen](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/issues) voor meer problemen met de sensor-SDK

## <a name="collecting-logs"></a>Logboeken verzamelen

Logboek registratie voor K4A.dll is ingeschakeld via omgevings variabelen. Standaard wordt logboek registratie verzonden naar stdout en worden alleen fouten en kritieke berichten gegenereerd. Deze instellingen kunnen zodanig worden gewijzigd dat logboek registratie naar een bestand wordt verplaatst. De uitgebreidere kan ook worden aangepast als dat nodig is. Hieronder vindt u een voor beeld van het inschakelen van logboek registratie in een bestand met de naam k4a. log en het vastleggen van waarschuwingen en berichten op een hoger niveau.

1. `set K4A_ENABLE_LOG_TO_A_FILE=k4a.log`
2. `set K4A_LOG_LEVEL=w`
3. Het scenario uitvoeren vanaf een opdracht prompt (bijvoorbeeld Start viewer)
4. Navigeer naar k4a. log en bestand delen.

Zie de onderstaande clip uit het header-bestand voor meer informatie.

```console
/**
* environment variables
* K4A_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4A_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4A_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

De logboek registratie van de SDK voor het bijhouden van hoofd tekst K4ABT.dll is vergelijkbaar, maar de gebruikers moeten een andere set met namen van omgevings variabelen wijzigen:

```console
/**
* environment variables
* K4ABT_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4ABT_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4ABT_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

## <a name="device-doesnt-enumerate-in-device-manager"></a>Apparaat wordt niet geïnventariseerd in Apparaatbeheer

- Controleer of de status van het apparaat wordt geactiveerd als het geel knippert dat er een probleem is met de USB-verbinding en dat er niet voldoende stroom wordt ontvangen. De voedings kabel moet worden aangesloten op de meegeleverde voedings adapter. Terwijl de stroom kabel een USB-type heeft aangesloten, is voor het apparaat meer stroom nodig dan een USB-poort van de PC kan leveren. Daarom kunt u deze niet verbinden met een PC-poort of USB-hub.
- Controleer of u een stroom kabel hebt aangesloten en USB3-poort gebruikt voor gegevens.
- Wijzig de USB3-poort voor de gegevens verbinding (aanbevolen voor het sluiten van een USB-poort op moeder bord, bijvoorbeeld op de computer).
- Controleer of de kabel, beschadigde of lagere kwaliteit kabels kunnen leiden tot onbetrouwbare inventarisatie (het apparaat houdt ' knipperend ' in Apparaatbeheer).
- Als u verbonden bent met een laptop en op de accu werkt, kan het de stroom beperken tot de poort.
- Host-PC opnieuw opstarten.
- Als het probleem zich blijft voordoen, is er mogelijk een probleem met de compatibiliteit.
- Als er een fout is opgetreden tijdens het bijwerken van de firmware en het apparaat niet zelf is hersteld, voert u [fabrieks instellingen opnieuw](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)uit.

## <a name="azure-kinect-viewer-fails-to-open"></a>Azure Kinect Viewer kan niet worden geopend

- Controleer eerst of uw apparaat inventariseert in Windows Apparaatbeheer.

    ![Azure Kinect-camera's in Windows Apparaatbeheer](./media/resources/viewer-fails.png)

- Controleer of u een andere toepassing hebt die gebruikmaakt van het apparaat (bijvoorbeeld Windows-camera toepassing). Er kan slechts één toepassing tegelijk toegang krijgen tot het apparaat.
- Controleer het logboek van k4aviewer. err op fout berichten.
- Open de Windows-camera toepassing en controleer of deze werkt.
- Energie cyclus apparaat, wachten op streamen om uit te scha kelen voordat het apparaat wordt gebruikt.
- Host-PC opnieuw opstarten.
- Zorg ervoor dat u de nieuwste grafische Stuur Programma's op uw PC gebruikt.
- Als u uw eigen build of SDK gebruikt, kunt u een officiële officiële versie gebruiken als het probleem is opgelost.
- Als het probleem zich blijft voordoen, kunt u [Logboeken](troubleshooting.md#collecting-logs) en feedback over het bestand verzamelen.

## <a name="cannot-find-microphone"></a>Kan microfoon niet vinden

- Controleer eerst of de microfoon matrix is geïnventariseerd in Apparaatbeheer.
- Als een apparaat wordt geïnventariseerd en op een andere manier werkt in Windows, is het probleem mogelijk dat er na het bijwerken van de firmware een andere container-ID is toegewezen aan de diepte camera.
- U kunt deze opnieuw instellen door naar Apparaatbeheer te gaan, met de rechter muisknop op ' Azure Kinect microphone array ' te klikken en apparaat verwijderen te selecteren. Als dat is voltooid, ontkoppelt u de sensor en koppelt u deze opnieuw.

    ![Azure Kinect-Mic-matrix](./media/resources/mic-not-found.png)

- Nadat de Azure Kinect-Viewer opnieuw is gestart en probeer het opnieuw.

## <a name="device-firmware-update-issues"></a>Update problemen voor de firmware van het apparaat

- Als het juiste versie nummer na de update niet wordt gerapporteerd, moet u het apparaat mogelijk uitschakelen.
- Als de firmware-update wordt onderbroken, kan de status ervan beschadigd raken en kan deze niet worden geïnventariseerd. Ontkoppel en koppel het apparaat opnieuw en wacht 60 seconden om te zien of het kan worden hersteld.
Als dat niet het geval is, moet u de [fabrieks instellingen terugzetten](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)

## <a name="image-quality-issues"></a>Problemen met de beeld kwaliteit

- Start [Azure Kinect Viewer](azure-kinect-viewer.md) en controleer de plaatsing van het apparaat voor interferentie of als de sensor is geblokkeerd of als de lens is beschadigd.
- Probeer verschillende bewerkings modi om te beperken als er een probleem optreedt in een specifieke modus.
- Voor het delen van afbeeldings kwaliteits problemen met het team kunt u het volgende doen:

1) De weer gave onderbreken in [Azure Kinect Viewer](azure-kinect-viewer.md) en een scherm opname maken of
2) Neem bijvoorbeeld een opname met [Azure Kinect-recorder](azure-kinect-recorder.md). `k4arecorder.exe -l 5 -r 5 output.mkv`

## <a name="inconsistent-or-unexpected-device-timestamps"></a>Inconsistente of onverwachte tijds tempels van het apparaat

Met aanroepen ```k4a_device_set_color_control``` kan de timing van het apparaat tijdelijk worden gewijzigd. Dit kan enkele opnamen duren. Vermijd het aanroepen van de API in de lus voor het vastleggen van afbeeldingen om te voor komen dat u de interne timing berekening opnieuw instelt met elke nieuwe afbeelding. Roep in plaats daarvan de API aan voordat u de camera start of alleen wanneer dit nodig is om de waarde in de lus voor het vastleggen van de installatie kopie te wijzigen. Met name voor komen dat kan worden aangeroepen ```k4a_device_set_color_control(K4A_COLOR_CONTROL_AUTO_EXPOSURE_PRIORITY)``` .

## <a name="usb3-host-controller-compatibility"></a>Compatibiliteit met USB3-hostcontroller

Als het apparaat niet wordt geïnventariseerd onder Apparaatbeheer, kan het zijn dat het is aangesloten op een niet-ondersteunde USB3-controller. 

Voor Azure Kinect DK op **Windows zijn Intel**, **Texas Instruments (TI)** en **Renesas** de *enige host-controllers die worden ondersteund*. De Azure Kinect SDK op Windows-platforms is afhankelijk van een uniforme container-ID en moet USB 2,0-en 3,0-apparaten omvatten zodat de SDK de diepte-, kleur-en audio apparaten kan vinden die zich fysiek op hetzelfde apparaat bevinden. In Linux kunnen meer hostgroepen worden ondersteund, omdat dat platform minder afhankelijk is van de container-ID en meer op serie nummers van apparaten. 

Het onderwerp van USB-hostcontrollers wordt nog gecompliceerder wanneer op een PC meer dan één host controller is geïnstalleerd. Wanneer host-controllers worden gemengd, kan een gebruiker problemen ondervinden waarbij sommige poorten goed en ander niet werken. Afhankelijk van de manier waarop de poorten aan de aanvraag worden gekoppeld, ziet u mogelijk alle poorten met het Azure-Kinect

**Windows:** Als u wilt weten welke host-controller u hebt geopend Apparaatbeheer

1. > apparaten weer geven op type 
2. Met Azure Kinect Connected cameras Select camera's->Azure Kinect 4.000-camera
3. > apparaten weer geven op verbinding

![Problemen met USB-poort oplossen](./media/resources/usb-troubleshooting.png)

Als u beter wilt begrijpen welke USB-poort op uw PC is aangesloten, herhaalt u deze stappen voor elke USB-poort wanneer u Azure Kinect DK verbindt met andere USB-poorten op de PC.

## <a name="depth-camera-auto-powers-down"></a>Automatische uitschakeling van diepte van camera

De laser die wordt gebruikt door de diepte camera voor het berekenen van diepte gegevens van afbeeldingen, heeft een beperkte levens duur. Om de levens duur van de lasers te maximaliseren, detecteert de diepte camera dat er geen gedetailleerde gegevens worden verbruikt. De diepte van de camera wordt uitgeschakeld wanneer het apparaat gedurende enkele minuten wordt gestreamd, maar de host-PC leest de gegevens niet. Het is ook van invloed op de synchronisatie van meerdere apparaten waarbij ondergeschikte apparaten worden gestart in een status waarbij de diepte camera streamt en diepte frames actief kunnen wachten op het moment dat de gegevens worden gesynchroniseerd met het Master-apparaat. Als u dit probleem wilt voor komen in het vastleggen van meerdere apparaten, zorgt u ervoor dat het hoofd apparaat wordt gestart binnen een minuut van de eerste ondergeschikte die wordt gestart. 

## <a name="using-body-tracking-sdk-with-unreal"></a>De hoofd tracking-SDK gebruiken met Unreal

Als u de Body tracking SDK wilt gebruiken met Unreal, moet u ervoor zorgen dat u hebt toegevoegd `<SDK Installation Path>\tools` aan de omgevings variabele `PATH` en is gekopieerd `dnn_model_2_0.onnx` en `cudnn64_7.dll` naar `Program Files/Epic Games/UE_4.23/Engine/Binaries/Win64` .

## <a name="using-azure-kinect-on-headless-linux-system"></a>Azure Kinect gebruiken op het systeem met headless Linux

De Azure Kinect depth-engine op Linux maakt gebruik van OpenGL. Voor OpenGL is een venster instantie vereist waarvoor een monitor moet worden verbonden met het systeem. Een tijdelijke oplossing voor dit probleem is:

1. Schakel automatische aanmelding in voor het gebruikers account dat u wilt gebruiken. Raadpleeg dit artikel voor instructies over [het](https://vitux.com/how-to-enable-disable-automatic-login-in-ubuntu-18-04-lts/) inschakelen van automatische aanmelding.
2. Schakel het systeem uit, koppel de monitor los en schakel het systeem uit. Met automatische aanmelding wordt het maken van een x-Server sessie afgedwongen.
3. Verbinding maken via SSH en de variabele weergave env instellen `export DISPLAY=:0`
4. Start uw Azure Kinect-toepassing.

Het hulp programma [xtrlock](http://manpages.ubuntu.com/manpages/xenial/man1/xtrlock.1x.html) kan worden gebruikt om het scherm direct na automatische aanmelding te vergren delen. Voeg de volgende opdracht toe aan de opstart toepassing of de systeemwerkte service:

`bash -c “xtrlock -b”`

## <a name="missing-c-documentation"></a>Ontbrekende C#-documentatie

De documentatie van de sensor SDK C# vindt u [hier](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/namespace_microsoft_1_1_azure_1_1_kinect_1_1_sensor.html).

De documentatie voor het bijhouden van de hoofd tekst van SDK bevindt zich [hier](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/namespace_microsoft_1_1_azure_1_1_kinect_1_1_body_tracking.html).

## <a name="specifying-onnx-runtime-execution-environment"></a>Uitvoerings omgeving van ONNX-runtime opgeven

De Body tracking SDK ondersteunt CPU, CUDA, DirectML (alleen Windows) en TensorRT Execution Environment om het model voor de geraamde schatting te decoderen. De `K4ABT_TRACKER_PROCESSING_MODE_GPU` standaard instellingen voor het uitvoeren van CUDA op Linux en DirectML worden uitgevoerd in Windows. Er zijn drie extra modi toegevoegd voor het selecteren van specifieke uitvoerings omgevingen: `K4ABT_TRACKER_PROCESSING_MODE_GPU_CUDA` , `K4ABT_TRACKER_PROCESSING_MODE_GPU_DIRECTML` en `K4ABT_TRACKER_PROCESSING_MODE_GPU_TENSORRT` .

ONNX runtime bevat omgevings variabelen voor het beheren van TensorRT-model cache. De aanbevolen waarden zijn:
- ORT_TENSORRT_ENGINE_CACHE_ENABLE = 1 
- ORT_TENSORRT_ENGINE_CACHE_PATH = "padnaam"

De map moet worden gemaakt voordat het bijhouden van de hoofd tekst wordt gestart.

De TensorRT-uitvoerings omgeving ondersteunt zowel FP32 (standaard) als FP16. FP16 Trades ~ 2x prestatie verhoging voor minimale nauw keurigheid. FP16 opgeven:
- ORT_TENSORRT_FP16_ENABLE = 1

## <a name="required-dlls-for-onnx-runtime-execution-environments"></a>Vereiste Dll's voor ONNX runtime execution environments

|Modus      | CUDA 11,1            | CUDNN 8.0.5          | TensorRT 7.2.1       |
|----------|----------------------|----------------------|----------------------|
| CPU      | cudart64_110         | cudnn64_8            | -                    |
|          | cufft64_10           |                      |                      |
|          | cublas64_11          |                      |                      |
|          | cublasLt64_11        |                      |                      |
| CUDA     | cudart64_110         | cudnn64_8            | -                    |
|          | cufft64_10           | cudnn_ops_infer64_8  |                      |
|          | cublas64_11          | cudnn_cnn_infer64_8  |                      |
|          | cublasLt64_11        |                      |                      |
| DirectML | cudart64_110         | cudnn64_8            | -                    |
|          | cufft64_10           |                      |                      |
|          | cublas64_11          |                      |                      |
|          | cublasLt64_11        |                      |                      |
| TensorRT | cudart64_110         | cudnn64_8            | nvinfer              |
|          | cufft64_10           | cudnn_ops_infer64_8  | nvinfer_plugin       |
|          | cublas64_11          | cudnn_cnn_infer64_8  | myelin64_1           |
|          | cublasLt64_11        |                      |                      |
|          | nvrtc64_111_0        |                      |                      |
|          | nvrtc-builtins64_111 |                      |                      |

## <a name="next-steps"></a>Volgende stappen

[Meer ondersteunings informatie](support.md)