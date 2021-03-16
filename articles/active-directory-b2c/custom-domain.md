---
title: Aangepaste domeinen Azure AD B2C inschakelen
titleSuffix: Azure AD B2C
description: Meer informatie over het inschakelen van aangepaste domeinen in de omleidings-Url's voor Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 869bd7b02186873f490d324cec863c7f26ee8469
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555315"
---
# <a name="enable-custom-domains-for-azure-active-directory-b2c"></a>Aangepaste domeinen inschakelen voor Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

In dit artikel wordt beschreven hoe u aangepaste domeinen in de omleidings-Url's voor Azure Active Directory B2C (Azure AD B2C) inschakelt. Het gebruik van een aangepast domein met uw toepassing biedt een meer naadloze gebruikers ervaring. Vanuit het oogpunt van de gebruiker blijven ze in uw domein tijdens het aanmeldings proces in plaats van omleiden naar het Azure AD B2C standaard domein *<Tenant naam>. b2clogin.com*.

![Aangepaste domein gebruikers ervaring](./media/custom-domain/custom-domain-user-experience.png)

## <a name="custom-domain-overview"></a>Overzicht van aangepast domein

U kunt aangepaste domeinen voor Azure AD B2C inschakelen met behulp van [Azure front-deur](https://azure.microsoft.com/services/frontdoor/). Azure front deur is een wereld wijd schaalbaar ingangs punt dat gebruikmaakt van het micro soft Global Edge-netwerk om snelle, veilige en zeer schaal bare webtoepassingen te maken. U kunt Azure AD B2C inhoud achter de Azure front-deur weer geven en vervolgens een optie in azure front deur configureren om de inhoud te leveren via een aangepast domein in de URL van uw toepassing.

In het volgende diagram ziet u de integratie van Azure front-deur:

1. Vanuit een toepassing klikt een gebruiker op de knop aanmelden, waarmee ze naar de aanmeldings pagina van Azure AD B2C. Op deze pagina wordt een aangepaste domein naam opgegeven.
1. De webbrowser zet de aangepaste domein naam om naar het IP-adres van de Azure front-deur. Tijdens de DNS-omzetting verwijst een canonieke-naam record (CNAME) met een aangepaste domein naam naar de standaard front-endwebserver van de front-end (bijvoorbeeld `contoso.azurefd.net` ). 
1. Het verkeer dat is geadresseerd aan het aangepaste domein (bijvoorbeeld `login.contoso.com` ), wordt doorgestuurd naar de opgegeven standaard front-end-host ( `contoso.azurefd.net` ).
1. Azure front deur roept Azure AD B2C inhoud aan met behulp van het Azure AD B2C `<tenant-name>.b2clogin.com` standaard domein. De aanvraag voor het Azure AD B2C-eind punt bevat een aangepaste HTTP-header die de oorspronkelijke aangepaste domein naam bevat.
1. Azure AD B2C reageert op de aanvraag door de relevante inhoud en het oorspronkelijke aangepaste domein weer te geven.

![Diagram van aangepast domein netwerk](./media/custom-domain/custom-domain-network-flow.png)

> [!IMPORTANT]
> De verbinding van de browser naar de Azure front deur moet altijd gebruikmaken van IPv4 in plaats van IPv6.

Bij het gebruik van aangepaste domeinen moet u rekening houden met het volgende:

- U kunt meerdere aangepaste domeinen instellen. Zie [Azure AD-service limieten en-beperkingen](../active-directory/enterprise-users/directory-service-limits-restrictions.md) voor Azure AD B2C en [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits) voor Azure front-deur voor het maximum aantal ondersteunde aangepaste domeinen.
- Azure front deur is een afzonderlijke Azure-service, waardoor er extra kosten in rekening worden gebracht. Zie voor meer informatie [prijzen voor deur vooraan](https://azure.microsoft.com/pricing/details/frontdoor).
- Op dit moment wordt de functie [Firewall voor webtoepassingen](../web-application-firewall/afds/afds-overview.md) van Azure front deur niet ondersteund.
- Nadat u aangepaste domeinen hebt geconfigureerd, kunnen gebruikers nog steeds toegang krijgen tot de Azure AD B2C standaard domein naam *<Tenant naam>. b2clogin.com* (tenzij u een aangepast beleid gebruikt en u [toegang blokkeert](#block-access-to-the-default-domain-name)).
- Als u meerdere toepassingen hebt, migreert u deze allemaal naar het aangepaste domein, omdat de browser de Azure AD B2C-sessie opslaat onder de domein naam die momenteel wordt gebruikt.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="add-a-custom-domain-name-to-your-tenant"></a>Een aangepaste domein naam toevoegen aan uw Tenant

Volg de richt lijnen voor het [toevoegen en valideren van uw aangepaste domein in azure AD](../active-directory/fundamentals/add-custom-domain.md). Nadat het domein is geverifieerd, verwijdert u de DNS TXT-record die u hebt gemaakt.

> [!IMPORTANT]
> Voor deze stappen moet u zich aanmelden bij uw **Azure AD B2C** -Tenant en de **Azure Active Directory** -service selecteren.

Verifieer elk subdomein dat u wilt gebruiken. Verificatie van alleen het domein op het hoogste niveau is niet voldoende. Als u zich bijvoorbeeld wilt aanmelden met *login.contoso.com* en *account.contoso.com*, moet u zowel subdomeinen als niet alleen de domein *contoso.com* op het hoogste niveau controleren.  

## <a name="create-a-new-azure-front-door-instance"></a>Een nieuw exemplaar van de Azure-front-deur maken

Volg de stappen voor het [maken van een voor deur voor uw toepassing](../frontdoor/quickstart-create-front-door.md#create-a-front-door-for-your-application) met behulp van de standaard instellingen voor de frontend-host en routerings regels. 

> [!IMPORTANT]
> Als u deze stappen hebt uitgevoerd en u zich hebt aangemeld bij de Azure Portal in stap 1, selecteert u **Directory + abonnement** en kiest u de map met het Azure-abonnement dat u wilt gebruiken voor Azure front-deur. Dit mag *niet* de map zijn met de Azure AD B2C Tenant. 

In de stap **een back-end toevoegen**, gebruikt u de volgende instellingen:

* Selecteer voor **type back-end** de optie **Custom host**.  
* Voor de **back-end-hostnaam** selecteert u de hostnaam voor uw Azure AD B2C-eind punt <tenant naam>. b2clogin.com. Bijvoorbeeld contoso.b2clogin.com. 
* Voor de **back-end-host-header** selecteert u dezelfde waarde die u hebt geselecteerd voor de back-end- **hostnaam**.

![Een back-end toevoegen](./media/custom-domain/add-a-backend.png)

Nadat u de **back-end** aan de back- **End-groep** hebt toegevoegd, moet u de **status controles** uitschakelen.

![Een back-endpool toevoegen](./media/custom-domain/add-a-backend-pool.png)

## <a name="set-up-your-custom-domain-on-azure-front-door"></a>Uw aangepaste domein instellen op de front-deur van Azure

Volg de stappen om [een aangepast domein toe te voegen aan uw voor deur](../frontdoor/front-door-custom-domain.md). Gebruik bij het maken `CNAME` van de record voor uw aangepaste domein de aangepaste domein naam die u eerder hebt geverifieerd in de stap [een aangepaste domein naam toevoegen aan uw Azure AD](#add-a-custom-domain-name-to-your-tenant) . 

Nadat de aangepaste domein naam is geverifieerd, selecteert u **aangepaste domein naam https**. Selecteer vervolgens het beheer van de front- [deur](../frontdoor/front-door-custom-domain-https.md#option-1-default-use-a-certificate-managed-by-front-door)onder het **type certificaat beheer** of [gebruik mijn eigen certificaat](../frontdoor/front-door-custom-domain-https.md#option-2-use-your-own-certificate). 

De volgende scherm afbeelding laat zien hoe u een aangepast domein toevoegt en HTTPS inschakelt met behulp van een Azure front-deur certificaat.

![Aangepast domein voor Azure front deur instellen](./media/custom-domain/azure-front-door-add-custom-domain.png) 

## <a name="configure-cors"></a>CORS configureren

Als u [de Azure AD B2C gebruikers interface](customize-ui-with-html.md) met een HTML-sjabloon aanpast, moet u [CORS configureren](customize-ui-with-html.md?pivots=b2c-user-flow.md#3-configure-cors) met uw aangepaste domein.

Configureer Azure Blob-opslag voor cross-Origin-resource delen door de volgende stappen uit te voeren:

1. Ga in [Azure Portal](https://portal.azure.com) naar uw opslagaccount.
1. Selecteer in het menu de optie **CORS**.
1. Geef `https://your-domain-name` op bij **Toegestane oorsprongen**. Vervang door `your-domain-name` uw domein naam. Bijvoorbeeld `https://login.contoso.com`. Gebruik alleen kleine letters bij het invoeren van de naam van uw Tenant.
1. Voor **toegestane methoden** selecteert u beide `GET` en `OPTIONS` .
1. Voer bij **Toegestane headers** een asterisk (*) in.
1. Voer bij **Zichtbare headers** een asterisk (*) in.
1. Voer bij **Maximumleeftijd** 200 in.
1. Selecteer **Opslaan**.

## <a name="configure-your-identity-provider"></a>Uw ID-provider configureren

Wanneer een gebruiker zich aanmeldt met een sociale ID-provider, Azure AD B2C initieert een autorisatie aanvraag en neemt de gebruiker de geselecteerde ID-provider om het aanmeldings proces te volt ooien. Met de autorisatie aanvraag wordt de `redirect_uri` Azure AD B2C standaard domein naam opgegeven: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>/oauth2/authresp
```

Als u uw beleid zodanig hebt geconfigureerd dat aanmelding met een externe ID-provider is toegestaan, werkt u de OAuth omleidings-Uri's bij met het aangepaste domein. Met de meeste id-providers kunt u meerdere omleidings-Uri's registreren. Het is raadzaam omleidings-Uri's toe te voegen in plaats van ze te vervangen zodat u uw aangepaste beleid kunt testen zonder dat dit van invloed is op toepassingen die gebruikmaken van de Azure AD B2C standaard domeinnaam. 

In de volgende omleidings-URI:

```http
https://<custom-domain-name>/<tenant-name>/oauth2/authresp
``` 

- Vervang **<aangepaste domein naam>** door de aangepaste domein naam.
- Vervang **<Tenant naam>** door de naam van uw Tenant of uw Tenant-id.

In het volgende voor beeld wordt een geldige OAuth omleidings-URI weer gegeven:

```http
https://login.contoso.com/contoso.onmicrosoft.com/oauth2/authresp
```

Als u ervoor kiest om de [Tenant-id](#optional-use-tenant-id)te gebruiken, ziet een geldige OAuth omleidings-URI er als volgt uit:

```http
https://login.contoso.com/11111111-1111-1111-1111-111111111111/oauth2/authresp
```

De meta gegevens van de [SAML-identiteits providers](saml-identity-provider-technical-profile.md) zien er als volgt uit:

```http
https://<custom-domain-name>.b2clogin.com/<tenant-name>/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

## <a name="test-your-custom-domain"></a>Uw aangepaste domein testen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer onder **beleids regels** **gebruikers stromen (beleid)**.
1. Selecteer een gebruikers stroom en selecteer vervolgens **gebruikers stroom uitvoeren**.
1. Selecteer voor **Toepassing** de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Klik op **kopiëren naar klem bord**.

    ![De URI voor de verificatie aanvraag kopiëren](./media/custom-domain/user-flow-run-now.png)

1. Vervang in de eind punt-URL van **gebruikers stroom uitvoeren** het Azure AD B2C domein (<tenant naam>. b2clogin.com) door uw aangepaste domein.  
    Bijvoorbeeld in plaats van:

    ```http
    https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login
    ```

    gebruiken

    ```http
    https://login.contoso.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login    
    ```
1. Selecteer **Gebruikersstroom uitvoeren**. Het Azure AD B2C-beleid moet worden geladen.
1. Meld u aan met zowel lokale als sociale accounts.
1. Herhaal de test met de rest van uw beleid.

## <a name="configure-your-application"></a>Uw toepassing configureren 

Nadat u het aangepaste domein hebt geconfigureerd en getest, kunt u uw toepassingen bijwerken om de URL te laden waarmee uw aangepaste domein wordt opgegeven als de hostnaam in plaats van het Azure AD B2C domein. 

De aangepaste domein integratie is van toepassing op verificatie-eind punten die gebruikmaken van Azure AD B2C beleid (gebruikers stromen of aangepast beleid) om gebruikers te verifiëren. Deze eind punten kunnen er als volgt uitzien:

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/v2.0/.well-known/openid-configuration</code>

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://\<custom-domain\>/<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

Vervang:
- **aangepast-domein** met uw aangepaste domein
- **Tenant naam** met uw Tenant naam of Tenant-id
- **beleids naam** met de naam van uw beleid. Meer [informatie over Azure AD B2C-beleid](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 


De meta gegevens van de [SAML-service provider](connect-with-saml-service-providers.md) kunnen er als volgt uitzien: 

```html
https://custom-domain-name/tenant-name/policy-name/Samlp/metadata
```

### <a name="optional-use-tenant-id"></a>Beschrijving Tenant-ID gebruiken

U kunt de naam van uw B2C-Tenant in de URL vervangen door de GUID van uw Tenant-ID, zodat alle verwijzingen naar ' B2C ' in de URL worden verwijderd. U kunt de GUID van uw Tenant-ID vinden op de overzichts pagina van B2C in Azure Portal.
Wijzig bijvoorbeeld `https://account.contosobank.co.uk/contosobank.onmicrosoft.com/` in `https://account.contosobank.co.uk/<tenant ID GUID>/`

Als u de Tenant-ID in plaats van de Tenant naam wilt gebruiken, moet u ervoor zorgen dat u de ID-provider **OAuth omleidings-uri's** dienovereenkomstig bijwerkt. Zie [uw ID-provider configureren](#configure-your-identity-provider)voor meer informatie.

### <a name="token-issuance"></a>Token uitgifte

De claim naam van de token Uitgever (ISS) wordt gewijzigd op basis van het aangepaste domein dat wordt gebruikt. Bijvoorbeeld:

```http
https://<domain-name>/11111111-1111-1111-1111-111111111111/v2.0/
```

::: zone pivot="b2c-custom-policy"

## <a name="block-access-to-the-default-domain-name"></a>Toegang tot de naam van het standaard domein blok keren

Nadat u het aangepaste domein hebt toegevoegd en uw toepassing hebt geconfigureerd, hebben gebruikers nog steeds toegang tot de <Tenant naam>. b2clogin.com domein. Om toegang te voor komen, kunt u het beleid configureren om de autorisatie aanvraag "hostnaam" te controleren op een lijst met toegestane domeinen. De hostnaam is de domein naam die wordt weer gegeven in de URL. De hostnaam is beschikbaar via `{Context:HostName}` [claim resolvers](claim-resolver-overview.md). Vervolgens kunt u een aangepast fout bericht weer geven. 

1. Bekijk het voor beeld van een beleid voor voorwaardelijke toegang dat de hostnaam controleert van [github](https://github.com/azure-ad-b2c/samples/blob/master/policies/check-host-name).
1. Vervang in elk bestand de teken reeks door `yourtenant` de naam van uw Azure AD B2C-Tenant. Als de naam van uw B2C-Tenant bijvoorbeeld *contosob2c* is, worden alle exemplaren van `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com` .
1. Upload de beleids bestanden in de volgende volg orde: `B2C_1A_TrustFrameworkExtensions_HostName.xml` en vervolgens `B2C_1A_signup_signin_HostName.xml` .

::: zone-end

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="azure-ad-b2c-returns-a-page-not-found-error"></a>Fout bij het retour neren van een niet-gevonden pagina Azure AD B2C

- **Symptoom** : nadat u een aangepast domein hebt geconfigureerd en u zich probeert aan te melden met het aangepaste domein, wordt een HTTP 404-fout bericht weer gegeven.
- **Mogelijke oorzaken** : dit probleem kan betrekking hebben op de configuratie van de back-end van de Azure-front 
- **Oplossing**:  
    1. Controleer of het aangepaste domein is [geregistreerd en of](#add-a-custom-domain-name-to-your-tenant) het is geverifieerd in uw Azure AD B2C Tenant.
    1. Controleer of het [aangepaste domein](../frontdoor/front-door-custom-domain.md) juist is geconfigureerd. De `CNAME` record voor uw aangepaste domein moet verwijzen naar de standaard frontend-host van de Azure front-deur (bijvoorbeeld contoso.azurefd.net).
    1. Zorg ervoor dat de configuratie van de back-end van de [Azure front-deur](#set-up-your-custom-domain-on-azure-front-door) naar de Tenant verwijst waar u de aangepaste domein naam instelt en waar uw gebruikers stroom of aangepast beleid is opgeslagen.

### <a name="identify-provider-returns-an-error"></a>Provider identificeren retourneert een fout

- **Symptoom** : nadat u een aangepast domein hebt geconfigureerd, kunt u zich aanmelden met lokale accounts. Maar wanneer u zich aanmeldt met referenties van externe [Social-of ENTER prise-id-providers](add-identity-provider.md), wordt er een fout bericht weer gegeven in de id-providers.
- **Mogelijke oorzaken** : wanneer Azure AD B2C de gebruiker zich aanmeldt met een federatieve id-provider, wordt de omleidings-URI opgegeven. De omleidings-URI is het eind punt waar de ID-provider het token retourneert. De omleidings-URI is hetzelfde domein dat door de toepassing wordt gebruikt met de autorisatie aanvraag. Als de omleidings-URI nog niet is geregistreerd bij de ID-provider, wordt de nieuwe omleidings-URI mogelijk niet vertrouwd, wat resulteert in een fout bericht. 
- **Oplossing** : Volg de stappen in [uw ID-provider configureren](#configure-your-identity-provider) om de nieuwe omleidings-URI toe te voegen. 


## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="can-i-use-azure-front-door-advanced-configuration-such-as-web-application-firewall-rules"></a>Kan ik geavanceerde configuratie voor de voor deur van Azure gebruiken, zoals *firewall regels voor webtoepassingen*? 
  
Hoewel de geavanceerde configuratie-instellingen van Azure front-deur niet officieel worden ondersteund, kunt u deze gebruiken voor uw eigen risico. 

### <a name="when-i-use-run-now-to-try-to-run-my-policy-why-i-cant-see-the-custom-domain"></a>Wanneer ik voer nu uit gebruik om mijn beleid uit te voeren, waarom kan ik het aangepaste domein niet zien?

Kopieer de URL, wijzig de domein naam hand matig en plak deze opnieuw in uw browser.

### <a name="which-ip-address-is-presented-to-azure-ad-b2c-the-users-ip-address-or-the-azure-front-door-ip-address"></a>Welk IP-adres wordt weer gegeven voor Azure AD B2C? Het IP-adres van de gebruiker of het IP-adres van de Azure front-deur?

Het oorspronkelijke IP-adres van de gebruiker wordt door Azure front deur door gegeven. Dit is het IP-adres dat u ziet in de audit Reporting of het aangepaste beleid.

### <a name="can-i-use-a-third-party-wab-application-firewall-waf-with-b2c"></a>Kan ik een wab Application firewall (WAF) van derden gebruiken met B2C?

Azure AD B2C ondersteunt momenteel een aangepast domein door alleen Azure front-deur te gebruiken. Voeg nog geen WAF toe vóór de Azure front-deur.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over [OAuth-autorisatie aanvragen](protocols-overview.md).

