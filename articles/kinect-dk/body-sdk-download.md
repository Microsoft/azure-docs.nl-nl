---
title: Azure Kinect Body tracking SDK downloaden
description: Meer informatie over het downloaden van elke versie van de Azure Kinect sensor SDK op Windows of Linux.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/18/2021
ms.topic: conceptual
keywords: Azure, kinect, SDK, update downloaden, nieuwste, beschikbaar, installeren, hoofd tekst, bijhouden
ms.openlocfilehash: 60018df56433785f3cb723dd54cc96a8e5289b67
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654489"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>De Azure Kinect Body tracking-SDK downloaden

Dit document bevat koppelingen voor het installeren van elke versie van de Azure Kinect Body tracking-SDK.

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Inhoud bijhouden SDK Azure Kinect Body

- Kopteksten en bibliotheken om een toepassing voor het bijhouden van hoofd tekst te maken met behulp van Azure Kinect DK.
- Herdistribueerbare dll-bestanden die nodig zijn voor het bijhouden van hoofd toepassingen met behulp van Azure Kinect DK.
- Voorbeeld toepassingen voor het bijhouden van hoofd tekst.

## <a name="windows-download-links"></a>Koppelingen voor het downloaden van Windows

Versie       | Downloaden
--------------|----------
1.1.0 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=102901)
1.0.1 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100942) - [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100848) - [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)

## <a name="linux-installation-instructions"></a>Linux-installatie-instructies

Momenteel is de enige ondersteunde distributie Ubuntu 18,04 en 20,04. Zie [Deze pagina](https://aka.ms/azurekinectfeedback)als u ondersteuning voor andere distributies wilt aanvragen.

Eerst moet u de [opslag plaats van micro soft-pakket](https://packages.microsoft.com/)configureren. Volg hiervoor de instructies [hier](/windows-server/administration/linux-package-repository-for-microsoft-software).

Het `libk4abt<major>.<minor>-dev` pakket bevat de kop-en cmake-bestanden die moeten worden gebouwd `libk4abt` .
Het `libk4abt<major>.<minor>` pakket bevat de gedeelde objecten die nodig zijn voor het uitvoeren van uitvoer bare bestanden die afhankelijk zijn van `libk4abt` en de voorbeeld viewer.

Voor de basis zelf studies is het `libk4abt<major>.<minor>-dev` pakket vereist. Voer uit om de installatie uit te voeren.

`sudo apt install libk4abt<major>.<minor>-dev`

Als de opdracht is geslaagd, is de SDK klaar voor gebruik.

> [!NOTE]
> Onthoud bij het installeren van de SDK het pad dat u wilt installeren. Bijvoorbeeld: "C:\Program Files\Azure Kinect Body tracking SDK 1.0.0". U vindt de voor beelden waarnaar in de artikelen in dit pad wordt verwezen.
> Voor beelden van het bijhouden van hoofd tekst bevinden zich in de map [Body-tracking-samples](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) in de opslag plaats Azure-Kinect-samples. U vindt hier de voor beelden waarnaar in artikelen wordt verwezen.

## <a name="change-log"></a>Wijzigingenlogboek

### <a name="v110"></a>v 1.1.0
* Hulp Voeg ondersteuning toe voor DirectML (alleen Windows) en TensorRT-uitvoering van een model voor het schatten van poses. Zie de veelgestelde vragen over nieuwe uitvoerings omgevingen.
* Hulp Toevoegen `model_path` aan `k4abt_tracker_configuration_t` struct. Hiermee kunnen gebruikers de padnaam voor het model voor het schatten van poses opgeven. Het standaard `dnn_model_2_0_op11.onnx` model voor geraamde modellen bevindt zich in de huidige map.
* Hulp Een `dnn_model_2_0_lite_op11.onnx` schattings model voor de Lite-pose toevoegen. Dit model handelt ~ 2x prestatie verhoging voor een nauw keurigheid van ~ 5%.
* Hulp Geverifieerde voor beelden worden gecompileerd met Visual Studio 2019 en update-voor beelden om deze release te gebruiken.
* [Laatste wijziging] Werk bij naar ONNX runtime 1,6 met ondersteuning voor CPU, CUDA 11,1, DirectML (alleen Windows) en TensorRT 7.2.1 execution environments. Vereist een NVIDIA-stuur programma-update voor R455 of hoger.
* [Laatste wijziging] Geen NuGet-installatie.
* [Fout oplossing] Ondersteuning toevoegen voor NVIDIA RTX 30xx Series Gpu's- [koppeling](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481)
* [Fout oplossing] Voeg ondersteuning toe voor de [koppeling](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481) AMD en Intel Integrated gpu's (alleen Windows)
* [Fout oplossing] Bijwerken naar CUDA 11,1- [koppeling](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1125)
* [Fout oplossing] Update to sensor SDK 1.4.1 [link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1248)

### <a name="v101"></a>v 1.0.1
* [Fout oplossing] Probleem oplossen dat de SDK vastloopt bij het laden van onnxruntime.dll van pad op Windows Build 19025 of hoger: [link](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v 1.0.0
* Hulp Voeg C#-wrapper toe aan het MSI-installatie programma.
* [Fout oplossing] Probleem oplossen dat de hoofd draaiing niet correct kan worden gedetecteerd: [koppeling](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Fout oplossing] Probleem oplossen dat het CPU-gebruik tot 100% op Linux-machine aanloopt: [koppeling](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* Voor beelden Voeg twee steek proeven toe aan het voor beeld-opslag plaats. Voor beeld 1 ziet u hoe u de resultaten van het bijhouden van hoofd tekst kunt transformeren van de diepte ruimte naar de [koppeling](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample)naar de kleur ruimte. voor beeld 2 ziet u hoe u een [koppeling](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample) naar het vloer vlak detecteert

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Kinect DK](about-azure-kinect-dk.md)

- [Azure Kinect DK installeren](set-up-azure-kinect-dk.md)

- [Bijhouden van de hoofd tekst van Azure Kinect instellen](body-sdk-setup.md)