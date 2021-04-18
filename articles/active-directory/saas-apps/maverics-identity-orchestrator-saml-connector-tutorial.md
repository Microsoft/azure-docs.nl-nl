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
ms.openlocfilehash: 402f6cd6961108cdf1e9c94fb4f93309fbf15ead
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599023"
---
# <a name="integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Een aanmelding van Azure AD integreren met Maverics Identity Orchestrator SAML Connector

Maverics Identity Orchestrator van Strata biedt een eenvoudige manier om on-premises toepassingen te integreren met Azure Active Directory (Azure AD) voor verificatie en toegangsbeheer. De Maverics Orchestrator kan verificatie en autorisatie moderniseren voor apps die momenteel afhankelijk zijn van headers, cookies en andere eigen verificatiemethoden. Maverics Orchestrator-exemplaren kunnen on-premises of in de cloud worden geïmplementeerd. 

In deze zelfstudie over hybride toegang wordt gedemonstreerd hoe u een on-premises webtoepassing migreert die momenteel wordt beveiligd door een verouderd product voor webtoegangsbeheer om Azure AD te gebruiken voor verificatie en toegangsbeheer. Dit zijn de basisstappen:

1. De Maverics Orchestrator instellen
1. Een proxy voor een toepassing
1. Een bedrijfstoepassing registreren in Azure AD
1. Verifiëren via Azure en toegang tot de toepassing autoriseren
1. Headers toevoegen voor naadloze toegang tot toepassingen
1. Werken met meerdere toepassingen

## <a name="prerequisites"></a>Vereisten

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Maverics Identity Orchestrator SAML Connector waarvoor eenmalige aanmelding is ingesteld. Neem contact op met strata sales om de Maverics-software [op te halen.](mailto:sales@strata.io)
* Ten minste één toepassing die gebruikmaakt van verificatie op basis van headers. De voorbeelden werken met een toepassing met de naam Connectulum, gehost op `https://app.connectulum.com` .
* Een Linux-computer voor het hosten van de Maverics Orchestrator
  * Besturingssysteem: RHEL 7.7 of hoger, CentOS 7+
  * Schijf: >= 10 GB
  * Geheugen: >= 4 GB
  * Poorten: 22 (SSH/SCP), 443, 7474
  * Toegang tot hoofdmap voor installatie-/beheertaken
  * Het netwerk gaat van de server die als host voor de Maverics Identity Orchestrator naar uw beveiligde toepassing wordt gehost

## <a name="step-1-set-up-the-maverics-orchestrator"></a>Stap 1: De Maverics Orchestrator instellen

### <a name="install-maverics"></a>Maverics installeren

1. Haal de nieuwste Maverics RPM op. Kopieer het pakket naar het systeem waarop u de Maverics-software wilt installeren.

1. Installeer het Maverics-pakket, waarbij u `maverics.rpm` vervangt door uw bestandsnaam.

   `sudo rpm -Uvf maverics.rpm`

   Nadat u Maverics hebt geïnstalleerd, wordt dit als een service uitgevoerd onder `systemd`. Voer de volgende opdracht uit om te controleren of de service wordt uitgevoerd:

   `sudo systemctl status maverics`

1. Als u de Orchestrator opnieuw wilt starten en de logboeken wilt volgen, kunt u de volgende opdracht uitvoeren:

   `sudo service maverics restart; sudo journalctl --identifier=maverics -f`

Nadat u Maverics hebt geïnstalleerd, wordt het `maverics.yaml` standaardbestand gemaakt in de `/etc/maverics` map . Voordat u de configuratie bewerkt om en op `appgateways` te `connectors` nemen, ziet uw configuratiebestand er als de volgende z uit:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```

### <a name="configure-dns"></a>DNS configureren

DNS is handig, zodat u het IP-adres van de Orchestrator-server niet hoeft te onthouden.

Bewerk het hostbestand van de browsercomputer (op uw laptop) met behulp van een hypothetisch Orchestrator-IP-adres van 12.34.56.78. Op Linux-gebaseerde besturingssystemen bevindt dit bestand zich in `/etc/hosts` . In Windows bevindt deze zich op `C:\windows\system32\drivers\etc` .

```
12.34.56.78 sonar.maverics.com
12.34.56.78 connectulum.maverics.com
```

Als u wilt controleren of DNS is geconfigureerd zoals verwacht, kunt u een aanvraag indienen bij het status-eindpunt van de Orchestrator. Vraag in uw browser http://sonar.maverics.com:7474/status aan.

### <a name="configure-tls"></a>TLS configureren

Communicatie via beveiligde kanalen om met uw Orchestrator te communiceren is essentieel om de beveiliging te handhaven. U kunt een certificaat/sleutelpaar toevoegen aan uw `tls` sectie om dit te bereiken.

Als u een zelf-ondertekend certificaat en een sleutel voor de Orchestrator-server wilt genereren, moet u de volgende opdracht uitvoeren vanuit de `/etc/maverics` map :

`openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out maverics.crt -keyout maverics.key`

> [!NOTE]
> Voor productieomgevingen wilt u waarschijnlijk een certificaat gebruiken dat is ondertekend door een bekende CA om waarschuwingen in de browser te voorkomen. [Let's Encrypt](https://letsencrypt.org/) is een goede en gratis optie als u op zoek bent naar een vertrouwde CA.

Gebruik nu het zojuist gegenereerde certificaat en de sleutel voor de Orchestrator. Uw configuratiebestand moet nu deze code bevatten:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key
```

Als u wilt controleren of TLS is geconfigureerd zoals verwacht, start u de Maverics-service opnieuw en doet u een aanvraag bij het status-eindpunt.

## <a name="step-2-proxy-an-application"></a>Stap 2: Een proxy voor een toepassing gebruiken

Configureer vervolgens basisproxy's in de Orchestrator met behulp van `appgateways` . Met deze stap kunt u controleren of de Orchestrator de benodigde verbinding met de beveiligde toepassing heeft.

Uw configuratiebestand moet nu deze code bevatten:

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

Als u wilt controleren of proxying werkt zoals verwacht, start u de Maverics-service opnieuw en doet u een aanvraag bij de toepassing via de Maverics-proxy. U kunt eventueel een aanvraag indienen bij specifieke toepassingsresources.

## <a name="step-3-register-an-enterprise-application-in-azure-ad"></a>Stap 3: Een bedrijfstoepassing registreren in Azure AD

Maak nu een nieuwe bedrijfstoepassing in Azure AD die wordt gebruikt voor het authenticeren van eindgebruikers.

> [!NOTE]
> Wanneer u Azure AD-functies zoals voorwaardelijke toegang gebruikt, is het belangrijk om een bedrijfstoepassing per on-premises toepassing te maken. Hierdoor is voorwaardelijke toegang per app, evaluatie van risico's per app, per app toegewezen machtigingen, en meer mogelijk. Over het algemeen wordt een bedrijfstoepassing in Azure AD aan een Azure-connector in Maverics toebesteed.

Een bedrijfstoepassing registreren in Azure AD:

1. Ga in uw Azure AD-tenant naar **Bedrijfstoepassingen** en selecteer **vervolgens Nieuwe toepassing.** Zoek in de Azure AD-galerie **naar Maverics Identity Orchestrator SAML Connector** en selecteer deze.

1. Stel in het deelvenster Eigenschappen van Maverics Identity Orchestrator  SAML **Connector** De gebruikerstoewijzing **vereist?** in op Nee om in te stellen dat de toepassing werkt voor alle gebruikers in uw directory.

1. In het deelvenster **Overzicht** van Maverics Identity Orchestrator SAML Connector selecteert u **Eenmalige aanmelding instellen** en selecteert u **SAML**.

1. In het deelvenster **Eenmalige aanmelding met SAML** van Maverics Identity Orchestrator SAML Connector bewerkt u de **standaard SAML-configuratie** door de knop **Bewerken** (potloodpictogram) te selecteren.

   ![Schermafbeelding van de knop Bewerken in het gedeelte Standaard SAML-configuratie.](common/edit-urls.png)

1. Voer een **entiteits-id** van `https://sonar.maverics.com` in. De entiteits-id moet uniek zijn binnen de apps in de tenant en kan een willekeurige waarde zijn. U gebruikt deze waarde wanneer u in de volgende sectie het veld `samlEntityID` voor uw Azure-connector definieert.

1. Voer een **antwoord-URL van** `https://sonar.maverics.com/acs` in. U gebruikt deze waarde bij het definiëren van het `samlConsumerServiceURL` veld voor uw Azure-connector in de volgende sectie.

1. Voer een **aanmeldings-URL van** `https://sonar.maverics.com/` in. Dit veld wordt niet gebruikt door Maverics, maar is vereist in Azure AD om gebruikers toegang te geven tot de toepassing via de Azure AD-Mijn apps portal.

1. Selecteer **Opslaan**.

1. Selecteer in **de sectie SAML-handtekeningcertificaat** de knop Kopiëren om de waarde van  **app-URL** voor federatief metagegevens te kopiëren en sla deze vervolgens op uw computer op.

   ![Schermafbeelding van de knop Kopiëren in het gedeelte SAML-handtekeningcertificaat.](common/copy-metadataurl.png)

## <a name="step-4-authenticate-via-azure-and-authorize-access-to-the-application"></a>Stap 4: verifiëren via Azure en toegang tot de toepassing autoriseren

Plaats vervolgens de bedrijfstoepassing die u zojuist hebt gemaakt voor gebruik door de Azure-connector te configureren in Maverics. Met `connectors` deze configuratie die is gekoppeld aan het `idps` -blok, kan de Orchestrator gebruikers verifiëren.

Uw configuratiebestand moet nu de volgende code bevatten. Zorg ervoor dat u vervangt `METADATA_URL` door de waarde van app-URL voor federatiemetagegevens uit de vorige stap.

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

Als u wilt controleren of de verificatie werkt zoals verwacht, start u de Maverics-service opnieuw en doet u een aanvraag bij een toepassingsresource via de Maverics-proxy. U moet worden omgeleid naar Azure voor verificatie voordat u toegang krijgt tot de resource.

## <a name="step-5-add-headers-for-seamless-application-access"></a>Stap 5: headers toevoegen voor naadloze toegang tot toepassingen

U verstuurt nog geen headers naar de upstream-toepassing. Laten we aan de aanvraag toevoegen terwijl deze de Maverics-proxy passeert, zodat de `headers` upstream-toepassing de gebruiker kan identificeren.

Uw configuratiebestand moet nu deze code bevatten:

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

Als u wilt controleren of de verificatie werkt zoals verwacht, moet u een aanvraag indienen bij een toepassingsresource via de Maverics-proxy. De beveiligde toepassing ontvangt nu headers voor de aanvraag. 

U kunt de headersleutels bewerken als uw toepassing andere headers verwacht. Alle claims die afkomstig zijn van Azure AD als onderdeel van de SAML-stroom, zijn beschikbaar voor gebruik in headers. U kunt bijvoorbeeld een andere header van opnemen, waarbij de naam van de connector is en een claim is die `secondary_email: azureSonarApp.email` wordt geretourneerd door Azure `azureSonarApp` `email` AD. 

## <a name="step-6-work-with-multiple-applications"></a>Stap 6: Werken met meerdere toepassingen

Laten we nu eens kijken wat er nodig is om een proxy uit te kunnen geven naar meerdere toepassingen die zich op verschillende hosts. Als u deze stap wilt uitvoeren, configureert u App Gateway, een andere bedrijfstoepassing in Azure AD en een andere connector.

Uw configuratiebestand moet nu deze code bevatten:

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

Het is u misschien opgevallen dat de code een veld `host` toevoegt aan uw App Gateway definities. Het veld stelt Maverics Orchestrator in staat om te onderscheiden naar welke `host` upstream-host verkeer via een proxy wordt doorverweken.

Als u wilt controleren of de zojuist toegevoegde App Gateway werkt zoals verwacht, moet u een aanvraag indienen bij `https://connectulum.maverics.com` .

## <a name="advanced-scenarios"></a>Geavanceerde scenario's

### <a name="identity-migration"></a>Identiteitsmigratie

Kunt u het beheerprogramma voor webtoegang aan het einde van de levensduur niet aan, maar u hebt geen manier om uw gebruikers te migreren zonder massaal wachtwoord opnieuw in te stellen? Maverics Orchestrator ondersteunt identiteitsmigratie met behulp van `migrationgateways` .

### <a name="web-server-gateways"></a>Webservergateways

Wilt u uw netwerk- en proxyverkeer niet herwerken via maverics Orchestrator? Geen probleem. De Maverics Orchestrator kan worden gekoppeld aan webservergateways (modules) om dezelfde oplossingen te bieden zonder proxy's.

## <a name="wrap-up"></a>Wrap-up

Op dit moment hebt u de Maverics Orchestrator geïnstalleerd, een bedrijfstoepassing gemaakt en geconfigureerd in Azure AD, en de Orchestrator geconfigureerd voor proxy naar een beveiligde toepassing terwijl verificatie- en afdwingingsbeleid vereist is. Neem contact op met Strata voor meer informatie over hoe de Maverics Orchestrator kan worden gebruikt voor gebruiksgevallen voor gedistribueerd [identiteitsbeheer.](mailto:sales@strata.io)

## <a name="next-steps"></a>Volgende stappen

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)
