---
title: Azure AD B2C ID-provider van lokale account instellen
titleSuffix: Azure AD B2C
description: Definieer de identiteits typen die kunnen worden gebruikt voor het registreren of aanmelden (e-mail adres, gebruikers naam, telefoon nummer) in uw Azure Active Directory B2C Tenant.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b2baff33d9e91e1b5259d79eca0a22535c00f419
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555241"
---
# <a name="set-up-the-local-account-identity-provider"></a>De ID-provider van het lokale account instellen

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C biedt verschillende manieren waarop gebruikers een gebruiker kunnen verifiëren. Gebruikers kunnen zich aanmelden bij een lokaal account, door gebruik te maken van gebruikers naam en wacht woord, verificatie via telefoon (ook wel wacht woord minder authenticatie genoemd) of sociale id-providers. E-mail aanmelding is standaard ingeschakeld in de instellingen van uw lokale account-ID-provider. 

In dit artikel wordt beschreven hoe gebruikers hun lokale accounts maken voor deze Azure AD B2C Tenant. Zie [een id-provider toevoegen](add-identity-provider.md)voor sociale of bedrijfs identiteiten, waarbij de identiteit van de gebruiker wordt beheerd door een federatieve id-provider, zoals Facebook en Google.

## <a name="email-sign-in"></a>E-mail aanmelding

Met de optie e-mail kunnen gebruikers zich aanmelden/het e-mail adres en wacht woord:

- **Aanmelding**, gebruikers wordt gevraagd hun e-mail adres en wacht woord op te geven.
- **Registratie**, gebruikers wordt gevraagd om een e-mail adres dat wordt geverifieerd bij het aanmelden (optioneel) en wordt de aanmeldings-id. De gebruiker voert vervolgens eventuele andere informatie die wordt gevraagd op de registratie pagina, bijvoorbeeld weergave naam, voor naam en de achternaam in. Selecteer vervolgens door gaan om het account te maken.
- **Wacht woord opnieuw instellen**: gebruikers moeten hun e-mail adres invoeren en verifiëren, waarna de gebruiker het wacht woord opnieuw kan instellen

![E-mail registratie of aanmeldings ervaring](./media/identity-provider-local/local-account-email-experience.png)

## <a name="username-sign-in"></a>Gebruikers naam aanmelding

Met de optie gebruiker kunnen gebruikers zich aanmelden/instellen met een gebruikers naam en wacht woord:

- **Aanmelden**: gebruikers wordt gevraagd hun gebruikers naam en wacht woord op te geven.
- **Meld** u aan: gebruikers wordt gevraagd om een gebruikers naam, die de aanmeldings-id wordt. Gebruikers wordt ook gevraagd om een e-mail adres dat wordt geverifieerd bij het aanmelden. Het e-mail adres wordt gebruikt tijdens een wachtwoord herstel stroom. De gebruiker voert een andere gevraagde informatie in op de registratie pagina, bijvoorbeeld weergave naam, voor naam en weer gave. De gebruiker selecteert vervolgens door gaan om het account te maken.
- **Wacht woord opnieuw instellen**: gebruikers moeten hun gebruikers naam en het bijbehorende e-mail adres invoeren. Het e-mail adres moet worden gecontroleerd, waarna de gebruiker het wacht woord opnieuw kan instellen.

![Aanmeld-of aanmeldings ervaring van gebruikers naam](./media/identity-provider-local/local-account-username-experience.png)

## <a name="phone-sign-in-preview"></a>Aanmelding via de telefoon (preview-versie)

Verificatie met een wacht woord is een type verificatie waarbij een gebruiker zich niet hoeft aan te melden met het wacht woord. Met de telefoon registratie en aanmelding kan de gebruiker zich aanmelden voor de app met behulp van een telefoon nummer als de primaire aanmeldings-id. De gebruiker heeft de volgende ervaring tijdens het aanmelden en aanmelden:

- **Aanmelden**: als de gebruiker een bestaand account heeft met een telefoon nummer als id, voert de gebruiker hun telefoon nummer in en selecteert *Aanmelden*. Ze bevestigen het land en telefoon nummer door *door gaan* te selecteren en er wordt een eenmalige verificatie code naar de telefoon verzonden. De gebruiker voert de verificatie code in en selecteert aanmelden *blijven* .
- **Meld u** aan: als de gebruiker nog geen account heeft voor uw toepassing, kunt u er een maken door te klikken op de koppeling *nu registreren* . 
    1. Er wordt een aanmeldings pagina weer gegeven, waar de gebruiker hun *land* selecteert, het telefoon nummer invoert en *code verzenden* selecteert. 
    1. Er wordt een eenmalige verificatie code verzonden naar het telefoon nummer van de gebruiker. De gebruiker voert de *verificatie code* op de registratie pagina in en selecteert vervolgens *code controleren*. (Als de gebruiker de code niet kan ophalen, kunnen ze *nieuwe code verzenden* selecteren). 
    1. De gebruiker voert een andere gevraagde informatie in op de registratie pagina, bijvoorbeeld weergave naam, voor naam en weer gave. Selecteer vervolgens Doorgaan.
    1. Vervolgens wordt de gebruiker gevraagd een **herstel bericht** op te geven. De gebruiker voert het e-mail adres in en selecteert vervolgens *verificatie code verzenden*. Er wordt een code verzonden naar het postvak in van het e-mail adres van de gebruiker, die ze kunnen ophalen en invoeren in het vak verificatie code. Vervolgens selecteert de gebruiker code verifiëren.
    1. Zodra de code is geverifieerd, selecteert de gebruiker *maken* om hun account te maken. 

![Aanmelding via de telefoon of aanmeldings ervaring](./media/identity-provider-local/local-account-phone-experience.png)

### <a name="pricing"></a>Prijzen

Eenmalige wacht woorden worden naar uw gebruikers verzonden met behulp van SMS-berichten. Afhankelijk van uw mobiele netwerk operator, worden er mogelijk kosten in rekening gebracht voor elk bericht dat wordt verzonden. Zie de sectie **afzonderlijke kosten** van [Azure Active Directory B2C prijzen](https://azure.microsoft.com/pricing/details/active-directory-b2c/)voor prijs informatie.

> [!NOTE]
> Multi-factor Authentication (MFA) is standaard uitgeschakeld wanneer u een gebruikers stroom configureert met aanmelding via de telefoon. U kunt MFA inschakelen in gebruikers stromen met aanmelding via de telefoon, maar omdat een telefoon nummer wordt gebruikt als de primaire id, is e-mail One-time wachtwoord code de enige optie die beschikbaar is voor de tweede verificatie factor.

### <a name="phone-recovery"></a>Herstel via telefoon

Wanneer u aanmelden bij de telefoon inschakelt voor uw gebruikers stromen, is het ook een goed idee om de functie voor herstel van e-mail in te scha kelen. Met deze functie kan een gebruiker een e-mail adres opgeven dat kan worden gebruikt om hun account te herstellen wanneer ze hun telefoon niet hebben. Dit e-mail adres wordt alleen gebruikt voor account herstel. Het kan niet worden gebruikt voor aanmelding.

- Wanneer de prompt voor het herstellen **van** E-mail is ingeschakeld, wordt een gebruiker die zich voor de eerste keer aanmeldt, gevraagd om een back-up te controleren. Een gebruiker die geen herstel bericht heeft gegeven voordat wordt gevraagd om een back-up-e-mail te verifiëren tijdens de volgende aanmelding.

- Wanneer het e-mail adres voor herstel is **uitgeschakeld**, wordt de prompt voor het herstellen van een gebruiker niet weer gegeven.
 
In de volgende scherm afbeeldingen wordt de stroom voor telefoon herstel gedemonstreerd:

![Gebruikers stroom voor telefoon herstel](./media/identity-provider-local/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in-preview"></a>Aanmelding via telefoon of e-mail (preview-versie)

U kunt ervoor kiezen om de [aanmelding](#phone-sign-in-preview)met de telefoon en het [e-mail bericht](#email-sign-in)te combi neren. Op de registratie pagina van een gebruiker kan een telefoon nummer of e-mail adres worden getypt. Op basis van de gebruikers invoer neemt Azure AD B2C de gebruiker de bijbehorende stroom. 

![Registratie op telefoon of e-mail adres of aanmeldings ervaring](./media/identity-provider-local/local-account-phone-and-email-experience.png)

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>Instellingen voor de lokale account-ID-provider configureren

U kunt de lokale id-providers configureren die beschikbaar zijn voor gebruik in een gebruikers stroom door de providers (e-mail adres, gebruikers naam of telefoon nummer) in of uit te scha kelen.  U kunt meer dan één lokale id-provider ingeschakeld op Tenant niveau.

Een gebruikers stroom kan alleen worden geconfigureerd voor het gebruik van een van de lokale account-id-providers tegelijk. Elke gebruikers stroom kan een andere provider voor de lokale account-id hebben ingesteld, als er meer dan één is ingeschakeld op Tenant niveau.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Azure AD-Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Onder **Beheren** selecteert u **Id-providers**.
1. In de lijst met id-providers selecteert u **Lokaal account**.
1. Selecteer op de pagina **lokale IDP configureren** ten minste één van de toegestane identiteits typen die consumenten kunnen gebruiken om hun lokale accounts te maken in uw Azure AD B2C-Tenant.
1. Selecteer **Opslaan**.

## <a name="configure-your-user-flow"></a>Uw gebruikers stroom configureren

1. Selecteer **Azure AD B2C** in het menu links van de Azure Portal.
1. Selecteer onder **beleids regels** **gebruikers stromen (beleid)**.
1. Selecteer de gebruikers stroom waarvoor u de registratie-en aanmeldings ervaring wilt configureren.
1. **Id-providers** selecteren
1. Onder de **lokale accounts** selecteert u een van de volgende opties: **e-mail aanmelding**,  **gebruikers-id-aanmelding**, **telefonische aanmelding**, **telefonische/e-mail aanmelding** of **geen**.

### <a name="enable-the-recovery-email-prompt"></a>De waarschuwing voor het herstellen van e-mail inschakelen

Als u de optie voor **telefoon** **-en e-mail aanmelding** kiest, schakelt u de prompt voor het herstellen van e-mail in.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer in Azure AD B2C onder **beleids regels** **gebruikers stromen**.
1. Selecteer de gebruikers stroom in de lijst.
1. Selecteer onder **Alle instellingen** de optie **Eigenschappen**.
1. Naast het **inschakelen van het e-mail bericht voor herstel van telefoon nummer en aanmelden (preview)**, selecteert u:
   - Aan om de prompt voor het herstellen **van** e-mail weer te geven tijdens de registratie en het aanmelden.
   - **Uit** om de waarschuwing voor het herstellen van e-mail te verbergen.
1. Selecteer **Opslaan**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>Het Starter Pack ophalen

Aangepaste beleids regels zijn een set van XML-bestanden die u uploadt naar uw Azure AD B2C-Tenant om gebruikers trajecten te definiëren. We bieden Starter Packs met verschillende vooraf ontwikkelde beleids regels. Down load het relevante Starter Pack: 

- [E-mail aanmelding](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [Gebruikers naam aanmelding](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [Aanmelden via de telefoon](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). Selecteer de [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml) relying party-beleid. 
- [Aanmelden via telefoon of e-mail](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). Selecteer de [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml) relying party-beleid.

Nadat u het Start pakket hebt gedownload.

1. Vervang in elk bestand de teken reeks door `yourtenant` de naam van uw Azure AD B2C-Tenant. Als de naam van uw B2C-Tenant bijvoorbeeld *contosob2c* is, worden alle exemplaren van `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com` .

1. Volg de stappen in de sectie [toepassings-Id's toevoegen aan het aangepaste beleid](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) van aan de [slag met aangepast beleid in azure Active Directory B2C](custom-policy-get-started.md). Bijvoorbeeld, update `/phone-number-passwordless/` **`Phone_Email_Base.xml`** met de **toepassings-id's (client)** van de twee toepassingen die u hebt geregistreerd bij het volt ooien van de vereisten, *IdentityExperienceFramework* en *ProxyIdentityExperienceFramework*.
1. De beleids bestanden uploaden

::: zone-end

## <a name="next-steps"></a>Volgende stappen

- [Externe ID-providers toevoegen](add-identity-provider.md)
- [Een gebruikersstroom maken](tutorial-create-user-flows.md)
