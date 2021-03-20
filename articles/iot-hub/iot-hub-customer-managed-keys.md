---
title: Azure IoT Hub gegevens versleuteling op rest via door de klant beheerde sleutels | Microsoft Docs
description: Versleuteling van gegevens in rust met door de klant beheerde sleutels voor IoT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: asrastog
ms.openlocfilehash: 352da24b36124ff0446a81c1ecbc584da545bb16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92142210"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Versleuteling van gegevens in rust met door de klant beheerde sleutels voor IoT Hub

IoT Hub ondersteunt versleuteling van gegevens in rust met door de klant beheerde sleutels (CMK), ook wel bekend als uw eigen sleutel (BYOK). Azure IoT Hub zorgt voor versleuteling van gegevens in rust en in transit, omdat deze in onze data centers wordt geschreven en voor u wordt gedecodeerd wanneer u deze opent. IoT Hub maakt standaard gebruik van door micro soft beheerde sleutels om de gegevens in rust te versleutelen. Met CMK kunt u een andere laag versleuteling krijgen boven standaard versleuteling en kunt u ervoor kiezen om gegevens in rust te versleutelen met een sleutel coderings sleutel, die wordt beheerd via uw [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Dit biedt u de flexibiliteit om toegangs beheer te maken, te draaien, uit te scha kelen en in te trekken. Als BYOK is geconfigureerd voor uw IoT Hub, bieden we ook dubbele code ring, die een tweede beveiligingslaag biedt, en waarmee u de versleutelings sleutel via uw Azure Key Vault kunt beheren.

Voor deze functie is het maken van een nieuwe IoT Hub vereist (Basic-of Standard-laag). Neem contact met ons op via [micro soft ondersteuning](https://azure.microsoft.com/support/create-ticket/)als u deze mogelijkheid wilt proberen. Deel uw bedrijfs naam en abonnements-ID wanneer u contact opneemt met micro soft ondersteuning.


## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over IoT Hub](./about-iot-hub.md)

* [Meer informatie over Azure Key Vault](../key-vault/general/overview.md)