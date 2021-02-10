---
title: Geneste virtualisatie inschakelen in azure Virtual Machines
description: Geneste virtualisatie inschakelen in azure Virtual Machines
author: cynthn
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: how-to
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.openlocfilehash: e85ac58c80e1fd695938bf09b6435dba1f4ee083
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100091343"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Geneste virtualisatie inschakelen in een Azure VM

Geneste virtualisatie wordt ondersteund in verschillende Azure virtual machine-families. Deze mogelijkheid biedt grote flexibiliteit bij het ondersteunen van scenario's zoals ontwikkeling, testen, training en demonstratie omgevingen.   

In dit artikel wordt beschreven hoe u Hyper-V op een virtuele Azure-machine inschakelt en hoe u de Internet verbinding met die gast-VM configureert.

## <a name="create-a-nesting-capable-azure-vm"></a>Een voor nesting geschikte Azure-VM maken

Maak een nieuwe Windows Server 2016 Azure-VM. Bekijk het [artikel Azure Compute unit](../acu.md)voor een volledige lijst met grootten voor virtuele machines die ondersteuning bieden voor nesten.

Vergeet niet om een VM-grootte te kiezen die groot genoeg is om de vereisten van een virtuele gast machine te ondersteunen. In dit voor beeld gebruiken we een Azure-VM met een grootte van D4_v3. 

U kunt [hier](https://azure.microsoft.com/regions/services/)de regionale Beschik baarheid van virtuele machines van de Dv3-of Ev3-serie bekijken.

>[!NOTE]
>
>Zie [Windows-Vm's maken en beheren met de module Azure PowerShell](./tutorial-manage-vm.md) voor gedetailleerde instructies voor het maken van een nieuwe virtuele machine
    
## <a name="connect-to-your-azure-vm"></a>Verbinding maken met uw Azure VM

Maak via een extern bureaublad verbinding met de virtuele machine.

1. Klik op de knop **Verbinden** in de eigenschappen van de virtuele machine. Er wordt een Remote Desktop Protocol-bestand (.rdp) gemaakt en gedownload.

2. Open het gedownloade RDP-bestand om verbinding met de VM te maken. Als u hierom wordt gevraagd, klikt u op **Verbinden**. Op een Mac hebt u een RDP-client nodig, zoals deze [Extern-bureaubladclient](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) uit de Mac App Store.

3. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine en klik vervolgens op **OK**.

4. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Klik op **Ja** of **Doorgaan** om door te gaan met de verbinding.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>De Hyper-V-functie inschakelen op de Azure VM
U kunt deze instellingen hand matig configureren of er is een Power shell-script voor het automatiseren van de configuratie.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Optie 1: een Power shell-script gebruiken voor het configureren van geneste virtualisatie
Een Power shell-script om geneste virtualisatie in te scha kelen op een Windows Server 2016-host is beschikbaar op [github](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). Met het script worden de vereisten gecontroleerd en wordt geneste virtualisatie geconfigureerd op de virtuele Azure-machine. De Azure-VM moet opnieuw worden opgestart om de configuratie te volt ooien. Dit script werkt mogelijk in andere omgevingen, maar is niet gegarandeerd. Bekijk het Azure-blog bericht met een live video demonstratie over geneste virtualisatie die wordt uitgevoerd op Azure. https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Optie 2: geneste virtualisatie hand matig configureren

1. Open Power shell als beheerder op de Azure-VM. 

2. Schakel de functies en beheer Programma's van Hyper-V in.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Met deze opdracht wordt de Azure-VM opnieuw gestart. Tijdens het opnieuw opstarten wordt uw RDP-verbinding verbroken.
    
3. Nadat de Azure VM opnieuw is opgestart, maakt u opnieuw verbinding met uw virtuele machine met behulp van RDP.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Internet connectiviteit voor de virtuele gast machine instellen
Maak een nieuwe virtuele netwerk adapter voor de virtuele gast machine en configureer een NAT-gateway om Internet connectiviteit in te scha kelen.

### <a name="create-a-nat-virtual-network-switch"></a>Een virtuele NAT-netwerk switch maken

1. Open Power shell als beheerder op de Azure-VM.
   
2. Maak een interne switch.

    ```powershell
    New-VMSwitch -Name "InternalNAT" -SwitchType Internal
    ```

3. Bekijk de eigenschappen van de switch en noteer de ifIndex voor de nieuwe adapter.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Noteer de ' ifIndex ' voor de virtuele switch die u zojuist hebt gemaakt.
    
4. Maak een IP-adres voor de NAT-gateway.
    
Als u de gateway wilt configureren, hebt u enige informatie over uw netwerk nodig:    
  * IP-adres-de NAT-gateway-IP geeft het IPv4-of IPv6-adres op dat moet worden gebruikt als het standaard gateway adres voor het subnet van het virtuele netwerk. Het algemene formulier is a. b. c. 1 (bijvoorbeeld ' 192.168.0.1 '). Hoewel de uiteindelijke positie niet van toepassing is, is dit meestal (op basis van de lengte van het voor voegsel). Normaal gesp roken moet u een RFC 1918 particulier-netwerk adres ruimte gebruiken. 
  * PrefixLength: de lengte van het subnetvoorvoegsel definieert de grootte van het lokale subnet (subnetmasker). De lengte van het subnetvoorvoegsel is een geheel getal tussen 0 en 32. 0 wijst het hele Internet toe, 32 zou slechts één toegewezen IP toestaan. De algemene waarden variëren van 24 tot 12, afhankelijk van het aantal Ip's dat aan de NAT moet worden gekoppeld. Een veelvoorkomende PrefixLength is 24--Dit is een subnetmasker van 255.255.255.0.
  * Interface index- **ifIndex** is de interface-index van de virtuele switch die in de vorige stap is gemaakt. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Het NAT-netwerk maken

Als u de gateway wilt configureren, moet u informatie opgeven over het netwerk en de NAT-gateway:
  * Naam: dit is de naam van het NAT-netwerk. 
  * InternalIPInterfaceAddressPrefix: in het voor voegsel NAT-subnet worden zowel het IP-voor voegsel van de NAT-gateway van boven als de lengte van het NAT-subnetvoorvoegsel van boven beschreven. Het algemene formulier is a. b. c. 0/NAT-subnet lengte. 

Maak in Power shell een nieuw NAT-netwerk.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>De virtuele gast machine maken

>[!IMPORTANT] 
>
>De Azure-gast agent wordt niet ondersteund op geneste Vm's en kan problemen veroorzaken op zowel de host als de geneste virtuele machines. Installeer de Azure-agent niet op geneste Vm's en gebruik geen installatie kopieën voor het maken van de geneste virtuele machines waarop al de Azure-gast agent is geïnstalleerd.

1. Open Hyper-V-beheer en maak een nieuwe virtuele machine. Configureer de virtuele machine voor het gebruik van het nieuwe interne netwerk dat u hebt gemaakt.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Installeer een besturings systeem op de virtuele gast machine.
    
    >[!NOTE] 
    >
    >U hebt installatie media nodig om een besturings systeem te installeren op de VM. In dit geval gebruiken we Windows 10 Enter prise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Een IP-adres toewijzen aan de virtuele gast machine

U kunt een IP-adres toewijzen aan de virtuele gast machine door hand matig een statisch IP-adres op de virtuele gast machine in te stellen of door DHCP op de Azure-VM te configureren om het IP-adres dynamisch toe te wijzen.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Optie 1: DHCP configureren om dynamisch een IP-adres toe te wijzen aan de virtuele gast machine
Volg de onderstaande stappen om DHCP op de virtuele machine van de host te configureren voor dynamische adres toewijzing.

#### <a name="install-dhcp-server-on-the-azure-vm"></a>DHCP-server installeren op de virtuele Azure-machine

1. Serverbeheer openen. Klik op het dash board op **functies en onderdelen toevoegen**. De wizard Functies en onderdelen toevoegen wordt weergegeven.
  
2. Klik in de wizard op **volgende** totdat de pagina Server functies.
  
3. Schakel het selectie vakje **DHCP-server** in, klik op **onderdelen toevoegen** en klik vervolgens op **volgende** totdat u de wizard hebt voltooid.
  
4. Klik op **Installeren**.

#### <a name="configure-a-new-dhcp-scope"></a>Een nieuwe DHCP-scope configureren

1. Open DHCP-beheer.
  
2. Vouw in het navigatie deel venster de server naam uit, klik met de rechter muisknop op **IPv4** en klik op **nieuwe scope**. De wizard Nieuwe scope wordt weer gegeven, klikt u op **volgende**.
  
3. Voer een naam en beschrijving voor het bereik in en klik op **volgende**.
  
4. Definieer een IP-bereik voor uw DHCP-server (bijvoorbeeld 192.168.0.100 naar 192.168.0.200).
  
5. Klik op **volgende** totdat de pagina standaard-gateway. Geef het IP-adres op dat u eerder hebt gemaakt (bijvoorbeeld 192.168.0.1) als de standaard gateway en klik vervolgens op **toevoegen**.
  
6. Klik op **volgende** totdat de wizard is voltooid, laat alle standaard waarden staan en klik vervolgens op **volt ooien**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Optie 2: een statisch IP-adres hand matig instellen op de virtuele gast machine
Als u DHCP niet hebt geconfigureerd om dynamisch een IP-adres toe te wijzen aan de virtuele gast machine, voert u deze stappen uit om een statisch IP-adres in te stellen.

1. Open Power shell als beheerder op de Azure-VM.

2. Klik met de rechter muisknop op de virtuele gast machine en klik op verbinden.

3. Meld u aan bij de virtuele gast machine.

4. Open het netwerk centrum op de virtuele gast machine.

5. Configureer de netwerk adapter voor een adres binnen het bereik van het NAT-netwerk dat u in de vorige sectie hebt gemaakt.

In dit voor beeld gebruikt u een adres in het bereik 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>De connectiviteit in de virtuele gast machine testen

Open in de virtuele gast machine uw browser en navigeer naar een webpagina.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

Raadpleeg [dit document](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)voor instructies over het inschakelen van transparante connectiviteit tussen virtuele gast-Vm's en Azure-vm's.
