---
title: Aan de slag met de rapportage-API voor Azure AD | Microsoft Docs
description: Aan de slag met de API voor Azure Active Directory rapportage
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/21/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6ef122cd36c3d4e9135876647075f22158df0d1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653232"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Aan de slag met de API voor Azure Active Directory rapportage

Azure Active Directory biedt u diverse [rapporten](overview-reports.md), met nuttige informatie voor toepassingen, zoals Siem-systemen, controle en Business Intelligence-hulpprogram ma's. 

Met de Microsoft Graph-API voor Azure AD-rapporten kunt u programmatisch toegang krijgen tot de gegevens via een set op REST gebaseerde Api's. U kunt deze API's vanuit een groot aantal computertalen en hulpprogramma's aanroepen.

Dit artikel bevat een overzicht van de rapportage-API, inclusief manieren om toegang te krijgen tot het rapport.

Zie [ondersteuning voor Azure Active Directory krijgen](../fundamentals/active-directory-troubleshooting-support-howto.md)als u problemen ondervindt.

## <a name="prerequisites"></a>Vereisten

Voor toegang tot de rapportage-API, met of zonder tussen komst van de gebruiker, moet u het volgende doen:

1. Rollen toewijzen (beveiligings lezer, beveiligings beheerder, globale beheerder)
2. Een toepassing registreren
3. Machtigingen verlenen
4. Configuratie-instellingen verzamelen

Zie voor gedetailleerde instructies de [vereisten voor toegang tot de Azure Active Directory rapportage-API](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="api-endpoints"></a>API-eind punten 

Het Microsoft Graph API-eind punt voor audit Logboeken is `https://graph.microsoft.com/v1.0/auditLogs/directoryAudits` en het Microsoft Graph API-eind punt voor aanmeldingen is `https://graph.microsoft.com/v1.0/auditLogs/signIns` . Zie de naslag informatie over de [controle-API](/graph/api/resources/directoryaudit) en [aanmeldings-API](/graph/api/resources/signIn)voor meer gegevens.

U kunt de [API voor risico detectie van identiteits beveiliging](/graph/api/resources/identityriskevent?view=graph-rest-beta) gebruiken om programmatisch toegang te krijgen tot beveiligings detecties met behulp van Microsoft Graph. Zie [aan de slag met Azure Active Directory Identity Protection en Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md)voor meer informatie. 
  
U kunt ook de API voor het [inrichtings logboek](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta) gebruiken om programmatisch toegang te krijgen tot het inrichtings gebeurtenissen in uw Tenant. 

## <a name="apis-with-microsoft-graph-explorer"></a>Api's met Microsoft Graph Explorer

U kunt de [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) gebruiken om uw aanmeld-en controle-API-gegevens te verifiëren. Meld u aan bij uw account met behulp van beide aanmeld knoppen in de gebruikers interface van Graph Explorer en stel **AuditLog. Read. all** en **Directory. Read. all** in voor uw Tenant, zoals wordt weer gegeven.   

![Grafiekverkenner](./media/concept-reporting-api/graph-explorer.png)

![Machtigingen voor gebruikers interface wijzigen](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Certificaten gebruiken voor toegang tot de Azure AD-rapportage-API 

Gebruik de rapportage-API van Azure AD met certificaten als u rapportage gegevens wilt ophalen zonder tussen komst van de gebruiker.

Zie [gegevens ophalen met de rapportage-API van Azure AD met certificaten](tutorial-access-api-with-certificates.md)voor gedetailleerde instructies.

## <a name="next-steps"></a>Volgende stappen

 * [Vereisten voor toegang tot de rapportage-API](howto-configure-prerequisites-for-reporting-api.md) 
 * [Gegevens ophalen met de rapportage-API van Azure AD met certificaten](tutorial-access-api-with-certificates.md)
 * [Fouten in de rapportage-API van Azure AD oplossen](troubleshoot-graph-api.md)