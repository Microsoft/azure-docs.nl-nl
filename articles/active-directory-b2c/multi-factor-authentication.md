---
title: Multi-Factor Authentication in Azure Active Directory B2C | Microsoft Docs
description: Multi-Factor Authentication in te scha kelen in consumenten toepassingen die door Azure Active Directory B2C zijn beveiligd.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b7b7f1c5fb0a7991707a26b4a7f54fb3ffaf7bab
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033517"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Meervoudige verificatie inschakelen in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) kan rechtstreeks worden geïntegreerd met [Azure AD multi-factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) zodat u een tweede beveiligingslaag kunt toevoegen om u aan te melden en zich aan te melden bij uw toepassingen. U kunt meervoudige verificatie inschakelen zonder ook maar één regel code te hoeven schrijven. Als u al registraties hebt gemaakt en gebruikers stromen hebt aangemeld, kunt u nog steeds multi-factor Authentication inschakelen.

Deze functie helpt toepassingen bij het afhandelen van scenario's zoals:

- U hebt geen multi-factor Authentication nodig om toegang te krijgen tot één toepassing, maar u hebt deze nodig om toegang te krijgen tot een ander programma. Bijvoorbeeld, de klant kan zich aanmelden bij een automatische verzekerings toepassing met een sociaal of lokaal account, maar moet het telefoon nummer controleren voordat ze toegang krijgen tot de toepassing voor de start van de verzekering die is geregistreerd in dezelfde map.
- U hebt geen multi-factor Authentication nodig om toegang te krijgen tot een toepassing in het algemeen, maar u hebt deze nodig om toegang te krijgen tot de gevoelige gedeelten erin. De klant kan zich bijvoorbeeld aanmelden bij een bank toepassing met een sociaal of lokaal account en het account saldo controleren, maar moet het telefoon nummer verifiëren voordat een overschrijving wordt gedaan.

## <a name="set-multi-factor-authentication"></a>Multi-factor Authentication instellen

::: zone pivot="b2c-user-flow"

1. Meld u aan bij [Azure Portal](https://portal.azure.com)
1. Gebruik het filter voor **adres lijst en abonnementen** in het bovenste menu om de map te selecteren die uw Azure AD B2C Tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **gebruikers stromen**.
1. Selecteer de gebruikers stroom waarvoor u MFA wilt inschakelen. Bijvoorbeeld *B2C_1_signinsignup*.
1. Selecteer **Eigenschappen**.
1. Selecteer in de sectie multi- **Factor Authentication** de gewenste **MFA-methode** en selecteer vervolgens onder **MFA Enforcement** de optie **altijd aan** of **voorwaardelijk (aanbevolen)**.
   > [!NOTE]
   >
   > - Als u **voorwaardelijk (aanbevolen)** selecteert, moet u ook [voorwaardelijke toegang toevoegen aan gebruikers stromen](conditional-access-user-flow.md)en de apps opgeven waarop u het beleid wilt Toep assen.
   > - Multi-factor Authentication (MFA) is standaard uitgeschakeld voor het registreren van gebruikers stromen. U kunt MFA inschakelen in gebruikers stromen met aanmelding via de telefoon, maar omdat een telefoon nummer wordt gebruikt als de primaire id, is e-mail One-time wachtwoord code de enige optie die beschikbaar is voor de tweede verificatie factor.

1. Selecteer **Opslaan**. MFA is nu ingeschakeld voor deze gebruikers stroom.

U kunt de **gebruikers stroom uitvoeren** gebruiken om de ervaring te controleren. Bevestig het volgende scenario:

Er wordt een klant account in uw Tenant gemaakt voordat de multi-factor Authentication-stap wordt uitgevoerd. Tijdens de stap wordt de klant gevraagd om een telefoon nummer op te geven en te verifiëren. Als de verificatie is geslaagd, wordt het telefoon nummer aan het account gekoppeld voor later gebruik. Zelfs als de klant annuleert of uitvalt, kan de klant worden gevraagd om een telefoon nummer opnieuw te verifiëren tijdens de volgende aanmelding waarbij multi-factor Authentication is ingeschakeld.

::: zone-end

::: zone pivot="b2c-custom-policy"

Als u de aangepaste beleids Starter Packs wilt inschakelen Multi-Factor Authentication ophalen van GitHub, werkt u de XML-bestanden in het **SocialAndLocalAccountsWithMFA** Starter Pack bij met de naam van uw Azure AD B2C Tenant. De **SocialAndLocalAccountsWithMFA**  maakt sociale, lokale en multi-factor Authentication-opties mogelijk. Zie [aan de slag met aangepast beleid in Active Directory B2C](custom-policy-get-started.md)voor meer informatie. 

::: zone-end
