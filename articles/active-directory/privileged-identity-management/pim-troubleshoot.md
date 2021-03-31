---
title: Problemen oplossen met Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Informatie over het oplossen van systeem fouten met rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcbebcb32e912abdf0112007c743c6890fae36e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92372392"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Problemen met Privileged Identity Management oplossen

Ondervindt u een probleem met Privileged Identity Management (PIM) in Azure Active Directory (Azure AD)? De volgende informatie kan u helpen bij het ophalen van dingen.

## <a name="access-to-azure-resources-denied"></a>Toegang tot Azure-resources geweigerd

### <a name="problem"></a>Probleem

Als een actieve eigenaar of beheerder van de gebruikers toegang voor een Azure-resource kunt u uw resource in Privileged Identity Management zien, maar u kunt geen acties uitvoeren zoals het maken van een in aanmerking komende toewijzing of het weer geven van een lijst met roltoewijzingen op de pagina overzicht van resources. Een van deze acties resulteert in een autorisatie fout.

### <a name="cause"></a>Oorzaak

Dit probleem kan zich voordoen wanneer de rol beheerder voor gebruikers toegang voor de PIM-Service-Principal per ongeluk is verwijderd uit het abonnement. De Privileged Identity Management-service kan alleen toegang krijgen tot Azure-resources als de MS-PIM-Service-Principal altijd wordt toegewezen aan de [rol van beheerder voor gebruikers toegang](../../role-based-access-control/built-in-roles.md#user-access-administrator) via het Azure-abonnement.

### <a name="resolution"></a>Oplossing

Wijs de rol beheerder voor gebruikers toegang toe aan de privileged Identity Management Service Principal Name (MS-PIM) op abonnements niveau. Deze toewijzing moet de privileged Identity Management-service toestaan om toegang te krijgen tot de Azure-resources. De rol kan worden toegewezen op een niveau van de beheer groep of op het abonnements niveau, afhankelijk van uw vereisten. Zie [een toepassing toewijzen aan een rol](../develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)voor meer informatie over service-principals.

## <a name="next-steps"></a>Volgende stappen

- [Licentievereisten voor het gebruik van Privileged Identity Management](subscription-requirements.md)
- [Bevoegde toegang beveiligen voor hybride implementaties en cloudimplementaties in Azure AD](../roles/security-planning.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Privileged Identity Management implementeren](pim-deployment-plan.md)