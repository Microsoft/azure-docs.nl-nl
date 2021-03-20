---
title: bestand opnemen
description: bestand opnemen
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: ebc23ce4238c736442fbc4507e858876f9192fd9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76020927"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Een Azure Storage-account koppelen aan IoT Hub

Omdat de app gesimuleerde apparaten een bestand uploadt naar een blob, moet u een [Azure Storage](../articles/storage/common/storage-account-create.md) -account hebben dat is gekoppeld aan uw IOT-hub. Wanneer u een Azure Storage-account koppelt aan een IoT-hub, genereert de IoT-hub een SAS-URI. Een apparaat kan deze SAS-URI gebruiken om een bestand veilig te uploaden naar een BLOB-container. De IoT Hub-service en de Sdk's van het apparaat coördineren het proces dat de SAS-URI genereert en maakt het beschikbaar voor een apparaat dat wordt gebruikt om een bestand te uploaden.

Volg de instructies in [uploads van bestanden configureren met behulp van de Azure Portal](../articles/iot-hub/iot-hub-configure-file-upload.md). Zorg ervoor dat een BLOB-container is gekoppeld aan uw IoT-hub en dat bestands meldingen worden ingeschakeld.

![Bestands meldingen inschakelen in de portal](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
