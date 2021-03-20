---
title: Eenmalige aanmelding bij apps met Azure AD-toepassingsproxy | Microsoft Docs
description: Schakel eenmalige aanmelding in voor uw on-premises toepassingen met Azure AD-toepassingsproxy in de Azure Portal.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/12/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0cb2830c019635e9020a4b586bdc370450fddb0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99253999"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Wachtwoord kluis voor eenmalige aanmelding met toepassings proxy

Azure Active Directory-toepassingsproxy helpt u bij het verbeteren van de productiviteit door on-premises toepassingen te publiceren zodat externe werk nemers er ook veilig toegang toe kunnen krijgen. In de Azure Portal kunt u eenmalige aanmelding (SSO) ook instellen voor deze apps. Uw gebruikers hoeven zich alleen te verifiëren bij Azure AD en ze hebben toegang tot uw bedrijfs toepassing zonder zich opnieuw aan te melden.

Toepassings proxy biedt ondersteuning voor verschillende [modi voor eenmalige aanmelding](sso-options.md#choosing-a-single-sign-on-method). Aanmelding op basis van wacht woorden is bedoeld voor toepassingen die gebruikmaken van een combi natie van gebruikers naam en wacht woord voor verificatie. Wanneer u aanmelden op basis van wacht woorden voor uw toepassing configureert, moeten uw gebruikers één keer zich aanmelden bij de on-premises toepassing. Hierna slaat Azure Active Directory de aanmeldings gegevens op en geeft deze automatisch aan de toepassing door wanneer uw gebruikers op afstand toegang hebben tot de app.

U moet uw app al hebben gepubliceerd en getest met toepassings proxy. Als dat niet het geval is, volgt u de stappen in [toepassingen publiceren met Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md) vervolgens weer terug.

## <a name="set-up-password-vaulting-for-your-application"></a>Wachtwoord kluizen instellen voor uw toepassing

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als beheerder.
1. Selecteer **Azure Active Directory**  >  **Enter prise-toepassingen**  >  **alle toepassingen**.
1. Selecteer in de lijst de app die u wilt instellen met SSO.  
1. Selecteer **toepassings proxy**. 
1. Wijzig het **type verificatie vooraf** in **passthrough** en selecteer **Opslaan**. U kunt later weer overschakelen naar **Azure Active Directory** type. 
1. Selecteer **Eenmalige aanmelding**.

   ![Eenmalige aanmelding selecteren op de overzichts pagina van de app](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. Voor de SSO-modus kiest u **aanmelden op basis van wacht woorden**.
1. Voer voor de aanmeldings-URL de URL in voor de pagina waar gebruikers hun gebruikers naam en wacht woord invoeren om zich buiten het bedrijfs netwerk aan te melden bij uw app. Dit kan de externe URL zijn die u hebt gemaakt tijdens het publiceren van de app via de toepassings proxy.

   ![Kies aanmelden op basis van wacht woord en voer uw URL in](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Selecteer **Opslaan**.
1. Selecteer **toepassings proxy**. 
1. Wijzig het **type verificatie vooraf** in **Azure Active Directory** en selecteer **Opslaan**. 
1. Selecteer **Users and Groups** (Gebruikers en groepen).
1. Gebruikers toewijzen aan de toepassing met de optie **gebruiker toevoegen**. 
1. Als u de referenties voor een gebruiker vooraf wilt definiëren, schakelt u het selectie vakje voor de gebruikers naam in en selecteert u **referenties bijwerken**.
1. Selecteer **Azure Active Directory**  >    >  **alle toepassingen** app-registraties.
1. Selecteer in de lijst de app die u hebt geconfigureerd met wacht woord-SSO.
1. Selecteer **huis stijl**. 
1. Werk de **URL van de start pagina** bij met de **aanmeldings-URL** op de SSO-pagina van het wacht woord en selecteer **Opslaan**.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Uw app testen

Ga naar de portal mijn apps. Meld u aan met uw referenties (of de referenties voor een test account dat u hebt ingesteld met toegang). Nadat u zich hebt aangemeld, klikt u op het pictogram van de app. Hiermee wordt mogelijk de installatie van de extensie voor beveiligde aanmelding van mijn apps geactiveerd. Als uw gebruiker vooraf gedefinieerde referenties had, moet de verificatie voor de app automatisch plaatsvinden, anders moet u de gebruikers naam of het wacht woord voor de eerste keer opgeven. 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over andere manieren om [eenmalige aanmelding](what-is-single-sign-on.md) te implementeren
- Meer informatie over de [beveiligings overwegingen voor het extern openen van apps met Azure AD-toepassingsproxy](application-proxy-security.md)
