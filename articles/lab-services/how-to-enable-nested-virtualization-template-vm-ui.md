---
title: Geneste virtualisatie inschakelen op een sjabloon-VM in Azure Lab Services (UI) | Microsoft Docs
description: Meer informatie over het maken van een sjabloon-VM met meerdere virtuele machines in.  Met andere woorden, schakel geneste virtualisatie in op een sjabloon-VM in Azure Lab Services.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: f8135e11fb7b7ddb588ab3a8ed01227712072fd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94647916"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-manually"></a>Geneste virtualisatie op een sjabloon virtuele machine in Azure Lab Services hand matig inschakelen

Met geneste virtualisatie kunt u een multi-VM-omgeving maken in de virtuele machine van een lab. Als u de sjabloon publiceert, wordt elke gebruiker in het lab voorzien van een virtuele machine die is ingesteld met meerdere virtuele machines in de test omgeving.  Zie voor meer informatie over geneste virtualisatie en Azure Lab Services [geneste virtualisatie inschakelen op een virtuele machine van een sjabloon in Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

In dit artikel wordt beschreven hoe u een geneste virtualisatie instelt op een sjabloon machine in Azure Lab Services met behulp van Windows-functies en-hulpprogram ma's.  Er zijn een aantal dingen nodig om een klasse in te scha kelen voor het gebruik van geneste virtualisatie.  In de onderstaande stappen wordt beschreven hoe u hand matig een Lab Services-machine sjabloon instelt met Hyper-V.  De stappen zijn bedoeld voor Windows Server 2016 of Windows Server 2019.  

>[!IMPORTANT]
>Selecteer **groot (geneste virtualisatie)** of **Gemiddeld (geneste virtualisatie)** voor de grootte van de virtuele machine bij het maken van het lab.  Geneste virtualisatie werkt anders niet.  

## <a name="enable-hyper-v-role"></a>Hyper-V-functie inschakelen

In de volgende stappen worden de acties beschreven die nodig zijn om Hyper-V op Windows Server in te scha kelen met behulp van een Serverbeheer.  Zodra de installatie is voltooid, is Hyper-V-beheer beschikbaar om virtuele machines van de client toe te voegen, te wijzigen en te verwijderen.

1. Klik in **Serverbeheer** op de pagina dash board op **functies en onderdelen toevoegen**.
2. Klik op de pagina **Voordat u begint** op **Volgende**.
3. Op de pagina **type installatie selecteren** behoudt u de standaard selectie van op rollen gebaseerde of op onderdelen gebaseerde installatie en klikt u vervolgens op **volgende**.
4. Selecteer op de pagina **doel server selecteren** de optie een server uit de Server groep selecteren.   De huidige server is al geselecteerd.  Klik op Volgende.
5. Op de **Serverfuncties selecteren** pagina **Hyper-V**.  
6. De pop-up **wizard functies en onderdelen toevoegen** wordt weer gegeven.  Selecteer **include management tools (indien van toepassing)**.  Klik op de knop **onderdelen toevoegen** .
7. Klik op de pagina **Serverfuncties selecteren** op **Volgende**.
8. Klik op de **pagina onderdelen selecteren** op **volgende**.
9. Klik op de pagina **Hyper-V** op **Volgende**.
10. Accepteer de standaard instellingen op de pagina **virtuele switches maken** en klik op **volgende**.
11. Accepteer de standaard instellingen op de pagina migratie van de **virtuele machine** en klik op **volgende**.
12. Accepteer de standaard instellingen op de pagina **standaard archieven** en klik op **volgende**.
13. Selecteer op de pagina **installatie selecties bevestigen** **de optie de doel server automatisch opnieuw opstarten als dat nodig** is.
14. Wanneer de **wizard functies en onderdelen toevoegen** pop-up wordt weer gegeven, klikt u op **Ja**.
15. Klik op **Installeren**.
16. Wacht totdat de pagina voortgang van de **installatie** is voltooid.  De computer wordt in het midden van de installatie opnieuw opgestart.
17. Klik op **Sluiten**.

## <a name="enable-dhcp-role"></a>DHCP-functie inschakelen

Alle virtuele Hyper-V-client machines die zijn gemaakt, hebben een IP-adres in het NAT-netwerk nodig.  Het NAT-netwerk wordt later gemaakt.  Een manier om IP-adressen toe te wijzen, is door de host in te stellen als een DHCP-server, in dit geval de sjabloon voor de virtuele machine van het lab.  Hieronder vindt u de stappen die nodig zijn om de DHCP-functie in te scha kelen.

1. Klik in **Serverbeheer** op de pagina **dash board** op **functies en onderdelen toevoegen**.
2. Klik op de pagina **Voordat u begint** op **Volgende**.
3. Op de **installatietype selecteren** pagina **installatie op basis van rollen of op basis van functie** en klik vervolgens op **volgende**.
4. Selecteer op de pagina de **doel server selecteren** de huidige server uit de Server groep en klik vervolgens op **volgende**.
5. Selecteer op de pagina **Server functies selecteren** de optie **DHCP-server**.  
6. De pop-up **wizard functies en onderdelen toevoegen** wordt weer gegeven.  Selecteer **include management tools (indien van toepassing)**.  Klik op **functies toevoegen**.

    >[!NOTE]
    >Mogelijk wordt er een validatie fout weer gegeven met de melding dat er geen statische IP-adressen zijn gevonden.  Deze waarschuwing kan voor ons scenario worden genegeerd.

7. Klik op de pagina **Serverfuncties selecteren** op **Volgende**.
8. Klik op de pagina **onderdelen selecteren** op **volgende**.
9. Klik op de pagina **DHCP-server** op **volgende**.
10. Klik op de pagina **Installatieselecties bevestigen** op **Installeren**.
11. Wacht totdat de **pagina voortgang** van de installatie is voltooid.
12. Klik op Sluiten.

## <a name="enable-routing-and-remote-access-role"></a>De functie route ring en RAS inschakelen

1. Klik in **Serverbeheer** op de pagina **dash board** op **functies en onderdelen toevoegen**.
2. Klik op de pagina **Voordat u begint** op **Volgende**.
3. Op de **installatietype selecteren** pagina **installatie op basis van rollen of op basis van functie** en klik vervolgens op **volgende**.
4. Selecteer op de pagina de **doel server selecteren** de huidige server uit de Server groep en klik vervolgens op **volgende**.
5. Selecteer op de pagina **Server functies selecteren** de optie **externe toegang**. Klik op **OK**.
6. Klik op de pagina **onderdelen selecteren** op **volgende**.
7. Klik op de pagina **externe toegang** op **volgende**.
8. Selecteer op de pagina **functie Services** de optie **route ring**.
9. De pop-up **wizard functies en onderdelen toevoegen** wordt weer gegeven.  Selecteer **include management tools (indien van toepassing)**.  Klik op **functies toevoegen**.
10. Klik op **Volgende**.
11. Klik op de pagina **Webserverfunctie (IIS)** op **Volgende**.
12. Klik op de pagina **Functieservices selecteren** op **Volgende**.
13. Klik op de pagina **Installatieselecties bevestigen** op **Installeren**.
14. Wacht totdat de pagina voortgang van de **installatie** is voltooid.  
15. Klik op **Sluiten**.

## <a name="create-virtual-nat-network"></a>Virtueel NAT-netwerk maken

Nu alle benodigde rollen zijn geïnstalleerd, is het tijd om het NAT-netwerk te maken.  Bij het maken van het proces moet u een switch en het NAT-netwerk zelf maken.  Een NAT-netwerk (Network Address Translation) wijst een openbaar IP-adres toe aan een groep virtuele machines in een particulier netwerk om verbinding met internet mogelijk te maken.  In ons geval is de groep persoonlijke Vm's de geneste Vm's.  Het NAT-netwerk staat de geneste Vm's toe om met elkaar te communiceren. Een switch is een netwerk apparaat dat het ontvangen en routeren van verkeer in een netwerk afhandelt.

### <a name="create-a-new-virtual-switch"></a>Een nieuwe virtuele switch maken

1. Open **Hyper-V-beheer** via Windows-beheer Programma's.
2. Selecteer de huidige server in het navigatie menu aan de linkerkant.
3. Klik op **Virtual Switch Manager...** vanuit het menu **acties** aan de rechter kant van het **Hyper-V-beheer**.
4. Selecteer in het pop-upvenster **Virtual Switch Manager** **interne** voor het type switch dat u wilt maken.  Klik op **virtuele switch maken**.
5. Voor de zojuist gemaakte virtuele switch stelt u de naam in op iets te onthouden.  Voor dit voor beeld gebruiken we ' LabServicesSwitch '.  Klik op **OK**.
6. Er wordt een nieuwe netwerk adapter gemaakt.  De naam is vergelijkbaar met ' vEthernet (LabServicesSwitch) '.  Als u wilt controleren of het **configuratie scherm** wordt geopend, klikt u op **netwerk en Internet** en vervolgens op **netwerk status en taken weer geven**.  Klik aan de linkerkant op **adapter instellingen wijzigen**.

### <a name="create-a-nat-network"></a>Een NAT-netwerk maken

1. Open het hulp programma **route ring en RAS** vanuit Windows-beheer Programma's.
2. Selecteer de lokale server in de linkernavigatiebalk.
3. Kies **actie** de optie  ->  **route ring en externe toegang configureren en inschakelen**.
4. Klik op **volgende** wanneer de **wizard Setup van routerings-en RAS-server** wordt weer gegeven.
5. Selecteer op de pagina **configuratie** **netwerk adres omzetting (NAT)-** configuratie.  Klik op **Volgende**.

    >[!WARNING]
    >Kies niet de optie virtueel particulier netwerk (VPN) toegang en NAT.

6. Kies op de pagina **NAT-Internet verbinding** de optie Ethernet.  Kies niet de vEthernet-verbinding (LabServicesSwitch) die u in Hyper-V-beheer hebt gemaakt. Klik op **Volgende**.
7. Klik op **volt ooien** op de laatste pagina van de wizard.
8. Wanneer het dialoog venster **service starten** wordt weer gegeven, klikt u op **service starten**.
9. Wacht tot de service is gestart.

## <a name="update-network-adapter-settings"></a>Instellingen voor netwerk Adapter bijwerken

De netwerk adapter wordt gekoppeld aan het IP-adres dat wordt gebruikt voor de standaard gateway-IP voor het NAT-netwerk dat u eerder hebt gemaakt.  In dit voor beeld maken we een IP-adres van 192.168.0.1 met een subnetmasker van 255.255.255.0.  De virtuele switch die u eerder hebt gemaakt, wordt gebruikt.

1. Open het **configuratie scherm**, klik op **netwerk en Internet** en klik op **netwerk status en taken weer geven**.
2. Klik aan de linkerkant op **adapter instellingen wijzigen**.  
3. Dubbel klik in het venster **netwerk verbindingen** op ' VEthernet (LabServicesSwitch) ' om het dialoog venster status Details van **vEthernet (LabServicesSwitch)** weer te geven.
4. Klik op de knop **Eigenschappen** .
5. Selecteer **Internet Protocol item versie 4 (TCP/IPv4)** en klik op de knop **Eigenschappen** .
6. Selecteer in het dialoog venster **Internet Protocol versie 4 (TCP/IPv4) eigenschappen** **het volgende IP-adres gebruiken**.  Geef 192.168.0.1 op voor het IP-adres. Voer 255.255.255.0 in voor het subnetmasker.  Laat de standaard gateway leeg.  Zorg er ook voor dat de DNS-servers leeg blijven.

    >[!NOTE]
    > Ons bereik voor het NAT-netwerk is, in CIDR-notatie, 192.168.0.0/24.  Hiermee maakt u een aantal bruikbare IP-adressen van 192.168.0.1 tot en met 192.168.0.254.  Per Conventie hebben gateways het eerste IP-adres in een subnet.

7. Klik op OK.

## <a name="create-dhcp-scope"></a>DHCP-scope maken

De volgende stappen zijn een instructie voor het toevoegen van een DHCP-scope.  In dit artikel is het NAT-netwerk 192.168.0.0/24 in CIDR-notatie.  Hiermee maakt u een aantal bruikbare IP-adressen van 192.168.0.1 tot en met 192.168.0.254.  Het bereik dat u hebt gemaakt, moet zich in dat bereik bevinden met bruikbare adressen met uitzonde ring van het IP-adres dat eerder is gemaakt.

1. Open **systeem beheer** en open het **DHCP-** beheer programma.
2. Vouw in het **DHCP-** hulp programma het knoop punt voor de huidige server uit en selecteer **IPv4**.
3. Kies in het menu Actie de optie **nieuwe scope...**
4. Wanneer de **wizard Nieuwe scope** wordt weer gegeven, klikt u op de **welkomst** pagina op **volgende** .
5. Voer op de pagina **Scope naam** ' LabServicesDhcpScope ' in of iets anders dat is onthouden voor de naam.  Klik op **Volgende**.
6. Voer de volgende waarden in op de pagina **IP-adres bereik** .

    - 192.168.0.100 voor het IP-begin adres
    - 192.168.0.200 voor het laatste IP-adres
    - 24 voor de lengte
    - 255.255.255.0 voor het subnetmasker

7. Klik op **Volgende**.
8. Klik op de pagina **uitsluitingen en vertraging toevoegen** op **volgende**.
9. Klik op de pagina **lease duur** op **volgende**.
10. Selecteer op de pagina **DHCP-opties configureren** de optie **Ja, ik wil deze opties nu configureren**. Klik op **Volgende**.
11. Op de **router (standaard gateway)**
12. Voeg 192.168.0.1 toe als u dit nog niet hebt gedaan. Klik op **Volgende**.
13. Voeg op de pagina **domein naam en DNS-servers** 168.63.129.16 als een IP-adres van een DNS-server toe als dit nog niet is gebeurd.  168.63.129.16 is het IP-adres voor een statische Azure DNS-server. Klik op **Volgende**.
14. Klik op de pagina **WINS-servers** op **volgende**.
15. Selecteer op de pagina **Scope activeren** de optie **Ja, ik wil deze scope nu activeren**.  Klik op **Volgende**.
16. Op de pagina **de wizard Nieuwe scope** volt ooien klikt u op **Finish**.

## <a name="conclusion"></a>Conclusie

Uw sjabloon machine is nu gereed voor het maken van virtuele Hyper-V-machines.   Zie [een virtuele machine maken in Hyper-v](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) voor instructies over het maken van virtuele Hyper-v-machines.  Zie ook het [micro soft Evaluation Center](https://www.microsoft.com/evalcenter/) om beschik bare besturings systemen en software te bekijken.

## <a name="next-steps"></a>Volgende stappen

De volgende stappen zijn gebruikelijk voor het instellen van elk lab.

- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een planning instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail registratie koppelingen naar studenten](how-to-configure-student-usage.md#send-invitations-to-users)