---
title: Versleuteling van gegevens in de vorm van een reserverings service
titleSuffix: Azure Cognitive Services
description: Micro soft biedt door micro soft beheerde coderings sleutels en kunt u ook uw Cognitive Services-abonnementen beheren met uw eigen sleutels, met de naam door de klant beheerde sleutels (CMK). In dit artikel wordt Inge gaan op de gegevens versleuteling van de formulier herkenner en hoe u CMK inschakelt en beheert.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 7a8b331c1295ed19afa64e95318bfa14414e6d9f
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524443"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Versleuteling van de gegevens in de rest van de formulier herkenning

Azure Form Recognizer versleutelt uw gegevens automatisch wanneer deze persistent worden gemaakt in de Cloud. Met versleuteling voor formulier herkenning worden uw gegevens beschermd zodat u kunt voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn alleen beschik bare resources die zijn gemaakt na 11 mei 2020. Als u CMK wilt gebruiken met de formulier herkenner, moet u een nieuwe resource voor de herkenner van een formulier maken. Zodra de resource is gemaakt, kunt u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Volgende stappen

* [Aanvraag formulier voor formulier herkenning Customer-Managed sleutel](https://aka.ms/cogsvc-cmk)
* [Meer informatie over Azure Key Vault](../../key-vault/general/overview.md)