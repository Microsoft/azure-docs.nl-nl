---
title: Vertrouwde opslag voor Media Services
description: Met verificatie van beheerde identiteiten kan Media Services toegang krijgen tot het opslag account dat is geconfigureerd met een firewall of een VNet-beperking via vertrouwde opslag toegang.
keywords: vertrouwde opslag, beheerde identiteiten
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: e8d21e57f9a844b3cc0538f4805780829a1350f4
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428585"
---
# <a name="trusted-storage-for-media-services"></a>Vertrouwde opslag voor Media Services

Wanneer u een Media Services-account maakt, moet u dit koppelen aan een opslag account. Media Services hebben toegang tot dat opslag account met behulp van systeem verificatie of beheerde identiteits verificatie. Media Services valideert of het Media Services-account en het opslag account zich in hetzelfde abonnement bevinden en valideert dat de gebruiker die de koppeling toevoegt, toegang heeft tot het opslag account met Azure Resource Manager RBAC.

## <a name="trusted-storage-with-a-firewall"></a>Vertrouwde opslag met een firewall

Als u echter een firewall wilt gebruiken om uw opslag account te beveiligen en vertrouwde opslag in te scha kelen, is [beheerde identiteits](concept-managed-identities.md) verificatie de voorkeurs optie. Hiermee kan Media Services toegang krijgen tot het opslag account dat is geconfigureerd met een firewall of een VNet-beperking via vertrouwde opslag toegang. Hiermee kan Media Services toegang krijgen tot het opslag account dat is geconfigureerd met een firewall of een VNet-beperking via vertrouwde opslag toegang.

> [!NOTE]
> U moet de AMS Managed Identity opslag BLOB data contributor-toegang verlenen om Media Services in staat te zijn om het opslag account te lezen en te schrijven.  Het verlenen van de rol algemene Inzender werkt niet omdat de juiste machtigingen niet worden ingeschakeld op het gegevens vlak.

## <a name="further-reading"></a>Meer lezen

Lees [beheerde identiteiten en Media Services](concept-managed-identities.md)om inzicht te krijgen in de methoden voor het maken van vertrouwde opslag met beheerde identiteiten.

Zie [Azure Storage firewalls en virtuele netwerken configureren](../../storage/common/storage-network-security.md#trusted-microsoft-services)voor meer informatie over vertrouwde micro soft-Services.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure AD Managed Identities](../../active-directory/managed-identities-azure-resources/overview.md)(Engelstalig) voor meer informatie over welke beheerde identiteiten voor u en uw Azure-toepassingen kunnen worden uitgevoerd.
