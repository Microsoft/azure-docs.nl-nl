---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 34455ba61095bfa65c9c82d0829bdc54b2b897e4
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090814"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[De toepassingsdefinitie voor de beheerde toepassing moet gebruikmaken van een door de klant verstrekt opslagaccount](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633) |Gebruik uw eigen opslagaccount om de toepassingsdefinitiegegevens te beheren wanneer dit een regelgevings- of compliance-vereiste is. U kunt ervoor kiezen om de definitie van de beheerde toepassing op te slaan in een opslagaccount dat u tijdens het maken hebt opgegeven, zodat u de locatie en toegang volledig zelf kunt beheren om te voldoen aan uw wettelijke compliance-vereisten. |controleren, weigeren, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[Koppelingen voor een beheerde toepassing implementeren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |Implementeert een koppelingsbron die geselecteerde resourcetypen koppelt aan de opgegeven beheerde toepassing.  Deze beleidsimplementatie biedt geen ondersteuning voor geneste resourcetypen. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |
