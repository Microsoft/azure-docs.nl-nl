---
title: Media Services v2 versus v3 API-toegang
description: In dit artikel worden de verschillen in API-toegang tussen Azure Media Services v2 en V3 beschreven.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 5f3c6526139389da3bfdbc3c43cf8b6d2a1dbccf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567964"
---
# <a name="api-access-differences-between-azure-media-services-v2-to-v3-api"></a>API-toegangs verschillen tussen Azure Media Services v2 en V3 API

![logo van de migratie handleiding](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![migratie stappen 2](./media/migration-guide/steps-2.svg)

In dit artikel worden de verschillen in API-toegang tussen Azure Media Services v2 en V3 beschreven.

## <a name="api-access"></a>API-toegang

Alle Media Services accounts hebben toegang tot de V3 API. We raden u echter met klem aan migratie te ontwikkelen voor een nieuw account voordat u bijgewerkte code toepast op een bestaand v2-account. Dit komt doordat v3-entiteiten niet achterwaarts compatibel zijn met v2. Sommige v2-entiteiten zoals activa zijn compatibel met v3.
U kunt bestaande accounts blijven gebruiken als u de v2-en V3-Api's niet combineert en vervolgens probeert terug te gaan naar v2, maar dit wordt afgeraden.

Toegang tot de v2 API is beschikbaar tot deze buiten gebruik is gesteld in 2024.

## <a name="create-a-v3-account"></a>Een v3-account maken

Tijdens de migratie kunt u een v3-account maken dat nog steeds toegang heeft tot v2.  Het maken van het account kan worden uitgevoerd met:

- De REST API en oudere versie
- Het selectie vakje in de portal te selecteren.

> [!div class="mx-imgBorder"]
> [![accounts maken in de portal ](./media/migration-guide/v-3-v-2-access-account-creation-small.png)](./media/migration-guide/v-3-v-2-access-account-creation.png#lightbox)

Alle .NET-, CLI-en andere Sdk's zullen gericht zijn op de nieuwste 2020-05-01-API, dus zoek of configureer de oudere API-versies.

> [!NOTE]
> Nieuwe accounts die zijn gemaakt met de 2020-05-01-API kunnen geen v2-Api's gebruiken.
