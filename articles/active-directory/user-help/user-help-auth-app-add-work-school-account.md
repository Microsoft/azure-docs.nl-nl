---
title: Een werk-of school account toevoegen aan de Microsoft Authenticator-app-Azure AD
description: Voeg uw werk-of school account toe aan de app Microsoft Authenticator om uw identiteit te verifiëren met twee ledige verificatie methoden.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 04c9bc429d9663f7ac36b6ba8f40abf225eb71c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359103"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Voeg uw werk-of school account toe aan de app Microsoft Authenticator

Als uw organisatie twee ledige verificatie gebruikt, kunt u uw werk-of school account instellen om de Microsoft Authenticator-app als een van de verificatie methoden te gebruiken.

>[!Important]
>Voordat u uw account kunt toevoegen, moet u de app Microsoft Authenticator downloaden en installeren. Als u dat nog niet hebt gedaan, volgt u de stappen in het artikel [De app downloaden en installeren](user-help-auth-app-download-install.md).

## <a name="add-your-work-or-school-account"></a>Uw werk- of schoolaccount toevoegen

U kunt uw werk-of school account toevoegen aan de app Microsoft Authenticator door een van de volgende handelingen uit te voeren:

- Aanmelden met uw werk-of schoolaccount referenties (preview-versie)
- Een QR-code scannen

### <a name="sign-in-with-your-credentials"></a>Aanmelden met uw referenties

>[!Note]
>Deze functie kan alleen worden gebruikt door gebruikers met wie de beheerder via de verificator-app zich heeft aangemeld.

Als u een account wilt toevoegen door u aan te melden bij uw werk-of school account met behulp van uw referenties:

1. Open de Microsoft Authenticator-app, selecteer de **+** knop en tik op **werk-of school account toevoegen**. Selecteer **Aanmelden**.

1. Voer de referenties voor uw werk-of school account in. Als u een tijdelijke toegangs doorvoer (tik) hebt, kunt u deze gebruiken om u aan te melden. Op dit moment kunt u mogelijk worden geblokkeerd door een van de volgende voor waarden uit te voeren:

   - Als u onvoldoende verificatie methoden hebt voor uw account om een sterk verificatie token te krijgen, kunt u niet door gaan met het toevoegen van een account.

   - Als u het bericht ontvangt `You might be signing in from a location that is restricted by your admin` , bent u geblokkeerd en moet een beheerder u de blok kering in [beveiligings gegevens](https://mysignins.microsoft.com/security-info)opheffen.

   - Als u zich niet hebt geblokkeerd voor het aanmelden via een telefoon met de verificator-app door uw beheerder, kunt u de apparaatregistratie door lopen om in te stellen voor aanmelding zonder wacht woord en Azure Multi-Factor Authentication (MFA).

1. Op dit moment kunt u worden gevraagd om een QR-code te scannen die door uw organisatie is opgegeven voor het instellen van een on-premises multi-factor Authentication-account in de app. U hoeft dit alleen te doen als uw organisatie gebruikmaakt van een on-premises MFA-server.

1. Tik op het apparaat op het account en Controleer in de weer gave volledig scherm dat uw account juist is en of er een verificatie code van zes cijfers is. Voor extra beveiliging verandert de verificatie code elke 30 seconden om te voor komen dat iemand meerdere keren een code gebruikt.

## <a name="sign-in-with-a-qr-code"></a>Aanmelden met een QR-code

Ga als volgt te werk om een account toe te voegen door een QR-code te scannen:

1. Ga op uw computer naar de pagina **aanvullende beveiligings verificatie** .

   >[!Note]
   >Als de pagina **aanvullende beveiligings verificatie** niet wordt weer gegeven, is het mogelijk dat de beheerder de ervaring voor beveiligings gegevens (preview) heeft ingeschakeld. Als dat het geval is, volgt u de instructies in de sectie [beveiligings gegevens instellen voor het gebruik van een verificator-app](security-info-setup-auth-app.md) . Als dat niet het geval is, moet u contact opnemen met de Help Desk van uw organisatie voor hulp. Zie voor meer informatie over beveiligings informatie [uw beveiligings gegevens instellen bij een aanmeldings prompt](security-info-setup-signin.md).

1. Schakel het selectie vakje naast de verificator-app in en selecteer vervolgens **configureren**. De pagina **Mobiele app configureren** wordt weergegeven.

   ![Scherm met een QR-code](./media/user-help-auth-app-add-work-school-account/auth-app-barcode.png)

1. Open de Microsoft Authenticator-app, selecteer het pictogram ![ met het plus teken op Ios-of Android-apparaten, ](media/user-help-auth-app-add-work-school-account/plus-icon.png) Selecteer **account toevoegen** en selecteer vervolgens **werk-of school account,** gevolgd door **een QR-code scannen**.
   Als u geen account hebt ingesteld in de verificator-app, ziet u een grote blauwe knop met de tekst **account toevoegen**.

Als u niet wordt gevraagd om uw camera te gebruiken om een QR-code te scannen, controleert u in de instellingen van uw telefoon of de verificator-app toegang heeft tot de telefoon camera.

## <a name="next-steps"></a>Volgende stappen

- Nadat u uw accounts aan de app hebt toegevoegd, kunt u zich aanmelden met behulp van de verificator-app op uw apparaat. Zie [Aanmelden met de app](user-help-auth-app-sign-in.md)voor meer informatie.

- Voor apparaten met iOS kunt u ook een back-up maken van uw account referenties en de bijbehorende app-instellingen, zoals de volg orde van uw accounts, naar de Cloud. Zie [back-up en herstellen met Microsoft Authenticator-app](user-help-auth-app-backup-recovery.md)voor meer informatie.
