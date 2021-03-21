---
title: 'Quickstart: Gebruikers toewijzen aan een app die gebruikmaakt van Azure Active Directory als een id-provider'
description: In deze quickstart wordt stapsgewijs uitgelegd hoe u gebruikers toestaat een app te gebruiken die u hebt geconfigureerd voor gebruik van Azure AD als een id-provider.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/01/2020
ms.author: kenwith
ms.openlocfilehash: 53dd2d15565149c3a9888ba063a6194ae033d8e0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258368"
---
# <a name="quickstart-assign-users-to-an-app-that-is-using-azure-ad-as-an-identity-provider"></a>Quickstart: Gebruikers toewijzen aan een app die gebruikmaakt van Azure AD als een id-provider

In de vorige quickstart hebt u de eigenschappen voor een app geconfigureerd. Door de eigenschappen in te stellen hebt u de ervaring geconfigureerd voor zowel toegewezen als niet-toegewezen gebruikers. In deze quickstart wordt stapsgewijs uitgelegd hoe u gebruikers toewijst aan de app.

## <a name="prerequisites"></a>Vereisten

Als u gebruikers wilt toewijzen aan een app die u hebt toegevoegd aan uw Azure AD-tenant, hebt u het volgende nodig:

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een van de volgende rollen: Globale beheerder, Cloudtoepassingsbeheerder, Toepassingsbeheerder of eigenaar van de service-principal.
- Optioneel: Voltooiing van [Uw apps weergeven](view-applications-portal.md).
- Optioneel: Voltooiing van [Een app toevoegen](add-application-portal.md).
- Optioneel: Voltooiing van [Een app configureren](add-application-portal-configure.md).

>[!IMPORTANT]
>Gebruik een niet-productieomgeving om de stappen in deze quickstart te testen.

## <a name="assign-users-to-an-app"></a>Gebruikers toewijzen aan een app
1. Selecteer in het Azure AD-portal **Bedrijfstoepassingen**. Zoek en selecteer vervolgens de toepassing die u wilt configureren.
2. Selecteer in het linkernavigatiemenu de optie **Gebruikers en groepen**.
   > [!NOTE]
   > Voor sommige Microsoft 365-apps is het gebruik van PowerShell vereist. 
3. Selecteer de knop **Gebruiker toevoegen**.
4. Selecteer in het deelvenster **Toewijzing toevoegen** de optie **Gebruikers en groepen**.
5. Selecteer de gebruiker of groep waaraan u de toepassing wilt toewijzen. U kunt ook beginnen met in het zoekvak de naam van de gebruiker of groep te typen. U kunt meerdere gebruikers en groepen kiezen. Wat u selecteert wordt weergegeven onder **Geselecteerde items**.
    > [!IMPORTANT]
    > Wanneer u een groep toewijst aan een toepassing, hebben alleen gebruikers in de groep toegang. De toewijzing verloopt niet trapsgewijs naar geneste groepen.

    > [!NOTE]
    > Voor een toewijzing op basis van een groep is de editie Azure Active Directory Premium P1 of P2 vereist. Toewijzing op basis van een groep wordt alleen ondersteund voor beveiligingsgroepen. Lidmaatschap van geneste groepen en Microsoft 365-groepen wordt momenteel niet ondersteund. Raadpleeg de [pagina met Azure Active Directory-prijzen](https://azure.microsoft.com/pricing/details/active-directory) voor meer licentievereisten voor de functies die in dit artikel worden besproken. 
6. Wanneer u klaar bent, kiest u **Selecteren**.
   ![Een gebruiker of groep toewijzen aan de app](./media/assign-user-or-group-access-portal/assign-users.png)
7. Selecteer in het deelvenster **Gebruikers en groepen** een of meer gebruikers of groepen in de lijst. Kies vervolgens de knop **Selecteren** onderaan het deelvenster.
8. Als de toepassing hiervoor ondersteuning biedt, kunt u een rol toewijzen aan de gebruiker of groep. Kies in het deelvenster **Toewijzing toevoegen** de optie **Toewijzen**. Kies vervolgens in het deelvenster **Rol selecteren** een rol om toe te wijzen aan de geselecteerde gebruikers of groepen. Selecteer vervolgens **OK** onderaan het deelvenster. 
    > [!NOTE]
    > Als de toepassing geen ondersteuning biedt voor het selecteren van rollen, wordt de standaardtoegangsrol toegewezen. In dit geval beheert de toepassing het toegangsniveau voor gebruikers.
9. Selecteer in het deelvenster **Toewijzing toevoegen** de knop **Toewijzen** onderaan het deelvenster.

U kunt de toewijzing van gebruikers of groepen ongedaan maken door dezelfde procedure te volgen. Selecteer de gebruiker of groep waarvoor u de toewijzing wilt opheffen, en selecteer **Verwijderen**. Voor sommige Microsoft 365- en Office 365-apps is het gebruik van PowerShell vereist. 

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de quickstart, kunt u overwegen de app te verwijderen. Op deze manier houdt u de tenant schoon. Zie [Een app verwijderen](delete-application-portal.md) in de laatste quickstart in deze reeks voor meer informatie over het verwijderen van een app.

## <a name="next-steps"></a>Volgende stappen

Ga door naar het volgende artikel voor meer informatie over het instellen van eenmalige aanmelding voor een app.
> [!div class="nextstepaction"]
> [Eenmalige aanmelding op basis van SAML instellen](add-application-portal-setup-sso.md)

OF

> [!div class="nextstepaction"]
> [Eenmalige aanmelding op basis van OIDC instellen](add-application-portal-setup-oidc-sso.md)
