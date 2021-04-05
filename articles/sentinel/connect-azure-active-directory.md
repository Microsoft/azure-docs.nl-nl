---
title: Azure Active Directory gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verzamelen van gegevens van Azure Active Directory en het streamen van Azure AD-aanmeld-, audit-en provisioning-Logboeken in azure Sentinel.
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
ms.openlocfilehash: f8931fedb380cf81d72b7b5280a5795498daaa57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99251978"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-azure-sentinel"></a>Azure Active Directory (Azure AD)-gegevens verbinden met Azure Sentinel

U kunt de ingebouwde connector van Azure Sentinel gebruiken om gegevens van [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) te verzamelen en deze in de Azure-Sentinel te streamen. Met de connector kunt u de volgende logboek typen streamen:

- [**Aanmeld logboeken**](../active-directory/reports-monitoring/concept-all-sign-ins.md), die informatie bevatten over [interactieve gebruikers aanmeldingen](../active-directory/reports-monitoring/concept-all-sign-ins.md#user-sign-ins) waarbij een gebruiker een verificatie factor biedt.

    De Azure AD-connector bevat nu de volgende drie aanvullende categorieën aanmeldings logboeken, die momenteel als **Preview-versie** beschikbaar zijn:
    
    - [**Niet-interactieve aanmeldings logboeken van gebruikers**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins), die informatie bevatten over aanmeldingen die zijn uitgevoerd door een client namens een gebruiker zonder interactie of verificatie factor van de gebruiker.
    
    - [**Logboeken met Service-Principal-aanmelding**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins), die informatie bevatten over aanmeldingen per app en service-principals die geen gebruik maken van een gebruiker. In deze aanmeldingen biedt de app of service een referentie voor het verifiëren of openen van bronnen.
    
    - [**Aanmeldings logboeken voor beheerde identiteiten**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins), die informatie bevatten over aanmeldingen door Azure-resources met geheimen die door Azure worden beheerd. Zie [Wat zijn beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.

- [**Audit logboeken**](../active-directory/reports-monitoring/concept-audit-logs.md), die informatie bevatten over systeem activiteiten die betrekking hebben op gebruikers-en groeps beheer, beheerde toepassingen en Directory-activiteiten.

- Het [**inrichten van Logboeken**](../active-directory/reports-monitoring/concept-provisioning-logs.md) (ook in **Preview**), die informatie over de systeem activiteit bevatten over gebruikers, groepen en rollen die worden ingericht door de Azure AD-inrichtings service. 

> [!IMPORTANT]
> Zoals hierboven aangegeven, zijn enkele van de beschik bare logboek typen momenteel als **Preview-versie** beschikbaar. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.
## <a name="prerequisites"></a>Vereisten

- Elke Azure AD-licentie (Free/O365/P1/P2) is voldoende voor het opnemen van aanmeldings Logboeken in azure Sentinel. Extra kosten per gigabyte kunnen van toepassing zijn op Azure Monitor (Log Analytics) en Azure Sentinel.

- Aan uw gebruiker moet de rol Azure Sentinel contributor worden toegewezen in de werk ruimte.

- Aan uw gebruiker moet de rol van globale beheerder of beveiligings beheerder zijn toegewezen voor de Tenant waarvan u de logboeken wilt streamen.

- Uw gebruiker moet lees-en schrijf machtigingen hebben voor de diagnostische instellingen van Azure AD om de verbindings status te kunnen zien. 

## <a name="connect-to-azure-active-directory"></a>Verbinding maken met Azure Active Directory

1. Selecteer in azure Sentinel **Data connectors** in het navigatie menu.

1. Selecteer in de galerie data connectors de optie **Azure Active Directory** en selecteer vervolgens **pagina connector openen**.

1. Schakel de selectie vakjes in naast de logboek typen die u wilt streamen naar Azure Sentinel (zie hierboven) en klik op **verbinden**.

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
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
