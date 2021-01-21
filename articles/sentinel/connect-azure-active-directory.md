---
title: Azure Active Directory gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verzamelen van gegevens van Azure Active Directory en het streamen van Azure AD-aanmeld logboeken en audit Logboeken in azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: e84484990725b0c39b132aead51e9b01dbb7e7ef
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632288"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Verbinding maken met gegevens van Azure Active Directory (Azure AD)

U kunt de ingebouwde connector van Azure Sentinel gebruiken om gegevens van [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) te verzamelen en deze in de Azure-Sentinel te streamen. Met de connector kunt u [aanmeldings logboeken](../active-directory/reports-monitoring/concept-sign-ins.md) en [audit logboeken](../active-directory/reports-monitoring/concept-audit-logs.md)streamen.

## <a name="prerequisites"></a>Vereisten

- Elke Azure AD-licentie (Free/O365/P1/P2) is voldoende voor het opnemen van aanmeldings Logboeken in azure Sentinel. Extra kosten per gigabyte kunnen van toepassing zijn op Azure Monitor (Log Analytics) en Azure Sentinel.

- Aan uw gebruiker moet de rol Azure Sentinel contributor worden toegewezen in de werk ruimte.

- Aan uw gebruiker moet de rol van globale beheerder of beveiligings beheerder zijn toegewezen voor de Tenant waarvan u de logboeken wilt streamen.

- Uw gebruiker moet lees-en schrijf machtigingen hebben voor de diagnostische instellingen van Azure AD om de verbindings status te kunnen zien. 

## <a name="connect-to-azure-active-directory"></a>Verbinding maken met Azure Active Directory

1. Selecteer in azure Sentinel **Data connectors** in het navigatie menu.

1. Selecteer in de galerie data connectors de optie **Azure Active Directory** en selecteer vervolgens **pagina connector openen**.

1. Schakel de selectie vakjes in naast de logboek typen die u wilt streamen naar Azure Sentinel en klik op **verbinden**. Dit zijn de logboek typen waaruit u kunt kiezen:

    - Aanmeldingslogboeken
    - Auditlogboeken
    - Logboeken voor niet-interactieve gebruikers aanmelding
    - Logboeken voor Service-Principal-aanmelding
    - Logboeken voor beheerde identiteits aanmelding
    - Inrichtingslogboeken

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken**, onder de sectie **LogManagement** , in de volgende tabellen:

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Als u de Azure AD-logboeken wilt doorzoeken, voert u de relevante tabel naam boven in het query venster in.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Azure Active Directory kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
