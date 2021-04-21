---
title: Overzicht van DNS-zones en -records - Azure DNS
description: Overzicht van ondersteuning voor het hosten van DNS-zones en -records in Microsoft Azure DNS.
author: rohinkoul
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 04/20/2021
ms.author: rohink
ms.openlocfilehash: 26eefe5cbcef417dee1a400b492ee847394ca6a9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816959"
---
# <a name="overview-of-dns-zones-and-records"></a>Overzicht van DNS-zones en -records

In dit artikel worden de belangrijkste concepten van domeinen, DNS-zones, DNS-records en recordsets uitgelegd. U leert hoe dit wordt ondersteund in Azure DNS.

## <a name="domain-names"></a>Domeinnamen

Het Domain Name System is een hiërarchie van domeinen. De hiërarchie start vanaf het hoofddomein. De naam van dit domein is eenvoudigweg '**.**'.  Hieronder komen de topleveldomeinen, zoals com, net, org, uk of nl.  Onder de domeinen op het hoogste niveau staan domeinen op het tweede niveau, zoals 'org.uk' of 'co.jp'. De domeinen in de DNS-hiërarchie worden wereldwijd gedistribueerd, gehost door DNS-naamservers over de hele wereld.

Een domeinnaamregistrar is een organisatie waarmee u een domeinnaam kunt kopen, zoals `contoso.com` .  Als u een domeinnaam aanschaft, hebt u het recht om de DNS-hiërarchie onder die naam te beheren, bijvoorbeeld zodat u de naam naar uw `www.contoso.com` bedrijfswebsite kunt sturen. De registrar kan het domein namens u hosten op eigen naamservers of u toestaan alternatieve naamservers op te geven.

Azure DNS biedt een wereldwijd gedistribueerde naamserverinfrastructuur met hoge beschikbaarheid die u kunt gebruiken om uw domein te hosten. Door uw domeinen te hosten in Azure DNS, kunt u uw DNS-records beheren met dezelfde referenties, API's, hulpprogramma's, facturering en ondersteuning als uw andere Azure-services.

Azure DNS biedt momenteel geen ondersteuning voor het kopen van domeinnamen. Als u een domeinnaam wilt aanschaffen, moet u een externe domeinnaamregistrar gebruiken. De registrar brengt doorgaans een klein bedrag per jaar in rekening. De domeinen kunnen vervolgens worden gehost in Azure DNS voor het beheer van DNS-records. Zie [Een domein aan Azure DNS overdragen](dns-domain-delegation.md) voor meer informatie.

## <a name="dns-zones"></a>DNS-zones

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>DNS-records

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Time-to-live

De time to live, of TTL, geeft aan hoelang elke record door clients in de cache wordt opgeslagen voordat deze opnieuw in dequerie wordt geplaatst. In het bovenstaande voorbeeld is de TTL 3600 seconden of 1 uur.

In Azure DNS TTL wordt opgegeven voor de recordset, niet voor elke record, zodat dezelfde waarde wordt gebruikt voor alle records in die recordset.  U kunt een TTL-waarde tussen 1 en 2.147.483.647 seconden opgeven.

### <a name="wildcard-records"></a>Jokertekenrecords

Azure DNS ondersteunt [recordsets met jokertekens](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Records met jokertekens worden geretourneerd als reactie op een query met een overeenkomende naam, tenzij er een dichtere overeenkomst is van een recordset zonder jokertekens. Azure DNS ondersteunt recordsets met jokertekens voor alle recordtypen, met uitzondering van NS en SOA.

Als u een recordset met jokertekens wilt maken, gebruikt u de recordsetnaam ' \* '. U kunt ook een naam gebruiken met ' ' als meest \* linkse label, bijvoorbeeld ' \* .foo'.

### <a name="caa-records"></a>CAA-records

Met CAA-records kunnen domeineigenaren opgeven welke certificeringsinstanties (CA's) zijn gemachtigd om certificaten voor hun domein uit te geven. Met deze record kunnen CERTIFICERINGsas in bepaalde omstandigheden voorkomen dat certificaten verkeerd worden gegeven. CAA-records hebben drie eigenschappen:
* **Vlaggen:** dit veld is een geheel getal tussen 0 en 255, dat wordt gebruikt om de kritieke vlag aan te geven die een speciale betekenis heeft volgens de [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Tag:** een ASCII-tekenreeks die een van de volgende kan zijn:
    * **probleem:** als u CERTIFICERINGscertificeringen (alle typen) wilt opgeven
    * **issuewild:** als u CERTIFICERINGsingevers wilt opgeven die certificaten mogen uitgeven (alleen certificaten met jokertekens)
    * **iodef:** geef een e-mailadres of hostnaam op waaraan CERTIFICERINGsaanvragen voor niet-geautoriseerde certificaatuitgevers kunnen worden verzonden
* **Waarde:** de waarde voor de gekozen specifieke tag

### <a name="cname-records"></a>CNAME-records

CNAME-recordsets kunnen niet naast elkaar bestaan met andere recordsets met dezelfde naam. U kunt bijvoorbeeld niet tegelijkertijd een CNAME-recordset maken met de relatieve naam 'www' en een A-record met de relatieve naam 'www'.

Omdat de zone-apex (naam = ' ') altijd de NS- en SOA-recordsets bevat tijdens het maken van de zone, kunt u geen CNAME-recordset maken in de \@ zone-apex.

Deze beperkingen worden veroorzaakt door de DNS-standaarden en zijn geen beperkingen van Azure DNS.

### <a name="ns-records"></a>NS-records

De NS-recordset in de zone-apex (naam ' ') wordt automatisch gemaakt met elke DNS-zone en wordt automatisch verwijderd wanneer \@ de zone wordt verwijderd. Deze kan niet afzonderlijk worden verwijderd.

Deze recordset bevat de namen van de Azure DNS naamservers die zijn toegewezen aan de zone. U kunt meer naamservers toevoegen aan deze NS-recordset om cohosting van domeinen met meer dan één DNS-provider te ondersteunen. U kunt ook de TTL en metagegevens voor deze recordset wijzigen. Het verwijderen of wijzigen van de vooraf ingevulde Azure DNS naamservers is echter niet toegestaan. 

Deze beperking geldt alleen voor de NS-recordset in de zone-apex. Andere NS-recordsets in uw zone (zoals gebruikt voor het delegeren van onderliggende zones) kunnen zonder beperkingen worden gemaakt, gewijzigd en verwijderd.

### <a name="soa-records"></a>SOA-records

Een SOA-recordset wordt automatisch gemaakt in de apex van elke zone (naam = ' ') en wordt automatisch verwijderd wanneer \@ de zone wordt verwijderd.  SOA-records kunnen niet afzonderlijk worden gemaakt of verwijderd.

U kunt alle eigenschappen van de SOA-record wijzigen, met uitzondering van de eigenschap 'host'. Deze eigenschap wordt vooraf geconfigureerd om te verwijzen naar de naam van de primaire naamserver die is opgegeven door Azure DNS.

Het serienummer van de zone in de SOA-record wordt niet automatisch bijgewerkt wanneer er wijzigingen worden aangebracht in de records in de zone. Deze kan indien nodig handmatig worden bijgewerkt door de SOA-record te bewerken.

### <a name="spf-records"></a>SPF-records

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>SRV-records

[SRV-records](https://en.wikipedia.org/wiki/SRV_record) worden door verschillende services gebruikt om serverlocaties op te geven. Bij het opgeven van een SRV-record in Azure DNS:

* De *service* en *het protocol* moeten worden opgegeven als onderdeel van de naam van de recordset, voorafgegaan door onderstrepingstekens.  Bijvoorbeeld' \_ sip. \_ tcp.name'.  Voor een record in de zone-apex is het niet nodig om ' ' op te geven in de recordnaam. Gebruik gewoon de service en het protocol, bijvoorbeeld \@ \_ 'sip'. \_ tcp'.
* De *prioriteit,* *het gewicht,* *de poort* en het doel worden opgegeven als parameters van elke record in de recordset. 

### <a name="txt-records"></a>TXT-records

TXT-records worden gebruikt om domeinnamen toe te wijs aan willekeurige tekstreeksen. Ze worden gebruikt in meerdere toepassingen, met name met betrekking tot e-mailconfiguratie, zoals [SPF (Sender Policy Framework)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) en [DomainKeys Identified Mail (DKIM).](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail)

De DNS-standaarden staan toe dat één TXT-record meerdere tekenreeksen bevat, die elk maximaal 254 tekens lang kunnen zijn. Wanneer er meerdere tekenreeksen worden gebruikt, worden ze door clients samenvoegd en behandeld als één tekenreeks.

Wanneer u de Azure DNS REST API aanroept, moet u elke TXT-tekenreeks afzonderlijk opgeven.  Wanneer u de Azure Portal-, PowerShell- of CLI-interfaces gebruikt, moet u één tekenreeks per record opgeven, die indien nodig automatisch wordt onderverdeeld in segmenten van 254 tekens.

De meerdere tekenreeksen in een DNS-record mogen niet worden verward met de meerdere TXT-records in een TXT-recordset.  Een TXT-recordset kan meerdere records bevatten, *die elk* meerdere tekenreeksen kunnen bevatten.  Azure DNS ondersteunt een totale tekenreekslengte van maximaal 1024 tekens in elke TXT-recordset (voor alle records gecombineerd).

## <a name="tags-and-metadata"></a>Tags en metagegevens

### <a name="tags"></a>Tags

Tags zijn een lijst met naam-waardeparen en worden gebruikt door Azure Resource Manager om resources te labelen. Azure Resource Manager gebruikt tags om gefilterde weergaven van uw Azure-factuur in te stellen en stelt u ook in staat een beleid in te stellen voor bepaalde tags. Zie voor meer informatie over tags [Tags gebruiken om uw Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md).

Azure DNS ondersteunt het gebruik Azure Resource Manager tags op DNS-zonebronnen.  Het biedt geen ondersteuning voor tags op DNS-recordsets, hoewel als alternatief 'metagegevens' wordt ondersteund in DNS-recordsets, zoals hieronder wordt uitgelegd.

### <a name="metadata"></a>Metagegevens

Als alternatief voor recordsettags biedt Azure DNS ondersteuning voor het toevoegen van aantekeningen aan recordsets met behulp van 'metagegevens'.  Net als bij tags kunt u met metagegevens naam-waardeparen aan elke recordset koppelen.  Deze functie kan handig zijn, bijvoorbeeld om het doel van elke recordset vast te stellen.  In tegenstelling tot tags kunnen metagegevens niet worden gebruikt om een gefilterde weergave van uw Azure-factuur te bieden en kunnen ze niet worden opgegeven in een Azure Resource Manager beleid.

## <a name="etags"></a>Etags

Stel dat twee personen of twee processen tegelijk proberen een DNS-record te wijzigen. Welke wint? En weet de winner dat hij wijzigingen heeft overschreven die door iemand anders zijn gemaakt?

Azure DNS Etags gebruikt om gelijktijdige wijzigingen in dezelfde resource veilig af te handelen. Etags staan los [van Azure Resource Manager 'Tags'](#tags). Aan elke DNS-resource (zone of recordset) is een Etag gekoppeld. Wanneer een resource wordt opgehaald, wordt de Etag ook opgehaald. Bij het bijwerken van een resource kunt u ervoor kiezen om de Etag door te geven, zodat Azure DNS de Etag op de server overeenkomt. Omdat elke update van een resource resulteert in het opnieuw worden ge regenereerd van de Etag, geeft een onjuiste Etag aan dat er een gelijktijdige wijziging is opgetreden. Etags kunnen ook worden gebruikt bij het maken van een nieuwe resource om ervoor te zorgen dat de resource nog niet bestaat.

PowerShell gebruikt Azure DNS standaard Etags om gelijktijdige wijzigingen in zones en recordsets te blokkeren. De *optionele schakelknop -Overwrite* kan worden gebruikt om Etag-controles te onderdrukken. In dat geval worden eventuele gelijktijdige wijzigingen die hebben plaatsgevonden, overschreven.

Op het niveau van de Azure DNS REST API worden Etags opgegeven met behulp van HTTP-headers.  Hun gedrag wordt gegeven in de volgende tabel:

| Header | Gedrag |
| --- | --- |
| Geen |PUT slaagt altijd (geen Etag-controles) |
| If-match \<etag> |PUT slaagt alleen als de resource bestaat en Etag overeenkomt |
| If-match * |PUT slaagt alleen als er een resource bestaat |
| If-none-match * |PUT slaagt alleen als er geen resource bestaat |


## <a name="limits"></a>Limieten

De volgende standaardlimieten zijn van toepassing wanneer u Azure DNS:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Volgende stappen

* Als u aan de slag Azure DNS, leert u hoe u [een DNS-zone maakt](./dns-getstarted-portal.md) en [DNS-records maakt.](./dns-getstarted-portal.md)
* Als u een bestaande DNS-zone wilt migreren, leert u hoe u [een DNS-zonebestand importeert en exporteert.](dns-import-export.md)