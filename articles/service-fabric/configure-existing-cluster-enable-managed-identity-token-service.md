---
title: Ondersteuning voor beheerde identiteiten configureren in een bestaand Service Fabric cluster
description: U kunt als volgt ondersteuning voor beheerde identiteiten inschakelen in een bestaand Azure Service Fabric-cluster
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: dc341688cae6d98f53be10351e4e4572a3539e4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790036"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster"></a>Ondersteuning voor beheerde identiteiten configureren in een bestaand Service Fabric cluster

Als u [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) in uw service Fabric-toepassingen wilt gebruiken, moet u eerst de *service beheerde identiteits token* inschakelen op het cluster. Deze service is verantwoordelijk voor de verificatie van Service Fabric toepassingen met behulp van hun beheerde identiteiten en voor het verkrijgen van toegangs tokens in hun naam. Zodra de service is ingeschakeld, kunt u deze weer geven in Service Fabric Explorer onder het gedeelte **systeem** in het linkerdeel venster, dat wordt uitgevoerd onder de naam **Fabric:/System/ManagedIdentityTokenService**.

> [!NOTE]
> Service Fabric runtime versie 6.5.658.9590 of hoger is vereist om de **beheerde identiteits token service** in te scha kelen.  
>
> U kunt de Service Fabric versie van een cluster uit de Azure Portal vinden door de cluster bron te openen en de eigenschap **service Fabric versie** te controleren in de sectie **Essentials** .
>
> Als het cluster zich in de **hand matige** upgrade modus bevindt, moet u het eerst upgraden naar 6.5.658.9590 of hoger.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>*Beheerde identiteits token service* inschakelen in een bestaand cluster

Als u de service beheerde identiteits token in een bestaand cluster wilt inschakelen, moet u een cluster upgrade initiëren die twee wijzigingen specificeert: (1) het inschakelen van de beheerde identiteits token service, en (2) het aanvragen van het opnieuw opstarten van elk knoop punt. Voeg eerst het volgende fragment toe uw cluster Azure Resource Manager sjabloon:

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

Om de wijzigingen van kracht te laten worden, moet u ook het upgrade beleid wijzigen om een geforceerde opnieuw op te geven van de Service Fabric runtime op elk knoop punt wanneer de upgrade wordt uitgevoerd via het cluster. Opnieuw opstarten zorgt ervoor dat de nieuw ingeschakelde systeem service wordt gestart en wordt uitgevoerd op elk knoop punt. In het onderstaande fragment `forceRestart` is de essentiële instelling voor het inschakelen van opnieuw opstarten. Voor de overige para meters gebruikt u de hieronder beschreven waarden of gebruikt u bestaande aangepaste waarden die al zijn opgegeven voor de cluster bron. Aangepaste instellingen voor Fabric-upgrade beleid (' upgradeDescription ') kunnen worden weer gegeven vanuit Azure portal door de optie infrastructuur upgrades te selecteren op de Service Fabric resource of resources.azure.com. Standaard opties voor het upgrade beleid (' upgradeDescription ') zijn niet zichtbaar vanuit Power shell of resources.azure.com. Zie [ClusterUpgradePolicy](/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy) voor meer informatie.  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> Wanneer de upgrade is voltooid, vergeet dan niet om de instelling terug te draaien `forceRestart` om de impact van de volgende upgrades te minimaliseren. 

## <a name="errors-and-troubleshooting"></a>Fouten en probleemoplossing

Als de implementatie mislukt met het volgende bericht, betekent dit dat het cluster niet wordt uitgevoerd op een hoge voldoende Service Fabric-versie:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Volgende stappen
* [Een Azure Service Fabric-toepassing implementeren met een door het systeem toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Een Azure Service Fabric-toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Maak gebruik van de beheerde identiteit van een Service Fabric toepassing vanuit service code](./how-to-managed-identity-service-fabric-app-code.md)
* [Een Azure Service Fabric-toepassing toegang verlenen tot andere Azure-resources](./how-to-grant-access-other-resources.md)
