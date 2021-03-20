---
title: API-vereisten-Azure Marketplace
description: Vereisten voor het gebruik van de Cloud Partner-portal-Api's.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/23/2020
ms.openlocfilehash: 374cc3398d1037b19ceddcbdaafd333f643e44ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91261155"
---
# <a name="api-prerequisites"></a>API-vereisten

> [!NOTE]
> De Cloud Partner-portal-Api's zijn geïntegreerd in en blijven werken in het partner centrum. De overgang introduceert kleine wijzigingen. Controleer de wijzigingen die worden vermeld in [Cloud Partner-Portal API-referentie](cloud-partner-portal-api-overview.md) om ervoor te zorgen dat uw code blijft werken na het overstappen naar het partner centrum. Gebruik alleen CCP-Api's voor bestaande producten die al zijn geïntegreerd vóór overgang naar partner centrum. nieuwe producten moeten de indienings-Api's van partner Center gebruiken.

U hebt twee vereiste programmatische elementen nodig om Cloud Partner-portal-Api's te kunnen gebruiken: een Service-Principal en een toegangs token voor Azure Active Directory (Azure AD).

## <a name="create-service-principal-in-azure-active-directory-tenant"></a>Een service-principal maken in Azure Active Directory Tenant

Eerst moet u een service-principal maken in uw Azure AD-Tenant. Aan deze Tenant wordt een eigen set machtigingen toegewezen in de Cloud Partner-portal. Met uw code worden Api's aangeroepen die gebruikmaken van deze Tenant in plaats van uw persoonlijke referenties. Zie [How to: de portal gebruiken om een Azure AD-toepassing en Service-Principal te maken voor toegang tot resources](../active-directory/develop/howto-create-service-principal-portal.md)voor een volledige uitleg over het maken van een service-principal.

## <a name="add-service-principal-to-your-account"></a>Service-Principal toevoegen aan uw account

Nu u de Service-Principal in uw Tenant hebt gemaakt, kunt u deze als een gebruiker aan uw partner centrum-Portal account toevoegen. Net als bij een gebruiker kan de service-principal een eigenaar of een bijdrager aan de portal zijn. Zie **volgende stappen** voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure AD-toepassingen beheren](partner-center-portal/manage-account.md#manage-azure-ad-applications).
