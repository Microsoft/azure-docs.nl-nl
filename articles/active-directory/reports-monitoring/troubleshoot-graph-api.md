---
title: Problemen oplossen in de API voor Azure Active Directory rapportage | Microsoft Docs
description: Biedt een oplossing voor fouten tijdens het aanroepen van Azure Active Directory rapportage-Api's.
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
ms.openlocfilehash: abc8badf261e631dd6ceb7af9a6a0cb3676ae25d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017590"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Problemen in Azure Active Directory rapportage-API oplossen

In dit artikel vindt u een overzicht van de algemene fout berichten die u kunt uitvoeren tijdens het openen van activiteiten rapporten met behulp van de Microsoft Graph-API en de stappen voor de oplossing.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 interne HTTP-server fout bij het openen van Microsoft Graph v2-eind punt

Het Microsoft Graph v2-eind punt wordt momenteel niet ondersteund. Zorg ervoor dat u toegang hebt tot de activiteiten logboeken met behulp van het Microsoft Graph v1-eind punt.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Fout: de Tenant is geen B2C of de Tenant heeft geen Premium-licentie

Voor toegang tot aanmeldings rapporten is een licentie voor Azure Active Directory Premium 1 (P1) vereist. Als dit fout bericht wordt weer gegeven tijdens het openen van de aanmeldingen, moet u ervoor zorgen dat uw Tenant is gelicentieerd met een Azure AD P1-licentie.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Fout: de gebruiker bevindt zich niet in de toegestane rollen 

Als dit fout bericht wordt weer gegeven tijdens het openen van controle Logboeken of-aanmeldingen met behulp van de API, moet u ervoor zorgen dat uw account deel uitmaakt van de rol **beveiligings lezer** of **rapport lezer** in uw Azure Active Directory Tenant. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Fout: de machtiging voor het lezen van de Directory gegevens van de toepassing voor AAD ontbreekt 

Volg de stappen in de [vereisten voor toegang tot de API voor Azure Active Directory rapportage](howto-configure-prerequisites-for-reporting-api.md) om ervoor te zorgen dat uw toepassing wordt uitgevoerd met de juiste set machtigingen. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Fout: er ontbreekt een machtiging voor de toepassing Microsoft Graph-API voor het lezen van alle audit logboek gegevens

Volg de stappen in de [vereisten voor toegang tot de API voor Azure Active Directory rapportage](howto-configure-prerequisites-for-reporting-api.md) om ervoor te zorgen dat uw toepassing wordt uitgevoerd met de juiste set machtigingen. 

## <a name="next-steps"></a>Volgende stappen

[De controle-API-verwijzing gebruiken](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
 [De API-naslag informatie voor aanmeld activiteiten gebruiken](/graph/api/resources/signin?view=graph-rest-beta)