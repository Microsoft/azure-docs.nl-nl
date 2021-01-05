---
title: Bestanden van apparaten uploaden naar Azure Storage | Microsoft Docs
description: Het configureren van Bestands-uploads van uw apparaten naar de Cloud. Nadat u het uploaden van bestanden hebt geconfigureerd, implementeert u het uploaden van bestanden op uw apparaten.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: ed2eff4d6ccbb9f85dfaf7049fa3cc18711bae0f
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796901"
---
# <a name="upload-files-from-your-devices-to-the-cloud"></a>Bestanden van uw apparaten uploaden naar de Cloud

*Dit onderwerp is van toepassing op beheerders en ontwikkel aars van apparaten.*

Met IoT Central kunt u media en andere bestanden uploaden van verbonden apparaten naar de Cloud opslag. U configureert de functie voor het uploaden van bestanden in uw IoT Central-toepassing en implementeert vervolgens de uploads van bestanden in uw apparaatcode.

## <a name="prerequisites"></a>Vereisten

U moet een beheerder zijn in uw IoT Central-toepassing om het uploaden van bestanden te kunnen configureren.

U hebt een Azure Storage-account en een container nodig om de geüploade bestanden op te slaan. Als u geen bestaand opslag account en een container wilt gebruiken, maakt u een [Nieuw opslag account in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="configure-device-file-uploads"></a>Uploads van het apparaatbestand configureren

De uploads van een apparaatbestand configureren:

1. Navigeer naar het gedeelte **beheer** in uw toepassing.

1. Selecteer **apparaatbestand uploaden**.

1. Selecteer het opslag account en de container die u wilt gebruiken. Als het opslag account zich in een ander Azure-abonnement dan uw toepassing bevindt, voert u een connection string voor het opslag account in.

1. Indien nodig kunt u de time-out voor uploaden aanpassen waarmee wordt ingesteld hoe lang een upload aanvraag geldig blijft. Geldige waarden zijn 1 tot 24 uur.

1. Selecteer **Opslaan**. Wanneer de status is **geconfigureerd**, bent u klaar om bestanden te uploaden van apparaten.

:::image type="content" source="media/howto-configure-file-uploads/file-upload-configuration.png" alt-text="Het uploaden van bestanden in de toepassing configureren":::

## <a name="disable-device-file-uploads"></a>Uploads van het apparaatbestand uitschakelen

Als u het uploaden van het apparaatbestand naar uw IoT Central-toepassing wilt uitschakelen:

1. Navigeer naar het gedeelte **beheer** in uw toepassing.

1. Selecteer **apparaatbestand uploaden**.

1. Selecteer **Verwijderen**.

## <a name="upload-a-file-from-a-device"></a>Een bestand van een apparaat uploaden

IoT Central gebruikt de bestands upload mogelijkheid van IoT Hub om apparaten in staat te stellen bestanden te uploaden. Zie het voor beeld van een IoT Central voor het [uploaden](/samples/iot-for-all/iotc-file-upload-device/iotc-file-upload-device/)van bestanden voor een voorbeeld code.

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u de uploads van apparaten in IoT Central configureert en implementeert, is een voorgestelde volgende stap meer informatie over het uploaden van een apparaatbestand:

- [Bestanden van uw apparaat uploaden naar de Cloud met IoT Hub (.NET)](../../iot-hub/iot-hub-csharp-csharp-file-upload.md)
- [Bestanden van uw apparaat uploaden naar de Cloud met IoT Hub (Java)](../../iot-hub/iot-hub-java-java-file-upload.md)
- [Bestanden van uw apparaat uploaden naar de Cloud met IoT Hub (Node.js)](../../iot-hub/iot-hub-node-node-file-upload.md)
- [Bestanden van uw apparaat uploaden naar de Cloud met IoT Hub (python)](../../iot-hub/iot-hub-python-python-file-upload.md)