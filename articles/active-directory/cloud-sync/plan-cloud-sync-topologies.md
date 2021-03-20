---
title: Azure AD Connect Cloud-synchronisatie-topologieën en-scenario's die worden ondersteund
description: Meer informatie over verschillende topologieën van on-premises en Azure Active Directory (Azure AD) die gebruikmaken van Azure AD Connect Cloud synchronisatie.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd14ed8d149cdc5296229c52ceb74afb2ce7b23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613243"
---
# <a name="azure-ad-connect-cloud-sync-supported-topologies-and-scenarios"></a>Azure AD Connect Cloud-synchronisatie-topologieën en-scenario's die worden ondersteund
In dit artikel worden verschillende topologieën voor on-premises en Azure Active Directory (Azure AD) beschreven die gebruikmaken van Azure AD Connect Cloud synchronisatie. Dit artikel bevat alleen ondersteunde configuraties en scenario's.

> [!IMPORTANT]
> Micro soft biedt geen ondersteuning voor het wijzigen of uitvoeren van Azure AD Connect Cloud synchronisatie buiten de configuraties of acties die formeel zijn gedocumenteerd. Een van deze configuraties of acties kan leiden tot een inconsistente of niet-ondersteunde status van Azure AD Connect Cloud synchronisatie. Als gevolg hiervan kan micro soft geen technische ondersteuning bieden voor dergelijke implementaties.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Wat u moet weten over alle scenario's en topologieën
Hier volgt een lijst met informatie die u moet onthouden wanneer u een oplossing selecteert.

- Gebruikers en groepen moeten uniek worden geïdentificeerd in alle forests
- Vergelijking tussen forests vindt niet plaats met Cloud synchronisatie
- Een gebruiker of groep mag slechts één keer worden weer gegeven in alle forests
- Het bron anker voor objecten wordt automatisch gekozen.  Er wordt gebruikgemaakt van MS-DS-ConsistencyGuid indien aanwezig, anders wordt ObjectGUID gebruikt.
- U kunt het kenmerk dat wordt gebruikt voor het bron anker niet wijzigen.

## <a name="single-forest-single-azure-ad-tenant"></a>Eén forest, één Azure AD-Tenant
![Diagram waarin de topologie voor één forest en één Tenant wordt weer gegeven.](media/tutorial-single-forest/diagram-2.png)

De eenvoudigste topologie is één on-premises forest met een of meer domeinen en één Azure AD-Tenant.  Zie [zelf studie: Eén forest met één Azure AD-Tenant](tutorial-single-forest.md) voor een voor beeld van dit scenario.


## <a name="multi-forest-single-azure-ad-tenant"></a>Meerdere forests, één Azure AD-Tenant
![Topologie voor een multi-forest en één Tenant](media/plan-cloud-provisioning-topologies/multi-forest-2.png)

Een algemene topologie is een meerdere AD-forests met een of meer domeinen en één Azure AD-Tenant.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Bestaand forest met Azure AD Connect, nieuw forest met Cloud inrichting
![Diagram waarin de topologie voor een bestaand forest en een nieuw forest wordt weer gegeven.](media/tutorial-existing-forest/existing-forest-new-forest-2.png)

Dit scenario is een topologie die vergelijkbaar is met het scenario met meerdere forests, maar dit is een bestaande Azure AD Connect omgeving en brengt vervolgens een nieuw forest met Azure AD Connect Cloud synchronisatie.  Zie [zelf studie: een bestaand forest met één Azure AD-Tenant](tutorial-existing-forest.md) voor een voor beeld van dit scenario.

## <a name="piloting-azure-ad-connect-cloud-sync-in-an-existing-hybrid-ad-forest"></a>Piloten Azure AD Connect Cloud synchronisatie in een bestaand hybride AD-forest
![Topologie voor één forest en één Tenant ](media/tutorial-migrate-aadc-aadccp/diagram-2.png) het test scenario omvat het bestaan van zowel Azure AD Connect als Azure AD Connect Cloud synchronisatie in hetzelfde forest en de gebruikers en groepen dienovereenkomstig. Opmerking: een object moet binnen het bereik van slechts een van de hulpprogram ma's zijn. 

Zie voor een voor beeld van dit scenario [zelf studie: Pilot Azure AD Connect Cloud Sync in een bestaand GESYNCHRONISEERD AD-forest](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect--cloudsynchronisatie?](what-is-cloud-sync.md)

