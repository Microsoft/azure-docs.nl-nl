---
title: Een externe cache gebruiken in Azure API Management | Microsoft Docs
description: Informatie over het configureren en gebruiken van een externe cache in Azure API Management. Door gebruik te maken van een externe cache kunt u enkele beperkingen van de ingebouwde cache opheffen.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 6288a10e111e42629abf5e09b84a6a7791dcfe95
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018219"
---
# <a name="use-an-external-redis-compatible-cache-in-azure-api-management"></a>Een extern met redis compatibele cache gebruiken in azure API Management

Naast het gebruik van de ingebouwde cache, biedt Azure API Management u de mogelijkheid om antwoorden in een cache op te slaan in een externe redis, zoals Azure cache voor redis.

Door gebruik te maken van een externe cache kunt u enkele beperkingen van de ingebouwde cache opheffen:

* Als u wilt vermijden dat uw cache periodiek wordt gewist tijdens het bijwerken van API Management
* Als u meer controle wilt over de configuratie van uw cache
* Als u meer gegevens in de cache wilt opslaan dan is toegestaan door uw API Management-laag
* Als u opslag in de cache wilt gebruiken met de laag Verbruik van API Management
* Caching inschakelen in de [API Management zelf-hostende gateways](self-hosted-gateway-overview.md)

Zie [De cachebeleidsregels van API Management](api-management-caching-policies.md) en [Aangepaste opslaan in de cache in Azure API Management](api-management-sample-cache-by-key.md) voor meer informatie over opslaan in de cache.

![Uw eigen cache gebruiken in API Management](media/api-management-howto-cache-external/overview.png)

Wat u leert:

> [!div class="checklist"]
> * Een externe cache toevoegen in API Management

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

+ [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)
+ Inzicht in [Aangepast opslaan in cache in Azure API Management](api-management-howto-cache.md)

## <a name="create-azure-cache-for-redis"></a><a name="create-cache"> </a> Azure-cache maken voor redis

In dit gedeelte wordt uitgelegd hoe u een Azure-cache voor Redis maakt in Azure. Als u al een Azure-cache voor Redis hebt, binnen of buiten Azure, kunt u <a href="#add-external-cache">doorgaan</a> naar het volgende gedeelte.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="deploy-redis-cache-to-kubernetes"></a><a name="create-cache"> </a> Redis-cache implementeren op Kubernetes

Voor het opslaan in de cache zijn zelf-hostende gateways uitsluitend afhankelijk van externe caches. Voor het opslaan in cache als effectief zelf-hostende gateways en de cache waarvan ze afhankelijk zijn, moeten zich dicht bij elkaar bevinden om de zoek-en opslag latentie te minimaliseren. Het implementeren van een redis-cache in hetzelfde Kubernetes-cluster of in een afzonderlijk cluster in de buurt zijn de beste opties. Volg deze [koppeling](https://github.com/kubernetes/examples/tree/master/guestbook) voor meer informatie over het implementeren van redis-cache naar een Kubernetes-cluster.

## <a name="add-an-external-cache"></a><a name="add-external-cache"> </a>Een externe cache toevoegen

Volg de onderstaande stappen als u een externe Azure-cache voor Redis wilt toevoegen in Azure API Management.

![Scherm afbeelding die laat zien hoe u een externe Azure-cache kunt toevoegen voor redis in azure API Management.](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> De instelling **gebruiken vanuit** geeft een Azure-regio of een zelf-hostende gateway locatie op die de geconfigureerde cache gebruikt. De caches die zijn geconfigureerd als **standaard** , worden overschreven door caches met een specifieke overeenkomende regio of locatie waarde.
>
> Bijvoorbeeld, als API Management wordt gehost in de regio's VS - oost, Azië - zuidoost en Europa - west, en er twee caches zijn geconfigureerd, één voor **Default** en één voor **Azië - zuidoost**, dan zal API Management in **Azië - zuidoost** een eigen cache gebruiken, terwijl de andere twee regio's de cachevermelding **Standaard** gebruiken.

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Een Azure-cache voor Redis toevoegen uit hetzelfde abonnement

1. Blader naar uw API Management-exemplaar in de Azure-portal.
2. Selecteer het tabblad **Externe cache** in het menu aan de linkerkant.
3. Klik op de knop **+ Toevoegen**.
4. Selecteer uw cache in de vervolgkeuzelijst **Cache-instantie**.
5. Selecteer **standaard** of geef de gewenste regio op in het veld **gebruiken vanuit** vervolg keuzelijst.
6. Klik op **Opslaan**.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Een Azure-cache voor Redis toevoegen dat wordt gehost buiten het huidige Azure-abonnement of Azure in het algemeen

1. Blader naar uw API Management-exemplaar in de Azure-portal.
2. Selecteer het tabblad **Externe cache** in het menu aan de linkerkant.
3. Klik op de knop **+ Toevoegen**.
4. Selecteer **Aangepast** in de vervolgkeuzelijst **Cache-instantie**.
5. Selecteer **standaard** of geef de gewenste regio op in het veld **gebruiken vanuit** vervolg keuzelijst.
6. Geef de verbindingsreeks van uw Azure-cache voor Redis op in het veld **Verbindingsreeks**.
7. Klik op **Opslaan**.

### <a name="add-a-redis-cache-to-a-self-hosted-gateway"></a>Een redis-cache toevoegen aan een zelf-hostende gateway

1. Blader naar uw API Management-exemplaar in de Azure-portal.
2. Selecteer het tabblad **Externe cache** in het menu aan de linkerkant.
3. Klik op de knop **+ Toevoegen**.
4. Selecteer **Aangepast** in de vervolgkeuzelijst **Cache-instantie**.
5. Geef de gewenste zelf-hostende gateway locatie of **standaard** op in het veld **gebruiken vanuit** vervolg keuzelijst.
6. Geef de verbindingsreeks van uw Redis-cache op in het veld **Verbindingsreeks**.
7. Klik op **Opslaan**.

## <a name="use-the-external-cache"></a>De externe cache gebruiken

Wanneer de externe cache in Azure API Management is geconfigureerd, kan deze worden gebruikt via cachebeleidsregels. Zie [Opslaan in cache toevoegen om de prestaties in Azure API Management te verbeteren](api-management-howto-cache.md) voor gedetailleerde stappen.

## <a name="next-steps"></a><a name="next-steps"> </a>Volgende stappen

* Zie [Cachebeleidsregels][Caching policies] in [Naslaginformatie over beleid voor API Management][API Management policy reference] voor meer informatie over cachebeleidsregels.
* Zie [Aangepast opslaan in cache in Azure API Management](api-management-sample-cache-by-key.md) voor informatie over het opslaan van items in de cache per sleutel met behulp van beleidsexpressies.

[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-caching-policies.md
