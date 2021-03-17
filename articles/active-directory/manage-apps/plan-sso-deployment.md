---
title: Een Azure Active Directory implementatie van eenmalige aanmelding plannen
description: Hulp bij het plannen, implementeren en beheren van eenmalige aanmelding in uw organisatie.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 06/10/2020
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: a7949324cd4ad0459aa5ba111037b2f580da9cf8
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103601709"
---
# <a name="plan-a-single-sign-on-deployment"></a>Een implementatie van eenmalige aanmelding plannen

Eenmalige aanmelding (SSO) betekent dat alle toepassingen en resources die een gebruiker nodig heeft, worden geopend door zich slechts één keer aan te melden met één gebruikers account. Met SSO hebben gebruikers toegang tot alle benodigde toepassingen zonder dat ze een tweede keer hoeven te verifiëren.

## <a name="benefits-of-sso"></a>Voor delen van SSO

Eenmalige aanmelding (SSO) voegt beveiligings-en gebruiks gemak toe wanneer gebruikers zich aanmelden bij toepassingen in Azure Active Directory (Azure AD). 

Veel organisaties zijn afhankelijk van SaaS-toepassingen (Software as a Service), zoals Microsoft 365, box en Sales Force, voor productiviteit van eind gebruikers. In het verleden moesten IT-mede werkers in elke SaaS-toepassing afzonderlijke gebruikers accounts maken en bijwerken, en moeten gebruikers een wacht woord onthouden voor elk.

Azure Marketplace heeft meer dan 3000 toepassingen met vooraf geïntegreerde SSO-verbindingen, waardoor deze eenvoudig kunnen worden geïntegreerd in uw Tenant.

## <a name="licensing"></a>Licentieverlening

- **Azure AD-licentie verlening** : SSO voor vooraf geïntegreerde SaaS-toepassingen is gratis. Voor het aantal objecten in uw directory en de functies die u wilt implementeren, zijn mogelijk extra licenties nodig. Zie [Azure Active Directory prijzen](https://azure.microsoft.com/pricing/details/active-directory/)voor een volledige lijst met licentie vereisten.
- **Toepassings licentie verlening** : u hebt de juiste licenties nodig voor uw SaaS-toepassingen om te voldoen aan de behoeften van uw bedrijf. Werk samen met de eigenaar van de toepassing om te bepalen of de gebruikers die zijn toegewezen aan de toepassing, over de juiste licenties beschikken voor hun rollen binnen de toepassing. Als Azure AD de automatische inrichting beheert op basis van rollen, moeten de rollen die zijn toegewezen in azure AD worden uitgelijnd met het aantal licenties dat eigendom is van de toepassing. Onjuist aantal licenties dat eigendom is van de toepassing kan leiden tot fouten tijdens het inrichten/bijwerken van een gebruiker.

## <a name="plan-your-sso-team"></a>Uw SSO-team plannen

- **De juiste belanghebbenden te benaderen** : wanneer technologie projecten mislukken, is dit doorgaans te wijten aan niet-overeenkomende verwachtingen wat betreft impact, resultaten en verantwoordelijkheden. Als u deze problemen wilt voor komen, moet [u ervoor zorgen dat u de juiste belanghebbenden gebruikt](../fundamentals/active-directory-deployment-plans.md) en dat belanghebbenden inzicht hebben in hun rollen.
- **Communicatie plannen** : communicatie is essentieel voor het slagen van een nieuwe service. Communiceer uw gebruikers proactief om te zien hoe hun ervaring verandert, wanneer deze wordt gewijzigd, en hoe u ondersteuning krijgt als er problemen optreden. Bekijk de opties voor de [manier waarop eind gebruikers toegang hebben tot hun SSO-toepassingen](end-user-experiences.md)en zorg ervoor dat uw communicaties overeenkomen met uw selectie. 

## <a name="plan-your-sso-protocol"></a>Uw SSO-protocol plannen

Een SSO-implementatie op basis van Federation-protocollen verbetert de beveiliging, betrouw baarheid en de ervaring van de eind gebruiker en is eenvoudiger te implementeren. Veel toepassingen zijn vooraf geïntegreerd in azure AD met [Stapsgewijze hand leidingen die beschikbaar](../saas-apps/tutorial-list.md)zijn. U kunt ze vinden op onze [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Gedetailleerde informatie over elke SSO-methode vindt u in het artikel [eenmalige aanmelding bij toepassingen in azure Active Directory](what-is-single-sign-on.md).

Er zijn twee primaire manieren waarop u uw gebruikers in staat kunt stellen om eenmalige aanmelding te maken voor uw apps:

- **Met federatieve eenmalige aanmelding** Azure AD verifieert de gebruiker aan de toepassing met behulp van hun Azure AD-account. Deze methode wordt ondersteund voor toepassingen die ondersteuning bieden voor protocollen zoals SAML 2,0, WS-Federation of OpenID Connect Connect, en is de uitgebreide modus van eenmalige aanmelding. U kunt het beste federatieve SSO met Azure AD gebruiken wanneer een toepassing dit ondersteunt, in plaats van SSO-en ADFS-aanmelding op basis van wacht woorden.

- **Wanneer gebruikers met een wacht woord** zijn aangemeld bij de toepassing met een gebruikers naam en wacht woord, krijgen ze de eerste keer toegang. Na de eerste aanmelding levert Azure AD de gebruikersnaam en het wachtwoord voor de toepassing. Bij eenmalige aanmelding op basis van een wachtwoord is beveiligde toepassingswachtwoordopslag en -herhaling mogelijk door middel van een webbrowserextensie of mobiele app. Deze optie maakt gebruik van het bestaande aanmeldings proces dat wordt meegeleverd met de toepassing, stelt een beheerder in staat om de wacht woorden te beheren en vereist niet dat de gebruiker het wacht woord kent.

### <a name="considerations-for-federation-based-sso"></a>Overwegingen voor SSO op basis van Federatie

- **OpenID Connect Connect en OAuth** gebruiken: als de toepassing waarmee u verbinding maakt, deze ondersteunt, gebruikt u de methode OIDC/OAuth 2,0 om uw SSO voor die toepassing in te scha kelen. Deze methode vereist minder configuratie en maakt gebruik van een uitgebreidere gebruikers ervaring. Zie [OAuth 2,0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect Connect 1,0](../develop/v2-protocols-oidc.md)en [Azure Active Directory hand leiding voor ontwikkel aars](../develop/index.yml)voor meer informatie.
- **Eindpunt configuraties voor op SAML gebaseerde SSO** : als u SAML gebruikt, hebben uw ontwikkel aars specifieke informatie nodig voordat de toepassing kan worden geconfigureerd. Zie [eenmalige aanmelding op basis van SAML configureren](configure-saml-single-sign-on.md)voor meer informatie.
- **Certificaat beheer voor op SAML gebaseerde SSO** : wanneer u federatieve SSO inschakelt voor uw toepassing, maakt Azure AD een certificaat dat standaard drie jaar geldig is. U kunt zo nodig de verval datum voor het certificaat aanpassen. Zorg ervoor dat er processen aanwezig zijn om certificaten te vernieuwen voordat deze verlopen. Zie voor meer informatie [Azure AD certificaten beheren](./manage-certificates-for-federated-single-sign-on.md).

### <a name="considerations-for-password-based-sso"></a>Overwegingen voor SSO op basis van wacht woorden

Voor het gebruik van Azure AD voor SSO op basis van een wacht woord moet een browser extensie worden geïmplementeerd waarmee de referenties veilig worden opgehaald en de aanmeldings formulieren worden ingevuld. Definieer een mechanisme voor het implementeren van de uitbrei ding op schaal met [ondersteunde browsers](../user-help/my-apps-portal-end-user-access.md). Een aantal opties:

- [groepsbeleid voor Internet Explorer](my-apps-deployment-plan.md)
- [Configuration Manager voor Internet Explorer](/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Door de gebruiker gestuurde down load en configuratie voor Chrome, Firefox, micro soft Edge of IE](../user-help/my-apps-portal-end-user-access.md)

Zie voor meer informatie [over het configureren van een eenmalige aanmelding met een wacht woord](./configure-password-single-sign-on-non-gallery-applications.md).

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Het vastleggen van meta gegevens van aanmeldings formulieren voor toepassingen die zich niet in de galerie bevinden

Micro soft ondersteunt het vastleggen van meta gegevens voor een webtoepassing voor wachtwoord kluizen (het vastleggen van de gebruikers naam en het wacht woord). Navigeer naar de aanmeldings-URL tijdens het configureren van de toepassing voor het vastleggen van de meta gegevens van formulieren. Vraag de eigenaar van de toepassing om de exacte aanmeldings-URL. Deze informatie wordt gebruikt tijdens het aanmeldings proces, waarbij de referenties van Azure AD worden toegewezen aan de toepassing tijdens het aanmelden.

Zie [Wat is toepassings toegang en eenmalige aanmelding met Azure AD?-SSO op basis van wacht woorden](./what-is-single-sign-on.md)voor meer informatie.

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Aanwijzingen dat meta gegevens in formulieren moeten worden opnieuw vastgelegd

Als de HTML-indeling van toepassingen wordt gewijzigd, moet u mogelijk de meta gegevens opnieuw vastleggen om deze aan te passen aan de wijzigingen. Algemene symptomen die aangeven dat de HTML-indeling wijzigingen bevat:

- Gebruikers die rapporteren dat op de toepassing wordt geklikt, krijgen de aanmeldings pagina
- Gebruikers die rapporteren dat de gebruikers naam of het wacht woord niet is gevuld

#### <a name="shared-accounts"></a>Gedeelde accounts

Vanuit het oogpunt van aanmelding zijn toepassingen met gedeelde accounts niet anders dan een galerie toepassing die gebruikmaakt van een wacht woord-SSO voor afzonderlijke gebruikers. Er zijn echter enkele extra stappen vereist bij het plannen en configureren van een toepassing die is bedoeld voor het gebruik van gedeelde accounts:

1. Werk samen met zakelijke gebruikers van toepassingen om het volgende te documenteren:
   1. Set gebruikers in de organisatie die de toepassing gaat gebruiken
   1. Bestaande set met referenties in de toepassing die is gekoppeld aan de set gebruikers 
1. Voor elke combi natie van gebruikersset en referenties maakt u een beveiligings groep in de Cloud of on-premises op basis van uw vereisten.
1. De gedeelde referenties opnieuw instellen. Zodra de app is geïmplementeerd in azure AD, hebben personen niet het wacht woord van het gedeelde account nodig. Omdat Azure AD het wacht woord opslaat, kunt u overwegen om het erg lang en ingewikkeld in te stellen. 
1. Configureer de automatische rollover van het wacht woord als de toepassing dit ondersteunt. Op die manier wordt niet zelfs de beheerder die de eerste Setup heeft gebruikt het wacht woord van het gedeelde account kennen. 

## <a name="plan-your-authentication-method"></a>Uw verificatie methode plannen

Het kiezen van de juiste verificatie methode is een cruciaal eerste beslissing bij het instellen van een Azure AD hybride identiteits oplossing. Implementeer de verificatie methode die is geconfigureerd met behulp van Azure AD Connect, waarmee ook gebruikers in de cloud worden ingericht.

Als u een verificatie methode wilt kiezen, moet u rekening houden met de tijd, de bestaande infra structuur, de complexiteit en de kosten van de implementatie van uw keuze. Deze factoren zijn voor elke organisatie verschillend en kunnen in de loop van de tijd veranderen. U moet de naam kiezen die het meest overeenkomt met uw specifieke scenario. Zie [de juiste verificatie methode kiezen voor uw Azure Active Directory hybride identiteits oplossing](../hybrid/choose-ad-authn.md)voor meer informatie.

## <a name="plan-your-security-and-governance"></a>De beveiliging en governance plannen 

De identiteit is de nieuwe primaire draai tabel voor de beveiliging en investeringen, omdat netwerk verbindingen steeds lager worden en minder effectief zijn met de explosie van BYOD-apparaten en Cloud toepassingen. 

### <a name="plan-access-reviews"></a>Toegangs beoordelingen plannen

Met [toegangs beoordelingen](../governance/create-access-review.md) kunnen organisaties efficiënt groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen beheren. U moet de gebruikers toegang regel matig controleren om ervoor te zorgen dat alleen de juiste personen toegang hebben.

Enkele van de belangrijkste onderwerpen om te plannen voor het instellen van toegangs beoordelingen zijn onder andere:

1. Een uitgebracht identificeren voor toegangs beoordelingen die aan uw zakelijke behoeften voldoet. Dit kan zo vaak als frequent per week, maandelijks, jaarlijks of als een on-demand oefening zijn.

1. Maak groepen die de controleurs van de app Access-rapporten vertegenwoordigen. U moet ervoor zorgen dat de belanghebbenden die bekend zijn met de app en de doel gebruikers en gebruiks kwesties deel nemen aan uw toegangs beoordelingen

1. Het volt ooien van een toegangs beoordeling omvat het gebruik van toegangs machtigingen voor de app voor gebruikers die niet langer toegang nodig hebben. Plannen voor het verwerken van potentiële ondersteunings aanvragen van geweigerde gebruikers. Een verwijderde gebruiker blijft gedurende 30 dagen in azure AD verwijderd gedurende welke tijd ze kunnen worden hersteld door een beheerder, indien nodig. Na dertig dagen wordt die gebruiker definitief verwijderd. Met behulp van de Azure Active Directory Portal kan een globale beheerder een onlangs verwijderde gebruiker permanent verwijderen voordat die periode wordt bereikt.

### <a name="plan-auditing"></a>Controle plannen

Azure AD biedt [rapporten die technische en zakelijke inzichten bevatten](../reports-monitoring/overview-reports.md). 

Er zijn zowel beveiligings-als activiteiten rapporten beschikbaar. Beveiligings rapporten tonen gebruikers die zijn gemarkeerd voor risico, en Risk ante aanmeldingen. Met activiteiten rapporten kunt u inzicht krijgen in het gedrag van gebruikers in uw organisatie door de aanmeldings activiteit te beschrijven en een controle spoor van alle aanmeldingen te bieden. U kunt rapporten gebruiken om Risico's te beheren, de productiviteit te verhogen en de naleving te controleren.

| Rapporttype | Toegangs beoordeling | Beveiligingsrapporten | Aanmeld rapport |
|-------------|---------------|------------------|----------------|
| Gebruiken om te controleren | Toepassings machtigingen en-gebruik. | Mogelijk gemanipuleerde accounts | Wie heeft toegang tot de toepassingen? |
| Mogelijke acties | Toegang controleren; machtigingen intrekken | Toegang intrekken; beveiliging opnieuw instellen afdwingen | Toegang intrekken |

Azure AD behoudt de meeste controle gegevens gedurende 30 dagen en maakt de gegevens beschikbaar via de Azure-beheer portal of via een API die u kunt downloaden naar uw analyse systemen.

### <a name="consider-using-microsoft-cloud-application-security"></a>Overweeg het gebruik van Microsoft Cloud toepassings beveiliging

Microsoft Cloud App Security (MCAS) is een CASB-oplossing (Cloud Access Security Broker). Het biedt u inzicht in uw Cloud-apps en-services, biedt geavanceerde analyses om Cyber dreigingen te identificeren en te bestrijden, en stelt u in staat om te bepalen hoe uw gegevens worden verplaatst.

Als u MCAS implementeert, kunt u:

- Gebruik Cloud Discovery om uw cloud omgeving en de Cloud-apps die uw organisatie gebruikt, toe te wijzen en te identificeren.
- Apps in uw Cloud goed keuren en ongedaan maken
- Gebruik eenvoudig te implementeren app-connectors die gebruikmaken van provider-Api's, voor zicht baarheid en beheer van apps waarmee u verbinding maakt
- Gebruik App-beheer voor voorwaardelijke toegang beveiliging om realtime zicht baarheid te krijgen en toegang en activiteiten in uw Cloud-apps te beheren
- Helpt u bij het instellen van doorlopend beheer door in te stellen, en het beleid doorlopend te verfijnen.

Sessie beheer van Microsoft Cloud-toepassing (MCAS) is beschikbaar voor elke browser op een belang rijk platform van elk besturings systeem. Mobiele apps en bureau blad-apps kunnen ook worden geblokkeerd of toegestaan. Als u systeem eigen integreert met Azure AD, kunnen apps die zijn geconfigureerd met SAML of Open-ID Connect-apps met eenmalige aanmelding in azure AD worden ondersteund, met inbegrip van [verschillende aanbevolen apps](/cloud-app-security/proxy-intro-aad).

Zie [Microsoft Cloud app Security-overzicht](/cloud-app-security/what-is-cloud-app-security)voor meer informatie over MCAS. MCAS is een abonnements service op basis van gebruikers. U kunt de licentie gegevens bekijken in het [MCAS-licentie gegevens blad](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO).

### <a name="use-conditional-access"></a>Voorwaardelijke toegang gebruiken

Met voorwaardelijke toegang kunt u op criteria gebaseerde beslissingen over toegangs beheer voor uw Cloud-apps automatiseren.

Beleid voor voorwaardelijke toegang wordt afgedwongen nadat de verificatie van de eerste factor is voltooid. Daarom is voorwaardelijke toegang niet bedoeld als een eerste verdedigings linie voor scenario's als DoS-aanvallen (Denial-of-service), maar kunnen signalen van deze gebeurtenissen gebruiken om de toegang te bepalen. Bijvoorbeeld: het risico niveau van de aanmelding, de locatie van de aanvraag, enzovoort kan worden gebruikt. Zie [overzicht](../conditional-access/plan-conditional-access.md) en het [implementatie plan](../conditional-access/plan-conditional-access.md)voor meer informatie over voorwaardelijke toegang.

## <a name="azure-sso-technical-requirements"></a>Technische vereisten voor Azure SSO

In de volgende sectie worden de vereisten beschreven voor het configureren van uw specifieke toepassing, met inbegrip van de benodigde omgeving (en), eind punten, claim toewijzing, vereiste kenmerken, certificaten en gebruikte protocollen. U hebt deze informatie nodig om SSO te configureren in de [Azure AD-Portal](https://portal.azure.com/).

### <a name="authentication-mechanism-details"></a>Details van verificatie mechanisme

Micro soft biedt een zelf studie voor alle vooraf geïntegreerde SaaS-apps en u hebt deze informatie niet nodig. Als de toepassing zich niet in onze toepassings Marketplace/-galerie bevindt, moet u mogelijk de volgende gegevens verzamelen:

- **Huidige ID-provider die de toepassing gebruikt voor SSO indien van toepassing** , bijvoorbeeld: AD FS, PingFederate, okta
- **Protocollen die worden ondersteund door de doel toepassing** , bijvoorbeeld SAML 2,0, OpenID Connect Connect, OAuth, Forms-Based auth, WS-inbrengen, WS-Trust
- Het **protocol wordt geconfigureerd met Azure AD** , zoals SAML 2,0 of 1,1, OpenID Connect Connect, OAuth, op formulieren gebaseerde, WS-Fed

### <a name="attribute-requirements"></a>Kenmerk vereisten

Er zijn vooraf geconfigureerde sets met kenmerken en kenmerktoewijzingen beschikbaar voor de koppeling tussen gebruikersobjecten van Azure AD en van de SaaS-app. Sommige apps beheren andere typen objecten, zoals groepen. Plan de toewijzing van gebruikers kenmerken van Azure AD aan uw toepassing en [Pas de standaard kenmerk toewijzingen](../app-provisioning/customize-application-attributes.md) aan op basis van de behoeften van uw bedrijf.

### <a name="certificate-requirements"></a>Certificaatvereisten

Het certificaat voor de toepassing moet up-to-date zijn, of er is een risico dat gebruikers geen toegang hebben tot de toepassing. De meeste SaaS-toepassings certificaten zijn 36 maanden geldig. U wijzigt de duur van het certificaat op de Blade van de toepassing. Zorg ervoor dat u de verval datum documenteert en weet hoe u het vernieuwen van het certificaat gaat beheren. 

Er zijn twee manieren om uw certificaten te beheren. 

- **Automatische rollover van certificaten** : micro soft ondersteunt de [rollover van de handtekening sleutel in azure AD](../develop/active-directory-signing-key-rollover.md). Hoewel dit de aanbevolen methode is voor het beheren van certificaten, wordt dit scenario niet door alle ISV'S ondersteund.

- **Hand matig bijwerken** : elke toepassing heeft een eigen certificaat dat verloopt op basis van hoe het is gedefinieerd. Voordat het certificaat van de toepassing verloopt, maakt u een nieuw certificaat en stuurt u het naar de ISV. Deze informatie kan worden opgehaald uit de federatieve meta gegevens. [Lees hier meer informatie over de federatieve meta gegevens.](../azuread-dev/azure-ad-federation-metadata.md)

## <a name="implement-sso"></a>SSO implementeren

Gebruik de volgende fasen voor het plannen en implementeren van uw oplossing in uw organisatie:

### <a name="user-configuration-for-sso"></a>Gebruikers configuratie voor SSO

- **Uw test gebruikers identificeren**

   Neem contact op met de eigenaar van de app en vraag hen om ten minste drie test gebruikers binnen de toepassing te maken. Zorg ervoor dat de gegevens die u gebruikt als de primaire id juist zijn ingevuld en overeenkomen met een kenmerk dat beschikbaar is in azure AD. In de meeste gevallen wordt deze toegewezen aan de ' NameID ' voor op SAML gebaseerde toepassingen. Voor JWT-tokens is het de preferred_username.
   
   Maak de gebruiker in azure AD hand matig als een Cloud gebruiker of synchroniseer de gebruiker van on-premises met behulp van de Azure AD Connect-synchronisatie-engine. Zorg ervoor dat de gegevens overeenkomen met de claims die naar de toepassing worden verzonden.

- **Eenmalige aanmelding configureren**

   Zoek in de [lijst met toepassingen](../saas-apps/tutorial-list.md)de zelf studie voor eenmalige aanmelding voor uw toepassing en volg de stappen in de zelf studie om uw SaaS-toepassing te configureren.

   Als u uw toepassing niet kunt vinden, raadpleegt u de [documentatie voor aangepaste toepassingen](./configure-saml-single-sign-on.md). Hiermee wordt u begeleid bij het toevoegen van een toepassing die zich niet in de Azure AD-galerie bevindt.

   U kunt ook claims gebruiken die zijn uitgegeven in het SAML-token voor de bedrijfs toepassing met behulp [van de richt lijnen van micro soft](../develop/active-directory-claims-mapping.md). Zorg ervoor dat deze is gekoppeld aan wat u verwacht te ontvangen in het SAML-antwoord voor uw toepassing. Als u problemen ondervindt tijdens de configuratie, gebruikt u onze richt lijnen voor [het opsporen](./debug-saml-sso-issues.md)van de SSO-integratie.

### <a name="provide-sso-change-communications-to-end-users"></a>SSO-wijzigings communicatie voor eind gebruikers opgeven

Implementeer uw communicatie plan. Zorg ervoor dat uw eind gebruikers weten dat er een wijziging is opgetreden, wanneer deze is aangekomen, wat u nu moet doen en hoe u hulp kunt krijgen.

### <a name="verify-end-user-scenarios-for-sso"></a>Scenario's voor eind gebruikers controleren voor eenmalige aanmelding

U kunt de volgende test cases gebruiken voor het uitvoeren van tests op bedrijfs eigen en persoonlijke apparaten om ervoor te zorgen dat uw SSO-configuraties naar verwachting werken. In de onderstaande scenario's wordt ervan uitgegaan dat een gebruiker naar een toepassings-URL navigeert en een verificatie stroom doorstuurt die is geïnitieerd door de service provider (door SP geïnitieerde verificatie stroom).

| Scenario | Verwacht resultaat op door SP geïnitieerde verificatie stroom door gebruiker |
|----------|---------------------------------------------------|
| Meld u aan bij de toepassing met Internet Explorer terwijl u op Corpnet. | Geïntegreerde Windows-verificatie (IWA) vindt plaats zonder extra prompts. |
| Meld u aan bij de toepassing met IE tijdens het uitschakelen van Corpnet met een nieuwe aanmeldings poging. | Prompt op basis van formulieren op AD FS server. De gebruiker heeft zich aangemeld en browser vraagt om MFA. |
| Meld u aan bij de toepassing met IE tijdens het uitschakelen van Corpnet met een huidige sessie en nooit MFA heeft uitgevoerd. | De gebruiker ontvangt geen prompt voor de eerste factor. Gebruiker ontvangt vragen om MFA. |
| Meld u aan bij de toepassing met IE terwijl u Corpnet met een huidige sessie en al MFA hebt uitgevoerd in deze sessie. | De gebruiker ontvangt geen prompt voor de eerste factor. De gebruiker ontvangt geen MFA. Gebruiker SSOs in toepassing. |
| Meld u aan bij de toepassing met Chrome/Firefox/Safari terwijl u Corpnet met een huidige sessie en al MFA hebt uitgevoerd tijdens deze sessie. | De gebruiker ontvangt geen prompt voor de eerste factor. De gebruiker ontvangt geen MFA. De gebruiker heeft de SSO-toepassing. |
| Meld u aan bij de toepassing met Chrome/Firefox/Safari terwijl u niet Corpnet met een nieuwe aanmeldings poging. | Prompt op basis van formulieren op AD FS server. De gebruiker heeft zich aangemeld en browser vraagt om MFA. |
| Meld u aan bij de toepassing met Chrome/Firefox tijdens een bedrijfs netwerk met een huidige sessie. | De gebruiker ontvangt geen prompt voor de eerste factor. De gebruiker ontvangt geen MFA. De gebruiker heeft de SSO-toepassing. |
| Meld u aan bij een toepassing met een mobiele app voor apps met een nieuwe aanmeldings poging. | Prompt op basis van formulieren op AD FS server. De gebruiker heeft zich aangemeld bij en de ADAL-client vraagt om MFA. |
| Niet-geautoriseerde gebruikers proberen zich aan te melden bij een toepassing met een aanmeldings-URL. | Prompt op basis van formulieren op AD FS server. De gebruiker kan zich niet aanmelden met de eerste factor. |
| Geautoriseerde gebruiker probeert zich aan te melden, maar voert een onjuist wacht woord in. | De gebruiker navigeert naar de URL van de toepassing en ontvangt een onjuiste gebruikers naam/wacht woord fout. |
| Geautoriseerde gebruiker klikt op een koppeling in een e-mail bericht en is al geverifieerd. | Gebruiker klikt op URL en is aangemeld bij de toepassing zonder extra prompts. |
| Geautoriseerde gebruiker klikt op een koppeling in een e-mail bericht en is nog niet geverifieerd. | Gebruiker klikt op URL en wordt gevraagd om te verifiëren met de eerste factor. |
| Geautoriseerde gebruiker meldt zich aan bij de toepassing met de mobiele app voor apps (SP-initiated) met een nieuwe aanmeldings poging. | Prompt op basis van formulieren op AD FS server. De gebruiker heeft zich aangemeld bij en de ADAL-client vraagt om MFA. |
| Niet-geautoriseerde gebruikers proberen zich aan te melden bij een toepassing met een aanmeldings-URL (SP-initiated). | Prompt op basis van formulieren op AD FS server. De gebruiker kan zich niet aanmelden met de eerste factor. |
| Geautoriseerde gebruiker probeert zich aan te melden, maar voert een onjuist wacht woord in.| De gebruiker navigeert naar de URL van de toepassing en ontvangt een onjuiste gebruikers naam/wacht woord fout. |
| Geautoriseerde gebruiker meldt zich af en meldt zich opnieuw aan. | Als de afmeldings-URL is geconfigureerd, wordt de gebruiker afgemeld bij alle services en wordt u gevraagd om te verifiëren. |
| Geautoriseerde gebruiker meldt zich af en meldt zich opnieuw aan. | Als de afmeldings-URL niet is geconfigureerd, wordt de gebruiker automatisch opnieuw aangemeld met behulp van het bestaande token van de bestaande Azure AD-browser sessie. |
| Geautoriseerde gebruiker klikt op een koppeling in een e-mail bericht en is al geverifieerd. | Gebruiker klikt op URL en is aangemeld bij de toepassing zonder extra prompts. |
| Geautoriseerde gebruiker klikt op een koppeling in een e-mail bericht en is nog niet geverifieerd. | Gebruiker klikt op URL en wordt gevraagd om te verifiëren met de eerste factor. |

## <a name="manage-sso"></a>Eenmalige aanmelding beheren

In deze sectie vindt u een overzicht van de vereisten en aanbevelingen voor het beheren van SSO.

### <a name="required-administrative-roles"></a>Vereiste beheerders rollen

Gebruik altijd de rol met de minste machtigingen die beschikbaar zijn om de vereiste taak in Azure Active Directory uit te voeren. Micro soft raadt aan om [de verschillende beschik bare rollen te bekijken](../roles/permissions-reference.md) en de juiste te kiezen om uw behoeften voor elke persoon voor deze toepassing op te lossen. Sommige rollen moeten mogelijk tijdelijk worden toegepast en worden verwijderd nadat de implementatie is voltooid.

| Persona| Rollen | Azure AD-rol (indien nodig) |
|--------|-------|-----------------------------|
| Help Desk-beheerder | Ondersteuning voor laag 1 | Geen |
| Identiteits beheerder | Configureren en fouten opsporen wanneer de problemen invloed hebben op Azure AD | Globale beheerder |
| Toepassings beheerder | Gebruikers verklaring in toepassing, configuratie voor gebruikers met machtigingen | Geen |
| Infrastructuur beheerders | Eigenaar certificaat rollover | Globale beheerder |
| Bedrijfs eigenaar/belanghebbende | Gebruikers verklaring in toepassing, configuratie voor gebruikers met machtigingen | Geen |

Het is raadzaam om [privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) te gebruiken voor het beheren van uw rollen om extra controle, controle en toegangs beoordeling te bieden voor gebruikers met mapmachtigingen.

### <a name="sso-certificate-lifecycle-management"></a>Beheer van de levens cyclus van SSO-certificaten

Het is belang rijk om de juiste rollen en e-mail distributie lijsten te identificeren die zijn opgedeeld met het beheren van de levens cyclus van het handtekening certificaat tussen Azure AD en de toepassing die wordt geconfigureerd met eenmalige aanmelding. Hier volgen enkele van de belangrijkste functies die we aanraden:

- Eigenaar voor het bijwerken van gebruikers eigenschappen in de toepassing
- Eigenaar van de aanroep voor toepassings afbreeking/reparatie
- Nauw keurig bewaakte e-mail distributie lijst voor meldingen met betrekking tot certificaat wijzigingen

De maximale levens duur van een certificaat is drie jaar. We raden u aan een proces te maken voor het afhandelen van een certificaat wijziging tussen Azure AD en uw toepassing. Dit kan helpen bij het voor komen of minimaliseren van een storing als gevolg van het verlopen van een certificaat of het afdwingen van het certificaat.

### <a name="rollback-process"></a>Terugdraai proces

Wanneer u klaar bent met testen op basis van uw test cases, is het tijd om in productie te gaan met uw toepassing. Als u overstapt op productie, worden uw geplande en geteste configuraties in uw productie Tenant geïmplementeerd en kunnen ze worden uitgebreid naar uw gebruikers. Het is echter belang rijk om te plannen wat u moet doen voor het geval uw implementatie niet wordt uitgevoerd zoals gepland. Als de SSO-configuratie tijdens de implementatie mislukt, moet u weten hoe u de onderbreking kunt beperken en de gevolgen voor uw gebruikers kunt verminderen.

De beschik baarheid van verificatie methoden binnen de toepassing bepaalt uw beste strategie. Zorg er altijd voor dat u gedetailleerde documentatie hebt voor app-eigen aren om precies te weten te komen hoe u terugkeert naar de oorspronkelijke status van de aanmeldings configuratie als uw implementatie in problemen wordt uitgevoerd.

- **Als uw app meerdere id-providers ondersteunt**, bijvoorbeeld LDAP en AD FS en ping, mag u de bestaande SSO-configuratie niet verwijderen tijdens de implementatie. Schakel deze functie tijdens de migratie uit, zodat u deze later opnieuw moet overschakelen. 

- **Als uw app niet meerdere id ondersteunt** , maar gebruikers zich kunnen aanmelden met verificatie op basis van formulieren (gebruikers naam/wacht woord), moet u ervoor zorgen dat gebruikers zich kunnen terugvallen op deze manier als de implementatie van de nieuwe SSO-configuratie mislukt.

### <a name="access-management"></a>Toegangsbeheer

We raden u aan een schaal bare benadering te kiezen bij het beheren van de toegang tot resources. Veelvoorkomende benaderingen zijn het gebruik van on-premises groepen door te synchroniseren via Azure AD Connect, [het maken van dynamische groepen in azure AD op basis van gebruikers kenmerken](../enterprise-users/groups-dynamic-membership.md)of het maken van [self-service groepen](../enterprise-users/groups-self-service-management.md) in azure AD die worden beheerd door een resource-eigenaar.

### <a name="monitor-security"></a>Beveiliging bewaken

We raden u aan een reguliere uitgebracht in te stellen waarin u de verschillende aspecten van SaaS app Security bekijkt en eventuele vereiste herstel bewerkingen uitvoert.

### <a name="troubleshooting"></a>Problemen oplossen

De volgende koppelingen geven scenario's voor het oplossen van problemen. U kunt een specifieke hand leiding maken voor het ondersteunings personeel dat deze scenario's bevat en de stappen om deze te herstellen.

#### <a name="consent-issues"></a>Problemen met toestemming

- [Fout bij onverwachte toestemming](./application-sign-in-unexpected-user-consent-prompt.md)

- [Fout bij toestemming gebruiker](./application-sign-in-unexpected-user-consent-error.md)

#### <a name="sign-in-issues"></a>Aanmeldingsproblemen

- [Problemen bij het aanmelden vanuit een aangepaste portal](./application-sign-in-other-problem-access-panel.md)

- [Problemen met aanmelden vanaf Mijn apps](./application-sign-in-other-problem-access-panel.md)

- [Fout bij de aanmeldingspagina van toepassing](./application-sign-in-problem-application-error.md)

- [Probleem bij het aanmelden bij een micro soft-toepassing](./application-sign-in-problem-first-party-microsoft.md)

#### <a name="sso-issues-for-applications"></a>SSO-problemen voor toepassingen

- [Probleem met wacht woord-SSO voor toepassingen](./troubleshoot-password-based-sso.md) 

- [Problemen met aanmelden bij apps die zijn geconfigureerd voor eenmalige aanmelding op basis van SAML](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps)   


## <a name="next-steps"></a>Volgende stappen

[Fouten opsporen van op SAML gebaseerde SSO](./debug-saml-sso-issues.md)

[Claim toewijzing voor apps via Power shell](../develop/active-directory-claims-mapping.md)

[Claims aanpassen die zijn uitgegeven in het SAML-token](../develop/active-directory-saml-claims-customization.md)

[SAML-protocol voor eenmalige aanmelding](../develop/single-sign-on-saml-protocol.md)

[Enkelvoudig Sign-Out SAML-Protocol](../develop/single-sign-out-saml-protocol.md)

[Azure AD B2B](../external-identities/what-is-b2b.md) (voor externe gebruikers, zoals partners en leveranciers)

[Voorwaardelijke toegang van Azure AD](../conditional-access/overview.md)

[Azure Identity Protection](../identity-protection/overview-identity-protection.md)

[Toegang via eenmalige aanmelding](./what-is-single-sign-on.md)

[Zelf studie voor Application SSO](../saas-apps/tutorial-list.md)

[Technisch document downloaden-implementatie plan voor eenmalige aanmelding](https://aka.ms/SSODeploymentPlan)