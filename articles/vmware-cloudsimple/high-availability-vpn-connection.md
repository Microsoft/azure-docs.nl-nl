---
title: 'Azure VMware-oplossing op CloudSimple: Configureer een hoge Beschik baarheid van on-premises naar CloudSimple VPN-gateway'
description: Hierin wordt beschreven hoe u een Maxi maal beschik bare verbinding van uw on-premises omgeving configureert met een CloudSimple VPN-gateway die is ingeschakeld voor hoge Beschik baarheid
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 80805aaa172518c40c7ad123ca24361ee0f15e69
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895696"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>Een verbinding met hoge Beschik baarheid van on-premises naar CloudSimple VPN-gateway configureren

Netwerk beheerders kunnen een VPN-verbinding tussen sites van Maxi maal Beschik baarheid configureren van de on-premises omgeving naar een CloudSimple VPN-gateway.

Deze hand leiding bevat stappen voor het configureren van een on-premises Firewall voor een VPN-verbinding met hoge Beschik baarheid van IPsec-site-naar-site. De gedetailleerde stappen zijn specifiek voor het type on-premises firewall. Als voor beeld bevat deze hand leiding stappen voor twee typen firewalls: Cisco ASA en Palo Alto Networks.

## <a name="before-you-begin"></a>Voordat u begint

Voer de volgende taken uit voordat u de on-premises firewall configureert.

1. Controleer of uw organisatie de vereiste knoop punten heeft [ingericht](create-nodes.md) en ten minste één CloudSimple-privécloud heeft gemaakt.
2. [Configureer een site-naar-site-VPN-gateway](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) tussen uw on-premises netwerk en uw CloudSimple-privécloud.

Zie [overzicht van VPN-gateways](cloudsimple-vpn-gateways.md) voor ondersteunde fase 1 en fase 2-Voorst Ellen.

## <a name="configure-on-premises-cisco-asa-firewall"></a>On-premises Cisco ASA-Firewall configureren

De instructies in deze sectie zijn van toepassing op Cisco ASA versie 8,4 en hoger. In het configuratie voorbeeld wordt Cisco Adaptive Security-toestel software versie 9,10 geïmplementeerd en geconfigureerd in de modus IKEv1.

Voor een goede werking van site-naar-site-VPN moet u UDP 500/4500 en ESP (IP-protocol 50) van de CloudSimple Primary en secundair open bare IP (peer-IP) toestaan op de buitenste interface van de on-premises Cisco ASA VPN-gateway.

### <a name="1-configure-phase-1-ikev1"></a>1. fase 1 (IKEv1) configureren

Als u phase 1 (IKEv1) wilt inschakelen voor de buiten-interface, voert u de volgende CLI-opdracht in de Cisco ASA-Firewall in.

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. een IKEv1-beleid maken

Maak een IKEv1-beleid waarmee de algoritmen en methoden worden gedefinieerd die moeten worden gebruikt voor hashing, authenticatie, Diffie-Hellman groep, levens duur en versleuteling.

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. een tunnel groep maken

Maak een tunnel groep onder de IPsec-kenmerken. Configureer het peer-IP-adres en de vooraf gedeelde tunnel sleutel, die u instelt bij [het configureren van de site-naar-site VPN-gateway](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. fase 2 (IPsec) configureren

Als u fase 2 (IPsec) wilt configureren, maakt u een toegangs beheer lijst (ACL) waarmee het verkeer dat moet worden versleuteld en getunneld, wordt gedefinieerd. In het volgende voor beeld is het verkeer van belang van de tunnel die is gebrond vanuit het on-premises lokale subnet (10.16.1.0/24) naar het externe subnet van de Privécloud (192.168.0.0/24). De ACL kan meerdere vermeldingen bevatten als er meerdere subnetten tussen de sites zijn.

In Cisco ASA-versies 8,4 en hoger kunnen objecten of object groepen worden gemaakt die fungeren als containers voor de netwerken, subnetten, IP-adressen van hosts of meerdere objecten. Maak een object voor het lokale en een object voor de externe subnetten en gebruik het voor de crypto-ACL en de NAT-instructies.

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>Een on-premises lokaal subnet definiëren als een object

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>Het CloudSimple-externe subnet definiëren als een object

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>Een toegangs lijst configureren voor het gewenste verkeer

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. de transformatie set configureren

Configureer de transformatie set (TS), die het sleutel woord moet omvatten ```ikev1``` . De versleutelings-en hash-kenmerken die zijn opgegeven in de TS moeten overeenkomen met de para meters die worden vermeld in de [standaard configuratie voor CloudSimple VPN-gateways](cloudsimple-vpn-gateways.md).

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. Configureer de crypto grafie-map

Configureer de crypto grafie-map die deze onderdelen bevat:

* IP-adres van peer
* Gedefinieerde ACL die het gewenste verkeer bevat
* Transformatie set

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. de cryptografie toewijzing Toep assen

De crypto grafie-map Toep assen op de buiten-Interface:

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. toepasselijke NAT-regels bevestigen

Hier volgt de NAT-regel die wordt gebruikt. Zorg ervoor dat het VPN-verkeer niet wordt onderhevig aan een andere NAT-regel.

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Voor beeld van een IPsec-site-naar-site-VPN-uitvoer van Cisco ASA

Uitvoer fase 1:

![Uitvoer fase 1 voor Cisco ASA-Firewall](media/ha-vpn-connection-cisco-phase1.png)

Uitvoer fase 2:

![Uitvoer fase 2 voor Cisco ASA-Firewall](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>On-premises Palo Alto Network Firewall configureren

De instructies in deze sectie zijn van toepassing op Palo Alto Networks versie 7,1 en hoger. In dit configuratie voorbeeld worden Palo Alto-netwerken VM-Series software versie 8.1.0 geïmplementeerd en geconfigureerd in de modus IKEv1.

Voor een goede werking van het site-naar-site-VPN moet u UDP 500/4500 en ESP (IP-protocol 50) van de CloudSimple Primary en secundair open bare IP (peer-IP) toestaan op de buitenste interface van de on-premises Palo Alto Networks gateway.

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. primaire en secundaire tunnel interfaces maken

Meld u aan bij de Palo Alto firewall, selecteer **netwerk**  >  **interfaces**  >  **tunnel**  >  **toevoegen**, Configureer de volgende velden en klik op **OK**.

* Interface naam. Het eerste veld wordt automatisch ingevuld met het sleutel woord tunnel. Voer in het veld aangrenzend een getal in tussen 1 en 9999. Deze interface wordt gebruikt als een primaire tunnel interface om site-naar-site-verkeer tussen het on-premises Data Center en de Privécloud uit te voeren.
* Heffen. Voer opmerkingen in om het doel van de tunnel eenvoudig te identificeren
* Netstroom profiel. Gebruik de standaardinstelling.
* Configuraties. Interface toewijzen aan: virtuele router: Selecteer **standaard**. 
        Beveiligings zone: Selecteer de zone voor vertrouwd LAN-verkeer. In dit voor beeld is de naam van de zone voor LAN-verkeer ' Trust '.
* IPv6. Klik op **toevoegen** en voeg een niet-overlappend of 32 IP-adres toe in uw omgeving, die wordt toegewezen aan de primaire tunnel interface en wordt gebruikt voor het bewaken van de tunnels (later uitgelegd).

Omdat deze configuratie voor een VPN met hoge Beschik baarheid is, zijn twee tunnel interfaces vereist: één primaire en een secundaire. Herhaal de vorige stappen om de secundaire tunnel interface te maken. Selecteer een andere tunnel-ID en een ander ongebruikt/32 IP-adres.

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. Stel statische routes in voor de subnetten van de Privécloud die moeten worden bereikt via de site-naar-site-VPN

Routes zijn nodig om de on-premises subnetten CloudSimple Private Cloud-subnetten te bereiken.

Virtuele **netwerk**  >  **routers** selecteren  >  *standaard*  >  **statische routes**  >  **toevoegen**, configureren van de volgende velden en klik op **OK**.

* Naam. Voer een naam in voor een eenvoudige identificatie van het doel van de route.
* Beoogde. Geef de CloudSimple particuliere cloud subnetten op die via S2S-tunnel interfaces van on-premises moeten worden bereikt
* Interface. Selecteer in de vervolg keuzelijst de primaire tunnel interface die u hebt gemaakt in stap-1 (sectie-2). In dit voor beeld is tunnel. 20.
* Volgende hop. Selecteer **Geen**.
* Beheer afstand. Gebruik de standaardinstelling.
* Gemeten. Voer een waarde tussen 1 en 65535 in. De sleutel is om een lagere metrische waarde in te voeren voor de route die overeenkomt met de primaire tunnel interface, vergeleken met de route die overeenkomt met de secundaire tunnel interface die de vorige route de voor keur heeft. Als tunnel. 20 de metrische waarde 20 heeft in plaats van de metrische waarde 30 voor tunnel. 30, tunnel. 20 verdient de voor keur.
* Route tabel. Gebruik de standaardinstelling.
* BFD-profiel. Gebruik de standaardinstelling.
* Pad bewaken. Schakel dit selectievakje niet in.

Herhaal de vorige stappen om een andere route te maken voor particuliere cloud-subnetten om te gebruiken als een secundaire/back-uproute via een secundaire tunnel interface. Selecteer deze keer een andere tunnel-ID en een hogere metrische waarde dan voor de primaire route.

### <a name="3-define-the-cryptographic-profile"></a>3. het cryptografische profiel definiëren

Definieer een cryptografisch profiel waarmee de protocollen en algoritmen voor identificatie, authenticatie en versleuteling worden opgegeven die moeten worden gebruikt voor het instellen van VPN-tunnels in IKEv1-fase 1.

Selecteer **netwerk**  >  **uitbreiden netwerk profielen**  >  **IKE crypto**  >  **toevoegen**, Configureer de volgende velden en klik op **OK**.

* Naam. Voer een naam in voor het IKE-crypto profiel.
* DH-groep. Klik op **toevoegen** en selecteer de juiste DH-groep.
* Versleuteling. Klik op **toevoegen** en selecteer de juiste versleutelings methode.
* Verificatie. Klik op **toevoegen** en selecteer de juiste verificatie methode.
* Levens duur van de sleutel. Gebruik de standaardinstelling.
* Meerdere IKEv2-verificaties. Gebruik de standaardinstelling.

### <a name="4-define-ike-gateways"></a>4. IKE-gateways definiëren

Geef IKE-gateways op om communicatie tot stand te brengen tussen de peers aan elk einde van de VPN-tunnel.

Selecteer **netwerk**  >  **uitvouwen netwerk profielen**  >  **IKE-gateways**  >  **toevoegen**, Configureer de volgende velden en klik op **OK**.

Tabblad Algemeen:

* Naam. Voer de naam in voor de IKE-gateway die moet worden gekoppeld aan de primaire CloudSimple VPN-peer.
* Versie. Selecteer de **modus alleen IKEv1**.
* Adres type. Selecteer **IPv4**.
* Interface. Selecteer de open bare interface voor Facing of buiten.
* Lokaal IP-adres. Gebruik de standaardinstelling.
* IP-adres type van peer. Selecteer **IP**.
* Adres van peer. Voer het primaire IP-adres van CloudSimple VPN peer in.
* Verificatie. Selecteer een **vooraf gedeelde sleutel**.
* Vooraf gedeelde sleutel/Bevestig de vooraf gedeelde sleutel. Voer de vooraf gedeelde sleutel in die overeenkomt met de CloudSimple VPN-gateway sleutel.
* Lokale identificatie. Voer het open bare IP-adres van de on-premises Palo Alto firewall in.
* Peer-identificatie. Voer het primaire IP-adres van CloudSimple VPN peer in.

Tabblad Geavanceerde opties:

* Schakel de passieve modus in. Schakel dit selectievakje niet in.
* Schakel NAT-passage in. Als de on-premises Palo Alto-firewall zich niet achter een NAT-apparaat bevindt, schakelt u dit selectie vakje uit. Schakel anders het selectie vakje in.

IKEv1

* Exchange-modus. Selecteer **hoofd**.
* IKE-crypto profiel. Selecteer het IKE-crypto profiel dat u eerder hebt gemaakt. Schakel het selectie vakje fragmentatie inschakelen uit.
* Detectie van onbestelbare peers. Schakel het selectie vakje uit.

Herhaal de vorige stappen om de secundaire IKE-gateway te maken.

### <a name="5-define-ipsec-crypto-profiles"></a>5. Definieer IPSEC-crypto profielen

Selecteer **netwerk** netwerk  >  **profielen uitvouwen**  >  **IPSec crypto**  >  **toevoegen**, Configureer de volgende velden en klik op **OK**.

* Naam. Voer een naam in voor het IPsec-crypto profiel.
* IPsec-protocol. Selecteer **ESP**.
* Versleuteling. Klik op **toevoegen** en selecteer de juiste versleutelings methode.
* Verificatie. Klik op **toevoegen** en selecteer de juiste verificatie methode.
* DH-groep. Selecteer **geen PFS**.
* Levensduur. Stel in op 30 minuten.
* Inschakelen. Schakel het selectie vakje uit.

Herhaal de vorige stappen om een ander IPsec-crypto profiel te maken, dat wordt gebruikt als de secundaire CloudSimple VPN-peer. Hetzelfde IPSEC-crypto profiel kan ook worden gebruikt voor de primaire en secundaire IPsec-tunnels (Zie de volgende procedure).

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. Monitor profielen definiëren voor tunnel bewaking

Selecteer **netwerk**  >  **uitbreiden netwerk profielen**  >  **monitor**  >  **toevoegen**, Configureer de volgende velden en klik op **OK**.

* Naam. Voer een naam in van het monitor profiel dat moet worden gebruikt voor het controleren van de tunnel voor proactieve reactie op de fout.
* Optreden. Selecteer **failover**.
* Bereik. Voer de waarde **3** in.
* Spreek. Voer de waarde **7** in.

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. primaire en secundaire IPsec-tunnels instellen.

Selecteer **netwerk**  >  **-IPSec-tunnels**  >  **toevoegen**, Configureer de volgende velden en klik op **OK**.

Tabblad Algemeen:

* Naam. Voer een naam in voor de primaire IPSEC-tunnel die moet worden gekoppeld aan de primaire CloudSimple VPN-peer.
* Tunnel interface. Selecteer de primaire tunnel interface.
* Voert. Gebruik de standaardinstelling.
* Adres type. Selecteer **IPv4**.
* IKE-gateway. Selecteer de primaire IKE-gateway.
* IPsec-crypto profiel. Selecteer het primaire IPsec-profiel. Selecteer **Geavanceerde opties weer geven**.
* Replay-beveiliging inschakelen. Gebruik de standaardinstelling.
* TOS-header kopiëren. Schakel het selectie vakje uit.
* Tunnel monitor. Schakel het selectie vakje in.
* Doel-IP. Voer een IP-adres in dat deel uitmaakt van het CloudSimple Private Cloud-subnet dat is toegestaan via de site-naar-site-verbinding. Zorg ervoor dat de tunnel interfaces (zoals tunnel. 20-10.64.5.2/32 en tunnel. 30-10.64.6.2/32) op Palo Alto het IP-adres van de CloudSimple Privécloud kunnen bereiken via de site-naar-site-VPN. Zie de volgende configuratie voor proxy-Id's.
* Uplinkpoortprofiel. Selecteer het monitor profiel.

Tabblad Proxy-id's: Klik op **IPv4**  >  **toevoegen** en configureer het volgende:

* Proxy-ID. Voer een naam in voor het interessante verkeer. Er kunnen meerdere proxy-Id's binnen één IPsec-tunnel worden uitgevoerd.
* Lokale. Geef de on-premises lokale subnetten op die via de site-naar-site-VPN mogen communiceren met Privécloud-subnetten.
* Beveiligingslek. Geef de externe subnetten voor de Privécloud op die mogen communiceren met de lokale subnetten.
* Protocolsubstatus. Selecteer **een**.

Herhaal de vorige stappen om nog een IPsec-tunnel te maken die u voor de secundaire CloudSimple VPN-peer kunt gebruiken.

## <a name="references"></a>Verwijzingen

NAT configureren op Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Cisco ASA 5500 Series-configuratie handleiding</a>

Ondersteunde IKEv1-en IKEv2-kenmerken op Cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">Cisco ASA-serie CLI-configuratie handleiding</a>

IPsec-site-naar-site-VPN configureren op Cisco ASA met versie 8,4 en hoger:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">De site-naar-site tunnels van IKEv1 IPsec configureren met de ASDM of CLI op de ASA</a>

Cisco Adaptive Security Appliance Virtual (ASAv) configureren op Azure:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">Snelstartgids voor adaptieve beveiliging van Cisco (ASAv)</a>

Site-naar-site-VPN configureren met proxy-Id's op Palo Alto:

[Site-naar-site-VPN instellen](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

Tunnel monitor instellen:

[Tunnel controle instellen](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

IKE-gateway-of IPsec-tunnel bewerkingen:

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">Een IKE-gateway of IPsec-tunnel inschakelen, uitschakelen, vernieuwen of opnieuw starten</a>
