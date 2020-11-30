---
title: Azure AD beveiligde hybride toegang met F5 | Microsoft Docs
description: F5 BIG-IP Access Policy Manager en Azure Active Directory-integratie voor beveiligde hybride toegang
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e011417b936ed83b4658e6dad25bf8e8ee88aed
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96317896"
---
# <a name="f5-big-ip-access-policy-manager-and-azure-active-directory-integration-for-secure-hybrid-access"></a>F5 BIG-IP Access Policy Manager en Azure Active Directory-integratie voor beveiligde hybride toegang

De toename van mobiliteit en zich ontwikkelende bedreigingen breidt een extra controle uit over de toegang tot en het beheer van resources, waarbij [geen vertrouwens relatie van nul](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/) en midden van alle moderniserings Programma's wordt geplaatst.
Bij micro soft en F5 realiseren we dat deze digitale trans formatie doorgaans een traject voor meerdere jaren is voor elk bedrijf, waardoor er mogelijk essentiële bronnen worden weer gegeven tot u dat hebt gerealiseerd. De Genesis achter F5 BIG-IP en Azure Active Directory Secure Hybrid Access (SHA) is niet alleen gericht op het verbeteren van externe toegang tot on-premises toepassingen, maar ook op het versterken van de algemene beveiligings postuur van deze kwets bare Services.

Onderzoek schat dat 60-80% van on-premises toepassingen verouderd is, of met andere woorden die niet rechtstreeks met Azure Active Directory (AD) kunnen worden geïntegreerd. Hetzelfde onderzoek heeft ook geconstateerd dat een groot deel van deze systemen wordt uitgevoerd op Down Level versies van SAP, Oracle, SAGE en andere bekende werk belastingen die essentiële services bieden.

SHA behandelt deze blinde positie door organisaties in staat te stellen hun F5-investeringen te blijven gebruiken voor superieure netwerk-en toepassings levering. In combi natie met Azure AD wordt de heterogene toepassing liggend gebrugd met het moderne identiteits beheergebied.

Als u Azure AD verificatie vooraf verifieert voor in BIG-IP gepubliceerde Services, hebt u een groot aantal voor delen:

- Wacht woord-minder authenticatie via [Windows hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-overview), [MS Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install), [Fast Identity online (Fido)-sleutels](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key)en [verificatie op basis van een certificaat](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started)

- [Voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) en [multi-factor Authentication (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) preventieve

- [Identiteits beveiliging](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection#:~:text=Identity%20Protection%20is%20a%20tool%20that%20allows%20organizations,detection%20data%20to%20third-party%20utilities%20for%20further%20analysis) -adaptief beheer via profile ring van gebruikers-en sessie risico

- [Gedetecteerde referentie detectie](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks)

- [Selfservice voor wachtwoordherstel (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-sspr)

- [Samen werking van partners](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users) -recht beheer voor de regeling van toegang tot gasten

- [Cloud app Security (CASB)](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) -voor het volt ooien van app-detectie en-beheer

- Bedreigings controle- [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) voor Advanced Threat Analytics

- De [Azure AD-Portal](https://azure.microsoft.com/features/azure-portal/) : een enkel besturings vlak voor het beheren van identiteiten en toegang

## <a name="scenario-description"></a>Scenariobeschrijving

Als Application Delivery controller (ADC) en SSL-VPN biedt een BIG-IP-systeem lokale en externe toegang tot alle typen services, waaronder:

- Moderne en verouderde webtoepassingen

- Niet-webtoepassingen

- REST-en SOAP Web API-services

Met de lokale Traffic Manager (LTM) kunt u veilig publiceren van services via reverse-proxy functionaliteit. Terzelfder tijd breidt een geavanceerd toegangs beleid (APM) het BIG-IP-adres uit met een uitgebreidere set mogelijkheden, waardoor Federatie en eenmalige aanmelding (SSO) mogelijk zijn.

De integratie is gebaseerd op een standaard Federatie vertrouwensrelatie tussen de APM en Azure AD, gemeen schappelijk voor de meeste SHA-use cases die het [scenario SSL-VPN](f5-aad-password-less-vpn.md)bevatten. Security Assertion Markup Language (SAML), OAuth-en open ID Connect-resources (OIDC) zijn geen uitzonde ringen, aangezien deze ook kunnen worden beveiligd voor externe toegang. Er kunnen ook scenario's zijn waarbij een BIG-IP een onderdrukkings punt wordt voor nul toegang tot alle services, met inbegrip van SaaS-apps.

De mogelijkheid van een BIG-IP-integratie met Azure AD is dat de protocol overgang wordt vereist voor het beveiligen van verouderde of niet-Azure AD-geïntegreerde services met moderne besturings elementen, zoals [wacht woord-less verificatie](https://www.microsoft.com/security/business/identity/passwordless) en [voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/overview). In dit scenario blijft een BIG-IP-adres voldoen aan de rol van de reverse proxy, terwijl vooraf verificatie en autorisatie wordt afgeleverd aan Azure AD, op basis van de service.

Stap 1-4 in het diagram illustreert de front-end uitwisseling vooraf voor verificatie tussen een gebruiker, een BIG-IP en Azure AD, in een service provider geïnitieerde stroom. In stap 5-6 worden de volgende APM-sessie verrijkingen en SSO-services weer gegeven voor afzonderlijke back-endservers.

![Afbeelding toont de architectuur op hoog niveau](./media/f5-aad-integration/integration-flow-diagram.png)

| Stap | Beschrijving |
|:------|:-----------|
| 1. | Gebruiker selecteert een toepassings pictogram in de portal, waarbij de URL wordt omgezet naar het SAML-SP (BIG-IP) |
| 2. | De BIG-IP leidt de gebruiker naar SAML IDP (Azure AD) voor verificatie vooraf|
| 3. | Azure AD verwerkt CA-beleid en [sessie besturings elementen](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-session) voor autorisatie|
| 4. | Gebruiker leidt terug naar een BIG-IP-adres met de SAML-claims die zijn uitgegeven door Azure AD |
| 5. | BIG-IP vraagt alle extra sessie gegevens op die moeten worden opgenomen in [SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) en [op rollen gebaseerd toegangs beheer (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) voor de gepubliceerde service |
| 6. | BIG-IP stuurt de client aanvraag door naar de back-end-service

## <a name="user-experience"></a>Gebruikerservaring

Of een directe werk nemer, gelieerde onderneming of consument, de meeste gebruikers bekend zijn met de Office 365-aanmeld ervaring, zodat de toegang tot BIG-IP-services via SHA grotendeels vertrouwd blijft.

Gebruikers vinden nu hun BIG-IP-services die zijn geconsolideerd in het  [MyApps](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) of [O365 launchpad](https://o365pp.blob.core.windows.net/media/Resources/Microsoft%20365%20Business/Launchpad%20Overview_for%20Partners_10292019.pdf) samen met selfservice mogelijkheden voor een uitgebreidere set services, ongeacht het type apparaat of locatie. Gebruikers kunnen nog steeds rechtstreeks toegang krijgen tot gepubliceerde Services via de BIG-IPs eigen WebTop-Portal, indien gewenst. Wanneer u zich afmeldt, zorgt SHA ervoor dat de sessie van een gebruiker wordt beëindigd aan beide kanten, het BIG-IP-adres en Azure AD, waardoor Services volledig beschermd blijven tegen onbevoegde toegang.  

De beschik bare scherm opnamen zijn afkomstig uit de Azure AD-App-Portal waarmee gebruikers veilig toegang hebben tot hun BIG-IP-gepubliceerde Services en voor het beheren van hun account eigenschappen.  

![De scherm opname toont de Woodgrove MyApps galerie](media/f5-aad-integration/woodgrove-app-gallery.png)

![De scherm opname toont de myaccounts-pagina van Woodgrove-service](media/f5-aad-integration/woodgrove-myaccount.png)

## <a name="insights-and-analytics"></a>Inzichten en analyse

De rol van een BIG IP-adres is van cruciaal belang voor elk bedrijf, zodat geïmplementeerde BIG-IP-instanties moeten worden bewaakt om ervoor te zorgen dat gepubliceerde Services Maxi maal beschikbaar zijn, zowel op een SHA-niveau als in de praktijk.

Er zijn verschillende opties voor het vastleggen van gebeurtenissen lokaal of op afstand via een SIEM-oplossing, waardoor Security Information and Event Management de opslag en verwerking van telemetrie niet mogelijk is. Een zeer efficiënte oplossing voor het bewaken van Azure AD en SHA-specifieke activiteiten is het gebruik van [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) en [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview), samen met de volgende aanbieding:

- Gedetailleerd overzicht van uw organisatie, mogelijk in meerdere clouds en on-premises locaties, inclusief BIG-IP-infra structuur

- Eén beheergebied biedt gecombineerde weer gave van alle signalen, waarbij wordt voor komen dat u afhankelijk bent van complexe en verschillende hulpprogram ma's

![De afbeelding toont de bewakings stroom](media/f5-aad-integration/azure-sentinel.png)

## <a name="prerequisites"></a>Vereisten

Voor het integreren van F5 BIG-IP met Azure AD voor SHA gelden de volgende vereisten:

- Een F5 BIG-IP-exemplaar dat wordt uitgevoerd op een van de volgende platforms:

  - Fysiek apparaat

  - Virtuele Hyper Visor-editie, zoals Microsoft Hyper-V, VMware ESXi, Linux KVM en Citrix Hyper Visor

  - Cloud Virtual Edition zoals Azure, VMware, KVM, Community xen, MS Hyper-V, AWS, Open stack en Google Cloud

    De werkelijke locatie van een BIG-IP-exemplaar kan on-premises of een ondersteund Cloud platform zijn, inclusief Azure, mits het verbinding heeft met internet, resources die worden gepubliceerd en andere vereiste services, zoals Active Directory.  

- Met een van de volgende opties kunt u een actieve F5-licentie voor een BIG-IP-adres.

   - F5 BIG-IP® Best-bundel (of)

   - F5 BIG-IP Access Policy Manager™ zelfstandige licentie

   - F5 BIG-IP Access Policy Manager™ (APM)-invoeg toepassing voor een bestaande BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM)

   - Een 90-daagse [proef licentie](https://www.f5.com/trial/big-ip-trial.php) voor het™ van beleids beheer (APM) voor een Big-IP-toegangs beleid

- Azure AD-licentie verlening via een van de volgende opties:

   - Een gratis Azure AD- [abonnement](https://docs.microsoft.com/windows/client-management/mdm/register-your-free-azure-active-directory-subscription#:~:text=%20Register%20your%20free%20Azure%20Active%20Directory%20subscription,will%20take%20you%20to%20the%20Azure...%20More%20) biedt de minimale kern vereisten voor het implementeren van SHA met wacht woord-less-verificatie

   - Een [Premium-abonnement](https://azure.microsoft.com/pricing/details/active-directory/) biedt alle extra waarde, die wordt beschreven in het voor woord, waaronder [voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/overview), [MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)en [identiteits beveiliging](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)

Er is geen eerdere ervaring of F5 BIG-IP-kennis nodig voor het implementeren van SHA, maar we raden u aan de terminologie van F5 BIG-IP te verkennen. F5's Rich [Knowledge Base](https://www.f5.com/services/resources/glossary) is ook een goede plaats om te beginnen met het bouwen van Big-IP-kennis.

## <a name="deployment-scenarios"></a>Implementatiescenario's

De volgende zelf studies bieden gedetailleerde richt lijnen voor het implementeren van een aantal van de meest voorkomende patronen voor BIG-IP en Azure AD SHA:

- [F5 BIG-IP in azure-implementatie-instructies](f5-bigip-deployment-guide.md)

- [F5 BIG-IP APM en Azure AD SSO to Kerberos-toepassingen](https://docs.microsoft.com/azure/active-directory/saas-apps/kerbf5-tutorial#configure-f5-single-sign-on-for-kerberos-application)

- [F5 BIG-IP APM en Azure AD SSO naar toepassingen die op Koptekst zijn gebaseerd](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial#configure-f5-single-sign-on-for-header-based-application)

- [F5 BIG-IP SSL-VPN beveiligen met Azure AD SHA](f5-aad-password-less-vpn.md)

## <a name="additional-resources"></a>Aanvullende resources

- [Het einde van wacht woorden, wacht woordloos](https://www.microsoft.com/security/business/identity/passwordless)

- [Azure Active Directory beveiligde hybride toegang](https://azure.microsoft.com//services/active-directory/sso/secure-hybrid-access/)

- [Micro soft Zero Trust Framework om extern werk in te scha kelen](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Aan de slag met Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/?&OCID=AID2100131_SEM_XfknpgAAAHoVMTvh:20200922160358:s&msclkid=5e0e022409fc1c94dab85d4e6f4710e3&ef_id=XfknpgAAAHoVMTvh:20200922160358:s&dclid=CJnX6vHU_esCFUq-ZAod1iQF6A)

## <a name="next-steps"></a>Volgende stappen

Overweeg het uitvoeren van een SHA-proef concept (haalbaarheids test) met behulp van uw bestaande BIG-IP-infra structuur of door een proef exemplaar te implementeren. [Het implementeren van een virtuele Edition-VM (Big-IP) in azure](f5-bigip-deployment-guide.md) duurt ongeveer 30 minuten, op welk moment u het volgende hebt:

- Een volledig beveiligd platform voor het model leren van een SHA-concept

- Een pre-productie-exemplaar, volledig beveiligd platform voor het testen van nieuwe BIG-IP-systeem updates en hotfixes

Op hetzelfde moment moet u een of twee toepassingen identificeren die kunnen worden bedoeld voor publicatie via het grote IP-adres en beveiligen met SHA.  

We raden u aan om te beginnen met een toepassing die nog niet is gepubliceerd via een BIG-IP-adres, zodat er geen mogelijke onderbreking van de productie Services wordt gemaakt. Aan de hand van de richt lijnen in dit artikel krijgt u kennis met de algemene procedure voor het maken van de verschillende BIG-IP-configuratie objecten en het instellen van SHA. Als u klaar bent, kunt u hetzelfde doen met andere nieuwe services, plus ook voldoende kennis om bestaande, gepubliceerde BIG-IP-services te converteren naar SHA met minimale inspanning.

De onderstaande interactieve hand leiding doorloopt de procedure op hoog niveau voor het implementeren van SHA en het bekijken van de ervaring van de eind gebruiker.

[![De afbeelding toont interactieve hand leiding](media/f5-aad-integration/interactive-guide.png)](https://aka.ms/Secure-Hybrid-Access-F5-Interactive-Guide)
