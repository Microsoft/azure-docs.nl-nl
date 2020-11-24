---
title: Een Virtual Network-Premium Azure-cache configureren voor redis
description: Meer informatie over het maken en beheren van Virtual Network ondersteuning voor uw Azure-cache voor de Premium-laag voor redis-instanties
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: 5fd82105c94bb9be2d07c8843834465821acd8bc
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95803781"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Virtual Network ondersteuning configureren voor een Premium Azure-cache voor redis
Azure cache voor redis heeft verschillende cache aanbiedingen, die flexibiliteit bieden bij het kiezen van de cache grootte en-functies, inclusief functies van de Premium-laag, zoals clustering, persistentie en ondersteuning voor virtuele netwerken. Een VNet is een privé netwerk in de Cloud. Wanneer een Azure-cache voor redis-exemplaar is geconfigureerd met een VNet, is het niet openbaar adresseerbaar en is deze alleen toegankelijk vanaf virtuele machines en toepassingen binnen het VNet. In dit artikel wordt beschreven hoe u ondersteuning voor virtuele netwerken kunt configureren voor een Premium Azure-cache voor een redis-exemplaar.

> [!NOTE]
> Azure cache voor redis ondersteunt zowel de klassieke als de VNets van Resource Manager.
> 
> 

## <a name="why-vnet"></a>Waarom VNet?
De implementatie van [azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/) biedt verbeterde beveiliging en isolatie voor uw Azure-cache voor redis, evenals subnetten, Toegangs beheer beleid en andere functies om de toegang verder te beperken.

## <a name="virtual-network-support"></a>Ondersteuning voor virtuele netwerken
Virtual Network-ondersteuning (VNet) is geconfigureerd op de Blade **nieuwe Azure-cache voor redis** tijdens het maken van de cache. 

1. Als u een Premium-cache wilt maken, meldt u zich aan bij de [Azure Portal](https://portal.azure.com) en selecteert u **een resource maken**. Naast het maken van caches in de Azure Portal, kunt u ze ook maken met behulp van Resource Manager-sjablonen, Power shell of Azure CLI. Zie [Een cache maken](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache) voor meer informatie over het maken van een Azure Cache voor Redis.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Resource maken.":::
   
2. Selecteer op de pagina **Nieuw** de optie **Databases** en selecteer vervolgens **Azure Cache voor Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selecteer Azure Cache voor Redis.":::

3. Configureer op de pagina **nieuw redis cache** de instellingen voor uw nieuwe Premium-cache.
   
   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-naam** | Geef een wereldwijd unieke naam op. | De cachenaam is een tekenreeks van 1 tot 63 tekens die alleen cijfers, letters en afbreekstreepjes mag bevatten. De naam moet beginnen en eindigen met een cijfer of letter en mag geen opeenvolgende afbreekstreepjes bevatten. De *hostnaam* van uw cache-exemplaar wordt *\<DNS name>.redis.cache.windows.net*. | 
   | **Abonnement** | Vervolg keuzelijst en selecteer uw abonnement. | Het abonnement waarmee dit nieuwe Azure Cache voor Redis-exemplaar wordt gemaakt. | 
   | **Resourcegroep** | Vervolg keuzelijst en selecteer een resource groep of selecteer **nieuwe maken** en voer een nieuwe naam voor de resource groep in. | Naam voor de resourcegroep waarin de cache en andere resources moeten worden gemaakt. Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
   | **Locatie** | Vervolg keuzelijst en selecteer een locatie. | Selecteer een [regio](https://azure.microsoft.com/regions/) in de buurt van andere services die gaan gebruikmaken van de cache. |
   | **Cachetype** | En selecteer een Premium-cache om Premium-functies te configureren. Zie [Azure cache for redis prijzen](https://azure.microsoft.com/pricing/details/cache/)voor meer informatie. |  De prijscategorie bepaalt de grootte, prestaties en functies die beschikbaar zijn voor de cache. Zie het [Azure Cache voor Redis-overzicht](cache-overview.md) voor meer informatie. |

4. Selecteer het tabblad **Netwerken** of klik op de knop **Netwerken** onderaan de pagina.

5. Op het tabblad **netwerken** selecteert u **virtuele netwerken** als verbindings methode. Als u een nieuw virtueel netwerk wilt gebruiken, maakt u het eerst door de stappen te volgen in [een virtueel netwerk maken met](../virtual-network/manage-virtual-network.md#create-a-virtual-network) behulp van de Azure portal of [een virtueel netwerk (klassiek) maken met behulp van de Azure Portal](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal) en vervolgens terugkeren naar de **nieuwe Azure-cache voor redis** -Blade om uw Premium-cache te maken en te configureren.

   > [!IMPORTANT]
   > Wanneer u een Azure-cache voor redis implementeert voor een resource manager VNet, moet de cache zich in een toegewijd subnet bevinden dat geen andere resources bevat behalve Azure cache voor redis-exemplaren. Als er een poging wordt gedaan om een Azure-cache te implementeren voor redis naar een resource manager-VNet naar een subnet dat andere bronnen bevat, mislukt de implementatie.
   > 
   > 

   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Virtual Network** | Vervolg keuzelijst en selecteer uw virtuele netwerk. | Selecteer een virtueel netwerk dat zich in hetzelfde abonnement en dezelfde locatie als uw cache bevindt. | 
   | **Subnet** | Vervolg keuzelijst en selecteer uw subnet. | Het adres bereik van het subnet moet de CIDR-notatie hebben (bijvoorbeeld 192.168.1.0/24). Het moet deel uitmaken van de adres ruimte van het virtuele netwerk. | 
   | **Statisch IP-adres** | Beschrijving Voer een statisch IP-adres in. | Als u geen statisch IP-adres opgeeft, wordt automatisch een IP-adressen gekozen. | 

   > [!IMPORTANT]
   > Azure reserveert een aantal IP-adressen binnen elk subnet en deze adressen kunnen niet worden gebruikt. Het eerste en laatste IP-adres van de subnetten zijn gereserveerd voor protocol conformiteit, samen met drie meer adressen die worden gebruikt voor Azure-Services. Zie [zijn er beperkingen voor het gebruik van IP-adressen in deze subnetten?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) voor meer informatie.
   > 
   > Naast de IP-adressen die worden gebruikt door de Azure VNET-infra structuur, maakt elk redis-exemplaar in het subnet gebruik van twee IP-adressen per Shard en één extra IP-adres voor de load balancer. Een niet-geclusterde cache wordt beschouwd als een Shard.
   > 
   > 

6. Selecteer het tabblad **Volgende: Geavanceerd** of klik op de knop **Volgende: Geavanceerd** onderaan de pagina.

7. Configureer op het tabblad **Geavanceerd** voor een Premium-cache-exemplaar de instellingen voor niet-TLS-poort, clustering en gegevens persistentie. 

8. Selecteer het tabblad **Volgende: Tags** of klik op de knop **Volgende: Tags** onderaan de pagina.

9. Voer desgewenst in het tabblad **Tags** de naam en waarde in om de resource te categoriseren. 

10. Selecteer **Controleren + maken**. Het tabblad Beoordelen + maken wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure.

11. Selecteer **Maken** nadat het groene bericht Validatie geslaagd verschijnt.

Het duurt even voor de cache is gemaakt. U kunt de voortgang bekijken op de **overzichtspagina** van Azure Cache voor Redis. Als u bij **Status** **Wordt uitgevoerd** ziet staan, kunt u de cache gebruiken. Nadat de cache is gemaakt, kunt u de configuratie voor het VNet bekijken door te klikken op **Virtual Network** in het **menu resource**.

![Virtueel netwerk][redis-cache-vnet-info]

Als u verbinding wilt maken met uw Azure-cache voor redis-instantie wanneer u een VNet gebruikt, geeft u de hostnaam van uw cache op in de connection string, zoals in het volgende voor beeld wordt weer gegeven:

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

## <a name="azure-cache-for-redis-vnet-faq"></a>Veelgestelde vragen over Azure cache voor redis VNet
De volgende lijst bevat antwoorden op veelgestelde vragen over de Azure-cache voor redis-schaling.

* Wat zijn enkele veelvoorkomende problemen met de configuratie met Azure cache voor redis en VNets?
* [Hoe kan ik controleren of mijn cache werkt in een VNET?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* Waarom wordt er een fout melding weer gegeven dat het externe certificaat ongeldig is, wanneer probeert verbinding te maken met mijn Azure-cache voor redis in een VNET?
* [Kan ik VNets gebruiken met een Standard-of Basic-cache?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Waarom wordt het maken van een Azure-cache voor redis mislukt in sommige subnetten, maar niet op andere computers?
* [Wat zijn de adres ruimte vereisten voor het subnet?](#what-are-the-subnet-address-space-requirements)
* [Werken alle cache functies wanneer ze een cache in een VNET hosten?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Wat zijn enkele veelvoorkomende problemen met de configuratie met Azure cache voor redis en VNets?
Wanneer Azure cache voor redis wordt gehost in een VNet, worden de poorten in de volgende tabellen gebruikt. 

>[!IMPORTANT]
>Als de poorten in de volgende tabellen zijn geblokkeerd, werkt de cache mogelijk niet correct. Een of meer van deze poorten zijn geblokkeerd is het meest voorkomende fout probleem bij het gebruik van Azure cache voor redis in een VNet.
> 
> 

- [Vereisten voor de uitgaande poort](#outbound-port-requirements)
- [Vereisten voor inkomende poort](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Vereisten voor de uitgaande poort

Er zijn negen uitgaande poort vereisten. Uitgaande aanvragen in deze bereiken zijn ofwel uitgaand voor andere services die nodig zijn om de cache te laten functioneren of intern voor het redis-subnet voor communicatie tussen knoop punten. Voor geo-replicatie bestaan er extra uitgaande vereisten voor communicatie tussen subnetten van de primaire en replica-cache.

| Poort(en) | Richting | Transportprotocol | Doel | Lokaal IP-adres | Extern IP-adres |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Uitgaand |TCP |Afhankelijkheden van redis op Azure Storage/PKI (Internet) | (Redis-subnet) |* <sup>3</sup> |
| 443 | Uitgaand | TCP | Afhankelijkheid van redis op Azure Key Vault en Azure Monitor | (Redis-subnet) | AzureKeyVault, AzureMonitor <sup>1</sup> |
| 53 |Uitgaand |TCP/UDP |Redis-afhankelijkheden op DNS (Internet/VNet) | (Redis-subnet) | 168.63.129.16 en 169.254.169.254 <sup>2</sup> en een aangepaste DNS-server voor het subnet <sup>3</sup> |
| 8443 |Uitgaand |TCP |Interne communicatie voor redis | (Redis-subnet) | (Redis-subnet) |
| 10221-10231 |Uitgaand |TCP |Interne communicatie voor redis | (Redis-subnet) | (Redis-subnet) |
| 20226 |Uitgaand |TCP |Interne communicatie voor redis | (Redis-subnet) |(Redis-subnet) |
| 13000-13999 |Uitgaand |TCP |Interne communicatie voor redis | (Redis-subnet) |(Redis-subnet) |
| 15000-15999 |Uitgaand |TCP |Interne communicatie voor redis en Geo-Replication | (Redis-subnet) |(Redis-subnet) (Geo-replica peer-subnet) |
| 6379-6380 |Uitgaand |TCP |Interne communicatie voor redis | (Redis-subnet) |(Redis-subnet) |

<sup>1</sup> u kunt de service tags ' AzureKeyVault ' en ' AzureMonitor ' gebruiken bij Resource Manager-netwerk beveiligings groepen.

<sup>2</sup> deze IP-adressen die eigendom zijn van micro soft, worden gebruikt om de host-VM te adresseren die Azure DNS.

<sup>3</sup> niet nodig voor subnetten zonder aangepaste DNS-server, of nieuwere redis-caches die aangepaste DNS negeren.

<sup>4</sup> Zie [aanvullende VNET-netwerk connectiviteits vereisten](#additional-vnet-network-connectivity-requirements)voor meer informatie.

#### <a name="geo-replication-peer-port-requirements"></a>Vereisten voor geo-replicatie peer-poort

Als u gebruikmaakt van georeplicatie tussen caches in virtuele netwerken van Azure, moet u er rekening mee houden dat de aanbevolen configuratie het blok keren van poorten 15000-15999 voor het hele subnet in zowel binnenkomende als uitgaande richting naar beide caches, zodat alle replica onderdelen in het subnet direct met elkaar kunnen communiceren, zelfs in het geval van een toekomstige geo-failover.

#### <a name="inbound-port-requirements"></a>Vereisten voor inkomende poort

Er zijn acht vereisten voor het poort bereik voor inkomend verkeer. Inkomende aanvragen in deze bereiken zijn ofwel inkomend van andere services die worden gehost in hetzelfde VNET of intern voor de communicatie van het redis-subnet.

| Poort(en) | Richting | Transportprotocol | Doel | Lokaal IP-adres | Extern IP-adres |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Inkomend |TCP |Client communicatie naar redis, Azure-taak verdeling | (Redis-subnet) | (Redis subnet), (client subnet), AzureLoadBalancer <sup>1</sup> |
| 8443 |Inkomend |TCP |Interne communicatie voor redis | (Redis-subnet) |(Redis-subnet) |
| 8500 |Inkomend |TCP/UDP |Azure-taakverdeling | (Redis-subnet) | AzureLoadBalancer |
| 10221-10231 |Inkomend |TCP |Client communicatie met redis-clusters, interne communicatie voor redis | (Redis-subnet) |(Redis subnet), AzureLoadBalancer, (client-subnet) |
| 13000-13999 |Inkomend |TCP |Client communicatie met redis-clusters, Azure-taak verdeling | (Redis-subnet) | (Redis subnet), (client subnet), AzureLoadBalancer |
| 15000-15999 |Inkomend |TCP |Client communicatie met redis-clusters, Azure-taak verdeling en Geo-Replication | (Redis-subnet) | (Redis subnet), (client subnet), AzureLoadBalancer, (geo-replica peer subnet) |
| 16001 |Inkomend |TCP/UDP |Azure-taakverdeling | (Redis-subnet) | AzureLoadBalancer |
| 20226 |Inkomend |TCP |Interne communicatie voor redis | (Redis-subnet) |(Redis-subnet) |

<sup>1</sup> u kunt de service label ' AzureLoadBalancer ' (Resource Manager) (of ' AZURE_LOADBALANCER ' voor klassiek) gebruiken om de NSG-regels te ontwerpen.

#### <a name="additional-vnet-network-connectivity-requirements"></a>Aanvullende vereisten voor VNET-netwerk connectiviteit

Er zijn vereisten voor de netwerk verbinding voor Azure cache voor redis die in eerste instantie niet in een virtueel netwerk kunnen worden bereikt. Azure cache voor redis vereist dat alle volgende items goed werken wanneer ze binnen een virtueel netwerk worden gebruikt.

* Uitgaande netwerk verbinding met Azure Storage eind punten wereld wijd. Dit geldt ook voor eind punten die zich in dezelfde regio bevinden als de Azure-cache voor redis-exemplaar, evenals opslag eindpunten die zich in **andere** Azure-regio's bevinden. Azure Storage-eind punten worden omgezet onder de volgende DNS-domeinen: *Table.core.Windows.net*, *blob.core.Windows.net*, *Queue.core.Windows.net* en *File.core.Windows.net*. 
* Uitgaande netwerk verbinding met *OCSP.Digicert.com*, *crl4.Digicert.com*, *OCSP.msocsp.com*, *mscrl.Microsoft.com*, *crl3.Digicert.com*, *cacerts.Digicert.com*, *oneocsp.Microsoft.com* en *CRL.Microsoft.com*. Deze connectiviteit is vereist voor de ondersteuning van TLS/SSL-functionaliteit.
* De DNS-configuratie voor het virtuele netwerk moet geschikt zijn voor het oplossen van alle eind punten en domeinen die worden vermeld in de eerdere punten. Aan deze DNS-vereisten kan worden voldaan door ervoor te zorgen dat een geldige DNS-infra structuur is geconfigureerd en onderhouden voor het virtuele netwerk.
* Uitgaande netwerk verbinding met de volgende Azure Monitor-eind punten die worden omgezet onder de volgende DNS-domeinen: *shoebox2-Black.shoebox2.Metrics.nsatc.net*, *North-prod2.prod2.Metrics.nsatc.net*, *azglobal-Black.azglobal.Metrics.nsatc.net*, *shoebox2-Red.shoebox2.Metrics.nsatc.net*, *East-prod2.prod2.Metrics.nsatc.net*, *azglobal-Red.azglobal.Metrics.nsatc.net*.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Hoe kan ik controleren of mijn cache werkt in een VNET?

>[!IMPORTANT]
>Wanneer u verbinding maakt met een Azure-cache voor een redis-exemplaar dat wordt gehost in een VNET, moeten uw cache-clients zich in hetzelfde VNET of in een VNET bevinden dat VNET-peering is ingeschakeld in dezelfde Azure-regio. Globale VNET-peering worden momenteel niet ondersteund. Dit omvat alle test toepassingen of diagnostische hulpprogram ma's voor diagnose. Ongeacht waar de client toepassing wordt gehost, moeten netwerk beveiligings groepen of andere netwerk lagen zodanig worden geconfigureerd dat het netwerk verkeer van de client het redis-exemplaar kan bereiken.
>
>

Zodra de poort vereisten zijn geconfigureerd zoals beschreven in de vorige sectie, kunt u controleren of uw cache werkt door de volgende stappen uit te voeren.

- [Start](cache-administration.md#reboot) alle cache knooppunten opnieuw op. Als niet alle vereiste cache-afhankelijkheden kunnen worden bereikt (zoals beschreven in de vereisten voor de [inkomende poort](cache-how-to-premium-vnet.md#inbound-port-requirements) en de [uitgaande poort](cache-how-to-premium-vnet.md#outbound-port-requirements)), kan de cache niet opnieuw worden opgestart.
- Zodra de cache knooppunten opnieuw zijn opgestart (zoals gerapporteerd door de cache status in de Azure Portal), kunt u de volgende tests uitvoeren:
  - Ping het cache-eind punt (via poort 6380) van een computer die zich binnen hetzelfde VNET bevindt als de cache met behulp van [tcping](https://www.elifulkerson.com/projects/tcping.php). Bijvoorbeeld:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Als het `tcping` hulp programma rapporteert dat de poort is geopend, is de cache beschikbaar voor verbinding van clients in het VNET.

  - Een andere manier om te testen is het maken van een test-cache-client (een eenvoudige console toepassing met stack Exchange. redis) die verbinding maakt met de cache en waarmee items uit de cache worden toegevoegd en opgehaald. Installeer de voor beeld-client toepassing op een virtuele machine die zich in hetzelfde VNET bevindt als de cache en voer deze uit om de connectiviteit met de cache te controleren.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Waarom wordt er een fout melding weer gegeven dat het externe certificaat ongeldig is, wanneer probeert verbinding te maken met mijn Azure-cache voor redis in een VNET?

Wanneer u probeert verbinding te maken met een Azure-cache voor redis in een VNET, ziet u een certificaat validatie fout, zoals:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

De oorzaak kan zijn dat u via het IP-adres verbinding maakt met de host. Het is raadzaam om de hostnaam te gebruiken. Met andere woorden, gebruikt u het volgende:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Vermijd het gebruik van het IP-adres dat lijkt op het volgende connection string:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Als u de DNS-naam niet kunt omzetten, bevatten sommige client bibliotheken configuratie opties zoals `sslHost` die worden geboden door de stack Exchange. redis-client. Hierdoor kunt u de hostnaam die wordt gebruikt voor certificaat validatie negeren. Bijvoorbeeld:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Kan ik VNets gebruiken met een Standard-of Basic-cache?
VNets kan alleen worden gebruikt met Premium-caches.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Waarom wordt het maken van een Azure-cache voor redis mislukt in sommige subnetten, maar niet op andere computers?
Als u een Azure-cache voor redis implementeert in een VNet, moet de cache zich in een toegewijd subnet bevinden dat geen ander bron type bevat. Als er een poging wordt gedaan om een Azure-cache te implementeren voor redis naar een resource manager VNet-subnet dat andere bronnen bevat (zoals toepassings gateways, uitgaande NAT, enzovoort), mislukt de implementatie meestal. U moet bestaande resources van andere typen verwijderen voordat u een nieuwe Azure-cache kunt maken voor redis.

Er moeten ook voldoende IP-adressen beschikbaar zijn in het subnet.

### <a name="what-are-the-subnet-address-space-requirements"></a>Wat zijn de adres ruimte vereisten voor het subnet?
Azure reserveert een aantal IP-adressen binnen elk subnet en deze adressen kunnen niet worden gebruikt. Het eerste en laatste IP-adres van de subnetten zijn gereserveerd voor protocol conformiteit, samen met drie meer adressen die worden gebruikt voor Azure-Services. Zie [zijn er beperkingen voor het gebruik van IP-adressen in deze subnetten?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) voor meer informatie.

Naast de IP-adressen die worden gebruikt door de Azure VNET-infra structuur, maakt elk redis-exemplaar in het subnet gebruik van twee IP-adressen per cluster Shard (plus extra IP-adressen voor aanvullende replica's, indien van toepassing) en een extra IP-adres voor de load balancer. Een niet-geclusterde cache wordt beschouwd als een Shard.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Werken alle cache functies wanneer ze een cache in een VNET hosten?
Wanneer uw cache deel uitmaakt van een VNET, hebben alleen clients in het VNET toegang tot de cache. Als gevolg hiervan werken de volgende cache beheer functies op dit moment niet.

* Redis-console: omdat de redis-console wordt uitgevoerd in uw lokale browser, die meestal op een ontwikkelaars computer staat die niet met het VNET is verbonden, kan deze geen verbinding maken met uw cache.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>ExpressRoute gebruiken met Azure cache voor redis

Klanten kunnen een [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) -circuit verbinden met hun virtuele netwerk infrastructuur, waardoor ze hun on-premises netwerk uitbreiden naar Azure. 

Standaard voert een nieuw gemaakt ExpressRoute-circuit geen geforceerde tunneling uit (advertisement of a default route, 0.0.0.0/0) op een VNET. Als gevolg hiervan is uitgaande internet connectiviteit rechtstreeks toegestaan vanuit de VNET-en client toepassingen kunnen verbinding maken met andere Azure-eind punten, waaronder Azure-cache voor redis.

Een algemene klant configuratie is echter het gebruik van geforceerde tunneling (adverteren van een standaard route) waardoor uitgaand Internet verkeer wordt afgedwongen in plaats van on-premises stroom. Deze verkeers stroom verbreekt de connectiviteit met Azure cache voor redis als het uitgaande verkeer vervolgens on-premises wordt geblokkeerd, zodat de Azure-cache voor het redis-exemplaar niet kan communiceren met de afhankelijkheden.

De oplossing is het definiëren van een (of meer) door de gebruiker gedefinieerde routes (Udr's) op het subnet dat de Azure-cache voor redis bevat. Een UDR definieert subnet-specifieke routes die worden uitgevoerd in plaats van de standaard route.

Als dat mogelijk is, kunt u het beste de volgende configuratie gebruiken:

* De ExpressRoute-configuratie adverteert 0.0.0.0/0 en dwingt standaard geforceerd al het uitgaande verkeer on-premises.
* De UDR die is toegepast op het subnet met de Azure-cache voor redis definieert 0.0.0.0/0 met een werkende route voor TCP/IP-verkeer naar het open bare Internet. bijvoorbeeld door het type van de volgende hop in te stellen op ' Internet '.

Het gecombineerde effect van deze stappen is dat de UDR van het subnet niveau voor rang heeft boven de geforceerde ExpressRoute, waardoor uitgaande internet toegang via de Azure-cache voor redis wordt gegarandeerd.

Het maken van een verbinding met een Azure-cache voor redis-instantie van een on-premises toepassing met behulp van ExpressRoute is geen typisch gebruiks scenario als gevolg van de oorzaak van de prestaties (voor de beste prestaties van Azure cache voor redis-clients moeten zich in dezelfde regio bevinden als de Azure-cache voor redis).

>[!IMPORTANT] 
>De routes die in een UDR zijn gedefinieerd, **moeten** specifiek genoeg zijn om voor rang te hebben op alle routes die worden geadverteerd door de ExpressRoute-configuratie. In het volgende voor beeld wordt het brede adres bereik 0.0.0.0/0 gebruikt. Dit kan mogelijk per ongeluk worden overschreven door route advertenties met meer specifieke adresbereiken.

>[!WARNING]  
>Azure cache voor redis wordt niet ondersteund met ExpressRoute-configuraties die **onjuiste cross-Advertising-routes van het open bare pad naar het privé**-peering-pad. ExpressRoute-configuraties waarvoor open bare peering is geconfigureerd, ontvangen route-advertisements van micro soft voor een groot aantal Microsoft Azure IP-adresbereiken. Als deze adresbereiken onjuist zijn geadverteerd op het pad van de privé-peering, is het resultaat dat alle uitgaande netwerk pakketten van de Azure-cache voor het subnet van het redis-exemplaar onjuist geforceerd worden getunneld naar de on-premises netwerk infrastructuur van een klant. Deze netwerk stroom verbreekt Azure-cache voor redis. De oplossing voor dit probleem is het stoppen van cross-Advertising routes van het open bare pad naar het privé-peering-pad.


In dit [overzicht](../virtual-network/virtual-networks-udr-overview.md)vindt u achtergrond informatie over door de gebruiker gedefinieerde routes.

Zie [technisch overzicht van ExpressRoute](../expressroute/expressroute-introduction.md)voor meer informatie over ExpressRoute.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over Azure cache voor redis-functies.

* [Azure-cache voor redis Premium-Service lagen](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
