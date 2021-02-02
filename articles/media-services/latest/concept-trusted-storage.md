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
ms.openlocfilehash: 59c1eb7936bc113f8935d6fa2ad378c6994c3ca9
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99408489"
---
# <a name="trusted-storage-for-media-services"></a>Vertrouwde opslag voor Media Services

Wanneer u een Media Services-account maakt, moet u dit koppelen aan een opslag account. Media Services hebben toegang tot dat opslag account met behulp van systeem verificatie. Media Services valideert of het Media Services-account en het opslag account zich in hetzelfde abonnement bevinden en valideert dat de gebruiker die de koppeling toevoegt, toegang heeft tot het opslag account met Azure Resource Manager RBAC.

Als u echter een firewall wilt gebruiken om uw opslag account te beveiligen en vertrouwde opslag in te scha kelen, moet u [beheerde identiteits](concept-managed-identities.md) verificatie gebruiken. Hiermee kan Media Services toegang krijgen tot het opslag account dat is geconfigureerd met een firewall of een VNet-beperking via vertrouwde opslag toegang.

Lees [beheerde identiteiten en Media Services](concept-managed-identities.md)om inzicht te krijgen in de methoden voor het maken van vertrouwde opslag met beheerde identiteiten.

Zie [uw eigen sleutel (door de klant beheerde sleutels) meenemen met Media Services](concept-use-customer-managed-keys-byok.md) voor meer informatie over door de klant beheerde sleutels en Key Vault.

Zie [Azure Storage firewalls en virtuele netwerken configureren](../../storage/common/storage-network-security.md#trusted-microsoft-services)voor meer informatie over vertrouwde micro soft-Services.

## <a name="tutorials"></a>Zelfstudies

Deze zelf studies bevatten beide hierboven vermelde scenario's.

- [De Azure Portal gebruiken om door klant beheerde sleutels of BYOK met Media Services](tutorial-byok-portal.md)
- [Gebruik door de klant beheerde sleutels of BYOK met Media Services rest API](tutorial-byok-postman.md).

## <a name="next-steps"></a>Volgende stappen

Zie [Azure AD Managed Identities](../../active-directory/managed-identities-azure-resources/overview.md)(Engelstalig) voor meer informatie over welke beheerde identiteiten voor u en uw Azure-toepassingen kunnen worden uitgevoerd.