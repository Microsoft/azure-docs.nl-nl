---
title: Beveiligings overwegingen voor Azure AD-toepassingsproxy | Microsoft Docs
description: Behandelt beveiligings overwegingen voor het gebruik van Azure AD-toepassingsproxy
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c98bce0be2b456220815a359aae1ee697f3ca2c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254962"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Beveiligings overwegingen voor het extern openen van apps met Azure AD-toepassingsproxy

In dit artikel worden de onderdelen beschreven waarmee u uw gebruikers en toepassingen kunt beveiligen wanneer u Azure Active Directory-toepassingsproxy gebruikt.

In het volgende diagram ziet u hoe Azure AD veilige externe toegang tot uw on-premises toepassingen mogelijk maakt.

 ![Diagram van beveiligde externe toegang via Azure AD-toepassingsproxy](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Beveiligingsvoordelen

Azure AD-toepassingsproxy biedt de volgende beveiligings voordelen:

### <a name="authenticated-access"></a>Geverifieerde toegang 

Als u ervoor kiest Azure Active Directory verificatie vooraf te gebruiken, hebben alleen geverifieerde verbindingen toegang tot uw netwerk.

Azure AD-toepassingsproxy is afhankelijk van de Azure AD Security Token Service (STS) voor alle verificatie.  Met de verificatie vooraf blokkeert een aanzienlijk aantal anonieme aanvallen, omdat alleen geverifieerde identiteiten toegang hebben tot de back-end-toepassing.

Als u passthrough kiest als uw methode voor verificatie vooraf, krijgt u dit voor deel niet. 

### <a name="conditional-access"></a>Voorwaardelijke toegang

Pas de besturings elementen voor uitgebreid beleid toe voordat er verbindingen met uw netwerk tot stand worden gebracht.

Met [voorwaardelijke toegang](../conditional-access/concept-conditional-access-cloud-apps.md)kunt u beperkingen definiëren voor de manier waarop gebruikers toegang krijgen tot uw toepassingen. U kunt beleids regels maken die aanmeldingen beperken op basis van locatie, sterkte van verificatie en gebruikers risico profiel.

U kunt ook voorwaardelijke toegang gebruiken om Multi-Factor Authentication-beleid te configureren en een extra beveiligingslaag toe te voegen aan uw gebruikers verificaties. Daarnaast kunnen uw toepassingen ook worden doorgestuurd naar Microsoft Cloud App Security via voorwaardelijke toegang van Azure AD om realtime bewaking en controles mogelijk te maken via [toegangs](/cloud-app-security/access-policy-aad) -en [sessie](/cloud-app-security/session-policy-aad) beleid

### <a name="traffic-termination"></a>Beëindiging van verkeer

Al het verkeer wordt beëindigd in de Cloud.

Omdat Azure AD-toepassingsproxy een reverse-proxy is, wordt al het verkeer naar back-end-toepassingen beëindigd bij de service. De sessie kan alleen worden hersteld met de back-endserver, wat betekent dat uw back-endservers niet worden blootgesteld aan direct HTTP-verkeer. Deze configuratie betekent dat u beter beschermd bent tegen gerichte aanvallen.

### <a name="all-access-is-outbound"></a>Alle toegang is uitgaand 

U hoeft geen binnenkomende verbindingen met het bedrijfs netwerk te openen.

Application proxy-connectors gebruiken alleen uitgaande verbindingen met de Azure AD-toepassingsproxy-service. Dit betekent dat het niet nodig is om firewall poorten te openen voor binnenkomende verbindingen. Traditionele proxy's vereist een perimeter netwerk (ook wel *DMZ*, *gedemilitariseerde zone* of *gescreend subnet* genoemd) en hebben toegang tot niet-geverifieerde verbindingen op het netwerk. Dit scenario vereist investeringen in Web Application Firewall producten om het verkeer te analyseren en de omgeving te beveiligen. Met toepassings proxy hebt u geen perimeter netwerk nodig omdat alle verbindingen uitgaand zijn en worden uitgevoerd via een beveiligd kanaal.

Zie [Wat is Azure AD-toepassingsproxy-connectors](application-proxy-connectors.md)? voor meer informatie over connectors.

### <a name="cloud-scale-analytics-and-machine-learning"></a>Analyse van Cloud-Scale en machine learning 

Profiteer van geavanceerde beveiliging.

Omdat het deel uitmaakt van Azure Active Directory, kan toepassings proxy gebruikmaken van [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md), met gegevens uit het micro soft Security Response Center en de digitale misdrijven-eenheid. Samen geven we proactief accounts voor gemanipuleerd account en bieden bescherming tegen aanmeldingen met een hoog risico. Er wordt rekening gehouden met talrijke factoren om te bepalen welke aanmeldings pogingen hoog risico lopen. Deze factoren zijn onder andere het markeren van geïnfecteerde apparaten, anoniem netwerken en ongewoone of onwaarschijnlijke locaties.

Veel van deze rapporten en gebeurtenissen zijn al beschikbaar via een API voor integratie met uw SIEM-systemen (Security Information and Event Management).

### <a name="remote-access-as-a-service"></a>Externe toegang als een service

U hoeft zich geen zorgen te maken over het onderhouden en patchen van on-premises servers.

Niet-gepatchte software-accounts voor een groot aantal aanvallen. Azure AD-toepassingsproxy is een Internet-Scale-service die micro soft eigenaar is, zodat u altijd de meest recente beveiligings patches en upgrades krijgt.

Ter verbetering van de beveiliging van toepassingen die zijn gepubliceerd door Azure AD-toepassingsproxy, blok keren we het robots van webcrawlers van het indexeren en archiveren van uw toepassingen. Telkens wanneer een webcrawler robot probeert de robot instellingen voor een gepubliceerde app op te halen, reageert de toepassings proxy met een robots.txt-bestand dat de inhoud bevat `User-agent: * Disallow: /` .

#### <a name="azure-ddos-protection-service"></a>Azure DDoS Protection-Service

Toepassingen die zijn gepubliceerd via toepassings proxy, worden beschermd tegen DDoS-aanvallen (Distributed Denial of service). Deze beveiliging wordt beheerd door micro soft en wordt automatisch ingeschakeld in al onze data centers. De Azure DDoS Protection-Service biedt altijd controle van verkeer en real-time beperking van veelvoorkomende aanvallen op netwerk niveau. 

## <a name="under-the-hood"></a>Onderhuids

Azure AD-toepassingsproxy bestaat uit twee delen:

* De Cloud service: deze service wordt uitgevoerd in Azure en is de plek waar de externe client/gebruikers verbindingen worden gemaakt.
* [De on-premises connector](application-proxy-connectors.md): een on-premises-onderdeel, luistert de connector op aanvragen van de Azure AD-toepassingsproxy-service en verwerkt de verbindingen met de interne toepassingen. 

Er wordt een stroom tussen de connector en de Application proxy-service tot stand gebracht wanneer:

* De connector is voor het eerst ingesteld.
* De connector haalt configuratie gegevens van de Application proxy-service op.
* Een gebruiker heeft toegang tot een gepubliceerde toepassing.

>[!NOTE]
>Alle communicatie vindt plaats via TLS en ze zijn altijd afkomstig van de connector voor de toepassings proxy service. De service is alleen uitgaand.

De connector gebruikt een client certificaat voor het verifiëren van de toepassings proxy service voor bijna alle aanroepen. De enige uitzonde ring op dit proces is de eerste stap van Setup, waarbij het client certificaat tot stand is gebracht.

### <a name="installing-the-connector"></a>De connector installeren

Wanneer de connector voor het eerst is ingesteld, worden de volgende stroom gebeurtenissen uitgevoerd:

1. De connector registratie voor de service treedt op als onderdeel van de installatie van de connector. Gebruikers wordt gevraagd hun Azure AD-beheerders referenties in te voeren. Het token dat is verkregen van deze verificatie wordt vervolgens weer gegeven aan de Azure AD-toepassingsproxy-service.
2. De Application proxy-service evalueert het token. Hiermee wordt gecontroleerd of de gebruiker een globale beheerder is in de Tenant. Als de gebruiker geen beheerder is, wordt het proces beëindigd.
3. De connector genereert een aanvraag voor een client certificaat en geeft deze, samen met het token, door aan de Application proxy-service. De service op zijn beurt verifieert het token en ondertekent de aanvraag van de client certificaat.
4. De connector gebruikt het client certificaat voor toekomstige communicatie met de Application proxy-service.
5. De connector voert een initiële pull-aanvraag uit van de systeem configuratie gegevens van de service met behulp van het client certificaat en is nu gereed om aanvragen te nemen.

### <a name="updating-the-configuration-settings"></a>De configuratie-instellingen bijwerken

Wanneer de Application proxy-service de configuratie-instellingen bijwerkt, worden de volgende stroom gebeurtenissen uitgevoerd:

1. De connector maakt verbinding met het configuratie-eind punt binnen de Application proxy-service met behulp van het client certificaat.
2. Nadat het client certificaat is gevalideerd, retourneert de Application proxy-service configuratie gegevens naar de connector (bijvoorbeeld de connector groep waarvan de connector deel moet uitmaken).
3. Als het huidige certificaat meer dan 180 dagen oud is, genereert de connector een nieuwe certificaat aanvraag, die het client certificaat in feite elke 180 dagen bijwerkt.

### <a name="accessing-published-applications"></a>Toegang tot gepubliceerde toepassingen

Wanneer gebruikers toegang krijgen tot een gepubliceerde toepassing, worden de volgende gebeurtenissen uitgevoerd tussen de Application proxy-service en de connector van de toepassings proxy:

1. De service verifieert de gebruiker voor de app
2. De service plaatst een aanvraag in de connector wachtrij
3. Een connector verwerkt de aanvraag uit de wachtrij
4. De connector wacht op een reactie
5. De service streamt gegevens naar de gebruiker

Lees voor meer informatie over wat er in elk van deze stappen gebeurt.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. de service verifieert de gebruiker voor de app

Als u de app hebt geconfigureerd voor het gebruik van passthrough als de methode voor verificatie vooraf, worden de stappen in deze sectie overgeslagen.

Als u de app hebt geconfigureerd om te worden geverifieerd met Azure AD, worden gebruikers omgeleid naar de Azure AD STS om te verifiëren en worden de volgende stappen uitgevoerd:

1. Toepassings proxy controleert de vereisten voor het beleid voor voorwaardelijke toegang voor de specifieke toepassing. Met deze stap zorgt u ervoor dat de gebruiker aan de toepassing is toegewezen. Als verificatie in twee stappen vereist is, vraagt de verificatie procedure de gebruiker om een tweede verificatie methode.

2. Nadat alle controles zijn door gegeven, geeft Azure AD STS een ondertekend token voor de toepassing uit en wordt de gebruiker omgeleid naar de service toepassings proxy.

3. Toepassings proxy controleert of het token is uitgegeven voor de juiste toepassing. Er worden ook andere controles uitgevoerd, zoals controleren of het token is ondertekend door Azure AD en dat het nog steeds binnen het geldige venster valt.

4. Toepassings proxy stelt een versleutelde verificatie cookie in om aan te geven dat verificatie voor de toepassing heeft plaatsgevonden. De cookie bevat een verloop tijds tempel dat is gebaseerd op het token van Azure AD en andere gegevens, zoals de gebruikers naam waarop de verificatie is gebaseerd. De cookie wordt versleuteld met een persoonlijke sleutel die alleen bekend is bij de toepassings proxy service.

5. Toepassings proxy stuurt de gebruiker terug naar de oorspronkelijk aangevraagde URL.

Als een deel van de stappen voor verificatie vooraf mislukt, wordt de aanvraag van de gebruiker geweigerd en wordt de gebruiker een bericht weer gegeven dat de oorzaak van het probleem is.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. de service plaatst een aanvraag in de connector wachtrij

Connectors zorgen ervoor dat een uitgaande verbinding wordt geopend voor de Application proxy-service. Wanneer een aanvraag binnenkomt, wacht de service de aanvraag op een van de open verbindingen voor de connector om op te halen.

De aanvraag bevat items van de toepassing, zoals de aanvraag headers, gegevens van de versleutelde cookie, de gebruiker die de aanvraag maakt en de aanvraag-ID. Hoewel gegevens van de versleutelde cookie met de aanvraag worden verzonden, is de verificatie cookie zelf niet.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. de connector verwerkt de aanvraag uit de wachtrij. 

Op basis van de aanvraag voert toepassings proxy een van de volgende acties uit:

* Als de aanvraag een eenvoudige bewerking is (bijvoorbeeld omdat er geen gegevens zijn in de hoofd tekst van een REST *Get* -aanvraag), maakt de connector een verbinding met de interne doel bron en wordt er gewacht op een reactie.

* Als de aanvraag gegevens bevat die in de hoofd tekst zijn gekoppeld (bijvoorbeeld een REST *post* -bewerking), maakt de connector een uitgaande verbinding door gebruik te maken van het client certificaat voor het toepassings proxy-exemplaar. Hiermee wordt deze verbinding gemaakt om de gegevens aan te vragen en een verbinding met de interne resource te openen. Nadat de aanvraag van de connector is ontvangen, wordt door de Application proxy-service inhoud van de gebruiker geaccepteerd en worden gegevens doorgestuurd naar de connector. De connector stuurt de gegevens op zijn beurt door naar de interne resource.

#### <a name="4-the-connector-waits-for-a-response"></a>4. de connector wacht op een antwoord.

Nadat de aanvraag en verzen ding van alle inhoud aan de back-end is voltooid, wacht de connector op een reactie.

Nadat het een reactie heeft ontvangen, maakt de connector een uitgaande verbinding met de Application proxy-service om de details van de header te retour neren en te beginnen met het streamen van de geretourneerde gegevens.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. de service streamt gegevens naar de gebruiker. 

Hier vindt u een mogelijke verwerking van de toepassing. Als u een toepassings proxy hebt geconfigureerd om kopteksten of Url's in uw toepassing te vertalen, gebeurt die verwerking tijdens deze stap.


## <a name="next-steps"></a>Volgende stappen

[Overwegingen voor de netwerk topologie bij het gebruik van Azure AD-toepassingsproxy](application-proxy-network-topology.md)

[Azure AD-toepassingsproxy-connectors begrijpen](application-proxy-connectors.md)