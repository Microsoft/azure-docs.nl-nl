---
title: Configuratie van Azure-toepassing gateway-infra structuur
description: In dit artikel wordt beschreven hoe u de infra structuur van de Azure-toepassing-gateway configureert.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: f214b0b0751f44ea1357f569fd814a7621af61ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397617"
---
# <a name="application-gateway-infrastructure-configuration"></a>Configuratie van Application Gateway-infra structuur

De infra structuur van de toepassings Gateway omvat het virtuele netwerk, subnetten, netwerk beveiligings groepen en door de gebruiker gedefinieerde routes.

## <a name="virtual-network-and-dedicated-subnet"></a>Virtueel netwerk en toegewezen subnet

Een toepassings gateway is een speciale implementatie in uw virtuele netwerk. In het virtuele netwerk is een toegewezen subnet vereist voor de toepassings gateway. U kunt meerdere exemplaren van een bepaalde toepassings gateway-implementatie in een subnet hebben. U kunt ook andere toepassings gateways implementeren in het subnet. Maar u kunt geen andere resources implementeren in het subnet van de toepassings gateway. U kunt Standard_v2 en de standaard Azure-toepassing gateway niet op hetzelfde subnet combi neren.

> [!NOTE]
> [Het beleid voor endpoints van virtuele netwerken](../virtual-network/virtual-network-service-endpoint-policies-overview.md) wordt momenteel niet ondersteund in een Application Gateway-subnet.

### <a name="size-of-the-subnet"></a>Grootte van het subnet

Application Gateway maakt gebruik van een privé-IP-adres per exemplaar, plus een ander privé-IP-adres als er een privé-front-end-IP is geconfigureerd.

Azure reserveert ook vijf IP-adressen in elk subnet voor intern gebruik: de eerste vier en het laatste IP-adres. Denk bijvoorbeeld aan 15 Application Gateway-instanties zonder persoonlijk front-end IP-adres. U hebt ten minste 20 IP-adressen voor dit subnet nodig: vijf voor intern gebruik en 15 voor de Application Gateway-exemplaren.

Overweeg een subnet met 27 Application Gateway-instanties en een IP-adres voor een privé-front-end-IP. In dit geval hebt u 33 IP-adressen nodig: 27 voor de Application Gateway-instanties, één voor de privé-front-end en vijf voor intern gebruik.

Application Gateway-SKU (Standard of WAF) kan Maxi maal 32 instanties ondersteunen (32 instantie-IP-adressen + 1 privé front-end IP + 5 Azure-gereserveerde). de minimale subnet grootte/26 wordt daarom aanbevolen

Application Gateway (Standard_v2 of WAF_v2 SKU) kan Maxi maal 125 instanties ondersteunen (125 instantie-IP-adressen + 1 privé front-end IP + 5 Azure-gereserveerde). de minimale grootte van het subnet van/24 wordt aanbevolen

## <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen

Netwerk beveiligings groepen (Nsg's) worden ondersteund op Application Gateway. Er zijn echter enkele beperkingen:

- U moet binnenkomend Internet verkeer toestaan op TCP-poorten 65503-65534 voor de Application Gateway v1-SKU en TCP-poorten 65200-65535 voor de v2-SKU met het doel-subnet als **enige** en bron als **GatewayManager** -service label. Dit poort bereik is vereist voor de communicatie van Azure-infra structuur. Deze poorten worden beveiligd (vergrendeld) door Azure-certificaten. Externe entiteiten, met inbegrip van de klanten van deze gateways, kunnen niet communiceren op deze eind punten.

- De uitgaande Internet verbinding kan niet worden geblokkeerd. Standaard regels voor uitgaande verbindingen in de NSG staan Internet connectiviteit toe. U wordt aangeraden dat u:

  - Verwijder de standaard regels voor uitgaande verbindingen niet.
  - Maak geen andere uitgaande regels waarmee uitgaande verbindingen worden geweigerd.

- Verkeer van de **AzureLoadBalancer** -tag met het doel- **subnet moet worden** toegestaan.

### <a name="allow-access-to-a-few-source-ips"></a>Toegang tot een aantal bron-Ip's toestaan

Voor dit scenario gebruikt u Nsg's in het subnet Application Gateway. Plaats de volgende beperkingen op het subnet in deze volg orde van prioriteit:

1. Sta binnenkomend verkeer van een bron-IP of IP-bereik met de bestemming als het gehele adres bereik van Application Gateway subnet en de doel poort toe als uw binnenkomende toegangs poort, bijvoorbeeld poort 80 voor HTTP-toegang.
2. Sta binnenkomende aanvragen van de bron als **GatewayManager** -service label en-bestemming toe als **wille keurige** en doel poorten als 65503-65534 voor de Application Gateway v1-SKU en poorten 65200-65535 voor v2-SKU voor de [status communicatie van back-end](./application-gateway-diagnostics.md). Dit poort bereik is vereist voor de communicatie van Azure-infra structuur. Deze poorten worden beveiligd (vergrendeld) door Azure-certificaten. Zonder de juiste certificaten kunnen externe entiteiten geen wijzigingen op deze eind punten initiëren.
3. Sta binnenkomende Azure Load Balancer tests (*AzureLoadBalancer* tag) en binnenkomend virtueel netwerk verkeer (*VirtualNetwork* -tag) toe aan de [netwerk beveiligings groep](../virtual-network/network-security-groups-overview.md).
4. Alle andere binnenkomende verkeer blok keren met behulp van de regel deny-all.
5. Uitgaand verkeer naar Internet toestaan voor alle bestemmingen.

## <a name="supported-user-defined-routes"></a>Ondersteunde door de gebruiker gedefinieerde routes 

> [!IMPORTANT]
> Het gebruik van Udr's op het Application Gateway subnet kan ertoe leiden dat de status in de status [weergave van de back-end](./application-gateway-diagnostics.md#back-end-health) wordt weer gegeven als **onbekend**. Het kan ook leiden tot het genereren van Application Gateway logboeken en de metrische gegevens. U wordt aangeraden Udr's niet te gebruiken op het Application Gateway subnet, zodat u de status, logboeken en metrische gegevens van de back-end kunt bekijken.

- **v1**

   Voor de V1-SKU worden door de gebruiker gedefinieerde routes (Udr's) ondersteund op het subnet van de Application Gateway, zolang ze de end-to-end-communicatie van aanvragen en antwoorden niet wijzigen. U kunt bijvoorbeeld een UDR in het subnet van Application Gateway instellen om te verwijzen naar een firewall apparaat voor pakket inspectie. Maar u moet ervoor zorgen dat het pakket na de inspectie de beoogde bestemming kan bereiken. Als u dit niet doet, kan dit leiden tot een onjuiste werking van de status test of het routeren van verkeer. Hiertoe behoren geleerde routes of standaard 0.0.0.0/0-routes die worden door gegeven door Azure ExpressRoute of VPN-gateways in het virtuele netwerk. Elk scenario waarin 0.0.0.0/0 on-premises (geforceerde Tunneling) moet worden omgeleid, wordt niet ondersteund voor v1.

- **offload**

   Voor de v2-SKU worden de volgende en niet-ondersteunde scenario's ondersteund:

   **v2 ondersteunde scenario's**
   > [!WARNING]
   > Een onjuiste configuratie van de route tabel kan leiden tot asymmetrische route ring in Application Gateway v2. Zorg ervoor dat alle verkeers verkeer van beheer/beheer rechtstreeks naar het Internet wordt verzonden en niet via een virtueel apparaat. Logboek registratie en metrische gegevens kunnen ook worden beïnvloed.


  **Scenario 1**: UDR-route doorgifte van Border Gateway Protocol (BGP) naar het Application Gateway subnet uitschakelen

   Soms wordt de standaard gateway route (0.0.0.0/0) geadverteerd via de ExpressRoute of VPN-gateways die zijn gekoppeld aan het Application Gateway virtuele netwerk. Hiermee wordt het verkeer van het beheer vlak onderbroken, waarvoor een direct pad naar het internet is vereist. In dergelijke scenario's kan een UDR worden gebruikt om BGP-route doorgifte uit te scha kelen. 

   Als u BGP-route doorgifte wilt uitschakelen, gebruikt u de volgende stappen:

   1. Maak een resource voor de route tabel in Azure.
   2. Schakel de para meter voor **gateway route doorgifte van het virtuele netwerk** uit. 
   3. Koppel de route tabel aan het juiste subnet. 

   Het inschakelen van de UDR voor dit scenario mag geen bestaande instellingen verstoren.

  **Scenario 2**: UDR om 0.0.0.0/0 te sturen naar Internet

   U kunt een UDR maken om het 0.0.0.0/0-verkeer rechtstreeks naar Internet te verzenden. 

  **Scenario 3**: UDR for Azure Kubernetes service met kubenet

  Als u kubenet met Azure Kubernetes service (AKS) en Application Gateway ingangs controller (AGIC) gebruikt, hebt u een route tabel nodig om verkeer toe te staan van Application Gateway naar het juiste knoop punt wordt verzonden. Dit is niet nodig als u Azure CNI gebruikt. 

  Volg de onderstaande stappen om de route tabel te gebruiken om kubenet toe te laten werken:

  1. Ga naar de resource groep die is gemaakt door AKS (de naam van de resource groep moet beginnen met ' MC_ ')
  2. Zoek de route tabel die is gemaakt door AKS in die resource groep. De route tabel moet worden gevuld met de volgende gegevens:
     - Het adres voorvoegsel moet het IP-bereik zijn van het Peul dat u wilt bereiken in AKS. 
     - Het type van de volgende hop moet virtueel apparaat zijn. 
     - Het adres van de volgende hop moet het IP-adres zijn van het knoop punt dat als host fungeert voor het Peul.
  3. Deze route tabel koppelen aan het Application Gateway subnet. 
    
  **v2 niet-ondersteunde scenario's**

  **Scenario 1**: UDR voor virtuele apparaten

  Elk scenario waarbij 0.0.0.0/0 moet worden omgeleid via een virtueel apparaat, een hub/spoke-virtueel netwerk of een on-premises (geforceerde Tunneling) wordt niet ondersteund voor v2.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over front-end-IP-adres configuratie](configuration-front-end-ip.md).