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
ms.openlocfilehash: fd92eed127ec50a3d3a86f667d9aa764b79c190a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585404"
---
# <a name="trusted-storage-for-media-services"></a>Vertrouwde opslag voor Media Services

Wanneer u een Media Services-account maakt, moet u dit koppelen aan een opslag account. Media Services hebben toegang tot dat opslag account met behulp van systeem verificatie of beheerde identiteits verificatie. Media Services valideert of het Media Services-account en het opslag account zich in hetzelfde abonnement bevinden en valideert dat de gebruiker die de koppeling toevoegt, toegang heeft tot het opslag account met Azure Resource Manager RBAC.

>[!NOTE]
>Vertrouwde opslag is alleen beschikbaar in de API en is momenteel niet ingeschakeld in de Azure Portal.

## <a name="trusted-storage-with-a-firewall"></a>Vertrouwde opslag met een firewall

Als u echter een firewall wilt gebruiken om uw opslag account te beveiligen en vertrouwde opslag in te scha kelen, is [beheerde identiteits](concept-managed-identities.md) verificatie de voorkeurs optie. Hiermee kan Media Services toegang krijgen tot het opslag account dat is geconfigureerd met een firewall of een VNet-beperking via vertrouwde opslag toegang.

## <a name="tutorial"></a>Zelfstudie

Meer informatie over het inschakelen van vertrouwde opslag vindt u in de zelf studie voor [vertrouwde opslag Media Services](tutorial-trusted-storage-rest.md) .

> [!NOTE]
> U moet de AMS Managed Identity opslag BLOB data contributor-toegang verlenen om Media Services in staat te zijn om het opslag account te lezen en te schrijven.  Het verlenen van de rol algemene Inzender werkt niet omdat de juiste machtigingen niet worden ingeschakeld op het gegevens vlak.

## <a name="further-reading"></a>Meer lezen

Lees [beheerde identiteiten en Media Services](concept-managed-identities.md)om inzicht te krijgen in de methoden voor het maken van vertrouwde opslag met beheerde identiteiten.

Zie [Azure Storage firewalls en virtuele netwerken configureren](../../storage/common/storage-network-security.md#trusted-microsoft-services)voor meer informatie over vertrouwde micro soft-Services.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure AD Managed Identities](../../active-directory/managed-identities-azure-resources/overview.md)(Engelstalig) voor meer informatie over welke beheerde identiteiten voor u en uw Azure-toepassingen kunnen worden uitgevoerd.
