---
title: Aan de slag met de Azure AD-rapportage-API | Microsoft Docs
description: Aan de slag met de rapportage AZURE ACTIVE DIRECTORY-API
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
ms.openlocfilehash: 8e5a095c87e46839c7c120bdd6d8db1595164e57
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532531"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Aan de slag met Azure Active Directory rapportage-API

Azure Active Directory biedt u diverse rapporten, met nuttige informatie voor toepassingen zoals SIEM-systemen, controle- en business intelligence hulpprogramma's. [](overview-reports.md) 

Met behulp van Microsoft Graph API voor Azure AD-rapporten kunt u programmatische toegang krijgen tot de gegevens via een set OP REST gebaseerde API's. U kunt deze API's vanuit een groot aantal computertalen en hulpprogramma's aanroepen.

In dit artikel vindt u een overzicht van de rapportage-API, inclusief manieren om er toegang toe te krijgen.

Als u problemen hebt, [bekijkt u hoe u ondersteuning kunt krijgen voor Azure Active Directory.](../fundamentals/active-directory-troubleshooting-support-howto.md)

## <a name="prerequisites"></a>Vereisten

Voor toegang tot de rapportage-API, met of zonder tussenkomst van de gebruiker, moet u het volgende doen:

1. Rollen toewijzen (Beveiligingslezer, Beveiligingsbeheerder, Globale beheerder)
2. Een toepassing registreren
3. Machtigingen verlenen
4. Configuratie-instellingen verzamelen

Zie de vereisten voor toegang tot de [rapportage-API](howto-configure-prerequisites-for-reporting-api.md)Azure Active Directory voor gedetailleerde instructies. 

## <a name="api-endpoints"></a>API-eindpunten 

Het Microsoft Graph API-eindpunt voor auditlogboeken is en `https://graph.microsoft.com/v1.0/auditLogs/directoryAudits` Microsoft Graph API-eindpunt voor aanmeldingen is `https://graph.microsoft.com/v1.0/auditLogs/signIns` . Zie de [audit-API-verwijzing](/graph/api/resources/directoryaudit) en [aanmeldings-API-verwijzing voor meer informatie.](/graph/api/resources/signIn)

U kunt de [API voor risicodetectie van Identiteitsbeveiliging](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true) gebruiken om programmatische toegang te krijgen tot beveiligingsdetecties met behulp van Microsoft Graph. Zie Aan de slag met Azure Active Directory Identity Protection en Microsoft Graph voor [meer Microsoft Graph.](../identity-protection/howto-identity-protection-graph-api.md) 
  
U kunt ook de API voor [inrichtingslogboeken gebruiken](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true) om programmatische toegang te krijgen tot inrichtingsgebeurtenissen in uw tenant. 

## <a name="apis-with-microsoft-graph-explorer"></a>API's met Microsoft Graph Explorer

U kunt de Microsoft Graph [explorer gebruiken om](https://developer.microsoft.com/graph/graph-explorer) uw aanmeldings- en audit-API-gegevens te controleren. Meld u aan bij uw account met beide aanmeldingsknoppen in de gebruikersinterface van Graph Explorer en stel de machtigingen **AuditLog.Read.All** en **Directory.Read.All** in voor uw tenant, zoals wordt weergegeven.   

![Grafiekverkenner](./media/concept-reporting-api/graph-explorer.png)

![Gebruikersinterface voor machtigingen wijzigen](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Certificaten gebruiken voor toegang tot de Rapportage-API van Azure AD 

Gebruik de Rapportage-API van Azure AD met certificaten als u rapportagegegevens wilt ophalen zonder tussenkomst van de gebruiker.

Zie Gegevens verkrijgen met behulp [van de Rapportage-API](tutorial-access-api-with-certificates.md)van Azure AD met certificaten voor gedetailleerde instructies.

## <a name="next-steps"></a>Volgende stappen

 * [Vereisten voor toegang tot rapportage-API](howto-configure-prerequisites-for-reporting-api.md) 
 * [Gegevens ophalen met de rapportage-API van Azure AD met certificaten](tutorial-access-api-with-certificates.md)
 * [Fouten in de rapportage-API van Azure AD oplossen](troubleshoot-graph-api.md)