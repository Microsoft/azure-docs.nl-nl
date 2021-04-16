---
title: Locatievoorwaarde in Azure Active Directory voorwaardelijke toegang
description: Meer informatie over het gebruik van de locatievoorwaarde om de toegang tot uw cloud-apps te beheren op basis van de netwerklocatie van een gebruiker.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 8b46ca16fc32a7b96c071a745f49bf5d5557f34b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530225"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>De locatievoorwaarde gebruiken in een beleid voor voorwaardelijke toegang 

Zoals uitgelegd [](overview.md) in het overzichtsartikel zijn beleidsregels voor voorwaardelijke toegang in de meeste basisregels een if-then-instructie die signalen combineert om beslissingen te nemen en organisatiebeleid af te dwingen. Een van deze signalen die kunnen worden opgenomen in het besluitvormingsproces is de netwerklocatie.

![Conceptueel voorwaardelijk signaal plus beslissing om afdwinging in te schakelen](./media/location-condition/conditional-access-signal-decision-enforcement.png)

Organisaties kunnen deze netwerklocatie gebruiken voor algemene taken zoals: 

- Meervoudige verificatie vereisen voor gebruikers die toegang hebben tot een service wanneer ze zich buiten het bedrijfsnetwerk.
- Toegang blokkeren voor gebruikers die toegang hebben tot een service vanuit specifieke landen of regio's.

De netwerklocatie wordt bepaald door het openbare IP-adres dat een client aan Azure Active Directory. Beleid voor voorwaardelijke toegang is standaard van toepassing op alle IPv4- en IPv6-adressen. 

## <a name="named-locations"></a>Benoemde locaties

Locaties worden aangeduid in de Azure Portal **onder** Azure Active Directory  >  **Security** Conditional  >  **Access Named**  >  **locations**. Deze benoemde netwerklocaties kunnen locaties bevatten zoals de netwerkbereiken van het hoofdkantoor van een organisatie, VPN-netwerkbereiken of de bereiken die u wilt blokkeren. Benoemde locaties kunnen worden gedefinieerd door IPv4/IPv6-adresbereiken of door landen/regio's. 

![Benoemde locaties in het Azure Portal](./media/location-condition/new-named-location.png)

### <a name="ip-address-ranges"></a>IP-adresbereiken

Als u een benoemde locatie wilt definiëren op IPv4/IPv6-adresbereiken, moet u een **naam** en een IP-bereik verstrekken. 

Benoemde locaties die zijn gedefinieerd door IPv4/IPv6-adresbereiken, gelden voor de volgende beperkingen: 
- Maximaal 195 benoemde locaties configureren
- Maximaal 2000 IP-adresbereiken per benoemde locatie configureren
- Zowel IPv4- als IPv6-bereik worden ondersteund
- Privé-IP-adresbereiken worden geconfigureerd
- Het aantal IP-adressen in een bereik is beperkt. Alleen CIDR-maskers groter dan /8 zijn toegestaan bij het definiëren van een IP-bereik. 

### <a name="trusted-locations"></a>Vertrouwde locaties

Beheerders kunnen benoemde locaties die zijn gedefinieerd door IP-adresbereiken, aanwijzen als vertrouwde benoemde locaties. 

![Vertrouwde locaties in de Azure Portal](./media/location-condition/new-trusted-location.png)

Aanmeldingen vanaf vertrouwde benoemde locaties verbeteren de nauwkeurigheid van de risicoberekening van Azure AD Identity Protection, waardoor het aanmeldingsrisico van gebruikers wordt verlaagd wanneer ze worden geverifieerd vanaf een locatie die als vertrouwd is gemarkeerd. Daarnaast kunnen vertrouwde benoemde locaties worden gebruikt in beleid voor voorwaardelijke toegang. U kunt bijvoorbeeld vereisen dat registratie van meervoudige verificatie wordt beperkt tot alleen vertrouwde benoemde locaties. 

### <a name="countries-and-regions"></a>Landen en regio's

Sommige organisaties kunnen ervoor kiezen om de toegang tot bepaalde landen of regio's te beperken met behulp van voorwaardelijke toegang. Naast het definiëren van benoemde locaties op IP-adresbereiken, kunnen beheerders benoemde locaties definiëren per land of regio. Wanneer een gebruiker zich meldt, zet Azure AD het IPv4-adres van de gebruiker om in een land of regio en wordt de toewijzing periodiek bijgewerkt. Organisaties kunnen benoemde locaties gebruiken die zijn gedefinieerd door landen om verkeer te blokkeren vanuit landen waar ze geen zaken doen, zoals Noord-Korea. 

> [!NOTE]
> Aanmeldingen vanaf IPv6-adressen kunnen niet worden toegevoegd aan landen of regio's en worden beschouwd als onbekende gebieden. Alleen IPv4-adressen kunnen worden toegevoegd aan landen of regio's.

![Maak een nieuwe locatie op basis van een land of regio in de Azure Portal](./media/location-condition/new-named-location-country-region.png)

#### <a name="include-unknown-areas"></a>Onbekende gebieden opnemen

Sommige IP-adressen zijn niet aan een specifiek land of specifieke regio, met inbegrip van alle IPv6-adressen. Als u deze IP-locaties wilt vastleggen, vink u het **selectievakje Onbekende gebieden opnemen aan** bij het definiëren van een locatie. Met deze optie kunt u kiezen of deze IP-adressen moeten worden opgenomen in de benoemde locatie. Gebruik deze instelling wanneer het beleid met de benoemde locatie van toepassing moet zijn op onbekende locaties.

### <a name="configure-mfa-trusted-ips"></a>Vertrouwde MFA-IP's configureren

U kunt ook IP-adresbereiken configureren die het lokale intranet van uw organisatie vertegenwoordigen in de [multi-factor authentication-service-instellingen.](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx) Met deze functie kunt u maximaal 50 IP-adresbereiken configureren. De IP-adresbereiken hebben een CIDR-indeling. Zie Trusted [IPs (Vertrouwde IP's) voor meer informatie.](../authentication/howto-mfa-mfasettings.md#trusted-ips)  

Als u vertrouwde IP-adressen hebt geconfigureerd, worden deze weergegeven als **MFA Trusted IPS** in de lijst met locaties voor de locatievoorwaarde.

### <a name="skipping-multi-factor-authentication"></a>Meervoudige verificatie overslaan

Op de pagina met service-instellingen voor Meervoudige verificatie kunt u gebruikers van het bedrijfsintranet identificeren door  **Multi-Factor Authentication** overslaan te selecteren voor aanvragen van federatief gebruikers op mijn intranet. Deze instelling geeft aan dat de claim binnen het bedrijfsnetwerk, die wordt uitgegeven door AD FS, moet worden vertrouwd en gebruikt om de gebruiker te identificeren als in het bedrijfsnetwerk. Zie Enable the Trusted IPs feature by using Conditional Access (Vertrouwde [IP's inschakelen met behulp van voorwaardelijke toegang) voor meer informatie.](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access)

Na het controleren van deze optie, met inbegrip van de benoemde locatie **MFA vertrouwde IPS** van toepassing op alle beleidsregels met deze optie geselecteerd.

Voor mobiele en desktoptoepassingen, die een lange levensduur van een sessie hebben, wordt voorwaardelijke toegang periodiek opnieuw geëvalueerd. De standaardwaarde is eenmaal per uur. Wanneer de claim binnen het bedrijfsnetwerk alleen wordt uitgegeven op het moment van de eerste verificatie, heeft Azure AD mogelijk geen lijst met vertrouwde IP-adresbereiken. In dit geval is het moeilijker om te bepalen of de gebruiker zich nog steeds in het bedrijfsnetwerk:

1. Controleer of het IP-adres van de gebruiker zich in een van de vertrouwde IP-adresbereiken.
1. Controleer of de eerste drie octets van het IP-adres van de gebruiker overeenkomen met de eerste drie octets van het IP-adres van de eerste verificatie. Het IP-adres wordt vergeleken met de eerste verificatie wanneer de claim binnen het bedrijfsnetwerk oorspronkelijk is uitgegeven en de gebruikerslocatie is gevalideerd.

Als beide stappen mislukken, wordt een gebruiker beschouwd als niet langer op een vertrouwd IP-adres.

## <a name="location-condition-in-policy"></a>Locatievoorwaarde in beleid

Wanneer u de locatievoorwaarde configureert, hebt u de mogelijkheid om onderscheid te maken tussen:

- Elke locatie
- Alle vertrouwde locaties
- Geselecteerde locaties

### <a name="any-location"></a>Elke locatie

Als u Standaard Elke locatie **selecteert,** wordt er een beleid toegepast op alle IP-adressen, wat elk adres op internet betekent. Deze instelling is niet beperkt tot IP-adressen die u hebt geconfigureerd als benoemde locatie. Wanneer u Elke **locatie selecteert,** kunt u nog steeds specifieke locaties uitsluiten van een beleid. U kunt bijvoorbeeld een beleid toepassen op alle locaties, met uitzondering van vertrouwde locaties, om het bereik in te stellen op alle locaties, met uitzondering van het bedrijfsnetwerk.

### <a name="all-trusted-locations"></a>Alle vertrouwde locaties

Deze optie is van toepassing op:

- Alle locaties die zijn gemarkeerd als vertrouwde locatie
- **Vertrouwde IPS voor MFA** (indien geconfigureerd)

### <a name="selected-locations"></a>Geselecteerde locaties

Met deze optie kunt u een of meer benoemde locaties selecteren. Voor een beleid waarin deze instelling van toepassing is, moet een gebruiker verbinding maken vanaf een van de geselecteerde locaties. Wanneer u klikt op **Selecteer het** benoemde netwerkselectiebesturingselement waarin de lijst met benoemde netwerken wordt weergegeven, wordt geopend. In de lijst wordt ook weergegeven of de netwerklocatie is gemarkeerd als vertrouwd. De benoemde locatie met de naam Vertrouwde IP-adressen voor **MFA** wordt gebruikt om de IP-instellingen op te nemen die kunnen worden geconfigureerd op de instellingspagina van de Service voor meervoudige verificatie.

## <a name="ipv6-traffic"></a>IPv6-verkeer

Beleid voor voorwaardelijke toegang is standaard van toepassing op al het IPv6-verkeer. U kunt specifieke IPv6-adresbereiken uitsluiten van een beleid voor voorwaardelijke toegang als u niet wilt dat beleid wordt afgedwongen voor specifieke IPv6-bereiken. Als u bijvoorbeeld geen beleid wilt afdwingen voor gebruik in uw bedrijfsnetwerk en uw bedrijfsnetwerk wordt gehost in openbare IPv6-adresbereiken.  

### <a name="when-will-my-tenant-have-ipv6-traffic"></a>Wanneer heeft mijn tenant IPv6-verkeer?

Azure Active Directory (Azure AD) biedt momenteel geen ondersteuning voor directe netwerkverbindingen die gebruikmaken van IPv6. Er zijn echter enkele gevallen dat verificatieverkeer via een andere service wordt geproxied. In dergelijke gevallen wordt het IPv6-adres gebruikt tijdens de beleidsevaluatie.

Het grootste deel van het IPv6-verkeer dat naar Azure AD wordt geproxied, is afkomstig van Microsoft Exchange Online. Indien beschikbaar, geeft Exchange de voorkeur aan IPv6-verbindingen. **Als u dus beleid voor voorwaardelijke toegang voor Exchange hebt dat is geconfigureerd voor specifieke IPv4-bereiken, moet u ervoor zorgen dat u ook IPv6-bereiken van uw organisatie hebt toegevoegd.** Als iPv6-bereik niet wordt opgenomen, wordt onverwacht gedrag veroorzaakt in de volgende twee gevallen:

- Wanneer een e-mailclient wordt gebruikt om met verouderde verificatie verbinding te maken met Exchange Online, ontvangt Azure AD mogelijk een IPv6-adres. De eerste verificatieaanvraag gaat naar Exchange en wordt vervolgens geproxied naar Azure AD.
- Wanneer Outlook Web Access (OWA) wordt gebruikt in de browser, wordt periodiek gecontroleerd of aan alle beleidsregels voor voorwaardelijke toegang wordt voldaan. Deze controle wordt gebruikt om gevallen te ondervangen waarbij een gebruiker mogelijk is verplaatst van een toegestaan IP-adres naar een nieuwe locatie, zoals de koffiebar in de straat. Als in dit geval een IPv6-adres wordt gebruikt en het IPv6-adres zich niet in een geconfigureerd bereik, kan de gebruiker de sessie onderbreken en worden teruggeleid naar Azure AD om zich opnieuw te autoreren. 

Dit zijn de meest voorkomende redenen waarom u mogelijk IPv6-adresbereiken in uw benoemde locaties moet configureren. Als u azure-VNets gebruikt, hebt u bovendien verkeer dat afkomstig is van een IPv6-adres. Als U VNet-verkeer hebt geblokkeerd door een beleid voor voorwaardelijke toegang, controleert u uw Azure AD-aanmeldingslogboek. Zodra u het verkeer hebt geïdentificeerd, kunt u het gebruikte IPv6-adres op halen en dit uitsluiten van uw beleid. 

> [!NOTE]
> Als u een IP-CIDR-bereik voor één adres wilt opgeven, moet u het /128-bits masker toepassen. Als u het IPv6-adres 2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a gebruikt en dat enkele adres wilt uitsluiten als een bereik, gebruikt u 2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a/128.

### <a name="identifying-ipv6-traffic-in-the-azure-ad-sign-in-activity-reports"></a>IPv6-verkeer identificeren in de azure AD-aanmeldactiviteitenrapporten

U kunt IPv6-verkeer in uw tenant ontdekken door de [azure AD-aanmeldactiviteitenrapporten te gaan.](../reports-monitoring/concept-sign-ins.md) Nadat u het activiteitenrapport hebt geopend, voegt u de kolom IP-adres toe. In deze kolom kunt u het IPv6-verkeer identificeren.

U kunt het IP-adres van de client ook vinden door op een rij in het rapport te klikken en vervolgens naar het tabblad Locatie te gaan in de details van de aanmeldingsactiviteiten. 

## <a name="what-you-should-know"></a>Wat u moet weten

### <a name="when-is-a-location-evaluated"></a>Wanneer wordt een locatie geëvalueerd?

Beleid voor voorwaardelijke toegang wordt geëvalueerd wanneer:

- Een gebruiker meldt zich in eerste instantie aan bij een web-app, mobiele toepassing of desktoptoepassing.
- Een mobiele toepassing of desktoptoepassing die gebruikmaakt van moderne verificatie, gebruikt een vernieuwingsteken om een nieuw toegang token te verkrijgen. Deze controle is standaard één keer per uur.

Deze controle betekent dat voor mobiele en desktoptoepassingen die moderne verificatie gebruiken, binnen een uur na het wijzigen van de netwerklocatie een locatiewijziging wordt gedetecteerd. Voor mobiele en bureaubladtoepassingen die geen moderne verificatie gebruiken, wordt het beleid toegepast op elke tokenaanvraag. De frequentie van de aanvraag kan variëren op basis van de toepassing. Op dezelfde manier wordt het beleid voor webtoepassingen toegepast bij de eerste aanmelding en is het goed voor de levensduur van de sessie bij de webtoepassing. Vanwege verschillen in de levensduur van sessies voor toepassingen, varieert de tijd tussen de evaluatie van het beleid ook. Telkens wanneer de toepassing een nieuw aanmeldings token aanvraagt, wordt het beleid toegepast.

Azure AD geeft standaard per uur een token uit. Nadat het bedrijfsnetwerk is verplaatst, wordt het beleid binnen een uur afgedwongen voor toepassingen die moderne verificatie gebruiken.

### <a name="user-ip-address"></a>IP-adres van gebruiker

Het IP-adres dat wordt gebruikt in de beleidsevaluatie is het openbare IP-adres van de gebruiker. Voor apparaten in een particulier netwerk is dit IP-adres niet het client-IP-adres van het apparaat van de gebruiker op het intranet. Het is het adres dat door het netwerk wordt gebruikt om verbinding te maken met het openbare internet.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Bulksgewijs uploaden en downloaden van benoemde locaties

Wanneer u benoemde locaties maakt of bij werkt voor bulkupdates, kunt u een CSV-bestand met de IP-adresbereiken uploaden of downloaden. Een upload vervangt de IP-adresbereiken in de lijst door de ip-adresbereiken uit het bestand. Elke rij van het bestand bevat één IP-adresbereik in CIDR-indeling.

### <a name="cloud-proxies-and-vpns"></a>Cloud-proxies en VPN's

Wanneer u een in de cloud gehoste proxy of VPN-oplossing gebruikt, is het IP-adres dat Azure AD gebruikt tijdens het evalueren van een beleid het IP-adres van de proxy. De X-Forwarded-For-header (XFF) die het openbare IP-adres van de gebruiker bevat, wordt niet gebruikt omdat er geen validatie is dat het afkomstig is van een vertrouwde bron. Er wordt dus een methode voor het nabootsen van een IP-adres gebruikt.

Wanneer een cloudproxy is geïnstalleerd, kan een beleid dat wordt gebruikt om een hybride Azure AD-apparaat te vereisen, worden gebruikt of de binnen corpnet-claim van AD FS.

### <a name="api-support-and-powershell"></a>API-ondersteuning en PowerShell

Er is een preview-versie van Graph API voor benoemde locaties beschikbaar. Zie de [namedLocation API voor meer informatie.](/graph/api/resources/namedlocation)

> [!NOTE]
> Benoemde locaties die u maakt met behulp van PowerShell worden alleen weergegeven in Benoemde locaties (preview). In de oude weergave ziet u geen benoemde locaties.  

## <a name="next-steps"></a>Volgende stappen

- Als u wilt weten hoe u een beleid voor voorwaardelijke toegang configureert, bekijkt u het artikel [Een beleid voor voorwaardelijke toegang bouwen.](concept-conditional-access-policies.md)
- Zoekt u een voorbeeldbeleid met behulp van de locatievoorwaarde? Zie het artikel [Voorwaardelijke toegang: Toegang per locatie blokkeren](howto-conditional-access-policy-location.md)
