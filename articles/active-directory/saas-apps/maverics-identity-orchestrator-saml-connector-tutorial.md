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
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 19f6b0601afe9ad84f02c93d7f6e1ae3a71a06a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585091"
---
# <a name="integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Eenmalige aanmelding voor Azure AD integreren met de Maverics Identity Orchestrator-SAML-connector

De Maverics Identity orchestrator van Strata biedt een eenvoudige manier om on-premises toepassingen te integreren met Azure Active Directory (Azure AD) voor verificatie en toegangs beheer. De Maverics Orchestrator kan authenticatie en autorisatie moderniseren voor apps die momenteel zijn gebaseerd op headers, cookies en andere eigen verificatie methoden. Maverics Orchestrator-exemplaren kunnen on-premises of in de cloud worden geïmplementeerd. 

In deze hybride zelf studie wordt gedemonstreerd hoe u een on-premises webtoepassing migreert die momenteel wordt beveiligd door een verouderd web Access-beheer product om Azure AD te gebruiken voor verificatie en toegangs beheer. Dit zijn de basisstappen:

1. De Maverics-Orchestrator instellen
1. Een toepassing proxy
1. Een bedrijfs toepassing registreren in azure AD
1. Verifiëren via Azure en toegang verlenen tot de toepassing
1. Headers toevoegen voor naadloze toegang tot toepassingen
1. Werken met meerdere toepassingen

## <a name="prerequisites"></a>Vereisten

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Maverics Identity Orchestrator SAML Connector waarvoor eenmalige aanmelding is ingesteld. Neem contact op met [Strata Sales](mailto:sales@strata.io)om de Maverics-software op te halen.
* Ten minste één toepassing die gebruikmaakt van verificatie op basis van een koptekst. De voor beelden werken op een toepassing met de naam sonar, die wordt gehost op https://app.sonarsystems.com , en een toepassing met de naam Connectulum, die wordt gehost op https://app.connectulum.com .
* Een Linux-machine voor het hosten van de Maverics-Orchestrator
  * Besturings systeem: RHEL 7,7 of hoger, CentOS 7 +
  * Schijf: >= 10 GB
  * Geheugen: >= 4 GB
  * Poorten: 22 (SSH/SCP), 443, 7474
  * Toegang tot de hoofdmap voor installatie/beheer taken
  * Netwerk uitgaand van de server die als host fungeert voor de Maverics Identity Orchestrator aan uw beveiligde toepassing

## <a name="step-1-set-up-the-maverics-orchestrator"></a>Stap 1: de Maverics-Orchestrator instellen

### <a name="install-maverics"></a>Maverics installeren

1. Ontvang de meest recente Maverics-RPM. Kopieer het pakket naar het systeem waarop u de Maverics-software wilt installeren.

1. Installeer het Maverics-pakket, waarbij u `maverics.rpm` vervangt door uw bestandsnaam.

   `sudo rpm -Uvf maverics.rpm`

   Nadat u Maverics hebt geïnstalleerd, wordt dit als een service uitgevoerd onder `systemd`. Voer de volgende opdracht uit om te controleren of de service wordt uitgevoerd:

   `sudo systemctl status maverics`

1. Als u de Orchestrator opnieuw wilt starten en de logboeken wilt volgen, kunt u de volgende opdracht uitvoeren:

   `sudo service maverics restart; sudo journalctl --identifier=maverics -f`

Nadat u Maverics hebt geïnstalleerd, wordt het standaard `maverics.yaml` bestand in de `/etc/maverics` map gemaakt. Voordat u de configuratie bewerkt `appgateways` `connectors` , ziet uw configuratie bestand er als volgt uit:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```

### <a name="configure-dns"></a>DNS configureren

DNS is handig, zodat u het IP-adres van de Orchestrator-server niet hoeft te onthouden.

Het hosts-bestand van de browser machine (uw laptop) bewerken met behulp van een hypothetisch Orchestrator IP van 12.34.56.78. Op Linux-besturings systemen bevindt dit bestand zich in `/etc/hosts` . In Windows bevindt het zich op `C:\windows\system32\drivers\etc` .

```
12.34.56.78 sonar.maverics.com
12.34.56.78 connectulum.maverics.com
```

Als u wilt controleren of DNS is geconfigureerd zoals verwacht, kunt u een aanvraag indienen bij het status eindpunt van de Orchestrator. Vraag vanuit uw browser naar http://sonar.maverics.com:7474/status .

### <a name="configure-tls"></a>TLS configureren

Communiceren via beveiligde kanalen om met uw Orchestrator te communiceren is essentieel voor het onderhouden van de beveiliging. U kunt een certificaat/sleutel paar in uw `tls` sectie toevoegen om dit te doen.

Als u een zelfondertekend certificaat en een sleutel voor de Orchestrator-server wilt genereren, voert u de volgende opdracht uit vanuit de `/etc/maverics` map:

`openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out maverics.crt -keyout maverics.key`

> [!NOTE]
> Voor productie omgevingen wilt u waarschijnlijk een certificaat gebruiken dat is ondertekend door een bekende CA om waarschuwingen in de browser te voor komen. U kunt het beste een goede en gratis optie [versleutelen](https://letsencrypt.org/) als u op zoek bent naar een vertrouwde certificerings instantie.

Gebruik nu het zojuist gegenereerde certificaat en de sleutel voor Orchestrator. Het configuratie bestand moet nu de volgende code bevatten:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key
```

Als u wilt controleren of TLS is geconfigureerd zoals verwacht, start u de Maverics-service opnieuw en brengt u een aanvraag naar het status eindpunt. Vraag vanuit uw browser naar https://sonar.maverics.com/status .

## <a name="step-2-proxy-an-application"></a>Stap 2: een toepassing proxy

Configureer vervolgens basis proxy in de Orchestrator met behulp van `appgateways` . Deze stap helpt u te valideren dat de Orchestrator de benodigde connectiviteit voor de beveiligde toepassing heeft.

Het configuratie bestand moet nu de volgende code bevatten:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com
```

Om te bevestigen dat de proxy werkt zoals verwacht, start u de Maverics-service opnieuw en maakt u een aanvraag voor de toepassing via de Maverics-proxy. Vraag vanuit uw browser naar https://sonar.maverics.com . U kunt eventueel een aanvraag indienen voor specifieke toepassings resources, bijvoorbeeld, `https://sonar.maverics.com/RESOURCE` waarbij `RESOURCE` een geldige toepassings resource van de beveiligde upstream-app is.

## <a name="step-3-register-an-enterprise-application-in-azure-ad"></a>Stap 3: een bedrijfs toepassing registreren in azure AD

Maak nu een nieuwe bedrijfs toepassing in azure AD die wordt gebruikt voor de verificatie van eind gebruikers.

> [!NOTE]
> Wanneer u Azure AD-functies, zoals voorwaardelijke toegang, gebruikt, is het belang rijk dat u een bedrijfs toepassing maakt per on-premises toepassing. Dit biedt voorwaardelijke toegang per app, per app-risico evaluatie, toegewezen machtigingen per app, enzovoort. Over het algemeen wordt een bedrijfs toepassing in azure AD toegewezen aan een Azure-connector in Maverics.

Een bedrijfs toepassing registreren in azure AD:

1. Ga in uw Azure AD-Tenant naar **bedrijfs toepassingen** en selecteer vervolgens **nieuwe toepassing**. Zoek in de Azure AD-galerie naar **Maverics Identity ORCHESTRATOR SAML connector** en selecteer deze.

1. Stel in het deel venster **Eigenschappen** van de Maverics-identiteits Orchestrator SAML-connector de **gebruikers toewijzing vereist** in op **Nee** om de toepassing te laten werken voor alle gebruikers in uw Directory.

1. In het deelvenster **Overzicht** van Maverics Identity Orchestrator SAML Connector selecteert u **Eenmalige aanmelding instellen** en selecteert u **SAML**.

1. In het deelvenster **Eenmalige aanmelding met SAML** van Maverics Identity Orchestrator SAML Connector bewerkt u de **standaard SAML-configuratie** door de knop **Bewerken** (potloodpictogram) te selecteren.

   ![Schermafbeelding van de knop Bewerken in het gedeelte Standaard SAML-configuratie.](common/edit-urls.png)

1. Voer een **entiteit-id** van in `https://sonar.maverics.com` . De entiteit-ID moet uniek zijn in de apps in de Tenant en kan een wille keurige waarde zijn. U gebruikt deze waarde wanneer u het `samlEntityID` veld voor uw Azure-connector in de volgende sectie definieert.

1. Voer een **antwoord-URL** van in `https://sonar.maverics.com/acs` . U gebruikt deze waarde wanneer u het `samlConsumerServiceURL` veld voor uw Azure-connector in de volgende sectie definieert.

1. Voer een **aanmeldings-URL** van in `https://sonar.maverics.com/` . Dit veld wordt niet gebruikt door Maverics, maar is wel vereist in azure AD om gebruikers in staat te stellen om toegang te krijgen tot de toepassing via de Azure AD my apps-Portal.

1. Selecteer **Opslaan**.

1. Selecteer in de sectie **SAML-handtekening certificaat** de knop **kopiëren** om de URL-waarde van de **app Federation-meta gegevens** te kopiëren en deze vervolgens op uw computer op te slaan.

   ![Schermafbeelding van de knop Kopiëren in het gedeelte SAML-handtekeningcertificaat.](common/copy-metadataurl.png)

## <a name="step-4-authenticate-via-azure-and-authorize-access-to-the-application"></a>Stap 4: verifiëren via Azure en toegang verlenen tot de toepassing

Vervolgens plaatst u de bedrijfs toepassing die u zojuist hebt gemaakt voor gebruik door de Azure-connector te configureren in Maverics. `connectors`Met deze configuratie gekoppeld aan het `idps` blok kan de Orchestrator gebruikers verifiëren.

Het configuratie bestand moet nu de volgende code bevatten. Zorg ervoor dat u vervangt door `METADATA_URL` de URL-waarde van de app Federation-meta gegevens uit de vorige stap.

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

Als u wilt bevestigen dat de verificatie werkt zoals verwacht, start u de Maverics-service opnieuw en maakt u een aanvraag voor een toepassings bron via de Maverics-proxy. U moet worden omgeleid naar Azure voor verificatie voordat u toegang krijgt tot de resource.

## <a name="step-5-add-headers-for-seamless-application-access"></a>Stap 5: headers toevoegen voor naadloze toegang tot toepassingen

U verzendt nog geen kopteksten naar de upstream-toepassing. We gaan `headers` aan de aanvraag toevoegen omdat deze via de Maverics-proxy wordt door gegeven, zodat de upstream-toepassing de gebruiker kan identificeren.

Het configuratie bestand moet nu de volgende code bevatten:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

Om te bevestigen dat de verificatie werkt zoals verwacht, moet u een aanvraag indienen bij een toepassings bron via de Maverics-proxy. De beveiligde toepassing moet nu kopteksten ontvangen voor de aanvraag. 

Bewerk de header sleutels als uw toepassing verschillende kopteksten verwacht. Alle claims die terugkomen van Azure AD als onderdeel van de SAML-stroom, zijn beschikbaar voor gebruik in kopteksten. U kunt bijvoorbeeld een andere header van gebruiken `secondary_email: azureSonarApp.email` , waarbij `azureSonarApp` de naam van de connector is en `email` een claim die wordt geretourneerd door Azure AD. 

## <a name="step-6-work-with-multiple-applications"></a>Stap 6: werken met meerdere toepassingen

We gaan nu kijken wat er is vereist voor het maken van een proxy naar meerdere toepassingen die zich op verschillende hosts bevinden. Als u deze stap wilt uitvoeren, configureert u een andere app-gateway, een andere bedrijfs toepassing in azure AD en een andere connector.

Het configuratie bestand moet nu de volgende code bevatten:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp
  - name: azureConnectulumApp

appgateways:
  - name: sonar
    host: sonar.maverics.com
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

  - name: connectulum
    host: connectulum.maverics.com
    location: /
    # Replace https://app.connectulum.com with the address of your protected application
    upstream: https://app.connectulum.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureConnectulumApp.authenticated}}", "true"]

    headers:
      email: azureConnectulumApp.name
      firstname: azureConnectulumApp.givenname
      lastname: azureConnectulumApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com

  - name: azureConnectulumApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://connectulum.maverics.com/acs
    samlEntityID: https://connectulum.maverics.com
```

Mogelijk hebt u opgemerkt dat de code een `host` veld toevoegt aan de definities van uw app-gateway. Met het `host` veld kan de Maverics-Orchestrator onderscheid maken tussen de upstream-host en het proxy verkeer naar.

Ga als volgt te werk om te bevestigen dat de zojuist toegevoegde app-gateway werkt zoals verwacht https://connectulum.maverics.com .

## <a name="advanced-scenarios"></a>Geavanceerde scenario's

### <a name="identity-migration"></a>Identiteits migratie

Kunt u niet voorzien in een hulp programma voor het beheer van de volledige levens cyclus voor webtoegang, maar u hebt geen manier om uw gebruikers te migreren zonder dat massa-wacht woorden opnieuw worden ingesteld? De Maverics Orchestrator ondersteunt identiteits migratie met behulp van `migrationgateways` .

### <a name="web-server-gateways"></a>Webserver gateways

Wilt u uw netwerk en proxy verkeer niet opnieuw gebruiken via de Maverics-Orchestrator? Geen probleem. De Maverics Orchestrator kan worden gekoppeld aan webserver gateways (modules) om dezelfde oplossingen aan te bieden zonder te kunnen verbinding maken.

## <a name="wrap-up"></a>Inpakken

Op dit moment hebt u de Maverics-Orchestrator geïnstalleerd, een bedrijfs toepassing gemaakt en geconfigureerd in azure AD, en de Orchestrator geconfigureerd voor een beveiligde toepassing, terwijl verificatie en het afdwingen van beleid is vereist. [Neem contact](mailto:sales@strata.io)op met Strata voor meer informatie over hoe de Maverics-Orchestrator kan worden gebruikt voor gedistribueerde identiteits beheer.

## <a name="next-steps"></a>Volgende stappen

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)
