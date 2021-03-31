---
title: Infrastructuur maken voor een cluster op Azure-VM's
description: In deze zelfstudie leert u hoe u de Azure-VM-infrastructuur instelt voor het uitvoeren van een Service Fabric-cluster.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: e9f62f944fff331bcf2dad1b380161e563614219
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90561837"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Zelfstudie: Azure-VM-infrastructuur maken voor het hosten van een Service Fabric-cluster

Zelfstandige Service Fabric-clusters bieden u de mogelijkheid om uw eigen omgeving te kiezen en een cluster te maken als onderdeel van de benadering "Elk besturingssysteem, elke cloud" die we in Service Fabric hanteren. In deze reeks zelfstudies maakt u een zelfstandig cluster dat in Azure VM's wordt gehost en installeert u vervolgens een toepassing in het cluster.

Deze zelfstudie is deel één van een serie. In dit artikel genereert u de Azure-VM-resources die vereist zijn voor het hosten van uw zelfstandige cluster van Service Fabric. In andere artikelen uit deze serie gaat u een zelfstandig Service Fabric-cluster installeren, een voorbeeldtoepassing in het cluster installeren en ten slotte het cluster opschonen.

In deel 1 van de reeks leert u het volgende:

> [!div class="checklist"]
> * Een set van Azure VM-exemplaren maken
> * De beveiligingsgroep wijzigen
> * Aanmelden bij een van de exemplaren
> * Het exemplaar voorbereiden voor Service Fabric

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig om deze zelfstudie te voltooien.  Als u nog geen account hebt, gaat u naar [Azure Portal](https://portal.azure.com) om er een te maken.

## <a name="create-azure-virtual-machine-instances"></a>Azure Virtual Machines-exemplaren maken

1. Meld u aan bij Azure Portal en selecteer **Virtual Machines** (niet Virtual Machines (klassiek)).

   ![Azure Portal-VM][az-console]

2. Selecteer de knop **Toevoegen** om het formulier **Een virtuele machine maken** te openen.

3. Zorg ervoor dat u het gewenste abonnement en de gewenste resourcegroep kiest (het gebruik van een nieuwe resource groep wordt aanbevolen) op het tabblad **Algemeen**.

4. Wijzig het type **installatiekopie** in **Windows Server 2016 Datacenter**. 
 
5. Wijzig de **grootte** van het exemplaar in **Standard DS2 v2**. Stel een administrator-**gebruikersnaam** en -**wachtwoord** in, en noteer deze.

6. Laat **Regels voor binnenkomende poort** voorlopig nog staan op geblokkeerd. Deze worden geconfigureerd in de volgende sectie.

7. Maak op het tabblad **Netwerken** een nieuw **virtueel netwerk** en noteer de naam ervan.

8. Stel vervolgens de **NIC-netwerkbeveiligingsgroep** in op **Geavanceerd**. Maak een nieuwe beveiligingsgroep, noteer de naam ervan en maak de volgende regels om TCP-verkeer van elke bron toe te staan:

   ![Schermopname met het maken van regels om inkomend TCP-verkeer toe te staan.][sf-inbound]

   * Poort `3389`, voor RDP en ICMP (basisconnectiviteit).
   * Poort `19000-19003`, voor Service Fabric.
   * Poort `19080-19081`, voor Service Fabric.
   * Poort `8080`, voor aanvragen van webbrowsers.

   > [!TIP]
   > Om uw virtuele machines te verbinden in Service Fabric, moeten de VM's die uw infrastructuur hosten dezelfde referenties hebben.  Er zijn twee manieren om consistente referenties te hanteren: alle machines toevoegen aan hetzelfde domein of op elke VM hetzelfde beheerderswachtwoord instellen. Gelukkig kunnen alle virtuele machines op hetzelfde **virtuele netwerk** in Azure eenvoudig verbinding met elkaar maken, dus we zorgen ervoor dat al onze exemplaren zich in hetzelfde netwerk bevinden.

9. Voeg nog een regel toe. Stel de bron in op **Servicetag** en stel de bronservicetag in op **VirtualNetwork**. Voor Service Fabric moeten de volgende poorten zijn geopend voor communicatie binnen het cluster: 135, 137-139, 445, 20001-20031 en 20606-20861.

   ![Schermopname met het maken van regels om TCP-verkeer toe te staan voor een cluster.][vnet-inbound]

10. Voor de rest van de opties kunt u de standaardinstelling accepteren. Controleer deze, indien gewenst, en start vervolgens de virtuele machine.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Meer exemplaren maken voor uw Service Fabric-cluster

Start nog twee **virtuele machines** en zorg ervoor dat u dezelfde instellingen gebruikt als in de vorige sectie. Het is met name belangrijk dat u dezelfde administrator-gebruikersnaam en hetzelfde wachtwoord gebruikt. Het **virtuele netwerk** en de **NIC-netwerkbeveiligingsgroep** mogen niet opnieuw worden gemaakt. Selecteer het netwerk en de groep die u al hebt gemaakt in de vervolgkeuzelijst. Het kan enkele minuten duren voordat elk exemplaar is geïmplementeerd.

## <a name="connect-to-your-instances"></a>Verbinding maken met uw exemplaren

1. Selecteer een van uw exemplaren in de sectie **Virtuele machine**.

2. Op het tabblad **Overzicht** noteert u het *privé*-IP-adres. Klik vervolgens op **Verbinden**.

3. Op het tabblad **RDP** ziet u dat we het openbare IP-adres en de poort 3389 gebruiken, die u eerder specifiek hebt opengezet. Download het RDP-bestand.
 
4. Open het RDP-bestand en voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven tijdens de installatie van de virtuele machine.

5. Nadat u verbinding hebt gemaakt met een exemplaar, moet u valideren of het externe register actief was, SMB inschakelen en de vereiste poorten openen voor SMB en het externe register.

   Dit is de PowerShell-opdracht voor het inschakelen van SMB:

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. Dit is de PowerShell-opdracht voor het openen van de poorten in de firewall:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. Herhaal dit proces voor uw andere exemplaren en noteer ook de privé-IP-adressen daarvan.

## <a name="verify-your-settings"></a>Uw instellingen verifiëren

1. Als u de basisconnectiviteit wilt valideren, maakt u verbinding met een van de virtuele machines met behulp van RDP.

2. Open de **opdrachtprompt** vanuit die VM en gebruik vervolgens de ping-opdracht om verbinding te maken tussen de ene VM en de andere, waarbij u het onderstaande IP-adres vervangt door een van de privé-IP-adressen die u eerder hebt genoteerd (niet het IP-adres van de VM waarmee u al bent verbonden).

   ```
   ping 172.31.20.163
   ```

   Als de uitvoer er gedurende vier pogingen uitziet zoals `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128`, werkt de verbinding tussen de exemplaren.

3. Gebruik nu de volgende opdracht om te controleren of delen via SMB werkt:

   ```
   net use * \\172.31.20.163\c$
   ```

   De uitvoer moet gelijk zijn aan `Drive Z: is now connected to \\172.31.20.163\c$.`.


   Uw exemplaren zijn nu correct voorbereid voor Service Fabric.

## <a name="next-steps"></a>Volgende stappen

In deel één van de reeks hebt u geleerd hoe u drie Azure-VM-exemplaren start en deze configureert voor de installatie van Service Fabric:

> [!div class="checklist"]
> * Een set van Azure VM-exemplaren maken
> * De beveiligingsgroep wijzigen
> * Aanmelden bij een van de exemplaren
> * Het exemplaar voorbereiden voor Service Fabric

Ga naar deel twee van de reeks om Service Fabric te configureren in uw cluster.

> [!div class="nextstepaction"]
> [Service Fabric installeren](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[az-console]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/az-console.png
[sf-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/sf-inbound.png
[vnet-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/vnet-inbound.png
