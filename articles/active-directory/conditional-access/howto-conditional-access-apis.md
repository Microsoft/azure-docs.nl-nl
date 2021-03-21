---
title: Api's voor voorwaardelijke toegang en Power shell-Azure Active Directory
description: De Azure AD-Api's voor voorwaardelijke toegang en Power shell gebruiken voor het beheren van beleids regels zoals code
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: videor, jeevanb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d4dd6e64053af41d66e3accbb7fc9b21d951fc6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96860210"
---
# <a name="conditional-access-programmatic-access"></a>Voorwaardelijke toegang: programmatische toegang

Veel organisaties hebben uitgedruct in hun behoefte om zoveel mogelijk hun omgevingen als code te beheren. Met Microsoft Graph kunt u beleid voor voorwaardelijke toegang zoals elk ander code fragment in uw omgeving behandelen.

Microsoft Graph biedt een uniform programmeerbaar model dat organisaties kunnen gebruiken om te werken met gegevens in Microsoft 365, Windows 10 en Enterprise Mobility + Security. Zie het artikel [overzicht van Microsoft Graph](/graph/overview)voor meer informatie over Microsoft Graph.

![Een afbeelding met de primaire resources en relaties die deel uitmaken van de grafiek](./media/howto-conditional-access-apis/microsoft-graph.png)

De volgende voor beelden worden meegeleverd zonder ondersteuning. U kunt deze voor beelden gebruiken als basis voor hulp middelen in uw organisatie. 

Veel van de volgende voor beelden gebruiken hulpprogram ma's zoals [beheerde identiteiten](../managed-identities-azure-resources/overview.md), [Logic apps](../../logic-apps/logic-apps-overview.md), [OneDrive](https://www.microsoft.com/microsoft-365/onedrive/online-cloud-storage), [teams](https://www.microsoft.com/microsoft-365/microsoft-teams/group-chat-software/)en [Azure Key Vault](../../key-vault/general/overview.md).

## <a name="configure"></a>Configureren

### <a name="powershell"></a>PowerShell

Voor veel beheerders is Power shell al een hulp programma voor het begrijpen van scripts. In het volgende voor beeld ziet u hoe u de [Azure AD Power shell-module](https://www.powershellgallery.com/packages/AzureAD) gebruikt voor het beheren van beleid voor voorwaardelijke toegang.

- [Beleid voor voorwaardelijke toegang configureren met Azure AD Power shell-opdrachten](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/powershell)

### <a name="graph-api"></a>Graph API

Dit voor beeld toont de basis opties voor maken, lezen, bijwerken en verwijderen (ruw) die beschikbaar zijn in de Graph-Api's voor voorwaardelijke toegang. Het voor beeld bevat ook enkele JSON-sjablonen die u kunt gebruiken voor het maken van een voor beeld van een beleid.

- [Beleid voor voorwaardelijke toegang configureren met Microsoft Graph-API-aanroepen](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/graphapi)

### <a name="configure-using-templates"></a>Configureren met behulp van sjablonen

Gebruik Api's voor voorwaardelijke toegang voor het implementeren van beleid voor voorwaardelijke toegang in uw pre-productie omgeving met behulp van een sjabloon.

- [Beleid voor voorwaardelijke toegang configureren met Microsoft Graph API-sjablonen](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/templates)

## <a name="test"></a>Testen

In dit voor beeld wordt gewaakt implementatie procedures met goedkeurings werk stromen waarmee het beleid voor voorwaardelijke toegang kan worden gekopieerd van de ene omgeving naar de andere, zoals uw productie omgeving.

- [Beleid voor voorwaardelijke toegang bevorderen vanuit test omgevingen](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/02-test)

## <a name="deploy"></a>Implementeren

In dit voor beeld wordt een mechanisme geboden voor het uitvoeren van een gefaseerde implementatie van beleid voor voorwaardelijke toegang op uw gebruikers populatie, zodat u de ondersteunings impact en de problemen met de oplossing vroegtijdig kunt beheren.

- [Beleid voor voorwaardelijke toegang implementeren in productie omgevingen met goedkeurings werk stromen](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/03-deploy)

## <a name="monitor"></a>Monitor

Dit voor beeld biedt een mechanisme voor het bewaken van de wijzigingen van het beleid voor voorwaardelijke toegang gedurende een bepaalde periode en kan waarschuwingen activeren wanneer het sleutel beleid wordt gewijzigd.

- [Geïmplementeerde beleids regels voor voorwaardelijke toegang voor wijzigingen en trigger waarschuwingen bewaken](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/04-monitor)

## <a name="manage"></a>Beheren

### <a name="backup-and-restore"></a>Back-up en herstel

U automatiseert het maken en herstellen van beleids regels voor voorwaardelijke toegang met goed keuringen in teams met behulp van dit voor beeld.

- [Het back-up-en herstel proces van beleids regels voor voorwaardelijke toegang beheren met Microsoft Graph-API-aanroepen](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/01-backup-restore)

### <a name="emergency-access-accounts"></a>Accounts voor toegang in nood gevallen

Meerdere beheerders kunnen beleid voor voorwaardelijke toegang maken en kunnen verg eten om uw [accounts voor toegang in nood gevallen](../roles/security-emergency-access.md) toe te voegen als een uitsluiting van die beleids regels. In dit voor beeld wordt ervoor gezorgd dat alle beleids regels worden bijgewerkt met de aangewezen accounts voor toegang in nood gevallen.

- [De toewijzing van accounts voor toegang in nood gevallen met behulp van Microsoft Graph API-aanroepen beheren in beleid voor voorwaardelijke toegang](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/02-emergency-access)

### <a name="contingency-planning"></a>Plannen voor onvoorziene gebeurtenissen

Dingen werken niet altijd op de gewenste manier, wanneer dat gebeurt, hebt u een manier om terug te gaan naar een staat waar werk kan worden voortgezet. Het volgende voor beeld biedt een manier om uw beleids regels terug te zetten naar een bekend goed nood geval en andere beleids regels voor voorwaardelijke toegang uit te scha kelen.

- [De activering van beleids regels voor voorwaardelijke toegang met behulp van Microsoft Graph-API-aanroepen beheren](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/03-contingency)

## <a name="community-contribution"></a>Community-bijdrage

Deze voor beelden zijn beschikbaar in onze [github-opslag plaats](https://github.com/Azure-Samples/azure-ad-conditional-access-apis). We zijn blij met de ondersteuning van Community-bijdragen met uitgebreide GitHub-problemen en pull-aanvragen.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Microsoft Graph](/graph/overview)

- [API voor voorwaardelijke toegang](/graph/api/resources/conditionalaccesspolicy)

- [Benoemde locatie-API](/graph/api/resources/namedlocation)
