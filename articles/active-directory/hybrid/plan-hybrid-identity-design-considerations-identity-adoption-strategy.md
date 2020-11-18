---
title: Ontwerp-strategie voor hybride identiteiten Azure | Microsoft Docs
description: Met voorwaardelijk toegangs beheer controleert Azure AD de specifieke voor waarden die u kiest bij het verifiëren van de gebruiker en voordat toegang tot de toepassing wordt toegestaan.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f52e46ff9cab7d3d150af9fd7b4f1c432bec74b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836186"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Een strategie voor het implementeren van een hybride identiteit definiëren
In deze taak definieert u de strategie voor het implementeren van de hybride identiteit voor uw hybride identiteits oplossing om te voldoen aan de bedrijfs vereisten die worden beschreven in:

* [Bedrijfs behoeften bepalen](plan-hybrid-identity-design-considerations-business-needs.md)
* [Vereisten voor adreslijst synchronisatie bepalen](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Vereisten voor multi-factor Authentication bepalen](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Strategie voor bedrijfs behoeften definiëren
De eerste taak is een oplossing voor het bepalen van de bedrijfs behoeften van de organisatie.  Dit kan zeer breed zijn en bereik kneep kan zich voordoen als u niet voorzichtig bent.  Zorg er in het begin voor dat u het eenvoudig kunt plannen voor een ontwerp dat in de toekomst kan worden aangepast en eenvoudiger kan worden gewijzigd.  Of het nu gaat om een eenvoudig ontwerp of een extreem complex, Azure Active Directory is het micro soft Identity-platform dat ondersteuning biedt voor Microsoft 365, micro soft Online Services en Cloud bewuste toepassingen.

## <a name="define-an-integration-strategy"></a>Een integratie strategie definiëren
Micro soft heeft drie belangrijkste integratie scenario's die Cloud-identiteiten, gesynchroniseerde identiteiten en federatieve identiteiten zijn.  U moet een van deze integratie strategieën plannen.  De strategie die u kiest, kan variëren en de beslissingen bij het kiezen van een van de mogelijkheden zijn, welk type gebruikers ervaring u wilt bieden, hebt u een bestaande infra structuur en wat is de meeste kosten effectief.  

![integratie scenario's](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

De scenario's die in de bovenstaande afbeelding zijn gedefinieerd, zijn:

* **Cloud-identiteiten**: Dit zijn identiteiten die uitsluitend bestaan in de Cloud.  In het geval van Azure AD zouden ze in de Active Directory van Azure AD zijn opgeslagen.
* **Gesynchroniseerd**: Dit zijn identiteiten die on-premises en in de Cloud aanwezig zijn.  Met Azure AD Connect worden deze gebruikers gemaakt of gekoppeld aan bestaande Azure AD-accounts.  De wacht woord-hash van de gebruiker wordt gesynchroniseerd van de on-premises omgeving naar de cloud in wat wordt een wacht woord-hash genoemd.  Bij het gebruik van gesynchroniseerd het ene voor behoud is dat als een gebruiker is uitgeschakeld in de on-premises omgeving, het kan tot drie uur duren voordat de account status wordt weer gegeven in azure AD.  Dit wordt veroorzaakt door het tijds interval voor synchronisatie.
* **Federatief**: deze identiteiten bestaan zowel on-premises als in de Cloud.  Met Azure AD Connect worden deze gebruikers gemaakt of gekoppeld aan bestaande Azure AD-accounts.  

> [!NOTE]
> Lees [uw on-premises identiteiten integreren met Azure Active Directory](whatis-hybrid-identity.md)voor meer informatie over de synchronisatie opties.
> 
> 

De volgende tabel helpt bij het bepalen van de voor-en nadelen van elk van de volgende strategieën:

| Strategie | Voordelen | Nadelen |
| --- | --- | --- |
| **Cloud-identiteiten** |Eenvoudiger te beheren voor kleine organisaties. <br> Niets om on-premises te installeren. Er is geen extra hardware nodig<br>Eenvoudig uitschakelen als de gebruiker het bedrijf verlaat |Gebruikers moeten zich aanmelden bij het openen van werk belastingen in de Cloud <br> Wacht woorden mogen al dan niet hetzelfde zijn voor Cloud-en on-premises identiteiten |
| **Aanvankelijk** |On-premises wacht woord verifieert on-premises en Cloud directory's <br>Eenvoudiger te beheren voor kleine, middel grote of grootschalige organisaties <br>Gebruikers kunnen eenmalige aanmelding (SSO) voor sommige resources hebben <br> Voorkeurs methode van micro soft voor synchronisatie <br> Eenvoudiger te beheren |Sommige klanten kunnen zich niet terugvinden om hun directory's te synchroniseren met de Cloud voor het specifieke bedrijfs beleid |
| **Federatief** |Gebruikers kunnen eenmalige aanmelding (SSO) hebben <br>Als een gebruiker wordt beëindigd of verlaat, kan het account direct worden uitgeschakeld en kan de toegang worden ingetrokken.<br> Ondersteunt geavanceerde scenario's die niet kunnen worden uitgevoerd met gesynchroniseerd |Meer stappen voor het instellen en configureren <br> Hoger onderhoud <br> Hiervoor is mogelijk extra hardware vereist voor de STS-infra structuur <br> Mogelijk is extra hardware vereist voor de installatie van de Federatie server. Aanvullende software is vereist als AD FS wordt gebruikt <br> Uitgebreide installatie vereisen voor SSO <br> Kritiek risico punt als de Federatie server niet beschikbaar is, kunnen gebruikers geen verificatie uitvoeren |

### <a name="client-experience"></a>Clientervaring
De strategie die u gebruikt, bepaalt de aanmeldings ervaring van de gebruiker.  In de volgende tabellen vindt u informatie over de gebruikers die hun aanmeld ervaring moeten verwachten.  Niet alle federatieve id-providers ondersteunen SSO in alle scenario's.

**Domain-en privé-netwerk toepassingen**:

| Toepassing | Gesynchroniseerde identiteit | Federatieve identiteit |
| --- | --- | --- |
| Webbrowsers |Verificatie op basis van formulieren |eenmalige aanmelding, soms vereist voor het leveren van organisatie-ID |
| Outlook |Vragen om referenties |Vragen om referenties |
| Skype voor bedrijven (Lync) |Vragen om referenties |eenmalige aanmelding voor Lync, aanmeldings gegevens voor Exchange |
| OneDrive voor Bedrijven |Vragen om referenties |eenmalige aanmelding |
| Office Pro Plus-abonnement |Vragen om referenties |eenmalige aanmelding |

**Externe of niet-vertrouwde bronnen**:

| Toepassing | Gesynchroniseerde identiteit | Federatieve identiteit |
| --- | --- | --- |
| Webbrowsers |Verificatie op basis van formulieren |Verificatie op basis van formulieren |
| Outlook, Skype voor bedrijven (Lync), OneDrive voor bedrijven, Office-abonnement |Vragen om referenties |Vragen om referenties |
| Exchange ActiveSync: |Vragen om referenties |eenmalige aanmelding voor Lync, aanmeldings gegevens voor Exchange |
| Mobiele apps |Vragen om referenties |Vragen om referenties |

Als u hebt vastgesteld van taak 1 dat u een IdP van derden hebt of als u een Federatie met Azure AD wilt gebruiken, moet u rekening houden met de volgende ondersteunde mogelijkheden:

* Een SAML 2,0-provider die compatibel is met het SP-Lite profiel kan verificatie ondersteunen voor Azure AD en de bijbehorende toepassingen
* Biedt ondersteuning voor passieve verificatie, waarmee de verificatie wordt vereenvoudigd voor OWA, SPO, enzovoort.
* Exchange Online-clients kunnen worden ondersteund via het SAML 2,0 Enhanced client profile (ECP)

U moet er ook van op de hoogte zijn van de mogelijkheden die niet beschikbaar zijn:

* Zonder WS-Trust/Federation-ondersteuning worden alle andere actieve clients verbroken
  * Dit betekent dat er geen Lync-client, OneDrive-client, Office-abonnement, Office Mobile vóór Office 2016
* Door overgang van Office naar passieve verificatie kunnen ze zuivere SAML 2,0 ID ondersteunen, maar is de ondersteuning nog steeds op client basis

> [!NOTE]
> Lees voor de meest recente lijst het artikel [Azure AD Federation Compatibility List](how-to-connect-fed-compatibility.md).
> 
> 

## <a name="define-synchronization-strategy"></a>Synchronisatie strategie definiëren
In deze taak definieert u de hulpprogram ma's die worden gebruikt om de on-premises gegevens van de organisatie te synchroniseren met de Cloud en welke topologie u moet gebruiken.  Omdat de meeste organisaties gebruikmaken van Active Directory, is informatie over het gebruik van Azure AD Connect om de bovenstaande vragen te beantwoorden, in sommige details opgenomen.  Voor omgevingen zonder Active Directory is er informatie over het gebruik van FIM 2010 R2 of MIM 2016 om deze strategie te plannen.  Toekomstige releases van Azure AD Connect zullen echter LDAP-directory's ondersteunen, dus afhankelijk van uw tijd lijn, kan deze informatie mogelijk wel worden geholpen.

### <a name="synchronization-tools"></a>Synchronisatie hulpprogramma's
Over de jaren zijn er verschillende synchronisatie hulpprogramma's bevonden en gebruikt voor verschillende scenario's.  Op Azure AD Connect dit moment kunt u kiezen voor alle ondersteunde scenario's.  AAD Sync en DirSync zijn ook nog steeds beschikbaar en kunnen zelfs in uw omgeving aanwezig zijn. 

> [!NOTE]
> Raadpleeg het artikel over het [vergelijken van hulpprogram ma's voor Directory-integratie](plan-hybrid-identity-design-considerations-tools-comparison.md) voor de meest recente informatie over de ondersteunde mogelijkheden van elk hulp programma.  
> 
> 

### <a name="supported-topologies"></a>Ondersteunde topologieën
Bij het definiëren van een synchronisatie strategie moet de gebruikte topologie worden bepaald. Afhankelijk van de informatie die u in stap 2 hebt vastgesteld, kunt u bepalen welke topologie het meest geschikte is voor gebruik. De enkelvoudige forest, één Azure AD-topologie is het meest gebruikelijk en bestaat uit één Active Directory-forest en één exemplaar van Azure AD.  Dit wordt gebruikt in een meerderheid van de scenario's en is de verwachte topologie bij gebruik van Azure AD Connect snelle installatie, zoals wordt weer gegeven in de afbeelding hieronder.

![Ondersteund topologieën ](./media/plan-hybrid-identity-design-considerations/single-forest.png) met één forest het is gebruikelijk voor grote en zelfs kleine organisaties om meerdere forests te hebben, zoals wordt weer gegeven in afbeelding 5.

> [!NOTE]
> Lees de artikel [topologieën voor Azure AD Connect](plan-connect-topologies.md)voor meer informatie over de verschillende on-premises en Azure AD-topologieën met Azure AD Connect Sync.
> 
> 

![topologie met meerdere forests](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

Scenario met meerdere forests

Als dit het geval is, moet de enkelvoudige Azure AD-topologie met meerdere forests worden beschouwd als de volgende items waar zijn:

* Gebruikers hebben slechts één identiteit voor alle forests: in het gedeelte unieke identificatie van gebruikers hieronder wordt dit gedetailleerd beschreven.
* De gebruiker wordt geverifieerd bij het forest waarin hun identiteit zich bevindt
* Het UPN-en bron anker (onveranderbare id) komt van dit forest
* Alle forests zijn toegankelijk via Azure AD Connect. Dit betekent dat het niet nodig is om lid te zijn van een domein en kan worden geplaatst in een DMZ als dit dit vereenvoudigt.
* Gebruikers hebben slechts één postvak
* Het forest dat als host fungeert voor het postvak van een gebruiker heeft de beste gegevens kwaliteit voor kenmerken die zichtbaar zijn in de algemene adres lijst van Exchange (GAL)
* Als er geen postvak is op de gebruiker, kan een van de forests worden gebruikt om deze waarden bij te dragen
* Als u een gekoppeld postvak hebt, is er ook een ander account in een ander forest dat wordt gebruikt om u aan te melden.

> [!NOTE]
> Objecten die voor komen in zowel on-premises als in de Cloud, zijn verbonden via een unieke id. In de context van Directory synchronisatie wordt deze unieke id aangeduid als de source Anchor. In de context van eenmalige aanmelding wordt dit de ImmutableId genoemd. [Ontwerp concepten voor Azure AD Connect](plan-connect-design-concepts.md#sourceanchor) voor meer overwegingen met betrekking tot het gebruik van source Anchor.
> 
> 

Als het bovenstaande niet waar is en u meer dan één actief account of meer dan één postvak hebt, kiest Azure AD Connect er een en wordt het andere abonnement genegeerd.  Als u gekoppelde post vakken hebt, maar geen ander account, worden deze accounts niet geëxporteerd naar Azure AD en is de gebruiker geen lid van een groep.  Dit wijkt af van de manier waarop deze zich in het verleden bevond met DirSync en is van opzet dat deze scenario's met meerdere forests beter worden ondersteund. In de afbeelding hieronder ziet u een scenario met meerdere forests.

![meerdere Azure AD-tenants](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**Multi-forest multiple Azure AD-scenario**

Het is raadzaam om slechts één map in azure AD voor een organisatie te hebben, maar dit wordt wel ondersteund. er wordt een 1:1-relatie tussen een Azure AD Connect synchronisatie server en een Azure AD-adres lijst bewaard.  Voor elk exemplaar van Azure AD hebt u een installatie van Azure AD Connect nodig.  Bovendien is Azure AD per ontwerp geïsoleerd en kunnen gebruikers in één exemplaar van Azure AD geen gebruikers in een ander exemplaar zien.

Het is mogelijk en wordt ondersteund voor het verbinden van een on-premises exemplaar van Active Directory naar meerdere Azure AD-mappen, zoals wordt weer gegeven in de onderstaande afbeelding:

![filteren met één forest](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**Filter scenario met één forest**

Hiervoor moet het volgende waar zijn:

* Azure AD Connect-synchronisatie servers moeten worden geconfigureerd voor filteren, zodat ze elk een verzameling objecten hebben die elkaar uitsluiten.  Dit is bijvoorbeeld het geval door elke server in te richten op een bepaald domein of een organisatie-eenheid.
* Een DNS-domein kan alleen worden geregistreerd in één Azure AD-Directory, zodat de Upn's van de gebruikers in de on-premises AD afzonderlijke naam ruimten moeten gebruiken
* Gebruikers in één exemplaar van Azure AD kunnen alleen gebruikers van hun exemplaar zien.  Ze kunnen geen gebruikers zien in de andere exemplaren
* Alleen een van de Azure AD-directory's kan Exchange hybride inschakelen met de on-premises AD
* Wederzijdse exclusiviteit is ook van toepassing op write-back.  Dit maakt het mogelijk dat sommige write-back-functies niet worden ondersteund met deze topologie, aangezien deze een afzonderlijke on-premises configuratie aannemen.  Dit omvat:
  * Groep write-back met de standaard configuratie
  * Apparaat terugschrijven

Het volgende wordt niet ondersteund en moet niet worden gekozen als implementatie:

* Het is niet mogelijk om meerdere Azure AD Connect synchronisatie servers te verbinden met dezelfde Azure AD-Directory, zelfs als ze zijn geconfigureerd voor het synchroniseren van elkaar wederzijds exclusieve set-objecten
* Het wordt niet ondersteund om dezelfde gebruiker te synchroniseren met meerdere Azure AD-directory's. 
* Het wordt ook niet ondersteund om een configuratie wijziging aan te brengen zodat gebruikers in één Azure AD worden weer gegeven als contact personen in een andere Azure AD-adres lijst. 
* Het wordt ook niet ondersteund om Azure AD Connect synchronisatie te wijzigen om verbinding te maken met meerdere Azure AD-mappen.
* Azure AD-directory's zijn op ontwerp geïsoleerd. Het wordt niet ondersteund om de configuratie van Azure AD Connect Sync te wijzigen voor het lezen van gegevens uit een andere Azure AD-Directory in een poging om een gemeen schappelijke en Unified GAL te maken tussen de directory's. Het wordt ook niet ondersteund voor het exporteren van gebruikers als contact personen naar een andere on-premises AD-server met behulp van Azure AD Connect-synchronisatie.

> [!NOTE]
> Als uw organisatie toestaat dat computers in uw netwerk verbinding maken met internet, worden in dit artikel de eind punten (FQDN-namen, IPv4-en IPv6-adresbereiken) vermeld die u moet opnemen in uw lijst met uitgaande en Internet Explorer-vertrouwde sites van client computers om ervoor te zorgen dat uw computers Microsoft 365 kunnen gebruiken. Lees voor meer informatie [Office 365-url's en IP-](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US)adresbereiken.
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Strategie voor multi-factor Authentication definiëren
In deze taak definieert u de multi-factor Authentication-strategie die moet worden gebruikt.  Azure AD Multi-Factor Authentication is verkrijgbaar in twee verschillende versies.  Een op de cloud gebaseerde en de andere is on-premises gebaseerd op de Azure MFA-server.  Op basis van de evaluatie die u hierboven hebt gedaan, kunt u bepalen welke oplossing het juiste is voor uw strategie.  Gebruik de onderstaande tabel om te bepalen welke ontwerp optie het beste voldoet aan de beveiligings vereisten van uw bedrijf:

Multi-factor design-opties:

| Te beveiligen Asset | MFA in de cloud | MFA on-premises |
| --- | --- | --- |
| Microsoft-apps |ja |ja |
| SaaS-apps in de app-galerie |ja |ja |
| IIS-toepassingen die zijn gepubliceerd via toepassingsproxy van Azure AD |ja |ja |
| IIS-toepassingen die niet zijn gepubliceerd via de Azure AD-app proxy |nee |ja |
| Externe toegang als VPN, RDG |nee |ja |

Hoewel u mogelijk hebt verrekend met een oplossing voor uw strategie, moet u de evaluatie nog steeds gebruiken op de locatie waar uw gebruikers zich bevinden.  Dit kan ertoe leiden dat de oplossing wordt gewijzigd.  Gebruik de onderstaande tabel om u te helpen bij het bepalen van dit:

| Gebruikerslocatie | Voorkeurs ontwerp optie |
| --- | --- |
| Azure Active Directory |Multi-FactorAuthentication in de Cloud |
| Azure AD en on-premises AD dat gebruikmaakt van federatie met AD FS |Beide |
| Azure AD en on-premises AD met Azure AD Connect geen wachtwoord synchronisatie |Beide |
| Azure AD en on-premises met Azure AD Connect met wachtwoord synchronisatie |Beide |
| On-premises AD |Multi-Factor Authentication-server |

> [!NOTE]
> U moet er ook voor zorgen dat de multi-factor Authentication-ontwerp optie die u hebt geselecteerd, ondersteuning biedt voor de functies die nodig zijn voor uw ontwerp.  Voor meer informatie raadpleegt [u de multi-factor security-oplossing voor u](../authentication/concept-mfa-howitworks.md).
> 

## <a name="multi-factor-auth-provider"></a>Multi-factor Authentication-provider
Multi-factor Authentication is standaard beschikbaar voor globale beheerders die een Azure Active Directory Tenant hebben. Als u echter multi-factor Authentication wilt uitbreiden naar al uw gebruikers en/of als u wilt dat uw globale beheerders functies kunnen gebruiken zoals de beheer Portal, aangepaste begroetingen en rapporten, moet u Multi-Factor Authentication provider kopen en configureren.

> [!NOTE]
> U moet er ook voor zorgen dat de multi-factor Authentication-ontwerp optie die u hebt geselecteerd, ondersteuning biedt voor de functies die nodig zijn voor uw ontwerp. 
> 
> 

## <a name="next-steps"></a>Volgende stappen
[Vereisten voor gegevens beveiliging bepalen](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Zie ook
[Overzicht van ontwerp overwegingen](plan-hybrid-identity-design-considerations-overview.md)

