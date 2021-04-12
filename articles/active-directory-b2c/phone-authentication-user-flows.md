---
title: De ID-provider van het lokale account instellen
titleSuffix: Azure AD B2C
description: Definieer de identiteits typen die u kunt gebruiken (e-mail adres, gebruikers naam, telefoon nummer) voor verificatie van lokale accounts wanneer u gebruikers stromen instelt in uw Azure Active Directory B2C-Tenant.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dd21c1dca0dd54331780ba98f9c53d5b99d6b4e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100557228"
---
# <a name="set-up-phone-sign-up-and-sign-in-for-user-flows-preview"></a>Registratie van de telefoon en aanmelding voor gebruikers stromen instellen (preview-versie)

> [!NOTE]
> De open bare preview-versie van de e-mail functies voor het registreren en herstellen van berichten voor gebruikers stromen is beschikbaar.

Naast het e-mail adres en de gebruikers naam kunt u het telefoon nummer inschakelen als een Tenant voor het instellen van een zelfstandige gebruiker door het aanmelden van de telefoon toe te voegen en u aan te melden bij uw lokale account-ID-provider. Nadat u aanmelding via de telefoon hebt ingeschakeld en u zich hebt aangemeld voor lokale accounts, kunt u een telefoon aanmelding toevoegen aan uw gebruikers stromen.

Het instellen van een telefoon aanmelding en aanmelding in een gebruikers stroom omvat de volgende stappen:

- [Configureer het registreren van de telefoon en het aanmelden bij Tenant-breed](#configure-phone-sign-up-and-sign-in-tenant-wide) in de ID-provider van uw lokale account om een telefoon nummer als de identiteit van een gebruiker te accepteren. 

- [Voeg aanmelding via de telefoon toe aan uw gebruikers stroom](#add-phone-sign-up-to-a-user-flow) zodat gebruikers zich kunnen registreren voor uw toepassing met behulp van hun telefoon nummer.

- [Schakel de optie voor het herstellen van e-mail (preview)](#enable-the-recovery-email-prompt-preview) in om gebruikers een e-mail bericht te laten opgeven dat kan worden gebruikt om hun account te herstellen wanneer ze hun telefoon niet hebben.

Multi-factor Authentication (MFA) is standaard uitgeschakeld wanneer u een gebruikers stroom configureert met aanmelding via de telefoon. U kunt MFA inschakelen in gebruikers stromen met aanmelding via de telefoon, maar omdat een telefoon nummer wordt gebruikt als de primaire id, is e-mail One-time wachtwoord code de enige optie die beschikbaar is voor de tweede verificatie factor.

## <a name="configure-phone-sign-up-and-sign-in-tenant-wide"></a>Aanmelding via de telefoon configureren en aanmelden op Tenant niveau

E-mail aanmelding is standaard ingeschakeld in de instellingen van uw lokale account-ID-provider. U kunt de identiteits typen die u in uw Tenant ondersteunt, wijzigen door e-mail registratie, gebruikers naam of telefoon nummer te selecteren of uit te scha kelen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Azure AD-Tenant bevat.

3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.

4. Onder **Beheren** selecteert u **Id-providers**.

5. In de lijst met id-providers selecteert u **Lokaal account**.

   ![Id-providers lokale account selecteren](media/phone-authentication-user-flows/identity-provider-local-account.png)

1. Controleer op de pagina **lokale IDP configureren** of **telefoon** is geselecteerd als een van de toegestane identiteits typen die consumenten kunnen gebruiken om hun lokale accounts te maken in uw Azure AD B2C-Tenant.

   ![Selecteer de toegestane identiteits typen](media/phone-authentication-user-flows/configure-local-idp.png)

1. Selecteer **Opslaan**.

## <a name="add-phone-sign-up-to-a-user-flow"></a>Aanmelding via de telefoon toevoegen aan een gebruikers stroom

Nadat u de optie voor het registreren van de telefoon hebt toegevoegd als een identiteit voor lokale accounts, kunt u deze toevoegen aan gebruikers stromen zolang ze de meest recente **Aanbevolen** versie van de gebruikers stroom zijn. Hier volgt een voor beeld waarin wordt getoond hoe u een telefoon aanmelding kunt toevoegen aan nieuwe gebruikers stromen. Maar u kunt ook telefoon aanmelding toevoegen aan bestaande gebruikers stromen voor aanbevolen versies (Selecteer **Gebruikersstromen**  >  *gebruikers stroom naam* van het  >    >  **lokale account telefoon-aanmelden**). 

Hier volgt een voor beeld waarin wordt getoond hoe u een telefoon aanmelding kunt toevoegen aan een nieuwe gebruikers stroom.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.

    ![B2C-tenant, deelvenster Map + Abonnement, Azure-portal](./media/phone-authentication-user-flows/directory-subscription-pane.png)

3. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
4. Selecteer onder **Beleid** de optie **Gebruikersstromen** en selecteer vervolgens **Nieuwe gebruikersstroom**.

    ![De pagina Gebruikersstromen in de portal met de knop Nieuwe gebruikersstroom gemarkeerd](./media/phone-authentication-user-flows/signup-signin-user-flow.png)

5. Selecteer op de pagina **Een gebruikersstroom maken** de gebruikersstroom **Registreren en aanmelden**.

    ![Een gebruikersstroompagina selecteren waarop registratie en aanmelding is gemarkeerd](./media/phone-authentication-user-flows/select-user-flow-type.png)

6. Onder **Selecteer een versie** selecteert u **Aanbevolen** en vervolgens **Maken**. ([Meer informatie](user-flow-versions.md) over gebruikersstroomversies.)

    ![Knop gebruikers stroom maken](./media/phone-authentication-user-flows/select-version.png)

7. Voer een **Naam** in voor de gebruikersstroom. Bijvoorbeeld *signupsignin1*.
8. Selecteer in de sectie **id-providers** onder **lokale accounts** de optie **telefoon aanmelding**.

   ![Optie voor telefoon gesprek voor gebruikers stroom geselecteerd](media/phone-authentication-user-flows/user-flow-phone-signup.png)

9. Onder **Social id-providers** selecteert u andere id-providers die u voor deze gebruikers stroom wilt toestaan.

   > [!NOTE]
   > Multi-factor Authentication (MFA) is standaard uitgeschakeld voor het registreren van gebruikers stromen. U kunt MFA inschakelen voor een gebruikers stroom voor het aanmelden via een telefoon, maar omdat een telefoon nummer wordt gebruikt als de primaire id, is e-mail eenmalige wachtwoord code de enige optie die beschikbaar is voor de tweede verificatie factor.

1. Kies in de sectie **gebruikers kenmerken en Token claims** de claims en kenmerken die u wilt verzamelen en verzenden van de gebruiker tijdens de registratie. Selecteer bijvoorbeeld **Meer weergeven** en kies vervolgens kenmerken en claims voor **Land/regio**, **Weergavenaam** en **Postcode**. Selecteer **OK**.

1. Selecteer **Maken** om de gebruikersstroom toe te voegen. Het voorvoegsel *B2C_1* wordt automatisch voor de naam geplaatst.

## <a name="enable-the-recovery-email-prompt-preview"></a>De waarschuwing voor het herstellen van e-mail inschakelen (preview)

Wanneer u aanmelden bij de telefoon inschakelt voor uw gebruikers stromen, is het ook een goed idee om de functie voor herstel van e-mail in te scha kelen. Met deze functie kan een gebruiker een e-mail adres opgeven dat kan worden gebruikt om hun account te herstellen wanneer ze hun telefoon niet hebben. Dit e-mail adres wordt alleen gebruikt voor account herstel. Het kan niet worden gebruikt voor aanmelding.

- Wanneer de prompt voor het herstellen **van** E-mail is ingeschakeld, wordt een gebruiker die zich voor de eerste keer aanmeldt, gevraagd om een back-up te controleren. Een gebruiker die geen herstel bericht heeft gegeven voordat wordt gevraagd om een back-up-e-mail te verifiëren tijdens de volgende aanmelding.

- Wanneer het e-mail adres voor herstel is **uitgeschakeld**, wordt de prompt voor het herstellen van een gebruiker niet weer gegeven.
 
U kunt de prompt voor het herstellen van e-mail inschakelen in de eigenschappen van de gebruikers stroom.

> [!NOTE]
> Voordat u begint, moet u ervoor zorgen dat u [de telefoon aanmelding hebt toegevoegd aan uw gebruikers stroom](#add-phone-sign-up-to-a-user-flow) zoals hierboven wordt beschreven.

### <a name="to-enable-the-recovery-email-prompt"></a>De prompt voor het herstellen van e-mail inschakelen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
3. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
4. Selecteer in Azure AD B2C onder **beleids regels** **gebruikers stromen**.
5. Selecteer de gebruikers stroom in de lijst.
6. Selecteer onder **Alle instellingen** de optie **Eigenschappen**.
7. Naast het **inschakelen van het e-mail bericht voor herstel van telefoon nummer en aanmelden (preview)**, selecteert u:

   - Aan om de prompt voor het herstellen **van** e-mail weer te geven tijdens de registratie en het aanmelden.
   - **Uit** om de waarschuwing voor het herstellen van e-mail te verbergen.

    ![Eigenschappen van gebruikers stromen met de optie voor het inschakelen van e-mail ingeschakeld](./media/phone-authentication-user-flows/recovery-email-settings.png)

8. Selecteer **Opslaan**.

### <a name="to-test-the-recovery-email-prompt"></a>De prompt voor het herstellen van e-mail testen

Nadat u aanmelding via de telefoon en aanmelding hebt ingeschakeld en de herstel-e-mail in uw gebruikers stroom hebt, kunt u **gebruikers stroom uitvoeren** gebruiken om de gebruikers ervaring te testen.

1. Selecteer **Beleid** > **Gebruikersstromen** en selecteer vervolgens de gebruikersstroom die u hebt gemaakt. Op de overzichtspagina van de gebruikersstroom selecteert u **Gebruikersstroom uitvoeren**.

2. Voor **Toepassing** selecteert u de webtoepassing die u in stap 1 hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.

3. Selecteer **gebruikers stroom uitvoeren** en controleer het volgende gedrag:

   - Een gebruiker die zich voor de eerste keer aanmeldt, wordt gevraagd een herstel bericht op te geven. 
   - Een gebruiker die zich al heeft aangemeld, maar geen herstel bericht heeft gegeven, wordt gevraagd een e-mail adres op te geven bij het aanmelden.

4. Voer een e-mail adres in en selecteer **verificatie code verzenden**. Controleer of een code wordt verzonden naar het postvak in van de e-mail die u hebt ingevoerd. Haal de code op en voer deze in het vak **verificatie code** in. Selecteer vervolgens **code verifiëren**.

## <a name="next-steps"></a>Volgende stappen

- [Externe ID-providers toevoegen](add-identity-provider.md)
- [Een gebruikersstroom maken](tutorial-create-user-flows.md)