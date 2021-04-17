---
title: Zelfstudie voor het configureren Azure Active Directory B2C met Nevis
titleSuffix: Azure AD B2C
description: Meer informatie over het integreren Azure AD B2C verificatie met Nevis voor verificatie zonder wachtwoord
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 42e244249ecb0539637918ae2439bdb4f5da4b38
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588476"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>Zelfstudie voor het configureren van Nevis Azure Active Directory B2C voor verificatie zonder wachtwoord

In deze voorbeeldzelfstudie leert u hoe u uw Azure AD B2C  [met Nevis](https://www.nevis.net/en/solution/authentication-cloud) kunt uitbreiden om verificatie zonder wachtwoord in te schakelen. Nevis biedt een mobiele, volledig merkbare eindgebruikerservaring met de Nevis Access-app om sterke klantverificatie te bieden en te voldoen aan payment services Directive 2 (PSD2) transactievereisten.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

- Een [Nevis-proefaccount](https://www.nevis-security.com/aadb2c/)

- Een Azure AD-abonnement Als u nog geen account hebt, krijgt u een [gratis account.](https://azure.microsoft.com/free/)

- Een [Azure AD B2C tenant](./tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.

- Geconfigureerd Azure AD B2C omgeving voor het gebruik van aangepast beleid [als](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)u Nevis wilt integreren in uw aanmeldingsbeleidsstroom.

## <a name="scenario-description"></a>Scenariobeschrijving

In dit scenario voegt u een volledig merktoegangs-app toe aan uw back-endtoepassing voor verificatie zonder wachtwoord. De volgende onderdelen maken de oplossing uit:

- Een Azure AD B2C tenant, met een gecombineerd aanmeldings- en aanmeldingsbeleid voor uw back-end
- Nevis-exemplaar en de REST API om de prestaties Azure AD B2C
- Uw eigen toegangs-app met huismerk

In het diagram ziet u de implementatie.

![Aanmeldingsstroom voor wachtwoorden op hoog niveau met Azure AD B2C en Nevis](./media/partner-nevis/nevis-architecture-diagram.png)

|Stap | Beschrijving |
|:-----| :-----------|
| 1. | Een gebruiker probeert zich aan te melden of zich aan te melden bij een toepassing via Azure AD B2C beleid voor aanmelden en registreren.
| 2. | Tijdens de registratie wordt de Nevis Access-app met behulp van een QR-code geregistreerd bij het gebruikersapparaat. Er wordt een persoonlijke sleutel gegenereerd op het gebruikersapparaat en deze wordt gebruikt om de aanvragen van de gebruiker te ondertekenen.
| 3. |  Azure AD B2C maakt gebruik van een technisch RESTful-profiel om de aanmelding te starten met de Nevis-oplossing.
| 4. | De aanmeldingsaanvraag wordt verzonden naar de toegangs-app als een pushbericht, QR-code of als een dieptekoppeling.
| 5. | De gebruiker keurt de aanmeldingspoging goed met zijn biometrie. Er wordt vervolgens een bericht geretourneerd naar Nevis, waarmee de aanmelding met de opgeslagen openbare sleutel wordt geverifieerd.
| 6. | Azure AD B2C een laatste aanvraag naar Nevis om te bevestigen dat de aanmelding is voltooid.
| 7. |Op basis van het bericht dat de toepassing is geslaagd/mislukt Azure AD B2C gebruiker toegang tot de toepassing verleend/geweigerd.

## <a name="integrate-your-azure-ad-b2c-tenant"></a>Uw tenant Azure AD B2C integreren

### <a name="onboard-to-nevis"></a>Onboarden naar Nevis 

[Meld u aan voor een Nevis-account.](https://www.nevis-security.com/aadb2c/)
U ontvangt twee e-mailberichten:

1. Een melding voor een beheeraccount

2. Een uitnodiging voor een mobiele app.

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>Uw Azure AD B2C toevoegen aan uw Nevis-account

1. Kopieer uw beheersleutel naar het klembord in het proefabonnement voor het Nevis-beheeraccount.

2. Open https://console.nevis.cloud/ in een browser.

3. Meld u aan bij de beheerconsole met uw sleutel.

4. Selecteer **Exemplaar toevoegen**

5. Selecteer het zojuist gemaakte exemplaar om het te openen.

6. Selecteer aangepaste integraties in de **navigatiebalk aan de zijkant**

7. Selecteer **Aangepaste integratie toevoegen.**

8. Voer bij Integratienaam de naam van Azure AD B2C tenant in.

9. Voer bij URL/domein in `https://yourtenant.onmicrosoft.com`

10. Selecteer **Volgende**

>[!NOTE]
>U hebt het Nevis-toegangs token later nodig.

11. Selecteer **Gereed**.

### <a name="install-the-nevis-access-app-on-your-phone"></a>De Nevis Access-app installeren op uw telefoon

1. Open vanuit het e-mailadres van de proefversie van de mobiele Nevis-app de **uitnodiging voor de Test Flight-app.**

2. Installeer de app.

3. Volg de instructies voor het installeren van de Nevis Access-app.

### <a name="integrate-azure-ad-b2c-with-nevis"></a>Azure AD B2C integreren met Nevis

1. Open de [Azure Portal](https://portal.azure.com/).

2. Schakel over naar uw Azure AD B2C tenant. Zorg ervoor dat u de juiste tenant hebt geselecteerd, omdat de Azure AD B2C tenant zich meestal in een afzonderlijke tenant.

3. Selecteer in het menu Identity Experience Framework **(IEF)**

4. **Beleidssleutels selecteren**

5. Selecteer **Toevoegen** en maak een nieuwe sleutel met de volgende instellingen:

      a. Selecteer **Handmatig** in Opties

      b. Stel Naam in **op AuthCloudAccessToken**

      c. Plak het eerder opgeslagen **Nevis Access Token** in het veld Geheim

      d. Selecteer versleuteling voor **sleutelgebruik**

      e. Selecteer **Maken**

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>Configureer en upload de nevis.html naar Azure Blob Storage

1. Ga in uw Identiteitsomgeving (IDE) naar de [**map beleid.**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy)

2. Open het [**nevis.html-bestand.**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html)

3. Vervang de  **authentication_cloud_url** door de URL van uw Nevis-beheerconsole - `https://<instance_id>.mauth.nevis.cloud` .

4. **Sla** de wijzigingen in het bestand op.

5. Volg de [instructies en](./customize-ui-with-html.md#2-create-an-azure-blob-storage-account) upload het **nevis.html-bestand** naar uw Azure Blob-opslag.

6. Volg de [instructies](./customize-ui-with-html.md#3-configure-cors) en schakel Cross-Origin Resource Sharing (CORS) in voor dit bestand.

7. Zodra het uploaden is voltooid en CORS is ingeschakeld, selecteert u **hetnevis.html-bestand** in de lijst.

8. Selecteer op **het** tabblad Overzicht naast de **URL** het pictogram **voor de kopieerkoppeling.**

9. Open de koppeling in een nieuw browsertabblad om er zeker van te zijn dat er een grijs vak wordt weergegeven.

>[!NOTE]
>U hebt de blobkoppeling later nodig.

### <a name="customize-your-trustframeworkbasexml"></a>Uw TrustFrameworkBase.xml

1. Ga in uw IDE naar de [**map beleid.**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy)

2. Open [](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml) hetTrustFrameworkBase.xmlbestand.

3. Vervang **yourtenant door** de naam van uw Azure-tenantaccount in de **TenantId**.

4. Vervang **yourtenant door** de naam van uw Azure-tenantaccount in **PublicPolicyURI.**

5. Vervang alle **authentication_cloud_url** door de URL van uw Nevis-beheerconsole

6. **Sla** de wijzigingen in het bestand op.

### <a name="customize-your-trustframeworkextensionsxml"></a>Uw TrustFrameworkExtensions.xml

1. Ga in uw IDE naar de [**map beleid.**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy)

2. Open [](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml) hetTrustFrameworkExtensions.xmlbestand.

3. Vervang **yourtenant door** de naam van uw Azure-tenantaccount in de **TenantId**.

4. Vervang **yourtenant door** de naam van uw Azure-tenantaccount in **PublicPolicyURI.**

5. Vervang **onder BasePolicy** in **de TenantId** ook _yourtenant_ door de naam van uw Azure-tenantaccount.

6. Vervang **loaduri onder BuildingBlocks** door **de** blobkoppelings-URL van uwnevis.htm _l_ in uw blobopslagaccount.

7. **Sla** het bestand op.

### <a name="customize-your-signuporsigninxml"></a>Uw SignUpOrSignin.xml

1. Ga in uw IDE naar de [**map beleid.**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy)

2. Open het [**SignUpOrSignin.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml) bestand.

3. Vervang **yourtenant door** de naam van uw Azure-tenantaccount in **de TenantId.**

4. Vervang **yourtenant door** de naam van uw Azure-tenantaccount in **PublicPolicyUri.**

5. Vervang **onder BasePolicy** in **TenantId** ook **yourtenant** door de naam van uw Azure-tenantaccount.

6. **Sla** het bestand op.

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>Uw aangepaste beleid uploaden naar Azure AD B2C

1. Open uw [Azure AD B2C tenant.](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview)

2. Selecteer **Identity Experience Framework**.

3. Selecteer **Aangepast beleid uploaden.**

4. Selecteer het **TrustFrameworkBase.xml** bestand dat u hebt gewijzigd.

5. Schakel het **selectievakje Het aangepaste beleid overschrijven als het al bestaat** in.
6. Selecteer **Uploaden**.

7. Herhaal stap 5 en 6 voor **TrustFrameworkExtensions.xml**.

8. Herhaal stap 5 en 6 voor **SignUpOrSignin.xml**.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

### <a name="test-account-creation-and-nevis-access-app-setup"></a>Het maken van een account testen en de Nevis Access-app instellen

1. Open uw [Azure AD B2C tenant.](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview)

2. Selecteer **Identity Experience Framework**.

3. Schuif omlaag naar Aangepast beleid en selecteer **B2C_1A_signup_signin.**

4. Selecteer **Nu uitvoeren.**

5. Selecteer nu registreren in het **pop-upvenster.**

6. Voeg uw e-mailadres toe.

7. Selecteer **Verificatiecode verzenden.**

8. Kopieer de verificatiecode uit het e-mailbericht.

9. Selecteer **Verifiëren**.

10. Vul in het formulier uw nieuwe wachtwoord en Weergavenaam in.

11. Selecteer **Maken**.

12. U wordt naar de scanpagina van de QR-code doorgenomen.

13. Open de **Nevis Access-app op uw telefoon.**

14. Selecteer **Face ID.**

15. Wanneer in het scherm wordt weergegeven dat **de Authenticator-registratie is geslaagd,** selecteert u **Doorgaan.**

16. Verifieert u op uw telefoon opnieuw met uw gezicht.

17. U wordt naar de landingspagina [jwt.ms](https://jwt.ms) met de details van uw gedecodeerde token.

### <a name="test-the-pure-passwordless-sign-in"></a>De pure aanmelding zonder wachtwoord testen

1. Selecteer **Identity Experience Framework** onder **B2C_1A_signup_signin**.

2. Selecteer **Nu uitvoeren.**

3. Selecteer in het pop-upvenster **De optie Verificatie zonder wachtwoord.**

4. Voer uw e-mailadres in.

5. Selecteer **Doorgaan**.

6. Selecteer op uw telefoon in meldingen de optie **Nevis Access-app-melding**.

7. Verifiëren met uw gezicht.

8. U wordt automatisch naar de landingspagina van [jwt.ms](https://jwt.ms) om uw tokens weer te geven.

## <a name="next-steps"></a>Volgende stappen

Lees de volgende artikelen voor meer informatie

- [Aangepast beleid in Azure AD B2C](./custom-policy-overview.md)

- [Aan de slag met aangepaste beleidsregels in Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
