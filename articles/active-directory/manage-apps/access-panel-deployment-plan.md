---
title: Een Azure Active Directory implementatie van mijn apps plannen
description: Richt lijnen voor het implementeren van Azure Active Directory mijn apps
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/31/2020
ms.author: kenwith
ms.openlocfilehash: 209760b51e0f3ab931fa43ef88406f15c75ac079
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832536"
---
# <a name="plan-an-azure-active-directory-my-apps-deployment"></a>Een Azure Active Directory implementatie van mijn apps plannen

Azure Active Directory (Azure AD) mijn apps is een portal op Internet die helpt ondersteunings kosten te verlagen, de productiviteit en beveiliging te verhogen en de gebruikers frustraties te verminderen. Het systeem bevat gedetailleerde rapporten die bijhouden wanneer u het systeem opent en waarschuwt beheerders van misbruik of misbruik. Zie [de Help van mijn apps-Portal](../user-help/my-apps-portal-end-user-access.md)voor meer informatie over het gebruik van mijn apps vanuit het oogpunt van eind gebruikers.

Met behulp van Azure AD my apps kunt u het volgende doen:

* Ontdek en toegang tot alle Azure AD-verbonden resources van hun bedrijf, zoals toepassingen
* Toegang aanvragen tot nieuwe apps en groepen
* Toegang tot deze resources voor anderen beheren
* Selfservice voor het opnieuw instellen van wacht woorden en Azure AD Multi-Factor Authentication-instellingen beheren
* Hun apparaten beheren

Daarnaast kunnen beheerders het volgende beheren:

* Service voorwaarden
* Organisaties
* Toegangsbeoordelingen


## <a name="benefits-of-azure-ad-my-apps-integration"></a>Voor delen van Azure AD integratie van mijn apps

Azure AD mijn apps voor delen bedrijven op de volgende manieren:

**Biedt intuïtieve gebruikers ervaring**: mijn apps biedt u één platform voor al uw Azure-toepassingen voor eenmalige aanmelding (SSO). U hebt een uniforme portal voor het vinden van bestaande instellingen en nieuwe mogelijkheden, zoals groeps beheer en self-service voor het opnieuw instellen van wacht woorden, wanneer ze worden toegevoegd. De intuïtieve ervaring stelt gebruikers in staat om sneller te werken en productiever te zijn, terwijl ze hun frustratie verminderen.

**Verhoogt de productiviteit**: alle gebruikers toepassingen in mijn apps hebben SSO ingeschakeld. Door EENMALIGe aanmelding in te scha kelen tussen bedrijfs toepassingen en Microsoft 365 wordt een superieure aanmeldings ervaring gemaakt door extra aanmeldings prompts te verminderen of te verwijderen. Mijn apps maakt gebruik van self-service en dynamisch lidmaatschap en verbetert de algehele beveiliging van uw identiteits systeem. Mijn apps zorgt ervoor dat de juiste personen de toegang tot de toepassingen beheren. Mijn apps fungeert als een samenhangende landings pagina voor u om snel resources te vinden en werk taken voort te zetten.

**Beheert kosten**: mijn apps met Azure AD inschakelen, kan u helpen bij het Divestment van on-premises infra structuren. Het vermindert de ondersteunings kosten door u een consistente portal te bieden om al uw apps te vinden, toegang te vragen tot bronnen en accounts te beheren.

**Verhoogt de flexibiliteit en beveiliging**: mijn apps bieden u toegang tot de beveiliging en flexibiliteit die een Cloud platform biedt. Beheerders kunnen instellingen eenvoudig wijzigen in toepassingen en bronnen en kunnen nieuwe beveiligings vereisten bieden zonder dat dit van invloed is op gebruikers.

**Maakt robuuste controle en het bijhouden van het gebruik mogelijk**: bij controle en gebruik bijhouden van alle gebruikers mogelijkheden kunt u zien wanneer gebruikers hun resources gebruiken en ervoor zorgen dat u de beveiliging kunt beoordelen.

### <a name="licensing-considerations"></a>Licentieoverwegingen

Mijn apps is gratis en er zijn geen licenties nodig om op basis niveau te gebruiken. Voor het aantal objecten in uw directory en de aanvullende functies die u wilt implementeren, kunnen echter extra licenties nodig zijn. Enkele algemene scenario's voor Azure AD met licentie vereisten zijn onder andere de volgende beveiligings functies:

* [Azure AD Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)
* [Groepslid maatschap](../fundamentals/active-directory-manage-groups.md)
* [Self-service voor wachtwoord opnieuw instellen](../authentication/tutorial-enable-sspr.md)
* [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md)

Raadpleeg de [volledige licentie handleiding voor Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-azure-ad-my-apps"></a>Vereisten voor het implementeren van Azure AD mijn apps

Voer de volgende vereisten uit voordat u met dit project begint:

* [SSO van de toepassing integreren](./plan-sso-deployment.md)
* [Azure AD-gebruikers-en-groeps infrastructuur beheren](../fundamentals/active-directory-manage-groups.md)

## <a name="plan-azure-ad-my-apps-deployment"></a>Implementatie van Azure AD-apps plannen

De volgende tabel bevat een overzicht van de belangrijkste use cases voor een implementatie van mijn apps:

| Gebied| Beschrijving |
| - | - |
| Toegang| De portal van mijn apps is toegankelijk vanaf zakelijke en persoonlijke apparaten in het bedrijfs netwerk. |
|Toegang | De portal van mijn apps is toegankelijk vanaf zakelijke apparaten buiten het bedrijfs netwerk. |
| Controles| Gebruiks gegevens worden minstens elke 29 dagen in bedrijfs systemen gedownload. |
| Beheer| De levens cyclus van de gebruikers toewijzingen aan toepassingen en groepen die zijn verbonden met Azure AD, wordt gedefinieerd en bewaakt. |
| Beveiliging| Toegang tot bronnen wordt geregeld via gebruikers-en groeps toewijzingen. Alleen geautoriseerde gebruikers kunnen toegang tot resources beheren. |
| Prestaties| De tijd lijnen voor het door geven van toegangs toewijzingen worden gedocumenteerd en gecontroleerd. |
| Gebruikerservaring| Gebruikers zijn op de hoogte van de mogelijkheden van mijn apps en hoe ze deze kunnen gebruiken.|
| Gebruikerservaring| Gebruikers kunnen hun toegang tot toepassingen en groepen beheren.|
| Gebruikerservaring| Gebruikers kunnen hun accounts beheren. |
| Gebruikerservaring| Gebruikers zijn op de hoogte van browser compatibiliteit. |
| Ondersteuning| Gebruikers kunnen ondersteuning voor mijn apps-problemen vinden. |


> [!TIP]
> Mijn apps kunnen worden gebruikt met interne bedrijfs-Url's tijdens het extern gebruik van een toepassings proxy. Zie [zelf studie: een on-premises toepassing toevoegen voor externe toegang via toepassings proxy in azure Active Directory](application-proxy-add-on-premises-application.md)voor meer informatie.

### <a name="best-practices-for-deploying-azure-ad-my-apps"></a>Aanbevolen procedures voor het implementeren van Azure AD mijn apps

De functionaliteit van mijn apps kan geleidelijk worden ingeschakeld. We raden u aan de volgende volg orde van de implementatie te implementeren:

1. Mijn apps
   * Start programma voor apps
   * Self-service app-beheer
   * Integratie van Microsoft 365

1. Detectie van self-service app
   * Self-service voor wachtwoord opnieuw instellen
   * Multi-Factor Authentication instellingen
   * Apparaatbeheer
   * Gebruiksvoorwaarden
   * Organisaties beheren

1. Mijn groepen
   * Groepsbeheer via selfservice
1. Toegangsbeoordelingen
   * Toegangs beoordelings beheer

Vanaf mijn apps introduceert gebruikers de portal als een gemeen schappelijke locatie voor toegang tot resources. Het toevoegen van self-service toepassings detectie bouwt voort op de ervaring mijn apps. Mijn groepen en toegangs beoordelingen zijn gebaseerd op de self-service mogelijkheden.

### <a name="plan-configurations-for-azure-my-apps"></a>Configuraties voor Azure mijn apps plannen

De volgende tabel bevat een lijst met verschillende belang rijke configuraties van mijn apps en de typische waarden die u kunt gebruiken:

| Configuratie| Typische waarden |
| - | - |
| De test groepen bepalen| Identificeer de Azure AD-beveiligings groep die moet worden gebruikt en zorg ervoor dat alle leden van de test groep deel uitmaken. |
| Bepaal welke groep of groepen moeten worden ingeschakeld voor productie.| Identificeer de Azure AD-beveiligings groepen of de Active Directory groepen die zijn gesynchroniseerd met Azure AD om te worden gebruikt. Zorg ervoor dat alle leden van de pilot deel uitmaken van de groep. |
| Gebruikers toestaan om SSO te gebruiken voor bepaalde typen toepassingen| Federatieve SSO, OAuth, wacht woord-SSO, app proxy |
| Gebruikers toestaan self-service voor wachtwoord herstel te gebruiken | Ja |
| Gebruikers toestaan om Multi-Factor Authentication te gebruiken| Ja |
| Gebruikers toestaan om beheer van selfservice groepen te gebruiken voor bepaalde typen groepen| Beveiligings groepen, Microsoft 365 groepen |
| Gebruikers toestaan self-service app-beheer te gebruiken| Ja |
| Gebruikers toestaan om toegangs beoordelingen te gebruiken| Ja |

### <a name="plan-consent-strategy"></a>Strategie voor het plannen van toestemming

Gebruikers of beheerders moeten toestemming geven voor de gebruiks voorwaarden en het privacybeleid van de toepassing. Gebruik, indien mogelijk, uw bedrijfs regels, toestemming van de beheerder, waarmee gebruikers een betere ervaring kunnen krijgen.

Als u toestemming van de beheerder wilt gebruiken, moet u een globale beheerder van de organisatie zijn en moeten de toepassingen:

* Geregistreerd in uw organisatie
* Geregistreerd in een andere Azure AD-organisatie en eerder door ten minste één gebruiker gezonden

Zie [configureren hoe eind gebruikers toestemming geven voor een toepassing in azure Active Directory](configure-user-consent.md)voor meer informatie.

### <a name="engage-the-right-stakeholders"></a>De juiste belanghebbenden benaderen

Wanneer technologie projecten mislukken, worden ze doorgaans door niet-overeenkomende verwachtingen voor impact, resultaten en verantwoordelijkheden. Als u deze problemen wilt voor komen, moet [u ervoor zorgen dat u de juiste belanghebbenden gebruikt](../fundamentals/active-directory-deployment-plans.md) en dat de rol van belanghebbenden in het project goed zijn begrepen.

### <a name="plan-communications"></a>De communicatie plannen

Communicatie is van cruciaal belang voor het slagen van een nieuwe service. Laat uw gebruikers proactief weten hoe en wanneer hun ervaring wordt gewijzigd en hoe u zo nodig ondersteuning krijgt.

Hoewel mijn apps doorgaans geen gebruikers problemen maken, is het belang rijk om voor te bereiden. Maak hand leidingen en een lijst met alle resources voor uw ondersteunings personeel voordat u het start.

#### <a name="communications-templates"></a>Communicatie sjablonen

Micro soft biedt [aanpas bare sjablonen voor e-mails en andere communicatie](https://aka.ms/APTemplates) voor mijn apps. U kunt deze assets aanpassen voor gebruik in andere communicatie kanalen die geschikt zijn voor uw bedrijfs cultuur.

## <a name="plan-your-sso-configuration"></a>Uw SSO-configuratie plannen

Wanneer een gebruiker zich aanmeldt bij een toepassing, gaan ze een verificatie proces en moeten ze bewijzen wie ze zijn. Zonder SSO wordt een wacht woord in de toepassing opgeslagen en moet de gebruiker het wacht woord kennen. Met SSO worden referenties van gebruikers door gegeven aan de toepassing, zodat ze geen wacht woorden hoeven in te voeren voor elke toepassing.

Voor het starten van toepassingen in mijn apps moet SSO zijn ingeschakeld. Azure AD ondersteunt meerdere SSO-opties. Zie [Opties voor eenmalige aanmelding in azure AD](sso-options.md)voor meer informatie.

> [!NOTE]
> Voor meer informatie over het gebruik van Azure AD als een id-provider voor een app raadpleegt u de Quick Start- [serie op toepassings beheer](view-applications-portal.md).

Voor de beste ervaring met de pagina mijn apps begint u met de integratie van Cloud toepassingen die beschikbaar zijn voor federatieve SSO. Met federatieve SSO kunnen gebruikers een consistente ervaring met één klik hebben in hun app-lanceer oppervlakken en zijn ze in het algemeen beter te verstevigen in configuratie beheer.

Gebruik federatieve SSO met Azure AD (OpenID Connect Connect/SAML) wanneer een toepassing dit ondersteunt, in plaats van SSO op basis van wacht woord en ADFS.

Zie voor meer informatie over het implementeren en configureren van uw SaaS-toepassingen het [SaaS SSO-implementatie plan](./plan-sso-deployment.md).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>De implementatie van de browser uitbreiding van mijn apps plannen

Wanneer gebruikers zich aanmelden bij SSO-toepassingen op basis van wacht woorden, moeten ze de uitbrei ding voor beveiligde aanmelding van mijn apps installeren en gebruiken. De uitbrei ding voert een script uit dat het wacht woord verzendt naar het aanmeld formulier van de toepassing. Gebruikers wordt gevraagd de uitbrei ding te installeren wanneer ze de SSO-toepassing op basis van een wacht woord voor het eerst starten. Meer informatie over de uitbrei ding vindt u in deze documentatie over het [installeren van de browser uitbreiding mijn apps]().

Als u SSO-toepassingen op basis van wacht woorden moet integreren, moet u een mechanisme definiëren om de uitbrei ding op schaal te implementeren met [ondersteunde browsers](../user-help/my-apps-portal-end-user-access.md). Mogelijke opties zijn:

* [groepsbeleid voor Internet Explorer]()
* [Configuration Manager voor Internet Explorer](/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
* [Door de gebruiker gestuurde down load en configuratie voor Chrome, Firefox, micro soft Edge of IE](../user-help/my-apps-portal-end-user-access.md)

Gebruikers die geen SSO-toepassingen op basis van wacht woorden gebruiken, profiteren ook van de uitbrei ding. Deze voor delen zijn onder andere de mogelijkheid om apps te starten vanaf de zoek balk, toegang te verkrijgen tot recent gebruikte toepassingen en een koppeling naar de pagina mijn apps te maken.

#### <a name="plan-for-mobile-access"></a>Mobile Access plannen

Een browser die is beveiligd met intune-beleid (micro soft Edge of Intune Managed Browser) is nodig voor mobiele gebruikers die SSO-toepassingen op basis van wacht woorden starten. Met een met beleid beveiligde browser kan de overdracht van het wacht woord dat voor de toepassing is opgeslagen, worden overgedragen. Micro soft Edge of de Managed browser biedt een aantal functies voor het beveiligen van webgegevens. U kunt ook micro soft Edge gebruiken voor zakelijke scenario's op iOS-en Android-apparaten. Micro soft Edge ondersteunt dezelfde beheer scenario's als de Intune Managed Browser en verbetert de gebruikers ervaring. Meer informatie: [webtoegang beheren met een door Microsoft intune-beleid beveiligde browser](/intune/app-configuration-managed-browser).

## <a name="plan-your-my-apps-deployment"></a>Uw implementatie van mijn apps plannen

De basis van mijn apps is de toepassings start Portal, waarmee gebruikers toegang hebben [https://myapps.microsoft.com](https://myapps.microsoft.com/) . De pagina mijn apps biedt gebruikers één plek om hun werk te starten en te profiteren van de benodigde toepassingen. Hier vinden gebruikers een lijst van alle toepassingen waarmee ze toegang hebben tot eenmalige aanmelding. 

> [!NOTE]
> Dezelfde toepassingen worden weer gegeven in het start programma voor apps van Microsoft 365.

Plan de volg orde waarin u toepassingen gaat toevoegen aan het start programma voor apps en beslis of u ze geleidelijk of allemaal tegelijk wilt uitrollen. U doet dit door een toepassings voorraad te maken met het type verificatie en eventuele bestaande SSO-integraties voor elke toepassing.

#### <a name="add-applications-to-the-my-apps-panel"></a>Toepassingen toevoegen aan het deel venster mijn apps

Elke toepassing met Azure AD SSO kan worden toegevoegd aan het start programma voor apps. Andere toepassingen worden toegevoegd met behulp van de optie gekoppelde SSO. U kunt een toepassings tegel configureren die wordt gekoppeld aan de URL van uw bestaande webtoepassing. Met gekoppelde SSO kunt u beginnen met het omleiden van gebruikers naar de portal mijn apps zonder alle toepassingen naar Azure AD SSO te migreren. U kunt geleidelijk overschakelen naar door Azure AD SSO geconfigureerde toepassingen zonder de gebruikers ervaring te onderbreken.

#### <a name="use-my-apps-collections"></a>Mijn apps verzamelingen gebruiken

Standaard worden alle toepassingen op één pagina weer gegeven. U kunt echter verzamelingen gebruiken om gerelateerde toepassingen te groeperen en deze op een afzonderlijk tabblad te presen teren, zodat u ze eenvoudiger kunt vinden. U kunt bijvoorbeeld verzamelingen gebruiken om logische groeperingen van toepassingen te maken voor specifieke taak rollen, taken, projecten, enzovoort. Zie [mijn apps-verzamelingen gebruiken](access-panel-collections.md)voor meer informatie. 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Plannen of mijn apps of een bestaande portal moeten worden gebruikt

Uw gebruikers hebben mogelijk al een andere toepassing of portal dan mijn apps. Als dit het geval is, moet u beslissen of u beide portals wilt ondersteunen of slechts één wilt gebruiken.

Als er al een bestaande portal wordt gebruikt als uitgangs punt voor gebruikers, kunt u de functionaliteit van mijn apps integreren met behulp van Url's voor gebruikers toegang. De functie Url's van gebruikers toegang is een directe koppeling naar de toepassingen die beschikbaar zijn in de portal mijn apps. Deze Url's kunnen worden inge sloten in een bestaande website. Wanneer een gebruiker de koppeling selecteert, wordt de toepassing geopend vanuit de portal mijn apps.

U vindt de eigenschap voor de gebruikers toegangs-URL in het gedeelte **Eigenschappen** van de toepassing in de Azure Portal.

![Een scherm opname van het deel venster apps](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>De detectie en toegang van self-service toepassingen plannen

Zodra een kernset van toepassingen op de pagina mijn apps van een gebruiker is geïmplementeerd, moet u selfservice-app-beheer functies inschakelen. Met self-service app-detectie kunnen gebruikers het volgende doen:

* Nieuwe apps zoeken om toe te voegen aan mijn apps. 
* Voeg optionele apps toe waarvan ze mogelijk niet weten dat ze tijdens de installatie nodig zijn.

Goedkeurings werk stromen zijn beschikbaar voor expliciete goed keuring voor toegang tot toepassingen. Gebruikers die goed keurders zijn, ontvangen meldingen binnen de portal mijn apps wanneer er een aanvraag voor toegang tot de toepassing in behandeling is.

## <a name="plan-self-service-group-membership"></a>Self-service groepslid maatschap plannen 

U kunt gebruikers in staat stellen hun eigen beveiligings groepen of Microsoft 365 groepen te maken en te beheren in azure AD. De eigenaar van de groep kan lidmaatschaps aanvragen goed keuren of weigeren en het beheer van groepslid maatschap delegeren. Self-service groeps beheer functies zijn niet beschikbaar voor beveiligings groepen met e-mail functionaliteit of distributie lijsten.

Als u wilt plannen voor een self-service groepslid maatschap, bepaalt u of u wilt dat alle gebruikers in uw organisatie groepen kunnen maken en beheren of alleen een subset van gebruikers. Als u een subset van gebruikers toestaat, moet u een groep instellen waaraan deze personen worden toegevoegd. Zie [self-service groeps beheer instellen in azure Active Directory](../enterprise-users/groups-self-service-management.md) voor meer informatie over het inschakelen van deze scenario's.

## <a name="plan-reporting-and-auditing"></a>Rapportage en controle plannen

Azure AD biedt [rapporten die technische en zakelijke inzichten bieden](../reports-monitoring/overview-reports.md). Werk samen met de eigen aren van zakelijke en technische toepassingen om het eigendom van deze rapporten te aannemen en ze regel matig te gebruiken. De volgende tabel bevat enkele voor beelden van typische rapportage scenario's.

| Voorbeeld | beheer risico's.| Productiviteit verhogen| Governance en naleving |
|  - |- | - | - |
| Rapporttypen|  Toepassings machtigingen en-gebruik| Activiteit voor het inrichten van accounts| Controleren wie toegang heeft tot de toepassingen |
| Mogelijke acties| Toegang controleren; machtigingen intrekken| Eventuele inrichtings fouten herstellen| Toegang intrekken |

Azure AD houdt de meeste controle gegevens gedurende 30 dagen. De gegevens zijn beschikbaar via de Azure-beheer portal of-API die u kunt downloaden naar uw analyse systemen.

#### <a name="auditing"></a>Controles

Audit logboeken voor toegang tot toepassingen zijn 30 dagen beschikbaar. Als de beveiligings controle binnen uw onderneming een langere retentie vereist, moeten de logboeken worden geëxporteerd naar een SIEM-hulp programma (Security Information Event and Management), zoals Splunk of ArcSight.

Voor controle-, rapportage-en nood herstel back-ups Documenteer de vereiste Download frequentie, het doel systeem en wie verantwoordelijk is voor het beheren van elke back-up. Mogelijk hebt u geen afzonderlijke back-ups van controle en rapportage nodig. Uw back-up voor herstel na nood gevallen moet een afzonderlijke entiteit zijn.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Toepassingen implementeren in het deel venster mijn apps van gebruikers

Nadat een toepassing is geconfigureerd voor SSO, krijgen groepen toegang toegewezen. Gebruikers in de toegewezen groepen hebben toegang en ze zien de toepassing in hun apps en het start programma voor apps van Microsoft 365.

Zie [gebruikers en groepen toewijzen aan een toepassing in Active Directory](./assign-user-or-group-access-portal.md).

Als tijdens het testen of de implementatie de groepen wilt toevoegen, maar nog niet toestaan dat de toepassingen in mijn apps worden weer gegeven, raadpleegt u [een toepassing verbergen van gebruikers ervaring in azure Active Directory](hide-application-from-user-portal.md).

### <a name="deploy-microsoft-365-applications-to-my-apps"></a>Microsoft 365-toepassingen implementeren in mijn apps

Voor Microsoft 365 toepassingen ontvangen gebruikers een exemplaar van Office op basis van licenties die hieraan zijn toegewezen. Een vereiste voor toegang tot Office-toepassingen is dat gebruikers de juiste licenties kunnen toewijzen die zijn gekoppeld aan de Office-toepassingen. Wanneer u een licentie voor een gebruiker toewijst, krijgen ze automatisch de toepassingen te zien die zijn gekoppeld aan de licentie op de pagina mijn apps en in het start programma voor het Microsoft 365 van de app.

Als u een set Office-toepassingen wilt verbergen voor gebruikers, is er een optie om apps te verbergen in de portal mijn apps, terwijl u toch toegang hebt tot de Microsoft 365 Portal. Meer informatie: [een toepassing verbergen voor gebruikers ervaring in azure Active Directory](hide-application-from-user-portal.md).

### <a name="deploy-application-self-service-capabilities"></a>Self-service mogelijkheden voor toepassingen implementeren

Met toegang door selfservice toepassingen kunnen gebruikers zelf toegang krijgen tot toepassingen en deze aanvragen. Gebruikers hebben de vrijheid om toegang te krijgen tot de apps die ze nodig hebben zonder telkens een IT-groep te gebruiken om toegang aan te vragen. Wanneer een gebruiker toegang vraagt en wordt goedgekeurd, hetzij automatisch of hand matig door de eigenaar van een app, worden ze toegevoegd aan een groep aan de back-end. Rapportage is ingeschakeld voor wie toegang heeft aangevraagd, goedgekeurd of verwijderd, en biedt u de controle over het beheer van de toegewezen rollen.

U kunt de goed keuring van aanvragen voor toegang tot een toepassing overdragen aan zakelijke goed keurders. De zakelijke goed keurder kan de app-toegangs wachtwoorden instellen op de pagina mijn apps van de zakelijke goed keurder.

Meer informatie: de [toegang tot selfservice toepassingen gebruiken](access-panel-manage-self-service-access.md).

## <a name="validate-your-deployment"></a>Uw implementatie valideren

Zorg ervoor dat de implementatie van mijn apps uitgebreid is getest en dat er een terugdraai plan is.

De volgende tests moeten worden uitgevoerd met apparaten in bedrijfs eigendom en persoonlijke apparaten. Deze test cases moeten ook overeenkomen met uw zakelijke gebruiks voorbeelden. Hieronder volgen enkele cases die zijn gebaseerd op de voorbeeld vereisten voor het bedrijf in dit document en aan typische technische scenario's. Voeg anderen specifiek toe aan uw behoeften.

#### <a name="application-sso-access-test-case-examples"></a>Case-voor beelden van Application SSO Access testen:


| Business Case| Verwacht resultaat |
| - | -|
| Gebruiker meldt zich aan bij de portal mijn apps| De gebruiker kan zich aanmelden en hun toepassingen bekijken |
| Gebruiker start een federatieve SSO-toepassing| De gebruiker wordt automatisch aangemeld bij de toepassing |
| De gebruiker start een SSO-toepassing voor wacht woord voor de eerste keer| De gebruiker moet de uitbrei ding mijn apps installeren |
| Gebruiker start een SSO-toepassing voor een wacht woord een volgende keer| De gebruiker wordt automatisch aangemeld bij de toepassing |
| Gebruiker start een app vanuit Microsoft 365 Portal| De gebruiker wordt automatisch aangemeld bij de toepassing |
| Gebruiker start een app vanuit de Managed Browser| De gebruiker wordt automatisch aangemeld bij de toepassing |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Case-voor beelden van de self-service voor toepassingen testen

| Business Case| Verwacht resultaat |
| - | - |
| Gebruiker kan lidmaatschap van de toepassing beheren| Gebruiker kan leden toevoegen/verwijderen die toegang hebben tot de app |
| Gebruiker kan de toepassing bewerken| Gebruiker kan de beschrijving en referenties voor wacht woord-SSO-toepassingen bewerken |

### <a name="rollback-steps"></a>Stappen voor ongedaan maken

Het is belang rijk om te plannen wat u moet doen als uw implementatie niet volgens de planning wordt uitgevoerd. Als de SSO-configuratie mislukt tijdens de implementatie, moet u weten hoe u [SSO-problemen oplost](../hybrid/tshoot-connect-sso.md) en de gevolgen voor uw gebruikers vermindert. In uitzonderlijke omstandigheden is het mogelijk om [SSO terug te draaien](../manage-apps/plan-sso-deployment.md#rollback-process).


## <a name="manage-your-implementation"></a>Uw implementatie beheren

Gebruik de rol met de minst privileged om een vereiste taak in Azure Active Directory uit te voeren. [Bekijk de verschillende beschik bare rollen](../roles/permissions-reference.md) en kies de juiste functie om uw behoeften voor elke persoon voor deze toepassing op te lossen. Sommige rollen moeten mogelijk tijdelijk worden toegepast en worden verwijderd nadat de implementatie is voltooid.

| Persona's| Rollen| Azure AD-rol  |
| - | -| -|
| Helpdesk beheerder| Ondersteuning voor laag 1| Geen |
| Identiteits beheerder| Configureren en fouten opsporen wanneer de problemen invloed hebben op Azure AD| Globale beheerder |
| Toepassings beheerder| Gebruikers verklaring in toepassing, configuratie voor gebruikers met machtigingen| Geen |
| Infrastructuur beheerders| Eigenaar certificaat rollover| Globale beheerder |
| Bedrijfs eigenaar/belanghebbende| Gebruikers verklaring in toepassing, configuratie voor gebruikers met machtigingen| Geen |

U kunt [privileged Identity Management](../privileged-identity-management/pim-configure.md) gebruiken om uw rollen te beheren om extra controle, controle en toegangs beoordeling te bieden voor gebruikers met mapmachtigingen.

## <a name="next-steps"></a>Volgende stappen
[Een implementatie van Azure AD Multi-Factor Authentication plannen](../authentication/howto-mfa-getstarted.md)
