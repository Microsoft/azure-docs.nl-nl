---
title: Connectiviteits instellingen van virtuele machines om te SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Connectiviteits instellingen van virtuele machines voor het gebruik van SAP HANA op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d9caf0a69d2f6caf23edbc35864e1aa560aaf63b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673013"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Azure-VM's verbinden met grote HANA-instanties

Het artikel [Wat is SAP Hana op Azure (grote exemplaren)?](./hana-overview-architecture.md) de minimale implementatie van HANA grote instanties met de SAP-toepassingslaag in azure ziet er als volgt uit:

![Azure VNet verbonden met SAP HANA op Azure (grote exemplaren) en on-premises](./media/hana-overview-architecture/image1-architecture.png)

Als u meer wilt weten over het virtuele Azure-netwerk, hebt u het volgende nodig:

- De definitie van een virtueel Azure-netwerk waarin u de Vm's van de SAP-toepassingslaag gaat implementeren.
- De definitie van een standaard subnet in het virtuele Azure-netwerk dat in feite de Vm's bevat waarin de virtuele machines worden geïmplementeerd.
- Het virtuele Azure-netwerk dat wordt gemaakt, moet ten minste één VM-subnet en één subnet van een virtuele Azure ExpressRoute-netwerk gateway hebben. Aan deze subnetten moeten de IP-adresbereiken worden toegewezen, zoals gespecificeerd en besproken in de volgende secties.


## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>Het virtuele Azure-netwerk voor HANA grote instanties maken

>[!Note]
>Het virtuele Azure-netwerk voor HANA grote instanties moet worden gemaakt met behulp van het Azure Resource Manager-implementatie model. Het oudere Azure-implementatie model, ook wel bekend als het klassieke implementatie model, wordt niet ondersteund door de HANA-oplossing voor grote exemplaren.

U kunt de Azure Portal, Power shell, een Azure-sjabloon of de Azure CLI gebruiken om het virtuele netwerk te maken. (Zie [een virtueel netwerk maken met behulp van de Azure Portal](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)) voor meer informatie. In het volgende voor beeld bekijken we een virtueel netwerk dat is gemaakt met behulp van de Azure Portal.

Wanneer wordt verwezen naar de **adres ruimte** in deze documentatie, naar de adres ruimte die het virtuele Azure-netwerk mag gebruiken. Deze adres ruimte is ook het adres bereik dat het virtuele netwerk gebruikt voor het door sturen van BGP-routes. Deze **adres ruimte** kan hier worden weer gegeven:

![Adres ruimte van een virtueel Azure-netwerk dat wordt weer gegeven in de Azure Portal](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

In het vorige voor beeld werd met 10.16.0.0/16 het virtuele netwerk van Azure een groot en breed IP-adres bereik gekregen dat moet worden gebruikt. Daarom kunnen alle IP-adresbereiken van de volgende subnetten binnen dit virtuele netwerk hun bereik binnen die adres ruimte hebben. Normaal gesp roken wordt een dergelijk groot adres bereik niet aanbevolen voor één virtueel netwerk in Azure. Maar laten we eens kijken naar de subnetten die zijn gedefinieerd in het virtuele Azure-netwerk:

![Subnetten van het virtuele Azure-netwerk en de IP-adresbereiken](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

We kijken naar een virtueel netwerk met een eerste VM-subnet (hier ' default ' genoemd) en een subnet met de naam ' GatewaySubnet '.

In de twee voor gaande afbeeldingen omvatten de **adres ruimte van het virtuele netwerk** het **IP-adres bereik van het subnet van de virtuele Azure-machine** en die van de gateway van een virtueel netwerk.

U kunt de **adres ruimte van het virtuele netwerk** beperken tot de specifieke bereiken die worden gebruikt door elk subnet. U kunt ook de **adres ruimte van het virtuele netwerk** van een virtueel netwerk definiëren als meerdere specifieke bereiken, zoals hier wordt weer gegeven:

![Azure Virtual Network-adres ruimte met twee spaties](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

In dit geval heeft de **adres ruimte van het virtuele netwerk** twee spaties gedefinieerd. Ze zijn hetzelfde als de IP-adresbereiken die zijn gedefinieerd voor het IP-adres bereik van het subnet van de Azure-VM en de gateway van het virtuele netwerk. 

U kunt elke gewenste naamgevings norm gebruiken voor deze Tenant subnetten (VM-subnetten). **Er moeten echter altijd één, en slechts één, gateway-subnet zijn voor elk virtueel netwerk** dat verbinding maakt met de SAP Hana op Azure (grote instanties) ExpressRoute-circuit. **Dit gateway-subnet moet de naam ' GatewaySubnet ' hebben** om ervoor te zorgen dat de ExpressRoute-gateway op de juiste wijze is geplaatst.

> [!WARNING] 
> Het is essentieel dat het gateway-subnet altijd de naam ' GatewaySubnet ' heeft.

U kunt meerdere VM-subnetten en niet-aaneengesloten adresbereiken gebruiken. Deze adresbereiken moeten worden gedekt door de **adres ruimte** van het virtuele netwerk van het virtuele netwerk. Ze kunnen zich in een geaggregeerd formulier bevinden. Ze kunnen zich ook in een lijst met de exacte bereiken van de VM-subnetten en het gateway-subnet bevinden.

Hieronder volgt een samen vatting van de belang rijke feiten over een virtueel Azure-netwerk dat is verbonden met HANA grote instanties:

- U moet de **adres ruimte van het virtuele netwerk** naar micro soft verzenden wanneer u een eerste implementatie van Hana grote instanties uitvoert. 
- De **adres ruimte van het virtuele netwerk** kan een groter bereik zijn dat de bereiken voor het IP-adres bereik van het subnet van de Azure-VM en de gateway van het virtuele netwerk dekt.
- U kunt ook meerdere bereiken verzenden die de verschillende IP-adresbereiken van IP-adresbereiken van het VM-subnet en het IP-adres bereik van de virtuele netwerk gateway omvatten.
- De gedefinieerde **adres ruimte van het virtuele netwerk** wordt gebruikt voor het door geven van BGP-route ring.
- De naam van het gateway-subnet moet zijn: **"GatewaySubnet"**.
- De adres ruimte wordt gebruikt als een filter op de HANA grote instantie zijde om verkeer toe te staan of niet toe te staan voor de HANA grote instantie-eenheden van Azure. De BGP-routerings gegevens van het virtuele Azure-netwerk en de IP-adresbereiken die zijn geconfigureerd voor filteren op de HANA grote instantie zijde moeten overeenkomen. Anders kunnen connectiviteits problemen optreden.
- Er zijn een aantal details over het gateway-subnet dat later wordt beschreven, in de sectie **verbinding maken met een virtueel netwerk met Hana grote instantie ExpressRoute.**



## <a name="different-ip-address-ranges-to-be-defined"></a>Verschillende IP-adresbereiken die moeten worden gedefinieerd 

Sommige IP-adresbereiken die nodig zijn voor de implementatie van HANA grote instanties, worden al geïntroduceerd. Maar er zijn meer IP-adresbereiken die ook belang rijk zijn. Niet alle volgende IP-adresbereiken moeten naar micro soft worden verzonden. U moet deze echter definiëren voordat u een aanvraag voor de eerste implementatie verzendt:

- **Adres ruimte van virtueel netwerk**: de **adres ruimte van het virtuele netwerk** is de IP-adresbereiken die u toewijst aan de para meter voor de adres ruimte in de virtuele Azure-netwerken. Deze netwerken maken verbinding met de SAP HANA-omgeving met grote instanties. We raden u aan deze adres ruimte parameter een waarde met meerdere regels te maken. Deze moet bestaan uit het subnetmasker van de Azure-VM en de subnetten van de Azure-gateway. Dit subnet-bereik wordt weer gegeven in de vorige grafische afbeelding. Het mag niet overlappen met uw on-premises of server-IP-adres groep of geen-P2P-adresbereiken. Hoe krijgt u deze IP-adres bereik (en)? Het team van uw bedrijfs netwerk of service provider moet een of meer IP-adres bereik (en) opgeven die niet binnen uw netwerk worden gebruikt. Het subnet van uw Azure-VM is bijvoorbeeld 10.0.1.0/24, en het subnet van het subnet van de Azure-gateway is 10.0.2.0/28.  We raden u aan de adres ruimte van uw virtuele Azure-netwerk te definiëren als: 10.0.1.0/24 en 10.0.2.0/28. Hoewel de adres ruimte waarden kunnen worden geaggregeerd, wordt aangeraden deze te vergelijken met de bereiken in het subnet. Op die manier kunt u ongebruikte IP-adresbereiken in grotere adres ruimten ergens anders in uw netwerk vermijden. **De adres ruimte van het virtuele netwerk is een IP-adres bereik. Het moet worden verzonden naar micro soft wanneer u een eerste implementatie vraagt**.
- **IP-adres bereik van Azure VM-subnet:** Dit is het IP-adres bereik dat u toewijst aan de para meter van het subnet van het virtuele netwerk van Azure. Deze para meter bevindt zich in uw virtuele Azure-netwerk en maakt verbinding met de SAP HANA-omgeving met grote instanties. Dit IP-adres bereik wordt gebruikt om IP-adressen toe te wijzen aan uw Azure-Vm's. De IP-adressen buiten dit bereik zijn toegestaan om verbinding te maken met uw SAP HANA grote instantie server (s). U kunt, indien nodig, meerdere Azure VM-subnetten gebruiken. We raden een/24 CIDR-blok aan voor elk Azure VM-subnet. Dit adres bereik moet deel uitmaken van de waarden die worden gebruikt in de adres ruimte van het virtuele Azure-netwerk. Hoe krijgt u dit IP-adres bereik? Het team of de service provider van het bedrijfs netwerk moet een IP-adres bereik opgeven dat niet in uw netwerk wordt gebruikt.
- **IP-adres bereik van het subnet van het virtuele netwerk gateway:** Afhankelijk van de functies die u wilt gebruiken, is de aanbevolen grootte:
   - Ultra-Performance ExpressRoute-gateway:/26 adres blok--vereist voor de klasse van type II van Sku's.
   - Samen werking met VPN en ExpressRoute met behulp van een ExpressRoute virtuele netwerk gateway met hoge prestaties (of kleiner):/27-adres blok.
   - Alle andere situaties:/28-adres blok. Dit adres bereik moet deel uitmaken van de waarden die worden gebruikt in de waarden voor de VNet-adres ruimte. Dit adres bereik moet deel uitmaken van de waarden die worden gebruikt in de adres ruimte waarden van het virtuele Azure-netwerk die u naar micro soft verzendt. Hoe krijgt u dit IP-adres bereik? Het team of de service provider van het bedrijfs netwerk moet een IP-adres bereik opgeven dat momenteel niet in uw netwerk wordt gebruikt. 
- **Adres bereik voor er-P2P-connectiviteit:** Dit bereik is het IP-bereik voor uw SAP HANA grote instance ExpressRoute (er) P2P-verbinding. Dit bereik van IP-adressen moet een/29 CIDR IP-adres bereik zijn. Dit bereik mag niet overlappen met uw on-premises of andere IP-adresbereiken voor Azure. Dit IP-adres bereik wordt gebruikt voor het instellen van de verbindings connectiviteit van uw virtuele ExpressRoute-gateway naar de SAP HANA grote instantie servers. Hoe krijgt u dit IP-adres bereik? Het team of de service provider van het bedrijfs netwerk moet een IP-adres bereik opgeven dat momenteel niet in uw netwerk wordt gebruikt. **Dit bereik is een IP-adres bereik. Het moet worden verzonden naar micro soft wanneer u een eerste implementatie vraagt**.  
- **Adres bereik van de server-IP-groep:** Dit IP-adres bereik wordt gebruikt om het afzonderlijke IP-adres toe te wijzen aan HANA-servers voor grote instanties. De aanbevolen subnet grootte is een/24 CIDR-blok. Als dat nodig is, kan het kleiner zijn, met slechts 64 IP-adressen. Vanuit dit bereik zijn de eerste 30 IP-adressen gereserveerd voor gebruik door micro soft. Zorg ervoor dat u het account voor dit feit hebt wanneer u de grootte van het bereik kiest. Dit bereik mag niet overlappen met uw on-premises of andere Azure IP-adressen. Hoe krijgt u dit IP-adres bereik? Het team of de service provider van het bedrijfs netwerk moet een IP-adres bereik opgeven dat momenteel niet in uw netwerk wordt gebruikt.  **Dit bereik is een IP-adres bereik dat naar micro soft moet worden verzonden wanneer u wordt gevraagd een eerste implementatie te installeren**.

Optionele IP-adresbereiken die uiteindelijk moeten worden verzonden naar micro soft:

- Als u ervoor kiest om [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md) te gebruiken om directe route ring van on-premises naar Hana grote instantie-eenheden in te scha kelen, moet u een ander/29 IP-adres bereik reserveren. Dit bereik mag niet overlappen met een van de andere IP-adresbereiken die u eerder hebt gedefinieerd.
- Als u ervoor kiest om [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md) te gebruiken om direct routeren vanuit een Hana grote instantie-Tenant in een Azure-regio naar een andere Hana grote instantie-Tenant in een andere Azure-regio te scha kelen, moet u een ander/29 IP-adres bereik reserveren. Dit bereik mag niet overlappen met een van de andere IP-adresbereiken die u eerder hebt gedefinieerd.

Voor meer informatie over Global Reach ExpressRoute en gebruik rond HANA grote instanties controleert u de documenten:

- [Netwerk architectuur van SAP HANA (grote exemplaren)](./hana-network-architecture.md)
- [Een virtueel netwerk verbinden met HANA grote instanties](./hana-connect-vnet-express-route.md)
 
U moet de IP-adresbereiken definiëren en plannen die eerder zijn beschreven. U hoeft deze echter niet allemaal naar micro soft te verzenden. De IP-adresbereiken die u nodig hebt voor de naam van micro soft zijn:

- Azure Virtual Network-adres ruimte (n)
- Adres bereik voor er-P2P-connectiviteit
- Adres bereik van de server-IP-groep

Als u extra virtuele netwerken toevoegt die verbinding moeten maken met HANA grote instanties, moet u de nieuwe Azure Virtual Network-adres ruimte die u aan micro soft toevoegt, indienen. 

Hieronder volgt een voor beeld van de verschillende bereiken en enkele voor beelden van de bereiken die u nodig hebt om micro soft te configureren en uiteindelijk aan te bieden. De waarde voor de adres ruimte van het virtuele Azure-netwerk wordt niet geaggregeerd in het eerste voor beeld. Het is echter gedefinieerd vanuit de bereiken van het eerste IP-adres bereik van een Azure-VM-subnet en het IP-adres bereik van het subnet van de virtuele netwerk gateway. 

U kunt meerdere VM-subnetten in het virtuele Azure-netwerk gebruiken wanneer u de extra IP-adresbereiken van de extra VM-subnetten als onderdeel van de adres ruimte van het virtuele Azure-netwerk hebt geconfigureerd en verzonden.

![IP-adresbereiken vereist in SAP HANA op Azure (grote exemplaren) minimale implementatie](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

De afbeelding bevat niet de extra IP-adres bereik (en) die zijn vereist voor het optionele gebruik van ExpressRoute Global Reach.

U kunt ook de gegevens die u naar micro soft verzendt, samen voegen. In dat geval bevat de adres ruimte van het virtuele netwerk van Azure slechts één spatie. Als u de IP-adresbereiken uit het vorige voor beeld gebruikt, kan de geaggregeerde adres ruimte van het virtuele netwerk eruitzien als de volgende afbeelding:

![Tweede mogelijkheid van IP-adresbereiken vereist in SAP HANA op Azure (grote exemplaren) minimale implementatie](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

In het voor beeld, in plaats van twee kleinere bereiken die de adres ruimte van het virtuele Azure-netwerk hebben gedefinieerd, hebben we een groter bereik dat 4096 IP-adressen dekt. Een dergelijke grote definitie van de adres ruimte laat enkele niet-gebruikte grote bereiken ongebruikt. Omdat de waarde (n) van de virtuele netwerk ruimte wordt gebruikt voor het door sturen van BGP-routes, kan het gebruik van de niet-gebruikte bereiken on-premises of ergens anders in uw netwerk routerings problemen veroorzaken. De afbeelding bevat niet de extra IP-adres bereik (en) die zijn vereist voor het optionele gebruik van ExpressRoute Global Reach.

We raden u aan de adres ruimte nauw keurig te laten afstemmen op de daad werkelijke adres ruimte van het subnet die u gebruikt. Indien nodig kunt u later altijd nieuwe adres ruimte waarden toevoegen zonder uitval tijd op het virtuele netwerk.
 
> [!IMPORTANT] 
> Elk IP-adres bereik in er-P2P, de server-IP-adres groep en de virtuele Azure-netwerk ruimte mogen **niet** overlappen met elkaar of met een ander bereik dat in uw netwerk wordt gebruikt. Elk moet afzonderlijk zijn. Als de twee vorige afbeeldingen worden weer gegeven, kunnen ze ook geen subnet van een ander bereik zijn. Als overlap pingen tussen bereiken optreden, kan het virtuele netwerk van Azure mogelijk geen verbinding maken met het ExpressRoute-circuit.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Volgende stappen na het bereiken van adresbereiken zijn gedefinieerd
Nadat de IP-adresbereiken zijn gedefinieerd, moet u het volgende doen:

1. Verzend de IP-adresbereiken voor de adres ruimte van het virtuele netwerk van Azure, het adres bereik voor de verbinding en de IP-groep van de server, samen met andere gegevens die aan het begin van het document staan vermeld. Op dit moment kunt u ook beginnen met het maken van het virtuele netwerk en de VM-subnetten. 
2. Er wordt een ExpressRoute-circuit gemaakt door micro soft tussen uw Azure-abonnement en de HANA grote instantie stempel.
3. Er wordt een Tenant netwerk gemaakt op basis van de stempel van het grote exemplaar van micro soft.
4. Micro soft configureert netwerken in de infra structuur van het SAP HANA op Azure (grote instanties) om IP-adressen te accepteren van de adres ruimte van uw virtuele Azure-netwerk die met HANA grote instanties communiceert.
5. Afhankelijk van de specifieke SAP HANA op Azure (grote instanties) SKU die u hebt gekocht, wijst micro soft een reken eenheid toe aan een Tenant netwerk. Ook wordt opslag toegewezen en gekoppeld en wordt het besturings systeem (SUSE of Red Hat Linux) geïnstalleerd. IP-adressen voor deze eenheden worden opgehaald uit het bereik van de IP-adres groep van de server die u hebt verzonden naar micro soft.

Aan het einde van het implementatie proces levert micro soft de volgende gegevens aan u:
- Informatie die nodig is om uw Azure Virtual Network (s) te verbinden met het ExpressRoute-circuit dat virtuele Azure-netwerken verbindt met HANA grote instanties:
     - Autorisatie sleutel (s)
     - ExpressRoute PeerID
- Gegevens voor toegang tot HANA grote instanties nadat u het ExpressRoute-circuit en het virtuele netwerk van Azure hebt ingesteld.

U kunt ook de volg orde van het verbinden van HANA grote instanties in het document vinden [SAP Hana op Azure (grote exemplaren) Setup](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Veel van de volgende stappen worden weer gegeven in een voorbeeld implementatie in dat document. 

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg [verbinding maken met een virtueel netwerk met Hana grote instantie ExpressRoute](hana-connect-vnet-express-route.md).
