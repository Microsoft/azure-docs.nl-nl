---
title: Een Azure Active Directory-toepassingsproxy-implementatie plannen
description: Een end-to-end-hand leiding voor het plannen van de implementatie van toepassings proxy in uw organisatie
services: active-directory
documentationcenter: azure
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: kenwith
ms.openlocfilehash: 9736e1b71f5e129989aba9a045581bae8a1c5f93
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997542"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Plan een implementatie voor de Azure AD-toepassingsproxy

Azure Active Directory-toepassings proxy (Azure AD) is een veilige en voordelige oplossing voor externe toegang voor on-premises toepassingen. Het biedt een direct overgangs traject voor organisaties ' Cloud First ' om de toegang te beheren tot oudere on-premises toepassingen die nog geen gebruik kunnen maken van moderne protocollen. Zie [Wat is toepassings proxy](./application-proxy.md)? voor aanvullende inleidende informatie.

Toepassings proxy wordt aanbevolen om externe gebruikers toegang te geven tot interne bronnen. Toepassings proxy vervangt de nood zaak van een VPN-of reverse-proxy voor deze toepassingen voor externe toegang. Het is niet bedoeld voor gebruikers die zich in het bedrijfs netwerk bevinden. Deze gebruikers die toepassings proxy gebruiken voor toegang tot het intranet, kunnen leiden tot ongewenste prestatie problemen.

Dit artikel bevat de resources die u nodig hebt om Azure AD-toepassingsproxy te plannen, te bedienen en te beheren.

## <a name="plan-your-implementation"></a>Uw implementatie plannen

De volgende sectie biedt een breed overzicht van de belangrijkste plannings elementen waarmee u een efficiënte implementatie-ervaring kunt instellen.

### <a name="prerequisites"></a>Vereisten

U moet voldoen aan de volgende vereisten voordat u begint met de implementatie. In deze [zelf studie](application-proxy-add-on-premises-application.md)vindt u meer informatie over het instellen van uw omgeving, inclusief deze vereisten.

* **Connectors**: connectors zijn lichte agents die u kunt implementeren op:
   * Fysieke hardware on-premises
   * Een virtuele machine die wordt gehost in een Hyper Visor-oplossing
   * Een virtuele machine die wordt gehost in azure om uitgaande verbinding met de service toepassings proxy in te scha kelen.

* Zie [Azure AD-App proxy-connectors begrijpen](application-proxy-connectors.md) voor een gedetailleerder overzicht.

     * Connector computers moeten [zijn ingeschakeld voor TLS 1,2](application-proxy-add-on-premises-application.md) voordat u de connectors installeert.

     * Als dat mogelijk is, implementeert u connectors in [hetzelfde netwerk](application-proxy-network-topology.md) en segment als de webtoepassingsserver van de back-end. Het is het beste om connectors te implementeren nadat u een detectie van toepassingen hebt voltooid.
     * U wordt aangeraden elke connector groep ten minste twee connectors te bieden voor hoge Beschik baarheid en schaal. Wanneer u drie connectors hebt, is het mogelijk dat u op elk gewenst moment een machine moet onderhouden. Bekijk de [capaciteits tabel](./application-proxy-connectors.md#capacity-planning) van de connector om te helpen bepalen van welk type computer connectors moeten worden geïnstalleerd. Hoe groter de computer, hoe meer de buffer en de uitvoering van de connector.

* **Instellingen voor netwerk toegang**: Azure AD-toepassingsproxy-connectors [maken verbinding met Azure via HTTPS (tcp-poort 443) en http (tcp-poort 80)](application-proxy-add-on-premises-application.md).

   * Het TLS-verkeer van de connector wordt niet ondersteund en voor komt dat connectors een beveiligd kanaal tot stand brengen met hun respectieve Azure-app-proxy-eind punten.

   * Vermijd alle vormen van inline inspectie op uitgaande TLS-communicatie tussen connectors en Azure. Interne controle tussen een connector en back-end-toepassingen is mogelijk, maar kan de gebruikers ervaring echter wel afnemen en wordt niet aanbevolen.

   * Taak verdeling van de connectors zelf wordt ook niet ondersteund, of zelfs nood zakelijk.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Belang rijke overwegingen voordat u Azure AD-toepassingsproxy configureert

Als u Azure AD-toepassingsproxy wilt configureren en implementeren, moet aan de volgende kern vereisten worden voldaan.

*  **Azure-onboarding**: voordat u een toepassings proxy implementeert, moeten gebruikers identiteiten worden gesynchroniseerd vanuit een on-premises Directory of rechtstreeks worden gemaakt in uw Azure AD-tenants. Dankzij identiteitssynchronisatie kan Azure AD gebruikers vooraf verifiëren alvorens hen toegang tot gepubliceerde Application Proxy-toepassingen te verlenen, en over de benodigde gebruikers-id-gegevens beschikken om eenmalige aanmelding (SSO) uit te voeren.

* **Vereisten voor voorwaardelijke toegang**: we raden u aan toepassings proxy niet te gebruiken voor toegang tot het intranet, omdat hierdoor latentie wordt toegevoegd die van invloed is op gebruikers. U kunt het beste toepassings proxy gebruiken met pre-authenticatie en beleid voor voorwaardelijke toegang voor externe toegang vanaf het internet.  Een benadering voor het bieden van voorwaardelijke toegang voor intranet gebruik is het moderniseren van toepassingen, zodat deze rechtstreeks kunnen worden geverifieerd met AAD. Raadpleeg [bronnen voor het migreren van toepassingen naar Aad](./migration-resources.md) voor meer informatie.

* **Service limieten**: als u wilt beveiligen tegen het overschrijden van resources door afzonderlijke tenants, zijn er beperkings limieten per toepassing en Tenant ingesteld. Raadpleeg de [Azure AD-service limieten en-beperkingen](../enterprise-users/directory-service-limits-restrictions.md)voor meer informatie over deze limieten. Deze beperkings limieten zijn gebaseerd op een vergelijkend referentie volume dat veel wordt gebruikt en biedt een ruimere buffer voor een meerderheid van de implementaties.

* **Openbaar certificaat**: als u aangepaste domein namen gebruikt, moet u een TLS/SSL-certificaat aanschaffen. Afhankelijk van de vereisten van uw organisatie kan het ophalen van een certificaat enige tijd duren. het is raadzaam om het proces zo snel mogelijk te starten. Azure-toepassing proxy ondersteunt standaard-, [Joker](application-proxy-wildcard.md)-of San-certificaten. Zie [Configure Custom Domains with Azure AD-toepassingsproxy](application-proxy-configure-custom-domain.md)voor meer informatie.

* **Domein vereisten**: eenmalige aanmelding bij uw gepubliceerde toepassingen met Kerberos-beperkte delegering (KCD) vereist dat de server waarop de-connector wordt uitgevoerd en de server met de app lid zijn van het domein en deel uitmaken van hetzelfde domein of vertrouwende domeinen.
Zie [KCD voor eenmalige aanmelding](application-proxy-configure-single-sign-on-with-kcd.md) met toepassings proxy voor gedetailleerde informatie over het onderwerp. De connector service wordt uitgevoerd in de context van het lokale systeem en moet niet worden geconfigureerd voor het gebruik van een aangepaste identiteit.

* **DNS-records voor Url's**

   * Voordat u aangepaste domeinen in de toepassings proxy gebruikt, moet u een CNAME-record in open bare DNS maken, zodat clients de aangepaste gedefinieerde externe URL naar het vooraf gedefinieerde toepassings proxy adres kunnen omzetten. Het is niet mogelijk om een CNAME-record te maken voor een toepassing die gebruikmaakt van een aangepast domein, waardoor externe gebruikers geen verbinding kunnen maken met de toepassing. De stappen die nodig zijn om CNAME-records toe te voegen, kunnen verschillen van de DNS-provider naar de provider, dus lees hoe u [DNS-records en-record sets beheert met behulp van de Azure Portal](../../dns/dns-operations-recordsets-portal.md).

   * Op dezelfde manier moeten connector-hosts de interne URL kunnen omzetten van de toepassingen die worden gepubliceerd.

* **Beheer rechten en-rollen**

   * Voor de installatie van een **connector** zijn lokale beheerders rechten vereist voor de Windows-Server waarop deze wordt geïnstalleerd. Ook moet u Mini maal een rol van *toepassings beheerder* hebben om de connector instantie te verifiëren en registreren bij uw Azure AD-Tenant.

   * Voor het **publiceren en beheren van toepassingen** is de rol *toepassings beheerder* vereist. Toepassings beheerders kunnen alle toepassingen in de map beheren, met inbegrip van registraties, SSO-instellingen, gebruikers-en groeps toewijzingen en licenties, toepassings proxy-instellingen en toestemming. De functie voor het beheren van voorwaardelijke toegang wordt niet door deze service verleend. De rol van de beheerder van de *Cloud toepassing* heeft alle mogelijkheden van de toepassings beheerder, behalve dat het beheer van de toepassings proxy-instellingen niet is toegestaan.

* **Licentie verlening**: toepassings proxy is beschikbaar via een Azure AD Premium-abonnement. Raadpleeg de [pagina met prijzen voor Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) voor een volledige lijst met licentie opties en-functies.

### <a name="application-discovery"></a>Toepassings detectie

Compileer een inventarisatie van alle toepassingen in het bereik die worden gepubliceerd via een toepassings proxy door de volgende informatie te verzamelen:

| Gegevens type| Te verzamelen informatie |
|---|---|
| Servicetype| Bijvoorbeeld: share point, SAP, CRM, aangepaste webtoepassing, API |
| Toepassings platform | Bijvoorbeeld: Windows IIS, Apache op Linux, Tomcat, NGINX |
| Lidmaatschap van domein| Fully Qualified Domain Name van de webserver (FQDN) |
| Toepassings locatie | Waar de webserver of Farm zich in uw infra structuur bevindt |
| Interne toegang | De exacte URL die wordt gebruikt om intern toegang te krijgen tot de toepassing. <br> Welk type taak verdeling is in gebruik als een farm? <br> Hiermee wordt aangegeven of de toepassing inhoud van andere bronnen dan zichzelf tekent.<br> Bepaal of de toepassing over websockets werkt. |
| Externe toegang | De oplossing van de leverancier die de toepassing al aan externe toegang heeft blootgesteld. <br> De URL die u wilt gebruiken voor externe toegang. Als share point, moet u ervoor zorgen dat alternatieve toegangs toewijzingen worden geconfigureerd volgens [deze richt lijnen](/SharePoint/administration/configure-alternate-access-mappings). Als dat niet het geval is, moet u externe Url's definiëren. |
| Openbaar certificaat | Als u een aangepast domein gebruikt, moet u een certificaat aanschaffen met een bijbehorende onderwerpnaam. Als er een certificaat bestaat, noteert u het serie nummer en de locatie waarvandaan het kan worden verkregen. |
| Verificatietype| Het verificatie type dat wordt ondersteund door de toepassings ondersteuning, zoals basis, Windows-integratie verificatie, op formulieren gebaseerde, op headers gebaseerde en claims. <br>Als de toepassing is geconfigureerd om te worden uitgevoerd onder een specifiek domein account, noteert u de FQDN-naam (Fully Qualified Domain Name) van het service account.<br> Als op basis van SAML, de id-en antwoord-Url's. <br> Als op de koptekst gebaseerd, de oplossing van de leverancier en de specifieke vereiste voor het verwerken van het verificatie type. |
| Groeps naam connector | De logische naam voor de groep Connect oren die wordt aangewezen om de-connector en de SSO naar deze back-end-toepassing op te geven. |
| Gebruikers/groepen toegang | De gebruikers of gebruikers groepen waaraan externe toegang tot de toepassing wordt verleend. |
| Aanvullende vereisten | Let op eventuele extra externe toegang of beveiligings vereisten die moeten worden opgenomen in de publicatie van de toepassing. |

U kunt dit [werk blad voor de toepassings inventaris](https://aka.ms/appdiscovery) downloaden om uw apps te inventariseren.

### <a name="define-organizational-requirements"></a>Organisatorische vereisten bepalen

Hier volgen enkele gebieden waarvoor u de zakelijke vereisten van uw organisatie moet definiëren. Elk gebied bevat voor beelden van vereisten

 **Toegang**

* Gebruikers van externe gebruikers die lid zijn van een domein of apparaten die lid zijn van Azure AD, kunnen veilig toegang krijgen tot gepubliceerde toepassingen met naadloze eenmalige aanmelding (SSO).

* Externe gebruikers met goedgekeurde persoonlijke apparaten kunnen veilig toegang krijgen tot gepubliceerde toepassingen, mits ze zijn Inge schreven bij MFA en de app Microsoft Authenticator op hun mobiele telefoon als verificatie methode hebben geregistreerd.

**Beheer**

* Beheerders kunnen de levens cyclus van gebruikers toewijzingen definiëren en controleren voor toepassingen die zijn gepubliceerd via toepassings proxy.

**Beveiliging**

* Alleen gebruikers die zijn toegewezen aan toepassingen via groepslid maatschap of afzonderlijk, hebben toegang tot deze toepassingen.

**Prestaties**

* De prestaties van de toepassing worden niet verminderd ten opzichte van de toegang tot de toepassing vanuit het interne netwerk.

**Gebruikers ervaring**

* Gebruikers weten hoe ze toegang krijgen tot hun toepassingen met behulp van vertrouwde bedrijfs-Url's op elk platform.

**Controle**
* Beheerders kunnen de activiteit van de gebruikers toegang controleren.


### <a name="best-practices-for-a-pilot"></a>Aanbevolen procedures voor een pilot

Bepaal de hoeveelheid tijd en inspanningen die nodig zijn om één toepassing voor externe toegang met eenmalige aanmelding (SSO) volledig in te stellen. Dit doet u door een pilot uit te voeren waarin de initiële detectie-, publicatie-en algemene tests worden beschouwd. Door gebruik te maken van een eenvoudige IIS-webtoepassing die al vooraf is geconfigureerd voor geïntegreerde Windows-verificatie (IWA), kunt u een basis lijn instellen, omdat voor deze installatie minimale inspanning vereist is om externe toegang en SSO te testen.

De volgende ontwerp elementen moeten het succes van uw pilot-implementatie rechtstreeks in een productie Tenant verg Roten.

**Connector beheer**:

* Connectors spelen een belang rijke rol bij het leveren van de on-premises-kanaal aan uw toepassingen. Het gebruik van de **standaard** connector groep is voldoende voor de eerste test van gepubliceerde toepassingen voordat deze in productie wordt genomen. Geslaagde toepassingen kunnen vervolgens worden verplaatst naar productie connector groepen.

**Toepassings beheer**:

* Het is waarschijnlijk dat uw werk nemers weet dat een externe URL bekend en relevant is. Vermijd het publiceren van uw toepassing met onze vooraf gedefinieerde msappproxy.net-of onmicrosoft.com-achtervoegsels. Geef in plaats daarvan een vertrouwd, op het hoogste niveau geverifieerd domein op, voorafgegaan door een logische hostnaam zoals *intranet. <customers_domain>. com*.

* Beperk de zicht baarheid van het pictogram van de proef toepassing naar een test groep door het pictogram Start te verbergen in de Azure MyApps-Portal. Wanneer u klaar bent voor de productie, kunt u de app met de doel groep van het doel groeperen, hetzij in dezelfde pre-productie Tenant, hetzij door de toepassing ook in uw productie Tenant te publiceren.

**Instellingen voor eenmalige aanmelding**: sommige SSO-instellingen hebben specifieke afhankelijkheden waarvan het instellen kan duren, dus vermijd het wijzigen van controle vertragingen door te zorgen dat afhankelijkheden vooraf worden verholpen. Dit omvat domein lidmaatschap van connector-hosts om SSO uit te voeren met beperkte Kerberos-delegering (KCD) en te zorgen voor andere tijdrovende activiteiten. U kunt bijvoorbeeld een PING-toegangs exemplaar instellen als u SSO wilt gebruiken die op Koptekst zijn gebaseerd.

**TLS tussen de connector-host en doel toepassing**: beveiliging is van cruciaal belang, zodat TLS tussen de host van de connector en de doel toepassing altijd moet worden gebruikt. Met name als de webtoepassing is geconfigureerd voor verificatie op basis van een formulier (FBA), worden de gebruikers referenties vervolgens effectief verzonden als ongecodeerde tekst.

**Implementeer stapsgewijs en test elke stap**.
Voer na het publiceren van een toepassing elementaire functionele tests uit om ervoor te zorgen dat aan alle gebruikers-en zakelijke vereisten wordt voldaan door de volgende instructies te volgen:

1. Testen en valideren van algemene toegang tot de webtoepassing met vooraf-verificatie uitgeschakeld.
2. Als geslaagde verificatie vooraf is ingeschakeld en gebruikers en groepen worden toegewezen. De toegang testen en valideren.
3. Voeg vervolgens de SSO-methode voor uw toepassing toe en test opnieuw om de toegang te valideren.
4. Pas het beleid voor voorwaardelijke toegang en MFA toe als dat nodig is. De toegang testen en valideren.

**Hulpprogram ma's voor probleem oplossing**: als u problemen wilt oplossen, moet u altijd eerst de toegang tot de gepubliceerde toepassing valideren vanuit de browser op de connector-host en controleren of de toepassing werkt zoals verwacht. Hoe eenvoudiger uw installatie, hoe eenvoudiger het is om de hoofd oorzaak te bepalen. Overweeg daarom om problemen met een minimale configuratie op te lossen, zoals het gebruik van slechts één connector en geen SSO. In sommige gevallen kunnen hulpprogram ma's voor fout opsporing, zoals Telerik van Fiddler, onmisbaar worden bewezen voor het oplossen van problemen met toegang of inhoud in toepassingen die toegankelijk zijn via een proxy. Fiddler kan ook fungeren als proxy voor het traceren en opsporen van fouten in verkeer voor mobiele platforms, zoals iOS en Android, en vrijwel alles wat kan worden geconfigureerd om te routeren via een proxy. Raadpleeg de [hand leiding](application-proxy-troubleshoot.md) voor het oplossen van problemen voor meer informatie.

## <a name="implement-your-solution"></a>Uw oplossing implementeren

### <a name="deploy-application-proxy"></a>Toepassings proxy implementeren

De stappen voor het implementeren van uw toepassings proxy worden behandeld in deze [zelf studie voor het toevoegen van een lokale toepassing voor externe toegang](application-proxy-add-on-premises-application.md). Als de installatie niet is geslaagd, selecteert u problemen met de  **toepassings proxy**  in de portal oplossen of gebruikt u de hand leiding voor het oplossen [van problemen met het installeren van de connector voor de toepassings Proxy agent](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Toepassingen publiceren via een toepassings proxy

Bij het publiceren van toepassingen wordt ervan uitgegaan dat u aan alle vereisten hebt voldaan en dat u verschillende connectors hebt die worden weer gegeven als geregistreerd en actief op de pagina Toepassings proxy.

U kunt ook toepassingen publiceren met behulp van [Power shell](/powershell/module/azuread/?view=azureadps-2.0-preview).

Hieronder volgen enkele aanbevolen procedures voor het publiceren van een toepassing:

* **Connector groepen gebruiken**: wijs een connector groep toe die is aangewezen voor het publiceren van elke desbetreffende toepassing. U wordt aangeraden elke connector groep ten minste twee connectors te bieden voor hoge Beschik baarheid en schaal. Wanneer u drie connectors hebt, is het mogelijk dat u op elk gewenst moment een machine moet onderhouden. Zie ook [toepassingen op verschillende netwerken en locaties publiceren met connector groepen](application-proxy-connector-groups.md) om te zien hoe u connector groepen kunt gebruiken om uw connectors te segmenteren per netwerk of locatie.

* **Time-out voor back-end van toepassing instellen**: deze instelling is handig in scenario's waarin de toepassing mogelijk meer dan 75 seconden nodig heeft om een client transactie te verwerken. Bijvoorbeeld wanneer een client een query verzendt naar een webtoepassing die fungeert als een front-end voor een Data Base. De front-end verzendt deze query naar de back-end-database server en wacht op een reactie, maar op het moment dat het een reactie ontvangt, wordt het client gedeelte van de conversatie een time-out. Als de time-out wordt ingesteld op lang, wordt 180 seconden voor het volt ooien van meer trans acties.

* **Juiste cookie typen gebruiken**

   * **Cookie voor alleen http**: biedt extra beveiliging doordat toepassings proxy de vlag HTTPOnly bevat in set-cookie http-antwoord headers. Deze instelling helpt u bij het oplossen van aanvallen zoals cross-site scripting (XSS). Laat deze set ingesteld op Nee voor clients/gebruikers agents die toegang tot de sessie cookie vereisen. Bijvoorbeeld: RDP/MTSC-client die verbinding maakt met een Extern bureaublad-gateway gepubliceerd via een app-proxy.

   * **Beveiligde cookie**: wanneer een cookie is ingesteld met het kenmerk Secure, wordt in de gebruikers agent (client-side app) alleen de cookie in HTTP-aanvragen vermeld als de aanvraag wordt verzonden via een TLS-beveiligd kanaal. Dit helpt het risico te beperken dat een cookie wordt aangetast via ongecodeerde tekst kanalen, dus moet worden ingeschakeld.

   * **Permanente cookie**: Hiermee kan het toepassings proxy sessie cookie tussen browser sluitingen blijven door geldig totdat het verloopt of is verwijderd. Wordt gebruikt voor scenario's waarbij een uitgebreide toepassing, zoals Office, toegang krijgt tot een document in een gepubliceerde webtoepassing, zonder dat de gebruiker om verificatie wordt gevraagd. Inschakelen met een waarschuwing, omdat permanente cookies uiteindelijk een service voor onbevoegde toegang kunnen verlaten, indien niet in combi natie met andere compenserende controles. Deze instelling mag alleen worden gebruikt voor oudere toepassingen die geen cookies tussen processen kunnen delen. Het is beter om uw toepassing bij te werken voor het afhandelen van het delen van cookies tussen processen in plaats van deze instelling te gebruiken.

* **Url's vertalen in kopteksten**: u schakelt dit in voor scenario's waarbij interne DNS niet kan worden geconfigureerd om te voldoen aan de open bare naam ruimte van de organisatie (a. k. a gesplitste DNS). Tenzij uw toepassing de oorspronkelijke host-header in de client aanvraag vereist, moet u deze waarde instellen op Ja. Het alternatief is dat de connector de FQDN in de interne URL moet gebruiken voor de route ring van het daad werkelijke verkeer, en de FQDN in de externe URL, als de host-header. In de meeste gevallen moet de toepassing als normaal worden gebruikt wanneer deze extern wordt geopend, maar uw gebruikers verliezen de voor delen van een overeenkomst binnen & buiten de URL.

* **Url's vertalen in de hoofd tekst** van de toepassing: Schakel de koppeling van de hoofd tekst van de toepassing in voor een app als u wilt dat de koppelingen van die app worden omgezet in antwoorden naar de client. Als deze functie is ingeschakeld, kunt u de beste poging doen bij het vertalen van alle interne koppelingen die door de app-proxy worden gevonden in HTML-en CSS-antwoorden die naar clients worden geretourneerd. Het is handig bij het publiceren van apps die een absolute of NetBIOS-verkorte koppeling in de inhoud bevatten, of apps met inhoud die is gekoppeld aan andere on-premises toepassingen.

Voor scenario's waarin een gepubliceerde app is gekoppeld aan andere gepubliceerde apps, schakelt u de koppelings vertalingen in voor elke toepassing, zodat u over de controle over de gebruikers ervaring op het niveau per app kunt beschikken.

Stel bijvoorbeeld dat u drie toepassingen hebt gepubliceerd via een toepassings proxy die helemaal aan elkaar is gekoppeld: voor delen, kosten en reizen, plus een vierde app, feedback die niet is gepubliceerd via de toepassings proxy.

![Afbeelding 1](media/App-proxy-deployment-plan/link-translation.png)

Wanneer u de koppelings vertaling voor de voor delen-app inschakelt, worden de koppelingen naar onkosten en reizen omgeleid naar de externe Url's voor die apps, zodat gebruikers die toegang hebben tot de toepassingen van buiten het bedrijfs netwerk toegang hebben tot ze. Koppelingen van onkosten en reizen terug naar voor delen werken niet omdat er geen koppelings conversie is ingeschakeld voor deze twee apps. De koppeling naar de feedback wordt niet omgeleid omdat er geen externe URL is. gebruikers die de voor delen-app gebruiken, hebben geen toegang tot de feedback-app van buiten het bedrijfs netwerk. Zie de gedetailleerde informatie over [koppelings vertalingen en andere Omleidings opties](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Toegang tot uw toepassing

Er zijn verschillende opties voor het beheren van de toegang tot de gepubliceerde bronnen van de app proxy. Kies daarom het beste voor uw specifieke scenario en schaal baarheid. Veelvoorkomende benaderingen zijn: het gebruik van on-premises groepen die worden gesynchroniseerd via Azure AD Connect, het maken van dynamische groepen in azure AD op basis van gebruikers kenmerken, het gebruiken van self-service groepen die worden beheerd door een resource-eigenaar of een combi natie van deze. Bekijk de gekoppelde resources voor de voor delen van elke.

De meest directe manier om gebruikers toegang te verlenen tot een toepassing, wordt in het linkerdeel venster van uw gepubliceerde toepassing opgenomen in de opties **gebruikers en groepen** . vervolgens worden groepen of personen rechtstreeks toegewezen.

![Afbeelding 24](media/App-proxy-deployment-plan/add-user.png)

U kunt gebruikers ook de mogelijkheid bieden om zelf toegang te krijgen tot uw toepassing door een groep toe te wijzen waarvan ze momenteel geen lid zijn en de opties voor zelf onderhoud te configureren.

![Afbeelding 25](media/App-proxy-deployment-plan/allow-access.png)

Als deze optie is ingeschakeld, kunnen gebruikers zich aanmelden bij de MyApps-Portal en toegang aanvragen, en worden ze automatisch goedgekeurd en toegevoegd aan de reeds toegestane selfservice groep of moeten ze goed keuring van een aangewezen goed keurder hebben.

Gast gebruikers kunnen ook worden [uitgenodigd voor toegang tot interne toepassingen die zijn gepubliceerd via een toepassings proxy via Azure AD B2B](../external-identities/add-users-information-worker.md).

Voor on-premises toepassingen die normaal gesp roken anoniem toegankelijk zijn en waarvoor geen verificatie is vereist, kunt u de optie die in de **Eigenschappen** van de toepassing zich bevindt, uitschakelen.

![Afbeelding 26](media/App-proxy-deployment-plan/assignment-required.png)


Als u deze optie instelt op Nee, hebben gebruikers geen toegang tot de on-premises toepassing via Azure AD-app proxy zonder machtigingen, dus wees voorzichtig.

Zodra de toepassing is gepubliceerd, moet deze toegankelijk zijn door de externe URL in een browser of het bijbehorende pictogram in te typen [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

### <a name="enable-pre-authentication"></a>Verificatie vooraf inschakelen

Controleer of uw toepassing toegankelijk is via een toepassings proxy via de externe URL.

1. Navigeer naar **Azure Active Directory**  >  **Enter prise-toepassingen**  >  **alle toepassingen** en kies de app die u wilt beheren.

2. Selecteer **toepassings proxy**.

3. In het veld **Pre-verificatie** gebruikt u de vervolg keuzelijst om **Azure Active Directory** te selecteren en selecteert u **Opslaan**.

Als verificatie vooraf is ingeschakeld, worden gebruikers eerst door Azure AD aangevraagd voor verificatie en als eenmalige aanmelding is geconfigureerd, controleert de back-end-toepassing ook de gebruiker voordat toegang tot de toepassing wordt verleend. Als u de modus vooraf-verificatie van passthrough naar Azure AD wijzigt, wordt ook de externe URL met HTTPS geconfigureerd, zodat alle toepassingen die in eerste instantie zijn geconfigureerd voor HTTP, nu worden beveiligd met HTTPS.

### <a name="enable-single-sign-on"></a>Eén Sign-On inschakelen

SSO biedt de best mogelijke gebruikers ervaring en beveiliging omdat gebruikers zich slechts eenmaal hoeven aan te melden bij het openen van Azure AD. Zodra een gebruiker vooraf is geverifieerd, wordt SSO uitgevoerd door de Application proxy-connector die namens de gebruiker is geverifieerd bij de on-premises toepassing. De back-end-toepassing verwerkt de aanmelding alsof deze de gebruiker zelf is.

Als u de **passthrough** -optie kiest, kunnen gebruikers toegang krijgen tot de gepubliceerde toepassing zonder dat ze zich hoeven te verifiëren bij Azure AD.

Het uitvoeren van SSO is alleen mogelijk als Azure AD de gebruiker kan identificeren die toegang vraagt tot een bron, zodat uw toepassing moet worden geconfigureerd om gebruikers met Azure AD vooraf te verifiëren op het moment dat ze toegang hebben tot SSO, anders worden de SSO-opties uitgeschakeld.

Lees [eenmalige aanmelding bij toepassingen in azure AD](what-is-single-sign-on.md) om u te helpen bij het configureren van uw toepassingen de meest geschikte SSO-methode te kiezen.

###  <a name="working-with-other-types-of-applications"></a>Werken met andere soorten toepassingen

Azure AD-toepassingsproxy kan ook toepassingen ondersteunen die zijn ontwikkeld voor gebruik van de [micro soft Authentication Library (MSAL)](../develop/v2-overview.md). Het ondersteunt native client-apps door Azure AD uitgegeven tokens te gebruiken die worden ontvangen in de header gegevens van de client aanvraag om verificatie vooraf namens de gebruikers uit te voeren.

Lees de [publicatie van systeem eigen en mobiele client-apps](./application-proxy-configure-native-client-application.md) en [op claims gebaseerde toepassingen](./application-proxy-configure-for-claims-aware-applications.md) voor meer informatie over de beschik bare configuraties van de toepassings proxy.

### <a name="use-conditional-access-to-strengthen-security"></a>Voorwaardelijke toegang gebruiken om de beveiliging te versterken

Voor de beveiliging van toepassingen is een geavanceerde set beveiligings mogelijkheden vereist die kunnen worden beveiligd en gereageerd op complexe bedreigingen op locatie en in de Cloud. Aanvallers krijgen meestal toegang tot het bedrijfs netwerk via zwakke, standaard of gestolen gebruikers referenties.  Door micro soft identiteiten gestuurde beveiliging vermindert het gebruik van gestolen referenties door zowel privileged als niet-geautoriseerde identiteiten te beheren en te beveiligen.

De volgende mogelijkheden kunnen worden gebruikt voor de ondersteuning van Azure AD-toepassingsproxy:

* Voorwaardelijke toegang op basis van gebruikers en locaties: behoud gevoelige gegevens die worden beveiligd door de gebruikers toegang te beperken op basis van de geografische locatie of een IP-adres met [op locatie gebaseerd beleid voor voorwaardelijke toegang](../conditional-access/location-condition.md).

* Voorwaardelijke toegang op basis van apparaten: Zorg ervoor dat alleen geregistreerde, goedgekeurde en compatibele apparaten toegang hebben tot Bedrijfs gegevens met [voorwaardelijke toegang op basis van een apparaat](../conditional-access/require-managed-devices.md).

* Voorwaardelijke toegang op basis van toepassingen: werk hoeft niet te worden gestopt wanneer een gebruiker zich niet in het bedrijfs netwerk bevindt. [Veilige toegang tot zakelijke cloud-en on-premises apps](../conditional-access/app-based-conditional-access.md) en behoud de controle met voorwaardelijke toegang.

* Voorwaardelijke toegang op basis van Risico's: Beveilig uw gegevens tegen kwaadwillende hackers met een op [risico gebaseerd beleid voor voorwaardelijke toegang](https://www.microsoft.com/cloud-platform/conditional-access) dat kan worden toegepast op alle apps en alle gebruikers, zowel on-premises als in de Cloud.

* Azure AD mijn apps: als uw Application proxy-service is geïmplementeerd en toepassingen die veilig zijn gepubliceerd, kunt u uw gebruikers een eenvoudige hub bieden om al hun toepassingen te detecteren en gebruiken. Verhoog de productiviteit met selfservice mogelijkheden, zoals de mogelijkheid om toegang aan te vragen bij nieuwe apps en groepen, of de toegang tot deze resources te beheren namens anderen, via [mijn apps](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Uw implementatie beheren

### <a name="required-roles"></a>Vereiste rollen

Micro soft heeft het principe van het verlenen van de minst mogelijke bevoegdheid voor het uitvoeren van de benodigde taken met Azure AD. [Bekijk de verschillende Azure-functies die beschikbaar zijn](../roles/permissions-reference.md) en kies de juiste functie om de behoeften van elke persoon aan te pakken. Sommige rollen moeten mogelijk tijdelijk worden toegepast en worden verwijderd nadat de implementatie is voltooid.

| Zakelijke rol| Zakelijke taken| Azure AD-rollen |
|---|---|---|
| Help Desk-beheerder | Doorgaans beperkt tot het kwalificeren van door eind gebruikers gerapporteerde problemen en het uitvoeren van beperkte taken, zoals het wijzigen van de wacht woorden van gebruikers, het ongeldig vernieuwen van tokens en het controleren van de status van de service. | Helpdesk beheerder |
| Identiteits beheerder| Lees de Azure AD-aanmeldings rapporten en audit Logboeken om problemen met de app-proxy op te lossen.| Beveiligingslezer |
| Eigenaar van de toepassing| Maak en beheer alle aspecten van bedrijfs toepassingen, toepassings registraties en toepassings proxy-instellingen.| Toepassings beheerder |
| Infrastructuur beheerder | Eigenaar certificaat overschakeling | Toepassings beheerder |

Het minimaliseren van het aantal mensen dat toegang heeft tot beveiligde informatie of bronnen helpt bij het verminderen van de kans op een kwaad aardige actor om onbevoegde toegang te verkrijgen of een geautoriseerde gebruiker heeft per ongeluk een gevoelige resource van invloed.

Gebruikers moeten echter wel dagelijkse privileged-bewerkingen uitvoeren, waardoor just-in-time (JIT) op basis van [privileged Identity Management](../privileged-identity-management/pim-configure.md) beleid wordt afgedwongen om op aanvraag bevoegde toegang tot Azure-resources te bieden en Azure AD de aanbevolen benadering is voor het effectief beheren van beheerders toegang en controle.

### <a name="reporting-and-monitoring"></a>Rapportage en bewaking

Azure AD biedt meer inzicht in het toepassings gebruik en de operationele status van uw organisatie via [controle logboeken en-rapporten](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Met toepassings proxy is het ook heel eenvoudig om connectors in de Azure AD-Portal en Windows-gebeurtenis logboeken te bewaken.

#### <a name="application-audit-logs"></a>Auditlogboeken van toepassingen

Deze logboeken bevatten gedetailleerde informatie over aanmeldingen bij toepassingen die zijn geconfigureerd met toepassings proxy en het apparaat en de gebruiker die toegang heeft tot de toepassing. [Audit logboeken](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) bevinden zich in de Azure Portal en in [audit-API](/graph/api/resources/directoryaudit?view=graph-rest-beta) voor export. Daarnaast zijn [gebruiks-en inzichten rapporten](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) ook beschikbaar voor uw toepassing.

#### <a name="application-proxy-connector-monitoring"></a>Bewaking toepassings proxy connector

De connectors en de service zorgen voor alle taken met hoge Beschik baarheid. U kunt de status van uw connectors controleren vanaf de pagina Toepassings proxy in de Azure AD-Portal. Zie [Wat is Azure AD-toepassingsproxy-connectors](./application-proxy-connectors.md#maintenance)? voor meer informatie over connector maintainence.

![Voor beeld: Azure AD-toepassingsproxy-connectors](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Windows-gebeurtenis logboeken en prestatie meter items

Connectors hebben zowel beheer-als sessie Logboeken. De beheerder logboeken bevatten belang rijke gebeurtenissen en fouten. De sessie logboeken bevatten alle trans acties en de bijbehorende verwerkings gegevens. Logboeken en prestatie meter items bevinden zich in de Windows-gebeurtenis logboeken Zie [informatie over Azure AD-toepassingsproxy-connectors](./application-proxy-connectors.md#under-the-hood)voor meer informatie. Volg deze [zelf studie voor het configureren van gegevens bronnen voor gebeurtenis Logboeken in azure monitor](../../azure-monitor/platform/data-sources-windows-events.md).

### <a name="troubleshooting-guide-and-steps"></a>Gids voor probleem oplossing en stappen

Meer informatie over veelvoorkomende problemen en hoe u deze kunt oplossen met onze hand leiding voor het [oplossen](application-proxy-troubleshoot.md) van fout berichten.

De volgende artikelen hebben betrekking op veelvoorkomende scenario's die ook kunnen worden gebruikt om probleemoplossings handleidingen voor uw ondersteunings organisatie te maken.

* [Probleem bij weergeven app-pagina](application-proxy-page-appearance-broken-problem.md)
* [Applicatie laden duurt te lang](application-proxy-page-load-speed-problem.md)
* [Koppelingen op de toepassingspagina werken niet](application-proxy-page-links-broken-problem.md)
* [Welke poorten moet ik openen voor mijn app?](application-proxy-add-on-premises-application.md)
* [Geen werkende connector in een connectorgroep voor mijn app](application-proxy-connectivity-no-working-connector.md)
* [Configureren in beheerportal](application-proxy-config-how-to.md)
* [Eenmalige aanmelding voor mijn app configureren](application-proxy-config-sso-how-to.md)
* [Probleem bij het maken van een app in de beheerportal](application-proxy-config-problem.md)
* [Kerberos-beperkte overdracht configureren](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Configureren met PingAccess](/azure/active-directory/manage-apps/application-proxy-ping-access-publishing-guide)
* [De fout 'Kan deze zakelijke toepassing niet openen'](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Probleem bij het installeren van de connector voor de toepassingsproxyagent](application-proxy-connector-installation-problem.md)
* [Aanmeldingsprobleem](application-sign-in-problem-on-premises-application-proxy.md)