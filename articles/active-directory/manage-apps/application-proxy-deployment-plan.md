---
title: Een implementatie Azure Active Directory toepassingsproxy plannen
description: Een end-to-end handleiding voor het plannen van de implementatie van de toepassingsproxy binnen uw organisatie
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/31/2020
ms.author: kenwith
ms.openlocfilehash: b0a3653d2cc840d745b1bb5788406b8d374c76d0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533780"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Plan een implementatie voor de Azure AD-toepassingsproxy

Azure Active Directory (Azure AD) toepassingsproxy is een veilige en rendabele externe toegangsoplossing voor on-premises toepassingen. Het biedt een onmiddellijke overgangspad voor Cloud First-organisaties voor het beheren van toegang tot verouderde on-premises toepassingen die nog niet geschikt zijn voor het gebruik van moderne protocollen. Zie Wat is toepassingsproxy voor [aanvullende toepassingsproxy.](./application-proxy.md)

toepassingsproxy wordt aanbevolen om externe gebruikers toegang te geven tot interne resources. toepassingsproxy vervangt de noodzaak van een VPN of omgekeerde proxy voor deze gebruiksgevallen voor externe toegang. Het is niet bedoeld voor gebruikers die zich in het bedrijfsnetwerk. Deze gebruikers die toepassingsproxy intranettoegang gebruiken, kunnen ongewenste prestatieproblemen ervaren.

Dit artikel bevat de resources die u nodig hebt voor het plannen, gebruiken en beheren van Azure AD-toepassingsproxy.

## <a name="plan-your-implementation"></a>Uw implementatie plannen

De volgende sectie biedt een breed overzicht van de belangrijkste planningselementen die u kunt instellen voor een efficiënte implementatie-ervaring.

### <a name="prerequisites"></a>Vereisten

U moet voldoen aan de volgende vereisten voordat u begint met de implementatie. In deze zelfstudie vindt u meer informatie over het instellen van uw omgeving, inclusief deze [vereisten.](application-proxy-add-on-premises-application.md)

* **Connectors:** connectors zijn lichtgewicht agents die u kunt implementeren op:
   * Fysieke hardware on-premises
   * Een VM die wordt gehost in een hypervisoroplossing
   * Een VM die wordt gehost in Azure om uitgaande verbindingen met de toepassingsproxy in te stellen.

* Zie [Inzicht in Azure AD-app proxyconnectoren](application-proxy-connectors.md) voor een gedetailleerder overzicht.

     * Connectormachines moeten [zijn ingeschakeld voor TLS 1.2 voordat](application-proxy-add-on-premises-application.md) u de connectors installeert.

     * Implementeer, indien mogelijk, connectors in [hetzelfde netwerk en](application-proxy-network-topology.md) segment als de back-endwebtoepassingsservers. Het is het beste om connectors te implementeren nadat u de detectie van toepassingen hebt voltooid.
     * We raden u aan dat elke connectorgroep ten minste twee connectors heeft om hoge beschikbaarheid en schaal te bieden. Het gebruik van drie connectors is optimaal voor het geval u een machine op elk moment moet voorzien van service. Bekijk de [tabel met connectorcapaciteit om](./application-proxy-connectors.md#capacity-planning) te bepalen op welk type computer connectors moeten worden geïnstalleerd. Hoe groter de machine, hoe meer buffer en hoe beter de connector presteert.

* **Instellingen voor netwerktoegang:** Azure AD toepassingsproxy-connectors maken verbinding met [Azure via HTTPS (TCP-poort 443) en HTTP (TCP-poort 80).](application-proxy-add-on-premises-application.md)

   * TLS-verkeer van de connector wordt niet ondersteund en voorkomt dat connectors een beveiligd kanaal tot stand kunnen Azure-app proxy-eindpunten.

   * Vermijd alle vormen van inlineinspectie bij uitgaande TLS-communicatie tussen connectors en Azure. Interne inspectie tussen een connector en back-endtoepassingen is mogelijk, maar kan de gebruikerservaring verslechteren en wordt daarom niet aanbevolen.

   * Taakverdeling van de connectors zelf wordt ook niet ondersteund of zelfs niet nodig.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Belangrijke overwegingen voordat u Azure AD-toepassingsproxy

Aan de volgende kernvereisten moet worden voldaan om Azure AD-beleid te configureren en toepassingsproxy.

*  **Azure-onboarding:** voordat u een toepassingsproxy implementeert, moeten gebruikersidentiteiten worden gesynchroniseerd vanuit een on-premises directory of rechtstreeks in uw Azure AD-tenants worden gemaakt. Dankzij identiteitssynchronisatie kan Azure AD gebruikers vooraf verifiëren alvorens hen toegang tot gepubliceerde Application Proxy-toepassingen te verlenen, en over de benodigde gebruikers-id-gegevens beschikken om eenmalige aanmelding (SSO) uit te voeren.

* **Vereisten voor voorwaardelijke** toegang: het gebruik van toepassingsproxy voor intranettoegang wordt niet aanbevolen, omdat hierdoor latentie wordt toegevoegd die van invloed is op gebruikers. We raden u aan toepassingsproxy met beleidsregels voor pre-verificatie en voorwaardelijke toegang te gebruiken voor externe toegang vanaf internet.  Een benadering voor het bieden van voorwaardelijke toegang voor intranetgebruik is het moderniseren van toepassingen zodat ze rechtstreeks kunnen worden geverifieerd met AAD. Raadpleeg [Resources voor het migreren van toepassingen naar AAD](./migration-resources.md) voor meer informatie.

* **Servicelimieten:** ter bescherming tegen oververbruik van resources door afzonderlijke tenants zijn er beperkingslimieten ingesteld per toepassing en tenant. Als u deze limieten wilt bekijken, [raadpleegt u Azure AD-servicelimieten en -beperkingen.](../enterprise-users/directory-service-limits-restrictions.md) Deze beperkingslimieten zijn gebaseerd op een benchmark die veel hoger is dan een typisch gebruiksvolume en biedt een ruim buffer voor een meerderheid van de implementaties.

* **Openbaar certificaat:** als u aangepaste domeinnamen gebruikt, moet u een TLS/SSL-certificaat aanschaffen. Afhankelijk van de vereisten van uw organisatie kan het verkrijgen van een certificaat enige tijd duren en wordt u aangeraden het proces zo vroeg mogelijk te starten. Azure-toepassing proxy ondersteunt standaardcertificaten met [jokertekens](application-proxy-wildcard.md)of OP SAN gebaseerde certificaten. Zie Aangepaste domeinen [configureren met Azure AD-toepassingsproxy](application-proxy-configure-custom-domain.md)voor meer toepassingsproxy.

* **Domeinvereisten:** voor een aanmelding bij uw gepubliceerde toepassingen met behulp van KCD (Beperkte Kerberos-delegering) moeten de server met de connector en de server met de app lid zijn van een domein en deel uitmaken van hetzelfde domein of vertrouwende domeinen.
Zie [KCD](application-proxy-configure-single-sign-on-with-kcd.md) voor een toepassingsproxy voor gedetailleerde informatie over toepassingsproxy. De connectorservice wordt uitgevoerd in de context van het lokale systeem en mag niet worden geconfigureerd voor het gebruik van een aangepaste identiteit.

* **DNS-records voor URL's**

   * Voordat u aangepaste domeinen in toepassingsproxy moet u een CNAME-record in openbare DNS maken, zodat clients de aangepaste gedefinieerde externe URL kunnen oplossen naar het vooraf gedefinieerde toepassingsproxy adres. Als er geen CNAME-record wordt gemaakt voor een toepassing die gebruikmaakt van een aangepast domein, kunnen externe gebruikers geen verbinding maken met de toepassing. De stappen die nodig zijn om CNAME-records toe te voegen, kunnen per DNS-provider verschillen. Leer dus hoe u [DNS-records en -recordsets](../../dns/dns-operations-recordsets-portal.md)beheert met behulp van de Azure Portal.

   * Op dezelfde manier moeten connectorhosts de interne URL kunnen oplossen van toepassingen die worden gepubliceerd.

* **Beheerdersrechten en -rollen**

   * **Voor de installatie** van de connector zijn lokale beheerdersrechten vereist voor de Windows-server waar deze wordt geïnstalleerd. Er is ook een minimale rol *toepassingsbeheerder* vereist om het connector-exemplaar te verifiëren en te registreren bij uw Azure AD-tenant.

   * **Voor het publiceren en beheer van toepassingen** is de rol *Toepassingsbeheerder* vereist. Toepassingsbeheerders kunnen alle toepassingen in de directory beheren, waaronder registraties, instellingen voor eenmalige aanmelding, gebruikers- en groepstoewijzingen en licenties, toepassingsproxy-instellingen en toestemming. Het verleent geen mogelijkheid om voorwaardelijke toegang te beheren. De *rol Cloud Application Administrator* heeft alle mogelijkheden van de toepassingsbeheerder, behalve dat het beheer van toepassingsproxy toestaat.

* Licentieverlening: toepassingsproxy is beschikbaar via een Azure AD Premium abonnement. Raadpleeg de pagina [Azure Active Directory prijzen](https://azure.microsoft.com/pricing/details/active-directory/) voor een volledige lijst met licentieopties en -functies.

### <a name="application-discovery"></a>Toepassingsdetectie

Compileer een inventaris van alle toepassingen binnen het bereik die worden gepubliceerd via toepassingsproxy door de volgende informatie te verzamelen:

| Informatietype| Te verzamelen informatie |
|---|---|
| Servicetype| Bijvoorbeeld: SharePoint, SAP, CRM, aangepaste webtoepassing, API |
| Toepassingsplatform | Bijvoorbeeld: Windows IIS, Apache op Linux, Tomcat, NGINX |
| Lidmaatschap van domein| FQDN (Fully Qualified Domain Name) van de webserver |
| Toepassingslocatie | Waar de webserver of farm zich in uw infrastructuur bevindt |
| Interne toegang | De exacte URL die wordt gebruikt bij het intern openen van de toepassing. <br> Welk type taakverdeling wordt er gebruikt als een farm? <br> Hiermee wordt bepaald of de toepassing inhoud uit andere bronnen dan zichzelf haalt.<br> Bepaal of de toepassing via WebSockets werkt. |
| Externe toegang | De leverancieroplossing waar de toepassing al extern aan is blootgesteld. <br> De URL die u wilt gebruiken voor externe toegang. Als SharePoint is ingesteld, moet u ervoor zorgen dat alternatieve toegangstoewijzingen zijn geconfigureerd volgens [deze richtlijnen.](/SharePoint/administration/configure-alternate-access-mappings) Zo niet, dan moet u externe URL's definiëren. |
| Openbaar certificaat | Als u een aangepast domein gebruikt, koopt u een certificaat met een bijbehorende onderwerpnaam aan. Als er een certificaat bestaat, noteer dan het serienummer en de locatie van waar het kan worden verkregen. |
| Verificatietype| Het type verificatie dat wordt ondersteund door de toepassingsondersteuning, zoals Basis, Windows-integratieverificatie, op formulieren gebaseerd, headergebaseerd en claims. <br>Als de toepassing is geconfigureerd om te worden uitgevoerd onder een specifiek domeinaccount, noteert u de Fully Qualified Domain Name (FQDN) van het serviceaccount.<br> Als SAML is gebaseerd, worden de id en antwoord-URL's gebruikt. <br> Als headers zijn gebaseerd, de leverancieroplossing en de specifieke vereiste voor het afhandelen van het verificatietype. |
| Naam connectorgroep | De logische naam voor de groep connectors die wordt aangewezen om de conduit en eenmalige aanmelding aan deze back-endtoepassing te leveren. |
| Toegang tot gebruikers/groepen | De gebruikers of gebruikersgroepen die externe toegang tot de toepassing krijgen. |
| Aanvullende vereisten | Houd rekening met eventuele aanvullende externe toegangs- of beveiligingsvereisten die moeten worden meegenomen bij het publiceren van de toepassing. |

U kunt dit werkblad voor [toepassingsinventarisatie downloaden](https://aka.ms/appdiscovery) om uw apps te inventariseren.

### <a name="define-organizational-requirements"></a>Organisatievereisten definiëren

Hier volgen de gebieden waarvoor u de zakelijke vereisten van uw organisatie moet definiëren. Elk gebied bevat voorbeelden van vereisten

 **Toegang**

* Externe gebruikers met apparaten die lid zijn van een domein of apparaten die zijn verbonden met Azure AD, hebben veilig toegang tot gepubliceerde toepassingen met naadloze eenmalige aanmelding (SSO).

* Externe gebruikers met goedgekeurde persoonlijke apparaten hebben veilig toegang tot gepubliceerde toepassingen, mits ze zijn ingeschreven bij MFA en de Microsoft Authenticator-app op hun mobiele telefoon hebben geregistreerd als verificatiemethode.

**Beheer**

* Beheerders kunnen de levenscyclus van gebruikerstoewijzingen definiëren en bewaken voor toepassingen die zijn gepubliceerd via toepassingsproxy.

**Beveiliging**

* Alleen gebruikers die zijn toegewezen aan toepassingen via groepslidmaatschap of afzonderlijk, hebben toegang tot deze toepassingen.

**Prestaties**

* De prestaties van toepassingen nemen niet af vergeleken met de toegang tot de toepassing vanuit het interne netwerk.

**Gebruikerservaring**

* Gebruikers weten hoe ze toegang kunnen krijgen tot hun toepassingen met behulp van vertrouwde bedrijfs-URL's op elk apparaatplatform.

**Controle**
* Beheerders kunnen activiteiten voor gebruikerstoegang controleren.


### <a name="best-practices-for-a-pilot"></a>Best practices voor een pilot

Bepaal de hoeveelheid tijd en moeite die nodig is om één toepassing volledig in te stellen voor externe toegang met eenmalige aanmelding (SSO). Dit doet u door een pilot uit te voeren die rekening moet houden met de initiële detectie, publicatie en algemene tests. Het gebruik van een eenvoudige webtoepassing op basis van IIS die al vooraf is geconfigureerd voor Geïntegreerde Windows-verificatie (IWA) zou helpen bij het opzetten van een basislijn, omdat voor deze installatie minimale inspanningen nodig zijn om externe toegang en eenmalige aanmelding succesvol te piloten.

De volgende ontwerpelementen moeten het succes van uw testuitvoering rechtstreeks in een productie-tenant vergroten.

**Connectorbeheer:**

* Connectors spelen een belangrijke rol bij het leveren van de on-premises conduit aan uw toepassingen. Het gebruik **van de standaardconnectorgroep** is voldoende voor de eerste testfase van gepubliceerde toepassingen voordat u ze in productie gaat nemen. Geteste toepassingen kunnen vervolgens worden verplaatst naar productieconnectorgroepen.

**Toepassingsbeheer:**

* Uw werknemers zullen waarschijnlijk onthouden dat een externe URL vertrouwd en relevant is. Vermijd het publiceren van uw toepassing met behulp van onze vooraf gedefinieerde msappproxy.net of onmicrosoft.com achtervoegsels. Geef in plaats daarvan een vertrouwd geverifieerd domein op het hoogste niveau op, voorafgegaan door een logische hostnaam zoals *intranet.<customers_domain>.com*.

* Beperk de zichtbaarheid van het pictogram van de testtoepassing tot een testgroep door het startpictogram te verbergen in de Azure MyApps-portal. Wanneer u klaar bent voor productie, kunt u de app richten op de betreffende doelgroep, in dezelfde preproductie-tenant of door de toepassing ook te publiceren in uw productie-tenant.

**Instellingen voor eenmalige** aanmelding: sommige instellingen voor eenmalige aanmelding hebben specifieke afhankelijkheden die enige tijd kunnen duren om in te stellen, dus voorkom vertragingen in het wijzigingsbeheer door ervoor te zorgen dat afhankelijkheden van tevoren worden aangepakt. Dit omvat hosts voor domeinconnector om eenmalige aanmelding uit te voeren met behulp van beperkte Kerberos-delegering (KCD) en om andere tijdrovende activiteiten uit te voeren. Bijvoorbeeld Een ping-toegangs exemplaar instellen, als u eenmalige aanmelding op basis van een header nodig hebt.

**TLS tussen connectorhost en doeltoepassing:** beveiliging is cruciaal, dus TLS tussen de connectorhost en doeltoepassingen moet altijd worden gebruikt. Met name als de webtoepassing is geconfigureerd voor op formulieren gebaseerde verificatie (FBA), omdat gebruikersreferenties vervolgens effectief worden verzonden in duidelijke tekst.

**Implementeert incrementeel en test elke stap**.
Eenvoudige functionele tests uitvoeren na het publiceren van een toepassing om ervoor te zorgen dat aan alle gebruikers- en bedrijfsvereisten wordt voldaan door de onderstaande aanwijzingen te volgen:

1. Test en valideer algemene toegang tot de webtoepassing met pre-verificatie uitgeschakeld.
2. Als dit lukt, kunt u verificatie vooraf inschakelen en gebruikers en groepen toewijzen. Test en valideer de toegang.
3. Voeg vervolgens de SSO-methode voor uw toepassing toe en test opnieuw om de toegang te valideren.
4. Pas waar nodig beleidsregels voor voorwaardelijke toegang en MFA toe. Test en valideer de toegang.

**Hulpprogramma's voor** probleemoplossing: bij het oplossen van problemen begint u altijd met het valideren van toegang tot de gepubliceerde toepassing vanuit de browser op de connectorhost en controleert u of de toepassing werkt zoals verwacht. Hoe eenvoudiger uw installatie is, hoe gemakkelijker de hoofdoorzaak kan worden bepaald. Overweeg daarom om problemen te reproduceren met een minimale configuratie, zoals het gebruik van slechts één connector en geen eenmalige aanmelding. In sommige gevallen kunnen hulpprogramma's voor foutopsporing op het web, zoals Fiddler van Telerik, bewijzen dat ze toegangs- of inhoudsproblemen kunnen oplossen in toepassingen die toegankelijk zijn via een proxy. Fiddler kan ook fungeren als proxy voor het traceren en opsporen van verkeer voor mobiele platforms zoals iOS en Android, en vrijwel alles wat kan worden geconfigureerd om te worden gerouteerd via een proxy. Zie de [gids voor probleemoplossing](application-proxy-troubleshoot.md) voor meer informatie.

## <a name="implement-your-solution"></a>Uw oplossing implementeren

### <a name="deploy-application-proxy"></a>Een toepassingsproxy

De stappen voor het implementeren van toepassingsproxy worden behandeld in deze zelfstudie voor het toevoegen van [een on-premises toepassing voor externe toegang.](application-proxy-add-on-premises-application.md) Als de installatie niet is geslaagd, selecteert u Problemen met toepassingsproxy **oplossen** in de portal of gebruikt u de gids voor probleemoplossing voor problemen met het installeren van [toepassingsproxy agentconnector.](application-proxy-connector-installation-problem.md)

### <a name="publish-applications-via-application-proxy"></a>Toepassingen publiceren via toepassingsproxy

Bij het publiceren van toepassingen wordt ervan uitgenomen dat u aan alle vereisten hebt voldaan en dat er verschillende connectors worden weergegeven als geregistreerd en actief op de toepassingsproxy pagina.

U kunt ook toepassingen publiceren met behulp van [PowerShell.](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true)

Hieronder vindt u enkele best practices die u kunt volgen bij het publiceren van een toepassing:

* **Connectorgroepen gebruiken:** wijs een connectorgroep toe die is aangewezen voor het publiceren van elke respectieve toepassing. We raden u aan dat elke connectorgroep ten minste twee connectors heeft om hoge beschikbaarheid en schaal te bieden. Het gebruik van drie connectors is optimaal voor het geval u een machine op elk moment moet voorzien van service. Zie bovendien Toepassingen publiceren op afzonderlijke netwerken en locaties met behulp van [connectorgroepen](application-proxy-connector-groups.md) om te zien hoe u connectorgroepen ook kunt gebruiken om uw connectors te segmenteren op netwerk of locatie.

* **Time-out voor back-out van back-out** van toepassing instellen: deze instelling is handig in scenario's waarin het verwerken van een clienttransactie voor de toepassing langer dan 75 seconden kan duren. Bijvoorbeeld wanneer een client een query verzendt naar een webtoepassing die fungeert als een front-end voor een database. De front-end verzendt deze query naar de back-enddatabaseserver en wacht op een antwoord, maar tegen de tijd dat deze een antwoord ontvangt, teert de clientzijde van het gesprek een time-out. Als u de time-out instelt op Lang, duurt het 180 seconden voordat langere transacties zijn voltooid.

* **De juiste cookietypen gebruiken**

   * **ALLEEN HTTP-cookie:** biedt extra beveiliging door toepassingsproxy HTTPOnly-vlag op te nemen in HTTP-antwoordheaders voor setcookie. Deze instelling helpt bij het beperken van aanvallen, zoals XSS (Cross-Site Scripting). Laat deze ingesteld op Nee voor clients/gebruikersagents die wel toegang tot de sessiecookie nodig hebben. Bijvoorbeeld RDP/MTSC-client die verbinding maakt met een Extern bureaublad-gateway gepubliceerd via app-proxy.

   * **Beveiligde cookie:** wanneer een cookie is ingesteld met het kenmerk Beveiligd, neemt de gebruikersagent (app aan de clientzijde) de cookie alleen op in HTTP-aanvragen als de aanvraag wordt verzonden via een met TLS beveiligd kanaal. Dit vermindert het risico dat een cookie wordt gecompromitteerd via niet-gecompromitteerde tekstkanalen, dus moet worden ingeschakeld.

   * **Permanente cookie:** hiermee kan toepassingsproxy sessiecookie tussen browsersluitingen blijven bestaan door geldig te blijven totdat deze verloopt of wordt verwijderd. Wordt gebruikt voor scenario's waarin een uitgebreide toepassing, zoals Office, toegang heeft tot een document in een gepubliceerde webtoepassing, zonder dat de gebruiker om verificatie wordt gevraagd. Wees voorzichtig, want permanente cookies kunnen er uiteindelijk voor zorgen dat een service het risico loopt op onbevoegde toegang, als deze niet wordt gebruikt in combinatie met andere compenserende besturingselementen. Deze instelling mag alleen worden gebruikt voor oudere toepassingen die cookies niet kunnen delen tussen processen. Het is beter om uw toepassing bij te werken voor het delen van cookies tussen processen in plaats van deze instelling te gebruiken.

* **URL's vertalen in headers:** u kunt dit inschakelen voor scenario's waarin interne DNS niet kan worden geconfigureerd zodat deze overeenkomen met de openbare naamruimte van de organisatie (ook wel Split DNS genoemd). Laat deze waarde ingesteld op Ja, tenzij uw toepassing de oorspronkelijke hostheader in de clientaanvraag vereist. Het alternatief is om de connector de FQDN in de interne URL te laten gebruiken voor routering van het werkelijke verkeer en de FQDN in de externe URL als host-header. In de meeste gevallen moet dit alternatief ervoor zorgen dat de toepassing normaal werkt wanneer deze extern wordt gebruikt, maar uw gebruikers verliezen de voordelen van een match binnen & externe URL.

* **URL's vertalen in application body:** schakel application body link translation in voor een app wanneer u wilt dat de koppelingen van die app worden vertaald in antwoorden naar de client. Indien ingeschakeld, biedt deze functie een poging om alle interne koppelingen te vertalen die app-proxy vindt in HTML- en CSS-antwoorden die worden geretourneerd naar clients. Dit is handig bij het publiceren van apps die in code gecodeerde absolute of NetBIOS-korte naamkoppelingen in de inhoud bevatten, of apps met inhoud die is koppelingen naar andere on-premises toepassingen.

Voor scenario's waarin een gepubliceerde app is gekoppeld aan andere gepubliceerde apps, moet u voor elke toepassing koppelingsvertaling inschakelen, zodat u controle hebt over de gebruikerservaring op app-niveau.

Stel bijvoorbeeld dat u drie toepassingen hebt gepubliceerd via toepassingsproxy die allemaal met elkaar zijn gekoppeld: Voordelen, Onkosten en Reizen, plus een vierde app, Feedback, die niet is gepubliceerd via toepassingsproxy.

![Afbeelding 1](media/App-proxy-deployment-plan/link-translation.png)

Wanneer u koppelingsomtaling inschakelen voor de Voordelen-app, worden de koppelingen naar Onkosten en reizen omgeleid naar de externe URL's voor die apps, zodat gebruikers die toegang hebben tot de toepassingen van buiten het bedrijfsnetwerk, er toegang toe hebben. Koppelingen van Onkosten en Reizen terug naar Voordelen werken niet omdat koppelingsvertaling niet is ingeschakeld voor deze twee apps. De koppeling naar Feedback wordt niet omgeleid omdat er geen externe URL is, zodat gebruikers die de voordelen-app gebruiken, geen toegang hebben tot de feedback-app van buiten het bedrijfsnetwerk. Zie gedetailleerde informatie over [koppelingsvertalingen en andere omleidingsopties.](application-proxy-configure-hard-coded-link-translation.md)

### <a name="access-your-application"></a>Toegang tot uw toepassing

Er bestaan verschillende opties voor het beheren van de toegang tot gepubliceerde app-proxy-resources, dus kies het meest geschikt voor uw scenario en schaalbaarheidsbehoeften. Veelvoorkomende benaderingen zijn onder andere het gebruik van on-premises groepen die worden gesynchroniseerd via Azure AD Connect, het maken van dynamische groepen in Azure AD op basis van gebruikerskenmerken, het gebruik van selfservicegroepen die worden beheerd door een resource-eigenaar of een combinatie hiervan. Zie de gekoppelde resources voor de voordelen van elk ervan.

De meest directe manier om gebruikers toegang te verlenen  tot een toepassing is door in het linkerdeelvenster van uw gepubliceerde toepassing naar de opties Gebruikers en groepen te gaan en groepen of personen rechtstreeks toe te wijzen.

![Afbeelding 24](media/App-proxy-deployment-plan/add-user.png)

U kunt gebruikers ook selfservicetoegang tot uw toepassing toestaan door een groep toe te wijzen waar ze momenteel geen lid van zijn en de selfserviceopties te configureren.

![Afbeelding 25](media/App-proxy-deployment-plan/allow-access.png)

Als deze functie is ingeschakeld, kunnen gebruikers zich aanmelden bij de MyApps-portal en toegang aanvragen. Ze worden automatisch goedgekeurd en toegevoegd aan de al toegestane selfservicegroep of moeten worden goedgekeurd door een aangewezen fiatteerder.

Gastgebruikers kunnen ook worden uitgenodigd voor toegang tot interne toepassingen die zijn [gepubliceerd via toepassingsproxy via Azure AD B2B.](../external-identities/add-users-information-worker.md)

Voor on-premises toepassingen die normaal gesproken anoniem toegankelijk zijn en waarvoor geen verificatie is vereist, kunt u de optie in eigenschappen van de toepassing **uitschakelen.**

![Afbeelding 26](media/App-proxy-deployment-plan/assignment-required.png)


Als u deze optie op Nee laat staan, hebben gebruikers toegang tot de on-premises toepassing via Azure AD-app Proxy zonder machtigingen, dus wees voorzichtig.

Zodra uw toepassing is gepubliceerd, moet deze toegankelijk zijn door de externe URL in een browser of door het pictogram op te [https://myapps.microsoft.com](https://myapps.microsoft.com/) typen.

### <a name="enable-pre-authentication"></a>Verificatie vooraf inschakelen

Controleer of uw toepassing toegankelijk is via toepassingsproxy via de externe URL.

1. **Navigeer Azure Active Directory**  >    >  **Bedrijfstoepassingen Alle toepassingen en** kies de app die u wilt beheren.

2. Selecteer **toepassingsproxy**.

3. Gebruik in **het veld Pre-Authentication** de vervolgkeuzelijst om Azure Active Directory **te** selecteren en selecteer **Opslaan.**

Als verificatie vooraf is ingeschakeld, vraagt Azure AD gebruikers eerst om verificatie. Als er een aanmelding is geconfigureerd, zal de back-endtoepassing de gebruiker ook verifiëren voordat toegang tot de toepassing wordt verleend. Als u de pre-verificatiemodus van Passthrough in Azure AD verandert, wordt ook de externe URL geconfigureerd met HTTPS, zodat elke toepassing die in eerste instantie is geconfigureerd voor HTTP nu wordt beveiligd met HTTPS.

### <a name="enable-single-sign-on"></a>Eén Sign-On

Eenmalige aanmelding biedt de best mogelijke gebruikerservaring en beveiliging, omdat gebruikers zich slechts één keer hoeven aan te melden bij toegang tot Azure AD. Zodra een gebruiker vooraf is geverifieerd, wordt eenmalige aanmelding namens de gebruiker uitgevoerd door de toepassingsproxy-connector voor verificatie bij de on-premises toepassing. De back-endtoepassing verwerkt de aanmelding alsof het de gebruiker zelf is.

Als u **de optie Passthrough** kiest, hebben gebruikers toegang tot de gepubliceerde toepassing zonder zich te moeten verifiëren bij Azure AD.

Het uitvoeren van eenmalige aanmelding is alleen mogelijk als Azure AD de gebruiker kan identificeren die toegang tot een resource aanvraagt. Uw toepassing moet dus worden geconfigureerd om gebruikers vooraf te verifiëren bij Azure AD bij toegang tot SSO to function, anders worden de opties voor eenmalige aanmelding uitgeschakeld.

Lees [Eenmalige aanmelding voor toepassingen in Azure AD om](what-is-single-sign-on.md) u te helpen de meest geschikte methode voor eenmalige aanmelding te kiezen bij het configureren van uw toepassingen.

###  <a name="working-with-other-types-of-applications"></a>Werken met andere typen toepassingen

Azure AD toepassingsproxy ook toepassingen ondersteunen die zijn ontwikkeld voor het gebruik van [de Microsoft Authentication Library (MSAL).](../develop/v2-overview.md) Het ondersteunt native client-apps door gebruik te maken van door Azure AD uitgegeven tokens die zijn ontvangen in de headerinformatie van clientaanvraag om pre-verificatie uit te voeren namens de gebruikers.

Lees [Het publiceren van systeemeigen en mobiele client-apps](./application-proxy-configure-native-client-application.md) en toepassingen [op](./application-proxy-configure-for-claims-aware-applications.md) basis van claims voor meer informatie over de beschikbare configuraties van toepassingsproxy.

### <a name="use-conditional-access-to-strengthen-security"></a>Voorwaardelijke toegang gebruiken om de beveiliging te versterken

Toepassingsbeveiliging vereist een geavanceerde set beveiligingsmogelijkheden die u kunt beveiligen tegen en reageren op complexe bedreigingen on-premises en in de cloud. Aanvallers krijgen meestal toegang tot het bedrijfsnetwerk via zwakke, standaard- of gestolen gebruikersreferenties.  Identiteitsgestuurde beveiliging van Microsoft vermindert het gebruik van gestolen referenties door zowel bevoegde als niet-bevoegde identiteiten te beheren en te beveiligen.

De volgende mogelijkheden kunnen worden gebruikt ter ondersteuning van Azure AD-toepassingsproxy:

* Voorwaardelijke toegang op basis van gebruiker en locatie: beperk gevoelige gegevens door gebruikerstoegang te beperken op basis van geografische locatie of een [IP-adres](../conditional-access/location-condition.md)met beleid voor voorwaardelijke toegang op basis van locatie.

* Voorwaardelijke toegang op basis van apparaten: Zorg ervoor dat alleen ingeschreven, goedgekeurde en compatibele apparaten toegang hebben tot bedrijfsgegevens met [voorwaardelijke toegang op basis van apparaten.](../conditional-access/require-managed-devices.md)

* Voorwaardelijke toegang op basis van toepassingen: het werk hoeft niet te worden gestopt wanneer een gebruiker zich niet in het bedrijfsnetwerk behoudt. [Beveilig de toegang tot zakelijke cloud- en on-premises apps en](../conditional-access/app-based-conditional-access.md) behoudt de controle met voorwaardelijke toegang.

* Op risico gebaseerde voorwaardelijke toegang: bebeveiligen [](https://www.microsoft.com/cloud-platform/conditional-access) uw gegevens tegen kwaadwillende hackers met een op risico gebaseerd beleid voor voorwaardelijke toegang dat kan worden toegepast op alle apps en alle gebruikers, on-premises of in de cloud.

* Azure AD Mijn apps: nu uw toepassingsproxy-service is geïmplementeerd en toepassingen veilig zijn gepubliceerd, bieden ze uw gebruikers een eenvoudige hub voor het ontdekken en openen van al hun toepassingen. Verhoog de productiviteit met selfservicemogelijkheden, zoals de mogelijkheid om toegang aan te vragen tot nieuwe apps en groepen of toegang tot deze resources namens anderen te beheren via [Mijn apps](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Uw implementatie beheren

### <a name="required-roles"></a>Vereiste rollen

Microsoft staat voor het principe van het verlenen van de minst mogelijke bevoegdheid om de benodigde taken uit te voeren met Azure AD. [Bekijk de verschillende Beschikbare Azure-rollen](../roles/permissions-reference.md) en kies de juiste om te voldoen aan de behoeften van elke persona. Sommige rollen moeten mogelijk tijdelijk worden toegepast en verwijderd nadat de implementatie is voltooid.

| Bedrijfsrol| Zakelijke taken| Azure AD-rollen |
|---|---|---|
| Helpdeskbeheerder | Doorgaans beperkt tot in aanmerking komende eindgebruikers gemelde problemen en het uitvoeren van beperkte taken, zoals het wijzigen van wachtwoorden van gebruikers, het ongeldig maken van vernieuwingstokens en het controleren van de service-status. | Helpdeskbeheerder |
| Identiteitsbeheerder| Lees Azure AD-aanmeldingsrapporten en auditlogboeken om problemen met betrekking tot app-proxy's op te sporen.| Beveiligingslezer |
| Toepassingseigenaar| Maak en beheer alle aspecten van bedrijfstoepassingen, toepassingsregistraties en toepassingsproxy-instellingen.| Toepassingsbeheerder |
| Infrastructuurbeheerder | Eigenaar van certificaatoverover | Toepassingsbeheerder |

Het minimaliseren van het aantal personen dat toegang heeft tot beveiligde informatie of resources, vermindert de kans dat een kwaadwillende gebruiker onbevoegde toegang krijgt of een geautoriseerde gebruiker per ongeluk een gevoelige resource beïnvloedt.

Gebruikers moeten echter nog steeds dagelijkse bevoorrechte bewerkingen uitvoeren, dus just-in-time(JIT) afdwingen op basis van [Privileged Identity Management-beleid](../privileged-identity-management/pim-configure.md) om bevoegde toegang op aanvraag te bieden tot Azure-resources en Azure AD is onze aanbevolen aanpak voor het effectief beheren van beheerderstoegang en -controle.

### <a name="reporting-and-monitoring"></a>Rapportage en bewaking

Azure AD biedt extra inzichten in het gebruik van toepassingen en de operationele status van uw organisatie via [auditlogboeken en -rapporten.](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) toepassingsproxy maakt het ook heel eenvoudig om connectors te bewaken vanuit de Azure AD-portal en Windows-gebeurtenislogboeken.

#### <a name="application-audit-logs"></a>Auditlogboeken van toepassingen

Deze logboeken bevatten gedetailleerde informatie over aanmeldingen bij toepassingen die zijn geconfigureerd met toepassingsproxy en het apparaat en de gebruiker die toegang tot de toepassing hebben. [Auditlogboeken](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) bevinden zich in de Azure Portal en in [audit-API](/graph/api/resources/directoryaudit) voor export. Daarnaast zijn rapporten [over gebruik en inzichten](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) ook beschikbaar voor uw toepassing.

#### <a name="application-proxy-connector-monitoring"></a>toepassingsproxy Connector bewaken

De connectors en de service zorgen voor alle taken voor hoge beschikbaarheid. U kunt de status van uw connectors bewaken vanaf de toepassingsproxy in de Azure AD-portal. Zie Understand Azure AD [toepassingsproxy Connectors (Azure AD toepassingsproxy-connectors) voor meer informatie over het onderhouden van connectors.](./application-proxy-connectors.md#maintenance)

![Voorbeeld: Azure AD toepassingsproxy-connectors](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Windows-gebeurtenislogboeken en prestatiemeters

Connectors hebben zowel beheerders- als sessielogboeken. De beheerderslogboeken bevatten belangrijke gebeurtenissen en hun fouten. De sessielogboeken bevatten alle transacties en de verwerkingsgegevens. Logboeken en tellers bevinden zich in Windows-gebeurtenislogboeken voor meer informatie. Zie Inzicht in [Azure AD toepassingsproxy Connectors.](./application-proxy-connectors.md#under-the-hood) Volg deze [zelfstudie om gegevensbronnen voor gebeurtenislogboek te configureren in Azure Monitor.](../../azure-monitor/agents/data-sources-windows-events.md)

### <a name="troubleshooting-guide-and-steps"></a>Gids voor probleemoplossing en stappen

Lees onze handleiding voor het oplossen van foutberichten voor meer informatie over veelvoorkomende problemen en hoe u deze [kunt](application-proxy-troubleshoot.md) oplossen.

De volgende artikelen hebben betrekking op algemene scenario's die ook kunnen worden gebruikt voor het maken van gidsen voor probleemoplossing voor uw ondersteuningsorganisatie.

* [Probleem bij weergeven app-pagina](application-proxy-page-appearance-broken-problem.md)
* [Applicatie laden duurt te lang](application-proxy-page-load-speed-problem.md)
* [Koppelingen op de toepassingspagina werken niet](application-proxy-page-links-broken-problem.md)
* [Welke poorten moet ik openen voor mijn app?](application-proxy-add-on-premises-application.md)
* [Geen werkende connector in een connectorgroep voor mijn app](application-proxy-connectivity-no-working-connector.md)
* [Configureren in beheerportal](application-proxy-config-how-to.md)
* [Eenmalige aanmelding voor mijn app configureren](application-proxy-config-sso-how-to.md)
* [Probleem bij het maken van een app in de beheerportal](application-proxy-config-problem.md)
* [Kerberos-beperkte overdracht configureren](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Configureren met PingAccess](./application-proxy-ping-access-publishing-guide.md)
* [De fout 'Kan deze zakelijke toepassing niet openen'](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Probleem bij het installeren van de connector voor de toepassingsproxyagent](application-proxy-connector-installation-problem.md)
* [Aanmeldingsprobleem](application-sign-in-problem-on-premises-application-proxy.md)
