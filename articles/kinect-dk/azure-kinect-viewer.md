---
title: Azure Kinect viewer
description: Meer informatie over het visualiseren van alle gegevens stromen van apparaten met behulp van de Azure Kinect-viewer.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, kinect, sensor, viewer, visualisatie, diepte, RGB, kleur, IMU, audio, microfoon, punt-Cloud
ms.openlocfilehash: f411de5854d516586dc64701cca729c692ce40e0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92166102"
---
# <a name="azure-kinect-viewer"></a>Azure Kinect viewer

De Azure Kinect-viewer, gevonden in de map geïnstalleerde hulpprogram ma's als `k4aviewer.exe` (bijvoorbeeld `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe` , waarbij `X.Y.Z` de geïnstalleerde versie van de SDK is), kan worden gebruikt voor het visualiseren van alle gegevens stromen van apparaten naar:

* Controleer of Sens oren goed werken.
* Help het plaatsen van het apparaat.
* Experimenteer met camera-instellingen.
* Apparaatconfiguratie lezen.
* Opnamen afspelen die zijn gemaakt met de [Azure Kinect-recorder](azure-kinect-recorder.md).

Bekijk [hoe u Azure Kinect-video gebruikt](https://www.microsoft.com/videoplayer/embed/RE3hNwG)voor meer informatie over Azure Kinect viewer.

Azure Kinect Viewer is [open source](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/tools/k4aviewer) en kan worden gebruikt als voor beeld voor het gebruik van de api's.

## <a name="use-viewer"></a>Viewer gebruiken

De Viewer kan in twee modi worden gebruikt: met Live gegevens van de sensor of van opgenomen gegevens ([Azure Kinect-recorder](azure-kinect-recorder.md)).

### <a name="start-application"></a>Toepassing starten

Start de toepassing door uit te voeren `k4aviewer.exe` .

![Scherm opname van de gestarte Viewer-toepassing.](./media/how-to-guides/open-viewer.png)

### <a name="use-the-viewer-with-live-data"></a>De viewer gebruiken met Live gegevens

1. Selecteer in de sectie **apparaat openen** het **serie nummer** van het apparaat dat u wilt openen. Selecteer vervolgens **vernieuwen** als het apparaat ontbreekt.
2. Selecteer de knop **apparaat openen** .
3. Selecteer **starten** om te beginnen met het streamen van gegevens met de standaard instellingen.

### <a name="use-the-viewer-with-recorded-data"></a>De viewer gebruiken met opgenomen gegevens

Ga in het gedeelte **registratie openen** naar het vastgelegde bestand en selecteer het.

## <a name="check-device-firmware-version"></a>Controleer de versie van de firmware van het apparaat

Open de versie van de firmware van het apparaat in het venster Configuratie, zoals wordt weer gegeven in de volgende afbeelding.

![De firmware versie van een apparaat controleren met behulp van viewer](./media/how-to-guides/check-firmware-update.png)

In dit geval wordt de 1.5.63 van de diepte camera bijvoorbeeld uitgevoerd op FW-out.

## <a name="depth-camera"></a>Dieptecamera

In de diepte Camera-Viewer worden twee vensters weer gegeven:

* Een wordt ' *actieve helderheid* ' genoemd. Dit is een grijs waarden afbeelding die een IR-helderheid weergeeft.
* De tweede wordt *dieper* genoemd. Dit heeft een gekleurde weer gave van de diepte gegevens.

Houd de muis aanwijzer op de pixel in het diepte venster om de waarde van de diepte sensor weer te geven, zoals hieronder weer gegeven.

![Diepte weergave](./media/how-to-guides/depth-camera.png)

## <a name="rgb-camera"></a>RGB-camera

De onderstaande afbeelding toont de kleur camera weergave.

![RGB-weer gave](./media/how-to-guides/viewer-rgb-camera.png)

U kunt de instellingen van de RGB-camera beheren vanuit het configuratie venster tijdens het streamen.

![Besturings elementen voor RGB-camera](./media/how-to-guides/rgb-camera-settings.png)

## <a name="inertial-measurement-unit-imu"></a>Inerte meting eenheid (IMU)

Het venster IMU bevat twee onderdelen: een versnellings meter en een gyroscope.

De bovenste helft is de versnellings meter en toont lineaire versnelling in meters/seconde<sup>2</sup>.  Het bevat een versnelling van de ZWAARTE kracht, dus als deze op een vlakke manier van een tabel wordt weer gegeven, is de Z-as waarschijnlijk ongeveer 9,8 m/s<sup>2</sup>.

De onderste helft is het gyroscope gedeelte en toont de rotatie bewegingen in radialen per seconde

![Bewegings sensor weergave](./media/how-to-guides/viewer-mu-settings.png)

## <a name="microphone-input"></a>Microfoon invoer

De microfoon weergave toont een weer gave van het geluid dat is gehoord op elke microfoon. Als er geen geluid is, wordt de grafiek weer gegeven als leeg, anders ziet u een donker blauwe Golf met een licht blauwe Golf vorm die erop staat.

De donker Golf vertegenwoordigt de minimum-en maximum waarden die de microfoon heeft geduurd over dat tijds segment. De lichte golf vertegenwoordigt het wortel gemiddelde van de waarden die worden waargenomen door de microfoon over dat tijds segment.

![Invoer weergave voor microfoon](./media/how-to-guides/microphone-data.png)

## <a name="point-cloud-visualization"></a>Visualisatie van punt-Cloud

Met diep gevisualiseerd in 3D kunt u de afbeelding verplaatsen met behulp van geadviseerde sleutels.

![Depth Point-Cloud](./media/how-to-guides/depth-point-cloud.png)

## <a name="synchronization-control"></a>Synchronisatie beheer

U kunt de viewer gebruiken om het apparaat te configureren als een zelfstandige (standaard), hoofd-of ondergeschikte modus bij het configureren van synchronisatie van meerdere apparaten.
Bij het wijzigen van de configuratie of het invoegen/verwijderen van een synchronisatie kabel selecteert u **vernieuwen** om bij te werken.

![Externe synchronisatie beheer](./media/how-to-guides/sync-control.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Gids voor externe synchronisatie-instellingen](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices)
