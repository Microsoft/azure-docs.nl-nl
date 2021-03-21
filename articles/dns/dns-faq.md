---
title: Veelgestelde vragen-Azure DNS
description: In dit artikel vindt u informatie over de veelgestelde vragen over Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 01/11/2021
ms.author: rohink
ms.openlocfilehash: cb14cb95ec1362782a634b0e62cfa2f8237a5852
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98246379"
---
# <a name="azure-dns-faq"></a>Veelgestelde vragen over Azure DNS

## <a name="about-azure-dns"></a>Over Azure DNS

### <a name="what-is-azure-dns"></a>Wat is Azure DNS?

De Domain Name System (DNS) zet een website of service naam om in het IP-adres. Azure DNS is een hostingservice voor DNS-domeinen. Het biedt naam omzetting door gebruik te maken van Microsoft Azure-infra structuur. Door uw domeinen in Azure te hosten, kunt u uw DNS-records met dezelfde referenties, API's, hulpprogramma's en facturering beheren als voor uw andere Azure-services.

DNS-domeinen in Azure DNS worden gehost op het globale Azure-netwerk van DNS-naam servers. Dit systeem gebruikt Anycast-netwerken zodat elke DNS-query wordt beantwoord door de dichtstbijzijnde beschik bare DNS-server. Azure DNS biedt snelle prestaties en hoge Beschik baarheid voor uw domein.

Azure DNS is gebaseerd op Azure Resource Manager. Azure DNS voor delen van Resource Manager-functies, zoals Azure op rollen gebaseerd toegangs beheer, controle logboeken en resource vergrendeling. U kunt domeinen en records beheren via de Azure Portal, Azure PowerShell-cmdlets en de platformoverschrijdende Azure CLI. Toepassingen waarvoor automatisch DNS-beheer is vereist, kunnen worden geïntegreerd met de service via de REST API en Sdk's.

### <a name="how-much-does-azure-dns-cost"></a>Hoeveel kost het Azure DNS?

Het facturerings model van Azure DNS is gebaseerd op het aantal DNS-zones dat wordt gehost in Azure DNS. Het is ook gebaseerd op het aantal DNS-query's dat wordt ontvangen. Kortingen worden verstrekt op basis van het gebruik.

Zie de pagina met prijzen voor [Azure DNS](https://azure.microsoft.com/pricing/details/dns/)voor meer informatie.

### <a name="what-is-the-sla-for-azure-dns"></a>Wat is de SLA voor Azure DNS?

Azure garandeert dat geldige DNS-aanvragen een reactie ontvangen van ten minste één Azure DNS naam server van 100% van de tijd.

Zie de [pagina Azure DNS Sla](https://azure.microsoft.com/support/legal/sla/dns)voor meer informatie.

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Wat is een DNS-zone? Is dit hetzelfde als een DNS-domein? 

Een domein is een unieke naam in het Domain Name System. Bijvoorbeeld: contoso.com.

Een DNS-zone wordt gebruikt om de DNS-records voor een bepaald domein te hosten. Het domein contoso.com kan bijvoorbeeld meerdere DNS-records bevatten. De records bevatten mogelijk mail.contoso.com voor een e-mail server en www- \. contoso.com voor een website. Deze records worden gehost in de DNS-zone contoso.com.

Een domein naam is *slechts een naam*. Een DNS-zone is een gegevens bron die de DNS-records voor een domein naam bevat. Met Azure DNS kunt u een DNS-zone hosten en de DNS-records voor een domein in Azure beheren. Het biedt ook DNS-naam servers om DNS-query's van het Internet te beantwoorden.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Moet ik een DNS-domein naam kopen om Azure DNS te gebruiken? 

Niet noodzakelijkerwijs.

U hoeft geen domein te kopen om een DNS-zone in Azure DNS te hosten. U kunt op elk gewenst moment een DNS-zone maken zonder eigenaar van de domein naam. DNS-query's voor deze zone worden alleen opgelost als deze worden omgeleid naar de Azure DNS naam servers die zijn toegewezen aan de zone.

Als u uw DNS-zone wilt koppelen aan de algemene DNS-hiërarchie, moet u de domein naam kopen. Vervolgens zoekt u op DNS-query's van overal ter wereld uw DNS-zone en beantwoordt u uw DNS-records.

## <a name="azure-dns-features"></a>Azure DNS functies

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Zijn er beperkingen bij het gebruik van alias records voor een domein naam Apex met Traffic Manager?

Ja. U moet statische open bare IP-adressen gebruiken met Azure Traffic Manager. Configureer het **externe eind punt** doel met behulp van een statisch IP-adres. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Ondersteunt Azure DNS verkeer routering of endpoint failover op basis van DNS?

Route ring op basis van DNS en endpoint failover worden verschaft door Traffic Manager. Traffic Manager is een afzonderlijke Azure-service die kan worden gebruikt met Azure DNS. Zie [Traffic Manager-overzicht](../traffic-manager/traffic-manager-overview.md)voor meer informatie.

Azure DNS biedt alleen ondersteuning voor het hosten van statische DNS-domeinen waarbij elke DNS-query voor een gegeven DNS-record altijd hetzelfde DNS-antwoord ontvangt.

### <a name="does-azure-dns-support-domain-name-registration"></a>Ondersteunt Azure DNS de registratie van de domein naam?

Nee. Azure DNS biedt momenteel geen ondersteuning voor de optie om domein namen te kopen. Als u domeinen wilt kopen, moet u een domein naam registratie van een derde partij gebruiken. De registratie service berekent doorgaans een klein jaarlijks bedrag. De domeinen kunnen vervolgens worden gehost in Azure DNS voor het beheer van DNS-records. Zie [Delegate a domain to Azure DNS](dns-domain-delegation.md) (Een domein aan Azure DNS overdragen) voor meer informatie.

De functie voor het kopen van domein namen wordt bijgehouden in de Azure-achterstand. Gebruik de feedback site om [uw ondersteuning voor deze functie te registreren](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Ondersteunt Azure DNS DNSSEC?

Nee. Azure DNS biedt momenteel geen ondersteuning voor de Domain Name System Security Extensions (DNSSEC).

De DNSSEC-functie wordt bijgehouden in de Azure DNS achterstand. Gebruik de feedback site om [uw ondersteuning voor deze functie te registreren](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Ondersteunt Azure DNS zone overdrachten (AXFR/IXFR)?

Nee. Azure DNS biedt momenteel geen ondersteuning voor zone overdrachten. DNS-zones kunnen worden [geïmporteerd in azure DNS met behulp van de Azure cli](dns-import-export.md). DNS-records worden beheerd via de [Azure DNS beheer Portal](dns-operations-recordsets-portal.md), [rest API](/powershell/module/az.dns), [SDK](dns-sdk.md), [Power shell-cmdlets](dns-operations-recordsets.md)of het [cli-hulp programma](dns-operations-recordsets-cli.md).

De functie zone overdracht wordt bijgehouden in de Azure DNS achterstand. Gebruik de feedback site om [uw ondersteuning voor deze functie te registreren](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Ondersteunt Azure DNS URL-omleidingen?

Nee. Services voor URL-omleiding zijn geen DNS-service. Ze werken op het HTTP-niveau in plaats van het DNS-niveau. Sommige DNS-providers bundelen een URL-omleidings service als onderdeel van hun hele aanbieding. Deze service wordt momenteel niet ondersteund door Azure DNS.

De functie voor URL-omleiding wordt bijgehouden in de Azure DNS achterstand. Gebruik de feedback site om [uw ondersteuning voor deze functie te registreren](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Ondersteunt Azure DNS de uitgebreide ASCII-code ring (8-bits) die is ingesteld voor TXT-record sets?

Ja. Azure DNS ondersteunt de uitgebreide ASCII-coderings voor TXT-record sets. Maar u moet de nieuwste versie van de Azure REST Api's, Sdk's, Power shell en de CLI gebruiken. Oudere versies dan 1 oktober 2017 of SDK 2,1 bieden geen ondersteuning voor de uitgebreide ASCII-set. 

U kunt bijvoorbeeld een teken reeks opgeven als de waarde voor een TXT-record met het uitgebreide ASCII-teken \ 128. Een voor beeld is ' abcd\128efgh. ' Azure DNS gebruikt de byte waarde van dit teken, 128, in interne weer gave. Op het moment van de DNS-omzetting wordt deze byte waarde geretourneerd in het antwoord. Houd er ook rekening mee dat "ABC" en "\ 097 \ 098 \ 099" elkaar verwisselbaar zijn. 

We volgen de regels voor de bestands hoofd indeling van de [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) -zone voor TXT-records. U kunt nu bijvoorbeeld alle gegevens per RFC vervolledigen `\` . Als u opgeeft `A\B` als waarde voor TXT-record, wordt deze als zodanig weer gegeven en opgelost `AB` . Als u wilt dat de TXT `A\B` -record bij een oplossing komt, moet u het opnieuw escape-teken `\` . Geef als voor beeld op `A\\B` .

Deze ondersteuning is momenteel niet beschikbaar voor TXT-records die zijn gemaakt op basis van de Azure Portal.

## <a name="alias-records"></a>Aliasrecords

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Wat zijn de scenario's waarin alias records nuttig zijn?

Zie de sectie scenario's in het [Azure DNS alias records Overview](dns-alias.md)(Engelstalig).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Welke record typen worden ondersteund voor alias record sets?

Alias record sets worden ondersteund voor de volgende record typen in een Azure DNS zone:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Welke resources worden ondersteund als doelen voor alias record sets?

- **Wijs naar een open bare IP-bron van een DNS A/AAAA-Recordset.** U kunt een A/AAAA-recordset maken en hiervan een alias instellen om naar een open bare IP-resource te verwijzen.
- **Ga naar een Traffic Manager profiel van een DNS A/AAAA/CNAME-Recordset.** U kunt naar de CNAME van een Traffic Manager profiel verwijzen vanuit een DNS CNAME-Recordset. Een voor beeld is contoso.trafficmanager.net. U kunt nu ook verwijzen naar een Traffic Manager profiel met externe eind punten van een A-of AAAA-record die is ingesteld in uw DNS-zone.
- **Ga naar een Azure Content Delivery Network-eind punt (CDN)**. Dit is handig wanneer u statische websites maakt met behulp van Azure Storage en Azure CDN.
- **Wijs naar een andere DNS-record die binnen dezelfde zone is ingesteld.** Alias records kunnen verwijzen naar andere record sets van hetzelfde type. U kunt bijvoorbeeld een DNS CNAME-recordset hebben als alias voor een andere CNAME-recordset van hetzelfde type. Deze indeling is handig als u wilt dat sommige record sets aliassen en bepaalde niet-aliassen hebben.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Kan ik alias records maken en bijwerken vanuit de Azure Portal?

Ja. U kunt alias records maken of beheren in de Azure Portal samen met de Azure REST Api's, Power shell, de CLI en Sdk's.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Helpt alias records om te controleren of mijn DNS-recordset is verwijderd wanneer de onderliggende open bare IP wordt verwijderd?

Ja. Deze functie is een van de belangrijkste mogelijkheden van alias records. Het helpt u potentiële storingen te voor komen voor gebruikers van uw toepassing.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Helpt alias records om te controleren of mijn DNS-record is ingesteld op het juiste IP-adres wanneer het onderliggende open bare IP-adres verandert?

Ja. Deze functie is een van de belangrijkste mogelijkheden van alias records. Het helpt u bij het voor komen van potentiële storingen of beveiligings Risico's voor uw toepassing.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Zijn er beperkingen bij het gebruik van alias record sets voor A-of AAAA-records om naar Traffic Manager te verwijzen?

Ja. Als u wilt verwijzen naar een Traffic Manager profiel als alias van een A-of AAAA-Recordset, moet het Traffic Manager profiel alleen externe eind punten gebruiken. Wanneer u de externe eind punten in Traffic Manager maakt, geeft u de werkelijke IP-adressen van de eind punten op.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Worden er extra kosten in rekening gebracht voor het gebruik van alias records?

Alias records zijn een kwalificatie voor een geldige DNS-Recordset. Er is geen aanvullende facturering voor alias records.

## <a name="use-azure-dns"></a>Azure DNS gebruiken

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Kan ik een domein naast elkaar hosten met behulp van Azure DNS en een andere DNS-provider?

Ja. Azure DNS ondersteunt het samen hosten van domeinen met andere DNS-services.

Als u co-hosting wilt instellen, wijzigt u de NS-records voor het domein zodat deze verwijzen naar de naam servers van beide providers. De naam server records bepalen welke providers DNS-query's ontvangen voor het domein. U kunt deze NS-records wijzigen in Azure DNS, in de andere provider en in de bovenliggende zone. De bovenliggende zone wordt doorgaans geconfigureerd via de domein naam van het REGI ster. Zie [DNS-domein overdracht](dns-domain-delegation.md)voor meer informatie over DNS-delegering.

Zorg er ook voor dat de DNS-records voor het domein synchroon zijn tussen DNS-providers. Azure DNS biedt momenteel geen ondersteuning voor DNS-zone overdrachten. DNS-records moeten worden gesynchroniseerd met behulp van de [Azure DNS beheer Portal](dns-operations-recordsets-portal.md), [rest API](/rest/api/dns/), [SDK](dns-sdk.md), [Power shell-cmdlets](dns-operations-recordsets.md)of het [cli-hulp programma](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Moet ik mijn domein overdragen aan alle vier Azure DNS naam servers?

Ja. Azure DNS wijst vier naam servers toe aan elke DNS-zone. Deze indeling is voor fout isolatie en verhoogde tolerantie. Als u in aanmerking wilt komen voor de Azure DNS SLA, moet u uw domein delegeren aan alle vier naam servers.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Wat zijn de gebruiks limieten voor Azure DNS?

De volgende standaard limieten zijn van toepassing wanneer u Azure DNS gebruikt.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Kan ik een Azure DNS zone verplaatsen tussen resource groepen of tussen abonnementen?

Ja. DNS-zones kunnen worden verplaatst tussen resource groepen of tussen-abonnementen.

Er zijn geen gevolgen voor DNS-query's wanneer u een DNS-zone verplaatst. De naam servers die aan de zone zijn toegewezen, blijven ongewijzigd. DNS-query's worden op de normale wijze verwerkt in.

Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)voor meer informatie en instructies voor het verplaatsen van DNS-zones.

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Hoe lang duurt het voordat DNS-wijzigingen van kracht worden?

Nieuwe DNS-zones en DNS-records worden doorgaans snel in de Azure DNS naam servers weer gegeven. De timing is een paar seconden.

Wijzigingen in bestaande DNS-records kunnen wat langer duren. Deze worden doorgaans binnen 60 seconden weer gegeven in de Azure DNS naam servers. DNS-caching door DNS-clients en DNS recursieve resolvers buiten Azure DNS kan ook invloed hebben op de timing. Als u deze cache duur wilt beheren, gebruikt u de eigenschap time-to-Live (TTL) van elke recordset.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Hoe kan ik mijn DNS-zones beveiligen tegen onbedoeld verwijderen?

Azure DNS wordt beheerd met behulp van Azure Resource Manager. Azure DNS voor delen van de functies voor toegangs beheer die Azure Resource Manager biedt. Toegangs beheer op basis van rollen kan worden gebruikt om te bepalen welke gebruikers lees-of schrijf toegang hebben tot DNS-zones en-record sets. Bron vergrendelingen voor komen onopzettelijke wijzigingen of verwijdering van DNS-zones en-record sets.

Zie [DNS-zones en-records beveiligen](dns-protect-zones-recordsets.md)voor meer informatie.

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Wilt u SPF-records Hoe kan ik instellen in Azure DNS?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Kunnen Azure DNS naam servers worden omgezet via IPv6? 

Ja. Azure DNS naam servers zijn dubbele stack. Dual stack betekent dat ze IPv4-en IPv6-adressen hebben. Gebruik een hulp programma zoals Nslookup om het IPv6-adres te vinden voor de Azure DNS naam servers die zijn toegewezen aan uw DNS-zone. Een voorbeeld is `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Hoe kan ik een IDN instellen in Azure DNS?

Geinternational Domain names (Idn's) versleutelt elke DNS-naam met behulp van [punycode](https://en.wikipedia.org/wiki/Punycode). DNS-query's worden gemaakt met behulp van deze met punycode gecodeerde namen.

Als u Idn's wilt configureren in Azure DNS, zet u de naam van de zone of de recordset om in punycode. Azure DNS biedt momenteel geen ondersteuning voor ingebouwde conversie naar of van punycode.

## <a name="next-steps"></a>Volgende stappen

- Meer [informatie over Azure DNS](dns-overview.md).

- Meer [informatie over het gebruik van Azure DNS voor privé domeinen](private-dns-overview.md).

- Meer [informatie over DNS-zones en-records](dns-zones-records.md).

- [Aan de slag met Azure DNS](dns-getstarted-portal.md).
