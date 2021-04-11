---
title: Azure Kinect sensor-SDK downloaden
description: Meer informatie over het downloaden en installeren van de Azure Kinect sensor SDK op Windows en Linux.
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, kinect, SDK, down load update, nieuwste, beschikbaar, installeren
ms.openlocfilehash: 591fcba4c887e298cf667c5d95c19184bc213ffe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102179626"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Azure Kinect sensor-SDK downloaden

Deze pagina bevat de Download koppelingen voor elke versie van de Azure Kinect sensor SDK. Het installatie programma bevat alle benodigde bestanden voor het ontwikkelen van Azure Kinect.

## <a name="azure-kinect-sensor-sdk-contents"></a>Inhoud van Azure Kinect sensor SDK

- Kopteksten en bibliotheken om een toepassing te bouwen met behulp van Azure Kinect DK.
- Herdistribueerbare dll-bestanden die nodig zijn voor toepassingen die gebruikmaken van Azure Kinect DK.
- De [Azure Kinect-Viewer](azure-kinect-viewer.md).
- De [Azure Kinect-recorder](azure-kinect-recorder.md).
- Het [hulp programma Azure Kinect-firmware](azure-kinect-firmware-tool.md).

## <a name="windows-installation-instructions"></a>Windows-installatie-instructies

U vindt [hier](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md)informatie over de installatie van de nieuwste en vorige versies van de Azure Kinect-sensor-SDK en-firmware.

U kunt [hier](https://github.com/microsoft/Azure-Kinect-Sensor-SDK)de bron code vinden.

> [!NOTE]
> Onthoud bij het installeren van de SDK het pad dat u wilt installeren. Bijvoorbeeld ' C:\Program Files\Azure Kinect SDK 1,2 '. U vindt de hulpprogram ma's waarnaar wordt verwezen in artikelen in dit pad.

## <a name="linux-installation-instructions"></a>Linux-installatie-instructies

Momenteel is de enige ondersteunde distributie Ubuntu 18,04. Zie [Deze pagina](https://aka.ms/azurekinectfeedback)als u ondersteuning voor andere distributies wilt aanvragen.

Eerst moet u de [opslag plaats van micro soft-pakket](https://packages.microsoft.com/)configureren. Volg hiervoor de instructies [hier](/windows-server/administration/linux-package-repository-for-microsoft-software).

U kunt nu de benodigde pakketten installeren. Het `k4a-tools` pakket bevat de [Azure Kinect-Viewer](azure-kinect-viewer.md), de [Azure Kinect-recorder](record-sensor-streams-file.md)en het [hulp programma Azure Kinect-firmware](azure-kinect-firmware-tool.md). Als u het pakket wilt installeren, voert u de volgende handelingen uit:

`sudo apt install k4a-tools`
 
Met deze opdracht worden de afhankelijkheids pakketten geïnstalleerd die nodig zijn om de hulpprogram ma's correct te laten werken, met inbegrip van de meest recente versie van `libk4a<major>.<minor>` . U moet udev-regels toevoegen om toegang te krijgen tot Azure Kinect DK zonder de hoofd gebruiker. Zie [Linux-apparaat instellen](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md#linux-device-setup)voor instructies. Als alternatief kunt u toepassingen starten die het apparaat als root gebruiken.

Het `libk4a<major>.<minor>-dev` pakket bevat de kop-en cmake-bestanden voor het samen stellen van uw toepassingen/uitvoer bare bestand `libk4a` .

Het `libk4a<major>.<minor>` pakket bevat de gedeelde objecten die nodig zijn voor het uitvoeren van toepassingen/uitvoer bare bestanden die afhankelijk zijn van `libk4a` .

Voor de basis zelf studies is het `libk4a<major>.<minor>-dev` pakket vereist. Als u het pakket wilt installeren, voert u de volgende handelingen uit:

`sudo apt install libk4a<major>.<minor>-dev` 

Als de opdracht is geslaagd, is de SDK klaar voor gebruik.

Zorg ervoor dat u de overeenkomende versie van `libk4a<major>.<minor>` met behulp van installeert `libk4a<major>.<minor>-dev` . Als u bijvoorbeeld het `libk4a4.1-dev` pakket installeert, installeert u het bijbehorende `libk4a4.1` pakket dat de overeenkomende versie van gedeelde object bestanden bevat. `libk4a`Zie de koppelingen in de volgende sectie voor de meest recente versie van.

## <a name="change-log-and-older-versions"></a>Wijzigingen logboek en oudere versies

U kunt het wijzigingslog bestand voor de Azure Kinect sensor [-SDK vinden](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md).

Als u een oudere versie van de Azure Kinect sensor SDK nodig hebt, kunt u deze [hier](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md)vinden.

## <a name="next-steps"></a>Volgende stappen

[Azure Kinect DK installeren](set-up-azure-kinect-dk.md)
