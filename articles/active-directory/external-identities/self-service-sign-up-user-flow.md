---
title: Een self-service stroom voor het aanmelden van gebruikers toevoegen-Azure AD
description: Gebruikers stromen maken voor apps die zijn gebouwd door uw organisatie. Gebruikers die deze app bezoeken, kunnen dan een gast account krijgen met behulp van de opties die in de gebruikers stroom zijn geconfigureerd.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f76f4a3e5fc87420c242c693e3c48a91244641e0
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560030"
---
# <a name="add-a-self-service-sign-up-user-flow-to-an-app-preview"></a>Een self-service-aanmeldings stroom voor gebruikers toevoegen aan een app (preview)
> [!NOTE]
> Aanmelden via self-service is een open bare preview-functie van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

U kunt gebruikers stromen maken voor apps die zijn gebouwd door uw organisatie. Als u uw gebruikers stroom aan een toepassing koppelt, kunt u zich aanmelden op die app inschakelen. U kunt ervoor kiezen om meer dan één toepassing te koppelen aan de gebruikers stroom. Wanneer u de gebruikers stroom aan een of meer toepassingen koppelt, kunnen gebruikers die de app bezoeken zich registreren en een gast account krijgen met de opties die in de gebruikers stroom zijn geconfigureerd.

> [!NOTE]
> U kunt gebruikers stromen koppelen aan apps die zijn gebouwd door uw organisatie. Gebruikers stromen kunnen niet worden gebruikt voor micro soft-apps, zoals share point of teams.

## <a name="before-you-begin"></a>Voordat u begint

### <a name="add-social-identity-providers-optional"></a>Social id-providers toevoegen (optioneel)

Azure AD is de standaard id-provider voor aanmelden via self-service. Dit betekent dat gebruikers standaard kunnen aanmelden met een Azure AD-account. Sociale id-providers kunnen ook worden opgenomen in deze registratie stromen ter ondersteuning van Google-en Facebook-accounts.

- [Facebook toevoegen aan uw lijst met sociale id-providers](facebook-federation.md)
- [Google toevoegen aan uw lijst met sociale id-providers](google-federation.md)

> [!NOTE]
> Als er in de huidige preview een self-service-aanmeldings stroom is gekoppeld aan een app en u een uitnodiging voor een gebruiker naar deze app stuurt, kan de gebruiker geen Gmail-account gebruiken om de uitnodiging te inwisselen. Als tijdelijke oplossing kan de gebruiker het aanmeldings proces voor de self-service door lopen. Of ze kunnen de uitnodiging inwisselen door toegang te krijgen tot een andere app of door gebruik te maken van de portal mijn apps op https://myapps.microsoft.com .

### <a name="define-custom-attributes-optional"></a>Aangepaste kenmerken definiëren (optioneel)

Gebruikers kenmerken zijn waarden die worden verzameld door de gebruiker tijdens het aanmelden met een self-service. Azure AD wordt geleverd met een ingebouwde set kenmerken, maar u kunt aangepaste kenmerken maken voor gebruik in uw gebruikers stroom. U kunt deze kenmerken ook lezen en schrijven met behulp van de Microsoft Graph-API. Zie [aangepaste kenmerken definiëren voor gebruikers stromen](user-flow-add-custom-attributes.md).

## <a name="enable-self-service-sign-up-for-your-tenant"></a>Selfservice registratie inschakelen voor uw Tenant

Voordat u een self-service registratie voor gebruikers stroom kunt toevoegen aan uw toepassingen, moet u de functie voor uw Tenant inschakelen. Nadat de functie is ingeschakeld, worden de besturings elementen beschikbaar in de gebruikers stroom waarmee u de gebruikers stroom kunt koppelen aan een toepassing.

1. Meld u als een Azure AD-administrator aan bij de [Azure Portal](https://portal.azure.com).
2. Onder **Azure-Services** selecteert u **Azure Active Directory**.
3. Selecteer **gebruikers instellingen** en selecteer vervolgens **externe instellingen voor samen werking beheren** onder **externe gebruikers**.
4. Stel de **Schakel optie aanmelden via selfservice inschakelen via gebruikers stromen (preview)** in op **Ja**.

   ![Aanmelden via self-service voor gasten inschakelen](media/self-service-sign-up-user-flow/enable-self-service-sign-up.png)
5. Selecteer **Opslaan**.
## <a name="create-the-user-flow-for-self-service-sign-up"></a>De gebruikers stroom voor het aanmelden via een self-service maken

Vervolgens maakt u de gebruikers stroom voor de Self-Service-aanmelding en voegt u deze toe aan een toepassing.

1. Meld u als een Azure AD-administrator aan bij de [Azure Portal](https://portal.azure.com).
2. Onder **Azure-Services** selecteert u **Azure Active Directory**.
3. Selecteer in het linkermenu **externe identiteiten**.
4. Selecteer **gebruikers stromen (preview)** en selecteer vervolgens **nieuwe gebruikers stroom**.

   ![Knop een nieuwe gebruikers stroom toevoegen](media/self-service-sign-up-user-flow/new-user-flow.png)

5. Voer op de pagina **maken** een **naam** in voor de gebruikers stroom. Houd er rekening mee dat de naam automatisch wordt voorafgegaan door **B2X_1_**.
6. Selecteer in de lijst **id-providers** een of meer id-providers die uw externe gebruikers kunnen gebruiken om zich aan te melden bij uw toepassing. **Azure Active Directory registreren** is standaard geselecteerd. (Zie [voordat u begint](#before-you-begin) met het eerder in dit artikel voor meer informatie over het toevoegen van id-providers.)
7. Kies onder **gebruikers kenmerken** de kenmerken die u wilt verzamelen van de gebruiker. Voor aanvullende kenmerken selecteert u **meer weer geven**. Selecteer bijvoorbeeld **Meer weergeven** en kies vervolgens kenmerken en claims voor **Land/regio**, **Weergavenaam** en **Postcode**. Selecteer **OK**.

   ![Een nieuwe gebruikers stroom pagina maken](media/self-service-sign-up-user-flow/create-user-flow.png)

8. Selecteer **Maken**.
9. De nieuwe gebruikers stroom wordt weer gegeven in de lijst **gebruikers stromen (preview)** . Vernieuw, indien nodig, de pagina.

## <a name="select-the-layout-of-the-attribute-collection-form"></a>De indeling van het formulier kenmerk verzameling selecteren

U kunt de volg orde kiezen waarin de kenmerken worden weer gegeven op de registratie pagina. 

1. Selecteer **Azure Active Directory** in de [Azure-portal](https://portal.azure.com).
2. Selecteer **externe identiteiten**, selecteer **gebruikers stromen (preview-versie)**.
3. Selecteer de self-service voor het registreren van de gebruikers stroom in de lijst.
4. Onder **aanpassen** selecteert u **pagina-indelingen**.
5. De kenmerken die u hebt gekozen om te verzamelen, worden weer gegeven. Als u de volg orde van de weer gave wilt wijzigen, selecteert u een kenmerk **en selecteert u omhoog,** **omlaag**, **verplaatsen naar de bovenkant** of **gaat u naar beneden**.
6. Selecteer **Opslaan**.

## <a name="add-applications-to-the-self-service-sign-up-user-flow"></a>Toepassingen toevoegen aan de self-service voor het aanmelden van de gebruikers stroom

U kunt nu toepassingen koppelen aan de gebruikers stroom.

1. Meld u als een Azure AD-administrator aan bij de [Azure Portal](https://portal.azure.com).
2. Onder **Azure-Services** selecteert u **Azure Active Directory**.
3. Selecteer in het linkermenu **externe identiteiten**.
4. Selecteer **gebruikers stromen (preview)** onder **self-service registreren**.
5. Selecteer de self-service voor het registreren van de gebruikers stroom in de lijst.
6. Selecteer in het menu links onder **gebruiken** de optie **toepassingen**.
7. Selecteer **Toepassing toevoegen**.

   ![Een toepassing toewijzen aan de gebruikers stroom](media/self-service-sign-up-user-flow/assign-app-to-user-flow.png)

8. Selecteer de toepassing in de lijst. Of gebruik het zoekvak om de toepassing te zoeken en selecteer deze.
9. Klik op **Selecteren**.

## <a name="next-steps"></a>Volgende stappen

- [Google toevoegen aan uw lijst met sociale id-providers](google-federation.md)
- [Facebook toevoegen aan uw lijst met sociale id-providers](facebook-federation.md)
- [API-connectors gebruiken om uw gebruikers stromen aan te passen en uit te breiden via web-Api's](api-connectors-overview.md)
- [Aangepaste goedkeurings werk stroom toevoegen aan uw gebruikers stroom](self-service-sign-up-add-approvals.md)
