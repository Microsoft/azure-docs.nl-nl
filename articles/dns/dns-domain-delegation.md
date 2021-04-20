---
title: Azure DNS delegatieoverzicht
description: Lees hoe u de domeindelegering wijzigt en DNS-naamservers kunt gebruiken om domeinen te hosten.
services: dns
author: rohinkoul
ms.service: dns
ms.date: 04/19/2021
ms.author: rohink
ms.topic: conceptual
ms.openlocfilehash: 4753b07cc2f3ccd998c26a3392eb08c8761dd6f7
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738842"
---
# <a name="delegation-of-dns-zones-with-azure-dns"></a>Delegatie van DNS-zones met Azure DNS

Met Azure DNS kunt u een DNS-zone hosten en de DNS-records voor een domein in Azure beheren. Het domein moet vanaf het bovenliggende domein worden gedelegeerd naar Azure DNS om ervoor te zorgen dat DNS-query's voor een domein Azure DNS bereiken. Houd er rekening mee Azure DNS niet de domeinregistrar is. In dit artikel wordt uitgelegd hoe domeindelegering werkt en hoe domeinen naar Azure DNS delegeert.

## <a name="how-dns-delegation-works"></a>Hoe DNS-delegering werkt

### <a name="domains-and-zones"></a>Domeinen en zones

Het Domain Name System is een hiërarchie van domeinen. De hiërarchie start vanaf het hoofddomein. De naam van dit domein is eenvoudigweg '**.**'.  Hieronder komen de topleveldomeinen, zoals com, net, org, uk of nl.  Onder deze topleveldomeinen komen de secondleveldomeinen, zoals org.uk of co.jp.  Enzovoort. De domeinen in de DNS-hiërarchie worden gehost met behulp van afzonderlijke DNS-zones. Deze zones worden globaal gedistribueerd en gehost door DNS-naamservers over de hele wereld.

**DNS-zone**: een domein is een unieke naam in het Domain Name System, bijvoorbeeld contoso.com. Een DNS-zone wordt gebruikt om de DNS-records voor een bepaald domein te hosten. Het domein contoso.com kan bijvoorbeeld een aantal DNS-records bevatten, zoals mail.contoso.com (voor een e-mailserver) en www.contoso.com (voor een website).

**Domeinregistrar**: een domeinregistrar is een bedrijf dat internetdomeinnamen kan leveren. Het bedrijf controleert of het internetdomein dat u wilt gebruiken, beschikbaar is en biedt u de mogelijkheid om de naam te kopen. Zodra de domeinnaam is geregistreerd, bent u de juridische eigenaar van de domeinnaam. Als u al een internetdomein hebt, gebruikt u de huidige domeinregistrar om te delegeren aan Azure DNS.

Zie [ICANN-Accredited Registrars](https://www.icann.org/registrar-reports/accredited-list.html)voor meer informatie over geaccrediteerde domeinregistrars.

### <a name="resolution-and-delegation"></a>Omzetting en delegering

Er zijn twee typen DNS-servers:

* Een *gezaghebbende* DNS-server host DNS-zones. Deze beantwoordt DNS-query's voor records in deze zones.
* Een *recursieve* DNS-server host geen DNS-zones. De server beantwoordt alle DNS-query's door gezaghebbende DNS-servers aan te roepen om de benodigde gegevens te verzamelen.

Azure DNS biedt een gezaghebbende DNS-service.  Het biedt geen recursieve DNS-service. Cloudservices en virtuele machines in Azure worden automatisch geconfigureerd voor het gebruik van een recursieve DNS-service die afzonderlijk wordt geleverd als onderdeel van de infrastructuur van Azure. Raadpleeg [Naamomzetting in Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) voor meer informatie over het wijzigen van deze DNS-instellingen.

DNS-clients op pc's of mobiele apparaten roepen doorgaans een recursieve DNS-server aan om DNS-query's uit te doen die de clienttoepassingen nodig hebben.

Wanneer een recursieve DNS-server een query voor een DNS-record zoals www.contoso.com ontvangt, moet eerst de naamserver worden gezocht die de zone voor het domein contoso.com host. Om de naamserver te zoeken, wordt er begonnen bij de basisnaamservers, om vervolgens de naamservers te vinden die de com-zone hosten. Vervolgens wordt er een query op de com-naamservers uitgevoerd om de naamservers te zoeken die de zone contoso.com hosten.  Ten slotte kan er een query worden uitgevoerd op deze naamservers voor 'www.contoso.com'.

Deze procedure wordt het omzetten van de DNS-naam genoemd. Strikt genomen bevat DNS-resolutie meer stappen, zoals het volgen van CNAME's, maar dat is niet belangrijk om te begrijpen hoe DNS-delegering werkt.

Hoe 'wijst' een bovenliggende zone naar de naamservers voor een onderliggende zone? Hiervoor wordt gebruikgemaakt van een speciaal type DNS-record, ook wel een NS-record genoemd (NS staat voor 'naamserver'). De hoofdzone bevat bijvoorbeeld NS-records voor com en toont u de naamservers voor de com-zone. De com-zone bevat op zijn beurt NS-records voor contoso.com, die u de naamservers toont voor de zone contoso.com. Het instellen van de NS-records voor een onderliggende zone in de bovenliggende zone wordt het delegeren van het domein genoemd.

In de volgende afbeelding ziet u een voorbeeld van een DNS-query. Contoso.net en partners.contoso.net zijn Azure-DNS-zones.

![DNS-naamserver](./media/dns-domain-delegation/image1.png)

1. De client vraagt `www.partners.contoso.net` aan van zijn lokale DNS-server.
2. De lokale DNS-server heeft de record niet en doet daarom een aanvraag bij de hoofdnaamserver.
3. De hoofdnaamserver beschikt niet over de record, maar kent het adres van de naamserver, maar geeft `.net` dat adres aan de DNS-server
4. De lokale DNS-server verzendt de aanvraag naar de `.net` naamserver.
5. De `.net` naamserver heeft de record niet, maar kent wel het adres van de `contoso.net` naamserver. In dit geval reageert deze met het adres van de naamserver voor de DNS-zone die wordt gehost in Azure DNS.
6. De lokale DNS-server verzendt de aanvraag naar de naamserver voor de `contoso.net` zone die wordt gehost in Azure DNS.
7. De zone `contoso.net` heeft de record niet, maar kent de naamserver voor `partners.contoso.net` en reageert met het adres. In dit geval is het een DNS-zone die wordt gehost in Azure DNS.
8. De lokale DNS-server verzendt de aanvraag naar de naamserver voor de `partners.contoso.net` zone.
9. De `partners.contoso.net` zone heeft de A-record en reageert met het IP-adres.
10. De lokale DNS-server levert het IP-adres aan de client
11. De client maakt verbinding met de website `www.partners.contoso.net`.

Elke delegering bevat twee kopieën van de NS-records. De ene kopie bevindt zich in de bovenliggende zone en wijst naar de onderliggende zone, terwijl de andere kopie zich in de onderliggende zone zelf bevindt. De zone contoso.net bevat de NS-records voor contoso.net (naast de NS-records in 'net'). Deze records worden gezaghebbende NS-records genoemd en bevinden zich in de apex van de onderliggende zone.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u [uw domein delegeert naar Azure DNS](dns-delegate-domain-azure-dns.md)
