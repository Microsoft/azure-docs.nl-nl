---
title: Beheerde identiteiten en vertrouwde opslag met Media Services
description: Media Services kan worden gebruikt met beheerde identiteiten voor het inschakelen van vertrouwde opslag.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: inhenkel
ms.openlocfilehash: d0811e8f9183ee334d413bcad69f2c7b32023be3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499353"
---
# <a name="managed-identities-and-trusted-storage-with-media-services"></a>Beheerde identiteiten en vertrouwde opslag met Media Services

Media Services kan worden gebruikt met [beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md) voor het inschakelen van vertrouwde opslag. Wanneer u een Media Services-account maakt, moet u dit koppelen aan een opslag account. Media Services hebben toegang tot dat opslag account met behulp van systeem verificatie. Media Services valideert of het Media Services-account en het opslag account zich in hetzelfde abonnement bevinden en valideert dat de gebruiker die de koppeling toevoegt, toegang heeft tot het opslag account met Azure Resource Manager RBAC.

## <a name="trusted-storage"></a>Vertrouwde opslag

Als u echter een firewall wilt gebruiken om uw opslag account te beveiligen, moet u beheerde identiteits verificatie gebruiken. Hiermee kan Media Services toegang krijgen tot het opslag account dat is geconfigureerd met een firewall of een VNet-beperking via vertrouwde opslag toegang.  Zie [Azure Storage firewalls en virtuele netwerken configureren](../../storage/common/storage-network-security.md#trusted-microsoft-services)voor meer informatie over vertrouwde micro soft-Services.

## <a name="media-services-managed-identity-scenarios"></a>Scenario's voor beheerde identiteiten van Media Services

Er zijn momenteel twee scenario's waarbij beheerde identiteit kan worden gebruikt met Media Services:

- Gebruik de beheerde identiteit van het Media Services-account voor toegang tot opslag accounts.

- Gebruik de beheerde identiteit van het Media Services account om toegang te krijgen tot Key Vault voor toegang tot klant sleutels.

In de volgende twee secties worden de verschillen in de twee scenario's beschreven.

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>De beheerde identiteit van het Media Services account gebruiken voor toegang tot opslag accounts

1. Maak een Media Services-account met een beheerde identiteit.
1. Verleen de beheerde identiteits-Principal toegang tot een opslag account dat u bezit.
1. Media Services kunt vervolgens met behulp van de beheerde identiteit toegang krijgen tot het opslag account.

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>De beheerde identiteit van het Media Services account gebruiken om toegang te krijgen tot Key Vault voor toegang tot klant sleutels

1. Maak een Media Services-account met een beheerde identiteit.
1. Verleen de beheerde identiteits-Principal toegang tot een Key Vault waarvan u de eigenaar bent.
1. Configureer het Media Services-account om de account versleuteling op basis van de klant te gebruiken.
1. Media Services met behulp van de beheerde identiteit Key Vault namens u toegang hebt.

Zie [uw eigen sleutel (door de klant beheerde sleutels) meenemen met Media Services](concept-use-customer-managed-keys-byok.md) voor meer informatie over door de klant beheerde sleutels en Key Vault.

## <a name="tutorials"></a>Zelfstudies

Deze zelf studies bevatten beide hierboven vermelde scenario's.

- [Gebruik de Azure Portal om door de klant beheerde sleutels of BYOK te gebruiken met Media Services](tutorial-byok-portal.md)
- [Gebruik door de klant beheerde sleutels of BYOK met Media Services rest API](tutorial-byok-postman.md).

## <a name="next-steps"></a>Volgende stappen

Zie [Azure AD Managed Identities](../../active-directory/managed-identities-azure-resources/overview.md)(Engelstalig) voor meer informatie over welke beheerde identiteiten voor u en uw Azure-toepassingen kunnen worden uitgevoerd.