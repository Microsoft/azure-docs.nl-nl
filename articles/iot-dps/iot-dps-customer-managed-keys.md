---
title: Azure Device Provisioning Service gegevens versleuteling op rest via door de klant beheerde sleutels | Microsoft Docs
description: Versleuteling van gegevens in rust met door de klant beheerde sleutels voor Device Provisioning Service
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: d22a01bab81fc330484e7715a65c89a1cfd7802c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94967173"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Versleuteling van gegevens in rust met door de klant beheerde sleutels voor Device Provisioning Service

## <a name="overview"></a>Overzicht

Device Provisioning Service (DPS) ondersteunt versleuteling van gegevens in rust met door de klant beheerde sleutels (CMK), ook wel bekend als uw eigen sleutel (BYOK). DPS voorziet in het versleutelen van gegevens in rust en onderweg, omdat deze worden geschreven in onze data centers en worden ontsleuteld voor u wanneer u er toegang toe hebt. DPS maakt standaard gebruik van door micro soft beheerde sleutels om de gegevens in rust te versleutelen. Met CMK kunt u een extra laag versleuteling krijgen boven standaard platform versleuteling door ervoor te kiezen om gegevens in rust te versleutelen met een sleutel versleutelings sleutel die wordt beheerd via uw [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Dit biedt u de flexibiliteit om sleutels te maken, te roteren, uit te scha kelen en in te trekken. Als CMK is geconfigureerd voor uw DPS, betekent dit dat dubbele versleuteling is ingeschakeld met twee beveiligings lagen die uw gegevens actief beveiligen. 

Voor deze functie is het maken van een nieuwe DPS vereist. Neem contact met ons op via [micro soft ondersteuning](https://azure.microsoft.com/support/create-ticket/)als u deze mogelijkheid wilt proberen. Deel uw bedrijfs naam en abonnements-ID wanneer u contact opneemt met micro soft ondersteuning.


## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Device Provisioning Service](./index.yml)

* [Meer informatie over Azure Key Vault](../key-vault/general/overview.md)