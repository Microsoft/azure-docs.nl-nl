---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 10/30/2020
ms.topic: include
ms.openlocfilehash: e006f804b8ab6411f4949424147acf567dc2ed24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97371305"
---
## <a name="sign-in-to-luis-portal"></a>Aanmelden bij de LUIS-portal

[!INCLUDE [Note about portal deprecation](luis-portal-note.md)]

Een nieuwe gebruiker van LUIS moet deze procedure volgen:

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai), selecteer uw land/regio en ga akkoord met de gebruiksvoorwaarden. Als u in plaats daarvan **Mijn apps** ziet, bestaat er al een LUIS-resource en kunt u verdergaan met het maken van een app. U hebt twee opties bij het aanmelden:

    * Met een Azure-resource (aanbevolen): Hiermee kunt u uw LUIS-account koppelen aan een nieuwe of bestaande Azure-creatieresource. Dit is gelijk aan aanmelden als u al bent gemigreerd. U hoeft later niet het [migratieproces](../luis-migration-authoring.md#what-is-migration) te doorlopen.

    * Een proefversiesleutel gebruiken. Zo kunt u zich bij LUIS aanmelden met een proefversieresource die u niet hoeft in te stellen. Als u deze optie kiest, moet u uiteindelijk [uw account migreren](../luis-migration-authoring.md#migration-steps) en uw toepassingen koppelen aan een creatieresource.

1. Zoek in het venster **Een creatie kiezen** dat verschijnt uw Azure-abonnement en LUIS-creatieresource. Als u geen resource hebt, kunt u een nieuwe maken.

    :::image type="content" source="../media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="Kies een type Language Understanding-creatieresource.":::
    
    Geef bij het maken van een nieuwe ontwerpresource de volgende informatie op:
    * **Tenantnaam**: de tenant waaraan uw Azure-abonnement is gekoppeld.
    * **Azure-abonnementsnaam**: het abonnement dat wordt gefactureerd voor de resource.
    * **Azure-resourcegroepsnaam**: een aangepaste resourcegroepnaam die u kiest of maakt. Met resourcegroepen kunt u Azure-resources groeperen voor toegang en beheer.
    * **Azure-resourcenaam**: een door u gekozen aangepaste naam die wordt gebruikt als onderdeel van de URL voor uw ontwerp- en voorspellingseindpuntquery's.
    * **Prijscategorie**: de prijscategorie bepaalt de maximale transactie per seconde en maand.


