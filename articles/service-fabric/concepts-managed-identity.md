---
title: Beheerde identiteiten voor Azure
description: Meer informatie over het gebruik van beheerde identiteiten voor Azure met Service Fabric.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 28c992792d4572a43e12f5d32855f8411b0f4c6f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574748"
---
# <a name="using-managed-identities-for-azure-with-service-fabric"></a>Beheerde identiteiten gebruiken voor Azure met Service Fabric

Een veelvoorkomende uitdaging bij het bouwen van Cloud toepassingen is het veilig beheren van de referenties in uw code voor verificatie bij verschillende services zonder ze lokaal op te slaan op een werk station voor ontwikkel aars of in broncode beheer. Met *beheerde identiteiten voor Azure* kunt u dit probleem oplossen voor al uw resources in azure Active Directory (Azure AD) door ze te voorzien van automatische beheerde identiteiten in azure AD. U kunt de identiteit van een service gebruiken om te verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder dat er referenties zijn opgeslagen in uw code.

*Beheerde identiteiten voor Azure-resources* zijn gratis met Azure AD voor Azure-abonnementen. Er zijn geen extra kosten.

> [!NOTE]
> *Beheerde identiteiten voor Azure* is de nieuwe naam voor de service, voorheen bekend als managed service Identity (MSI).

## <a name="concepts"></a>Concepten

Beheerde identiteiten voor Azure zijn gebaseerd op verschillende belang rijke concepten:

- **Client-id** : een unieke id die wordt gegenereerd door Azure AD die is gekoppeld aan een toepassing en Service-Principal tijdens de eerste inrichting (ook de [toepassings-id](../active-directory/develop/developer-glossary.md#application-id-client-id).)

- **Principal-id** : de object-id van het Service-Principal-object voor uw beheerde identiteit die wordt gebruikt om op rollen gebaseerde toegang tot een Azure-resource te verlenen.

- **Service-Principal** : een Azure Active Directory-object dat de projectie van een Aad-toepassing in een bepaalde Tenant vertegenwoordigt (ook [Service-Principal](../active-directory/develop/developer-glossary.md#service-principal-object)weer gegeven.)

Er zijn twee typen beheerde identiteit:

- Een door het **systeem toegewezen beheerde identiteit** wordt rechtstreeks ingeschakeld voor een Azure-service-exemplaar.  De levens cyclus van een door het systeem toegewezen identiteit is uniek voor het Azure-service-exemplaar waarop het is ingeschakeld.
- Een **door de gebruiker toegewezen beheerde identiteit** wordt gemaakt als een zelfstandige Azure-resource. De identiteit kan worden toegewezen aan een of meer Azure-service-exemplaren en wordt onafhankelijk van de levens cyclus van die instanties beheerd.

Zie [Hoe worden beheerde identiteiten voor Azure-resources werken?](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)voor meer informatie over het verschil tussen beheerde identiteits typen.

## <a name="supported-scenarios-for-service-fabric-applications"></a>Ondersteunde scenario's voor Service Fabric toepassingen

Beheerde identiteiten voor Service Fabric worden alleen ondersteund in door Azure geïmplementeerde Service Fabric-clusters en alleen voor toepassingen die zijn geïmplementeerd als Azure-resources. Er kan geen identiteit worden toegewezen aan een toepassing die niet is geïmplementeerd als een Azure-resource. De ondersteuning voor beheerde identiteiten in een Azure Service Fabric-cluster bestaat uit twee fasen:

1. Een of meer beheerde identiteiten toewijzen aan de resource van de toepassing. aan een toepassing kunnen respectievelijk een door het systeem toegewezen identiteit en/of Maxi maal 32 door de gebruiker toegewezen identiteiten worden toegewezen.

2. In de definitie van de toepassing wijst u een van de identiteiten die aan de toepassing zijn toegewezen toe aan een afzonderlijke service die de toepassing omvat.

De door het systeem toegewezen identiteit van een toepassing is uniek voor die toepassing. een door de gebruiker toegewezen identiteit is een zelfstandige resource die kan worden toegewezen aan meerdere toepassingen. Binnen een toepassing kan één identiteit (of toegewezen systeem of gebruikers toegewezen) aan meerdere services van de toepassing worden toegewezen, maar aan elke afzonderlijke service kan slechts één identiteit worden toegewezen. Ten slotte moet aan een service expliciet een identiteit worden toegewezen om toegang te krijgen tot deze functie. Als gevolg hiervan kan de toewijzing van de identiteit van een toepassing aan de samenstellende Services in de toepassings isolatie worden uitgevoerd: een service mag alleen de identiteit gebruiken die eraan is toegewezen.  

Op dit moment worden de volgende scenario's ondersteund voor deze functie:

- Een nieuwe toepassing met een of meer services en een of meer toegewezen identiteiten implementeren

- Een of meer beheerde identiteiten toewijzen aan een bestaande (door Azure geïmplementeerde) toepassing om toegang te krijgen tot Azure-resources

De volgende scenario's worden niet ondersteund of worden niet aanbevolen. Houd er rekening mee dat deze acties mogelijk niet worden geblokkeerd, maar kan leiden tot storingen in uw toepassingen:

- De identiteiten die aan een toepassing zijn toegewezen, verwijderen of wijzigen. Als u wijzigingen moet aanbrengen, dient u afzonderlijke implementaties in te dienen om eerst een nieuwe identiteits toewijzing toe te voegen en vervolgens een eerder toegewezen account te verwijderen. Het verwijderen van een identiteit uit een bestaande toepassing kan ongewenste gevolgen hebben, zoals het verlaten van uw toepassing in een status die niet kan worden geüpgraded. Het is veilig om de toepassing te verwijderen als het verwijderen van een identiteit nood zakelijk is. Houd er rekening mee dat hiermee de door het systeem toegewezen identiteit (indien gedefinieerd) die aan de toepassing is gekoppeld, wordt verwijderd en alle koppelingen met de door de gebruiker toegewezen identiteiten die aan de toepassing zijn toegewezen, worden verwijderd.

- Service Fabric ondersteuning voor beheerde identiteiten is op dit moment niet geïntegreerd in de [AzureServiceTokenProvider](../key-vault/general/service-to-service-authentication.md).

## <a name="next-steps"></a>Volgende stappen

- [Een nieuw Azure Service Fabric-cluster implementeren met beheerde identiteits ondersteuning](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [Ondersteuning van beheerde identiteiten inschakelen in een bestaand Azure Service Fabric-cluster](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [Een Azure Service Fabric-toepassing implementeren met een door het systeem toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [Een Azure Service Fabric-toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [Maak gebruik van de beheerde identiteit van een Service Fabric toepassing vanuit service code](./how-to-managed-identity-service-fabric-app-code.md)
- [Een Azure Service Fabric-toepassing toegang verlenen tot andere Azure-resources](./how-to-grant-access-other-resources.md)
- [Toepassings geheimen declareren en gebruiken als KeyVaultReferences](./service-fabric-keyvault-references.md)
