---
title: Content Moderator versleuteling van gegevens in rust
titleSuffix: Azure Cognitive Services
description: Content Moderator versleuteling van gegevens in rust.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 1401108a594e30790e842ec379724603f11d493f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100524476"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Content Moderator versleuteling van gegevens in rust

Content Moderator versleutelt uw gegevens automatisch wanneer deze in de cloud worden bewaard, zodat u kunt voldoen aan de beveiligings-en nalevings doelen van uw organisatie.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn alleen beschikbaar in de prijs categorie E0. Als u de mogelijkheid wilt aanvragen om door de klant beheerde sleutels te gebruiken, vult u het [Content Moderator Customer-Managed aanvraag formulier](https://aka.ms/cogsvc-cmk)in. Het duurt ongeveer 3-5 werk dagen voordat de status van uw aanvraag wordt weer gegeven. Afhankelijk van de vraag, kunt u in een wachtrij plaatsen en worden goedgekeurd als er ruimte beschikbaar is. Nadat het is goedgekeurd voor het gebruik van CMK met de Content Moderator-service, moet u een nieuwe Content Moderator resource maken en E0 selecteren als prijs categorie. Zodra uw Content Moderator resource met de prijs categorie E0 is gemaakt, kunt u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Gegevens versleuteling inschakelen voor uw Content Moderator team

Als u gegevens versleuteling wilt inschakelen voor uw Content Moderator beoordelings team, raadpleegt u de [Snelstartgids: probeer content moderator op Internet](quick-start.md#create-a-review-team).  

> [!NOTE]
> U moet een _resource-id_ opgeven met de prijs categorie content moderator E0.

## <a name="next-steps"></a>Volgende stappen

* Zie door de [klant beheerde sleutels voor Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md) voor een volledige lijst met services die CMK ondersteunen
* [Wat is Azure Key Vault](../../key-vault/general/overview.md)?
* [Aanvraag formulier voor Cognitive Services Customer-Managed](https://aka.ms/cogsvc-cmk)