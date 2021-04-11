---
title: Overzicht van Azure-netwerk beveiligings groepen
titlesuffix: Azure Virtual Network
description: Meer informatie over netwerk beveiligings groepen. Met netwerk beveiligings groepen kunt u netwerk verkeer tussen Azure-resources filteren.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: kumud
ms.reviewer: kumud
ms.custom: contperf-fy21q1
ms.openlocfilehash: d10c10d602dcfa8f83ee56c2755d800d76516ff1
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058541"
---
# <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen
<a name="network-security-groups"></a>

U kunt een Azure-netwerk beveiligings groep gebruiken voor het filteren van netwerk verkeer naar en van Azure-resources in een virtueel Azure-netwerk. Een netwerk beveiligings groep bevat [beveiligings regels](#security-rules) voor het toestaan of weigeren van binnenkomend netwerk verkeer naar of uitgaand netwerk verkeer van verschillende typen Azure-resources. Voor elke regel kunt u de bron en het doel, de poort en het protocol opgeven.

In dit artikel worden de eigenschappen van een regel voor de netwerk beveiligings groep, de [standaard beveiligings regels](#default-security-rules) die worden toegepast en de regel eigenschappen beschreven die u kunt wijzigen om een uitgebreide [beveiligings regel](#augmented-security-rules)te maken.

## <a name="security-rules"></a><a name="security-rules"></a> Beveiligings regels

Een netwerkbeveiligingsgroep bevat nul regels of zoveel regels als u wilt binnen de [limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) van uw Azure-abonnement. Elke regel geeft de volgende eigenschappen aan:

|Eigenschap  |Uitleg  |
|---------|---------|
|Naam|Een unieke naam binnen de netwerkbeveiligingsgroep.|
|Prioriteit | Een getal tussen 100 en 4096. Regels worden verwerkt in volgorde van prioriteit, waarbij lagere getallen worden verwerkt vóór hogere getallen omdat lagere getallen een hogere prioriteit hebben. Zodra het verkeer overeenkomt met een regel, wordt de verwerking beëindigd. Daardoor worden regels met een lagere prioriteit (een hoger getal) die dezelfde kenmerken hebben als regels met een hogere prioriteit, niet verwerkt.|
|Bron of doel| Een IP-adres, CIDR-blok (bijvoorbeeld 10.0.0.0/24), servicetag of toepassingsbeveiligingsgroep. Als u een adres opgeeft voor een Azure-resource, geeft u het privé-IP-adres op dat aan de resource is toegewezen. Netwerkbeveiligingsgroepen worden verwerkt nadat Azure een openbaar IP-adres vertaalt naar een privé-IP-adres voor binnenkomend verkeer en voordat Azure een privé-IP-adres naar een openbaar IP-adres voor uitgaand verkeer vertaalt. . Als u een bereik, servicetag of toepassingsbeveiligingsgroep opgeeft, hoeft u minder beveiligingsregels te maken. De mogelijkheid om meerdere afzonderlijke IP-adressen en-bereiken op te geven (u kunt niet meerdere service tags of toepassings groepen opgeven) in een regel worden uitgebreide [beveiligings regels](#augmented-security-rules)genoemd. Uitgebreide beveiligingsregels kunnen alleen worden gemaakt in netwerkbeveiligingsgroepen die zijn gemaakt via het Resource Manager-implementatiemodel. U kunt niet meerdere IP-adressen en IP-adresbereiken opgeven in netwerkbeveiligingsgroepen die zijn gemaakt via het klassieke implementatiemodel.|
|Protocol     | TCP, UDP, ICMP, ESP, AH of any.|
|Richting| Hiermee wordt aangegeven of de regel van toepassing is op binnenkomend of uitgaand verkeer.|
|Poortbereik     |U kunt één poort of een poortbereik opgeven. U kunt bijvoorbeeld 80 of 10000-10005 opgeven. Als u bereiken opgeeft, hoeft u minder beveiligingsregels te maken. Uitgebreide beveiligingsregels kunnen alleen worden gemaakt in netwerkbeveiligingsgroepen die zijn gemaakt via het Resource Manager-implementatiemodel. U kunt niet meerdere poorten of poortbereiken opgeven in dezelfde beveiligingsregel in netwerkbeveiligingsgroepen die zijn gemaakt via het klassieke implementatiemodel.   |
|Bewerking     | Toestaan of weigeren        |

Beveiligingsregels voor netwerkbeveiligingsgroepen worden op prioriteit geëvalueerd op basis van de 5 tuple-gegevens (bron, bronpoort, doel, doelpoort en protocol) voor het toestaan of weigeren van het verkeer. U kunt niet twee beveiligings regels maken met dezelfde prioriteit en richting. Voor bestaande verbindingen wordt een stroomrecord gemaakt. Communicatie wordt toegestaan of geweigerd op basis van de verbindingsstatus van de stroomrecord. Met de stroomrecord wordt een netwerkbeveiligingsgroep toegestaan stateful te zijn. Als u bijvoorbeeld een beveiligingsregel voor uitgaand verkeer opgeeft voor elk adres via poort 80, hoeft u geen beveiligingsregel voor binnenkomend verkeer op te geven voor de reacties op het uitgaande verkeer. U hoeft alleen een beveiligingsregel voor binnenkomend verkeer op te geven als de communicatie extern is gestart. Het omgekeerde geldt ook. Als binnenkomend verkeer via een poort is toegestaan, is het niet nodig om een beveiligingsregel voor uitgaand verkeer op te geven om te reageren op verkeer via die poort.

Bestaande verbindingen kunnen niet worden onderbroken wanneer u een beveiligingsregel verwijdert die de stroom mogelijk heeft gemaakt. Verkeersstromen worden onderbroken wanneer er verbindingen worden gestopt en er gedurende minstens een paar minuten in beide richtingen geen verkeer stroomt.

Er gelden beperkingen voor het aantal beveiligingsregels dat u in een netwerkbeveiligingsgroep kunt maken. Zie [Netwerkenlimieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor meer informatie.

### <a name="default-security-rules"></a><a name="default-security-rules"></a> Standaard beveiligings regels

Azure maakt de volgende standaardregels in elke netwerkbeveiligingsgroep die u maakt:

#### <a name="inbound"></a>Inkomend

##### <a name="allowvnetinbound"></a>AllowVNetInBound

|Prioriteit|Bron|Bronpoorten|Doel|Doelpoorten|Protocol|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Alle|Toestaan|

##### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Prioriteit|Bron|Bronpoorten|Doel|Doelpoorten|Protocol|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Alle|Toestaan|

##### <a name="denyallinbound"></a>DenyAllInbound

|Prioriteit|Bron|Bronpoorten|Doel|Doelpoorten|Protocol|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Alle|Weigeren|

#### <a name="outbound"></a>Uitgaand

##### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Prioriteit|Bron|Bronpoorten| Doel | Doelpoorten | Protocol | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Alle | Toestaan |

##### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Prioriteit|Bron|Bronpoorten| Doel | Doelpoorten | Protocol | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Alle | Toestaan |

##### <a name="denyalloutbound"></a>DenyAllOutBound

|Prioriteit|Bron|Bronpoorten| Doel | Doelpoorten | Protocol | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Alle | Weigeren |

In de kolommen **Bron** en **Doel** zijn *VirtualNetwork*, *AzureLoadBalancer* en *Internet*[servicetags](service-tags-overview.md) in plaats van IP-adressen. In de kolom Protocol omvat **elk** het TCP, UDP en ICMP. Wanneer u een regel maakt, kunt u TCP, UDP, ICMP of any opgeven. *0.0.0.0/0* in de kolommen **Bron** en **Doel** vertegenwoordigt alle adressen. Clients als Azure Portal, Azure CLI of Power shell kunnen * of een wille keurige voor deze expressie gebruiken.
 
U kunt de standaardregels niet verwijderen, maar u kunt ze negeren door regels te maken met een hogere prioriteit.

### <a name="augmented-security-rules"></a><a name="augmented-security-rules"></a> Uitgebreide beveiligings regels

Uitgebreide beveiligingsregels vereenvoudigen de beveiligingsdefinitie voor virtuele netwerken, zodat u een uitgebreider en complexer netwerkbeveiligingsbeleid kunt definiëren met minder regels. U kunt meerdere poorten en meerdere expliciete IP-adressen en -bereiken combineren tot één gemakkelijk te begrijpen beveiligingsregel. U kunt uitgebreide regels gebruiken in de velden voor bron, doel en poort van een regel. Om het onderhoud van de definitie van uw beveiligingsregel te vereenvoudigen, kunt u uitgebreide beveiligingsregels combineren met [servicetags](service-tags-overview.md) of [toepassingsbeveiligingsgroepen](#application-security-groups). Er zijn limieten voor het aantal adressen, bereiken en poorten die u in een regel kunt opgeven. Zie [Netwerkenlimieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor meer informatie.

#### <a name="service-tags"></a>Servicetags

Een servicetag vertegenwoordigt een groep IP-adres voorvoegsels van een bepaalde Azure-service. Zo kunt u de complexiteit van regel matige updates voor netwerk beveiligings regels minimaliseren.

Zie [Azure service Tags](service-tags-overview.md)(Engelstalig) voor meer informatie. Zie [netwerk toegang tot PaaS-resources beperken](tutorial-restrict-network-access-to-resources.md)voor een voor beeld van het gebruik van het tag Storage-service om netwerk toegang te beperken.

#### <a name="application-security-groups"></a>Toepassingsbeveiligingsgroepen

Met behulp van toepassingsbeveiligingsgroepen kunt u netwerkbeveiliging configureren als een natuurlijk verlengstuk van de structuur van een toepassing, waarbij u virtuele machines kunt groeperen en netwerkbeveiligingsbeleid kunt definiëren op basis van die groepen. U kunt het beveiligingsbeleid op grote schaal opnieuw gebruiken zonder handmatig onderhoud van expliciete IP-adressen. Zie [toepassings beveiligings groepen](application-security-groups.md)voor meer informatie.

## <a name="azure-platform-considerations"></a>Overwegingen bij het Azure-platform

- **Virtueel IP-adres van het host-knoop punt**: basis infrastructuur services zoals DHCP, DNS, IMDS en status controle worden verschaft via de gevirtualiseerde host-IP-adressen 168.63.129.16 en 169.254.169.254. Deze IP-adressen zijn van Microsoft en zijn de enige gevirtualiseerde IP-adressen die in alle regio's voor dit doel worden gebruikt. De juiste beveiligings regels en de juiste routes bevatten deze platform regels niet. Als u deze basis infrastructuur communicatie wilt overschrijven, kunt u een beveiligings regel voor het weigeren van verkeer maken met behulp van de volgende [service Tags](service-tags-overview.md) in de regels voor de netwerk beveiligings groep: AzurePlatformDNS, AzurePlatformIMDS, AzurePlatformLKM. Meer informatie over het vaststellen van het [filteren van netwerk verkeer](diagnose-network-traffic-filter-problem.md) en het [diagnosticeren van netwerk routering](diagnose-network-routing-problem.md).
- **Licentieverlening (Key Management Service):** voor alle Windows installatiekopieën die op virtuele machines worden uitgevoerd, is een licentie vereist. Hiervoor wordt een licentieaanvraag verstuurd naar de Key Management Service-hostservers waarop dergelijke query's worden afgehandeld. De uitgaande aanvraag wordt gedaan via poort. 1688. Voor implementaties die gebruikmaken van een configuratie met de [standaardroute 0.0.0.0/0](virtual-networks-udr-overview.md#default-route), wordt deze platformregel uitgeschakeld.
- **Virtuele machines in groepen met gelijke taakverdeling**: de bronpoort en het bronadresbereik die worden toegepast, zijn die van de oorspronkelijke computer, niet van de load balancer. De doelpoort en het doeladresbereik zijn die van de doelcomputer, niet van de load balancer.
- **Azure-service-exemplaren**: exemplaren van verschillende Azure-services, zoals HDInsight, toepassingsserviceomgevingen en virtuele-machineschaalsets, worden geïmplementeerd in virtuele netwerksubnetten. Zie [Virtueel netwerk voor Azure-services](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) voor een volledige lijst met services die u in virtuele netwerken kunt implementeren. Zorg ervoor dat u vertrouwd raakt met de poortvereisten voor elke service voordat u een netwerkbeveiligingsgroep toepast op het subnet waarin de resource is geïmplementeerd. Als u poorten weigert die vereist zijn voor de service, werkt de service niet naar behoren.
- **Verzenden van uitgaande e-mail**: micro soft raadt u aan om GEVERIFIEERDe SMTP-relay-Services (meestal verbonden via TCP-poort 587, maar ook vaak andere) te gebruiken om e-mail te verzenden vanuit Azure virtual machines. SMTP-relayservices leggen zich toe op de reputatie van de afzender, om zo de kans dat e-mailproviders van derden berichten weigeren, tot het uiterste terug te dringen. Dergelijke SMTP-relayservices omvatten, maar beperken zich niet tot Exchange Online Protection en SendGrid. Het gebruik van de SMTP-relayservices wordt in Azure op geen enkele wijze beperkt, ongeacht welk type abonnement u hebt. 

  Als u uw Azure-abonnement vóór 15 november 2017 hebt gemaakt, kunt u naast de mogelijkheid om gebruik te maken van SMTP-relayservices ook rechtstreeks e-mail verzenden via TCP-poort 25. Als u uw abonnement na 15 november 2017 hebt gemaakt kunt u mogelijk geen e-mail rechtstreeks via poort 25 verzenden. Hoe uitgaande communicatie via poort 25 verloopt, hangt als volgt samen met het type abonnement dat u hebt:

     - **Enterprise Overeenkomst**: communicatie via poort 25 is toegestaan. U kunt een uitgaand e-mail bericht rechtstreeks van virtuele machines naar externe e-mail providers verzenden, zonder beperkingen van het Azure-platform. 
     - **Betalen naar gebruik:** communicatie via uitgaande poort 25 is voor alle resources geblokkeerd. Als u e-mail vanaf een virtuele machine rechtstreeks naar de externe e-mailproviders wilt verzenden (niet via een geverifieerde SMTP-relay), kunt u een aanvraag indienen om de beperking op te heffen. Het is aan Microsoft om te bepalen of aanvragen worden gecontroleerd en goedgekeurd, en aanvragen worden alleen toegekend nadat er controles ter voorkoming van fraude zijn uitgevoerd. Als u een aanvraag wilt indienen, opent u een ondersteuningsaanvraag met het probleemtype *Technisch*, *Virtuele netwerkverbinding*, *Kan geen e-mail verzenden (SMTP/poort 25)*. Vermeld in uw ondersteuningsaanvraag informatie zoals waarom uw abonnement e-mail rechtstreeks naar e-mailproviders moet kunnen verzenden in plaats van via een geverifieerde SMTP-relay. Als de aanvraag voor uw abonnement wordt toegekend, kunnen alleen de virtuele machines die na de toekenningsdatum zijn gemaakt poort 25 voor uitgaande communicatie gebruiken.
     - **MSDN, Azure Pass, Azure in Open, Education, BizSpark en gratis proefabonnementen**: uitgaande communicatie via poort 25 voor alle resources geblokkeerd. Er kunnen geen aanvragen worden ingediend voor het opheffen van de beperking, omdat zulke aanvragen niet worden toegekend. Als u e-mail moet verzenden vanaf uw virtuele machine, dient u een SMTP-relayservice te gebruiken.
     - **Cloudserviceprovider**: klanten die Azure-resources via een cloudserviceprovider gebruiken, kunnen een ondersteuningsaanvraag maken bij hun cloudserviceprovider. Zij kunnen vervolgens aanvragen dat de provider namens hen een blokkering opheft, als er geen beveiligde SMTP-relay kan worden gebruikt.

  Als u in Azure e-mail kunt verzenden via poort 25, garandeert Microsoft niet dat e-mailproviders inkomende e-mail vanaf uw virtuele machine accepteren. Als een specifieke provider e-mail van uw virtuele machine weigert, dient u samen met de provider alle eventuele problemen met de aflevering van berichten of met de spamfilter op te lossen, of u moet een geverifieerde SMTP-relayservice gebruiken.

## <a name="next-steps"></a>Volgende stappen

* Zie [virtuele netwerk integratie voor Azure-Services](virtual-network-for-azure-services.md) voor meer informatie over welke Azure-resources kunnen worden geïmplementeerd in een virtueel netwerk en of er netwerk beveiligings groepen aan zijn gekoppeld.
* Zie de werking van [netwerk beveiligings groepen](network-security-group-how-it-works.md)voor meer informatie over het evalueren van verkeer met netwerk beveiligings groepen.
* Als u nog nooit een netwerkbeveiligingsgroep hebt gemaakt, kunt u een snelle [zelfstudie](tutorial-filter-network-traffic.md) uitvoeren om enige ervaring met het maken van een netwerkbeveiligingsgroep op te doen.
* Zie [Een netwerkbeveiligingsgroep maken, wijzigen of verwijderen](manage-network-security-group.md) als u bekend bent met het netwerkbeveiligingsgroepen en u ze wilt beheren. 
* Zie [Een probleem met een netwerkverkeersfilter in een virtuele machine vaststellen](diagnose-network-traffic-filter-problem.md) als u communicatieproblemen hebt en problemen met netwerkbeveiligingsgroepen wilt oplossen. 
* Meer informatie over het inschakelen van [stroom logboeken voor netwerk beveiligings groepen](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor het analyseren van netwerk verkeer naar en van resources die een gekoppelde netwerk beveiligings groep hebben.
