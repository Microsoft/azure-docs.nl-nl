---
title: Beheerdershandleiding voor Atlassian Jira/Confluence - Azure Active Directory| Microsoft Docs
description: Beheerdershandleiding voor het gebruik van Atlassian Jira en Confluence met Azure Active Directory (Azure AD).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 8e73ea3650e631bed277ab95092b714eef7596d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359154"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Beheerdershandleiding voor Atlassian Jira en Confluence voor Azure Active Directory

## <a name="overview"></a>Overzicht

Met de Azure Active Directory-invoegtoepassing (Azure AD) voor eenmalige aanmelding kunnen klanten van Microsoft Azure AD hun werk- of schoolaccount gebruiken om zich aan te melden bij op Atlassian Jira en Confluence Server gebaseerde producten. Door deze invoegtoepassing wordt eenmalige aanmelding op basis van SAML 2.0 geïmplementeerd.

## <a name="how-it-works"></a>Uitleg

Wanneer gebruikers zich willen aanmelden bij de Atlassian Jira- of Confluence-toepassing, zien ze de knop **Login with Azure AD** op de aanmeldingspagina. Als ze deze selecteren, moeten ze zich aanmelden met behulp van de aanmeldingspagina van de Azure AD-organisatie (dat wil zeggen hun werk- of schoolaccount).

Nadat de gebruikers zijn geverifieerd, moeten ze zich bij de toepassing kunnen aanmelden. Als ze al zijn geverifieerd met de id en het wachtwoord voor hun werk- of schoolaccount, kunnen ze zich rechtstreeks bij de toepassing aanmelden. 

Aanmelden werkt overal in Jira en Confluence. Als gebruikers zijn aangemeld bij de Jira-toepassing en Confluence in hetzelfde browservenster wordt geopend, hoeven ze de referenties voor de andere app niet op te geven. 

Gebruikers kunnen via Mijn apps in het werk- of schoolaccount ook toegang krijgen tot het Atlassian-product. Ze zouden moeten worden aangemeld zonder dat hen om referenties wordt gevraagd.

> [!NOTE]
> Gebruikersinrichting wordt niet uitgevoerd via de invoegtoepassing.

## <a name="audience"></a>Doelgroep

Jira- en Confluence-beheerders kunnen de invoegtoepassing gebruiken om eenmalige aanmelding in te schakelen met behulp van Azure AD.

## <a name="assumptions"></a>Aannames

* Voor Jira- en Confluence-exemplaren is HTTPS ingeschakeld.
* Er zijn al gebruikers gemaakt in Jira of Confluence.
* Aan gebruikers zijn rollen toegewezen in Jira of Confluence.
* Beheerders hebben toegang tot de vereiste informatie voor het configureren van de invoegtoepassing.
* Jira of Confluence is ook beschikbaar buiten het bedrijfsnetwerk.
* De invoegtoepassing werkt alleen met de on-premises versie van Jira en Confluence.

## <a name="prerequisites"></a>Vereisten

Houd rekening met de volgende informatie voordat u de invoegtoepassing installeert:

* Jira en Confluence zijn geïnstalleerd op een 64-bits versie van Windows.
* Voor Jira- en Confluence-versies is HTTPS ingeschakeld.
* Jira en Confluence zijn beschikbaar op internet.
* Er zijn beheerdersreferenties aanwezig voor Jira en Confluence.
* Er zijn beheerdersreferenties aanwezig voor Azure AD.
* WebSudo is uitgeschakeld in Jira en Confluence.

## <a name="supported-versions-of-jira-and-confluence"></a>Ondersteunde versies van Jira en Confluence

De invoegtoepassing biedt ondersteuning voor de volgende versies van Jira en Confluence:

* Jira Core en Software: 6.0 – 7.12
* Jira Service Desk: 3.0.0 tot en met 3.5.0
* JIRA ondersteunt ook 5.2. Klik voor meer informatie op [Microsoft Azure Active Directory-eenmalige aanmelding voor JIRA 5.2](./jira52microsoft-tutorial.md)
* Confluence: 5.0 t/m 5.10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

## <a name="installation"></a>Installatie

Voer de volgende stappen uit om de invoegtoepassing te installeren:

1. Meld u als beheerder aan bij uw Jira- of Confluence-instantie.

2. Ga naar de beheerconsole van Jira/Confluence en selecteer **Add-ons**.

3. Download in het Microsoft Downloadcentrum de [Microsoft-invoegtoepassing voor eenmalige aanmelding op basis van SAML voor Jira](https://www.microsoft.com/download/details.aspx?id=56506)/ [Microsoft-invoegtoepassing voor eenmalige aanmelding op basis van SAML voor Confluence](https://www.microsoft.com/download/details.aspx?id=56503).

   De juiste versie van de invoegtoepassing wordt weergegeven in de zoekresultaten.

4. Selecteer de invoegtoepassing. Deze wordt dan door de Universal Plug-in Manager (UPM) geïnstalleerd.

Nadat de invoegtoepassing is geïnstalleerd, wordt deze weergegeven in de sectie **User Installed Add-ons** van **Manage add-ons**.

## <a name="plug-in-configuration"></a>Configuratie van de invoegtoepassing

Voordat u de invoegtoepassing gaat gebruiken, moet u deze configureren. Selecteer de invoegtoepassing, selecteer de knop **Configure** en geef de configuratiedetails op.

Op de volgende afbeelding ziet u het configuratiescherm in zowel Jira als Confluence:

![Configuratiescherm voor de invoegtoepassing](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **Metadata URL**: De URL voor het ophalen van federatieve metagegevens uit Azure AD.

* **Identifiers**: De URL die door Azure AD wordt gebruikt om de bron van de aanvraag te valideren. Deze wordt toegewezen aan het element **Identifier** in Azure AD. Deze URL wordt door de invoegtoepassing automatisch afgeleid als https:// *\<domain:port>* /.

* **Antwoord-URL**: De antwoord-URL in uw id-provider (IdP) die de SAML-aanmelding initieert. Deze wordt toegewezen aan het element **Reply URL** in Azure AD. Deze URL wordt door de invoegtoepassing automatisch afgeleid als https:// *\<domain:port>* /plugins/servlet/saml/auth.

* **Sign On URL**: De aanmeld-URL in uw IdP die de SAML-aanmelding initieert. Deze wordt toegewezen aan het element **Sign On** in Azure AD. Deze URL wordt door de invoegtoepassing automatisch afgeleid als https:// *\<domain:port>* /plugins/servlet/saml/auth.

* **IdP Entity ID**: De entiteit-id die uw IdP gebruikt. Dit vak wordt ingevuld wanneer de metagegevens-URL is omgezet.

* **Login URL**: De aanmeldings-URL van uw IdP. Dit vak wordt ingevuld vanuit Azure AD wanneer de metagegevens-URL is omgezet.

* **Logout URL**: De afmeldings-URL van uw IdP. Dit vak wordt ingevuld vanuit Azure AD wanneer de metagegevens-URL is omgezet.

* **X.509 Certificate**: Het X.509-certificaat van uw IdP. Dit vak wordt ingevuld vanuit Azure AD wanneer de metagegevens-URL is omgezet.

* **Login Button Name**: De naam van de aanmeldingsknop die uw organisatie gebruikers wil laten zien op de aanmeldingspagina.

* **SAML User ID Locations**: De locatie waar de gebruikers-id van Jira of Confluence wordt verwacht in het SAML-antwoord. Deze kan zich in **NameID** of in een aangepaste kenmerknaam bevinden.

* **Attribute Name**: De naam van het kenmerk waarin de gebruikers-id wordt verwacht.

* **Enable Home Realm Discovery**: De selectie die moet worden gedaan als het bedrijf gebruikmaakt van aanmelding op basis van Active Directory Federation Services (AD FS).

* **Domain Name**: De domeinnaam als aanmelding op basis van AD FS is.

* **Enable Single Signout**: De selectie die moet worden gedaan als u zich wilt afmelden bij Azure AD wanneer een gebruiker zich afmeldt bij Jira of Confluence.

## <a name="troubleshooting"></a>Problemen oplossen

* **U krijgt meerdere certificaatfouten**: Meld u aan bij Azure AD en verwijder de meerdere certificaten die beschikbaar zijn voor de app. Zorg ervoor dat er slechts één certificaat aanwezig is.

* **Een certificaat verloopt binnenkort in Azure AD**: Invoegtoepassingen zorgen voor automatische rollover van het certificaat. Wanneer een certificaat bijna verloopt, moet een nieuw certificaat worden gemarkeerd als actief en moeten ongebruikte certificaten worden verwijderd. Wanneer een gebruiker zich probeert aan te melden bij Jira in dit scenario, wordt het nieuwe certificaat door de invoegtoepassing opgehaald en opgeslagen.

* **U wilt WebSudo uitschakelen (de beveiligde beheerdersessie uitschakelen)** :

  * Voor Jira zijn beveiligde beheerdersessies (dat wil zeggen, wachtwoord bevestigen vóór toegang tot beheerdersfuncties) standaard ingeschakeld. Als u deze mogelijkheid in uw Jira-exemplaar wilt verwijderen, geeft u de volgende regel op in het bestand jira-config.properties: `jira.websudo.is.disabled = true`

  * Volg voor Confluence de stappen op de ondersteuningssite van [Confluence](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Velden die moeten worden ingevuld door de metagegevens-URL, worden niet gevuld**:

  * Controleer of de URL juist is. Controleer of u de juiste tenant en app-id hebt toegewezen.

  * Voer de URL in een browser in en controleer of u de federatieve metagegevens-XML ontvangt.

* **Er is een interne serverfout**: Bekijk de logboeken in de logboekmap van de installatie. Als u de foutmelding krijgt wanneer de gebruiker zich probeert aan te melden met behulp van Azure AD SSO, kunt u de logboeken delen met het ondersteuningsteam.

* **De fout 'Gebruikers-id is niet gevonden' wordt weergegeven wanneer de gebruiker zich probeert aan te melden**: Maak de gebruikers-id in Jira of Confluence.

* **Er is een foutmelding 'App niet gevonden' in Azure AD**: Controleer of de juiste URL is toegewezen aan de app in Azure AD.

* **U hebt ondersteuning nodig**: Neem contact op met het [Azure AD-team voor integratie van eenmalige aanmelding](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Het team reageert binnen 24-48 werkuren.

  U kunt ook een ondersteuningsticket bij Microsoft indienen via het Azure Portal-kanaal.

## <a name="plug-in-faq"></a>Veelgestelde vragen over de invoegtoepassing

Raadpleeg de onderstaande veelgestelde vragen als u een vraag hebt over deze invoegtoepassing.

### <a name="what-does-the-plug-in-do"></a>Wat doet de invoegtoepassing?

De invoegtoepassing biedt ondersteuning voor eenmalige aanmelding voor on-premises software van Atlassian Jira (waaronder Jira Core, Jira Software en Jira Service Desk) en Confluence. De invoegtoepassing werkt met Azure Active Directory (Azure AD) als id-provider (IdP).

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Welke Atlassian-producten werken met de invoegtoepassing?

De invoegtoepassing werkt met on-premises versies van Jira en Confluence.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>Werkt de invoegtoepassing in cloudversies?

Nee. De invoegtoepassing biedt alleen ondersteuning voor on-premises versies van Jira en Confluence.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Voor welke versies van Jira en Confluence biedt de invoegtoepassing ondersteuning?

De invoegtoepassing biedt ondersteuning voor de volgende versies:

* Jira Core en Software: 6.0 – 7.12
* Jira Service Desk: 3.0.0 tot en met 3.5.0
* JIRA ondersteunt ook 5.2. Klik voor meer informatie op [Microsoft Azure Active Directory-eenmalige aanmelding voor JIRA 5.2](./jira52microsoft-tutorial.md)
* Confluence: 5.0 t/m 5.10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>Is de invoegtoepassing gratis of betaald?

Het is een gratis invoegtoepassing.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Moet ik Jira of Confluence opnieuw starten nadat ik de invoegtoepassing heb geïmplementeerd?

Opnieuw opstarten is niet nodig. U kunt de invoegtoepassing onmiddellijk gaan gebruiken.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Hoe kan ik ondersteuning voor de invoegtoepassing krijgen?

U kunt contact opnemen met het [Azure AD-team voor integratie van eenmalige aanmelding](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) voor alle ondersteuning die nodig is voor deze invoegtoepassing. Het team reageert binnen 24-48 werkuren.

U kunt ook een ondersteuningsticket bij Microsoft indienen via het Azure Portal-kanaal.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Werkt de invoegtoepassing op een Mac- of Ubuntu-installatie van Jira en Confluence?

We hebben de invoegtoepassing alleen getest op 64-bits Windows Server-installaties van Jira en Confluence.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Werkt de invoegtoepassing met een andere id dan Azure AD?

Nee. De invoegtoepassing werkt alleen met Azure AD.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Welke versie van SAML werkt met de invoegtoepassing?

De invoegtoepassing werkt met SAML 2.0.

### <a name="does-the-plug-in-do-user-provisioning"></a>Biedt de invoegtoepassing gebruikersinrichting?

Nee. De invoegtoepassing biedt alleen op SAML 2.0 gebaseerde eenmalige aanmelding. De gebruiker moet in de toepassing worden ingericht voordat eenmalige aanmelding kan worden uitgevoerd.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Biedt de invoegtoepassing ondersteuning voor clusterversies van Jira en Confluence?

Nee. De invoegtoepassing werkt met on-premises versies van Jira en Confluence.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Werkt de invoegtoepassing met HTTP-versies van Jira en Confluence?

Nee. De invoegtoepassing werkt alleen met installaties met HTTPS-functionaliteit.