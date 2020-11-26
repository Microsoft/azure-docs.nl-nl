---
title: Multi-Factor Authentication in Azure Active Directory B2C | Microsoft Docs
description: Multi-Factor Authentication in te scha kelen in consumenten toepassingen die door Azure Active Directory B2C zijn beveiligd.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8c150235dc6b867a69b539aba9c90d666122a210
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96170443"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Multi-factor Authentication inschakelen in Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) kan rechtstreeks worden geïntegreerd met [Azure AD multi-factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) zodat u een tweede beveiligingslaag kunt toevoegen om u aan te melden en zich aan te melden bij uw toepassingen. U schakelt multi-factor Authentication in zonder één regel code te schrijven. Als u al registraties hebt gemaakt en gebruikers stromen hebt aangemeld, kunt u nog steeds multi-factor Authentication inschakelen.

Deze functie helpt toepassingen bij het afhandelen van scenario's zoals:

- U hebt geen multi-factor Authentication nodig om toegang te krijgen tot één toepassing, maar u hebt deze nodig om toegang te krijgen tot een ander programma. Bijvoorbeeld, de klant kan zich aanmelden bij een automatische verzekerings toepassing met een sociaal of lokaal account, maar moet het telefoon nummer controleren voordat ze toegang krijgen tot de toepassing voor de start van de verzekering die is geregistreerd in dezelfde map.
- U hebt geen multi-factor Authentication nodig om toegang te krijgen tot een toepassing in het algemeen, maar u hebt deze nodig om toegang te krijgen tot de gevoelige gedeelten erin. De klant kan zich bijvoorbeeld aanmelden bij een bank toepassing met een sociaal of lokaal account en het account saldo controleren, maar moet het telefoon nummer verifiëren voordat een overschrijving wordt gedaan.

## <a name="set-multi-factor-authentication"></a>Multi-factor Authentication instellen

1. Meld u aan bij [Azure Portal](https://portal.azure.com)
1. Gebruik het filter voor **adres lijst en abonnementen** in het bovenste menu om de map te selecteren die uw Azure AD B2C Tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **gebruikers stromen**.
1. Selecteer de gebruikers stroom waarvoor u MFA wilt inschakelen. Bijvoorbeeld *B2C_1_signinsignup*.
1. Selecteer **Eigenschappen**.
1. Selecteer in de sectie multi- **Factor Authentication** de gewenste **MFA-methode** en selecteer vervolgens onder **MFA Enforcement** de optie **altijd aan** of **[voorwaardelijk](conditional-access-user-flow.md) (aanbevolen)**. Voor voorwaardelijk, maakt u beleid voor [voorwaardelijke toegang](conditional-access-identity-protection-setup.md) en geeft u de apps op waarop u het beleid wilt Toep assen. 
1. Selecteer Opslaan. MFA is nu ingeschakeld voor deze gebruikers stroom.

U kunt de **gebruikers stroom uitvoeren** gebruiken om de ervaring te controleren. Bevestig het volgende scenario:

Er wordt een klant account in uw Tenant gemaakt voordat de multi-factor Authentication-stap wordt uitgevoerd. Tijdens de stap wordt de klant gevraagd om een telefoon nummer op te geven en te verifiëren. Als de verificatie is geslaagd, wordt het telefoon nummer aan het account gekoppeld voor later gebruik. Zelfs als de klant annuleert of uitvalt, kan de klant worden gevraagd om een telefoon nummer opnieuw te verifiëren tijdens de volgende aanmelding waarbij multi-factor Authentication is ingeschakeld.