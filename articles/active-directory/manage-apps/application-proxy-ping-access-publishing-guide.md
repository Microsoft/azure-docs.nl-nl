---
title: Verificatie op basis van een header met PingAccess voor Azure AD-toepassingsproxy
description: Publiceer toepassingen met PingAccess en app-proxy om verificatie op basis van een header te ondersteunen.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 128e053016faf3ed2a9c53ad21f35a13f3ac1265
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258164"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Op headers gebaseerde verificatie voor eenmalige aanmelding met de toepassingsproxy en PingAccess

Azure Active Directory-toepassings proxy (Azure AD) is gekoppeld aan PingAccess, zodat uw Azure AD-klanten meer toegang hebben tot uw toepassingen. PingAccess biedt een andere optie dan een geïntegreerde, op [headers gebaseerde eenmalige aanmelding](application-proxy-configure-single-sign-on-with-headers.md).

## <a name="whats-pingaccess-for-azure-ad"></a>Wat is PingAccess voor Azure AD?

Met PingAccess voor Azure AD kunt u gebruikers toegang en eenmalige aanmelding (SSO) geven aan toepassingen die headers gebruiken voor verificatie. Application Proxy behandelt deze toepassingen hetzelfde als andere, waarbij Azure AD wordt gebruikt om de toegang te verifiëren en vervolgens het verkeer door te geven via de connectorservice. PingAccess bevindt zich voor de toepassingen en vertaalt het toegangs token van Azure AD in een header. De toepassing ontvangt vervolgens de verificatie in de indeling die kan worden gelezen.

De gebruikers merken hier niets van wanneer ze zich aanmelden om uw bedrijfstoepassingen te gebruiken. Ze kunnen nog steeds vanaf elke locatie op elk apparaat werken. De connectors van de toepassings proxy sturen extern verkeer naar alle apps, zonder rekening te houden met het verificatie type, zodat ze nog steeds automatisch worden geladen.

## <a name="how-do-i-get-access"></a>Hoe kan ik toegang krijgen?

Aangezien dit scenario afkomstig is van een partnership tussen Azure Active Directory en PingAccess, hebt u licenties nodig voor beide services. Azure Active Directory Premium-abonnementen omvatten echter een eenvoudige PingAccess-licentie die betrekking heeft op Maxi maal 20 toepassingen. Als u meer dan 20 toepassingen op basis van headers wilt publiceren, kunt u een extra licentie aanschaffen bij PingAccess.

Zie [Azure Active Directory-edities](../fundamentals/active-directory-whatis.md) voor meer informatie.

## <a name="publish-your-application-in-azure"></a>Uw toepassing publiceren in azure

Dit artikel is een voor de eerste keer publiceren van een toepassing met dit scenario. Naast de publicatie stappen wordt u begeleid bij het aan de slag met zowel de toepassings proxy als de PingAccess. Als u beide services al hebt geconfigureerd, maar een nieuwe vernieuwing wilt uitvoeren voor de publicatie stappen, gaat u naar de sectie [uw toepassing toevoegen aan Azure AD met toepassings proxy](#add-your-application-to-azure-ad-with-application-proxy) .

> [!NOTE]
> Aangezien dit scenario een partnerschap is tussen Azure AD en PingAccess, bestaan enkele van de instructies op de ping-identiteits site.

### <a name="install-an-application-proxy-connector"></a>Een toepassings proxy connector installeren

Als u de toepassings proxy hebt ingeschakeld en al een connector hebt geïnstalleerd, kunt u deze sectie overs Laan en [uw toepassing toevoegen aan Azure AD met toepassings proxy](#add-your-application-to-azure-ad-with-application-proxy).

De connector van de toepassings proxy is een Windows Server-service die het verkeer van uw externe werk nemers doorstuurt naar uw gepubliceerde toepassingen. Zie [zelf studie: een on-premises toepassing toevoegen voor externe toegang via toepassings proxy in azure Active Directory](application-proxy-add-on-premises-application.md)voor meer gedetailleerde installatie-instructies.

1. Meld u aan bij de [Azure Active Directory-Portal](https://aad.portal.azure.com/) als een toepassings beheerder. De pagina **Azure Active Directory beheer centrum** wordt weer gegeven.
1. Selecteer **Azure Active Directory**  >  **Application proxy**  >  **down load connector service**. De download pagina voor de **toepassings proxy connector** wordt weer gegeven.

   ![Application proxy-connector downloaden](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Volg de installatie-instructies.

Als u de connector downloadt, wordt de toepassings proxy automatisch ingeschakeld voor uw directory, maar als dat niet het geval is, kunt u **toepassings proxy inschakelen** selecteren.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Uw toepassing toevoegen aan Azure AD met toepassings proxy

Er zijn twee acties die u moet uitvoeren in de Azure Portal. Eerst moet u uw toepassing publiceren met toepassings proxy. Vervolgens moet u informatie verzamelen over de toepassing die u tijdens de PingAccess-stappen kunt gebruiken.

#### <a name="publish-your-application"></a>Uw toepassing publiceren

U moet eerst uw toepassing publiceren. Deze actie omvat:

- Uw on-premises toepassing toevoegen aan Azure AD
- Een gebruiker toewijzen voor het testen van de toepassing en het kiezen van op headers gebaseerde SSO
- De omleidings-URL voor de toepassing instellen
- Machtigingen verlenen voor gebruikers en andere toepassingen voor het gebruik van uw on-premises toepassing

Uw eigen on-premises toepassing publiceren:

1. Als u zich niet in de laatste sectie bevindt, meldt u zich aan bij de [Azure Active Directory Portal](https://aad.portal.azure.com/) als een toepassings beheerder.
1. **Zakelijke toepassingen** selecteren  >  **nieuwe toepassing**  >  **een lokale toepassing toevoegen**. De pagina **uw eigen on-premises toepassing toevoegen** wordt weer gegeven.

   ![Uw eigen on-premises toepassing toevoegen](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Vul de vereiste velden in met informatie over de nieuwe toepassing. Gebruik de onderstaande instructies voor de instellingen.

   > [!NOTE]
   > Zie [een on-premises app toevoegen aan Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)voor een meer gedetailleerd overzicht van deze stap.

   1. **Interne URL**: normaal gesp roken geeft u de URL op waarmee u naar de aanmeldings pagina van de app gaat wanneer u zich in het bedrijfs netwerk bevindt. Voor dit scenario moet de connector de PingAccess-proxy beschouwen als de front page van de toepassing. Gebruik deze indeling: `https://<host name of your PingAccess server>:<port>` . De poort is standaard 3000, maar u kunt deze configureren in PingAccess.

      > [!WARNING]
      > Voor dit type eenmalige aanmelding moet de interne URL `https` worden gebruikt en kan niet worden gebruikt `http` . Er is ook een beperking bij het configureren van een toepassing die niet twee apps dezelfde interne URL moeten hebben als Hiermee kan app proxy onderscheid tussen toepassingen onderhouden.

   1. **Methode vóór verificatie**: Kies **Azure Active Directory**.
   1. **URL in kopteksten vertalen**: Kies **Nee**.

   > [!NOTE]
   > Als dit uw eerste toepassing is, gebruikt u poort 3000 om te starten en gaat u terug om deze instelling bij te werken als u de PingAccess-configuratie wijzigt. Voor volgende toepassingen moet de poort overeenkomen met de listener die u hebt geconfigureerd in PingAccess. Meer informatie over [listeners in PingAccess](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Selecteer **Toevoegen**. De overzichts pagina voor de nieuwe toepassing wordt weer gegeven.

Wijs nu een gebruiker toe voor het testen van toepassingen en kies op headers gebaseerde eenmalige aanmelding:

1. Selecteer **gebruikers en groepen** in de zijbalk van de toepassing gebruikers en  >    >  **groepen toevoegen ( \<Number> geselecteerd)**. Er wordt een lijst met gebruikers en groepen weer gegeven waaruit u kunt kiezen.

   ![Hiermee wordt de lijst met gebruikers en groepen weer gegeven](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Selecteer een gebruiker voor het testen van de toepassing en selecteer **selecteren**. Zorg ervoor dat dit test account toegang heeft tot de on-premises toepassing.
1. Selecteer **Toewijzen**.
1. Selecteer in de zijbalk van de toepassing **eenmalige aanmelding op**  >  **basis** van de header.

   > [!TIP]
   > Als dit de eerste keer is dat u gebruikmaakt van eenmalige aanmelding op basis van een header, moet u PingAccess installeren. Als u er zeker van wilt zijn dat uw Azure-abonnement automatisch wordt gekoppeld aan uw PingAccess-installatie, gebruikt u de koppeling op deze pagina voor eenmalige aanmelding om PingAccess te downloaden. U kunt de site nu downloaden of later terugkeren naar deze pagina.

   ![Hiermee wordt het op de koptekst gebaseerde aanmeldings scherm en PingAccess weer gegeven](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Selecteer **Opslaan**.

Controleer vervolgens of de omleidings-URL is ingesteld op uw externe URL:

1. Selecteer in de Azure Active Directory-zijbalk van het **beheer centrum** **Azure Active Directory**  >  **app-registraties**. Er wordt een lijst met toepassingen weer gegeven.
1. Selecteer uw toepassing.
1. Selecteer de koppeling naast **omleidings-uri's**, met het aantal omleidings-uri's dat is ingesteld voor web-en open bare clients. De pagina **\<application name> -verificatie** wordt weer gegeven.
1. Controleer of de externe URL die u eerder hebt toegewezen aan uw toepassing, voor komt in de lijst met **omleidings-uri's** . Als dat niet het geval is, voegt u de externe URL nu toe met behulp van het omleidings-URI-type **Web** en selecteert u **Opslaan**.

Naast de externe URL moet een eind punt van Azure Active Directory op de externe URL worden toegevoegd aan de lijst met omleidings-Uri's.

`https://*.msappproxy.net/pa/oidc/cb`
`https://*.msappproxy.net/`

Stel tot slot uw on-premises toepassing in zodat gebruikers lees-en schrijf toegang hebben en andere toepassingen lees-/schrijftoegang hebben:

1. Selecteer op de **app-registraties** zijbalk voor uw toepassing **API-machtigingen**  >  **een machtiging toevoegen**  >  **micro soft api's**  >  **Microsoft Graph**. De pagina **API-machtigingen voor aanvragen** voor **Microsoft Graph** wordt weer gegeven, die de api's voor Windows Azure Active Directory bevat.

   ![Hiermee wordt de pagina API-machtigingen voor aanvragen weer gegeven](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Selecteer **gedelegeerde machtigingen**  >  **gebruiker**  >  **gebruiker. lezen**.
1. Selecteer toepassings **machtigingen**  >  **toepassings**  >  **toepassing. readwrite. all**.
1. Selecteer **Machtigingen toevoegen**.
1. Selecteer op de pagina **API-machtigingen** de optie **beheerder toestemming \<your directory name> geven voor**.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Gegevens verzamelen voor de PingAccess-stappen

U moet deze drie stukjes informatie (alle GUID'S) verzamelen om uw toepassing in te stellen met PingAccess:

| Naam van het Azure AD-veld | De naam van het veld PingAccess | Gegevensindeling |
| --- | --- | --- |
| **(Client-)id van de app** | **Client ID** | GUID |
| **(Tenant-)id van de map** | **Verlener** | GUID |
| `PingAccess key` | **Clientgeheim** | Wille keurige teken reeks |

Deze gegevens verzamelen:

1. Selecteer in de Azure Active Directory-zijbalk van het **beheer centrum** **Azure Active Directory**  >  **app-registraties**. Er wordt een lijst met toepassingen weer gegeven.
1. Selecteer uw toepassing. De **app-registraties** -pagina voor uw toepassing wordt weer gegeven.

   ![Registratie overzicht voor een toepassing](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Selecteer naast de waarde van de **toepassing (client) ID** het pictogram **kopiëren naar klem bord** en kopieer dit vervolgens en sla het op. U geeft deze waarde later op als de client-ID van PingAccess.
1. Klik vervolgens op de waarde van de **Directory-id (Tenant)** , selecteer **kopiëren naar klem bord**, kopieer deze vervolgens en sla deze op. U geeft deze waarde later op als verlener van PingAccess.
1. Selecteer in de zijbalk van de **app-registraties** voor uw toepassing **certificaten en geheimen**  >  **Nieuw client geheim**. De pagina **een geheim van client toevoegen** wordt weer gegeven.

   ![Toont de geheime pagina voor het toevoegen van een client](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. Typ in **Beschrijving** `PingAccess key` .
1. Kies onder **Expires** hoe u de PingAccess-sleutel instelt: **in één jaar**, **in twee jaar** of **nooit**.
1. Selecteer **Toevoegen**. De PingAccess-sleutel wordt weer gegeven in de tabel met client geheimen, met een wille keurige teken reeks die wordt ingevuld in het veld **waarde** .
1. Klik naast het veld **waarde** van de PingAccess-sleutel op het pictogram **kopiëren naar klem bord** en kopieer dit vervolgens en sla het op. U geeft deze waarde later op als het client geheim van PingAccess.

**Het `acceptMappedClaims` veld bijwerken:**

1. Meld u aan bij de [Azure Active Directory-Portal](https://aad.portal.azure.com/) als een toepassings beheerder.
1. Selecteer **Azure Active Directory** > **App-registraties**. Er wordt een lijst met toepassingen weer gegeven.
1. Selecteer uw toepassing.
1. Selecteer in de zijbalk van de pagina **app-registraties** voor uw toepassing **manifest**. De manifest-JSON-code voor de registratie van uw toepassing wordt weer gegeven.
1. Zoek het `acceptMappedClaims` veld en wijzig de waarde in `True` .
1. Selecteer **Opslaan**.

### <a name="use-of-optional-claims-optional"></a>Gebruik van optionele claims (optioneel)

Met optionele claims kunt u Standard-maar niet-inbegrepen standaard claims toevoegen die elke gebruiker en Tenant heeft. U kunt optionele claims voor uw toepassing configureren door het toepassings manifest te wijzigen. Zie het [artikel over het Azure AD-toepassings manifest](../develop/reference-app-manifest.md) voor meer informatie

Voor beeld om e-mail adres op te nemen in de access_token die PingAccess worden gebruikt:
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Beleid voor claim toewijzing gebruiken (optioneel)

[Claim toewijzings beleid (preview)](../develop/active-directory-claims-mapping.md#claims-mapping-policy-properties) voor kenmerken die niet bestaan in AzureAD. Met claim toewijzing kunt u oude on-premises apps naar de Cloud migreren door extra aangepaste claims toe te voegen die worden ondersteund door uw ADFS-of gebruikers objecten

Om ervoor te zorgen dat uw toepassing een aangepaste claim gebruikt en aanvullende velden bevat, moet u ook [een aangepast claim toewijzings beleid hebben gemaakt en aan de toepassing toegewezen](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Als u een aangepaste claim wilt gebruiken, moet u ook een aangepast beleid hebben gedefinieerd en toegewezen aan de toepassing. Dit beleid moet alle vereiste aangepaste kenmerken bevatten.
>
> U kunt beleids definities en-toewijzingen uitvoeren via Power shell of Microsoft Graph. Als u ze in Power shell gebruikt, moet u het mogelijk eerst gebruiken `New-AzureADPolicy` en vervolgens toewijzen aan de toepassing met `Add-AzureADServicePrincipalPolicy` . Zie [toewijzing van claim toewijzings beleid](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)voor meer informatie.

Voorbeeld:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>PingAccess inschakelen voor het gebruik van aangepaste claims

Het inschakelen van PingAccess voor het gebruik van aangepaste claims is optioneel, maar is wel vereist als u verwacht dat de toepassing extra claims verbruikt.

Wanneer u PingAccess in de volgende stap configureert, moet u voor de websessie die u maakt (instellingen->toegang->websessies) de selectie van het **aanvraag profiel** opheffen en **gebruikers kenmerken** die zijn ingesteld op **Nee** , vernieuwen.

## <a name="download-pingaccess-and-configure-your-application"></a>PingAccess downloaden en uw toepassing configureren

Nu u alle stappen voor de installatie van Azure Active Directory hebt voltooid, kunt u door gaan met het configureren van PingAccess.

De gedetailleerde stappen voor het deel PingAccess van dit scenario worden door lopen in de documentatie over ping-identiteit. Volg de instructies in [Configure PingAccess for Azure AD om toepassingen te beveiligen die zijn gepubliceerd met Microsoft Azure AD toepassings proxy](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) op de ping Identity-website en down load de [nieuwste versie van PingAccess](https://www.pingidentity.com/en/lp/azure-download.html?).

Deze stappen helpen u PingAccess te installeren en een PingAccess-account in te stellen (als u er nog geen hebt). Als u vervolgens een Azure AD OpenID Connect Connect (OIDC)-verbinding wilt maken, stelt u een token provider in met de ID-waarde van de **Directory (Tenant)** die u hebt gekopieerd uit de Azure AD-Portal. Vervolgens gebruikt u de **toepassings-id en-** waarden om een websessie te maken op PingAccess `PingAccess key` . Daarna kunt u identiteits toewijzing instellen en een virtuele host, site en toepassing maken.

### <a name="test-your-application"></a>Uw toepassing testen

Wanneer u al deze stappen hebt uitgevoerd, moet uw toepassing actief zijn. Als u dit wilt testen, opent u een browser en gaat u naar de externe URL die u hebt gemaakt tijdens het publiceren van de toepassing in Azure. Meld u aan met het test account dat u aan de toepassing hebt toegewezen.

## <a name="next-steps"></a>Volgende stappen

- [PingAccess voor Azure AD configureren voor het beveiligen van toepassingen die zijn gepubliceerd met Microsoft Azure AD toepassings proxy](https://docs.pingidentity.com/bundle/pingaccess-60/page/jep1564006742933.html)
- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](what-is-single-sign-on.md)
- [Problemen en foutberichten met Application Proxy oplossen](application-proxy-troubleshoot.md)