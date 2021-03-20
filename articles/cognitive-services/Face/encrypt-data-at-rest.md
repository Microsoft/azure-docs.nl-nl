---
title: Face service-versleuteling van data-at-rest
titleSuffix: Azure Cognitive Services
description: Micro soft biedt door micro soft beheerde coderings sleutels en kunt u ook uw Cognitive Services-abonnementen beheren met uw eigen sleutels, met de naam door de klant beheerde sleutels (CMK). In dit artikel worden gegevens versleuteling in rust voor gezicht beschreven en wordt uitgelegd hoe u CMK kunt inschakelen en beheren.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: a46253e586aaf90a85bc10e2c62ab6eb238f2ead
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100650810"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Face service-versleuteling van data-at-rest

Met de service Face worden uw gegevens automatisch versleuteld wanneer deze worden opgeslagen in de Cloud. Met de face service-versleuteling beschermt u uw gegevens en kunt u voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn alleen beschikbaar in de prijs categorie E0. Als u de mogelijkheid wilt aanvragen om door de klant beheerde sleutels te gebruiken, vult u het [aanvraag formulier voor het gezichts Service Customer-Managed](https://aka.ms/cogsvc-cmk). Het duurt ongeveer 3-5 werk dagen voordat de status van uw aanvraag wordt weer gegeven. Afhankelijk van de vraag, kunt u in een wachtrij plaatsen en worden goedgekeurd als er ruimte beschikbaar is. Nadat u hebt goedgekeurd voor het gebruik van CMK met de face-service, moet u een nieuwe gezichts bron maken en E0 selecteren als prijs categorie. Zodra uw gezichts bron met de prijs categorie E0 is gemaakt, kunt u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Volgende stappen

* Zie door de [klant beheerde sleutels voor Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md) voor een volledige lijst met services die CMK ondersteunen
* [Wat is Azure Key Vault](../../key-vault/general/overview.md)?
* [Aanvraag formulier voor Cognitive Services Customer-Managed](https://aka.ms/cogsvc-cmk)
