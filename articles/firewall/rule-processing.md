---
title: Regels voor de logicaverwerking in Azure Firewall
description: Azure Firewall heeft NAT-regels, netwerk regels en toepassings regels. De regels worden verwerkt volgens het regel type.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/01/2021
ms.author: victorh
ms.openlocfilehash: bbf838cfa2a6addc665df4b62e2322d056778b49
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741358"
---
# <a name="configure-azure-firewall-rules"></a>Azure Firewall-regels configureren
U kunt NAT-regels, netwerk regels en toepassings regels configureren op Azure Firewall. Regel verzamelingen worden verwerkt op basis van het regel type in volg orde van prioriteit, waarbij een lager aantal is van 100 tot 65.000. De naam van een regel verzameling mag alleen letters, cijfers, onderstrepings tekens, punten of afbreek streepjes bevatten. De naam moet beginnen met een letter of cijfer en eindigen op een letter, cijfer of onderstrepings teken. De maximale naam mag Maxi maal 80 tekens lang zijn.

U kunt de prioriteits nummers van uw regel verzameling het beste in 100 stappen (100, 200, 300 enzovoort) plaatsen, zodat u zo nodig ruimte hebt om meer regel verzamelingen toe te voegen.

> [!NOTE]
> Als u filtering op basis van bedreigings informatie inschakelt, zijn deze regels de hoogste prioriteit en worden ze altijd eerst verwerkt. Threat-Intelligence-filtering kan verkeer weigeren voordat er geconfigureerde regels worden verwerkt. Zie [Azure firewall op Threat Intelligence gebaseerde filtering](threat-intel.md)voor meer informatie.

## <a name="outbound-connectivity"></a>Uitgaande connectiviteit

### <a name="network-rules-and-applications-rules"></a>Netwerk regels en toepassings regels

Als u netwerk regels en toepassings regels configureert, worden netwerk regels in volg orde van prioriteit toegepast vóór toepassings regels. De regels worden afgesloten. Dus als er een overeenkomst wordt gevonden in een netwerk regel, worden er geen andere regels verwerkt.  Als er geen overeenkomst met de netwerk regel is en als het Protocol HTTP, HTTPS of MSSQL is, wordt het pakket vervolgens geëvalueerd op basis van de toepassings regels in volg orde van prioriteit. Als er nog geen overeenkomst wordt gevonden, wordt het pakket vergeleken met de [verzameling van de infrastructuur regel](infrastructure-fqdns.md). Als er nog steeds geen overeenkomst is, wordt het pakket standaard geweigerd.

#### <a name="network-rule-protocol"></a>Netwerk regel Protocol

Netwerk regels kunnen worden geconfigureerd voor **TCP**, **UDP**, **ICMP** of **een** IP-protocol. Elk IP-protocol bevat alle IP-protocollen zoals gedefinieerd in het [protocol nummer van de Internet Assigned Numbers Authority (IANA)](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) . Als een doel poort expliciet is geconfigureerd, wordt de regel omgezet naar een TCP + UDP-regel.

Vóór 9 november 2020 **alle** bewarende **TCP**, **UDP** of **ICMP**. Het is dus mogelijk dat u voor die datum een regel hebt geconfigureerd met protocol = any en de doel poorten = ' * '. Als u geen IP-protocol wilt toestaan zoals momenteel is gedefinieerd, wijzigt u de regel om de gewenste protocollen expliciet te configureren (TCP, UDP of ICMP).

## <a name="inbound-connectivity"></a>Binnenkomende connectiviteit

### <a name="nat-rules"></a>NAT-regels

Inkomende Internet connectiviteit kan worden ingeschakeld door DNAT (Destination Network Address Translation) te configureren, zoals beschreven in [zelf studie: inkomend verkeer filteren met Azure firewall DNAT met behulp van de Azure Portal](tutorial-firewall-dnat.md). NAT-regels worden vóór netwerk regels op prioriteit toegepast. Als er een overeenkomst wordt gevonden, wordt er een impliciet overeenkomende netwerk regel toegevoegd om het vertaalde verkeer toe te staan. Uit veiligheids overwegingen is het raadzaam een specifieke Internet bron toe te voegen om DNAT toegang te geven tot het netwerk en om te voor komen dat Joker tekens worden gebruikt.

Toepassings regels worden niet toegepast voor binnenkomende verbindingen. Als u echter het binnenkomende HTTP/S-verkeer wilt filteren, gebruikt u Web Application firewall (WAF). Zie [Wat is Azure Web Application firewall?](../web-application-firewall/overview.md) voor meer informatie.

## <a name="examples"></a>Voorbeelden

In de volgende voor beelden ziet u de resultaten van sommige van deze regel combinaties.

### <a name="example-1"></a>Voorbeeld 1

De verbinding met google.com is toegestaan vanwege een overeenkomende netwerk regel.

**Netwerk regel**

- Actie: Toestaan


|naam  |Protocol  |Brontype  |Bron  |Doeltype  |Doel adres  |Doelpoorten|
|---------|---------|---------|---------|----------|----------|--------|
|Toestaan-Web     |TCP|IP-adres|*|IP-adres|*|80.443

**Toepassings regel**

- Actie: Weigeren

|naam  |Brontype  |Bron  |Protocol:Poort|Doel-FQDN-naam|
|---------|---------|---------|---------|----------|----------|
|Weigeren: Google     |IP-adres|*|http: 80, https: 443|google.com

**Resultaat**

De verbinding met google.com is toegestaan omdat het pakket overeenkomt met de regel voor het *toestaan* van webnetwerk. Regel verwerking stopt op dit moment.

### <a name="example-2"></a>Voorbeeld 2

SSH-verkeer wordt geweigerd omdat de verzameling netwerk regels voor *weigeren* van een hogere prioriteit de waarde blokkeert.

**Verzameling van netwerk regels 1**

- Naam: toestaan-verzameling
- Prioriteit: 200
- Actie: Toestaan

|naam  |Protocol  |Brontype  |Bron  |Doeltype  |Doel adres  |Doelpoorten|
|---------|---------|---------|---------|----------|----------|--------|
|Toestaan-SSH     |TCP|IP-adres|*|IP-adres|*|22

**Verzameling van netwerk regels 2**

- Naam: weigeren-verzameling
- Prioriteit: 100
- Actie: Weigeren

|naam  |Protocol  |Brontype  |Bron  |Doeltype  |Doel adres  |Doelpoorten|
|---------|---------|---------|---------|----------|----------|--------|
|Weigeren-SSH     |TCP|IP-adres|*|IP-adres|*|22

**Resultaat**

SSH-verbindingen worden geweigerd omdat een netwerk regel verzameling met een hogere prioriteit deze blokkeert. Regel verwerking stopt op dit moment.

## <a name="rule-changes"></a>Regel wijzigingen

Als u een regel wijzigt om eerder toegestaan verkeer te weigeren, worden alle relevante bestaande sessies verwijderd.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [implementeren en configureren van een Azure firewall](tutorial-firewall-deploy-portal.md).
