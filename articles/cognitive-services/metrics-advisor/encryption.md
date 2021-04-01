---
title: Metrics Advisor-service versleuteling
titleSuffix: Azure Cognitive Services
description: Metrics Advisor-service versleuteling van gegevens in rust.
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 5d41500a9c53e38cd36f0feba602e0e1baa5da2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "92909740"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>Metrics Advisor-service versleuteling van gegevens in rust

Met de data Advisor-service worden uw gegevens automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. Met de metrische gegevens van de Advisor-service wordt de informatie beschermd en kunnen we u helpen om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn alleen beschikbaar in de prijs categorie E0. Als u de mogelijkheid wilt aanvragen om door de klant beheerde sleutels te gebruiken, vult u het [aanvraag formulier voor metrische gegevens Advisor-Service Customer-Managed](https://aka.ms/cogsvc-cmk). Het duurt ongeveer 3-5 werk dagen voordat de status van uw aanvraag wordt weer gegeven. Afhankelijk van de vraag, kunt u in een wachtrij plaatsen en worden goedgekeurd als er ruimte beschikbaar is. Na goed keuring voor het gebruik van CMK met de metrics Advisor-service, moet u een nieuwe resource Advisor maken en selecteert u E0 als prijs categorie. Zodra uw metrische Advisor-resource met de prijs categorie E0 is gemaakt, kunt u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Volgende stappen

* [Aanvraag formulier voor metrische gegevens Advisor-service Customer-Managed](https://aka.ms/cogsvc-cmk)
* [Meer informatie over Azure Key Vault](../../key-vault/general/overview.md)