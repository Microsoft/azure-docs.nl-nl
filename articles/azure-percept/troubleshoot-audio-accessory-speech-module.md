---
title: Problemen oplossen met Azure percept audio en spraak modules
description: Tips voor het oplossen van problemen met enkele van de meest voorkomende problemen die tijdens de on-boarding-ervaring zijn gevonden
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 1e2ad920afb55066f07430568f976154f6d7cae1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679635"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Problemen met de Azure percept-audio en spraak module oplossen

Gebruik de onderstaande richt lijnen voor het oplossen van problemen met toepassings problemen van Voice Assistant.

## <a name="collecting-speech-module-logs"></a>Logboeken met spraak modules verzamelen

Als u deze opdrachten wilt uitvoeren, [maakt u verbinding met het Azure PERCEPT DK Wi-Fi toegangs punt en maakt u verbinding met de dev kit via SSH](./how-to-ssh-into-percept-dk.md) en voert u de opdrachten in de SSH-terminal in.

```console
 iotedge logs azureearspeechclientmodule
```

Als u de uitvoer naar een txt-bestand wilt omleiden voor verdere analyse, gebruikt u de volgende syntaxis:

```console
[command] > [file name].txt
```

Nadat de uitvoer naar een txt-bestand is omgeleid, kopieert u het bestand naar uw host-PC via SCP:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[pad naar lokale hostbestand] verwijst naar de locatie op de host-PC waarnaar u het txt-bestand wilt kopiëren. [externe gebruikers naam] is de SSH-gebruikers naam die u hebt gekozen tijdens de [installatie-ervaring](./quickstart-percept-dk-set-up.md). Als u geen SSH-aanmelding tijdens het OOBE hebt ingesteld, is de externe gebruikers naam root.

## <a name="checking-runtime-status-of-the-speech-module"></a>De runtime status van de spraak module controleren

Controleer of de runtime status van **azureearspeechclientmodule** wordt weer gegeven als **actief**. Als u de runtime status van de apparaat modules wilt vinden, opent u de [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) en navigeert u naar **alle resources**  ->  **\<your IoT hub>**  ->  **IOT Edge**  ->  **\<your device ID>** . Klik op het tabblad **modules** om de runtime status van alle geïnstalleerde modules weer te geven.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="De pagina rand apparaat in de Azure Portal.":::

Als de runtime status van **azureearspeechclientmodule** niet wordt weer gegeven als **actief**, klikt u op **set modules**  ->  **azureearspeechclientmodule**. Stel op de pagina **module-instellingen** de **gewenste status** in op **actief** en klik op **bijwerken**.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/firmware-desired-status-stopped.png" alt-text="Het scherm modules instellen in de Azure Portal.":::

## <a name="understanding-ear-som-led-indicators"></a>Informatie over de LED-Indica tors van het oormerk

U kunt LED-indica toren gebruiken om te begrijpen in welke staat het apparaat zich bevindt. Doorgaans duurt het ongeveer twee minuten voordat de module volledig kan worden geïnitialiseerd na *inschakeling*. Tijdens de initialisatie stappen ziet u het volgende:

1. 1 links groen licht: het apparaat is ingeschakeld. 
2. 1 links groen licht en LED voor Center-knipperend groene authenticatie wordt uitgevoerd. 
3. Alle drie de Led's worden gewijzigd in blauw zodra het apparaat is geverifieerd en gereed is voor gebruik.

|LED-status                  |De SoM-status van het oormerk            |
|----------------------------|---------------------------|
|1x-groen (LED links)         |inschakelen |
|1x-groen (LED links) <br> 1x knipperend groen (LED voor midden) |verificatie wordt uitgevoerd |
|3x uit                      |initialisatie is voltooid |
|3x blauw                     |klaar voor gebruik |
|3x knipperend blauw            |sleutel woord herkend |
|3x race Blue              |bezig |
|3x rood                      |dempen |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [algemene hand leiding voor probleem oplossing](./troubleshoot-dev-kit.md) voor meer informatie over het oplossen van problemen met Azure percept DK.