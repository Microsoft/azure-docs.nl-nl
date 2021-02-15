---
title: Personalisatie service versleuteling van gegevens in rust
titleSuffix: Azure Cognitive Services
description: Micro soft biedt door micro soft beheerde coderings sleutels en kunt u ook uw Cognitive Services-abonnementen beheren met uw eigen sleutels, met de naam door de klant beheerde sleutels (CMK). In dit artikel vindt u informatie over gegevens versleuteling in rust voor Personaler en hoe u CMK inschakelt en beheert.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 1a27199930587c1a096dd99462ebd0c9d65054ee
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524436"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>Personalisatie service versleuteling van gegevens in rust

Met de Personaler service worden uw gegevens automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. De Personaler service Encryption beveiligt uw gegevens en helpt u om te voldoen aan de verplichtingen voor beveiliging en naleving van uw organisatie.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn alleen beschikbaar in de prijs categorie E0. Als u de mogelijkheid wilt aanvragen om door de klant beheerde sleutels te gebruiken, vult u de [personaler Service Customer-Managed aanvraag formulier](https://aka.ms/cogsvc-cmk)in en verzendt u deze. Het duurt ongeveer 3-5 werk dagen voordat de status van uw aanvraag wordt weer gegeven. Afhankelijk van de vraag, kunt u in een wachtrij plaatsen en worden goedgekeurd als er ruimte beschikbaar is. Nadat u hebt goedgekeurd voor het gebruik van CMK met de Personaler-service, moet u een nieuwe Personaler-resource maken en E0 selecteren als prijs categorie. Zodra uw persoonlijke resource met de prijs categorie E0 is gemaakt, kunt u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Volgende stappen

* [Aanvraag formulier voor de personaler service Customer-Managed sleutel](https://aka.ms/cogsvc-cmk)
* [Meer informatie over Azure Key Vault](../../key-vault/general/overview.md)