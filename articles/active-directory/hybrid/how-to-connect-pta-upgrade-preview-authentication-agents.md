---
title: Azure AD Connect Pass Through-verificatie-auth-agents bijwerken | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de configuratie van de Pass-Through-verificatie van Azure Active Directory (Azure AD) bijwerkt.
services: active-directory
keywords: Azure AD Connect Pass-Through-verificatie, installeren van Active Directory, vereiste onderdelen voor Azure AD, SSO, eenmalige aanmelding
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d0507f7751305af5e626cbd7dd6e0dfd1a63a74
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973045"
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Pass-Through-verificatie Azure Active Directory: Preview-verificatie agenten bijwerken

## <a name="overview"></a>Overzicht

Dit artikel is voor klanten die gebruikmaken van Azure AD Pass-Through-verificatie via preview. Er is een upgrade uitgevoerd voor de verificatie-agent software. U moet _hand matig_ upgrade-verificatie agenten installeren die op uw on-premises servers zijn geïnstalleerd. Deze hand matige upgrade is slechts één keer een actie. Alle toekomstige updates voor verificatie agenten worden automatisch. De volgende redenen zijn van een upgrade:

- De Preview-versies van verificatie agenten ontvangen geen verdere beveiligings-of fout oplossingen.
-   De Preview-versies van verificatie agenten kunnen niet worden geïnstalleerd op extra servers voor hoge Beschik baarheid.

## <a name="check-versions-of-your-authentication-agents"></a>Controleer de versies van uw verificatie agenten

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Stap 1: controleren waar uw verificatie agenten zijn geïnstalleerd

Volg deze stappen om te controleren waar uw verificatie agenten zijn geïnstalleerd:

1. Meld u aan bij het [Azure Active Directory-beheer centrum](https://aad.portal.azure.com) met de referenties van de globale beheerder voor uw Tenant.
2. Selecteer **Azure Active Directory** in de linkernavigatiebalk.
3. Selecteer **Azure AD Connect**. 
4. Selecteer **Pass-Through-verificatie**. Deze Blade bevat de servers waarop uw verificatie agenten zijn geïnstalleerd.

![Azure Active Directory beheer centrum: Pass Through-verificatie Blade](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Stap 2: Controleer de versies van uw verificatie agenten

Als u de versies van uw verificatie agenten wilt controleren, volgt u deze instructies op elke server die u in de voor gaande stap hebt gevonden:

1. Ga naar **configuratie scherm-> Program ma's-> Program ma's en onderdelen** op de on-premises server.
2. Als er een vermelding voor '**Microsoft Azure AD Connect authentication-agent**' is, hoeft u geen actie op deze server uit te voeren.
3. Als er een vermelding is voor '**Microsoft Azure AD Application proxy connector**', moet u hand matig een upgrade uitvoeren op deze server.

![Preview-versie van verificatie agent](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Aanbevolen procedures voordat u de upgrade start

Voordat u een upgrade uitvoert, moet u ervoor zorgen dat u over de volgende items beschikt:

1. **Alleen een globale beheerders account voor de Cloud maken**: Voer geen upgrade uit zonder alleen-Cloud account voor globale beheerders die moet worden gebruikt in nood situaties waarbij uw Pass Through-verificatie agenten niet goed werken. Meer informatie over [het toevoegen van een globale beheerders account voor de Cloud](../fundamentals/add-users-azure-active-directory.md). Deze stap is kritiek en zorgt ervoor dat u uw Tenant niet hebt vergrendeld.
2.  **Hoge Beschik baarheid garanderen**: als dit nog niet is gedaan, installeert u een tweede zelfstandige verificatie-agent om hoge Beschik baarheid te bieden voor aanmeldings aanvragen, met behulp van deze [instructies](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>De verificatie agent op uw Azure AD Connect-server bijwerken

U moet een upgrade uitvoeren Azure AD Connect voordat u de verificatie agent op dezelfde server bijwerkt. Volg deze stappen op uw primaire en tijdelijke Azure AD Connect servers:

1. **Upgrade Azure AD Connect**: Volg dit [artikel](how-to-upgrade-previous-version.md) en voer een upgrade uit naar de meest recente versie van Azure AD Connect.
2. **De preview-versie van de verificatie agent verwijderen**: down load [Dit Power shell-script](https://aka.ms/rmpreviewagent) en voer dit uit als Administrator op de server.
3. **Down load de nieuwste versie van de verificatie agent (versies 1.5.389.0 of hoger)**: Meld u aan bij het [Azure Active Directory-beheer centrum](https://aad.portal.azure.com) met de referenties van de globale beheerder van uw Tenant. Selecteer **Azure Active Directory-> Azure AD Connect-> Pass-Through-verificatie-> Download Agent**. Ga akkoord met de [Service voorwaarden](https://aka.ms/authagenteula) en down load de nieuwste versie van de verificatie agent. U kunt de verificatie agent ook [hier](https://aka.ms/getauthagent)downloaden.
4. **Installeer de nieuwste versie van de verificatie agent**: Voer het uitvoer bare bestand dat u hebt gedownload in stap 3 uit. Geef de referenties van de globale beheerder van uw Tenant op wanneer u hierom wordt gevraagd.
5. **Controleer of de nieuwste versie is geïnstalleerd**: zoals wordt weer gegeven, gaat u naar **configuratie scherm-> program Ma's-> Program ma's en onderdelen** en controleer of er een vermelding is voor '**Microsoft Azure AD Connect authentication agent**'.

>[!NOTE]
>Als u de Blade Pass-Through-verificatie in het [Azure Active Directory-beheer centrum](https://aad.portal.azure.com) controleert nadat u de voor gaande stappen hebt voltooid, ziet u twee vermeldingen voor de verificatie agent per server: één vermelding waarin de verificatie agent als **actief** wordt weer gegeven en de andere als **inactief**. Dit wordt _verwacht_. De **inactieve** vermelding wordt na een paar dagen automatisch verwijderd.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>De verificatie agent op andere servers bijwerken

Volg deze stappen om verificatie agenten op andere servers bij te werken (waarbij Azure AD Connect niet is geïnstalleerd):

1. **De preview-versie van de verificatie agent verwijderen**: down load [Dit Power shell-script](https://aka.ms/rmpreviewagent) en voer dit uit als Administrator op de server.
2. **Down load de nieuwste versie van de verificatie agent (versies 1.5.389.0 of hoger)**: Meld u aan bij het [Azure Active Directory-beheer centrum](https://aad.portal.azure.com) met de referenties van de globale beheerder van uw Tenant. Selecteer **Azure Active Directory-> Azure AD Connect-> Pass-Through-verificatie-> Download Agent**. Ga akkoord met de service voorwaarden en down load de nieuwste versie.
3. **Installeer de meest recente versie van de verificatie agent**: Voer het uitvoer bare bestand dat u hebt gedownload, uit in stap 2. Geef de referenties van de globale beheerder van uw Tenant op wanneer u hierom wordt gevraagd.
4. **Controleer of de nieuwste versie is geïnstalleerd**: zoals wordt weer gegeven, gaat u naar **configuratie scherm-> program Ma's-> Program ma's en onderdelen** en controleert u of er een vermelding is met de naam **Microsoft Azure AD Connect authentication agent**.

>[!NOTE]
>Als u de Blade Pass-Through-verificatie in het [Azure Active Directory-beheer centrum](https://aad.portal.azure.com) controleert nadat u de voor gaande stappen hebt voltooid, ziet u twee vermeldingen voor de verificatie agent per server: één vermelding waarin de verificatie agent als **actief** wordt weer gegeven en de andere als **inactief**. Dit wordt _verwacht_. De **inactieve** vermelding wordt na een paar dagen automatisch verwijderd.

## <a name="next-steps"></a>Volgende stappen
- [**Problemen oplossen**](tshoot-connect-pass-through-authentication.md) : informatie over het oplossen van veelvoorkomende problemen met de functie.