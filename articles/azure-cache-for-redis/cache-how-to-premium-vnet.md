---
title: Een virtueel netwerk configureren-Azure-cache voor Premium-tier voor redis-exemplaar
description: Meer informatie over het maken en beheren van virtuele netwerk ondersteuning voor uw Azure-cache voor de Premium-laag voor een redis-exemplaar
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 94bbb9bb683f40d44d6649802b66bda6feeee218
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375269"
---
# <a name="configure-virtual-network-support-for-a-premium-azure-cache-for-redis-instance"></a>Ondersteuning voor virtuele netwerken configureren voor een Premium Azure-cache voor een redis-exemplaar

[Azure Virtual Network](https://azure.microsoft.com/services/virtual-network/) -implementatie biedt verbeterde beveiliging en isolatie naast subnetten, beleid voor toegangs beheer en andere functies om de toegang verder te beperken. Wanneer een Azure-cache voor redis-exemplaar is geconfigureerd met een virtueel netwerk, kan het niet openbaar worden benaderd en kan alleen worden geopend vanuit virtuele machines en toepassingen binnen het virtuele netwerk. In dit artikel wordt beschreven hoe u ondersteuning voor virtuele netwerken kunt configureren voor een Azure-cache met een Premium-laag voor een redis-exemplaar.

> [!NOTE]
> Azure cache voor redis ondersteunt zowel het klassieke implementatie model als Azure Resource Manager virtuele netwerken.
> 

## <a name="set-up-virtual-network-support"></a>Ondersteuning voor virtuele netwerken instellen

Ondersteuning voor virtuele netwerken is geconfigureerd in het deel venster **nieuwe Azure-cache voor redis** tijdens het maken van de cache.

1. Als u een Premium-laag cache wilt maken, meldt u zich aan bij de [Azure Portal](https://portal.azure.com) en selecteert u **een resource maken**. Naast het maken van caches in de Azure Portal, kunt u ze ook maken met behulp van Resource Manager-sjablonen, Power shell of de Azure CLI. Zie [Create a cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)(Engelstalig) voor meer informatie over het maken van een Azure-cache voor redis-instantie.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Scherm opname waarin een resource wordt gemaakt.":::
   
1. Selecteer op de pagina **Nieuw** de optie **data bases**. Selecteer vervolgens **Azure-cache voor redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Scherm opname van het selecteren van Azure cache voor redis.":::

1. Configureer op de pagina **nieuw redis cache** de instellingen voor de nieuwe Premium-tier-cache.
   
   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-naam** | Geef een wereldwijd unieke naam op. | De cache naam moet een teken reeks zijn tussen 1 en 63 tekens die alleen cijfers, letters of afbreek streepjes bevatten. De naam moet beginnen en eindigen met een getal of letter en mag geen opeenvolgende afbreek streepjes bevatten. De *hostnaam* van uw cache-exemplaar wordt *\<DNS name>.redis.cache.windows.net*. |
   | **Abonnement** | Selecteer uw abonnement in de vervolgkeuzelijst. | Het abonnement waarmee dit nieuwe Azure Cache voor Redis-exemplaar wordt gemaakt. |
   | **Resourcegroep** | Selecteer een resource groep in de vervolg keuzelijst of selecteer **nieuwe maken** en voer een nieuwe naam voor de resource groep in. | De naam voor de resource groep waarin u uw cache en andere resources wilt maken. Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. |
   | **Locatie** | Selecteer een locatie in de vervolg keuzelijst. | Selecteer een [regio](https://azure.microsoft.com/regions/) in de buurt van andere services die gaan gebruikmaken van de cache. |
   | **Cachetype** |Selecteer een Premium-laag cache in de vervolg keuzelijst om functies van de Premium-laag te configureren. Zie [Prijzen van Azure Cache voor Redis](https://azure.microsoft.com/pricing/details/cache/) voor meer informatie. |  De prijscategorie bepaalt de grootte, prestaties en functies die beschikbaar zijn voor de cache. Zie [Azure cache for redis Overview](cache-overview.md)(Engelstalig) voor meer informatie. |

1. Selecteer het tabblad **netwerken** of selecteer de knop **netwerk** onder aan de pagina.

1. Op het tabblad **netwerken** selecteert u **virtuele netwerken** als verbindings methode. Als u een nieuw virtueel netwerk wilt gebruiken, maakt u het eerst door de stappen in [een virtueel netwerk maken met](../virtual-network/manage-virtual-network.md#create-a-virtual-network) behulp van de Azure portal te volgen of [een virtueel netwerk (klassiek) te maken met behulp van de Azure Portal](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal). Ga vervolgens terug naar het deel venster **nieuwe Azure-cache voor redis** om uw Premium-laag cache te maken en te configureren.

   > [!IMPORTANT]
   > Wanneer u Azure cache voor redis implementeert voor een virtueel netwerk van Resource Manager, moet de cache zich in een toegewijd subnet bevinden dat geen andere resources bevat behalve Azure cache voor redis-exemplaren. Als u probeert een Azure-cache voor een redis-exemplaar te implementeren in een subnet van een virtueel netwerk van Resource Manager dat andere bronnen bevat, mislukt de implementatie.
   > 
   > 

   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Virtueel netwerk** | Selecteer uw virtuele netwerk in de vervolg keuzelijst. | Selecteer een virtueel netwerk dat zich in hetzelfde abonnement en dezelfde locatie bevinden als de cache. |
   | **Subnet** | Selecteer uw subnet in de vervolg keuzelijst. | Het adres bereik van het subnet moet de CIDR-notatie hebben (bijvoorbeeld 192.168.1.0/24). Het moet deel uitmaken van de adres ruimte van het virtuele netwerk. |
   | **Statisch IP-adres** | Beschrijving Voer een statisch IP-adres in. | Als u geen statisch IP-adres opgeeft, wordt automatisch een IP-adres gekozen. |

   > [!IMPORTANT]
   > Azure reserveert een aantal IP-adressen binnen elk subnet en deze adressen kunnen niet worden gebruikt. Het eerste en laatste IP-adres van de subnetten zijn gereserveerd voor protocol conformiteit, samen met drie meer adressen die worden gebruikt voor Azure-Services. Zie [zijn er beperkingen voor het gebruik van IP-adressen in deze subnetten?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) voor meer informatie.
   > 
   > Naast de IP-adressen die worden gebruikt door de infra structuur van het virtuele Azure-netwerk, gebruikt elk Azure cache voor redis-exemplaar in het subnet twee IP-adressen per Shard en één extra IP-adres voor de load balancer. Een niet-geclusterde cache wordt beschouwd als een Shard.
   > 

1. Selecteer het tabblad **volgende: Geavanceerd** of selecteer de knop **volgende: Geavanceerd** onder aan de pagina.

1. Configureer op het tabblad **Geavanceerd** voor een cache-exemplaar voor de Premium-laag de instellingen voor niet-TLS-poort, clustering en gegevens persistentie.

1. Selecteer het tabblad **volgende: Labels** of selecteer de knop **volgende: Labels** onder aan de pagina.

1. U kunt eventueel op het tabblad **labels** de naam en waarde opgeven als u de resource wilt categoriseren.

1. Selecteer **Controleren + maken**. U gaat naar het tabblad **controleren + maken** , waar Azure uw configuratie valideert.

1. Wanneer het bericht groene **validatie is voltooid** wordt weer gegeven, selecteert u **maken**.

Het duurt even voor de cache is gemaakt. U kunt de voortgang bekijken op de **overzichtspagina** van Azure Cache voor Redis. Als u bij **Status** **Wordt uitgevoerd** ziet staan, kunt u de cache gebruiken. Nadat de cache is gemaakt, kunt u de configuratie voor het virtuele netwerk weer geven door **Virtual Network** te selecteren in het menu **resource** .

![Virtueel netwerk][redis-cache-vnet-info]

Als u verbinding wilt maken met uw Azure-cache voor redis-instantie wanneer u een virtueel netwerk gebruikt, geeft u de hostnaam van uw cache op in de connection string, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

## <a name="azure-cache-for-redis-virtual-network-faq"></a>Veelgestelde vragen over Azure cache voor redis virtuele netwerken

De volgende lijst bevat antwoorden op veelgestelde vragen over Azure cache voor redis-schaling.

* Wat zijn enkele veelvoorkomende problemen met de configuratie van Azure cache voor redis en virtuele netwerken?
* [Hoe kan ik controleren of mijn cache werkt in een virtueel netwerk?](#how-can-i-verify-that-my-cache-is-working-in-a-virtual-network)
* Waarom wordt er een fout melding weer gegeven dat het externe certificaat ongeldig is wanneer ik verbinding probeer te maken met mijn Azure-cache voor redis-exemplaar in een virtueel netwerk?
* [Kan ik virtuele netwerken met een Standard-of Basic-cache gebruiken?](#can-i-use-virtual-networks-with-a-standard-or-basic-cache)
* Waarom mislukt het maken van een Azure-cache voor een redis-exemplaar in sommige subnetten, maar niet op andere?
* [Wat zijn de adres ruimte vereisten voor het subnet?](#what-are-the-subnet-address-space-requirements)
* [Werken alle cache functies wanneer een cache wordt gehost in een virtueel netwerk?](#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks"></a>Wat zijn enkele veelvoorkomende problemen met de configuratie van Azure cache voor redis en virtuele netwerken?

Wanneer Azure cache voor redis wordt gehost in een virtueel netwerk, worden de poorten in de volgende tabellen gebruikt.

>[!IMPORTANT]
>Als de poorten in de volgende tabellen zijn geblokkeerd, werkt de cache mogelijk niet correct. Als u een of meer van deze poorten hebt geblokkeerd, wordt het meest voorkomende fout probleem veroorzaakt wanneer u Azure cache gebruikt voor redis in een virtueel netwerk.
> 

- [Vereisten voor de uitgaande poort](#outbound-port-requirements)
- [Vereisten voor inkomende poort](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Vereisten voor de uitgaande poort

Er zijn negen uitgaande poort vereisten. Uitgaande aanvragen in deze bereiken zijn ofwel uitgaand voor andere services die nodig zijn om de cache te laten functioneren of intern voor het redis-subnet voor communicatie tussen knoop punten. Voor geo-replicatie bestaan er extra uitgaande vereisten voor communicatie tussen subnetten van de primaire en replica-cache.

| Poorten | Richting | Transport Protocol | Doel | Lokaal IP-adres | Extern IP-adres |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Uitgaand |TCP |Afhankelijkheden van redis op Azure Storage/PKI (Internet) | (Redis-subnet) |* <sup>3</sup> |
| 443 | Uitgaand | TCP | Afhankelijkheid van redis op Azure Key Vault en Azure Monitor | (Redis-subnet) | AzureKeyVault, AzureMonitor <sup>1</sup> |
| 53 |Uitgaand |TCP/UDP |Redis-afhankelijkheden op DNS (Internet/virtueel netwerk) | (Redis-subnet) | 168.63.129.16 en 169.254.169.254 <sup>2</sup> en een aangepaste DNS-server voor het subnet <sup>3</sup> |
| 8443 |Uitgaand |TCP |Interne communicatie voor redis | (Redis-subnet) | (Redis-subnet) |
| 10221-10231 |Uitgaand |TCP |Interne communicatie voor redis | (Redis-subnet) | (Redis-subnet) |
| 20226 |Uitgaand |TCP |Interne communicatie voor redis | (Redis-subnet) |(Redis-subnet) |
| 13000-13999 |Uitgaand |TCP |Interne communicatie voor redis | (Redis-subnet) |(Redis-subnet) |
| 15000-15999 |Uitgaand |TCP |Interne communicatie voor redis en geo-replicatie | (Redis-subnet) |(Redis-subnet) (Geo-replica peer-subnet) |
| 6379-6380 |Uitgaand |TCP |Interne communicatie voor redis | (Redis-subnet) |(Redis-subnet) |

<sup>1</sup> u kunt de AzureKeyVault-en AzureMonitor-service tags gebruiken met Resource Manager-netwerk beveiligings groepen (nsg's).

<sup>2</sup> deze IP-adressen die eigendom zijn van micro soft, worden gebruikt om de host-VM te adresseren die Azure DNS.

<sup>3</sup> deze informatie is niet nodig voor subnetten zonder aangepaste DNS-server of nieuwere redis-caches die aangepaste DNS negeren.

<sup>4</sup> Zie [aanvullende vereisten voor virtuele netwerk connectiviteit](#additional-virtual-network-connectivity-requirements)voor meer informatie.

#### <a name="geo-replication-peer-port-requirements"></a>Vereisten voor geo-replicatie peer-poort

Als u geo-replicatie tussen caches in virtuele Azure-netwerken gebruikt, moet u poort 15000-15999 voor het hele subnet in zowel binnenkomende *als* uitgaande richting op beide caches blok keren. Met deze configuratie kunnen alle replica onderdelen in het subnet direct met elkaar communiceren, zelfs als er sprake is van een toekomstige geo-failover.

#### <a name="inbound-port-requirements"></a>Vereisten voor inkomende poort

Er zijn acht vereisten voor het poort bereik voor inkomend verkeer. Inkomende aanvragen in deze bereiken zijn ofwel inkomend van andere services die worden gehost in hetzelfde virtuele netwerk of intern voor de communicatie van het redis-subnet.

| Poorten | Richting | Transport Protocol | Doel | Lokaal IP-adres | Extern IP-adres |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Inkomend |TCP |Client communicatie naar redis, Azure-taak verdeling | (Redis-subnet) | (Redis subnet), (client subnet), AzureLoadBalancer <sup>1</sup> |
| 8443 |Inkomend |TCP |Interne communicatie voor redis | (Redis-subnet) |(Redis-subnet) |
| 8500 |Inkomend |TCP/UDP |Azure-taakverdeling | (Redis-subnet) | AzureLoadBalancer |
| 10221-10231 |Inkomend |TCP |Client communicatie met redis-clusters, interne communicatie voor redis | (Redis-subnet) |(Redis subnet), AzureLoadBalancer, (client-subnet) |
| 13000-13999 |Inkomend |TCP |Client communicatie met redis-clusters, Azure-taak verdeling | (Redis-subnet) | (Redis subnet), (client subnet), AzureLoadBalancer |
| 15000-15999 |Inkomend |TCP |Client communicatie met redis-clusters, Azure-taak verdeling en geo-replicatie | (Redis-subnet) | (Redis subnet), (client subnet), AzureLoadBalancer, (geo-replica peer subnet) |
| 16001 |Inkomend |TCP/UDP |Azure-taakverdeling | (Redis-subnet) | AzureLoadBalancer |
| 20226 |Inkomend |TCP |Interne communicatie voor redis | (Redis-subnet) |(Redis-subnet) |

<sup>1</sup> u kunt de service label AzureLoadBalancer voor Resource Manager of AZURE_LOADBALANCER voor het klassieke implementatie model voor het ontwerpen van de NSG-regels gebruiken.

#### <a name="additional-virtual-network-connectivity-requirements"></a>Aanvullende vereisten voor de connectiviteit van virtueel netwerk

Er zijn netwerk connectiviteits vereisten voor Azure cache voor redis die mogelijk niet in eerste instantie worden voldaan in een virtueel netwerk. Azure cache voor redis vereist dat alle volgende items goed werken wanneer ze worden gebruikt binnen een virtueel netwerk:

* Uitgaande netwerk verbinding met Azure Storage eind punten wereld wijd. Eind punten die zich in dezelfde regio bevinden als de Azure-cache voor redis-exemplaren en opslag eindpunten die zich in *andere* Azure-regio's bevinden, worden opgenomen. Azure Storage-eind punten worden omgezet onder de volgende DNS-domeinen: *Table.core.Windows.net*, *blob.core.Windows.net*, *Queue.core.Windows.net* en *File.core.Windows.net*.
* Uitgaande netwerk verbinding met *OCSP.Digicert.com*, *crl4.Digicert.com*, *OCSP.msocsp.com*, *mscrl.Microsoft.com*, *crl3.Digicert.com*, *cacerts.Digicert.com*, *oneocsp.Microsoft.com* en *CRL.Microsoft.com*. Deze connectiviteit is vereist voor de ondersteuning van TLS/SSL-functionaliteit.
* De DNS-configuratie voor het virtuele netwerk moet geschikt zijn voor het oplossen van alle eind punten en domeinen die worden vermeld in de eerdere punten. Aan deze DNS-vereisten kan worden voldaan door ervoor te zorgen dat een geldige DNS-infra structuur is geconfigureerd en onderhouden voor het virtuele netwerk.
* Uitgaande netwerk verbinding met de volgende Azure Monitor-eind punten die worden omgezet onder de volgende DNS-domeinen: *shoebox2-Black.shoebox2.Metrics.nsatc.net*, *North-prod2.prod2.Metrics.nsatc.net*, *azglobal-Black.azglobal.Metrics.nsatc.net*, *shoebox2-Red.shoebox2.Metrics.nsatc.net*, *East-prod2.prod2.Metrics.nsatc.net*, *azglobal-Red.azglobal.Metrics.nsatc.net*, *shoebox3.Prod.microsoftmetrics.com*, *shoebox3-Red.Prod.microsoftmetrics.com* en *shoebox3-Black.Prod.microsoftmetrics.com*.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-virtual-network"></a>Hoe kan ik controleren of mijn cache werkt in een virtueel netwerk?

>[!IMPORTANT]
>Wanneer u verbinding maakt met een Azure-cache voor een redis-exemplaar dat wordt gehost in een virtueel netwerk, moeten uw cache-clients zich in hetzelfde virtuele netwerk of in een virtueel netwerk met peering op virtuele netwerken bevinden dat in dezelfde Azure-regio is ingeschakeld. De globale peering van het virtuele netwerk wordt momenteel niet ondersteund. Deze vereiste is van toepassing op Test toepassingen of diagnostische hulpprogram ma's voor diagnose. Ongeacht waar de client toepassing wordt gehost, moeten Nsg's of andere netwerk lagen zodanig worden geconfigureerd dat het netwerk verkeer van de client de Azure-cache voor het redis-exemplaar mag bereiken.
>

Nadat de poort vereisten zijn geconfigureerd zoals beschreven in de vorige sectie, kunt u controleren of uw cache werkt door de volgende stappen uit te voeren:

- [Start](cache-administration.md#reboot) alle cache knooppunten opnieuw op. Als niet alle vereiste cache afhankelijkheden kunnen worden bereikt, zoals beschreven in de [Binnenkomende poort vereisten](cache-how-to-premium-vnet.md#inbound-port-requirements) en de vereisten voor de [uitgaande poort](cache-how-to-premium-vnet.md#outbound-port-requirements), kan de cache niet opnieuw worden opgestart.
- Nadat de cache knooppunten opnieuw zijn opgestart, zoals gerapporteerd door de cache status in de Azure Portal, kunt u de volgende tests uitvoeren:
  - Ping het cache-eind punt met behulp van poort 6380 van een computer die zich in hetzelfde virtuele netwerk bevinden als de cache met behulp van [tcping](https://www.elifulkerson.com/projects/tcping.php). Bijvoorbeeld:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Als het `tcping` hulp programma rapporteert dat de poort is geopend, is de cache beschikbaar voor verbinding van clients in het virtuele netwerk.

  - Een andere manier om te testen is het maken van een test-cache-client (een eenvoudige console toepassing met stack Exchange. redis) die verbinding maakt met de cache en waarmee items uit de cache worden toegevoegd en opgehaald. Installeer de voor beeld-client toepassing op een virtuele machine die zich in hetzelfde virtuele netwerk bevinden als de cache. Voer het vervolgens uit om de verbinding met de cache te controleren.


### <a name="when-i-try-to-connect-to-my-azure-cache-for-redis-instance-in-a-virtual-network-why-do-i-get-an-error-stating-the-remote-certificate-is-invalid"></a>Waarom wordt er een fout melding weer gegeven dat het externe certificaat ongeldig is wanneer ik verbinding probeer te maken met mijn Azure-cache voor redis-exemplaar in een virtueel netwerk?

Wanneer u probeert verbinding te maken met een Azure-cache voor een redis-exemplaar in een virtueel netwerk, ziet u een certificaat validatie fout, zoals deze:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

De oorzaak kan zijn dat u via het IP-adres verbinding maakt met de host. U wordt aangeraden de hostnaam te gebruiken. Met andere woorden, gebruikt u de volgende teken reeks:

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Vermijd het gebruik van het IP-adres dat lijkt op het volgende connection string:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Als u de DNS-naam niet kunt omzetten, bevatten sommige client bibliotheken configuratie opties zoals `sslHost` , die worden geleverd door de stack Exchange. redis-client. Met deze optie kunt u de hostnaam die wordt gebruikt voor certificaat validatie negeren. Bijvoorbeeld:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-virtual-networks-with-a-standard-or-basic-cache"></a>Kan ik virtuele netwerken met een Standard-of Basic-cache gebruiken?

Virtuele netwerken kunnen alleen worden gebruikt met Premium-laag caches.

### <a name="why-does-creating-an-azure-cache-for-redis-instance-fail-in-some-subnets-but-not-others"></a>Waarom mislukt het maken van een Azure-cache voor een redis-exemplaar in sommige subnetten, maar niet op andere?

Als u een Azure-cache voor een redis-exemplaar in een virtueel netwerk implementeert, moet de cache zich in een toegewijd subnet bevinden dat geen ander bron type bevat. Als er een poging wordt gedaan om een Azure-cache te implementeren voor een redis-exemplaar naar een virtueel netwerk van Resource Manager dat andere bronnen bevat, zoals Azure-toepassing gateway instanties en uitgaande NAT, mislukt de implementatie meestal. U moet bestaande resources van andere typen verwijderen voordat u een nieuw Azure-cache geheugen kunt maken voor redis-instantie.

Er moeten ook voldoende IP-adressen beschikbaar zijn in het subnet.

### <a name="what-are-the-subnet-address-space-requirements"></a>Wat zijn de adres ruimte vereisten voor het subnet?

Azure reserveert een aantal IP-adressen binnen elk subnet en deze adressen kunnen niet worden gebruikt. Het eerste en laatste IP-adres van de subnetten zijn gereserveerd voor protocol conformiteit, samen met drie meer adressen die worden gebruikt voor Azure-Services. Zie [zijn er beperkingen voor het gebruik van IP-adressen in deze subnetten?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) voor meer informatie.

Naast de IP-adressen die worden gebruikt door de infra structuur van het virtuele Azure-netwerk, gebruikt elk Azure cache voor redis-exemplaar in het subnet twee IP-adressen per cluster Shard, plus extra IP-adressen voor aanvullende replica's, indien van toepassing. Er wordt één aanvullend IP-adres gebruikt voor de load balancer. Een niet-geclusterde cache wordt beschouwd als een Shard.

### <a name="do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network"></a>Werken alle cache functies wanneer een cache wordt gehost in een virtueel netwerk?

Wanneer uw cache deel uitmaakt van een virtueel netwerk, hebben alleen clients in het virtuele netwerk toegang tot de cache. Als gevolg hiervan werken de volgende cache beheer functies op dit moment niet:

* **Redis-console**: omdat de redis-console wordt uitgevoerd in uw lokale browser, die meestal op een ontwikkelaars computer staat die niet is verbonden met het virtuele netwerk, kan deze geen verbinding maken met uw cache.

## <a name="use-expressroute-with-azure-cache-for-redis"></a>ExpressRoute gebruiken met Azure cache voor redis

Klanten kunnen een [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) -circuit verbinden met hun virtuele netwerk infrastructuur. Op deze manier kunnen ze hun on-premises netwerk uitbreiden naar Azure.

Een nieuw gemaakt ExpressRoute-circuit voert standaard geen geforceerde tunneling uit (advertisement of a default route, 0.0.0.0/0) in een virtueel netwerk. Als gevolg hiervan is uitgaande internet connectiviteit rechtstreeks toegestaan vanuit het virtuele netwerk. Client toepassingen kunnen verbinding maken met andere Azure-eind punten, die een Azure-cache voor redis-exemplaar bevatten.

Een algemene klant configuratie is het gebruik van geforceerde tunneling (adverteren van een standaard route), waardoor uitgaand Internet verkeer wordt afgedwongen in plaats van on-premises stroom. Deze verkeers stroom verbreekt de connectiviteit met Azure cache voor redis als het uitgaande verkeer vervolgens on-premises wordt geblokkeerd, zodat de Azure-cache voor het redis-exemplaar niet kan communiceren met de afhankelijkheden.

De oplossing bestaat uit het definiëren van een of meer door de gebruiker gedefinieerde routes (Udr's) op het subnet met de Azure-cache voor het redis-exemplaar. Een UDR definieert subnet-specifieke routes die worden uitgevoerd in plaats van de standaard route.

Gebruik, indien mogelijk, de volgende configuratie:

* De ExpressRoute-configuratie adverteert 0.0.0.0/0 en dwingt standaard al het uitgaande verkeer on-premises af in een tunnel.
* De UDR die wordt toegepast op het subnet met de Azure-cache voor het redis-exemplaar, definieert 0.0.0.0/0 met een werkende route voor TCP/IP-verkeer naar het open bare Internet. Zo wordt het type van de volgende hop ingesteld op *Internet*.

Het gecombineerde effect van deze stappen is dat de UDR van het subnet prioriteit heeft voor de ExpressRoute geforceerde tunneling, waardoor uitgaande internet toegang wordt gegarandeerd vanuit het Azure-cache geheugen voor redis-exemplaar.

Het maken van een verbinding met een Azure-cache voor een redis-exemplaar van een lokale toepassing met behulp van ExpressRoute is geen typisch gebruiks scenario om de oorzaak van de prestaties. Voor de beste prestaties moeten Azure cache voor redis-clients zich in dezelfde regio bevinden als de Azure-cache voor het redis-exemplaar.

>[!IMPORTANT]
>De routes die in een UDR zijn gedefinieerd, *moeten* specifiek genoeg zijn om voor rang te hebben op alle routes die worden geadverteerd door de ExpressRoute-configuratie. In het volgende voor beeld wordt het brede adres bereik 0.0.0.0/0 gebruikt, en kan dit als zodanig per ongeluk worden overschreven door route advertenties die meer specifieke adresbereiken gebruiken.

>[!WARNING]
>Azure cache voor redis wordt niet ondersteund met ExpressRoute-configuraties die *onjuiste cross-Advertising-routes van het open bare pad naar het privé*-peering-pad. ExpressRoute-configuraties waarvoor open bare peering is geconfigureerd, ontvangen route-advertisements van micro soft voor een groot aantal Microsoft Azure IP-adresbereiken. Als deze adresbereiken onjuist zijn geadverteerd op het pad van de privé-peering, is het resultaat dat alle uitgaande netwerk pakketten van de Azure-cache voor het subnet van het redis-exemplaar onjuist geforceerd worden getunneld naar de on-premises netwerk infrastructuur van een klant. Deze netwerk stroom verbreekt Azure-cache voor redis. De oplossing voor dit probleem is het stoppen van cross-Advertising routes van het open bare pad naar het privé-peering-pad.

Achtergrond informatie over Udr's is beschikbaar in [route ring van virtueel netwerk verkeer](../virtual-network/virtual-networks-udr-overview.md).

Zie [technisch overzicht van ExpressRoute](../expressroute/expressroute-introduction.md)voor meer informatie over ExpressRoute.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure cache voor redis-functies.

* [Azure-cache voor redis Premium-Service lagen](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
