---
title: Aangepaste domeinen in azure AD-toepassingsproxy
description: Aangepaste domeinen configureren en beheren in azure AD-toepassingsproxy.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: bef120e754c84798b2d1b48f4f00fbb8f5fb3c1d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997610"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Aangepaste domeinen configureren met Azure AD-toepassingsproxy

Wanneer u een toepassing publiceert via Azure Active Directory-toepassingsproxy, maakt u een externe URL voor uw gebruikers. Met deze URL wordt het standaard domein *yourtenant.msappproxy.net* opgehaald. Als u bijvoorbeeld een app met de naam *uitgaven* in uw Tenant met de naam *Contoso* publiceert, is de externe URL *https://expenses-contoso.msappproxy.net*. Als u uw eigen domein naam wilt gebruiken in plaats van *msappproxy.net*, kunt u een aangepast domein voor uw toepassing configureren. 

## <a name="benefits-of-custom-domains"></a>Voor delen van aangepaste domeinen

Het is een goed idee om waar mogelijk aangepaste domeinen voor uw apps in te stellen. Enkele redenen voor het gebruik van aangepaste domeinen zijn:

- Koppelingen tussen apps werken zelfs buiten het bedrijfs netwerk. Zonder een aangepast domein geldt dat als uw app hardcoded interne koppelingen naar doelen buiten de toepassings proxy heeft, de koppelingen niet extern kunnen worden omgezet. Als uw interne en externe Url's hetzelfde zijn, kunt u dit probleem vermijden. Als u geen aangepaste domeinen kunt gebruiken, raadpleegt u [hardcoded koppelingen omleiden voor apps die zijn gepubliceerd met Azure AD-toepassingsproxy](./application-proxy-configure-hard-coded-link-translation.md) voor andere manieren om dit probleem op te lossen. 
  
- Uw gebruikers hebben een gemakkelijke ervaring, omdat ze naar de app kunnen gaan met dezelfde URL binnen of buiten uw netwerk. Ze hoeven geen andere interne en externe Url's te leren of hun huidige locatie bij te houden. 

- U kunt uw huis stijl beheren en de gewenste Url's maken. Een aangepast domein kan helpen het vertrouwen van uw gebruikers te bouwen, omdat gebruikers een vertrouwde naam zien en gebruiken in plaats van *msappproxy.net*.

- Sommige configuraties werken alleen met aangepaste domeinen. U hebt bijvoorbeeld aangepaste domeinen nodig voor apps die gebruikmaken van Security Assertion Markup Language (SAML), bijvoorbeeld wanneer u Active Directory Federation Services (AD FS) gebruikt, maar geen gebruik kunt maken van WS-Federation. Zie [werken met claim bewuste apps in Application proxy](application-proxy-configure-for-claims-aware-applications.md)voor meer informatie. 

Als u niet in staat bent om de interne en externe Url's overeenkomen, is het niet zo belang rijk dat u aangepaste domeinen gebruikt, maar u kunt nog steeds profiteren van de andere voor delen. 

## <a name="dns-configuration-options"></a>Opties voor DNS-configuratie

Er zijn verschillende opties voor het instellen van uw DNS-configuratie, afhankelijk van uw vereisten:

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>Dezelfde interne en externe URL, verschillend intern en extern gedrag 

Als u niet wilt dat uw interne gebruikers worden omgeleid via de toepassings proxy, kunt u een *Split-DNS-server* instellen. Een gesplitste DNS-infra structuur stuurt interne hosts naar een interne domein naam server en externe hosts naar een externe domein naam server, voor naam omzetting. 

![Split-brain DNS](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>Verschillende interne en externe Url's 

Als de interne en externe Url's verschillend zijn, hoeft u geen split-gedrag te configureren, omdat gebruikers routering wordt bepaald door de URL. In dit geval wijzigt u alleen de externe DNS en stuurt u de externe URL naar het eind punt van de toepassings proxy. 

Wanneer u een aangepast domein voor een externe URL selecteert, wordt in een informatie balk de CNAME-vermelding weer gegeven die u moet toevoegen aan de externe DNS-provider. U kunt deze informatie altijd weer geven door naar de pagina **toepassings proxy** van de app te gaan.

## <a name="set-up-and-use-custom-domains"></a>Aangepaste domeinen instellen en gebruiken

Als u een on-premises app wilt configureren voor het gebruik van een aangepast domein, hebt u een geverifieerd Azure Active Directory aangepast domein, een PFX-certificaat voor het aangepaste domein en een on-premises app die moet worden geconfigureerd. 

### <a name="create-and-verify-a-custom-domain"></a>Een aangepast domein maken en verifiëren

Een aangepast domein maken en verifiëren:

1. Selecteer in Azure Active Directory **aangepaste domein namen** in het linkernavigatievenster en selecteer vervolgens **aangepast domein toevoegen**. 
1. Voer de naam van uw aangepaste domein in en selecteer **domein toevoegen**. 
1. Kopieer op de pagina domein de TXT-record gegevens voor uw domein. 
1. Ga naar uw domein registratie service en maak een nieuwe TXT-record voor uw domein op basis van de gekopieerde DNS-gegevens.
1. Nadat u het domein hebt geregistreerd, selecteert u op de pagina van het domein in Azure Active Directory **verifiëren**. Zodra de domein status is **geverifieerd**, kunt u het domein gebruiken voor al uw Azure AD-configuraties, inclusief toepassings proxy. 

Zie [uw aangepaste domein naam toevoegen met behulp van de Azure Active Directory Portal](../fundamentals/add-custom-domain.md)voor meer gedetailleerde instructies.

### <a name="configure-an-app-to-use-a-custom-domain"></a>Een app configureren voor het gebruik van een aangepast domein

Uw app publiceren via een toepassings proxy met een aangepast domein:

1. Selecteer voor een nieuwe app in Azure Active Directory **Enter prise-toepassingen** in het linkernavigatievenster. Selecteer **Nieuwe toepassing**. Selecteer in de sectie **On-premises toepassingen** de optie **Een on-premises toepassing toevoegen**. 
   
   Voor een app die al in **bedrijfs toepassingen** is, selecteert u deze in de lijst en selecteert u vervolgens **toepassings proxy** in de linkernavigatiebalk. 

2. Voer op de pagina instellingen van toepassings proxy een **naam** in als u uw eigen on-premises toepassing wilt toevoegen.

3.  Voer in het veld **interne URL** de interne URL voor uw app in.
   
4. In het veld **externe URL** gaat u naar de lijst en selecteert u het aangepaste domein dat u wilt gebruiken.
   
5. Selecteer **Toevoegen**.
   
   ![Aangepast domein selecteren](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. Als het domein al een certificaat heeft, wordt in het veld **certificaat** de gegevens van het certificaat weer gegeven. Als dat niet het geval is, selecteert u het veld **certificaat** .
   
   ![Klik om een certificaat te uploaden](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. Blader op de pagina **SSL-certificaat** naar het pfx-certificaat bestand en selecteer dit. Voer het wacht woord voor het certificaat in en selecteer **certificaat uploaden**. Zie de sectie [certificaten voor aangepaste domeinen](#certificates-for-custom-domains) voor meer informatie over certificaten. Als het certificaat ongeldig is of als er een probleem is met het wacht woord, wordt een fout bericht weer gegeven. De [Veelgestelde vragen over Application proxy](application-proxy-faq.md#application-configuration) bevat enkele stappen voor probleem oplossing die u kunt proberen.
   
   ![Certificaat uploaden](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > Een aangepast domein heeft het certificaat alleen eenmaal geüpload. Daarna wordt het geüploade certificaat automatisch toegepast wanneer u het aangepaste domein gebruikt voor andere apps.
   
8. Als u een certificaat hebt toegevoegd, selecteert u op de pagina **toepassings proxy** **Opslaan**. 
   
9. In de informatie balk op de pagina **toepassings proxy** noteert u de CNAME-vermelding die u moet toevoegen aan uw DNS-zone. 
   
   ![CNAME DNS-vermelding toevoegen](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Volg de instructies in [DNS-records en-record sets beheren met behulp van de Azure Portal](../../dns/dns-operations-recordsets-portal.md) om een DNS-record toe te voegen waarmee de nieuwe externe URL wordt omgeleid naar het domein *msappproxy.net* .
   
11. Als u wilt controleren of de DNS-record correct is geconfigureerd, gebruikt u de [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) opdracht om te bevestigen dat uw externe URL bereikbaar is en het domein *msapproxy.net* wordt weer gegeven als een alias.

Uw toepassing is nu ingesteld voor het gebruik van het aangepaste domein. Zorg ervoor dat u gebruikers toewijst aan uw toepassing voordat u deze test of uitgeeft. 

Als u het domein voor een app wilt wijzigen, selecteert u een ander domein in de vervolg keuzelijst in **externe URL** op de pagina **toepassings proxy** van de app. Upload een certificaat voor het bijgewerkte domein, indien nodig, en werk de DNS-record bij. Als u het aangepaste domein dat u wilt in de vervolg keuzelijst in de **externe URL** niet ziet, wordt het mogelijk niet gecontroleerd.

Zie [zelf studie: een on-premises toepassing toevoegen voor externe toegang via toepassings proxy in azure Active Directory](application-proxy-add-on-premises-application.md)voor meer gedetailleerde instructies voor de toepassings proxy.

## <a name="certificates-for-custom-domains"></a>Certificaten voor aangepaste domeinen

Met een certificaat maakt u de beveiligde TLS-verbinding voor uw aangepaste domein. 

### <a name="certificate-formats"></a>Certificaat indelingen

U moet een PFX-certificaat gebruiken om ervoor te zorgen dat alle vereiste tussenliggende certificaten zijn opgenomen. Het certificaat moet de persoonlijke sleutel bevatten.

De meest voorkomende methoden voor certificaat handtekeningen worden ondersteund, zoals een alternatieve naam voor het onderwerp (SAN). 

U kunt Joker certificaten gebruiken zolang het Joker teken overeenkomt met de externe URL. U moet joker tekens gebruiken voor [toepassingen met Joker](application-proxy-wildcard.md)tekens. Als u het certificaat wilt gebruiken om ook toegang te krijgen tot subdomeinen, moet u de joker tekens subdomein toevoegen als alternatieve namen voor het onderwerp in hetzelfde certificaat. Een certificaat voor *\* . Adventure-Works.com* werkt bijvoorbeeld niet voor *\* . apps.Adventure-Works.com* tenzij u *\* . apps.Adventure-Works.com* toevoegt als alternatieve naam voor het onderwerp. 

U kunt certificaten gebruiken die zijn uitgegeven door uw eigen open bare-sleutel infrastructuur (PKI) als de certificaat keten is geïnstalleerd op uw client apparaten. InTune kan deze certificaten implementeren op beheerde apparaten. Voor niet-beheerde apparaten moet u deze certificaten hand matig installeren. 

Het is niet raadzaam om een persoonlijke basis certificerings instantie te gebruiken omdat de CA van de particuliere basis ook moet worden gepusht naar client machines, die veel problemen kunnen veroorzaken.

### <a name="certificate-management"></a>Certificaatbeheer

Alle certificaat beheer gaat via de afzonderlijke toepassings pagina's. Ga naar de **toepassings proxy** pagina van de toepassing om toegang te krijgen tot het veld **certificaat** .

Zodra een certificaat is geüpload voor een toepassing, wordt het ook automatisch toegepast op **nieuwe** apps die gebruikmaken van hetzelfde certificaat. U moet het certificaat opnieuw uploaden voor bestaande apps in uw Tenant.

Wanneer een certificaat verloopt, wordt een waarschuwing weer gegeven waarin u wordt gevraagd om een ander certificaat te uploaden. Als het certificaat wordt ingetrokken, zien uw gebruikers mogelijk een beveiligings waarschuwing bij het openen van de app. Als u het certificaat voor een app wilt bijwerken, gaat u naar de pagina **toepassings proxy** voor de app, selecteert u **certificaat** en uploadt u een nieuw certificaat. Als het oude certificaat niet door andere apps wordt gebruikt, wordt het automatisch verwijderd. 

## <a name="next-steps"></a>Volgende stappen

* [Schakel eenmalige aanmelding](application-proxy-configure-single-sign-on-with-kcd.md) in voor uw gepubliceerde apps met Azure AD-verificatie.
* [Voorwaardelijke toegang](../conditional-access/concept-conditional-access-cloud-apps.md) voor uw gepubliceerde Cloud-apps.