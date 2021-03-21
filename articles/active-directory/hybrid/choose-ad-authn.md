---
title: Verificatie voor Azure AD Hybrid Identity Solutions
titleSuffix: Active Directory
description: Deze hand leiding helpt CEOs, Cio's, CISOs, Chief Identity Architects, Enter prise Architects en Security en IT-besluit vormers die verantwoordelijk zijn voor het kiezen van een verificatie methode voor hun Azure AD hybride identiteits oplossing in gemiddeld tot grote organisaties.
keywords: ''
author: martincoetzer
ms.author: martinco
ms.date: 10/30/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: 15d62f40b50617fd1f6e543cb404a0d38361d3bd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94836492"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Kies de juiste verificatie methode voor uw Azure Active Directory hybride identiteits oplossing

Het kiezen van de juiste verificatie methode is de eerste reden voor organisaties die hun apps willen verplaatsen naar de Cloud. Doe deze beslissing niet lichter, om de volgende redenen:

1. Het is de eerste beslissing voor een organisatie die naar de Cloud wil verplaatsen.

2. De verificatie methode is een essentieel onderdeel van de aanwezigheid van een organisatie in de Cloud. Het beheert de toegang tot alle Cloud gegevens en-resources.

3. Het is de basis van alle andere geavanceerde functies voor beveiliging en gebruikers ervaring in azure AD.

De identiteit is het nieuwe beheer vlak van IT-beveiliging, dus is de toegangs beveiliging van een organisatie naar de nieuwe Cloud wereld. Organisaties hebben een identiteits beheergebied nodig waarmee de beveiliging wordt versterkt en de Cloud-apps worden beschermd tegen indringers.

> [!NOTE]
> Het wijzigen van de verificatie methode vereist planning, testen en mogelijk downtime. [Gefaseerde implementatie](./how-to-connect-staged-rollout.md) is een uitstekende manier om gebruikers migratie van Federatie naar Cloud verificatie te testen.

### <a name="out-of-scope"></a>Buiten bereik
Organisaties die geen bestaande on-premises Directory-footprint hebben, zijn niet de focus van dit artikel. Normaal gesp roken maken deze bedrijven alleen identiteiten in de Cloud, waarvoor geen hybride identiteits oplossing nodig is. Alleen Cloud-identiteiten bestaan uitsluitend in de Cloud en zijn niet gekoppeld aan de bijbehorende on-premises identiteiten.

## <a name="authentication-methods"></a>Verificatiemethoden
Wanneer de hybride identiteits oplossing van Azure AD uw nieuwe besturings vlak is, is verificatie de basis van Cloud toegang. Het kiezen van de juiste verificatie methode is een cruciaal eerste beslissing bij het instellen van een Azure AD hybride identiteits oplossing. Implementeer de verificatie methode die is geconfigureerd met behulp van Azure AD Connect, waarmee ook gebruikers in de cloud worden ingericht.

Als u een verificatie methode wilt kiezen, moet u rekening houden met de tijd, de bestaande infra structuur, de complexiteit en de kosten van de implementatie van uw keuze. Deze factoren zijn voor elke organisatie verschillend en kunnen in de loop van de tijd veranderen.

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Azure AD biedt ondersteuning voor de volgende verificatie methoden voor hybride identiteits oplossingen.

### <a name="cloud-authentication"></a>Cloud authenticatie
Wanneer u deze verificatie methode kiest, wordt het aanmeldings proces van gebruikers door Azure AD afgehandeld. Met naadloze eenmalige aanmelding (SSO) kunnen gebruikers zich aanmelden bij Cloud-apps zonder hun referenties opnieuw in te voeren. Met Cloud authenticatie kunt u kiezen uit twee opties:

**Azure AD-wachtwoord-hashsynchronisatie**. De eenvoudigste manier om verificatie in te scha kelen voor on-premises Directory-objecten in azure AD. Gebruikers kunnen dezelfde gebruikers naam en hetzelfde wacht woord gebruiken als ze on-premises gebruiken zonder dat ze een extra infra structuur hoeven te implementeren. Voor sommige Premium-functies van Azure AD, zoals identiteits beveiliging en [Azure AD Domain Services](../../active-directory-domain-services/tutorial-create-instance.md), is wachtwoord hash-synchronisatie vereist, ongeacht welke verificatie methode u kiest.

> [!NOTE]
> Wacht woorden worden nooit in ongecodeerde tekst opgeslagen of versleuteld met een omkeer bare algoritme in azure AD. Zie [wachtwoord hash synchronisatie implementeren met Azure AD Connect Sync](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)voor meer informatie over het feitelijke proces van wachtwoord hash-synchronisatie.

**Pass-Through-verificatie van Azure AD**. Biedt een eenvoudige wachtwoord validatie voor Azure AD-verificatie services door gebruik te maken van een software-agent die wordt uitgevoerd op een of meer on-premises servers. De servers valideren de gebruikers rechtstreeks met uw on-premises Active Directory, wat ervoor zorgt dat de wachtwoord validatie niet plaatsvindt in de Cloud.

Bedrijven met een beveiligings vereiste voor het direct afdwingen van on-premises gebruikers accounts, wachtwoord beleid en aanmeldings tijden kunnen deze verificatie methode gebruiken. Zie [aanmelding van gebruikers met Azure AD Pass-Through-verificatie](../../active-directory/hybrid/how-to-connect-pta.md)voor meer informatie over het feitelijke Pass-Through-verificatie proces.

### <a name="federated-authentication"></a>Federatieve verificatie
Wanneer u deze verificatie methode kiest, wordt het verificatie proces door Azure AD naar een afzonderlijk vertrouwd verificatie systeem, zoals on-premises Active Directory Federation Services (AD FS), voor het valideren van het wacht woord van de gebruiker.

Het verificatie systeem kan extra geavanceerde verificatie vereisten bieden. Voor beelden zijn verificatie op basis van een Smart Card of multi-factor Authentication van derden. Zie [deploying Active Directory Federation Services](/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide)(Engelstalig) voor meer informatie.

In het volgende gedeelte kunt u bepalen welke verificatie methode het meest geschikt is voor u met een beslissings structuur. Hiermee kunt u bepalen of u Cloud of Federated Authentication wilt implementeren voor uw Azure AD hybride identiteits oplossing.

## <a name="decision-tree"></a>Beslissingsstructuur

![Beslissings structuur voor Azure AD-verificatie](./media/choose-ad-authn/azure-ad-authn-image1.png)

Details over beslissings vragen:

1. Azure AD kan aanmelden voor gebruikers afhandelen zonder afhankelijk te zijn van on-premises onderdelen om wacht woorden te controleren.
2. Azure AD kan gebruikers aanmelding afleveren bij een vertrouwde verificatie provider, zoals de AD FS van micro soft.
3. Als u wilt Toep assen op gebruikers niveau Active Directory beveiligings beleid, zoals verlopen accounts, uitgeschakelde accounts, wacht woorden verlopen, account vergrendeld en aanmeldings tijden voor elke aanmelding van de gebruiker, vereist Azure AD enkele on-premises onderdelen.
4. Aanmeld functies die niet systeem eigen worden ondersteund door Azure AD:
   * Meld u aan met Smart Cards of certificaten.
   * Meld u aan met on-premises MFA-server.
   * Meld u aan met verificatie oplossing van derden.
   * Oplossing voor on-premises verificatie op meerdere locaties.
5. Azure AD Identity Protection wacht woord-hash-synchronisatie vereist ongeacht de aanmeldings methode die u kiest, om het rapport *gebruikers met gelekte referenties* op te geven. Organisaties kunnen een failover uitvoeren naar een wachtwoord hash-synchronisatie als hun primaire aanmeldings methode mislukt en is geconfigureerd voor de fout gebeurtenis.

> [!NOTE]
> [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) -licenties Azure AD Identity Protection vereisen.

## <a name="detailed-considerations"></a>Gedetailleerde overwegingen

### <a name="cloud-authentication-password-hash-synchronization"></a>Cloud authenticatie: wacht woord-hash-synchronisatie

* **Moeite**. Voor de synchronisatie van wacht woord-hashes is de minimale inspanning vereist voor de implementatie, het onderhoud en de infra structuur.  Dit inspannings niveau is doorgaans van toepassing op organisaties die hun gebruikers alleen nodig hebben om zich aan te melden bij Microsoft 365, SaaS-apps en andere resources op basis van Azure AD. Wanneer deze functie is ingeschakeld, wordt de synchronisatie van wacht woord-hashes onderdeel van het Azure AD Connect synchronisatie proces en wordt elke twee minuten uitgevoerd.

* **Gebruikers ervaring**. Als u de aanmeld procedure van gebruikers wilt verbeteren, implementeert u naadloze SSO met synchronisatie van wacht woord-hashes. Naadloze SSO elimineert overbodige vragen wanneer gebruikers zijn aangemeld.

* **Geavanceerde scenario's**. Als organisaties hiervoor kiezen, is het mogelijk om inzichten te gebruiken van identiteiten met Azure AD Identity Protection rapporten met Azure AD Premium P2. Een voor beeld is het rapport met gelekte referenties. Windows hello voor bedrijven heeft [specifieke vereisten voor het gebruik van wachtwoord-hash-synchronisatie](/windows/access-protection/hello-for-business/hello-identity-verification). Voor [Azure AD Domain Services](../../active-directory-domain-services/tutorial-create-instance.md) is wachtwoord hash-synchronisatie vereist om gebruikers in te richten met hun bedrijfs referenties in het beheerde domein.

    Organisaties waarvoor multi-factor Authentication met wachtwoord hash-synchronisatie vereist is, moeten gebruikmaken van [aangepaste besturings elementen](../../active-directory/conditional-access/controls.md#custom-controls-preview)voor Azure AD multi-factor Authentication of voorwaardelijke toegang. Deze organisaties kunnen geen derden of on-premises multi-factor Authentication-methoden gebruiken die afhankelijk zijn van Federatie.

> [!NOTE]
> Voor voorwaardelijke toegang van Azure AD zijn [Azure AD Premium P1](https://azure.microsoft.com/pricing/details/active-directory/) -licenties vereist.

* **Bedrijfs continuïteit**. Het gebruik van wacht woord-hash synchronisatie met Cloud authenticatie is Maxi maal beschikbaar als een Cloud service die wordt geschaald naar alle micro soft-data centers. Implementeer een tweede Azure AD Connect-server in de faserings modus in een stand-by configuratie om te controleren of de synchronisatie van wacht woord-hashes niet kan worden uitgevoerd voor langere Peri Oden.

* **Overwegingen**. Op dit moment worden wijzigingen in de status van on-premises accounts niet onmiddellijk door synchronisatie van wacht woord-hashes afgedwongen. In deze situatie heeft een gebruiker toegang tot Cloud-apps totdat de status van het gebruikers account is gesynchroniseerd met Azure AD. Organisaties kunnen dit beperken door een nieuwe synchronisatie cyclus uit te voeren nadat beheerders bulksgewijs updates uitvoeren op de statussen van on-premises gebruikers accounts. Een voor beeld is het uitschakelen van accounts.

> [!NOTE]
> Het wacht woord is verlopen en de status van vergrendelde account vergrendelingen worden momenteel niet gesynchroniseerd met Azure AD met Azure AD Connect. Wanneer u het wacht woord van een gebruiker wijzigt en de *gebruiker het wacht woord moet wijzigen bij de volgende aanmeldings* vlag, wordt de wacht woord-hash niet gesynchroniseerd met Azure AD met Azure AD Connect totdat de gebruiker het wacht woord wijzigt.

Raadpleeg de implementatie van [wacht woord-hash synchronisatie](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) voor implementaties tappen.

### <a name="cloud-authentication-pass-through-authentication"></a>Cloud authenticatie: Pass Through-verificatie  

* **Moeite**. Voor Pass-Through-verificatie hebt u een of meer nodig (drie) lichte agents die zijn geïnstalleerd op bestaande servers. Deze agents moeten toegang hebben tot uw on-premises Active Directory Domain Services, met inbegrip van uw on-premises AD-domein controllers. Ze hebben uitgaande toegang tot internet nodig en toegang tot uw domein controllers. Daarom wordt het niet ondersteund voor het implementeren van de agents in een perimeter netwerk.

    Pass-Through-verificatie vereist onbeperkte netwerk toegang tot domein controllers. Al het netwerk verkeer wordt versleuteld en beperkt tot verificatie aanvragen. Zie voor meer informatie over dit proces de [beveiligings grondige](../../active-directory/hybrid/how-to-connect-pta-security-deep-dive.md) gang bij Pass-Through-verificatie.

* **Gebruikers ervaring**. Als u de aanmeld procedure van gebruikers wilt verbeteren, implementeert u naadloze SSO met Pass-Through-verificatie. Naadloze SSO elimineert overbodige vragen nadat gebruikers zich hebben aangemeld.

* **Geavanceerde scenario's**. Pass-Through-verificatie dwingt het on-premises account beleid af op het moment dat u zich aanmeldt. Bijvoorbeeld: de toegang wordt geweigerd wanneer de account status van een on-premises gebruiker is uitgeschakeld, vergrendeld of het [wacht woord is verlopen](../../active-directory/hybrid/how-to-connect-pta-faq.md#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) of de aanmeldings poging buiten de uren valt wanneer de gebruiker zich mag aanmelden.

    Organisaties waarvoor multi-factor Authentication met Pass Through-verificatie is vereist, moeten gebruikmaken van [aangepaste besturings elementen](../../active-directory/conditional-access/controls.md#custom-controls-preview)voor Azure AD multi-factor Authentication (MFA) of voorwaardelijke toegang. Deze organisaties kunnen een multi-factor-verificatie methode van derden of een lokale meervoudige authenticatie gebruiken die afhankelijk is van Federatie. Geavanceerde functies vereisen dat wachtwoord-hash-synchronisatie wordt geïmplementeerd, ongeacht of u Pass-Through-verificatie kiest. Een voor beeld is het rapport met gelekte referenties van identiteits beveiliging.

* **Bedrijfs continuïteit**. We raden u aan om twee extra Pass-Through-verificatie agenten te implementeren. Deze extra's bevinden zich in aanvulling op de eerste agent op de Azure AD Connect-server. Deze extra implementatie zorgt voor een hoge Beschik baarheid van verificatie aanvragen. Wanneer er drie agents zijn geïmplementeerd, kan een agent nog steeds mislukken wanneer een andere agent niet beschikbaar is voor onderhoud.

    Er is nog een voor deel voor het implementeren van wachtwoord hash-synchronisatie naast Pass-Through-verificatie. Het fungeert als een methode voor het maken van een back-upverificatie wanneer de primaire authenticatie methode niet meer beschikbaar is.

* **Overwegingen**. U kunt wachtwoord hash-synchronisatie gebruiken als een back-upauthenticatie methode voor Pass-Through-verificatie, wanneer de agents de referenties van een gebruiker niet kunnen valideren als gevolg van een belang rijke on-premises fout. Failover naar wachtwoord-hash-synchronisatie vindt niet automatisch plaats en u moet Azure AD Connect gebruiken om de aanmeldings methode hand matig te wijzigen.

    Zie [Veelgestelde vragen](../../active-directory/hybrid/how-to-connect-pta-faq.md)voor andere overwegingen met betrekking tot Pass-Through-verificatie, met inbegrip van alternatieve ID-ondersteuning.

Raadpleeg [implement-through-verificatie implementeren](../../active-directory/hybrid/how-to-connect-pta.md) voor implementaties tappen.

### <a name="federated-authentication"></a>Federatieve verificatie

* **Moeite**. Een Federated Authentication systeem is afhankelijk van een extern vertrouwd systeem om gebruikers te verifiëren. Sommige bedrijven willen hun bestaande federatieve systeem investeringen opnieuw gebruiken met hun Azure AD hybride identiteits oplossing. Het onderhoud en beheer van het federatieve systeem valt buiten het beheer van Azure AD. Het is aan de organisatie door gebruik te maken van het federatieve systeem om ervoor te zorgen dat het veilig wordt geïmplementeerd en de verificatie belasting kan afhandelen.

* **Gebruikers ervaring**. De gebruikers ervaring van Federated Authentication is afhankelijk van de implementatie van de functies, topologie en configuratie van de Federatie farm. Sommige organisaties hebben deze flexibiliteit nodig om de toegang tot de Federatie Farm aan te passen en te configureren op basis van hun beveiligings vereisten. Het is bijvoorbeeld mogelijk om intern verbonden gebruikers en apparaten te configureren voor het automatisch aanmelden van gebruikers, zonder dat ze om referenties wordt gevraagd. Deze configuratie werkt omdat ze al zijn aangemeld op hun apparaten. Als dat nodig is, maken sommige geavanceerde beveiligings functies het aanmeldings proces van gebruikers moeilijker.

* **Geavanceerde scenario's**. Een Federated Authentication oplossing is vereist wanneer klanten een verificatie vereiste hebben die Azure AD niet systeem eigen ondersteunt. Bekijk de gedetailleerde informatie die u kan helpen bij [het kiezen van de juiste aanmeldings optie](/archive/blogs/samueld/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365). Houd rekening met de volgende algemene vereisten:

  * Verificatie waarvoor Smart Cards of certificaten zijn vereist.
  * On-premises MFA-servers of providers van derden waarvoor een federatieve id-provider is vereist.
  * Verificatie met behulp van verificatie oplossingen van derden. Zie de [Azure AD Federation-compatibiliteits lijst](../../active-directory/hybrid/how-to-connect-fed-compatibility.md).
  * Meld u aan waarvoor een sAMAccountName vereist is, bijvoorbeeld Domein\gebruikersnaam, in plaats van een UPN (User Principal Name), bijvoorbeeld user@domain.com .

* **Bedrijfs continuïteit**. Federatieve systemen vereisen doorgaans een matrix met gelijke taak verdeling van servers, ook wel een farm genoemd. Deze farm is geconfigureerd in een interne netwerk-en perimeter netwerk topologie om te zorgen voor hoge Beschik baarheid van verificatie aanvragen.

    Implementeer de synchronisatie van wacht woord-hashen samen met Federated Authentication als een methode voor het maken van een back-upverificatie wanneer de primaire verificatie methode niet meer beschikbaar is. Een voor beeld hiervan is wanneer de on-premises servers niet beschikbaar zijn. Voor sommige grote ondernemingen is een Federatie oplossing vereist voor ondersteuning van meerdere Internet toegangs punten die zijn geconfigureerd met geo-DNS voor verificatie aanvragen met een lage latentie.

* **Overwegingen**. Federatieve systemen vereisen doorgaans een aanzienlijke investering in on-premises infra structuur. De meeste organisaties kiezen deze optie als ze al een on-premises Federatie-investering hebben. En als het een sterke zakelijke vereiste is om een provider met één identiteit te gebruiken. De Federatie is complexer om te worden gebruikt en problemen op te lossen in vergelijking met oplossingen voor Cloud authenticatie.

Voor een niet-routeerbaar domein dat niet kan worden geverifieerd in azure AD, hebt u extra configuratie nodig om gebruikers-ID-aanmelding te implementeren. Deze vereiste wordt ook wel alternatieve aanmeldings-ID-ondersteuning genoemd. Zie [alternatieve aanmeldings-id configureren](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) voor beperkingen en vereisten. Als u ervoor kiest om een multi-factor Authentication-provider met Federatie van derden te gebruiken, moet u ervoor zorgen dat de provider WS-Trust ondersteunt om apparaten toe te voegen aan Azure AD.

Raadpleeg [Federatie servers implementeren](/windows-server/identity/ad-fs/deployment/deploying-federation-servers) voor implementaties tappen.

> [!NOTE]
> Wanneer u uw Azure AD hybride identiteits oplossing implementeert, moet u een van de ondersteunde topologieën van Azure AD Connect implementeren. Meer informatie over ondersteunde en niet-ondersteunde configuraties in [topologieën voor Azure AD Connect](../../active-directory/hybrid/plan-connect-topologies.md).

## <a name="architecture-diagrams"></a>Architectuur diagrammen

In de volgende diagrammen vindt u een overzicht van de architectuur onderdelen op hoog niveau die zijn vereist voor elke verificatie methode die u kunt gebruiken met uw Azure AD hybride identiteits oplossing. Ze bieden een overzicht waarmee u de verschillen tussen de oplossingen kunt vergelijken.

* Eenvoud van een wachtwoord hash-synchronisatie oplossing:

    ![Azure AD hybride identiteit met wachtwoord-hash-synchronisatie](./media/choose-ad-authn/azure-ad-authn-image2.png)

* Agent vereisten voor Pass-Through-verificatie met twee agents voor redundantie:

    ![Azure AD hybride identiteit met Pass-Through-verificatie](./media/choose-ad-authn/azure-ad-authn-image3.png)

* Onderdelen die vereist zijn voor de Federatie in uw perimeter netwerk en interne netwerken van uw organisatie:

    ![Azure AD Hybrid Identity met Federated Authentication](./media/choose-ad-authn/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Methoden vergelijken

|Overweging|Wachtwoord hash synchronisatie + naadloze SSO|Pass-Through-verificatie + naadloze SSO|Federatie met AD FS|
|:-----|:-----|:-----|:-----|
|Waar gebeurt de verificatie?|In de cloud|In de Cloud nadat een beveiligd wachtwoord verificatie is uitgewisseld met de on-premises verificatie agent|On-premises|
|Wat zijn de on-premises Server vereisten buiten het inrichtings systeem: Azure AD Connect?|Geen|Eén server voor elke aanvullende verificatie agent|Twee of meer AD FS servers<br><br>Twee of meer WAP-servers in het perimeter/DMZ-netwerk|
|Wat zijn de vereisten voor on-premises Internet en netwerken buiten het inrichtings systeem?|Geen|[Uitgaande internet toegang](../../active-directory/hybrid/how-to-connect-pta-quick-start.md) vanaf de servers met verificatie agenten|[Inkomende Internet toegang](/windows-server/identity/ad-fs/overview/ad-fs-requirements) tot WAP-servers in het perimeter netwerk<br><br>Inkomende netwerk toegang tot AD FS servers van WAP-servers in de perimeter<br><br>Taakverdeling voor netwerken|
|Is er een vereiste voor TLS/SSL-certificaat?|Nee|Nee|Ja|
|Is er een oplossing voor status controle?|Niet vereist|Agent status die is verschaft door [Azure Active Directory-beheer centrum](../../active-directory/hybrid/tshoot-connect-pass-through-authentication.md)|[Azure AD Connect Health (Engelstalig)](../../active-directory/hybrid/how-to-connect-health-adfs.md)|
|Krijgen gebruikers eenmalige aanmelding voor cloud resources van apparaten die lid zijn van een domein in het bedrijfs netwerk?|Ja, met [naadloze SSO](../../active-directory/hybrid/how-to-connect-sso.md)|Ja, met [naadloze SSO](../../active-directory/hybrid/how-to-connect-sso.md)|Ja|
|Welke typen aanmelding worden ondersteund?|UserPrincipalName + wacht woord<br><br>Verificatie Windows-Integrated met [naadloze SSO](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[Alternatieve aanmeldings-ID](../../active-directory/hybrid/how-to-connect-install-custom.md)|UserPrincipalName + wacht woord<br><br>Verificatie Windows-Integrated met [naadloze SSO](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[Alternatieve aanmeldings-ID](../../active-directory/hybrid/how-to-connect-pta-faq.md)|UserPrincipalName + wacht woord<br><br>sAMAccountName + wacht woord<br><br>Windows-Integrated-verificatie<br><br>[Verificatie van certificaten en Smart Cards](/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Alternatieve aanmeldings-ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Wordt Windows hello voor bedrijven ondersteund?|[Sleutel vertrouwens model](/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[Sleutel vertrouwens model](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*Vereist een Windows Server 2016-domein functionaliteits niveau*|[Sleutel vertrouwens model](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Certificaat vertrouwens model](/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Wat zijn de opties voor multi-factor Authentication?|[Azure AD MFA](/azure/multi-factor-authentication/)<br><br>[Aangepaste besturings elementen met voorwaardelijke toegang *](../../active-directory/conditional-access/controls.md)|[Azure AD MFA](/azure/multi-factor-authentication/)<br><br>[Aangepaste besturings elementen met voorwaardelijke toegang *](../../active-directory/conditional-access/controls.md)|[Azure AD MFA](/azure/multi-factor-authentication/)<br><br>[Azure MFA-server](../../active-directory/authentication/howto-mfaserver-deploy.md)<br><br>[MFA van derden](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Aangepaste besturings elementen met voorwaardelijke toegang *](../../active-directory/conditional-access/controls.md)|
|Welke status van gebruikers accounts worden ondersteund?|Uitgeschakelde accounts<br>(Maxi maal 30 minuten)|Uitgeschakelde accounts<br><br>Account vergrendeld<br><br>Het account is verlopen<br><br>Wacht woord is verlopen<br><br>Aanmeldings tijden|Uitgeschakelde accounts<br><br>Account vergrendeld<br><br>Het account is verlopen<br><br>Wacht woord is verlopen<br><br>Aanmeldings tijden|
|Wat zijn de opties voor voorwaardelijke toegang?|[Voorwaardelijke toegang tot Azure AD, met Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Voorwaardelijke toegang tot Azure AD, met Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Voorwaardelijke toegang tot Azure AD, met Azure AD Premium](../../active-directory/conditional-access/overview.md)<br><br>[AD FS claim regels](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Worden verouderde protocollen geblokkeerd?|[Ja](../../active-directory/conditional-access/overview.md)|[Ja](../../active-directory/conditional-access/overview.md)|[Ja](/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Kunt u het logo, de afbeelding en de beschrijving aanpassen op de aanmeldings pagina's?|[Ja, met Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Ja, met Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Ja](../../active-directory/hybrid/how-to-connect-fed-management.md)|
|Welke geavanceerde scenario's worden ondersteund?|[Slim wacht woord vergren delen](../../active-directory/authentication/howto-password-smart-lockout.md)<br><br>[Rapporten met gelekte referenties, met Azure AD Premium P2](../identity-protection/overview-identity-protection.md)|[Slim wacht woord vergren delen](../../active-directory/authentication/howto-password-smart-lockout.md)|Verificatie systeem met lage latentie voor meerdere locaties<br><br>[Vergren deling AD FS extranet](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integratie met identiteits systemen van derden](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)|

> [!NOTE]
> Aangepaste besturings elementen in azure AD voorwaardelijke toegang bieden momenteel geen ondersteuning voor apparaatregistratie.

## <a name="recommendations"></a>Aanbevelingen
Uw identiteits systeem zorgt ervoor dat gebruikers toegang hebben tot Cloud-apps en de LOB-apps die u migreert en beschikbaar maakt in de Cloud. Verificatie beheert de toegang tot apps om gemachtigde gebruikers productief en slecht actors van de gevoelige gegevens van uw organisatie te laten beheren.

Het gebruik of inschakelen van hash-synchronisatie van wacht woord voor de verificatie methode die u kiest, om de volgende redenen:

1. **Hoge Beschik baarheid en herstel na nood gevallen**. Pass-Through-verificatie en Federatie zijn afhankelijk van on-premises infra structuur. Voor Pass-Through-verificatie omvat de on-premises footprint de serverhardware en netwerken die de Pass-Through-verificatie agenten vereisen. De on-premises footprint voor Federatie is zelfs groter. Hiervoor zijn servers in uw perimeter netwerk vereist voor het proxy verificatie aanvragen en de interne Federatie servers.

    Implementeer redundante servers om afzonderlijke storings punten te voor komen. Verificatie aanvragen worden dan altijd verwerkt als een onderdeel mislukt. Zowel Pass-Through-verificatie als Federatie is ook afhankelijk van domein controllers om te reageren op verificatie aanvragen. Dit kan ook mislukken. Veel van deze onderdelen hebben onderhoud nodig om in de juiste staat te blijven. Storingen zijn waarschijnlijker wanneer onderhoud niet wordt gepland en correct wordt geïmplementeerd. Vermijd storingen door wachtwoord hash-synchronisatie te gebruiken, omdat de Microsoft Azure AD Cloud Authentication Service wereld wijd wordt geschaald en altijd beschikbaar is.

2. **Overleving van on-premises storingen**.  De gevolgen van een on-premises storing als gevolg van een cyber aanval of nood geval kunnen aanzienlijk zijn, variërend van reputatie-brand beschadiging aan een Paralyzed-organisatie die niet kan omgaan met de aanval. Onlangs waren veel organisaties slacht offer van malware-aanvallen, met inbegrip van gerichte Ransomware, waardoor hun on-premises servers niet meer beschikbaar zijn. Wanneer micro soft klanten helpt bij het oplossen van dergelijke aanvallen, ziet hij twee categorieën organisaties:

   * Organisaties die eerder ook de validatie van wacht woord-hashes op federatieve of Pass Through-verificatie hebben ingeschakeld, hebben hun primaire authenticatie methode gewijzigd om vervolgens wachtwoord-hash-synchronisatie te gebruiken. Ze zijn binnen een paar uur weer online. Door toegang tot e-mail via Microsoft 365 te gebruiken, hebben ze gewerkt om problemen op te lossen en om toegang te krijgen tot andere cloud-gebaseerde workloads.

   * Organisaties waarvoor niet eerder wachtwoord hash-synchronisatie is ingeschakeld, moesten een niet-vertrouwde externe e-mail systeem voor consumenten gebruiken voor communicatie om problemen op te lossen. In dergelijke gevallen hebben ze weken nodig om hun on-premises identiteits infrastructuur te herstellen voordat gebruikers zich weer kunnen aanmelden bij apps in de Cloud.

3. **Identiteits beveiliging**. Een van de beste manieren om gebruikers in de cloud te beveiligen, is Azure AD Identity Protection met Azure AD Premium P2. Micro soft scant voortdurend het Internet op gebruikers-en wachtwoord lijsten dat onjuiste actoren verkopen en beschikbaar maken op het donkere web. Azure AD kan deze informatie gebruiken om te controleren of een van de gebruikers namen en wacht woorden in uw organisatie is aangetast. Daarom is het van essentieel belang om wachtwoord hash-synchronisatie in te scha kelen, ongeacht de verificatie methode die u gebruikt, ongeacht of deze federatief of Pass-Through-verificatie is. Gelekte referenties worden weer gegeven als een rapport. Gebruik deze informatie om gebruikers te blok keren of te dwingen hun wacht woord te wijzigen wanneer ze zich proberen aan te melden met gelekte wacht woorden.

## <a name="conclusion"></a>Conclusie

In dit artikel vindt u een overzicht van verschillende verificatie opties die organisaties kunnen configureren en implementeren voor de ondersteuning van toegang tot Cloud-apps. Om te voldoen aan verschillende bedrijfs-, beveiligings-en technische vereisten, kunnen organisaties kiezen tussen wacht woord-hash synchronisatie, Pass Through-verificatie en Federatie.

Overweeg elke verificatie methode. Is de moeite om de oplossing te implementeren en de gebruikers ervaring van het aanmeldings proces is gericht op uw bedrijfs vereisten? Evalueer of uw organisatie de geavanceerde scenario's en de functies van de bedrijfs continuïteit van elke verificatie methode nodig heeft. Evalueer ten slotte de overwegingen van elke verificatie methode. Kunt u een van deze opties gebruiken om uw keuze te implementeren?

## <a name="next-steps"></a>Volgende stappen

In de huidige wereld zijn bedreigingen 24 uur per dag aanwezig en zijn ze overal verkrijgbaar. Implementeer de juiste verificatie methode en zorgt ervoor dat uw beveiligings Risico's worden verholpen en uw identiteiten worden beschermd.

[Ga](../fundamentals/active-directory-whatis.md) aan de slag met Azure AD en implementeer de juiste verificatie oplossing voor uw organisatie.

Als u overweegt om te migreren van federatieve naar Cloud authenticatie, lees dan meer informatie over [het wijzigen van de aanmeldings methode](../../active-directory/hybrid/plan-connect-user-signin.md). Als hulp bij het plannen en implementeren van de migratie, gebruikt u [Deze implementatie plannen](../fundamentals/active-directory-deployment-plans.md) van het project of overweegt u de nieuwe functie voor [gefaseerde](../../active-directory/hybrid/how-to-connect-staged-rollout.md) implementatie te gebruiken voor het migreren van federatieve gebruikers naar het gebruik van Cloud-verificatie in een gefaseerde benadering.