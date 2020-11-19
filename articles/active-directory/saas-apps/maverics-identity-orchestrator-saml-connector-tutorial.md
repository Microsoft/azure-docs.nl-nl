---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Maverics Identity Orchestrator SAML Connector | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Maverics Identity Orchestrator SAML Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.openlocfilehash: f4fe368e9a56e5ac4c9dfa2648ce4af15a1b5ac0
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684420"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Zelfstudie: Eenmalige aanmelding van Microsoft Azure AD integreren met Maverics Identity Orchestrator SAML Connector

Strata biedt een eenvoudige manier om on-premises toepassingen te integreren met Azure Active Directory (Azure AD) voor verificatie en toegangsbeheer.

In dit artikel wordt uitgelegd hoe u Maverics Identity Orchestrator configureert om:
* Gebruikers incrementeel te migreren van een on-premises identiteitssysteem naar Azure AD tijdens aanmelding bij een verouderde on-premises toepassing.
* Aanmeldingsaanvragen van een verouderd product voor webtoegangsbeheer, zoals CA SiteMinder of Oracle Access Manager, naar Azure AD te routeren.
* Gebruikers te verifiëren bij on-premises toepassingen die worden beveiligd door middel van HTTP-headers of bedrijfseigen sessiecookies, nadat de gebruiker is geverifieerd bij Azure AD.

Strata biedt software die u on-premises of in de cloud kunt implementeren. Het is behulpzaam bij het ontdekken, verbinden en beheren van identiteitsproviders om gedistribueerd identiteitsbeheer te creëren voor hybride en multi-cloudondernemingen.

In deze zelfstudie wordt uitgelegd hoe u een on-premises webtoepassing die momenteel wordt beveiligd door een verouderd product voor webtoegangsbeheer (CA SiteMinder), kunt migreren om Azure AD te gebruiken voor verificatie en toegangsbeheer. Dit zijn de basisstappen:
1. Installeer Maverics Identity Orchestrator.
2. Registreer uw ondernemingstoepassing bij Azure AD en configureer deze om de Maverics Azure AD SAML Zero Code Connector te gebruiken voor eenmalige aanmelding met SAML.
3. Integreer Maverics met SiteMinder en het LDAP-gebruikersarchief (Lightweight Directory Access Protocol).
4. Stel een Azure-sleutelkluis in en configureer Maverics zodanig dat deze wordt gebruikt als provider van geheimenbeheer.
5. Demonstreer gebruikersmigratie en sessieabstractie met behulp van Maverics om toegang tot een on-premises Java-webtoepassing te bieden.

Ga voor aanvullende installatie- en configuratie-instructies naar de [website van Strata](https://www.strata.io).

## <a name="prerequisites"></a>Vereisten

- Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
- Een abonnement op Maverics Identity Orchestrator SAML Connector waarvoor eenmalige aanmelding is ingesteld. Als u de Maverics-software wilt aanschaffen, neemt u contact op met [de verkoopafdeling van Strata](mailto:sales@strata.io).

## <a name="install-maverics-identity-orchestrator"></a>Maverics Identity Orchestrator installeren

Als u aan de slag wilt gaan met het installeren van Maverics Identity Orchestrator, raadpleegt u de [installatie-instructies](https://www.strata.io).

### <a name="system-requirements"></a>Systeemvereisten
* Ondersteunde besturingssystemen
  * RHEL 7+
  * CentOS 7+

* Afhankelijkheden
  * systemd

### <a name="installation"></a>Installatie

1. Download het meest recente Maverics Redhat Package Manager (RPM)-pakket. Kopieer het pakket naar het systeem waarop u de Maverics-software wilt installeren.

2. Installeer het Maverics-pakket, waarbij u `maverics.rpm` vervangt door uw bestandsnaam.

    `sudo rpm -Uvf maverics.rpm`

3. Nadat u Maverics hebt geïnstalleerd, wordt dit als een service uitgevoerd onder `systemd`. Voer de volgende opdracht uit om te controleren of de service wordt uitgevoerd:

    `sudo systemctl status maverics`

Maverics wordt standaard geïnstalleerd in de map */usr/local/bin*.

Nadat u Maverics hebt geïnstalleerd, wordt het standaardbestand *maverics.yaml* in de map */etc/maverics* gemaakt. Voordat u uw configuratie bewerkt om `workflows` en `connectors` te bevatten, ziet uw configuratiebestand er als volgt uit:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="configuration-options"></a>Configuratie-opties
### <a name="version"></a>Versie
Het veld `version` geeft aan welke versie van het configuratiebestand wordt gebruikt. Als de versie niet is opgegeven, wordt de meest recente versie van het configuratiebestand gebruikt.

```yaml
version: 0.1
```
### <a name="listenaddress"></a>listenAddress
`listenAddress` declareert op welk adres Orchestrator luistert. Als het hostgedeelte van het adres leeg is, luistert Orchestrator op alle beschikbare unicast- en anycast-IP-adressen van het lokale systeem. Als het poortgedeelte van het adres leeg is, wordt er automatisch een poortnummer gekozen.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

Het veld `tls` declareert een kaart van TLS-objecten (Transport Layer Security). De TLS-objecten kunnen door connectors en de Orchestrator-server worden gebruikt. Raadpleeg de `transport`-pakketdocumentatie voor alle beschikbare TLS-opties.

Voor Microsoft Azure is communicatie via TLS vereist wanneer u op SAML gebaseerde eenmalige aanmelding gebruikt. Ga naar de [website van Let's Encrypt](https://letsencrypt.org/getting-started/) voor meer informatie over het genereren van certificaten.

De sleutel `maverics` is gereserveerd voor de Orchestrator-server. Alle andere sleutels zijn beschikbaar en kunnen worden gebruikt om een TLS-object in te voeren in een opgegeven connector.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>Bestanden opnemen

`connectors` en `workflows` kunnen worden gedefinieerd in hun eigen, afzonderlijke configuratiebestanden en er kan in het bestand *maverics.yaml* naar worden verwezen met behulp van `includeFiles`, zoals in het volgende voorbeeld:

```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

In deze zelfstudie wordt één *maverics.yaml*-configuratiebestand gebruikt.

## <a name="use-azure-key-vault-as-your-secrets-provider"></a>Azure Key Vault gebruiken als uw provider van geheimenbeheer

### <a name="manage-secrets"></a>Geheimen beheren

Maverics kan worden geïntegreerd met verschillende oplossingen voor geheimenbeheer om geheimen te laden. De huidige integraties omvatten een bestand, Hashicorp Vault en Azure Key Vault. Als er geen geheimenbeheeroplossing is opgegeven, laadt Maverics geheimen standaard als tekst zonder opmaak vanuit het bestand *maverics.yaml*.

Als u een waarde als een geheim wilt declareren in een *maverics.yaml*-configuratiebestand, zet u chevrons om het geheim:

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="load-secrets-from-a-file"></a>Geheimen vanuit een bestand laden

1. Als u geheimen vanuit een bestand wilt laden, voegt u omgevingsvariabele `MAVERICS_SECRET_PROVIDER` toe aan het bestand */etc/maverics/maverics.env* met behulp van:

   `MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

2. Vervolgens start u de Maverics-service opnieuw op door de volgende opdracht uit te voeren:

   `sudo systemctl restart maverics`

De inhoud van bestand *secrets.yaml* kan worden gevuld met een willekeurig aantal `secrets`.

```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="set-up-an-azure-key-vault"></a>Een Azure-sleutelkluis instellen

U kunt een Azure-sleutelkluis instellen met behulp van de Azure-portal of de Azure CLI.

**Azure Portal gebruiken**
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. [Een nieuwe sleutelkluis maken](../../key-vault/general/quick-create-portal.md).
1. [Geheimen toevoegen aan de sleutelkluis](../../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault).
1. [Een toepassing bij Azure AD registreren](../develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal).
1. [Een toepassing toestemming verlenen om een geheim te gebruiken](../../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault).

**Azure CLI gebruiken**

1. Open de [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) en voer de volgende opdracht in:

    ```shell
    az login
    ```

1. Maak een nieuwe sleutelkluis door de volgende opdracht uit te voeren:
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

1. Voeg de geheimen aan de sleutelkluis toe door de volgende opdracht uit te voeren:
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

1. Registreer een toepassing bij Azure AD door de volgende opdracht uit te voeren:
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

1. Verleen een toepassing toestemming een geheim te gebruiken door de volgende opdracht uit te voeren:
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

1. Als u geheimen wilt laden vanuit de Azure-sleutelkluis, stelt u omgevingsvariabele `MAVERICS_SECRET_PROVIDER` in bestand */etc/maverics/maverics.env* in met behulp van de referenties in het bestand *azure-credentials.json*, en in de volgende indeling:
 
   `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

1. Start de Maverics-service opnieuw op: `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Uw toepassing configureren in Azure AD voor eenmalige aanmelding met SAML

1. Ga in uw Azure AD-tenant naar **Ondernemingstoepassingen**, zoek **Maverics Identity Orchestrator SAML Connector** en selecteer het.

1. In het deelvenster **Eigenschappen** van Maverics Identity Orchestrator SAML Connector stelt u **Gebruikerstoewijzing vereist?** in op **Nee** om de toepassing te laten werken voor pas gemigreerde gebruikers.

1. In het deelvenster **Overzicht** van Maverics Identity Orchestrator SAML Connector selecteert u **Eenmalige aanmelding instellen** en selecteert u **SAML**.

1. In het deelvenster **Eenmalige aanmelding met SAML** van Maverics Identity Orchestrator SAML Connector bewerkt u de **standaard SAML-configuratie** door de knop **Bewerken** (potloodpictogram) te selecteren.

   ![Schermafbeelding van de knop Bewerken in het gedeelte Standaard SAML-configuratie.](common/edit-urls.png)

1. Voer de **entiteit-id** in door een URL te typen in de volgende indeling: `https://<SUBDOMAIN>.maverics.org`. De entiteit-id moet uniek zijn voor alle apps in de tenant. Sla de hier ingevoerde waarde op om deze op te nemen in de configuratie van Maverics.

1. Voer de **antwoord-URL** in in de volgende indeling: `https://<AZURECOMPANY.COM>/<MY_APP>/`. 

1. Voer de **aanmeldings-URL** in in de volgende indeling: `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>`. 

1. Selecteer **Opslaan**.

1. Selecteer in het gedeelte **SAML-handtekeningcertificaat** de knop **Kopiëren** om de **URL voor federatieve metagegevens van de app** te kopiëren, en sla de URL op de computer op.

    ![Schermafbeelding van de knop Kopiëren in het gedeelte SAML-handtekeningcertificaat.](common/copy-metadataurl.png)

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector"></a>Maverics Identity Orchestrator Azure AD SAML Connector configureren

Maverics Identity Orchestrator Azure AD Connector ondersteunt OpenID Connect en SAML Connect. Ga als volgt te werk om de connector te configureren: 

1. Stel `authType: saml` in om eenmalige aanmelding met SAML in te schakelen.

1. Maak de waarde voor `samlMetadataURL` in de volgende indeling: `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`.

1. Definieer de URL waarmee een gebruiker naar uw app wordt teruggeleid nadat deze zich heeft aangemeld met diens Azure-referenties. Gebruik de volgende indeling: `samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>`.

1. Kopieer de waarde van de entiteit-id die u eerder hebt geconfigureerd: `samlEntityID: https://<SUBDOMAIN>.maverics.org`.

1. Kopieer de waarde van de antwoord-URL die Azure AD gebruikt om het SAML-antwoord te posten: `samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>`.

1. Genereer een JWT-ondertekeningssleutel (JSON Web Token). Dit wordt gebruikt om de Maverics Identity Orchestrator-sessiegegevens te beveiligen met behulp van het [hulpprogramma OpenSSL](https://www.openssl.org/source/):

    ```shell 
    openssl rand 64 | base64
    ```
1. Kopieer het antwoord naar de configuratie-eigenschap `jwtSigningKey`: `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==`.

## <a name="attributes-and-attribute-mapping"></a>Kenmerken en kenmerktoewijzing
Kenmerktoewijzing wordt gebruikt om de toewijzing van gebruikerskenmerken uit een on-premises gebruikerslijst in een Azure AD-tenant te definiëren als de gebruiker is ingesteld.

Kenmerken bepalen welke gebruikersgegevens aan een toepassing kunnen worden geretourneerd in een claim, doorgegeven in sessiecookies of doorgegeven aan de toepassing in HTTP-headervariabelen.

## <a name="configure-the-maverics-identity-orchestrator-azure-ad-saml-connector-yaml-file"></a>YAML-bestand van Maverics Identity Orchestrator Azure AD SAML Connector configureren

Uw configuratie van Maverics Identity Orchestrator Azure AD Connector ziet er als volgt uit:

```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: <SIGNING KEY>
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="migrate-users-to-an-azure-ad-tenant"></a>Gebruikers migreren naar een Azure AD-tenant

Volg deze configuratie om gebruikers incrementeel te migreren vanuit een product voor webtoegangsbeheer zoals CA SiteMinder, Oracle Access Manager of IBM Tivoli. U kunt ze ook migreren vanuit een LDAP-adreslijst (Lightweight Directory Access Protocol) of een SQL-database.

### <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>Uw toepassingsmachtigingen configureren in Azure AD om gebruikers te maken

1. Ga in uw Azure AD-tenant naar `App registrations` en selecteer de toepassing **Maverics Identity Orchestrator SAML Connector**.

1. In het deelvenster **Maverics Identity Orchestrator SAML Connector |Certificaten en geheimen** selecteert u `New client secret` en vervolgens een vervaloptie. Selecteer de knop **Kopiëren** om het geheim te kopiëren. Sla het op uw computer op.

1. In het deelvenster **Maverics Identity Orchestrator SAML Connector | API-machtigingen**, selecteert u **Machtiging toevoegen**. Selecteer vervolgens in het deelvenster **API-machtigingen aanvragen** de optie **Microsoft Graph** en **Toepassingstoestemmingen**. 

1. Selecteer in het volgende scherm **User. ReadWrite.All** en vervolgens **Machtigingen toevoegen**. 

1. Terug in het deel venster **API-machtigingen** selecteert u **Beheerderstoestemming verlenen**.

### <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-file-for-user-migration"></a>Het YAML-bestand van Maverics Identity Orchestrator SAML Connector configureren voor gebruikersmigratie

Voeg de volgende aanvullende eigenschappen aan het configuratiebestand toe om de werkstroom voor gebruikersmigratie in te schakelen:
1. Voer de **Azure Graph-URL** in in de volgende indeling: `graphURL: https://graph.microsoft.com`.
1. Voer de **OAuth-token-URL** in in de volgende indeling: `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`.
1. Voer het eerder gegenereerde clientgeheim in in de volgende indeling: `oauthClientSecret: <CLIENT SECRET>`.


Het uiteindelijke configuratiebestand van Maverics Identity Orchestrator Azure AD Connector ziet er als volgt uit:

```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  graphURL: https://graph.microsoft.com
  oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/oauth2/v2.0/token
  oauthClientID: <APP ID>
  oauthClientSecret: <NEW CLIENT SECRET>
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

### <a name="configure-maverics-zero-code-connector-for-siteminder"></a>Maverics Zero Code Connector configureren voor SiteMinder

U gebruikt de SiteMinder-connector om gebruikers naar een Azure AD-Tenant te migreren. U meldt de gebruikers aan bij oudere, on-premises toepassingen die door SiteMinder worden beveiligd met behulp van de zojuist gemaakte Azure AD-identiteiten en -referenties.

Voor deze zelfstudie is SiteMinder geconfigureerd om de verouderde toepassing te beveiligen met op formulieren gebaseerde verificatie en met behulp van het `SMSESSION`-cookie. Als u wilt integreren met een app die gebruikmaakt van verificatie en sessies via HTTP-headers, moet u de header-emulatieconfiguratie aan de connector toevoegen.

In dit voorbeeld wordt het kenmerk `username` toegewezen aan HTTP-header `SM_USER`:

```yaml
  headers:
    SM_USER: username
```

Stel `proxyPass` in op de locatie waarnaar aanvragen via een proxy worden verzonden. Deze locatie is gewoonlijk de host van de beveiligde toepassing.

`loginPage` moet overeenkomen met de URL van het aanmeldingsformulier dat momenteel door SiteMinder wordt gebruikt wanneer gebruikers voor verificatie worden omgeleid.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

### <a name="configure-maverics-zero-code-connector-for-ldap"></a>Maverics Zero Code Connector configureren voor LDAP

Wanneer toepassingen worden beveiligd door een WAM-product (Web Access Management), bijvoorbeeld SiteMinder, worden gebruikersidentiteiten en kenmerken meestal opgeslagen in een LDAP-adreslijst.

In de configuratie van deze connector ziet u hoe u verbinding maakt met de LDAP-adreslijst. De connector is geconfigureerd als het gebruikersarchief voor SiteMinder, zodat de juiste gebruikersprofielgegevens tijdens de migratiewerkstroom kunnen worden verzameld en er een bijbehorende gebruiker in Azure AD kan worden gemaakt.

* `baseDN` specificeert de locatie in de adreslijst waarop de LDAP-zoekopdracht moet worden uitgevoerd.

* `url` is het adres en de poort van de LDAP-server waarmee verbinding moet worden gemaakt.

* `serviceAccountUsername` is de gebruikersnaam die wordt gebruikt om verbinding te maken met de LDAP-server, meestal uitgedrukt als een bindings-DN (bijvoorbeeld `CN=Directory Manager`).

* `serviceAccountPassword` is het wachtwoord dat wordt gebruikt om verbinding te maken met de LDAP-server. Deze waarde wordt opgeslagen in het Azure-sleutelkluisexemplaar dat eerder is geconfigureerd.  

* `userAttributes` definieert de lijst met gebruikerskenmerken waarop een query moet worden uitgevoerd. Deze kenmerken worden later toegewezen aan bijbehorende Azure AD-kenmerken.

```yaml
- name: company-ldap
  type: ldap
  url: "ldap://ldap.company.com:389"
  baseDN: ou=People,o=company,c=US
  serviceAccountUsername: uid=admin,ou=Admins,o=company,c=US
  serviceAccountPassword: <vaulted-password>
  userAttributes:
    - uid
    - cn
    - givenName
    - sn
    - mail
    - mobile
```

### <a name="configure-the-migration-workflow"></a>De migratiewerkstroom configureren

De configuratie van de migratiewerkstroom bepaalt hoe Maverics gebruikers migreert van SiteMinder of LDAP naar Azure AD.

Deze werkstroom:
- Maakt gebruik van de SiteMinder-connector om de SiteMinder-aanmelding via een proxy uit te voeren. Gebruikersreferenties worden gevalideerd via SiteMinder-verificatie en vervolgens doorgegeven aan volgende stappen van de werkstroom.
- Haalt gebruikersprofielkenmerken op uit het SiteMinder-gebruikersarchief.
- Dient een aanvraag in bij de Microsoft Graph-API om de gebruiker te maken in uw Azure AD-tenant.

Als u de migratiewerkstroom wilt configureren, gaat u als volgt te werk:

1. Geef de werkstroom een naam, bijvoorbeeld **Migratie van SiteMinder naar Azure AD**.
1. Geef het `endpoint` op. Dit is een HTTP-pad waarop de werkstroom beschikbaar wordt gemaakt, waardoor de `actions` van die werkstroom worden geactiveerd in reactie op aanvragen. Het `endpoint` komt meestal overeen met de app die via een proxy wordt verzonden (bijvoorbeeld `/my_app`). De waarde moet zowel de voorloop- als volgslash bevatten.
1. Voeg de toepasselijke `actions` aan de werkstroom toe.

   a. Definieer de `login`-methode voor de SiteMinder-connector. De connectorwaarde moet overeenkomen met de naamwaarde in de connectorconfiguratie.

   b. Definieer de `getprofile`-methode voor de LDAP-connector.

   c.  Definieer de `createuser`-methode voor de Azure AD-connector.

    ```yaml
      workflows:
      - name: SiteMinder to Azure AD Migration
        endpoint: /my_app/
        actions:
        - connector: siteminder-login-form
          method: login
        - connector: company-ldap
          method: getprofile
        - connector: AzureAD
          method: createuser
    ```
### <a name="verify-the-migration-workflow"></a>De migratiewerkstroom verifiëren

1. Als de Maverics-service nog niet wordt uitgevoerd, start u deze door de volgende opdracht uit te voeren:  

   `sudo systemctl start maverics`

1. Navigeer naar de aanmeldings-URL die via een proxy is verzonden, `http://host.company.com/my_app`.
1. Geef gebruikersreferenties op die worden gebruikt voor aanmelding bij de toepassing terwijl deze door SiteMinder wordt beveiligd.
4. Ga naar **Start** > **Gebruikers | Alle gebruikers** om te verifiëren dat de gebruiker is gemaakt in de Azure AD-tenant.  

### <a name="configure-the-session-abstraction-workflow"></a>De werkstroom voor sessieabstractie configureren

Met de werkstroom voor sessieabstractie worden verificatie en toegangsbeheer voor de verouderde, on-premises webtoepassing naar de Azure AD-tenant verplaatst.

De Azure-connector maakt gebruik van de `login`-methode om de gebruiker naar de aanmeldings-URL om te leiden, ervan uitgaande dat er geen sessie bestaat.

Als de verificatie is voltooid, wordt het sessietoken, dat als resultaat is gemaakt, doorgegeven aan Maverics. De `emulate`-methode van de SiteMinder-connector wordt gebruikt om de op cookies of headers gebaseerde sessie te emuleren en vervolgens de aanvraag te voorzien van aanvullende kenmerken die door de toepassing worden vereist.

1. Geef de werkstroom een naam, bijvoorbeeld **Sessieabstractie SiteMinder**.
1. Geeft het `endpoint` op, dat overeenkomt met de app die via een proxy wordt verzonden. De waarde moet zowel de voorloop- als volgslash bevatten, bijvoorbeeld `/my_app/`.
1. Voeg de toepasselijke `actions` aan de werkstroom toe.

   a. Definieer de `login`-methode voor de Azure-connector. De `connector`-waarde moet overeenkomen met de `name`-waarde in de connectorconfiguratie.

   b. Definieer de `emulate`-methode voor de SiteMinder-connector.

     ```yaml
      - name: SiteMinder Session Abstraction
        endpoint: /my_app/
        actions:
      - connector: azure
        method: login
      - connector: siteminder-login-form
        method: emulate
     ```
### <a name="verify-the-session-abstraction-workflow"></a>De werkstroom voor sessieabstractie verifiëren

1. Ga naar de toepassings-URL die via een proxy is verzonden, `https://<AZURECOMPANY.COM>/<MY_APP>`. 
    
    U wordt omgeleid naar de aanmeldingspagina die via een proxy is verzonden.

1. Voer de Azure AD-gebruikersreferenties in.

   U wordt naar de toepassing omgeleid alsof u rechtstreeks door SiteMinder bent geverifieerd.