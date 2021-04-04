---
title: 'Azure VMware-oplossing door CloudSimple: een laag 2-netwerk on-premises uitrekken naar een Privécloud'
description: Hierin wordt beschreven hoe u een laag 2-VPN instelt tussen NSX-T op een CloudSimple-Privécloud en een on-premises zelfstandige NSX Edge-client
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 06446b6c36e36466fe891d7327d8151603cdecd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899368"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>Workloads migreren met behulp van uitgerekte netwerken van Laag 2

In deze hand leiding wordt beschreven hoe u Layer 2 VPN (L2VPN) kunt gebruiken om een laag 2-netwerk uit te breiden van uw on-premises omgeving naar uw CloudSimple-Privécloud. Met deze oplossing wordt de migratie van werk belastingen die worden uitgevoerd in uw on-premises VMware-omgeving naar de Privécloud in azure binnen dezelfde subnet-adres ruimte, zonder dat u uw werk belastingen hoeft te herschrijven.

L2VPN op basis van het uitrekken van laag 2-netwerken kan met of zonder NSX-netwerken in uw on-premises VMware-omgeving werken. Als u geen NSX-netwerken voor on-premises werk belastingen hebt, kunt u een zelfstandige NSX Edge Services-gateway gebruiken.

> [!NOTE]
> In deze hand leiding wordt het scenario beschreven waarbij on-premises en de data centers van de Privécloud zijn verbonden via site-naar-site-VPN.

## <a name="deployment-scenario"></a>Implementatiescenario

Om uw on-premises netwerk uit te breiden met behulp van L2VPN, moet u een L2VPN-server (Destination NSX-T Tier0 router) en een L2VPN-client (zelfstandige bron-client) configureren.  

In dit implementatie scenario is uw Privécloud verbonden met uw on-premises omgeving via een site-naar-site-VPN-tunnel waarmee on-premises beheer en vMotion-subnetten kunnen communiceren met de Private Cloud Management en vMotion-subnetten. Deze indeling is nood zakelijk voor cross vCenter vMotion (xVC-vMotion). Een NSX-T Tier0-router wordt geïmplementeerd als een L2VPN-server in de Privécloud.

Zelfstandige NSX-Edge wordt in uw on-premises omgeving geïmplementeerd als een L2VPN-client en vervolgens gekoppeld aan de L2VPN-server. Er wordt aan elke zijde een GRE-tunnel eindpunt gemaakt en geconfigureerd voor het ' Stretch ' van het on-premises laag 2-netwerk met uw Privécloud. Deze configuratie wordt weer gegeven in de volgende afbeelding.

![Implementatiescenario](media/l2vpn-deployment-scenario.png)

Zie voor meer informatie over migratie met behulp van L2 VPN [virtuele particuliere netwerken](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) in de VMware-documentatie.

## <a name="prerequisites-for-deploying-the-solution"></a>Vereisten voor het implementeren van de oplossing

Controleer of de volgende locaties aanwezig zijn voordat u de oplossing implementeert en configureert:

* De on-premises vSphere-versie is 6,7 U1 + of 6.5 P03 +.
* De on-premises vSphere-licentie bevindt zich op het niveau van de onderneming plus (voor de gedistribueerde switch vSphere).
* Identificeer het netwerkworkload Layer 2-netwerk dat moet worden uitgerekt naar uw Privécloud.
* Identificeer een laag 2-netwerk in uw on-premises omgeving voor het implementeren van uw L2VPN-client apparaat.
* [Er is al een privécloud gemaakt](create-private-cloud.md).
* De versie van het zelfstandige NSX-T-rand apparaat is compatibel met de NSX-T-beheer versie (NSX-T 2.3.0) die wordt gebruikt in uw Privécloud.
* Er is een trunk-poort groep gemaakt in de on-premises vCenter met vervalste overdrachten ingeschakeld.
* Een openbaar IP-adres is gereserveerd voor gebruik voor het NSX-T-client-uplink-IP-adres en 1:1 NAT is aanwezig voor de omzetting tussen de twee adressen.
* DNS-door sturen is ingesteld op de on-premises DNS-servers voor het az.cloudsimple.io-domein om te verwijzen naar de DNS-servers van de Privécloud.
* De RTT-latentie is kleiner dan of gelijk aan 150 MS, zoals vereist voor het werken met vMotion op de twee sites.

## <a name="limitations-and-considerations"></a>Beperkingen en overwegingen

De volgende tabel geeft een lijst van ondersteunde vSphere-versies en typen netwerk adapters.  

| vSphere-versie | Type bron-vSwitch | Virtuele NIC-stuur programma | Type doel-vSwitch | Ondersteund? |
------------ | ------------- | ------------ | ------------- | ------------- 
| Alles | DVS | Alles | DVS | Yes |
| vSphere 6,7 UI of hoger, 6.5 P03 of hoger | DVS | VMXNET3 | N-VDS | Yes |
| vSphere 6,7 UI of hoger, 6.5 P03 of hoger | DVS | E1000 | N-VDS | [Niet ondersteund per VWware](https://kb.vmware.com/s/article/56991) |
| vSphere 6,7 UI of 6.5 P03, NSX-V of-versies onder NSX-T 2.2, 6.5 P03 of hoger | Alles | Alles | N-VDS | [Niet ondersteund per VWware](https://kb.vmware.com/s/article/56991) |

Vanaf de VMware NSX-T 2,3 release:

* De logische switch op de Privécloud die is uitgerekt tot on-premises via L2VPN, kan niet tegelijkertijd worden gerouteerd. De uitgerekte logische switch kan niet worden verbonden met een logische router.
* L2VPN en op route gebaseerde IPSEC-Vpn's kunnen alleen worden geconfigureerd met API-aanroepen.

Zie voor meer informatie [virtuele particuliere netwerken](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) in de VMware-documentatie.

### <a name="sample-l2-vpn-deployment-addressing"></a>Voor beeld van L2-VPN-implementatie adressen

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>On-premises netwerk waarop de zelfstandige ESG (L2-VPN-client) is geïmplementeerd

| **Item** | **Waarde** |
|------------|-----------------|
| Netwerknaam | MGMT_NET_VLAN469 |
| VLAN | 469 |
| CIDR| 10.250.0.0/24 |
| Zelfstandig edge-apparaat-IP-adres | 10.250.0.111 |
| Het NAT IP-adres van het zelfstandige edge-apparaat | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>On-premises netwerk dat moet worden uitgerekt

| **Item** | **Waarde** |
|------------|-----------------|
| VLAN | 472 |
| CIDR| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>IP-schema van de privécloud voor NSX-T Tier0 router (L2-VPN)

| **Item** | **Waarde** |
|------------|-----------------|
| Loop back-interface | 192.168.254.254/32 |
| Tunnel interface | 5.5.5.1/29 |
| Logische switch (uitgerekt) | Stretch_LS |
| Loop back-interface (NAT IP-adres) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>Een particulier Cloud netwerk dat moet worden toegewezen aan het uitgerekte netwerk

| **Item** | **Waarde** |
|------------|-----------------|
| VLAN | 712 |
| CIDR| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>De logische router-ID ophalen die nodig is voor L2VPN

De volgende stappen laten zien hoe u de logische router-ID van de Tier0 DR-routerings instantie voor de IPsec-en L2VPN-Services kunt ophalen. De ID van de logische router is later nodig bij het implementeren van de L2VPN.

1. Meld u aan bij NSX-T-beheer `https://*nsx-t-manager-ip-address*` en selecteer **netwerk**  >  **routers**  >  **provider-**  >  **overzicht** van LR. Voor de **modus hoge Beschik baarheid** selecteert u **actief/stand-by**. Met deze actie wordt een pop-upvenster geopend waarin de Edge-VM wordt weer gegeven waarop de Tier0-router momenteel actief is.

    ![Selecteer actief/stand-by](media/l2vpn-fetch01.png)

2. Selecteer de randen van **infrastructuur**  >  **knooppunten**  >  . Noteer het IP-adres van het beheer van de actieve Edge-VM (Edge VM1) die in de vorige stap is geïdentificeerd.

    ![Notitie beheer-IP](media/l2vpn-fetch02.png)

3. Open een SSH-sessie met het IP-adres van het beheer van de Edge-VM. Voer de ```get logical-router``` opdracht uit met de gebruikers naam **beheerder** en het wacht woord **CloudSimple 123!**.

    ![Scherm opname waarin een open SSH-sessie wordt weer gegeven.](media/l2vpn-fetch03.png)

4. Als u geen vermelding ' DR-provider-LR ' ziet, voert u de volgende stappen uit.

5. Maak twee logische overlay-back-upswitches. Eén logische switch is uitgerekt tot on-premises waar de gemigreerde werk belastingen zich bevinden. Een andere logische switch is een dummy schakelaar. Zie [een logische switch maken](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html) in de documentatie van VMware voor instructies.

    ![Logische switch maken](media/l2vpn-fetch04.png)

6. Koppel de dummy switch aan de Tier1-router met een link local IP-adres of een niet-overlappend subnet van on-premises of uw Privécloud. Zie [een downlink-poort toevoegen aan een logische router in laag 1](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html) in de documentatie van VMware.

    ![Dummy switch koppelen](media/l2vpn-fetch05.png)

7. Voer de `get logical-router` opdracht opnieuw uit op de SSH-sessie van de Edge-VM. De UUID van de logische router ' DR-provider-LR ' wordt weer gegeven. Noteer de UUID die is vereist bij het configureren van de L2VPN.

    ![Scherm opname van de UUID van de logische router.](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>De ID van de logische switch ophalen die nodig is voor L2VPN

1. Meld u aan bij NSX-T Manager ( `https://nsx-t-manager-ip-address` ).
2. Selecteer switches van **Network**  >  **switching**  >    >  **\>< \logical switch**  >  **Overview**(Engelstalig).
3. Noteer de UUID van de logische switch Stretch, die vereist is voor het configureren van de L2VPN.

    ![logische-router uitvoer ophalen](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>Routerings-en beveiligings overwegingen voor L2VPN

Voor het tot stand brengen van een VPN-route op basis van IPsec tussen de NSX-T Tier0 router en de zelfstandige NSX Edge-client moet de loop back-interface van de NSX-T Tier0-router kunnen communiceren met het open bare IP-adres van de zelfstandige NSX-client op locatie via UDP 500/4500.

### <a name="allow-udp-5004500-for-ipsec"></a>UDP 500/4500 voor IPsec toestaan

1. [Maak een openbaar IP-adres](public-ips.md) voor de NSX-T Tier0 loop back-interface in de CloudSimple-Portal.

2. [Maak een firewall tabel](firewall.md) met stateful regels die binnenkomend UDP 500/4500-verkeer toestaan en koppel de firewall tabel aan het NSX-T HostTransport-subnet.

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>Het IP-adres van de loop back-interface adverteren naar het aan-netwerk

1. Maak een null-route voor het loop back-interface netwerk. Meld u aan bij NSX-T-beheer en selecteer **Network**  >  **Routing**  >  **routers**  >  **provider-LR**  >  **route ring**  >  **statische routes**. Klik op **Add**. Voor **netwerk** voert u het IP-adres van de loop back-interface in. Klik voor **volgende hops** op **toevoegen**, geef ' null ' op voor de volgende hop en behoud de standaard waarde van 1 voor de beheer afstand.

    ![Statische route toevoegen](media/l2vpn-routing-security01.png)

2. Een IP-voorvoegsel lijst maken. Meld u aan bij NSX-T-beheer en selecteer **netwerk**  >  **Routering**  >  **routers**  >  **provider-LR**  >  **routerings**  >  **lijst met IP-voor voegsel**. Klik op **Add**. Voer een naam in om de lijst aan te duiden. Voor voor **voegsels** klikt u twee keer op **toevoegen** . Voer op de eerste regel ' 0.0.0.0/0 ' in voor het **netwerk** en ' weigeren ' voor de **actie**. Selecteer in de tweede regel **een** voor **netwerk** en **toestaan** om **actie te ondernemen**.
3. Koppel de IP-voorvoegsel lijst aan beide BGP-neighbors (TOR). Als u de lijst met IP-voor voegsels aan de BGP-neighbor koppelt, wordt voor komen dat de standaard route in BGP wordt geadverteerd naar de TOR-switches. Elke andere route die de null-route bevat, adverteert echter het IP-adres van de loop back-interface aan de TOR-switches.

    ![Lijst met IP-voor voegsels maken](media/l2vpn-routing-security02.png)

4. Meld u aan bij NSX-T-beheer en selecteer **Network**  >  **Routing**  >  **routers**  >  **provider-LR**  >  **Routering**  >  **BGP**  >  **Neighbors**. Selecteer de eerste neighbor. Klik op  >  **adres families** bewerken. Voor de IPv4-serie bewerkt u de kolom **out filter** en selecteert u de lijst met IP-voor voegsels die u hebt gemaakt. Klik op **Opslaan**. Herhaal deze stap voor de tweede neighbor.

    ![IP-voorvoegsel lijst 1 koppelen lijst ](media/l2vpn-routing-security03.png) ![ met IP-voor voegsels 2](media/l2vpn-routing-security04.png)

5. Distribueer de statische null-route naar BGP. Als u de loop back-interface route wilt adverteren naar de aan, moet u de statische null-route opnieuw distribueren naar BGP. Meld u aan bij NSX-T-beheer en selecteer **Network**  >  **Routing**  >  **routers**  >  **provider-LR**  >  **route ring**  >  **route distributie**-  >  **Neighbors**. Selecteer **provider-LR-Route_Redistribution** en klik op **bewerken**. Schakel het selectie vakje **statisch** in en klik op **Opslaan**.

    ![Statische null-route opnieuw distribueren naar BGP](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>Een op route gebaseerd VPN configureren op de NSX-T Tier0-router

Gebruik de volgende sjabloon om alle gegevens in te vullen voor het configureren van een op route gebaseerd VPN op de NSX-T Tier0-router. De UUID in elke POST-aanroep zijn vereist in volgende POST-aanroepen. De IP-adressen voor de loop back en de tunnel interface voor L2VPN moeten uniek zijn en mogen niet overlappen met de on-premises of particuliere Cloud netwerken.

De IP-adressen die u hebt gekozen voor de loop back en de tunnel interface die worden gebruikt voor L2VPN, moeten uniek zijn en mogen niet overlappen met de on-premises of particuliere Cloud netwerken. Het loop back-interface netwerk moet altijd/32 zijn.

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section "Steps to fetch Logical-Router ID needed for L2VPN"
Logical-switch ID(Stretch) : UUID of Stretch Logical Switch obtained earlier
IPSec Service ID :
IKE profile ID :
DPD profile ID :
Tunnel Profile ID :
Local-endpoint ID :
Peer end-point ID :
IPSec VPN session ID (route-based) :
L2VPN service ID :
L2VPN session ID :
Logical-Port ID :
Peer Code :
```

Voor de volgende API-aanroepen vervangt u het IP-adres door het IP-adres van uw NSX-T-beheerder. U kunt al deze API-aanroepen uitvoeren vanaf de POSTman-client of door gebruik te maken van `curl` opdrachten.

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>De IPSec VPN-service inschakelen op de logische router

```
POST   https://192.168.110.201/api/v1/vpn/ipsec/services/
{
"resource_type": "IPSecVPNService",
"description": "Manage VPN service",
"display_name": "IPSec VPN service",
"logical_router_id": "Logical-router ID",
"ike_log_level": "INFO",
"enabled": true
}
```

### <a name="create-profiles-ike"></a>Profielen maken: IKE

```
POST https://192.168.110.201/api/v1/vpn/ipsec/ike-profiles
 
{
"resource_type": "IPSecVPNIKEProfile",
"description": "IKEProfile for siteA",
"display_name": "IKEProfile siteA",
"encryption_algorithms": ["AES_128"],
"ike_version": "IKE_V2",
"digest_algorithms": ["SHA2_256"],
"sa_life_time":21600,
"dh_groups": ["GROUP14"]
}
```

### <a name="create-profiles-dpd"></a>Profielen maken: DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>Profielen maken: tunnel

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/tunnel-profiles
 
{
"resource_type": "IPSecVPNTunnelProfile",
"display_name": "nsx-default-tunnel-profile",
"enable_perfect_forward_secrecy": true,
"encryption_algorithms": ["AES_GCM_128"],
"digest_algorithms": [],
"sa_life_time":3600,
"dh_groups": ["GROUP14"],
"encapsulation_mode": "TUNNEL_MODE",
"transform_protocol": "ESP",
"df_policy": "COPY"
}
```

### <a name="create-a-local-endpoint"></a>Een lokaal eind punt maken

``` 
POST https://192.168.110.201/api/v1/vpn/ipsec/local-endpoints
 
{
"resource_type": "IPSecVPNLocalEndpoint",
"description": "Local endpoint",
"display_name": "Local endpoint",
"local_id": "<Public IP of Loopback interface>",
"ipsec_vpn_service_id": {
"target_id": "IPSec VPN service ID"},
"local_address": "<IP of Loopback interface>",
"trust_ca_ids": [],
"trust_crl_ids": []
}
```

### <a name="create-a-peer-endpoint"></a>Een peer-eind punt maken

```
POST https://192.168.110.201/api/v1/vpn/ipsec/peer-endpoints

{
"resource_type": "IPSecVPNPeerEndpoint",
"description": "Peer endpoint for site B",
"display_name": "Peer endpoint for site B",
"connection_initiation_mode": "INITIATOR",
"authentication_mode": "PSK",
"ipsec_tunnel_profile_id": "IPSec Tunnel profile ID",
"dpd_profile_id": "DPD profile ID",
"psk":"nsx",
"ike_profile_id": "IKE profile ID",
"peer_address": "<Public IP of Standalone client",
"peer_id": "<Public IP of Standalone client>"
}
```

### <a name="create-a-route-based-vpn-session"></a>Een op een route gebaseerde VPN-sessie maken

```
POST :  https://192.168.110.201/api/v1/vpn/ipsec/sessions
 
{
"resource_type": "RouteBasedIPSecVPNSession",
"peer_endpoint_id": "Peer Endpoint ID",
"ipsec_vpn_service_id": "IPSec VPN service ID",
"local_endpoint_id": "Local Endpoint ID",
"enabled": true,
"tunnel_ports": [
{
"ip_subnets": [
{
"ip_addresses": [
 "5.5.5.1"
],
"prefix_length": 24
}
  ]
}
]
}
```

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>L2VPN configureren op NSX-T Tier0-router

Vul de volgende gegevens in na elke POST-oproep. De Id's zijn vereist in volgende POST-aanroepen.

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>De L2VPN-service maken

De uitvoer van de volgende GET-opdracht is leeg, omdat de configuratie nog niet is voltooid.

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

Voor de volgende POST opdracht is de logische router-ID de UUID van de logische Tier0 DR-router die eerder is verkregen.

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>De L2VPN-sessie maken

Voor de volgende POST opdracht is de L2VPN-Service-ID de ID die u zojuist hebt verkregen en is de IPsec VPN-sessie-ID de ID die is verkregen in de vorige sectie.

```    
POST: https://192.168.110.201/api/v1/vpn/l2vpn/sessions

{
"l2vpn_service_id" : "L2VPN service ID",
"transport_tunnels" : [
    {
    "target_id" : "IPSec VPN session ID"
    }]
}
```

Deze aanroepen maken een GRE-tunnel-eind punt. Als u de status wilt controleren, voert u de volgende opdracht uit.

```
edge-2> get tunnel-port
Tunnel      : 44648dae-8566-5bc9-a065-b1c4e5c3e03f
IFUID       : 328
LOCAL       : 169.254.64.1
REMOTE      : 169.254.64.2
ENCAP       : GRE

Tunnel      : cf950ca1-5cf8-5438-9b1a-d2c8c8e7229b
IFUID       : 318
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.152
ENCAP       : GENEVE

Tunnel      : 63639321-87c5-529e-8a61-92c1939799b2
IFUID       : 304
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.156
ENCAP       : GENEVE
```

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>Logische poort maken met de tunnel-ID die is opgegeven

```
    POST https://192.168.110.201/api/v1/logical-ports/

{
"resource_type": "LogicalPort",
"display_name": "Extend logicalSwitch, port for service",
"logical_switch_id": "Logical switch ID",
"admin_state" : "UP",
"attachment": {
"attachment_type":"L2VPN_SESSION",
"id":"L2VPN session ID",
"context" : {
"resource_type" : "L2VpnAttachmentContext",
    "tunnel_id" : 10
}
    }
        }
```

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>De peer code ophalen voor L2VPN op de NSX-T-zijde

Haal de peer code van het NSX-T-eind punt op. De peer code is vereist bij het configureren van het externe eind punt. De L2VPN <sessie-id-> kunnen worden verkregen uit de vorige sectie. Zie de [NSX-T 2,3 API Guide (Engelstalig](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html)) voor meer informatie.

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>De NSX-T-zelfstandige client implementeren (on-premises)

Voordat u implementeert, controleert u of uw on-premises firewall regels binnenkomend en uitgaand UDP 500/4500 verkeer toestaan van/naar het open bare IP-adres van CloudSimple dat eerder is gereserveerd voor de NSX-T T0 router loop back-interface. 

1. [De zelfstandige NSX Edge-client downloaden](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230) OVF de bestanden van de gedownloade bundel in een map en pak deze uit.

    ![Zelfstandige NSX Edge-client downloaden](media/l2vpn-deploy-client01.png)

2. Ga naar de map met alle uitgepakte bestanden. Selecteer alle vmdk's (NSX-l2t-client-large. MF en NSX-l2t-client-large. OVF voor grote toestel grootte of NSX-l2t-client-xlarge. MF en NSX-l2t-client-Xlarge. OVF voor extra groot formaat van het apparaat). Klik op **Volgende**.

    ![Selecteer ](media/l2vpn-deploy-client02.png) ![ de scherm afbeelding van de sjabloon waarin de geselecteerde vmdk's-bestanden worden weer gegeven.](media/l2vpn-deploy-client03.png)

3. Voer een naam in voor de zelfstandige NSX-T-client en klik op **volgende**.

    ![Sjabloon naam invoeren](media/l2vpn-deploy-client04.png)

4. Klik op **volgende** als dat nodig is om de instellingen van het gegevens archief te bereiken. Selecteer het juiste gegevens Archief voor de zelfstandige NSX-T-client en klik op **volgende**.

    ![Gegevens opslag selecteren](media/l2vpn-deploy-client06.png)

5. Selecteer de juiste poort groepen voor trunk (trunk PG), Public (uplink-pag) en HA-interface (uplink-pag) voor de NSX-T zelfstandige client. Klik op **Volgende**.

    ![Poort groepen selecteren](media/l2vpn-deploy-client07.png)

6. Vul de volgende gegevens in het scherm **sjabloon aanpassen** in en klik op **volgende**:

    L2T uitvouwen:

    * **Adres van peer**. Voer het IP-adres in dat is gereserveerd op de Azure CloudSimple-portal voor de NSX-T Tier0 loop back-interface.
    * **Peer code**. Plak de peer code die is verkregen uit de laatste stap van de implementatie van L2VPN-servers.
    * **Subinterfaces VLAN (tunnel-id)**. Voer de VLAN-ID in die moet worden uitgerekt. Voer tussen haakjes () de tunnel-ID in die u eerder hebt geconfigureerd.

    Uplink-interface uitbreiden:

    * **DNS IP-adres**. Voer het on-premises DNS IP-adres in.
    * **Standaard gateway**.  Voer de standaard gateway van het VLAN in dat fungeert als standaard gateway voor deze client.
    * **IP-adres**. Voer het IP-adres van de uplink van de zelfstandige client in.
    * **Lengte van voor voegsel**. Voer de lengte van het voor voegsel van het uplink-VLAN/subnet in.
    * **Cli-beheerder/inschakelen/hoofd gebruikers wachtwoord**. Stel het wacht woord voor admin/Enable/root-account in.

      ![Sjabloon aanpassen sjabloon ](media/l2vpn-deploy-client08.png)
       ![ aanpassen-meer](media/l2vpn-deploy-client09.png)

7. Controleer de instellingen en klik op **volt ooien**.

    ![Configuratie volt ooien](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>Een on-premises Sink-poort configureren

Als op een van de VPN-sites geen NSX is geïmplementeerd, kunt u een L2-VPN configureren door een zelfstandige NSX-rand te implementeren op die site. Een zelfstandige NSX-rand wordt geïmplementeerd met behulp van een OVF-bestand op een host die niet wordt beheerd door NSX. Hiermee implementeert u een NSX Edge Services-gateway apparaat om te fungeren als een L2-VPN-client.

Als een zelfstandige Edge-vNIC is verbonden met een gedistribueerde vSphere-switch, is promiscuous mode of een Sink-poort vereist voor de L2-VPN-functie. Het gebruik van promiscuous mode kan dubbele pings en dubbele reacties veroorzaken. Gebruik voor deze reden de Sink-poort modus in de configuratie van de zelfstandige L2-NSX Edge voor VPN. Zie de [poort een Sink configureren](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html) in de VMware-documentatie.

## <a name="ipsec-vpn-and-l2vpn-verification"></a>IPsec VPN-en L2VPN-verificatie

Gebruik de volgende opdrachten om IPsec-en L2VPN-sessies van zelfstandige NSX-T-rand te controleren.

```
nsx-l2t-edge> show service ipsec
-----------------------------------------------------------------------
vShield Edge IPSec Service Status:
IPSec Server is running.
AESNI is enabled.
Total Sites: 1, 1 UP, 0 Down
Total Tunnels: 1, 1 UP, 0 Down
----------------------------------
Site:  10.250.0.111_0.0.0.0/0-104.40.21.81_0.0.0.0/0
Channel: PeerIp: 104.40.21.81    LocalIP: 10.250.0.111  Version: IKEv2  Status: UP
Tunnel: PeerSubnet: 0.0.0.0/0    LocalSubnet: 0.0.0.0/0   Status: UP
----------------------------------
```

```
nsx-l2t-edge> show service l2vpn
L2 VPN is running
----------------------------------------
L2 VPN type: Client/Spoke

SITENAME                       IPSECSTATUS          VTI                  GRE
1ecb00fb-a538-4740-b788-c9049e8cb6c6 UP                   vti-100              l2t-1
```

Gebruik de volgende opdrachten om IPsec-en L2VPN-sessies van de NSX-T Tier0-router te controleren.

```
edge-2> get ipsecvpn session
Total Number of Sessions: 1

IKE Session ID : 3
UUID           : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Type           : Route

Local IP       : 192.168.254.254      Peer IP        : 192.227.85.167
Local ID       : 104.40.21.81         Peer ID        : 192.227.85.167
Session Status : Up

Policy Rules
    VTI UUID       : 4bf96e3b-e50b-49cc-a16e-43a6390e3d53
    ToRule ID      : 560874406            FromRule ID    : 2708358054
    Local Subnet   : 0.0.0.0/0            Peer Subnet    : 0.0.0.0/0
    Tunnel Status  : Up
```

```
edge-2> get l2vpn session
Session       : f7147137-5dd0-47fe-9e53-fdc2b687b160
Tunnel        : b026095b-98c8-5932-96ff-dda922ffe316
IPSEC Session : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Status        : UP
```

Gebruik de volgende opdrachten om de Sink-poort op de ESXi-host te controleren waar de virtuele machine van de zelfstandige client zich in de on-premises omgeving bevindt.

```
 [root@esxi02:~] esxcfg-vswitch -l |grep NSX
  53                  1           NSXT-client-large.eth2
  225                1           NSXT-client-large.eth1
  52                  1           NSXT-client-large.eth0
```

```
[root@esxi02:~] net-dvs -l | grep "port\ [0-9]\|SINK\|com.vmware.common.alias"
                com.vmware.common.alias = csmlab2DS ,   propType = CONFIG
        port 24:
        port 25:
        port 26:
        port 27:
        port 13:
        port 19:
        port 169:
        port 54:
        port 110:
        port 6:
        port 107:
        port 4:
        port 199:
        port 168:
        port 201:
        port 0:
        port 49:
        port 53:
        port 225:
                com.vmware.etherswitch.port.extraEthFRP =   SINK
        port 52:
```
