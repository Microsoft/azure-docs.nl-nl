---
title: Verbinding Azure Active Directory gegevens maken met Azure Sentinel | Microsoft Docs
description: Informatie over het verzamelen van gegevens van Azure Active Directory en het streamen van aanmeldings-, audit- en inrichtingslogboeken van Azure AD naar Azure Sentinel.
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
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: d2cfc2d592c24cf5d15a489ea4bde36ea3c2f863
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872367"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-azure-sentinel"></a>Verbinding Azure Active Directory (Azure AD)-gegevens met Azure Sentinel

U kunt de Azure Sentinel connector van de Azure Active Directory gebruiken [](../active-directory/fundamentals/active-directory-whatis.md) om deze gegevens te verzamelen en naar Azure Sentinel. Met de connector kunt u de volgende logboektypen streamen:

- [**Aanmeldingslogboeken,**](../active-directory/reports-monitoring/concept-all-sign-ins.md)die informatie bevatten over [interactieve aanmeldingen](../active-directory/reports-monitoring/concept-all-sign-ins.md#user-sign-ins) van gebruikers waarbij een gebruiker een verificatiefactor biedt.

    De Azure AD-connector bevat nu de volgende drie extra categorieën aanmeldingslogboeken, die momenteel in **preview zijn:**
    
    - [**Niet-interactieve**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins)aanmeldingslogboeken van gebruikers, die informatie bevatten over aanmeldingen die worden uitgevoerd door een client namens een gebruiker zonder tussenkomst of verificatiefactor van de gebruiker.
    
    - [**Aanmeldingslogboeken van de service-principal,**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins)die informatie bevatten over aanmeldingen door apps en service-principals waarbij geen gebruiker betrokken is. Bij deze aanmeldingen verstrekt de app of service namens zichzelf een referentie om resources te verifiëren of te openen.
    
    - [**Aanmeldingslogboeken voor**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins)beheerde identiteiten, die informatie bevatten over aanmeldingen door Azure-resources met geheimen die worden beheerd door Azure. Zie Wat zijn beheerde [identiteiten voor Azure-resources? voor meer informatie.](../active-directory/managed-identities-azure-resources/overview.md)

- [**Auditlogboeken,**](../active-directory/reports-monitoring/concept-audit-logs.md)die informatie bevatten over systeemactiviteiten met betrekking tot gebruikers- en groepsbeheer, beheerde toepassingen en directory-activiteiten.

- [**Inrichtingslogboeken**](../active-directory/reports-monitoring/concept-provisioning-logs.md) (ook in **PREVIEW)** die informatie over systeemactiviteit bevatten over gebruikers, groepen en rollen die zijn ingericht door de Azure AD-inrichtingsservice. 

> [!IMPORTANT]
> Zoals hierboven is aangegeven, zijn enkele van de beschikbare logboektypen momenteel beschikbaar als **PREVIEW.** Zie de aanvullende gebruiksvoorwaarden voor [Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voorwaarden die van toepassing zijn op Azure-functies die bètaversies of preview-functies hebben of die nog niet algemeen beschikbaar zijn.
## <a name="prerequisites"></a>Vereisten

- Een Azure Active Directory P1- of P2-licentie is vereist om aanmeldingslogboeken op te nemen in Azure Sentinel. Een Azure AD-licentie (Free/O365/P1/P2) is voldoende om de andere logboektypen op te nemen. Er kunnen extra kosten per gigabyte worden toegepast voor Azure Monitor (Log Analytics) en Azure Sentinel.

- Aan uw gebruiker moet de rol inzender Azure Sentinel de werkruimte zijn toegewezen.

- Aan uw gebruiker moeten de rollen Globale beheerder of Beveiligingsbeheerder zijn toegewezen voor de tenant van waaruit u de logboeken wilt streamen.

- Uw gebruiker moet lees- en schrijfmachtigingen hebben voor de diagnostische azure AD-instellingen om de verbindingsstatus te kunnen zien. 

## <a name="connect-to-azure-active-directory"></a>Verbinding maken met Azure Active Directory

1. Selecteer Azure Sentinel **gegevensconnectoren in** het navigatiemenu.

1. Selecteer in de galerie met gegevensconnectoren **Azure Active Directory** selecteer vervolgens **Pagina connector openen.**

1. Markeer de selectievakjes naast de logboektypen die u wilt streamen naar Azure Sentinel (zie hierboven) en klik op **Verbinden.**

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat er een verbinding tot stand is gebracht, worden de gegevens weergegeven in **Logboeken,** onder de **sectie LogManagement,** in de volgende tabellen:

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Als u een query wilt uitvoeren op de Azure AD-logboeken, voert u de relevante tabelnaam boven aan het queryvenster in.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u verbinding maakt Azure Active Directory met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [krijgen van inzicht in uw gegevens en mogelijke bedreigingen.](quickstart-get-visibility.md)
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
