---
title: Zelf studie voor het configureren van Azure Active Directory B2C met Nevis
titleSuffix: Azure AD B2C
description: Meer informatie over het integreren van Azure AD B2C verificatie met Nevis voor verificatie zonder wacht woord
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 282ec6a25dc381dc51f28534d272bae57d2e792e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98674990"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>Zelf studie voor het configureren van Nevis met Azure Active Directory B2C voor verificatie zonder wacht woord

In deze voorbeeld zelfstudie vindt u informatie over het uitbreiden van Azure AD B2C met  [Nevis](https://www.nevis.net/solution/authentication-cloud) om verificatie zonder wacht woord in te scha kelen. Nevis biedt een mobiele, volledig brandende eindgebruikers ervaring met Nevis Access-app voor sterke klanten verificatie en voldoet aan de vereisten voor de betalings Services-instructie 2 (PSD2).

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

- Een [proef account](https://www.nevis-security.com/aadb2c/) van Nevis

- Een Azure AD-abonnement Als u er nog geen hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.

- Een [Azure AD B2C-Tenant](./tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.

- Geconfigureerde Azure AD B2C-omgeving voor het gebruik van [aangepast beleid](./custom-policy-get-started.md), als u Nevis wilt integreren in uw aanmeldings beleids stroom.

## <a name="scenario-description"></a>Scenariobeschrijving

In dit scenario voegt u volledig bemerkte Access-app toe aan uw back-end-toepassing voor verificatie met een wacht woord. De volgende onderdelen vormen de oplossing:

- Een Azure AD B2C-Tenant, met een gecombineerd aanmeldings-en registratie beleid voor uw back-end
- Nevis-exemplaar en de REST API om Azure AD B2C te verbeteren
- Uw eigen app met merken toegang

In het diagram wordt de implementatie weer gegeven.

![Aanmeldings stroom op hoog niveau met Azure AD B2C en Nevis](./media/partner-nevis/nevis-architecture-diagram.png)

|Stap | Beschrijving |
|:-----| :-----------|
| 1. | Een gebruiker probeert zich aan te melden of zich aan te melden bij een toepassing via Azure AD B2C aanmeldings-en registratie beleid.
| 2. | Tijdens het aanmelden wordt de Nevis Access-app geregistreerd bij het gebruikers apparaat met behulp van een QR-code. Er wordt een persoonlijke sleutel gegenereerd op het apparaat van de gebruiker en wordt gebruikt voor het ondertekenen van de gebruikers aanvragen.
| 3. |  Azure AD B2C gebruikt een onderliggend technisch profiel om de aanmelding met de Nevis-oplossing te starten.
| 4. | De aanmeldings aanvraag wordt verzonden naar de app voor toegang, hetzij als een push bericht, QR-code of een diep gaande koppeling.
| 5. | De gebruiker keurt de aanmeldings poging met hun biometrie goed. Er wordt vervolgens een bericht geretourneerd naar Nevis, waarmee de aanmelding wordt gecontroleerd met de opgeslagen open bare sleutel.
| 6. | Azure AD B2C stuurt een laatste aanvraag naar Nevis om te bevestigen dat de aanmelding is voltooid.
| 7. |Op basis van het geslaagde/mislukte bericht van Azure AD B2C gebruiker wordt toegang tot de toepassing verleend/geweigerd.

## <a name="integrate-your-azure-ad-b2c-tenant"></a>Uw Azure AD B2C-Tenant integreren

### <a name="onboard-to-nevis"></a>Onboarden naar Nevis 

[Meld u aan voor een Nevis-account](https://www.nevis-security.com/aadb2c/).
U ontvangt twee e-mail berichten:

1. Melding van een beheer account

2. Een uitnodiging voor een mobiele app.

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>Uw Azure AD B2C-Tenant toevoegen aan uw Nevis-account

1. Kopieer uw beheer sleutel naar het klem bord via de e-mail met de proef versie van het Nevis-beheer account.

2. Open https://console.nevis.cloud/ in een browser.

3. Meld u aan bij de beheer console met uw sleutel.

4. **Instantie toevoegen** selecteren

5. Selecteer de zojuist gemaakte instantie om deze te openen.

6. Selecteer in de navigatie balk aan de zijkant **aangepaste integraties**

7. Selecteer **aangepaste integratie toevoegen**.

8. Voer bij integratie naam uw Azure AD B2C Tenant naam in.

9. Voer voor URL/domein `https://yourtenant.onmicrosoft.com`

10. Selecteer **Volgende**

>[!NOTE]
>U hebt later het toegangs token van Nevis nodig.

11. Selecteer **Gereed**.

### <a name="install-the-nevis-access-app-on-your-phone"></a>De Nevis Access-app installeren op uw telefoon

1. Open in het Nevis-e-mail bericht voor mobiele app-proef versie de uitnodiging om de **app te testen** .

2. Installeer de app.

3. Volg de instructies die worden gegeven om de Nevis Access-app te installeren.

### <a name="integrate-azure-ad-b2c-with-nevis"></a>Azure AD B2C integreren met Nevis

1. Open de [Azure Portal](https://portal.azure.com/).

2. Schakel over naar uw Azure AD B2C-Tenant. Zorg ervoor dat u de juiste Tenant hebt geselecteerd, omdat de Azure AD B2C Tenant doorgaans zich in een afzonderlijke Tenant bevindt.

3. Selecteer in het menu **Identity experience Framework (IEF)**

4. **Beleids sleutels** selecteren

5. Selecteer **toevoegen** en maak een nieuwe sleutel met de volgende instellingen:

      a. Selecteer **hand matig** in opties

      b. Naam instellen op **AuthCloudAccessToken**

      c. Plak het eerder opgeslagen **toegangs token voor Nevis** in het veld geheim

      d. Selecteer **versleuteling** voor het sleutel gebruik

      e. Selecteer **Maken**

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>De nevis.html configureren en uploaden naar Azure Blob Storage

1. Ga in uw identiteits omgeving (IDE) naar de map [**beleid**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Open het bestand  [**nevis.html**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html) .

3. Vervang de  **authentication_cloud_url** door de URL van uw Nevis-beheer console `https://<instance_id>.mauth.nevis.cloud` .

4. **Sla** de wijzigingen in het bestand op.

5. Volg de [instructies](./customize-ui-with-html.md#2-create-an-azure-blob-storage-account) en upload het **nevis.html** -bestand naar uw Azure Blob-opslag.

6. Volg de [instructies](./customize-ui-with-html.md#3-configure-cors) om cross-Origin resource SHARING (CORS) voor dit bestand in te scha kelen.

7. Zodra het uploaden is voltooid en CORS is ingeschakeld, selecteert u het **nevis.html** -bestand in de lijst.

8. Op het tabblad **overzicht** , naast de **URL**, selecteert u het pictogram **koppeling kopiëren** .

9. Open de koppeling in een nieuw browser tabblad om te controleren of er een grijs vak wordt weer gegeven.

>[!NOTE]
>U hebt de BLOB-koppeling later nodig.

### <a name="customize-your-trustframeworkbasexml"></a>Uw TrustFrameworkBase.xml aanpassen

1. Ga in uw IDE naar de map [**beleid**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Open het [**TrustFrameworkBase.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml) -bestand.

3. Vervang **yourtenant** door de naam van uw Azure-Tenant account in de **TenantId**.

4. Vervang **yourtenant** door de naam van uw Azure-Tenant account in **PublicPolicyURI**.

5. Alle **authentication_cloud_url** instanties vervangen door de URL van uw Nevis-beheer console

6. **Sla** de wijzigingen in het bestand op.

### <a name="customize-your-trustframeworkextensionsxml"></a>Uw TrustFrameworkExtensions.xml aanpassen

1. Ga in uw IDE naar de map [**beleid**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Open het [**TrustFrameworkExtensions.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml) -bestand.

3. Vervang **yourtenant** door de naam van uw Azure-Tenant account in de **TenantId**.

4. Vervang **yourtenant** door de naam van uw Azure-Tenant account in **PublicPolicyURI**.

5. Onder **BasePolicy**, in de **TenantId**, vervangt u _yourtenant_ door de naam van uw Azure-Tenant account.

6. Vervang **LoadUri** onder **BuildingBlocks** door de URL van de BLOB-koppeling van uw _nevis.html_ in uw Blob Storage-account.

7. **Sla** het bestand op.

### <a name="customize-your-signuporsigninxml"></a>Uw SignUpOrSignin.xml aanpassen

1. Ga in uw IDE naar de map [**beleid**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Open het [**SignUpOrSignin.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml) -bestand.

3. Vervang **yourtenant** door de naam van uw Azure-Tenant account in de **TenantId**.

4. Vervang **yourtenant** door de naam van uw Azure-Tenant account in **PublicPolicyUri**.

5. Onder **BasePolicy**, in **TenantId**, vervangt u **yourtenant** door de naam van uw Azure-Tenant account.

6. **Sla** het bestand op.

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>Uw aangepaste beleid uploaden naar Azure AD B2C

1. Open de start pagina van de [Azure AD B2C Tenant](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) .

2. Selecteer een **Framework voor identiteits ervaring**.

3. Selecteer **aangepast beleid uploaden**.

4. Selecteer het **TrustFrameworkBase.xml** bestand dat u hebt gewijzigd.

5. Selecteer het selectie vakje **aangepast beleid overschrijven als dit al bestaat** .
6. Selecteer **Uploaden**.

7. Herhaal stap 5 en 6 voor **TrustFrameworkExtensions.xml**.

8. Herhaal stap 5 en 6 voor **SignUpOrSignin.xml**.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

### <a name="test-account-creation-and-nevis-access-app-setup"></a>Het maken van accounts en de Nevis Access-app instellen

1. Open de start pagina van de [Azure AD B2C Tenant](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) .

2. Selecteer een **Framework voor identiteits ervaring**.

3. Schuif omlaag naar aangepast beleid en selecteer **B2C_1A_signup_signin**.

4. Selecteer **nu uitvoeren**.

5. Selecteer **nu registreren** in het pop-upvenster.

6. Voeg uw e-mail adres toe.

7. Selecteer **verificatie code verzenden**.

8. Kopieer de verificatie code uit het e-mail bericht.

9. Selecteer **Verifiëren**.

10. Vul het formulier in met het nieuwe wacht woord en de weergave naam.

11. Selecteer **Maken**.

12. U gaat naar de scan pagina voor QR-code.

13. Open de **Nevis Access-app** op uw telefoon.

14. Selecteer **face id**.

15. Wanneer het scherm met de melding registratie van de **verificator is geslaagd**, selecteert u **door gaan**.

16. Verifieer opnieuw op uw telefoon met uw gezicht.

17. U gaat naar de landings pagina voor [JWT.MS](https://jwt.ms) waarop de gedecodeerde token Details worden weer gegeven.

### <a name="test-the-pure-passwordless-sign-in"></a>De pure aanmelding zonder wacht woord testen

1. Selecteer onder **identiteits ervaring-Framework** de **B2C_1A_signup_signin**.

2. Selecteer **nu uitvoeren**.

3. Selecteer **wacht woordloze verificatie** in het pop-upvenster.

4. Voer uw e-mailadres in.

5. Selecteer **Doorgaan**.

6. Selecteer op uw telefoon, in meldingen, de **melding Nevis Access-app**.

7. Verifieer met uw gezicht.

8. U wordt automatisch naar de landings pagina van de [JWT.MS](https://jwt.ms) geleid waarop uw tokens worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie

- [Aangepast beleid in Azure AD B2C](./custom-policy-overview.md)

- [Aan de slag met aangepast beleid in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)