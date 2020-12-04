---
title: Ondersteuning voor beheerde identiteiten configureren voor een nieuw Service Fabric cluster
description: U kunt als volgt ondersteuning voor beheerde identiteiten inschakelen in een nieuw Azure Service Fabric-cluster
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: ae4fec452a2342a68843d874ba955b594014c46d
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574663"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster"></a>Ondersteuning voor beheerde identiteiten configureren voor een nieuw Service Fabric cluster

Als u [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) in uw service Fabric-toepassingen wilt gebruiken, moet u eerst de *service beheerde identiteits token* inschakelen op het cluster. Deze service is verantwoordelijk voor de verificatie van Service Fabric toepassingen met behulp van hun beheerde identiteiten en voor het verkrijgen van toegangs tokens in hun naam. Zodra de service is ingeschakeld, kunt u deze weer geven in Service Fabric Explorer onder het gedeelte **systeem** in het linkerdeel venster, dat wordt uitgevoerd onder de naam **Fabric:/System/ManagedIdentityTokenService** naast andere systeem services.

> [!NOTE]
> Service Fabric runtime versie 6.5.658.9590 of hoger is vereist om de **beheerde identiteits token service** in te scha kelen.  

## <a name="enable-the-managed-identity-token-service"></a>De service beheerde identiteits token inschakelen

Voeg het volgende code fragment toe aan uw cluster Azure Resource Manager sjabloon om de beheerde identiteits token service in te scha kelen tijdens het maken van het cluster:

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

## <a name="errors"></a>Fouten

Als de implementatie mislukt met dit bericht, betekent dit dat het cluster niet op de vereiste Service Fabric versie is (de mini maal ondersteunde runtime is 6,5 CU2):


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>Gerelateerde artikelen

* [Ondersteuning voor beheerde identiteiten](./concepts-managed-identity.md) bekijken in azure service Fabric

* [Ondersteuning van beheerde identiteiten inschakelen in een bestaand Azure Service Fabric-cluster](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Volgende stappen

* [Een Azure Service Fabric-toepassing implementeren met een door het systeem toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Een Azure Service Fabric-toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Maak gebruik van de beheerde identiteit van een Service Fabric toepassing vanuit service code](./how-to-managed-identity-service-fabric-app-code.md)
* [Een Azure Service Fabric-toepassing toegang verlenen tot andere Azure-resources](./how-to-grant-access-other-resources.md)