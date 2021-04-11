---
title: Problemen met Azure percept audio en de module spraak oplossen
description: Tips voor het oplossen van problemen met Azure percept audio en azureearspeechclientmodule
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c4fc7d7564ecd30326fbec832639b2a81d55e6d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605651"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Problemen met de Azure percept-audio en spraak module oplossen

Gebruik de onderstaande richt lijnen voor het oplossen van problemen met toepassings problemen van Voice Assistant.

## <a name="collecting-speech-module-logs"></a>Logboeken met spraak modules verzamelen

Als u deze opdrachten wilt uitvoeren, gaat u naar [de dev kit](./how-to-ssh-into-percept-dk.md) en voert u de opdrachten in bij de prompt van de SSH-client.

Logboeken met spraak modules verzamelen:

```console
sudo iotedge logs azureearspeechclientmodule
```

Als u de uitvoer wilt omleiden naar een txt-bestand voor verdere analyse, gebruikt u de volgende syntaxis:

```console
sudo [command] > [file name].txt
```

Wijzig de machtigingen van het txt-bestand zodat het kan worden gekopieerd:

```console
sudo chmod 666 [file name].txt
```

Nadat de uitvoer naar een txt-bestand is omgeleid, kopieert u het bestand naar uw host-PC via SCP:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[pad naar lokale hostbestand] verwijst naar de locatie op de host-PC waarnaar u het txt-bestand wilt kopiëren. [externe gebruikers naam] is de SSH-gebruikers naam die u hebt gekozen tijdens de [installatie-ervaring](./quickstart-percept-dk-set-up.md).

## <a name="checking-runtime-status-of-the-speech-module"></a>De runtime status van de spraak module controleren

Controleer of de runtime status van **azureearspeechclientmodule** wordt weer gegeven als **actief**. Als u de runtime status van uw apparaat modules wilt vinden, opent u de [Azure Portal](https://portal.azure.com/) en navigeert u naar **alle resources**  ->  **[uw IOT hub]**  ->  **IOT Edge**  ->  **[uw apparaat-id]**. Klik op het tabblad **modules** om de runtime status van alle geïnstalleerde modules weer te geven.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="De pagina rand apparaat in de Azure Portal.":::

Als de runtime status van **azureearspeechclientmodule** niet wordt weer gegeven als **actief**, klikt u op **set modules**  ->  **azureearspeechclientmodule**. Stel op de pagina **module-instellingen** de **gewenste status** in op **actief** en klik op **bijwerken**.

## <a name="understanding-ear-som-led-indicators"></a>Informatie over de LED-Indica tors van het oormerk

U kunt LED-indica toren gebruiken om te begrijpen in welke staat het apparaat zich bevindt. Doorgaans duurt het ongeveer twee minuten voordat de module volledig kan worden geïnitialiseerd nadat het apparaat is ingeschakeld. Tijdens de initialisatie stappen ziet u het volgende:

1. Gecentreerd, Wit LED on (statisch): het apparaat is ingeschakeld.
2. Midden-witte LED (knipperend): de verificatie wordt uitgevoerd.
3. Alle drie de Led's worden gewijzigd in blauw zodra het apparaat is geverifieerd en gereed is voor gebruik.

|GELEID|LED-status|De SoM-status van het oormerk|
|---|---------|--------------|
|L02|1x-wit, statisch op|Inschakelen |
|L02|1x-wit, 0,5 Hz flashen|Verificatie wordt uitgevoerd |
|L01 & L02 & L03|3x blauw, statisch op|Wachten op sleutel woord|
|L01 & L02 & L03|LED voor matrix flitsen, 20fps |Luis teren of spreken|
|L01 & L02 & L03|LED array Racing, 20fps|Gedachten|
|L01 & L02 & L03|3x rood, statisch op |Dempen|

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [algemene hand leiding voor probleem oplossing](./troubleshoot-dev-kit.md) voor meer informatie over het oplossen van problemen met Azure percept DK.
