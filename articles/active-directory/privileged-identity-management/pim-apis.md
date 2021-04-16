---
title: Microsoft Graph-API's voor PIM (preview) - Azure AD | Microsoft Docs
description: Bevat informatie over het gebruik van de Microsoft Graph-API's voor Azure AD PIM (Privileged Identity Management) (preview).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: how-to
ms.date: 01/02/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce1900ca1ca04923f98642f0783925f2dd06619b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533569"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>Microsoft Graph-API's voor PIM (Privileged Identity Management) (preview)

U kunt Privileged Identity Management-taken uitvoeren met behulp van de [Microsoft Graph-API's](/graph/overview) voor Azure Active Directory. In dit artikel worden enkele belangrijke concepten beschreven bij het gebruik van de Microsoft Graph-API's voor Privileged Identity Management.

Zie de [naslag bij Azure AD Privileged Identity Management-API](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta&preserve-view=true) voor meer informatie over de Microsoft Graph-API's.

> [!IMPORTANT]
> API's onder de /bèta-versie in Microsoft Graph zijn beschikbaar als preview en kunnen worden gewijzigd. Het gebruik van deze API's in productie-apps wordt niet ondersteund.

## <a name="required-permissions"></a>Vereiste machtigingen

Voor het aanroepen van de Microsoft Graph-API's voor Privileged Identity Management, hebt u **een of meer** van de volgende machtigingen nodig:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Machtigingen instellen

Toepassingen die de Microsoft Graph-API's voor Privileged Identity Management willen aanroepen, moeten beschikken over de vereiste machtigingen. De eenvoudigste manier om de vereiste machtigingen op te geven, is door het [toestemmingsframework van Azure AD-](../develop/consent-framework.md) te gebruiken.

### <a name="set-permissions-in-graph-explorer"></a>Machtigingen instellen in Graph Explorer

Als u Graph Explorer gebruikt om uw aanroepen te testen, kunt u de machtigingen opgeven in het hulpprogramma.

1. Meld u als globale beheerder aan bij [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

1. Klik op **Machtigingen wijzigen**.

    ![Schermopname van de pagina 'Graph Explorer' met de actie 'Machtigingen wijzigen' geselecteerd.](./media/pim-apis/graph-explorer.png)

1. Schakel de selectievakjes in naast de machtigingen die u wilt opnemen. `PrivilegedAccess.ReadWrite.AzureAD` is nog niet beschikbaar in Graph Explorer.

    ![Graph Explorer - machtigingen wijzigen](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Klik op **Machtigingen wijzigen** om de wijzigingen in de machtigingen toe te passen.

## <a name="next-steps"></a>Volgende stappen

- [Naslag bij Azure AD Privileged Identity Management-API](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta&preserve-view=true)
