---
title: Fouten in api Azure Active Directory rapportage-API-| Microsoft Docs
description: Biedt u een oplossing voor fouten tijdens het aanroepen van Azure Active Directory Reporting API's.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 445514297550210d80cd50895201d1129fac4f20
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533698"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Fouten in de rapportage Azure Active Directory-API oplossen

In dit artikel vindt u een overzicht van de algemene foutberichten die kunnen worden weergegeven tijdens het openen van activiteitenrapporten met behulp van de Microsoft Graph API en de stappen voor de oplossing.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 INTERNE HTTP-serverfout bij het openen van Microsoft Graph V2-eindpunt

Momenteel wordt het V2 Microsoft Graph-eindpunt niet ondersteund. Zorg ervoor dat u toegang hebt tot de activiteitenlogboeken met behulp van Microsoft Graph v1-eindpunt.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Fout: Geen van beide tenants is B2C of tenant heeft geen Premium-licentie

Voor toegang tot aanmeldingsrapporten is een Azure Active Directory Premium 1-licentie (P1) vereist. Als u dit foutbericht ziet tijdens het openen van aanmeldingen, moet u ervoor zorgen dat uw tenant een licentie heeft met een Azure AD P1-licentie.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Fout: Gebruiker heeft niet de toegestane rollen 

Als u dit foutbericht ziet tijdens het openen van auditlogboeken of aanmeldingen met  behulp van  de API, moet u ervoor zorgen dat uw account deel uitmaakt van de rol Beveiligingslezer of Rapportlezer in uw Azure Active Directory tenant. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Fout: AAD-machtiging 'Mapgegevens lezen' ontbreekt voor toepassing 

Volg de stappen in Vereisten voor toegang tot de [rapportage-API Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) om ervoor te zorgen dat uw toepassing wordt uitgevoerd met de juiste set machtigingen. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Fout: Toepassing ontbreekt Microsoft Graph API-machtiging 'Alle auditlogboekgegevens lezen'

Volg de stappen in Vereisten voor toegang tot de [rapportage-API Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) om ervoor te zorgen dat uw toepassing wordt uitgevoerd met de juiste set machtigingen. 

## <a name="next-steps"></a>Volgende stappen

[De audit-API-verwijzing gebruiken](/graph/api/resources/directoryaudit) 
 [Gebruik de API-verwijzing voor het rapport voor aanmeldingsactiviteiten](/graph/api/resources/signin)