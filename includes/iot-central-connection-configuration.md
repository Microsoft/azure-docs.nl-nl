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
ms.openlocfilehash: 2b7881e7fa1ccbcec1325b2af0a570c86b0585a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017477"
---
Wanneer u de toepassing voor het voorbeeldapparaat later in deze zelfstudie uitvoert, hebt u de volgende configuratiewaarden nodig:

* Id-bereik: Navigeer in uw IoT Central-toepassing naar **Beheer > Apparaatverbinding**. Noteer de waarde van **Id-bereik**.
* Primaire sleutel van groep: Navigeer in uw IoT Central-toepassing naar **Beheer > Apparaatverbinding > SAS-IoT-Devices**. Noteer de waarde van **Primaire sleutel** onder Shared Access Signature (SAS).

Gebruik de Cloud Shell om een apparaatsleutel te genereren op basis van de groeps-SAS-sleutel die u zojuist hebt genoteerd:

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key  --device-id sample-device-01 --pk <the group SAS primary key value>
```

Noteer de gegenereerde apparaatsleutel. U hebt deze waarde verderop in deze zelfstudie nodig.
