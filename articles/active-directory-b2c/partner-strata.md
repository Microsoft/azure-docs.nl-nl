---
title: Zelf studie voor het configureren van Azure Active Directory B2C met Strata
titleSuffix: Azure AD B2C
description: Meer informatie over het integreren van Azure AD B2C verificatie met whoIam voor gebruikers verificatie
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 49def79c467aa8f2b3fda9688dc77fcb003a34a5
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256562"
---
# <a name="tutorial-for-extending-azure-ad-b2c-to-protect-on-premises-applications-using-strata"></a>Zelf studie voor het uitbreiden van Azure AD B2C om on-premises toepassingen te beveiligen met behulp van Strata

In deze voorbeeld zelfstudie vindt u informatie over het integreren van Azure Active Directory (AD) B2C met de [Maverics Identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/)van Strata.
Maverics Identity Orchestrator breidt Azure AD B2C uit om on-premises toepassingen te beveiligen. Hiermee wordt verbinding gemaakt met elk identiteits systeem, worden gebruikers en referenties transparant gemigreerd, worden beleids regels en configuraties gesynchroniseerd en worden de verificatie en sessie beheer van de samen vatting geauthenticeerd. Het gebruik van Strata-ondernemingen kan snel overstappen van verouderde naar Azure AD B2C zonder toepassingen opnieuw te schrijven. Deze oplossing biedt de volgende voordelen:

- **Klant Single Sign-On (SSO) naar on-premises hybride apps**: Azure AD B2C ondersteunt Customer SSO met Maverics Identity Orchestrator. Gebruikers melden zich aan met hun accounts die worden gehost in Azure AD B2C of sociale ID-provider (IdP). Maverics breidt SSO uit naar apps die historisch zijn beveiligd door oudere identiteits systemen, zoals Symantec SiteMinder.

- Op **standaarden gebaseerde SSO naar apps uitbreiden zonder ze opnieuw te schrijven**: gebruik Azure AD B2C om gebruikers toegang te beheren en eenmalige aanmelding in te scha kelen met Maverics Identity Orchestrator SAML-of OIDC-connectors.

- **Eenvoudige configuratie**: Azure AD B2C biedt een eenvoudige stapsgewijze gebruikers interface voor het verbinden van Maverics Identity Orchestrator SAML-of OIDC-connectors naar Azure AD B2C.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

- Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

- Een [Azure AD B2C-Tenant](./tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.

- Een instantie van [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) voor het opslaan van geheimen die worden gebruikt door de Maverics Identity Orchestrator. Het wordt gebruikt om verbinding te maken met Azure AD B2C of andere kenmerk providers, zoals een LDAP-Directory of-data base (Lightweight Directory Access Protocol).

- Een exemplaar van de [Maverics Identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/) die is geïnstalleerd en wordt uitgevoerd op een virtuele Azure-machine of de on-premises server van uw keuze. Neem contact op met [Strata](https://www.strata.io/contact/) voor informatie over het verkrijgen van de software en toegang tot de installatie-en configuratie documentatie.

- Een on-premises toepassing die u overstapt van een verouderd identiteits systeem naar Azure AD B2C.

## <a name="scenario-description"></a>Scenariobeschrijving

De Maverics-integratie van Strata omvat de volgende onderdelen:

- **Azure AD B2C**: de autorisatie server die verantwoordelijk is voor het controleren van de referenties van de gebruiker. Geverifieerde gebruikers hebben toegang tot on-premises apps met behulp van een lokaal account dat is opgeslagen in de Azure AD B2C Directory.

- **Een externe Social of ENTER prise IDP**: kan een OpenID Connect Connect provider, Facebook, Google of github zijn. Zie voor meer informatie over het gebruik van [externe ID](./technical-overview.md#external-identity-providers) met Azure AD B2C.  

- **Strata Maverics Identity Orchestrator**: de service die gebruikers aanmeldt en op transparante wijze identiteiten doorgeeft aan apps via HTTP-headers.

In het volgende architectuur diagram wordt de implementatie weer gegeven.

![Afbeelding toont de architectuur van een Azure AD B2C-integratie met Strata Maverics om toegang tot hybride apps mogelijk te maken](./media/partner-strata/strata-architecture-diagram.png)

| Stappen | Description |
|:-------|:---------------|
| 1. | De gebruiker maakt een aanvraag voor toegang tot de on-premises gehoste toepassing. De Maverics-identiteits Orchestrator proxy's de aanvraag die de gebruiker heeft gemaakt voor de toepassing.|
| 2. | De Orchestrator controleert de verificatie status van de gebruiker. Als er geen sessie token wordt ontvangen of het opgegeven sessie token ongeldig is, wordt de gebruiker naar Azure AD B2C voor verificatie verzonden.|
| 3. | Azure AD B2C verzendt de verificatie aanvraag naar de geconfigureerde Social IdP.|
| 4. | De IdP vraagt de gebruiker om referenties. Afhankelijk van de IdP moet de gebruiker mogelijk multi-factor Authentication (MFA) uitvoeren.|
| 5. | De IdP stuurt de verificatie reactie terug naar Azure AD B2C. De gebruiker kan tijdens deze stap eventueel een lokaal account maken in de map Azure AD B2C.|
| 6. | Azure AD B2C verzendt de gebruikers aanvraag naar het eind punt dat is opgegeven tijdens de registratie van de Orchestrator-app in de Azure AD B2C-Tenant.|
| 7. | De Orchestrator evalueert het toegangs beleid en berekent kenmerk waarden die moeten worden opgenomen in HTTP-headers die worden doorgestuurd naar de app. Tijdens deze stap kan de Orchestrator extra kenmerk providers aanroepen om de gegevens op te halen die nodig zijn om de header waarden correct in te stellen. De Orchestrator stelt de header waarden in en verzendt de aanvraag naar de app.|
| 8. | De gebruiker is nu geverifieerd en heeft toegang tot de app.|

## <a name="get-maverics-identity-orchestrator"></a>Maverics Identity Orchestrator ophalen
Neem contact op met [Strata](https://www.strata.io/contact/)om de software te gebruiken die u gebruikt om uw oude on-premises app te integreren met Azure AD B2C. Nadat u de software hebt verkregen, volgt u de onderstaande stappen om de specifieke vereisten voor Orchestrator te bepalen en de vereiste installatie-en configuratie stappen uit te voeren.

## <a name="configure-your-azure-ad-b2c-tenant"></a>Uw Azure AD B2C-Tenant configureren

1. **Uw toepassing registreren**

   a. [Registreer de Orchestrator als een toepassing](./tutorial-register-applications.md?tabs=app-reg-ga) in azure AD B2C Tenant.
   >[!Note]
   >U hebt de Tenant naam en-id, client-ID, client geheim, geconfigureerde claims en omleidings-URI later nodig wanneer u uw Orchestrator-exemplaar configureert.

   b. Verleen micro soft MS Graph API machtigingen voor uw toepassingen. Uw toepassing heeft de volgende machtigingen nodig: `offline_access` , `openid` .

   c. Voeg een omleidings-URI voor uw toepassing toe. Deze URI komt overeen met de `oauthRedirectURL` para meter van de Azure AD B2C connector configuratie van uw orchestrator, bijvoorbeeld `https://example.com/oidc-endpoint` .

2. **Een gebruikers stroom maken**: Maak een [aanmeldings-en aanmeldings gebruikers stroom](./tutorial-create-user-flows.md).

3. **Een IDP toevoegen**: Kies voor het aanmelden bij uw gebruiker met een lokaal account of een sociaal-of ENTER prise- [IDP](./add-identity-provider.md).

4. **Gebruikers kenmerken definiëren**: Definieer de kenmerken die moeten worden verzameld tijdens het aanmelden.

5. **Toepassings claims opgeven**: Geef de kenmerken op die moeten worden geretourneerd naar de toepassing via uw Orchestrator-exemplaar. De Orchestrator gebruikt kenmerken van claims die worden geretourneerd door Azure AD B2C en kan extra kenmerken ophalen van andere verbonden identiteits systemen, zoals LDAP-directory's en data bases. Deze kenmerken worden ingesteld in HTTP-headers en verzonden naar de upstream-toepassing on-premises.

## <a name="configure-maverics-identity-orchestrator"></a>Maverics Identity Orchestrator configureren

In de volgende secties wordt u stapsgewijs begeleid bij de stappen die nodig zijn om uw Orchestrator-exemplaar te configureren. Neem contact op met [Strata](https://www.strata.io/contact/)voor aanvullende ondersteuning en documentatie.

### <a name="maverics-identity-orchestrator-server-requirements"></a>Maverics identiteit Orchestrator-Server vereisten

U kunt uw Orchestrator-exemplaar op elke server uitvoeren, hetzij on-premises, hetzij in een open bare Cloud infrastructuur per provider als Azure, AWS of GCP.

- Besturings systeem: REHL 7,7 of hoger, CentOS 7 +

- Schijf: 10 GB (klein)

- Geheugen: 16 GB

- Poorten: 22 (SSH/SCP), 443, 80

- Toegang tot de hoofdmap voor installatie/beheer taken

- Maverics Identity Orchestrator wordt uitgevoerd als gebruiker `maverics` onder `systemd`

- Netwerk uitgaand van de server die als host fungeert voor Maverics Identity Orchestrator met de mogelijkheid om uw Azure AD-Tenant te bereiken.

### <a name="install-maverics-identity-orchestrator"></a>Maverics Identity Orchestrator installeren

1. Haal het nieuwste Maverics RPM-pakket op. Plaats het pakket op het systeem waarop u Maverics wilt installeren. Als u het bestand naar een externe host kopieert, is [SCP](https://www.ssh.com/ssh/scp/) een handig hulp programma.

2. Als u het Maverics-pakket wilt installeren, voert u de volgende opdracht uit, waarbij u de bestands naam vervangt in plaats van `maverics.rpm` .

   `sudo rpm -Uvf maverics.rpm`

   Maverics wordt standaard geïnstalleerd in de map `/usr/local/bin`.

3. Nadat u Maverics hebt geïnstalleerd, wordt dit als een service uitgevoerd onder `systemd`.  Als u wilt controleren of de Maverics-service wordt uitgevoerd, voert u de volgende opdracht uit:

   `sudo service maverics status`

  Als de Orchestrator-installatie is geslaagd, ziet u een bericht dat lijkt op het volgende:

```
Redirecting to /bin/systemctl status maverics.service
  maverics.service - Maverics
  Loaded: loaded (/etc/systemd/system/maverics.service; enabled; vendor preset: disabled)
  Active: active (running) since Thu 2020-08-13 16:48:01 UTC; 24h ago
  Main PID: 330772 (maverics)
  Tasks: 5 (limit: 11389)
  Memory: 14.0M
  CGroup: /system.slice/maverics.service
          └─330772 /usr/local/bin/maverics --config /etc/maverics/maverics.yaml
  ```

4. Als de Maverics-service niet kan worden gestart, voert u de volgende opdracht uit om het probleem te onderzoeken:

   `journalctl --unit=maverics.service --reverse`

   De meest recente logboek vermelding wordt aan het begin van de uitvoer weer gegeven.

Nadat u Maverics hebt geïnstalleerd, wordt het standaardbestand `maverics.yaml` gemaakt in de map `/etc/maverics`.

Configureer uw Orchestrator om de toepassing te beveiligen. Integreer met Azure AD B2C, sla en haal geheimen op uit [Azure Key Vault](https://azure.microsoft.com/services/key-vault/?OCID=AID2100131_SEM_bf7bdd52c7b91367064882c1ce4d83a9:G:s&ef_id=bf7bdd52c7b91367064882c1ce4d83a9:G:s&msclkid=bf7bdd52c7b91367064882c1ce4d83a9). Definieer de locatie van waaruit de Orchestrator de configuratie moet lezen.

### <a name="supply-configuration-using-environment-variables"></a>Configuratie opgeven met behulp van omgevings variabelen

Geef configuratie op voor uw Orchestrator-instanties via omgevings variabelen.

`MAVERICS_CONFIG`

Deze omgevings variabele vertelt het Orchestrator-exemplaar welk YAML te gebruiken configuratie bestanden en waar ze tijdens het opstarten worden gevonden of opnieuw worden gestart. Stel de omgevings variabele in in `/etc/maverics/maverics.env` .

### <a name="create-the-orchestrators-tls-configuration"></a>De TLS-configuratie van de Orchestrator maken

`tls`In het veld in uw `maverics.yaml` declareert u de beveiligings configuraties voor Trans Port-laag beveiliging die uw Orchestrator-exemplaar gaat gebruiken. Connectors kunnen TLS-objecten en de Orchestrator-server gebruiken.

De sleutel `maverics` is gereserveerd voor de Orchestrator-server. Alle andere sleutels zijn beschikbaar en kunnen worden gebruikt om een TLS-object in te voeren in een opgegeven connector.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```

### <a name="configure-the-azure-ad-b2c-connector"></a>De Azure AD B2C-connector configureren

Orchestrator gebruikt connectors om te integreren met verificatie-en kenmerk providers. In dit geval gebruikt de Orchestrator app gateway de Azure AD B2C-connector als verificatie-en kenmerk provider. Azure AD B2C gebruikt de sociale IdP voor verificatie en fungeert als een kenmerk provider voor de orchestrator, waarbij kenmerken worden door gegeven in claims die zijn ingesteld in HTTP-headers.  

De configuratie van deze connector komt overeen met de app die is geregistreerd in de Azure AD B2C Tenant.

1. Kopieer de client-ID, het geheim en de omleidings-URI vanuit uw app-configuratie in uw Tenant.

2. Geef uw connector een naam, die hier wordt weer gegeven `azureADB2C` en stel de connector `type` in op `azure` . Noteer de naam van de connector, omdat deze waarde wordt gebruikt in andere configuratie parameters hieronder.

3. Voor deze integratie moet de `authType` worden ingesteld op `oidc` .

4. Stel de client-ID die u in stap 1 hebt gekopieerd in als de waarde voor de `oauthClientID` para meter.

5. Stel het client geheim dat u in stap 1 hebt gekopieerd in als de waarde voor de `oauthClientSecret` para meter.

6. Stel de omleidings-URI die u in stap 1 hebt gekopieerd in als de waarde voor de `oauthRedirectURL` para meter.

7. De Azure AD B2C OIDC-connector gebruikt het bekende OIDC-eind punt voor het detecteren van meta gegevens, met inbegrip van Url's en ondertekeningssleutel. Stel de waarde van `oidcWellKnownURL` in op het eind punt van uw Tenant.

```yaml
connectors:
  name: azureADB2C
  type: azure
  oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
  oauthRedirectURL: https://example.com/oidc-endpoint
  oauthClientID: <azureADB2CClientID>
  oauthClientSecret: <azureADB2CClientSecret>
  authType: oidc
```

### <a name="define-azure-ad-b2c-as-your-authentication-provider"></a>Azure AD B2C definiëren als uw verificatie provider

Een verificatie provider bepaalt hoe verificatie moet worden uitgevoerd voor een gebruiker die geen geldige sessie heeft gepresenteerd als onderdeel van de app-resource aanvraag. Configuratie in uw Azure AD B2C-Tenant bepaalt hoe een gebruiker moet worden gechallenget voor referenties en dat er extra authenticatie beleidsregels worden toegepast. Als u bijvoorbeeld een tweede factor wilt vereisen om het verificatie proces te volt ooien en te bepalen welke claims moeten worden geretourneerd naar de Orchestrator-app-gateway nadat de verificatie is geslaagd.

De waarde voor de `authProvider` moet overeenkomen met de waarde van uw connector `name` .

```yaml
authProvider: azureADB2C
```

### <a name="protect-your-on-premises-app-with-an-orchestrator-app-gateway"></a>Uw on-premises app beveiligen met een Orchestrator-app-gateway

De configuratie van de app-gateway van Orchestrator declareert hoe Azure AD B2C uw toepassing moet beveiligen en hoe gebruikers toegang moeten hebben tot de app.

1. Maak een naam voor uw app-gateway. U kunt een beschrijvende naam of een volledig gekwalificeerde hostnaam gebruiken als id voor uw app.

2. Stel de `location` . In het volgende voor beeld wordt de hoofdmap van de app gebruikt `/` . Dit kan echter een URL-pad van uw toepassing zijn.

3. Definieer de beveiligde toepassing in `upstream` met behulp van de-host: poort Conventie: `https://example.com:8080` .

4. Stel de waarden in voor fout en onbevoegde pagina's.

5. Definieer de namen van de HTTP-headers en kenmerk waarden die moeten worden opgegeven aan de toepassing om verificatie en toegang tot de app te beheren. De namen van de headers zijn wille keurig en komen doorgaans overeen met de configuratie van de app. Kenmerk waarden worden in een naam ruimte voorzien door de connector waarmee ze worden geleverd. In het onderstaande voor beeld worden de waarden die worden geretourneerd door Azure AD B2C voorafgegaan door de naam `azureADB2C` van de connector waarbij het achtervoegsel de naam is van het kenmerk dat de vereiste waarde bevat, bijvoorbeeld `given_name` .

6. Stel in dat het beleid moet worden geëvalueerd en afgedwongen. Er worden drie acties gedefinieerd: `allowUnauthenticated` , `allowAnyAuthenticated` en `allowIfAny` . Elke actie is gekoppeld aan een `resource` en het beleid wordt hiervoor geëvalueerd `resource` .

>[!NOTE]
>`headers`En `policies` gebruiken Java script-of GoLang-service-extensies om een wille keurige logica te implementeren waarmee de standaard mogelijkheden aanzienlijk worden uitgebreid.

```yaml
appgateways:
  - name: Sonar
    location: /
    upstream: https://example.com:8080
    errorPage: https://example.com:8080/sonar/error
    unauthorizedPage: https://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

### <a name="use-azure-key-vault-as-your-secrets-provider"></a>Azure Key Vault gebruiken als uw provider van geheimenbeheer

Het is belang rijk om de geheimen te beveiligen die uw Orchestrator gebruikt om verbinding te maken met Azure AD B2C en een ander identiteits systeem. Maverics wordt standaard geheimen in tekst zonder opmaak geladen `maverics.yaml` , maar in deze zelf studie gebruikt u Azure Key Vault als de provider geheimen.

Volg de instructies voor het [maken van een nieuwe Key Vault](../key-vault/secrets/quick-create-portal.md) die door uw Orchestrator-exemplaar wordt gebruikt als een provider van geheimen. Voeg uw geheimen toe aan uw kluis en noteer het `SECRET NAME` gegeven aan elk geheim. Bijvoorbeeld `AzureADB2CClientSecret`.

Om een waarde als een geheim te declareren in een `maverics.yaml`-configuratiebestand, zet u haakjes rondom het geheim:

```yaml
connectors:
  - name: AzureADB2C
    type: azure
    oauthClientID: <AzureADB2CClientID>
    oauthClientSecret: <AzureADB2CClientSecret>
```

De waarde die is opgegeven in de punt haken moet overeenkomen met de `SECRET NAME` gegeven aan het geheim in uw Azure Key Vault.

Als u geheimen van Azure Key Vault wilt laden, stelt u de omgevings variabele `MAVERICS_SECRET_PROVIDER` in het bestand in met de referenties die zijn `/etc/maverics/maverics.env` gevonden in de azure-credentials.jsvoor het bestand, met behulp van het volgende patroon:

`MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

### <a name="put-everything-together"></a>Alles samen plaatsen

Hier ziet u hoe de Orchestrator-configuratie wordt weer gegeven wanneer u de hierboven beschreven configuraties voltooit.

```yaml
version: 0.4.2
listenAddress: ":443"
tls:
  maverics:
    certFile: certs/maverics.crt
    keyFile: certs/maverics.key

authProvider: azureADB2C

connectors:
  - name: azureADB2C
    type: azure
    oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
    oauthRedirectURL: https://example.com/oidc-endpoint
    oauthClientID: <azureADB2CClientID>
    oauthClientSecret: <azureADB2CClientSecret>
    authType: oidc

appgateways:
  - name: Sonar
    location: /
    upstream: http://example.com:8080
    errorPage: http://example.com:8080/sonar/accessdenied
    unauthorizedPage: http://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

## <a name="test-the-flow"></a>De stroom testen

1. Ga naar de on-premises toepassings-URL, `https://example.com/sonar/dashboard` .

2. De Orchestrator moet omleiden naar de pagina die u in uw gebruikers stroom hebt geconfigureerd.

3. Selecteer de IdP in de lijst op de pagina.

4. Zodra u bent omgeleid naar de IdP, geeft u uw referenties op als aangevraagd, met inbegrip van een MFA-token, indien vereist door die IdP.

5. Nadat de verificatie is voltooid, moet u worden omgeleid naar Azure AD B2C, waarmee de app-aanvraag wordt doorgestuurd naar de Orchestrator-omleidings-URI.

6. De Orchestrator evalueert beleids regels, berekent kopteksten en stuurt de gebruiker naar de upstream-toepassing.  

7. De aangevraagde toepassing wordt weer geven.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Aangepast beleid in Azure AD B2C](./custom-policy-overview.md)

- [Aan de slag met aangepast beleid in Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
