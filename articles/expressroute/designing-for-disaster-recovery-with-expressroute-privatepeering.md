---
title: 'Azure ExpressRoute: ontwerpen voor herstel na nood geval'
description: Deze pagina bevat architectuur aanbevelingen voor herstel na nood gevallen tijdens het gebruik van Azure ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: duau
ms.openlocfilehash: 2a5730cd75ccb76d25897e9109555113f7355c2f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92202410"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Ontwerpen voor herstel na nood gevallen met persoonlijke ExpressRoute-peering

ExpressRoute is ontworpen voor hoge Beschik baarheid om de communicatie van particuliere netwerken naar micro soft-resources te verzorgen. Met andere woorden: er is geen Single Point of Failure in het pad ExpressRoute in het micro soft-netwerk. Zie [ontwerpen voor hoge Beschik baarheid met ExpressRoute][HA]voor overwegingen bij het ontwerpen van de beschik baarheid van een ExpressRoute-circuit.

*Als er echter iets mis gaat* met Murphy--adage, kunt u in dit artikel zich richten op oplossingen die verder gaan dan storingen die kunnen worden opgelost met één ExpressRoute-circuit. Met andere woorden, in dit artikel, kunnen we in het kader van de netwerk architectuur controleren op het bouwen van robuuste back-end-netwerk connectiviteit voor herstel na nood gevallen met geo-redundante ExpressRoute-circuits.

>[!NOTE]
>De concepten die in dit artikel worden beschreven, zijn ook van toepassing wanneer een ExpressRoute-circuit wordt gemaakt onder een virtueel WAN of daarbuiten.
>

## <a name="need-for-redundant-connectivity-solution"></a>Behoefte aan redundante connectiviteits oplossing

Er zijn mogelijkheden en instanties waarbij een volledige regionale service (die van micro soft, netwerk serviceproviders, klanten of andere Cloud serviceproviders) wordt gedegradeerd. De hoofd oorzaak van dergelijke regionale service-impact is onder andere natuurlijke Calamity. Daarom is het belang rijk om voor bedrijfs continuïteit en essentiële toepassingen te plannen voor herstel na nood gevallen.   

Ongeacht of u uw essentiële bedrijfs toepassingen uitvoert in een Azure-regio of on-premises of op een wille keurige locatie, kunt u een andere Azure-regio gebruiken als uw failover-site. De volgende artikelen zijn bedoeld voor nood herstel van toepassingen en perspectieven voor toegang tot de frontend:

- [Herstel na noodgevallen voor bedrijven][Enterprise DR]
- [SMB-herstel na noodgeval met Azure Site Recovery][SMB DR]

Als u afhankelijk bent van de ExpressRoute-verbinding tussen uw on-premises netwerk en micro soft voor bedrijfs kritieke bewerkingen, moet uw plan voor herstel na nood gevallen ook geo-redundante netwerk connectiviteit bevatten. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Problemen met het gebruik van meerdere ExpressRoute-circuits

Wanneer u dezelfde set netwerken vergelijkt met meer dan één verbinding, maakt u parallelle paden tussen de netwerken. Parallelle paden, wanneer deze niet correct zijn ontworpen, kunnen leiden tot asymmetrische route ring. Als u stateful-entiteiten (bijvoorbeeld NAT, firewall) in het pad hebt, kan asymmetrische route ring de verkeers stroom blok keren.  Normaal gesp roken komt het over het ExpressRoute persoonlijke peering-pad niet over stateful-entiteiten zoals NAT of firewalls. Daarom blokkeert asymmetrische route ring via ExpressRoute persoonlijke peering niet noodzakelijkerwijs de verkeers stroom.
 
Als u echter taak verdeling van verkeer via Geo-redundante parallelle paden hebt, ongeacht of u stateful entiteiten hebt of niet, zou u inconsistente netwerk prestaties ondervinden. In dit artikel bespreken we hoe u deze uitdagingen kunt aanpakken.

## <a name="small-to-medium-on-premises-network-considerations"></a>Aandachtspunten voor kleine tot middel grote on-premises netwerken

Laten we eens kijken naar het voorbeeld netwerk geïllustreerd in het volgende diagram. In het voor beeld wordt geo-redundante ExpressRoute-connectiviteit tot stand gebracht tussen de on-premises locatie van Contoso en het VNet van Contoso in een Azure-regio. In het diagram geeft effen groene lijn aan dat het voorkeurs pad (via ExpressRoute 1) en de gestippelde waarde staat voor een pad (via ExpressRoute 2).

[![i]][1]

Wanneer u ExpressRoute-connectiviteit ontwerpt voor herstel na nood gevallen, moet u rekening houden met het volgende:

- Geo-redundante ExpressRoute-circuits gebruiken
- diverse service provider netwerk (en) gebruiken voor verschillende ExpressRoute-circuits
- elk ExpressRoute-circuit ontwerpen voor [hoge Beschik baarheid][HA]
- het verschillende ExpressRoute-circuit wordt beëindigd op een andere locatie in het netwerk van de klant

Als u bijvoorbeeld de routes op identieke wijze adverteert over alle ExpressRoute-paden, wordt door Azure het on-premises gebonden verkeer verdeeld over alle ExpressRoute-paden met een gelijke-kosten Multi-Path (ECMP)-route ring.

Met het geo-redundante ExpressRoute-circuit moeten we echter rekening houden met verschillende netwerk prestaties met verschillende netwerk paden (met name voor netwerk latentie). Voor meer consistente netwerk prestaties tijdens de normale werking, wilt u mogelijk de voor keur geven aan het ExpressRoute-circuit dat de minimale latentie biedt.

U kunt met behulp van een van de volgende technieken (Zie de volg orde van effectiviteit) van invloed zijn op Azure om de voor keur te geven aan een ExpressRoute-circuit van een andere.

- adverteren van een specifieke route via het voorkeurs ExpressRoute-Circuit vergeleken met andere ExpressRoute-circuit (s)
- een hoger verbindings gewicht configureren op de verbinding die het virtuele netwerk koppelt aan het voorkeurs ExpressRoute-circuit
- het adverteren van de routes via minder ExpressRoute-circuit met meer dan het pad (als pad laten voorafgaan door)

### <a name="more-specific-route"></a>Meer specifieke route

In het volgende diagram ziet u de selectie van het ExpressRoute met behulp van een meer specifieke route advertentie. In het Gedemonstreerde voor beeld wordt het IP-adres van Contoso op locatie/24 geadverteerd als twee/25 adresbereiken via het voorkeurs pad (ExpressRoute 1) en als/24 via het standaard traject (ExpressRoute 2).

[![twee]][2]

Omdat/25 specifiekere, vergeleken met/24, verzendt Azure het verkeer dat is bestemd voor 10.1.11.0/24 via ExpressRoute 1 met de status normaal. Als beide verbindingen van ExpressRoute 1 omlaag gaan, wordt de 10.1.11.0/24 route advertisement alleen weer geven via ExpressRoute 2. en daarom wordt het standby-circuit gebruikt in deze fout status.

### <a name="connection-weight"></a>Verbindings gewicht

De volgende scherm afbeelding illustreert het configureren van het gewicht van een ExpressRoute-verbinding via Azure Portal.

[![3]][3]

Het volgende diagram illustreert de selectie van het ExpressRoute met behulp van het verbindings gewicht. Het standaard verbindings gewicht is 0. In het onderstaande voor beeld is het gewicht van de verbinding voor ExpressRoute 1 geconfigureerd als 100. Wanneer een VNet een route voorvoegsel ontvangt dat is geadverteerd via meer dan één ExpressRoute-circuit, zal het VNet de voor keur geven aan de verbinding met het hoogste gewicht.

[![3]][4]

Als beide verbindingen van ExpressRoute 1 omlaag gaan, wordt de 10.1.11.0/24 route advertisement alleen weer geven via ExpressRoute 2. en daarom wordt het standby-circuit gebruikt in deze fout status.

### <a name="as-path-prepend"></a>ALS pad laten voorafgaan door

In het volgende diagram ziet u de selectie van ExpressRoute paden gebruiken als pad laten voorafgaan door. In het diagram geeft de route advertentie via ExpressRoute 1 het standaard gedrag van eBGP aan. Op de route advertisement via ExpressRoute 2 wordt het ASN van het on-premises netwerk op het pad van de route geplaatst. Wanneer dezelfde route wordt ontvangen via meerdere ExpressRoute-circuits, geeft VNet de voor keur aan de route met het kortste AS-pad. 

[![5,0]][5]

Als beide verbindingen van ExpressRoute 1 omlaag gaan, wordt de 10.1.11.0/24-route-advertisement alleen weer geven via ExpressRoute 2. Het pad dat langer is dan is niet alleen relevant. Daarom wordt het standby-circuit gebruikt in deze fout status.

Als u gebruikmaakt van een van de technieken, moet u er ook voor zorgen dat het on-premises netwerk van invloed is op een van de voor delen van een van uw ExpressRoute. De lokale voorkeurs waarde wordt doorgaans gebruikt om het on-premises netwerk te beïnvloeden om de voor keur aan een ExpressRoute-circuit over andere te geven. Lokale voor keur is een interne BGP-waarde (iBGP). De BGP-route met de hoogste lokale voorkeurs waarde verdient de voor keur.

> [!IMPORTANT]
> Wanneer u bepaalde ExpressRoute-circuits als stand-by gebruikt, moet u ze actief beheren en de failoverbewerking periodiek testen. 
> 

## <a name="large-distributed-enterprise-network"></a>Groot gedistribueerd bedrijfs netwerk

Wanneer u een groot gedistribueerd bedrijfs netwerk hebt, hebt u waarschijnlijk meerdere ExpressRoute-circuits. In dit gedeelte ziet u hoe u herstel na nood gevallen kunt ontwerpen met het actief-actief ExpressRoute-circuits, zonder extra standaard circuits. 

Laten we eens kijken naar het voor beeld in het volgende diagram. In het voor beeld heeft Contoso twee on-premises locaties die zijn verbonden met twee contoso IaaS-implementaties in twee verschillende Azure-regio's via ExpressRoute-circuits op twee verschillende peering locaties. 

[![6,5]][6]

De manier waarop het herstel na nood gevallen wordt beïnvloed, is van invloed op de manier waarop het verkeer van de regionale naar de andere locatie (region1/REGION2 naar location2/location1) wordt gerouteerd. Laten we eens kijken naar twee verschillende rampen architecturen die interregionale verkeer op verschillende locaties routeren.

### <a name="scenario-1"></a>Scenario 1

In het eerste scenario ontwerpt u herstel na nood gevallen, waardoor al het verkeer tussen een Azure-regio en een on-premises netwerk stroomt via het lokale ExpressRoute-circuit in de stationaire toestand. Als het lokale ExpressRoute-circuit mislukt, wordt het circuit voor externe ExpressRoute gebruikt voor alle verkeer stromen tussen Azure en het on-premises netwerk.

Scenario 1 wordt geïllustreerd in het volgende diagram. In het diagram geven groene lijnen paden voor de verkeers stroom tussen VNet1 en on-premises netwerken aan. De blauwe lijnen geven paden voor de verkeers stroom tussen VNet2 en on-premises netwerken aan. Effen lijnen geven het gewenste pad in de stationaire toestand aan en de onderbroken lijnen geven het verkeers traject aan bij het mislukken van het bijbehorende ExpressRoute-circuit dat een stationaire verkeers stroom uitvoert. 

[![7]][7]

U kunt het scenario ontwerpen met behulp van het verbindings gewicht om te beïnvloeden hoe VNets verbinding maakt met de lokale locatie van de peering ExpressRoute voor on-premises gebonden netwerk verkeer. Als u de oplossing wilt volt ooien, moet u ervoor zorgen dat symmetrisch omgekeerd verkeer stroom wordt uitgevoerd. U kunt de lokale voor keur gebruiken op de iBGP-sessie tussen uw BGP-routers (waarop ExpressRoute-circuits aan de on-premises kant worden beëindigd) om de voor keur te geven aan een ExpressRoute-circuit. De oplossing wordt geïllustreerd in het volgende diagram. 

[![8]][8]

### <a name="scenario-2"></a>Scenario 2

Het scenario 2 wordt geïllustreerd in het volgende diagram. In het diagram geven groene lijnen paden voor de verkeers stroom tussen VNet1 en on-premises netwerken aan. De blauwe lijnen geven paden voor de verkeers stroom tussen VNet2 en on-premises netwerken aan. In de stationaire toestand (ononderbroken lijnen in het diagram) stroomt al het verkeer tussen VNets en on-premises locaties via micro soft backbone voor het grootste deel, en doorloopt de onderlinge verbinding tussen on-premises locaties, alleen in de fout status (stippel lijnen in het diagram) van een ExpressRoute.

[![9,4]][9]

De oplossing wordt geïllustreerd in het volgende diagram. Zoals u ziet, kunt u het scenario architecten met behulp van een specifiekere route (optie 1) of als laten voorafgaan door (optie 2) om de selectie van het VNet-pad te beïnvloeden. Als u de selectie van een on-premises netwerk route wilt beïnvloeden voor afhankelijk Azure-verkeer, moet u de onderlinge verbinding tussen de on-premises locatie zo weinig mogelijk configureren. Howe u de koppeling voor verbinding zoals gewenst configureert, is afhankelijk van het routerings protocol dat in het on-premises netwerk wordt gebruikt. U kunt lokale voor keur gebruiken met iBGP of metric met IGP (OSPF of IS-IS).

[![6]][10]


## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u een herstel na nood gevallen van een persoonlijke peering-verbinding met een ExpressRoute-circuit kunt ontwerpen. De volgende artikelen zijn bedoeld voor nood herstel van toepassingen en perspectieven voor toegang tot de frontend:

- [Herstel na noodgevallen voor bedrijven][Enterprise DR]
- [SMB-herstel na noodgeval met Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "klein tot middel groot aantal on-premises netwerk overwegingen"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "invloed op het selecteren van paden met meer specifieke routes"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.pnghet "verbindings gewicht configureren via Azure Portal"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "invloed op het selecteren van paden met verbindings gewicht"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "invloed op het selecteren van paden met als pad laten voorafgaan door"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "grote aandachtspunten voor het distribueren van een on-premises netwerk"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "scenario 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "actief-actief ExpressRoute-circuit oplossing 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "scenario 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "actief-actief ExpressRoute-circuit oplossing 2"

<!--Link References-->
[HA]: ./designing-for-high-availability-with-expressroute.md
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: ./expressroute-optimize-routing.md#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: ./expressroute-optimize-routing.md#solution-use-as-path-prepending