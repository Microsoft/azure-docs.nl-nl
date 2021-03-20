---
title: Portal-instellingen voor Azure Load Balancer
description: Aan de slag met het leren van Azure Load Balancer Portal-instellingen
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/8/2020
ms.author: allensu
ms.openlocfilehash: e1080aea12e70f4312fbee07b063d5a5cfbd1201
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89596279"
---
# <a name="azure-load-balancer-portal-settings"></a>Portal-instellingen voor Azure Load Balancer

Wanneer u Azure Load Balancer maakt, vindt u in dit artikel informatie over de afzonderlijke instellingen en wat de juiste configuratie voor u is.

## <a name="create-load-balancer"></a>Load balancer maken

Azure Load Balancer is een netwerk load balancer dat verkeer distribueert over VM-exemplaren in de back-end-pool.

### <a name="basics"></a>Basisbeginselen

Op het tabblad **basis principes** van de pagina Create Load Balancer Portal ziet u de volgende informatie:



| Instelling |  Details |
| ---------- | ---------- |
| Abonnement  | Selecteer uw abonnement. Deze selectie is het abonnement waarop u uw load balancer wilt implementeren. |
| Resourcegroep | Selecteer **nieuwe maken** en typ de naam voor de resource groep in het tekstvak. Als u een bestaande resource groep hebt gemaakt, selecteert u deze. |
| Name | Deze instelling is de naam voor uw Azure Load Balancer. |
| Region | Selecteer een Azure-regio waarin u uw load balancer wilt implementeren. |
| Type | De Load Balancer heeft twee typen: </br> **Intern (privé)** </br> **Openbaar (extern)**.</br> Met een interne load balancer (ILB) wordt verkeer gerouteerd naar de back-end-groeps leden via een privé-IP-adres.</br> Een openbaar load balancer stuurt aanvragen van clients via internet naar de back-end-groep.</br> Meer informatie over [Load Balancer typen](components.md#frontend-ip-configuration-).|
| SKU  | selecteer **Standaard**. </br> De Load Balancer heeft twee Sku's: **Basic** en **Standard**. </br> Basic heeft beperkte functionaliteit. </br> **Standaard** wordt aanbevolen voor productie werkbelastingen. </br> Meer informatie over [sku's](skus.md). |

Als u **openbaar** selecteert als uw type, ziet u de volgende informatie:

| Instelling |  Details |
| ---------- | ---------- |
| Openbaar IP-adres | Selecteer **nieuwe maken** om een openbaar IP-adres te maken voor uw open bare Load Balancer. </br> Als u een bestaand openbaar IP-adres hebt, selecteert u **bestaande gebruiken**.  |
| Naam openbaar IP-adres | De naam van het open bare IP-adres.|
| Openbaar IP-adres SKU | Open bare IP-adressen hebben twee Sku's: **Basic** en **Standard**. </br> Basic biedt geen ondersteuning voor zone-tolerantie-en zonegebonden-kenmerken. </br> **Standaard** wordt aanbevolen voor productie werkbelastingen. </br> De Load Balancer en het open bare IP-adres-Sku's **moeten overeenkomen**. |
| Toewijzing | **Statisch** is automatisch geselecteerd voor standaard. </br> Algemene open bare Ip's hebben twee typen: **dynamisch** en **statisch**. </br> Dynamische open bare IP-adressen worden pas toegewezen nadat ze zijn gemaakt. </br> Ip's kunnen verloren gaan als de bron wordt verwijderd. </br> Statische IP-adressen worden aanbevolen. |
| Beschikbaarheidszone | Selecteer **Zone-redundant** om een tolerante load balancer te maken. </br> Als u een zonegebonden-load balancer wilt maken, selecteert u een specifieke zone van **1**, **2** of **3**. </br> Standard-load balancer en open bare IPs-zones voor ondersteuning. </br> Meer informatie over [Load Balancer-en beschikbaarheids zones](load-balancer-standard-availability-zones.md). </br> U ziet geen zone selectie voor Basic. Een basisversie van een load balancer biedt geen ondersteuning voor zones. |
| Een openbaar IPv6-adres toevoegen | De Load Balancer biedt ondersteuning voor **IPv6-** adressen voor uw frontend. </br> Meer informatie over [Load Balancer en IPv6](load-balancer-ipv6-overview.md)
| Routeringsvoorkeur | Selecteer **micro soft-netwerk**. </br> Micro soft-netwerk betekent dat verkeer wordt gerouteerd via het wereld wijde micro soft-netwerk. </br> Internet betekent dat verkeer wordt gerouteerd via het netwerk van de Internet service provider. </br> Meer informatie over [routerings voorkeuren](../virtual-network/routing-preference-overview.md)|

:::image type="content" source="./media/manage/create-public-load-balancer-basics.png" alt-text="Maak load balancer openbaar." border="true":::

Als u **intern** selecteert in type, ziet u de volgende informatie:

| Instelling |  Details |
| ---------- | ---------- |
| Virtueel netwerk | Het virtuele netwerk waarvan u de interne load balancer deel moet uitmaken. </br> Het persoonlijke frontend-IP-adres dat u voor uw interne load balancer selecteert, is van dit virtuele netwerk. |
| IP-adrestoewijzing | Uw opties zijn **statisch** of **dynamisch**. </br> Statisch garandeert dat het IP-adres niet wordt gewijzigd. Een dynamisch IP-adres kan veranderen. |
| Beschikbaarheidszone | Uw opties zijn: </br> **Zone-redundant** </br> **Zone 1** </br> **Zone 2** </br> **Zone 3** </br> Selecteer een **zone-redundante** IP als u een Load Balancer wilt maken dat Maxi maal beschikbaar is voor fouten in de beschikbaarheids zone. |

:::image type="content" source="./media/manage/create-internal-load-balancer-basics.png" alt-text="Load balancer intern maken." border="true":::

## <a name="frontend-ip-configuration"></a>Front-end-IP-configuratie

Het IP-adres van uw Azure Load Balancer. Dit is het contactpunt voor clients. 

U kunt een of meer frontend-IP-configuraties hebben. Als u de sectie basis beginselen hierboven hebt door lopen, hebt u al een front-end voor uw load balancer gemaakt. 

Als u een front-end-IP-configuratie aan uw load balancer wilt toevoegen, gaat u naar uw load balancer in de Azure Portal, selecteert u **frontend IP-configuratie** en selecteert u **+ toevoegen**.

| Instelling |  Details |
| ---------- | ---------- |
| Name | De naam van de front-end-IP-configuratie. |
| IP-versie | De versie van het IP-adres dat u wilt voor uw frontend. </br> De Load Balancer ondersteunt zowel IPv4-als IPv6-frontend-IP-configuraties. |
| IP-type | IP-type bepaalt of één IP-adres is gekoppeld aan uw front-end of een bereik van IP-adressen met behulp van een IP-voor voegsel. </br> Een [openbaar IP-voor voegsel](../virtual-network/public-ip-address-prefix.md) helpt wanneer u herhaaldelijk verbinding met hetzelfde eind punt moet maken. Het voor voegsel zorgt ervoor dat voldoende poorten worden gegeven om te helpen bij het oplossen van problemen met de SNAT-poort. |
| Openbaar IP-adres (of voor voegsel als u hierboven een voor voegsel hebt geselecteerd) | Selecteer of maak een nieuw openbaar IP-adres (of voor voegsel) voor uw load balancer frontend. |

:::image type="content" source="./media/manage/frontend.png" alt-text="Pagina front-end-IP-configuratie maken." border="true":::

## <a name="backend-pools"></a>Back-end-Pools

Een back-end-adres groep bevat de IP-adressen van de virtuele netwerk interfaces in de back-end-pool. 

Als u een back-end-groep aan uw load balancer wilt toevoegen, gaat u naar uw load balancer in de Azure Portal, selecteert u **back-endservers** en selecteert u **+ toevoegen**.

| Instelling | Details |
| ---------- |  ---------- |
| Name | De naam van de back-end-pool. |
| Virtueel netwerk | Het virtuele netwerk van uw back-end-exemplaren is. |
| IP-versie | Uw opties zijn **IPv4** of **IPv6**. |

U kunt virtuele machines of virtuele-machine schaal sets toevoegen aan de back-end-groep van uw Azure Load Balancer. Maak eerst de virtuele machines of virtuele-machine schaal sets. Voeg deze vervolgens toe aan de load balancer in de portal.

:::image type="content" source="./media/manage/backend.png" alt-text="Pagina Back-upgroep maken." border="true":::

## <a name="health-probes"></a>Statuscontroles

Een status test wordt gebruikt om de status van uw back-end-Vm's of-exemplaren te bewaken. De status probe bepaalt wanneer nieuwe verbindingen worden verzonden naar een exemplaar op basis van status controles. 

Als u een status test aan uw load balancer wilt toevoegen, gaat u naar uw load balancer in de Azure Portal, selecteert u **status controles** en selecteert u **+ toevoegen**.

| Instelling | Details |
| ---------- | ---------- |
| Name | De naam van uw Health probe. |
| Protocol | Het protocol dat u selecteert, bepaalt het type controle dat wordt gebruikt om te bepalen of de back-end-exemplaren in orde zijn. </br> Uw opties zijn: </br> **TCP** </br> **HTTPS** </br> **HTTP** </br> Zorg ervoor dat u het juiste protocol gebruikt. Deze selectie is afhankelijk van de aard van uw toepassing. </br> De configuratie van de status test en test reacties bepaalt welke exemplaren van de back-endadresgroep nieuwe stromen ontvangen. </br> U kunt Health tests gebruiken om het mislukken van een toepassing op een back-end-eind punt te detecteren. </br> Meer informatie over [status controles](load-balancer-custom-probe-overview.md). |
| Poort | De doel poort voor de status test. </br> Deze instelling is de poort op het back-end-exemplaar dat door de status test wordt gebruikt om de status van het exemplaar te bepalen. |
| Interval | Het aantal seconden tussen de test pogingen. </br> Het interval bepaalt hoe vaak de Health probe zal proberen om het back-end-exemplaar te bereiken. </br> Als u 5 selecteert, wordt de tweede test poging gedaan na vijf seconden, enzovoort. |
| Drempelwaarde voor beschadigde status | Het aantal opeenvolgende test fouten dat moet optreden voordat een VM als slecht wordt beschouwd.</br> Als u 2 selecteert, worden er na twee opeenvolgende fouten geen nieuwe stromen ingesteld op deze back-end-instantie. |

:::image type="content" source="./media/manage/health-probe.png" alt-text="Status test toevoegen." border="true":::

## <a name="load-balancing-rules"></a>Taakverdelingsregels

Hiermee wordt gedefinieerd hoe binnenkomend verkeer wordt gedistribueerd naar alle exemplaren in de back-end-pool. Een taakverdelingsregel wijst een gegeven front-end-IP-configuratie en -poort toe aan meerdere back-end-IP-adressen en -poorten.

Als u een load balancer regel wilt toevoegen aan uw load balancer, gaat u naar uw load balancer in de Azure Portal, selecteert u **taakverdelings regels** en selecteert u **+ toevoegen**.
    
| Instelling | Details |
| ---------- | ---------- |
| Name | De naam van de load balancer regel. |
| IP-versie | Uw opties zijn **IPv4** of **IPv6**.  |
| IP-adres voor front-end | Selecteer het frontend-IP-adres. </br> Het front-end-IP-adres van de load balancer waaraan u de load balancer regel wilt koppelen.|
| Protocol | Azure Load Balancer is een laag 4-netwerk load balancer. </br> U hebt de volgende opties: **TCP** of **UDP**. |
| Poort | Deze instelling is de poort die is gekoppeld aan het frontend-IP-adres dat verkeer moet worden gedistribueerd op basis van deze taakverdelings regel. |
| Back-endpoort | Deze instelling is de poort van de exemplaren in de back-endadresgroep waarnaar de load balancer verkeer moet verzenden. Deze instelling kan hetzelfde zijn als de frontend-poort of andere als u de flexibiliteit voor uw toepassing nodig hebt. |
| Back-end-pool | De back-end-groep waarop u deze load balancer regel wilt Toep assen. |
| Statustest | De status test die u hebt gemaakt om de status van de exemplaren in de back-end-pool te controleren. </br> Alleen in orde zijnde instanties ontvangen nieuw verkeer. |
| Sessiepersistentie |  Uw opties zijn: </br> **Geen** </br> **IP van client** </br> **Client-IP en-protocol**</br> </br> Verkeer van een client naar dezelfde virtuele machine in de back-end-groep onderhouden. Dit verkeer wordt gedurende de duur van de sessie bewaard. </br> **Geen** geeft aan dat opeenvolgende aanvragen van dezelfde client kunnen worden verwerkt door elke virtuele machine. </br> **Client-IP** geeft aan dat opeenvolgende aanvragen van hetzelfde client-IP-adres worden verwerkt door dezelfde virtuele machine. </br> **Client-IP en-protocol** zorgen ervoor dat opeenvolgende aanvragen van hetzelfde client-IP-adres en-protocol worden verwerkt door dezelfde virtuele machine. </br> Meer informatie over [distributie modi](load-balancer-distribution-mode.md). |
| Time-out voor inactiviteit (minuten) | Ervoor zorgen dat een **TCP** **-of http-** verbinding wordt geopend zonder dat er wordt gebruikgemaakt van clients om Keep-Alive-berichten te verzenden |  
| Opnieuw instellen van TCP | De Load Balancer kan **TCP-resets** verzenden om een meer voorspel bare toepassings gedrag te maken wanneer de verbinding niet actief is. </br> Meer informatie over [TCP Reset](load-balancer-tcp-reset.md)|
| Zwevend IP-adres | Zwevend IP is de terminologie van Azure voor een deel van wat **direct server Return (DSR)** wordt genoemd. </br> DSR bestaat uit twee delen: <br> 1. stroom topologie </br> 2. een IP-adres toewijzings schema op platform niveau. </br></br> Azure Load Balancer werkt altijd in een DSR-stroom topologie, ongeacht of zwevende IP is ingeschakeld. </br> Met deze bewerking wordt aangegeven dat het uitgaande deel van een stroom altijd correct wordt herschreven naar flow direct terug naar de oorsprong. </br> Zonder zwevend IP-adres van Azure wordt een traditioneel IP-toewijzings schema voor taak verdeling weer gegeven, de VM-exemplaren IP. </br> Als zwevende IP wordt ingeschakeld, wordt de IP-adres toewijzing gewijzigd in het frontend-IP van de Load Balancer om extra flexibiliteit te bieden. </br> Zie meerdere front-ends [voor Azure Load Balancer](load-balancer-multivip-overview.md)voor meer informatie.|
| Impliciete uitgaande regels maken | Selecteer **Nee**. </br> Standaard: **disableoutboundsnat toegevoegd = False**  </br> In dit geval wordt uitgaand uitgevoerd via hetzelfde frontend-IP-adres. </br></br> **Disableoutboundsnat toegevoegd = True** </br>In dit geval zijn uitgaande regels nodig voor uitgaand verkeer. |

:::image type="content" source="./media/manage/load-balancing-rule.png" alt-text="Regel voor taak verdeling toevoegen." border="true":::

## <a name="inbound-nat-rules"></a>Inkomende NAT-regels

Via een inkomende NAT-regel wordt binnenkomend verkeer doorgestuurd naar een combinatie van een front-end-IP-adres en een geselecteerde poort. 

Het verkeer wordt verzonden naar een specifieke virtuele machine of exemplaar in de back-end-pool. Port forwarding wordt gedaan door de dezelfde hash-distributie als taakverdeling.

Als voor uw scenario Remote Desktop Protocol (RDP) of SSH-sessies (Secure Shell) zijn vereist voor het scheiden van VM-exemplaren in een back-end-groep. Meerdere interne eind punten kunnen worden toegewezen aan poorten op hetzelfde frontend-IP-adres. 

De frontend-IP-adressen kunnen worden gebruikt voor het extern beheren van uw Vm's zonder een extra Jump box.

Als u een binnenkomende NAT-regel aan uw load balancer wilt toevoegen, gaat u naar uw load balancer in de Azure Portal, selecteert u **binnenkomende NAT-regels** en selecteert u **+ toevoegen**.

| Instelling | Details |
| ---------- | ---------- |
| Name | De naam van uw binnenkomende NAT-regel |
| IP-adres voor front-end | Selecteer het frontend-IP-adres. </br> Het front-end-IP-adres van uw load balancer waaraan u de binnenkomende NAT-regel wilt koppelen. |
| IP-versie | Uw opties zijn IPv4 en IPv6. |
| Service | Het type service dat u op Azure Load Balancer uitvoert. </br> Met een selectie hier worden de poort gegevens op de juiste wijze bijgewerkt. |
| Protocol | Azure Load Balancer is een laag 4-netwerk load balancer. </br> U hebt de volgende opties: TCP of UDP. |
| Time-out voor inactiviteit (minuten) | Zorg ervoor dat een TCP-of HTTP-verbinding is geopend zonder dat de clients keep-alive-berichten kunnen verzenden. |
| TCP opnieuw instellen | Load Balancer kunt TCP-resets verzenden om een meer voorspel bare toepassings gedrag te maken wanneer de verbinding niet actief is. </br> Meer informatie over [TCP Reset](load-balancer-tcp-reset.md) |
| Poort | Deze instelling is de poort die is gekoppeld aan het frontend-IP-adres dat verkeer moet worden gedistribueerd op basis van deze binnenkomende NAT-regel. |
| Virtuele doelmachine. | Het onderdeel virtuele machine van de back-endserver waaraan u deze regel wilt koppelen. |
| Poort toewijzing | Deze instelling kan standaard of aangepast zijn op basis van de voor keuren van uw toepassing. |

:::image type="content" source="./media/manage/inbound-nat-rule.png" alt-text="Voeg een binnenkomende NAT-regel toe." border="true":::

## <a name="outbound-rules"></a>Regels voor uitgaand verkeer

Uitgaande regels van load balancers configureren uitgaande SNAT voor virtuele machines in de back-endgroep.

Als u een regel voor uitgaande verbindingen aan uw load balancer wilt toevoegen, gaat u naar uw load balancer in de Azure Portal, selecteert u **Uitgaande regels** en selecteert u **+ toevoegen**.

| Instelling | Details |
| ------- | ------ |
| Name | De naam van uw uitgaande regel. |
| IP-adres voor front-end | Selecteer het frontend-IP-adres. </br> Het front-end-IP-adres van uw load balancer waaraan de uitgaande regel moet worden gekoppeld. |
| Protocol | Azure Load Balancer is een laag 4-netwerk load balancer. </br> U hebt de volgende opties: **alle**, **TCP** of **UDP**. |
| Time-out voor inactiviteit (minuten) | Zorg ervoor dat een **TCP** **-of http-** verbinding is geopend zonder dat de clients keep-alive-berichten kunnen verzenden. |
| TCP opnieuw instellen | De Load Balancer kan **TCP-resets** verzenden om een meer voorspel bare toepassings gedrag te maken wanneer de verbinding niet actief is. </br> Meer informatie over [TCP Reset](load-balancer-tcp-reset.md) |
| Back-end-pool | De back-end-groep waarop u deze uitgaande regel wilt Toep assen. |

### <a name="port-allocation"></a>Poort toewijzing

| Instelling | Details |
| ------- | ------ |
| Poort toewijzing | U kunt het beste **hand matig een aantal uitgaande poorten** selecteren.|

### <a name="outbound-ports"></a>Uitgaande poorten

| Instelling | Details |
| ------- | ------ |
| Kiezen op | Selecteer **Poorten per exemplaar** |
| Poorten per instantie | Voer **10.000** in. |

:::image type="content" source="./media/manage/outbound-rule.png" alt-text="Binnenkomende uitgaande regel toevoegen." border="true":::

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de verschillende voor waarden en instellingen in de Azure Portal voor Azure Load Balancer.

* Meer [informatie](./load-balancer-overview.md) over Azure Load Balancer.
* [Veelgestelde vragen](./load-balancer-faqs.md) over Azure Load Balancer.