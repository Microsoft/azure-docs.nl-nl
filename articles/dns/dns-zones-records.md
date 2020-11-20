---
title: Overzicht van DNS-zones en records-Azure DNS
description: Overzicht van ondersteuning voor het hosten van DNS-zones en-records in Microsoft Azure DNS.
author: rohinkoul
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: rohink
ms.openlocfilehash: 41eb15a38c97532951723f12d1ac74c90c838eb1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968193"
---
# <a name="overview-of-dns-zones-and-records"></a>Overzicht van DNS-zones en -records

Op deze pagina worden de belangrijkste concepten van domeinen, DNS-zones, DNS-records en-record sets uitgelegd en hoe deze worden ondersteund in Azure DNS.

## <a name="domain-names"></a>Domeinnamen

Het Domain Name System is een hiërarchie van domeinen. De hiërarchie start vanaf het hoofddomein. De naam van dit domein is eenvoudigweg '**.**'.  Hieronder komen de topleveldomeinen, zoals com, net, org, uk of nl.  Onder de topleveldomeinen komen de secondleveldomeinen, zoals org.uk of co.jp. De domeinen in de DNS-hiërarchie zijn wereld wijd gedistribueerd, gehost door DNS-naam servers over de hele wereld.

Een domein naam registratie is een organisatie waarmee u een domein naam kunt kopen, zoals `contoso.com` .  Als u een domein naam aanschaft, kunt u de DNS-hiërarchie onder die naam beheren, bijvoorbeeld zodat u de naam `www.contoso.com` naar de website van uw bedrijf kunt sturen. De registratie van het domein kan in eigen naam servers worden gehost of u kunt alternatieve naam servers opgeven.

Azure DNS biedt een wereld wijd gedistribueerde naam server infrastructuur met hoge Beschik baarheid, die u kunt gebruiken om uw domein te hosten. Door uw domeinen in Azure DNS te hosten, kunt u uw DNS-records met dezelfde referenties, Api's, hulpprogram ma's, facturering en ondersteuning als uw andere Azure-Services beheren.

Azure DNS biedt momenteel geen ondersteuning voor de aanschaf van domein namen. Als u een domein naam wilt kopen, moet u een domein naam van derden gebruiken. De registratie service berekent doorgaans een klein jaarlijks bedrag. De domeinen kunnen vervolgens worden gehost in Azure DNS voor het beheer van DNS-records. Zie [Een domein aan Azure DNS overdragen](dns-domain-delegation.md) voor meer informatie.

## <a name="dns-zones"></a>DNS-zones

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>DNS-records

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Time-to-Live

De TTL (time to Live), geeft aan hoe lang elke record door clients in de cache wordt opgeslagen voordat opnieuw een query wordt uitgevoerd. In het bovenstaande voor beeld is de TTL 3600 seconden of 1 uur.

In Azure DNS wordt de TTL voor de recordset opgegeven, niet voor elke record, zodat dezelfde waarde wordt gebruikt voor alle records in die recordset.  U kunt een wille keurige TTL-waarde tussen 1 en 2.147.483.647 seconden opgeven.

### <a name="wildcard-records"></a>Joker records

Azure DNS ondersteunt [recordsets met jokertekens](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Joker records worden geretourneerd als antwoord op een wille keurige query met een overeenkomende naam (tenzij er een nauwere overeenkomst is met een niet-Joker record). Azure DNS ondersteunt Joker record sets voor alle record typen behalve NS en SOA.

Als u een set met Joker tekens wilt maken, gebruikt u de naam van de Recordset \* . U kunt ook een naam met ' \* ' gebruiken als het meest linkse label, bijvoorbeeld ' \* . foo '.

### <a name="caa-records"></a>CAA records

Met CAA-records kunnen domein eigen aars opgeven welke certificerings instanties (Ca's) geautoriseerd zijn om certificaten voor hun domein te verlenen. Op die manier kunnen certificerings instanties in sommige gevallen niet-verleende certificaten vermijden. CAA records hebben drie eigenschappen:
* **Flags**: dit is een geheel getal tussen 0 en 255, dat wordt gebruikt om de kritieke vlag weer te geven die een speciale betekenis heeft per [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Tag**: een ASCII-teken reeks die een van de volgende kan zijn:
    * **probleem**: gebruik deze als u ca's wilt opgeven die certificaten mogen uitgeven (alle typen)
    * **issuewild**: gebruik deze als u ca's wilt opgeven die certificaten mogen verlenen (alleen voor joker tekens)
    * **iodef**: Geef een e-mail adres of hostnaam op waarnaar ca's kunnen worden gewaarschuwd voor niet-geautoriseerde certificaat uitgifte aanvragen
* **Waarde**: de waarde voor de specifieke tag gekozen

### <a name="cname-records"></a>CNAME-records

CNAME-recordsets kunnen niet worden gecombineerd met andere recordsets met dezelfde naam. U kunt bijvoorbeeld geen CNAME-recordset maken met de relatieve naam ' www ' en een record met de relatieve naam ' www ' op hetzelfde moment.

Omdat de zone Apex (name = ' \@ ') altijd de NS-en SOA-record sets bevat die zijn gemaakt toen de zone werd gemaakt, kunt u geen CNAME-recordset maken op de zone Apex.

Deze beperkingen komen voort uit de DNS-standaarden en zijn geen beperkingen van Azure DNS.

### <a name="ns-records"></a>NS-records

De NS-record die is ingesteld op de zone Apex (naam \@ ) wordt automatisch gemaakt bij elke DNS-zone en wordt automatisch verwijderd wanneer de zone wordt verwijderd (deze kan niet afzonderlijk worden verwijderd).

Deze recordset bevat de namen van de Azure DNS naam servers die zijn toegewezen aan de zone. U kunt aanvullende naam servers toevoegen aan deze NS-Recordset, ter ondersteuning van co-hosting domeinen met meer dan één DNS-provider. U kunt ook de TTL en meta gegevens voor deze recordset wijzigen. U kunt de vooraf ingevulde Azure DNS naam servers echter niet verwijderen of wijzigen. 

Dit geldt alleen voor de NS-recordset die is ingesteld op de zone Apex. Andere NS-record sets in uw zone (zoals gebruikt voor het delegeren van onderliggende zones) kunnen zonder beperking worden gemaakt, gewijzigd en verwijderd.

### <a name="soa-records"></a>SOA-records

Er wordt automatisch een SOA-recordset gemaakt aan de Apex van elke zone (naam = ' \@ ') en wordt automatisch verwijderd wanneer de zone wordt verwijderd.  SOA-records kunnen niet afzonderlijk worden gemaakt of verwijderd.

U kunt alle eigenschappen van de SOA-record wijzigen, met uitzonde ring van de eigenschap host, die vooraf is geconfigureerd om te verwijzen naar de naam van de primaire naam server die wordt opgegeven door Azure DNS.

Het serie nummer van de zone in de SOA-record wordt niet automatisch bijgewerkt wanneer er wijzigingen worden aangebracht in de records in de zone. Het kan hand matig worden bijgewerkt door de SOA-record te bewerken, indien nodig.

### <a name="spf-records"></a>SPF-records

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>SRV-records

[SRV-records](https://en.wikipedia.org/wiki/SRV_record) worden door verschillende services gebruikt om server locaties op te geven. Bij het opgeven van een SRV-record in Azure DNS:

* De *service* en het *protocol* moeten worden opgegeven als onderdeel van de naam van de recordset, voorafgegaan door onderstrepings tekens.  Bijvoorbeeld: ' \_ SIP. \_ tcp.name '.  Voor een record op de zone Apex is het niet nodig om \@ in de record naam op te geven. u hoeft alleen de service en het protocol te gebruiken, bijvoorbeeld ' \_ SIP '. \_ TCP.
* De *prioriteit*, het *gewicht*, de *poort* en het *doel* worden opgegeven als para meters van elke record in de recordset.

### <a name="txt-records"></a>TXT-records

TXT-records worden gebruikt voor het toewijzen van domein namen aan wille keurige teken reeksen. Ze worden gebruikt in meerdere toepassingen, in het bijzonder met betrekking tot e-mail configuratie, zoals de [SPF (Sender Policy Framework)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) en [DomainKeys (dkim) geïdentificeerde e-mail](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

De DNS-standaarden staan toe dat één TXT-record meerdere teken reeksen bevat, die elk Maxi maal 254 tekens lang kunnen zijn. Wanneer er meerdere teken reeksen worden gebruikt, worden deze samengevoegd door clients en behandeld als één teken reeks.

Wanneer u de Azure DNS REST API aanroept, moet u elke teken reeks afzonderlijk opgeven.  Wanneer u de Azure Portal-, Power shell-of CLI-interface gebruikt, moet u één teken reeks per record opgeven. dit wordt zo nodig automatisch onderverdeeld in 254-teken segmenten.

De meerdere teken reeksen in een DNS-record mogen niet worden verward met de meerdere TXT-records in een TXT-Recordset.  Een TXT-recordset kan meerdere records bevatten, *die elk* meerdere teken reeksen kunnen bevatten.  Azure DNS ondersteunt een totale teken reeks lengte van Maxi maal 1024 tekens in elke TXT-recordset (in alle records gecombineerd).

## <a name="tags-and-metadata"></a>Tags en meta gegevens

### <a name="tags"></a>Tags

Tags zijn een lijst met naam/waarde-paren en worden gebruikt door Azure Resource Manager om resources te labelen.  Azure Resource Manager maakt gebruik van tags om gefilterde weer gaven van uw Azure-factuur in te scha kelen. Daarnaast kunt u een beleid instellen waarvoor Tags zijn vereist. Zie voor meer informatie over tags [Tags gebruiken om uw Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md).

Azure DNS ondersteunt het gebruik van Azure Resource Manager-Tags op DNS-zone bronnen.  Tags in DNS-record sets worden niet ondersteund, hoewel als alternatieve meta gegevens wordt ondersteund voor DNS-record sets, zoals hieronder wordt uitgelegd.

### <a name="metadata"></a>Metagegevens

Als alternatief voor labels voor record sets Azure DNS ondersteunt het aantekeningen maken van record sets met behulp van meta gegevens.  Net als bij Tags kunt u met meta gegevens naam/waarde-paren koppelen aan elke recordset.  Dit kan handig zijn, bijvoorbeeld om het doel van elke recordset te registreren.  In tegens telling tot Tags kunnen meta gegevens niet worden gebruikt om een gefilterde weer gave van uw Azure-factuur te bieden en kan niet worden opgegeven in een Azure Resource Manager beleid.

## <a name="etags"></a>ETags

Stel dat twee personen of twee processen proberen om een DNS-record tegelijkertijd te wijzigen. Welke één WINS? En weet de winnaar dat er wijzigingen zijn aangebracht die zijn gemaakt door iemand anders?

Azure DNS gebruikt eTags om gelijktijdige wijzigingen in dezelfde resource veilig te verwerken. ETags zijn gescheiden van [Azure Resource Manager Tags](#tags). Aan elke DNS-resource (zone of Recordset) is een ETag gekoppeld. Wanneer een resource wordt opgehaald, wordt ook de ETAG opgehaald. Wanneer u een resource bijwerkt, kunt u ervoor kiezen om de ETAG terug te geven, zodat Azure DNS kunt controleren of de ETAG op de server overeenkomt. Omdat elke update van een resource resulteert in de ETAG die opnieuw wordt gegenereerd, geeft een ETag niet-overeenkomen aan dat er een gelijktijdige wijziging heeft plaatsgevonden. ETags kan ook worden gebruikt bij het maken van een nieuwe resource om ervoor te zorgen dat de resource nog niet bestaat.

Azure DNS Power shell gebruikt standaard eTags om gelijktijdige wijzigingen in zones en record sets te blok keren. De schakel optie *-overwrite* kan worden gebruikt om ETAG-controles te onderdrukken, in welk geval alle gelijktijdige wijzigingen die zijn opgetreden, worden overschreven.

Op het niveau van de Azure DNS REST API worden eTags opgegeven met behulp van HTTP-headers.  Hun gedrag wordt vermeld in de volgende tabel:

| Header | Gedrag |
| --- | --- |
| Geen |PUT altijd geslaagd (geen ETag-controles) |
| If-match \<etag> |Alleen geslaagd als resource bestaat en ETAG overeenkomt |
| If-match * |Alleen geslaagd als de resource bestaat |
| If-None-Match * |Alleen geslaagd als de resource niet bestaat |


## <a name="limits"></a>Limieten

De volgende standaard limieten zijn van toepassing wanneer u Azure DNS gebruikt:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Volgende stappen

* Als u Azure DNS wilt gaan gebruiken, leest u hoe u [een DNS-zone maakt](./dns-getstarted-portal.md) en hoe u [DNS-records maakt](./dns-getstarted-portal.md).
* Als u een bestaande DNS-zone wilt migreren, leert u hoe u [een DNS-zone bestand importeert en exporteert](dns-import-export.md).