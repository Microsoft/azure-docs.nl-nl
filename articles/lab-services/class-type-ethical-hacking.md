---
title: Stel een ethische hacking Lab in met Azure Lab Services | Microsoft Docs
description: Meer informatie over het instellen van een Lab met Azure Lab Services om ethische hacking te leren.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 97cdf13f39cc73ee7f35fb402229469195f1456c
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616419"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Een Lab instellen om ethische hacking-klasse te leren

In dit artikel wordt beschreven hoe u een klasse instelt die zich richt op forensischee-hacking. Indringings tests, een praktijk die wordt gebruikt door de ethische hacking-Community, treedt op wanneer iemand probeert toegang te krijgen tot het systeem of netwerk om beveiligings problemen te demonstreren die een kwaadwillende aanvaller kan misbruiken.

In een ethische hacking-klasse kunnen studenten moderne technieken leren voor het beschermen tegen beveiligings problemen. Elke student haalt een virtuele Windows Server-host op met twee geneste virtuele machines: één virtuele machine met [Metasploitable3](https://github.com/rapid7/metasploitable3) -installatie kopie en een andere machine met [Kali Linux](https://www.kali.org/) -installatie kopie. De virtuele machine Metasploitable wordt gebruikt voor het exploiteren van toepassingen en Kali virtual machine biedt toegang tot de hulpprogram ma's die nodig zijn om forensische-taken uit te voeren.

Dit artikel bevat twee hoofd secties. In de eerste sectie wordt beschreven hoe u het leslokaal Lab maakt. In de tweede sectie wordt beschreven hoe u de sjabloon machine maakt waarvoor geneste virtualisatie is ingeschakeld en met de hulpprogram ma's en installatie kopieën die nodig zijn. In dit geval wordt een Metasploitable-installatie kopie en een Kali Linux-installatie kopie op een computer waarop Hyper-V is ingeschakeld voor het hosten van de installatie kopieën.

## <a name="lab-configuration"></a>Lab-configuratie

Als u dit Lab wilt instellen, hebt u een Azure-abonnement nodig om aan de slag te gaan. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint. Zodra u een Azure-abonnement hebt ontvangen, kunt u een nieuw Lab-account maken in Azure Lab Services of een bestaand account gebruiken. Raadpleeg de volgende zelf studie voor het maken van een nieuw Lab-account: [zelf studie voor het instellen van een Lab-account](tutorial-setup-lab-account.md).

Volg [deze zelf studie](tutorial-setup-classroom-lab.md) voor het maken van een nieuw lab en pas vervolgens de volgende instellingen toe:

| Grootte van de virtuele machine | Installatiekopie |
| -------------------- | ----- |
| Gemiddeld (geneste virtualisatie) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Sjabloon machine

Nadat de sjabloon machine is gemaakt, start u de machine en maakt u er verbinding mee om de volgende drie belang rijke taken uit te voeren.

1. Stel de computer in voor geneste virtualisatie. Hiermee worden alle relevante Windows-functies, zoals Hyper-V, ingeschakeld en wordt het netwerk voor de Hyper-V-installatie kopieën ingesteld om met elkaar en Internet te communiceren.
2. Stel de [Kali](https://www.kali.org/) Linux-installatie kopie in. Kali is een Linux-distributie met hulpprogram ma's voor indringings tests en beveiligings controles.
3. Stel de Metasploitable-installatie kopie in. Voor dit voor beeld wordt de [Metasploitable3](https://github.com/rapid7/metasploitable3) -afbeelding gebruikt. Deze installatie kopie is bedoeld om beveiligings problemen te ondervinden.

De rest van dit artikel heeft betrekking op de hand matige stappen voor het volt ooien van de bovenstaande taken.  U kunt ook de [test services voor Hyper-V-scripts](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) en [Lab-services van ethische Hack scripts](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking)uitvoeren.

### <a name="prepare-template-machine-for-nested-virtualization"></a>Sjabloon machine voorbereiden voor geneste virtualisatie

Volg de instructies om [geneste virtualisatie in te scha kelen](how-to-enable-nested-virtualization-template-vm.md) voor het voorbereiden van de virtuele machine van uw sjabloon voor geneste virtualisatie

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Een geneste virtuele machine met Kali Linux-installatie kopie instellen

Kali is een Linux-distributie met hulpprogram ma's voor indringings tests en beveiligings controles.

1. Down load installatie kopie van [aanstootgevende beveiliging Kali Linux VM-installatie kopieën](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/).  Onthoud de standaard gebruikers naam en het wacht woord die worden vermeld op de download pagina.
    1. Down load de **Kali Linux vmware 64-bits (7z)-** installatie kopie voor VMware.
    1. Pak het. 7z-bestand uit.  Als u nog geen 7-post code hebt, kunt u deze downloaden van [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html) . Onthoud de locatie van de uitgepakte map zoals u deze later nodig hebt.
1. Converteer het uitgepakte VMDK-bestand naar een vhdx-bestand, zodat u het vhdx-bestand kunt gebruiken met Hyper-V. Er zijn verschillende hulpprogram ma's beschikbaar om VMware-installatie kopieën te converteren naar Hyper-V-installatie kopieën.  We gebruiken het [Star wind V2V-conversie programma](https://www.starwindsoftware.com/starwind-v2v-converter).  Zie [Star wind V2V Converter-Download pagina](https://www.starwindsoftware.com/starwind-v2v-converter#download)om te downloaden.
    1. Start het **Star wind V2V-conversie programma**.
    1. Kies **lokaal bestand** op de pagina **Selecteer de locatie van de afbeelding die u wilt converteren** .  Selecteer **Volgende**.
    1. Ga op de pagina **bron installatie kopie** naar en selecteer het Kali Linux VMDK-bestand dat u in de vorige stap hebt geëxtraheerd voor de instelling van de **Bestands naam** .  Het bestand heeft de indeling Kali-Linux-{version}-VMware-amd64. vmdk.  Selecteer **Volgende**.
    1. Kies **lokaal bestand** op de **locatie van de doel installatie kopie selecteren**.  Selecteer **Volgende**.
    1. Kies op de pagina **indeling van doel afbeelding selecteren** de optie **VHD/VHDX**.  Selecteer **Volgende**.
    1. Kies op de **optie selecteren voor de pagina afbeeldings indeling voor VHD/vhdx** een **groter formaat voor VHDX**.  Selecteer **Volgende**.
    1. Accepteer op de pagina Selecteer de naam van het **doel bestand** de standaard naam van het bestand.  Selecteer **converteren**.
    1. Wacht op de pagina **converteren** naar de afbeelding die moet worden geconverteerd.  Dit kan enkele minuten duren.  Selecteer **volt ooien** wanneer de conversie is voltooid.
1. Maak een nieuwe Hyper-V-virtuele machine.
    1. Open **Hyper-V-beheer**.
    1. Kies **actie**  ->  **nieuwe**  ->  **virtuele machine**.
    1. Selecteer op de pagina **voordat u begint** van de **wizard Nieuwe virtuele machine** de optie **volgende**.
    1. Voer op de pagina **naam en locatie opgeven** **Kali-Linux** in voor de **naam** en selecteer **volgende**.
    1. Accepteer de standaard instellingen op de pagina **generatie opgeven** en selecteer **volgende**.
    1. Op de pagina **geheugen toewijzen** voert u **2048 MB** in voor het **opstart geheugen** en selecteert u **volgende**.
    1. Op de pagina **netwerk configureren sluit u** de verbinding als **niet verbonden**. U dient de netwerk adapter later in te stellen.
    1. Selecteer op de pagina **virtuele harde schijf aansluiten** de optie **een bestaande virtuele harde schijf gebruiken**. Blader naar de locatie voor het **Kali-Linux-{version}-VMware-amd64. vmdk** -bestand dat u in de vorige stap hebt gemaakt en selecteer **volgende**.
    1. Op de pagina **de wizard Nieuwe virtuele machine volt** ooien en selecteer **volt ooien**.
    1. Wanneer de virtuele machine is gemaakt, selecteert u deze in de Hyper-V-beheer. Schakel de machine nog niet in.  
    1. Kies **actie**-  ->  **instellingen**.
    1. Selecteer **Hardware toevoegen** in het dialoog venster **instellingen voor Kali-Linux** voor.
    1. Selecteer **legacy netwerk adapter** en selecteer **toevoegen**.
    1. Selecteer op de pagina **legacynetwerkadapter** de optie **LabServicesSwitch** voor de **virtuele switch** en selecteer **OK**. LabServicesSwitch is gemaakt tijdens het voorbereiden van de sjabloon machine voor Hyper-V in de sectie **sjabloon voorbereiden voor geneste virtualisatie** .
    1. De Kali-Linux installatie kopie is nu klaar voor gebruik. Klik in **Hyper-V-beheer** op **actie**  ->  **starten** en kies **actie**  ->  **verbinden** om verbinding te maken met de virtuele machine.  De standaard gebruikersnaam is **Kali** en het wacht woord is **Kali**.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Een geneste VM met Metasploitable-installatie kopie instellen  

De Rapid7 Metasploitable-installatie kopie is een installatie kopie die als doel is geconfigureerd met beveiligings problemen. U gebruikt deze afbeelding om problemen te testen en op te sporen. De volgende instructies laten zien hoe u een vooraf gemaakte Metasploitable-installatie kopie gebruikt. Als er echter een nieuwere versie van de Metasploitable-installatie kopie nodig is, raadpleegt u [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3) .

1. Down load de Metasploitable-installatie kopie.
    1. Navigeer naar [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html) . Vul het formulier in om de installatie kopie te downloaden en selecteer de knop **verzenden** .
    2. Selecteer de knop **Metasploitable nu downloaden** .
    3. Wanneer het zip-bestand is gedownload, pakt u het zip-bestand uit en herinnert u de locatie van het Metasploitable. VMDK-bestand.
1. Converteer het uitgepakte VMDK-bestand naar een vhdx-bestand, zodat u het vhdx-bestand kunt gebruiken met Hyper-V. Er zijn verschillende hulpprogram ma's beschikbaar om VMware-installatie kopieën te converteren naar Hyper-V-installatie kopieën.  Het [Star wind V2V-conversie programma](https://www.starwindsoftware.com/starwind-v2v-converter) wordt opnieuw gebruikt.  Zie [Star wind V2V Converter-Download pagina](https://www.starwindsoftware.com/starwind-v2v-converter#download)om te downloaden.
    1. Start het **Star wind V2V-conversie programma**.
    1. Kies **lokaal bestand** op de pagina **Selecteer de locatie van de afbeelding die u wilt converteren** .  Selecteer **Volgende**.
    1. Ga op de pagina **bron installatie kopie** naar en selecteer de Metasploitable. vmdk geëxtraheerd in de vorige stap voor de instelling van de **Bestands naam** .  Selecteer **Volgende**.
    1. Kies **lokaal bestand** op de **locatie van de doel installatie kopie selecteren**.  Selecteer **Volgende**.
    1. Kies op de pagina **indeling van doel afbeelding selecteren** de optie **VHD/VHDX**.  Selecteer **Volgende**.
    1. Kies op de **optie selecteren voor de pagina afbeeldings indeling voor VHD/vhdx** een **groter formaat voor VHDX**.  Selecteer **Volgende**.
    1. Accepteer op de pagina Selecteer de naam van het **doel bestand** de standaard naam van het bestand.  Selecteer **converteren**.
    1. Wacht op de pagina **converteren** naar de afbeelding die moet worden geconverteerd.  Dit kan enkele minuten duren.  Selecteer **volt ooien** wanneer de conversie is voltooid.
1. Maak een nieuwe Hyper-V-virtuele machine.
    1. Open **Hyper-V-beheer**.
    1. Kies **actie**  ->  **nieuwe**  ->  **virtuele machine**.
    1. Selecteer op de pagina **voordat u begint** van de **wizard Nieuwe virtuele machine** de optie **volgende**.
    1. Voer op de pagina **naam en locatie opgeven** **Metasploitable** in als **naam** en selecteer **volgende**.

        ![Wizard nieuwe VM-installatie kopie](./media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. Accepteer de standaard instellingen op de pagina **generatie opgeven** en selecteer **volgende**.
    1. Op de pagina **geheugen toewijzen** voert u **512 MB** in voor het **opstart geheugen** en selecteert u **volgende**.

        ![Pagina voor toewijzen van geheugen](./media/class-type-ethical-hacking/assign-memory-page.png)
    1. Op de pagina **netwerk configureren sluit u** de verbinding als **niet verbonden**. U dient de netwerk adapter later in te stellen.
    1. Selecteer op de pagina **virtuele harde schijf aansluiten** de optie **een bestaande virtuele harde schijf gebruiken**. Blader naar de locatie voor het bestand **metasploitable. vhdx** dat u in de vorige stap hebt gemaakt en selecteer **volgende**.

        ![Pagina virtuele netwerk schijf verbinden](./media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. Op de pagina **de wizard Nieuwe virtuele machine volt** ooien en selecteer **volt ooien**.
    1. Wanneer de virtuele machine is gemaakt, selecteert u deze in de Hyper-V-beheer. Schakel de machine nog niet in.  
    1. Kies **actie**-  ->  **instellingen**.
    1. Selecteer **Hardware toevoegen** in het dialoog venster **instellingen voor Metasploitable** .
    1. Selecteer **legacy netwerk adapter** en selecteer **toevoegen**.

        ![Pagina netwerk adapter](./media/class-type-ethical-hacking/network-adapter-page.png)
    1. Selecteer op de pagina **legacynetwerkadapter** de optie **LabServicesSwitch** voor de **virtuele switch** en selecteer **OK**. LabServicesSwitch is gemaakt tijdens het voorbereiden van de sjabloon machine voor Hyper-V in de sectie **sjabloon voorbereiden voor geneste virtualisatie** .

        ![Pagina verouderde netwerk adapter](./media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. De Metasploitable-installatie kopie is nu klaar voor gebruik. Klik in **Hyper-V-beheer** op **actie**  ->  **starten** en kies **actie**  ->  **verbinden** om verbinding te maken met de virtuele machine.  De standaard gebruikersnaam is **msfadmin** en het wacht woord is **msfadmin**.

De sjabloon is nu bijgewerkt en bevat installatie kopieën die nodig zijn voor een ethische test klasse met ingrijpende indringing, een afbeelding met hulpprogram ma's voor het uitvoeren van de indringings tests en een andere installatie kopie met beveiligings problemen die moeten worden gedetecteerd. De sjabloon afbeelding kan nu worden gepubliceerd naar de klasse. Selecteer de knop **publiceren** op de sjabloon pagina om de sjabloon te publiceren naar het lab.

## <a name="cost"></a>Kosten  

Als u de kosten van dit Lab wilt schatten, kunt u het volgende voor beeld gebruiken:

Voor een klasse van 25 studenten met 20 uur geplande en tien uur aan quota voor huis werk of-toewijzingen is de prijs voor het Lab:

25 studenten \* (20 + 10) uur \* 55 Lab-eenheden \* 0,01 USD per uur = 412,50 USD

>[!IMPORTANT]
>Kosten raming is alleen bedoeld als voor beeld. Zie [Azure Lab Services prijzen](https://azure.microsoft.com/pricing/details/lab-services/)voor actuele Details over prijzen.

## <a name="conclusion"></a>Conclusie

In dit artikel werd uitgelegd hoe u stapsgewijs door de stappen voor het maken van een Lab voor ethische hacking-klasse. Hierin worden de stappen beschreven voor het instellen van geneste virtualisatie voor het maken van twee virtuele machines in de virtuele host-machine voor het doordringen van testen.

## <a name="next-steps"></a>Volgende stappen

De volgende stappen zijn gebruikelijk voor het instellen van elk lab:

- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een planning instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail registratie koppelingen naar studenten](how-to-configure-student-usage.md#send-invitations-to-users).
