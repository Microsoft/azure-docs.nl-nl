---
title: bestand opnemen
description: bestand opnemen
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 11/03/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: e4f9fd537d7743a5bbb9d129b21c4bf0a529d32d
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491073"
---
Wanneer u de toepassing voor het voorbeeldapparaat later in deze zelfstudie uitvoert, hebt u de volgende configuratiewaarden nodig:

* Id-bereik: Navigeer in uw IoT Central-toepassing naar **Beheer > Apparaatverbinding**. Noteer de waarde van **Id-bereik**.
* Primaire sleutel van groep: Navigeer in uw IoT Central-toepassing naar **Beheer > Apparaatverbinding > SAS-IoT-Devices**. Noteer de waarde van **Primaire sleutel** onder Shared Access Signature (SAS).

Gebruik de Cloud Shell om een apparaatcode te genereren op basis van de primaire sleutel van de groep die u hebt opgehaald:

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key --device-id sample-device-01 --pk <the group primary key value>
```

Noteer de gegenereerde apparaatsleutel. U hebt deze waarde verderop in deze zelfstudie nodig.
