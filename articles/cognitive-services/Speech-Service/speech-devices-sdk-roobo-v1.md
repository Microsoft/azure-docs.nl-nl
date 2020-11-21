---
title: Speech-apparaten SDK roobo Smart audio dev kit v1-Speech Service
titleSuffix: Azure Cognitive Services
description: Vereisten en instructies voor het aan de slag gaan met de SDK voor spraak apparaten, roobo Smart audio dev kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 73eb1225ffc5fd01f9a27ca99ad2b059d45a36cf
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015287"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Apparaat: roobo Smart audio dev kit

Dit artikel bevat apparaatspecifieke informatie voor de roobo Smart audio dev kit.

## <a name="set-up-the-development-kit"></a>De Development Kit instellen

1. De Development Kit heeft twee micro USB-connectors. De linker connector is de Power Development Kit om te zetten en is gemarkeerd als stroom in de onderstaande afbeelding. De juiste is om deze te beheren en is gemarkeerd als debug in de installatie kopie.

    ![De dev kit verbinden](media/speech-devices-sdk/qsg-1.png)

1. Schakel de Development Kit uit met behulp van een micro USB-kabel om de voedings poort te verbinden met een PC of een stroom adapter. Een groene stroom indicator is afhankelijk van het bovenste bord.

1. Als u de Development Kit wilt beheren, verbindt u de poort voor fout opsporing met een computer met een tweede micro USB-kabel. Het is essentieel dat u een kabel van hoge kwaliteit gebruikt om betrouw bare communicatie te garanderen.

1. U kunt uw Development Kit richten op de ronde of lineaire configuratie.

    |Development Kit-configuratie|Afdrukstand|
    |-----------------------------|------------|
    |Uniforme|Rechtop, met microfoons gericht op het plafond|
    |Lineair|Aan de zijkant, met microfoons die op u zijn gericht (weer gegeven in de volgende afbeelding)|

    ![De richting van de lineaire ontwikkel Kit](media/speech-devices-sdk/qsg-2.png)

1. Installeer de certificaten en stel de machtigingen van het geluids apparaat in. Typ de volgende opdrachten in een opdracht prompt venster:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Deze opdrachten maken gebruik van de Android debug Bridge, `adb.exe` die deel uitmaakt van de Android Studio-installatie. Dit hulpprogramma bevindt zich in C:\Users\[gebruikersnaam] \AppData\Local\Android\Sdk\platform-tools. U kunt deze map toevoegen aan uw pad om het gemakkelijker te maken om `adb` aan te roepen. Anders moet u het volledige pad naar uw installatie van adb.exe opgeven in elke opdracht waarin `adb` wordt aangeroepen.
    >
    > Als er een fout optreedt `no devices/emulators found` , controleer dan of de USB-kabel is aangesloten en de kabel van een hoge kwaliteit is. U kunt gebruiken `adb devices` om te controleren of uw computer kan communiceren met de Development Kit, omdat er een lijst met apparaten wordt geretourneerd.
    >
    > [!TIP]
    > Demp de microfoon en spreker van uw PC om er zeker van te zijn dat u werkt met de smartphones van de Development Kit. Op deze manier wordt het apparaat niet per ongeluk geactiveerd op basis van de audio van de PC.

1. Als u een spreker wilt toevoegen aan de dev kit, kunt u deze verbinden met de audio lijn uit. U moet een goede luid spreker met een analoge stekker van 3,5 mm kiezen.

    ![Vysor-audio](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Ontwikkelings informatie

Zie de [roobo Development Guide (Engelstalig](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)) voor meer informatie over ontwikkel aars.

## <a name="audio"></a>Audio

Roobo biedt een hulp programma waarmee alle audio wordt vastgelegd in Flash-geheugen. Het kan handig zijn om geluids problemen op te lossen. Er wordt een versie van het hulp programma gegeven voor elke Development Kit-configuratie. Selecteer uw apparaat op de  [roobo-site](http://ddk.roobo.com/)en selecteer vervolgens de koppeling **roobo extra** onder aan de pagina.

## <a name="next-steps"></a>Volgende stappen

* [De Android-voor beeld-app uitvoeren](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)