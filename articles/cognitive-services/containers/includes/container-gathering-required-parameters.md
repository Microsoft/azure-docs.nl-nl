---
title: Vereiste parameters verzamelen
services: cognitive-services
author: aahill
manager: nitinme
description: De para meters voor alle Cognitive Services containers
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 85c67384c38376dc6f5701cf26e879d6e1b76c7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "95996408"
---
## <a name="gathering-required-parameters"></a>Vereiste parameters verzamelen

Er zijn drie primaire para meters voor alle vereiste containers van Cognitive Services. De gebruiksrecht overeenkomst (EULA) moet aanwezig zijn met een waarde van `accept` . Bovendien zijn er een eindpunt-URL en API-sleutel nodig.

### <a name="endpoint-uri-endpoint_uri"></a>Eind punt-URI `{ENDPOINT_URI}`

De URI-waarde van het **eind punt** is beschikbaar op de pagina *overzicht* van Azure portal van de bijbehorende cognitieve service resource. Ga naar de *overzichts* pagina, beweeg de muis aanwijzer over het eind punt en er `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> wordt een pictogram weer gegeven. Kopieer en gebruik waar nodig.

![De URI van het eind punt verzamelen voor later gebruik](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Subknooppuntsleutels `{API_KEY}`

Deze sleutel wordt gebruikt om de container te starten en is beschikbaar op de pagina sleutels van de Azure Portal van de bijbehorende cognitieve service resource. Ga naar de pagina *sleutels* en klik op het `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> pictogram.

![Een van de twee sleutels ophalen voor later gebruik](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Deze abonnementssleutels worden gebruikt om toegang te krijgen tot de API van Cognitive Services. Deel uw sleutels niet. Sla ze veilig op, bijvoorbeeld met behulp van Azure Key Vault. We raden u ook aan om deze sleutels regelmatig opnieuw te genereren. Er is slechts één sleutel nodig om een API-aanroep te maken. Wanneer u de eerste sleutel opnieuw genereert, kunt u de tweede sleutel gebruiken voor verdere toegang tot de service.