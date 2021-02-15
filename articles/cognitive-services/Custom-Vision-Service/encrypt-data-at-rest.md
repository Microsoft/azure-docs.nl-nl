---
title: Custom Vision versleuteling van gegevens in rust
titleSuffix: Azure Cognitive Services
description: Micro soft biedt door micro soft beheerde coderings sleutels en kunt u ook uw Cognitive Services-abonnementen beheren met uw eigen sleutels, met de naam door de klant beheerde sleutels (CMK). Dit artikel bevat informatie over gegevens versleuteling in rust voor Custom Vision en hoe u CMK inschakelt en beheert.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 6c65f28c040b15aaa2ec8f3425209351e4b60486
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524468"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Custom Vision versleuteling van gegevens in rust

Met Azure Custom Vision worden uw gegevens automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. Custom Vision versleuteling beveiligt uw gegevens en helpt u om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn alleen beschik bare resources die zijn gemaakt na 11 mei 2020. Als u CMK met Custom Vision wilt gebruiken, moet u een nieuwe Custom Vision resource maken. Zodra de resource is gemaakt, kunt u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

## <a name="regional-availability"></a>Regionale beschikbaarheid

Door de klant beheerde sleutels zijn momenteel beschikbaar in deze regio's:

* US - zuid-centraal
* VS - west 2
* VS - oost
* VS (overheid) - Virginia

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Volgende stappen

* Zie door de [klant beheerde sleutels voor Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md) voor een volledige lijst met services die CMK ondersteunen
* [Wat is Azure Key Vault](../../key-vault/general/overview.md)?
* [Aanvraag formulier voor Cognitive Services Customer-Managed](https://aka.ms/cogsvc-cmk)