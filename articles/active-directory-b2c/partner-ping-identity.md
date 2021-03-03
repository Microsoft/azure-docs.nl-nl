---
title: Zelf studie voor het configureren van Azure Active Directory B2C met de ping-identiteit
titleSuffix: Azure AD B2C
description: Meer informatie over het integreren van Azure AD B2C verificatie met de ping-identiteit
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 94e7ae93d05ae8ee35028882e14d8da74814d833
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650223"
---
# <a name="tutorial-configure-ping-identity-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>Zelf studie: ping-identiteit configureren met Azure Active Directory B2C voor beveiligde hybride toegang

In deze voorbeeld zelfstudie vindt u informatie over het uitbreiden van Azure Active Directory (AD) B2C met  [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) en [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) voor het inschakelen van beveiligde hybride toegang.

Veel bestaande webeigenschappen, zoals sites van Commerce en webtoepassingen die beschikbaar zijn voor Internet, worden geïmplementeerd achter een proxy systeem, ook wel een reverse proxy-systeem genoemd. Deze proxy systemen bieden diverse functies, waaronder pre-verificatie, beleids afdwinging en verkeers routering. Voor beelden van use cases zijn het beschermen van de webtoepassing tegen inkomend webverkeer en het bieden van een uniform sessie beheer voor gedistribueerde server implementaties.

In de meeste gevallen bevat deze configuratie een laag voor verificatie omzetting waarmee de verificatie van de webtoepassing wordt waarop. Omgekeerde proxy's bieden de context van de geverifieerde gebruikers aan de webtoepassingen in een eenvoudigere vorm, zoals een header waarde in de vorm van wissen of samen vatting. In een dergelijke configuratie gebruiken de toepassingen geen standaard tokens van de industrie, zoals Security Assertion Markup Language (SAML), OAuth of Open ID Connect (OIDC), in plaats daarvan afhankelijk van de proxy om de verificatie context op te geven en de sessie met de agent voor eind gebruikers, zoals browser of de systeem eigen toepassing, te onderhouden. Als een service die wordt uitgevoerd in een ' man-in-the-middle ', kunnen proxy's het ultieme sessie beheer bieden. Dit betekent ook dat de proxy service zeer efficiënt en schaalbaar moet zijn, en dat deze geen bottlenecks of Single Point of Failure voor de toepassingen achter de proxy service. Het diagram is een weer gave van een typische omgekeerde proxy-implementatie en stroom van de communicatie.

![afbeelding toont de omgekeerde proxy-implementatie](./media/partner-ping/reverse-proxy.png)

Als u een situatie hebt waarin u het identiteits platform in dergelijke configuraties wilt moderniseren, worden de volgende problemen gegenereerd.

- Hoe kan de inspanning van de applicatie-modernisering worden losgekoppeld van de modernisering van het identiteits platform?

- Hoe kan een compatibiliteits omgeving tot stand worden gebracht met moderne en verouderde verificatie, waarbij gebruik wordt gemaakt van de moderne ID-service provider?

  a. Hoe kan ik de consistentie van de eind gebruikers?

  b. Hoe kunt u een eenmalige aanmelding bieden in de naast elkaar bestaande toepassingen?

We bespreken een aanpak om dergelijke problemen op te lossen door de integratie van Azure AD B2C met [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) -en [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) -technologieën.

## <a name="coexistence-environment"></a>Samenwerkings omgeving

Een technisch levensvat bare eenvoudige oplossing die ook rendabel is, is het configureren van het omgekeerde proxy systeem voor het gebruik van het moderne identiteits systeem, waarbij de verificatie wordt gedelegeerd.  
Proxy's in dit geval bieden ondersteuning voor moderne verificatie protocollen en gebruiken de omleidings verificatie op basis van (passief) waarmee gebruikers naar de nieuwe ID-provider (IdP) worden verzonden.

### <a name="azure-ad-b2c-as-an-identity-provider"></a>Azure AD B2C als een id-provider

Azure AD B2C beschikt over de mogelijkheid om **beleids regels** te definiëren waarmee verschillende gebruikers ervaringen en-gedrag worden gestimuleerd die ook **gebruikers ritten** worden genoemd, zoals deze worden georganisatied vanaf het einde van de server. Met elk van deze beleids regels wordt een protocol eindpunt beschikbaar gemaakt waarmee de verificatie kan worden uitgevoerd als een IdP. Er is geen speciale verwerking vereist aan de kant van de toepassing voor specifiek beleid. Toepassing maakt gewoon een industrie standaard verificatie aanvraag voor het protocol-specifieke verificatie-eind punt dat wordt weer gegeven door het beleid van belang.  
Azure AD B2C kunnen worden geconfigureerd om dezelfde verlener te delen voor meerdere beleids regels of voor elk beleid een unieke verlener. Elke toepassing kan verwijzen naar een of meer van deze beleids regels door een protocol systeem eigen verificatie aanvraag te maken en het gewenste gedrag van gebruikers te doen, zoals aanmelding, registratie en profiel bewerkingen. Het diagram toont OIDC-en SAML-toepassings werk stromen.

![afbeelding toont de OIDC-en SAML-implementatie](./media/partner-ping/azure-ad-identity-provider.png)

Hoewel het genoemde scenario goed werkt voor moderne toepassingen, kan het lastig zijn om de gebruiker op de juiste wijze om te leiden als de toegangs aanvraag voor de toepassingen de context voor gebruikers ervaring niet kan bevatten. In de meeste gevallen onderschept de proxy-laag of een geïntegreerde agent in de webtoepassing de toegangs aanvraag.

### <a name="pingaccess-as-a-reverse-proxy"></a>PingAccess als een omgekeerde proxy

Veel klanten hebben PingAccess geïmplementeerd als de omgekeerde proxy voor het afspelen van een of meer rollen zoals eerder in dit artikel vermeld. PingAccess kan een directe aanvraag onderscheppen via de man-in-the-Middle of als een omleiding die afkomstig is van een agent die wordt uitgevoerd op de webtoepassingsserver.

PingAccess kan worden geconfigureerd met OIDC, OAuth2 of SAML om verificatie uit te voeren op basis van een upstream-verificatie provider. Er kan één upstream-IdP worden geconfigureerd voor dit doel op de PingAccess-server. In het volgende diagram ziet u deze configuratie.

![afbeelding toont de PingAccess met OIDC-implementatie](./media/partner-ping/authorization-flow.png)

In een typische Azure AD B2C-implementatie waarbij meerdere beleids regels meerdere **id** beschikbaar maken, vormt dit een uitdaging. Omdat PingAccess kan alleen worden geconfigureerd met één upstream-IdP.  

### <a name="pingfederate-as-a-federation-proxy"></a>PingFederate als een Federatie proxy

PingFederate is een bedrijfs identiteits brug die volledig kan worden geconfigureerd als een verificatie provider of een proxy voor andere meerdere upstream-id, indien nodig. In het volgende diagram ziet u deze mogelijkheid.

![afbeelding toont de PingFederate-implementatie](./media/partner-ping/pingfederate.png)

Deze mogelijkheid kan worden gebruikt om contextuele/dynamische of declaratieve een inkomende aanvraag naar een specifiek Azure AD B2C beleid te scha kelen. Hier volgt een voor beeld van een protocol sequentie stroom voor deze configuratie.

![afbeelding toont de PingAccess-en PingFederate-werk stroom](./media/partner-ping/pingaccess-pingfederate-workflow.png)

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

- Een Azure-abonnement. Als u er nog geen hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.

- Een [Azure AD B2C-Tenant](./tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.

- PingAccess en PingFederate zijn geïmplementeerd in docker-containers of rechtstreeks op virtuele Azure-machines.

## <a name="connectivity"></a>Connectiviteit

Controleer of het volgende is verbonden.

- **PingAccess-server** : kan communiceren met de PingFederate-server, client browser, OIDC, OAuth-bekende en detectie sleutels die door de Azure AD B2C-service en PingFederate-server zijn gepubliceerd.

- **PingFederate-server** : kan communiceren met de PingAccess-server, client browser, OIDC, OAuth-bekende en detectie sleutels die door de Azure AD B2C-service worden gepubliceerd.

- **Verouderde of op headers gebaseerde verificatie toepassing** : kan communiceren met en van PingAccess server.

- **SAML-Relying Party toepassing** : kan het browser verkeer van de client bereiken. Toegang tot de SAML-federatieve meta gegevens die zijn gepubliceerd door de Azure AD B2C-service.

- **Moderne toepassing** : kan het browser verkeer van de client bereiken. Kan toegang krijgen tot de OIDC, OAuth-bekende en sleutels detectie die door de Azure AD B2C service is gepubliceerd.

- **Rest API** : kan het verkeer bereiken van een systeem eigen of webclient. Kan toegang krijgen tot de OIDC, OAuth-bekende en sleutels detectie die door de Azure AD B2C service is gepubliceerd.

## <a name="configure-azure-ad-b2c"></a>Azure AD B2C configureren

U kunt voor dit doel de basis gebruikers stromen of IEF-beleids regels (Advanced Identity Enter prise Framework) gebruiken. PingAccess genereert het eind punt van de meta gegevens op basis van de waarde van de **verlener** met behulp van de detectie Conventie op basis van [webfinger](https://tools.ietf.org/html/rfc7033) .
Als u dit Verdrag wilt volgen, moet u de update van de Azure AD B2C verlener bijwerken met behulp van de beleids eigenschappen in gebruikers stromen.

![afbeelding toont de token instellingen](./media/partner-ping/token-setting.png)

In het geavanceerde beleid kan dit worden geconfigureerd met behulp van de **IssuanceClaimPattern** -meta gegevenselement naar **AuthorityWithTfp** -waarde in het [JWT-token certificaat](./jwt-issuer-technical-profile.md)voor de uitgever van het product.

## <a name="configure-pingaccesspingfederate"></a>PingAccess/PingFederate configureren

In de volgende sectie wordt de vereiste configuratie beschreven.
Het diagram illustreert de algehele gebruikers stroom voor de integratie.

![afbeelding toont de PingAccess-en PingFederate-integratie](./media/partner-ping/pingaccess.png)

### <a name="configure-pingfederate-as-the-token-provider"></a>PingFederate configureren als de token provider

Als u PingFederate wilt configureren als de token provider voor PingAccess, zorgt u ervoor dat er verbinding wordt gemaakt van PingFederate naar PingAccess, gevolgd door de verbinding van PingAccess met PingFederate.  
Zie [dit artikel](https://docs.pingidentity.com/bundle/pingaccess-61/page/zgh1581446287067.html) voor configuratie stappen.

### <a name="configure-a-pingaccess-application-for-header-based-authentication"></a>Een PingAccess-toepassing configureren voor verificatie op basis van een header

Er moet een PingAccess-toepassing worden gemaakt voor de doel-webtoepassing voor verificatie op basis van een header. Volg deze stappen.

#### <a name="step-1--create-a-virtual-host"></a>Stap 1: een virtuele host maken

>[!IMPORTANT]
>Voor het configureren van deze oplossing moet er een virtuele host worden gemaakt voor elke toepassing. Zie [belang rijke overwegingen](https://docs.pingidentity.com/bundle/pingaccess-43/page/reference/pa_c_KeyConsiderations.html)voor meer informatie over de configuratie overwegingen en hun gevolgen.

Volg deze stappen om een virtuele host te maken:

1. Ga naar **instellingen**  >  **toegang tot**  >  **virtuele hosts**

2. **Virtuele host toevoegen** selecteren

3. Voer in het veld host het FQDN-gedeelte van de toepassings-URL in

4. Voer **443** in het veld poort in

5. Selecteer **Opslaan**.

#### <a name="step-2--create-a-web-session"></a>Stap 2: een websessie maken

Volg deze stappen om een websessie te maken:

1. Ga naar **instellingen**  >  **toegang tot**  >  **websessies**

2. **Websessie toevoegen** selecteren

3. Geef een **naam** op voor de websessie.

4. Selecteer het **type cookie**, ofwel **ondertekende JWT** of **versleutelde JWT**

5. Een unieke waarde voor de **doel groep** opgeven

6. Voer in het veld **client-id** de **Azure AD-toepassings-id** in

7. Voer in het veld **client geheim** de **sleutel** in die u hebt gegenereerd voor de toepassing in azure AD.

8. Optioneel: u kunt aangepaste claims maken en gebruiken met de Microsoft Graph-API. Als u ervoor kiest om dit te doen, selecteert u **Geavanceerd** en schakelt u de opties **profiel aanvragen** en **gebruikers kenmerken vernieuwen** . Zie [een aangepaste claim gebruiken](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-headers.md)voor meer informatie over het gebruik van aangepaste claims.

9. Selecteer **Opslaan**.

#### <a name="step-3--create-identity-mapping"></a>Stap 3: identiteits toewijzing maken

>[!NOTE]
>Identiteits toewijzing kan worden gebruikt met meer dan één toepassing als meer dan één toepassing dezelfde gegevens in de header verwacht.

Volg deze stappen om identiteits toewijzing te maken:

1. Ga naar **instellingen**  >  **toegangs**  >  **identiteits toewijzingen**

2. **Identiteits toewijzing toevoegen** selecteren

3. Een **naam** opgeven

4. Selecteer het type identiteits toewijzing **van header identiteits toewijzing**

5. Geef in de **kenmerk toewijzings** tabel de vereiste toewijzingen op. Bijvoorbeeld:

   Kenmerknaam | Headernaam |
   |-------|--------|
   |upn | x-userprinciplename |
   |e-mail   |    x-e-mail  |
   |nogmaals   | x-OID  |
   |verbindings   |     x-Scope |
   |AMR    |    x-AMR    |

6. Selecteer **Opslaan**.

#### <a name="step-4--create-a-site"></a>Stap 4: een site maken

>[!NOTE]
>In sommige configuraties is het mogelijk dat een site meer dan één toepassing kan bevatten. Een site kan worden gebruikt met meer dan één toepassing, waar nodig.

Volg deze stappen om een site te maken:

1. Ga naar **hoofd**  >  **sites**

2. Selecteer **site toevoegen**

3. Geef een **naam** op voor de site

4. Voer het **doel** van de site in. Het doel is de hostnaam: poort paar voor de server die als host fungeert voor de toepassing. Voer in dit veld niet het pad in voor de toepassing. Een toepassing met https://mysite:9999/AppName heeft bijvoorbeeld de doel waarde van mijn site: 9999

5. Geef aan of het doel beveiligde verbindingen verwacht.

6. Als voor het doel beveiligde verbindingen worden verwacht, stelt u de vertrouwde certificaat groep in op **elk vertrouwen**.

7. Selecteer **Opslaan**.

#### <a name="step-5--create-an-application"></a>Stap 5: een toepassing maken

Volg deze stappen voor het maken van een toepassing in PingAccess voor elke toepassing in azure die u wilt beveiligen.

1. Ga naar **hoofd**  >  **toepassingen**

2. Selecteer **toepassing toevoegen**

3. Geef een **naam** op voor de toepassing

4. Voer desgewenst een **Beschrijving** in voor de toepassing

5. Geef de **context basis** voor de toepassing op. Een toepassing op https://mysite:9999/AppName heeft bijvoorbeeld de context root/Appname. Het context hoofd moet beginnen met een slash (/), mag niet eindigen met een slash (/) en kan meer dan één laag diep zijn, bijvoorbeeld/Apps/MyApp.

6. Selecteer de **virtuele host** die u hebt gemaakt

   >[!NOTE]
   >De combi natie van virtuele host en context basis moet uniek zijn in PingAccess.

7. Selecteer de **websessie** die u hebt gemaakt

8. Selecteer de **site** die u hebt gemaakt en die de toepassing bevat

9. Selecteer de **identiteits toewijzing** die u hebt gemaakt

10. Selecteer **ingeschakeld** om de site in te scha kelen wanneer u opslaat

11. Selecteer **Opslaan**.

### <a name="configure-the-pingfederate-authentication-policy"></a>Het PingFederate-verificatie beleid configureren

Configureer het PingFederate-verificatie beleid om te communiceren met de meerdere id van de Azure AD B2C-tenants

1. Maak een contract voor het overbruggen van de kenmerken tussen de id en de SP. Zie [contracten voor Federatie hub en verificatie beleid](https://docs.pingidentity.com/bundle/pingfederate-101/page/ope1564002971971.html)voor meer informatie. Waarschijnlijk hebt u slechts één contract nodig, tenzij voor de SP een andere set kenmerken van elke IdP is vereist.

2. Voor elke IdP maakt u een IdP-verbinding tussen de IdP en PingFederate, de Federatie-hub als SP.

3. Voeg in het venster **doel sessie toewijzing** de toepasselijke verificatie beleids contracten toe aan de IDP-verbinding.

4. Configureer in het venster **selecters** een verificatie kiezer. Zie bijvoorbeeld een exemplaar van de **id eerste adapter** om elke IDP toe te wijzen aan de bijbehorende IDP-verbinding in een verificatie beleid.

5. Maak een SP-verbinding tussen PingFederate, de Federatie-hub als de IdP en de SP.

6. Voeg het bijbehorende verificatie beleids contract toe aan de SP-verbinding in het venster **verificatie bron toewijzing** .

7. Werk met elk IdP om verbinding te maken met PingFederate, de Federatie hub als SP.

8. Werk met de SP om verbinding te maken met PingFederate, de Federatie-hub als IdP.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie

- [Aangepast beleid in Azure AD B2C](./custom-policy-overview.md)

- [Aan de slag met aangepast beleid in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
