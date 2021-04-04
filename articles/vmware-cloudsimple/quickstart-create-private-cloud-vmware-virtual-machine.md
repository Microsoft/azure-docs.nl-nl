---
title: 'Quick Start: een Azure VMware-VM maken op een Privécloud-Azure VMware-oplossing door CloudSimple'
description: Meer informatie over het maken van een virtuele VMware-machine op CloudSimple-Privécloud. Open de CloudSimple-Portal vanuit het Azure Portal.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c741f01ccc3e34ec991aa77fc850fd9f60c4d8aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899283"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Virtuele VMware-machines maken in uw Privécloud

Als u virtuele machines in uw Privécloud wilt maken, moet u beginnen met het openen van de CloudSimple-Portal vanuit de Azure Portal.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Toegang tot de CloudSimple-portal

1. Selecteer **Alle services**.
2. Zoek naar **CloudSimple Services**.
3. Selecteer de CloudSimple-service waarop u uw Privécloud wilt maken.
4. Klik op de pagina **overzicht** op **Ga naar de CloudSimple-Portal** om een nieuw browser tabblad voor CloudSimple-portal te openen.  Meld u aan met uw aanmeldings referenties voor Azure als u hierom wordt gevraagd.  

    ![CloudSimple-Portal starten](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>VCenter-Web-UI starten

U kunt nu vCenter starten om virtuele machines en beleids regels in te stellen.

Als u toegang wilt krijgen tot vCenter, start u vanuit de CloudSimple-Portal. Klik op de start pagina onder **algemene taken** op **VSphere-client starten**.  Selecteer de privécloud en klik vervolgens op **vSphere-client starten** op de privécloud.

   ![VSphere-client starten](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Een ISO-of vSphere-sjabloon uploaden

  > [!WARNING]
  > Voor de ISO-upload gebruikt u de vSphere HTML5-client.  Het gebruik van een Flash-client kan een fout veroorzaken.

1. Verkrijg de ISO-of vSphere-sjabloon die u wilt uploaden naar vCenter om een virtuele machine te maken en deze beschikbaar te stellen op uw lokale systeem.
2. Klik in vCenter op het pictogram **schijf** en selecteer **vsanDatastore**. Klik op **bestanden** en klik vervolgens op **nieuwe map**.
    ![vCenter ISO](media/vciso00.png)

3. Maak een map met de titel ' Iso's en templates '.

4. Ga naar de map Iso's in Iso's en sjablonen en klik op **bestanden uploaden**. Volg de instructies op het scherm om de ISO te uploaden.

## <a name="create-a-virtual-machine-in-vcenter"></a>Een virtuele machine maken in vCenter

1. Klik in vCenter op het pictogram **hosts en clusters** .

2. Klik met de rechter muisknop op **workload** en selecteer **nieuwe virtuele machine**.
    ![Scherm opname van de menu optie voor de nieuwe virtuele machine.](media/vcvm01.png)

3. Selecteer **nieuwe virtuele machine maken** en klik op **volgende**.
    ![Scherm afbeelding die de optie nieuwe virtuele machine maken markeert.](media/vcvm02.png)

4. Geef de machine een naam, selecteer de locatie **van de VM van de werk belasting** en klik op **volgende**.
    ![Scherm opname van de optie werk belasting Vm's.](media/vcvm03.png)

5. Selecteer de **werk belasting** Compute resource en klik op **volgende**.
    ![Scherm afbeelding die de reken resource van de werk belasting markeert.](media/vcvm04.png)

6. Selecteer **vsanDatastore** en klik op **volgende**.
    ![Scherm afbeelding die de vsanDatastore-optie markeert.](media/vcvm05.png)

7. Behoud de standaard selectie van de ESXi 6,5-compatibiliteit en klik op **volgende**.
    ![Scherm afbeelding met de geselecteerde compatibiliteits optie voor ESXi 6,5.](media/vcvm06.png)

8. Selecteer het gast besturingssysteem van de ISO voor de virtuele machine die u maakt en klik op **volgende**.
    ![Scherm afbeelding die laat zien hoe u het guese-besturings systeem van de ISO selecteert voor de virtuele machine.](media/vcvm07.png)

9. Selecteer de opties voor harde schijf en netwerk. Selecteer **ISO-bestand Data Store** voor nieuwe cd/dvd-stations.  Als u verkeer van het open bare IP-adres naar deze VM wilt toestaan, selecteert u het netwerk als **VM-1**.
    ![Scherm opname van de locatie waar u het ISO-bestand Data Store selecteert.](media/vcvm08.png)

10. Er wordt een selectie venster geopend. Selecteer het bestand dat u eerder hebt geüpload naar de map Iso's en templates en klik op **OK**.
    ![Nieuwe VM](media/vcvm10.png)

11. Controleer de instellingen en klik op **OK** om de virtuele machine te maken.
    ![Scherm afbeelding die laat zien waar u de instellingen bekijkt.](media/vcvm11.png)

De VM wordt nu toegevoegd aan de werk belasting reken resources en is klaar voor gebruik. 
![Scherm opname van de virtuele machine die is toegevoegd aan de werk belasting reken resources.](media/vcvm12.png)

De basis installatie is nu voltooid. U kunt uw Privécloud op dezelfde manier gebruiken als uw on-premises VM-infra structuur.

De volgende secties bevatten optionele informatie over het instellen van DNS-en DHCP-servers voor de werk belasting van de privécloud en het wijzigen van de standaard netwerk configuratie.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Gebruikers en identiteits bronnen aan vCenter toevoegen (optioneel)

CloudSimple wijst een standaard-vCenter-gebruikers account toe met een gebruikers naam `cloudowner@cloudsimple.local` . U hoeft geen extra account in te stellen om aan de slag te gaan.  CloudSimple wijst beheerders normaal gesp roken de bevoegdheden toe die ze nodig hebben om normale bewerkingen uit te voeren.  Stel uw on-premises Active Directory of Azure AD in als een [extra id-bron](set-vcenter-identity.md) in uw privécloud.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Een DNS-en DHCP-server maken (optioneel)

Toepassingen en werk belastingen die worden uitgevoerd in een Privécloud-omgeving, moeten naam omzetting en DHCP-services voor lookup-en IP-adres toewijzing hebben. U hebt de juiste DHCP- en DNS-infrastructuur nodig om deze services te kunnen leveren. U kunt een virtuele machine in vCenter configureren om deze services te leveren in uw Privécloud.

Vereisten

* Een gedistribueerde poort groep waarvoor VLAN is geconfigureerd

* De installatie naar on-premises of op internet gebaseerde DNS-servers routeren

* Virtuele-machine sjabloon of ISO voor het maken van een virtuele machine

De volgende koppelingen bieden richt lijnen voor het instellen van DHCP-en DNS-servers in Linux en Windows.

#### <a name="linux-based-dns-server-setup"></a>Installatie van DNS-server op basis van Linux

Linux biedt verschillende pakketten voor het instellen van DNS-servers.  Hier volgt een koppeling naar instructies voor het instellen van een open-source BIND-DNS-server.

[Voor beeld van installatie](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Installatie op basis van Windows

In deze micro soft-onderwerpen wordt beschreven hoe u een Windows-Server als een DNS-server en als een DHCP-server instelt.

[Windows Server als DNS-server](/windows-server/networking/dns/dns-top)

[Windows Server als DHCP-server](/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Netwerk configuratie aanpassen (optioneel)

Met de netwerk pagina's in de CloudSimple-Portal kunt u de configuratie voor Firewall tabellen en open bare IP-adressen voor Vm's opgeven.

### <a name="allocate-public-ips"></a>Open bare IP-adressen toewijzen

1. Navigeer naar **netwerk > open bare IP** in de CloudSimple-Portal.
2. Klik op **openbaar IP-adres toewijzen**.
3. Voer een naam in om de IP-adres vermelding te identificeren.
4. Behoud de standaard locatie.
5. Gebruik de schuif regelaar om de time-out voor inactiviteit indien gewenst te wijzigen.
6. Voer het lokale IP-adres in waarvoor u een openbaar IP-adres wilt toewijzen.
7. Voer desgewenst een bijbehorende DNS-naam in.
8. Klik op **Gereed**.

    ![Openbare IP](media/quick-create-pc-public-ip.png)

De taak voor het toewijzen van het open bare IP-adres begint. U kunt de status van de taak controleren op de pagina **activiteit > taken** . Wanneer de toewijzing is voltooid, wordt de nieuwe vermelding op de open bare IPs-pagina weer gegeven.

De virtuele machine waaraan dit IP-adres moet worden toegewezen, moet worden geconfigureerd met het lokale adres dat hierboven is opgegeven. De procedure voor het configureren van een IP-adres is specifiek voor het besturings systeem van de virtuele machine. Raadpleeg de documentatie voor uw VM-besturings systeem voor de juiste procedure.

#### <a name="example"></a>Voorbeeld

Dit zijn bijvoorbeeld de Details voor Ubuntu 16,04.

Voeg de statische methode toe aan de configuratie van de inet-adres familie in het bestand/etc/network/interfaces. Wijzig de waarden voor adres, netmasker en gateway. Voor dit voor beeld gebruiken we de eth0-interface, het interne IP-adres 192.168.24.10, het gateway adres 192.168.24.1 en netmask 255.255.255.0. De beschik bare subnetgegevens voor uw omgeving vindt u in de welkomst-e-mail.

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
Schakel de interface hand matig uit.

```
sudo ifdown eth0
```
Schakel de interface hand matig opnieuw in.

```
sudo ifup eth0
```

Standaard wordt al het binnenkomende verkeer van Internet **geweigerd**. Als u een andere poort wilt openen, moet u een [firewall tabel](firewall.md)maken.

Nadat u een intern IP-adres hebt geconfigureerd als het statische IP-adres, controleert u of u het internet kunt bereiken vanuit de VM.

```
ping 8.8.8.8
```
Controleer ook of u de virtuele machine via internet kunt bereiken met behulp van het open bare IP-adres.

Zorg ervoor dat de iptable-regels op de virtuele machine geen poort 80 inkomend blokkeert.
        
```
netstat -an | grep 80
```

Start een http-server die luistert op poort 80.
       
```
python2.7 -m SimpleHTTPServer 80
```

of

```
python3 -m http.server 80
```
Start een browser op uw bureau blad en ga naar poort 80 voor het open bare IP-adres om door de bestanden op uw virtuele machine te bladeren.

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Standaard CloudSimple-firewall regels voor openbaar IP-adres

* VPN-verkeer: al het verkeer tussen (van/naar) de VPN-en alle werkbelasting netwerken en het beheer netwerk is toegestaan.
* Intern verkeer van de privécloud: alle Oost-West-verkeer tussen (van/naar) werkbelasting netwerken en het beheer netwerk (zie hierboven) is toegestaan.
* Internet verkeer:
  * Al het binnenkomende verkeer van Internet wordt geweigerd aan workload netwerken en het beheer netwerk.
  * Al het uitgaande verkeer naar het Internet vanuit workload netwerken of het beheer netwerk is toegestaan.

U kunt ook de manier wijzigen waarop het verkeer wordt beveiligd, met de functie Firewall regels. Zie [firewall tabellen en-regels instellen](firewall.md)voor meer informatie.

## <a name="install-solutions-optional"></a>Oplossingen installeren (optioneel)

U kunt oplossingen op uw CloudSimple-Privécloud installeren om optimaal gebruik te kunnen maken van uw persoonlijke Cloud vCenter-omgeving. U kunt back-ups, herstel na nood gevallen, replicatie en andere functies instellen om uw virtuele machines te beveiligen. Voor beelden hiervan zijn VMware Site Recovery Manager (VMware-Veeam) en back-up& replicatie.

Als u een oplossing wilt installeren, moet u voor een beperkte periode extra bevoegdheden aanvragen. Zie [bevoegdheden escaleren](escalate-private-cloud-privileges.md).

## <a name="next-steps"></a>Volgende stappen

* [VMware-VM's in Azure gebruiken](quickstart-create-vmware-virtual-machine.md)
* [Verbinding maken met een on-premises netwerk met behulp van Azure ExpressRoute](on-premises-connection.md)
* [VPN-gateways instellen op het CloudSimple-netwerk](vpn-gateway.md)
