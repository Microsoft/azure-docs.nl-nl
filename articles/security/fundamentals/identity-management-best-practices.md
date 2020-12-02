---
title: Aanbevolen procedures voor Azure Identity & Access-beveiliging | Microsoft Docs
description: Dit artikel bevat een aantal aanbevolen procedures voor het beheer van identiteiten en toegangs beheer met ingebouwde Azure-functies.
services: security
documentationcenter: na
author: terrylanfear
manager: RKarlin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: bf593eab54233ab65435585485dd8e3940f5addb
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489816"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Aanbevolen procedures voor de beveiliging van Azure Identity Management en Access Control

In dit artikel bespreken we een verzameling van de aanbevolen procedures voor het beheren van Azure-identiteits beheer en-toegangs beheer. Deze aanbevolen procedures zijn afgeleid van onze ervaring met [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) en de ervaringen van klanten, zoals uzelf.

Voor elke best practice wordt uitgelegd:

* Wat de best practice is
* Waarom u die best practice wilt inschakelen
* Wat kan het gevolg zijn als u de best practice niet inschakelt
* Mogelijke alternatieven voor de best practice
* Informatie over het inschakelen van de best practice

Dit artikel over best practices voor het beheren van Azure-identiteits beheer en-toegang is gebaseerd op een advies-en Azure-platform mogelijkheden en-functie sets, zoals deze bestaan op het moment dat dit artikel werd geschreven.

Het schrijven van dit artikel is bedoeld om een algemeen overzicht te bieden van een robuuste beveiligings postuur na implementatie die is begeleid door onze "[5 stappen voor het beveiligen van uw identiteits infrastructuur](steps-secure-identity.md)", die u door enkele van onze kern functies en-Services helpt.

Meningen en technologieën veranderen in de loop van de tijd en dit artikel wordt regel matig bijgewerkt om deze wijzigingen weer te geven.

De aanbevolen procedures voor het beheren van Azure Identity Management en Access Control die in dit artikel worden beschreven, zijn:

* Identiteit behandelen als de primaire beveiligings verbinding
* Identiteits beheer centraliseren
* Verbonden tenants beheren
* Eenmalige aanmelding inschakelen
* Voorwaardelijke toegang inschakelen
* Plannen voor routine matige beveiligings verbeteringen
* Wachtwoordbeheer inschakelen
* Multi-factor-verificatie afdwingen voor gebruikers
* Op rollen gebaseerd toegangsbeheer gebruiken
* Minder bloot stelling van accounts met bevoegdheden
* Controle locaties waar resources zich bevinden
* Azure AD gebruiken voor opslag verificatie

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Identiteit behandelen als de primaire beveiligings verbinding

Het is veel zo dat identiteit de primaire verbinding is voor beveiliging. Dit is een verschuiving van de traditionele focus op netwerk beveiliging. Netwerk verbindingen blijven meer poreuze op elkaar en de verdediging van de verbinding kan niet zo effectief zijn als voorheen vóór de explosie van [BYOD](/mem/intune/fundamentals/byod-technology-decisions) -apparaten en Cloud toepassingen.

[Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) is de Azure-oplossing voor identiteits-en toegangs beheer. Azure AD is een multi tenant-, Cloud-en identiteits beheer service van micro soft. Het combineert belangrijke directoryservices, toegangsbeheer voor toepassingen en identiteitsbeveiliging in één oplossing.

In de volgende secties worden aanbevolen procedures beschreven voor identiteits-en toegangs beveiliging met behulp van Azure AD.

**Aanbevolen procedure**: Center Security-besturings elementen en detecties rond gebruikers-en service-identiteiten.
**Details**: gebruik Azure AD voor het termijnen van besturings elementen en identiteiten.

## <a name="centralize-identity-management"></a>Identiteits beheer centraliseren

In een scenario met [hybride identiteit](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) raden we u aan uw on-premises en Cloud directory's te integreren. Met integratie kan uw IT-team accounts beheren vanaf één locatie, ongeacht waar een account wordt gemaakt. Integratie helpt uw gebruikers ook productiever te zijn door een algemene identiteit op te geven voor toegang tot zowel Cloud-als on-premises resources.

**Best Practice**: Stel één Azure AD-exemplaar in. Consistentie en één gezaghebbende bron leiden tot meer duidelijkheid en verminderen de beveiligings Risico's van menselijke fouten en configuratie complexiteit.
**Details**: Wijs één Azure AD-Directory aan als de gezaghebbende bron voor zakelijke en organisatie accounts.

**Aanbevolen procedure**: Integreer uw on-premises Directory's met Azure AD.  
**Details**: gebruik [Azure AD Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) om uw on-premises adres lijst te synchroniseren met uw Cloud Directory.

> [!Note]
> Er zijn [factoren die van invloed zijn op de prestaties van Azure AD Connect](../../active-directory/hybrid/plan-connect-performance-factors.md). Zorg ervoor dat Azure AD Connect voldoende capaciteit heeft om te voor komen dat systemen de veiligheid en productiviteit belemmeren. Grote of complexe organisaties (organisaties die meer dan 100.000 objecten inrichten) moeten de [aanbevelingen](../../active-directory/hybrid/whatis-hybrid-identity.md) volgen om hun Azure AD Connect-implementatie te optimaliseren.

**Best Practice**: Synchroniseer accounts niet naar Azure AD met hoge bevoegdheden in uw bestaande Active Directory-exemplaar.
**Details**: Wijzig de standaard [Azure AD Connect configuratie](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) waarmee deze accounts worden gefilterd. Deze configuratie vermindert het risico dat aanvallers draait van Cloud naar on-premises activa (waardoor een belang rijk incident kan worden gemaakt).

**Aanbevolen procedure**: wacht woord-hash synchronisatie inschakelen.  
**Details**: wacht woord-hash-synchronisatie is een functie die wordt gebruikt voor het synchroniseren van wacht woord-hashes van gebruikers van een on-premises Active Directory-exemplaar naar een Azure AD-exemplaar in de Cloud. Deze synchronisatie helpt te beschermen tegen gelekte referenties die worden herhaald bij eerdere aanvallen.

Zelfs als u besluit Federatie met Active Directory Federation Services (AD FS) of andere id-providers te gebruiken, kunt u optioneel de wachtwoord hash-synchronisatie instellen als back-up voor het geval uw on-premises servers mislukken of tijdelijk niet beschikbaar zijn. Deze synchronisatie stelt gebruikers in staat om zich bij de service aan te melden met hetzelfde wacht woord dat ze gebruiken om zich aan te melden bij hun on-premises Active Directory-exemplaar. Daarnaast kan identiteits beveiliging de aangetaste referenties detecteren door gesynchroniseerde wacht woord-hashes te vergelijken met wacht woorden waarvan bekend is dat ze worden aangetast, als een gebruiker hetzelfde e-mail adres en wacht woord heeft gebruikt op andere services die niet zijn verbonden met Azure AD.

Zie [wachtwoord hash synchronisatie implementeren met Azure AD Connect Sync](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)voor meer informatie.

**Aanbevolen procedure**: voor het ontwikkelen van nieuwe toepassingen moet u Azure AD gebruiken voor verificatie.
**Details**: gebruik de juiste mogelijkheden om verificatie te ondersteunen:

  - Azure AD voor mede werkers
  - [Azure AD B2B](../../active-directory/external-identities/index.yml) voor gast gebruikers en externe partners
  - [Azure AD B2C](../../active-directory-b2c/index.yml) om te bepalen hoe klanten zich registreren, aanmelden en hun profielen beheren wanneer ze uw toepassingen gebruiken

Organisaties die hun on-premises identiteit niet integreren met hun Cloud identiteit, kunnen meer overhead hebben bij het beheer van accounts. Deze overhead verhoogt de kans op fouten en schending van de beveiliging.

> [!Note]
> U moet kiezen in welke mappen essentiële accounts zich bevinden en of het beheer werkstation dat wordt gebruikt, wordt beheerd door nieuwe Cloud Services of bestaande processen. Het gebruik van bestaande beheer-en identiteits Provisioning-processen kan een aantal Risico's verminderen, maar kan ook het risico vormen dat een aanvaller een on-premises account in gevaar brengt en draait naar de Cloud. U kunt een andere strategie voor verschillende rollen gebruiken (bijvoorbeeld IT-beheerders en beheerders van bedrijfs eenheden). U hebt twee opties. De eerste optie is het maken van Azure AD-accounts die niet zijn gesynchroniseerd met uw on-premises Active Directory-exemplaar. Voeg uw beheer werkstation toe aan Azure AD, dat u kunt beheren en gebruiken met behulp van Microsoft Intune. Tweede optie is het gebruik van bestaande beheerders accounts door te synchroniseren met uw on-premises Active Directory-exemplaar. Gebruik bestaande werk stations in uw Active Directory domein voor beheer en beveiliging.

## <a name="manage-connected-tenants"></a>Verbonden tenants beheren
Uw beveiligings organisatie heeft inzicht nodig om Risico's te beoordelen en te bepalen of het beleid van uw organisatie en eventuele wettelijke vereisten wordt gevolgd. Zorg ervoor dat uw beveiligings organisatie inzicht heeft in alle abonnementen die zijn verbonden met uw productie omgeving en netwerk (via [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) of [site-naar-site-VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)). Een [globale beheerder/bedrijfs beheerder](../../active-directory/roles/permissions-reference.md#company-administrator-permissions) in azure AD kan hun toegang tot de rol [beheerder van gebruikers toegang](../../role-based-access-control/built-in-roles.md#user-access-administrator) verhogen en alle abonnementen en beheerde groepen zien die zijn verbonden met uw omgeving.

Zie [toegang verhogen voor het beheer van alle Azure-abonnementen en-beheer groepen](../../role-based-access-control/elevate-access-global-admin.md) om ervoor te zorgen dat u en uw beveiligings groep alle abonnementen of beheer groepen kunnen weer geven die zijn verbonden met uw omgeving. U moet deze verhoogde toegang verwijderen nadat u de Risico's hebt beoordeeld.

## <a name="enable-single-sign-on"></a>Eenmalige aanmelding inschakelen

In de Cloud-eerste wereld kunt u eenmalige aanmelding (SSO) inschakelen voor apparaten, apps en services vanaf elke locatie zodat uw gebruikers overal en altijd productief kunnen zijn. Wanneer u meerdere identiteits oplossingen hebt om te beheren, wordt dit een probleem met het beheer, maar ook voor gebruikers die meerdere wacht woorden moeten onthouden.

Door dezelfde identiteits oplossing voor al uw apps en resources te gebruiken, kunt u SSO bezorgen. En uw gebruikers kunnen dezelfde set referenties gebruiken om zich aan te melden en toegang te krijgen tot de resources die ze nodig hebben, of de resources zich lokaal of in de cloud bevinden.

**Aanbevolen procedure**: SSO inschakelen.  
**Details**: Azure AD [breidt on-premises Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md) naar de Cloud. Gebruikers kunnen hun primaire werk-of school account gebruiken voor hun aan het domein gekoppelde apparaten, bedrijfs bronnen en alle web-en SaaS-toepassingen die ze nodig hebben om hun taken uit te voeren. Gebruikers hoeven niet meerdere sets met gebruikers namen en wacht woorden te onthouden en hun toepassings toegang kan automatisch worden ingericht (of ongedaan gemaakt) op basis van de lidmaatschappen van hun organisatie groep en hun status als werk nemer. En u kunt deze toegang beheren voor galerie-apps of voor uw eigen on-premises apps die u hebt ontwikkeld en gepubliceerd via de [Azure AD-toepassingsproxy](../../active-directory/manage-apps/application-proxy.md).

Gebruik SSO om gebruikers toegang te geven tot hun [SaaS-toepassingen](../../active-directory/manage-apps/what-is-single-sign-on.md) op basis van hun werk-of school account in azure AD. Dit is niet alleen van toepassing op micro soft SaaS-apps, maar ook voor andere apps, zoals [Google Apps](../../active-directory/saas-apps/google-apps-tutorial.md) en [Sales Force](../../active-directory/saas-apps/salesforce-tutorial.md). U kunt uw toepassing configureren voor het gebruik van Azure AD als [ID-provider op basis van SAML](../../active-directory/fundamentals/active-directory-whatis.md) . Als beveiligings beheer verleent Azure AD geen token waarmee gebruikers zich kunnen aanmelden bij de toepassing, tenzij ze toegang hebben gekregen via Azure AD. U kunt rechtstreeks toegang verlenen, of via een groep waarvan gebruikers lid zijn.

Organisaties die geen gemeen schappelijke identiteit voor het opzetten van SSO voor hun gebruikers en toepassingen maken, zijn meer beschikbaar in scenario's waarin gebruikers meerdere wacht woorden hebben. Deze scenario's verg Roten de kans dat gebruikers wacht woorden opnieuw gebruiken of zwakke wacht woorden gebruiken.

## <a name="turn-on-conditional-access"></a>Voorwaardelijke toegang inschakelen

Gebruikers hebben vanaf elke locatie toegang tot de resources van uw organisatie door gebruik te maken van verschillende apparaten en apps. Als IT-beheerder wilt u er zeker van zijn dat deze apparaten voldoen aan uw standaarden voor beveiliging en naleving. U hoeft alleen maar te focussen op wie toegang heeft tot een resource die niet meer voldoende is.

Als u de beveiliging en productiviteit wilt afstemmen, moet u nadenken over de manier waarop een resource wordt geopend voordat u een beslissing kunt nemen over toegangs beheer. Met voorwaardelijke toegang van Azure AD kunt u deze vereiste aanpakken. Met voorwaardelijke toegang kunt u automatische beslissingen voor toegangs beheer maken op basis van de voor waarden voor toegang tot uw Cloud-apps.

**Aanbevolen procedure**: toegang tot bedrijfs resources beheren en controleren.  
**Details**: Configureer algemene [beleids regels voor voorwaardelijke toegang](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) van Azure AD op basis van een groep, locatie en toepassings gevoeligheid voor SaaS-apps en Azure AD-verbonden apps.

**Best Practice**: verouderde verificatie protocollen blok keren.
**Details**: aanvallers maken misbruik van zwakke plekken in oudere protocollen elke dag, met name voor aanvallen met een wacht woord. Configureer voorwaardelijke toegang om [verouderde protocollen te blok keren](../../active-directory/conditional-access/howto-conditional-access-policy-block-legacy.md).

## <a name="plan-for-routine-security-improvements"></a>Plannen voor routine matige beveiligings verbeteringen

Beveiliging is altijd in ontwikkeling, en het is belang rijk dat u in uw Cloud-en identiteits beheer raamwerk bouwt om regel matig groei te tonen en nieuwe manieren te ontdekken om uw omgeving te beveiligen.

Beveiligde scores voor identiteiten is een reeks aanbevolen beveiligings controles die door micro soft worden gepubliceerd om u een numerieke score te bieden om uw beveiligings postuur objectief te meten en toekomstige beveiligings verbeteringen te plannen. U kunt ook uw score weer geven in vergelijking met die in andere branches en uw eigen trends in de loop van de tijd.

**Best Practice**: plan routine matige beveiligings beoordelingen en verbeteringen op basis van best practices in uw branche.
**Details**: gebruik de functie beveiligde score voor identiteit om uw verbeteringen in de loop van de tijd te rangschikken.

## <a name="enable-password-management"></a>Wachtwoordbeheer inschakelen

Als u meerdere tenants hebt of gebruikers in staat wilt stellen [hun eigen wacht woord opnieuw](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)in te stellen, is het belang rijk dat u het juiste beveiligings beleid gebruikt om misbruik te voor komen.

**Best Practice**: Stel self-service voor wachtwoord herstel (SSPR) in voor uw gebruikers.  
**Details**: gebruik de Azure AD [self-service voor wachtwoord herstel](../../active-directory-b2c/user-flow-self-service-password-reset.md) .

**Aanbevolen** procedure: Bewaak hoe of als SSPR daad werkelijk wordt gebruikt.  
**Details**: de gebruikers controleren die worden geregistreerd met behulp van het Azure AD- [rapport registratie activiteit voor het opnieuw instellen van het wacht woord](../../active-directory/authentication/howto-sspr-reporting.md). De rapportage functie die door Azure AD wordt geboden, helpt u bij het beantwoorden van vragen met behulp van vooraf ontwikkelde rapporten. Als u een juiste licentie hebt, kunt u ook aangepaste query's maken.

**Aanbevolen procedure**: Breid wachtwoord beleidsregels op basis van de Cloud uit naar uw on-premises infra structuur.
**Details**: Verbeter het wachtwoord beleid in uw organisatie door dezelfde controles uit te voeren voor on-premises wachtwoord wijzigingen wanneer u wijzigingen aanbrengt in de Cloud op basis van wacht woorden. Installeer [Azure AD-wachtwoord beveiliging](../../active-directory/authentication/concept-password-ban-bad.md) voor Windows Server Active Directory-agents on-premises om geblokkeerde wachtwoord lijsten uit te breiden naar uw bestaande infra structuur. Gebruikers en beheerders die on-premises wacht woorden wijzigen, instellen of opnieuw instellen, moeten voldoen aan hetzelfde wachtwoord beleid als alleen-Cloud gebruikers.

## <a name="enforce-multi-factor-verification-for-users"></a>Multi-factor-verificatie afdwingen voor gebruikers

U kunt het beste verificatie in twee stappen vereisen voor al uw gebruikers. Dit omvat beheerders en andere gebruikers in uw organisatie die aanzienlijke gevolgen kunnen hebben als hun account is aangetast (bijvoorbeeld financiële officieren).

Er zijn meerdere opties voor het vereisen van verificatie in twee stappen. De beste optie voor u is afhankelijk van uw doelen, de Azure AD-editie die u uitvoert en uw licentie programma. Zie [verificatie in twee stappen vereisen voor een gebruiker](../../active-directory/authentication/howto-mfa-userstates.md) om de beste optie voor u te bepalen. Raadpleeg de pagina met prijzen voor [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) en [Azure AD multi-factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) voor meer informatie over licenties en prijzen.

Hieronder vindt u opties en voor delen voor het inschakelen van verificatie in twee stappen:

**Optie 1**: MFA inschakelen voor alle gebruikers en aanmeldings methoden met **voor delen** van Azure AD-standaard. met deze optie kunt u op eenvoudige wijze MFA afdwingen voor alle gebruikers in uw omgeving met een strikt beleid voor het volgende:

* Beheer accounts en administratieve aanmeldings mechanismen
* MFA-Challenge vereisen via Microsoft Authenticator voor alle gebruikers
* Verperk verouderde verificatie protocollen.

Deze methode is beschikbaar voor alle licentie categorieën, maar kan niet worden gemengd met het bestaande beleid voor voorwaardelijke toegang. U vindt meer informatie in de [standaard instellingen van Azure AD-beveiliging](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)

**Optie 2**: [Schakel multi-factor Authentication in door de gebruikers status te wijzigen](../../active-directory/authentication/howto-mfa-userstates.md).   
**Voor deel**: dit is de traditionele methode voor het vereisen van verificatie in twee stappen. Het werkt met [Azure AD-multi-factor Authentication in de Cloud en azure multi-factor Authentication-Server](../../active-directory/authentication/concept-mfa-howitworks.md). Wanneer u deze methode gebruikt, moeten gebruikers elke keer dat ze zich aanmelden een verificatie in twee stappen uitvoeren en het beleid voor voorwaardelijke toegang negeren.

Als u wilt weten waar Multi-Factor Authentication moet worden ingeschakeld, raadpleegt u [welke versie van Azure AD MFA het meest geschikt is voor mijn organisatie?](../../active-directory/authentication/concept-mfa-howitworks.md).

**Optie 3**: [multi-factor Authentication met beleid voor voorwaardelijke toegang inschakelen](../../active-directory/authentication/howto-mfa-getstarted.md).
**Voor deel**: met deze optie kunt u een verificatie in twee stappen onder specifieke voor waarden vragen door gebruik te maken van [voorwaardelijke toegang](../../active-directory/conditional-access/concept-conditional-access-policy-common.md). Specifieke voor waarden kunnen gebruikers zich aanmelden vanaf verschillende locaties, niet-vertrouwde apparaten of toepassingen die u als riskant beschouwt. Als u specifieke voor waarden definieert waarvoor u verificatie in twee stappen nodig hebt, kunt u voor uw gebruikers geen constante vragen stellen, wat een onaangename gebruikers ervaring kan zijn.

Dit is de meest flexibele manier om verificatie in twee stappen voor uw gebruikers in te scha kelen. Het inschakelen van een beleid voor voorwaardelijke toegang werkt alleen voor Azure AD-Multi-Factor Authentication in de Cloud en is een Premium-functie van Azure AD. Meer informatie over deze methode vindt u in de [implementatie van Azure AD-multi-factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md)in de Cloud.

**Optie 4**: multi-factor Authentication met beleid voor voorwaardelijke toegang inschakelen door te evalueren [op risico gebaseerd beleid voor voorwaardelijke toegang](../../active-directory/conditional-access/howto-conditional-access-policy-risk.md).   
**Voor deel**: met deze optie kunt u:

* Detecteer mogelijke beveiligings problemen die van invloed zijn op de identiteiten van uw organisatie.
* Automatische antwoorden configureren op gedetecteerde verdachte acties die zijn gerelateerd aan de identiteiten van uw organisatie.
* Onderzoek verdachte incidenten en onderneem passende maat regelen om deze op te lossen.

Deze methode maakt gebruik van de Azure AD Identity Protection risico evaluatie om te bepalen of verificatie in twee stappen is vereist op basis van gebruikers-en aanmeldings Risico's voor alle Cloud toepassingen. Voor deze methode is Azure Active Directory P2-licentie vereist. Meer informatie over deze methode vindt u in [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md).

> [!Note]
> Optie 2, waarmee Multi-Factor Authentication wordt ingeschakeld door de gebruikers status te wijzigen, overschrijft het beleid voor voorwaardelijke toegang. Omdat opties 3 en 4 gebruikmaken van het beleid voor voorwaardelijke toegang, kunt u de optie 2 niet gebruiken.

Organisaties die geen extra lagen van identiteits beveiliging toevoegen, zoals verificatie in twee stappen, zijn gevoeliger voor aanvallen met referentie diefstal. Een aanval met een referentie diefstal kan leiden tot inbreuk op gegevens.

## <a name="use-role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer gebruiken

Toegangs beheer voor cloud resources is van cruciaal belang voor alle organisaties die gebruikmaken van de Cloud. Met [op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure](../../role-based-access-control/overview.md) kunt u beheren wie toegang heeft tot Azure-resources, wat ze kunnen doen met deze resources en op welke gebieden ze toegang hebben.

Het aanwijzen van groepen of afzonderlijke rollen die verantwoordelijk zijn voor specifieke functies in azure, helpt Verwar ring te voor komen die kan leiden tot mensen-en automatiserings fouten die beveiligings Risico's veroorzaken. Het beperken van de toegang op basis van de beveiligings principes van de [nood zaak om te kennen](https://en.wikipedia.org/wiki/Need_to_know) , is van cruciaal belang voor organisaties [die beveiligings beleid](https://en.wikipedia.org/wiki/Principle_of_least_privilege) voor gegevens toegang willen afdwingen.

Uw beveiligings team moet inzicht hebben in uw Azure-resources om Risico's te kunnen beoordelen en oplossen. Als het beveiligings team operationele verantwoordelijkheden heeft, hebben ze extra machtigingen nodig om hun taken uit te voeren.

U kunt [Azure RBAC](../../role-based-access-control/overview.md) gebruiken om machtigingen toe te wijzen aan gebruikers, groepen en toepassingen bij een bepaald bereik. Het bereik van een roltoewijzing kan een abonnement, een resourcegroep of een enkele resource zijn.

**Best Practice**: Verdeel taken binnen uw team en ken alleen de hoeveelheid toegang toe aan gebruikers die ze nodig hebben om hun taken uit te voeren. In plaats van iedereen onbeperkte machtigingen te geven in uw Azure-abonnement of-resources, mag u alleen bepaalde acties in een bepaald bereik toestaan.
**Details**: gebruik [Azure ingebouwde rollen](../../role-based-access-control/built-in-roles.md) in azure om machtigingen toe te wijzen aan gebruikers.

> [!Note]
> Specifieke machtigingen maken overbodige complexiteit en verwar ring en kunnen worden gecumuleerd in een ' legacy ' configuratie die moeilijk te herstellen is zonder dat u iets hoeft te verbreken. Vermijd resourcespecifieke bevoegdheden. Gebruik in plaats daarvan beheer groepen voor machtigingen voor het hele onderneming en resource groepen voor de bevoegdheden binnen abonnementen. Vermijd gebruikersspecifieke machtigingen. Wijs in plaats daarvan toegang toe aan groepen in Azure AD.

**Best Practice**: verleen beveiligings teams met Azure-verantwoordelijkheden toegang om Azure-resources te bekijken zodat ze Risico's kunnen beoordelen en oplossen.
**Details**: verleen beveiligings teams de rol Azure RBAC- [beveiligings lezer](../../role-based-access-control/built-in-roles.md#security-reader) . U kunt de hoofd beheer groep of de segment beheer groep gebruiken, afhankelijk van het bereik van verantwoordelijkheden:

* **Hoofd beheer groep** voor teams die verantwoordelijk zijn voor alle ondernemings resources
* **Segment beheer groep** voor teams met een beperkt bereik (doorgaans door wettelijke of andere grenzen van de organisatie)

**Best Practice**: Ken de juiste machtigingen toe aan beveiligings teams die directe operationele verantwoordelijkheden hebben.
**Details**: Controleer de ingebouwde rollen van Azure voor de juiste roltoewijzing. Als de ingebouwde rollen niet voldoen aan de specifieke behoeften van uw organisatie, kunt u [aangepaste Azure-rollen](../../role-based-access-control/custom-roles.md)maken. Net als bij ingebouwde rollen kunt u aangepaste rollen toewijzen aan gebruikers, groepen en service-principals bij het abonnement, de resource groep en het bereik van de resource.

**Best practices**: Ken Azure Security Center toegang toe aan beveiligings rollen die deze nodig hebben. Met Security Center kunnen beveiligings teams snel Risico's identificeren en oplossen.
**Details**: Voeg beveiligings teams met deze behoeften toe aan de rol van Azure RBAC- [beveiligings beheerder](../../role-based-access-control/built-in-roles.md#security-admin) zodat ze het beveiligings beleid kunnen weer geven, beveiligings status weer geven, beveiligings beleid bewerken, waarschuwingen en aanbevelingen weer geven en waarschuwingen en aanbevelingen negeren. U kunt dit doen met behulp van de hoofd beheer groep of de segment beheer groep, afhankelijk van het bereik van verantwoordelijkheden.

Organisaties die geen gegevens toegangs beheer afdwingen met behulp van mogelijkheden zoals Azure RBAC, kunnen meer bevoegdheden geven dan nodig zijn voor hun gebruikers. Dit kan leiden tot inbreuk op gegevens door gebruikers toe te staan toegang te krijgen tot gegevens typen (bijvoorbeeld een hoge impact op het bedrijf).

## <a name="lower-exposure-of-privileged-accounts"></a>Minder bloot stelling van accounts met bevoegdheden

Het beveiligen van bevoegde toegang is een kritieke eerste stap bij het beveiligen van bedrijfs middelen. Het minimaliseren van het aantal personen die toegang hebben tot beveiligde informatie of resources vermindert de kans dat een kwaadwillende gebruiker toegang krijgt of een geautoriseerde gebruiker per ongeluk een gevoelige bron heeft beïnvloed.

Geprivilegieerde accounts zijn accounts die IT-systemen beheren en beheren. Cyber aanvallers richten zich op deze accounts om toegang te krijgen tot de gegevens en systemen van een organisatie. Voor het beveiligen van bevoegde toegang moet u de accounts en systemen isoleren van het risico dat ze worden blootgesteld aan een kwaadwillende gebruiker.

We raden u aan een plan te ontwikkelen en te volgen om bevoegde toegang te beveiligen tegen Cyber aanvallers. Raadpleeg voor meer informatie over het maken van een gedetailleerd schema voor het beveiligen van identiteiten en toegang die worden beheerd of gerapporteerd in azure AD, Microsoft Azure, Microsoft 365 en andere Cloud Services, de [beveiliging van bevoegde toegang voor hybride en Cloud implementaties in azure AD](../../active-directory/roles/security-planning.md).

Hieronder vindt u een overzicht van de aanbevolen procedures [voor het beveiligen van bevoegde toegang voor hybride en Cloud implementaties in azure AD](../../active-directory/roles/security-planning.md):

**Aanbevolen procedure**: toegang tot geprivilegieerde accounts beheren, beheren en bewaken.   
**Details**: Schakel [Azure AD privileged Identity Management](../../active-directory/roles/security-planning.md)in. Nadat u Privileged Identity Management hebt ingeschakeld, ontvangt u een e-mail bericht met meldingen voor de gewijzigde rol van de toegang. Deze meldingen geven een vroegtijdige waarschuwing wanneer extra gebruikers worden toegevoegd aan rollen met zeer privileged in uw Directory.

**Aanbevolen procedure**: Zorg ervoor dat alle essentiële beheerders accounts worden beheerd in azure AD-accounts.
**Details**: Verwijder alle consumenten accounts uit essentiële beheerders rollen (bijvoorbeeld micro soft-accounts zoals hotmail.com, live.com en Outlook.com).

**Best Practice**: Zorg ervoor dat alle belang rijke beheerders rollen een apart account hebben voor beheer taken om te voor komen dat phishing en andere aanvallen zich kunnen voordoen.
**Details**: Maak een afzonderlijk beheerders account dat de benodigde bevoegdheden voor het uitvoeren van de beheer taken heeft gekregen. Blok keer het gebruik van deze beheerders accounts voor dagelijkse productiviteits Programma's, zoals Microsoft 365 e-mail of een wille keurige webbrowser.

**Best Practice**: accounts identificeren en categoriseren die zich in rollen met een hoge bevoegdheden bevinden.   
**Details**: nadat u Azure AD privileged Identity Management hebt ingeschakeld, bekijkt u de gebruikers die deel uitmaken van de globale beheerder, de beheerder van de bevoegde rol en andere rollen met een zeer goed privilege. Verwijder alle accounts die niet meer nodig zijn voor deze rollen en Categoriseer de overige accounts die aan beheerders rollen zijn toegewezen:

* Individueel toegewezen aan gebruikers met beheerders rechten en kunnen worden gebruikt voor niet-administratieve doel einden (bijvoorbeeld persoonlijk e-mail adres)
* Individueel toegewezen aan gebruikers met beheerders rechten en alleen voor administratieve doel einden worden aangewezen
* Gedeeld door meerdere gebruikers
* Voor scenario's voor nood toegang
* Voor geautomatiseerde scripts
* Voor externe gebruikers

**Best Practice**: implementeer de just-in-time-toegang (JIT) om de belichtings tijd van bevoegdheden verder te verlagen en verbeter uw zicht baarheid in het gebruik van geprivilegieerde accounts.   
**Details**: met Azure AD privileged Identity Management kunt u het volgende doen:

* Beperk gebruikers tot het uitvoeren van de JIT-bevoegdheden.
* Wijs rollen toe voor een verkorte duur met vertrouwen dat de bevoegdheden automatisch worden ingetrokken.

**Aanbevolen procedure**: Definieer ten minste twee nood toegangs accounts.   
**Details**: met behulp van accounts voor toegang tot een nood geval kunnen organisaties bevoorrechte toegang in een bestaande Azure Active Directory omgeving beperken. Deze accounts zijn zeer privileged en worden niet toegewezen aan specifieke personen. Accounts voor toegang in nood gevallen zijn beperkt tot scenario's waarin normale beheerders accounts niet kunnen worden gebruikt. Organisaties moeten het gebruik van het calamiteiten account beperken tot alleen de benodigde hoeveelheid tijd.

Evalueer de accounts die zijn toegewezen of die in aanmerking komen voor de rol van globale beheerder. Als u geen alleen-Cloud accounts met behulp van het `*.onmicrosoft.com` domein (bedoeld voor nood toegang) ziet, maakt u deze. Zie [Beheer accounts voor nood toegang beheren in azure AD](../../active-directory/roles/security-emergency-access.md)voor meer informatie.

**Best Practice**: Stel een "sche glas"-proces in voor een nood geval.
**Details**: Volg de stappen in het [beveiligen van bevoegde toegang voor hybride en Cloud implementaties in azure AD](../../active-directory/roles/security-planning.md).

**Aanbevolen procedure**: vereisen dat alle essentiële beheerders accounts een wacht woord hebben (bij voor keur) of vereisen dat multi-factor Authentication.
**Details**: gebruik de [Microsoft Authenticator-app](../../active-directory/authentication/howto-authentication-passwordless-phone.md) om u aan te melden bij een Azure ad-account zonder een wacht woord te gebruiken. Net als [Windows hello voor bedrijven](/windows/security/identity-protection/hello-for-business/hello-identity-verification)gebruikt de Microsoft Authenticator verificatie op basis van een sleutel om een gebruikers referentie in te scha kelen die is gekoppeld aan een apparaat en gebruikmaakt van biometrische verificatie of een pincode.

Azure AD Multi-Factor Authentication vereisen bij aanmelding voor alle afzonderlijke gebruikers die permanent zijn toegewezen aan een of meer van de Azure AD-beheerders rollen: globale beheerder, bevoegde rol beheerder, Exchange Online beheerder en share point online-beheerder. Schakel [multi-factor Authentication in voor uw beheerders accounts](../../active-directory/authentication/howto-mfa-userstates.md) en zorg ervoor dat het beheerders account van de gebruiker is geregistreerd.

**Aanbevolen procedure**: voor essentiële beheerders accounts beschikt u over een beheer werkstation waar productie taken niet zijn toegestaan (bijvoorbeeld bladeren en e-mail). Hiermee worden uw beheerders accounts beschermd tegen aanvals vectoren die Browse en e-mail gebruiken en het risico van een belang rijk incident aanzienlijk verminderen.
**Details**: gebruik een beheer werkstation. Kies een niveau voor beveiliging van werk station:

- Zeer veilige productiviteits apparaten bieden geavanceerde beveiliging voor surfen en andere productiviteits taken.
- [Privileged Access workstations (paw's)](/windows-server/identity/securing-privileged-access/privileged-access-workstations) bieden een speciaal besturings systeem dat wordt beschermd tegen aanvallen via internet en bedreigings vectoren voor gevoelige taken.

**Aanbevolen procedure**: de inrichting van beheerders accounts ongedaan maken wanneer werk nemers uw organisatie verlaten.
**Details**: Stel een proces in dat beheerders accounts uitschakelt of verwijdert wanneer werk nemers uw organisatie verlaten.

**Best Practice**: beheerders accounts regel matig testen met behulp van de huidige aanvals technieken.
**Details**: gebruik Microsoft 365 aanvals versie of een aanbieding van derden om realistische aanvals scenario's uit te voeren in uw organisatie. Dit kan u helpen kwets bare gebruikers te vinden voordat een echte aanval plaatsvindt.

**Aanbevolen procedure**: Neem stappen om de meest gebruikte aanvals technieken te verhelpen.  
**Detail**: [micro soft-accounts in beheerders rollen identificeren die moeten worden overgeschakeld naar werk-of school accounts](../../active-directory/roles/security-planning.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Zorg ervoor dat afzonderlijke gebruikers accounts en e-mail berichten worden doorgestuurd voor globale beheerders accounts](../../active-directory/roles/security-planning.md)  

[Zorg ervoor dat de wacht woorden van de beheerders accounts onlangs zijn gewijzigd](../../active-directory/roles/security-planning.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Wachtwoord hash synchronisatie inschakelen](../../active-directory/roles/security-planning.md#turn-on-password-hash-synchronization)  

[Multi-Factor Authentication vereisen voor gebruikers in alle geprivilegieerde rollen en gebruikers die ze kunnen belichten](../../active-directory/roles/security-planning.md#require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users)  

[Uw Microsoft 365 beveiligde score verkrijgen (als u Microsoft 365 gebruikt)](../../active-directory/roles/security-planning.md#obtain-your-microsoft-365-secure-score-if-using-microsoft-365)  

[Raadpleeg de Microsoft 365 Security Guidance (als u Microsoft 365 gebruikt)](../../active-directory/roles/security-planning.md#review-the-microsoft-365-security-and-compliance-guidance-if-using-microsoft-365)  

[Bewaking van Microsoft 365 activiteiten configureren (als u Microsoft 365 gebruikt)](../../active-directory/roles/security-planning.md#configure-microsoft-365-activity-monitoring-if-using-microsoft-365)  

[Eigen aren van incidenten/nood-antwoord plannen vastleggen](../../active-directory/roles/security-planning.md#establish-incidentemergency-response-plan-owners)  

[On-premises geprivilegieerde beheerders accounts beveiligen](../../active-directory/roles/security-planning.md#turn-on-password-hash-synchronization)

Als u geen beveiligde toegang hebt, is het mogelijk dat u te veel gebruikers hebt in zeer beschermde rollen en kwetsbaarder is voor aanvallen. Kwaadwillende actoren, met inbegrip van Cyber aanvallers, vaak doel beheerders accounts en andere elementen van bevoegde toegang om toegang te krijgen tot gevoelige gegevens en systemen door referentie diefstal te gebruiken.

## <a name="control-locations-where-resources-are-created"></a>Controle locaties waar resources worden gemaakt

Het is belang rijk om Cloud operators in te scha kelen om taken uit te voeren en te voor komen dat ze conventies voor het beheren van de resources van uw organisatie verbreken. Organisaties die de locaties willen bepalen waar resources worden gemaakt, moeten deze locaties op de vaste wijze coderen.

U kunt [Azure Resource Manager](../../azure-resource-manager/management/overview.md) gebruiken om beveiligings beleid te maken waarvan de definities de acties of resources beschrijven die specifiek worden geweigerd. U wijst deze beleids definities toe aan het gewenste bereik, zoals het abonnement, de resource groep of een afzonderlijke resource.

> [!NOTE]
> Beveiligings beleid is niet hetzelfde als Azure RBAC. Ze gebruiken echt Azure RBAC om gebruikers te autoriseren om die resources te maken.
>
>

Organisaties die niet bepalen hoe resources worden gemaakt, zijn gevoeliger voor gebruikers die de service kunnen misbruiken door meer resources te maken dan ze nodig hebben. Het proces voor het maken van resources is een belang rijke stap bij het beveiligen van een scenario met meerdere tenants.

## <a name="actively-monitor-for-suspicious-activities"></a>Actief controleren op verdachte activiteiten

Een actief identiteits bewakings systeem kan snel verdacht gedrag detecteren en een waarschuwing activeren voor verder onderzoek. De volgende tabel bevat twee Azure AD-mogelijkheden die organisaties kunnen helpen hun identiteiten te controleren:

**Aanbevolen procedure**: een methode voor het identificeren van:

- Er wordt geprobeerd om u aan te melden [zonder te worden getraceerd](../../active-directory/reports-monitoring/howto-find-activity-reports.md).
- Beveiligings [aanvallen tegen](../../active-directory/reports-monitoring/howto-find-activity-reports.md) een bepaald account.
- Probeert zich aan te melden vanaf meerdere locaties.
- Aanmeldingen vanaf [geïnfecteerde apparaten](../../active-directory/reports-monitoring/howto-find-activity-reports.md).
- Verdachte IP-adressen.

**Details**: gebruik Azure AD Premium [anomalie rapporten](../../active-directory/reports-monitoring/overview-reports.md). Beschikken over processen en procedures voor IT-beheerders om deze rapporten dagelijks of op aanvraag uit te voeren (meestal in een scenario met een incident respons).

**Best Practice**: een actief bewakings systeem hebben dat u op de hoogte stelt van Risico's en het risico niveau (hoog, gemiddeld of laag) aan uw bedrijfs vereisten kan aanpassen.   
**Details**: gebruik [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md), waarmee de huidige Risico's van een eigen dash board worden gemarkeerd en dagelijkse samenvattings meldingen per e-mail worden verzonden. Als u de identiteiten van uw organisatie wilt helpen beveiligen, kunt u op risico gebaseerd beleid configureren dat automatisch reageert op gedetecteerde problemen wanneer een opgegeven risico niveau wordt bereikt.

Organisaties die hun identiteits systemen niet actief bewaken, lopen een risico dat gebruikers referenties worden aangetast. Zonder kennis dat verdachte activiteiten door deze referenties worden uitgevoerd, kunnen organisaties dit type bedreiging niet oplossen.

## <a name="use-azure-ad-for-storage-authentication"></a>Azure AD gebruiken voor opslag verificatie
[Azure Storage](../../storage/common/storage-auth-aad.md) ondersteunt verificatie en autorisatie met Azure AD voor Blob Storage en Queue Storage. Met Azure AD-verificatie kunt u het toegangs beheer op basis van rollen gebruiken om specifieke machtigingen voor gebruikers, groepen en toepassingen te verlenen aan het bereik van een afzonderlijke BLOB-container of-wachtrij.

We raden u [aan Azure ad te gebruiken voor het verifiëren van de toegang tot opslag](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/).

## <a name="next-step"></a>Volgende stap

Zie [Aanbevolen procedures en patronen voor Azure-beveiliging](best-practices-and-patterns.md) voor meer aanbevolen procedures voor beveiliging bij het ontwerpen, implementeren en beheren van uw cloud oplossingen met behulp van Azure.