---
title: Gebruik de Azure Portal voor het configureren van bestand uploaden | Microsoft Docs
description: De Azure Portal gebruiken om uw IoT-hub te configureren om het uploaden van bestanden vanaf verbonden apparaten mogelijk te maken. Bevat informatie over het configureren van het Azure Storage-doel account.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: da28bfa31c74ff33a200967267500033dd6a9b1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92535872"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Uploaden van IoT Hub-bestanden configureren met behulp van de Azure-portal

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Bestand uploaden

Als u de [functie voor het uploaden van bestanden in IOT hub](iot-hub-devguide-file-upload.md)wilt gebruiken, moet u eerst een Azure Storage-account koppelen aan uw hub. Selecteer **bestand uploaden** om een lijst weer te geven met eigenschappen voor het uploaden van bestanden voor de IOT-hub die wordt gewijzigd.

![Instellingen voor het uploaden van IoT Hub bestanden weer geven in de portal](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Opslag container**: gebruik de Azure Portal om een BLOB-container te selecteren in een Azure Storage-account in uw huidige Azure-abonnement om te koppelen aan uw IOT hub. Als dat nodig is, kunt u een Azure Storage-account maken op de Blade **opslag accounts** en de BLOB-container op de Blade **containers** . IoT Hub genereert automatisch SAS-Uri's met schrijf machtigingen voor deze BLOB-container zodat apparaten kunnen worden gebruikt bij het uploaden van bestanden.

   ![Opslag containers voor het uploaden van bestanden weer geven in de portal](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Meldingen ontvangen voor geüploade bestanden**: Schakel meldingen over het uploaden van bestanden via de wissel knop in of uit.

* **SAS TTL**: deze instelling is de time-to-Live van de SAS-uri's die op het apparaat worden geretourneerd door IOT hub. Is standaard ingesteld op één uur, maar kan worden aangepast aan andere waarden met behulp van de schuif regelaar.

* **Instellingen voor bestands meldingen standaard-TTL**: de time-to-Live van een melding bij het uploaden van een bestand voordat deze is verlopen. Is standaard ingesteld op één dag, maar kan worden aangepast aan andere waarden met behulp van de schuif regelaar.

* **Aantal bestands meldingen maximale leverings** duur: het aantal keren dat de IOT hub een melding over het uploaden van een bestand probeert te leveren. Standaard ingesteld op 10, maar kan worden aangepast aan andere waarden met behulp van de schuif regelaar.

   ![IoT Hub bestand uploaden configureren in de portal](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Volgende stappen

Zie [bestanden uploaden van een apparaat](iot-hub-devguide-file-upload.md) in de ontwikkelaars handleiding voor IOT hub voor meer informatie over de mogelijkheden voor het uploaden van bestanden van IOT hub.

Volg deze koppelingen voor meer informatie over het beheren van Azure IoT Hub:

* [IoT-apparaten bulksgewijs beheren](iot-hub-bulk-identity-mgmt.md)
* [Uw IoT-hub bewaken](monitor-iot-hub.md)

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [Ontwikkelaars handleiding IoT Hub](iot-hub-devguide.md)
* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/quickstart-linux.md)
* [Beveilig uw IoT-oplossing vanaf de grond](../iot-fundamentals/iot-security-ground-up.md)